pipeline {
    agent any
    environment {
        SONAR_TOKEN = credentials('sonar-token')
    }
    stages {
        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
           
                }
            }
        }
    

        stage('Build & Analyze') {
          steps {
            withSonarQubeEnv('SonarQube') {
              withMaven(maven: 'Maven 3.5') {
                sh 'mvn clean package sonar:sonar'
              }
            }
          }
        }
        
        stage('Quality Gate') {
           steps {
              timeout(time: 1, unit: 'HOURS') {
                 waitForQualityGate abortPipeline: true
              }
           }
        }


        stage('Docker Build and Push') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub', url: '']) {
                    sh 'docker build -t m0hamedzaki/numeric-app:${GIT_COMMIT} .'
                    sh 'docker push m0hamedzaki/numeric-app:${GIT_COMMIT}'
                }
            }
        }
    }
}
