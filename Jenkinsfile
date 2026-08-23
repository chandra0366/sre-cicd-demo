pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Source code checked out by Jenkins SCM'
            }
        }

        stage('Setup Environment') {
            steps {
                sh '''
                    python3 -m venv venv
                    ./venv/bin/python -m pip install --upgrade pip
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    ./venv/bin/pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    ./venv/bin/python -c "import flask; print('Flask installation successful')"
            '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                    tar -czf sre-app.tar.gz app.py requirements.txt
                '''
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['jenkins-ec2-key']) {
                    sh '''
                        scp -o StrictHostKeyChecking=no \
                            sre-app.tar.gz \
                            ec2-user@3.80.83.46:/tmp/sre-app.tar.gz

                        ssh -o StrictHostKeyChecking=no \
                            ec2-user@3.80.83.46 \
                            "sudo tar -xzf /tmp/sre-app.tar.gz -C /opt/sre-app && \
                             sudo systemctl restart sre-app"
                    '''
                }
            }
        }

        stage('Health Check') {
            steps {
                sshagent(['jenkins-ec2-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                            ec2-user@3.80.83.46 \
                            "curl -f http://localhost:5000/health"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed!'
        }
    }
}
