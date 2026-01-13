pipeline {
    agent any

    tools {
        // NodeJS already configured globally
        nodejs 'nodejs'
    }

    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Docker build') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        env.APP_PORT = '3000'
                        env.IMAGE    = 'nodemain:v1.0'
                        env.CNAME    = 'nodemain'
          } else if (env.BRANCH_NAME == 'dev') {
                        env.APP_PORT = '3001'
                        env.IMAGE    = 'nodedev:v1.0'
                        env.CNAME    = 'nodedev'
          } else {
                        error('Only main and dev branches are supported')
                    }
                }

                sh 'docker build -t $IMAGE .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
          set -e

          docker stop "$CNAME" >/dev/null 2>&1 || true
          docker rm   "$CNAME" >/dev/null 2>&1 || true

          docker run -d --name "$CNAME" \
            --expose "$APP_PORT" \
            -p "$APP_PORT":3000 \
            "$IMAGE"

          echo "Application URL: http://localhost:$APP_PORT"
        '''
            }
        }
    }
}
