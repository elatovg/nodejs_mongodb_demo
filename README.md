# NodeJs and MongoDB Docker Demo

This repository contains a simple example of how to run [NodeJs](https://nodejs.org/en/) and [mongoDB](https://www.mongodb.com//) on [docker](https://www.docker.com/)

## Installation

### Option 1: Running locally with “Docker for Desktop” docker-compose

> 💡 Recommended if you're planning to develop the application.

1. Install tools to run a Docker locally:

   - [Docker for Desktop (Mac/Windows)](https://docs.docker.com/docker-for-mac/install/): It provides Kubernetes support as well ([noted here](https://docs.docker.com/docker-for-mac/kubernetes/)). 
   - [docker-compose](https://docs.docker.com/compose/install/). If you are using brew, you can run the following: `brew install docker-compose`

2. Confirm all the software is installed:

```bash
> docker version
Client: Docker Engine - Community
 Version:           19.03.12
 API version:       1.40
 Go version:        go1.13.10
 Git commit:        48a66213fe
 Built:             Mon Jun 22 15:41:33 2020
 OS/Arch:           darwin/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.12
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.13.10
  Git commit:       48a66213fe
  Built:            Mon Jun 22 15:49:27 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          v1.2.13
  GitCommit:        7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
> docker-compose version
docker-compose version 1.26.2, build eefe0d31
docker-py version: 4.2.2
CPython version: 3.7.7
OpenSSL version: OpenSSL 1.1.1g  21 Apr 2020
```

### Option 2: Running on Google Kubernetes Engine (GKE) TODO

> 💡  Recommended for demos and making it available publicly.

1. Install tools specified in the previous section (Docker, kubectl)

2. Create a Google Kubernetes Engine cluster and make sure `kubectl` is pointing to the cluster.
    ```bash
    gcloud services enable container.googleapis.com
    gcloud services enable cloudbuild.googleapis.com
    gcloud container clusters create node --enable-autoupgrade --num-nodes 2 --zone us-east4-c
    kubectl get nodes
    ```
3. Enable Google Container Registry (GCR) on your GCP project and configure the `docker` CLI to authenticate to GCR:
   ```bash
   gcloud services enable containerregistry.googleapis.com
   gcloud auth configure-docker -q
	```

## Deployment
### Locally with Docker
Now that we have our tools installed let's go ahead and deploy the application:

```bash
> git clone https://github.com/elatovg/nodejs_mongodb_demo.git
> docker-compose up
...
...
mongo_1  | 2020-07-29T19:38:35.639+0000 I  NETWORK  [conn1] end connection 172.19.0.3:33998 (0 connections now open)
node_1   | wait-for-it.sh: mongo:27017 is available after 10 seconds
node_1   |
node_1   | > app@1.0.0 start /app
node_1   | > node server.js
node_1   |
node_1   | (node:50) DeprecationWarning: current Server Discovery and Monitoring engine is deprecated, and will be removed in a future version. To use the new Server Discover and Monitoring engine, pass option { useUnifiedTopology: true } to the MongoClient constructor.
node_1   | (Use `node --trace-deprecation ...` to show where the warning was created)
mongo_1  | 2020-07-29T19:38:36.623+0000 I  NETWORK  [listener] connection accepted from 172.19.0.3:34000 #2 (1 connection now open)
mongo_1  | 2020-07-29T19:38:36.632+0000 I  NETWORK  [conn2] received client metadata from 172.19.0.3:34000 conn2: { driver: { name: "nodejs", version: "3.5.9" }, os: { type: "Linux", name: "linux", architecture: "x64", version: "4.19.76-linuxkit" }, platform: "'Node.js v14.6.0, LE (legacy)" }
```

After it's done it will create a local directory called `data` and that's where the mongodb contents will be stored. Also if you check for local docker images, you will see a newly built and pulled images:

```bash
 > docker images
REPOSITORY                           TAG                 IMAGE ID            CREATED             SIZE
nodejs_mongodb_demo_node             latest              a9b5fa53581f        59 minutes ago      962MB
node                                 latest              9b21eddf6af2        22 hours ago        943MB
mongo                                latest              dee5b549543c        5 days ago          388MB
```

And you can `curl` the nodejs container to see the simple app:

```bash
> curl http://localhost:3000 -s | grep /title
    <title>NodeJS MongoDB demo</title>
```
### Remotely on GKE (TODO)