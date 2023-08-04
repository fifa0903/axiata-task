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

![image](https://github.com/fifa0903/axiata/assets/132969781/110a435e-7506-4f64-8b6a-500cc7a369c0)

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
![image](https://github.com/fifa0903/axiata/assets/132969781/8ee24c80-17f8-4375-a67c-1e9cf1d35ad7)

![image](https://github.com/fifa0903/axiata/assets/132969781/fd66b0e1-a224-4a8e-a56f-df5880401877)

![image](https://github.com/fifa0903/axiata/assets/132969781/645ed730-027b-4144-ad57-d32dcccd5118)

![image](https://github.com/fifa0903/axiata/assets/132969781/26757b2f-9346-4cb4-9476-c9e9c7ecaf9d)

![image](https://github.com/fifa0903/axiata/assets/132969781/d50dc798-71d2-49b9-aa56-4f9311df26eb)

![image](https://github.com/fifa0903/axiata/assets/132969781/55d26ca5-921b-46e6-a384-48dfc895342f)

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

![image](https://github.com/fifa0903/axiata/assets/132969781/1f27af15-e3df-440a-a8fa-e9f587c75127)

![image](https://github.com/fifa0903/axiata/assets/132969781/44507ebf-ab73-404e-9381-4769e4a40c17)

![image](https://github.com/fifa0903/axiata/assets/132969781/d8790cb1-c4c9-45ae-813d-cfd833b6d3e0)

![image](https://github.com/fifa0903/axiata/assets/132969781/6001877c-75e6-43a6-9e64-30ae75fc8ade)

![image](https://github.com/fifa0903/axiata/assets/132969781/5c494f02-dc81-44d0-80d7-c7cab6ec7d63)

![image](https://github.com/fifa0903/axiata/assets/132969781/fe6fb2e0-1d97-4492-ba5b-090d992e78d8)

# Docker 

### installation

![image](https://github.com/fifa0903/axiata/assets/132969781/3eead336-c7e6-4b32-a5e3-6f668b7b4e99)

### pull repo

![image](https://github.com/fifa0903/axiata/assets/132969781/5127910d-fabd-4f34-8c8e-a1d3ce5a5dd3)

### running docker compose on dist/docker-compose

![image](https://github.com/fifa0903/axiata/assets/132969781/4ab2ccac-bfd7-4120-afd4-b08ae02e6f36)

![image](https://github.com/fifa0903/axiata/assets/132969781/ab93603a-dcf3-4f02-ac95-e525f1bc3134)

the deployment is complete. if there is errorr, it must be setup from app. so we can discuss it with the developer.

![image](https://github.com/fifa0903/axiata/assets/132969781/6ec65165-ef61-44ab-99ff-2ca8370bfc5f)

# Dockerhub

docker login, docker tag, and push image to dockerhub

![image](https://github.com/fifa0903/axiata/assets/132969781/66ad92d3-7c3a-4c3a-8f33-561b4fbe813e)

![image](https://github.com/fifa0903/axiata/assets/132969781/d08b3167-20e6-4614-a903-3f405e5e757f)

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
### making docker file 

![image](https://github.com/fifa0903/axiata/assets/132969781/d7a7dec4-432a-4c14-9e14-c3d31eeaaf01)

![image](https://github.com/fifa0903/axiata/assets/132969781/cb5d83c2-1366-4194-8623-6e0ac0d6fdce)

### copy password from docker logs jenkins

![image](https://github.com/fifa0903/axiata/assets/132969781/920930af-ebc7-41cd-9770-26aaf6b6372b)

![image](https://github.com/fifa0903/axiata/assets/132969781/1a582b58-cbc9-4e54-a8e6-c93b1af2db03)

### make sure that ssh-agent is installed

![image](https://github.com/fifa0903/axiata/assets/132969781/04e4ee9c-0401-4056-b4ae-c5c661abd2f1)

### installation process

![image](https://github.com/fifa0903/axiata/assets/132969781/ba267dd8-e4e1-4ed0-a828-5572d3cba1bf)

### create account

![image](https://github.com/fifa0903/axiata/assets/132969781/b82d6cfd-f632-4766-9675-ca74d02deb3f)

### input ip or domain in running app

![image](https://github.com/fifa0903/axiata/assets/132969781/449d73d9-9153-4781-8196-471a8e15392a)

![image](https://github.com/fifa0903/axiata/assets/132969781/7956b32f-fc03-49c0-a80e-cd6eed93fa9d)

### create credential using ssh-key

![image](https://github.com/fifa0903/axiata/assets/132969781/fff926b1-cd1d-496c-9048-aca31d94a738)

![image](https://github.com/fifa0903/axiata/assets/132969781/2872ca00-5df0-4d5b-b093-4ecb4cc09245)

![image](https://github.com/fifa0903/axiata/assets/132969781/46a40777-dbdb-40ed-9fad-cd2d47b32970)

### ssh key for jenkins to server

![image](https://github.com/fifa0903/axiata/assets/132969781/2f839176-5a11-4f30-bbf6-149a9caee15e)

### ssh key for server to github

![image](https://github.com/fifa0903/axiata/assets/132969781/836a810d-8517-4345-b0bb-f80738ebb91c)

### create pipeline

![image](https://github.com/fifa0903/axiata/assets/132969781/88e12ca3-252e-4965-857c-fbd4ebe16b50)

![image](https://github.com/fifa0903/axiata/assets/132969781/26c44ab0-fa05-4f7e-b4d9-7b4cd5af22d0)

### make Jenkinsfile

![image](https://github.com/fifa0903/axiata/assets/132969781/53ffb1f0-73b5-4139-bc7f-e43c02777753)

![image](https://github.com/fifa0903/axiata/assets/132969781/cd8cda0e-5d1a-45d2-95bd-89a1125ab4a4)

![image](https://github.com/fifa0903/axiata/assets/132969781/6203d0ee-cae0-4873-b75f-6bd5a6182781)

### setup github webhook

![image](https://github.com/fifa0903/axiata/assets/132969781/c28bbf06-6296-4afc-828d-e2ce8da286ae)

### execution

![image](https://github.com/fifa0903/axiata/assets/132969781/b7b4ae01-9168-4b12-8700-4feba1db6c77)

![image](https://github.com/fifa0903/axiata-task/assets/132969781/ab509b0b-4fc5-4b40-85fa-994d728edabe)


