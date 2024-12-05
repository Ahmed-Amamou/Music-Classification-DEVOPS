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
                        url: 'https://github.com/koukaa-500/MusicClassifier.git',
                        credentialsId: "1"
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
                withCredentials([string(credentialsId: 'DockerHubAccessToken', variable: 'DOCKER_TOKEN')]) {
                    bat 'echo %DOCKER_TOKEN% | docker login -u ahmedamamou --password-stdin'
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
