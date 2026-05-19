pipeline {
    agent any
    
    triggers {
        githubPush()
    }
    
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm config set cache /tmp/.npm-cache
                    npm cache clean --force
                    npm ci --legacy-peer-deps
                    npm run build
                '''
            }
        }
        
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    test -f build/index.html
                    CI=true npm test -- --watchAll=false
                '''
            }
        }
        
        stage('Deploy to Netlify') {
            steps {
                withCredentials([string(credentialsId: 'Jenkinsapp', variable: 'Jenkinsapp')]) {
                    sh '''
                        curl -X POST -d {} https://api.netlify.com/build_hooks/6a0c380cd01047b8764e9232
                    '''
                }
            }
        }
    }
    
    post {
        always {
            junit testResults: '**/test-results.xml', allowEmptyResults: true
        }
        success {
            echo 'Pipeline completed - app deployed to Netlify'
        }
        failure {
            echo 'Pipeline failed - deployment to Netlify did not occur'
        }
    }
}