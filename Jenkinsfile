pipeline {
  agent any

  tools { 
    maven 'mvn-01'
  }

  environment {
    DOCKER_USER = credentials('docker-username')
    DOCKER_PASS = credentials('docker-password')
    IMAGE_REPO  = 'batoullmahmoud/app-java'
    IMAGE_TAG   = "v${BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') { 
      steps { 
        git branch: 'main', url: 'git@github.com:Hassan-Eid-Hassan/java.git'
      } 
    }

    stage('Build & Test') { 
      steps { 
        sh 'mvn -B clean package' 
      } 
    }

    stage('Docker build & push') {
      steps {
        sh """
          docker build -t ${IMAGE_REPO}:${IMAGE_TAG} .
          echo "${DOCKER_PASS}" | docker login -u "${DOCKER_USER}" --password-stdin
          docker push ${IMAGE_REPO}:${IMAGE_TAG}
          docker logout || true
        """
      }
    }

    stage('Push to GitHub') {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'github-ssh', 
                                           keyFileVariable: 'SSH_KEY')]) {
          sh '''
            # Configure Git
            git config --global user.name "Jenkins CI"
            git config --global user.email "jenkins@example.com"

            # Push using SSH key
            GIT_SSH_COMMAND="ssh -i $SSH_KEY -o StrictHostKeyChecking=no" \
            git push origin main
          '''
        }
      }
    }
  }
}
