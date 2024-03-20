#Kubernetes End to End project on EKS | EKS Install and app Deploy with Ingress

====> First install/Download pre-requisites which are Kubectl, Eksctl, AWS CLI
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

2. Deploy the Namespace, Deployment, Service and Ingress by running the below command:

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
