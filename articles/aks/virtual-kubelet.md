---
title: Uruchom kubelet wirtualnego w klastrze usługi Kubernetes Azure (AKS)
description: Użyj wirtualnych kubelet, aby uruchomić kontenery Kubernetes na wystąpień kontenera platformy Azure.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/12/2018
ms.author: nepeters
ms.openlocfilehash: 0d046970b40f5253b07005ab578035c7c179e7df
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304444"
---
# <a name="virtual-kubelet-with-aks"></a>Wirtualne Kubelet z AKS

Wystąpień kontenera platformy Azure (ACI) zapewniają hostowanej środowisko dla uruchomionych kontenerów na platformie Azure. Korzystając z ACI, nie musi zarządzać podstawowej infrastruktury obliczeń, Azure obsługuje ten zarządzania. Podczas uruchamiania kontenery w ACI, naliczane są opłaty przez drugą dla każdego uruchomionego kontenera.

W przypadku używania dostawcy Kubelet wirtualnych dla wystąpień kontenera platformy Azure, kontenerów zarówno systemu Linux i Windows można zaplanować na wystąpieniu kontenera tak, jakby była standardowe węzła Kubernetes. Ta konfiguracja pozwala wykorzystać możliwości Kubernetes i korzyści wartość i kosztów zarządzania wystąpień kontenera.

> [!NOTE]
> Kubelet wirtualnego jest projekt typu open source eksperymentalne i powinna być używana jako takie. Do ich współtworzenia, problemy z pliku i odczytu więcej informacji na temat wirtualnej kubelet, zobacz [projektu wirtualnego Kubelet GitHub][vk-github].

Konfigurowanie wirtualnego Kubelet dla wystąpień kontenera na AKS szczegóły tego dokumentu.

## <a name="prerequisite"></a>Wymagania wstępne

Tym dokumencie przyjęto założenie, że masz AKS klastra. Jeśli potrzebujesz klastrem AKS zobacz [Szybki Start Azure Kubernetes usługi (AKS)][aks-quick-start].

Należy również wersji interfejsu wiersza polecenia Azure **2.0.33** lub nowszym. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

[Helm](https://docs.helm.sh/using_helm/#installing-helm) jest również wymagane w celu zainstalowania Kubelet wirtualnego.

## <a name="installation"></a>Instalacja

Użyj [aks az install łącznik] [ aks-install-connector] polecenie, aby zainstalować wirtualne Kubelet. Poniższy przykład wdraża łącznika Linux i Windows.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

Są dostępne dla tych argumentów `aks install-connector` polecenia.

| Argument: | Opis | Wymagane |
|---|---|:---:|
| `--connector-name` | Nazwa łącznika ACI.| Yes |
| `--name` `-n` | Nazwa zarządzanego klastra. | Yes |
| `--resource-group` `-g` | Nazwa grupy zasobów. | Yes |
| `--os-type` | Typ systemu operacyjnego wystąpień kontenera. Dozwolone wartości: zarówno, Linux, systemu Windows. Domyślne: systemu Linux. | Nie |
| `--aci-resource-group` | Grupy zasobów, w którym można utworzyć grupy kontenerów ACI. | Nie |
| `--location` `-l` | Lokalizacji do utworzenia grupy kontenerów ACI. | Nie |
| `--service-principal` | Nazwy głównej usługi używany do uwierzytelniania do interfejsów API platformy Azure. | Nie |
| `--client-secret` | Klucz tajny skojarzony z nazwy głównej usługi. | Nie |
| `--chart-url` | Adres URL Helm wykresu, który instaluje ACI łącznika. | Nie |
| `--image-tag` | Tag obrazu wirtualnego kubelet obrazu kontenera. | Nie |

## <a name="validate-virtual-kubelet"></a>Sprawdź poprawność Kubelet wirtualnego

Aby sprawdzić, czy Kubelet wirtualnego został zainstalowany, należy powrócić do listy węzłów Kubernetes za pomocą [kubectl uzyskać węzłów] [ kubectl-get] polecenia.

```console
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Uruchom kontenera systemu Linux

Utwórz plik o nazwie `virtual-kubelet-linux.yaml` i skopiuj następujące yaml programu. Zastąp `kubernetes.io/hostname` wartości o nazwie węzła Kubelet wirtualnych systemu Linux. Zwróć uwagę, że [nodeSelector] [ node-selector] i [toleration] [ toleration] są używane do zaplanowania kontenera w węźle.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-linux
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

Uruchom aplikację klawiszem [utworzyć kubectl] [ kubectl-create] polecenia.

```azurecli-interactive
kubectl create -f virtual-kubelet-linux.yaml
```

Użyj [kubectl uzyskać stanowiskami] [ kubectl-get] z `-o wide` argument wyjściowy listę stanowiskami z węzłem zaplanowane. Zwróć uwagę, że `aci-helloworld` pod zostało zaplanowane na `virtual-kubelet-virtual-kubelet-linux` węzła.

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Uruchom kontenera systemu Windows

Utwórz plik o nazwie `virtual-kubelet-windows.yaml` i skopiuj następujące yaml programu. Zastąp `kubernetes.io/hostname` wartości o nazwie węzła Kubelet wirtualnych systemu Windows. Zwróć uwagę, że [nodeSelector] [ node-selector] i [toleration] [ toleration] są używane do zaplanowania kontenera w węźle.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: nanoserver/iis
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-win
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

Uruchom aplikację klawiszem [utworzyć kubectl] [ kubectl-create] polecenia.

```azurecli-interactive
kubectl create -f virtual-kubelet-windows.yaml
```

Użyj [kubectl uzyskać stanowiskami] [ kubectl-get] z `-o wide` argument wyjściowy listę stanowiskami z węzłem zaplanowane. Zwróć uwagę, że `nanoserver-iis` pod zostało zaplanowane na `virtual-kubelet-virtual-kubelet-win` węzła.

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Usuń wirtualne Kubelet

Użyj [aks az remove łącznik] [ aks-remove-connector] polecenie, aby usunąć wirtualny Kubelet. Zamień na nazwę łącznika, AKS klastra i grupę zasobów klastra AKS wartości argumentów.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o wirtualnej Kubelet w [wirtualnego Kubelet Github projet][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az_aks_list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
