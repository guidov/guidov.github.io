---
layout: post
title: "N8N local install over https (nginx)"
date: 2025-05-24
categories:
  - tools
  - speech-to-text
  - open-source
---

---
- ## Navigating the Labyrinth: Setting Up n8n with Gmail OAuth and a Reverse Proxy
  
  Automating workflows with n8n is a game-changer, and connecting it to services like Gmail is often a top priority. However, the path from setup to a smoothly running automation isn't always straightforward, especially when dealing with OAuth, remote servers, and reverse proxies. I recently went through this gauntlet, and here’s a breakdown of the hurdles I faced and how I overcame them, hoping it might save you some time.
  
  My goal was to get n8n, running on a remote server (`cloudhost.com`), to securely authenticate with Gmail, ideally served under a subpath like `https://cloudhost.com/n8n/` via an Nginx reverse proxy.
- ### Hurdle 1: The Google "Access Denied" Error
  
  After setting up my OAuth 2.0 credentials in the Google Cloud Console, I eagerly tried to connect n8n to my Gmail account. I was immediately met with:
  
  **"Access blocked: [My App Name] has not completed the Google verification process."**
  
  This is a common first roadblock. The issue here is that newly created OAuth applications in Google Cloud are, by default, in "Testing" mode. In this state, only users explicitly added as "Test Users" can authorize the application.
  
  **The Fix:**
  
  1.  Navigate to the **Google Cloud Console**.
  2.  Go to **"APIs & Services" -> "OAuth consent screen"**.
  3.  Ensure the "Publishing status" is "Testing" (which it likely will be).
  4.  Scroll down to the **"Test users"** section.
  5.  Click **"+ ADD USERS"** and add the Gmail address(es) you intend to connect with n8n.
  
  With my email added as a test user, this particular error was resolved.
- ### Hurdle 2: The `localhost` Redirect Trap
  
  Confident, I tried the Gmail authentication in n8n again. The Google part seemed to work, but then my browser landed on an error page:
  
  **`ERR_CONNECTION_REFUSED` for `http://localhost:5678/...`**
  
  Of course! My n8n instance was running on my remote server, `cloudhost.com`, not on my local machine. The redirect URI that n8n was telling Google to use (and that it expected the browser to return to) was pointing to `localhost`.
  
  **The Fix:**
  
  n8n needs to know its correct public-facing URL to construct proper redirect URIs. This is configured via environment variables. Since I wanted to serve n8n under `https://cloudhost.com/n8n/`, I updated my n8n systemd service file (`/etc/systemd/system/n8n.service` or similar):
  
  ```ini
  # Part of n8n.service
  [Service]
  Environment=N8N_PROTOCOL="https"
  Environment=N8N_EDITOR_BASE_URL="https://cloudhost.com/n8n/"
  Environment=WEBHOOK_URL="https://cloudhost.com/n8n/" # Often derived from EDITOR_BASE_URL but good to be explicit
  Environment=N8N_HOST="localhost" # n8n listens locally, Nginx will handle public access
  Environment=N8N_PORT="5678"
  # ... other settings ...
  ```
  
  After setting these:
  1.  Reload the systemd daemon: `sudo systemctl daemon-reload`
  2.  Restart n8n: `sudo systemctl restart n8n.service`
  
  Then, critically, I updated the **"Authorized redirect URIs"** in my Google Cloud Console OAuth 2.0 Client ID settings to:
  `https://cloudhost.com/n8n/rest/oauth2-credential/callback`
- ### Hurdle 3: The Blank Page - Nginx Subpath and Asset Woes
  
  With the redirect URI seemingly correct, I navigated to `https://cloudhost.com/n8n/`. Instead of the n8n login page, I got a blank white page.
  
  My Nginx configuration for the subpath looked something like this:
  
  ```nginx
  # /etc/nginx/sites-available/default (or your site's config)
  server {
    listen 443 ssl http2;
    server_name cloudhost.com;
    # ... other SSL settings ...
  
    location /n8n/ {
        proxy_pass http://localhost:5678/; # Note the trailing slash!
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        # ... other headers ...
    }
    # ... other locations ...
  }
  ```
  
  A look at the Nginx error logs (`/var/log/nginx/error.log`) revealed 404 errors. Nginx was trying to serve assets like `/assets/some-script.js` from my main website's root, not from within the `/n8n/` subpath.
  
  Opening the browser's "View Page Source" for the blank page confirmed it: n8n was generating HTML with asset links like `<script src="/assets/app.js">` instead of `<script src="/n8n/assets/app.js">`. The `window.BASE_PATH` JavaScript variable was also set to `'/'`.
  
  Even though my n8n startup logs showed `Editor is now accessible via: https://cloudhost.com/n8n/` (indicating `N8N_EDITOR_BASE_URL` was read), the UI asset paths weren't respecting it.
  
  **The Fix (Attempt 1 - `N8N_PATH_PREFIX`):**
  
  For some n8n versions, especially older ones or in certain subpath configurations, an additional environment variable `N8N_PATH_PREFIX` is needed to hint to the UI about the subpath. I added this to my `n8n.service` file:
  
  ```ini
  # Part of n8n.service
  Environment=N8N_PATH_PREFIX="/n8n/"
  ```
  Followed by `daemon-reload` and `restart n8n.service`.
  
  Unfortunately, even after verifying with `cat /proc/<N8N_PID>/environ | tr '\0' '\n' | grep N8N_` that the n8n process *was* seeing `N8N_PATH_PREFIX=/n8n/`, the page source still showed incorrect asset paths. This was baffling and pointed to a potential issue with my specific n8n version (`X.Y.Z` - *insert your version here if known, as this behavior can be version-dependent*) and how it handled these variables for UI rendering.
  
  **The Fix (Attempt 2 - Nginx `sub_filter` Workaround):**
  
  As a more forceful approach, I considered using Nginx's `sub_filter` module to rewrite the incorrect paths in the HTML response from n8n before sending it to the browser. This isn't ideal, as it's a bit of a hack.
  
  ```nginx
  # In the location /n8n/ block
  sub_filter_once off;
  sub_filter_types text/html text/javascript;
  sub_filter 'src="/assets/' 'src="/n8n/assets/';
  sub_filter "href='/assets/" "href='/n8n/assets/';
  sub_filter 'href="/favicon.ico"' 'href="/n8n/favicon.ico"';
  sub_filter "window.BASE_PATH = '/';" "window.BASE_PATH = '/n8n/';";
  ```
