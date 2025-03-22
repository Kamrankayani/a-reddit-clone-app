pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        APP_NAME = "reddit-clone-app"
        RELEASE = "1.0.0"
        DOCKER_USER = "kamrandevops23"
        DOCKER_PASS = "dockerhub"
        
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"  // Correct interpolation
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"  // Correct interpolation
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Git CheckOut') {
            steps {
                git branch: 'main', changelog: false, credentialsId: 'github', poll: false, url: 'https://github.com/Kamrankayani/a-reddit-clone-app.git'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(installationName: 'sonar-server' , credentialsId: 'jenkins-agent2') {
                   sh '''$SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=reddit-clone-ci '''
    
                   }
            }
        }
        stage('Quality Gate') {
            steps {
              waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-agent2'
            }
        }
        stage('Install dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Trivy FS Scan') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
        stage("Build & Push Docker Image") {
    steps {
        script {
            docker.withRegistry('', DOCKER_PASS) {
                def dockerImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")  // Build Image
                
                dockerImage.push("${IMAGE_TAG}")  // Push with version
                dockerImage.push("latest")  // Push latest tag
            }
        }
    }
}

    }
}
