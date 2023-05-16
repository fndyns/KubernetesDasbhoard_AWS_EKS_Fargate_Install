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


Teams den kopyaladıklarım :

-------------------------


[Yesterday 4:43 PM] Saddam Zourob




How can we configure and activate Kubernetes Dashboard in AWS EKS Fargate cluster

 

To configure and activate Kubernetes Dashboard in an AWS EKS Fargate cluster, you can follow these steps:

 

Step 1: Install the Kubernetes Dashboard

1. Connect to your EKS cluster using the `kubectl` command-line tool.

2. Run the following command to install the Kubernetes Dashboard:

 

```

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.3.1/aio/deploy/recommended.yaml

```

 

This command will deploy the necessary resources for the Kubernetes Dashboard.

 

Step 2: Create an Administrative User

1. Create a file called `dashboard-adminuser.yaml` and paste the following contents into it:

 

```yaml

apiVersion: v1

kind: ServiceAccount

metadata:

  name: admin-user

  namespace: kubernetes-dashboard

---

apiVersion: rbac.authorization.k8s.io/v1

kind: ClusterRoleBinding

metadata:

  name: admin-user

roleRef:

  apiGroup: rbac.authorization.k8s.io

  kind: ClusterRole

  name: cluster-admin

subjects:

- kind: ServiceAccount

  name: admin-user

  namespace: kubernetes-dashboard

```

 

2. Apply the configuration using the following command:

 

```

kubectl apply -f dashboard-adminuser.yaml

```

 

This will create a service account and assign the `cluster-admin` role to it.

 

Step 3: Access the Kubernetes Dashboard

1. Start a proxy server to access the Kubernetes Dashboard:

 

```

kubectl proxy

```

 

2. Access the dashboard by opening the following URL in your web browser:

 

```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

```

 

3. To authenticate with the Dashboard, you'll need to retrieve an authentication token. Run the following command to get the token:

 

```

kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')

```

 

This command will display the token you can use to log in to the Kubernetes Dashboard.

 

4. Copy the token and paste it into the "Enter token" field on the Kubernetes Dashboard login page.

 

Once you've completed these steps, you should be able to access and authenticate with the Kubernetes Dashboard in your AWS EKS Fargate cluster.










[Yesterday 4:47 PM] Saddam Zourob




I apologize for the confusion. It seems that the `kubernetes-dashboard` ClusterRoleBinding already exists and cannot be modified directly.

 

To resolve this issue, you can create a new ClusterRoleBinding with the necessary RBAC configuration. Follow these steps:

 

1. Create a file called `dashboard-rbac.yaml` and paste the following RBAC configuration into it:

 

```yaml

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

```

 

2. Apply the new ClusterRoleBinding by running the following command:

 

```shell

kubectl apply -f dashboard-rbac.yaml

```

 

This will create a new ClusterRoleBinding named `kubernetes-dashboard-custom` that grants the `cluster-admin` role to the `kubernetes-dashboard` service account.

 

Once the new ClusterRoleBinding is applied, give the cluster a few moments to propagate the changes. Then, try accessing the Kubernetes Dashboard again. The service account should now have the necessary permissions to list the resources without encountering the "forbidden" errors.

 

Please note that granting the `cluster-admin` role to the Kubernetes Dashboard service account gives it extensive permissions. Make sure to use this approach only for development or testing purposes. In production environments, it's recommended to assign more restrictive RBAC roles based on the required permissions.







[Yesterday 4:47 PM] Saddam Zourob




The errors:

 

 

The error message indicates that the service account used by the Kubernetes Dashboard (`system:serviceaccount:kubernetes-dashboard:kubernetes-dashboard`) doesn't have the necessary permissions to access various resources in the cluster. To resolve this issue, you'll need to grant the appropriate RBAC permissions to the service account.

 

Here are the steps to grant the necessary permissions to the Kubernetes Dashboard service account:

 

1. Create a file called `dashboard-rbac.yaml` and paste the following RBAC configuration into it:

 

```yaml

apiVersion: rbac.authorization.k8s.io/v1

kind: ClusterRoleBinding

metadata:

  name: kubernetes-dashboard

  namespace: kubernetes-dashboard

roleRef:

  apiGroup: rbac.authorization.k8s.io

  kind: ClusterRole

  name: cluster-admin

subjects:

- kind: ServiceAccount

  name: kubernetes-dashboard

  namespace: kubernetes-dashboard

```

 

2. Apply the RBAC configuration by running the following command:

 

```shell

kubectl apply -f dashboard-rbac.yaml

```

 

This configuration creates a `ClusterRoleBinding` that binds the `cluster-admin` role to the `kubernetes-dashboard` service account within the `kubernetes-dashboard` namespace. The `cluster-admin` role grants full access to the cluster resources.

 

After applying the RBAC configuration, give the cluster a few moments to propagate the changes. Then, try accessing the Kubernetes Dashboard again. The service account should now have the necessary permissions to list the resources without encountering the "forbidden" errors.

 

Note: Granting the `cluster-admin` role to the Kubernetes Dashboard service account gives it extensive permissions. Make sure to use this approach only for development or testing purposes. In production environments, it's recommended to assign more restrictive RBAC roles based on the required permissions.







[Yesterday 4:48 PM] Saddam Zourob




Saddam Zourob

I apologize for the confusion. It seems that the `kubernetes-dashboard` ClusterRoleBinding already exists and cannot be modified directly.     To resolve this issue, you can create a new ClusterRoleBinding with the necessary RBAC configuration. Follow these steps:     1. Create a file called `dashb…


Extensions error








[Yesterday 4:54 PM] Saddam Zourob




How can I see which services are calling the other services in my EKS cluster? I already have Kuberentes-Dashboard

 

To determine which services are calling other services within your Amazon Elastic Kubernetes Service (EKS) cluster, you can use several tools and techniques. While Kubernetes Dashboard provides a visual representation of your cluster, it might not provide the level of detail you require for service-to-service communication. Here are a few alternative approaches you can consider:

 

1. **kubectl**: Use the `kubectl` command-line tool to extract information about your EKS cluster. You can use the following commands to retrieve information on services, deployments, and pods:

 

   - To list all services in your cluster: `kubectl get services --all-namespaces`

   - To list all deployments in your cluster: `kubectl get deployments --all-namespaces`

   - To list all pods in your cluster: `kubectl get pods --all-namespaces`

 

   By inspecting these resources, you can identify the relationships between services.

 

2. **kube-state-metrics**: Install the kube-state-metrics add-on in your cluster. It exposes detailed cluster metrics and information about services, deployments, and pods. With kube-state-metrics, you can use Prometheus or other monitoring tools to query and visualize the data. You can find installation instructions and more details in the official kube-state-metrics documentation.

 

3. **Service Mesh**: Consider implementing a service mesh such as Istio or Linkerd in your EKS cluster. Service meshes provide advanced observability and traffic management capabilities, including the ability to visualize service-to-service communication. They can generate detailed telemetry data and visualizations that show the flow of requests between services. By deploying a service mesh and configuring it to intercept traffic, you can gain insight into service communication patterns.

 

4. **Distributed Tracing**: Implementing distributed tracing in your applications can provide detailed information about the flow of requests and the interactions between services. Tools like Jaeger, Zipkin, or AWS X-Ray can help you trace requests as they travel through your services, enabling you to understand the dependencies and troubleshoot issues.

 

Combining these approaches should give you a good understanding of the service communication within your EKS cluster. Choose the method that best suits your requirements and familiarity with the tools.


