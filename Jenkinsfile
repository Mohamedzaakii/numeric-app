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
    

        stage('Build and Analysis') {
          steps {
           // withSonarQubeEnv('SonarQube') {
                sh '''
                   mvn clean package sonar:sonar \
                   -Dsonar.token=$SONAR_TOKEN \
                   -Dsonar.projectKey=numeric-application 
            
                   '''
              }
            }
          }
        
        
        stage('Quality Gate') {
           steps {
              timeout(time: 10, unit: 'MINUTES') {
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
