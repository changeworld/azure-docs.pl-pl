---
title: Używanie Strefy dostępności w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć klaster, który dystrybuuje węzły w strefach dostępności w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: mlearned
ms.openlocfilehash: 690d22eadf37a24b4679ce10838074533ac65fcb
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390067"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Wersja zapoznawcza — Tworzenie klastra usługi Azure Kubernetes Service (AKS), który używa Strefy dostępności

Klaster usługi Azure Kubernetes Service (AKS) dystrybuuje zasoby, takie jak węzły i magazyn, w logicznych sekcjach podstawowej infrastruktury obliczeniowej platformy Azure. Ten model wdrażania zapewnia, że węzły są uruchamiane w oddzielnych domenach aktualizacji i błędów w jednym centrum danych platformy Azure. Klastry AKS wdrożone z tym domyślnym zachowaniem zapewniają wysoki poziom dostępności do ochrony przed awarią sprzętową lub zaplanowanym zdarzeniem konserwacji.

Aby zapewnić wyższy poziom dostępności aplikacji, klastry AKS mogą być dystrybuowane między strefami dostępności. Te strefy są fizycznie oddzielone centrami danych w danym regionie. Gdy składniki klastra są rozproszone w wielu strefach, klaster AKS może tolerować awarię w jednej z tych stref. Aplikacje i operacje zarządzania są nadal dostępne nawet wtedy, gdy w całym centrum danych wystąpił problem.

W tym artykule pokazano, jak utworzyć klaster AKS i rozesłać składniki węzła w strefach dostępności. Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcja AKS w wersji zapoznawczej to samoobsługowe uczestnictwo. Wersje zapoznawcze są udostępniane w postaci "AS-IS" i "jako dostępne" i są wyłączone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte obsługą klienta w oparciu o najlepszy nakład pracy. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby dowiedzieć się więcej, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.66 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia AKS-Preview

Aby utworzyć klastry AKS korzystające ze stref dostępności, potrzebujesz rozszerzenia interfejsu wiersza polecenia *AKS-Preview* w wersji 0.4.1 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej* przy użyciu poleceń [AZ Extension Add][az-extension-add] , a następnie wyszukaj wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-availabilityzonepreview-feature-flag-for-your-subscription"></a>Zarejestruj flagę funkcji AvailabilityZonePreview dla subskrypcji

Aby utworzyć klaster AKS z strefami dostępności, najpierw włącz flagę funkcji *AvailabilityZonePreview* w subskrypcji. Zarejestruj flagę funkcji *AvailabilityZonePreview* za pomocą polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

