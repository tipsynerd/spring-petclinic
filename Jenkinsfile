pipeline {
    agent any
//     tools {
//         maven 'Maven'
//         jdk 'Java'
//         sonarqube 'SonarQube'
//     }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/tipsynerd/spring-petclinic'
            }
        }
        stage('Build') {
            steps {
                sh './mvnw clean package'
            }
        }
        stage('SonarQube Analysis') {
            environment {
                SONARQUBE_HOME = tool 'SonarQube Scanner'
                SONARQUBE_HOST = 'http://SonarQube:9000'
                SONARQUBE_AUTH_TOKEN = credentials('sonarqube-token')
            }
            steps {
                withSonarQubeEnv('SonarQube Scanner') {
                    script {
                          sh 'cd ..'
                          sh "./mvnw clean verify sonar:sonar -Dsonar.projectKey=pet-clinic -Dsonar.projectName=pet-clinic"
                        }
                }
            }
        }

        stage('Deploy') {
            steps {
                sh './mvnw package'
                sh 'nohup java -jar target/*.jar --server.port=8081'
            }
        }
    }

    post {
        always {
            publishHTML([allowMissing: false,
                         alwaysLinkToLastBuild: false,
                         keepAll: true,
                         reportDir: 'target/site/jacoco',
                         reportFiles: 'index.html',
                         reportName: 'Code Coverage Report'])
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }

        success {
            echo 'Success'
        }

        failure {
            echo 'Failed'
        }
    }
}
