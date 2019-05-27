---
title: W wersji zapoznawczej — tworzenie kontenerów systemu Windows Server w klastrze usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes, Wdróż aplikację w kontenerze systemu Windows Server w usłudze Azure Kubernetes Service (AKS) przy użyciu wiersza polecenia platformy Azure.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: zarhoads
ms.openlocfilehash: 328fb9707c7151b8696cfb55e13567db90e45b7f
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991152"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>W wersji zapoznawczej — tworzenie kontenerów systemu Windows Server w klastrze usługi Azure Kubernetes Service (AKS) przy użyciu wiersza polecenia platformy Azure

Azure Kubernetes Service (AKS) to zarządzana usługa platformy Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. W tym artykule wdrożysz klastra usługi AKS przy użyciu wiersza polecenia platformy Azure. Możesz również wdrażanie przykładowej aplikacji ASP.NET w kontenerze systemu Windows Server w klastrze.

Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

![Obraz przedstawiający Przechodzenie do aplikacji przykładowej platformy ASP.NET](media/windows-container/asp-net-sample-app.png)

W tym artykule założono podstawową wiedzę na temat pojęciach usługi Kubernetes. Aby uzyskać więcej informacji, zobacz temat [Kubernetes core concepts for Azure Kubernetes Service (AKS)][kubernetes-concepts] (Kubernetes — podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Po utworzeniu klastra, który umożliwia uruchamianie kontenerów systemu Windows Server, należy dodać do puli dodatkowego węzła. Dodawanie puli dodatkowego węzła zostało omówione w dalszej części, ale najpierw należy włączyć kilka funkcji w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są samoobsługi i opcjonalnych. Wersje zapoznawcze są udostępniane do zbierania opinii i błędy z naszej społeczności. Nie są one jednak obsługiwane przez pomoc techniczną systemu Azure. Jeśli tworzenie klastra lub Dodaj następujące funkcje do istniejących klastrów tego klastra jest obsługiwany, dopóki ta funkcja nie jest już dostępna w wersji zapoznawczej i absolwentów, które są ogólnie dostępne (GA).
>
> Jeśli wystąpią problemy związane z wersji zapoznawczej, [Otwórz problem w repozytorium GitHub usługi AKS] [ aks-github] o nazwie funkcja w wersji zapoznawczej w tytuł usterki.

### <a name="install-aks-preview-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia w wersji zapoznawczej usługi aks
    
Polecenia interfejsu wiersza polecenia do tworzenia i obsługi wielu pul węzłów są dostępne w *podglądu usługi aks* rozszerzenie interfejsu wiersza polecenia. Zainstaluj *podglądu usługi aks* rozszerzenie interfejsu wiersza polecenia platformy Azure przy użyciu polecenia [Dodaj rozszerzenie az] [ az-extension-add] polecenia, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Jeśli wcześniej zainstalowano *podglądu usługi aks* rozszerzenia, zainstaluj dostępne aktualizacje, przy użyciu `az extension update --name aks-preview` polecenia.

### <a name="register-windows-preview-feature"></a>Zarejestruj funkcję Windows w wersji zapoznawczej

Aby utworzyć klaster usługi AKS, które mogą używać wielu pul węzłów i uruchamianie kontenerów systemu Windows Server, należy najpierw włączyć *WindowsPreview* funkcji flagi w ramach Twojej subskrypcji. *WindowsPreview* funkcja używa także puli wielowęzłowych klastrów i maszyn wirtualnych zestawu skalowania w celu zarządzania wdrażaniem i konfiguracji węzłów rozwiązania Kubernetes. Zarejestruj *WindowsPreview* przy użyciu flagi funkcji [az feature register] [ az-feature-register] polecenia, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Każdy klaster AKS możesz utworzyć po pomyślnym zarejestrowaniu *WindowsPreview* flagę funkcji, użyj tego środowiska klastra w wersji zapoznawczej. Aby kontynuować tworzenie klastrów regularnych, w pełni obsługiwane, nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji w środowisku produkcyjnym. Użyj oddzielnych testowym lub deweloperskim subskrypcji platformy Azure do testowania funkcji w wersji zapoznawczej.

Zajmuje kilka minut, zanim stan wyświetlany *zarejestrowanej*. Można sprawdzić stan rejestracji przy użyciu [lista funkcji az] [ az-feature-list] polecenia:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestracji *Microsoft.ContainerService* dostawcę zasobów przy użyciu [az provider register] [ az-provider-register] polecenia:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Ograniczenia

Podczas tworzenia i zarządzania klastrami usługi AKS, które obsługują wiele pul węzłów obowiązują następujące ograniczenia:

* Wiele pul węzłów są dostępne w przypadku klastrów utworzonych po pomyślnym zarejestrowaniu *WindowsPreview*. Wiele pul węzłów są również dostępne, Jeżeli zarejestrujesz *MultiAgentpoolPreview* i *VMSSPreview* funkcje dla Twojej subskrypcji. Nie można dodać lub Zarządzaj pulami węzła z istniejącego klastra AKS utworzone przed te funkcje zostały pomyślnie zarejestrowane.
* Nie można usunąć puli pierwszy węzeł.

Chociaż ta funkcja jest dostępna w wersji zapoznawczej, następujące dodatkowe ograniczenia:

* Klaster AKS może mieć maksymalnie osiem pule węzłów.
* Klaster AKS może mieć maksymalnie 400 węzłów w tych pulach osiem węzłów.
* Nazwa puli węzeł systemu Windows Server z limitem 6 znaków.
* Pule węzłów systemu Windows Server nie są dostępne w regionach Kanada w tej chwili.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Podczas tworzenia grupy zasobów użytkownik jest proszony o określenie lokalizacji. Ta lokalizacja jest, gdzie przechowywane są metadane grupy zasobów, jest również, gdy zasoby uruchamianie na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobu. Utwórz grupę zasobów przy użyciu polecenia [az group create][az-group-create].

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Następujące przykładowe dane wyjściowe przedstawiają pomyślnie utworzoną grupę zasobów:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>Tworzenie klastra AKS
Aby można było uruchomić klaster AKS, która obsługuje pule węzłów dla kontenerów systemu Windows Server, klaster musi korzystać z zasad sieciowych, który używa [wtyczki Azure CNI] [ azure-cni-about] wtyczki sieci (zaawansowane). Aby uzyskać szczegółowe informacje, aby pomóc w planowaniu podsieci wymaganych zakresów i zagadnienia dotyczące sieci, zobacz [konfigurowaniu sieci wtyczki Azure CNI][use-advanced-networking]. Użyj [tworzenie az aks] [ az-aks-create] polecenie, aby utworzyć klaster usługi AKS, o nazwie *myAKSCluster*. To polecenie spowoduje utworzenie zasoby sieciowe, jeśli nie istnieją.
  * Klaster jest skonfigurowany z jednym węzłem
  * *Systemu windows-admin-password* i *nazwa użytkownika administratora systemu windows* Parametry ustawione poświadczenia administratora dla wszelkich kontenerów systemu Windows Server w klastrze utworzono.

Podaj własne bezpieczne *PASSWORD_WIN*.

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.0 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON.

## <a name="add-a-windows-server-node-pool"></a>Dodaj pulę węzłów systemu Windows Server

Domyślnie klaster AKS jest tworzony z puli węzeł, który umożliwia uruchamianie kontenerów systemu Linux. Użyj `az aks nodepool add` polecenie, aby dodać do puli dodatkowego węzła, który umożliwia uruchamianie kontenerów systemu Windows Server.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.0
```

Powyższe polecenie tworzy pulę węzłów o nazwie *npwin* i dodaje go do *myAKSCluster*. Podczas tworzenia puli węzłów do uruchamiania kontenerów systemu Windows Server, wartość domyślna *rozmiar w przypadku maszyn wirtualnych węzła* jest *Standard_D2s_v3*. Jeśli zdecydujesz się ustawić *rozmiar w przypadku maszyn wirtualnych węzła* parametru, sprawdź, czy lista [rozmiarów maszyn wirtualnych z ograniczeniami][restricted-vm-sizes]. Minimalny rozmiar zalecany to *Standard_D2s_v3*. Powyższe polecenie używa również podsieć domyślna w sieci wirtualnej domyślny utworzony podczas uruchamiania `az aks create`.

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes — narzędzia [kubectl][kubectl]. Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Aby zainstalować narzędzie `kubectl` lokalnie, użyj polecenia [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. To polecenie powoduje pobranie poświadczeń i skonfigurowanie interfejsu wiersza polecenia Kubernetes do ich użycia.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby zweryfikować połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get] w celu zwrócenia listy węzłów klastra.

```azurecli-interactive
kubectl get nodes
```

Poniższe przykładowe dane wyjściowe zawierają jeden węzeł utworzony w poprzednich krokach. Upewnij się, że stan węzła to *Gotowy*:

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.0
aksnpwin987654                      Ready    agent   108s   v1.14.0
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Plik manifestu platformy Kubernetes definiuje żądany stan klastra, w tym informacje o obrazach kontenera do uruchomienia. W tym artykule manifest służy do tworzenia wszystkich obiektów potrzebnych do uruchamiania przykładowej aplikacji platformy ASP.NET w kontenerze systemu Windows Server. Obejmuje to manifest [wdrażanie Kubernetes] [ kubernetes-deployment] dla przykładowej aplikacji platformy ASP.NET i zewnętrznego [usługi Kubernetes] [ kubernetes-service] do Uzyskaj dostęp do aplikacji z Internetu.

Przykładowa aplikacja ASP.NET jest dostarczana jako część [przykłady dla platformy .NET Framework] [ dotnet-samples] i jest uruchamiany w kontenerze systemu Windows Server. Wymaga usługi AKS, kontenery systemu Windows Server była oparta na obrazy *systemu Windows Server 2019* lub nowszej. Również muszą definiować plik manifestu rozwiązania Kubernetes [selektor węzła] [ node-selector] mówić klastra usługi AKS, aby uruchomić aplikację przykładową ASP.NET pod w węźle, który umożliwia uruchamianie kontenerów systemu Windows Server.

Utwórz plik o nazwie `sample.yaml` i skopiuj go do poniższej definicji YAML. Jeśli używasz usługi Azure Cloud Shell, ten plik można utworzyć przy użyciu programu `vi` lub `nano`, tak jak podczas pracy w systemie wirtualnym lub fizycznym:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800m
          requests:
            cpu: .1
            memory: 300m
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Wdróż aplikację przy użyciu polecenia [kubectl apply][kubectl-apply] i podaj nazwę manifestu YAML:

```azurecli-interactive
kubectl apply -f sample.yaml
```

Następujące przykładowe dane wyjściowe pokazuje, wdrażania i pomyślnie utworzono usługę:

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testowanie aplikacji

Podczas uruchamiania aplikacji usługa Kubernetes uwidacznia fronton aplikacji w Internecie. Ten proces może potrwać kilka minut.

Aby monitorować postęp, użyj polecenia [kubectl get-service][kubectl-get] z argumentem `--watch`.

```azurecli-interactive
kubectl get service sample --watch
```

Początkowo *EXTERNAL-IP* dla *przykładowe* usługi jest przedstawiana w postaci *oczekujące*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Gdy dla adresu *EXTERNAL-IP* wartość *oczekujący* zmieni się na rzeczywisty publiczny adres IP, naciśnij klawisze `CTRL-C`, aby zatrzymać proces śledzenia narzędzia `kubectl`. Następujące przykładowe dane wyjściowe przedstawiają prawidłowy publiczny adres IP przypisany do usługi:

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Aby wyświetlić przykładową aplikację w akcji, otwórz przeglądarkę sieci web w taki sposób, aby zewnętrzny adres IP usługi.

![Obraz przedstawiający Przechodzenie do aplikacji przykładowej platformy ASP.NET](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Usuń klaster

Gdy klaster nie będzie już potrzebny, usuń grupę zasobów, usługę kontenera i wszystkie pokrewne zasoby za pomocą polecenia [az group delete][az-group-delete].

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby sprawdzić, jak usunąć jednostkę usługi, zobacz [AKS service principal considerations and deletion (Uwagi dotyczące jednostki usługi AKS i jej usuwanie)][sp-delete].

## <a name="next-steps"></a>Kolejne kroki

W tym artykule wdrożono klaster Kubernetes i przykładowej aplikacji ASP.NET w kontenerze systemu Windows Server w nim wdrożona. [Uzyskaj dostęp do internetowego pulpitu nawigacyjnego rozwiązania Kubernetes][kubernetes-dashboard] dla właśnie utworzonego klastra.

Aby dowiedzieć się więcej o usłudze AKS i poznać dokładnie proces od kompletnego kodu do wdrożenia, przejdź do samouczka dotyczącego klastra Kubernetes.

> [!div class="nextstepaction"]
> [Samouczek usługi AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[aks-github]: https://github.com/azure/aks/issues
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
