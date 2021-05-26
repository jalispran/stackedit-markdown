---
title: Dockerizing Springboot Elasticsearch Application
author: pranjal
tags: 'docker, springboot, elasticsearch, docker-compose'
categories: blog
excerpt: >-
  My learnings from dockerizing springboot and elasticsearch application using
  docker compose
featuredImage: 'https://upload.wikimedia.org/wikipedia/commons/f/f4/Elasticsearch_logo.svg'
date: '2021-05-27'

---

<hr>
<p>title: Dockerizing Springboot Elasticsearch Application<br>
author: pranjal<br>
tags: ‘docker, springboot, elasticsearch, docker-compose’<br>
categories: blog<br>
excerpt: &gt;-<br>
My learnings from dockerizing springboot and elasticsearch application using<br>
docker compose<br>
featuredImage: ‘<a href="https://upload.wikimedia.org/wikipedia/commons/f/f4/Elasticsearch_logo.svg">https://upload.wikimedia.org/wikipedia/commons/f/f4/Elasticsearch_logo.svg</a>’<br>
date: ‘2021-05-27’</p>
<hr>
<h2 id="dockerizing-springboot-elasticsearch-application-with-docker-compose">Dockerizing Springboot Elasticsearch application with docker compose</h2>
<p>So all this started when I decided to create an application that recommends food recipes based on the ingredients you have at your home.</p>
<p>This is the tech stack that I want to use:</p>
<ul>
<li>Spring Boot for a RESTful API Server</li>
<li>React Native with Expo for frontend and</li>
<li>Elastic Search for full text search queries on the recipes.</li>
</ul>
<p>I have already found a <a href="https://www.kaggle.com/kanishk307/6000-indian-food-recipes-dataset">dataset of around 7k recipes on kaggle</a>. So I am all set for development.</p>
<p>Now, this application is not meant to be an application for the general public. Its meant to be run on my private server in a private network for some limited number of people. Hence, I want to keep the deployment and maintainance overhead to a minimum and not manually do a full fledged for elastic search cluster deployment.</p>
<p>I am aiming for a single command deployment.</p>
<p>Obviously, I am going the docker route. I am going to dockerize my application and run it with <code>docker run</code> command, or at least thats the plan.</p>
<p>This is my development environment:</p>
<ul>
<li>Mac Mini M1 with 16GB Memory</li>
<li>Java 11</li>
<li>Spring Boot 2.5.0</li>
<li>Maven</li>
</ul>
<p>So the following is my learning from setting all this up.</p>
<ol>
<li>Docker now has support for Apple Silicon 🤩🤩🤩</li>
</ol>
<p>However, it still requires <code>Rosetta 2</code> as some binaries are yet to make the transition to Apple Silicon. If you need, you can use this command to install Rosetta 2.</p>
<p><img src="https://i.imgur.com/YUropvW.png" alt="enter image description here"></p>
<ol start="2">
<li>Use docker compose</li>
</ol>
<p>I quickly realised that for a setup like this, I will have to use <code>docker compose</code>. If you are new to docker or docker compose, let me quickly clarify this.</p>
<p>Docker is used to containerize your application while docker compose is a discriptor about how multiple containers behave in each others presence. Do they share a network? Do they share a volume? What about environment variables? In what order should they be run? Do you need to scale them? If yes, how many containers would you need?</p>
<p>So now, the plan has been modified a bit, I am going to have two containers and I will run them using <code>docker compose up</code>. Great, I am still aiming for a single command deplyment.</p>
<p><a href="https://docs.docker.com/compose/gettingstarted/">Getting started with docker compose</a> is the best place to start if you are curious.</p>
<ol start="3">
<li>Getting elasticsearch to run is amazingly easy with docker</li>
</ol>
<p>Just go to the <a href="https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html">elastic search website</a> and you will find a <code>docker run</code> command for a single node dev deployment and a <code>docker compose</code> for a more elaborate 3 node cluster setup. I did the best of both and selected single node deployment with docker compose.</p>
<ol start="4">
<li>Dockerizing Spring Boot is easy once you know what not to do</li>
</ol>
<p>Spring has a <a href="https://spring.io/guides/gs/spring-boot-docker/">great tutorial</a> about how to dockerize spring boot apps and how to divide them in layers for efficiency. But I still screwed up mine and had to waste a day figuring out what was the issue.</p>
<blockquote>
<p>The springboot tutorial says there is a <a href="https://www.docker.com/products/docker#/mac">limitation with Docker for Mac</a>. So it might be a good idea to avoid using container IP as far as possible.</p>
</blockquote>
<p>The weirdest thing was, my app would run perfectly fine in development mode. But it would fail when I ran it in container.</p>
<p>There is a reason for it (or thats what I thought at the time). To keep the image size to a minimum, I was using a distroless java 11 base image. On the other hand, on my dev machine I was using <code>OpenJdk 11</code>. This java 11 distroless image was buggy as far as I can tell.</p>
<p>Finally, after wasting one day trying to fix this, I gave up and shifted to good old <code>openJdk:11</code>. Although I had to compromise the image size (which increased 3x), atleast I got the service running.</p>
<p>(Un)fortunately, I am unable to reproduce the same error anymore so I can not upload a screenshot. (Murphy’s Law here 😅) However, if you are feeling curious, this is what I was using <code>gcr.io/distroless/java:11</code>. And here is my observation</p>
<table>
<thead>
<tr>
<th>Base Image</th>
<th>Final Image Size</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>gcr.io/distroless/java:11</code></td>
<td>257 MB</td>
</tr>
<tr>
<td><code>openjdk:11</code></td>
<td>695 MB</td>
</tr>
</tbody>
</table><p>Now, I am by no means an expert in this. I am not even aware of the differences in those two base images, but that is a striking difference for me.</p>
<ol start="5">
<li>Docker containers don’t connect to other containers on their own. Or do they? 🤔</li>
</ol>
<p>When I started off, I did not know that you need special configuration to enable the containers to talk to each other.</p>
<p>So I ended up creating a bridge network in my <code>docker-compose.yml</code>. Now everything works as expected. Both my containers (springboot and elasticsearch) get connected to the new bridge network and they can discover each other. However, there is a caveat.</p>
<p>When running locally, you can specify <code>localhost:9092</code> for springboot to connect to the elasticsearch docker. However, its a different story in the container.</p>
<p>When running in a container, the hostname has to be replaced with the container name. So, the new address becomes <code>&lt;elasticsearch-container-name&gt;:9092</code></p>
<p>I don’t want to remember this caveat every time I need to deploy something. So I created an environment variable, called it <code>ELASTIC_HOST</code>. I am setting its value in my <code>docker-compose.yml</code>. And I am reading it in my <code>application.yml</code> file.</p>
<p>That felt nice. Earlier my application was not running but now my application can run and connect to the elastic docker sometimes. Thats progress!</p>
<p>By the way, I just found out that docker compose creates a <code>default</code> network. So maybe I unnecessarily created a bridge network and maybe the containers can actually talk to each other on their own. Who knows?! Well, not me.</p>
<p>Anyhow, the problem I face now, is that I need to ask spring boot to wait until elasticsearch cluster is up and running before spring boot tries to connect to it. Docker compose has a <code>depends_on</code> option to express the startup and shutdown order.  More about this <a href="https://docs.docker.com/compose/startup-order/">here</a>.</p>
<p>However, this wont work the way you expect it to. And its understandable.</p>
<p>So <code>depends_on</code> will wait for the container to run before it starts the dependant containers. However, a container in running state does not mean that the application is in running state. For example, in case of a database, the database container might be in running state but that does not mean that the database is accepting connections yet.</p>
<p>Docker compose can not know the application readiness and hence this needs to be handled by the developer. The recommended way is to retry on connection failure. However, the more widely used approach seems to be a busy waiting scripts (<a href="https://github.com/vishnubob/wait-for-it">wait-for-it</a>, <a href="https://github.com/jwilder/dockerize">dockerize</a> etc) that keep checking if the service is up.</p>
<p>I am planning to go the recommended route of retrying the connection on failure. This functionality is not yet available in spring boot. There is an open issue about this <a href="https://github.com/spring-projects/spring-boot/issues/4779">#4779</a>. Meanwhile, I am planning to implement my own.</p>
<p>As always, the source code is available on <a href="https://github.com/jalispran/springboot-elasticsearch-docker">github</a>. Check that out for the code and stay tuned for the retry mechanism.</p>

