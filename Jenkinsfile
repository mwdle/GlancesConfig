pipeline {
    agent { label 'docker' }
    options { disableConcurrentBuilds() }
    parameters {
        string(name: 'TARGET_HOST', defaultValue: 'pi', description: 'The resolvable hostname or IP of the target machine.')
        credentials(
            name: 'GLANCES_CREDENTIAL_ID',
            credentialType: 'Username with password',
            defaultValue: 'rpi-glances',
            description: 'The Jenkins credential ID for the Glances password (Username with password). The username is ignored.',
            required: true
        )
        credentials(
            name: 'SSH_CREDENTIAL_ID',
            credentialType: 'SSH Username with private key',
            defaultValue: 'rpi-ssh',
            description: 'The Jenkins credential ID for SSH access to the target machine.',
            required: true
        )
        credentials(
            name: 'TARGET_MACHINE_CREDENTIAL_ID',
            credentialType: 'Username with password',
            defaultValue: 'rpi',
            description: 'The Jenkins credential ID containing the sudo password to use on the target machine',
            required: true
        )
    }

    stages {
        stage('Deploy Glances') {
            steps {
                withCredentials([
                    usernamePassword(credentialsId: params.GLANCES_CREDENTIAL_ID, usernameVariable: 'UNUSED_VAR_1', passwordVariable: 'GLANCES_PASS'),
                    sshUserPrivateKey(credentialsId: params.SSH_CREDENTIAL_ID, keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER'),
                    usernamePassword(credentialsId: params.TARGET_MACHINE_CREDENTIAL_ID, usernameVariable: 'UNUSED_VAR_2', passwordVariable: 'SUDO_PASS'),
                ]) {
                    script {
                        sh 'ansible-playbook deploy.yaml -i "$TARGET_HOST," --user "$SSH_USER" --private-key "$SSH_KEY" -e "target_host=$TARGET_HOST" -e "ansible_become_pass=$SUDO_PASS" -e "glances_pass=$GLANCES_PASS"'
                    }
                }
            }
        }
    }
}
