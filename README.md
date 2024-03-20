#Kubernetes End to End project on EKS | EKS Install and app Deploy with Ingress

====> 1. First install/Download pre-requisites which are Kubectl, Eksctl, AWS CLI, Helm
 Step 1 :- Create a Cluster in the AWS EKS using the command line. Here I created a Cluster with serverless compute "FARGATE".
        $ eksctl create cluster --name <cluster_name> --region <specify_region> --fargate
    (After completed the deployment delete cluster. Otherwise it causes billing. For deleting the created Cluster use the below command:
        $ eksctl delete cluster --name <cluster_name> --region <specify_region>)

 Step 2 :- Create a Fargate profile first after created a EKS Cluster in AWS. Follow the below command:

 $ eksctl create fargateprofile \
    --cluster <cluster_name> \
    --region <specify_region> \
    --name <provide_name_for_profile> \
    --namespace <provide_name_for_namespace>

====> 2. Deploy the Namespace, Deployment, Service and Ingress by running the below command:

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml


After deployment run the below command for IAM OpenID Connection:

$ eksctl utils associate-iam-oidc-provider --cluster <cluster_name> --approve

=====> 3. SetUp Application Load Balancer:

- Download the IAM Policy and Create it using the downloaded JSON code:

$ curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json

To create policy, run the below command: 

$ aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

====> 4. Create an IAM role for serviceaccount:

$ eksctl create iamserviceaccount \
  --cluster=<cluster_name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<YOUR_AWS_ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve

===> 5. Deploy the ALB Controller:

- Add helm repo and update it:

$ helm repo add eks https://aws.github.io/eks-charts

$ helm repo update eks

- Insatll the ALB:

$ helm install aws-load-balancer-controller eks/aws-load-balancer-controller \            
  -n kube-system \
  --set clusterName=<cluster_name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<specify_region> \
  --set vpcId=<YOUR-VPC-ID>


====> 6. Finally, Verify the Deployment are running using the below command:

$ kubectl get deployment -n kube-system aws-load-balancer-controller

=====> Now we can use the load balancer IP to access the running application through the Browser.