- ### The "Aha!" Moment: Direct HTTPS on n8n
  
  While wrestling with `sub_filter`, I recalled an earlier configuration I had tried: letting n8n handle HTTPS directly. My `n8n.service` file contained:
  
  ```ini
  # Part of n8n.service
  Environment=N8N_PROTOCOL="https"
  Environment=N8N_HOST="cloudhost.com" # Or 0.0.0.0
  Environment=N8N_PORT="5678"
  Environment=N8N_SSL_CERT="/path/to/your/cloudhost_com.crt"
  Environment=N8N_SSL_KEY="/path/to/your/cloudhost_com.key"
  # Initially, N8N_EDITOR_BASE_URL was for the subpath
  ```
  
  On a hunch, I tried accessing `https://cloudhost.com:5678` directly in my browser. **It worked!** n8n loaded perfectly, serving HTTPS on its own.
  
  The 502 Bad Gateway I was seeing for `https://cloudhost.com/n8n/` suddenly made sense. My Nginx `proxy_pass` was attempting to connect to `http://localhost:5678` (plain HTTP), but n8n, with its SSL settings active, was *only* listening for HTTPS connections on port 5678. Nginx's HTTP request to an HTTPS-only port was failing.
  
  **The Final (Simpler) Solution for My Use Case:**
  
  I decided that accessing n8n directly via `https://cloudhost.com:5678` was acceptable for my needs, bypassing the Nginx subpath complexity for n8n.
  
  1.  **Updated `n8n.service` for direct HTTPS access:**
    ```ini
    # Final n8n.service for direct HTTPS
    [Service]
    Environment=N8N_PROTOCOL="https"
    Environment=N8N_EDITOR_BASE_URL="https://cloudhost.com:5678/"
    Environment=WEBHOOK_URL="https://cloudhost.com:5678/"
    Environment=N8N_HOST="0.0.0.0" # Listen on all interfaces
    Environment=N8N_PORT="5678"
    Environment=N8N_SSL_CERT="/path/to/your/cloudhost_com.crt"
    Environment=N8N_SSL_KEY="/path/to/your/cloudhost_com.key"
    Environment=N8N_SECURE_COOKIE=true # Crucial for direct HTTPS
    # ... other settings ...
    ```
    Then `sudo systemctl daemon-reload` and `sudo systemctl restart n8n.service`.
  
  2.  **Updated Google OAuth Redirect URI:**
    Changed to `https://cloudhost.com:5678/rest/oauth2-credential/callback`.
  
  3.  **Cleaned Nginx Configuration:**
    I removed the entire `location /n8n/ { ... }` block from my Nginx config, as Nginx was no longer proxying requests to n8n.
  
  4.  **Firewall:** Ensured port `5678` (TCP) was open.
  
  With these changes, Gmail authentication worked flawlessly via `https://cloudhost.com:5678`.
- ### Key Takeaways
  
  This troubleshooting marathon reinforced several key principles:
  
  *   **Google OAuth "Testing" Mode:** Always add your email as a test user for new OAuth apps.
  *   **Public URLs are Paramount:** n8n and any OAuth provider need to know n8n's actual, publicly accessible URL. No `localhost` for remote servers.
  *   **n8n Environment Variables:**
    *   `N8N_EDITOR_BASE_URL` is the primary variable for defining how n8n is accessed.
    *   For subpath hosting, `N8N_PATH_PREFIX` (or similar, depending on n8n version) might be necessary if UI assets don't get the correct prefix from `N8N_EDITOR_BASE_URL` alone.
  *   **Nginx Reverse Proxy:**
    *   Pay attention to trailing slashes in `proxy_pass` when dealing with subpaths.
    *   Ensure necessary headers like `X-Forwarded-Proto` are passed so n8n knows it's behind an HTTPS proxy.
  *   **Direct n8n HTTPS:** If n8n serves HTTPS directly (using `N8N_SSL_CERT` and `N8N_SSL_KEY`), any reverse proxy trying to connect to it must do so via HTTPS to that port, or you bypass the proxy for n8n access. Remember to set `N8N_SECURE_COOKIE=true`.
  *   **Debugging Tools:**
    *   **Logs are your best friends:** n8n service logs, Nginx access/error logs.
    *   **Browser Developer Tools:** "View Page Source" to see what HTML n8n is actually generating, and the "Network" tab to inspect requests and responses.
    *   **Verify Process Environment:** Use `cat /proc/<PID>/environ` to confirm your service is actually getting the environment variables you think it is.
  
  While the journey was convoluted, each step provided clues. Hopefully, sharing this helps you navigate your own n8n setup with a bit more clarity!
-
- ~~~~
