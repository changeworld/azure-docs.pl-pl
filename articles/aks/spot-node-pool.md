---
title: Podgląd — dodawanie puli węzłów punktowych do klastra usługi Azure Kubernetes (AKS)
description: Dowiedz się, jak dodać pulę węzłów punktowych do klastra usługi Azure Kubernetes (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.author: zarhoads
ms.openlocfilehash: 466ad7c88547b6676ba0ae263b74d14059322f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622039"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Podgląd — dodawanie puli węzłów punktowych do klastra usługi Azure Kubernetes (AKS)

Pula węzłów punktowych to pula węzłów, wspierana przez [zestaw skalowania maszyny wirtualnej punktowej][vmss-spot]. Korzystanie z maszyn wirtualnych punktowych dla węzłów z klastrem AKS umożliwia korzystanie z niewykorzystanej pojemności na platformie Azure przy znacznych oszczędnościach kosztów. Ilość dostępnych niewykorzystanych zdolności produkcyjnych będzie się różnić w zależności od wielu czynników, w tym rozmiaru węzła, regionu i porą dnia.

Podczas wdrażania puli węzłów punktowych platforma Azure przydzieli węzły punktowe, jeśli dostępna jest pojemność. Ale nie ma umowy SLA dla węzłów punktowych. Zestaw skalowania punktowego, który cofa pulę węzłów punktowych jest wdrażany w jednej domenie błędów i nie oferuje gwarancji wysokiej dostępności. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktury platformy Azure będzie eksmitować węzłów punktowych.

Węzły punktowe doskonale nadają się do obciążeń, które mogą obsługiwać przerwy, wcześniejsze zakończenia lub eksmisje. Na przykład obciążeń, takich jak zadania przetwarzania wsadowego, środowiska programowania i testowania i dużych obciążeń obliczeniowych może być dobrymi kandydatami do zaplanowania w puli węzłów punktowych.

W tym artykule należy dodać pomocniczą pulę węzłów punktowych do istniejącego klastra usługi Azure Kubernetes Service (AKS).

W tym artykule przyjęto podstawową wiedzę o pojęciach platformy Kubernetes i Azure Load Balancer. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts].

Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Podczas tworzenia klastra do używania puli węzłów punktowych, ten klaster musi również używać zestawów skalowania maszyny wirtualnej dla pul węzłów i *standardowego* modułu równoważenia obciążenia jednostki SKU. Należy również dodać dodatkową pulę węzłów po utworzeniu klastra, aby użyć puli węzłów punktowych. Dodanie dodatkowej puli węzłów jest omówione w późniejszym kroku, ale najpierw należy włączyć funkcję podglądu.

> [!IMPORTANT]
> Funkcje podglądu usługi AKS to samoobsługowe, opt-in. Są one dostarczane w celu zebrania opinii i błędów od naszej społeczności. W wersji zapoznawczej te funkcje nie są przeznaczone do użytku w wersji produkcyjnej. Funkcje w publicznej wersji zapoznawczej są objęte wsparciem "najlepszego wysiłku". Pomoc zespołów pomocy technicznej AKS jest dostępna tylko w godzinach pracy strefy czasowej Pacyfiku (PST). Aby uzyskać dodatkowe informacje, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady wsparcia AKS][aks-support-policies]
> * [Często zadawane pytania dotyczące pomocy technicznej platformy Azure][aks-faq]

### <a name="register-spotpoolpreview-preview-feature"></a>Rejestrowanie funkcji podglądu spotpoolpreview

Aby utworzyć klaster usługi AKS, który używa puli węzłów punktowych, należy włączyć flagę funkcji *spotpoolpreview* w ramach subskrypcji. Ta funkcja zapewnia najnowszy zestaw ulepszeń usługi podczas konfigurowania klastra.

> [!CAUTION]
> Podczas rejestrowania funkcji w ramach subskrypcji nie można obecnie odeszło od rejestracji tej funkcji. Po włączeniu niektórych funkcji w wersji zapoznawczej ustawienia domyślne mogą być używane dla wszystkich klastrów AKS utworzonych w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w subskrypcjach produkcyjnych. Użyj oddzielnej subskrypcji, aby przetestować funkcje w wersji zapoznawczej i zebrać opinie.

