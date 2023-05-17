# Server Info 
![Server Info](./svgs/servers.svg "Server Info")
*How to be a Redhat aficionado*

## CentOS 9
Why centOS 9? Stable and reliable, doesn't need to be updated often. I like Redhat.  

### First steps:  

**Give ssh access from host PC to remote server.**
```bash 
ssh-keygen -t ed25519
ssh -i keyfile root@address
ssh-copy-id -i ed_25519.pub <host>@<domain>  
```
**Make sure to get the Extra Packages for Enterprise Linux on first use.**
```bash
dnf config-manager --set-enabled crb
dnf install epel-release epel-next-release
rpm -ql package-name    #To find out where you installed something.
```
**Download and use mlocate.**
```bash
dnf install mlocate
updatedb
```
**Download a lot of soy**
```bash
dnf update -y && dnf install -y gcc g++ curl git neovim vim zsh wget nodejs npm
chsh -s /usr/bin/zsh
```
**Optionally install Development Tools and Oh-My-Zsh**
```bash
dnf group install "Development Tools"
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
**Add this line in .zshrc to stop auto updates**
```bash
zstyle ':omz:update' mode disabled # Must come before sourcing oh-my-zsh.sh
source ~/.oh-my-zsh/oh-my-zsh.sh
```

### System Info 
2 ways to get os info. 

    hostnamectl
    cat /etc/os-release
    
Hardware info

    sudo lshw -short    #list most hardware  
    sudo lsblk          #list blocks  
    sudo fdisk -l       #more detailed partition data  
    sudo htop           #cpu & ram usage
    sudo df -h          #storage used

### Swap Memory
Sometimes you'll run out of RAM (if you bought the cheapest 1GB droplet) 
when trying to run basic tasks, the solution is to add swap memory. 

    dd if=/dev/zero of=/mnt/2GB.swap count=2048 bs=1024K                                                                     1 ↵
    mkswap /mnt/2GB.swap
    chmod 600 /mnt/2GB.swap
    swapon /mnt/2GB.swap

Then the tasks should run fine.   

NOTE: Be careful with the `dd if=/dev/zero` command, this command 0's out your hard drive 
(wipes it literally by flipping everything to 0) so make sure when you're using it you don't `/dev/zero` 
an important directory _smile_. In this case we're creating a swap file `mkswap` with 2GB worth of 
space, filled with 0s, then changing permissions and turning the `swapon`.

[Here's a great article on dd](https://www.baeldung.com/linux/dd-command)

## Important directories

`man hier` for a quick look at important directories.

**Etc (etcetera) is where configuration files are stored**

```
/etc/nginx/conf.d           (nginx config)
/etc/pki/tls/               (where to store certs ---Public Key Infrastructure--- ---Transport Layer Security---)
/etc/systemd/system         (Create a file this directory example.service and then start it to run on startup)
/usr/lib/systemd/system     (where you put specific user services)
```

```
[Unit]
Description=JSON Bateman facts
After=network.target
StartLimitIntervalSec=0
Requires=Nginx.service postgresql.service

[Service]
Type=simple
WorkingDirectory=/home/jsonb/json_b
ExecStart=/usr/bin/node ./app.js
Restart=always
RestartSec=1
User=jsonb
Environment=ENV_VAR=anything (or create a file with no extension with all the environment variables you want the service to have)

