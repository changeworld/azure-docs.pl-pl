---
title: Używanie stref dostępności w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć klaster, który dystrybuuje węzły w strefach dostępności w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596814"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Tworzenie klastra usługi Azure Kubernetes (AKS), który używa stref dostępności

Klaster usługi Azure Kubernetes (AKS) dystrybuuje zasoby, takie jak węzły i magazyn, w logicznych sekcjach podstawowej infrastruktury obliczeniowej platformy Azure. Ten model wdrażania zapewnia, że węzły są uruchamiane w oddzielnych domenach aktualizacji i błędów w jednym centrum danych platformy Azure. Klastry usługi AKS wdrożone z tym domyślnym zachowaniem zapewniają wysoki poziom dostępności w celu ochrony przed awarią sprzętu lub zdarzeniem planowanej konserwacji.

Aby zapewnić wyższy poziom dostępności aplikacji, klastry AKS mogą być dystrybuowane między strefami dostępności. Strefy te są fizycznie oddzielne centra danych w danym regionie. Gdy składniki klastra są rozmieszczone w wielu strefach, klaster usługi AKS jest w stanie tolerować błąd w jednej z tych stref. Operacje zarządzania aplikacjami i zarządzaniem są nadal dostępne, nawet jeśli wystąpił problem w jednym całym centrum danych.

W tym artykule pokazano, jak utworzyć klaster usługi AKS i rozmieścić składniki węzła w strefach dostępności.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz zainstalowanego i skonfigurowany interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Ograniczenia i dostępność regionów

Klastry AKS można obecnie tworzyć przy użyciu stref dostępności w następujących regionach:

* Środkowe stany USA
* Wschodnie stany USA 2
* Wschodnie stany USA
* Francja Środkowa
* Japonia Wschodnia
* Europa Północna
* Azja Południowo-Wschodnia
* Południowe Zjednoczone Królestwo
* Europa Zachodnia
* Zachodnie stany USA 2

Podczas tworzenia klastra AKS przy użyciu stref dostępności obowiązują następujące ograniczenia:

* Strefy dostępności można włączyć tylko podczas tworzenia klastra.
* Nie można zaktualizować ustawień strefy dostępności po utworzeniu klastra. Nie można również zaktualizować istniejącego klastra stref niedostępnych, aby używać stref dostępności.
* Po jego utworzeniu nie można wyłączyć stref dostępności klastra AKS.
* Wybrany rozmiar węzła (jednostka SKU maszyny Wirtualnej) musi być dostępny we wszystkich strefach dostępności.
* Klastry z włączonymi strefami dostępności wymagają użycia standardowych modułów równoważenia obciążenia platformy Azure do dystrybucji między strefami.
* Aby wdrożyć standardowe moduły równoważenia obciążenia, należy użyć programu Kubernetes w wersji 1.13.5 lub nowszej.

