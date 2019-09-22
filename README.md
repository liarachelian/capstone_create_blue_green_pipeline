# Udacity Capstone - Cloud DevOps

## Project Overview
In this project we had to build a CI/CD pipeline for a microservices application. I've designed this CI/CD pipeline to work wth Jenkins and it follows this blue/green deployment methodology. This project has **three** braches **master**, **blue** & **green**.

In terms of the type of application this pipeline is deploying I've decided to create a simple **PHP** application which is built into a docker image. I'm reasons for this is I've a application development by trade and we build our API in **PHP**.

## Installation
In order to run this CI/CD pipeline you will first need access to **Jenkins**, and have an **AWS** account. You will also need to install the **Blue Ocean** and **AWS pipline** plugins.

Once you have your Jenkins and AWS enviroments setup you will need to first run my #Create cluster pipeline# which can be found [Here].(https://github.com/adamsteff/capstone_create_cluster_pipeline) 

## Overview
The pipeline has been designed so when code changes are made to the different branches the step run will are conditional.
The steps in the pipeline are as follows.

### Lint HTML
This step is used to make sure thier is no syntax error in the code

### Build Docker Image
This step builds a new docker image of the website

### Push Docker Image
This step builds a uploads the new docker image to [DockerHub](https://cloud.docker.com) 

### Set Kubectl Context to Cluster
This step switches to the correct cluster using the following command `kubectl config use-context arn:aws:eks:ap-southeast-2:048353547478:cluster/capstonecluster` 

### Create Blue Controller
This step creates the blue controller using the following command `kubectl apply -f ./blue-controller.json`

### Create Green Controller
This step creates the green controller using the following command `kubectl apply -f ./green-controller.json`

### Deploy to Production
This is an interactive step where the user decides if the changes are to be released to production or not.

### Rollout Blue Changes
This step rolls out the last changes which were just push to DockerHub to the blue pods using the command `kubectl rolling-update blueversion --image=adamsteff/capstonerepository:latest`

### Rollout Green Changes
This step rolls out the last changes which were just push to DockerHub to the green pods using the command `kubectl rolling-update greenversion --image=adamsteff/capstonerepository:latest`

### Create Blue-Green service
This steps applies the change to loadblancer by running `kubectl apply -f ./blue-green-service.json`

## Usage

### Master Branch
When your code change are commited and push in the master branch the follow steps will occur.
- Lint HTML
- Build Docker Image
- Push Docker Image
- Set Kubectl Context to Cluster

All the other steps in the pipeline are skipped and no upates to the website will be made.

### Blue Branch
When your code change are commited and push in the blue branch the follow steps will occur.
- Lint HTML
- Build Docker Image
- Push Docker Image
- Set Kubectl Context to Cluster
- Create Blue Controller
- Deploy to Production
- Rollout Blue Changes
- Create Blue-Green service - the Loadbalancer will be pointed to the blue pods

### Green Branch
When your code change are commited and push in the green branch the follow steps will occur.
- Lint HTML
- Build Docker Image
- Push Docker Image
- Set Kubectl Context to Cluster
- Create Green Controller
- Deploy to Production
- Rollout Green Changes
- Create Blue-Green service - the Loadbalancer will be pointed to the green pods
