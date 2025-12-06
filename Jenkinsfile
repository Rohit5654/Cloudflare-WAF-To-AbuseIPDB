
stage('EC2 Connectivity Smoke Test') {
  steps {
    withCredentials([sshUserPrivateKey(
      credentialsId: 'ec2-ssh-key',          // exact ID in Jenkins Credentials
      keyFileVariable: 'KEYFILE',            // Jenkins exports a temp private key file path
      usernameVariable: 'SSHUSER'            // Jenkins exports the username from the cred
    )]) {
      sh '''
        set -e
        HOST="${EC2_IP}"                      # EC2_IP should be defined in environment{}
        echo "=== Smoke test to ${SSHUSER}@${HOST} ==="

        echo "[1/2] TCP reachability on port 22..."
        (timeout 5 bash -c "</dev/tcp/$HOST/22") && echo "OK: Port 22 open" || { echo "ERROR: Port 22 closed"; exit 1; }

        echo "[2/2] SSH command execution..."
        ssh -o StrictHostKeyChecking=no -i "$KEYFILE" ${SSHUSER}@${HOST} \
          "echo Connected OK; whoami; uname -a; echo HOME=\$HOME; date"
      '''
    }
  }
}
