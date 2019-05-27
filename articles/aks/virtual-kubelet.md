---
title: Uruchom rozwiązania Virtual Kubelet w klastrze usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak za pomocą rozwiązania Virtual Kubelet za pomocą usługi Azure Kubernetes Service (AKS) uruchamianie kontenerów systemu Linux i Windows w usłudze Azure Container Instances.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/14/2018
ms.author: iainfou
ms.openlocfilehash: f7a0269ff22987648d134cb7f4fba8e28e29fd8b
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956292"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Rozwiązania Virtual Kubelet za pomocą usługi Azure Kubernetes Service (AKS)

Usługa Azure Container Instances (ACI) zapewniają hostowanej środowisko do uruchamiania kontenerów na platformie Azure. Korzystając z usługi ACI, nie ma konieczności zarządzania podstawową infrastrukturą obliczeń, Azure obsługuje zarządzania dla Ciebie. Uruchamianie kontenerów w usłudze ACI, opłaty są naliczane za sekundę dla każdego uruchomionego kontenera.

W przypadku używania dostawcy rozwiązania Virtual Kubelet dla usługi Azure Container Instances, kontenerów systemów Linux i Windows można zaplanować na wystąpienie kontenera tak, jakby była standardowy węzeł rozwiązania Kubernetes. Ta konfiguracja umożliwia korzystanie z zalet możliwości platformy Kubernetes i korzyści wartość i kosztów zarządzania wystąpienia kontenera.

> [!NOTE]
> AKS ma teraz wbudowaną obsługę planowania kontenerów w usłudze ACI, o nazwie *wirtualnych węzłów*. Te węzły wirtualny obsługuje obecnie wystąpień kontenera systemu Linux. Jeśli potrzebujesz do zaplanowania Windows container instances, możesz kontynuować używanie rozwiązania Virtual Kubelet. W przeciwnym razie należy użyć wirtualnych węzłów zamiast ręcznego instrukcji rozwiązania Virtual Kubelet wymienionych w tym artykule. Możesz rozpocząć pracę z wirtualnych węzłów przy użyciu [wiersza polecenia platformy Azure] [ virtual-nodes-cli] lub [witryny Azure portal][virtual-nodes-portal].
>
> Rozwiązania Virtual Kubelet jest projektem eksperymentalne "open source" i powinna być używana w związku z tym. Aby współtworzyć, problemów z plików i Przeczytaj więcej na temat rozwiązania virtual kubelet, zobacz [projektu wirtualnego GitHub agenta Kubelet][vk-github].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym dokumencie przyjęto założenie, iż klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz [szybkiego startu usługi Azure Kubernetes Service (AKS)][aks-quick-start].

