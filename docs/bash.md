# Bourne Again Shell 

Shell program named after Jason Bourne* 
*not really

## Soft and Hard Links

Soft links affect the inode, the inode is all the meta info about each file on a system, analogus to
an index. 

view files inodes with `ls -lia`

*create a soft link*

    ln -s <file-to-link> <soft-link-name>
A soft link has a *different* inode number and permissions, it points to your file, analogus to a shortcut. 
If you change the symlink contents it will also change the file contents. However if you delete the 
file it points to, the symlink will not allow you to open the file, as none exists. It is now just an 
empty pointer. Also this allows you to link directories.

*create a hard link*

    ln <file-to-link> <hard-link-name>
A hard link has the *same* inode number and permissions, it essentially mirrors your file, when you make changes to the 
original file, or the hard link, both copies will change. Removing either will not destroy the other.
Hard links cannot link directories, only files.
