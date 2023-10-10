pipeline {
    agent any

    parameters {
        choice(name: 'DEPLOY_ENV', choices: ['blue', 'green'], description: 'Select deployment environment')
    }

    environment {
        KUBECONFIG = credentials('KUBECONFIG')
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    def dockerImageTag
                    def dockerImageName = "your-dockerhub-username/your-app-name"

                    if (params.DEPLOY_ENV == 'blue') {
                        dockerImageTag = "blue-${env.BUILD_NUMBER}"
                    } else {
                        dockerImageTag = "green-${env.BUILD_NUMBER}"
                    }

                    // Build and push Docker image
                    withCredentials([usernamePassword(credentialsId: 'DOCKERHUB_CREDENTIALS', usernameVariable: 'DOCKERHUB_USR', passwordVariable: 'DOCKERHUB_PSW')]) {
                        sh """
                            docker build -t ${dockerImageName}:${dockerImageTag} .
                            docker login -u ${DOCKERHUB_USR} -p ${DOCKERHUB_PSW}
                            docker push ${dockerImageName}:${dockerImageTag}
                        """
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                script {
                    def kubectlCmd = "kubectl --kubeconfig=${KUBECONFIG} apply -f k8s/${params.DEPLOY_ENV}-deployment.yaml"

                    // Apply the Kubernetes deployment file for the selected environment (blue/green)
                    sh "${kubectlCmd}"
                }
            }
        }

        stage('Swap Environments') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                script {
                    def newEnv = params.DEPLOY_ENV == 'blue' ? 'green' : 'blue'
                    sh "kubectl --kubeconfig=${KUBECONFIG} patch svc your-app-name -p '{\"spec\":{\"selector\":{\"env\":\"${newEnv}\"}}}'"
                }
            }
        }
    }

    post {
        failure {
            // Handle deployment failure here (e.g., rollback)
        }
        success {
            // Perform any post-deployment tasks (e.g., cleanup)
        }
    }
}
