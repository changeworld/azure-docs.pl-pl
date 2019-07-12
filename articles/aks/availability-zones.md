---
title: Strefy dostępności w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć klaster, który rozdziela węzły w różnych strefach dostępności w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: iainfou
ms.openlocfilehash: 0f99386aa9eeb75a990507e383c32412fb39eceb
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840682"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>W wersji zapoznawczej — Tworzenie klastra usługi Azure Kubernetes Service (AKS), który używa strefy dostępności

Klaster Azure Kubernetes Service (AKS) dystrybuuje zasoby, takie jak węzły i Magazyn w logiczne sekcje podstawowej platformy Azure infrastruktury obliczeniowej. Ten model wdrażania gwarantuje, że węzły są uruchamiane w oddzielnych domenach aktualizacji i błędów w jednym centrum danych platformy Azure. Klastry usługi AKS wdrożona za pomocą to zachowanie domyślne zapewniają wysoką dostępność w celu ochrony przed awariami sprzętu lub planowanych zdarzeń związanych z konserwacją.

Aby zapewnić wyższą dostępność Twoim aplikacjom, AKS klastrów można rozpowszechniać w różnych strefach dostępności. Te strefy są fizycznie oddzielnymi centrami danych w obrębie danego regionu. Gdy składniki klastra są rozproszone w wielu strefach, klastra usługi AKS jest w stanie tolerować awaria w jednej z tych stref. Aplikacje i operacje zarządzania nadal dostępne nawet wtedy, gdy jeden całe centrum danych ma problem.

W tym artykule przedstawiono sposób tworzenia klastra usługi AKS i rozpowszechnić węzła w strefach dostępności. Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są samoobsługi, uczestnictwo. Są one udostępniane do zbierania opinii i błędy z naszej społeczności. W wersji zapoznawczej te funkcje nie są przeznaczone do użytku produkcyjnego. Funkcje w wersji zapoznawczej objęte "starań" pomocy technicznej. Pomoc od zespołów pomocy technicznej usługi AKS jest dostępna w godzinach pracy i Pacyfik, część strefy czasowej (PST) tylko. Aby uzyskać więcej informacji zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej usługi AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz wiersza polecenia platformy Azure w wersji 2.0.66 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli potrzebujesz instalacja lub uaktualnienie, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia w wersji zapoznawczej usługi aks

Tworzenie klastrów usługi AKS, które korzystać ze stref dostępności, należy *podglądu usługi aks* interfejsu wiersza polecenia wersja rozszerzenia 0.4.1 lub nowszej. Zainstaluj *podglądu usługi aks* rozszerzenie interfejsu wiersza polecenia platformy Azure przy użyciu polecenia [Dodaj rozszerzenie az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] polecenia::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flags-for-your-subscription"></a>Zarejestruj flag funkcji dla Twojej subskrypcji

Aby utworzyć klaster usługi AKS tej strefy dostępności, należy najpierw włączyć niektórych flag funkcji w ramach subskrypcji. Klastry używają zestawu do zarządzania wdrażaniem i konfiguracji w węzłach Kubernetes skalowania maszyny wirtualnej. *Standardowa* jednostki SKU modułu równoważenia obciążenia platformy Azure jest również wymagane, aby zapewnić odporność składników sieci na potrzeby kierowania ruchu do klastra. Zarejestruj *AvailabilityZonePreview*, *AKSAzureStandardLoadBalancer*, i *VMSSPreview* przy użyciu flagi funkcji [az feature register][az-feature-register] polecenia, jak pokazano w poniższym przykładzie:

> [!CAUTION]
> Po zarejestrowaniu funkcji w ramach subskrypcji, nie można obecnie wyrejestrować tę funkcję. Po włączeniu niektóre funkcje w wersji zapoznawczej, wartości domyślne mogą służyć dla wszystkich klastrów usługi AKS, które następnie są tworzone w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji w środowisku produkcyjnym. Aby przetestować funkcje w wersji zapoznawczej i zbieranie opinii, należy użyć oddzielnej subskrypcji.

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
az feature register --name AKSAzureStandardLoadBalancer --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Zajmuje kilka minut, zanim stan wyświetlany *zarejestrowanej*. Można sprawdzić stan rejestracji przy użyciu [lista funkcji az][az-feature-list] polecenia:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestracji *Microsoft.ContainerService* dostawcę zasobów przy użyciu [az provider register][az-provider-register] polecenia:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>Ograniczenia i dostępność regionów

Obecnie można utworzyć klastry usługi AKS przy użyciu stref dostępności w następujących regionach:

* Wschodnie stany USA 2
* Europa Północna
* Azja Południowo-wschodnia
* Europa Zachodnia
* Zachodnie stany USA 2

Podczas tworzenia klastra usługi AKS przy użyciu stref dostępności, obowiązują następujące ograniczenia:

* Strefy dostępności można włączyć tylko wtedy, gdy został utworzony klaster.
* Nie można zaktualizować ustawienia strefy dostępności, po utworzeniu klastra. Ponadto nie można zaktualizować istniejących, brak dostępności klastra strefy do korzystania ze stref dostępności.
* Nie można wyłączyć strefy dostępności do klastra usługi AKS, po jego utworzeniu.
* Wybrany rozmiar węzła (jednostki SKU maszyny Wirtualnej) muszą być dostępne we wszystkich strefach dostępności.
* Klastry z dostępnością, których wymagają strefach włączone użycie standardowego równoważenia obciążenia platformy Azure do dystrybucji w strefach.
* Należy użyć wersji pytanie 1.13.5 Kubernetes lub nowszej w celu wdrożenia standardowych modułów równoważenia obciążenia.

