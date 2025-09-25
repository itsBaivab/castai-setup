# CAST AI Installation Guide for EKS

This guide explains how to install CAST AI on your Amazon EKS cluster.

# Create EKS cluster on AWS
- Go to AWS console
- Navigate to EKS service
- Click on "Create cluster"
- Choose "Customer configuration" option
- Provide a name for your cluster "castai-eks"
- only select system in the built-in node pool(Auto mode)
- Follow the prompts to configure your cluster (name, version, role, VPC, subnets, security groups) (keep them default)
- Cluster Endpoint access: Public 
- Add all possible Add-ons to get started with some extra pods for the demo
- Create the cluster and wait for it to become active


# Create a node group with custom launch template
- Go to the "Compute" tab in your EKS cluster
- Click on "Add Node Group"
- Provide a name for your node group "castai-node-group"
- Choose "Custom launch template" option
- Click on "Create launch template"
- Create a new lunch tempte using the following command:
```sh
     aws ec2 create-launch-template \
    --launch-template-name castai-nodegroup-template \
    --launch-template-data '{
        "MetadataOptions": {
            "HttpPutResponseHopLimit": 2,
            "HttpTokens": "required"
        }
    }'
```
- After creating the launch template, select it from the dropdown
- Choose the latest Amazon Linux 2 AMI (or anything else) for EKS
- Choose instance type "t3.medium"(or anything else)
- Set the desired, minimum, and maximum size for your node group (go with a larger number of minimun node as it will be autometically scaled down by CAST AI to the required number it needs)
- Ensure that the launch template has the necessary IAM role with required permissions
- Create the launch template
- Select the created launch template in the node group creation wizard
- Complete the node group creation process and wait for the nodes to become active

## Prerequisites

- An existing EKS cluster with node pool built using custom launch template
- `kubectl` configured for your cluster
- AWS CLI installed
- CAST AI account ([Sign up here](https://app.cast.ai/signup))


## Steps
### Run below command to access your cluster using kubectl
```sh
aws eks --region <region-code> update-kubeconfig --name castai-eks
```
### 1. Install CAST AI Connector
- log in to your CAST AI account
- Go to connect your first cluster option 
- Select EKS option
- Run the command present in the window
- Close it and move to next step (It will take some time to install the read only agent in your cluster)
- Open the cluster and go to Eable CAST AI option
- Select all the options and run the command to install everything in your cluster
- Wait for some time to get everything installed
- click on I ran the script button
- You will see that your cluster is connected

### 2. Optimizing Cost of the Cluster
- Go the auto scaler tab -> settings
- Enable unschedule pod policy
- Node Deletion Policy -> Make sure to active Evictor as well
- Save the settings
- Go to the Available savings tab
- Turn on workload resizing
- Turn on Use spot instances for only spot friendly workloads (you don't want run your critical workloads on spot instances) Here is how you can mark your workloads as spot friendly  [Mark Workloads as Spot Friendly](https://docs.cast.ai/docs/spot#supported-workload-configurations)
- Use cluster scheduler to hibernate your cluster during non working hours configuring the time as per your need
- Use ARM based CPU options if your application supports it
- Then Optimize using rebalancer


## References

- [CAST AI EKS Integration Docs](https://docs.cast.ai/docs/step-by-step-guide-to-connecting-your-cluster)
- [CAST AI GitHub](https://docs.cast.ai/docs/getting-started)
