You can create an alias to open a file in your default editor by appending the following line to your .gitconfig file:

edit = "!f() { $(git config core.editor) -- $@; }; f"
Then, git edit foo.txt will open the file foo.txt for editing.

It's much easier to open .gitconfig with git config --global --edit and paste the line, rather than figure out how to escape all the characters to enter the alias directly from the command line with git config alias.edit "..."

How it works
! starts a bash command, not an internal git command
f() {...}; starts a function
$(git config core.editor) will get the name of your editor, from the local config, or the global if the local is not set. Unfortunately it will not look in $VISUAL or $EDITOR for this, if none is set.
-- separates the editor command with the file list. This works for most command line editors, so is safer to put in. If skipped and the core.editor is not set then it is possible that an executable file is executed instead of being edited. With it here, the command will just fail.
$@ will add the files entered at the command line.
f will execute the function after it is defined.
Use case
The other answers express doubt as to why you would want this. My use case is that I want to edit files as part of other git functions that I am building, and I want to edit them in the same editor that the user has configured. For example, the following is one of my aliases:

reedit = "!f() { $(git config core.editor) -- $(git diff --name-only $1); }; f"
Then, git reedit will open all the files that I have already started modifying, and git reedit --cached will open all the staged files.