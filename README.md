# Server

Ref: [Deploy Single Consul Datacenter Across Multiple Kubernetes Clusters](https://developer.hashicorp.com/consul/docs/k8s/deployment-configurations/single-dc-multi-k8s)

1. `kubectl create secret generic consul-gossip-encryption-key --from-literal=key=$(consul keygen)`
2. `helm install server --values cluster1-values.yaml hashicorp/consul`
3. `kubectl get secret server-consul-ca-cert server-consul-bootstrap-acl-token --output yaml > cluster1-credentials.yaml`

Get LB ip
`kubectl get svc server-consul-expose-servers -o jsonpath='{.status.loadBalancer.ingress[*].ip}'`

# Consul1

1. switch context
2. update `externalServers.hosts` to LB ip in cluster2-vaules.yaml
3. `kubectl apply --filename cluster1-credentials.yaml`
4. `helm install consul --values cluster2-values.yaml hashicorp/consul`

# Validate Service Mesh

1. switch context to server and apply static-server.yaml
2. switch context to consul1 and apply static-client.yaml