[Install]
WantedBy=multi-user.target
```

After you save the service reload all daemons and enable your new daemon. 
```
sudo systemctl daemon-reload
sudo systemctl enable your.service
```

**Var standard subdirectory where the system writes data during operation**

```
- /var/www/html           (save statically served files here)
- /var/lib                (lots of programs and options)
- /var/log                (log files stored here)
```

**Others**

```
- /root/                  (holds root user)
- /home/                  (holds other created users)
- ${HOME}/.ssh            (holds keys for users ssh auth)
```

## Permissions
Every file on an ext filesystem has:

1. An owner user  
2. An owner group  
3. Permissions for this user, this group and everybody else.  

If you're setting rwx group permissions on a file, only the owner group of that file can read/write/execute it. You can however change who owns the files or directories with the chown and chgrp commands.

chmod means **ch**ange **mod**e...  

| read    | write    | execute    |
|---------------- | --------------- | --------------- |
| 4    | 2    | 1    |
| r    | w    | x    |
  
`chmod [options] permissions [target]`  
order is user/group/others so `chmod 754 [target]`  
**user**: 4 + 2 + 1 read + write + execute  
**group**: 4 + 1 read + execute  
**others**: 4 read  
NOTE: `chmod 700 <target>` removes all permissions from group, others  
  
The semantic version is `chmod u=rwx,g=rx,o=r [target]`  
**user**: u=rwx read + write + execute  
**group**: g=rx read + execute  
**others**: o=r read  
NOTE: `chmod go= <target>` removes all permissions from group, others   

For the number version it is perhaps easier to think of the permissions as bits:  

    100 - read  
    010 - write  
    001 - execute  

`chown` a command meaning **ch**ange **own**er

    chown <user> <file1> <file2>...
    chown -R <user> <somedir>...

`chgrp` a command meaning **ch**ange **gr**ou**p**

    chgrp <group> <file1> <file2>...
    chgrp -R <group> <somedir>

## Database on the Server

-L flag binds the address of a local socket to a remote socket.
This command exposes a ssh tunnel between selected ports in this fashion: 
<local_socket>:<host>:<remote_socket> <server>
`ssh -L 3306:127.0.0.1:3306 root@jacktrusler.com`

    
## How to get HTTPS certs

install acme.sh for certs.   
[Install Guide](https://decovar.dev/blog/2021/04/05/acme-sh-instead-of-certbot/)

The general overview is this, acme.sh is a script that runs to ask a certificate authority to confirm
the location of a website. It does so by sending a token to the .well-known/acme-challenge 
directory, then whatever certificate authority you're using (zeroSSL by default, I changed it to Let's Encrypt) 
will try to reach the token, if it succeeds you get a certificate. Change cert authority with this command:  
```
acme.sh --set-default-ca  --server  letsencrypt
```
After you get a certificate, you need to place them in a location where nginx can find them. 
I placed mine in:   
```
/home/jack/www-data/certs
```
finally change and reload Nginx conf:  
```nginx
server {
  listen 80;
  server_name www.jacktrusler.com jacktrusler.com;
  return 301 https://jacktrusler.com;
}

server {
  listen 443 ssl http2;
  server_name jacktrusler.com;
  ssl_certificate /home/jack/www-data/certs/jacktrusler.com/fullchain.pem;
  ssl_certificate_key /home/jack/www-data/certs/jacktrusler.com/key.pem;

  location / {
  root /usr/local/www/jacktrusler.com;
  index index.html index.htm;
  }
}
```
Note: `/usr/local/www` is the preferred path for freeBSD to put www files. Linux uses `/var/www` by convention.

## How to start an email server

Linode server running cyberpanel.  (I used AlmaLinux, a RHEL distro, cyberpanel doesn't work with Centos9 stream).  
[Youtube Guide](https://www.youtube.com/watch?v=8G93NVWkXZk)  

## Logging Services
`/var/log` contains many of the systems log files.  
To see the last 100 lines of system log info: `sudo tail -n -100 /var/log/messages`  

Running daemons will often contain important info too. (like node processes, or nginx logs)  
To see logs from running daemons: `journalctl -u service-name.service`  
or from just the current boot: `journalctl -u service-name.service -b`

## Checking Connections
To find open ports  
`netstat -tulnp`  
To see if a website is running  
`nslookup <website>`  
If you don't have nslookup install it with  
`dnf install bind-utils`

## Miscellaneous
Turn off water droplet sound in applications
`dconf write /org/gnome/desktop/sound/event-sounds "false"`

# RHEL 9.1 

The system has to be registered with the entitlement server. Basically you have to tell Redhat
that you have made an account to access the software. 

    sudo subscription-manager register
    sudo dnf update

Check if you have repos.

    sudo dnf repolist
If not, change the subscription manager to allow repos. 

    sudo vi /etc/yum/pluginconf.d/subscription-manager.conf
Enable CodeReady Linux Builder repository (crb)

    sudo subscription-manager repos --enable codeready-builder-for-rhel-9-$(arch)-rpms
Install the Epel

    sudo dnf install \
    https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm

