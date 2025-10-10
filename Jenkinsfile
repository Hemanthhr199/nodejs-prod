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
                echo 'Deploying to remote Ubuntu server with PM2...'

                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'ubuntu-server', // your Jenkins SSH config
                            transfers: [
                                sshTransfer(
                                    sourceFiles: '**/*',
                                    removePrefix: '',
                                    remoteDirectory: '',
                                    execCommand: '''
                                        

                                        # Install dependencies on remote server
                                        npm install

                                        # Stop existing pm2 app if running (replace "node-app" with your pm2 process name)
                                        pm2 delete node-app || true

                                        # Start app with pm2
                                        pm2 start npm --name "node-app" -- start

                                        # Save the pm2 process list and configure pm2 to startup on boot
                                        pm2 save
                                        pm2 startup systemd -u ubuntu --hp 
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
            echo 'Build, test, and deploy pipeline completed.'
        }
    }
}
