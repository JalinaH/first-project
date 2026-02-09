pipeline {
    agent any

    environment {
        DOCKER_HOST_NAME = 'dockerhost'
        APP_NAME = 'mywebapp'
        CONTAINER_NAME = 'mywebapp'
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy to Docker Server') {
            steps {
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'dockerhost',
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'target/*.war,Dockerfile',
                                    remoteDirectory: 'docker-app',
                                    execCommand: '''
                                        cd docker-app
                                        docker stop mywebapp || true
                                        docker rm mywebapp || true
                                        docker build -t mywebapp:${BUILD_NUMBER} .
                                        docker run -d -p 8080:8080 --name mywebapp mywebapp:${BUILD_NUMBER}
                                    '''
                                )
                            ]
                        )
                    ]
                )
            }
}
    }
}