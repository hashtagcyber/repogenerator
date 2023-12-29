# Create Repo and Namespace GitHub Action

## Summary
Over the years I've noticed a few problem statements that generally result in "high friction" processes:
- I'm a SWE, how do I get a new repository created?
- I have a repo, how do I get a namespace to deploy to?
- I have a repo, and a namespace, but how do I get the secrets setup in Github for deployment?

This action aims to solve those problems in a simple way. Create this action in a repository owned by the Platform team, but enable all members of the organization to run it. A successful run will result in the creation of the resources listed below.
### Kubernetes Resources
- **Development Namespace**: `<reponame>-infra-dev`
    - A Kubernetes namespace for development is created, named based on the provided repository name with a `-dev` suffix.
- **Production Namespace**: `<reponame>-infra-prod`
    - Similarly, a production namespace is created, named with a `-prod` suffix.
- **Service Accounts**: `<reponame>-admin`
    - Service accounts are created in both the development and production namespaces.
### Variables and Secrets Created in the New Repository
The action creates the following environment variables and secrets in the new repository:

| Name                      | Type     | Description                                         |
|---------------------------|----------|-----------------------------------------------------|
| `KUBECONFIG_DEV`          | Secret   | Kubeconfig for the development namespace.           |
| `KUBECONFIG_PROD`         | Secret   | Kubeconfig for the production namespace.            |
| `K8S_NAMESPACE_DEV`       | Variable | Name of the development Kubernetes namespace.       |
| `K8S_NAMESPACE_PROD`      | Variable | Name of the production Kubernetes namespace.        |
| `K8S_SERVICEACCOUNT_DEV`  | Variable | Service account name in the development namespace.  |
| `K8S_SERVICEACCOUNT_PROD` | Variable | Service account name in the production namespace.   |

## Requirements
- An Ubuntu based self-hosted runner with network access to your kubernetes cluster. 
    - Update the `runs-on` section of `.github/workflows/create-repo.yaml` to the appropriate runner tag.
- GitHub CLI installed on the runner. \(Local Action "setup-ghcli" will install it if needed)
- A Github App with the following permissions:
    - Repository :
        - Actions: Read and write
        - Administration: Read and write
        - Metadata: Read-only
        - Secrets: Read and write
        - Variables: Read and write

- GitHub Secrets configured \(see below)
## GitHub Secrets
The following GitHub secrets must be set for the action to function correctly:
- `GH_APP_ID`: The ID of your GitHub App.
- `GH_APP_PRIVATE_KEY`: The private key of your GitHub App.
- `KUBE_CONFIG_DATA`: Base64 encoded Kubernetes configuration data for accessing your cluster.



## Notes
- **Self-Hosted Runner Requirements**: This action is built for self-hosted runners that may not have the necessary tools pre-installed. It includes steps to install the GitHub CLI as needed.
- **Future Improvements with Caching**: To enhance efficiency, future iterations of this action could benefit from caching mechanisms for storing the GitHub CLI and other dependencies, reducing installation times in subsequent runs.

## Usage
To use this action, dispatch it with the required input of `repo-name`. This will trigger the creation of the new repository and the corresponding Kubernetes namespaces and service accounts.