Zarejestruj flagę funkcji *spotpoolpreview* za pomocą polecenia [az feature register,][az-feature-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

Trwa kilka minut, aby stan pokazał *zarejestrowany*. Stan rejestracji można sprawdzić za pomocą polecenia [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

Gdy będzie to gotowe, odśwież rejestrację dostawcy zasobów *Microsoft.ContainerService* za pomocą polecenia [rejestru dostawcy az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby utworzyć klaster usługi AKS, który używa puli węzłów punktowych, potrzebne jest rozszerzenie interfejsu wiersza polecenia *aks-preview* w wersji 0.4.32 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure *aks-preview* przy użyciu polecenia [dodawania rozszerzenia az,][az-extension-add] a następnie sprawdź dostępność dostępnych aktualizacji za pomocą polecenia [aktualizacji rozszerzenia az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Ograniczenia

Podczas tworzenia klastrów AKS i zarządzania nimi z pulą węzłów punktowych obowiązują następujące ograniczenia:

* Pula węzłów punktowych nie może być domyślną pulą węzłów klastra. Pula węzłów punktowych może być używana tylko dla puli dodatkowej.
* Nie można uaktualnić puli węzłów punktowych, ponieważ pule węzłów punktowych nie mogą zagwarantować kordonu i drenażu. Należy zastąpić istniejącą pulę węzłów punktowych nową, aby wykonać operacje, takie jak uaktualnianie wersji usługi Kubernetes. Aby zastąpić pulę węzłów punktowych, utwórz nową pulę węzłów punktowych z inną wersją aplikacji Kubernetes, poczekaj, aż jej stan będzie *gotowy,* a następnie usuń starą pulę węzłów.
* Pul płaszczyzny sterowania i węzłów nie można uaktualnić w tym samym czasie. Należy uaktualnić je oddzielnie lub usunąć pulę węzłów punktowych, aby uaktualnić płaszczyznę sterowania i pozostałe pule węzłów w tym samym czasie.
* Pula węzłów punktowych musi używać zestawów skalowania maszyny wirtualnej.
* Po utworzeniu nie można zmienić scalesetpriority ani SpotMaxPrice.
* Podczas ustawiania SpotMaxPrice wartość musi wynosić -1 lub wartość dodatnią z maksymalnie pięcioma miejscami po przecinku.
* Pula węzłów punktowych będzie miała *etykietę kubernetes.azure.com/scalesetpriority:spot*, *skazy kubernetes.azure.com/scalesetpriority=spot:NoSchedule,* a zasobniki systemowe będą miały antykowincje.
* Należy dodać [odpowiednią tolerancję,][spot-toleration] aby zaplanować obciążenia w puli węzłów punktowych.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Dodawanie puli węzłów punktowych do klastra AKS

Należy dodać pulę węzłów punktowych do istniejącego klastra, który ma włączone puli wielu węzłów. Więcej szczegółów na temat tworzenia klastra AKS z wieloma pulami węzłów można znaleźć [tutaj][use-multiple-node-pools].

Utwórz pulę węzłów przy użyciu [az aks nodepool add][az-aks-nodepool-add].
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Domyślnie podczas tworzenia klastra z wieloma pulami węzłów należy utworzyć pulę węzłów o *priorytecie Regular* w klastrze AKS. *priority* Powyższe polecenie dodaje pomocniczą pulę węzłów do istniejącego klastra AKS o *priorytecie* *Spot*. *Priorytet* *Spot* sprawia, że pula węzłów puli węzłów. Parametr *zasady eksmisji* jest ustawiony na *Usuń* w powyższym przykładzie, który jest wartością domyślną. Po ustawieniu [zasady eksmisji][eviction-policy] na *Usuń,* węzły w podstawowym zestawie skali puli węzłów są usuwane po ich eksmisji. Można również ustawić zasady eksmisji na *Deallocate*. Po ustawieniu zasad eksmisji na *Deallocate,* węzły w podstawowym zestawie skalowania są ustawione na stan zatrzymanego przydzielonego po eksmisji. Węzły w stanie zatrzymano-cofnięto alokację są wliczane do przydziału obliczeniowego i mogą powodować problemy ze skalowaniem lub uaktualnianiem klastra. Wartości *zasad priorytetu* i *eksmisji* można ustawić tylko podczas tworzenia puli węzłów. Tych wartości nie można zaktualizować później.

Polecenie włącza również [skalowanie automatyczne klastra,][cluster-autoscaler]który jest zalecany do użycia z pulami węzłów punktowych. Na podstawie obciążeń uruchomionych w klastrze skalowanie automatyczne klastra skaluje w górę i skaluje w dół liczbę węzłów w puli węzłów. W przypadku pul węzłów punktowych skalowanie automatyczne klastra spowoduje skalowanie w górę liczby węzłów po eksmisji, jeśli nadal potrzebne są dodatkowe węzły. Jeśli zmienisz maksymalną liczbę węzłów, które może mieć pula węzłów, należy również dostosować `maxCount` wartość skojarzoną z skalowaniem automatycznym klastra. Jeśli nie używasz skalowania automatycznego klastra, po eksmisji pula punktowa ostatecznie zmniejszy się do zera i wymaga ręcznej operacji, aby otrzymać dodatkowe węzły punktowe.

> [!Important]
> Zaplanuj tylko obciążenia w pulach węzłów miejscu, które mogą obsługiwać przerwy, takie jak zadania przetwarzania wsadowego i środowiska testowe. Zaleca się skonfigurowanie [taints i tolerancji][taints-tolerations] w puli węzłów miejscu, aby upewnić się, że tylko obciążenia, które mogą obsługiwać eksmisji węzłów są zaplanowane w puli węzłów miejscu. Na przykład powyższe polecenie ny default dodaje skazę *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* więc tylko zasobniki z odpowiednią tolerancją są zaplanowane w tym węźle.

## <a name="verify-the-spot-node-pool"></a>Sprawdzanie puli węzłów punktowych

Aby sprawdzić, czy pula węzłów została dodana jako pula węzłów punktowych:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Potwierdź *skalSetPriority* jest *spot*.

Aby zaplanować uruchomienie zasobnika w węźle punktowym, dodaj tolerancję odpowiadającą skazom zastosowanym do węzła punktowego. Poniższy przykład przedstawia część pliku yaml, który definiuje tolerancję, która odpowiada *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* taint używane w poprzednim kroku.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Po wdrożeniu zasobnika z tą tolerancją, kubernetes można pomyślnie zaplanować zasobnika w węzłach z taint zastosowane.

## <a name="max-price-for-a-spot-pool"></a>Maksymalna cena za basen punktowy
[Ceny dla wystąpień kasowych są zmienne][pricing-spot], na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz ceny dla [systemów Linux][pricing-linux] i [Windows][pricing-windows].

W przypadku zmiennych cen możesz ustawić cenę maksymalną w dolarach amerykańskich (USD), używając do 5 miejsc po przecinku. Na przykład wartość *0.98765* będzie maksymalna cena $0.98765 USD za godzinę. Jeśli ustawisz cenę maksymalną na *-1,* wystąpienie nie zostanie eksmitowane na podstawie ceny. Cena dla wystąpienia będzie bieżącą ceną spotu lub ceną dla standardowego wystąpienia, w zależności od tego, która z tych wartości jest niższa, o ile dostępna jest zdolność produkcyjna i kwota.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak dodać pulę węzłów punktowych do klastra AKS. Aby uzyskać więcej informacji na temat kontrolowania zasobników w pulach węzłów, zobacz [Najważniejsze wskazówki dotyczące zaawansowanych funkcji harmonogramu w ustroju AKS][operator-best-practices-advanced-scheduler].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deploy-create]: /cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-template-deploy]: ../azure-resource-manager/templates/deploy-cli.md#deployment-scope
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md