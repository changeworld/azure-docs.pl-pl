---
title: Uruchamianie Kubelet wirtualnych w klastrze usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak używać wirtualnej usługi Kubelet z usługą Azure Kubernetes Service (AKS) do uruchamiania kontenerów systemów Linux i Windows w systemie Azure Container Instances.
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: f18992be353d2d6cc739412d98ccd97d5e78d4c7
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "67613854"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Korzystanie z wirtualnego Kubelet z usługą Azure Kubernetes Service (AKS)

Azure Container Instances (ACI) zapewniają hostowane środowisko do uruchamiania kontenerów na platformie Azure. W przypadku korzystania z ACI nie ma potrzeby zarządzania podstawową infrastrukturą obliczeniową. platforma Azure obsługuje to zarządzanie za Ciebie. W przypadku korzystania z kontenerów w programie ACI opłata jest naliczana na sekundę dla każdego uruchomionego kontenera.

W przypadku korzystania z dostawcy wirtualnego Kubelet dla Azure Container Instances, można zaplanować zarówno kontenery systemu Linux, jak i Windows w przypadku wystąpienia kontenera, tak jakby był to standardowy węzeł Kubernetes. Ta konfiguracja pozwala korzystać z zalet Kubernetes oraz wartości zarządzania i kosztów usługi Container Instances.

> [!NOTE]
> AKS teraz ma wbudowaną obsługę planowania kontenerów w usłudze ACI, nazywanej *węzłami wirtualnymi*. Te węzły wirtualne obecnie obsługują wystąpienia kontenera systemu Linux. Jeśli musisz zaplanować wystąpienia kontenera systemu Windows, możesz kontynuować korzystanie z wirtualnego Kubelet. W przeciwnym razie należy użyć węzłów wirtualnych zamiast ręcznie instrukcji wirtualnego Kubelet zanotowanych w tym artykule. Możesz rozpocząć pracę z węzłami wirtualnymi za pomocą [interfejsu wiersza polecenia platformy Azure][virtual-nodes-cli] lub [Azure Portal][virtual-nodes-portal].
>
> Virtual Kubelet to eksperymentalny projekt typu "open source", który powinien być używany jako taki. Aby współtworzyć, rozwiązywać problemy z plikami i dowiedzieć się więcej na temat usługi Virtual kubelet, zobacz [Virtual Kubelet GitHub Project][vk-github].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym dokumencie przyjęto założenie, że masz klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z [przewodnikiem Szybki Start dotyczącym usługi Azure Kubernetes Service (AKS)][aks-quick-start].

