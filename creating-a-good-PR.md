# Creating a good PR

1. Make sure to create a new branch named after the ticket 

        git checkout -b <branch-name>

2. Remove any comments or console.logs
3. In the backend folder run
        pdm run format
        pdm run lint

4. In one of the frontend folders run

        npm run format
        npm run lint

The first one will nicely format your code and the second will make 
sure that there isn't anything extra that shouldn't be there.

5. Stage files to commit

       git add .
   OR
       git add <specific-file>

6. Make sure that in your ~/.gitconfig file looks like this with the
   --wait flag present next to editor. And get the GitLens extension in VSCode. In order to edit the .gitconfig file type

       nano ~/.gitconfig

   This opens the file in a text editor. Then edit it to look like this.

       [editor]

            code --wait

7. If that's set up then go for

       git commit

   This will open a new window - the first vertical line indicates when you reach 50 characters per line,
   while the second one demarcates 80. Keep the commit title to the first one (ideally) and the lines of
   the commit message to the second. When you're done with the message close that window and VSCode will
   carry out the commit - this is what the --wait flag does - it makes it wait until the message is complete.
   Without it the commit fails automatically due to the lack of message.

   The comment in the message should explain why the change was made. It should be clear even if you revisit
   the message in the future. The code itself should demonstrate what was done.

8. Use

       git push origin <branch-name>

   to push the commit to github and click on 'create a pull request'.

IMPORTANT - keep it to one commit per change, so if you are tweaking something you did, but it's not a separate 
change then edit the previous commit that related to it. See below. 

9. Remember to pull from main to stay up to date using

       git pull origin main 
