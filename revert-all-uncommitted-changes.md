The command git checkout ./ is a way to revert changes in the current working directory.

Here’s what it does:

      git checkout ./

This command resets all modified files in the current directory
(and its subdirectories) back to their state in the last committed snapshot 
(the state of the last commit on the branch you are on).

Effects:
Any changes you have made to the files that have not been committed will be discarded.
It does not affect untracked or new files (files that haven't been added to Git using git add).
Important:
This command will erase any local changes that haven’t been committed, so make sure to use
it carefully when you don't want to lose your work. If you want to revert just a specific file or folder, you can run git checkout <file> instead.
