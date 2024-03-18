pipeline {
  agent any
  tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

  stages {
        stage('Checkout from Git') {
            steps {
                git branch: 'master', url: 'https://github.com/Veeresh2708/macho.git'
            }
        }
        stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }
        stage('Unit Tests') {
            steps {
              sh "mvn test"
            }
        }
    }
}

