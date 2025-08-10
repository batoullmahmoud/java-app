pipeline{
    agent {
        label "java"
    }
    tools{
        maven 'mvn-3-5-4'
        jdk 'java-11'
    }
    environment{
        DOCKER_USER = credentials('docker-username')
        DOCKER_PASS = credentials('docker-password')
    }
    stages{
        // stage("Dependancy check"){
        //     steps{
        //         sh "mvn dependency-check:check"
        //         dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
        //     }
        // }
        stage("build app"){
            steps{
                sh "mvn package install"
            }
        }
        stage("archive app"){
            steps{
                archiveArtifacts artifacts: '**/*.jar', followSymlinks: false
            }
        }
        stage("docker build"){
            steps{
                sh "docker build -t hassaneid/iti-java:v${BUILD_NUMBER}-test ."
                sh "docker images"
            }
        }
        // stage("docker push"){
        //     steps{
        //         sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}"
        //         sh "docker push hassaneid/iti-java:v${BUILD_NUMBER}"
        //     }
        // }
    }
    post {
        always {
            sh "ls -al"
        }
        success {
            cleanWs()
        }
        failure {
           echo "Failed"
        }
    }
}