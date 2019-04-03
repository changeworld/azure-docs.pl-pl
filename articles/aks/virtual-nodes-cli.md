---
title: Tworzenie wirtualnych węzłów przy użyciu interfejsu wiersza polecenia platformy Azure usługi Kubernetes (AKS)
description: Dowiedz się, jak utworzyć klaster usługi Kubernetes usługi Azure (AKS), korzystającą z wirtualnych węzłów w celu uruchomienia zasobników za pomocą wiersza polecenia platformy Azure.
services: container-service
author: iainfoulds
ms.topic: conceptual
ms.service: container-service
ms.date: 12/03/2018
ms.author: iainfou
ms.openlocfilehash: 54c8e44685bb69e845c819b0c2846b188a771d71
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878234"
---
# <a name="preview---create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>W wersji zapoznawczej — tworzenie i konfigurowanie klastra usługi Azure Kubernetes usługi (AKS) do użycia wirtualnych węzłów przy użyciu wiersza polecenia platformy Azure

Szybkie skalowanie obciążeń aplikacji w klastrze usługi Azure Kubernetes Service (AKS), można użyć wirtualnych węzłów. Wirtualne węzły, możesz mieć szybkiej aprowizacji zasobników i płacić tylko na sekundę na czas wykonywania uległ. Nie należy czekać do wdrożenia węzłów obliczeniowych maszyn wirtualnych, aby uruchomić dodatkowe zasobniki skalowanie klastra usługi Kubernetes. W tym artykule pokazano, jak utworzyć i skonfigurować zasoby sieci wirtualnej i klastra AKS, a następnie włącz wirtualnych węzłów.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są samoobsługi i opcjonalnych. Wersje zapoznawcze są udostępniane do zbierania opinii i błędy z naszej społeczności. Nie są one jednak obsługiwane przez pomoc techniczną systemu Azure. Jeśli tworzenie klastra lub Dodaj następujące funkcje do istniejących klastrów tego klastra jest obsługiwany, dopóki ta funkcja nie jest już dostępna w wersji zapoznawczej i absolwentów, które są ogólnie dostępne (GA).
>
> Jeśli wystąpią problemy związane z wersji zapoznawczej, [Otwórz problem w repozytorium GitHub usługi AKS] [ aks-github] o nazwie funkcja w wersji zapoznawczej w tytuł usterki.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wirtualne węzły Włącz komunikację sieciową między zasobników, które są uruchamiane w usłudze ACI i klastrem AKS. Aby zapewnić tę komunikację, podsieci sieci wirtualnej jest tworzony i przypisanych uprawnień delegowanych. Wirtualne węzły działają tylko z klastrami usługi AKS utworzone za pomocą *zaawansowane* sieci. Domyślnie klastry usługi AKS są tworzone za pomocą *podstawowe* sieci. W tym artykule przedstawiono sposób tworzenia sieci wirtualnej i podsieci, a następnie wdrożyć klaster AKS, która używa zaawansowane sieci.

Jeśli wcześniej nie używano usługi ACI, należy zarejestrować dostawcę usług w ramach subskrypcji. Możesz sprawdzić stan usługi ACI dostawcy rejestracji za pomocą [az provider list] [ az-provider-list] polecenia, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* dostawca powinien wysyłać raporty jako *zarejestrowanej*, jak pokazano w następujących przykładowych danych wyjściowych:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Jeśli dostawca jest wyświetlany jako *NotRegistered*, zarejestruj dostawcę przy użyciu [az provider register] [ az-provider-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Dostępność regionalna

Następujące regiony są obsługiwane dla wdrożeń wirtualnego węzła:

* Australia Wschodnia (australiaeast)
* Wschodnie stany USA (eastus)
* Zachodnio-środkowe stany USA (westcentralus)
* Europa Zachodnia (westeurope)
* Zachodnie stany USA (westus)

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usłudze Cloud Shell, wybierz **wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli wolisz zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, ten artykuł wymaga interfejsu wiersza polecenia Azure w wersji 2.0.49 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create]. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Tworzenie sieci wirtualnej przy użyciu [tworzenie sieci wirtualnej sieci az] [ az-network-vnet-create] polecenia. Poniższy przykład tworzy nazwę sieci wirtualnej *myVnet* z prefiksem adresu *10.0.0.0/8*, a podsieć o nazwie *myAKSSubnet*. Prefiks adresu podsieci to wartość domyślna to *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Teraz Utwórz dodatkowe podsieci wirtualnych węzłów przy użyciu [az podsieci sieci wirtualnej Utwórz] [ az-network-vnet-subnet-create] polecenia. Poniższy przykład tworzy podsieć o nazwie *myVirtualNodeSubnet* z prefiksem adresu *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefix 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Jednostka usługi Azure Active Directory zezwala klastrowi usługi AKS na interakcje z innymi zasobami platformy Azure. Tę jednostkę usługi może automatycznie utworzyć interfejs wiersza polecenia platformy Azure lub portal. Inna możliwość to jej wstępne utworzenie i przypisanie dodatkowych uprawnień.

