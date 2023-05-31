# Register-External-Cluster-on-AWS-EKS
This project shows you how to Register an  EXTERNAL Cluster on AWS EKS


PROJECT: How to Register EXTERNAL Cluster on AWS EKS = https://docs.aws.amazon.com/eks/latest/userguide/connecting-cluster.html 

You can connect an external Kubernetes cluster to Amazon EKS with AWS CLI and the AWS Management Console. This process involves two steps: registering the cluster with Amazon EKS and applying a YAML manifest file to enable connectivity.


	Using the Amazon EKS Connector requires the following two IAM roles:
* The Amazon EKS Connector service-linked role is created when you register the cluster.
* The Amazon EKS Connector agent IAM role must be created manually.
* To enable cluster and workload view permission for another user, you must apply the eks-connector and Amazon EKS Connector cluster roles to your cluster. 
* How to create the Amazon EKS Connector agent IAM role. = https://docs.aws.amazon.com/eks/latest/userguide/connector_IAM_role.html 

    - Create a file named eks-connector-agent-trust-policy.json that contains the following JSON to use for the IAM role.
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": [
                    "ssm.amazonaws.com"
                ]
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
    - Create a file named eks-connector-agent-policy.json that contains the following JSON to use for the IAM role.
	{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "SsmControlChannel",
            "Effect": "Allow",
            "Action": [
                "ssmmessages:CreateControlChannel"
            ],
            "Resource": "arn:aws:eks:*:*:cluster/*"
        },
        {
            "Sid": "ssmDataplaneOperations",
            "Effect": "Allow",
            "Action": [
                "ssmmessages:CreateDataChannel",
                "ssmmessages:OpenDataChannel",
                "ssmmessages:OpenControlChannel"
            ],
            "Resource": "*"
        }
    ]
}
    - Create the Amazon EKS Connector agent role using the trust policy and policy you created in the previous list items.
	
	aws iam create-role \
     	--role-name AmazonEKSConnectorAgentRole \
     	--assume-role-policy-document file://eks-connector-agent-trust-policy.json

    - Attach the policy to your Amazon EKS Connector agent role.
	
	aws iam put-role-policy \
     	--role-name AmazonEKSConnectorAgentRole \
     	--policy-name AmazonEKSConnectorAgentPolicy \
     	--policy-document file://eks-connector-agent-policy.json

    - A role that includes AmazonEKSConnectorAgentRole now appears in your AWS IAM console. Select the role to view the attached policies.
    - Choose Permissions.
    - Ensure that the AmazonEKSClusterPolicy managed policy is attached to the role. If the policy is attached, your Amazon EKS cluster role is properly configured.
    - Choose Trust relationships, and then choose Edit trust policy.
    - Verify that the trust relationship contains the following policy. If the trust relationship matches the following policy, choose Cancel. If the trust relationship doesn't match, copy the policy into the Edit trust policy window and choose Update policy.

STEPS

1. Create the following permissions on IAM to enable you register the cluster

* eks:RegisterCluster
* ssm:CreateActivation
* ssm:DeleteActivation
* iam:PassRole


2. Create the following permissions on IAM to enable you de-register the cluster
* eks:DeregisterCluster
* ssm:DeleteActivation
* ssm:DeregisterManagedInstance

3. Install AWS CLI: use aws cli script provided or follow these steps: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
4. Install and Configure Kubectl = https://kubernetes.io/docs/tasks/tools/install-kubectl 






