# Jenkins Pipeline for Blue-Green Deployment

This repository contains a Jenkinsfile that defines a Jenkins pipeline for blue-green deployment of a Dockerized application to a Kubernetes cluster.

## Pipeline Stages

The pipeline consists of the following stages:

1. **Checkout Code**: This stage checks out the source code from the version control system.

2. **Build and Push Docker Image**: This stage builds a Docker image of the application and pushes it to Docker Hub. The image is tagged with the selected deployment environment (blue or green) and the build number.

3. **Deploy to Kubernetes**: This stage applies the Kubernetes deployment file for the selected environment. This deploys the new version of the application to the inactive environment.

4. **Swap Environments**: This stage updates the Kubernetes service to point to the new environment, effectively switching traffic from the old environment to the new environment.

## Parameters

The pipeline has one parameter:

- `DEPLOY_ENV`: The deployment environment (blue or green). This determines which environment the new version of the application is deployed to.

## Environment Variables

The pipeline uses the following environment variables:

- `KUBECONFIG`: The ID of the Jenkins credential that contains the kubeconfig file for the Kubernetes cluster.

- `DOCKERHUB_CREDENTIALS`: The ID of the Jenkins credential that contains the Docker Hub username and password.

## Usage

To use this pipeline, you need to have Jenkins installed and configured with the necessary plugins (Pipeline, Docker Pipeline, etc.). You also need to have a Kubernetes cluster and Docker Hub account.

To run the pipeline, follow these steps:

1. Create a new Jenkins job and select 'Pipeline' as the job type.

2. In the job configuration, select 'Pipeline script from SCM' and enter the URL of your Git repository.

3. In the 'Script Path' field, enter the path to the Jenkinsfile in your repository.

4. Save the job configuration and click 'Build with Parameters' to run the pipeline.
