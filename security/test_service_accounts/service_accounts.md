In Kubernetes, a service account is an identity that allows processes running within a pod to authenticate and interact with the Kubernetes API server. It provides a way for pods to access cluster resources and perform operations on behalf of the service account.  

When a pod is created, you can specify a service account to associate with it. The service account is defined in the Kubernetes cluster and has its own credentials, which are mounted into the pod as a token or a set of environment variables. These credentials are used to authenticate the pod with the Kubernetes API server.

Service accounts are useful for various purposes, such as:

Authentication: Service accounts authenticate pods to the Kubernetes API server, allowing them to make requests and perform actions based on the permissions granted to the service account.

Authorization: By using RBAC (Role-Based Access Control) rules, you can control the permissions and access levels of service accounts. This ensures that pods can only access the resources and perform the operations they are allowed to.

Fine-grained access control: Different service accounts can have different RBAC roles and permissions. This enables you to control access to specific resources and restrict certain operations based on the needs of the pods or applications.

Integration with other Kubernetes features: Service accounts can be used in conjunction with other Kubernetes features, such as Secrets and ConfigMaps, to securely provide sensitive information or configuration data to the pods.

Overall, service accounts are a fundamental component of Kubernetes security and help ensure that pods and applications can securely interact with the cluster's API server. By properly configuring and managing service accounts, you can control access and permissions, enhancing the security and stability of your Kubernetes environment. 
 
 ### Excercises:
 
 1. What is the secret token used by the default service account?  
 Run the command `kubectl describe serviceaccount default` and look at the Tokens field.  
 
 2. We just deployed the Dashboard application. Inspect the deployment. What is the image used by the deployment?  
Run the command kubectl describe deployment  
 
3. Inspect the Dashboard Application POD and identify the Service Account mounted on it.  
Run the
command kubectl get po -o yaml and inspect serviceAccount.  

4. At what location is the ServiceAccount credentials available within the pod?  
Run the command kubectl describe pod and look for volume mount path.  

5. The application needs a ServiceAccount with the Right permissions to be created to authenticate to Kubernetes. The default ServiceAccount has limited access. Create a new ServiceAccount named dashboard-sa.  
Run the command kubectl create serviceaccount dashboard-sa  

6. Enter the access token in the UI of the dashboard application. Click Load Dashboard button to load Dashboard
Create an authorization token for the newly created service account, copy the generated token and paste it into the token field of the UI.
To do this, run kubectl create token dashboard-sa for the dashboard-sa service account, copy the token and paste it in the UI.  
You shouldn't have to copy and paste the token each time. The Dashboard application is programmed to read token from the secret mount location. However currently, the default service account is mounted. Update the deployment to use the newly created ServiceAccount
Edit the deployment to change ServiceAccount from default to dashboard-sa.  
Use the kubectl edit command for the deployment and specify the serviceAccountName field inside the pod spec.

OR

Make use of the kubectl set command. Run the following command to use the newly created service account: - kubectl set serviceaccount deploy/web-dashboard dashboard-sa  
create yaml  
kubectl apply -f <FILE-NAME>.yaml  
  
  




