pipeline {
    agent any
    stages {
        stage('Checkout Code') {
            steps {
                echo 'Checking out code from private GitHub repository...'
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/master']], // Adjust branch name if needed
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    userRemoteConfigs: [[
                        url: 'https://github.com/Ahmed-Amamou/Music-Classification-DEVOPS.git',
                        credentialsId: "music-CLassification"
                    ]]
                ])
            }
        }
        stage('Build Frontend') {
            steps {
                script {
                    dir('frontend') {
                        bat 'docker build -t front .'
                    }
                }
            }
        }
        stage('Build SVM') {
            steps {
                script {
                    dir('Backend/SVM') {
                        bat 'docker build -t svm .'
                    }
                }
            }
        }
        
  stage('Build VGG19') {
            steps {
                script {
                    dir('Backend/VGG19') {
                        bat 'docker build -t vgg19 .'
                    }
                }
            }
        }
        
        // stage('Deploy'){
        //     steps{
        //          script {
                    
        //                 bat 'docker-compose up'
                    
        //         }
        //     }
        // }
        stage('Push Docker Images') {
             steps {
                script {
                // Login to Docker Hub using the credentials stored in Jenkins
                withCredentials([usernamePassword(credentialsId: 'DockerHubAccessToken', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                }

                // Tag and push Docker images
                bat 'docker tag front ahmedamamou/frontend:latest'
                bat 'docker tag svm ahmedamamou/svm_image:latest'
                bat 'docker tag vgg19 ahmedamamou/vgg19_image:latest'
                bat 'docker push ahmedamamou/frontend:latest'
                bat 'docker push ahmedamamou/svm_image:latest'
                bat 'docker push ahmedamamou/vgg19_image:latest'
                }
             }
        }

        stage('Test'){
            steps{
                script{
                    
                    dir('Backend/SVM') {
                        bat 'py test.py'
                    }
                }
            }
        }
        
      
    }
    post {
        success {
            echo 'All images built, tagged, and pushed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
