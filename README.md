# GPU Operator without preinstalled driver in Host (the Case #3 below)

You might use the Vagrantfile attached. It will deploy the following Virtual Machines.
- One master node (No GPU machine)
- Eight worker nodes (GPU machine and CPU machine)
- If you wanna use specific version of kubernetes, you may use the followings instead of the line in each Vagrantfile.
```
sudo apt-get install -y -q kubelet=1.23.6-00 kubectl=1.23.6-00 kubeadm=1.23.6-00
```

|  | CPU | Memory | GPU | GPU Driver | Vagrantfile |
| --- | --- | --- | --- | --- | --- |
| Master | 2 | 8,192 MB | no | --- | [Vagrantfile](https://github.com/developer-onizuka/gpu-operator4/tree/master/precision3620/Vagrantfile) |
| Worker1 | 2 | 4,096 MB | no | --- | [Vagrantfile](https://github.com/developer-onizuka/gpu-operator4/tree/master/optiplex3050/Vagrantfile) |
| Worker2 | 2 | 4,096 MB | no | --- | [Vagrantfile](https://github.com/developer-onizuka/gpu-operator4/tree/master/optiplex3050/Vagrantfile) |
| Worker3 | 2 | 4,096 MB | no | --- | [Vagrantfile](https://github.com/developer-onizuka/gpu-operator4/tree/master/optiplex3050/Vagrantfile) |
| Worker4 | 2 | 16,384 MB | Quadro P1000 | DaemonSet | [Vagrantfile](https://github.com/developer-onizuka/gpu-operator4/tree/master/optiplex3050/Vagrantfile) |
| Worker5 | 2 | 4,096 MB | no | --- | [Vagrantfile](https://github.com/developer-onizuka/gpu-operator4/tree/master/optiplex5050/Vagrantfile) |
| Worker6 | 2 | 4,096 MB | no | --- | [Vagrantfile](https://github.com/developer-onizuka/gpu-operator4/tree/master/optiplex5050/Vagrantfile) |
| Worker7 | 2 | 4,096 MB | no | --- | [Vagrantfile](https://github.com/developer-onizuka/gpu-operator4/tree/master/optiplex5050/Vagrantfile) |
| Worker8 | 2 | 16,384 MB | Quadro P400 | DaemonSet | [Vagrantfile](https://github.com/developer-onizuka/gpu-operator4/tree/master/optiplex5050/Vagrantfile) |

https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/getting-started.html#chart-customization-options

| # | Scenario | Nvidia Driver | Nvidia Toolkit |
| --- | --- | --- | --- |
| #1 | No GPU operator | In Host | In Host |
| #2 | GPU Operator (w/ driver.enabled=false) | In Host | DaemonSet |
| #3 | GPU Operator (default) | DaemonSet | DaemonSet |
| #4 | GPU Operator (w/ toolkit.enabled=false) | DaemonSet | In Host |


# 1. Install Helm chart at Master node
```
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 \
&& chmod 700 get_helm.sh \
&& ./get_helm.sh

$ helm repo add nvidia https://nvidia.github.io/gpu-operator \
&& helm repo update

$ helm install --wait --generate-name \
nvidia/gpu-operator
```

# 2. Check if gpu-operator properly finished deploying DaemonSet
```
kubectl get pods -o wide
NAME                                                              READY   STATUS     RESTARTS         AGE     IP              NODE      NOMINATED NODE   READINESS GATES
gpu-feature-discovery-4r5ls                                       2/2     Running    10 (5h21m ago)   6h39m   10.10.45.249    worker8   <none>           <none>
gpu-operator-1646200929-node-feature-discovery-master-6f7cxcxfh   2/2     Running    1 (6h59m ago)    6h59m   10.10.219.92    master    <none>           <none>
gpu-operator-1646200929-node-feature-discovery-worker-cfttq       2/2     Running    2 (6h59m ago)    6h59m   10.10.199.133   worker4   <none>           <none>
gpu-operator-1646200929-node-feature-discovery-worker-d6p2b       2/2     Running    2 (6h59m ago)    6h59m   10.10.189.67    worker2   <none>           <none>
gpu-operator-1646200929-node-feature-discovery-worker-gn648       2/2     Running    6 (4h50m ago)    6h59m   10.10.45.250    worker8   <none>           <none>
gpu-operator-1646200929-node-feature-discovery-worker-jmp6c       2/2     Running    0                6h59m   10.10.219.94    master    <none>           <none>
gpu-operator-1646200929-node-feature-discovery-worker-kl7mp       2/2     Running    5 (4h48m ago)    6h59m   10.10.42.70     worker5   <none>           <none>
gpu-operator-1646200929-node-feature-discovery-worker-p55ns       2/2     Running    5 (4h49m ago)    6h59m   10.10.168.195   worker7   <none>           <none>
gpu-operator-1646200929-node-feature-discovery-worker-sj5jm       2/2     Running    2 (6h59m ago)    6h59m   10.10.235.133   worker1   <none>           <none>
gpu-operator-1646200929-node-feature-discovery-worker-t5bt6       2/2     Running    4 (4h50m ago)    6h59m   10.10.35.3      worker6   <none>           <none>
gpu-operator-1646200929-node-feature-discovery-worker-w9gbb       2/2     Running    1 (6h59m ago)    6h59m   10.10.182.2     worker3   <none>           <none>
gpu-operator-7ff85f9c4f-ktzfk                                     2/2     Running    1 (6h59m ago)    6h59m   10.10.219.93    master    <none>           <none>
nvidia-container-toolkit-daemonset-qtrpk                          2/2     Running    4 (4h50m ago)    6h39m   10.10.45.246    worker8   <none>           <none>
nvidia-cuda-validator-8l6zq                                       1/2     NotReady   0                86s     10.10.45.238    worker8   <none>           <none>
nvidia-dcgm-exporter-vwkl9                                        2/2     Running    10 (5h21m ago)   6h39m   10.10.45.247    worker8   <none>           <none>
nvidia-device-plugin-daemonset-5jntg                              2/2     Running    10 (5h21m ago)   6h39m   10.10.45.248    worker8   <none>           <none>
nvidia-driver-daemonset-sfhk8                                     2/2     Running    6 (5h21m ago)    6h58m   10.10.45.245    worker8   <none>           <none>
nvidia-operator-validator-4rvfq                                   0/2     Init:2/5   46 (2m48s ago)   6h39m   10.10.45.244    worker8   <none>           <none>
```

# 3. Face-Recognizer
Before this step, make the container and store it in private registry. See the URL below:
- https://github.com/developer-onizuka/nvidia-docker_VirtualMachine3
- https://github.com/developer-onizuka/private_dockerRegistry
```
$ curl 192.168.1.5:5000/v2/_catalog
{"repositories":["face_recognizer","ubuntu"]}

$ curl http://192.168.1.5:5000/v2/face_recognizer/tags/list
{"name":"face_recognizer","tags":["1.0.1"]}

$ git clone https://github.com/developer-onizuka/gpu-operator4.git
$ cd gpu-operator4
$ kubectl apply -f face_recognizer.yaml
```	
	
# 4. Uninstall GPU operator
```
$ helm delete $(helm ls -n default | awk '/gpu-operator/{print $1}') -n default
```


