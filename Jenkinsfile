pipeline {
  agent any
 
  tools { 
    maven 'mvn-01'
    git 'git-01'
  }
 
  environment {
    DOCKER_USER = credentials('docker-username')
    DOCKER_PASS = credentials('docker-password')
    IMAGE_REPO  = 'batoullmahmoud/app-java'
    IMAGE_TAG   = "v${env.BUILD_NUMBER}"
  }
 
  stages {
    stage('Checkout') { 
      steps { 
        git 'https://github.com/Hassan-Eid-Hassan/java.git' 
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
    stage('Update ArgoCD') {
      steps {
        // Checkout repo using Jenkins credentials
        git branch: 'main',
            credentialsId: 'github-credentials',
            url: 'git@github.com:batoullmahmoud/argocd.git'
 
        // Now run shell commands
        sh """
            git config user.name "batoullmahmoud"
            git config user.email "batoulmahmoudhassan@gmail.com"
            pwd
            ls
            sed -i "s|image: .*|image: ${IMAGE_REPO}:${IMAGE_TAG}|" deployment.yaml
            git add .
            git commit -m "update image" || true
            git push origin main
        """
      }
    }
  }
}
