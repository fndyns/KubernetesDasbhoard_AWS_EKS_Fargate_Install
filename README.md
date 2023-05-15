# KubernetesDasbhoard_AWS_EKS_Fargate_Install
KubernetesDasbhoard_AWS_EKS_Fargate_Install

To install Kubernetes Dashboard to AWS EKS Fargate cluster , I followed the link below ;

https://gist.github.com/vgribok/138b71dbe291022a07990647f8a5956f

But in this link, the step "To login, run these commands to copy security token to the clipboard:" did not work, and have gotten the error in the SS "Error_1" in this repo. Then to fix this error , Saddam did the steps below to create role bindings etc ;



Create an admin user for the Kubernetes Dashboard:

    Create a file named eks-admin-service-account.yaml with the following content:
   
apiVersion: v1
kind: ServiceAccount
metadata:
  name: eks-admin
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: eks-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: eks-admin
    namespace: kube-system


Apply the configuration by running the following command:

kubectl apply -f eks-admin-service-account.yaml

Create a proxy connection to the Kubernetes Dashboard:

    Start the proxy connection by running the following command in a new terminal:
    
    
kubectl proxy


Then run below command to get the token ;

kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')

*** Saddam said that he created below for Role binding stuff

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard-custom
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
