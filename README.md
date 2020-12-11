## Building micorservices application

### Install docker compose via link:
https://docs.docker.com/compose/install/#install-compose

* Setup  username and other environments (optional) at ./docker/.env.example
* Rename .env.example to .env

### Build containers:
``` bash
# Create firewall rules for app and prometheus
gcloud compute firewall-rules create prometheus-default --allow tcp:9090
gcloud compute firewall-rules create puma-default --allow tcp:9292

# Create docker host
docker-machine create --driver google \
                      --google-machine-image https://www.googleapis.com/compute/v1/projects/ubuntu-os-cloud/global/images/family/ubuntu-1604-lts \
                      --google-machine-type n1-standard-1 \
                      --google-zone europe-west1-b \
                      docker-host
 
# configure local env
eval $(docker-machine env docker-host)

# Enter username:
read USER_NAME
# Or enter it below:
##
#  export USER_NAME=<username>
##

# Build images via scripts:
cd monitoring/prometheus/ && docker build -t $USER_NAME/prometheus .
cd ../../comment && bash docker_build.sh
cd ../post-py && bash docker_build.sh
cd ../ui && bash docker_build.sh

```

### To run all services:
``` bash
cd ../docker/ && docker-compose up -d
```

### To stop and remove containers use:
``` bash
docker-compose down
```

* <docker_host_ip>/9292 - Application link
* <docker_host_ip>/9090 - Prometheus link