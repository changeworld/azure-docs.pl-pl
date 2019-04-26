---
title: Operator najlepsze rozwiązania — wysokiej dostępności i odzyskiwania po awarii w usłudze Azure Kubernetes usługi (AKS)
description: Dowiedz się operatora w klastrze o najlepszych praktykach maksymalny czas działania aplikacji zapewnić wysoką dostępność i przygotować się do sytuacji odzyskiwania po awarii w usłudze Azure Kubernetes usługi (AKS)
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 926f470b8a4dbdb6d6cbfe09ee61349a819600e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60464600"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące firm ciągłości działania i odzyskiwania po awarii w usłudze Azure Kubernetes Service (AKS)

W przypadku zarządzania klastrów w usłudze Azure Kubernetes Service (AKS), czas działania aplikacji staje się ważne. AKS zapewnia wysoką dostępność przy użyciu wielu węzłów w zestawie dostępności. Te wiele węzłów nie chronić klientów przed awarią regionu. Aby zmaksymalizować czas aktywności, należy zaimplementować niektórych ciągłość prowadzenia działalności biznesowej i funkcje odzyskiwania po awarii.

To najlepsze rozwiązania, które artykuł koncentruje się na zagadnienia, które ułatwiają planowanie firm ciągłości działania i odzyskiwania po awarii w usłudze AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Planowanie klastrów usługi AKS w wielu regionach
> * Kierowanie ruchu przez wielu klastrów za pomocą usługi Azure Traffic Manager
> * Użycie replikacji geograficznej dla usługi rejestry obrazów kontenerów
> * Planowanie stanu aplikacji w wielu klastrach
> * Replikacja magazynu w wielu regionach

## <a name="plan-for-multi-region-deployment"></a>Planowanie wdrożenia w wielu regionach

**Najważniejsze wskazówki** — w przypadku wdrażania wielu klastrów usługi AKS, gdzie dostępna jest AKS regiony wybiera się i używać sparowane regiony.

Klaster AKS jest wdrażany w jednym regionie. Aby zabezpieczyć się przed awariami regionu, należy wdrożyć aplikację w wielu klastrów usługi AKS w różnych regionach. Planując jakie regiony, do wdrożenia klastra usługi AKS, obowiązują następujące zastrzeżenia:

