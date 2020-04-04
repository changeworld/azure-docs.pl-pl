---
title: Wysoka dostępność i odzyskiwanie po awarii w usłudze Azure Kubernetes Service (AKS)
description: Zapoznaj się z najlepszymi rozwiązaniami operatora klastra, aby osiągnąć maksymalny czas pracy aplikacji, zapewniając wysoką dostępność i przygotowując się do odzyskiwania po awarii w usłudze Azure Kubernetes Service (AKS).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 6aff60cbc4a4cab557e6e202ea1181d5b20bbd20
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655873"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Najważniejsze wskazówki dotyczące ciągłości działania i odzyskiwania po awarii w usłudze Azure Kubernetes (AKS)

Podczas zarządzania klastrami w usłudze Azure Kubernetes Service (AKS) czas pracy aplikacji staje się ważny. Domyślnie usługa AKS zapewnia wysoką dostępność przy użyciu wielu węzłów w [zestawie skalowania maszyny wirtualnej (VMSS).](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) Jednak te wiele węzłów nie chroni systemu przed awarią regionu. Aby zmaksymalizować czas pracy bez przestojów, zaplanuj z wyprzedzeniem, aby zachować ciągłość działania i przygotować się do odzyskiwania po awarii.

W tym artykule skupiono się na tym, jak zaplanować ciągłość działania i odzyskiwanie po awarii w udręki WA. Omawiane kwestie:

> [!div class="checklist"]
> * Planowanie klastrów AKS w wielu regionach.
> * Przekieruj ruch między wieloma klastrami przy użyciu usługi Azure Traffic Manager.
> * Użyj replikacji geograficznej dla rejestrów obrazów kontenerów.
> * Planowanie stanu aplikacji w wielu klastrach.
> * Replikowanie magazynu w wielu regionach.

## <a name="plan-for-multiregion-deployment"></a>Planowanie wdrażania wieloregionu

**Najlepsze rozwiązanie:** Podczas wdrażania wielu klastrów usługi AKS wybierz regiony, w których usługa AKS jest dostępna, i użyj sparowanych regionów.

Klaster AKS jest wdrażany w jednym regionie. Aby chronić system przed awarią regionu, należy wdrożyć aplikację w wielu klastrach usługi AKS w różnych regionach. Plan, gdzie wdrożyć klaster usługi AKS, należy wziąć pod uwagę:

* [**Dostępność regionu AKS:**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability)wybierz regiony bliskie użytkownikom. AKS stale rozszerza się na nowe regiony.
* [**Sparowane regiony platformy Azure:**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)dla obszaru geograficznego wybierz dwa regiony, które są ze sobą powiązane. Sparowane regiony koordynują aktualizacje platform i w razie potrzeby ustalają priorytety działań naprawczych.
* **Dostępność usług:** Zdecyduj, czy sparowane regiony powinny być gorące/gorące, gorące/ciepłe, czy gorące/zimne. Czy chcesz uruchomić oba regiony w tym samym czasie, z jednym regionem *gotowym* do rozpoczęcia obsługi ruchu? A może chcesz, aby jeden region miał czas, aby przygotować się do obsługi ruchu?

Dostępność regionu AKS i sparowane regiony są wspólne rozważania. Wdrażanie klastrów usługi AKS w sparowanych regionach, które są przeznaczone do zarządzania odzyskiwaniem po awarii regionu razem. Na przykład AKS jest dostępny we wschodnich stanach USA i zachodnich stanach USA. Te regiony są sparowane. Wybierz te dwa regiony podczas tworzenia strategii AKS BC/DR.

Podczas wdrażania aplikacji należy dodać kolejny krok do potoku ciągłej integracji/ciągłego wdrażania, aby wdrożyć je w tych wielu klastrach usługi AKS. Jeśli nie zaktualizujesz potoków wdrażania, aplikacje mogą być wdrażane tylko w jednym z regionów i klastrów AKS. Ruch klientów, który jest kierowany do regionu pomocniczego nie otrzyma najnowszych aktualizacji kodu.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Kierowanie ruchu za pomocą usługi Azure Traffic Manager

**Najlepsze rozwiązanie:** Usługa Azure Traffic Manager może kierować klientów do najbliższego klastra AKS i wystąpienia aplikacji. Aby uzyskać najlepszą wydajność i nadmiarowość, należy kierować cały ruch aplikacji za pośrednictwem usługi Traffic Manager, zanim przejdzie do klastra AKS.

Jeśli masz wiele klastrów AKS w różnych regionach, użyj Usługi Traffic Manager, aby kontrolować sposób przepływu ruchu do aplikacji uruchamianych w każdym klastrze. [Usługa Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) to moduł równoważenia obciążenia ruchu opartego na systemie DNS, który może dystrybuować ruch sieciowy w różnych regionach. Usługa Traffic Manager służy do kierowania użytkowników na podstawie czasu odpowiedzi klastra lub na podstawie lokalizacji geograficznej.

