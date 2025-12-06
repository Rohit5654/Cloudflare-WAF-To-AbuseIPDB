/*pipeline {
    agent any
    triggers {
        githubPush() // Auto-trigger on GitHub push
    }
    environment {
        EC2_HOST = "ec2-user@98.81.206.158"
        SSH_KEY = credentials('ec2-ssh-key') // Jenkins credential ID
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Rohit5654/Cloudflare-WAF-To-AbuseIPDB.git'
            }
        }
        stage('Install & Test') {
            steps {
                sh """
                  npm install
                  npm test
                """
            }
        }
        stage('Deploy to EC2') {
            steps {
                sh """
                  # Copy files to EC2
                  scp -o StrictHostKeyChecking=no -i $SSH_KEY -r * $EC2_HOST:/home/ec2-user/node-app

                  # SSH into EC2 and restart app
                  ssh -o StrictHostKeyChecking=no -i $SSH_KEY $EC2_HOST "cd /home/ec2-user/node-app && npm install --production && nohup npm start > app.log 2>&1 &"
                """
            }
        }
    }
    post {
        success {
            echo "Deployment successful!"
        }
        failure {
            echo "Pipeline failed. Check logs."
        }
    }
}
*/


stage('Credential Smoke Test') {
  steps {
    sshagent(credentials: ['ec2-ssh-key']) {
        EC2_HOST = "ec2-user@98.81.206.158"
      sh 'ssh -o StrictHostKeyChecking=no ${EC2_HOST} "echo Connected OK; uname -a"'
    }
  }
}





