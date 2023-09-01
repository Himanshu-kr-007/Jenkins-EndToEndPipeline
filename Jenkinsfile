pipeline {
    agent {
        label "DeveloperBuildNode"
    }
    stages {
        stage('Pull Code From GitHub') {
            steps {
                git branch: 'main', url: 'https://github.com/Himanshu-kr-007/Jenkins-EndToEndPipeline.git'
            }
        }
        stage('Build Docker Image'){
            steps{
                sh 'sudo docker build -t himanshukr0612/webserver:${BUILD_TAG} .'
            }
        }
        stage('Push Image Into Docker Hub'){
            steps{
                withCredentials([string(credentialsId: 'DockerHubPassword', variable: 'DockerHubPassword')]) {
                    sh 'sudo docker login -u himanshukr0612 -p $DockerHubPassword'
                }
                sh 'sudo docker push himanshukr0612/webserver:${BUILD_TAG}'
            }
        }
        stage('Deploy Webapp In Dev Environment'){
            steps{
                sh 'sudo docker rm -f mywebserver'
                sh 'sudo docker run -d -p 80:80 --name mywebserver himanshukr0612/webserver:${BUILD_TAG}'

            }
        }
        stage('Launch Container In Testing Environment'){
            steps{
                sshagent(['Environment-Key']) {
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@13.235.69.220 sudo docker rm -f mywebserver"
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@13.235.69.220 sudo docker run -d -p 80:80 --name mywebserver himanshukr0612/webserver:${BUILD_TAG}"
                }
            }
        }
        stage('Testing Phase'){
            steps{
                sh 'curl -sleep http://13.235.69.220:80/ | grep DevOps'
                sh 'curl -sleep http://13.235.69.220:80/ | grep Jenkins'
            }
        }
        stage('Release To Production'){
            steps{
                input(message: "Release To Production?")
            }
        }
        stage('Deploy In Prod'){
            steps{
                sshagent(['Environment-Key']) {
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@13.235.69.220 sudo docker rm -f prod"
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@13.235.69.220 sudo docker run -d -p 1234:80 --name prod himanshukr0612/webserver:${BUILD_TAG}"
                }
            }
        }
    }
}