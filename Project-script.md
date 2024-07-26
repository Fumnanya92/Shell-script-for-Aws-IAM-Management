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


# Function to create IAM group
iam_group_creation() {
    # Define a group name
    group="admin"
    # Create IAM group
    aws iam create-group --group-name "$group"
    if [ $? -eq 0 ]; then
        echo "IAM group $group created"
    else
        echo "Failed to create IAM group $group"
    fi
}


#Function to attahce policy to group
attach_admin_policy_to_group() {
aws iam attach-group-policy --group-name admin --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
if [ $? -eq 0 ]; then
   echo "Administrative policy attached to group 'admin' successfully."
else
   echo "Failed to attach policy to group 'admin'."
fi

}



# Function to create IAM users
iam_user_creation() {
    # Array of user names
    names=("Micheal" "Dayo" "Praise" "Chinaza" "Ola")

    # Loop through the array and create IAM users on Aws
    for users in "${names[@]}"; do
        # Create IAM users
        aws iam create-user --user-name "$users"
        if [ $? -eq 0 ]; then
            echo "IAM users $users created"
        else
            echo "Failed to create users $users"
        fi
    done
}

#Function to assign users to group
  assign_users_to_group() {
        for user in "${names[@]}"; do
            aws iam add-user-to-group --user-name "$user" --group-name admin
            if [ $? -eq 0 ]; then
                echo "IAM user '$user' added to group 'admin' successfully."
            else
                echo "Failed to add IAM user '$user' to group 'admin'."
            fi
        done
}



# Function to create EC2 Instances
create_ec2_instances() {

    # Specify the parameters for the EC2 instances
    instance_type="t2.micro"
    ami_id="ami-07d7e3e669718ab45"
    count=2  # Number of instances to create
    region="us-east-2" # Region to create cloud resources
    key_name="ohiopem"

    # Create the EC2 instances
    aws ec2 run-instances \
        --image-id "$ami_id" \
        --instance-type "$instance_type" \
        --count "$count" \
        --key-name "$key_name" \
        --region "$region"
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
    company="everythingfashion"
    region="us-east-2"
    # Array of department names
    departments=("marketing" "sales" "hr" "operations" "media")
    # Loop through the array and create S3 buckets for each department
    for department in "${departments[@]}"; do
        bucket_name="${company}-${department}-data-bucket"
        # Check if the bucket already exists
        if aws s3api head-bucket --bucket "$bucket_name" 2>/dev/null; then
            echo "S3 bucket '$bucket_name' already exists."
        else
            # Create S3 bucket using AWS CLI
            aws s3api create-bucket --bucket "$bucket_name" --region "$region" --create-bucket-configuration LocationConstraint="$region"
            if [ $? -eq 0 ]; then
                echo "S3 bucket '$bucket_name' created successfully."
            else
                echo "Failed to create S3 bucket '$bucket_name'."
            fi
        fi
    done
}


# Function for Argument
check_num_of_args
activate_infra_environment
check_aws_cli
check_aws_profile
# Call Function for group creation
iam_group_creation
# Call function
attach_admin_policy_to_group
# Function to call IAM users
iam_user_creation
# call function to assign
assign_users_to_group
create_ec2_instances
create_s3_buckets
