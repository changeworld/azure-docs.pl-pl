---
title: Wersja zapoznawcza — Tworzenie kontenera systemu Windows Server w klastrze usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes, wdrożyć aplikację w kontenerze systemu Windows Server w usłudze Azure Kubernetes Service (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: mlearned
ms.openlocfilehash: 305901007180cfb197cf5c0dfb338800449560a1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68382031"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Wersja zapoznawcza — Tworzenie kontenera systemu Windows Server w klastrze usługi Azure Kubernetes Service (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure

Azure Kubernetes Service (AKS) to zarządzana usługa platformy Kubernetes, która umożliwia szybkie wdrażanie klastrów i zarządzanie nimi. W tym artykule opisano wdrażanie klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure. Możesz również wdrożyć aplikację przykładową ASP.NET w kontenerze systemu Windows Server w klastrze.

Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

![Obraz przedstawiający przeglądanie do aplikacji przykładowej ASP.NET](media/windows-container/asp-net-sample-app.png)

W tym artykule założono podstawową wiedzę na temat koncepcji Kubernetes. Aby uzyskać więcej informacji, zobacz [Kubernetes podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Po utworzeniu klastra, który może uruchamiać kontenery systemu Windows Server, należy dodać dodatkową pulę węzłów. Dodanie dodatkowej puli węzłów jest omówione w późniejszym kroku, ale najpierw musisz włączyć kilka funkcji w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcje AKS w wersji zapoznawczej to samoobsługowe i niezgodne. Są one dostarczane w celu zebrania opinii i usterek z naszej społeczności. W wersji zapoznawczej te funkcje nie są przeznaczone do użytku produkcyjnego. Funkcje w publicznej wersji zapoznawczej są objęte wsparciem "najlepsze wysiłki". Pomoc dla zespołów pomocy technicznej AKS jest dostępna w godzinach pracy w czasie pracy (PST). Dodatkowe informacje można znaleźć w następujących artykułach pomocy technicznej:
>
> * [Zasady pomocy technicznej AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia AKS-Preview

Aby można było korzystać z kontenerów systemu Windows Server, wymagany jest interfejs wiersza polecenia *AKS-Preview* w wersji 0.4.1 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji* zapoznawczej przy użyciu poleceń [AZ Extension Add][az-extension-add] , a następnie wyszukaj wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Funkcja rejestrowania systemu Windows w wersji zapoznawczej

Aby utworzyć klaster AKS, który może korzystać z wielu pul węzłów i uruchamiać kontenery systemu Windows Server, należy najpierw włączyć flagi funkcji *WindowsPreview* w ramach subskrypcji. Funkcja *WindowsPreview* używa również klastrów puli wielowęzłowej i zestawu skalowania maszyn wirtualnych do zarządzania wdrożeniem i konfiguracją węzłów Kubernetes. Zarejestruj flagę funkcji *WindowsPreview* za pomocą polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Wszystkie klastry AKS utworzone po pomyślnym zarejestrowaniu flagi funkcji *WindowsPreview* używają tego środowiska w wersji zapoznawczej. Aby kontynuować tworzenie regularnych, w pełni obsługiwanych klastrów, nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Użyj oddzielnej subskrypcji testowej lub deweloperskiej platformy Azure do testowania funkcji w wersji zapoznawczej.

Ukończenie rejestracji trwa kilka minut. Sprawdź stan rejestracji za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Po wybraniu `Registered`stanu rejestracji naciśnij klawisze CTRL + C, aby zatrzymać monitorowanie stanu.  Następnie Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Ograniczenia

Następujące ograniczenia są stosowane podczas tworzenia klastrów AKS i zarządzania nimi, które obsługują pule wielu węzłów:

* W przypadku klastrów utworzonych po pomyślnym zarejestrowaniu *WindowsPreview*dostępne są pule wielu węzłów. Pule wielu węzłów są również dostępne w przypadku rejestrowania funkcji *MultiAgentpoolPreview* i *VMSSPreview* dla subskrypcji. Nie można dodać pul węzłów i zarządzać nimi z istniejącym klastrem AKS utworzonym przed pomyślnym zarejestrowaniem tych funkcji.
* Nie można usunąć pierwszej puli węzłów.

Chociaż ta funkcja jest dostępna w wersji zapoznawczej, obowiązują następujące dodatkowe ograniczenia:

* Klaster AKS może mieć maksymalnie osiem pul węzłów.
* Klaster AKS może mieć maksymalnie 400 węzłów w ramach tych ośmiu pul węzłów.
* Nazwa puli węzłów systemu Windows Server ma limit 6 znaków.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Podczas tworzenia grupy zasobów użytkownik jest proszony o określenie lokalizacji. Ta lokalizacja wskazuje, gdzie są przechowywane metadane grupy zasobów, a także czy zasoby są uruchamiane na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobów. Utwórz grupę zasobów za pomocą polecenia [AZ Group Create][az-group-create] .

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

> [!NOTE]
> W tym artykule użyto składni bash dla poleceń w tym samouczku.
> Jeśli używasz Azure Cloud Shell, upewnij się, że lista rozwijana w lewym górnym rogu okna Cloud Shell jest ustawiona na **bash**.

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

Aby można było uruchomić klaster AKS, który obsługuje pule węzłów dla kontenerów systemu Windows Server, klaster musi używać zasad sieciowych, które korzystają z wtyczki sieciowej [Azure CNI][azure-cni-about] (Advanced). Aby uzyskać bardziej szczegółowe informacje ułatwiające planowanie wymaganych zakresów podsieci i zagadnień dotyczących sieci, zobacz [Konfigurowanie usługi Azure CNI Networking][use-advanced-networking]. Użyj polecenia [AZ AKS Create][az-aks-create] , aby utworzyć klaster AKS o nazwie *myAKSCluster*. To polecenie spowoduje utworzenie niezbędnych zasobów sieciowych, jeśli nie istnieją.
  * Klaster jest skonfigurowany z jednym węzłem
  * Parametry *Windows-Admin-Password* i *Windows-admin-username* ustawiają poświadczenia administratora dla wszystkich kontenerów systemu Windows Server utworzonych w klastrze.

Zapewnij własne bezpieczne *PASSWORD_WIN* (Pamiętaj, że polecenia w tym artykule są wprowadzane do powłoki bash):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.1 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

> [!Note]
> Jeśli zostanie wyświetlony komunikat o błędzie weryfikacji hasła, spróbuj utworzyć grupę zasobów w innym regionie.
> Następnie spróbuj utworzyć klaster przy użyciu nowej grupy zasobów.

Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON.

## <a name="add-a-windows-server-node-pool"></a>Dodawanie puli węzłów systemu Windows Server

Domyślnie klaster AKS jest tworzony przy użyciu puli węzłów, która może uruchamiać kontenery systemu Linux. Użyj `az aks nodepool add` polecenia, aby dodać dodatkową pulę węzłów, która może uruchamiać kontenery systemu Windows Server.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.1
```

Powyższe polecenie tworzy nową pulę węzłów o nazwie *npwin* i dodaje ją do *myAKSCluster*. Podczas tworzenia puli węzłów do uruchamiania kontenerów systemu Windows Server wartość domyślna dla *węzła Node-VM-size* to *Standard_D2s_v3*. Jeśli zdecydujesz się ustawić parametr *Node-VM-size* , sprawdź listę [rozmiarów maszyn wirtualnych][restricted-vm-sizes]z ograniczeniami. Minimalny zalecany rozmiar to *Standard_D2s_v3*. Powyższe polecenie używa również domyślnej podsieci w domyślnej sieci wirtualnej utworzonej podczas uruchamiania `az aks create`.

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Aby zarządzać klastrem Kubernetes, należy użyć [polecenia kubectl][kubectl], klienta wiersza polecenia Kubernetes. Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Aby zainstalować `kubectl` lokalnie, użyj polecenia [AZ AKS Install-CLI][az-aks-install-cli] :

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
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
aksnpwin987654                      Ready    agent   108s   v1.14.1
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Plik manifestu platformy Kubernetes definiuje żądany stan klastra, w tym informacje o obrazach kontenera do uruchomienia. W tym artykule manifest służy do tworzenia wszystkich obiektów wymaganych do uruchomienia przykładowej aplikacji ASP.NET w kontenerze systemu Windows Server. Ten manifest obejmuje [wdrożenie Kubernetes][kubernetes-deployment] dla przykładowej aplikacji ASP.NET i zewnętrznej [usługi Kubernetes][kubernetes-service] , aby uzyskać dostęp do aplikacji z Internetu.

Przykładowa aplikacja ASP.NET jest dostarczana jako część [przykładów .NET Framework][dotnet-samples] i uruchamiana w kontenerze systemu Windows Server. AKS wymaga, aby kontenery systemu Windows Server były oparte na obrazach *systemu Windows server 2019* lub nowszego. Plik manifestu Kubernetes musi także definiować [Selektor węzła][node-selector] , aby poinformować klaster AKS o konieczności uruchomienia ASP.NET przykładowej aplikacji na węźle, który może uruchamiać kontenery systemu Windows Server.

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

Wdróż aplikację przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```azurecli-interactive
kubectl apply -f sample.yaml
```

Następujące przykładowe dane wyjściowe pokazują, że wdrożenie i usługa została utworzona pomyślnie:

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

Początkowo *adres IP* dla przykładowej usługi jest pokazywany jako *oczekujący*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Gdy dla adresu *EXTERNAL-IP* wartość *oczekujący* zmieni się na rzeczywisty publiczny adres IP, naciśnij klawisze `CTRL-C`, aby zatrzymać proces śledzenia narzędzia `kubectl`. Następujące przykładowe dane wyjściowe przedstawiają prawidłowy publiczny adres IP przypisany do usługi:

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Aby wyświetlić przykładową aplikację w działaniu, Otwórz przeglądarkę internetową na zewnętrzny adres IP usługi.

![Obraz przedstawiający przeglądanie do aplikacji przykładowej ASP.NET](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Usuń klaster

Gdy klaster nie jest już wymagany, użyj polecenia [AZ Group Delete][az-group-delete] , aby usunąć grupę zasobów, usługę kontenera i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Po usunięciu klastra jednostka usługi Azure Active Directory używana przez klaster usługi AKS nie jest usuwana. Aby zapoznać się z instrukcjami dotyczącymi usuwania jednostki usługi, zobacz temat [zagadnienia i usuwanie głównej usługi AKS][sp-delete].

## <a name="next-steps"></a>Kolejne kroki

W tym artykule został wdrożony klaster Kubernetes i wdrożono przykładową aplikację ASP.NET w kontenerze systemu Windows Server. [Uzyskaj dostęp do pulpitu nawigacyjnego sieci Web Kubernetes][kubernetes-dashboard] dla właśnie utworzonego klastra.

Aby dowiedzieć się więcej o usłudze AKS i poznać dokładnie proces od kompletnego kodu do wdrożenia, przejdź do samouczka dotyczącego klastra Kubernetes.

> [!div class="nextstepaction"]
> [Samouczek AKS][aks-tutorial]

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
