---
title: Wersja zapoznawcza — używanie standardowego modułu równoważenia obciążenia jednostki SKU w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak używać modułu równoważenia obciążenia ze standardową jednostką SKU, aby udostępnić swoje usługi za pomocą usługi Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: 422189952096ef25b69e62aa2708c59385b0637a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898957"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Wersja zapoznawcza — używanie standardowego modułu równoważenia obciążenia jednostki SKU w usłudze Azure Kubernetes Service (AKS)

Aby zapewnić dostęp do aplikacji w usłudze Azure Kubernetes Service (AKS), możesz utworzyć i użyć Azure Load Balancer. Moduł równoważenia obciążenia uruchomiony w systemie AKS może być używany jako wewnętrzny lub zewnętrzny moduł równoważenia obciążenia. Wewnętrzny moduł równoważenia obciążenia sprawia, że usługa Kubernetes jest dostępna tylko dla aplikacji działających w tej samej sieci wirtualnej co klaster AKS. Zewnętrzny moduł równoważenia obciążenia otrzymuje co najmniej jeden publiczny adres IP dla ruchu przychodzącego, a usługa Kubernetes jest dostępna zewnętrznie przy użyciu publicznych adresów IP.

Azure Load Balancer jest dostępny w dwóch jednostkach SKU — *podstawowa* i *standardowa*. Domyślnie *podstawowa* jednostka SKU jest używana, gdy manifest usługi jest używany do tworzenia modułu równoważenia obciążenia na AKS. Użycie usługi równoważenia obciążenia ze standardową jednostką SKU zapewnia dodatkowe funkcje, takie jak większy rozmiar puli zaplecza i strefy dostępności. Ważne jest, aby zrozumieć różnice między standardowymi i *podstawowymi* usługami równoważenia obciążenia przed wybraniem, który ma być używany. Po utworzeniu klastra AKS nie można zmienić jednostki SKU modułu równoważenia obciążenia dla tego klastra. Aby uzyskać więcej informacji na temat *podstawowych* i *standardowych* jednostek SKU, zobacz [porównanie jednostki SKU modułu równoważenia obciążenia platformy Azure][azure-lb-comparison].

W tym artykule opisano sposób tworzenia i używania Azure Load Balancer ze *standardową* jednostką SKU przy użyciu usługi Azure Kubernetes Service (AKS).

W tym artykule założono podstawową wiedzę na temat koncepcji Kubernetes i Azure Load Balancer. Aby uzyskać więcej informacji, zobacz [Kubernetes podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts] i [co to jest Azure Load Balancer?][azure-lb].

Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Nazwa główna usługi klastra AKS wymaga uprawnień do zarządzania zasobami sieci, jeśli używana jest istniejąca podsieć lub Grupa zasobów. Ogólnie rzecz biorąc Przypisz rolę *współautor sieci* do nazwy głównej usługi w odniesieniu do zasobów delegowanych. Aby uzyskać więcej informacji o uprawnieniach, zobacz [delegowanie dostępu AKS do innych zasobów platformy Azure][aks-sp].

Należy utworzyć klaster AKS, który ustawia jednostkę SKU dla usługi równoważenia obciążenia na *Standard* zamiast domyślnej wersji *podstawowej*. Tworzenie klastra AKS jest omówione w późniejszym kroku, ale najpierw musisz włączyć kilka funkcji w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcja AKS w wersji zapoznawczej to samoobsługowe uczestnictwo. Wersje zapoznawcze są udostępniane w postaci "AS-IS" i "jako dostępne" i są wyłączone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte obsługą klienta w oparciu o najlepszy nakład pracy. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby dowiedzieć się więcej, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia AKS-Preview

Aby korzystać z standardowej jednostki SKU modułu równoważenia obciążenia platformy Azure, musisz mieć rozszerzenie interfejsu wiersza polecenia *AKS-Preview* w wersji 0.4.1 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji* zapoznawczej przy użyciu poleceń [AZ Extension Add][az-extension-add] , a następnie wyszukaj wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>Rejestrowanie funkcji AKSAzureStandardLoadBalancer w wersji zapoznawczej

