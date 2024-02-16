# Creating a good PR

1. Make sure to create a new branch named after the ticket 

    git checkout -b <branch-name>

2. Remove any comments or console.logs
3. Run 

    npm run format
    npm run lint

The first one will nicely format your code and the second will make 
sure that there isn't anything extra that shouldn't be there.

4. Stage files to commit

   git add .
   OR
   git add <specific-file>

5. Make sure that in your ~/.gitconfig file looks like this with the
   --wait flag present next to editor. And get the GitLens extension in VSCode

   [editor]

        code --wait

6. If that's set up then go for

       git commit

   This will open a new window - the first vertical line 
   
