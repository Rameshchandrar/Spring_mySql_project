pipeline {
    agent any
    
    tools {
        maven 'maven'
    }
    
    stages {
        stage('Prepare Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'sudo docker build -t rc .'
                    sh 'sudo docker tag rc rameshchandrar/kubernetes:$BUILD_ID'
                    sh 'sudo docker tag rc rameshchandrar/kubernetes:latest'
                }
            }
        }
        
        stage('Docker Login') {
    steps {
        script {
            withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USER --password-stdin"
            }
        }
    }
}

        
        stage('Push Docker Image') {
            steps {
                script {
                    sh 'sudo docker push rameshchandrar/kubernetes:$BUILD_ID'
                    sh 'sudo docker push rameshchandrar/kubernetes:latest'
                    sh 'sudo docker rmi -f rameshchandrar/kubernetes:$BUILD_ID'
                    sh 'sudo docker rmi -f rameshchandrar/kubernetes:latest'
                }
            }
        }
        
        stage('Run Docker Container') {
            steps {
                script {
                    sh 'sudo docker rm -f deploy'
                    sh 'sudo docker run -it -d --name deployment -p 1234:1234 rameshchandrar/kubernetes:latest'
                }
            }
        }
   }
}
