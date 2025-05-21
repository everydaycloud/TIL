## Our deployment

The database is built by executing migrations in order and then inserting data 
from data.py.
In production every migration runs only once and then new ones are ran on top. 
This is why we can't edit past migrations - it just wouldn't work, so we need
to build on top of the past ones. 
In development when we recreate the db the whole db is erased and built from 
scratch by executing the migrations one by one, but we should still just add 
new migrations and not attempt to edit any past ones.


    SELECT * FROM schema_migration;

Tells you what the shape of the db is at the moment.

### Terraform & CD

Terraform just holds the info about existing services and passwords for them, but
nothing else.

It's the CD action that creates a container (a snapshot of the app in its current 
state) and adds it to ECR to be available. 

ECR - elastic container repository - where the images of the app from different 
times are stored 
ECS - elastic container service - runs the image from above

CD 'push job' just pushes the image to ECR
CD 'deploy job' deploys an image to ECS by tagging it as 'PROD'

So the CD action adds the image to available images in ECR and marks it as prod,
so that the ECS will know which one to run as it will always run the one tagged as 'prod'.

This is why we can roll back a commit by re-running the deploy job for the 
commit before. This means that the older image will get tagged as 'prod' and thus the 
older verion will be displayed.

### CAN'T DO THIS IF THE LAST COMMIT CONTAINED A SUCCESSFUL MIGRATION. 

If we roll back this way, we also need to let everyone know to stop them from deploying
newer commits. Deploying new code again after rolling back will re-deploy everything
before it, so we need to have fixed the bad commit before this is done.

'Actions' repo has all these deployment procedures specified

Infrastructure code just creates the ECS and ECR in AWS, but doesn't do anything else.

'ECS task'  - is when an image is run in the container

### Swapping the old code for new code on a deploy


We always use 4 ECS tasks at the same time. When we deploy new code this will spin up a
new task with the new code and then shut down one of the old ones and it will continue 
to do this until all the old code is replaced. 

### This is called a 'canary deployment' 

"blue-green" deployment is another way of doing it, but it means that you swap everything
at the same time, meaning that if it's broken then nothing will work. 

Canary deployment means that if the task with the new code fails then it won't continue
deploying.

### More info 
'Site reliability engineering' book 
