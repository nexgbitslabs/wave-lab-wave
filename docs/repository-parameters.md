# Wave Lab Repository Parameters

## Purpose

`repository-parameters.yaml` contains non-secret configuration required by
Wave Lab workflows.

During a Jenkins-to-Wave migration, the migration agent must inspect the
legacy Jenkins properties files and translate applicable configuration into
this format.

Secrets must never be copied into this file.

---

# Source Configuration

The migration agent should inspect legacy configuration sources including:

- `Jenkins/properties/dev.properties`
- `Jenkins/properties/qa.properties`
- `Jenkins/properties/uat.properties`
- `Jenkins/properties/prod.properties`
- Jenkins environment declarations
- Jenkins pipeline variables
- Terraform variable templates

The existing values and environment separation should be preserved whenever
possible.

---

# Target File

The generated file must be:

`repository-parameters.yaml`

The default structure is:

```yaml
version: "1.0"

repository:
  name: ""

application:
  name: ""

container:
  registry_name: ""
  registry_login_server: ""

gitops:
  repository: ""
  branch: "main"

environments:

  dev: {}

  qa: {}

  uat: {}

  prod: {}