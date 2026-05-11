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
    

        stage('SonarQube-SAST') {
            steps {
                sh 'mvn clean package -DskipTests=true'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                sh """
                    mvn sonar:sonar \
                        -Dsonar.projectKey=numeric-application \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.token=${SONAR_TOKEN}
                """
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
