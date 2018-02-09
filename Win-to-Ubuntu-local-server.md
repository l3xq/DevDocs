# Windows & Ubuntu server interaction workflow

### Table of Contents
- [Use case](#use-case)
- [Server](#server)
- [Windows 10](#win10)
  * [Terminal](#terminal)
  * [Mounting Linux FS](#mounting-linux-fs)
  * [SSH](#ssh)
  * [Firewall](#firewall)
  * [PHP Storm setup](#php-storm-setup)
  * [Workbench 6.3 setup](#workbench-6.3-setup)
    + [MySQL](#mysql)
- [Ubuntu Server](#ubuntu-server)
  * [Post OS Installation](#post-os-installation)
  




<!-- toc -->

## Use case
> When one gets tired of _enterprise_ applications breaking up on Linux (usually with poor explanation why, and no way to fix it). But there is a way to get the best of both worlds IMHO.

> At this point local server is intended only for PHP related work, and a bit of Python/Node. So anything that I've installed was towards that goal.


## Server
The so-called server is refurbished laptop that I've used up until recently _**[Eq: i7-4720HQ, 16GB RAM & SSD]**_. The router is 1G, Huawei-something (the devil is in the details, right? Security is important!)

I was surprised by the unexpected power that headless Ubuntu brought - and it stays extremely quiet while under quite a bit of load.

_**Workflow**_
    At this point, the only job of the server is to serve :troll: in local emulated environment PHP projects, and from time to time some Django/Node. Which I access from my Windows machine, using a combination of emulation and SSH (GitBash, WinSSHfs, PHPStorm, Workbench)...

    For now only unresolved questions are how do I bind my local server with only one internal IP, and how to resolve double V-Hosts after IPs have been sorted out (So only a bit of research...)

## Windows 10 client


### *Terminal* 

**GitBash** suggested. Because in SSH it supports xterm colors, **ZSH** (Oh-My-Zsh) as well as **tmux**. Here are my [GitBashTTY settings](https://gist.github.com/l3xq/07aebaad79e51ed28756e9a4dc2a8012). And demo of solarized GitBash with xterm colors enabled for VIM within SSH:


![Git Bash][git-bash-gif]


### *Mounting Linux FS*


For mounting remote-local drive, I've used [WinSSHfs](https://github.com/Foreveryone-cz/win-sshfs/releases). Which in my case required additional dll `dokan1.dll` to be placed in source dir; it can be found at [Dokany repo](https://github.com/dokan-dev/dokany/releases)
Example mounting connection using SSH pairs.


### *SSH*


I've decided to use ssh-keys _(they can be located at C:\Users\<whoami>\.ssh)_ instead of passwords. So to create new pair, simply insert in terminal:


`$ ssh-keygen -t rsa -b 4096 -C "email@thyself.com"`


Now to enable **passwordless flow**, you'll have to get physical contact with your Server, and type:


`$ ssh-keygen -t rsa -b 4096 -C "email@thyself.com"`    [Note: Will be required later for ease-of-use Git]
`$ ip addr show`


Write down local IP, and MAC addresses, based on which connection type you've used.

Now on Windows Client type:


`$ cat ~/.ssh/id_rsa.pub`    [Note: There might be more elegant solution than this!]


Mark the output of the public key, and Copy it (Ctrl+Ins) to clipboard. Now:


`$ ssh user@IP_ADDR`


This is the only point in which password will be required. Next:


`$ vim ~/.ssh/authorized_keys` and Paste(Shift+Ins) the Public key of Client machine. Esc+wq.
`$ sudo vim /etc/ssh/sshd_config` find _PasswordAuthentication_, uncomment and set to _no_. Find _PubkeyAuthentication_ and set to _yes_.
`$ sudo systemctl reload ssh`


Now with each connection request, password wont be required.


### *Firewall*


    TODO: Add UFW suggestions


### *PHP Storm setup*


In these [pictures (1 to 4)](https://github.com/l3xq/DevDocs/blob/master/ext/pics_PHPStorm_WBench) is shown how to setup remote 'deployment' within PHPStorm.


### *Workbench 6.3 setup*


![Workbench setup][workbench]


#### *MySQL*


If there are any issues connecting Workbench to MySQL server do the following:


`$ sudo vim /etc/mysql/mysql.conf.d/mysql.cnf`

Comment out these lines:
```
skip-external-locking
bind-address
```


## Ubuntu Server


* Download [Ubuntu16.04LTS(headless) Torrent](http://releases.ubuntu.com/16.04/ubuntu-16.04.3-server-amd64.iso.torrent?_ga=2.93931348.89627716.1518119172-582049912.1518119172) -> in installation process choose what works best for you. Following is my suggestion for newcommers to Linux:

1. Choose "Install Ubuntu Server HWM" only if your hardware is really recent (e.g. CPU fabricated less than 4 months ago). Else use standard Kernel.


![Installation Type Standard/HWM][install-type]


2. Enable home directory encryption! Be mindful of your security.


![Encrypt Home Folder][encrypt-home]


3. If you have little experience with Partitioning Disks, choose Guided mode **(LVM)**, which will enable easier resizing, adding and encrypting/decrypting down the road. 


![Guided LVM][part-disks]


And if you are experienced - why are you reading this? In my case I had no use for /home, so I've placed partitioned it with following scheme:

 Mount point  |  Size
 -------------|-------------
 /boot/efi    |  512M
 /            |  40G
 /var         |  250G
 /tmp         |  10G
 /home        |  40G
 [SWAP]       |  30G

4. I suggest that you install security update automatically.
5. Considering that this was my first headless installation of headless Ubuntu, I had new clue what I actually needed preinstalled, so I've choosen everything. Everything that follows after is waiting time for download, and voala!
![Which Software do I actually need?][soft-select]


### *Post OS Installation*


Script with my specific software requirements. TODO GIST






[install-type]: https://github.com/l3xq/DevDocs/blob/master/ext/pics_UbuntuServer/1.PNG
[encrypt-home]: https://github.com/l3xq/DevDocs/blob/master/ext/pics_UbuntuServer/2.PNG
[part-disks]: https://github.com/l3xq/DevDocs/blob/master/ext/pics_UbuntuServer/3.PNG
[soft-select]: https://github.com/l3xq/DevDocs/blob/master/ext/pics_UbuntuServer/4.PNG
[git-bash-gif]: https://github.com/l3xq/DevDocs/blob/master/ext/GitBash.gif
[workbench]: https://github.com/l3xq/DevDocs/blob/master/ext/pics_PHPStorm_WBench/wb-connect.PNG
