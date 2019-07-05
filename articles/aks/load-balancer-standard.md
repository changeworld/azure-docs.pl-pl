---
title: W wersji zapoznawczej — Użyj modułu równoważenia obciążenia standardowej jednostki SKU w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak za pomocą modułu równoważenia obciążenia standardowej jednostki SKU udostępnić swoje usługi z usługi Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: a9cf3db3a15fab5a2f067a146950e02923a20379
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476805"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>W wersji zapoznawczej — Użyj modułu równoważenia obciążenia standardowej jednostki SKU w usłudze Azure Kubernetes Service (AKS)

Aby zapewnić dostęp do aplikacji w usłudze Azure Kubernetes Service (AKS), można tworzyć i używać usługi Azure Load Balancer. Uruchamianie w usłudze AKS modułu równoważenia obciążenia może służyć jako wewnętrzny lub zewnętrzny moduł równoważenia obciążenia. Wewnętrzny moduł równoważenia obciążenia sprawia, że usługa Kubernetes jest dostępny tylko dla aplikacji działających w tej samej sieci wirtualnej jako klaster AKS. Zewnętrznym modułem równoważenia obciążenia odbiera co najmniej jeden publicznych adresów IP dla ruchu przychodzącego i sprawia, że usługa Kubernetes jest dostępna zewnętrznie przy użyciu publicznych adresów IP.

Równoważenie obciążenia Azure jest dostępna w dwóch jednostkach SKU - *podstawowe* i *standardowa*. Domyślnie *podstawowe* jednostki SKU jest używany, gdy manifestu usługi służy do tworzenia modułu równoważenia obciążenia w usłudze AKS. Za pomocą *standardowa* moduł równoważenia obciążenia jednostki SKU zapewnia dodatkowe funkcje i możliwości, takie jak większy rozmiar puli zaplecza i strefy dostępności. Jest ważne, aby zrozumieć różnice między *standardowa* i *podstawowe* moduły równoważenia obciążenia przed wybraniem, której mają zostać użyte. Po utworzeniu klastra usługi AKS, nie można zmienić modułu równoważenia obciążenia jednostki SKU dla tego klastra. Aby uzyskać więcej informacji na temat *podstawowe* i *standardowa* jednostek SKU, zobacz [porównania jednostki SKU modułu równoważenia obciążenia platformy Azure][azure-lb-comparison].

W tym artykule przedstawiono sposób tworzenia i używania usługi Azure Load Balancer z *standardowa* jednostki SKU za pomocą usługi Azure Kubernetes Service (AKS).

W tym artykule założono podstawową wiedzę na temat pojęć usługi Kubernetes i Azure Load Balancer. Aby uzyskać więcej informacji, zobacz [Kubernetes podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts] and [What is Azure Load Balancer?][azure-lb].

Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jednostki usługi Klaster AKS wymaga zgody na zarządzanie zasobami sieciowymi, jeśli używasz istniejącej podsieci lub grupy zasobów. Ogólnie rzecz biorąc, Przypisz *Współautor sieci* roli do jednostki usługi dla delegowanego zasobów. Aby uzyskać więcej informacji na temat uprawnień, zobacz [AKS delegowanego dostępu do innych zasobów platformy Azure][aks-sp].

Należy utworzyć klaster usługi AKS, która ustawia jednostkę SKU modułu równoważenia obciążenia do *standardowa* zamiast domyślnego *podstawowe*. Tworzenie klastra AKS został omówiony w dalszej części, ale najpierw należy włączyć kilka funkcji w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są samoobsługi, uczestnictwo. Są one udostępniane do zbierania opinii i błędy z naszej społeczności. W wersji zapoznawczej te funkcje nie są przeznaczone do użytku produkcyjnego. Funkcje w wersji zapoznawczej objęte "starań" pomocy technicznej. Pomoc od zespołów pomocy technicznej usługi AKS jest dostępna w godzinach pracy i Pacyfik, część strefy czasowej (PST) tylko. Aby uzyskać więcej informacji zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej usługi AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia w wersji zapoznawczej usługi aks

Przy użyciu modułu równoważenia obciążenia platformy Azure standardowej jednostki SKU, potrzebujesz *podglądu usługi aks* interfejsu wiersza polecenia wersja rozszerzenia 0.4.1 lub nowszej. Zainstaluj *podglądu usługi aks* rozszerzenie interfejsu wiersza polecenia platformy Azure przy użyciu polecenia [Dodaj rozszerzenie az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] polecenia::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>Zarejestruj funkcję w wersji zapoznawczej AKSAzureStandardLoadBalancer

