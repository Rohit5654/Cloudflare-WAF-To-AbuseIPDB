
pipeline {
  agent any

  // If you can't use GitHub plugin triggers, configure the webhook in the job UI,
  // or temporarily enable polling:
  // triggers { pollSCM('H/5 * * * *') } // every 5 minutes

  environment {
    // For Amazon Linux use ec2-user; for Ubuntu AMIs use ubuntu
    EC2_USER = 'ec2-user'
    EC2_IP   = '98.81.206.158'
  }

  stages {
    stage('Checkout') {
      steps {
        // For Multibranch jobs, use: checkout scm
        git branch: 'main', url: 'https://github.com/Rohit5654/Cloudflare-WAF-To-AbuseIPDB.git'
      }
    }

    stage('Install & Test') {
      steps {
        sh '''
          npm install
          npm test
        '''
      }
    }

    stage('EC2 Connectivity Smoke Test') {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'ec2-ssh-key',
                                           keyFileVariable: 'KEYFILE',
                                           usernameVariable: 'SSHUSER')]) {
          sh '''
            HOST="${EC2_IP}"

            echo "Testing TCP reachability to $HOST:22 ..."
            (timeout 5 bash -c "</dev/tcp/$HOST/22") && echo "Port 22 open" || { echo "Port 22 closed"; exit 1; }

            echo "SSH smoke test..."
            ssh -o StrictHostKeyChecking=no -i "$KEYFILE" ${SSHUSER}@${HOST} "echo Connected OK; whoami; uname -a; echo HOME=$HOME; date"
          '''
        }
      }
    }

    stage('Deploy to EC2') {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'ec2-ssh-key',
                                           keyFileVariable: 'KEYFILE',
                                           usernameVariable: 'SSHUSER')]) {
          sh '''
            HOST="${EC2_IP}"

            # Ensure target dir exists
            ssh -o StrictHostKeyChecking=no -i "$KEYFILE" ${SSHUSER}@${HOST} "mkdir -p /home/${SSHUSER}/node-app"

            # Copy sources
            scp -o StrictHostKeyChecking=no -i "$KEYFILE" -r * ${SSHUSER}@${HOST}:/home/${SSHUSER}/node-app

            # Install deps & (re)start app
            ssh -o StrictHostKeyChecking=no -i "$KEYFILE" ${SSHUSER}@${HOST} \
              "cd /home/${SSHUSER}/node-app && npm install --production && nohup npm start > app.log 2>&1 &"
          '''
        }
      }
    }
  }

  post {
    success { echo 'Deployment successful!' }
    failure { echo 'Pipeline failed. Check logs.' }
  }
}

