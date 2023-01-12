# Server Info 
![images](https://user-images.githubusercontent.com/89369559/208187073-66cd63a9-439c-4fea-a9b1-c60699eac2a0.png)

## CentOS 9
Why centOS 9? Stable and reliable, doesn't need to be updated often.  

    ssh-copy-id -i ed_25519.pub <host>@<domain>  
    dnf update
    chsh -s /usr/bin/zsh

Make sure to get the Extra Packages for Enterprise Linux on first use.

    dnf config-manager --set-enabled crb
    dnf install epel-release epel-next-release
    rpm -ql package-name    #To find out where you installed something.

Optionally install Development Tools

    dnf group install "Development Tools"

### System Info 
2 ways to get os info. 

    hostnamectl
    cat /etc/os-release
    
Hardware info

    sudo lshw -short    #list most hardware  
    sudo lsblk          #list blocks  
    sudo fdisk -l       #more detailed partition data  
    sudo htop           #cpu & ram usage

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
- /home/                  (holds all users as root)
- ~/.ssh                  (holds keys for users ssh auth)
```

``` 
ssh-keygen -t ed25519
ssh -i keyfile root@address
ssh-copy-id -i ed_25519.pub <host>@<domain>  
```

## How to get HTTPS certs

install acme.sh for certs.   
[Install Guide](https://decovar.dev/blog/2021/04/05/acme-sh-instead-of-certbot/)

## How to start an email server

Linode server running cyberpanel.  (I used AlmaLinux, a Redhat distro, cyberpanel doesn't work with Centos9 stream).  
[Youtube Guide](https://www.youtube.com/watch?v=8G93NVWkXZk)  

## Logging Services
`journalctl -u service-name.service`  
or from just the current boot.  
`journalctl -u service-name.service -b`

## Checking Connections
To find open ports
`netstat -tulnp`

## Miscellaneous
Turn off water droplet sound in applications
`dconf write /org/gnome/desktop/sound/event-sounds "false"`