Należy również wiersza polecenia platformy Azure w wersji **2.0.33** lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Aby zainstalować rozwiązania Virtual Kubelet [Helm](https://docs.helm.sh/using_helm/#installing-helm) jest również wymagany.

### <a name="register-container-instances-feature-provider"></a>Zarejestruj dostawcę funkcji Container Instances

Jeśli wcześniej nie używano usługi wystąpienia kontenera platformy Azure (ACI), należy zarejestrować dostawcę usług w ramach subskrypcji. Stan rejestracji dostawcy usługi ACI, za pomocą polecenia [az dostawca list] [az provider list], można sprawdzić, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* dostawca powinien wysyłać raporty jako *zarejestrowanej*, jak pokazano w następujących przykładowych danych wyjściowych:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Jeśli dostawca jest wyświetlany jako *NotRegistered*, zarejestruj dostawcę, przy użyciu [az zarejestrować dostawcy] [az-provider register], jak pokazano w poniższym przykładzie:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>W przypadku klastrów z obsługą kontroli RBAC

W przypadku klastra usługi AKS z obsługą funkcji RBAC, należy utworzyć konta usługi i roli powiązania do użycia z Tiller. Aby uzyskać więcej informacji, zobacz [kontroli dostępu opartej na rolach Helm][helm-rbac]. Aby utworzyć konto usługi i powiązania roli, Utwórz plik o nazwie *rbac wirtualnego — kubelet.yaml* i wklej następującą definicję:

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

Zastosuj konta usługi i powiązania z [zastosować kubectl] [ kubectl-apply] i określ swoje *rbac wirtualnego — kubelet.yaml* pliku, jak pokazano w poniższym przykładzie:

```
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Konfigurowanie narzędzia Helm, aby użyć konta usługi programu tiller:

```console
helm init --service-account tiller
```

Teraz można kontynuować instalowania rozwiązania Virtual Kubelet w klastrze AKS.

## <a name="installation"></a>Instalacja

Użyj [az aks install-connector] [ aks-install-connector] polecenie, aby zainstalować rozwiązania Virtual Kubelet. Poniższy przykład służy do wdrażania łącznika z systemem Linux i Windows.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

Te argumenty są dostępne dla `aks install-connector` polecenia.

| Argument: | Opis | Wymagane |
|---|---|:---:|
| `--connector-name` | Nazwa łącznika usługi ACI.| Tak |
| `--name` `-n` | Nazwa zarządzanego klastra. | Tak |
| `--resource-group` `-g` | Nazwa grupy zasobów. | Tak |
| `--os-type` | Typ systemu operacyjnego wystąpienia kontenera. Dozwolone wartości: Oba, Linux oraz Windows. Domyślne: Linux. | Nie |
| `--aci-resource-group` | Grupa zasobów, w której chcesz utworzyć grup kontenerów usługi ACI. | Nie |
| `--location` `-l` | Lokalizacja do tworzenia grup kontenerów usługi ACI. | Nie |
| `--service-principal` | Jednostki usługi używany do uwierzytelniania do interfejsów API platformy Azure. | Nie |
| `--client-secret` | Klucz tajny skojarzony z jednostką usługi. | Nie |
| `--chart-url` | Adres URL wykresu Helm, który instaluje łącznik ACI. | Nie |
| `--image-tag` | Tag obrazu rozwiązania virtual kubelet obrazu kontenera. | Nie |

## <a name="validate-virtual-kubelet"></a>Sprawdź poprawność rozwiązania Virtual Kubelet

Aby sprawdzić, czy zainstalowano rozwiązania Virtual Kubelet, powróci do listy węzłów rozwiązania Kubernetes za pomocą [kubectl get-węzły] [ kubectl-get] polecenia.

```
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Uruchamianie kontenera systemu Linux

Utwórz plik o nazwie `virtual-kubelet-linux.yaml` i skopiuj do poniższego kodu YAML. Zwróć uwagę, że [nodeSelector] [ node-selector] i [toleration] [ toleration] są używane do zaplanowania kontenerów w węźle.

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

Uruchom aplikację za pomocą [tworzenie kubectl] [ kubectl-create] polecenia.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Użyj [kubectl get pods-] [ kubectl-get] polecenia `-o wide` argument służący do wypełniania wyjściowego listę zasobników w węźle zaplanowane. Należy zauważyć, że `aci-helloworld` zasobnika została zaplanowana na `virtual-kubelet-virtual-kubelet-linux` węzła.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Uruchamianie kontenera systemu Windows

Utwórz plik o nazwie `virtual-kubelet-windows.yaml` i skopiuj do poniższego kodu YAML. Zwróć uwagę, że [nodeSelector] [ node-selector] i [toleration] [ toleration] są używane do zaplanowania kontenerów w węźle.

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

Uruchom aplikację za pomocą [tworzenie kubectl] [ kubectl-create] polecenia.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Użyj [kubectl get pods-] [ kubectl-get] polecenia `-o wide` argument służący do wypełniania wyjściowego listę zasobników w węźle zaplanowane. Należy zauważyć, że `nanoserver-iis` zasobnika została zaplanowana na `virtual-kubelet-virtual-kubelet-win` węzła.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Usuwanie rozwiązania Virtual Kubelet

Użyj [az aks remove-connector] [ aks-remove-connector] polecenie, aby usunąć rozwiązania Virtual Kubelet. Zastąp wartości argumentu z nazwą łącznika, klaster AKS i grupa zasobów klastra usługi AKS.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

> [!NOTE]
> Wystąpiły błędy usuwania oba łączniki systemu operacyjnego, czy chcesz usunąć tylko Windows lub systemu operacyjnego Linux łącznika, można ręcznie określić typ systemu operacyjnego. Dodaj `--os-type` parametru do poprzedniego `az aks remove-connector` polecenia, a następnie określ `Windows` lub `Linux`.

## <a name="next-steps"></a>Kolejne kroki

W przypadku możliwych problemów za pomocą rozwiązania Virtual Kubelet, zobacz [znane Osobliwości wraz z ich obejściami][vk-troubleshooting]. Na zgłaszanie problemów za pomocą rozwiązania Virtual Kubelet [Otwórz problem w usłudze GitHub][vk-issues].

Przeczytaj więcej na temat rozwiązania Virtual Kubelet w [projektu wirtualnego GitHub agenta Kubelet][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md

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
