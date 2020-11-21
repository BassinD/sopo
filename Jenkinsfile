node('sopo-agent'){
    stage('Clone code source') {
        // Get sopo repo code 
        git branch: 'main', credentialsId: 'GitHub-acc', url: 'https://github.com/BassinD/sopo.git'
    }
    stage('Work with code') {
        // List source
        sh 'ls .'
        // Run ansible galaxy
        sh 'ansible-galaxy install -r ansible/roles/requirements.yml --force'
        // Run ansible-playbook
        sh 'ansible-playbook -i ansible/environments/vbox/inventory ansible/playbooks/ansible.yml --extra-vars "ansible_sudo_pass=${sudo_pass}" -D'
        // Show ansible version
        sh 'ansible --version'
    }
}
