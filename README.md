## Dockerfile-apache-test

#### Create Image

docker build -t ajmezav/webserver:latest .

#### Push iamge

docker push ajmezav/webserver:latest

#### Run Docker

docker container run -d -p 80:80 ajmezav/webserver:latest

#### Entrar por bash en un Docker

docker exec -it CONTAINER_ID /bin/bash

#### Stop and delete docker


docker container rm CONTAINER_ID -f

#### Delete Iamge 

docker rmi -f ajmezav/webserver:latest

#### Deployment

kubectl apply -f deployment_apache.yaml

#### port-forward

kubectl port-forward svc/webserver 80:8080
