pipeline {
    agent any

    tools {
        nodejs "nodejs"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-username/your-repo.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test -- --watchAll=false'
            }
        }

        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'sonar-scanner'
            }
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=react-app \
                        -Dsonar.sources=src \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.login=${SONAR_AUTH_TOKEN}'''
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
