# Mac OS

How could you possibly need a cheat sheet for mac?? Isn't it the easiest operating system even granny could use it?

Well yes but also no.

## Homebrew

> The Missing Package Manager for macOS (or Linux)

that's the tagline, Linux isn't usually missing a package manager unless you're doing some crazy gamer stuff. 
However, MacOS is definitely missing a package manager, also brew is mostly worse than most Linux alternatives but 
hey, you get what you get. 

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

here's they're awesomely named website for more info. 
[https://brew.sh/](https://brew.sh/)

## Ansible
install python3 via homebrew: `brew install python3`  
The install tells you where python3 was installed on your system, also gives you pip3 (python's package manager)

```
sudo pip3 install pyyaml
sudo pip3 install ansible 
sudo pip3 install ansible --upgrade 
```

`ansible --version`

ansible looks for a hosts and ansible.cfg file in `etc/ansible`, however mac doesn't add this folder by default
so add an `/etc/ansible` folder and add a `hosts` (no extension) and `ansible.cfg` file to it. 
