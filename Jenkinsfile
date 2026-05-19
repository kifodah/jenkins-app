pipeline {
    agent any

    stages {
        stage('w/o Docker') {
            steps {
                sh '''
                   echo "without Docker"
                   ls -la
                   touch container no.txt
                 '''
            }
        }
        stage('w/ Docker') {
            agent {
                docker {
                    image 'node:18-alpine'
                }
            }
            steps {
                sh '''
                  echo "with Docker"
                  ls -la
                  touch container yes.txt
                '''
            }
        }
    }
}