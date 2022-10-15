### Creating a serve container to serve a static web application using node|JS
create a folder display
```
mkdir display
```
Inside display create text file conten.txt
```
touch display/content.txt
```
inside display, I will create a subfolder called site and a file inside it called index.html
```
mkdir display/site | touch display/site/index.html
```

To create the DOCKER IMAGE, we need to create a Dockerfile

```
touch Dockerfile
```

server app depends on nodejs

I will use docker search to find a node base image on dockerhub
```
docker search node
```
```
deles@DESKTOP-PURLK18 MINGW64 ~/Documents/build-docker-file-server-image (main)
$ docker search node
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
node                              Node.js is a JavaScript-based platform for s…   11948     [OK]
mongo-express                     Web-based MongoDB admin interface, written w…   1223      [OK]
circleci/node                     Node.js is a JavaScript-based platform for s…   129
kindest/node                      sigs.k8s.io/kind node image                     73
bitnami/node                      Bitnami Node.js Docker Image                    66                   [OK]     
cimg/node                         The CircleCI Node.js Docker Convenience Imag…   13
```

Create Dockerfile
- install latest node
FROM node:latest
- run command to install serve package globally(-g)
RUN npm install -g serve
- copy file and contents into container
COPY ./display/ ./dislplay

- run the serve application in specified directory

CMD serve ./display
The Docker file will now look lik e that below 
```
FROM node:latest
RUN npm install -g serve
COPY ./display ./display
CMD serve ./display

```