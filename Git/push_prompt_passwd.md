# Why is Git always asking for my password?

If Git prompts you for a username and password every time you try to interact with GitHub, you're probably using the HTTPS clone URL for your repository

Using an HTTPS remote URL has some advantages compared with using SSH. It's easier to set up than SSH, and usually works through strict firewalls and proxies. However, it also prompts you to enter your GitHub credentials every time you pull or push a repository.

When Git prompts you for your password, enter your personal access token. Alternatively, you can use a credential helper like `Git Credential Manager`. `Password-based authentication for Git has been removed` in favor of more secure authentication methods.

It is `recommended to install the latest Git for Windows` in order to share credentials & settings between WSL and the Windows host. Git Credential Manager is included with Git for Windows and the latest version is included in each new Git for Windows release. During the installation, you will be asked to select a credential helper, with GCM set as the default.

If GIT installed is **>= v2.39.0**

```
git config --global credential.helper "/mnt/c/Program\ Files/Git/mingw64/bin/git-credential-manager.exe"
```
