---
title: Migrowanie do usługi Azure Kubernetes (AKS)
description: Migruj do usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 8315560c679f9807715af14dc315fa3000be0472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624815"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrowanie do usługi Azure Kubernetes (AKS)

Ten artykuł ułatwia planowanie i wykonywanie pomyślnej migracji do usługi Azure Kubernetes Service (AKS). Aby ułatwić podejmowanie kluczowych decyzji, w tym przewodniku przedstawiono szczegółowe informacje dotyczące bieżącej zalecanej konfiguracji usługi AKS. Ten artykuł nie obejmuje każdego scenariusza i w stosownych przypadkach artykuł zawiera łącza do bardziej szczegółowych informacji dotyczących planowania pomyślnej migracji.

Ten dokument może służyć do obsługi następujących scenariuszy:

* Migrowanie klastra AKS wspierane przez [zestawy dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) do [zestawów skalowania maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* Migrowanie klastra AKS w celu użycia [standardowego modułu równoważenia obciążenia jednostki SKU](https://docs.microsoft.com/azure/aks/load-balancer-standard)
* Migracja z [usługi Azure Container Service (ACS) — przejście na emeryturę 31 stycznia 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) r. do usługi AKS
* Migracja z [aparatu AKS](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) do AKS
* Migracja z klastrów kubernetes nienapartych na platformie Azure do usługi AKS

Podczas migracji upewnij się, że docelowa wersja kubernetes znajduje się w obsługiwanym oknie dla usługi AKS. Jeśli używasz starszej wersji, może nie mieszczeć się w obsługiwanym zakresie i wymagać uaktualnienia wersje mają być obsługiwane przez usługi AKS. Aby uzyskać więcej informacji, zobacz [obsługiwane przez usługi AKS wersje kubernetes.](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)

Jeśli przeprowadzasz migrację do nowszej wersji programu Kubernetes, zapoznaj się z [zasadami obsługi obsługi wersji i wersji programu Kubernetes.](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

W zależności od scenariusza może pomóc kilka narzędzi typu open source:

* [Velero](https://velero.io/) (wymaga Kubernetes 1.7+)
* [Rozszerzenie interfejsu wiersza polecenia platformy Azure Kube](https://github.com/yaron2/azure-kube-cli)
* [ReShifter (ReShifter)](https://github.com/mhausenblas/reshifter)

W tym artykule podsumujemy szczegóły migracji dla:

> [!div class="checklist"]
> * AKS ze standardowym modułem równoważenia obciążenia i zestawami skalowania maszyn wirtualnych
> * Istniejące dołączone usługi platformy Azure
> * Zapewnianie prawidłowych przydziałów
> * Wysoka dostępność i ciągłość działania
> * Zagadnienia dotyczące aplikacji bezstanowych
> * Zagadnienia dotyczące aplikacji stanowych
> * Wdrażanie konfiguracji klastra

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS ze standardowym modułem równoważenia obciążenia i zestawami skalowania maszyn wirtualnych

AKS to usługa zarządzana oferująca unikatowe możliwości z mniejszym obciążeniem zarządzania. W wyniku usługi zarządzanej należy wybrać z zestawu [regionów,](https://docs.microsoft.com/azure/aks/quotas-skus-regions) które obsługuje usługi AKS. Przejście z istniejącego klastra do usługi AKS może wymagać modyfikowania istniejących aplikacji, aby pozostały w dobrej kondycji na płaszczyźnie sterowania zarządzanego przez usługi AKS.

Zalecamy użycie klastrów AKS wspieranych przez [zestawy skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machine-scale-sets) i [modułu Azure Standard Load Balancer,](https://docs.microsoft.com/azure/aks/load-balancer-standard) aby zapewnić korzystanie z funkcji, takich jak [wiele pul węzłów,](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) [strefy dostępności,](https://docs.microsoft.com/azure/availability-zones/az-overview) [autoryzowane zakresy adresów IP,](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges) [skalowanie automatyczne klastra,](https://docs.microsoft.com/azure/aks/cluster-autoscaler) [zasady azure dla usługi AKS](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)i inne nowe funkcje w miarę ich zwalniania.

Klastry AKS wspierane przez [zestawy dostępności maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) nie obsługują wielu z tych funkcji.

Poniższy przykład tworzy klaster AKS z puli pojedynczego węzła wspierane przez zestaw skalowania maszyny wirtualnej. Używa standardowego modułu równoważenia obciążenia. Umożliwia również skalowanie automatyczne klastra w puli węzłów dla klastra i ustawia co najmniej *1* i maksymalnie *3* węzły:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Istniejące dołączone usługi platformy Azure

Podczas migracji klastrów być może dołączono zewnętrzne usługi platformy Azure. Nie wymagają one odtworzenia zasobów, ale będą wymagać aktualizacji połączeń z poprzednich do nowych klastrów w celu zachowania funkcjonalności.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Konto magazynu
* Zewnętrzne bazy danych

## <a name="ensure-valid-quotas"></a>Zapewnianie prawidłowych przydziałów

Ponieważ dodatkowe maszyny wirtualne zostaną wdrożone w ramach subskrypcji podczas migracji, należy sprawdzić, czy przydziały i limity są wystarczające dla tych zasobów. Może być konieczne żądanie zwiększenia [przydziału vCPU](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).

Może być konieczne żądanie zwiększenia [przydziałów sieci,](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) aby upewnić się, że nie wyczerpujesz usług IP. Aby uzyskać dodatkowe informacje, zobacz [zakresy sieci i adresów IP dla usługi AKS.](https://docs.microsoft.com/azure/aks/configure-kubenet)

Aby uzyskać więcej informacji, zobacz [Limity subskrypcji i usług platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Aby sprawdzić bieżące przydziały, w witrynie Azure portal przejdź do [bloku subskrypcji](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), wybierz subskrypcję, a następnie wybierz opcję Użycie **+ przydziały**.

## <a name="high-availability-and-business-continuity"></a>Wysoka dostępność i ciągłość działania

Jeśli aplikacja nie może obsłużyć przestojów, należy postępować zgodnie z najlepszymi rozwiązaniami dla scenariuszy migracji o wysokiej dostępności.  Najważniejsze wskazówki dotyczące kompleksowego planowania ciągłości działania, odzyskiwania po awarii i maksymalizacji czasu pracy bez przestojów wykraczają poza zakres tego dokumentu.  Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących ciągłości działania i odzyskiwania po awarii w usłudze Azure Kubernetes Service (AKS),](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) aby dowiedzieć się więcej.

W przypadku złożonych aplikacji zazwyczaj przeprowadzasz migrację w czasie, a nie wszystkie naraz. Oznacza to, że stare i nowe środowiska mogą wymagać komunikacji za pośrednictwem sieci. Aplikacje, które `ClusterIP` wcześniej używane usługi do komunikowania się może być konieczne, aby być widoczne jako typ `LoadBalancer` i być odpowiednio zabezpieczone.

Aby zakończyć migrację, należy wskazać klientom nowe usługi, które są uruchomione w u usługi AKS. Zaleca się przekierowanie ruchu przez aktualizację dns, aby wskazać moduł równoważenia obciążenia, który znajduje się przed klastrem AKS.

[Usługa Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) może kierować klientów do żądanego klastra i wystąpienia aplikacji Kubernetes.  Traffic Manager to moduł równoważenia obciążenia ruchu oparty na systemie DNS, który może dystrybuować ruch sieciowy w różnych regionach.  Aby uzyskać najlepszą wydajność i nadmiarowość, należy kierować cały ruch aplikacji za pośrednictwem usługi Traffic Manager, zanim przejdzie do klastra AKS.  We wdrożeniu wieloklastrowym klienci powinni połączyć się z nazwą DNS usługi Menedżera ruchu, która wskazuje usługi w każdym klastrze AKS. Zdefiniuj te usługi przy użyciu punktów końcowych usługi Traffic Manager. Każdy punkt końcowy jest *ip modułu równoważenia obciążenia usługi*. Ta konfiguracja służy do kierowania ruchu sieciowego z punktu końcowego usługi Traffic Manager w jednym regionie do punktu końcowego w innym regionie.

![AKS z menedżerem ruchu](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Usługa Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) to kolejna opcja routingu ruchu dla klastrów AKS.  Usługa Azure Front Door Service umożliwia definiowanie i monitorowanie globalnego routingu ruchu internetowego oraz zarządzanie nim przez optymalizowanie pod kątem uzyskiwania najlepszej wydajności i natychmiastowego przechodzenia w tryb failover w celu zapewnienia wysokiej dostępności. 

### <a name="considerations-for-stateless-applications"></a>Zagadnienia dotyczące aplikacji bezstanowych

Migracja aplikacji bezstanowych jest najprostszym przypadkiem. Zastosuj definicje zasobów (YAML lub Helm) do nowego klastra, upewnij się, że wszystko działa zgodnie z oczekiwaniami i przekieruj ruch, aby aktywować nowy klaster.

### <a name="considerations-for-stateful-applications"></a>Zagadnienia dotyczące aplikacji stanowych

Starannie zaplanuj migrację aplikacji stanowych, aby uniknąć utraty danych lub nieoczekiwanych przestojów.

Jeśli używasz usługi Azure Files, możesz zainstalować udział plików jako wolumin w nowym klastrze:
* [Instalowanie statycznych plików platformy Azure jako woluminu](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Jeśli używasz dysków zarządzanych platformy Azure, można zainstalować dysk tylko wtedy, gdy nie jest przymocowany do dowolnej maszyny Wirtualnej:
* [Instalowanie statycznego dysku azure jako woluminu](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Jeśli żadna z tych metod nie działa, można użyć opcji tworzenia kopii zapasowych i przywracania:
* [Velero na platformie Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Azure Files

W przeciwieństwie do dysków usługi Azure Files można instalować jednocześnie na wielu hostach. W klastrze AKS platformy Azure i Kubernetes nie uniemożliwiają tworzenia zasobnika, który nadal używa klastra usługi ACS. Aby zapobiec utracie danych i nieoczekiwanemu zachowaniu, upewnij się, że klastry nie zapisują do tych samych plików w tym samym czasie.

Jeśli aplikacja może obsługiwać wiele replik, które wskazują na ten sam udział plików, wykonaj kroki migracji bezstanowej i wdrożyć definicje YAML do nowego klastra. Jeśli nie, jedno możliwe podejście migracji obejmuje następujące kroki:

* Sprawdzanie poprawności aplikacji działa poprawnie.
* Skieruj ruch na żywo do nowego klastra AKS.
* Odłącz stary klaster.

Jeśli chcesz rozpocząć od pustego udziału i wykonać kopię danych źródłowych, możesz użyć [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) poleceń do migracji danych.


#### <a name="migrating-persistent-volumes"></a>Migrowanie woluminów trwałych

W przypadku migracji istniejących woluminów trwałych do usługi AKS zazwyczaj wykonaj następujące czynności:

* Quiesce zapisuje do aplikacji. (Ten krok jest opcjonalny i wymaga przestojów).
* Rób migawki dysków.
* Tworzenie nowych dysków zarządzanych z migawek.
* Tworzenie woluminów trwałych w uzywniu AKS.
* Zaktualizuj specyfikacje zasobników, aby [używać istniejących woluminów,](https://docs.microsoft.com/azure/aks/azure-disk-volume) a nie PersistentVolumeClaims (statyczne inicjowanie obsługi administracyjnej).
* Wdrażanie aplikacji w u usługi AKS.
* Sprawdzanie poprawności aplikacji działa poprawnie.
* Skieruj ruch na żywo do nowego klastra AKS.

> [!IMPORTANT]
> Jeśli nie chcesz zapisywać pism, musisz replikować dane do nowego wdrożenia. W przeciwnym razie przegapisz dane, które zostały napisane po zrobiono migawki dysku.

Niektóre narzędzia typu open source mogą pomóc w tworzeniu dysków zarządzanych i migracji woluminów między klastrami kubernetes:

* [Rozszerzenie kopii dysku interfejsu wiersza polecenia platformy Azure](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopiuje i konwertuje dyski między grupami zasobów i regionami platformy Azure.
* [Rozszerzenie interfejsu wiersza polecenia platformy Azure Kube](https://github.com/yaron2/azure-kube-cli) wylicza woluminy kubernetes usługi ACS i migruje je do klastra AKS.


### <a name="deployment-of-your-cluster-configuration"></a>Wdrażanie konfiguracji klastra

Zaleca się użycie istniejącego potoku ciągłej integracji (CI) i ciągłego dostarczania (CD) w celu wdrożenia znanej dobrej konfiguracji w u usług AKS. Za pomocą usługi Azure Pipelines można [tworzyć i wdrażać aplikacje w usłudze AKS.](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops) Sklonuj istniejące zadania `kubeconfig` wdrażania i upewnij się, że wskazuje nowy klaster AKS.

Jeśli nie jest to możliwe, eksportuj definicje zasobów z istniejącego klastra kubernetes, a następnie zastosuj je do usługi AKS. Można użyć `kubectl` do eksportowania obiektów.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Przenoszenie istniejących zasobów do innego regionu

Klaster usługi AKS można przenieść do [innego regionu obsługiwanego przez usługi AKS][region-availability]. Zaleca się utworzenie nowego klastra w innym regionie, a następnie wdrożenie zasobów i aplikacji w nowym klastrze. Ponadto jeśli masz jakieś usługi, takie jak [usługi Azure Dev Spaces][azure-dev-spaces] uruchomione w klastrze AKS, należy również zainstalować i skonfigurować te usługi w klastrze w nowym regionie.


W tym artykule podsumowaliśmy szczegóły migracji dla:

> [!div class="checklist"]
> * AKS ze standardowym modułem równoważenia obciążenia i zestawami skalowania maszyn wirtualnych
> * Istniejące dołączone usługi platformy Azure
> * Zapewnianie prawidłowych przydziałów
> * Wysoka dostępność i ciągłość działania
> * Zagadnienia dotyczące aplikacji bezstanowych
> * Zagadnienia dotyczące aplikacji stanowych
> * Wdrażanie konfiguracji klastra


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/