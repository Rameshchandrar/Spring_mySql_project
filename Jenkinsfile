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
                    sh 'sudo docker tag rc rameshchandrar/deploy:$BUILD_ID'
                    sh 'sudo docker tag rc rameshchandrar/deploy:latest'
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
                    sh 'sudo docker push rameshchandrar/deploy:$BUILD_ID'
                    sh 'sudo docker push rameshchandrar/deploy:latest'
                    sh 'sudo docker rmi -f rameshchandrar/deploy:$BUILD_ID'
                    sh 'sudo docker rmi -f rameshchandrar/deploy:latest'
                }
            }
        }
        
        stage('Run Docker Container') {
            steps {
                script {
                    sh 'sudo docker rm -f deploy'
                    sh 'sudo docker run -it -d --name mytask -p 1234:1234 rameshchandrar/deploy:latest'
                }
            }
        }
   }
}
