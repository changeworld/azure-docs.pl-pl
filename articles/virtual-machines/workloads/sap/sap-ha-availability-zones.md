---
title: Konfiguracje obciążeń SAP ze strefami dostępności platformy Azure | Dokumenty firmy Microsoft
description: Architektura i scenariusze wysokiej dostępności dla sap NetWeaver przy użyciu stref dostępności platformy Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7a92bef85cd4ee7530940a065135e88c7530781
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675604"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Konfiguracje obciążenia SAP ze strefami dostępności platformy Azure
[Strefy dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) to jedna z funkcji o wysokiej dostępności, które zapewnia platforma Azure. Korzystanie ze stref dostępności zwiększa ogólną dostępność obciążeń SAP na platformie Azure. Ta funkcja jest już dostępna w niektórych [regionach platformy Azure.](https://azure.microsoft.com/global-infrastructure/regions/) W przyszłości będzie dostępna w większej liczbie regionów.

Ta grafika przedstawia podstawową architekturę wysokiej dostępności SAP:

![Standardowa konfiguracja wysokiej dostępności](./media/sap-ha-availability-zones/standard-ha-config.png)

Warstwa aplikacji SAP jest wdrażana w jednym [zestawie dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)platformy Azure. Aby uzyskać wysoką dostępność usług SAP Central Services, można wdrożyć dwie maszyny wirtualne w oddzielnym zestawie dostępności. Użyj klastrowania trybu failover systemu Windows Server lub rozrusznika serca (Linux) jako struktury o wysokiej dostępności z automatyczną pracy awaryjnej w przypadku problemu z infrastrukturą lub oprogramowaniem. Aby dowiedzieć się więcej o tych wdrożeniach, zobacz:

- [Klaster wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Klaster wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udziału plików](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Wysoka dostępność sap NetWeaver na maszynach wirtualnych platformy Azure na suse Linux Enterprise Server dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Wysoka dostępność maszyn wirtualnych platformy Azure dla sap NetWeaver w systemie Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Podobna architektura dotyczy warstwy DBMS systemów SAP NetWeaver, S/4HANA lub Hybris. Warstwę DBMS można wdrożyć w trybie aktywnym/pasywnym z rozwiązaniem klastra trybu failover w celu ochrony przed awarią infrastruktury lub oprogramowania. Rozwiązaniem klastra trybu failover może być struktura trybu failover specyficzne dla systemu dbms, klastrowanie trybu failover systemu Windows Server lub rozrusznik serca.

Aby wdrożyć tę samą architekturę przy użyciu stref dostępności platformy Azure, należy wprowadzić pewne zmiany w architekturze opisane wcześniej. W tym artykule opisano te zmiany.

## <a name="considerations-for-deploying-across-availability-zones"></a>Zagadnienia dotyczące wdrażania w strefach dostępności


Podczas korzystania ze stref dostępności należy wziąć pod uwagę następujące kwestie:

- Nie ma żadnych gwarancji dotyczących odległości między różnymi strefami dostępności w regionie platformy Azure.
- Strefy dostępności nie są idealnym rozwiązaniem odzyskiwania po awarii. Klęski żywiołowe mogą spowodować rozległe szkody w regionach świata, w tym poważne szkody w infrastrukturze energetycznej. Odległości między różnymi strefami mogą nie być wystarczająco duże, aby stanowić odpowiednie rozwiązanie odzyskiwania po awarii.
- Opóźnienie sieci w strefach dostępności nie jest taka sama we wszystkich regionach platformy Azure. W niektórych przypadkach można wdrożyć i uruchomić warstwę aplikacji SAP w różnych strefach, ponieważ opóźnienie sieci z jednej strefy do aktywnej maszyny Wirtualnej DBMS jest dopuszczalne. Jednak w niektórych regionach platformy Azure opóźnienie między aktywnym maszyną wirtualną DBMS a wystąpieniem aplikacji SAP, po wdrożeniu w różnych strefach, może być nie do przyjęcia dla procesów biznesowych SAP. W takich przypadkach architektura wdrażania musi być inna, z aktywną/aktywną architekturą dla aplikacji lub architekturą aktywną/pasywną, w której opóźnienie sieci między strefami jest zbyt wysokie.
- Przy podejmowaniu decyzji, gdzie używać stref dostępności, należy oprzeć swoją decyzję na opóźnienie sieci między strefami. Opóźnienie sieci odgrywa ważną rolę w dwóch obszarach:
    - Opóźnienie między dwoma wystąpieniami DBMS, które muszą mieć replikację synchronikową. Im wyższe opóźnienie sieci, tym większe prawdopodobieństwo, że wpłynie to na skalowalność obciążenia.
    - Różnica w opóźnieniu sieci między maszyną wirtualną z uruchomionym wystąpieniem okna dialogowego SAP w strefie z aktywnym wystąpieniem DBMS i podobną maszyną wirtualną w innej strefie. Wraz ze wzrostem tej różnicy zwiększa się również wpływ na czas pracy procesów biznesowych i zadań wsadowych, w zależności od tego, czy działają one w strefie z systemem dbms, czy w innej strefie.

Podczas wdrażania maszyn wirtualnych platformy Azure w strefach dostępności i ustanawiania rozwiązań trybu failover w tym samym regionie platformy Azure obowiązują pewne ograniczenia:

- Podczas wdrażania w strefach dostępności platformy Azure należy używać [dysków zarządzanych](https://azure.microsoft.com/services/managed-disks/) platformy Azure. 
- Mapowanie wyliczenia stref do stref fizycznych jest ustalana na podstawie subskrypcji platformy Azure. Jeśli używasz różnych subskrypcji do wdrażania systemów SAP, musisz zdefiniować idealne strefy dla każdej subskrypcji.
- Nie można wdrożyć zestawów dostępności platformy Azure w strefie dostępności platformy Azure, chyba że używasz [grupy umieszczania zbliżeniowego platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) Sposób, w jaki można wdrożyć warstwę SAP DBMS i usługi centralne w różnych strefach, a jednocześnie wdrożyć warstwę aplikacji SAP przy użyciu zestawów dostępności i nadal osiągać bliskość maszyn wirtualnych, opisano w artykule [Grupy umieszczania zbliżeniowego platformy Azure w celu uzyskania optymalnego opóźnienia sieci w aplikacjach SAP.](sap-proximity-placement-scenarios.md) Jeśli nie wykorzystujesz grup umieszczania zbliżeniowego platformy Azure, musisz wybrać jedną lub drugą jako strukturę wdrażania maszyn wirtualnych.
- Nie można użyć [modułu Równoważenia obciążenia podstawowego platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) do tworzenia rozwiązań klastra trybu failover opartych na klastrze trybu failover systemu Windows Server lub rozruszniku systemu Linux. Zamiast tego należy użyć jednostki [SKU równoważenia obciążenia standardowego platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).



## <a name="the-ideal-availability-zones-combination"></a>Idealne połączenie stref dostępności
Zanim zdecydujesz, jak korzystać ze stref dostępności, należy określić:

- Opóźnienie sieci wśród trzech stref regionu platformy Azure. Umożliwi to wybranie stref o najmniejszym opóźnieniu sieci w ruchu sieciowym między strefami.
- Różnica między opóźnieniem maszyny Wirtualnej do maszyny Wirtualnej w jednej ze stref, według wyboru, a opóźnieniem sieci w dwóch wybranych strefach.
- Określenie, czy typy maszyn wirtualnych, które należy wdrożyć są dostępne w dwóch strefach, które zostały wybrane. W przypadku niektórych maszyn wirtualnych, zwłaszcza maszyn wirtualnych z serii M, mogą wystąpić sytuacje, w których niektóre jednostki SKU są dostępne tylko w dwóch z trzech stref.

## <a name="network-latency-between-and-within-zones"></a>Opóźnienie sieci między strefami i w obrębie
Aby określić opóźnienie między różnymi strefami, należy:

- Wdrażanie jednostki SKU maszyny Wirtualnej, której chcesz użyć dla wystąpienia usługi DBMS we wszystkich trzech strefach. Upewnij się, że [platforma Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jest włączona podczas dokonywania tego pomiaru.
- Po znalezieniu dwóch stref z najmniejszym opóźnieniem sieci, wdrożyć kolejne trzy maszyny wirtualne jednostki SKU, które mają być używane jako maszyny Wirtualnej warstwy aplikacji w trzech strefach dostępności. Zmierz opóźnienie sieci względem dwóch maszyn wirtualnych DBMS w dwóch wybranych strefach DBMS. 
- Użyj **szczypania** jako narzędzia pomiarowego. To narzędzie firmy SAP jest opisane w uwagach do obsługi SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) i [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Skoncentruj się na poleceniach udokumentowanych dla pomiarów opóźnienia. Ponieważ **ping** nie działa za pośrednictwem ścieżek kodu przyspieszonej sieci platformy Azure, nie zaleca się go używać.

Nie trzeba wykonywać tych testów ręcznie. Można znaleźć procedurę powershell [dostępność strefy opóźnienie test,](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) który automatyzuje testy opóźnienia opisane. 

Na podstawie pomiarów i dostępności jednostek SKU maszyn wirtualnych w strefach dostępności należy podjąć pewne decyzje:

- Zdefiniuj strefy idealne dla warstwy DBMS.
- Określ, czy chcesz dystrybuować aktywną warstwę aplikacji SAP w jednej, dwóch czy wszystkich trzech strefach, na podstawie różnic opóźnienia sieci w strefie w porównaniu ze strefami.
- Określ, czy chcesz wdrożyć konfigurację aktywną/pasywną, czy konfigurację aktywną/aktywną, z punktu widzenia aplikacji. (Te konfiguracje są wyjaśnione w dalszej części tego artykułu.

Podejmując te decyzje, należy również wziąć pod uwagę zalecenia SAP dotyczące opóźnień sieci, zgodnie z dokumentami zawartymi w notatce SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> Pomiary i decyzje, które podejmujesz, są prawidłowe dla subskrypcji platformy Azure używanej podczas pomiarów. Jeśli używasz innej subskrypcji platformy Azure, musisz powtórzyć pomiary. Mapowanie wyliczonych stref może być inna dla innej subskrypcji platformy Azure.


> [!IMPORTANT]
> Oczekuje się, że opisane wcześniej pomiary zapewnią różne wyniki w każdym regionie platformy Azure, który obsługuje [strefy dostępności.](https://docs.microsoft.com/azure/availability-zones/az-overview) Nawet jeśli wymagania dotyczące opóźnienia sieci są takie same, może być konieczne przyjęcie różnych strategii wdrażania w różnych regionach platformy Azure, ponieważ opóźnienie sieci między strefami może być różne. W niektórych regionach platformy Azure opóźnienie sieci między trzema różnymi strefami może się znacznie różnić. W innych regionach opóźnienie sieci między trzema różnymi strefami może być bardziej jednolite. Twierdzenie, że zawsze występuje opóźnienie sieci między 1 i 2 milisekundy nie jest poprawne. Nie można uogólniać opóźnienia sieci w strefach dostępności w regionach platformy Azure.

## <a name="activeactive-deployment"></a>Aktywne/aktywne wdrażanie
Ta architektura wdrażania jest nazywana aktywną/aktywną, ponieważ można wdrożyć aktywne serwery aplikacji SAP w dwóch lub trzech strefach. Wystąpienie sap central services, które używa replikacji enqueue zostanie wdrożony między dwiema strefami. To samo dotyczy warstwy DBMS, która zostanie wdrożona w tych samych strefach co usługa SAP Central Service.

Rozważając tę konfigurację, należy znaleźć dwie strefy dostępności w regionie, które oferują międzystrefowe opóźnienie sieci, które jest dopuszczalne dla obciążenia i synchronicznego replikacji DBMS. Należy również upewnić się, że różnica między opóźnieniem sieci w wybranych strefach i opóźnienie sieci między strefami nie jest zbyt duże. Dzieje się tak, ponieważ nie chcesz dużych odmian, w zależności od tego, czy zadanie jest uruchamiane w strefie z serwerem DBMS, czy między strefami, w czasie wykonywania procesów biznesowych lub zadań wsadowych. Niektóre odmiany są dopuszczalne, ale nie czynniki różnicy.

Uproszczony schemat aktywnego/aktywnego wdrożenia w dwóch strefach może wyglądać następująco:

![Wdrażanie strefy aktywnej/aktywnej](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

W przypadku tej konfiguracji obowiązują następujące zagadnienia:

- Nie przy użyciu [grupy umieszczania zbliżeniowego platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location), należy traktować strefy dostępności platformy Azure jako usterkę i zaktualizować domeny dla wszystkich maszyn wirtualnych, ponieważ zestawy dostępności nie można wdrożyć w strefach dostępności platformy Azure.
- Jeśli chcesz połączyć wdrożenia strefowe dla warstwy DBMS i usług centralnych, ale chcesz użyć zestawów dostępności platformy Azure dla warstwy aplikacji, musisz użyć grup zbliżeniowych platformy Azure, jak opisano w artykule [Grupy umieszczania zbliżeniowego usługi Azure, aby uzyskać optymalne opóźnienie sieci w aplikacjach SAP.](sap-proximity-placement-scenarios.md)
- W przypadku modułów równoważenia obciążenia klastrów trybu failover usług SAP Central services i warstwy DBMS należy użyć [standardowego modułu SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Podstawowy moduł równoważenia obciążenia nie będzie działać w różnych strefach.
- Sieć wirtualna platformy Azure wdrożona w celu obsługi systemu SAP wraz z jego podsieciami jest rozciągnięta między strefami. Nie potrzebujesz oddzielnych sieci wirtualnych dla każdej strefy.
- W przypadku wszystkich wdrażanych maszyn wirtualnych należy użyć [dysków zarządzanych platformy Azure](https://azure.microsoft.com/services/managed-disks/). Dyski niezarządzane nie są obsługiwane dla wdrożeń strefowych.
- Usługa Azure Premium Storage i [magazyn ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nie obsługują replikacji magazynu dowolnego typu w różnych strefach. Aplikacja (DBMS lub SAP Central Services) musi replikować ważne dane.
- To samo dotyczy udostępnionego katalogu sapmnt, który jest dyskiem udostępnionym (Windows), udziałem CIFS (Windows) lub udziałem NFS (Linux). Należy użyć technologii, która replikuje te dyski udostępnione lub udziały między strefami. Technologie te są obsługiwane:
  - W systemie Windows rozwiązanie klastra korzystające z programu SIOS DataKeeper, zgodnie z dokumentem w [programie Cluster, wystąpienie SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra na platformie Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
  - Dla systemu SUSE Linux, udziału NFS, który jest zbudowany zgodnie z udokumentowaną w [wysokiej dostępności nfs na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
    Obecnie rozwiązanie, które używa programu Microsoft Scale-W poziomie serwera plików, zgodnie z dokumentami w [przygotowanie infrastruktury platformy Azure dla sap wysokiej dostępności przy użyciu klastra trybu failover systemu Windows i udziału plików dla wystąpień SAP ASCS/SCS,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)nie jest obsługiwany w różnych strefach.
- Trzecia strefa jest używana do obsługi urządzenia SBD w przypadku, gdy tworzysz [klaster SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) lub dodatkowe wystąpienia aplikacji.
- Aby osiągnąć spójność czasu wykonywania dla krytycznych procesów biznesowych, można spróbować skierować niektóre zadania wsadowe i użytkowników do wystąpień aplikacji, które znajdują się w strefie z aktywnym wystąpieniem DBMS przy użyciu grup serwerów wsadowych SAP, grup logowania SAP lub grup RFC. Jednak w przypadku strefyowej pracy awaryjnej, należy ręcznie przenieść te grupy do wystąpień uruchomionych na maszynach wirtualnych, które są w strefie z aktywną maszyną wirtualną bazy danych.  
- Można wdrożyć uśpione wystąpienia okna dialogowego w każdej ze stref. Ma to na celu włączenie natychmiastowego powrotu do dawnej pojemności zasobów, jeśli strefa używana przez część wystąpień aplikacji jest niesąsa.

> [!IMPORTANT]
> W tym aktywnym/aktywnym scenariuszu dodatkowe opłaty za przepustowość są ogłaszane przez firmę Microsoft od 04/01/2020. Sprawdź [szczegóły cen przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/)dokumentu . Transfer danych między warstwą aplikacji SAP a warstwą SAP DBMS jest dość intensywny. W związku z tym aktywny/aktywny scenariusz może przyczynić się do kosztów sporo. Sprawdzaj ten artykuł, aby uzyskać dokładne koszty 


## <a name="activepassive-deployment"></a>Wdrażanie aktywne/pasywne
Jeśli nie można znaleźć dopuszczalne delta między opóźnieniem sieci w jednej strefie i opóźnienie ruchu sieciowego między strefami, można wdrożyć architekturę, która ma aktywny/pasywny znak z punktu widzenia warstwy aplikacji SAP. Definiujesz *strefę aktywną,* która jest strefą, w której wdrażasz pełną warstwę aplikacji i w której próbujesz uruchomić zarówno aktywny system DBMS, jak i wystąpienie SAP Central Services. W przypadku takiej konfiguracji należy upewnić się, że nie występują skrajne różnice czasu wykonywania, w zależności od tego, czy zadanie jest uruchamiane w strefie z aktywnym wystąpieniem systemu DBMS, czy nie, w transakcjach biznesowych i zadaniach wsadowych.

Podstawowy układ architektury wygląda następująco:

![Wdrażanie strefy aktywnej/pasywnej](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

W przypadku tej konfiguracji obowiązują następujące zagadnienia:

- Zestawów dostępności nie można wdrożyć w strefach dostępności platformy Azure. Aby to zrekompensować, można użyć grup umieszczania zbliżeniowego platformy Azure zgodnie z artykułem [Grupy miejsc docelowych zbliżeniami platformy Azure, aby uzyskać optymalne opóźnienie sieci w aplikacjach SAP.](sap-proximity-placement-scenarios.md)
- Korzystając z tej architektury, należy ściśle monitorować stan i starać się zachować aktywne wystąpienia usług DBMS i SAP Central Services w tej samej strefie co wdrożona warstwa aplikacji. W przypadku pracy awaryjnej usługi SAP Central service lub wystąpienia systemu DBMS należy upewnić się, że można ręcznie wrócić po awarii do strefy z warstwą aplikacji SAP wdrożoną tak szybko, jak to możliwe.
- W przypadku modułów równoważenia obciążenia klastrów trybu failover usług SAP Central services i warstwy DBMS należy użyć [standardowego modułu SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Podstawowy moduł równoważenia obciążenia nie będzie działać w różnych strefach.
- Sieć wirtualna platformy Azure wdrożona w celu obsługi systemu SAP wraz z jego podsieciami jest rozciągnięta między strefami. Nie potrzebujesz oddzielnych sieci wirtualnych dla każdej strefy.
- W przypadku wszystkich wdrażanych maszyn wirtualnych należy użyć [dysków zarządzanych platformy Azure](https://azure.microsoft.com/services/managed-disks/). Dyski niezarządzane nie są obsługiwane dla wdrożeń strefowych.
- Usługa Azure Premium Storage i [magazyn ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nie obsługują replikacji magazynu dowolnego typu w różnych strefach. Aplikacja (DBMS lub SAP Central Services) musi replikować ważne dane.
- To samo dotyczy udostępnionego katalogu sapmnt, który jest dyskiem udostępnionym (Windows), udziałem CIFS (Windows) lub udziałem NFS (Linux). Należy użyć technologii, która replikuje te dyski udostępnione lub udziały między strefami. Technologie te są obsługiwane:
    - W systemie Windows rozwiązanie klastra korzystające z programu SIOS DataKeeper, zgodnie z dokumentem w [programie Cluster, wystąpienie SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra na platformie Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - Dla systemu SUSE Linux, udziału NFS, który jest zbudowany zgodnie z udokumentowaną w [wysokiej dostępności nfs na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
  Obecnie rozwiązanie, które używa programu Microsoft Scale-W poziomie serwera plików, zgodnie z dokumentami w [przygotowanie infrastruktury platformy Azure dla sap wysokiej dostępności przy użyciu klastra trybu failover systemu Windows i udziału plików dla wystąpień SAP ASCS/SCS,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)nie jest obsługiwany w różnych strefach.
- Trzecia strefa jest używana do obsługi urządzenia SBD w przypadku, gdy tworzysz [klaster SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) lub dodatkowe wystąpienia aplikacji.
- Należy wdrożyć uśpione maszyny wirtualne w strefie pasywnej (z punktu widzenia DBMS), dzięki czemu można uruchomić zasoby aplikacji w przypadku awarii strefy.
    - [Usługa Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) nie może obecnie replikować aktywnych maszyn wirtualnych na uśpionych maszynach wirtualnych między strefami. 
- Należy zainwestować w automatyzację, która umożliwia, w przypadku awarii strefy, automatyczne uruchamianie warstwy aplikacji SAP w drugiej strefie.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Łączna wysoka dostępność i konfiguracja odzyskiwania po awarii
Firma Microsoft nie udostępnia żadnych informacji na temat odległości geograficznych między obiektami obsługującymi różne strefy dostępności platformy Azure w regionie platformy Azure. Mimo to niektórzy klienci używają stref dla połączonej konfiguracji wysokiej dostępności i odzyskiwania po awarii, która obiecuje cel punktu odzyskiwania (RPO) zerowy. Oznacza to, że nie należy utracić żadnych transakcji bazy danych popełnione nawet w przypadku odzyskiwania po awarii. 

> [!NOTE]
> Zaleca się użycie takiej konfiguracji tylko w pewnych okolicznościach. Na przykład można go używać, gdy dane nie mogą opuścić regionu platformy Azure ze względów bezpieczeństwa lub zgodności. 

Oto jeden z przykładów tego, jak taka konfiguracja może wyglądać:

![Połączone dr o wysokiej dostępności w strefach](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

W przypadku tej konfiguracji obowiązują następujące zagadnienia:

- Zakładasz, że istnieje znaczna odległość między obiektami obsługującymi strefę dostępności lub jesteś zmuszony do pozostania w określonym regionie platformy Azure. Zestawów dostępności nie można wdrożyć w strefach dostępności platformy Azure. Aby to zrekompensować, można użyć grup umieszczania zbliżeniowego platformy Azure zgodnie z artykułem [Grupy miejsc docelowych zbliżeniami platformy Azure, aby uzyskać optymalne opóźnienie sieci w aplikacjach SAP.](sap-proximity-placement-scenarios.md)
- Korzystając z tej architektury, należy ściśle monitorować stan i starać się zachować aktywne wystąpienia usług DBMS i SAP Central Services w tej samej strefie co wdrożona warstwa aplikacji. W przypadku pracy awaryjnej usługi SAP Central service lub wystąpienia systemu DBMS należy upewnić się, że można ręcznie wrócić po awarii do strefy z warstwą aplikacji SAP wdrożoną tak szybko, jak to możliwe.
- Powinny mieć wstępnie zainstalowane wystąpienia aplikacji produkcyjnych na maszynach wirtualnych, które uruchamiają aktywne wystąpienia aplikacji kontroli jakości.
- W przypadku awarii strefy zamknij wystąpienia aplikacji kontroli jakości i zamiast tego uruchom wystąpienia produkcyjne. Należy zauważyć, że należy użyć nazw wirtualnych dla wystąpień aplikacji, aby to działało.
- W przypadku modułów równoważenia obciążenia klastrów trybu failover usług SAP Central services i warstwy DBMS należy użyć [standardowego modułu SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Podstawowy moduł równoważenia obciążenia nie będzie działać w różnych strefach.
- Sieć wirtualna platformy Azure wdrożona w celu obsługi systemu SAP wraz z jego podsieciami jest rozciągnięta między strefami. Nie potrzebujesz oddzielnych sieci wirtualnych dla każdej strefy.
- W przypadku wszystkich wdrażanych maszyn wirtualnych należy użyć [dysków zarządzanych platformy Azure](https://azure.microsoft.com/services/managed-disks/). Dyski niezarządzane nie są obsługiwane dla wdrożeń strefowych.
- Usługa Azure Premium Storage i [magazyn ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nie obsługują replikacji magazynu dowolnego typu w różnych strefach. Aplikacja (DBMS lub SAP Central Services) musi replikować ważne dane.
- To samo dotyczy udostępnionego katalogu sapmnt, który jest dyskiem udostępnionym (Windows), udziałem CIFS (Windows) lub udziałem NFS (Linux). Należy użyć technologii, która replikuje te dyski udostępnione lub udziały między strefami. Technologie te są obsługiwane:
    - W systemie Windows rozwiązanie klastra korzystające z programu SIOS DataKeeper, zgodnie z dokumentem w [programie Cluster, wystąpienie SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra na platformie Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - Dla systemu SUSE Linux, udziału NFS, który jest zbudowany zgodnie z udokumentowaną w [wysokiej dostępności nfs na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).

  Obecnie rozwiązanie, które używa programu Microsoft Scale-W poziomie serwera plików, zgodnie z dokumentami w [przygotowanie infrastruktury platformy Azure dla sap wysokiej dostępności przy użyciu klastra trybu failover systemu Windows i udziału plików dla wystąpień SAP ASCS/SCS,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)nie jest obsługiwany w różnych strefach.
- Trzecia strefa jest używana do obsługi urządzenia SBD w przypadku, gdy tworzysz [klaster SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) lub dodatkowe wystąpienia aplikacji.





## <a name="next-steps"></a>Następne kroki
Oto kilka następnych kroków wdrażania w strefach dostępności platformy Azure:

- [Klaster wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Przygotowywanie infrastruktury platformy Azure dla wysokiej dostępności systemu SAP przy użyciu klastra trybu failover systemu Windows i udziału plików dla wystąpień SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






