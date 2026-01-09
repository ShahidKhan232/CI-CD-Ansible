properties([
    parameters([
        string(defaultValue: 'Installation', name: 'Playbook Name'),
        choice(choices: ['Dry-Run','Playbook-deploy'], name: 'Playbook Action')
    ])
])
pipeline {
    agent any 
    stages {
        stage('Preparing') {
            steps{
                sh 'echo Preparing'
            }
        }
        stage('Git Pulling') {
            steps{
                git branch: 'main', url: 'https://github.com/ShahidKhan232/CI-CD-Ansible.git'
            }
        }
        stage('Playbook Initializing') {
            steps{
                sh 'echo Playbook Initializing'
            }
        }
        stage('Playbook Running') {
            when {
                expression { params['Playbook Action'] == 'Dry-Run' || params['Playbook Action'] == 'Playbook-deploy' }
            }
            steps {
                script {
                    if (params['Playbook Action'] == 'Dry-Run') {
                        sh "ansible-playbook --check -i /etc/ansible/hosts --private-key ${credentials('ansible-connect')} ${params["Playbook Name"]}.yml"
                    } else if (params['Playbook Action'] == 'Playbook-deploy') {
                        ansiblePlaybook credentialsId: 'ansible-connect', disableHostKeyChecking: true, inventory: '/etc/ansible/hosts', playbook: "${params['Playbook Name']}.yml"
                    }
                }
            }
        }
        stage('Playbook deployed') {
            steps{
                sh 'echo Deployment done!!!!'
            }
        }
    }
}