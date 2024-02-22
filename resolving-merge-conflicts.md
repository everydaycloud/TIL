#Solving merge conflicts

If after creating a PR you get this message 

![resolving-merge-conflicts](https://github.com/everydaycloud/TIL/assets/126498815/99d913bf-e476-48f8-ae47-bf821baa5e7c)

don't do any edits online through the options that github gives you! This can mess things up.

1. If you have already tried to resolve it online on that github page fix that first.
   Push up to the PR branch again to remove your resolution.

     git push origin <branch-name>

   This should return things to the state from the picture above.

2. Checkout to main and pull the latest version of the code.

     git checkout main
     git pull origin main

3. Checkout back to the branch and rebase.

     git checkout <branch-name>
     git rebase main

4. Once you do this, you'll be able to see the conflicts in your code editor
   and resolve them there. Once you're done use run any formatting and linting things
   that you may need to run and then type

     git add -u //this will only add updated files
     git rebase --continue

6. Edit the previous commit to incorporate these changes

     git commit --amend --no-edit

7. You'll need to force update the branch that you created the PR from.
   NEVER do this with main.

     git push origin <branch-name> --force

8. ALL DONE ✅ The commit should now be able to be merged. If the checks are failing
   format and lint again and then add files, update the commit and force push again.


   
