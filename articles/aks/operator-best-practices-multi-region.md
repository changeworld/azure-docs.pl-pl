---
title: Wysoka dostępność i odzyskiwanie po awarii w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, operatorem klastra najlepszych rozwiązań, aby osiągnąć maksymalny czas działania aplikacji, zapewniając wysoką dostępność i przygotowywanie do odzyskiwania po awarii w usłudze Azure Kubernetes Service (AKS).
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 4afc1231e6c9fa49c04c7bf6dfe26ee5eb87cc31
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475170"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące firm ciągłości działania i odzyskiwania po awarii w usłudze Azure Kubernetes Service (AKS)

W przypadku zarządzania klastrów w usłudze Azure Kubernetes Service (AKS), czas działania aplikacji staje się ważne. AKS zapewnia wysoką dostępność przy użyciu wielu węzłów w zestawie dostępności. Jednak te wiele węzłów nie chronić system przed awarią regionu. Aby zmaksymalizować czas aktywności, Planuj z wyprzedzeniem do zapewniania ciągłości biznesowej i przygotowywanie do odzyskiwania po awarii.

Ten artykuł koncentruje się na sposób planowania ciągłości biznesowej i odzyskiwania po awarii w usłudze AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Planowanie klastrów usługi AKS w wielu regionach.
> * Kierowanie ruchu przez wielu klastrów za pomocą usługi Azure Traffic Manager.
> * Użyj replikacji geograficznej dla usługi rejestry obrazów kontenera.
> * Planowanie stanu aplikacji w wielu klastrach.
> * Replikacja magazynu w wielu regionach.

## <a name="plan-for-multiregion-deployment"></a>Planowanie wdrożenia multiregion

**Najlepsze rozwiązanie**: Podczas wdrażania wielu klastrów usługi AKS Wybierz regiony, w których AKS jest dostępna, a następnie użyć sparowane regiony.

Klaster AKS jest wdrażany w jednym regionie. Aby chronić system przed awarią regionu, należy wdrożyć aplikację w wielu klastrów usługi AKS w różnych regionach. Podczas planowania wdrażania klastra usługi AKS, należy wziąć pod uwagę:

* [**Dostępność w poszczególnych regionach AKS**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Wybierz regiony, w pobliżu użytkowników. AKS nieustannie rozwija na nowe regiony.
* [**Sparowanych regionów platformy Azure**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): Dla tego obszaru Wybierz dwa regiony, które są skojarzone ze sobą. Sparowane regiony dzieli koordynować aktualizacje platformy i ustalić ich priorytety wysiłków odzyskiwania w razie potrzeby.
* **Dostępność usług**: Zdecyduj, czy Twoje sparowane regiony powinny być hot hot hot ciepło i gorące i zimne. Czy chcesz uruchomić oba regiony, w tym samym czasie, z jednym regionem *gotowe* można uruchomić obsługujące ruch? Czy chcesz jeden region, aby mieć czas na przygotowanie się do obsługi ruchu?

Dostępność usługi AKS w poszczególnych regionach i sparowane regiony są wspólnego brany pod uwagę. Wdrożyć klastry usługi AKS do sparowane regiony, które są przeznaczone do zarządzania równocześnie w regionie odzyskiwania po awarii. Na przykład AKS jest dostępna w regionach wschodnie stany USA i zachodnie stany USA. Te regiony są skojarzone. Wybierz tych dwóch regionach, gdy tworzysz strategię BC AKS odzyskiwania po awarii.

Podczas wdrażania aplikacji, należy dodać kolejny krok do potoku ciągłej integracji/ciągłego wdrażania, aby wdrożyć te wielu klastrów usługi AKS. Jeśli nie zaktualizujesz potoków wdrażania aplikacji wdrażanych na tylko jeden z regionów i klastry usługi AKS. Ruch klientów, który jest kierowany do regionu pomocniczego, nie będziesz otrzymywać najnowsze aktualizacje kodu.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Użyj usługi Azure Traffic Manager na potrzeby kierowania ruchu

**Najlepsze rozwiązanie**: Usługa Azure Traffic Manager może skierować klientów do najbliższego wystąpienia klastrami i aplikacjami usługi AKS. Aby uzyskać najlepszą wydajność i nadmiarowość należy kierować cały ruch aplikacji za pomocą usługi Traffic Manager, zanim przejdzie do klastra usługi AKS.

W przypadku wielu klastrów usługi AKS w różnych regionach, należy użyć usługi Traffic Manager, aby kontrolować przepływ ruchu do aplikacji, które są uruchamiane w każdym klastrze. [Usługa Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) jest równoważenia obciążenia ruchu opartego na protokole DNS, który można dystrybuować ruch sieciowy między regionami. Traffic Manager można używać do przekierowania użytkowników na podstawie czasu odpowiedzi klastra lub oparte na lokalizacji geograficznej.

![Usługi AKS przy użyciu usługi Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Klienci, którzy zwykle mają jeden klaster AKS nawiązać połączenia usługi IP lub nazwę DNS danej aplikacji. W przypadku wdrożenia multicluster klientów należy łączyć się nazwę DNS usługi Traffic Manager, która wskazuje na usługi w każdym klastrze AKS. Zdefiniowanie tych usług za pomocą punktów końcowych usługi Traffic Manager. Każdy punkt końcowy jest *usługi IP modułu równoważenia obciążenia*. Ta konfiguracja umożliwia kierowanie ruchem z punktu końcowego usługi Traffic Manager w jednym regionie do punktu końcowego w innym regionie.

![Geograficzny routing za pośrednictwem usługi Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Usługa Traffic Manager wykonuje wyszukiwania DNS i zwraca najbardziej odpowiednich punktów końcowych przez użytkownika. Zagnieżdżone profile można określić priorytet lokalizacji głównej. Na przykład użytkownicy powinien ogólnie łączyć się z jego najbliższym regionie geograficznym. Jeśli obsługiwany w tym regionie występuje problem, usługa Traffic Manager zamiast kieruje użytkowników do regionu pomocniczego. Takie podejście zapewnia, że klienci mogą się łączyć do wystąpienia aplikacji, nawet jeśli ich najbliższego regionu geograficznego jest niedostępna.

Aby uzyskać informacji na temat sposobu konfigurowania punktów końcowych i routingu, zobacz [skonfigurować metody geograficznego routingu ruchu przy użyciu usługi Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Routing aplikacji warstwy 7 za pomocą usługi Azure Service drzwi

Usługa Traffic Manager wykorzystuje system DNS (warstwa 3) do ruchu kształtu. [Usługa Azure drzwiami frontowymi](https://docs.microsoft.com/azure/frontdoor/front-door-overview) zapewnia routing opcjonalnym HTTP/HTTPS (warstwa 7). Dodatkowe funkcje usługi Azure Service drzwiami frontowymi obejmują SSL zakończenia, domenę niestandardową, zapory aplikacji sieci web, ponowne zapisywanie adresów URL i koligacja sesji. Sprawdź wymaga ruchu aplikacji, aby zrozumieć, jakie rozwiązanie będzie najbardziej odpowiedni.

## <a name="enable-geo-replication-for-container-images"></a>Włączanie replikacji geograficznej dla obrazów kontenerów

**Najlepsze rozwiązanie**: Store obrazów kontenerów w usłudze Azure Container Registry i replikacja geograficzna rejestru do każdego regionu świadczenia usługi AKS.

Wdrażanie i uruchamianie aplikacji w usłudze AKS, potrzebujesz sposobu przechowywania i pobierała obrazy kontenerów. Container Registry integruje się za pomocą usługi AKS, aby go bezpiecznie przechowywać swoje obrazy kontenerów lub wykresów rozwiązania Helm. Container Registry obsługuje replikację replikację geograficzną, automatyczną replikację obrazów do regionów platformy Azure na całym świecie. 

Aby zwiększyć wydajność i dostępność, umożliwia utworzenie rejestru w każdym regionie, w którym znajduje się klaster AKS replikacji geograficznej rejestru kontenerów. Każdy klaster AKS następnie ściąga obrazy kontenera z rejestru lokalny kontener, w tym samym regionie:

![Kontener rejestru replikacji geograficznej dla obrazów kontenerów](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Gdy używasz replikacji geograficznej rejestru kontenerów do ściągania obrazów z tym samym regionie, wyniki są:

* **Szybsze**: W tym samym regionie platformy Azure jest ściąganie obrazów z połączenia sieciowe o dużej szybkości i małych opóźnieniach.
* **Bardziej niezawodna**: Jeśli region jest niedostępny, klastra usługi AKS ściąga obrazów z rejestru kontenerów dostępne.
* **Tańsze**: Między centrami danych jest bezpłatna ruch wychodzący w sieci.

Replikacja geograficzna jest funkcją *Premium* rejestry kontenerów jednostki SKU. Aby uzyskać informacje dotyczące sposobu konfigurowania replikacji geograficznej, zobacz [replikacji geograficznej rejestru kontenerów](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Usuń usługę stanu z wewnątrz kontenerów

**Najlepsze rozwiązanie**: Jeśli to możliwe, nie należy przechowywać stanu usługi w kontenerze. W zamian użyj platformy Azure jako usługa (PaaS), który obsługuje replikacja.

*Stan usługi* odnosi się do danych w pamięci lub na dysku, którego usługa wymaga, aby funkcja. Stan obejmuje struktur danych i zmiennych składowych, które usługa operacji odczytu i zapisu. W zależności od tego, jak usługa została zaprojektowana stan może również obejmować pliki lub inne zasoby, które są przechowywane na dysku. Na przykład stan może zawierać pliki, które korzysta z bazy danych do przechowywania danych i dzienników transakcji.

Stan może zostać zewnętrznych albo wspólnie przechowywane z kodem, który obsługuje stan. Zazwyczaj oddzielenie stanu przy użyciu bazy danych lub w innym magazynie danych, które jest uruchamiane na różnych komputerach za pośrednictwem sieci lub, która działa poza procesem w tym samym komputerze.

Kontenery i mikrousługi są najbardziej odporne na błędy, gdy procesy, które są uruchamiane wewnątrz nich nie zachowuje stan. Aplikacje prawie zawsze zawierać pewnego stanu, na użytek rozwiązania PaaS takie jak bazy danych Azure MySQL, Azure Database for PostgreSQL i Azure SQL Database.

Aby skompilować aplikacje przenośne, zobacz następujące wytyczne:

* [Metodologia aplikacji 12-składnikowych](https://12factor.net/)
* [Uruchamianie aplikacji sieci web w wielu regionach platformy Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Utwórz plan migracji magazynu

**Najlepsze rozwiązanie**: Jeśli używasz usługi Azure Storage, Przygotuj i przetestować sposób migrowania magazynu z regionu podstawowego do regionu kopii zapasowej.

Aplikacje mogą używać usługi Azure Storage dla danych. Ponieważ aplikacje zostały rozmieszczone w obrębie wielu klastrów usługi AKS w różnych regionach, należy zachować magazynu zsynchronizowane. Oto dwa podstawowe sposoby umożliwiała ona replikowanie magazynu:

* Replikacja asynchroniczna opartego na infrastrukturze
* Replikacja asynchroniczna opartych na aplikacji

### <a name="infrastructure-based-asynchronous-replication"></a>Replikacja asynchroniczna opartego na infrastrukturze

Aplikacje mogą wymagać magazynu trwałego, nawet w przypadku, po usunięciu zasobnik. W usłudze Kubernetes można użyć trwałych woluminów można utrwalić magazyn danych. Trwałe woluminy są instalowane na węzeł maszyny Wirtualnej i następnie udostępniane do zasobników. Woluminy trwałe postępuj zgodnie z zasobników, nawet wtedy, gdy zasobników zostaną przeniesione do innego węzła w tym samym klastrze.

Strategię replikacji, którego używasz, zależy od rozwiązań pamięci masowej. Typowe rozwiązania magazynu, takie jak [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [Ceph](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [wieżę](https://rook.io/docs/rook/master/disaster-recovery.html), i [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) zapewniają własne wskazówki dotyczące odzyskiwania po awarii i Replikacja.

Typowe strategia polega na zapewnić wspólnego punktu magazynu, w których aplikacje mogą zapisywać swoje dane. Te dane są następnie replikowane między regionami i uzyskać dostęp lokalnie.

![Replikacja asynchroniczna opartego na infrastrukturze](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Jeśli używasz usługi Azure Managed Disks, możesz wybrać replikację i rozwiązania odzyskiwania po awarii, takie jak te:

* [Velero na platformie Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Replikacja asynchroniczna opartych na aplikacji

Usługa Kubernetes zapewnia obecnie natywnej implementacji opartych na aplikacjach replikacji asynchronicznej. Ponieważ kontenery i rozwiązania Kubernetes są luźno powiązane, powinny działać wszelkie tradycyjne podejście aplikacji lub języka. Zazwyczaj same aplikacje replikować żądania pamięci masowej, które są następnie zapisywane do każdego klastra podstawowego magazynu danych.

![Replikacja asynchroniczna opartych na aplikacji](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Kolejne kroki

Ten artykuł koncentruje się na ciągłość biznesową i zagadnienia dotyczące odzyskiwania po awarii w przypadku klastrów usługi AKS. Aby uzyskać więcej informacji na temat operacji klastra w usłudze AKS zobacz następujące artykuły dotyczące najlepszych rozwiązań:

* [Izolacja wielodostępność i klastra][aks-best-practices-cluster-isolation]
* [Podstawowe funkcje usługi scheduler rozwiązania Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
