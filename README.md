# Auto CTFd

Automatically deploy your CTF challenges from GitHub to CTFd.

## Requirements

* [Hosted CTFd instance](https://docs.ctfd.io/hosted/management/creating-hosted-instances). [Contact support](https://ctfd.io/contact) for an education discount
  * [Self-hosted instances](https://docs.ctfd.io/docs/deployment/installation) can be used if you don't need containers (web or pwn challenges)
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
| `CTFD_DOMAIN` | [CTFd domain](https://docs.ctfd.io/hosted/management/setting-custom-domains), eg `pecan.ctfd.io` |
| `CTFD_REGISTRY` | [CTFd challenge registry](https://docs.ctfd.io/tutorials/challenges/deploying-challenges), eg `registry.ctfd.io/pecan` |
| `FLAG_PREFIX` (optional) | Flag prefix for linting, eg `ctf{` |

## Usage

* [Invite your team members to access the repo](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-access-to-your-personal-repositories/inviting-collaborators-to-a-personal-repository)
* [Create GitHub issues](https://docs.github.com/en/issues/tracking-your-work-with-issues/creating-an-issue) for each challenge. Send a link like this to your challenge authors: [your-repo-url]/issues/new/choose
* Consider using [GitHub Codespaces](https://github.com/features/codespaces) for challenge development
* Add [CTFd pages](https://docs.ctfd.io/docs/management/ctfcli/pages) to the `pages` folder, and they'll automatically be deployed

## Updating

Get the latest updates with the following commands. You may need to resolve merge conflicts.

```
git remote add template https://github.com/pl4nty/auto-CTFd
git fetch template main
git merge template/main --allow-unrelated-histories --squash
git push
```

## Containers

Some challenges, like pwn or web, may wish to run services in containers. These have certain requirements and limitations. Please see the [CTFd documentation](https://docs.ctfd.io/tutorials/challenges/deploying-challenges) for more details.
