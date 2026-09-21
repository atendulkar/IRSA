# IRSA
# Kubernetes IAM Role For Service Accounts
## Flow Diagram
<img width="714" height="684" alt="IRSA5 drawio" src="https://github.com/user-attachments/assets/5be27655-9bac-4783-a2b2-264a6293782e" />

## Steps
1. AWS Reference: [IAM roles for service accounts - Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html)
2. Create an EKS cluster
   - By default it provides OIDC provider
3. Create an IAM OIDC provider
   - [Create an IAM OIDC provider for your cluster - Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/enable-iam-roles-for-service-accounts.html)
5. Create a S3 bucket and upload file(for testing)
6. Create IAM policy(JSON format)
```
{
		    "Version":"2012-10-17",
		    "Statement": [
		        {
		            "Effect": "Allow",
		            "Action":  [
		                              "s3:ListBucket",
		                              "s3:GetObject"
		             ],
		            "Resource": [
		                                 "arn:aws:s3:::my-pod-secrets-bucket"
		              ]
		        }
		    ]
		}
```
		
7. Create IAM role and service account
   - [Assign IAM roles to Kubernetes service accounts - Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/associate-service-account-role.html)
   - ```
      kubectl apply -f <service-account_irsa.yaml>
      ```
   - ```
     aws iam create-role --role-name my-role --assume-role-policy-document file://trust-relationship.json --description "my-role-description"
     ```
   - ```
     aws iam attach-role-policy --role-name my-role --policy-arn=arn:aws:iam::$account_id:policy/my-policy
     ```
8. Annotate your service account
   - ```
     kubectl annotate serviceaccount -n $namespace $service_account eks.amazonaws.com/role-arn=arn:aws:iam::$account_id:role/my-role
     ```
9. Create your app
   - ```
     Kubectl apply -f <pod-definition.yaml>
     ```
10. Test access permission
    - Login to container
     ```
     kubectl exec -it <pod name> -n <namespace> -- /bin/sh
     ```
    - List bucket
     ```
     aws s3 list <bucket name>
     ```
     
