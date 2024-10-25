pipeline {
    agent any
    
    environment {
        SONAR_HOST = 'http://3.80.75.38:9000/'
        SONAR_TOKEN = 'sqa_525b043838540148ab22924325465e382fe48e8f'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/abhichaurasiya2022/helloworld.git'
            }
        }
        
        stage('Install Python and Dependencies') {
            steps {
                sh '''
                    # Ensure Python 3 is installed
                    sudo apt-get update
                    sudo apt-get install -y python3 python3-pip
                    
                    # Upgrade pip and install dependencies
                    python3 -m pip install --upgrade pip
                    pip3 install -r requirements.txt
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
            cleanWs()
        }
    }
}
