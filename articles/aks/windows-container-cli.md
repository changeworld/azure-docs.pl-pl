---
title: W wersji zapoznawczej — tworzenie kontenerów systemu Windows Server w klastrze usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes, Wdróż aplikację w kontenerze systemu Windows Server w usłudze Azure Kubernetes Service (AKS) przy użyciu wiersza polecenia platformy Azure.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: twhitney
ms.openlocfilehash: b753d643b4651cd6665b5b85dcb8b7c5f0b3583d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444142"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>W wersji zapoznawczej — tworzenie kontenerów systemu Windows Server w klastrze usługi Azure Kubernetes Service (AKS) przy użyciu wiersza polecenia platformy Azure

Azure Kubernetes Service (AKS) to zarządzana usługa platformy Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. W tym artykule wdrożysz klastra usługi AKS przy użyciu wiersza polecenia platformy Azure. Możesz również wdrażanie przykładowej aplikacji ASP.NET w kontenerze systemu Windows Server w klastrze.

Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

![Obraz przedstawiający Przechodzenie do aplikacji przykładowej platformy ASP.NET](media/windows-container/asp-net-sample-app.png)

W tym artykule założono podstawową wiedzę na temat pojęciach usługi Kubernetes. Aby uzyskać więcej informacji, zobacz [Kubernetes podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Po utworzeniu klastra, który umożliwia uruchamianie kontenerów systemu Windows Server, należy dodać do puli dodatkowego węzła. Dodawanie puli dodatkowego węzła zostało omówione w dalszej części, ale najpierw należy włączyć kilka funkcji w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są samoobsługi, uczestnictwo. Są one udostępniane do zbierania opinii i błędy z naszej społeczności. W wersji zapoznawczej te funkcje nie są przeznaczone do użytku produkcyjnego. Funkcje w wersji zapoznawczej objęte "starań" pomocy technicznej. Pomoc od zespołów pomocy technicznej usługi AKS jest dostępna w godzinach pracy i Pacyfik, część strefy czasowej (PST) tylko. Aby uzyskać więcej informacji zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej usługi AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia w wersji zapoznawczej usługi aks

Aby korzystać z kontenerów systemu Windows Server, musisz mieć *podglądu usługi aks* interfejsu wiersza polecenia wersja rozszerzenia 0.4.1 lub nowszej. Zainstaluj *podglądu usługi aks* rozszerzenie interfejsu wiersza polecenia platformy Azure przy użyciu polecenia [Dodaj rozszerzenie az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] polecenia::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Zarejestruj funkcję Windows w wersji zapoznawczej

Aby utworzyć klaster usługi AKS, które mogą używać wielu pul węzłów i uruchamianie kontenerów systemu Windows Server, należy najpierw włączyć *WindowsPreview* funkcji flagi w ramach Twojej subskrypcji. *WindowsPreview* funkcja używa także puli wielowęzłowych klastrów i maszyn wirtualnych zestawu skalowania w celu zarządzania wdrażaniem i konfiguracji węzłów rozwiązania Kubernetes. Zarejestruj *WindowsPreview* przy użyciu flagi funkcji [az feature register][az-feature-register] polecenia, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Każdy klaster AKS możesz utworzyć po pomyślnym zarejestrowaniu *WindowsPreview* flagę funkcji, użyj tego środowiska klastra w wersji zapoznawczej. Aby kontynuować tworzenie klastrów regularnych, w pełni obsługiwane, nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji w środowisku produkcyjnym. Użyj oddzielnych testowym lub deweloperskim subskrypcji platformy Azure do testowania funkcji w wersji zapoznawczej.

Trwa kilka minut, zanim rejestracji w celu ukończenia. Sprawdź stan rejestracji przy użyciu [lista funkcji az][az-feature-list] polecenia:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Gdy stan rejestracji to `Registered`, naciśnij klawisze Ctrl-C, aby zatrzymać monitorowanie stanu.  Następnie Odśwież rejestracji *Microsoft.ContainerService* dostawcę zasobów przy użyciu [az provider register][az-provider-register] polecenia:

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

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Podczas tworzenia grupy zasobów użytkownik jest proszony o określenie lokalizacji. Ta lokalizacja jest, gdzie przechowywane są metadane grupy zasobów, jest również, gdy zasoby uruchamianie na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobu. Utwórz grupę zasobów za pomocą [Tworzenie grupy az][az-group-create] polecenia.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

> [!NOTE]
> W tym artykule używa składni Bash dla poleceń w ramach tego samouczka.
> Jeśli używasz usługi Azure Cloud Shell, upewnij się, że lista rozwijana w lewym górnym rogu okna usługi Cloud Shell jest ustawiona na **Bash**.

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

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Aby można było uruchomić klaster AKS, która obsługuje pule węzłów dla kontenerów systemu Windows Server, klaster musi korzystać z zasad sieciowych, który używa [wtyczki Azure CNI][azure-cni-about] (advanced) network plugin. For more detailed information to help plan out the required subnet ranges and network considerations, see [configure Azure CNI networking][use-advanced-networking]. Użyj [tworzenie az aks][az-aks create] polecenie, aby utworzyć klaster usługi AKS, o nazwie *myAKSCluster*. To polecenie spowoduje utworzenie zasoby sieciowe, jeśli nie istnieją.
  * Klaster jest skonfigurowany z jednym węzłem
  * *Systemu windows-admin-password* i *nazwa użytkownika administratora systemu windows* Parametry ustawione poświadczenia administratora dla wszelkich kontenerów systemu Windows Server w klastrze utworzono.

Podaj własne bezpieczne *PASSWORD_WIN* (należy pamiętać, że polecenia w tym artykule są wprowadzane do powłoki BASH):

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

> [!Note]
> Jeśli wystąpi błąd sprawdzania poprawności hasła, spróbuj, tworzenie grupy zasobów w innym regionie.
> Spróbuj tworzenia klastra przy użyciu nowej grupy zasobów.

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

Aby zarządzać klastrem Kubernetes, należy użyć [kubectl][kubectl], czyli klienta wiersza polecenia usługi Kubernetes. Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Aby zainstalować `kubectl` lokalnie, użyj [az aks install-cli][az-aks-install-cli] polecenia:

```azurecli
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get], aby powrócić do listy węzłów klastra.

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

Plik manifestu platformy Kubernetes definiuje żądany stan klastra, w tym informacje o obrazach kontenera do uruchomienia. W tym artykule manifest służy do tworzenia wszystkich obiektów potrzebnych do uruchamiania przykładowej aplikacji platformy ASP.NET w kontenerze systemu Windows Server. Obejmuje to manifest [wdrażanie Kubernetes][kubernetes-deployment] for the ASP.NET sample application and an external [Kubernetes service][kubernetes-service] dostępu do aplikacji z Internetu.

Przykładowa aplikacja ASP.NET jest dostarczana jako część [przykłady dla platformy .NET Framework][dotnet-samples] i jest uruchamiany w kontenerze systemu Windows Server. Wymaga usługi AKS, kontenery systemu Windows Server była oparta na obrazy *systemu Windows Server 2019* lub nowszej. Również muszą definiować plik manifestu rozwiązania Kubernetes [selektor węzła][node-selector] mówić klastra usługi AKS, aby uruchomić aplikację przykładową ASP.NET pod w węźle, który umożliwia uruchamianie kontenerów systemu Windows Server.

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
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
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

Wdrażanie aplikacji przy użyciu [zastosować kubectl][kubectl-apply] polecenia i podaj nazwę manifeście YAML:

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

## <a name="delete-cluster"></a>Usuwanie klastra

Gdy klaster nie będzie już potrzebny, należy użyć [usunięcie grupy az][az-group-delete] polecenia, aby usunąć grupę zasobów, usługę kontenera i wszystkie pokrewne zasoby.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby uzyskać instrukcje dotyczące sposobu usuwania nazwy głównej usługi, zobacz [uwagi podmiotu zabezpieczeń i usuwania usługi AKS][sp-delete].

## <a name="next-steps"></a>Kolejne kroki

W tym artykule wdrożono klaster Kubernetes i przykładowej aplikacji ASP.NET w kontenerze systemu Windows Server w nim wdrożona. [Dostęp do pulpitu nawigacyjnego sieci web rozwiązania Kubernetes][kubernetes-dashboard] klastra został utworzony.

Aby dowiedzieć się więcej o usłudze AKS i poznać dokładnie proces od kompletnego kodu do wdrożenia, przejdź do samouczka dotyczącego klastra Kubernetes.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
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
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
