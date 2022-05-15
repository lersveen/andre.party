---
layout: post
title:  "How to greet an unknown linux system"
categories: blog
tags: dev linux
---

Sometimes you find yourself (with an SSH connection or otherwise) in the shell of a system you know nothing about beyond its hostname – what to do?

There are many ways to go about this, but here is my quick cheatsheet to get you started. Excuse the terrible analogies.

## Hi, how are you?
Start politely by checking how long the system has been up, load, what users are logged in and what they are doing:
```
w
```
The command `w` combines several other Unix programs: `who`, `uptime` and `ps -a`.

## Who are you? <small>(Determine OS version and hostname)</small>
Type any one of the following commands to find OS name, version and hostname:
```sh
cat /etc/os-release
lsb_release -a
hostnamectl
```

Some more for Unix systems (including MacOS):
```sh
uname -a
hostname
sw_vers
```

## What are you packing? <small>(Inspect hardware)</small>

If you want to know some basics about the system hardware, try these:
```sh
lscpu
lshw –List Hardware
hwinfo
cat /proc/cpuinfo
cat /proc/version
```

It's also nice to get an overview of the filesystem disk space usage:
```
df -h
```

## So, what do you do? <small>(list running services)</small>

You first need to determine the system manager:
```sh
pstree | head -n 5
```
The very first process in the tree should be the system manager. It's most likely `systemd`, `upstart` or `init` (SysVinit).


### SystemD
```sh
sudo systemctl list-units --type=service --all
```

### Upstart
```sh
sudo initctl list
```

### SysVinit
```sh
sudo service --status-all
```

## But what do you really do? <small>(List running processes)</small>
Get a real-time overview of running processes:
```
top
```

List all running processes:
```
ps ax
```


## Anyway, what's in your bag? <small>(List installed software)</small>

You first need to determine the package manager. This [depends on the Linux distribution](https://en.wikipedia.org/wiki/List_of_software_package_management_systems#Linux).

Try checking for a few popular ones:
```sh
which apt-get
which pacman
which nix-env
```
If none of these are installed – look up what package manager the linux distribution typically comes with.

### apt-get/dpkg (Debian, Ubuntu)
List manually installed packages:
```sh
apt-mark showmanual
```

List all installed packages:
```sh
dpkg --get-selections | grep -v deinstall
```

Get the full install history:
```sh
zgrep -hE '^(Start-Date:|Commandline:)' $(ls -tr /var/log/apt/history.log*.gz ) \
| egrep -v 'aptdaemon' \
| egrep -B1 '^Commandline:'
```

### pacman (Arch)
List manually installed packages:
```sh
pacman -Qe
```

List all installed packages:
```sh
pacman -Q
```

Get the full install history:
```sh
/var/log/pacman.log
```

## OK, what now?

By now you should hopefully have all the clues you need to look up how to do whatever you were planning to do on the (previously unknown) linux system. 

Good luck. Be nice.