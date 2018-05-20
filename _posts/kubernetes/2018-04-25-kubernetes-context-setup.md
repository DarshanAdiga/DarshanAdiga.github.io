# Understanding Kubernetes Users, Namespaces and Contexts
This is post is about my takeaways of few sub-topics of [kubernetes](https://kubernetes.io/).

> Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications.

### Target audience
The readers are expected to have a basic understanding of kubernetes and a basic level of hands on experience with Kubernetes.
This post will shed some light on Kubernetes Users, namespaces and Contexts.

**Note** All the example snippets in this post were tested against Kubernetes version 1.9

## Contents of this post
1. Kubernetes Namespaces
2. Kubernetes Users
3. Kubernetes Clusters and Contexts

## Kubernetes Namespaces
In general, namespaces are used to have separation of concerns. In Kubernetes namespaces are used to have the clear separation of cluster resources.
There are [3 namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) already created by Kubernetes.
1. default
2. kube-system
3. kube-public

But in most scenarios, it's normal to create multiple user-defined namespaces.
A user-defined namespace called `prod-space` can be created as shown below:

Create a yaml file `prod-namespace.yml` with below content
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: prod-space
```

And then create the namespace by running below command:
```bash
kubectl create -f prod-namespace.yml
```
Verify that the namespace `prod-space`  has been created by running:
```bash
kubectl describe namespace prod-space
```

## Kubernetes Users
Kubernetes supports two types of user entities

1. **Service accounts**

   The credential/secrets for service accounts is maintained by Kubernetes itself. Cluster resources like pods and services run as Service account users. Thus, these cluster resources will have access to the identity of the service accounts.
2. **Normal users**

   The credential for the normal users should be maintained by external services by yourself. The Kubernetes API requests(via `kubectl` or other clients) can be made as normal users.

### Creating an Admin roled Service account
Service accounts can be given fine-grained access to various cluster resources based on account roles. This type of access control is called `Role-Based Access Control` or in-short `RBAC`.
Let's see how we can create a service account with all the admin privileges.

#### Creating a Service Account
Create a file `kube-admin-service-account.yml`
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: kube-admin
  namespace: prod-space
```
And then create the service account
```bash
kubectl create -f kube-admin-service-account.yml
```
Use the below command to verify that the service account is created successfully
```bash
kubectl get serviceAccount kube-admin -n `prod-space`
```
This should result in,
```bash
NAME         SECRETS   AGE
kube-admin   1         4d
```

As you can see, the service account `kube-admin` gets created under `prod-space` namespace created by the Kubernetes.
For example, try to get the service account in `default` namespace
```bash
kubectl get serviceAccount kube-admin -n default
```
The above command will result in:
```bash
> Error from server (NotFound): serviceaccounts "kube-admin" not found
```

The service account created doesn't actually have admin privileges yet. Let's see how we can make this service account more powerful.

#### Role Binding of Service Accounts
Kubernetes service accounts and users entities can be assigned different types of roles to grant different types of accesses. In other words, roles define permissions.
There are two types of roles based on their scope:
1. Role: Can be used to grant access to resources inside a given namespace
2. ClusterRole: Can be used to grant access to cluster-level resources and are not bound to any namespace

Now, lets see how to assign admin capabilities to the `kube-admin` for all the resources under `prod-space` namespace.
Create a file `kube-admin-rolebinding.yml`
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: kube-admin-role  # Name of the RoleBinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: cluster-admin  # Name of the role to be assigned to kube-admin
subjects:
- kind: ServiceAccount
  name: kube-admin
  namespace: prod-space
```
And then create the RoleBinding
```bash
kubectl create -f kube-admin-rolebinding.yml
```

Verify that the role binding has been created using below command
```bash
kubectl -n prod-space get roleBindings
```

### Accessing Kubernetes Dashboard using kube-admin service account
We can login to Kubernetes dashboard running at `https://<YOUR_KUBE_MASTER>:6443/ui` using `kube-admin` credentials. But we don't have to create/assign any kind of credential to our `kube-admin`.

That's because whenever a service account is created/modified, Kubernetes automatically creates certain secrets for it.

So when we created `kube-admin` service account and assigned `cluster-admin` role to it, Kubernetes API server will have created and saved an id-token for the service account `kube-admin` under namespace `prod-space` with role as `cluster-admin`.

Run the below commands to find the token of `kube-admin` service account:
```bash
TOKEN_NAME=kubectl -n prod-space get secret | grep kube-admin | awk '{print $1}'
kubectl -n prod-space describe secret $TOKEN_NAME | grep token:
```
This will print out the token as shown below:
```bash
token:      eyJhbGciOi.......6GQ
```
Use that token to login to dashboard (https://YOUR_KUBE_MASTER:6443/ui) as `kube-admin` user.

## Cluster and Context
Kubernetes actions requested by clients(`kubectl` or any other clients) will always be run against a specific context. In most cases, user doesn't have to be aware of this contextual details. But to make the complete use of Kubernetes capabilities, the basic understanding of contexts and clusters is important.

- **Cluster**
  A cluster entity is nothing but a collection of resources grouped together and separated from rest of the resources.

- **Context**
  A context is nothing but a bounded environment with certain state. Any entity within a context will have access to the state of the context.

Sorry, I tried my best to keep the above definitions simple and I know I have failed :)
Let's look at these definitions from Kubernetes perspective.

Kubernetes has a way to divide the *entire cluster into sub-clusters*. Individual sub-cluster can be defined using a `cluster` entity.

Now, we can associate a service account/user with a cluster. And as you already know, an account/user will be assigned a namespace.
So a *context will have an account/user, his namespace, and a cluster*. That means, all these entities together form an environment and that forms a `context`.

**In summary,**
- A user belongs to a Cluster, and a user belongs to a namespace.
- A context will have a cluster with a specific user under a particular namespace.

So, all the actions that are requested by any type of Kubernetes client **will have contextual information as part of the request, implicitly**.

Usually, the default contextual information is stored under `/etc/kubernetes/admin.conf`. And any modifications or creation of new contextual information will result in a copy of `/etc/kubernetes/admin.conf` in `~/.kube/config` file along with the changes.

Currently active context and other state of the active context can be viewed using below command:
```bash
kubectl config view
```

### Putting it all together
Now let's understand creation and use of contexts.

Create a credential for service account `kube-admin` with name `kube-admin-cred`
```bash
kubectl config set-credentials kube-admin-cred --token=<TOKEN_of_kube-admin_SERVICE_ACCOUNT_HERE>
```

Create a cluster with name `prod-cluster`
```bash
kubectl config set-cluster prod-cluster --insecure-skip-tls-verify=true
```

Create a context with name `prod-context` with `prod-space` namespace, with `prod-cluster` cluster and with `kube-admin-cred` credential of the account `kube-admin`.
```bash
kubectl config set-context prod-context --user=kube-admin-cred --namespace=prod-space --cluster=prod-cluster
```

**NOTE:** All these changes should be saved, by default, in a config file at `~/.kube/config`

Now, to start using this `prod-context` context (which is configured to use `prod-cluster` cluster with `kube-admin` user) with every command of `kubectl` *from now onwards*, set the current context as shown below:
```bash
kubectl config use-context prod-context
```
To verify that the current context has been switched to `prod-context`, run this
```bash
kubectl config view
```
Now, the field `current-context:` should be set to `prod-context`.

**NOTE: Now whenever you enter a kubectl command, the action will apply to the cluster and namespace configured under the *prod-context* context.**

## Final notes
This post just skims through Kubernetes contexts and clusters capabilities. And there is lot more to these features, especially regarding resource allocation, security and access controls. This post is just a basic documentation of my intuitions about Kubernetes contexts and clusters.

**Reference:**
- [https://github.com/kubernetes/dashboard/wiki/Creating-sample-user](https://github.com/kubernetes/dashboard/wiki/Creating-sample-user)
- [http://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/](http://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/)
- [https://v1-9.docs.kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/](https://v1-9.docs.kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)
