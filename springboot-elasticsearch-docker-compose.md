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

1. 
I am planning o
<!--stackedit_data:
eyJoaXN0b3J5IjpbNjIwNTE1NzM5LC03MzkxMTc3NTUsLTE0NT
U0MzUyOTcsMTAxNDQ0MTAyMV19
-->