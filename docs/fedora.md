# Fedora
![distributor-logo-fedora-icon](https://user-images.githubusercontent.com/89369559/208185875-cbe96cb7-de32-4294-8d55-2e54f983dbce.png) 
Redhat's desktop environment for part-time nerds.

[Fedora Workstation Download](https://getfedora.org/en/workstation/download/)  
[Fedora Silverblue Download](https://silverblue.fedoraproject.org/download)  

**Fedora workstation** is the official distribution, while **Fedora Silverblue** is a version of Fedora 
that makes the operating system immutable. That is, you can't go in and mess with the OS files, generally 
better for new users or people that just want an operating system that works and don't want to deal with 
customizing the internals. However, the **dnf** package manager doesn't work the same, instead you have to 
use **OSTree**, which is not a package system, summarized as "git for operating system binaries". 

Basically dnf installs packages that are usually composed of partial filesystem trees with metadata 
and scripts will then assemble the programs on the client machine. OSTree only deploys *complete* 
filesystem trees and thus does not need to know what dependencies are available. This makes it ideal 
for an isolated OS environment like silverblue.

[Here's more info if you're curious](https://ostree.readthedocs.io/en/stable/manual/introduction/)

Back to **Fedora Workstation**  
Dandified Yum **-- dnf --** Why Dandified? I haven't a clue and its tough to find on the internet.
I guess it's because it's like **-- yum --** but fancier?

### Installing Stuff
Official Red Hat package manager file **.rpm** is best usually, then **flatpak**, then **snap**. 

In general flatpak is better than snap for desktop fedora, try to find flatpak packages if possible. 

    sudo dnf install -y flatpak
    flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

What is flatpak/snap? 
A distribution independent package format designed to run apps in a virtual sandbox
that contains all the dependencies needed to run the software. Flatpak is built on top of OSTree. 

  - flatpak is a packaging format for desktop GUI apps, snap has larger scope
  - snaps can be used for terminal programs and are best(tm) suited for server applications
  - snap generally spawn daemons and auto update and have a larger footprint

Check software store in the top right to see what type of file you're downloading. 

For information on the filesystem hierachy standard, aka where Fedora puts files by convention  

    opt/
    usr/lib
    usr/local/lib
    usr/bin
    usr/local/bin
are the most common, run `man hier` for more info.  

Finally, you can `rpm -ql package-name` To find out where you installed something.

### Keyboard commands
Has all the basic commands that are mega handy on Mac and Windows:
(Gnome kinda bussin ong)

  - 3 finger swipe L - R to change screen.
  - 3 finger swipe UP for explode windows, again for applications.
  - Windows key also explodes windows start typing to search.
  - Windows key + 1 2 3 etc to start your taskbar applications and switch to them.
  - Windows key + arrow keys to fill window, move window Left - Right, shrink window.
  - Windows key + Shift + arrow keys to move applications between screens.

### Custom Keyboard Commands
download input-remapper
https://github.com/sezanzeb/input-remapper
must be compiled from python 3

use GUI or:  
systemctl start input-remapper (this looks for a fedora.json file with already mapped keys)  

`input-remapper-control --command autoload`

### Misc
MSteams:: slap this into chrome to make screen sharing work:  
`chrome://flags/#enable-webrtc-pipewire-capturer`  

grep wifi passwords: `sudo grep psk= /etc/NetworkManager/system-connections/*`

Turn off water droplet sound in applications:  
`dconf write /org/gnome/desktop/sound/event-sounds "false"`  

### Signing Pdfs in Fedora
[Here's the normal Adobe Reader](https://www.if-not-true-then-false.com/2010/install-adobe-acrobat-pdf-reader-on-fedora-centos-red-hat-rhel/) download instructions for Fedora, but if the signature field is not available it won't let you sign. 
It's pretty annoying, the best solution i've found is: 
`dnf install xournalpp`

opening it in there then you can draw a custom signature and export the whole file. Gimp works but only for 1 page files.

### Setting up VMs with Qemu/KVM
KVM already built into the kernal so use this instead of Virtual Box (unless you like virtual box then w/e)

[here's the video](https://www.youtube.com/watch?v=zQHkk9Jjhls)
TL:DW

    dnf groupinfo virtualization
    sudo dnf group install --with-optional virtualization

start the daemon
    
    sudo systemctl enable --now libvirtd

make sure virt-manager is installed

    sudo dnf install virt-manager

If everything installed correctly you should be able to open the GUI tool: `sudo virt-manager`
Grab an iso and follow the install steps, put the iso in: `/var/lib/libvirt/images`
If your root partition isn't very large (like mine) I would recommend putting your images and storage 
into your home directory... I put mine in 

    /home/jack/lib/libvirt/images
    /home/jack/lib/libvirt/storage

To configure storage go to: 

    Edit > Connection Details > Storage

And add new storage with the amount of space you want to reserve for the OS. When installing a VM use 
one of these storage partitions you made by browsing for it in the "custom" option.

After you install, if you want to change your config (for things like storage or RAM size), you can: 

    sudo virsh edit <what you named your OS install>

### Getting Connected to a VPN
Fedora comes with built in [NetworkManager-openconnect.](https://src.fedoraproject.org/rpms/NetworkManager-openconnect)  

This means you can connect to a Cisco Network using the built in vpn settings manager under   
Settings > Network > (Press the + to add a network)
