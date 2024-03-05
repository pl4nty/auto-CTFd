# Auto CTFd

Automatically deploy your CTF challenges from GitHub to CTFd. Also supports containerised challenges on managed CTFd, Kubernetes, and Microsoft Azure.

## Requirements

* A [managed](https://docs.ctfd.io/hosted/management/creating-hosted-instances) or [self-hosted](https://docs.ctfd.io/docs/deployment/installation/) CTFd instance
  * An education discount is available for managed instances. [Contact support](https://ctfd.io/contact) for more details.
* [GitHub account](https://github.com/join) for each challenge author. Consider [GitHub Education](https://education.github.com/benefits) if you're eligible

## Getting Started

1. [Click here](https://github.com/new?template_name=auto-ctfd&template_owner=pl4nty) to create a repository for your CTF. Select "Private" to prevent public access
2. [Allow GitHub Actions to create pull requests](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/enabling-features-for-your-repository/managing-github-actions-settings-for-a-repository#preventing-github-actions-from-creating-or-approving-pull-requests)
3. [Create the following secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-a-repository):

| Name | Value |
| ---- | ----- |
| `CTFD_TOKEN` | [CTFd admin access token](https://docs.ctfd.io/docs/api/getting-started#generating-an-admin-access-token) |
| `CTFD_SITE_PASSWORD` (optional) | [CTFd site password](https://docs.ctfd.io/hosted/security/setting-site-password), if enabled |

4. [Create the following variables](https://docs.github.com/en/actions/learn-github-actions/variables#creating-configuration-variables-for-a-repository):

| Name | Value |
| ---- | ----- |
| `CTFD_DOMAIN` | [CTFd domain](https://docs.ctfd.io/hosted/management/setting-custom-domains), eg `example.ctfd.io` |
| `FLAG_PREFIX` (optional) | Flag prefix for linting, eg `ctf{` |

5. See [containers](#containers) for more options

## Usage

* [Invite your team members to access the repo](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-access-to-your-personal-repositories/inviting-collaborators-to-a-personal-repository)
* [Create GitHub issues](https://docs.github.com/en/issues/tracking-your-work-with-issues/creating-an-issue) for each challenge. Send a link like this to your challenge authors: [your-repo-url]/issues/new/choose
* Consider using [GitHub Codespaces](https://github.com/features/codespaces) for challenge development
* Add [CTFd pages](https://docs.ctfd.io/docs/management/ctfcli/pages) to the `pages` folder, and they'll automatically be deployed

## Updating

Get the latest updates with the following commands. You may need to resolve merge conflicts.

```
git pull https://github.com/pl4nty/auto-CTFd --allow-unrelated-histories --rebase=false --squash -X theirs
git commit -m "chore: update repo template"
git push
```

## Containers

Some challenges, like pwn or web, may need to run services in containers. These can be deployed to several platforms. To disable a platform, [disable its GitHub workflow](https://docs.github.com/en/actions/using-workflows/disabling-and-enabling-a-workflow).

### Managed CTFd

Note that managed CTFd has certain Dockerfile requirements and limitations. Please see the [CTFd documentation](https://docs.ctfd.io/tutorials/challenges/deploying-challenges) for more details.

Create the following variables:

| Name | Value |
| ---- | ----- |
| `REGISTRY` | [Managed CTFd registry](https://docs.ctfd.io/tutorials/challenges/deploying-challenges), eg `registry.ctfd.io/example` |

### Kubernetes

1. Add a Compose file like `docker-compose.yml` to each of your challenge(s)
2. Ensure TCP challenges have unique ports
3. Create the following variables:

| Name | Value |
| ---- | ----- |
| `REGISTRY` | A container registry accessible by the Kubernetes cluster |
| `KUBE_HOST` | Hostname for challenges. HTTP challenges will be available via ingress on `example.KUBE_HOST`, and TCP challenges via load balancer service on `KUBE_HOST:port` |

4. Create the following secrets:

| Name | Value |
| ---- | ----- |
| `REGISTRY_USERNAME` | Container registry username |
| `REGISTRY_PASSWORD` | Container registry password |
| `KUBE_CONFIG` | A static [kubeconfig file](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/). To use a dynamic file instead, modify the workflow to retrieve its own kubeconfig eg using [azure/aks-set-context](https://github.com/Azure/aks-set-context) |

5. Deploy the challenges
6. Create an ingress controller in the cluster
7. Create a public DNS record for `*.KUBE_HOST` to the controller's IP address
8. Create a public DNS record for `KUBE_HOST` to the load balancer IP address

### Microsoft Azure

1. [Create an Azure app registration and federated credentials](https://learn.microsoft.com/en-us/azure/developer/github/connect-from-azure?tabs=azure-portal%2Clinux#use-the-azure-login-action-with-openid-connect)
2. [Create an Azure Container Apps environment](https://learn.microsoft.com/en-us/azure/container-apps/quickstart-portal). Note that a [custom vnet is required for TCP ports](https://learn.microsoft.com/en-us/azure/container-apps/ingress-overview#tcp).
3. Delete the quickstart Container App, and [assign the `Contributor` role](https://learn.microsoft.com/en-us/azure/role-based-access-control/role-assignments-steps) on its resource group to the app registration
4. [Create a user-assigned managed identity](https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/how-manage-user-assigned-managed-identities#create-a-user-assigned-managed-identity) and
5. [Create an Azure Container Registry](https://learn.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal) and assign the `AcrPull` role on it to the managed identity
6. (Optional) [Add a custom DNS suffix](https://learn.microsoft.com/en-us/azure/container-apps/environment-custom-dns-suffix) to the Container Apps environment
6. Create the following variables:

| Name | Value |
| ---- | ----- |
| `REGISTRY` | A container registry accessible by the Container Apps environment |
| `AZURE_TENANT_ID` | App registration tenant ID |
| `AZURE_CLIENT_ID` | App registration client ID |
| `AZURE_CONTAINER_ENV` | Container Apps enviroment [resource ID](https://learn.microsoft.com/en-us/azure/storage/common/storage-account-get-info?tabs=portal#get-the-resource-id-for-a-storage-account) |
| `AZURE_CONTAINER_IDENTITY` | Managed identity resource ID |
| `AZURE_CONTAINER_SUFFIX` | Container Apps environment DNS suffix, eg chals.example.com |
