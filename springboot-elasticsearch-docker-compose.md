## Dockerizing Springboot Elasticsearch application with docker compose

So all this started when I decided to create an application that recommends food recipes based on the ingredients you have at your home. 

This is the tech stack that I want to use:
* Spring Boot for a RESTful API Server
* React Native with Expo for frontend and
* Elastic Search for full text search queries on the recipes. 

I have already found a dataset of around 7k recipes on kaggle. So I am all set for development.

Now, this application is not mwith docker now supporting Apple Silicon, I wanted to try something on my brand new Mac Mini M1.  I was also exploring Elastic Search, so I thought this is a greant to be an application for the general public. Its meant to be run on my private server in a private network for some limited number of people. Hence, I want to keep the deployment and maintainance overhead to a minimum and not manually create a cluster for elastic search. Obviously, I am going the docker route. I am going to dockerize my application and run it with `docker run` command, or at least thats the plan.

This is my development environment:
* Mac Mini M1 with 16GB Memory
* Java 11
* Spring Boot 2.5.0

So the following is my learning from setting all this up. 

1. Docker is now supported for Apple Silicon 🤩

2. Use docker compose
I quickly realised that for a setup like this, I will have to use `docker compose`. If you are new to docker or docker compose, let me quickly clarify this.

Docker is used to containerize your application while docker compose is a discriptor about how multiple containers behave in each others presence. Do they share a network? Do they share a volume? What about environment variables? In what order should they be run? Do you need to scale them? If yes, how many containers would you need? 

So now, the plan has been modified a bit, I am going to have two containers and I will run them using `docker compose up`. Great, I am still aiming for a single command deplyment. 

3. Getting elasticsearch to run is amazingly easy with docker

Just got to the elastic search website and you will find a `docker run` command for a single node dev deployment and a `docker compose` for a more elaborate 3 node cluster setup. I did the best of both and selected single node deployment with docker compose.

4. Dockerizing Spring Boot is once you know what not to do

Even though Spring has a great tutorial about how to dockerize spring boot apps and dividing them in layers for efficiency, I still screwed up mine and had to waste a day figuring out what was the issue. 






I am planning o
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1Njg0NjU5NjYsLTczOTExNzc1NSwtMT
Q1NTQzNTI5NywxMDE0NDQxMDIxXX0=
-->