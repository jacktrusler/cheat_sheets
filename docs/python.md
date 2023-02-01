# Python 
![Python](./svgs/python.svg "Python")
*Weird Snaky Language*

When using python you usually want to set up a [virtual environment](https://realpython.com/python-virtual-environments-a-primer/). 

    python3 -m venv ~/path/to/project
This is because python will install files in your base installation, and you will quickly pollute your site-packages/ folder in your base install. 
Therefore you must activate your virtual environment, when your environment is active, python will install packages only local to that environment.

### Activating/Deactivating your venv

Once inside your project directory do a lil' 

    source ./bin/activate
This activates the venv, and is now safe to install packages

    deactivate
Deactivates the venv, venv must be activated and deactivated everytime you want to use it. 

### Custom Prompt Stuff

If you want a custom prompt you can put this inside your .zshrc

    plugins=(virtualenv)
    function virtualenv_info { 
        [ $VIRTUAL_ENV ] && echo '('`basename $VIRTUAL_ENV`') '
    }

And then add this line to themes (I'm using oh\_my\_zsh)

    vim ~/.oh-my-zsh/themes/some_theme
    PROMPT+='%{$fg[green]%}$(virtualenv_info)%{$reset_color%}%'

There should be a default prompt that tells you the directory you're in before the shell prompt if you've **activated** a virtual environment, so test that out before changing it. [Taken from this stack overflow page](https://stackoverflow.com/questions/38928717/virtualenv-name-not-show-in-zsh-prompt)

### Creating environment folders
[This article has great advice](https://realpython.com/python-virtual-environments-a-primer/#decide-where-to-create-your-environment-folders)  
The way I set up my python virtual environments is as follows (method #1 from the above article). Keeping the venvs inside the project directories themselves.  

    ~/
    │
    └── Coding/
       │
       └── python/
           │
           ├── first-project/
           │   │
           │   ├── src/
           │   │
           │   └── venv_first/
           │
           └── second-project/
               │
               ├── src/
               │
               └── venv_second/

### pip install
If you have activated a virtual environment, pip will install packages into your venv folder, if you
have not then pip will install programs into a default location on your computer. 

    MacOS: /usr/local/lib/python2.7/site-packages
    Fedora37: $HOME/.local/lib/python3.11/site-packages

Note: MacOS will install to a different location if you specify pip3. The way to find where a specific 
package resides is by using the `pip show <package name>` command.

### What the heck is `if __name__ == "__main__"`

This is a way of running python as a script. When you run a script in the terminal you run it in the top-level code environment. So any code after this conditional only runs when you are in a top-level code environment. I.e a shell, where the automatically defined variable `__name__ == '__main__'`

When imported as a module for example, `__name__ == '__<module_name>__'` and the code after this conditional will not run.
