pipeline {
    agent any
    triggers {
        githubPush()
    }

    stages {
        stage('Build')  {
            agent {
                docker {
                    image 'node:18-alpine'    // ← Changed from alpine
                    reuseNode true
                }
            }
            steps {
                sh '''
                npm --version
                node --version
                npm cache verify
                npm ci --verbose
                npm run build
                '''
            }
        }
        
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'    // ← Changed from alpine
                    reuseNode true
                }
            }
            steps {
                sh '''
                test -f build/index.html
                CI=true npm test
                '''
            }
        }
        
        stage('Deploy to Render') {
            agent {
                docker {
                    image 'node:18'
                    reuseNode true
                }
            }
            steps {
                withCredentials([string(credentialsId: 'Jenkinsapp', variable: 'RENDER_API_KEY')]) {
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
            echo 'Pipeline completed - app deployed to Render'
        }
        failure {
            echo 'Pipeline failed - deployment to Render did not occur'
        }
    }
}