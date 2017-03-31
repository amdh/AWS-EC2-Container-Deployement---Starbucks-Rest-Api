Goal:
Sample implementation of the Restbucks API implemented in Java with Restlet.  Test the API locally with Postman, Deploy and test using a Local Docker Host, and finally deploy to AWS EC2 Container Service.

SOURCE:  https://github.com/paulnguyen/cmpe281/tree/master/restlet/starbucks_v1 (Links to an external site.)

1. Compile, Build JAR and Run Local Instance on port 9090

compile:
    gradle build

jar: compile
    gradle shadowJar

run: 
    echo Starting Service at:  http://localhost:9090
    java -cp build/libs/starbucks-all.jar api.StarbucksServer

2. Test on postman for local running with Starbucks APIs:
	POST  http://localhost:9090/v1/starbucks/order

	{
  "location": "take-out",
  "items": [
    {
      "qty": 1,
      "name": "latte",
      "milk": "whole",
      "size": "large"
    }
  ]
}

3.Compile, Build JAR and Run Local Docker Instance on port 9090

 docker-build: 
    docker build -t starbucks .
    docker images

docker-run:
    docker run --name starbucks -td starbucks
    docker ps

docker-run-host:
    docker run --name starbucks -td --net=host starbucks
    docker ps

4. Test docker running instance API

POST   http://192.168.99.100:9090/v1/starbucks/order

5.. Login to AWS console

- create ecs repository with name - starbucks-v1.
- URL generated - 132838082170.dkr.ecr.us-west-1.amazonaws.com/starbucks-v1:latest

6.  Windows user : open docker terminal and follow below commands

aws ecr get-login --region us-west-1

docker tag starbucks:latest 132838082170.dkr.ecr.us-west-1.amazonaws.com/starbucks-v1:latest

docker push 132838082170.dkr.ecr.us-west-1.amazonaws.com/starbucks-v1:latest

This step will push the image on ECS repository

7. Create task 

-Provide task name - starbucks-v1-task
-Add contianer 
    name :starbucks-v1-container
    image: 132838082170.dkr.ecr.us-west-1.amazonaws.com/starbucks-v1:latest
    memeory limits: Hard limit-128
-create task

8.Create cluster

-Provide details

    name :starbucks-v1-cluster
    Ec2 instance : t2.micro
    Number of Instances :1
    