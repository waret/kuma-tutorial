



```
$ tar xvzf [FILE]

$ cd bin && ls
envoy   kuma-cp   kuma-dp   kuma-tcp-echo kumactl

$ kumactl install control-plane | kubectl apply -f -

$ kubectl apply -f https://raw.githubusercontent.com/Kong/kuma/master/examples/kubernetes/sample-service.yaml

$ echo "apiVersion: kuma.io/v1alpha1
kind: Mesh
metadata:
  namespace: kuma-system
  name: default
spec:
  mtls:
    enabled: true
    ca:
      builtin: {}" | kubectl apply -f -

$ kubectl get svc -n kuma-system
NAME                 TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                                                                      AGE
kuma-control-plane   LoadBalancer   10.233.23.91   10.20.1.103   5677:32226/TCP,5678:31449/TCP,5679:30661/TCP,5681:31627/TCP,5682:32407/TCP   47h
kuma-injector        LoadBalancer   10.233.28.64   10.20.1.102   443:30158/TCP                                                                47h

$ kumactl config control-planes add --name=XYZ --address=http://10.20.1.103:5681

$ kumactl get meshes -o yaml
items:
- mtls:
    ca:
      builtin: {}
  name: default
  type: Mesh

$ kumactl config view
contexts:
- controlPlane: local
  name: local
- controlPlane: XYZ
  name: XYZ
controlPlanes:
- coordinates:
    apiServer:
      url: http://localhost:5681
  name: local
- coordinates:
    apiServer:
      url: http://10.20.1.103:5681
  name: XYZ
currentContext: XYZ

$ kumactl get meshes
NAME
default

$ kumactl get dataplanes
MESH      NAME        TAGS
default   dp-echo-1   service=echo

$ kumactl inspect dataplanes
MESH      NAME        TAGS              STATUS   LAST CONNECTED AGO   LAST UPDATED AGO   TOTAL UPDATES   TOTAL ERRORS
default   dp-echo-1   service=echo      Online   19s                  18s                2               0
```

