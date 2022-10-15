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

Now we are ready to build the image with the dockerfile instructions

- Navigate to Dockerfiles directory i.e build-docker ....
-t to tag i.e name the image
- docker build . -t serve-image 

```
$ docker build . -t serve-image
Sending build context to Docker daemon    213kB
Step 1/4 : FROM node:latest
 ---> 3adbe565b1f0
Step 2/4 : RUN npm install -g serve
 ---> Running in e0334bd06477

added 89 packages, and audited 90 packages in 12s

24 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
npm notice 
npm notice New minor version of npm available! 8.15.0 -> 8.19.2        
npm notice Changelog: <https://github.com/npm/cli/releases/tag/v8.19.2>
npm notice Run `npm install -g npm@8.19.2` to update!
npm notice 
Removing intermediate container e0334bd06477
 ---> a94ff2af75b3
Step 3/4 : COPY ./display ./display
 ---> d8cdc63fb419
Step 4/4 : CMD serve ./display
 ---> Running in ab4248af30da
Removing intermediate container ab4248af30da
 ---> 4b7580fd5227
Successfully built 4b7580fd5227
Successfully tagged serve-image:latest
SECURITY WARNING: You are building a Docker image from Windows against a non-Windows Docker host. All files and directories added to build context will have '-rwxr-xr-x' permissions. It is recommended to double check and reset permissions for sensitive files and directories.

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/build-docker-file-server-image (main)
$ docker image ls
REPOSITORY                 TAG        IMAGE ID       CREATED              SIZE
serve-image                latest     4b7580fd5227   About a minute ago   1GB
redis                      latest     dc7b40a0b05d   7 weeks ago          117MB
tooling                    0.0.1      3753453529de   7 weeks ago          457MB
```