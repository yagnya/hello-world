pipeline {
    agent any
    
    environment {
     DOCKER_TAG = getVersion()
    }

    tools {
         maven 'maven-home'
    }
    stages {
        stage('SCM') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/yagnya/hello-world.git'
            }
        }
        stage('Maven build') {
            steps {
                sh "mvn clean package"
            }
        }
        stage('Docker build') {
            steps {
                sh "docker container prune -f"
                sh "docker image prune -a -f"
                sh "docker build . -t yagnya42/testapp:${DOCKER_TAG}"
            }
        }
        stage('Docker push') {
            steps {
               withCredentials([string(credentialsId: 'docker_hub_password', variable: 'docker_hub')]) {
                sh "docker login -u yagnya42 -p ${docker_hub}"
                sh "docker push yagnya42/testapp:${DOCKER_TAG}"
                }
            }
        }
        stage('Ansible deployment docker') {
            steps {
              ansiblePlaybook disableHostKeyChecking: true, extras: "-e dockertag=${DOCKER_TAG}", installation: 'ansible', playbook: 'docker-deploy.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