Aby utworzyć klaster AKS, który może używać modułu równoważenia obciążenia ze standardową jednostką SKU, należy włączyć flagę funkcji *AKSAzureStandardLoadBalancer* w subskrypcji. Funkcja *AKSAzureStandardLoadBalancer* korzysta także z *VMSSPreview* podczas tworzenia klastra przy użyciu zestawów skalowania maszyn wirtualnych. Ta funkcja udostępnia najnowszy zestaw rozszerzeń usługi podczas konfigurowania klastra. Chociaż nie jest to wymagane, zaleca się również włączenie flagi funkcji *VMSSPreview* .

> [!CAUTION]
> Po zarejestrowaniu funkcji w ramach subskrypcji nie można obecnie wyrejestrować tej funkcji. Po włączeniu niektórych funkcji w wersji zapoznawczej można użyć wartości domyślnych dla wszystkich klastrów AKS utworzonych w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Korzystaj z oddzielnej subskrypcji, aby testować funkcje w wersji zapoznawczej i zbierać opinie.

Zarejestruj flagi funkcji *VMSSPreview* i *AKSAzureStandardLoadBalancer* za pomocą polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> Każdy klaster AKS utworzony po pomyślnym zarejestrowaniu flag funkcji *VMSSPreview* lub *AKSAzureStandardLoadBalancer* korzysta z tego środowiska klastra w wersji zapoznawczej. Aby kontynuować tworzenie regularnych, w pełni obsługiwanych klastrów, nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Użyj oddzielnej subskrypcji testowej lub deweloperskiej platformy Azure do testowania funkcji w wersji zapoznawczej.

Wyświetlenie stanu *rejestracji*może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Ograniczenia

Podczas tworzenia klastrów AKS i zarządzania nimi, które obsługują moduł równoważenia obciążenia ze standardową jednostką SKU , obowiązują następujące ograniczenia:

* W przypadku korzystania z *standardowej* jednostki SKU usługi równoważenia obciążenia należy zezwolić na publiczne adresy i uniknąć tworzenia Azure Policy, które zakazują tworzenie adresów IP. Klaster AKS automatycznie tworzy publiczny adres IP jednostki SKU w tej samej grupie zasobów utworzonej dla klastra AKS, który zwykle nosi nazwę z *MC_* na początku. AKS przypisuje publiczny adres IP do modułu równoważenia obciążenia *standardowej* jednostki SKU. Publiczny adres IP jest wymagany do zezwalania na ruch wychodzący z klastra AKS. Ten publiczny adres IP jest również wymagany do utrzymania łączności między płaszczyzną kontroli a węzłami agenta, a także w celu zachowania zgodności z poprzednimi wersjami AKS.
* W przypadku korzystania z *standardowej* jednostki SKU dla modułu równoważenia obciążenia należy użyć Kubernetes w wersji 1.13.5 lub nowszej.

Chociaż ta funkcja jest dostępna w wersji zapoznawczej, obowiązują następujące dodatkowe ograniczenia:

