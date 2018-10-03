---
title: Procesorach GPU znajdujących się w usłudze Azure Kubernetes Service (AKS)
description: Użyj procesorach GPU znajdujących się w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/05/2018
ms.author: laevenso
ms.custom: mvc
ms.openlocfilehash: 231d7b875a7163aaa532be4a6477ca4e2eb67286
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043630"
---
# <a name="using-gpus-on-aks"></a>Przy użyciu procesorów GPU w usłudze AKS

Usługa AKS obsługuje tworzenie pul węzłów z obsługą procesorów GPU. Obecnie platforma Azure udostępnia maszyny wirtualne obsługujące jeden lub wielu procesorów GPU. Maszyny wirtualne obsługujące procesory GPU są zaprojektowane do przetwarzania obciążeń z dużą ilością obliczeń, dużą ilością grafiki i wizualizacji. Lista GPU maszyn wirtualnych z włączoną można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu).

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Procesory GPU są zwykle wymagane dla obciążeń intensywnie korzystających z obliczeń, takich jak bogatych w elementy graficzne i obciążeń wizualizacji. Zobacz następujące [dokumentu](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu) ustalenie właściwy rozmiar maszyny Wirtualnej dla obciążenia.
Zalecamy minimalny rozmiar `Standard_NC6` dla węzłów usługi Azure Kubernetes Service (AKS).

