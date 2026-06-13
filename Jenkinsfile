pipeline {
    agent any

    tools {
        jdk 'jdk21'
        nodejs 'node16'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        IMAGE_NAME    = 'blujaytech/starbucks'
        CONTAINER_NAME = 'starbucks'
        PORT          = '3000'
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Blujaytech/starbucks.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectName=starbucks \
                        -Dsonar.projectKey=starbucks'''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                }
            }
        }

        stage('Install NPM Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('OWASP FS Scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage('Trivy File Scan') {
            steps {
                sh 'trivy fs . > trivy.txt'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t blujaytech/starbucks .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker') {
                        sh 'docker push ${IMAGE_NAME}:latest'
                    }
                }
            }
        }

        stage('Deploy to Container') {
            steps {
                script {
                    // Stop and remove existing container if running
                    sh '''
                        if docker ps -a --format '{{.Names}}' | grep -q "^${CONTAINER_NAME}$"; then
                            echo "Stopping and removing existing container: ${CONTAINER_NAME}"
                            docker stop ${CONTAINER_NAME}
                            docker rm ${CONTAINER_NAME}
                        else
                            echo "No existing container found. Proceeding with fresh deploy."
                        fi
                    '''
                    // Remove old local image to free up space
                    sh '''
                        if docker images -q ${IMAGE_NAME}:latest | grep -q .; then
                            echo "Removing old image: ${IMAGE_NAME}:latest"
                            docker rmi -f ${IMAGE_NAME}:latest
                        fi
                    '''
                    // Pull latest and run
                    sh '''
                        docker pull ${IMAGE_NAME}:latest
                        docker run -d \
                            --name ${CONTAINER_NAME} \
                            --restart unless-stopped \
                            -p ${PORT}:3000 \
                            ${IMAGE_NAME}:latest
                        echo "Container deployed successfully on port ${PORT}"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline succeeded. Starbucks app is live at http://<your-server-ip>:${PORT}"
        }
        failure {
            echo "Pipeline failed. Check logs above for details."
        }
        always {
            // Clean up dangling images to save disk space
            sh 'docker image prune -f'
        }
    }
}
