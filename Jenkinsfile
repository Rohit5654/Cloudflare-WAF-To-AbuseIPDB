
pipeline {
    agent any

    triggers {
        // Trigger build when GitHub webhook is received
        githubPush()
    }

    environment {
        NODE_HOME = '/usr/bin' // Adjust if Node.js is installed elsewhere
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Rohit5654/Cloudflare-WAF-To-AbuseIPDB.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'echo "Build step (optional for Node.js)"'
            }
        }

        stage('Deploy') {
            steps {
                // Use PM2 for process management
                sh '''
                if ! command -v pm2 &> /dev/null; then
                  npm install -g pm2
                fi
                pm2 stop all || true
                pm2 start app.js --name node-app
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