> [!NOTE]
> Maszyny wirtualne obsługujące procesor GPU zawierają wyspecjalizowanym sprzęcie, który podlega wyższą dostępność ceny i regionu. Aby uzyskać więcej informacji, zobacz [ceny](https://azure.microsoft.com/pricing/) narzędzia i [dostępność w poszczególnych regionach](https://azure.microsoft.com/global-infrastructure/services/) witryny, aby uzyskać więcej informacji.


Jeśli potrzebujesz klastra AKS, który spełnia minimalne zalecenie, uruchom następujące polecenia.

Utwórz grupę zasobów klastra.

```azurecli
az group create --name myGPUCluster --location eastus
```

Tworzenie klastra AKS z węzłów, które są o rozmiarze `Standard_NC6`.

```azurecli
az aks create --resource-group myGPUCluster --name myGPUCluster --node-vm-size Standard_NC6
```

Łączenie z klastrem usługi AKS.

```azurecli
az aks get-credentials --resource-group myGPUCluster --name myGPUCluster
```

## <a name="confirm-gpus-are-schedulable"></a>Upewnij się, że procesorów GPU ustalonych w harmonogramie

Uruchom następujące polecenia, aby potwierdzić, że GPU ustalonych w harmonogramie za pomocą platformy Kubernetes.

Pobieranie bieżącej listy węzłów.

```
$ kubectl get nodes
NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-22139053-0   Ready     agent     10h       v1.9.6
aks-nodepool1-22139053-1   Ready     agent     10h       v1.9.6
aks-nodepool1-22139053-2   Ready     agent     10h       v1.9.6
```

Opisz, jednym z węzłów, aby potwierdzić, że ustalonych w harmonogramie procesorów GPU. Ten znajduje się w obszarze `Capacity` sekcji. Na przykład `nvidia.com/gpu:  1`. Jeśli nie widzisz procesorów GPU, zapoznaj się z **rozwiązywanie** poniższej sekcji.

```
$ kubectl describe node aks-nodepool1-22139053-0
Name:               aks-nodepool1-22139053-0
Roles:              agent
Labels:             agentpool=nodepool1
                    beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/instance-type=Standard_NC6
                    beta.kubernetes.io/os=linux
                    failure-domain.beta.kubernetes.io/region=eastus
                    failure-domain.beta.kubernetes.io/zone=1
                    kubernetes.azure.com/cluster=MC_myGPUCluster_myGPUCluster
                    kubernetes.io/hostname=aks-nodepool1-22139053-0
                    kubernetes.io/role=agent
                    storageprofile=managed
                    storagetier=Standard_LRS
Annotations:        node.alpha.kubernetes.io/ttl=0
                    volumes.kubernetes.io/controller-managed-attach-detach=true
Taints:             <none>
CreationTimestamp:  Thu, 05 Apr 2018 12:13:20 -0700
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Thu, 05 Apr 2018 12:15:07 -0700   Thu, 05 Apr 2018 12:15:07 -0700   RouteCreated                 RouteController created a route
  OutOfDisk            False   Thu, 05 Apr 2018 22:14:33 -0700   Thu, 05 Apr 2018 12:13:20 -0700   KubeletHasSufficientDisk     kubelet has sufficient disk space available
  MemoryPressure       False   Thu, 05 Apr 2018 22:14:33 -0700   Thu, 05 Apr 2018 12:13:20 -0700   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Thu, 05 Apr 2018 22:14:33 -0700   Thu, 05 Apr 2018 12:13:20 -0700   KubeletHasNoDiskPressure     kubelet has no disk pressure
  Ready                True    Thu, 05 Apr 2018 22:14:33 -0700   Thu, 05 Apr 2018 12:15:10 -0700   KubeletReady                 kubelet is posting ready status. AppArmor enabled
Addresses:
  InternalIP:  10.240.0.4
  Hostname:    aks-nodepool1-22139053-0
Capacity:
 nvidia.com/gpu:                  1
 cpu:                             6
 memory:                          57691688Ki
 pods:                            110
Allocatable:
 nvidia.com/gpu:                  1
 cpu:                             6
 memory:                          57589288Ki
 pods:                            110
System Info:
 Machine ID:                 2eb0e90bd1fe450ba3cf83479443a511
 System UUID:                CFB485B6-CB49-A545-A2C9-8E4C592C3273
 Boot ID:                    fea24544-596d-4246-b8c3-610fc7ac7280
 Kernel Version:             4.13.0-1011-azure
 OS Image:                   Debian GNU/Linux 9 (stretch)
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.9.6
 Kube-Proxy Version:         v1.9.6
PodCIDR:                     10.244.1.0/24
ExternalID:                  /subscriptions/8ecadfc9-d1a3-4ea4-b844-0d9f87e4d7c8/resourceGroups/MC_myGPUCluster_myGPUCluster/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-22139053-0
Non-terminated Pods:         (2 in total)
  Namespace                  Name                       CPU Requests  CPU Limits  Memory Requests  Memory Limits
  ---------                  ----                       ------------  ----------  ---------------  -------------
  kube-system                kube-proxy-pwffr           100m (1%)     0 (0%)      0 (0%)           0 (0%)
  kube-system                kube-svc-redirect-mkpf4    0 (0%)        0 (0%)      0 (0%)           0 (0%)
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  CPU Requests  CPU Limits  Memory Requests  Memory Limits
  ------------  ----------  ---------------  -------------
  100m (1%)     0 (0%)      0 (0%)           0 (0%)
Events:         <none>
```

## <a name="run-a-gpu-enabled-workload"></a>Uruchamianie obciążenia procesora GPU

Aby zademonstrować, że w rzeczywistości działają procesorów GPU, obciążenie z żądaniem odpowiedni zasób włączyć harmonogramu procesora GPU. W tym przykładzie zostanie uruchomiony [Tensorflow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) zadania względem [zestawu danych mnist ręcznie ZAPISANYCH](http://yann.lecun.com/exdb/mnist/).

Następujące manifest zadania obejmuje limit zasobów `nvidia.com/gpu: 1`. 

Skopiuj manifest i Zapisz jako **przykłady tf-mnist ręcznie zapisanych demo.yaml**.
```
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: microsoft/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Użyj [zastosować kubectl] [ kubectl-apply] polecenie, aby uruchomić zadanie. To polecenie analizuje plik manifestu i tworzy zdefiniowane obiekty usługi Kubernetes.
```
$ kubectl apply -f samples-tf-mnist-demo.yaml
job "samples-tf-mnist-demo" created
```

Monitoruj postęp zadania do czasu pomyślnego zakończenia przy użyciu [kubectl get-zadania] [ kubectl-get] polecenia `--watch` argumentu.
```
$ kubectl get jobs samples-tf-mnist-demo --watch
NAME                    DESIRED   SUCCESSFUL   AGE
samples-tf-mnist-demo   1         0            8s
samples-tf-mnist-demo   1         1            35s
```

Określ nazwę zasobników, aby wyświetlić dzienniki, przedstawiający ukończone zasobników.
```
$ kubectl get pods --selector app=samples-tf-mnist-demo --show-all
NAME                          READY     STATUS      RESTARTS   AGE
samples-tf-mnist-demo-smnr6   0/1       Completed   0          4m
```

Przy użyciu nazwy zasobnika z danych wyjściowych polecenia powyżej, można znaleźć w dziennikach zasobników, aby upewnić się, że odpowiednie urządzenie GPU został odnaleziony w tym przypadku `Tesla K80`.
```
$ kubectl logs samples-tf-mnist-demo-smnr6
2018-04-13 04:11:08.710863: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-04-13 04:11:15.824349: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 04e1:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2018-04-13 04:11:15.824394: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 04e1:00:00.0, compute capability: 3.7)
2018-04-13 04:11:20.891910: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.0487
Accuracy at step 10: 0.6571
Accuracy at step 20: 0.8111
Accuracy at step 30: 0.8562
Accuracy at step 40: 0.8786
Accuracy at step 50: 0.8911
Accuracy at step 60: 0.8986
Accuracy at step 70: 0.9017
Accuracy at step 80: 0.9049
Accuracy at step 90: 0.9114
Adding run metadata for 99
Accuracy at step 100: 0.9109
Accuracy at step 110: 0.9143
Accuracy at step 120: 0.9188
Accuracy at step 130: 0.9194
Accuracy at step 140: 0.9237
Accuracy at step 150: 0.9231
Accuracy at step 160: 0.9158
Accuracy at step 170: 0.9259
Accuracy at step 180: 0.9303
Accuracy at step 190: 0.9315
Adding run metadata for 199
Accuracy at step 200: 0.9334
Accuracy at step 210: 0.9342
Accuracy at step 220: 0.9359
Accuracy at step 230: 0.9353
Accuracy at step 240: 0.933
Accuracy at step 250: 0.9353
Accuracy at step 260: 0.9408
Accuracy at step 270: 0.9396
Accuracy at step 280: 0.9406
Accuracy at step 290: 0.9444
Adding run metadata for 299
Accuracy at step 300: 0.9453
Accuracy at step 310: 0.946
Accuracy at step 320: 0.9464
Accuracy at step 330: 0.9472
Accuracy at step 340: 0.9516
Accuracy at step 350: 0.9473
Accuracy at step 360: 0.9502
Accuracy at step 370: 0.9483
Accuracy at step 380: 0.9481
Accuracy at step 390: 0.9467
Adding run metadata for 399
Accuracy at step 400: 0.9477
Accuracy at step 410: 0.948
Accuracy at step 420: 0.9496
Accuracy at step 430: 0.9501
Accuracy at step 440: 0.9534
Accuracy at step 450: 0.9551
Accuracy at step 460: 0.9518
Accuracy at step 470: 0.9562
Accuracy at step 480: 0.9583
Accuracy at step 490: 0.9575
Adding run metadata for 499
```

## <a name="cleanup"></a>Czyszczenie
Usuń skojarzone obiekty usługi Kubernetes, utworzone w tym kroku.
```
$ kubectl delete jobs samples-tf-mnist-demo
job "samples-tf-mnist-demo" deleted
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W niektórych scenariuszach może nie być wyświetlana zasobów procesora GPU w ramach pojemności. Na przykład: Uaktualnianie klastra do wersji 1.10 Kubernetes lub tworzenie nowych wersji 1.10 klastra Kubernetes, oczekiwany po `nvidia.com/gpu` brakuje zasobu `Capacity` podczas uruchamiania `kubectl describe node <node-name>`. 

Aby rozwiązać ten problem, zastosuj następujące daemonset po aprowizacji lub uaktualniania, a następnie zostanie wyświetlony `nvidia.com/gpu` jako zasób ustalonych w harmonogramie. 

Skopiuj manifest i Zapisz jako **nvidia — urządzenie wtyczka ds.yaml**. Dla tagu obrazu `image: nvidia/k8s-device-plugin:1.10` poniżej, zaktualizuj znacznik być dopasowane do wersji platformy Kubernetes. Na przykład użyć tagu `1.11` dla wersji 1.11 rozwiązania Kubernetes.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  labels:
    kubernetes.io/cluster-service: "true"
  name: nvidia-device-plugin
  namespace: kube-system
spec:
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      containers:
      - image: nvidia/k8s-device-plugin:1.10 # Update this tag to match your Kubernetes version
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
      nodeSelector:
        beta.kubernetes.io/os: linux
        accelerator: nvidia
```

Użyj [zastosować kubectl] [ kubectl-apply] polecenie, aby utworzyć element daemonset.

```
$ kubectl apply -f nvidia-device-plugin-ds.yaml
daemonset "nvidia-device-plugin" created
```

## <a name="next-steps"></a>Kolejne kroki

Są Państwo zainteresowani uruchamiania obciążeń usługi Machine Learning na platformie Kubernetes? Zapoznaj się z laboratoriów Kubeflow, aby uzyskać więcej szczegółów.

> [!div class="nextstepaction"]
> [Laboratoria Kubeflow][kubeflow-labs]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
