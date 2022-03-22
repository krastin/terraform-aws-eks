# terraform-aws-eks
A repository to bring up an AWS flavour of K8s

# Prerequisites

- an AWS account that **WILL BE BILLED**
- a configured AWS CLI

# Procedure

## EKS K8s
- `terraform init`
- `terraform apply`
- `aws eks --region $(terraform output -raw region) update-kubeconfig --name $(terraform output -raw cluster_name) --kubeconfig $(terraform output -raw cluster_name)_config`
- `export KUBECONFIG=$(pwd)/$(terraform output -raw cluster_name)_config`

## Dashboard and Metrics

- `wget -O v0.3.6.tar.gz https://codeload.github.com/kubernetes-sigs/metrics-server/tar.gz/v0.3.6 && tar -xzf v0.3.6.tar.gz`
- `kubectl apply -f metrics-server-0.3.6/deploy/1.8+/`
- `kubectl get deployment metrics-server -n kube-system`
- `kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml`
- `kubectl proxy --port=8001 &` &  [open it](http://127.0.0.1:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/)
- Create a the _ClusterRoleBinding_ resource `kubectl apply -f https://raw.githubusercontent.com/hashicorp/learn-terraform-provision-eks-cluster/main/kubernetes-dashboard-admin.rbac.yaml`
- Fetch admin token `kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user-token | awk '{print $1}')`
- Use the token in the dashboard to login

# Notes

# Thank you

This project has reused content from the following repository:

- [Provision an EKS Cluster (AWS)](https://learn.hashicorp.com/tutorials/terraform/eks)