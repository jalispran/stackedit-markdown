## Dockerizing Springboot Elasticsearch application with docker compose

So all this started when I decided to create an application that suggests food recipes based on the ingredients you have at your home. 

This is the tech stack that I want to use:
* Spring Boot for a RESTful API Server
* React Native with Expo for frontend and
* Elastic Search for full text search queries on the recipes. 

I have already found a dataset of around 7k recipes on kaggle. So I am all set for development.

Now, this application is not meant to be an application for the general public. Its meant to be run on my private server in a private network for some limited number of people. Hence, I want to keep the deployment and maintainance overhead to a minimum and not manually create a cluster for elastic search. Obviously, I am going the docker route. I am going to dockerize my application and run it with `docker run` command, or at least thats the plan.

Fortunately my development and 



<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIxMzMwODE3NDksNjg3NjAxNjAyLDEwMT
Q0NDEwMjFdfQ==
-->