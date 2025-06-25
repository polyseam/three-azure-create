# three-azure-create

This project was created with [CNDI](https://github.com/polyseam/cndi), and this
README is to help show you the ropes.

## files and directories

### cndi/cluster_manifests

All files in the [cndi/cluster_manifests](/cndi/cluster_manifests) folder are
Kubernetes manifests. These are the files that will be applied to your
Kubernetes cluster when it is deployed and ready using ArgoCD.

### cndi/terraform

All files in the [cndi/terraform](/cndi/terraform) folder are
[Terraform](https://terraform.io) Resource files. These are the files that will
be used to provision your cloud infrastructure, as well as configure the virtual
machines to join your cluster as they come online.

### .github

The files within the [.github](/.github) folder are the workflows that run
within [GitHub Actions](https://docs.github.com/en/actions) to call the
`cndi run` command when you push to the `"main"` branch.

### .env

The [.env](/.env) file is where you can set environment variables that will be
used by the `cndi` commands. These variables are used to configure the Terraform
resources and Kubernetes manifests. This file can contain secrets, because it
will not be committed to your repository. It comes pre-populated with a few
generated values, but there are a couple you must set yourself too. This file is
present in the [.gitignore](/.gitignore) file, so it will not be committed to
your repository.

### .gitignore

The [.gitignore](/.gitignore) file is where you can set files and directories
that you do not want to be committed to your repository. This file comes
pre-populated with a list of files that we know contain secret information.

## usage

Now that you've run `cndi init` and have a project, the next step is to set your
environment variables in the [.env](/.env) file. Once you've done that you can
use the GitHub CLI to set these variables as
[GitHub Actions Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets?tool=cli).

### secret setup

```bash
# this gh cli command sets .env variables as GitHub Actions Secrets

gh secret set -f .env
```

### push to GitHub, trigger `cndi run`

The next step is to just push your repo up to GitHub!

```bash
git add . 
git commit -m "initial commit" 
git push
```

Now that you have pushed to the `"main"` branch, the
[/.github/workflows/cndi-run.yaml](/.github/workflows/cndi-run.yaml) workflow
will run, and call the `cndi run` command. This will deploy your cluster to the
cloud, and then apply the Kubernetes manifests to it.

### updating your cluster

Now that you have a cluster, you can update it by:

1. modifying your [cndi_config.yaml](/cndi-config.yaml) file
2. running `cndi ow`
3. pushing all files to the `"main"` branch again

If you've modified your nodes, the infrastructure should be updated with
Terraform. If you've modified your Kubernetes manifests, the changes to the
manifests will be applied to the cluster.


## azure

This cluster will be deployed on
[Microsoft Azure](https://azure.microsoft.com/en-ca/). When your cluster is
initialized the next step is to go to your domain registrar and create an `A`
record for [ArgoCD](https://argo-cd.readthedocs.io/en/stable/).


## aks

This cluster will be deployed on
[Azure Kubernetes Service](https://azure.microsoft.com/en-ca/services/kubernetes-service/).
To see your cluster, checkout your
[AKS Dashboard](https://portal.azure.com/#view/HubsExtension/BrowseResource/resourceType/Microsoft.ContainerService%2FmanagedClusters).


# Apache Airflow Deployment Guide

This Template deploys a standalone production-ready [Apache Airflow](https://airflow.apache.org) application using Helm on a Kubernetes cluster.
Apache Airflow is an open-source platform designed to programmatically author, schedule, and monitor workflows, offering a robust foundation for managing complex data pipelines.

## Walkthroughs

For detailed guidance on deploying Airflow across different environments, we offer step-by-step walkthroughs for each supported deployment target.
These walkthroughs illustrate how to deploy a production-grade Airflow cluster using CNDI's `airflow` Template on various platforms:

- **AWS EKS:** [eks/airflow](https://github.com/polyseam/cndi/blob/main/docs/walkthroughs/eks/airflow.md)
- **GCP GKE:** [gke/airflow](https://github.com/polyseam/cndi/blob/main/docs/walkthroughs/gke/airflow.md)
- **Azure AKS:** [aks/airflow](https://github.com/polyseam/cndi/blob/main/docs/walkthroughs/aks/airflow.md)
- **Local Development:** [dev/airflow](https://github.com/polyseam/cndi/blob/main/docs/walkthroughs/dev/airflow.md)

## Configuration

Customizing your Airflow deployment is straightforward with the Helm chart's extensive configuration options, adjustable in the `cndi_config.yaml` file under `applications.airflow.values`. Key configuration areas include:

- **Executor:** Choose between Sequential, Local, Celery, and Kubernetes executors for task execution.
- **Persistence:** Configure persistent storage solutions for DAGs and logs, ensuring data durability.
- **Webserver:** Customize service type and ingress settings for external web access.
- **Scheduler:** Fine-tune scheduler behavior and resource allocations to optimize performance.

For comprehensive details on all configurable values, please refer to the [chart's values.yaml file](https://github.com/apache/airflow/blob/main/chart/values.yaml) or the [official chart documentation](https://airflow.apache.org/docs/helm-chart/stable/index.html).

## Verifying Airflow Accessibility

### Steps for Verification

**Access the Web Interface**:
  - Open a web browser and navigate to the domain you've configured for Airflow, e.g., `https://airflow.yourdomain.com`.
  - You should be greeted with Airflow's login screen.

**Default Credentials**:
  - By default, the login credentials for a fresh Airflow installation are:
    - **Username:** `admin`
    - **Password:** `admin`
  - It's strongly recommended to change these default credentials during the initial setup to ensure your Airflow instance is secure.

**Verify that Airflow is connected to the private DAG repository**:
  - If correct, the private DAGs should be visible on the Airflow UI. If not,you should go back and make sure that the private DAG repository is properly connected to Airflow with the correct credentials that located in the `.env` file