Aby utworzyć klaster usługi AKS, można użyć równoważenia obciążenia z *standardowa* jednostki SKU, należy włączyć *AKSAzureStandardLoadBalancer* funkcji flagi w ramach subskrypcji. *AKSAzureStandardLoadBalancer* funkcji również używa *VMSSPreview* podczas tworzenia klastra przy użyciu zestawów skalowania maszyn wirtualnych. Ta funkcja zapewnia najnowszy zestaw ulepszeń usługi, podczas konfigurowania klastra. Chociaż nie jest to wymagane, zaleca się włączeniu *VMSSPreview* także flagi funkcji.

> [!CAUTION]
> Po zarejestrowaniu funkcji w ramach subskrypcji, nie można obecnie wyrejestrować tę funkcję. Po włączeniu niektóre funkcje w wersji zapoznawczej, wartości domyślne mogą służyć dla wszystkich klastrów usługi AKS, które następnie są tworzone w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji w środowisku produkcyjnym. Aby przetestować funkcje w wersji zapoznawczej i zbieranie opinii, należy użyć oddzielnej subskrypcji.

Zarejestruj *VMSSPreview* i *AKSAzureStandardLoadBalancer* przy użyciu flagi funkcji [az feature register][az-feature-register] polecenia, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> Każdy klaster AKS możesz utworzyć po pomyślnym zarejestrowaniu *VMSSPreview* lub *AKSAzureStandardLoadBalancer* flag funkcji, użyj tego środowiska klastra w wersji zapoznawczej. Aby kontynuować tworzenie klastrów regularnych, w pełni obsługiwane, nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji w środowisku produkcyjnym. Użyj oddzielnych testowym lub deweloperskim subskrypcji platformy Azure do testowania funkcji w wersji zapoznawczej.

Zajmuje kilka minut, zanim stan wyświetlany *zarejestrowanej*. Można sprawdzić stan rejestracji przy użyciu [lista funkcji az][az-feature-list] polecenia:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestracji *Microsoft.ContainerService* dostawcę zasobów przy użyciu [az provider register][az-provider-register] polecenia:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Ograniczenia

Poniższe ograniczenia mają zastosowanie podczas tworzenia i zarządzania klastrami usługi AKS, które obsługują moduł równoważenia obciążenia z *standardowa* jednostki SKU:

* Korzystając z *standardowa* jednostki SKU dla modułu równoważenia obciążenia, musisz zezwolić na publiczne adresy i unikanie tworzenia żadnych zasad platformy Azure, która zakazuje tworzenia adresu IP. Automatycznie tworzy klaster AKS *standardowa* jednostki SKU publicznego adresu IP w tej samej grupie zasobów utworzonych dla klastra usługi AKS, który zwykle o nazwie *MC_* na początku. AKS przypisuje publicznego adresu IP *standardowa* jednostki SKU modułu równoważenia obciążenia. Publiczny adres IP jest wymagany dla umożliwiać ruch wychodzący z klastra usługi AKS. Ten publiczny adres IP jest również wymagane do obsługi łączności między kontroli płaszczyzny i węzłów agentów oraz aby utrzymać zgodność z poprzednimi wersjami usługi AKS.
* Korzystając z *standardowa* jednostki SKU dla usługi równoważenia obciążenia, należy użyć wersji pytanie 1.13.5 Kubernetes lub nowszej.

Chociaż ta funkcja jest dostępna w wersji zapoznawczej, następujące dodatkowe ograniczenia:

* Korzystając z *standardowa* jednostki SKU dla modułu równoważenia obciążenia w usłudze AKS i nie można ustawić własnego publicznego adresu IP dla ruchu wychodzącego dla modułu równoważenia obciążenia. Należy użyć adresu IP, który AKS przypisuje do modułu równoważenia obciążenia.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Podczas tworzenia grupy zasobów użytkownik jest proszony o określenie lokalizacji. Ta lokalizacja jest, gdzie przechowywane są metadane grupy zasobów, jest również, gdy zasoby uruchamianie na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobu. Utwórz grupę zasobów za pomocą [Tworzenie grupy az][az-group-create] polecenia.

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
Aby można było uruchomić klaster AKS, obsługującego usługę równoważenia obciążenia za pomocą *standardowa* jednostki SKU, klaster musi ustawić *obciążenia balancer-sku* parametr *standardowa*. Ten parametr tworzy moduł równoważenia obciążenia z *standardowa* jednostki SKU po utworzeniu klastra. Po uruchomieniu *modułu równoważenia obciążenia* usługi w klastrze, konfiguracja *standardowa* modułu równoważenia obciążenia SK będą aktualizowane przy użyciu konfiguracji usługi. Użyj [tworzenie az aks][az-aks-create] polecenie, aby utworzyć klaster usługi AKS, o nazwie *myAKSCluster*.

