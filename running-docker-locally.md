Have Docker running on your machine for this


build the app (run in root directory where the Dockerfile is)

      docker build -t staff-bjj-app .

or 

      docker build --no-cache -t staff-bjj-app .
      

run the app pulling the variables from development.env

      docker run --network=host --env-file backend/development.env --name my-app -p 8080:8080 -d  staff-bjj-app

stop the app and remove it if you will be making changes

      docker stop my-app
      docker rm my-app

build it again etc.
