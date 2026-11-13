git reset HEAD~1

psql -d clinic -U cura  -f playground.sql > playground.txt

Shift + tab —> to remove indentation in vs code for a block of text 

print(type(thing)) —> print the type of a variable 

open -e <path to file> 
Much easier than nano <path-to-file>

SELECT 
  column_name, 
  data_type, 
  is_nullable, 
  column_default
FROM 
  information_schema.columns
WHERE 
  table_schema = 'public' 
  AND table_name = 'users';

Ctrl+R  —> command search in terminal 

To see what changed compared to the last committed version 
git diff 

git grep <search term> —> search the repo for that term 

For debugging python code 
breakpoint() —> put this where needed 
Then run the tests and it will stop at that point
n + enter —> goes to the next line
print(something) + enter —> you can run python code within this 
Command + c —> to exit 

GET THE LATEST VERSION OF ALL REPOS AT THE SAME TIME
Run this in the parent directory

for dir in */.git(N); do
  repo=${dir%/.git}
  echo "Updating $repo"
  (cd "$repo" && git pull)
done

To undo any changes to a file
git restore <file path OR .> 

rm -fr node_modules && pnpm i?

To search through everything in the folder you are in 
The search term here is ‘zshrc’
ls -a ~ | grep zshrc

PULL SCRIPTS
\copy (SELECT * FROM persons) to 'C:\temp\persons_client.csv' with csv
