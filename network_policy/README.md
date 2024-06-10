**We will use kind cluster for the networkpolicy demonstrate**

### create kind cluster
- `kind create cluster`

**Use will use calico plugin to apply networking rules**
### Deploy calico
- kubectl apply -f calico.yaml

### Deploy app1 and app2
 - `kubectl apply -f app1.yaml`
 - `kubectl apply -f app2.yaml`

### Deploy networkpolicy
 - `kubectl apply -f networkpolicy.yaml`

### Verfiy the networkpolicy working as expected or not.
**login to app1 pod**
 - `kubectl exec -it <app1-pod-name> -- bash`
 - `curl app2:80 ### should work`

**login to app2 pod**
 - `kubectl exec -it <app2-pod-name> -- bash`
 - `curl app2:80 ### should not work, this is because we have set networkpolicy such that app2 service is only accessible from app1`

### Get kind cluster IP and verfiy the direct access to service
 - `docker inspect <kind-container-id> | grep IP`
 - `curl http://<kind_cluster_ip>:30180 ### should work`
 - `curl http://<kind_cluster_ip>:30280 ### should not work i.e. direct access to app2 will not work as defined in networkpolicy.`

### Networkpolicy to access pod using external ip
**deploy networkpolicy, before deploy update the local machine ip into networkpolicy-certain-ip.yaml**
 - `kubectl apply -f networkpolicy-certain-ip.yaml`

### Verfiy it
 - `curl http://<base_machine_ip>:30180 ### should work if try to access from allowed ip`