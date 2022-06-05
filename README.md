# Dockerizing Spring Boot API and Angular Frontend

## Plan
Using the Docker extension in Visual Studio Code (VSCode) allowed me to not have to type in the docker commands each time I wanted to build an image or container, although I have added them in this document for reference.

The plan was to create two micro services in docker one being the angular front end and the second being the spring boot api and then run them both using docker compose.

A micro service is just a docker container. However a series of micro services can all be started and run using one command, the docker-compose command instead of running each container individually.

A common use is in applications like the one provided with a connected front end, backend and a database.

### Angular Frontend
To containerise the front end, I created a Dockerfile at the base of the angular frontend folder `\spring-boot-projects\spring-boot-modules\spring-boot-angular\src\main\js\application`. Then was to put in the necessary code in the Dockerfile to create the image. The image was then created using the docker extension in VSCode but could have also been done with this command `docker build -t angular:latest . `. Then the container was created using the image, again this was done in VSCode using the docker extension but could have been done using this command, `docker run -d angular:latest`. On successfully completion of these steps the first micro service will be completed.

### Spring Boot API
Containerising the backend (the spring boot API) would follow the same steps as the previous micro service. I created a Dockerfile at the base of the spring boot API folder, `\spring-boot-projects\spring-boot-modules\spring-boot-angular\src\main\java\com\baeldung`. Then was to put in the necessary code in the Dockerfile to create the image. The image was then created using the docker extension in VSCode but could have also been done with this command `docker build -t springboot:latest . `. Then the container was created using the image, again this was done in VSCode using the docker extension but could have been done using this command, `docker run -d springboot:latest`. On successfully completion of these steps the second micro service will be completed.

### Launch them together
Lastly the final step was to create a docker-compose.yaml file to run both the angular frontend and spring boot api with one command. This file would find the images and dockerfile respectively, state the link between them and launch both services, with this command `docker-compose up`

### Hierarchy Structure
Below is a simplified hierarchy structure of the application after the addition of docker
    
    
    Base
    ├── (SpringBootAPI)   
        ├── Dockerfile             
    ├── (AngularApp)
         ├── Dockerfile                 
         ├── nginx.conf           
    ├── docker-compose.yaml
    └── README.md


## Angular Frontend Dockerfile Implementation
As it was a node based project, docker took a base image for docker’s official node image.

Looking at the brief/instructions provided I knew that node and angular CLI both had to be installed on the container for the front end to run.

The brief also stated that when the server is up and running it will run on port 4200.

The brief also provided the start, build and test commands.

I ran into a few initial issues with the dockerfile.

`npm install` wouldn’t work but `npm install —production —silent` did.

This allowed the image to successfully be made. But when creating the container to test that it worked I got this error.

![image](https://user-images.githubusercontent.com/71643186/172043647-85432332-03f8-402a-8b08-304cd996dc57.png)

I then when through looking at the `packages.json` I thought that possibly the way it was written the cmd might be `npm start` or just `start`. I then checked the `ng build` in `packages.json` and it was also set to production so that was causing any issues.

Error with running `CMD ["start"]` :

![image](https://user-images.githubusercontent.com/71643186/172043817-68b1ff75-d22b-4ef5-add7-a0666da469c5.png)

I messed around with the 	`CMD` and 	`RUN` commands with the build and serve commands to see if I got a different error or anything. I still was receiving the ng argument error ![image](https://user-images.githubusercontent.com/71643186/172043647-85432332-03f8-402a-8b08-304cd996dc57.png) with whatever command (bulid, serve, start, etc) was in the command.

Throughout this I utilised Google and docker and angular documentation to try and find the issue and what I was doing would but I could not find a solution for the errors I was getting.

I then re-cloned the repo to try and just run it locally. I received the same error as when tryinng with docker, but did find online that maybe npm and ng weren't linked, and to solve this enter `npm link @angular/cli`. I received the same errors as before. However a new angular workspace in a different folder and `npm install` and then `ng new project` followed by `cd project` and then `ng serve` and it did work. I checked the folder sturcture of this new project to check that  was running everything in the right place. 

`npm install` still wasn't working in the folder `\spring-boot-projects\spring-boot-modules\spring-boot-angular\src\main\js\application` verifed correct according to the other project I created but was working else where on my computer.`npm install` would install in every other folder bar the  `..\application` but when ng serve or build was run inside said folder it still came up with the agruement error. From the other project my conclusions on this were either I was running it in the wrong directory or the versions of tools installed on my computer did not match those in which created the application adn that caused issues.

I went back to my test app and successfully build the test project into a docker container, and if you wish to you can view it in [this repo](https://github.com/hannahnairn/AngularDocker).

## Spring Boot API Implementation
When attempting to create the image off the Dockerfile the error messages I was getting was that the `pom.xml` through looking at other projects their dockerfile and pom.xml were in the same location so I moved the pom.xml file to the dockerfile location and vica versa, and recieved the same error for both.

![image](https://user-images.githubusercontent.com/71643186/172048109-164d256c-460c-4670-bbcc-e3411f3d416b.png)

This error displayed that there was something wrong with the pom.xml file and when opened it was found to be the `<parent>` tag and that maven didn't accept it.ng serve 

![image](https://user-images.githubusercontent.com/71643186/172048450-042435dd-9b44-4fde-8a32-12ed43bb6d33.png)

I tried the suggestions that VSCode provided but none of them chnged the error presented.

As the error occured in both file locations where ever the dockerfile and pom.xml were located as long as they were together I decided to keep them both in the pom.xml orginial location. updating the simplified hierarchy structure of the application to look like the below:
    
    Base
    ├── (SpringBootAPI)           
    ├── (AngularApp)
         ├── Dockerfile                 
         ├── nginx.conf           
    ├── docker-compose.yaml
    ├── Dockerfile (SpringBootAPI) 
    ├── pom.xml  
    └── README.md

Like the angular frontend I then attempted to run the spring boot API locally, using the given commands and did get it to work.

### Final Working Method
I then started to research maven and docker more closing and found that maven has a image building command `mvn spring-boot:build-image` this successfully made the docker image and saved it as `docker.io/library/spring-boot-angular:1.0`. 

Then all there was was to run it using `docker run -p 9090:8080 -t docker.io/library/spring-boot-angular:1.0` command.

This method ingores any Dockerfile I had created.

## Docker Compose File Implementation
The `docker-compose.yaml` file is below – filled in with the image names if they were successfully build and runnable. 

![image](https://user-images.githubusercontent.com/71643186/172060495-a9f3b4b0-ceb8-4eff-bc7e-98de94fd3778.png)

## In a Office Environment

- After making my attempts at making the images and containers, I would firstly ask the development team for further clarification of what are the base directories of each application as the dockerfile for each service, in the end especially the angular frontend, would not be correct if placed else where in the folder system.
- Second I would ask a colleague of help. I found that writing the dockerfile themselves was not my biggest struggle or problem but in fact the use of node and angular. As I have never used either in or without docker I think it was the install and use of these tools that made my containers not work. One example of something new I found was that Maven had a build in command to build the container making it far more easy to create an image. With so many guides and tools on the internet it was easy to go down a rabbit hole and find lots of ways of dockerising spring boot including the likes of creating a JAR file and so forth, which were far more complicated methods.
- By asking a colleague with more experience they could have lead me in the right direction and shown me the correct use of the tools in the dockerfile and I would then be able to use this for the project and in the future. After all I would be working in a team and continuously learning throughout and by doing so the project would have been completed sooner and to the same standard, notation and syntax as previous built docker services by the team.
