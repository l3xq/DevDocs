# Windows & Ubuntu server interaction workflow

## Use case:
> When one gets tired of _enterprise_ applications breaking up on Linux (usualy with poor explanation why, and no way to fix it). But there is a way to get the best of both worlds IMHO.

> At this point local server is intended only for PHP related work, and a bit of node. So anything that I've installed was towards that goal.

[Windows 10 client]


*Terminal* 

GitBash suggested. Because in SSH it supports xterm colors, ZSH (Oh-My-Zsh) as well as tmux. Here is my CUSTOMPROF settings.


*Mounting Linux FS*


For mounting remote-local drive, I've used [WinSSHfs](https://github.com/Foreveryone-cz/win-sshfs/releases). Which in my case required additional dll `dokan1.dll` to be placed in source dir; it can be found at [Dokany repo](https://github.com/dokan-dev/dokany/releases)
Example mounting connection using SSH pairs.


*SSH*


I've decided to use ssh-keys instead of passwords. So to create new pair, simply insert in terminal:
    `$ ssh-keygen -t rsa -b 4096 -C "email@thyself.com"`
and they can be located at C:\Users\<whoami>\.ssh

Now to enable passwordless flow, you'll have to get pysical contact with your Server, and type:
`$ ssh-keygen -t rsa -b 4096 -C "email@thyself.com"` [Note: Will be required later for ease-of-use Git]
`$ ip addr show`
Write down local IP, and MAC addresses, based on which connection type you've used.

Now on Windows Client type:
`$ cat ~/.ssh/id_rsa.pub` [Note: There might be more elegant solution than this!]
Mark the output of the public key, and Copy it (Ctrl+Ins) to clipboard. Now:
`$ ssh user@IP_ADDR`
This is the only point in which password will be required. Next:
`$ vim ~/.ssh/authorized_keys` and Paste(Shift+Ins) the Public key of Client machine. Esc+wq.
`$ sudo vim /etc/ssh/sshd_config` find _PasswordAuthentication_, uncomment and set to _no_. Find _PubkeyAuthentication_ and set to _yes_.
`$ sudo systemctl reload ssh`

Now with each connection request, password wont be required.


*Firewall*


    TODO: Add UFW suggestions

[Ubuntu Server]


* Download [Ubuntu16.04LTS(headless) Torrent](http://releases.ubuntu.com/16.04/ubuntu-16.04.3-server-amd64.iso.torrent?_ga=2.93931348.89627716.1518119172-582049912.1518119172) -> in installation process choose what works best for you. Following is my suggestion for newcommers to Linux:

1. Choose "Install Ubuntu Server HWM" only if your hardware is really recent (e.g. CPU fabricated less than 4 months ago). Else use standard Kernel.
2. Enable home directory encryption! Be mindful of your security.
3. If you have little experience with Partitioning Disks, choose Guided mode **(LVM)**, which will enable easier resizing, adding and encrypting/decrypting down the road. And if you are experienced - why are you reading this? In my case I had no use for /home, so I've placed partitioned it with following scheme:
most in /var and /usr, with bit of swap and root

4. I suggest that you install security update automatically.
5. Considering that this was my first headless installation of headless Ubuntu, I had new clue what I actually needed preinstalled, so I've choosen everything. Everything that follows after is waiting time for download, and voala!

_Post OS Installation_
6. Script with my specific software needs.



