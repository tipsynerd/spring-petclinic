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
                          def scannerHome = tool 'SonarQube Scanner'
                          def scannerCmd = "${scannerHome}/bin/sonar-scanner"

                          sh "${scannerCmd} \
                            -Dsonar.projectKey=my-project \
                            -Dsonar.sources=src \
                            -Dsonar.host.url=${env.SONARQUBE_HOST} \
                            -Dsonar.login=${env.SONARQUBE_AUTH_TOKEN}"
                        }
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'java -jar target/spring-petclinic-3.0.0-SNAPSHOT.jar'
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
