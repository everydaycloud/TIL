# Moving changes to another branch 

If you've already created a commit on a branch, but then it turns out it should be on another branch.

    git reset HEAD~1

This uncommits the last commit.

    git stash

Saves the changes locally, but not in git.

    git checkout <the-correct-branch>

    git stash pop

Adds the changes to the new branch and then you can commit them again.
