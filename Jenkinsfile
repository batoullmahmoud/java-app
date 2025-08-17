@Library('iti-sharedlib')_

properties([
    disableConcurrentBuilds()
])

node {
    def javaHome = tool name: 'java-11', type: 'jdk'
    def mavenHome = tool name: 'mvn-3-5-4', type: 'maven'
    def DOCKER_USER = credentials('docker-username')
    def DOCKER_PASS = credentials('docker-password')

    stage("Get code") {
        checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/batoullmahmoud/java-app.git']])
    }
    
    stage("build app") {
        env.JAVA_HOME = javaHome
        env.PATH = "${javaHome}/bin:${mavenHome}/bin:${env.PATH}"
        sh 'java -version'
        sh 'mvn -version'
        sh 'mvn clean package'
    }
    
    stage("archive app") {
        archiveArtifacts artifacts: '**/target/*.jar', followSymlinks: false
    }
    
    stage('docker build') {
        sh 'docker build -t iti-java:${BUILD_NUMBER} .'
    }
    
    stage("push java app image") {
        // Option 1: Use shared library methods (correct syntax)
        def docker = new com.iti.docker()
        docker.login "${DOCKER_USER}", "${DOCKER_PASS}"  // No parentheses
        docker.push "iti-java", "${BUILD_NUMBER}"       // No parentheses
        
        // Option 2: Or use direct docker commands
        // sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}"
        // sh "docker push iti-java:${BUILD_NUMBER}"
    }
    
    stage("update argocd") {
        dir('argocd') {
            checkout scmGit(branches: [[name: '*/main']], userRemoteConfigs: [[url: 'https://github.com/batoullmahmoud/argocd-app.git']])
            sh "sed -i 's#image: .*#image: iti-java:${BUILD_NUMBER}#' iti-dev/deployment.yaml"
        }
    }
}
