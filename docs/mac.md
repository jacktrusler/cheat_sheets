# Mac OS
![Macintosh](./svgs/apple.svg "Macintosh")
*Nothin more posh than a Macintosh*

How could you possibly need a cheat sheet for mac?? Isn't it the easiest operating system even granny could use it?

Well yes but also no.

## Homebrew

> The Missing Package Manager for macOS (or Linux)

That's the tagline, Linux isn't usually missing a package manager unless you're doing some crazy gamer stuff. 
However, MacOS is definitely missing a package manager, also brew is mostly worse than most Linux alternatives but 
hey, you get what you get. 

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

here's they're awesomely named website for more info. 
[https://brew.sh/](https://brew.sh/)

## Disable Gatekeeper

Sometimes when you download something from the internet MacOS won't let you open it because it 
is from an unknown source and could be malware. If you're not afraid to live dangerously this is the 
command to disable Gatekeeper:
`sudo spctl --global-disable`
and re-enabling it is: 
`sudo spctl --global-enable`

## Stupid .DS_Store file
When you open a folder using finder, macOS automatically writes a `.DS_Store` file. This file stores 
custom attributes like folder view options, icon positions, visual information, spotlight comments and
more. The problem is that this file also contains metadata about that folder, which you don't want to expose. 
When you're commiting to a repository it is also annoying to have a .DS_Store file in every folder,
but .gitignore usually is ignoring the .DS_Store. If you want to get rid of the file use this command: 

`defaults write com.apple.desktopservices DSDontWriteNetworkStores true`

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

## Optional Apps I like

### Raycast
Really nice program that you can use instead of spotlight search. Need to change default bindings to 
open raycast instead of spotlight. There's a pretty nice tutorial on the app to show you how to use it.   
[raycast](https://raycast.com)

### AltTab
Nice window tabbing utility, I basically only use it to see full screen windows and tab through multiple 
chrome instances.   
[alt-tab](https://alt-tab-macos.netlify.app/)

### Karabiner Elements
Program that runs on startup that remaps keys, I like remapping capslock -> esc and visa versa.  
[karabiner-elements](https://karabiner-elements.pqrs.org/)

### Linear mouse
To turn off mouse acceleration and turn up pointer speed past what mac allows.    
[linear-mouse](https://linearmouse.org/)