> [!CAUTION]
> Po zarejestrowaniu funkcji w ramach subskrypcji nie można obecnie wyrejestrować tej funkcji. Po włączeniu niektórych funkcji w wersji zapoznawczej można użyć wartości domyślnych dla wszystkich klastrów AKS utworzonych w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Korzystaj z oddzielnej subskrypcji, aby testować funkcje w wersji zapoznawczej i zbierać opinie.

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
```

Wyświetlenie stanu *rejestracji*może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>Ograniczenia i dostępność regionów

Klastry AKS można obecnie tworzyć przy użyciu stref dostępności w następujących regionach:

* Wschodnie stany USA 2
* Europa Północna
* Azja Południowo-wschodnia
* Europa Zachodnia
* Zachodnie stany USA 2

Podczas tworzenia klastra AKS przy użyciu stref dostępności są stosowane następujące ograniczenia:

* Strefy dostępności można włączyć tylko podczas tworzenia klastra.
* Nie można zaktualizować ustawień strefy dostępności po utworzeniu klastra. Nie można również zaktualizować istniejącego klastra strefowego niebędącego dostępnością do korzystania ze stref dostępności.
* Nie można wyłączyć stref dostępności dla klastra AKS po jego utworzeniu.
* Wybrany rozmiar węzła (jednostki SKU maszyny wirtualnej) musi być dostępny we wszystkich strefach dostępności.
* Klastry z włączonymi strefami dostępności wymagają użycia usługi równoważenia obciążenia w warstwie Standardowa na potrzeby dystrybucji między strefami.
* Aby wdrożyć usługi równoważenia obciążenia w warstwie Standardowa, należy użyć Kubernetes w wersji 1.13.5 lub nowszej.

Klastry AKS korzystające ze stref dostępności muszą używać *standardowej* jednostki SKU modułu równoważenia obciążenia platformy Azure. Domyślna *podstawowa* jednostka SKU modułu równoważenia obciążenia platformy Azure nie obsługuje dystrybucji w strefach dostępności. Aby uzyskać więcej informacji i ograniczeń dotyczących standardowego modułu równoważenia obciążenia, zobacz [ograniczenia dotyczące standardowej jednostki SKU modułu równoważenia obciążenia platformy Azure][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Ograniczenia dotyczące dysków platformy Azure

Woluminy korzystające z usługi Azure Managed disks nie są obecnie zasobami stref. W przypadku zmiany planu na podstawie pierwotnej strefy w innej strefie nie można ponownie dołączyć poprzednich dysków. Zaleca się uruchamianie bezstanowych obciążeń, które nie wymagają trwałego magazynu, który może występować w przypadku problemów z strefą.

Jeśli konieczne jest uruchamianie obciążeń stanowych, należy użyć przystawek i tolerowania w danych ze specyfikacją, aby poinformować usługę Kubernetes Scheduler o utworzeniu zasobników w tej samej strefie co dyski. Alternatywnie możesz korzystać z magazynu opartego na sieci, takiego jak Azure Files, które można dołączyć do zasobników, gdy są one planowane między strefami.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Omówienie Strefy dostępności klastrów AKS

Strefy dostępności to oferta wysokiej dostępności, która chroni Twoje aplikacje i dane przed awariami centrów danych. Strefy są unikatowymi lokalizacjami fizycznymi w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jeden centrów danych, wyposażone w niezależne zasilanie, chłodzenie i usługi sieciowe. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Fizyczna separacja stref dostępności w ramach regionu chroni aplikacje i dane przed awariami centrum danych. Usługi strefowo nadmiarowe replikujeją aplikacje i dane między Strefy dostępności, aby chronić je przed awariami jednego punktu.

Aby uzyskać więcej informacji, zobacz [co to jest strefy dostępności na platformie Azure?][az-overview]

Klastry AKS wdrożone przy użyciu stref dostępności umożliwiają dystrybucję węzłów między wieloma strefami w jednym regionie. Na przykład klaster w regionie *Wschodnie stany USA 2* może tworzyć węzły we wszystkich trzech strefach dostępności w *regionach Wschodnie stany USA 2*. Ta dystrybucja zasobów klastra AKS zwiększa dostępność klastra w miarę odporności na awarię określonej strefy.

![Rozkład węzłów AKS w różnych strefach dostępności](media/availability-zones/aks-availability-zones.png)

W przypadku awarii strefy węzły można ponownie zrównoważyć ręcznie lub przy użyciu automatycznego skalowania klastra. Jeśli jedna strefa będzie niedostępna, aplikacje będą nadal działać.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Tworzenie klastra AKS w różnych strefach dostępności

W przypadku tworzenia klastra przy użyciu polecenia [AZ AKS Create][az-aks-create] `--node-zones` parametr określa, które węzły agenta stref są wdrożone w programie. Składniki płaszczyzny kontroli AKS dla klastra są również rozłożone między strefy w najwyższej dostępnej konfiguracji podczas tworzenia klastra określającego `--node-zones` parametr.

Jeśli nie zdefiniowano żadnych stref dla domyślnej puli agentów podczas tworzenia klastra AKS, składniki płaszczyzny kontroli AKS dla klastra nie będą używać stref dostępności. Można dodać dodatkowe pule węzłów (obecnie w wersji zapoznawczej w AKS) przy użyciu polecenia [AZ AKS nodepool Add][az-aks-nodepool-add] i określić `--node-zones` dla tych nowych węzłów agenta, jednak składniki płaszczyzny kontroli pozostają bez świadomości strefy dostępności. Po wdrożeniu nie można zmienić świadomości strefy dla puli węzłów ani składników płaszczyzny kontroli AKS.

Poniższy przykład tworzy klaster AKS o nazwie *myAKSCluster* w grupie zasobów o nazwie Moja *resourceName*. Łącznie *3* węzły są tworzone — jeden Agent w strefie *1*, jeden w *2*, a następnie jeden w *3*. Składniki płaszczyzny kontroli AKS są również dystrybuowane między strefami w najwyższej dostępnej konfiguracji, ponieważ są one definiowane jako część procesu tworzenia klastra.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
```

Utworzenie klastra AKS może potrwać kilka minut.

## <a name="verify-node-distribution-across-zones"></a>Weryfikowanie dystrybucji węzłów między strefami

Gdy klaster jest gotowy, Utwórz listę węzłów agenta w zestawie skalowania, aby zobaczyć, w jakiej strefie dostępności są one wdrażane.

Najpierw pobierz poświadczenia klastra AKS za pomocą polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Następnie użyj polecenia [polecenia kubectl opisywania][kubectl-describe] , aby wyświetlić listę węzłów w klastrze. Odfiltruj wartość *Failure-Domain.beta.Kubernetes.IO/Zone* , jak pokazano w następującym przykładzie:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Poniższe przykładowe dane wyjściowe przedstawiają trzy węzły rozproszone w określonym regionie i strefach dostępności, takie jak *eastus2-1* dla pierwszej strefy dostępności i *eastus2-2* dla drugiej strefy dostępności:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Podczas dodawania kolejnych węzłów do puli agentów platforma Azure automatycznie dystrybuuje bazowe maszyny wirtualne w określonych strefach dostępności.

## <a name="next-steps"></a>Następne kroki

W tym artykule szczegółowo opisano sposób tworzenia klastra AKS używającego stref dostępności. Aby uzyskać więcej informacji na temat klastrów o wysokiej dostępności, zobacz [najlepsze rozwiązania dotyczące ciągłości działania i odzyskiwania po awarii w programie AKS][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
