---
title: Użyj procesorach GPU znajdujących się w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak używać procesorów GPU na potrzeby obliczenia o wysokiej wydajności lub obciążeń intensywnie korzystających z grafiki na platformie Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: 150eaa6a4df558ed0c737d99cbcc8010baf63e96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466396"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Użycie procesorów GPU w przypadku obciążeń intensywnie korzystających z obliczeń w usłudze Azure Kubernetes Service (AKS)

Graficzny graficznych (GPU) są często używane dla obciążeń intensywnie korzystających z obliczeń, takich jak obciążenia grafiki i wizualizacji. AKS obsługuje tworzenie pul węzłów z włączonymi procesorami GPU do uruchamiania tych obciążeń intensywnie korzystających z obliczeń w usłudze Kubernetes. Aby uzyskać więcej informacji na temat maszyny wirtualne obsługujące procesor GPU dostępności, zobacz [rozmiarów maszyn wirtualnych na platformie Azure zoptymalizowane pod kątem procesora GPU][gpu-skus]. W przypadku węzłów AKS zalecamy minimalny rozmiar *maszyna wirtualna Standard_NC6*.

> [!NOTE]
> Maszyny wirtualne z włączonymi procesorami GPU zawierają wyspecjalizowanym sprzęcie, który podlega wyższą dostępność ceny i regionu. Aby uzyskać więcej informacji, zobacz [ceny] [ azure-pricing] narzędzia i [dostępność w poszczególnych regionach][azure-availability].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster AKS z węzłami, które obsługują procesorów GPU. Klastra usługi AKS musi działać Kubernetes, 1,10 lub nowszej. Jeśli potrzebujesz klastra AKS, która spełnia te wymagania, zobacz ten artykuł, aby w pierwszej sekcji [Tworzenie klastra AKS](#create-an-aks-cluster).

Możesz również muszą wiersza polecenia platformy Azure w wersji 2.0.59 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Jeśli potrzebujesz klastra AKS, który spełnia minimalne wymagania (węzeł z włączonymi procesorami GPU i Kubernetes wersji 1,10 lub nowszej), wykonaj następujące czynności. Jeśli masz już klaster AKS, która spełnia te wymagania [od razu przejść do następnej sekcji](#confirm-that-gpus-are-schedulable).

Najpierw utwórz grupę zasobów dla klastra przy użyciu [Tworzenie grupy az] [ az-group-create] polecenia. Poniższy przykład tworzy nazwę grupy zasobów *myResourceGroup* w *eastus* regionu:

```azurecli
az group create --name myResourceGroup --location eastus
```

Teraz Utwórz klaster usługi AKS przy użyciu [tworzenie az aks] [ az-aks-create] polecenia. Poniższy przykład tworzy klaster z jednym węzłem o rozmiarze `Standard_NC6`, i uruchamia Kubernetes wersji 1.11.7:

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1 \
    --kubernetes-version 1.11.8
```

Uzyskiwanie poświadczeń dla klastra usługi AKS przy użyciu [az aks get-credentials] [ az-aks-get-credentials] polecenia:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="confirm-that-gpus-are-schedulable"></a>Upewnij się, że procesorów GPU są ustalonych w harmonogramie

Przy użyciu klastra usługi AKS utworzone Potwierdź ustalonych w harmonogramie w usłudze Kubernetes procesorów GPU. Najpierw Wyświetl listę węzłów w klastrze za pomocą [kubectl get-węzły] [ kubectl-get] polecenia:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   6m    v1.11.7
```

Teraz za pomocą [kubectl opisują węzła] [ kubectl-describe] polecenie, aby potwierdzić, czy ustalonych w harmonogramie procesorów GPU. W obszarze *pojemności* sekcji procesora GPU pomysłem jest wystawienie jako `nvidia.com/gpu:  1`. Jeśli nie widzisz procesorów GPU, zobacz [dostępności Rozwiązywanie problemów z procesora GPU](#troubleshoot-gpu-availability) sekcji.

Poniższego, skróconego przykładu pokazuje, że procesora GPU jest dostępny w węźle, nazwane *aks-nodepool1-18821093-0*:

```
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 cpu:                6
 ephemeral-storage:  30428648Ki
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             57713780Ki
 nvidia.com/gpu:     1
 pods:               110
Allocatable:
 cpu:                5916m
 ephemeral-storage:  28043041951
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             52368500Ki
 nvidia.com/gpu:     1
 pods:               110
System Info:
 Machine ID:                 9148b74152374d049a68436ac59ee7c7
 System UUID:                D599728C-96F3-B941-BC79-E0B70453609C
 Boot ID:                    a2a6dbc3-6090-4f54-a2b7-7b4a209dffaf
 Kernel Version:             4.15.0-1037-azure
 OS Image:                   Ubuntu 16.04.5 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.11.7
 Kube-Proxy Version:         v1.11.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  gpu-resources              nvidia-device-plugin-97zfc               0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m4s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Uruchamianie obciążenia obsługujące procesor GPU

Aby wyświetlić procesora GPU w akcji, należy zaplanować obciążenia obsługujące procesor GPU z żądaniem odpowiedni zasób. W tym przykładzie uruchommy [Tensorflow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) zadania względem [zestawu danych mnist ręcznie ZAPISANYCH](http://yann.lecun.com/exdb/mnist/).

Utwórz plik o nazwie *przykłady tf-mnist ręcznie zapisanych demo.yaml* i wklej następujące manifest YAML. Następujące manifest zadania obejmuje limit zasobów `nvidia.com/gpu: 1`:

> [!NOTE]
> Jeśli zostanie wyświetlony błąd niezgodności wersji podczas wywoływania do sterowników, takie jak wersja sterownika CUDA jest niewystarczająca dla wersji środowiska uruchomieniowego CUDA, zapoznaj się z wykresu zgodności macierzy sterownika nVidia — [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
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

Użyj [zastosować kubectl] [ kubectl-apply] polecenie, aby uruchomić zadanie. To polecenie analizuje plik manifestu i tworzy zdefiniowane obiekty usługi Kubernetes:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Wyświetl stan i dane wyjściowe obciążenia obsługujące procesor GPU

Monitoruj postęp zadania za pomocą polecenia [kubectl get-zadania] [ kubectl-get] polecenia `--watch` argumentu. Może potrwać kilka minut do ściągnięcia pierwsze obraz i przetworzyć zestawu danych. Gdy *uzupełnienia* kolumna pokazuje *1/1*, zadanie zostało zakończone pomyślnie:

```
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Aby wyświetlić dane wyjściowe z włączonymi procesorami GPU obciążenia, należy najpierw uzyskać nazwę zasobników z [kubectl get pods-] [ kubectl-get] polecenia:

```
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-smnr6   0/1     Completed   0          3m
```

Teraz za pomocą [Dzienniki narzędzia kubectl] [ kubectl-logs] polecenie, aby wyświetlić dzienniki zasobników. Następujące przykładowe dzienniki pod upewnij się, że odpowiednie urządzenie GPU został odnaleziony, `Tesla K80`. Podaj nazwę własnego zasobnika:

```
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-02-28 23:47:34.749013: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-02-28 23:47:34.879877: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 3130:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2019-02-28 23:47:34.879915: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 3130:00:00.0, compute capability: 3.7)
2019-02-28 23:47:39.492532: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
Accuracy at step 40: 0.8685
Accuracy at step 50: 0.8864
Accuracy at step 60: 0.901
Accuracy at step 70: 0.905
Accuracy at step 80: 0.9103
Accuracy at step 90: 0.9126
Adding run metadata for 99
Accuracy at step 100: 0.9176
Accuracy at step 110: 0.9149
Accuracy at step 120: 0.9187
Accuracy at step 130: 0.9253
Accuracy at step 140: 0.9252
Accuracy at step 150: 0.9266
Accuracy at step 160: 0.9255
Accuracy at step 170: 0.9267
Accuracy at step 180: 0.9257
Accuracy at step 190: 0.9309
Adding run metadata for 199
Accuracy at step 200: 0.9272
Accuracy at step 210: 0.9321
Accuracy at step 220: 0.9343
Accuracy at step 230: 0.9388
Accuracy at step 240: 0.9408
Accuracy at step 250: 0.9394
Accuracy at step 260: 0.9412
Accuracy at step 270: 0.9422
Accuracy at step 280: 0.9436
Accuracy at step 290: 0.9411
Adding run metadata for 299
Accuracy at step 300: 0.9426
Accuracy at step 310: 0.9466
Accuracy at step 320: 0.9458
Accuracy at step 330: 0.9407
Accuracy at step 340: 0.9445
Accuracy at step 350: 0.9486
Accuracy at step 360: 0.9475
Accuracy at step 370: 0.948
Accuracy at step 380: 0.9516
Accuracy at step 390: 0.9534
Adding run metadata for 399
Accuracy at step 400: 0.9501
Accuracy at step 410: 0.9552
Accuracy at step 420: 0.9535
Accuracy at step 430: 0.9545
Accuracy at step 440: 0.9533
Accuracy at step 450: 0.9526
Accuracy at step 460: 0.9566
Accuracy at step 470: 0.9547
Accuracy at step 480: 0.9548
Accuracy at step 490: 0.9545
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć skojarzone obiekty usługi Kubernetes, utworzone w tym artykule, należy użyć [kubectl Usuń zadanie] [ kubectl delete] polecenia w następujący sposób:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="troubleshoot-gpu-availability"></a>Rozwiązywanie problemów z dostępności procesora GPU

Procesory GPU nie są widoczne jako dostępne w węzły, może być konieczne wdrażanie DaemonSet dla wtyczki urządzenia firmy nVidia. Ta DaemonSet uruchamia zasobnik w każdym węźle, aby zapewnić wymagane sterowniki dla procesorów GPU.

Najpierw należy utworzyć za pomocą nazw [kubectl tworzenie przestrzeni nazw] [ kubectl-create] polecenia, takie jak *zasoby procesora gpu*:

```console
kubectl create namespace gpu-resources
```

Utwórz plik o nazwie *nvidia — urządzenie wtyczka ds.yaml* i wklej następujące manifest YAML. Aktualizacja `image: nvidia/k8s-device-plugin:1.11` w połowie dół manifest być dopasowane do wersji platformy Kubernetes. Na przykład, jeśli klastra usługi AKS rozwiązania Kubernetes wersji 1.12 uruchomieniu zaktualizować tagu `image: nvidia/k8s-device-plugin:1.12`.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  labels:
    kubernetes.io/cluster-service: "true"
  name: nvidia-device-plugin
  namespace: gpu-resources
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
      - image: nvidia/k8s-device-plugin:1.11 # Update this tag to match your Kubernetes version
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

Teraz za pomocą [zastosować kubectl] [ kubectl-apply] polecenie, aby utworzyć element DaemonSet:

```
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

Uruchom [kubectl opisują węzła] [ kubectl-describe] polecenie ponownie, aby sprawdzić, czy procesor GPU jest teraz dostępna w węźle.

## <a name="next-steps"></a>Kolejne kroki

Aby uruchamiać zadania Apache Spark, zobacz [zadania uruchamiania Apache Spark w usłudze AKS][aks-spark].

Aby uzyskać więcej informacji na temat obciążeń machine learning (uczenie Maszynowe) na platformie Kubernetes, zobacz [Kubeflow Labs][kubeflow-labs].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