* [Dostępność usługi AKS w danym regionie](https://docs.microsoft.com/azure/aks/container-service-quotas#region-availability)
  * Wybierz regiony, w pobliżu użytkowników. AKS jest stale rozszerzasz działalność o nowe regiony.
* [Regiony sparowane platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
  * Dla tego obszaru Wybierz dwa regiony, które są skojarzone ze sobą. Te regiony koordynować aktualizacje platformy i określać priorytety w zakresie odzyskiwania w razie potrzeby.
* Poziom dostępności usług (gorąca Hot Hot ciepło, gorące i zimne)
  * Czy chcesz uruchomić oba regiony, w tym samym czasie, z jednym regionem *gotowe* można uruchomić ruchu lub regionami, wymagającym czas na przygotowanie się do obsługi ruchu.

Dostępność usługi AKS w poszczególnych regionach i sparowane regiony są wspólnego brany pod uwagę. Wdrożyć klastry usługi AKS do sparowane regiony, które są przeznaczone do zarządzania równocześnie w regionie odzyskiwania po awarii. Na przykład, jest dostępna w usłudze AKS *wschodnie stany USA* i *zachodnie stany USA*. Te regiony są również skojarzone. Tych dwóch regionach będą zalecane, tworząc strategii BC AKS odzyskiwania po awarii.

Podczas wdrażania aplikacji, musisz również dodać kolejny krok do potoku ciągłej integracji/ciągłego wdrażania, aby wdrożyć te wielu klastrów usługi AKS. Jeśli nie zaktualizujesz potoków wdrażania, wdrożeń aplikacji może można wdrożyć tylko do jednego regionów i klastry usługi AKS. Ruch klientów, który jest kierowany do regionu pomocniczego, nie będziesz otrzymywać najnowsze aktualizacje kodu.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Użyj usługi Azure Traffic Manager na potrzeby kierowania ruchu

**Najważniejsze wskazówki** — usługa Azure Traffic Manager może skierować klientów do najbliższego wystąpienia klastrami i aplikacjami usługi AKS. Aby uzyskać najlepszą wydajność i nadmiarowość należy kierować cały ruch aplikacji za pomocą usługi Traffic Manager przed przejściem do klastra usługi AKS.

Dzięki wielu klastrów usługi AKS w różnych regionach musisz kontrolować sposób kierowania ruchu do aplikacji, które są uruchamiane w każdym klastrze. [Usługa Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) jest równoważenia obciążenia ruchu opartego na protokole DNS, który można dystrybuować ruch sieciowy między regionami. Można przekierowywać użytkowników, na podstawie czasu odpowiedzi klastra lub oparte na lokalizacji geograficznej.

![Usługi AKS przy użyciu usługi Azure Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Z jednym klastrem AKS, klienci zwykle łączą się z *IP usługi* lub nazwa DNS danej aplikacji. W przypadku wdrożenia klastra wielu klientów należy łączyć się nazwę DNS usługi Traffic Manager, która wskazuje na usługi w każdym klastrze AKS. Te usługi są definiowane za pomocą punktów końcowych usługi Traffic Manager. Każdy punkt końcowy jest *IP modułu równoważenia obciążenia usługi*. Ta konfiguracja umożliwia kierowanie ruchem z punktu końcowego usługi Traffic Manager w jednym regionie do punktu końcowego w innym regionie.

![Geograficznego routingu za pomocą usługi Azure Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Usługa Traffic Manager służy do wyszukiwania DNS i zwracają najbardziej odpowiednich punktów końcowych dla użytkownika. Profile zagnieżdżone może służyć priorytetem dla lokalizacji głównej. Na przykład użytkownik przede wszystkim należy połączyć się ich najbliższego regionu geograficznego. Jeśli jest obsługiwany w tym regionie występuje jakiś problem, usługa Traffic Manager zamiast hiperlinkiem do regionu pomocniczego. To podejście zapewnia, że klienci zawsze możesz połączyć się z wystąpieniem aplikacji nawet jeśli ich najbliższego regionu geograficznego jest niedostępna.

Aby uzyskać instrukcje dotyczące sposobu konfigurowania tych punktów końcowych i routingu, zobacz [Konfigurowanie metody routingu geograficznego ruchu przy użyciu usługi Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door"></a>Routing aplikacji za pomocą usługi Azure drzwiami frontowymi warstwy 7

Usługa Azure Traffic Manager wykorzystuje system DNS (warstwa 3) dla ruchu kształtu. [Usługa Azure drzwiami frontowymi (obecnie w wersji zapoznawczej)](https://docs.microsoft.com/azure/frontdoor/front-door-overview) zapewnia routing opcjonalnym HTTP/HTTPS (warstwa 7). Dodatkowe funkcje drzwiami frontowymi obejmują SSL zakończenia, domenę niestandardową, zapory aplikacji sieci Web, ponowne zapisywanie adresów URL i koligacja sesji.

Sprawdź wymaga ruchu aplikacji, aby zrozumieć, jakie rozwiązanie będzie najbardziej odpowiedni.

## <a name="enable-geo-replication-for-container-images"></a>Włączanie replikacji geograficznej dla obrazów kontenerów

**Najważniejsze wskazówki** -Store obrazów kontenerów w usłudze Azure Container Registry i replikacja geograficzna rejestru do każdego regionu świadczenia usługi AKS.

Wdrażanie i uruchamianie aplikacji w usłudze AKS, potrzebujesz sposobu przechowywania i pobierała obrazy kontenerów. Usługa Azure Container Registry (ACR) można zintegrować z usługi AKS, aby bezpiecznie przechowywać swoje obrazy kontenerów lub wykresów rozwiązania Helm. Rejestru Azure container Registry obsługuje wielu wzorców replikację geograficzną, automatyczną replikację obrazów do regionów platformy Azure na całym świecie. Aby zwiększyć wydajność i dostępność, umożliwia utworzenie rejestru w każdym regionie, w którym znajduje się klaster AKS replikacji geograficznej usługi ACR. Każdy klaster AKS następnie ściąga obrazy kontenera z rejestru ACR lokalnego, w tym samym regionie:

![Usługa Azure Container Registry replikacji geograficznej dla obrazów kontenerów](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Zalety funkcji replikacji geograficznej usługi ACR obejmują następujące czynności:

* **Ściąganie obrazów z tym samym regionie jest szybsze.** Możesz ściągać obrazy z szybkich i o małym opóźnieniu połączenia sieciowe w ramach tego samego regionu platformy Azure.
* **Ściąganie obrazów z tym samym regionie jest bardziej niezawodna.** Jeśli region jest niedostępny, klastra usługi AKS ściąga obraz z różnych rejestru ACR, nadal będzie dostępne.
* **Ściąganie obrazów z tym samym regionie jest tańsze.** Między centrami danych jest bezpłatna ruch wychodzący w sieci.

Replikacja geograficzna jest funkcją *Premium* rejestrów jednostki SKU rejestru Azure container Registry. Aby uzyskać instrukcje dotyczące sposobu konfigurowania replikacji, zobacz [replikacji geograficznej usługi Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

## <a name="remove-service-state-from-inside-containers"></a>Usuń usługę stanu z wewnątrz kontenerów

**Najważniejsze wskazówki** — Jeśli to możliwe, nie należy przechowywać stanu usługi w kontenerze. Zamiast tego należy użyć usług PaaS platformy Azure, które obsługują replikacja w wielu regionach.

Stan usługi odnosi się do danych w pamięci lub na dysku, którego usługa wymaga, aby funkcja. Stan obejmuje struktur danych i zmiennych składowych, które usługa operacji odczytu i zapisu. W zależności od tego, jak usługa została zaprojektowana stan może również obejmować pliki lub inne zasoby, które są przechowywane na dysku. Na przykład pliki bazy danych będzie używać do przechowywania danych i dzienników transakcji.

Stan może zostać zewnętrznych albo wspólnie przechowywane z kodem, który jest manipulowanie stanu. O eksternalizację stanu odbywa się zwykle przy użyciu bazy danych lub innym magazynie danych, która działa na różnych maszynach, za pośrednictwem sieci lub poza procesem na tym samym komputerze.

Kontenery i mikrousługi są najbardziej odporne na błędy, gdy procesy, które są uruchamiane wewnątrz nich nie przechowują stanu. Ponieważ aplikacje prawie zawsze zawiera pewnego stanu, na użytek platformy jako rozwiązanie usługi, takie jak bazy danych Azure MySQL/Postgres lub Azure SQL.

Aby uzyskać szczegółowe informacje dotyczące sposobu tworzenia aplikacji, które są bardziej przenośny zobacz następujące wytyczne:

* [Metodologia aplikacji 12 Factor](https://12factor.net/).
* [Uruchamianie aplikacji sieci web w wielu regionach platformy Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Utwórz plan migracji magazynu

**Najważniejsze wskazówki** — Jeśli używasz usługi Azure Storage, przygotowywania i przetestować sposób migrowania magazynu z serwera podstawowego na region kopii zapasowej.

Twoje aplikacje mogą używać usługi Azure Storage dla danych. Jak aplikacje zostały rozmieszczone w obrębie wielu klastrów usługi AKS w różnych regionach, należy zachować magazynu zsynchronizowane. Typowych sposobów replikowania magazynu obejmują następujących metod:

* Replikacja asynchroniczna opartych na aplikacji
* Replikacja asynchroniczna opartego na infrastrukturze

### <a name="infrastructure-based-asynchronous-replication"></a>Replikacja asynchroniczna opartego na infrastrukturze

Twoje aplikacje mogą wymagać pojemności magazynu trwałego, nawet w przypadku, po usunięciu zasobnik. W usłudze Kubernetes można użyć trwałych woluminów można utrwalić magazyn danych. Te woluminy trwałe są zainstalowane do węzła maszyny Wirtualnej, a następnie udostępniane do zasobników. Woluminy trwałe postępuj zgodnie z zasobników, nawet wtedy, gdy zasobnika ustawiany jest przenoszony do innego węzła w tym samym klastrze.

W zależności od użycia rozwiązania magazynu mogą być inne strategie replikacji. Typowe rozwiązania magazynu, takie jak [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [CEPH](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [wieżę](https://rook.io/docs/rook/master/disaster-recovery.html), i [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) wszystkie mają własne wskazówki.

Centralna podejście jest wspólnego punktu magazynu dla aplikacji do zapisania swoich danych. Te dane są następnie replikowane między regionami i uzyskać dostęp lokalnie.

![Replikacja asynchroniczna opartego na infrastrukturze](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Jeśli używasz usługi Azure Managed Disks, dostępne replikacji i odzyskiwania po awarii rozwiązania obejmują przy użyciu jednej z następujących metod:

* [Ark na platformie Azure](https://github.com/heptio/ark/blob/master/docs/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Replikacja asynchroniczna opartych na aplikacji

Obecnie jest implementacji Kubernetes native opartych na aplikacjach replikacji asynchronicznej. Za pomocą luźno powiązanych rodzaj kontenerów usługi Kubernetes powinny działać wszelkie tradycyjne podejście aplikacji lub języka. Centralna podejście jest dla same aplikacje wchodzi do replikowania żądań magazynu, które są następnie zapisywane w każdym klastrze źródłowy magazyn danych.

![Replikacja asynchroniczna opartych na aplikacji](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Kolejne kroki

Ten artykuł koncentruje się na ciągłość biznesową i odzyskiwaniem po awarii, zagadnienia dotyczące odzyskiwania w klastrach usługi AKS. Aby uzyskać więcej informacji na temat operacji klastra w usłudze AKS zobacz poniższe najlepsze rozwiązania:

* [Izolację wielodostępu i klastra][aks-best-practices-cluster-isolation]
* [Podstawowe funkcje usługi scheduler rozwiązania Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
