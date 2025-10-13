pipeline {
    agent { label 'docker' }

    parameters {
        string(name: 'TARGET_HOST', defaultValue: 'pi', description: 'The resolvable hostname or IP of the target machine.')
        credentials(
            name: 'GLANCES_PASSWORD_CREDENTIAL_ID',
            credentialType: 'Username with password',
            defaultValue: 'rpi-glances',
            description: 'The Jenkins credential ID for the Glances password (Username with password). The username is ignored.',
            required: true
        )
        credentials(
            name: 'PI_SSH_CREDENTIAL_ID',
            credentialType: 'SSH Username with private key',
            defaultValue: 'rpi-ssh',
            description: 'The Jenkins credential ID for SSH access to the target machine.',
            required: true
        )
    }

    stages {
        stage('Deploy Glances') {
            steps {
                withCredentials([
                    usernamePassword(credentialsId: params.GLANCES_PASSWORD_CREDENTIAL_ID, usernameVariable: 'GLANCES_USER', passwordVariable: 'GLANCES_PASS'),
                    sshUserPrivateKey(credentialsId: params.PI_SSH_CREDENTIAL_ID, keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')
                ]) {
                    script {
                        sh 'ansible-playbook deploy.yaml -i "$TARGET_HOST," --user "$SSH_USER" --private-key "$SSH_KEY" -e "target_host=$TARGET_HOST" -e "glances_pass=$GLANCES_PASS"'
                    }
                }
            }
        }
    }
}
