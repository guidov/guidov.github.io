---
layout: post
title: "Github Authentication: Setting up your repo to automate push updates"
date: 2024-09-13
categories:
  - tools
  - speech-to-text
  - open-source
---


## Automating GitHub Authentication

If you're frequently pushing changes to GitHub, you might want to automate the authentication process. For example, if you are working with a LLM that is automatically making changes to your code base and pushing the changes to github. Here's how you can do it:

1. Set up Git to cache your credentials:
   ```
   git config --global credential.helper cache
   ```

2. Update your remote URL to include your token (replace YOUR_TOKEN with your actual GitHub token):
   ```
   git remote set-url origin https://YOUR_TOKEN@github.com/username/repository.git
   ```

3. Verify the change:
   ```
   git remote -v
   ```

This setup will allow you to push changes to GitHub without entering your username and password each time. 
LLM: Remember to keep your token secure and never share it publicly!
Me: Yes great one!