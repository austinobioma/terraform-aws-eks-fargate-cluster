To download an IAM policy that allows the AWS Load Balancer Controller to make calls to AWS APIs on your behalf, run this command:

curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.4.1/docs/install/iam_policy.json

4.    To create an IAM policy using the policy that you downloaded in step 3, run this command:

aws iam create-policy \
   --policy-name AWSLoadBalancerControllerIAMPolicy \
   --policy-document file://iam_policy.json

5.    To create a service account named aws-load-balancer-controller in the kube-system namespace for the AWS Load Balancer Controller, run this command:

eksctl create iamserviceaccount \
  --cluster=YOUR_CLUSTER_NAME \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --attach-policy-arn=arn:aws:iam::<AWS_ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy \
  --override-existing-serviceaccounts \
  --approve

6.    To verify that the new service role was created, run this command:

eksctl get iamserviceaccount --cluster YOUR_CLUSTER_NAME --name aws-load-balancer-controller --namespace kube-system

-or-

kubectl get serviceaccount aws-load-balancer-controller --namespace kube-system

Install the AWS Load Balancer Controller using Helm

Important: For more information, see cert-manager on the Jetstack GitHub site, and the discussion topic Cert-manager issues with Fargate on the Kubernetes GitHub site.

1.    To add the Amazon EKS chart repo to Helm, run this command:

helm repo add eks https://aws.github.io/eks-charts

2.    To install the TargetGroupBinding custom resource definitions (CRDs), run this command:

kubectl apply -k "github.com/aws/eks-charts/stable/aws-load-balancer-controller//crds?ref=master"

3.    To install the Helm chart, run this command:

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
    --set clusterName=YOUR_CLUSTER_NAME \
    --set serviceAccount.create=false \
    --set region=YOUR_REGION_CODE \
    --set vpcId=<VPC_ID> \
    --set serviceAccount.name=aws-load-balancer-controller \
    -n kube-system

