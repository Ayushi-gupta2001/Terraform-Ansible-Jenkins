pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_KEY_ID')
    }
    stages {
        stage('Create') {
            steps {
                echo 'Creating resources on AWS EC2 using terraform script......'
                sh '''
                cd ./infra-as-code/
                terraform fmt
                terraform init
                terraform validate
                terraform plan
                terraform apply --auto-approve
                '''
            }
            post{
                failure {
                    echo "Deleting the resources if resources creation get fails"
                    sh '''
                    cd ./infra-as-code/
                    terraform destroy --auto-approve
                    '''
                } 
            }
        }
        stage('Fetch public Ip of EC2 machine') {
            steps {
                echo 'Fetching public ip of ec2 resource'
                sh '''  
                cd ./infra-as-code/
                terraform refresh
                PUBLIC_IP_ADDRESS=$(terraform output expose_public_ip_address)
                echo $PUBLIC_IP_ADDRESS
                mkdir -p '../ansible'
                echo "[WEB_SERVER]" > ../ansible/inventory
                cat ../ansible/inventory
                echo "${PUBLIC_IP_ADDRESS} ansible_user=ubuntu ansible_ssh_private_key_file="${WORKSPACE}/infra-as-code/MyEC2InstanceLoginKey" ansible_ssh_common_args='-o StrictHostKeyChecking=no'" >> ../ansible/inventory
                cat ../ansible/inventory
                '''
            }
        }
        stage('check ansible installed'){
            steps {
                echo "checking ansible version"
                sh '''
                    ansible --version
                    '''
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploy the code using ansible on newly created resources by terraform'
                sh '''
                    cd $WORKSPACE
                    ansible-playbook -i ./ansible/inventory ./ansible/deploy.yml
                '''
            }
        }
    }
}
