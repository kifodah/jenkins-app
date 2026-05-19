pipeline {
    agent any
    
    triggers {
        githubPush()
    }
    
    stages {
        stage('Build') {
            steps {
                    sh '''
                        npm config set cache /tmp/.npm-cache --global
                        npm cache clean --force
                        npm ci --legacy-peer-deps
                        npm run build
                       '''
                    }
                }
            }
        }
        
        stage('Test') {
            steps {
                script {
                    docker.image('node:18').inside {
                        sh '''
                            test -f build/index.html
                            CI=true npm test -- --watchAll=false
                        '''
                    }
                }
            }
        }
        
        stage('Deploy to Render') {
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