
pipeline {
  agent any

  environment {
    EC2_IP = '98.81.206.158'   // set your IP
  }

  stages {
    stage('EC2 Connectivity Smoke Test') {
      steps {
        withCredentials([sshUserPrivateKey(
          credentialsId: 'ec2-ssh-key',
          keyFileVariable: 'KEYFILE',
          usernameVariable: 'SSHUSER'
        )]) {
          sh '''
            set -e
            HOST="${EC2_IP}"
            echo "=== Smoke test to ${SSHUSER}@${HOST} ==="

            echo "[1/2] TCP reachability on port 22..."
            (timeout 5 bash -c "</dev/tcp/$HOST/22") && echo "OK: Port 22 open" || { echo "ERROR: Port 22 closed"; exit 1; }

            echo "[2/2] SSH command execution..."
            ssh -o StrictHostKeyChecking=no -i "$KEYFILE" ${SSHUSER}@${HOST} \
              "echo Connected OK; whoami; uname -a; echo HOME=\\$HOME; date"
          '''
        }
      }
    }

    stage('Deploy to EC2') {
      steps {
        withCredentials([sshUserPrivateKey(
          credentialsId: 'ec2-ssh-key',
          keyFileVariable: 'KEYFILE',
          usernameVariable: 'SSHUSER'
        )]) {
          sh '''
            set -e
            HOST="${EC2_IP}"
            REMOTE_DIR="/home/${SSHUSER}/node-app"

            echo "=== Deploy to ${SSHUSER}@${HOST} ==="
            ssh -o StrictHostKeyChecking=no -i "$KEYFILE" ${SSHUSER}@${HOST} "mkdir -p ${REMOTE_DIR}"
            scp -o StrictHostKeyChecking=no -i "$KEYFILE" -r * ${SSHUSER}@${HOST}:${REMOTE_DIR}
            ssh -o StrictHostKeyChecking=no -i "$KEYFILE" ${SSHUSER}@${HOST} \
              "cd ${REMOTE_DIR} && npm install --production && nohup npm start > app.log 2>&1 &"
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
