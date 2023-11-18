pipeline {
    agent any
    environment {
        DOCKERHUB_CREDS = credentials('docker-hub-id')
        EMAIL_RECIPIENT = 'xxx@mail.com'
    }

    stages {
        stage('source') {
            steps {
                git branch: 'main', credentialsId: 'github-creds', url: 'https://github.com/sidiclaudio/realworld-webapp.git'
            }
        }
        
        stage('build') {
            steps {
                echo 'building image and tagging it with $BUILD_NUMBER'
                sh 'docker build -t sidiclaudio/slick-app:$BUILD_NUMBER .'
            }
        }
        
        stage('login to dockerhub ') {
            steps {
                sh 'docker login -u$DOCKERHUB_CREDS_USR -p$DOCKERHUB_CREDS_PSW'
            }
        }
        
        stage('test') {
            steps {
                sh 'docker run --name "container-$BUILD_NUMBER" -t -d -p 80:80 --rm sidiclaudio/slick-app:$BUILD_NUMBER'
            }
        }
        
        stage('Manual Approval') {
            steps {
                script {
                    // Pause and wait for manual approval
                    input message: 'Deploy to production?'

                    // Send an email notification upon approval
                }
            }
        }
        
        stage('deploy') {
            steps {
                sh 'docker push sidiclaudio/slick-app:$BUILD_NUMBER'
            }
        }
    }
    
    post {
        always {
            // Clean up - remove the Docker image locally
            script {
                sh 'docker stop "container-$BUILD_NUMBER"'
                //sh 'docker rm "container-$BUILD_NUMBER"'
            }
        }
    }
}
