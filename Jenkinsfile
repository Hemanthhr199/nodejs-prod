pipeline {
    agent any

    environment {
        NODE_ENV = 'development'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Hemanthhr199/nodejs-prod.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'npm test'
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying to remote Ubuntu server...'

                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'ubuntu-server', // Your SSH config name in Jenkins
                            transfers: [
                                sshTransfer(
                                    sourceFiles: '**/*',
                                    removePrefix: '',
                                    remoteDirectory: '',
                                    execCommand: '''
                                        # Kill any process using port 3000
                                        fuser -k 3000/tcp || true

                                    
                                        npm install --production

                                        nohup npm start > output.log 2>&1 &
                                    '''
                                )
                            ],
                            usePromotionTimestamp: false,
                            verbose: true
                        )
                    ]
                )
            }
        }
    }

    post {
        always {
            echo 'Build, test and deploy pipeline completed.'
        }
    }
}
