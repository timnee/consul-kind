Kind implementation of example from [Deploy Single Consul Datacenter Across Multiple Kubernetes Clusters](https://developer.hashicorp.com/consul/docs/k8s/deployment-configurations/single-dc-multi-k8s)

# Rereqs

To run this exmaple, you need the following installed:
* kind
* helm
* ansible


# How to run

Run in NodePort mode as decribed in the doc above

```
ansible-playbook startup.yaml --tags np
```

Alternatively you can run in LoadBalancer mode.  Note the limitation of kind [loadbalancer](https://kind.sigs.k8s.io/docs/user/loadbalancer/) if you running on MacOS or Windows.

```
ansible-playbook startup.yaml --tags lb
```

To access the consule ui, port-foward service server-consul-ui to https://localhost:8443

```
kubectl port-forward svc/server-consul-ui 8443:443 -n consul --context kind-consul-server
```

# Cleanup

```
ansible-playbook cleanup.yaml
```

# Known issue

Unable to verify Consul Service Mesh works with either NodePort or LoadBalancer mode.  Getting Connection reset by peer

```
$ kubectl --context kind-consul-client exec deploy/static-client -- curl -v localhost:1234
Defaulted container "static-client" out of: static-client, consul-dataplane, consul-connect-inject-init (init)
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0*   Trying 127.0.0.1:1234...
* Connected to localhost (127.0.0.1) port 1234 (#0)
> GET / HTTP/1.1
> Host: localhost:1234
> User-Agent: curl/8.0.1-DEV
> Accept: */*
>
  0     0    0     0    0     0      0      0 --:--:--  0:00:04 --:--:--     0* Recv failure: Connection reset by peer
  0     0    0     0    0     0      0      0 --:--:--  0:00:05 --:--:--     0
* Closing connection 0
curl: (56) Recv failure: Connection reset by peer
command terminated with exit code 56
```