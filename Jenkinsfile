pipeline {
    environment {
        imageName = "spiroskafk/falcon-demo-app:${env.BUILD_ID}"
        customImage = ''
    }

    agent any
    stages {


        stage('Start Container for Testing') {
            steps {
                script {
                    sh 'docker-compose rm -f; docker-compose up -d images'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    sh 'pip3 install -r requirements.txt; python3 -m pytest tests'
                }
            }
            post {
                success {
                    script {
                        sh 'docker-compose down'
                    }
                }
                failure {
                    script {
                        sh 'docker-compose down'
                    }
                }
            }
        }

        stage('Build Image') {
            steps {
                script {
                    customImage = docker.build(imageName)
                }
            }
        }

        stage('Push to Registry') {
            when {
                branch "main"
            }
            steps {
                script {
                    docker.withRegistry('', 'Dockerhub-access') {
                        customImage.push()
                    }
                }
            }
        }
    }
}