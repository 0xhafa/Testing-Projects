# Setting Up a Catalyst Node
A Catalyst is a server that runs different services. These services currently work as the backbone for Decentraland. Some of these project are:

* Lighthouse - Communications coordinator
* Communications peer library
* Content Server

Step 1. Install Docker

```shell
$ sudo apt-get update

$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg

$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

$  echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

$ sudo apt-get update

$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Step 2. Install Docker-Compose
```shell
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.28.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

$ sudo chmod +x /usr/local/bin/docker-compose
```

Step 3. Add $USER to Docker Group
```shell
$ sudo groupadd docker

$ sudo usermod -aG docker $USER

$ newgrp docker
```

Step 4. Verify Docker Install
```shell
$ docker run hello-world
```

Step 5. Verify Docker-Compose Install
```shell
$ docker-compose --version
docker-compose version 1.28.5, build 1110ad01
```
Step 6. Stop Apache2
```shell
$ sudo systemctl disable apache2 && sudo systemctl stop apache2
```

Step 7. Install Git
```shell
$ sudo apt-get update

$ sudo apt-get install git
```

Step 8. Create Directory for Catalyst Source Code
```shell
$ mkdir catalyst

$ cd catalyst
```

Step 9. Download Catalyst-Owner from Decentraland's Github
```shell
$ git clone https://github.com/decentraland/catalyst-owner
```

Step 10. Delete .example extensions from environment files
```shell
$ mv .env.example .env

$ mv .env-advanced.example .env-advanced
```
Step 11. Edit Environment File
```shell
$ nano .env

# Configuration to be set by node owners
EMAIL=a@a.com
CONTENT_SERVER_STORAGE=./storage
CATALYST_URL=http://localhost
```

Step 12. Create Storage Folder
```shell
$ mkdir storage
```
Step 13. Run Catalyst Server
```shell
$ ./init.sh
```
Final message to confirm that the server is functioning:
```shell
## Catalyst server is up and running at http://localhost
```