Wymagany jest również interfejs wiersza polecenia platformy Azure w wersji **2.0.65** lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Aby zainstalować wirtualną Kubelet, zainstaluj i skonfiguruj [Helm][aks-helm] w klastrze AKS. Upewnij się, że do korzystania z usługi Kubernetes RBAC, w razie potrzeby, został [skonfigurowany do użycia w programie](#for-rbac-enabled-clusters).

### <a name="register-container-instances-feature-provider"></a>Zarejestruj dostawcę funkcji Container Instances

Jeśli nie korzystano wcześniej z usługi Azure Container Instance (ACI), zarejestruj dostawcę usług w ramach subskrypcji. Stan rejestracji dostawcy ACI można sprawdzić za pomocą polecenia [AZ Provider list][az-provider-list] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Dostawca *Microsoft. ContainerInstance* powinien raportować jako *zarejestrowane*, jak pokazano w następujących przykładowych danych wyjściowych:

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Jeśli dostawca jest wyświetlany jako *NotRegistered*, zarejestruj dostawcę przy użyciu polecenia [AZ Provider Register][az-provider-register] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>W przypadku klastrów z obsługą kontroli dostępu RBAC

Jeśli klaster AKS jest włączony przez funkcję RBAC, należy utworzyć konto usługi i powiązanie roli do użycia z tym itd. Aby uzyskać więcej informacji, zobacz [Helm kontroli dostępu opartej na rolach][helm-rbac]. Aby utworzyć konto usługi i powiązanie roli, Utwórz plik o nazwie *RBAC-Virtual-kubelet. YAML* i wklej następującą definicję:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Zastosuj konto usługi i powiązanie z [polecenia kubectl Zastosuj][kubectl-apply] i określ plik *RBAC-Virtual-kubelet. YAML* , jak pokazano w następującym przykładzie:

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Skonfiguruj Helm do korzystania z konta usługi do obsługi:

```console
helm init --service-account tiller
```

Teraz można nadal instalować wirtualne Kubelet w klastrze AKS.

## <a name="installation"></a>Instalacja

Użyj polecenia [AZ AKS Install-Connector][aks-install-connector] , aby zainstalować wirtualną Kubelet. Poniższy przykład służy do wdrażania łącznika Linux i Windows.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

Te argumenty są dostępne dla polecenia [AZ AKS Install-Connector][aks-install-connector] .

| Argument | Opis | Wymagane |
|---|---|:---:|
| `--connector-name` | Nazwa łącznika ACI.| Tak |
| `--name``-n` | Nazwa zarządzanego klastra. | Tak |
| `--resource-group``-g` | Nazwa grupy zasobów. | Tak |
| `--os-type` | Typ systemu operacyjnego wystąpień kontenerów. Dozwolone wartości: Oba systemy, Linux i Windows. Domyślne: Linux. | Nie |
| `--aci-resource-group` | Grupa zasobów, w której mają zostać utworzone grupy kontenerów ACI. | Nie |
| `--location``-l` | Lokalizacja, w której mają zostać utworzone grupy kontenerów ACI. | Nie |
| `--service-principal` | Nazwa główna usługi używana na potrzeby uwierzytelniania w interfejsach API platformy Azure. | Nie |
| `--client-secret` | Wpis tajny skojarzony z jednostką usługi. | Nie |
| `--chart-url` | Adres URL wykresu Helm, który instaluje łącznik ACI. | Nie |
| `--image-tag` | Tag obrazu wirtualnego obrazu kontenera kubelet. | Nie |

## <a name="validate-virtual-kubelet"></a>Weryfikuj wirtualną Kubelet

Aby sprawdzić, czy zainstalowano wirtualną Kubelet, zwróć listę węzłów Kubernetes przy użyciu polecenia [polecenia kubectl Get nodes][kubectl-get] :

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Uruchamianie kontenera systemu Linux

Utwórz plik o nazwie `virtual-kubelet-linux.yaml` i skopiuj w następującym YAML. Należy zauważyć, że [nodeSelector][node-selector] i [tolerowanie][toleration] są używane do zaplanowania kontenera w węźle.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
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
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Uruchom aplikację za pomocą polecenia [polecenia kubectl Create][kubectl-create] .

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Użyj [polecenia kubectl Get][kubectl-get] — polecenie z `-o wide` argumentem, aby wypróbować listę zasobników z zaplanowanym węzłem. Zwróć uwagę `aci-helloworld` `virtual-kubelet-virtual-kubelet-linux` na to, że zgodnie z harmonogramem zaplanowano w węźle.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Uruchamianie kontenera systemu Windows

Utwórz plik o nazwie `virtual-kubelet-windows.yaml` i skopiuj w następującym YAML. Należy zauważyć, że [nodeSelector][node-selector] i [tolerowanie][toleration] są używane do zaplanowania kontenera w węźle.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Uruchom aplikację za pomocą polecenia [polecenia kubectl Create][kubectl-create] .

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Użyj [polecenia kubectl Get][kubectl-get] — polecenie z `-o wide` argumentem, aby wypróbować listę zasobników z zaplanowanym węzłem. Zwróć uwagę `nanoserver-iis` `virtual-kubelet-virtual-kubelet-windows` na to, że zgodnie z harmonogramem zaplanowano w węźle.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Usuń wirtualną Kubelet

Użyj polecenia [AZ AKS Remove-Connector][aks-remove-connector] , aby usunąć wirtualną Kubelet. Zastąp wartości argumentów nazwą łącznika, klastrem AKS i grupą zasobów klastra AKS.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> Jeśli wystąpią błędy podczas usuwania obu łączników systemu operacyjnego lub chcesz usunąć tylko łącznik systemu operacyjnego Windows lub Linux, możesz ręcznie określić typ systemu operacyjnego. Dodaj parametr do poprzedniego `az aks remove-connector` polecenia i określ `Windows` lub `Linux`. `--os-type`

## <a name="next-steps"></a>Następne kroki

Aby uzyskać możliwe problemy z wirtualną Kubelet, zobacz [znane osobliwości i obejścia][vk-troubleshooting]. Aby zgłosić problemy z wirtualną Kubelet, [Otwórz problem][vk-issues]z usługą GitHub.

Przeczytaj więcej na temat wirtualnego Kubelet w [projekcie usługi Kubelet][vk-github]w witrynie GitHub.

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md
[aks-helm]: kubernetes-helm.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
