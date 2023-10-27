# GitOps Fundamentals Exercises
This repository contains exercises for the GitOps fundamentals training.

## What is GitOps?
---

- Core idea: having a Git repository that contains declarative descriptions of the infrastructure currently desired.
- Automated process to match the desired state in the repository.

The promises:

- Deploy faster more often;
- Error recovery;
- Self-documenting deployments;
- Easier credentials management.

### Overview

![GitOps overview](./figures/Gitops.drawio.png)

### Flux

![Flux overview](./figures/flux-overview.png)

## Prerequisites
---

### Install software

- kubectl https://kubernetes.io/docs/tasks/tools/
- flux cli https://fluxcd.io/docs/installation/#install-the-flux-cli
- git https://git-scm.com/book/en/v2/Getting-Started-Installing-Git

### Other prerequisites

- A personal namespace provided by your trainer
- An instruction to download your kubeconfig file

### Configure kubeconfig
By default, kubectl looks for a file named config in the $HOME/.kube directory. You can specify other kubeconfig files by setting the KUBECONFIG environment variable or by setting the --kubeconfig flag. Make your life easier with the kubectl cheatsheet (https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

1. Copy the content of your kubeconfig file and write to ~/.kube/config.
2. Test your connection with the following commands:

```
kubectl version
```

```
kubectl get pods -n <NAMESPACE>
```
3. Permanently save the namespace for all subsequent kubectl commands with the following command:
```
kubectl config set-context --current --namespace=<NAMESPACE>
```
4. Flux CLI uses the namespace flux-system by default. Set the evironment variable FLUX_SYSTEM_NAMESPACE=\<NAMESPACE> so it will be added to each flux command.

> When you performed steps 3 & 4 you can ignore -n \<NAMESPACE> during the exercises.

### Fork or copy this repository and create a Personal Access Token.
We are going to connect a Git Repository to Flux and use this repo to create, update and remove an application via git changes. It is possible to fork this repository under your personal user if you are using Github. It is also possible to use another Git repository like Gitlab or Bitbucket and copy the files to this repository. As long as you have your own repository with full access you should be fine. Follow the instructions below to create a Personal Access Token for your Git repository. Scope the permission level for this token to read-write on your forked repository. Read is needed for the controller, the write is used for you locally to be able to push to Github/Gitlab.
For production a read is sufficient most of the time (except when using the imageAutomationController).

- Create a Personal Access Token for Github https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token
- Create a Personal Access Token for Gitlab https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html

### Clone your brand new fork

Use you HTTPS url from the green clone button:
```
git clone https://<your repo>
```

## Exercises
---

### Source controller

* [Source controller](exercises/source-controller/)

### Kustomize controller

* [Kustomize controller](exercises/kustomize-controller/)

### Helm controller

* [Helm controller](exercises/helm-controller/)