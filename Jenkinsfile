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
        stage("Sonarqube Analysis's") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''mvn sonar:sonar \ 
                    -Dsonar.projectName=Macho1 \
                    -Dsonar.projectKey=Macho1 \
                    -Dsonar.host.url=http://35.230.59.202:9000'''
                }
            }
        }
        stage("quality gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
    }
}

