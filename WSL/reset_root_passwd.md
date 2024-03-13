# How To Reset Linux Password in WSL

first, identify the executable file for the Linux distribution you're using with the WSL. Normally sitting in this directory

`C:\Users\<username>\AppData\Local\Microsoft\WindowsApps>`

type `dir` to look into the directory files. Example shows at below

`-a---l         19/7/2022   8:53 AM              0 ubuntu.exe`

second, change the login Ubuntu session to root user

`C:\Windows\system32> ubuntu config --default-user root`

third, start the wsl2 Ubuntu session and type the following commands:

`root@YKCHOO-ASUS:~# passwd ykchoo` _type in the new password for ykchoo user_

`root@YKCHOO-ASUS:~# passwd` _to change the root password_

forth, exit the wsl2 Ubuntu session. Go to PowerShell or Cmd and continue the following commands:

`C:\Windows\system32> ubuntu config --default-user ykchoo`

fifth, start the wsl2 Ubuntu session and the ykchoo user is login

`ykchoo@YKCHOO-ASUS:~$`
