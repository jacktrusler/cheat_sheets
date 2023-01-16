# Vi IMproved (and beyond!)
Before we start I wanted to say how fun it is being able to look in the past and actually use the 
exact same software that was used. Like when you do a report on printing it's rather hard to get
your hands on a printing press... Anyway, without further ado lets talk about... 
*(scroll for effect)* 

## Ed
Maybe unsurprisingly, the story of vi starts here. Ed was 
one of the first text editors, it was developed by Ken Thompson in August 1969. It was influenced by 
qed text editor, which was developed at UC Berkeley, Ken's alma mater. Thompson implemented regular
expressions on qed, which was notable.

Ed is a line based text editor with no GUI, just simple command line options.
*(on fedora, write an express app using ed then demonstrate g/re/p)*
`g/re/p` was influenced by an ed command **g**lobally find by **re**gex and **p**rint.  
[some common ed commands](https://www.computerhope.com/unix/ued.htm)


## Vi origins
The text editor that caused us to start down this twisted (righteous?) path was called vi, written 
by Bill Joy in 1976. This was the **vi(sual)** mode for a line editor called **ex**, and was released 
as part of the first Berkely Software Distribution (BSD) / Unix release in March 1978.

"**ex** stands for extended, because it was originally an extension of the simple line editor **ed**. Similarly, "vi" stands for visual, because vi is the "visual" (full-screen) editing mode, which was eventually added to ex."  
-- [computerhope.com](https://www.computerhope.com/unix/uex.htm)

## Vi
"The commands ex and vi point to the same program, started in different modes. You can start ex by running vi -e, or you can start vi by running ex -v. Additionally, from within ex, you can start vi with the visual command (or vi for short). From inside vi, you can start ex with the command Q."

I found some preserved source code for a legacy version of vi:  
    https://github.com/n-t-roff/heirloom-ex-vi  
Most Linux distros come with a binary called vi but it is really vim in disguise with a few features
taken out. 

    ssh jack@jacktrusler.com

I installed Vi on BSD because it feels like Bill Joy would've liked it that way, plus installing it
on Linux is actually pretty annoying because of terminal features.

[Here's a list of original Vi commands](https://www.cs.colostate.edu/helpdocs/vi.html#:~:text=To%20use%20vi%20on%20a,which%20you%20may%20enter%20text.)

commands that work that list missed: 

    f -- find char in line
    ; -- next
    , -- previous
underlying ex commands: 

    :%s/foo/bar/g -- global search and replace

*(on jack@jacktrusler.com write a node cli tool in vi)*

## Vi IMproved

## NeoVim
Here's the About on github.com/neovim/neovim:  
> Vim-fork focused on extensibility and usability  

And here's the first paragraph of the readme:  

> Neovim is a project that seeks to aggressively refactor Vim in order to:   
> - Simplify maintenance and encourage contributions
> - Split the work between multiple developers
> - Enable advanced UIs without modifications to the core
> - Maximize extensibility

I think Neovim does a great job on delivering on all of these goals honestly, and part of the reason 
is because of their decision to use Lua.  

### What is Lua
[I stole the notes from this video, one of the core maintainers of Neovim](youtube.com/watch?v=IP3J56sKtn0)  
Lua is a language created in Brazil in 1993. 

Neovim decided to embed the Lua language into the program. There were 5 main reasons for this.
1. simplicity (reference manual is only 100 pages, and covers the entire language, standard lib, and C api)
2. small size (200kb binary size for Linux)
3. portability (Lua is implemented in ISO C, which can be used basically anywhere)
4. embeddability (Lua functions can be used inside of vimscript) 
5. speed (LuaJIT is only 10-40% slower than C, which is extremely fast)

For a text editor, these goals seem like an obvious win. Simplicity is really also key because it 
allows developers to quickly get up and running with a relatively popular and simple language instead 
of having to learn vimscript which only really has one use, which is to extend vim. 
