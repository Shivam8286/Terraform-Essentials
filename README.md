# Terraform Essentials: Provisioning Cloud Firestore on Google Cloud

Welcome to **Terraform Essentials**! This guide walks you through provisioning a Google Cloud Firestore database using Terraform, with state management in Cloud Storage. By following this tutorial, you’ll learn to define infrastructure as code, automate deployments, and manage resources efficiently.

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Activate Google Cloud Shell](#activate-google-cloud-shell)
3. [Set Up Project and Enable APIs](#set-up-project-and-enable-apis)
4. [Create Terraform Configuration](#create-terraform-configuration)
5. [Apply Terraform Configuration](#apply-terraform-configuration)
6. [Verify Resources](#verify-resources)
7. [Clean Up](#clean-up)
8. [References](#references)

---

## Prerequisites

- Google Cloud Platform account
- [Google Cloud SDK](https://cloud.google.com/sdk) (comes pre-installed in Cloud Shell)
- Basic knowledge of Terraform

---

## Activate Google Cloud Shell

Cloud Shell is an online terminal with essential development tools and a persistent 5GB home directory. It provides immediate access to Google Cloud resources.

1. Click the **Activate Cloud Shell** icon at the top right of the Google Cloud Console.
2. Once connected, you are automatically authenticated, and your `PROJECT_ID` is set.
3. To list the active account:
    ```sh
    gcloud auth list
    ```
4. To confirm the active project:
    ```sh
    gcloud config list project
    ```
    Example output:
    ```
    [core]
    project = projectId here
    ```

---

## Set Up Project and Enable APIs

1. **Set your active project:**
    ```sh
    gcloud config set project "<PROJECT_ID>"
    ```

2. **Enable required APIs:**
    ```sh
    gcloud services enable firestore.googleapis.com
    gcloud services enable cloudbuild.googleapis.com
    ```

3. **Create a Cloud Storage bucket for Terraform state:**
    ```sh
    gcloud storage buckets create gs://<PROJECT_ID>-tf-state --location=us
    ```

*Replace `<PROJECT_ID>` with your actual project ID.*

---

## Create Terraform Configuration

### 1. main.tf

```hcl
terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "~> 4.0"
    }
  }
  backend "gcs" {
    bucket = "${var.bucket_name}"
    prefix = "terraform/state"
  }
}

provider "google" {
  project = var.project_id
  region  = var.region
}

resource "google_firestore_database" "default" {
  name        = "default"
  project     = var.project_id
  location_id = var.region
  type        = "FIRESTORE_NATIVE"
}

output "firestore_database_name" {
  value       = google_firestore_database.default.name
  description = "The name of the Cloud Firestore database."
}
```

### 2. variables.tf

```hcl
variable "project_id" {
  type        = string
  description = "The ID of the Google Cloud project."
  default     = "<PROJECT_ID>"
}

variable "bucket_name" {
  type        = string
  description = "Bucket name for Terraform state."
  default     = "<PROJECT_ID>-tf-state"
}

variable "region" {
  type        = string
  description = "Region for Firestore database."
  default     = "nam5"
}
```

### 3. outputs.tf

```hcl
output "project_id" {
  value       = var.project_id
  description = "The ID of the Google Cloud project."
}

output "bucket_name" {
  value       = var.bucket_name
  description = "The name of the bucket to store Terraform state."
}
```
<img width="908" height="442" alt="Image" src="https://github.com/user-attachments/assets/153f8b40-efc3-494a-b37f-456742dce648" />


---

## Apply Terraform Configuration

1. **Initialize Terraform:**
    ```sh
    terraform init
    ```
    This downloads the necessary plugins and configures the backend.
   <img width="863" height="509" alt="Image" src="https://github.com/user-attachments/assets/f5cf027b-94a2-4cb8-bf09-19b792694cdc" />


3. **Review the planned changes:**
    ```sh
    terraform plan
    ```

4. **Apply the configuration:**
    ```sh
    terraform apply
    ```
    Type `yes` to confirm.
   <img width="888" height="576" alt="Image" src="https://github.com/user-attachments/assets/dc35f4ba-2092-4ae3-b1a8-70f94670300d" />


---

## Verify Resources

- Visit the [Cloud Firestore section](https://console.cloud.google.com/firestore) in Google Cloud Console.
- Ensure the database exists and the configuration matches your Terraform code.

---

## Clean Up

To destroy the resources created by Terraform:

```sh
terraform destroy
```
Type `yes` to confirm.

---

## References

- [Terraform GCP Provider Documentation](https://registry.terraform.io/providers/hashicorp/google/latest/docs)
- [Google Cloud Firestore Documentation](https://cloud.google.com/firestore/docs)
- [gcloud CLI Overview](https://cloud.google.com/sdk/gcloud)

---

**Happy automating with Terraform!**
<img width="893" height="369" alt="Image" src="https://github.com/user-attachments/assets/ac342cca-ccfd-4158-a4bc-6c0b129c9c33" />
<img width="781" height="252" alt="Image" src="https://github.com/user-attachments/assets/eb5c05a1-27f6-45c7-aff4-3a99ca709c74" />
