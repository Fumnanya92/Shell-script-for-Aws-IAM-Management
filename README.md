# Shell-script-for-Aws-IAM-Management
Datawise Solutions requires efficient management of AWS Identity and Access Management (IAM) resources. The company is expanding its team and needs to onboard five new employees to access AWS resources securely


## Objectives

1. **Script Enhancement:**
    - Extend the provided script to include IAM management.
    
    ```bash
    #!/bin/bash

    # Environment variables
    ENVIRONMENT=$1

    check_num_of_args() {
    # Checking the number of arguments
    if [ "$#" -ne 0 ]; then
        echo "Usage: $0 <environment>"
        exit 1
    fi
    }

    activate_infra_environment() {
    # Acting based on the argument value
    if [ "$ENVIRONMENT" == "local" ]; then
      echo "Running script for Local Environment..."
    elif [ "$ENVIRONMENT" == "testing" ]; then
      echo "Running script for Testing Environment..."
    elif [ "$ENVIRONMENT" == "production" ]; then
      echo "Running script for Production Environment..."
    else
      echo "Invalid environment specified. Please use 'local', 'testing', or 'production'."
      exit 2
    fi
    }

    # Function to check if AWS CLI is installed
    check_aws_cli() {
        if ! command -v aws &> /dev/null; then
            echo "AWS CLI is not installed. Please install it before proceeding."
            return 1
        fi
    }

    # Function to check if AWS profile is set
    check_aws_profile() {
        if [ -z "$AWS_PROFILE" ]; then
            echo "AWS profile environment variable is not set."
            return 1
        fi
    }

    # Function to create EC2 Instances
    create_ec2_instances() {

        # Specify the parameters for the EC2 instances
        instance_type="t2.micro"
        ami_id="ami-0cd59ecaf368e5ccf"  
        count=2  # Number of instances to create
        region="eu-west-2" # Region to create cloud resources
        
        # Create the EC2 instances
        aws ec2 run-instances \
            --image-id "$ami_id" \
            --instance-type "$instance_type" \
            --count $count
            --key-name MyKeyPair
            
        # Check if the EC2 instances were created successfully
        if [ $? -eq 0 ]; then
            echo "EC2 instances created successfully."
        else
            echo "Failed to create EC2 instances."
        fi
    }

    # Function to create S3 buckets for different departments
    create_s3_buckets() {
        # Define a company name as prefix
        company="datawise"
        # Array of department names
        departments=("Marketing" "Sales" "HR" "Operations" "Media")
        
        # Loop through the array and create S3 buckets for each department
        for department in "${departments[@]}"; do
            bucket_name="${company}-${department}-Data-Bucket"
            # Create S3 bucket using AWS CLI
            aws s3api create-bucket --bucket "$bucket_name" --region your-region
            if [ $? -eq 0 ]; then
                echo "S3 bucket '$bucket_name' created successfully."
            else
                echo "Failed to create S3 bucket '$bucket_name'."
            fi
        done
    }

    check_num_of_args
    activate_infra_environment
    check_aws_cli
    check_aws_profile
    create_ec2_instances
    create_s3_buckets
    ```

2. **Define IAM User Names Array:**
    - Store the names of the five IAM users in an array for easy iteration during user creation.

    ```bash
    # Define IAM User Names Array
    iam_users=("user1" "user2" "user3" "user4" "user5")
    ```

3. **Create IAM Users:**
    - Iterate through the IAM user names array and create IAM users for each employee using AWS CLI commands.

    ```bash
    create_iam_users() {
        for user in "${iam_users[@]}"; do
            aws iam create-user --user-name "$user"
            if [ $? -eq 0 ]; then
                echo "IAM user '$user' created successfully."
            else
                echo "Failed to create IAM user '$user'."
            fi
        done
    }
    ```
    ![image](https://github.com/Fumnanya92/Darey.io_Projects/assets/104866089/0134cbd8-1d7c-4236-9c93-de428358bbcc)


4. **Create IAM Group:**
    - Define a function to create an IAM group named "admin" using the AWS CLI.

    ```bash
    create_iam_group() {
        aws iam create-group --group-name admin
        if [ $? -eq 0 ]; then
            echo "IAM group 'admin' created successfully."
        else
            echo "Failed to create IAM group 'admin'."
        fi
    }
    ```
    ![image](https://github.com/Fumnanya92/Darey.io_Projects/assets/104866089/699b86ad-6c37-49e2-be25-24f76924b145)


5. **Attach Administrative Policy to Group:**
    - Attach an AWS-managed administrative policy (e.g., "AdministratorAccess") to the "admin" group to grant administrative privileges.

    ```bash
    attach_admin_policy_to_group() {
        aws iam attach-group-policy --group-name admin --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
        if [ $? -eq 0 ]; then
            echo "Administrative policy attached to group 'admin' successfully."
        else
            echo "Failed to attach policy to group 'admin'."
        fi
    }
    ```



6. **Assign Users to Group:**
    - Iterate through the array of IAM user names and assign each user to the "admin" group using AWS CLI commands.

    ```bash
    assign_users_to_group() {
        for user in "${iam_users[@]}"; do
            aws iam add-user-to-group --user-name "$user" --group-name admin
            if [ $? -eq 0 ]; then
                echo "IAM user '$user' added to group 'admin' successfully."
            else
                echo "Failed to add IAM user '$user' to group 'admin'."
            fi
        done
    }
    ```

## Project Deliverables

1. Comprehensive documentation detailing your entire thought process in developing the script.
2. Link to the script used for remote execution.
[link to the complete script](https://github.com/Fumnanya92/Darey.io_Projects/blob/main/Linux_project/project_script/linux_administration_and_shell_script_script.md)

## Remote Deployment Process

1. **Script Enhancement:**
    - Enhance the provided script to include IAM management.
    - Ensure the script supports multiple Linux distributions including CentOS.

2. **Infrastructure Setup:**
    - Launch EC2 instances with Amazon Linux, Ubuntu, and CentOS.
    - Configure instance attributes and security groups.

3. **Remote Execution and Deployment:**
    - Upload the enhanced script to a centralized server.
    - Execute the script remotely on each instance.
    ![image](https://github.com/Fumnanya92/Darey.io_Projects/assets/104866089/728040e9-cfaf-404c-971c-ea9ac11c8516)

   ![image](https://github.com/Fumnanya92/Darey.io_Projects/assets/104866089/61424904-f782-44e2-8c4b-db9979b716bd)
   ![image](https://github.com/Fumnanya92/Darey.io_Projects/assets/104866089/1e17e4aa-0c6c-469b-b177-bfa613c8d4e8)


