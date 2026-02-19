pipeline {
    agent {
        label 'slave'
    }
    options {
        ansiColor('xterm')
    }
    parameters {
        string(name: 'hostname', defaultValue: '', description: 'Enter the IP')
        string(name: 'region', defaultValue: 'ap-south-1', description: 'Enter the Region')
        choice(name: 'add_or_extend', choices: ['Add Disk', 'Extend Existing Disk'], description: 'Select "Add Disk" if you want to add a disk to the existing server. Select "Extend Existing disk" if you want to extend a existing disk')
        string(name: 'mountPoint_name', defaultValue: '', description: 'Enter the Mount Point name, which you want to create or extend. For ex - /apps')
        string(name: 'disk_size', defaultValue: '0', description: 'Enter the Disk Size, which you want to create or extend. For ex - 10')
    }
    stages {

        stage('Git SCM checkout') {
            steps {
                    checkout scm
                  }
            }

        stage('Update the Given Hostname/IP in the ansible hosts file') {
            steps {
                script {
                    if(params.hostname) {
                        writeFile file: 'ansible/hosts', 
                                 text: "${params.hostname}"
                    } else {
                        echo "No HOSTNAME parameter provided."
                    }
                }
            }
        }
        stage('Debug Permissions') {
            steps {
                sh """
                    echo "Current user: \$(whoami)"
                    echo "Hosts file permissions:"
                    ls -l ansible/hosts
                    pwd
                """
            }
        }
        
        stage('Add Disk') {
            when {
                expression { return params.add_or_extend == 'Add Disk' }
            }
            steps {
                script {
                    if (params.mountPoint_name && params.disk_size) {
                        withCredentials([
                            sshUserPrivateKey(
                                credentialsId: 'ansible_key',
                                keyFileVariable: 'SSH_KEY_FILE'
                            ),
                            [
                                $class: 'AmazonWebServicesCredentialsBinding',
                                credentialsId: 'aws_ansible_creds'
                            ]
                        ]) {
                                sh """
                                    export MP='${params.mountPoint_name}'
                                    export SIZE='${params.disk_size}'
                                    export ANSIBLE_HOST_KEY_CHECKING=False
                                    export AWS_REGION='${params.region}'
    
                                    /opt/jenkins/venv/ansible/bin/ansible-playbook \\
                                    -i ansible/hosts \\
                                    ansible/playbooks/add_vol.yaml \\
                                    --user=ansible_user \\
                                    --private-key="\$SSH_KEY_FILE" \\
                                    -e "mountPoint_name=\$MP" \\
                                    -e "ec2_instance_volume_size=\$SIZE" \\
                                    -e "aws_default_region=\$AWS_REGION"
                                """
                        }
                    } else {
                        echo "Mountpoint name OR disk size has not provided."
                    }
                }
            }
        }

        stage('Extend Existing Disk') {
            when {
                expression { return params.add_or_extend == 'Extend Existing Disk' }
            }
            steps {
                script {
                    if (params.mountPoint_name && params.disk_size) {
                        withCredentials([
                            sshUserPrivateKey(
                                credentialsId: 'ansible_key',
                                keyFileVariable: 'SSH_KEY_FILE'
                            ),
                            [
                                $class: 'AmazonWebServicesCredentialsBinding',
                                credentialsId: 'aws_ansible_creds'
                            ]
                        ]) {
                                sh """
                                    export MP='${params.mountPoint_name}'
                                    export SIZE='${params.disk_size}'
                                    export ANSIBLE_HOST_KEY_CHECKING=False
                                    export AWS_REGION='${params.region}'
    
                                    /opt/jenkins/venv/ansible/bin/ansible-playbook \\
                                    -i ansible/hosts \\
                                    ansible/playbooks/extend_volume.yaml \\
                                    --user=ansible_user \\
                                    --private-key="\$SSH_KEY_FILE" \\
                                    -e "mountPoint_name=\$MP" \\
                                    -e "ec2_instance_volume_size=\$SIZE" \\
                                    -e "aws_default_region=\$AWS_REGION"
                                """
                        }
                    } else {
                        echo "Mountpoint name OR disk size has not provided."
                    }
                }
            }
        }

    }
}
