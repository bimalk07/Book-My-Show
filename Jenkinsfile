pipeline {
    agent any
    tools {
        jdk 'JAVA_HOME'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner' // Jenkins > Tool
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'feature', url: 'https://github.com/bimalk07/Book-My-Show.git'
                sh 'ls -la'  // Verify files after checkout
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {     // Jenkins > System
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \           
                        -Dsonar.projectName=bimal-project \
                        -Dsonar.projectKey=bimal-project
                    '''
                }
            }
        }
        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh '''
                    cd bookmyshow-app
                    ls -la  # Verify package.json exists
                    if [ -f package.json ]; then
                        rm -rf node_modules package-lock.json  # Remove old dependencies
                        npm i chokidar
                        npm install  # Install fresh dependencies
                    else
                        echo "Error: package.json not found in bookmyshow-app!"
                        exit 1
                    fi
                '''
            }
        }
    }
}
