pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
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
    }
}
