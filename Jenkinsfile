pipeline {
    agent any

    tools {
        ansible 'ansible'
    }

    stages {

        stage('Checkout Ansible Repo') {
            steps {
                git branch: 'main',
                    credentialsId: 'deploykey-ansible-repo',
                    url: 'git@github.com:YOURUSER/ansible-repo.git'
            }
        }

        stage('Checkout Website Repo') {
            steps {
                dir('web-src') {
                    git branch: 'main',
                        credentialsId: 'deploykey-website-repo',
                        url: 'git@github.com:YOURUSER/website-repo.git'
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'vm2-deploy-key',
                    keyFileVariable: 'SSH_KEY'
                )]) {
                    sh 'cp ${SSH_KEY} /tmp/ssh_key'
                    sh 'chmod 600 /tmp/ssh_key'

                    ansiblePlaybook(
                        playbook: 'deploy.yml',
                        inventory: 'inventory/hosts.ini',
                        colorized: true
                    )

                    sh 'rm -f /tmp/ssh_key'
                }
            }
        }
    }
}
