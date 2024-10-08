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
                    sh 'docker build -t rc .'
                    sh 'docker tag rc rameshchandrar/deploy:$BUILD_ID'
                    sh 'docker tag rc rameshchandrar/deploy:latest'
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
                    sh 'docker push rameshchandrar/deploy:$BUILD_ID'
                    sh 'docker push rameshchandrar/deploy:latest'
                    sh 'docker rmi -f rameshchandrar/deploy:$BUILD_ID'
                    sh 'docker rmi -f rameshchandrar/deploy:latest'
                }
            }
        }
        
        stage('Run Docker Container') {
            steps {
                script {
                    sh 'docker rm -f deploy'
                    sh 'docker run -it -d --name mytask -p 1234:1234 rameshchandrar/deploy:latest'
                }
            }
        }
   }
}