![AKS z menedżerem ruchu](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Klienci, którzy mają jeden klaster AKS zazwyczaj łączą się z nazwą IP usługi lub DNS danej aplikacji. We wdrożeniu wieloklastrowym klienci powinni połączyć się z nazwą DNS usługi Menedżera ruchu, która wskazuje usługi w każdym klastrze AKS. Zdefiniuj te usługi przy użyciu punktów końcowych usługi Traffic Manager. Każdy punkt końcowy jest *ip modułu równoważenia obciążenia usługi*. Ta konfiguracja służy do kierowania ruchu sieciowego z punktu końcowego usługi Traffic Manager w jednym regionie do punktu końcowego w innym regionie.

![Routing geograficzny za pośrednictwem usługi Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Menedżer ruchu wykonuje wyszukiwania DNS i zwraca najbardziej odpowiedni punkt końcowy użytkownika. Profile zagnieżdżone mogą ustalać priorytety lokalizacji podstawowej. Na przykład użytkownicy powinni zazwyczaj łączyć się z najbliższym regionem geograficznym. Jeśli ten region ma problem, Usługa Traffic Manager zamiast tego kieruje użytkowników do regionu pomocniczego. Takie podejście gwarantuje, że klienci mogą łączyć się z wystąpieniem aplikacji, nawet jeśli ich najbliższy region geograficzny jest niedostępny.

Aby uzyskać informacje na temat konfigurowania punktów końcowych i routingu, zobacz [Konfigurowanie metody routingu ruchu geograficznego przy użyciu usługi Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Routing aplikacji warstwy 7 z usługą Azure Front Door Service

Usługa Traffic Manager używa systemu DNS (warstwa 3) do kształtowania ruchu. [Usługa Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) udostępnia opcję routingu HTTP/HTTPS (warstwa 7). Dodatkowe funkcje usługi Azure Front Door Service obejmują zakończenie protokołu TLS, domenę niestandardową, zaporę aplikacji sieci web, przepisywanie adresów URL i koligacji sesji. Przejrzyj potrzeby ruchu aplikacji, aby dowiedzieć się, które rozwiązanie jest najbardziej odpowiednie.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Wzajemne połączenia regionów z globalną komunikacją równorzędnie sieci wirtualnej

Jeśli klastry muszą ze sobą rozmawiać, połączenie obu sieci wirtualnych ze sobą można osiągnąć za pomocą [komunikacji równorzędnej sieci wirtualnej.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Ta technologia łączy sieci wirtualne ze sobą, zapewniając wysoką przepustowość w sieci szkieletowej firmy Microsoft, nawet w różnych regionach geograficznych.

Warunkiem wstępnym równoważenia sieci wirtualnych, w których są uruchomione klastry AKS, jest użycie standardowego modułu równoważenia obciążenia w klastrze AKS, tak aby usługi Kubernetes były dostępne w komunikacji równorzędnej sieci wirtualnej.

## <a name="enable-geo-replication-for-container-images"></a>Włączanie replikacji geograficznej dla obrazów kontenerów

**Najlepsze rozwiązanie:** Przechowuj obrazy kontenerów w rejestrze kontenerów platformy Azure i replikuj rejestr geograficznie do każdego regionu usługi AKS.

Aby wdrożyć i uruchomić aplikacje w usłudze AKS, należy sposób przechowywania i ściągania obrazów kontenerów. Rejestr kontenerów integruje się z programem AKS, dzięki czemu można bezpiecznie przechowywać obrazy kontenerów lub wykresy helm. Rejestr kontenerów obsługuje replikację geograficzną wielu rzeszy, aby automatycznie replikować obrazy do regionów platformy Azure na całym świecie. 

Aby zwiększyć wydajność i dostępność, użyj replikacji geograficznej rejestru kontenerów, aby utworzyć rejestr w każdym regionie, w którym masz klaster AKS. Każdy klaster usługi AKS pobiera obrazy kontenerów z lokalnego rejestru kontenerów w tym samym regionie:

![Replikacja geograficzna rejestru kontenerów dla obrazów kontenerów](media/operator-best-practices-bc-dr/acr-geo-replication.png)

W przypadku używania replikacji geograficznej rejestru kontenerów do ściągania obrazów z tego samego regionu wyniki są następujące:

* **Szybciej:** Pobierasz obrazy z szybkich połączeń sieciowych o małych opóźnieniach w tym samym regionie platformy Azure.
* **Bardziej niezawodne:** Jeśli region jest niedostępny, klaster usługi AKS pobiera obrazy z rejestru dostępnych kontenerów.
* **Tańsze**: Nie ma ładowania wyjścia sieciowego między centrami danych.

Replikacja geograficzna jest funkcją *rejestrów* kontenerów usługi Premium SKU. Aby uzyskać informacje dotyczące konfigurowania replikacji geograficznej, zobacz [Replikacja geograficzna rejestru kontenerów](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Usuwanie stanu usługi z wewnętrznych kontenerów

**Najlepsze rozwiązanie:** Jeśli to możliwe, nie przechowuj stanu usługi wewnątrz kontenera. Zamiast tego należy użyć platformy Azure jako usługi (PaaS), która obsługuje replikację wieloregionową.

*Stan usługi* odnosi się do danych w pamięci lub na dysku, które usługa wymaga do działania. State zawiera struktury danych i zmienne członkowskie, które usługa odczytuje i zapisuje. W zależności od sposobu ekwiomowania usługi stan może również zawierać pliki lub inne zasoby, które są przechowywane na dysku. Na przykład stan może zawierać pliki bazy danych używa do przechowywania danych i dzienników transakcji.

Stan może być zewnętrznie lub współlokowane z kodem, który manipuluje stanem. Zazwyczaj można zewnętrznie stan przy użyciu bazy danych lub innego magazynu danych, który działa na różnych komputerach w sieci lub który kończy się proces na tym samym komputerze.

Kontenery i mikrousługi są najbardziej odporne, gdy procesy, które są uruchamiane wewnątrz nich nie zachowują stanu. Ponieważ aplikacje prawie zawsze zawierają jakiś stan, należy użyć rozwiązania PaaS, takiego jak Azure Database for MySQL, Azure Database for PostgreSQL lub Azure SQL Database.

Aby tworzyć aplikacje przenośne, zobacz następujące wskazówki:

* [12-factor metodologia aplikacji](https://12factor.net/)
* [Uruchamianie aplikacji sieci web w wielu regionach platformy Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Tworzenie planu migracji magazynu

**Najlepsze rozwiązanie:** Jeśli używasz usługi Azure Storage, przygotuj i przetestuj sposób migracji magazynu z regionu podstawowego do regionu kopii zapasowej.

Aplikacje mogą używać usługi Azure Storage dla swoich danych. Ponieważ aplikacje są rozłożone na wiele klastrów AKS w różnych regionach, należy zachować magazyn zsynchronizowane. Oto dwa typowe sposoby replikowania magazynu:

* Replikacja asynchronizacyjna oparta na infrastrukturze
* Replikacja asynchronizacyjna oparta na aplikacji

### <a name="infrastructure-based-asynchronous-replication"></a>Replikacja asynchronizacyjna oparta na infrastrukturze

Aplikacje mogą wymagać trwałego przechowywania nawet po usunięciu zasobnika. W użyczanych sieciach można używać woluminów trwałych do utrwalania magazynu danych. Woluminy trwałe są instalowane na maszynie wirtualnej węzła, a następnie udostępniane zasobnikom. Woluminy trwałe podążają za zasobnikami, nawet jeśli zasobniki są przenoszone do innego węzła wewnątrz tego samego klastra.

Strategia replikacji, której używasz, zależy od rozwiązania magazynu. Typowe rozwiązania pamięci masowej, takie jak [Gluster,](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/) [Ceph,](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/) [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)i [Portworx,](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) zawierają własne wskazówki dotyczące odzyskiwania po awarii i replikacji.

Typową strategią jest zapewnienie wspólnego punktu magazynu, w którym aplikacje mogą zapisywać swoje dane. Te dane są następnie replikowane w różnych regionach, a następnie dostępne lokalnie.

![Replikacja asynchronizacyjna oparta na infrastrukturze](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Jeśli używasz dysków zarządzanych platformy Azure, możesz wybrać rozwiązania replikacji i odzyskiwania po awarii, takie jak:

* [Velero na platformie Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Replikacja asynchronizacyjna oparta na aplikacji

Kubernetes obecnie nie zapewnia implementacji macierzystej dla replikacji asynchroniiowej opartej na aplikacji. Ponieważ kontenery i kubernetes są luźno powiązane, wszelkie tradycyjne podejście aplikacji lub języka powinny działać. Zazwyczaj same aplikacje replikują żądania magazynu, które są następnie zapisywane do magazynu danych źródłowych każdego klastra.

![Replikacja asynchronizacyjna oparta na aplikacji](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule koncentruje się na ciągłości biznesowej i odzyskiwania po awarii zagadnienia dla klastrów AKS. Aby uzyskać więcej informacji na temat operacji klastra w uzywce AKS, zobacz te artykuły dotyczące najlepszych rozwiązań:

* [Wielodostępność i izolacja klastra][aks-best-practices-cluster-isolation]
* [Podstawowe funkcje harmonogramu kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
