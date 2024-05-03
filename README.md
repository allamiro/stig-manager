STIG MANAGER
---
This guide provides instructions on how to set up STIG Manager using Docker and Docker Compose.
Prerequisites


Before you begin, ensure Docker and Docker Compose are installed on your system. If not, follow the installation instructions below.

## Installing Docker

### For Ubuntu:

#### Update your package index:


```
sudo apt update
```
#### Install packages to allow apt to use a repository over HTTPS:

```
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```
#### Add Docker’s official GPG key:

```

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
#### Add the Docker repository to APT sources:

```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

#### Update the package database with Docker packages from the newly added repo:


```
sudo apt update
```
#### Install Docker:

```
sudo apt install docker-ce
```


### For Windows and Mac:

#### Download and install Docker Desktop from Docker Hub.

#### Installing Docker Compose

#### Download the current stable release of Docker Compose:

```

sudo curl -L "https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
#### Apply executable permissions to the binary:


```
sudo chmod +x /usr/local/bin/docker-compose
```
#### Setting Up STIG Manager

#### Clone the repository containing your Docker Compose file:

 
```
git clone https://github.com/allamiro/stig-manager.git
cd stig-manager
```


