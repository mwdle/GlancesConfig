def jobParams = [
    parameters([
        string(name: 'TARGET_HOST', defaultValue: 'pi', description: 'The resolvable hostname or IP of the target machine.'),
        credentials(
            name: 'SSH_CREDENTIAL_ID',
            // `credentialType` omitted to maintain compatibility with bitwarden-credentials-provider-plugin (Credentials of all types will be shown as options for this parameter)
            defaultValue: 'rpi-ssh',
            description: 'The Jenkins "SSH Username with private key" credential ID for SSH access to the target machine.',
            required: true
        ),
        credentials(
            name: 'TARGET_MACHINE_CREDENTIAL_ID',
            // `credentialType` omitted to maintain compatibility with bitwarden-credentials-provider-plugin (Credentials of all types will be shown as options for this parameter)
            defaultValue: 'rpi',
            description: 'The Jenkins "Username with password" credential ID containing the sudo password to use on the target machine',
            required: true
        ),
        credentials(
            name: 'GLANCES_CREDENTIAL_ID',
            // `credentialType` omitted to maintain compatibility with bitwarden-credentials-provider-plugin (Credentials of all types will be shown as options for this parameter)
            defaultValue: 'rpi-glances',
            description: 'The Jenkins "Username with password" credential ID for the Glances password. The username is ignored.',
            required: true
        )
    ])
]

// Apply job parameters
properties(jobParams)

// Exit early after parameter registration on the first build to avoid failure since the parameters inherently cannot be populated at first build.
if (env.BUILD_NUMBER == '1') {
    echo "Jenkins is initializing this job. Please re-run to start a deployment."
    currentBuild.result = 'NOT_BUILT'
    return
}

pipeline {
    agent { label 'docker' }
    options { disableConcurrentBuilds() }
    stages {
        stage('Deploy Glances') {
            steps {
                withCredentials([
                    sshUserPrivateKey(credentialsId: params.SSH_CREDENTIAL_ID, keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER'),
                    usernamePassword(credentialsId: params.TARGET_MACHINE_CREDENTIAL_ID, usernameVariable: 'UNUSED_VAR_1', passwordVariable: 'SUDO_PASS'),
                    usernamePassword(credentialsId: params.GLANCES_CREDENTIAL_ID, usernameVariable: 'UNUSED_VAR_2', passwordVariable: 'GLANCES_PASS')
                ]) {
                    script {
                        sh 'ansible-playbook deploy.yaml -i "$TARGET_HOST," --user "$SSH_USER" --private-key "$SSH_KEY" -e "target_host=$TARGET_HOST" -e "ansible_become_pass=$SUDO_PASS" -e "glances_pass=$GLANCES_PASS"'
                    }
                }
            }
        }
    }
}
