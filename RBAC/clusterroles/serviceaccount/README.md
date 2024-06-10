### Create serviceaccount and its token using secrets
 - `kubectl apply -f serviceaccount.yaml`

### Get token of access serviceaccount
- `token=$(kubectl describe secret satest-token | awk '/token:/ {print $2}')`

### Access k8s api and verfiy the permissions by using serviceaccount
 - `curl -k --header "Authorization: Bearer $token" https://<node_ip>:8443/api/v1/namespaces/default/pods` 

**The above should return 403 forbidden error as the role and rolebinding is not associated with service account**

### Create role and rolebinding for serviceaccount
 - `kubectl apply -f clusterrole.yaml`
 - `kubectl apply -f clusterrolebinding.yaml`

### Access k8s api and verfiy the permissions by using serviceaccount
 - `curl -k --header "Authorization: Bearer $token" https://<node_ip>:8443/api/v1/namespaces/default/pods` 

**now above will work without any error**

### Deploy pod using k8s api with serviceaccount token
 - `curl -k --header "Authorization: Bearer $token" -X POST -H "Content-Type: application/yaml" https://<node_ip>:8443/api/v1/namespaces/default/pods -d @pod.json`

### Try to delete pod using k8s api with serviceaccount token
 - `curl -k --header "Authorization: Bearer $token" -X DELETE https://<node_ip>:8443/api/v1/namespaces/default/pods/nginx-pod` 

**above will return 403 forbidden error as the role is not having permission to delete pod**