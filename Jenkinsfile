pipeline {
    agent any

    stages {
        stage('Test SSH Connection') {
            steps {
                sshagent(['jenkins-ec2-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                        ec2-user@3.80.83.46 \
                        "hostname && echo Jenkins-to-EC2-SSH-SUCCESS"
                    '''
                }
            }
        }
    }
}
