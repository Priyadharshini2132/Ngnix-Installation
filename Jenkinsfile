pipeline {
    agent any

    environment {
        ANSIBLE_HOST_KEY_CHECKING = 'False'  // Disable SSH host key checking
        INVENTORY_FILE = '/etc/ansible/hosts'  // Path to your Ansible inventory file
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Checkout your code repository (where the Ansible playbook is stored)
                git 'https://github.com/Priyadharshini2132/Ngnix-Installation.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Ensure Ansible is installed (if using Jenkins agents that may not have it)
                    sh 'sudo apt-get update'
                    sh 'sudo apt-get install -y ansible'
                }
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                script {
                    // Run the Ansible playbook to install Nginx
                    sh '''
                    ansible-playbook -i $INVENTORY_FILE install_nginx.yml
                    '''
                }
            }
        }

        stage('Verify Installation') {
            steps {
                script {
                    // Optionally verify if Nginx is running
                    sh 'curl -I http://localhost'
                }
            }
        }
    }

    post {
        success {
            echo 'Nginx installation completed successfully.'
        }
        failure {
            echo 'Nginx installation failed.'
        }
    }
}