Tworzenie jednostki usługi przy użyciu [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] polecenia. Parametr `--skip-assignment` umożliwia ograniczenie przypisywania dodatkowych uprawnień.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Dane wyjściowe są podobne do poniższego przykładu:

```
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Zwróć uwagę na wartości *appId* i *password*. Te wartości są używane w kolejnych krokach.

## <a name="assign-permissions-to-the-virtual-network"></a>Przypisywanie uprawnień do sieci wirtualnej

Aby umożliwić klastrem i zarządzać sieci wirtualnej, należy udzielić nazwy głównej usługi AKS odpowiednich uprawnień, aby korzystać z zasobów sieciowych.

Najpierw Pobierz, używając Identyfikatora zasobu sieci wirtualnej [az sieci vnet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Aby udzielić prawidłowy dostęp do klastra AKS użyć sieci wirtualnej, utworzyć przypisania roli przy użyciu [utworzenia przypisania roli az] [ az-role-assignment-create] polecenia. Zastąp ciągi `<appId`> i `<vnetId>` wartościami określonymi w dwóch poprzednich krokach.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Możesz wdrożyć klaster usługi AKS w taki sposób, w podsieci usługi AKS utworzonego w poprzednim kroku. Pobierz identyfikator tej podsieci przy użyciu [Pokaż podsieci sieci wirtualnej sieci az][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Utwórz klaster AKS za pomocą polecenia [az aks create][az-aks-create]. W poniższym przykładzie pokazano tworzenie klastra o nazwie *myAKSCluster* z jednym węzłem. Zastąp `<subnetId>` o identyfikatorze uzyskanego w poprzednim kroku, a następnie `<appId>` i `<password>` z 

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON.

## <a name="enable-virtual-nodes-addon"></a>Włącz dodatek wirtualnych węzłów

Aby włączyć wirtualne węzły, teraz używać [az aks enable dodatków] [ az-aks-enable-addons] polecenia. W poniższym przykładzie użyto podsieci o nazwie *myVirtualNodeSubnet* utworzony w poprzednim kroku:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```
> [!NOTE]
> Jeśli zostanie wyświetlony błąd dotyczący wirtualnego węzła nie znaleziono, może być konieczne zainstalowanie jej rozszerzenie interfejsu wiersza polecenia 
> ```azurecli-interactive
> az extension add --source https://aksvnodeextension.blob.core.windows.net/aks-virtual-node/aks_virtual_node-0.2.0-py2.py3-none-any.whl
> ```

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. Ten krok umożliwia pobranie poświadczeń i skonfigurowanie interfejsu wiersza polecenia Kubernetes do ich użycia.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby zweryfikować połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get] w celu zwrócenia listy węzłów klastra.

```console
kubectl get nodes
```

Następujące przykładowe dane wyjściowe zawiera jeden węzeł maszyny Wirtualnej utworzone, a następnie wirtualnego węzła dla systemu Linux, *wirtualnego node-aci-linux*:

