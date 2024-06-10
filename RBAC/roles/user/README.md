## Create client certificate
 - `mkdir cert & cd cert`

### Generate key
 - `openssl genrsa -out user1.key 2048`

### Generate CRS
 - `openssl req -new -key user1.key -out user1.csr -subj "/CN=user1/O=group1"`

### Generate CRT
 - `openssl x509 -req -in user1.csr -CA ~/.minikube/ca.crt -CAkey ~/.minikube/ca.key -CAcreateserial -out user1.crt -days 500`

### Create user
 - `kubectl config set-credentials user1 --client-certificate=user1.crt --client-key=user1.key`

### Update context entry in kubeconfig
 - `kubectl config set-context user1-context --cluster=minikube --user=user1`

### Verfiy the added entry
 - `kubectl config view`

###Go to previous dir
 - `cd ..`

### Switch the user and verfiy the permissions
 - `kubectl config use-context user1-context`
 - `kubectl get pods ### this will give 403 Forbidden error, this is because newly created user is not having permissions.`

### Assign the required permission to the user, by using role and rolebindings
 - `kubectl config use-context <default context name> ### minikube or default`
 - `kubectl apply -f role.yaml`
 - `kubectl apply -f rolebinding.yaml`

### Verfiy permissions
 - `kubectl config use-context user1-context`
 - `kubectl get pods ### should work now without any error`
 - `create pod using: kubectl apply -f pod.yaml`
 - `kubectl get pod`

### Attempt to delete created pod (will get 403 Forbidden error as newly create user is not having permission to delete it)
 - `kubectl delete pod nginx-pod`
 - `kubectl delete -f pod.yaml` 

### Simple auth checks cmds
 - `kubectl auth can-i get pods -n default --as=user1 ### this will return "yes" which means user1 has permission to get list of pods`
 - `kubectl auth can-i delete pods -n default --as=user1 ### this will retunr "no" which means user1 has no permission to get list of pods`