Klastry usługi AKS, które korzystać ze stref dostępności, należy użyć równoważenia obciążenia platformy Azure *standardowa* jednostki SKU. Wartość domyślna *podstawowe* jednostki SKU modułu równoważenia obciążenia platformy Azure nie obsługuje dystrybucji w różnych strefach dostępności. Aby uzyskać więcej informacji i ograniczenia dotyczące standardowych modułu równoważenia obciążenia, zobacz [ograniczenia wersji zapoznawczej jednostki SKU standardowa modułu równoważenia obciążenia platformy Azure][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Ograniczenia dysków platformy Azure

Woluminów korzystających z usługi Azure managed disks nie są obecnie zasobach strefowych. Zasobników zaplanowane ponownie w innej strefie z ich oryginalnego strefy nie może dołączyć ponownie ich poprzednich dyski. Zaleca się uruchamiania obciążeń bezstanowych, które nie wymagają, że problemy z magazynu trwałego, które mogą pochodzić między strefowych.

Jeśli są to obciążenia stanowe, muszą być wykonywane, użyj nasłonecznieniem oraz tolerations w swoje dane pod mówić harmonogram Kubernetes, aby utworzyć zasobników w tej samej strefie jako dyski. Można również użyć magazynu opartego na sieci, takich jak pliki Azure, który można dołączyć do zasobników, ponieważ są one zaplanowane między strefami.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Klastry Przegląd stref dostępności dla usługi AKS

Strefy dostępności to oferta, która chroni aplikacje i dane przed awariami centrów danych o wysokiej dostępności. Strefy są unikatowe fizycznie lokalizacje w regionie platformy Azure. Każda strefa składa się z co najmniej jeden centrów danych, wyposażone w niezależne zasilanie, chłodzenie i usługi sieciowe. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Fizyczna separacja stref dostępności w ramach regionu chroni aplikacje i dane przed awariami centrum danych. Strefowo nadmiarowe usługi replikować aplikacji i danych w różnych strefach dostępności, aby zapewnić ochronę przed pojedynczej punktami z awarią.

Aby uzyskać więcej informacji, zobacz [co to są strefy dostępności na platformie Azure?][az-overview].

Klastry usługi AKS, które są wdrażane przy użyciu stref dostępności można rozpowszechniać węzły w wielu strefach w jednym regionie. Na przykład w klastrze na platformie *wschodnie stany USA 2* regionie mogą tworzyć węzły w trzech strefach dostępności w *wschodnie stany USA 2*. Tej dystrybucji zasobów klastra usługi AKS poprawia dostępność klastra, ponieważ są one odporna na awarie określonej strefy.

![Dystrybucja węzłów AKS w strefach dostępności](media/availability-zones/aks-availability-zones.png)

W przypadku awarii strefy, węzły można ponownie zbilansowana ręcznie lub za pomocą skalowania automatycznego klastra. Jeśli w jednej strefie staje się niedostępny, aplikacje w dalszym ciągu uruchamiać.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Tworzenie klastra AKS w różnych strefach dostępności

Podczas tworzenia klastra przy użyciu [tworzenie az aks][az-aks-create] polecenia `--node-zones` parametr określa węzły agenta stref, które są wdrażane w. Składniki płaszczyzna kontroli usługi AKS dla klastra są również rozkłada stref w najwyższym konfiguracji dostępne podczas tworzenia klastra, określając `--node-zones` parametru.

Jeśli nie zdefiniowano żadnej strefy domyślnej puli agenta, podczas tworzenia klastra usługi AKS, składniki płaszczyzna kontroli usługi AKS dla klastra nie będzie używać stref dostępności. Można dodać dodatkowy węzeł pul (obecnie dostępna w wersji zapoznawczej w usłudze AKS) przy użyciu [Dodaj az aks nodepool][az-aks-nodepool-add] polecenia i określić `--node-zones` o tych nowych węzłów agenta, jednak składniki płaszczyzna kontroli pozostaną bez strefy dostępności rozpoznawanie. Nie można zmienić świadomości strefy dla puli węzeł lub AKS kontrolowania płaszczyzny składniki po zostały wdrożone.

Poniższy przykład obejmuje tworzenie klastra AKS o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*. Daje w sumie *3* utworzone węzły — jednego agenta w strefie *1*, jeden w *2*, a następnie jeden *3*. Składniki płaszczyzna kontroli AKS również są dystrybuowane na strefy w najwyższym konfiguracji dostępności, ponieważ są one zdefiniowane jako część klastra tworzenia procesu.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.13.5 \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
```

Trwa kilka minut, aby utworzyć klaster usługi AKS.

## <a name="verify-node-distribution-across-zones"></a>Sprawdź dystrybucji węzła w strefach

Gdy klaster będzie gotowy, można wyświetlić listę węzłów agenta w zestawie skalowania, aby zobaczyć, jakie strefę dostępności, które zostały wdrożone w.

Najpierw Pobierz poświadczenia klastra AKS przy użyciu [az aks get-credentials][az-aks-get-credentials] polecenia:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Następnie użyj [opisują kubectl][kubectl-describe] polecenie, aby wyświetlić listę węzłów w klastrze. Filtrowanie według *failure-domain.beta.kubernetes.io/zone* wartość, jak pokazano w poniższym przykładzie:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Następujące przykładowe dane wyjściowe zawierają trzy węzły, które są rozproszone na określonego regionu i strefy dostępności, takich jak *eastus2 1* dla po raz pierwszy strefy dostępności i *eastus2 2* dla drugiego Strefa dostępności:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

W miarę dodawania kolejnych węzłów do puli agentów, platforma Azure automatycznie rozdziela podstawowych maszyn wirtualnych w strefach dostępności określony.

## <a name="next-steps"></a>Następne kroki

W tym artykule szczegółowe informacje o sposobie tworzenia klastra usługi AKS, która używa strefy dostępności. Aby uzyskać więcej zagadnień dotyczących klastrów o wysokiej dostępności, zobacz [najlepszych rozwiązań biznesowych ciągłość działalności biznesowej i odzyskiwanie po awarii w usłudze AKS][best-practices-bc-dr].

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
