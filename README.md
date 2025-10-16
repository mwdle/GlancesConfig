# Glances Ansible Deployment

This project contains an Ansible playbook and a Jenkinsfile to automate the deployment of [Glances](https://github.com/nicolargo/glances) in webserver mode on a remote machine (e.g., a Raspberry Pi).

The deployment is designed to be secure and idempotent, using a dedicated system user and managing Glances with a `systemd` service.

## Table of Contents

- [Features](#features)
- [How It Works](#how-it-works)
- [Prerequisites](#prerequisites)
- [File Structure](#file-structure)
- [How to Use](#how-to-use)
- [License](#license)
- [Disclaimer](#disclaimer)

## Features

- **Automated Installation:** Installs the latest version of Glances via `pipx`.
- **Cross-Platform:** Supports both Debian Linux distributions ONLY (e.g., DietPi, Ubuntu, Debian, etc.).
- **Secure:**
  - Runs Glances as a dedicated, non-login system user (`glances`).
  - Configures password authentication for server mode.
- **Robust:** Manages Glances with a `systemd` service that automatically restarts on failure.
- **Parameterized:** The Jenkins pipeline allows you to dynamically specify the target host and credentials for each deployment.

## How It Works

The Ansible playbook follows several best practices for a clean and secure deployment:

1. **Installation via Pip:** The playbook installs Glances using `pipx` instead of a `apt`. This ensures that the latest version of Glances is always installed, providing access to the newest features and bug fixes. Additionally this approach works around a known issue, being that the Debian package does _not_ include the necessary files for the Glances web server to function.
2. **Dedicated System User:** For security, a system user named `glances` with no login shell (`/usr/sbin/nologin`) is created. The Glances service runs entirely under this unprivileged account, adhering to the principle of least privilege.
3. **Automated Password Setup:** Glances does not have a non-interactive method for setting its password. The playbook automates this interactive-only prompt by using Ansible's `expect` module, which securely provides the password to the command-line prompts.
4. **Systemd Service Management:** A `systemd` service file is created to manage the Glances process. This allows the service to start automatically on boot and restart if it ever crashes, ensuring high availability.

## Prerequisites

1. **Ansible:** The Jenkins agent must have Ansible installed.
2. **Jenkins Credentials Plugin:** The `Credentials Binding` and `SSH Credentials` plugins must be installed in Jenkins.
3. **Jenkins Credentials:**
   - **Glances Password:** A "Username with password" credential must be created in Jenkins to store the desired Glances password.
   - **SSH Key:** An "SSH Username with private key" credential must be created for SSH access to the target machine.
   - **Sudo Password:** A "Username with password" credential must be created to store the sudo password for the SSH user on the target machine.

## File Structure

- `deploy.yml`: The main Ansible playbook that performs the installation and configuration.
- `Jenkinsfile`: The declarative pipeline script for automating the deployment via Jenkins.
- `README.md`: This documentation file.

## How to Use

1. **Add Credentials to Jenkins:**

   - Create a "Username with password" credential (e.g., ID: `rpi-glances`). The username is ignored.
   - Create an "SSH Username with private key" credential for your target device (e.g., ID: `rpi-ssh`).
   - Create another "Username with password" credential for the sudo password (e.g., ID: `rpi`). The username is ignored.

2. **Configure the Jenkins Pipeline:**

   - Create a new "Pipeline" job in Jenkins.
   - Configure the "Pipeline script from SCM" to point to this repository.
   - The `Jenkinsfile` will be automatically detected.

3. **Run the Pipeline:**
   - Click "Build with Parameters".
   - Verify or update the `TARGET_HOST` to match your device's IP or hostname.
   - Select the correct credential IDs from the dropdowns for the Glances password, SSH key, and sudo password.
   - Click "Build".

The pipeline will execute the Ansible playbook, deploying and configuring Glances on the specified host.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Disclaimer

This repository and its contents are provided as-is for personal and educational use. The author assumes no responsibility for any errors, omissions, or consequences that may arise from the use of the information and scripts provided.
