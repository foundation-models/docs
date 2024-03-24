# GPU pods on Microk8s Ubuntu 22.04

Install Nvidia driver 535 (tested on 22.04) and installed Cuda 12.2

![Untitled](GPU%20pods%20on%20Microk8s%20Ubuntu%2022%2004%207f37093358b54d6ba2dd0c9ffebbe442/Untitled.png)

Then install microk8s via

```
sudo snap install microk8s --classic --channel=1.22/stable
sudo snap alias microk8s.kubectl kubectl
microk8s start

microk8s enable gpu
microk8s enable registry
```

Verify gpu

```
microk8s kubectl logs -n gpu-operator-resources -lapp=nvidia-operator-validator -c nvidia-operator-validator
```

Then pull the image and push it to local registry

```jsx
docker pull k8s.gcr.io/cuda-vector-add:v0.1
docker tag k8s.gcr.io/cuda-vector-add:v0.1 localhost:32000/cuda-vector-add:v0.1
docker push localhost:32000/cuda-vector-add:v0.1
```

```
microk8s kubectl apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: cuda-vector-add
spec:
  restartPolicy: OnFailure
  containers:
    - name: cuda-vector-add
      image: "localhost:32000/cuda-vector-add:v0.1"
      resources:
        limits:
          nvidia.com/gpu: 1
EOF
```

![Untitled](GPU%20pods%20on%20Microk8s%20Ubuntu%2022%2004%207f37093358b54d6ba2dd0c9ffebbe442/Untitled%201.png)

![Untitled](GPU%20pods%20on%20Microk8s%20Ubuntu%2022%2004%207f37093358b54d6ba2dd0c9ffebbe442/Untitled%202.png)

To check it from remote you need to run 
`microk8s config`

and incorporate it in `~/.kube/config`

![Untitled](GPU%20pods%20on%20Microk8s%20Ubuntu%2022%2004%207f37093358b54d6ba2dd0c9ffebbe442/Untitled%203.png)

![Untitled](GPU%20pods%20on%20Microk8s%20Ubuntu%2022%2004%207f37093358b54d6ba2dd0c9ffebbe442/Untitled%204.png)

![Untitled](GPU%20pods%20on%20Microk8s%20Ubuntu%2022%2004%207f37093358b54d6ba2dd0c9ffebbe442/Untitled%205.png)