* W przypadku korzystania z *standardowej* jednostki SKU usługi równoważenia obciążenia w programie AKS nie można ustawić własnego publicznego adresu IP dla ruchu wychodzącego dla modułu równoważenia obciążenia. Należy użyć AKS adresów IP przypisywanych do modułu równoważenia obciążenia.
* Nie można jej używać z [funkcją publicznego adresu IP węzła](use-multiple-node-pools.md#assign-a-public-ip-per-node-in-a-node-pool).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi. Podczas tworzenia grupy zasobów użytkownik jest proszony o określenie lokalizacji. Ta lokalizacja wskazuje, gdzie są przechowywane metadane grupy zasobów, a także czy zasoby są uruchamiane na platformie Azure, jeśli nie określisz innego regionu podczas tworzenia zasobów. Utwórz grupę zasobów za pomocą polecenia [AZ Group Create][az-group-create] .

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
Aby można było uruchomić klaster AKS, który obsługuje moduł równoważenia obciążenia ze standardową jednostką SKU, klaster musi skonfigurować parametr *SKU modułu równoważenia obciążenia* do warstwy *standardowa*. Ten parametr tworzy moduł równoważenia obciążenia ze standardową jednostką SKU podczas tworzenia klastra. Po uruchomieniu usługi *równoważenia* obciążenia w klastrze konfiguracja *standardowego* programu do konfiguracji usługi jest aktualizowana wraz z konfiguracją usług. Użyj polecenia [AZ AKS Create][az-aks-create] , aby utworzyć klaster AKS o nazwie *myAKSCluster*.

> [!NOTE]
> Właściwość *SKU modułu równoważenia obciążenia* może być używana tylko podczas tworzenia klastra. Nie można zmienić jednostki SKU modułu równoważenia obciążenia po utworzeniu klastra AKS. Ponadto w pojedynczym klastrze można używać tylko jednego typu jednostki SKU modułu równoważenia obciążenia.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Po kilku minutach polecenie zostanie wykonane i zwróci informacje o klastrze w formacie JSON.

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
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.10
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Weryfikowanie, czy klaster używa *standardowej* jednostki SKU

Użyj [AZ AKS show][az-aks-show] , aby wyświetlić konfigurację klastra.

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

Sprawdź, czy właściwość *loadBalancerSku* jest wyświetlana jako *standardowa*.

## <a name="use-the-load-balancer"></a>Korzystanie z modułu równoważenia obciążenia

Aby użyć modułu równoważenia obciążenia w klastrze, należy utworzyć manifest usługi z typem usługi *równoważenia*obciążenia. Aby wyświetlić pracę modułu równoważenia obciążenia, należy utworzyć kolejny manifest z przykładową aplikacją do uruchomienia w klastrze. Ta przykładowa aplikacja jest udostępniana za pomocą modułu równoważenia obciążenia i można ją przeglądać za pomocą przeglądarki.

Utwórz manifest o nazwie `sample.yaml` , jak pokazano w następującym przykładzie:

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

Powyższy manifest konfiguruje dwa wdrożenia: *Azure-głosowa — przód* i *Azure —* zagłosuj na spód. Aby skonfigurować wdrożenie z dowolnego *głosu z platformy Azure* w celu udostępnienia przy użyciu modułu równoważenia obciążenia, Utwórz manifest `standard-lb.yaml` o nazwie, jak pokazano w następującym przykładzie:

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

Usługa *Azure-głosowa — przede* wszystkim używa typu *modułu równoważenia* obciążenia w celu skonfigurowania modułu równoważenia obciążeń w klastrze AKS w celu nawiązania połączenia z wdrożeniem na *platformie Azure* .

Wdróż przykładową aplikację i moduł równoważenia obciążenia przy użyciu [polecenia kubectl Zastosuj][kubectl-apply] i określ nazwę manifestów YAML:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

Moduł równoważenia obciążenia *standardowej* jednostki SKU jest teraz skonfigurowany do udostępniania przykładowej aplikacji. Aby wyświetlić publiczny adres IP modułu równoważenia obciążenia, zobacz szczegóły usługi *Azure — z przodu* przy użyciu [polecenia kubectl][kubectl-get] . Publiczny adres IP modułu równoważenia obciążenia jest wyświetlany w kolumnie *External-IP* . Zmiana adresu IP z  *\<\> oczekującej* na rzeczywisty zewnętrzny adres IP może potrwać minutę lub dwa, jak pokazano w następującym przykładzie:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Przejdź do publicznego adresu IP w przeglądarce i sprawdź, czy zobaczysz przykładową aplikację. W powyższym przykładzie publiczny adres IP to `52.179.23.131`.

![Obraz przedstawiający przechodzenie do aplikacji Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> Można również skonfigurować usługę równoważenia obciążenia jako wewnętrzną i nie ujawniać publicznego adresu IP. Aby skonfigurować moduł równoważenia obciążenia jako wewnętrzny, należy dodać `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` jako adnotację do usługi *modułu równoważenia* . Możesz zobaczyć przykład manifestu YAML oraz więcej szczegółów dotyczących wewnętrznego modułu równoważenia obciążenia. [][internal-lb-yaml]

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Czyszczenie konfiguracji standardowego modułu równoważenia obciążenia jednostki SKU

Aby usunąć przykładową konfigurację aplikacji i modułu równoważenia obciążenia, użyj [polecenia kubectl Delete][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach Kubernetes Services w [dokumentacji usług Kubernetes Services][kubernetes-services].

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
