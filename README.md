STIG MANAGER
---
This guide provides instructions on how to set up STIG Manager using Docker and Docker Compose and utilizing nginx proxy.


# Prerequisites


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

Download and install Docker Desktop from Docker Hub.

#### Installing Docker Compose

#### Download the current stable release of Docker Compose:

```

sudo curl -L "https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
#### Apply executable permissions to the binary:


```
sudo chmod +x /usr/local/bin/docker-compose
```




### For RHEL:

#### Uninstall old versions of Docker, if any:

```

sudo yum remove docker docker-common docker-selinux docker-engine

```

#### Install required packages for setting up the repository:

```

sudo yum install -y yum-utils
```
Set up the stable repository:

```

sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
#### Install the Docker package:

```

sudo yum install docker-ce
```

#### Start Docker:

```

sudo systemctl start docker
```

#### Enable Docker to start at boot:

```

sudo systemctl enable docker
```

### Setting Up STIG Manager

#### Clone the repository containing your Docker Compose file:

 
```
git clone https://github.com/allamiro/stig-manager.git
cd stig-manager
```



#### Environment variables
Update the environment variables ```.env``` file 


Start the services using Docker Compose:

```
docker-compose up -d
```
Verify that the containers are running successfully:

```
docker-compose ps
```

Configure NGINX for TLS and Reverse Proxy
---

To deploy STIG Manager with TLS using NGINX as a reverse proxy, you will need to follow these essential steps based on the information you provided:

1. Install nginx then generate your CSR requests and get them signed by your Certificate Authority 

For RHEL 
```
dnf install openssl nginx -y


```

FOR UBUNTU

```
sudo apt update
sudo apt install openssl nginx


```



Generate the CSR Using the Private Key

```
openssl genrsa -out mydomain.key 2048
openssl req -new -key mydomain.key -out mydomain.csr



```


To create a self signed certificate without a prompt 


```
openssl req -newkey rsa:4096 \
            -x509 \
            -sha256 \
            -days 3650 \
            -nodes \
            -out example.crt \
            -keyout example.key \
            -subj "/C=SUST/ST=VA/L=Sterling/O=Security/OU=IT Department/CN=www.example.com"

```



2. Create you configuration file at ``` /etc/nginx/conf.d/site-domain.conf ``` as follows:


```
server {
    listen 443 ssl;
    server_name your-domain.com;

    ssl_certificate /etc/nginx/ssl/your-domain.crt;  # SSL Certificate
    ssl_certificate_key /etc/nginx/ssl/your-domain.key;  # SSL Certificate Key

    # Adjusted location for the STIG Manager API ######
    location /stig-api {
        proxy_pass http://localhost:54000;  # Proxy traffic to the API service on port 54000
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Location for the STIG Manager main interface
    location / {
        proxy_pass http://localhost:8080;  # Proxy traffic to the main STIG Manager service on port 8080
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

```

3. Enable and restart nginx service

```
systemctl enable nginx 
systemctl start nginx 

```





4 . Access the site - https://your-site-domainname  happy STIGGING




Reference
---
1. https://stig-manager.readthedocs.io
2. https://github.com/NUWCDIVNPT/stig-manager
3. https://github.com/NUWCDIVNPT/stigman-orchestration/blob/main/docker-compose.yml
4. https://github.com/NUWCDIVNPT/stigman-orchestration
5. https://github.com/NUWCDIVNPT/stig-manager/issues/1001



