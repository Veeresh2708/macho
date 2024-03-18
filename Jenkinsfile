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
                git branch: 'Jacoco/unit_testing', url: 'https://github.com/Veeresh2708/macho.git'
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
            post {
                always {
                junit 'target/surefire-reports/*.xml'
                jacoco execPattern: 'target/jacoco.exec'
                }
            }
        }
        stage('Docker Build and Push') {
            steps {
               withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                sh 'printenv'
                sh 'docker build -t veereshvanga/macho1:""$GIT_COMMIT"" .'
                sh 'docker push veereshvanga/macho1:""$GIT_COMMIT""'
               }
            }
        }
    }
}

