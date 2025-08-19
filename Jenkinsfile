pipeline {
    agent any

    environment {
        DOCKER_USER = credentials('docker-username')   // Jenkins credentials ID for DockerHub username
        DOCKER_PASS = credentials('docker-password')   // Jenkins credentials ID for DockerHub password
        GIT_CRED    = credentials('github-credentials') // Jenkins credentials ID for GitHub username+token
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/batoullmahmoud/java-app.git',
                    credentialsId: 'github-credentials'
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean test'
            }
        }

        stage('Docker build & push') {
            steps {
                script {
                    sh """
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker build -t $DOCKER_USER/java-app:latest .
                        docker push $DOCKER_USER/java-app:latest
                    """
                }
            }
        }

        stage('Push to GitHub') {
            steps {
                script {
                    sh """
                        git config user.email "jenkins@example.com"
                        git config user.name "Jenkins"
                        git add .
                        git commit -m "CI: Update build artifacts" || echo "No changes to commit"
                        git push https://${GIT_CRED_USR}:${GIT_CRED_PSW}@github.com/batoullmahmoud/java-app.git HEAD:main
                    """
                }
            }
        }
    }
}