> [!NOTE]
> *Obciążenia balancer-sku* właściwość może być używana tylko podczas tworzenia klastra. Jednostka SKU modułu równoważenia obciążenia nie można zmienić po utworzeniu klastra usługi AKS. Ponadto można użyć tylko jednego typu usługi równoważenia obciążenia jednostek SKU w jednym klastrze.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --kubernetes-version 1.14.0 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON.

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
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.14.0
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Sprawdź w klastrze jest używany *standardowa* jednostki SKU

Użyj [az aks show][az-aks-show] Wyświetla konfigurację klastra.

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

Sprawdź *loadBalancerSku* właściwości jest wyświetlana jako *standardowa*.

## <a name="use-the-load-balancer"></a>Używanie usługi równoważenia obciążenia

Aby użyć modułu równoważenia obciążenia w klastrze, należy utworzyć manifestu usługi z typem usługi *modułu równoważenia obciążenia*. Aby przedstawić modułu równoważenia obciążenia, praca, należy utworzyć inny manifest przy użyciu przykładowej aplikacji do uruchamiania w klastrze. Ta przykładowa aplikacja jest dostępna za pośrednictwem modułu równoważenia obciążenia i mogą być wyświetlane za pośrednictwem przeglądarki.

Tworzenie manifestu o nazwie `sample.yaml` jak pokazano w poniższym przykładzie:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
```

Manifest powyżej konfiguruje dwa wdrożenia: *azure-vote-front* i *azure vote zwrotnego*. Aby skonfigurować *azure-vote-front* wdrożenia ujawnianie przy użyciu usługi równoważenia obciążenia, tworzenie manifestu o nazwie `standard-lb.yaml` jak pokazano w poniższym przykładzie:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Usługa *azure-vote-front* używa *modułu równoważenia obciążenia* typu do skonfigurowania modułu równoważenia obciążenia w klastrze usługi AKS, aby nawiązać połączenie *azure-vote-front* wdrożenia.

Wdrażanie przykładowej aplikacji i załadować równoważenia przy użyciu [zastosować kubectl][kubectl-apply] i określ nazwę usługi manifesty YAML:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

*Standardowa* modułu równoważenia obciążenia jednostki SKU jest teraz skonfigurowane do udostępnienia przykładowej aplikacji. Wyświetl szczegóły usługi *azure-vote-front* przy użyciu [kubectl get-][kubectl-get] Aby wyświetlić publiczny adres IP modułu równoważenia obciążenia. Publiczny adres IP modułu równoważenia obciążenia jest wyświetlana w *EXTERNAL-IP* kolumny. Może potrwać minutę lub dwie dla adresu IP do zmiany z *\<oczekujące\>* rzeczywiste zewnętrzny adres IP, jak pokazano w poniższym przykładzie:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Przejdź do publicznego adresu IP w przeglądarce i sprawdź, czy widzisz przykładowej aplikacji. W powyższym przykładzie jest publiczny adres IP `52.179.23.131`.

![Obraz przedstawiający przechodzenie do aplikacji Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> Można również skonfigurować moduł równoważenia obciążenia jako wewnętrzny i ujawnia publiczny adres IP. Aby skonfigurować moduł równoważenia obciążenia jako wewnętrzne, Dodaj `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` jako adnotację *modułu równoważenia obciążenia* usługi. Możesz zobaczyć yaml przykład manifestu, a także jak więcej szczegółów na temat wewnętrznego modułu równoważenia obciążenia [tutaj][internal-lb-yaml].

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Wyczyść konfigurację modułu równoważenia obciążenia standardowej jednostki SKU

Aby usunąć Przykładowa konfiguracja usługi równoważenia aplikacji i obciążenia, użyj [Usuń kubectl][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi Kubernetes na [Kubernetes usługi dokumentacji][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
