install ollama
ollama run yi-coder:1.5b

set up open-webui

221  conda create -n open-webui python=3.11
  222  conda activate open-webui
  223  ls -rtl
  224  conda install pip
  225  cd open-webui/
  226  ls -rtl
  227  pip install -e .
  228  conda install nodejs
  229  pip install -e .

## Manual Installation[​](https://docs.openwebui.com/getting-started/#manual-installation "Direct link to Manual Installation")

### Installation with `pip` (Beta)[​](https://docs.openwebui.com/getting-started/#installation-with-pip-beta "Direct link to installation-with-pip-beta")

For users who prefer to use Python's package manager `pip`, Open WebUI offers a installation method. Python 3.11 is required for this method.

1. **Install Open WebUI**: Open your terminal and run the following command:
    
    ```
    pip install open-webui
    ```
    
2. **Start Open WebUI**: Once installed, start the server using:
    
    ```
    open-webui serve
    ```
    

This method installs all necessary dependencies and starts Open WebUI, allowing for a simple and efficient setup. After installation, you can access Open WebUI at [http://localhost:8080](http://localhost:8080/). Enjoy! 😄

### Install from Open WebUI GitHub Repo[​](https://docs.openwebui.com/getting-started/#install-from-open-webui-github-repo "Direct link to Install from Open WebUI GitHub Repo")

info

Open WebUI consists of two primary components: the frontend and the backend (which serves as a reverse proxy, handling static frontend files, and additional features). Both need to be running concurrently for the development environment.

#### Requirements 📦[​](https://docs.openwebui.com/getting-started/#requirements- "Direct link to Requirements 📦")

- 🐰 [Node.js](https://nodejs.org/en) >= 20.10
- 🐍 [Python](https://python.org/) >= 3.11

#### Build and Install 🛠️[​](https://docs.openwebui.com/getting-started/#build-and-install-%EF%B8%8F "Direct link to Build and Install 🛠️")

Run the following commands to install:

For Linux/macOS:

```
git clone https://github.com/open-webui/open-webui.gitcd open-webui/# Copying required .env filecp -RPp .env.example .env# Building Frontend Using Nodenpm installnpm run buildcd ./backend# Optional: To install using Conda as your development environment, follow these instructions:# Create and activate a Conda environmentconda create --name open-webui-env python=3.11conda activate open-webui-env# Install dependenciespip install -r requirements.txt -U# Start the applicationbash start.sh
```

Install ffmpeg alsoe

To get access to port in local network:
Environment="OLLAMA_HOST=0.0.0.0:11434"
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/usr/local/bin/ollama serve
User=ollama
Group=ollama
Restart=always
RestartSec=3
Environment="PATH=/home/guido/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/usr/lib/wsl/lib:/mnt/c/Windows/system32:/mnt/c/Windows:/mnt/c/Windows/System32/Wbem:/mnt/c/Windows/System32/WindowsPowerShell/v1.0/:/mnt/c/Windows/System32/OpenSSH/:/mnt/c/Program Files/dotnet/:/mnt/c/Program Files/PuTTY/:/mnt/c/Program Files/Docker/Docker/resources/bin:/mnt/c/Program Files/Tailscale/:/mnt/c/Users/Brita Ralph/AppData/Local/Microsoft/WindowsApps:/mnt/c/Users/Brita Ralph/AppData/Local/Programs/Ollama:/mnt/c/Users/Brita Ralph/AppData/Local/Programs/Microsoft VS Code/bin:/snap/bin"
Environment="OLLAMA_HOST=0.0.0.0:11434"

[Install]
WantedBy=default.target


sudo systemctl daemon-reload
sudo systemctl status ollama.service 
sudo systemctl start ollama.service 
sudo systemctl status ollama.service 
  