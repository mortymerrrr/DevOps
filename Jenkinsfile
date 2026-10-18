pipeline {
    agent any

    environment {
        ANSIBLE_HOST_KEY_CHECKING = 'false'
        ANSIBLE_FORCE_COLOR = 'true'
        MAC_USER = 'aleksandrlebedev'
        PROJECT_DIR = '/Users/aleksandrlebedev/DevOps/lab_4/DevOps/ansible'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out DevOps repository...'
                sh 'ls -la'
            }
        }

        stage('Prepare Environment') {
            steps {
                echo 'Stage 1: Preparing environment (Docker, repos)...'
                sshagent(credentials: ['ansible-node-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ${MAC_USER}@host.docker.internal \
                        "cd ${PROJECT_DIR} && \
                         ansible-playbook -i hosts-jenkins jenkins-playbook.yml --tags prepare"
                    '''
                }
            }
        }

        stage('Start Applications') {
            steps {
                echo 'Stage 2: Starting microservices...'
                sshagent(credentials: ['ansible-node-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ${MAC_USER}@host.docker.internal \
                        "cd ${PROJECT_DIR} && \
                         ansible-playbook -i hosts-jenkins jenkins-playbook.yml --tags deploy"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs above.'
        }
    }
}