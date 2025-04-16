# :rocket: Deploy Web Application on AWS Using Jenkins, Ansible and Terraform

This project automates the deployment of a web application on an AWS EC2 instance using:
    
-  **Terraform** for infrastructure provisioning
-  **Jenkins** for CI/CD
-  **Ansible** for configuration management

---

## :bookmark: Prerequisites: 

### 1. Install Jenkins on Your Local Machine

  Run the following commands: 

    1. sudo apt update && sudo apt upgrade -y
    2. sudo apt install -y fontconfig openjdk-17-jre
    3. java -version
    4. wget -O - https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
    5. echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
    6. sudo apt update
    7. sudo apt install -y jenkins
    8. sudo systemctl enable --now jenkins
    9. sudo systemctl status jenkins
    
- Access Jenkins
	```
    https://<server-ip-address>:8080
- Unlock Jenkins
    ```
    Get the initial admin password by running:
	sudo cat /var/lib/jenkins/secrets/initialAdminPassword

### 2. Install Ansible

 ```
    sudo apt install ansible -y
    ansible --version 
 ```

 ## :snowflake: Configure Jenkins

 1. Create a new pipeline in Jenkins.
 2. Generate a GitHub personal access token and add your credentials to Jenkins (Manage Jenkins → Credentials).
 3. In the pipeline configuration: 
    - Enable "POLL SCM"
    - Set the cron schedule to run every minute:
    
    ```
        * * * * *
    ```
4. Specify your GitHub repository URL and branch name.

 ## :arrow_forward: Deploy web application

 1. Fork and Clone the Repo
    ```
    git clone https://github.com/<your-username>/Terraform-Ansible-Jenkins.git
    cd Terraform-Ansible-Jenkins
    ```

 2. Create IAM User in AWS
    
    - Create a new IAM user on AWS Cosnole For Security
    - Assign necessary permissions (e.g., AmazonEC2FullAccess, AmazonS3FullAccess or Admin).
    - Store the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY securely.
    - Add them as environment variables or credentials in Jenkins.

 3. Create an S3 Bucket
    
    - Create an S3 bucket to store the Terraform state file.
    - Ensure the bucket name matches the one in your terraform.tf file.
    - If using a different name, update it in your Terraform configuration.

 4. Run the Pipeline
    
    - Make a small change in the repository and push it.
    - Jenkins will automatically detect the change and trigger the pipeline.
    - After successful execution, your web application should be deployed on the EC2 instance.

 5. Access the Application
    
    - Find the EC2 instance’s public IP from the AWS Console or by using:

    ```
    terraform output expose_public_ip_address
    ```
    - Open a browser and visit:
    
    ```
       http://<public-ip>
    ```

TADA!! YOUR WEBSITE IS LIVE!! :smiley:
