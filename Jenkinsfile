pipeline {
    agent any

    parameters {
        string(name: 'TARGET_HOST', defaultValue: 'pi', description: 'The resolvable hostname or IP of the target machine.')
        credentials(
            name: 'GLANCES_PASSWORD_CREDENTIAL_ID',
            type: 'com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl',
            defaultValue: 'Glances - rpi',
            description: 'The Jenkins credential ID for the Glances password (Username with password). The username is ignored.',
            required: true
        )
        credentials(
            name: 'PI_SSH_CREDENTIAL_ID',
            type: 'com.cloudbees.plugins.credentials.impl.BasicSSHUserPrivateKey',
            defaultValue: 'rpi-ssh',
            description: 'The Jenkins credential ID for SSH access to the target machine.',
            required: true
        )
    }

    stages {
        stage('Deploy Glances') {
            steps {
                withCredentials([
                    usernamePassword(credentialsId: params.GLANCES_PASSWORD_CREDENTIAL_ID, passwordVariable: 'GLANCES_PASS'),
                    sshUserPrivateKey(credentialsId: params.PI_SSH_CREDENTIAL_ID, keyFileVariable: 'SSH_KEY_FILE', usernameVariable: 'SSH_USER')
                ]) {
                    script {
                        env.TARGET_HOST = params.TARGET_HOST
                        sh 'ansible-playbook deploy_glances.yml -i "$TARGET_HOST," --user "$SSH_USER" --private-key "$SSH_KEY_FILE" -e "target_hosts=$TARGET_HOST" -e "glances_password=$GLANCES_PASS"'
                    }
                }
            }
        }
    }
}
