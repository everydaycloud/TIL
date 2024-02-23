## When making changes to a pull request edit the previous commit instead of creating another one.
 If you already created two or more this is how to fix it. You need to have GitLens installed
 in your VScode for this. 
 
## NEVER EDIT COMMITS ON MAIN

1. Use this command to open up your past commits on the branch. The number on the end
   indicates how many previous commits you want to display. If you have two instead of
   one then the number will be 2.

       git rebase -i HEAD~2

   Delete the first commit of the two.

2. Use

       git commit --amend

   to update the latest commit.

3. Push to the branch. You'll need to use --force.

       gir push origin <branch-name> --force

DONE ✅
