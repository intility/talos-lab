# Talos Controlplane in Kubernetes

This repo contains guides and resources on how to run the Talos controlplane as pods in a Kubernetes cluster.

**Pre-reqs**

- kubectl
- talosctl

You must also have access to a Kubernetes/OpenShift cluster with cluster-admin rights.

**Step-by-step guide**

1. Apply the statefulset++ from this repo:

```shell
kubectl apply -f statefulset.yaml
```

2. Make sure your hostfile has an entry like this:

```
127.0.0.1        talos talos-0 talos-1 talos-2
```

3. Start portforwarding to port 50000 of one of the talos pods:

```
kubectl port-forward pod/talos-0 50000:50000 -n talos
```

4. Bootstrap talos:

```
talosctl -e talos-0 -n talos-0 --talosconfig talosconfig bootstrap
```

5. Generate a kubeconfig:

```
talosctl -e talos-0 -n talos-0 --talosconfig talosconfig kubeconfig ~/.kube/config.d/talos
```

6. Modify the endpoint in the kubeconfig to your environment (wip)

7. ???

8. profit

```
❯ kubectl --kubeconfig ~/.kube/config.d/talos get node
NAME      STATUS   ROLES           AGE     VERSION
talos-0   Ready    control-plane   6m39s   v1.33.0
talos-1   Ready    control-plane   6m29s   v1.33.0
talos-2   Ready    control-plane   6m59s   v1.33.0
```

**Adding a node**

1. Create a VM from talos iso (wip)
2. Portforward to port 50000 of the vm:

```
kubectl virt port-forward vm/talos-node-1 50000
```

3. Add node to cluster:

```
talosctl apply-config --insecure --nodes 127.0.0.1:50000 --file worker.yaml
```
