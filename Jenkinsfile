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
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
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
              sh "mvn test jacoco:report" 
            }
            post {
                always {
                junit 'target/surefire-reports/*.xml'
                jacoco execPattern: 'target/jacoco.exec'
                }
            }
        }
        stage("Sonarqube Analysis's") {
            steps {
                sh """
              $SCANNER_HOME/bin/sonar-scanner \
              -Dsonar.projectName=Macho1 \
              -Dsonar.projectKey=Macho1 \
              -Dsonar.sources=. \
              -Dsonar.host.url= "$sonar-server" \
              -Dsonar.login=$SONAR_TOKEN
            """
                //withSonarQubeEnv('sonar-server') {
                    //sh '''mvn sonar:sonar 00-Dsonar.projectName=Macho1 -Dsonar.projectKey=Macho1 -Dsonar.host.url=http://34.83.246.71/:9000 -Dsonar.login=sqp_a9406e2370dfee4eb89869a0185a10af6a818316'''
                    //sh ''' mvn clean verify $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Macho1 \ -Dsonar.projectKey=Macho1 '''}
            }
        }
        stage("quality gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('Trivy FS Scanning') {
            steps {
              sh 'trivy fs . > trivyfs.txt'
            }
        }
        stage('Docker Build and Push') {
            steps {
                script{
                   withDockerRegistry(credentialsId: 'docker-hub', toolName: 'docker'){
                   //withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                   sh 'printenv'
                   sh 'docker build -t springboot_app:""$GIT_COMMIT"" .'
                   sh 'docker tag springboot_app:""$GIT_COMMIT"" veereshvanga/macho1:$BUILD_NUMBER'
                   sh 'docker push veereshvanga/macho1:""$BUILD_NUMBER""'
                   }
               }
            }
        }
        stage('Trivy Scanning') {
            steps {
              sh 'trivy image nasi101/netflix:latest > trivyimage.txt'
            }
        }
    }
}

