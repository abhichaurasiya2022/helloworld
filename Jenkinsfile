// Save this as 'Jenkinsfile' in your repository
pipeline {
    agent any
    
    environment {
        // Replace with your actual SonarQube IP address from Terraform output
        SONAR_HOST = 'http://34.233.71.226:9000'
        SONAR_TOKEN = credentials('sonar-token')
    }
    
    tools {
        // Add this if you're using specific Python version
        python 'Python3'
    }
    
    stages {
        stage('Checkout') {
            steps {
                // Replace with your actual repository URL
                git 'https://github.com/your-username/your-python-repo.git'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh '''
                    python -m pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('SonarQube') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=python-app \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=${SONAR_HOST} \
                            -Dsonar.login=${SONAR_TOKEN} \
                            -Dsonar.python.version=3 \
                            -Dsonar.language=python \
                            -Dsonar.sourceEncoding=UTF-8
                        """
                    }
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
    
    post {
        always {
            // Clean workspace after build
            cleanWs()
        }
    }
}