pipeline {
    agent {
        docker {
            image 'maven:3.9.5-eclipse-temurin-17-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'mvn -B -DskipTests clean package' 
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input(message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed', parameters: [choice(name: 'ACTION', choices: 'Proceed\nAbort', description: 'Pilih tindakan')], submitter: 'admin')
                    if (userInput == 'Proceed') {
                        echo 'Melanjutkan ke tahap Deploy...'
                    } else {
                        error 'Pipeline dihentikan oleh pengguna.'
                    }
                }
            }
        }

        stage('Deploy') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 
                echo 'Waiting for 1 minute...'
                sh 'sleep 1m'
            }
        }
    }
}