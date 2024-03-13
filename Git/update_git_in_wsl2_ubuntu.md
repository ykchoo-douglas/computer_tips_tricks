# How to update Git in Ubuntu + Windows Subsystem for Linux

To check your Git version, run git --version

```
$ git --version
git version 2.25.1
```

To add a PPA (Personal Package Archive), maintained by the Git team for Ubuntu users.

```
$ sudo add-apt-repository ppa:git-core/ppa
```

Update your repository cache, and install Git from the new source

```
$ sudo apt update
$ sudo apt install git
```

Confirm that git --version returns a newer version of Git

```
$ git --version
git version 2.43.2
```
