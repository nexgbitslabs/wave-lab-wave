# Wave Lab Workflow Catalog

## Purpose

This catalog defines the reusable workflow capabilities available in the
Wave Lab platform.

Migration agents must use this catalog when converting legacy Jenkins
pipelines to Wave-compatible GitHub Actions workflows.

The migration agent must not invent workflow capabilities that are not
defined in this catalog.

---

# Application Workflows

## container-build

### Purpose

Build a container image from an application repository containing a
Dockerfile.

### Legacy Jenkins Pattern

Typical Jenkins behavior:

- checkout source
- execute `docker build`
- tag image with build identifier

### Inputs

- application_name
- dockerfile
- build_context
- image_tag

### Output

A locally built container image ready for publication.

---

## container-publish

### Purpose

Publish a container image to Azure Container Registry.

### Legacy Jenkins Pattern

Typical Jenkins behavior:

- authenticate to Azure
- authenticate Docker to ACR
- tag local image
- push image to ACR

### Inputs

- application_name
- image_tag
- registry_name
- registry_login_server

### Authentication

Azure authentication must be supplied by the execution platform.

Credentials must not be stored in repository parameter files.

### Output

Container image published to ACR.

---

## gitops-promotion

### Purpose

Update the GitOps repository with the newly published application image.

### Legacy Jenkins Pattern

Typical Jenkins behavior:

- clone GitOps repository
- locate Kubernetes deployment manifest
- replace container image tag
- commit change
- push change

### Inputs

- gitops_repository
- gitops_branch
- deployment_file
- application_name
- registry_login_server
- image_tag
- environment

### Output

A committed GitOps desired-state change.

### Deployment Boundary

This workflow does NOT deploy directly to Kubernetes.

Flux v2 observes the GitOps repository and performs reconciliation against
the target AKS cluster.

Migration agents must preserve this separation.

---

# Infrastructure Workflows

## terraform-initialize

### Purpose

Initialize and validate an existing Terraform environment.

### Operations

- render environment configuration when required
- terraform init
- terraform validate

This workflow must NOT automatically execute plan or apply.

---

## terraform-plan

### Purpose

Generate a Terraform execution plan for a selected environment.

### Operations

- terraform plan
- save the generated plan

This workflow must NOT automatically execute apply.

---

## terraform-apply

### Purpose

Apply an explicitly generated Terraform execution plan.

### Operations

- consume the existing saved Terraform plan
- terraform apply

The workflow must not automatically create a new plan.

---

## terraform-destroy

### Purpose

Destroy Terraform-managed resources for an explicitly selected environment.

### Operations

- terraform destroy

Destruction must always be explicitly requested.

---

# Supported Environments

The Wave Lab currently recognizes:

- dev
- qa
- uat
- prod

Environment-specific configuration must remain separate from secrets.

---

# GitOps

Wave Lab uses Flux v2 for Kubernetes continuous delivery.

The delivery model is:

Application Repository
→ CI Workflow
→ Azure Container Registry
→ GitOps Repository
→ Flux v2
→ AKS

CI workflows must not use `kubectl apply` to deploy applications directly
to AKS.

---

# Migration Rules

Migration agents must:

1. Inspect the existing Jenkins pipeline before selecting workflows.
2. Preserve the behavioral intent of the Jenkins pipeline.
3. Select capabilities defined in this catalog.
4. Extract non-secret Jenkins properties into repository parameters.
5. Keep credentials and secrets outside repository parameter files.
6. Preserve explicit infrastructure lifecycle operations.
7. Preserve the GitOps deployment boundary.
8. Report Jenkins behavior for which no Wave Lab workflow exists.

Migration agents must not silently invent unsupported Wave capabilities.