# Why a global gitignore?

A global gitignore will ignore extra files that you may often use in different projects 
(such as playground.sql etc), but that you don't want to upload to github and that you 
don't want to include in the .gitignore in the projects as other people won't know what
these files are.

The global gitignore will ignore listed files across your computer, while the local 
gitignores will disregard the listed files inside the folder that they are in.

1. Tell git to use the global .gitignore

       git config --global core.excludefile ~/.gitignore

2. Actually create that file at the root of the home directory

       touch .gitignore

3. Add whatever you want to ignore across your computer. Open the file with

       nano ~/.gitignore

   and add whatever you want (e.g. playground* to ignore all playground files).
