# Provision-an-EKS-Cluster-AWS-using-terraform
Provision an EKS Cluster (AWS) 
Using Terraform

The Amazon Elastic Kubernetes Service (EKS) is the AWS service for deploying, managing and scaling containerized applications with Kubernetes.

Why deploy with Terraform?
While you could use the built-in AWS provisioning processes (UI, CLI, CloudFormation) for EKS clusters, Terraform provides you with several benefits:
•	Unified Workflow - If you are already deploying infrastructure to AWS with Terraform, your EKS cluster can fit into that workflow. You can also deploy applications into your EKS cluster using Terraform.
•	Full Lifecycle Management - Terraform doesn't only create resources, it updates, and deletes tracked resources without requiring you to inspect the API to identify those resources.
•	Graph of Relationships - Terraform understands dependency relationships between resources. For example, if an AWS Kubernetes cluster needs a specific VPC and subnet configurations, Terraform won't attempt to create the cluster if the VPC and subnets failed to create with the proper configuration.
STEP 1: First, we must configure the AWS CLI in VS Code.
AWS Access Key ID [None]: YOUR_AWS_ACCESS_KEY_ID
AWS Secret Access Key [None]: YOUR_AWS_SECRET_ACCESS_KEY
Default region name [None]: YOUR_AWS_REGION

STEP 2: In your terminal, clone the following repository. 

git clone https://github.com/hashicorp/learn-terraform-provision-eks-cluster

STEP 3: You can explore this repository by changing directories or navigating in your UI.
cd learn-terraform-provision-eks-cluster

STEP 4: In here, you will find six files used to provision a VPC, security groups, and an EKS cluster. The final product should be similar to this:

![image](https://user-images.githubusercontent.com/100334754/173350669-d6054954-c5e1-4a7f-8bac-2031acf205fe.png)

STEP 5: Initialize Terraform workspace, Once you have cloned the repository, initialize your Terraform workspace, which will download and configure the providers.

terraform init using this command we can initialize the workspace.

![image](https://user-images.githubusercontent.com/100334754/173350765-4c495943-c3c9-4bb9-ae62-8d8e92543eea.png)

STEP 6: Provision the EKS cluster, In your initialized directory, run terraform apply and review the planned actions. Your terminal output should indicate the plan is running and what resources will be created.
terraform apply using this command we can review the planned actions.

![image](https://user-images.githubusercontent.com/100334754/173350853-f6daac42-0049-4fc6-8dcd-3e1c6432b63f.png)

STEP 7: Configure kubectl, Now that you've provisioned your EKS cluster, you need to configure kubectl.
Run the following command to retrieve the access credentials for your cluster and automatically configure kubectl.
aws eks --region $(terraform output -raw region) update-kubeconfig --name $(terraform output -raw cluster_name)
aws eks --region us-east-2 update-kubeconfig --name education-eks-V2r5Ppcx

![image](https://user-images.githubusercontent.com/100334754/173350919-caefca50-47c7-4e71-a66d-a81abba6ce87.png)

STEP 8: Deploy and access Kubernetes Dashboard
To verify that your cluster is configured correctly and running, you will deploy the Kubernetes dashboard and navigate to it in your local browser.
Deploy Kubernetes Metrics Server
The Kubernetes Metrics Server used to gather metrics such as cluster CPU and memory usage over time, is not deployed by default in EKS clusters.
1.	Deploy the Metrics Server with the following command
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

![image](https://user-images.githubusercontent.com/100334754/173350984-8c56aee4-197f-46dc-bd2a-5cd1d96bdd6e.png)

2.	Verify that the metrics-server deployment is running the desired number of pods with the following command.
kubectl get deployment metrics-server -n kube-system

![image](https://user-images.githubusercontent.com/100334754/173351069-32c9aadf-970a-461c-b863-194e8d73bec1.png)

STEP 9: Now, create a proxy server that will allow you to navigate to the dashboard from the browser on your local machine. This will continue running until you stop the process by pressing CTRL + C
kubectl proxy Using this command we can see the dashboard from the browser.

![image](https://user-images.githubusercontent.com/100334754/173351153-91357b78-a78a-46f2-a0ba-ded6be4bb4c1.png)

You should be able to access the Kubernetes dashboard here (http://127.0.0.1:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/).

![image](https://user-images.githubusercontent.com/100334754/173351231-2b3f1ea0-90b8-415a-9d83-f696828f52d3.png)

STEP 10: Authenticate the dashboard
To use the Kubernetes dashboard, you need to create a ClusterRoleBinding and provide an authorization token. This gives the cluster-admin permission to access the Kubernetes dashboard. Authenticating using kubeconfig is not an option. You can read more about it in the Kubernetes documentation.
In another terminal (do not close the kubectl proxy process), create the ClusterRoleBinding resource.
1.kubectl apply -f https://raw.githubusercontent.com/hashicorp/learn-terraform-provision-eks-cluster/main/kubernetes-dashboard-admin.rbac.yaml

![image](https://user-images.githubusercontent.com/100334754/173351318-d089f16e-0e48-46e2-994e-7d4755f4d745.png)

2.kubectl get secrets -n kube-system

![image](https://user-images.githubusercontent.com/100334754/173351369-61050a32-5d9a-4267-b456-004be5475e72.png)

Note:(We should copy the service controller token only)
3.kubectl describe secret service-controller-token-5gj8p -n kube-system

![image](https://user-images.githubusercontent.com/100334754/173351442-c6008bf3-8494-47c9-aec8-0d361ebdba9d.png)

STEP 11: Select "Token" on the Dashboard UI then copy and paste the entire token you receive into the dashboard authentication screen to sign in. You are now signed in to the dashboard for your Kubernetes cluster.

![image](https://user-images.githubusercontent.com/100334754/173351498-24590356-7805-40d7-9bd4-7f454e35e551.png)

![image](https://user-images.githubusercontent.com/100334754/173351537-251d446c-e9ee-4378-87ac-761d7b52a6f0.png)

STEP 11: Navigate to the "Cluster" page by clicking on "Cluster" in the left navigation bar. You should see a list of nodes in your cluster.

![image](https://user-images.githubusercontent.com/100334754/173351607-177b5708-09bd-4a59-9ebf-dba33c0bca7e.png)

STEP 11: If not, remember to destroy any resources. Run the destroy command and confirm with yes in your terminal.
terraform destroy












