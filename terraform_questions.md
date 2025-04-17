## TERRAFORM QUESTIONS

### How to limit the access control in the tfstatefile ?

- Add a DynamoDb table with the `lock_id`; it ensures that only 1 person could apply lock at a time.
- Interpretation would be like as follows.
  ![alt text](lock_id.png)

### How to determine if changes are done in an AWS Resources? How to find who did this ?

- If Done via AWS SERVICES we can use the =. CloudTrail

### What are different Backends for storing the TF State ?

- The backend ios most popularly stored in the `AWS S3`, then there are `Hashicorp Consul` , `Azure Blob Storage` and `Google Cloud Storage`
- When the files are kept in the backned then the Local State File can be deleted and it will be easier to maintain the versions.

### What are Benefits of using the Remote Backend?

- Improved Collabration among the Team Members.
- State Locking can be provided to prevent the Concurrent Operations.
- Enhanced Security by storeing the state data in the centrailized locations.

### How to use the TF State file in team env to prevent conflict?

- Terraform provides the `state-locking` of remote backends such that only a user can initiate terraform operation at a time.
- `S3` - It uses DynamoDB for state locking using `id` as `lockid`

### How to manage the External Sensitive Data; such as API KEYS in Terraform State?

- WE can use the external secret management tool like `Hashicorp Vault` for storing the API KEYS and Secrets.
- OR can use the `ENV_VARIABLES` [but it will go as soom as sessions terminates].

### What startegies can you employ for mamaging Terraform State across multiple environments like (Dev, Staging, Prod etc)?

- We can make use of the the `terraform workspace` to manage multiple environments with same statefile.

```
terraform workspace list
terraform workspaceselect production
terraform workspace show
terraform workspace delete test
```

### What is Drift Detection?

- It is the process of identifying the desired state declared in terraform filesand actual state of deployed infrastructure.
- Drift in infra can be due to => someone manulaly chnaging he resources in the cloud providers console.
- It comapres {tf current state of Resources} Vs {state recorded in tf state files}
- Checkout the drift in Infrastructure using ==> {terraform plan}

### What are tainted Resources?

- They are `resources which are destroyed & recreated with each "terraform apply"`

```
terraform taint aws_resource.my_example
```

### Does TF suppport multi provider Deployment ?

- YES; as TF is Cloud Agnoistic.

### What are some of the Built In Provisoners?

- File Provisioners, Dirname, abspath

### How can you upgrade plugins on TF?

- terraform init -upgrade

### How can you define dependencies in the TF?

- USing the `depends_on`

### What is terraform show?

- It is used to provide the human readable O/P from a state or Plan File.

### YOU have existing indfrastructure in the AWS not in the TF Code ? How to bring that Infra in the Terraform Control?

- If we just want the state you can either just import it using the `terraform import`
- WE want the whole code to be made; either do it by yourself or make use of the OpenSource tool using `Terraformer`

### If N people are using the TF; How to prevent team to bring up the resources in AWS/GCP which are too expensive?

- Can be a way using the `Open policy Agent`

### How to tackle secrets in TF?

- We can make the variable type as `sensitive`
- Integrate it withe External Secret Provider like Vault.

### What is the use of the Data Resources?

- DataResources are usd to refer to resources taht already exist in the AWS eg.: AWS AMI

### What is Terraform Workspace?

- They are sued to perform the isolation; here the seprate statefiles for each environment like `DEV, QA, Staging, Production`.

```
terraform workspace select <workspace_name>
```

### Terraform variable precedence order [1-4; 4 being Highest Order] ?

- 1. Environment variables
- 2. terraform.tfvars
- 3. \*.auto.tfvars
- 4. -var or --var file

- **Variable Types**: String, Number, Boolean, Array, List, Map, Set, Object, tuples

### How can we modify aonly certain resources in the Terraform?

- Use `target` flag in tf command; it will mark that resource and recreate.
- OR WE can use the `terraform taint` but should be careful


### What happens if your state file is accidentally deleted?
Terraform loses track of managed infrastructure. On next apply, it attempts to recreate everything, causing duplicates or failures. Recovery requires manual imports or restoring backups. Always enable versioning on S3 state storage.

### How do you handle large-scale refactoring without downtime?
Use "terraform state mv" to rename resources without destroying them. Control changes with targeted applies. Split refactoring into multiple non-destructive PRs and verify plans carefully to prevent resource destruction.

### What happens if a resource fails halfway through a terraform apply?
Answer: Terraform creates a partial deployment with successful resources running but failed ones marked as tainted. Use targeted applies and "-refresh-only" to recover systematically.

### How do you manage secrets in Terraform?
Use external secret stores (Vault, AWS Secrets Manager), ensure state encryption, mark outputs as sensitive, and integrate securely with CI/CD. For highly sensitive values, consider managing them outside Terraform completely.

### What happens if terraform plan shows no changes but infrastructure was modified outside Terraform?
Terraform remains unaware until "terraform refresh" is run. Implement regular drift detection in your CI/CD process to catch unauthorized changes.

### What happens if you delete a resource definition from your configuration?
Terraform destroys the corresponding infrastructure. Either use "terraform state rm" first or implement "lifecycle { prevent_destroy = true }" for critical resources.

### What happens if Terraform provider APIs change between versions?
Compatibility issues may arise. Always read release notes, use version constraints, test upgrades in lower environments, and consider targeted updates for gradual migration.

### How do you implement zero-downtime infrastructure updates?
Use "create_before_destroy" lifecycle blocks, blue-green deployments, health checks, and state manipulation for complex scenarios. For databases, use replicas or managed services with failover capabilities.

### What happens if you have circular dependencies in your Terraform modules?
Terraform fails with "dependency cycle" errors. Refactor module structure using data sources, outputs, or restructuring resources to establish clear dependency hierarchy.

### What happens if you rename a resource in your Terraform code?
Terraform sees this as destroying and recreating the resource. Use "terraform state mv" to update state while preserving infrastructure, avoiding rebuilds and downtime.