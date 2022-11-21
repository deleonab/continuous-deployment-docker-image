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

With our image built, we can run a container based on that image

```
docker run -p 3001:3000 --name=serve-container serve-image
```

```
$ docker run -p 3001:3000 --name=serve-container serve-image
INFO: Accepting connections at http://localhost:3000.
```

### NOW WE SHALL PUSH OUR CONTAINER IMAGE TO DOCKER HUB

- We shall start by running Docker Login with docker username and password
```
docker login
```

```
$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: deleonabowu
Password: 
Login Succeeded
```

#### Next we shall build our docker image

```
docker build . -t deleonabowu/serve_image 
```

### Then we shall push the image to dockerhub

```
docker push deleonabowu/serve_image 
```
```
$ docker push deleonabowu/serve_image
Using default tag: latest
The push refers to repository [docker.io/deleonabowu/serve_image]
936d43cd91d5: Pushed
7c322f3d99a0: Pushed
a0e83da8ee49: Mounted from library/node
797d12bcfeb4: Mounted from library/node
8ca8a587d4f0: Mounted from library/node
158b630f9dde: Mounted from library/node
397a239a053b: Mounted from library/node
89c3244a87b2: Mounted from library/node
80231db1194c: Mounted from library/node
f1c1f2298584: Mounted from library/node
ccba29d69370: Mounted from library/node
latest: digest: sha256:1b0ec48706ca06b0df5e0ebd35920fd943930c5df0b6c741af190e80fefb68cb size: 2633
```

![dockerhub1](./images/dockerhub1.PNG)

### Now that we have pushed it to dockerhub, we can use it from any docker machine

- I wll delete from the local machine $ docker rmi   3d69(part of image id)
```
$ docker rmi 3d69
```
### Now that it has been removed, we can run it as docker will check dockerhub if not found locally.

```
docker run -p=3001:3000 deleonabowu/serve_image
```
```
$ docker run -p=3001:3000 deleonabowu/serve_image
Unable to find image 'deleonabowu/serve_image:latest' locally
latest: Pulling from deleonabowu/serve_image
a8ca11554fce: Already exists
e4e46864aba2: Already exists
c85a0be79bfb: Already exists
195ea6a58ca8: Already exists
157f16ed0a0c: Already exists
3745f00fc1cc: Already exists
5bbc49fd3a32: Already exists
bf8c7ae7c46e: Already exists
dc0e02f1aec6: Already exists
9a55986f0f8a: Already exists
101db3d8a0d3: Already exists
Digest: sha256:1b0ec48706ca06b0df5e0ebd35920fd943930c5df0b6c741af190e80fefb68cb
Status: Downloaded newer image for deleonabowu/serve_image:latest
 INFO  Accepting connections at http://localhost:3000
```




![dockerhub1](./images/browser1.PNG)

### I will create a new repository in github called continuous-deployment-docker-image

### I will generate an SSH key to add to github to make automation easier

```
ssh-keygen -t ed25519 -C "delesodade@hotmail.com"
```

```
eval "$(ssh-agent -s)"
```

```
clip < ~/.ssh/id_ed25519.pub

```
```

ssh-ed25519 Axxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx deles@DESKTOP-PURLK18

```
### Add SSH key to git 

### Push code to git via SSH not http

git init 
git add remote origin git@github.com:deleonab/continuous-deployment-docker-serve-image.git
git commit
git push origin main


#### Now, we need to set up actions for the git repository using the github actions tab

- Acions > Docker Image > Configure

![Git Configure docker image](./images/dockerhub2.PNG)

###  Configure

![Git Configure docker image](./images/dockerhub3.PNG)

- Start commit > Commit new file

docker-image.yml created in continuous-deployment-docker-serve-image/.github/workflows/

![Git Configure docker image](./images/dockerhub4.PNG)


#### Next, we need to gain access to our container registry

We shall use Amazon ECR instead of Dockerhub as our container registry as docker hub isn't free. Amazon has a free tier.


#### I will go to Amazon ECR and create a public repository
- Log into AWS
- Go to ECR
- Select   public   tab
- Click create repository

- check public radio button selected
- Give repository name. I gave it deleonabowu/continuous-serve-image
- You may leave other settings as is
- Click create repository

![Rpository created successfully](./images/dockerhub5.PNG)


- Repositorycreated succesfully

#### I will now create an IAM user for the registry;
- I created an IAM user ECRAdministrator and granted only programatic access. I assigned the user to the administrator group.

I HAVE SHOWN THEM HERE AS THEY WILL BE DELETED TODAY AFTER THIS PROJECT
- Acess key   AKIASWLIU2NL5EHL73YA

- Secret Key   ThrFCPui9DlN8H57JVPc+gnYJtykdeHagOk+Lh4a


