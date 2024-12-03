---
layout: post
title: "Exploring Devbox for Local Development: First Impressions"
tags:
- tech
- tools
category: tech
date: 2024-12-03 11:25:00 +0530
---

For a long time, I’ve been using docker to isolate multiple development projects on my local machine. My setup includes VS Code with Dev Containers, which has worked well. However, I recently came across [**Devbox**](https://www.jetify.com/devbox) in the [ThoughtWorks Technology Radar](https://www.thoughtworks.com/radar) which produces isolated dev environments using nix package manager without using virtual machines or containers. Wondering if it could prove to be a better tool than docker containers, I decided to give it a shot.

### Installation and Setup  

Getting Devbox up and running on macOS is straightforward. Just run the following command:  

```bash  
curl -fsSL https://get.jetify.com/devbox | bash  
```  

Once installed, initializing a Devbox environment in current directory is as simple as:  

```bash  
devbox init  
```  

This creates a `devbox.json` file in the current directory, which serves as the configuration file for your Devbox environment.  

### Adding and Managing Packages  

Devbox makes it easy to manage dependencies:  

- **Search for packages:**  
  ```bash  
  devbox search <package-name>  
  ```  

- **Add a package:**  
  ```bash  
  devbox add <package>  
  ```  
  Adding a package updates the `devbox.json` file automatically.  

### Starting and Exiting the Devbox Shell  

To enter a Devbox shell, simply type:  

```bash  
devbox shell  
```
```bash
apple@Apples-MacBook-Pro prefect (v2) $ devbox shell
Info: Ensuring packages are installed.
✓ Computed the Devbox environment.
Starting a devbox shell...
Virtual environment directory doesn't exist. Creating new one...

The default interactive shell is now zsh.
To update your account to use zsh, please run `chsh -s /bin/zsh`.
For more details, please visit https://support.apple.com/kb/HT208050.
(.venv) (devbox) bash-3.2$ 
```

To exit the shell, just type `exit`.  

### First Impressions  

Compared to Docker containers, Devbox feels lighter and more direct, with fewer abstraction layers between the OS and the application code. This makes it especially appealing for tasks that demand high performance, such as running AI models locally.  

### Key Takeaways  

- **Ease of setup:** Devbox is quick to install and configure.  
- **Performance boost:** Fewer layers can mean better performance for resource-intensive applications.  
- **Simple package management:** The `devbox.json` file makes it easy to track and manage dependencies.  

If you’re looking for a tool that balances isolation and performance for local development, Devbox might be worth exploring. I will dive deeper into it in coming weeks and see how it performs across different projects.

