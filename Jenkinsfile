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
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }
      stage('Checkout from Git') {
            steps {
                git branch: 'master', url: 'https://github.com/Veeresh2708/macho.git'
            }
        }
        stage("Sonarqube Analysis's") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar:sonar \
                          -Dsonar.projectName=Macho \
                          -Dsonar.projectKey=Macho \
                          -Dsonar.host.url=http://35.230.59.202:9000 \
                          -Dsonar.login=sqp_9849ac270211c889bf9fb8e7898017b18cc18811'''
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

