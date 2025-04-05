File Name - Jenkinsfile

pipeline {
    agent any
    environment {
        DOCKER_HUB_USERNAME = 'rhtvs32'
        SONARQUBE_URL = 'http://sonarqube:9000'
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/rhtvs32/ShoibJAVAapp.git'  // Replace with your GitHub repo URL
            }
        }
        stage('Build') {
            steps {
                sh 'docker run --rm -v $(pwd):/app openjdk:17-jdk-slim javac /app/Main.java'
            }
        }
        stage('Test') {
            steps {
                sh 'docker run --rm -v $(pwd):/app openjdk:11-jdk-slim java -cp /app Main'  // Basic test (expand with real tests)
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'docker run --rm -v $(pwd):/usr/src --network my-cicd-network openjdk:8-jdk-slim /bin/bash -c "sonar-scanner -Dsonar.host.url=${SONARQUBE_URL} -Dsonar.login=admin -Dsonar.password=password123 -Dsonar.projectKey=shoib-java-app"'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${DOCKER_HUB_USERNAME}/java-app:latest .'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'docker login -u $DOCKER_USER -p $DOCKER_PASS'
                    sh 'docker push ${DOCKER_HUB_USERNAME}/java-app:latest'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}