# Terraform-Essentials

Activate Cloud Shell
Cloud Shell is a virtual machine that is loaded with development tools. It offers a persistent 5GB home directory and runs on the Google Cloud. Cloud Shell provides command-line access to your Google Cloud resources.

Click Activate Cloud Shell Activate Cloud Shell icon at the top of the Google Cloud console.
When you are connected, you are already authenticated, and the project is set to your PROJECT_ID. The output contains a line that declares the PROJECT_ID for this session:

Your Cloud Platform project in this session is set to YOUR_PROJECT_ID
gcloud is the command-line tool for Google Cloud. It comes pre-installed on Cloud Shell and supports tab-completion.

(Optional) You can list the active account name with this command:
gcloud auth list
Copied!
Click Authorize.

Your output should now look like this:

Output:

ACTIVE: *
ACCOUNT: student-01-xxxxxxxxxxxx@qwiklabs.net

To set the active account, run:
    $ gcloud config set account `ACCOUNT`
(Optional) You can list the project ID with this command:
gcloud config list project
Copied!
Output:

[core]
project = <project_ID>
Example output:

[core]
project = qwiklabs-gcp-44776a13dea667a6
Note: For full documentation of gcloud, in Google Cloud, refer to the gcloud CLI overview guide.
Overview
In this lab, you will learn how to create a Cloud Firestore database using Terraform. You will define your infrastructure as code, store the Terraform state in a Cloud Storage bucket, and deploy the Firestore database using Terraform commands. This lab assumes you have basic knowledge of Google Cloud and Terraform.

Task 1. Set Up Google Cloud Project and Terraform
Before you begin, configure your Google Cloud project and initialize Terraform.

Set your Project ID: PROJECT_ID

gcloud config set project "PROJECT_ID"
Copied!
Note:
This command sets your active project. This ensures that subsequent commands are executed within the correct project context.
Enable the Cloud Firestore API.

gcloud services enable firestore.googleapis.com
Copied!
Note:
This command enables the Cloud Firestore API, allowing you to create and manage Cloud Firestore databases in your project.
Enable the Cloud Build API.

gcloud services enable cloudbuild.googleapis.com
Copied!
Note:
This command enables the Cloud Build API. Cloud Build automates the deployment of your Terraform configurations.
Create a Cloud Storage bucket to store the Terraform state.

gcloud storage buckets create gs://"PROJECT_ID"-tf-state --location=us
Copied!
Note:
This command creates a Cloud Storage bucket to store your Terraform state file. Storing the state remotely allows for collaboration and versioning. The `--location=us` flag specifies the region for the bucket.
Task 2. Create Terraform Configuration
Define the Cloud Firestore database resource in a Terraform configuration file.

Create a file named main.tf with the following content.

terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "~> 4.0"
    }
  }
  backend "gcs" {
    bucket = ""PROJECT_ID"-tf-state"
    prefix = "terraform/state"
  }
}

provider "google" {
  project = ""PROJECT_ID""
  region  = ""REGION""
}

resource "google_firestore_database" "default" {
  name     = "default"
  project  = ""PROJECT_ID""
  location_id = "nam5"
  type     = "FIRESTORE_NATIVE"
}

output "firestore_database_name" {
  value       = google_firestore_database.default.name
  description = "The name of the Cloud Firestore database."
}
Copied!
Note:
This Terraform configuration file defines a Cloud Firestore database resource. The `backend "gcs"` block configures Terraform to store the state file in the Cloud Storage bucket you created earlier.
Create a variables.tf file with the following content:

variable "project_id" {
  type        = string
  description = "The ID of the Google Cloud project."
  default     = ""PROJECT_ID""
}

variable "bucket_name" {
  type        = string
  description = "Bucket name for terraform state"
  default     = ""PROJECT_ID"-tf-state"
}
Copied!
Note:
This defines variables for the Google Cloud Project ID and the bucket name. It's a best practice to keep variables separate for better configuration management and reusability.
Create a outputs.tf file with the following content:

output "project_id" {
  value       = var.project_id
  description = "The ID of the Google Cloud project."
}

output "bucket_name" {
  value       = var.bucket_name
  description = "The name of the bucket to store terraform state."
}
Copied!
Note:
This defines the outputs for Terraform, allowing you to easily retrieve the values of the project ID and bucket name after the configuration is applied.
Task 3. Apply the Terraform Configuration
Deploy the Cloud Firestore database using Terraform.

Initialize Terraform in your working directory.

terraform init
Copied!
Note:
This command initializes Terraform in your current directory, downloads the necessary provider plugins, and prepares the working directory for Terraform operations.
Run terraform plan to review the changes.

terraform plan
Copied!
Note:
This command shows the changes that Terraform will make to your infrastructure. Review the plan carefully to ensure that the changes are what you expect.
Run terraform apply to apply the configuration. Type yes when prompted to confirm.

terraform apply
Copied!
Note:
This command applies the Terraform configuration and creates the Cloud Firestore database. Terraform will prompt you to confirm the changes before proceeding.
Verify that the Cloud Firestore database has been created in the Google Cloud Console.

Note:
Go to the Cloud Firestore section in the Google Cloud Console to verify the database. Ensure that the database is configured as specified in your Terraform configuration.
Task 4. Clean Up Resources
Destroy the resources created by Terraform.

Run terraform destroy to destroy the resources. Type yes when prompted to confirm.

terraform destroy
Copied!
Note:
This command destroys the resources created by Terraform, including the Cloud Firestore database and any other resources created by the configuration.
