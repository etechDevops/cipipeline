pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="619238069440"
        AWS_DEFAULT_REGION="us-west-2"
        IMAGE_REPO_NAME="cipipeline"
        IMAGE_TAG="latest"
        REPOSITORY_URI= "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }

    stages {
       
        stage('Logging into AWS ECR') {
            steps {
                    
                script {sh "aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 619238069440.dkr.ecr.us-west-2.amazonaws.com"
                }
            }
        }
        stage('Cloning git') {
            steps {
                script {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/etechDevops/cipipeline.git']])
                }
            }
        }
        
        stage ('Building Image') {
            steps {
                script {
                    dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }
        stage ('Pushing to ECR') {
            steps {
                script{
                    docker.withRegistry('https://****************.dkr.ecr.us-west-2.amazonaws.com', 'ecr:us-west-2:aws-credentials') {                    
                    dockerImage.push("${env.BUILD_NUMBER}")
                    dockerImage.push("latest")
                    }
                }
            }
        }
        stage ('Updating the Deployment File') {
            environment {
                GIT_REPO_NAME = "cipipeline"
                GIT_USER_NAME = "etechdevops"
            }
            steps {
                withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]){
                    sh '''
                    
                        git pull https://github.com/etechdevops/cipipeline.git
                        git config  user.email "etechdevops@gmail.com"
                        git config  user.name "etechdevops"
                        BUILD_NUMBER=${BUILD_NUMBER}
                        sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" ArgoCD/deployments.yml
                        git add ArgoCD/deployments.yml
                        git commit -m "updated the image ${BUILD_NUMBER}"
                        git push @github.com/${GIT_USER_NAME}/${GIT_REPO_NAME">@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME">@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME">https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                        
                       
                    '''
                }
            }
        }
    }

}