Klastry usługi AKS korzystające ze stref dostępności muszą używać *standardowej jednostki* SKU modułu równoważenia obciążenia platformy Azure, która jest wartością domyślną dla typu modułu równoważenia obciążenia. Ten typ modułu równoważenia obciążenia można zdefiniować tylko w czasie tworzenia klastra. Aby uzyskać więcej informacji i ograniczenia standardowego modułu równoważenia obciążenia, zobacz [standardowe ograniczenia jednostki SKU modułu równoważenia obciążenia platformy Azure][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Ograniczenia dysków platformy Azure

Woluminy korzystające z dysków zarządzanych platformy Azure nie są obecnie zasobami strefowymi. Zasobniki przełożone w innej strefie niż ich oryginalna strefa nie mogą ponownie dołączyć poprzedniego dysku.Zasobki rescheduled in a different zone from their original zone can't reattach their previous disk(s). Zaleca się uruchamianie obciążeń bezstanowych, które nie wymagają trwałego magazynu, które mogą napotkać problemy strefowe.

Jeśli musisz uruchomić obciążenia stanowe, użyj skazy i tolerancji w specyfikacjach zasobnika, aby poinformować harmonogram Kubernetes o utworzeniu zasobników w tej samej strefie co dyski. Alternatywnie należy użyć magazynu sieciowego, takich jak usługi Azure Files, które można dołączyć do zasobników, ponieważ są one zaplanowane między strefami.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Omówienie stref dostępności dla klastrów AKS

Strefy dostępności to oferta o wysokiej dostępności, która chroni aplikacje i dane przed awariami centrów danych. Strefy są unikatowe lokalizacje fizyczne w regionie platformy Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Fizyczna separacja stref dostępności w ramach regionu chroni aplikacje i dane przed awariami centrum danych. Usługi nadmiarowe strefowe replikują aplikacje i dane w strefach dostępności, aby chronić je przed pojedynczymi punktami awarii.

Aby uzyskać więcej informacji, zobacz [Co to są strefy dostępności na platformie Azure?][az-overview].

Klastry usługi AKS wdrażane przy użyciu stref dostępności mogą rozmieszczać węzły w wielu strefach w obrębie jednego regionu. Na przykład klaster w regionie *Wschodnie stany USA 2* może tworzyć węzły we wszystkich trzech strefach dostępności we *wschodnich stanach USA 2*. Ta dystrybucja zasobów klastra usługi AKS zwiększa dostępność klastra, ponieważ są one odporne na awarię określonej strefy.

![Rozkład węzłów AKS w strefach dostępności](media/availability-zones/aks-availability-zones.png)

W przypadku awarii strefy węzły można równocześnie równoważone ręcznie lub przy użyciu skalowania automatycznego klastra. Jeśli pojedyncza strefa stanie się niedostępna, aplikacje będą nadal działać.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Tworzenie klastra AKS w strefach dostępności

Podczas tworzenia klastra za pomocą polecenia [az aks create][az-aks-create] `--zones` parametr określa, w których węzłach agenta stref są wdrażane. Komponenty płaszczyzny sterowania AKS dla klastra są również rozłożone na `--zones` strefy w najwyższej dostępnej konfiguracji podczas definiowania parametru w czasie tworzenia klastra.

Jeśli podczas tworzenia klastra AKS nie zostaną zdefiniowane żadne strefy dla domyślnej puli agentów, składniki płaszczyzny sterowania AKS dla klastra nie będą używać stref dostępności. Można dodać dodatkowe pule węzłów za pomocą [polecenia az aks nodepool add][az-aks-nodepool-add] i określić `--zones` dla tych nowych węzłów, jednak składniki płaszczyzny sterowania pozostają bez świadomości strefy dostępności. Nie można zmienić świadomości strefy dla puli węzłów lub składników płaszczyzny sterowania AKS po ich wdrożeniu.

Poniższy przykład tworzy klaster AKS o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*. W sumie tworzone są *3* węzły - jeden agent w strefie *1,* jeden na *2,* a następnie jeden na *3.* Komponenty płaszczyzny sterowania AKS są również rozmieszczone między strefami w najwyższej dostępnej konfiguracji, ponieważ są zdefiniowane jako część procesu tworzenia klastra.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Utworzenie klastra usługi AKS zajmuje kilka minut.

## <a name="verify-node-distribution-across-zones"></a>Weryfikowanie rozkładu węzłów między strefami

Gdy klaster jest gotowy, wyświetl listę węzłów agenta w zestawie skalowania, aby zobaczyć, w jakiej strefie dostępności są one wdrażane.

Najpierw pobierz poświadczenia klastra AKS za pomocą polecenia [az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Następnie użyj [polecenia kubectl describe,][kubectl-describe] aby wyświetlić listę węzłów w klastrze. Filtruj *wartość failure-domain.beta.kubernetes.io/zone,* jak pokazano w poniższym przykładzie:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Poniższe przykładowe dane wyjściowe przedstawia trzy węzły rozproszone w określonym regionie i strefach dostępności, takie jak *eastus2-1* dla pierwszej strefy dostępności i *eastus2-2* dla drugiej strefy dostępności:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Podczas dodawania dodatkowych węzłów do puli agentów platforma Azure automatycznie dystrybuuje podstawowe maszyny wirtualne w określonych strefach dostępności.

Należy zauważyć, że w nowszych wersjach kubernetes (1.17.0 `topology.kubernetes.io/zone` i nowsze), `failure-domain.beta.kubernetes.io/zone`AKS używa nowszej etykiety oprócz przestarzałe .

## <a name="verify-pod-distribution-across-zones"></a>Sprawdź rozkład zasobników w różnych strefach

Zgodnie z opisem w [obszarze Dobrze znane etykiety, adnotacje i tainty][kubectl-well_known_labels], kubernetes używa `failure-domain.beta.kubernetes.io/zone` etykiety do automatycznego rozpowszechniania zasobników w kontrolerze replikacji lub usłudze w różnych dostępnych strefach. Aby to sprawdzić, można skalować w górę klastra z 3 do 5 węzłów, aby sprawdzić prawidłowe rozmieszczenie zasobników:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Po zakończeniu operacji skalowania po kilku `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` minutach polecenie powinno dać dane wyjściowe podobne do tego przykładu:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Jak widać, mamy teraz dwa dodatkowe węzły w strefach 1 i 2. Można wdrożyć aplikację składającą się z trzech replik. Użyjemy NGINX jako przykładu:

```console
kubectl run nginx --image=nginx --replicas=3
```

Jeśli sprawdzisz, że węzły, w których zasobników są uruchomione, zobaczysz, że zasobników są uruchomione na zasobników odpowiadających trzech różnych stref dostępności. Na przykład za `kubectl describe pod | grep -e "^Name:" -e "^Node:"` pomocą polecenia można uzyskać dane wyjściowe podobne do tego:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Jak widać z poprzedniego wyjścia, pierwszy zasobnik jest uruchomiony w węźle 0, który znajduje się w strefie `eastus2-1`dostępności . Drugi zasobnik jest uruchomiony na węźle `eastus2-3`2, który odpowiada , `eastus2-2`a trzeci w węźle 4, w . Bez żadnej dodatkowej konfiguracji, Kubernetes rozkłada zasobników poprawnie we wszystkich trzech strefach dostępności.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób tworzenia klastra usługi AKS, który używa stref dostępności. Aby uzyskać więcej informacji na temat klastrów o wysokiej dostępności, zobacz [Najważniejsze wskazówki dotyczące ciągłości działania i odzyskiwania po awarii w udręki AKS][best-practices-bc-dr].

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
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
