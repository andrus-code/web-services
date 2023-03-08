# Best Practices for Deploying Production-Level Web Services using Docker

## Tested Infrastructure

<table class="tg">
  <tr>
    <th class="tg-yw4l"><b>Platform</b></th>
    <th class="tg-yw4l"><b>Number of Instance</b></th>
    <th class="tg-yw4l"><b>Reading Time</b></th>
    
  </tr>
  <tr>
    <td class="tg-yw4l"><b> Play with Docker(testing webservice)</b></td>
    <td class="tg-yw4l"><b>1</b></td>
    <td class="tg-yw4l"><b>10 min</b></td>
    
  </tr>
  
</table>

## Pre-requisite

In this article, the creator show as deploy your production web services. Basically we are going to use the method of running multiple production containers using docker.
When you do docker in day to day work then you can come across docker-compose. Docker is really a magical tool! 
We are gifted with tools in this modern era and we should utilize them to deliver services seamlessly.

## Traditional approach (Know the existing things)


## Anatomy of webservice 

![service][https://github.com/andrus-code/web-services/img/web-service1.png]

Steps to follow:

Clone the Repository:

```
git clone https://github.com/andrus-code/web_services.git
```

Change directory to webservices as shown below:


```
cd webservices 
```

Bringing up app using Docker Compose:
 
```
docker-compose up 
```

for [PWD](https://labs.play-with-docker.com/) click on port you will get health check page 

![](https://github.com/andrus-code/img/web-service1.png)


health check by curl 

```
$ curl http://localhost/api/v1/healthcheck
"2018-11-01T03:26:07.605Z/"
```
## Important thing

If you see, we are creating a simple express service with a health check endpoint.
```
https://github.com/andrus-code/web_services/app/server.js

```

check the nginx configuration file.
```
https://github.com/andrus-code/web_services/nginx/default.conf
```

```
upstream service { 
    server app:8080;
}
```
nginx and app both are bridged using mynetwork, one can access another by the service name. So DNS is already taken care by docker. If this privilege is not available, we need to hard code IP in Nginx configuration file or assign a static IP from the subnet in the docker-compose.yam file. This is a wonderful thing about docker networking.

Is important understand the sentence above about server app:8080, because "server" apoint to container named "app" which is listening on port 8080.

```
version: "2"
services:
    nginx:
        build: ./nginx
        ports:
          - "80:80"
        networks:
          - mynetwork
    app:
        build: ./app
        networks:
          - mynetwork
        expose: 
          - 8080
networks:
    mynetwork: 
        driver: bridge
```

![webservice2] (https://github.com/andrus-code/web-services/img/web-service2.png)

By default, all the containers we create will fall under the same Internal IP range(Subnet). Docker networking allows us to create custom networks with additional properties like automatic DNS resolution etc.

In the above YAML file, we are creating a network called mynetwork. The services(containers) app and nginx will lie in the same subnet and can communicate to each other without the need of exposing the web service container to the outside world. In this way, we can make a single entry point to our web service that is through the Nginx service. If anyone tries to access app service directly they cannot do it because it is hidden. This actually secures our application.

## Contributor - 

Sangam biradar - smbiradar14@gmail.com -www.codexplus.in 


[sdfsad]: https://github.com/andrus-code/web-services/img/web-service1.png