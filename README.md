# retail store app
 
## [1. Terraform](#Terraform)
## [2. AWS](#AWS)
## [3. GCP](#GCP)
## [4. Docker](#Docker)
## [5. Dockerhub](#Dockerhub)
## [6. Jenkins](#Jenkins)
   
# Terraform


# AWS

### create access key

![image](https://github.com/fifa0903/axiata-task/assets/132969781/fe514ff5-4b38-4d65-894f-7b3c01521275)

### configure ec2 policy

![image](https://github.com/fifa0903/axiata-task/assets/132969781/d1b85703-4333-4a45-9428-a33b9888dc09)

### make terraform script and apply

```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
    }
  }
}

provider "aws" {
  region = "ap-southeast-1"  
  access_key = "AKIATFMMKEDTVBAKWMVY"
  secret_key = "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"
}

resource "aws_instance" "appserver" {
  ami           = "ami-002843b0a9e09324a"  
  instance_type = "t2.micro"              
  key_name      = "nobody1305"    
  vpc_security_group_ids = [ aws_security_group.websg.id ]
  tags = {
    Name = "retail-store-app"
  }
}

resource "aws_security_group" "websg" {
  name = "web-sg01"
  ingress {
    protocol = "tcp"
    from_port = 22
    to_port = 22
    cidr_blocks = [ "0.0.0.0/0" ]
  }
  ingress {
    protocol = "tcp"
    from_port = 80
    to_port = 80
    cidr_blocks = [ "0.0.0.0/0" ]
  }
  ingress {
    protocol = "tcp"
    from_port = 443  
    to_port = 443  
    cidr_blocks = [ "0.0.0.0/0" ]
  }
  ingress {
    protocol = "tcp"
    from_port = 8000
    to_port = 8000
    cidr_blocks = [ "0.0.0.0/0" ]
  }
  ingress {
    protocol = "tcp"
    from_port = 8080
    to_port = 8080
    cidr_blocks = [ "0.0.0.0/0" ]
  }
  ingress {
    protocol = "tcp"
    from_port = 8888
    to_port = 8888
    cidr_blocks = [ "0.0.0.0/0" ]
  }
  ingress {
    protocol = "tcp"
    from_port = 4369
    to_port = 4369
    cidr_blocks = [ "0.0.0.0/0" ]
  }
  ingress {
    protocol = "tcp"
    from_port = 3306
    to_port = 3306
    cidr_blocks = [ "0.0.0.0/0" ]
  }
  ingress {
    protocol = "tcp"
    from_port = 8080
    to_port = 8888
    cidr_blocks = [ "0.0.0.0/0" ]
  }
  ingress {
    protocol = "tcp"
    from_port = 6379
    to_port = 6379
    cidr_blocks = [ "0.0.0.0/0" ]
  }

  egress {
    protocol = "-1"
    from_port = 0
    to_port = 0
    cidr_blocks = [ "0.0.0.0/0" ]
  }

}

output "instance_ips" {
  value = aws_instance.appserver.public_ip
}

```
![image](https://github.com/fifa0903/axiata-task/assets/132969781/be022049-7155-4cb4-9b52-92e5d831988b)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/8da2d908-4c79-43df-ad86-d1818143b3d0)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/496092b3-fc8c-4211-84ea-396870dd77f3)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/0027e2c0-91d2-4943-b7ee-2098b9142055)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/3f4fab6e-b923-4642-b3eb-765c7ba843f1)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/a8209bb3-daa7-4502-82be-d3b228d25b60)

# GCP
```
provider "google" {
  credentials = file("/home/nobody1305/Downloads/retail-app-store-6ea5079d0074.json")
  project     = "retail-app-store"
  region      = "asia-southeast1"
  zone	      = "asia-southeast1-a"
}

resource "google_compute_address" "static" {
  name = "ipv4-address"
}

resource "google_compute_firewall" "allow-firewall" {
  name    = "allow-firewall"
  network = google_compute_network.vpc_network.name

  allow {
    protocol = "tcp"
    ports    = ["22", "80", "443", "8080", "8888", "4369", "3306", "6379", "8000", "8081", "50000"]
  }

  source_ranges = ["0.0.0.0/0"]
}

resource "google_compute_network" "vpc_network" {
  project = "retail-app-store"
  name = "vpc-network"
  auto_create_subnetworks = true
  mtu = 1460
}


resource "google_compute_instance" "retail-store" {
  name         = "retail-store"
  machine_type = "e2-standard-4"
  zone         = "asia-southeast1-a"
  allow_stopping_for_update = true 

  boot_disk {
    initialize_params {
      image = "ubuntu-os-cloud/ubuntu-2004-lts"
    }
  }

  network_interface {
    network = google_compute_network.vpc_network.name
    access_config {
      nat_ip = google_compute_address.static.address
    }
  }

  metadata = {
    ssh-keys = "nobody1305:ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICucHSV7nh0yljyXZX3LLybdprzOWy8a8pPq8wdnuobK nobody1305@UbuntuFaizal"
  }
}

output "public_ip" {
  value = google_compute_instance.retail-store.network_interface[0].access_config[0].nat_ip
}

```

### create service account

![image](https://github.com/fifa0903/axiata-task/assets/132969781/76d662ef-d873-4b14-84a4-ff7af1c9b4a4)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/50a5cd6a-c179-4ca4-aeca-df6933863804)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/18bad063-dec4-4339-a63d-8e580dd78231)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/47f1a418-d137-4a3e-a18d-81deb47dd791)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/983b7c32-a796-429b-8f74-08a6d6adb0d3)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/76f42764-5818-4b16-b4a0-e431b0d2f821)

# Docker 

### installation

![image](https://github.com/fifa0903/axiata-task/assets/132969781/21a256c5-6522-4b7f-a810-06374f1e74de)

### pull repo

![image](https://github.com/fifa0903/axiata-task/assets/132969781/def99055-f1f3-4dc9-b701-8497896677bb)

### running docker compose on dist/docker-compose

![image](https://github.com/fifa0903/axiata-task/assets/132969781/b366054e-ed68-47d9-8e56-08f8ebe0476f)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/c1610e31-5bb0-4a35-8394-4f155ef16c5d)

the deployment is complete. if there is errorr, it must be setup from app. so we can discuss it with the developer.

![image](https://github.com/fifa0903/axiata-task/assets/132969781/c09febad-e574-4ae5-aa17-f321dfe5ed85)

# Dockerhub

docker login, docker tag, and push image to dockerhub

![image](https://github.com/fifa0903/axiata-task/assets/132969781/07f31630-53b6-494f-b0ed-854e2550e0c3)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/e2c95cfe-b4fc-40a0-80d6-b0db440849fc)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/dc1c45bb-cdf2-473c-958b-bd6888b9f2ac)

# Jenkins

### pipeline (docker compose down, pull github repository, docker compose up, docker push image)
```
def branch = "main"
def remote = "origin"
def directory1 = "~/retail-store-sample-app/dist/docker-compose"
def directory2 = "~/retail-store-sample-app"
def server = "nobody1305@34.124.165.216"
def cred = "credentials"
def image1 = "nobody1305/redis:6-alpine"
def image2 = "nobody1305/mariadb:10.9"
def image3 = "nobody1305/rabbitmq:3-management"
def image4 = "nobody1305/retail-store-sample-assets:0.4.0"
def image5 = "nobody1305/retail-store-sample-checkout:0.4.0"
def image6 = "nobody1305/retail-store-sample-orders:0.4.0"
def image7 = "nobody1305/retail-store-sample-cart:0.4.0"
def image8 = "nobody1305/retail-store-sample-catalog:0.4.0"
def image9 = "nobody1305/retail-store-sample-ui:0.4.0"
def image10 = "nobody1305/dynamodb-local:1.20.0"
def image11 = "redis:6-alpine"
def image12 = "mariadb:10.9"
def image13 = "rabbitmq:3-management"
def image14 = "public.ecr.aws/aws-containers/retail-store-sample-assets:0.4.0"
def image15 = "public.ecr.aws/aws-containers/retail-store-sample-checkout:0.4.0"
def image16 = "public.ecr.aws/aws-containers/retail-store-sample-orders:0.4.0"
def image17 = "public.ecr.aws/aws-containers/retail-store-sample-cart:0.4.0"
def image18 = "public.ecr.aws/aws-containers/retail-store-sample-catalog:0.4.0"
def image19 = "public.ecr.aws/aws-containers/retail-store-sample-ui:0.4.0"
def image20 = "amazon/dynamodb-local:1.20.0"

pipeline{
    agent any
    stages{
        stage('docker compose down'){
            steps{
                sshagent([cred]){
                    sh """ssh -tt -o StrictHostKeyChecking=no ${server} << EOF
		    cd ${directory1}
		    docker compose down 
                    exit
                    EOF"""
                    }
                }
            }
        stage('pull repository'){
            steps{
                sshagent([cred]){
                    sh """ssh -o StrictHostKeyChecking=no ${server}<< EOF
                    cd ${directory2}
                    git pull ${remote} ${branch}
                    exit
                    EOF"""
                    }
                }
            }

        stage('docker compose up'){
            steps{
                sshagent([cred]){
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                    cd ${directory1}
                    docker compose up -d
                    exit
                    EOF"""
                    }
                }
            }
	
	 stage('docker push'){
            steps{
                sshagent([cred]){
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                    cd ${directory1}
		    docker login
		    docker tag ${image11} ${image1}
                    docker push ${image1}
                    docker tag ${image12} ${image2}
                    docker push ${image2}
                    docker tag ${image13} ${image3}
                    docker push ${image3}
                    docker tag ${image14} ${image4}
                    docker push ${image4}
                    docker tag ${image15} ${image5}
                    docker push ${image5}
                    docker tag ${image16} ${image6}
                    docker push ${image6}
                    docker tag ${image17} ${image7}
                    docker push ${image7}
                    docker tag ${image18} ${image8}
                    docker push ${image8}
                    docker tag ${image19} ${image9}
                    docker push ${image9}
                    docker tag ${image20} ${image10}
                    docker push ${image10}

                    exit
                    EOF"""
                    }
                }
            }
        }
    }

```
### making docker compose for jenkins

![image](https://github.com/fifa0903/axiata-task/assets/132969781/19422e52-21af-47e7-977f-0bfee919ea92)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/6f558c11-287b-49d7-b2da-acad475467ac)

### copy password from docker logs jenkins

![image](https://github.com/fifa0903/axiata-task/assets/132969781/cbf7d8d1-7327-4965-a564-90a7ddfaae58)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/682fc495-7eed-40e5-a7c3-6e4ed399dd9c)

### make sure that ssh-agent is installed

![image](https://github.com/fifa0903/axiata-task/assets/132969781/afd1e881-19fd-452b-9120-2cd2f86f745e)

### installation process

![image](https://github.com/fifa0903/axiata-task/assets/132969781/90814ec9-10c8-4c17-99d5-9201c3fa6633)

### create account

![image](https://github.com/fifa0903/axiata-task/assets/132969781/0cb9abe0-1c98-4881-b88e-847e45b9e803)

### input ip or domain in running app

![image](https://github.com/fifa0903/axiata-task/assets/132969781/fc491a95-b20a-46df-a833-3f076af0fab8)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/039537b4-c640-493a-96d3-694c326f92d3)

### create credential using ssh-key

![image](https://github.com/fifa0903/axiata-task/assets/132969781/ea326fc0-73fe-4dd8-8091-db9b39984630)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/269b3adb-eb7a-4ab4-b6e0-d3f4a300e8e9)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/80cdf392-f92e-4c40-b286-3e22d3cbb59a)

### ssh key for jenkins to server

![image](https://github.com/fifa0903/axiata-task/assets/132969781/dbe5df3c-cd42-4a50-bd96-ee61f85ebb96)

### ssh key for server to github

![image](https://github.com/fifa0903/axiata-task/assets/132969781/27993c2c-298a-4c2f-9e8e-7057447c75ed)

### create pipeline

![image](https://github.com/fifa0903/axiata-task/assets/132969781/73f5844d-c6a1-4dc9-9858-1848bfa7a7cc)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/92a813fd-7e5a-4a44-ab5d-fd0c883796fe)

### make Jenkinsfile

![image](https://github.com/fifa0903/axiata-task/assets/132969781/a9ebf9f0-d7a8-495c-9f22-2ca745e7b7eb)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/b7dfae24-a0e5-49ed-bf8c-ad762e341005)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/54d46ad9-f652-4a03-8ace-8d2366333b3e)

### setup github webhook

![image](https://github.com/fifa0903/axiata-task/assets/132969781/fbe2678e-e5d7-4da2-9e2a-1c4c5c847415)

### execution

![image](https://github.com/fifa0903/axiata-task/assets/132969781/d24f0237-5419-4de3-8e8a-52480f376ac3)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/ab509b0b-4fc5-4b40-85fa-994d728edabe)


