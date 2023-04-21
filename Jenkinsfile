pipeline {
    agent any

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


        stage('Deploy') {
            steps {
                sh './mvnw package'
//                 sh 'nohup java -jar target/*.jar --server.port=8081'
            }
        }
        stage('Deploy with Ansible') {
            environment {
                ANSIBLE_HOST_KEY_CHECKING = "False"
            }
            steps {
                sh 'ansible-playbook -i ansible/inventory ansible/playbook.yml'
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
