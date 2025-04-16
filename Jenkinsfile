pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_KEY_ID')
    }
    stages {
        stage('Provision AWS EC2 Instance with Terraform') {
            steps {
                echo 'Provisioning AWS EC2 instance using Terraform...'
                sh '''
                cd ./infra-as-code/
                terraform init
                terraform validate
                terraform plan
                terraform apply --auto-approve
                '''
            }
            /***
            post{
                failure {
                    echo "Cleaning up resources as Terraform apply failed"
                    sh '''
                    cd ./infra-as-code/
                    terraform destroy --auto-approve
                    '''
                } 
            }
            ***/
        }
        stage('Retrieve EC2 Public IP') {
            steps {
                echo 'Retrieving public IP address of the EC2 instance...'
                sh '''  
                cd ./infra-as-code/
                PUBLIC_IP_ADDRESS=$(terraform output expose_public_ip_address)
                mkdir -p '../ansible'
                echo "[WEB_SERVER]" > ../ansible/inventory
                chmod 600 ${WORKSPACE}/infra-as-code/MyEC2InstanceLoginKey
                echo "${PUBLIC_IP_ADDRESS} ansible_user=ubuntu ansible_ssh_private_key_file="${WORKSPACE}/infra-as-code/MyEC2InstanceLoginKey" ansible_ssh_common_args='-o StrictHostKeyChecking=no'" >> ../ansible/inventory
                '''
            }
        }
        stage('Deploy Application using Ansible') {
            steps {
                echo 'Deploying application to EC2 instance using Ansible...'
                sh '''
                    cd $WORKSPACE
                    ansible-playbook -i ./ansible/inventory ./ansible/deploy.yml
                '''
            }
        }
    }
}
