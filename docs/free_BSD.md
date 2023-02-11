# Free BSD
![Free BSD](./svgs/freeBSD.svg "Free BSD")
*You can't spell based without BSD*

## Why BSD
Brought to you by the boys (and girls) at Berkley, the Berkley Software Distribution. For Free! 

Unix based OS, shares a general schmood with Linux.

    ssh-copy-id -i ed_25519.pub <host>@<domain>  
    sudo pkg [update | install | remove] <package name>
    chsh -s /usr/bin/zsh

## Important directories
`man hier` for a quick look at important directories.
A lot of stuff gets downloaded into the `/usr/` directory

    /usr/local/etc   (kinda similar to etc in linux)

## Commands

    pw groupadd
    pw user show <user>
    pw group show <user>
    pw usermod -G <group> <user>
    pw groupmod <group> -M <user>

### Misc
`ls -G` to colorize your directories.
`service <name> restart` service runs the init script if provided by <name> program.
