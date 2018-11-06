## Kubernetes dashboard 

### Kubernetes Dashboard Authorization 

To access the kubernetes dashboard you login to your controller or use your own client where you have a _kubectl_ working.

#### Kubectl 

Enable proxy, local client: 
$ kubectl proxy 

**ssh-tunnel proxy:**

ssh -L 8001:localhost:8001 host-with-kubectl
$ kubectl proxy 

What proxy is doing is that it opens up a localhost proxy to various functions in your kubernetesystem via webinterface and you will be authorized to do stuff. 


#### Kubernetes dashboard 

Since a few versions back you are not allowed to access the dashboard without authorization and there is a few steps needed to get the token. 

This will create a ServiceAccount with admin access (This is not recommended for production) name this file: dashboard-admin.yaml 

```yaml 
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard
  labels:
    k8s-app: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kube-system
```

To add this account you run: 
$ kubectl apply -f dashboard-admin.yaml 
_clusterrolebinding.rbac.authorization.k8s.io "kubernetes-dashboard" created_

Next step is to get some information about this account, this is done with this command: 
$ kubectl describe serviceaccount kubernetes-dashboard -n kube-system

```
Name:                kubernetes-dashboard
Namespace:           kube-system
Labels:              k8s-app=kubernetes-dashboard
Annotations:         kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"v1","kind":"ServiceAccount","metadata":{"annotations":{},"labels":{"k8s-app":"kubernetes-dashboard"},"name":"kubernetes-dashboard","name...
Image pull secrets:  <none>
Mountable secrets:   kubernetes-dashboard-token-vrlfx
Tokens:              kubernetes-dashboard-token-vrlfx
Events:              <none>
```

This will print some information about the account, but you are interested in the variable beside: mountable secrets. 
For my example the secret is named (This will be different from each cluster): kubernetes-dashboard-token-vrlfx 

To get the token that will allow you to login to your kubernetes dashboard you issue this command (This is with the variable from last command): 
$ kubectl describe secret kubernetes-dashboard-token-vrlfx -n kube-system 

```
Name:         kubernetes-dashboard-token-vrlfx
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name=kubernetes-dashboard
              kubernetes.io/service-account.uid=acb9aad9-dc1b-11e8-8943-001a4a102616

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1094 bytes
namespace:  11 bytes
token:      longtoken (mine is 905 chars), this is what you will paste into the "token" field in kubernetes dashboard.
```

This will output a block of text,and you are interested in the one saying token: followed by alot of random letters and numbers, this whole block will be your dashboard token. 
