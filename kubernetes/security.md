## API Access

#### Authentication

* Authentication are used to include client certificates, password or tokens
    * Human accounts typically use client certificates
    * Service accounts typicall use tokens
* In kubernetes authentication means serving right certificates from client to cluster
* Request that can't authenticated are rejected with HTTP 401
* k8s doesn't have user objects. A user is a just a set of client certificates.
* Users are just aliases in .kube/config file
* User certificate components;
    * client public key
    * client private key
    * certificate authority
* kubectl commands show certificate detail with verbosity level.
    `kubectl get pods -v=10`
* `kubectl proxy` can be used alternatively to run a proxy that takes care of passing authentications to kube-api server
* if curl used to reach API, pem files(private key, public key, ca) should be provided

#### Authorization

* After authentication user must be authorization
* k8s is doing this with Authorization Modes
* Kubernetes is doing this by using Authorization Modes. 
* An authorization mode implements policies to allow access. 
* A request is authorized if an existing policy declares that the user has permissions to complete the requested actions. 
* And Kubernetes can use different Authorization Modules, such as RBAC or ABAC. 
* Authorization Modules are specified when kube-apiserver is started. You can have look at the etc/kubernetes/manifests/kube-apiserver.yaml to change the current setting. 
* Two authorization modes
    * ABAC, attribute based access control
    * RBAC, role based access control
    * Webhook, uses HTTP POST when something happens
* There are two global Deny/Allow settings
    * AlwaysDeny
    * AlwaysAllow
* ABAC example

    ** Bob can just read pods in namespace “projectCaribou” **
    ```
        {"apiVersion": "abac.authorization.kubernetes.io/v1beta1", "kind": "Policy", "spec": {"user": "bob", "namespace": "projectCaribou", "resource": "pods", "readonly": true}}
    ```
* RBAC is more sophisticated
* There are 4 different type
    * Role, used to grant access to resources within a single namespace
    * ClusterRole, grant access at cluster level
    * Rolebinding, use to grant permissions defined in a role to one or more users. RoleBinding refer to a role or a ClusterRole
    * ClusterRoleBinding, use to grant permissions at the cluster level or all namespaces
* Authorization steps
    * Determine or create a namespace
    * Create the user account by defining certificates
    * Set the credentials for the user to the namespace using context
    * Create role
    * Bind the user to role
    * Verify user has access

#### Admission Control

* After authorization admission works.
* Admission checks additional restrictions(such as resource quota)
* More specifications at /etc/kubernetes/manifests/kube-apiserver.yaml

#### Security context

* Defines privilege and access control settings for pods or containers such as;
    * uid or gid based DAC
    * SELinux
    * Apparmor
    * Linux capabilities
    ...
* Example; creates a pod uid and gid is 1000, the owner group of /data/demo directory is 2000

```
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 1000
    fsGroup: 2000
  volumes:
  - name: securevol
    emptyDir: {}
  containers:
  - name: sec-demo
    image: busybox
    command: ["sh", "-c", "sleep 3600"]
    volumeMounts:
    - name: securevol
      mountPath: /data/demo
    securityContext:
      allowPrivilegeEscalation: false
```

#### User Accounts

* To create a user account;
    * Create a public/private key pair
    * Create certificate signing request
    * Sign the certificate
    * Create a config file that uses this certificates to access cluster
    * Create an RBAC role
    * Create an RBAC role binding

* User create example
   1. Create user working environment 
    `kubectl create ns staff` 
    `kubectl config get-contexts` --> gets current config
   
   2. Create user account
    `sudo useradd -G wheel anna`
    `sudo passwd anna`
    `openssl genrsa -out anna.key 2048` --> create private key
    `openssl req -new -key anna.key -out anna.csr -subj "/CN=anna/O=staff"` --> create a certificate signing request
    `sudo openssl x509 -req -in anna.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out anna.crt -days 180` --> create a certificate signing request to CA
   
   3. View and update kubernetes credential files for new user
    `kubectl config view`
    `kubectl config set-credentials anna --client-certificate=./anna.crt --client-key=./anna.key`
    `kubectl config view`
   
   4. Create default context for new user
    `kubectl config set-context anna-context --cluster=kubernetes --namespace=staff --user=anna`
    `kubectl --context=anna-context get pods` --> will fail because there is no RBAC
    `kubectl config get-contexts`
   
   5. Configure RBAC to define a staff role
    `vi staff-role.yaml`
    `kubectl create -f staff-role.yaml`
    
    staff-role.yaml;

    ```
    kind: Role
    apiVersion: rbac.authorization.k8s.io/v1beta1
    metadata:
        namespace: staff
        name: staff
    rules:
      - apiGroups: ["", "extensions", "apps"]
        resources: ["deployments", "replicasets", "pods"]
        verbs: ["list", "get", "watch", "create", "update", "patch", "delete"] 
    ```

   6. Bind user to role
    `vi rolebind.yaml`
    `kubectl create -f rolebind.yaml`

    rolebind.yaml;

    ```
    kind: RoleBinding
    apiVersion: rbac.authorization.k8s.io/v1beta1
    metadata:
        name: staff-role-binding
        namespace: staff
    subjects:
        - kind: User
          name: anna
          apiGroup: ""
    roleRef:
        kind: Role
        name: staff
        apiGroup: ""
    ```
   
   7. Test it
    `kubectl --context=anna-context get pods`
    `kubectl create deployment nginx --image=nginx`
    `kubectl -n staff describe role staff`

* `/etc/kubernetes/admin.conf`should be copied under `.kube` at `home` directory to execute `kubectl` commands as reguler user.