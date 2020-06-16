# kube-netc: A Kubernetes eBPF network monitor

[![Build Status](https://travis-ci.org/nirmata/kube-netc.svg?branch=master)](https://travis-ci.org/nirmata/kube-netc) [![Go Report Card](https://goreportcard.com/badge/github.com/nirmata/kube-netc)](https://goreportcard.com/report/github.com/nirmata/kube-netc)


kube-netc (pronounced <i>kube-net-see</i>) is a Kubernetes network monitor built using eBPF

## Getting Started

To test the current capabilities of **kube-netc**, this guide will walk you through viewing the network statistics of your nodes.

### Install kube-netc

First, install the daemon set using the install.yaml:

``` 
kubectl apply -f https://github.com/nirmata/kube-netc/raw/master/config/install.yaml
```

### View results

This will start the **kube-netc** DaemonSet on your cluster and setup the required roles. Then, we get the name of the kube-netc pod:

```
kubectl get pods | grep kube-netc
```

For example, my **kube-netc** pod is:

```
kube-netc-j56cx
```
In a new terminal, we port-forward the port of our pod so we can access it with *curl* outside the cluster with:

```
kubectl port-forward kube-netc-j56cx 2112:2112
```

2112 is the port we are going to access the Prometheus endpoint on. We can then curl the /metrics endpoint using curl on local host to show the Prometheus metrics:

```
curl localhost:2112/metrics | grep bytes
```

This is an example output of the query showing the total bytes received by this node from each given connection:

```
...
bytes_recv{destination_address="172.17.0.2:35145",destination_pod_name="kube-controller-manager-minikube",source_address="172.17.0.2:8443",source_pod_name="kube-controller-manager-minikube"} 102
bytes_recv{destination_address="172.17.0.2:35145",destination_pod_name="kube-scheduler-minikube",source_address="172.17.0.2:8443",source_pod_name="kube-scheduler-minikube"} 102
bytes_recv{destination_address="172.17.0.2:35145",destination_pod_name="storage-provisioner",source_address="172.17.0.2:8443",source_pod_name="storage-provisioner"} 102
bytes_recv{destination_address="172.17.0.2:46834",destination_pod_name="etcd-minikube",source_address="172.17.0.2:10250",source_pod_name="etcd-minikube"} 1561
bytes_recv{destination_address="172.17.0.2:46834",destination_pod_name="kube-controller-manager-minikube",source_address="172.17.0.2:10250",source_pod_name="kube-controller-manager-minikube"} 1561
bytes_recv{destination_address="172.17.0.2:46834",destination_pod_name="kube-scheduler-minikube",source_address="172.17.0.2:10250",source_pod_name="kube-scheduler-minikube"} 1585
bytes_recv{destination_address="172.17.0.2:46834",destination_pod_name="storage-provisioner",source_address="172.17.0.2:10250",source_pod_name="storage-provisioner"} 1561
bytes_recv{destination_address="172.17.0.2:47146",destination_pod_name="etcd-minikube",source_address="172.17.0.2:10250",source_pod_name="etcd-minikube"} 671
bytes_recv{destination_address="172.17.0.2:47146",destination_pod_name="kube-apiserver-minikube",source_address="172.17.0.2:10250",source_pod_name="kube-apiserver-minikube"} 671
bytes_recv{destination_address="172.17.0.2:47146",destination_pod_name="kube-controller-manager-minikube",source_address="172.17.0.2:10250",source_pod_name="kube-controller-manager-minikube"} 671
bytes_recv{destination_address="172.17.0.2:47146",destination_pod_name="kube-proxy-b8hqq",source_address="172.17.0.2:10250",source_pod_name="kube-proxy-b8hqq"} 2428
bytes_recv{destination_address="172.17.0.2:47146",destination_pod_name="kube-scheduler-minikube",source_address="172.17.0.2:10250",source_pod_name="kube-scheduler-minikube"} 671
bytes_recv{destination_address="172.17.0.2:47146",destination_pod_name="storage-provisioner",source_address="172.17.0.2:10250",source_pod_name="storage-provisioner"} 671
bytes_recv{destination_address="172.17.0.2:48358",destination_pod_name="kube-scheduler-minikube",source_address="172.17.0.2:8443",source_pod_name="kube-scheduler-minikube"} 572
bytes_recv{destination_address="172.17.0.2:48414",destination_pod_name="kube-controller-manager-minikube",source_address="172.17.0.2:8443",source_pod_name="kube-controller-manager-minikube"} 572
bytes_recv{destination_address="172.17.0.2:48474",destination_pod_name="etcd-minikube",source_address="172.17.0.2:8443",source_pod_name="etcd-minikube"} 572
...
```

As we see the bytes received by each connection is shown and the source IP is given. If there is a known pod with the same IP the *pod_name* is also given.