```
$ kubectl get nodes

NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Wdróż przykładową aplikację

Utwórz plik o nazwie `virtual-node.yaml` i skopiuj do poniższego kodu YAML. Aby zaplanować uruchamianie kontenera w węźle [nodeSelector] [ node-selector] i [toleration] [ toleration] są zdefiniowane.

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
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Uruchom aplikację za pomocą [zastosować kubectl] [ kubectl-apply] polecenia.

```console
kubectl apply -f virtual-node.yaml
```

Użyj [kubectl get pods-] [ kubectl-get] polecenia `-o wide` argument służący do wypełniania wyjściowego listę zasobników i zaplanowane węzła. Należy zauważyć, że `aci-helloworld` zasobnika została zaplanowana na `virtual-node-aci-linux` węzła.

```
$ kubectl get pods -o wide

NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Zasobnik jest przypisany wewnętrznego adresu IP z podsieci sieci wirtualnej platformy Azure, delegowane do użytku z wirtualnych węzłów.

> [!NOTE]
> Jeśli używasz obrazów przechowywanych w usłudze Azure Container Registry, [Konfigurowanie i używanie wpisie tajnym rozwiązania Kubernetes][acr-aks-secrets]. To aktualne ograniczenie wirtualnym węzłami (wersja zapoznawcza) to, że nie używasz zintegrowane uwierzytelnianie jednostki usługi Azure AD. Jeśli nie używasz klucza tajnego, zasobników zaplanowane na wirtualnych węzłów nie można uruchomić i zgłoś błąd `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testowanie pod węzłem wirtualnym

Aby przetestować uruchomiony na wirtualny węzeł zasobnik, przejdź do aplikacji demonstracyjnej z klienta sieci web. Zasobnik jest przypisany wewnętrznego adresu IP, możesz szybko przetestować tego połączenia z poziomu innego zasobnik w klastrze AKS. Utwórz zasobnik testu i do niej dołączyć sesję terminalu:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Zainstaluj `curl` w zasobnik przy użyciu `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Teraz uzyskiwać dostęp do sieci za pomocą pod adres `curl`, takich jak *http://10.241.0.4*. Podaj własne wewnętrzny adres IP, które są wyświetlane w ciągu poprzednich `kubectl get pods` polecenia:

```console
curl -L http://10.241.0.4
```

Wyświetlane są aplikacji pokazowej, jak pokazano w następujących danych wyjściowych skróconego przykładu:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zamknij sesję terminala tym zasobniku testu za pomocą `exit`. Gdy sesja zostanie zakończona, zasobnika ustawiany jest usunięte.

## <a name="remove-virtual-nodes"></a>Usuń wirtualne węzły

Jeśli nie chcesz już używać wirtualnych węzłów, można je wyłączyć za pomocą [az aks disable dodatków] [ az aks disable-addons] polecenia. Poniższy przykład wyłącza wirtualnych węzłów systemu Linux:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Teraz Usuń zasoby sieci wirtualnej i grupy zasobów:

```azurecli-interactive
# Change the name of your resource group and network resources as needed
RES_GROUP=myResourceGroup

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name myVnet --name myVirtualNodeSubnet --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name myVnet --name myVirtualNodeSubnet --remove delegations 0
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zasobnik została zaplanowana na wirtualny węzeł i przypisać prywatny, wewnętrzny adres IP. Można zamiast tego utworzyć wdrożenie usługi i kierowanie ruchem na tym zasobniku za pośrednictwem modułu równoważenia obciążenia lub kontrolera danych przychodzących. Aby uzyskać więcej informacji, zobacz [utworzyć kontroler podstawowy ruch przychodzący w usłudze AKS][aks-basic-ingress].

Wirtualne węzły są często jeden składnik skalowania rozwiązania w usłudze AKS. Aby uzyskać więcej informacji na temat skalowania rozwiązań zobacz następujące artykuły:

- [Użyj skalowania automatycznego zasobników w poziomie rozwiązania Kubernetes][aks-hpa]
- [Użyj skalowania automatycznego klastra Kubernetes][aks-cluster-autoscaler]
- [Zapoznaj się z przykładowych skalowania automatycznego dla wirtualnych węzłów][virtual-node-autoscale]
- [Więcej informacji na temat rozwiązania Virtual Kubelet biblioteki typu open source][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