#### Retrieve an authentication token and authenticate your Docker client to your registry.
Use the AWS CLI:
```
aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/c8v1l0n9
```
#### Note: If you receive an error using the AWS CLI, make sure that you have the latest version of the AWS CLI and Docker installed.
Build your Docker image using the following command. For information on building a Docker file from scratch see the instructions here . You can skip this step if your image is already built:
```
docker build -t deleonabowu/continuous-serve-image .
```
####After the build completes, tag your image so you can push the image to this repository:
```
docker tag deleonabowu/continuous-serve-image:latest public.ecr.aws/c8v1l0n9/deleonabowu/continuous-serve-image:latest
```
#### Run the following command to push this image to your newly created AWS repository:
```
docker push public.ecr.aws/c8v1l0n9/deleonabowu/continuous-serve-image:latest
```

```
aws --version

aws-cli/2.9.0 Python/3.9.11 Windows/10 exe/AMD64 prompt/off

```

#### We need to configure the AWS client to use our AWS credentials to access AWS resources(ECR)

```
aws configure
```

#### login from command line
```
aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/c8v1l0n9
```
```
$ aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/c8v1l0n9
Login Succeeded
```
#### Logging in with your password grants your terminal complete access to your account.
For better security, log in with a limited-privilege personal access token. Learn more at https://docs.docker.com/go/access-tokens/ 

#### Next I build the Docker Image
```
docker build -t deleonabowu/continuous-serve-image .
```
#### The next thing is to tag the image to specify that it isn't going to the default dockerhub repository but ECR

```
docker tag deleonabowu/continuous-serve-image:latest public.ecr.aws/c8v1l0n9/deleonabowu/continuous-serve-image:latest
```


#### Now that it is built locally and tagged, I will push it to amazon ECR
```
$ docker push public.ecr.aws/c8v1l0n9/deleonabowu/continuous-serve-image:latest
The push refers to repository [public.ecr.aws/c8v1l0n9/deleonabowu/continuous-serve-image]
936d43cd91d5: Layer already exists
7c322f3d99a0: Pushed
a0e83da8ee49: Layer already exists
797d12bcfeb4: Layer already exists
8ca8a587d4f0: Layer already exists
158b630f9dde: Layer already exists
397a239a053b: Layer already exists
89c3244a87b2: Pushed
80231db1194c: Layer already exists
f1c1f2298584: Layer already exists
ccba29d69370: Layer already exists
latest: digest: sha256:1b0ec48706ca06b0df5e0ebd35920fd943930c5df0b6c741af190e80fefb68cb size: 2633
```

![ecr image1](./images/ecrimage1.PNG)

#### Now i will sync my localrepository with the git repository as we made local changes setting up the git action

```
git pull origin main
```
```
$ git pull origin main
remote: Enumerating objects: 6, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 5 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (5/5), 936 bytes | 12.00 KiB/s, done.
From github.com:deleonab/continuous-deployment-docker-serve-image
 * branch            main       -> FETCH_HEAD
   441f291..90596bb  main       -> origin/main
Merge made by the 'ort' strategy.
 .github/workflows/docker-image.yml | 17 +++++++++++++++++
 1 file changed, 17 insertions(+)
 create mode 100644 .github/workflows/docker-image.yml
 ```

 #### Let us noe edit the docker-image.yml file locally. We already have 2 steps

 - One to checkout the code base
 - To build out container image

 #### We however still need to authenticate when pushed to AWS and also to tag the image

 #### Here is the updated docker-image.yml file
 ```
name: Docker Image CD

on:
  push:
    branches: [ "main" ]
  
jobs:

  build:

    runs-on: ubuntu-latest

    steps:
    - name: checkout the dockerfile code
      uses: actions/checkout@v3

    - name: configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
       aws-access-key-id: AKIASWLIU2NL5EHL73YA
       aws-secret-access-key: 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
       aws-region: us-east-1

    - name: Log into ECR
      run: aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/c8v1l0n9

    - name: Build, tag and push the Docker image
      run: |
        docker build . -t deleonabowu/continuous-serve-image
        docker tag deleonabowu/continuous-serve-image:latest public.ecr.aws/c8v1l0n9/deleonabowu/continuous-serve-image:latest
        docker push public.ecr.aws/c8v1l0n9/deleonabowu/continuous-serve-image:latest
 ```

#### We need to avoid displaying our secrets publicly in github. 
 - We shall create new repository secrets by going to actions/security 

- AWS_ACCESS_KEY_ID  :  AKIASWLIU2NL5EHL73YA
- AWS_SECRET_ACCESS_KEY : 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

#### docker-image.yml now looks like this:

```
name: Docker Image CD

on:
  push:
    branches: [ "main" ]
  
jobs:

  build:

    runs-on: ubuntu-latest

    steps:
    - name: checkout the dockerfile code
      uses: actions/checkout@v3

    - name: configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
       aws-access-key-id: ${{secrets.AWS_ACCESS_KEY_ID}}
       aws-secret-access-key: ${{secrets.AWS_SECRET_ACCESS_KEY}}
       aws-region: us-east-1

    - name: Log into ECR
      run: aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/c8v1l0n9

    - name: Build, tag and push the Docker image
      run: |
        docker build . -t deleonabowu/continuous-serve-image
        docker tag deleonabowu/continuous-serve-image:latest public.ecr.aws/c8v1l0n9/deleonabowu/continuous-serve-image:latest
        docker push public.ecr.aws/c8v1l0n9/deleonabowu/continuous-serve-image:latest

```