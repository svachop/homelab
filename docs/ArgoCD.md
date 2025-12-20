# ArgoCD Installation on Kubernetes

**Argo CD is a Continuous Delivery (CD) tool that has gained popularity in DevOps for performing application delivery onto Kubernetes. It relies on a deployment method known as GitOps. GitOps is a mechanism that can pull the latest code and application configuration from the last known Git commit version and deploy it directly into Kubernetes resources.
In this guide, we’ll install ArgoCD on a Kubernetes cluster and deploy first application using ArgoCD.**

![A screenshot of the dashboard interface](images/argocd.jpeg)

## Table of Contents

* [Prerequisites](#prerequisites)
* [What is ArgoCD?](#what-is-argocd)
* [How Does ArgoCD Work?](#how-does-argocd-work)
* [Installing ArgoCD on Kubernetes](installing-argocd-on-kubernetes)
* [Accessing the ArgoCD Dashboard](accessing-the-argocd-dashboard)
* [Deploying First Application with ArgoCD](deploying-first-application-with-argocd)
* [Syncing and Monitoring Your Application](syncing-and-monitoring)

* [Test](test)
    * [Prerequisites](#prerequisites)
    * [Installation](#installation)
* [Usage](#usage)
    * [Basic Example](#basic-example)
    * [Advanced Usage](#advanced-usage)
* [Project Structure](#project-structure)
* [Contributing](#contributing)
* [License](#license)

---

## Prerequisites

* Feature 1: Quick bullet point description.
* Feature 2: Another benefit or capability.
* Feature 3: Something unique or important.

## What is ArgoCD?

ArgoCD is a GitOps continuous delivery tool designed for Kubernetes. It treats a Git repository as the single source of truth for your desired application state. By continuously monitoring running applications, ArgoCD compares the current state with the desired state stored in Git. When discrepancies occur, it not only flags these differences but also provides visual insights, allowing developers to synchronize the live state with the desired configuration either manually or automatically.

ArgoCD simplifies Kubernetes resource management by ensuring that your application's live state always reflects the configuration defined in your Git repository.

## How Does ArgoCD Work?

ArgoCD adheres to the GitOps model by using Git repositories as the authority for both your application’s desired state and its target deployment environment. This transparent and consistent approach makes deployment processes reliable and easily auditable.
ArgoCD supports a variety of Kubernetes manifests. Whether you work with customized applications, Helm charts, JSON files, or YAML configurations, ArgoCD automates the synchronization process to ensure that deployed application states across all target environments are always aligned with those defined in Git.

![A screenshot of the dashboard interface](images/what-why-how-argocd.jpg)

### Prerequisites

List all the software, tools, or dependencies required to run the project.

```bash
brew install git
brew install helm
```

## Installing ArgoCD on Kubernetes

We'll use Helm to install Argo CD with the community-maintained chart from argoproj/argo-helm. The Argo project doesn't provide an official Helm chart.

Specifically, we are going to create a Helm "umbrella chart". This is basically a custom chart that wraps another chart. It pulls the original chart in as a dependency, and overrides the default values. In our case, we create an argo-cd Helm chart that wraps the community-maintained argo-cd Helm chart.

Using this approach, we have more flexibility in the future, by possibly including additional Kubernetes resources. The most common use case for this is to add Secrets (which could be encrypted using 1Password) to our application. For example, if we use webhooks with Argo CD, we have the possibility to securely store the webhook URL in a Secret.

To create the umbrella chart, we make a directory in our Git repository:

```bash
$ pwd
/Users/svachop/git/homelab
$ mkdir -p charts/argo-cd
```

Then place a Chart.yaml file in it:

https://github.com/svachop/homelab/charts/argo-cd/Chart.yaml

```bash
apiVersion: v2
appVersion: v3.2.2
description: A Helm "umbrella chart" for Argo CD, a declarative, GitOps continuous delivery tool for Kubernetes.
name: argo-cd
version: 1.0.0
dependencies:
  - name: argo-cd
    version: 9.1.9
    repository: https://argoproj.github.io/argo-helm
```

The version of our custom chart doesn't matter and can stay the same. The version of the dependency matters and if you want to upgrade the chart, would be the place to do it. The important thing is that we pull in the community-maintained argo-cd chart as a dependency. Next, create a values.yaml file for our chart:

https://github.com/svachop/homelab/charts/argo-cd/values.yaml

To override the chart values of a dependency, we have to place them under the dependency name. Since our dependency in the Chart.yaml is called **argo-cd**, we have to place our values under the **argo-cd**: key. If the dependency name would be **abcd**, we'd place the values under the **abcd**: key.

All available options for the Argo CD Helm chart can be found in the [values.yaml](https://github.com/argoproj/argo-helm/blob/main/charts/argo-cd/values.yaml) file.

Before we install our chart, we need to generate a Helm chart lock file for it. When installing a Helm chart, Argo CD checks the lock file for any dependencies and downloads them. Not having the lock file will result in an error.

```bash
$ cd /Users/svachop/git/homelab/charts
$ helm repo list
$ helm repo add argo-cd https://argoproj.github.io/argo-helm
$ helm dep update argo-cd/
$ helm repo update
```

This will create the Chart.lock and charts/argo-cd-<version>.tgz files. The .tgz file is only required for the initial installation from our local machine. To avoid accidentally committing it, we can add it to the gitignore file:

```bash
$ cd /Users/svachop/git/homelab
echo "homelab/charts/argo-cd/**/charts/" >> .gitignore
echo ".DS_Store" >> ~/.gitignore_global
git config --global core.excludesfile ~/.gitignore_global
```

Our custom chart is ready and can be pushed to our public Git repository:

```bash
$ cd /Users/svachop/git/homelab
$ git add .
$ git commit -m 'add argo-cd chart + documentation'
$ git push
```

The next step is to install our chart.


