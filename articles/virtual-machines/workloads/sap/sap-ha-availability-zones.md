---
title: Konfiguracje obciążeń SAP o strefy dostępności platformy Azure | Dokumentacja firmy Microsoft
description: Architektura wysokiej dostępności i scenariusze SAP NetWeaver przy użyciu strefy dostępności platformy Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3772dbdc8582eea1b2eac368784878a8a36d34ad
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125322"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Konfiguracje obciążenia SAP ze strefami dostępności platformy Azure
[Strefy dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) to jedna z funkcji wysokiej dostępności, zapewnianymi przez platformę Azure. Przy użyciu stref dostępności zwiększa ogólną dostępność obciążeń SAP na platformie Azure. Ta funkcja jest już dostępna w niektórych [regionów świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/). W przyszłości będzie on dostępny w większej liczbie regionów.

Podstawowa architektura wysokiej dostępności SAP pokazano na tym rysunku:

![Konfiguracja standardowa wysokiej dostępności](./media/sap-ha-availability-zones/standard-ha-config.png)

Warstwa aplikacji SAP jest wdrażana na platformie Azure w jeden [zestaw dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Aby zapewnić wysoką dostępność usług SAP Central Services można wdrożyć dwóch maszyn wirtualnych w osobnym zestawie dostępności. Jako platforma o wysokiej dostępności za pomocą automatycznej pracy awaryjnej w przypadku infrastruktury ani oprogramowania, problem, należy użyć klastra trybu Failover w systemie Windows Server lub program Pacemaker (Linux). Aby dowiedzieć się więcej na temat tych wdrożeń, zobacz:

- [Klaster wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udostępnionego dysku klastra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Klaster wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udziału plików](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Wysoka dostępność środowiska SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Azure maszyny wirtualne wysokiej dostępności dla oprogramowania SAP NetWeaver w systemie Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Podobną architekturę ma zastosowanie do warstwy system DBMS oprogramowanie SAP NetWeaver, S/4HANA lub Hybris systemów. Możesz wdrożyć warstwy system DBMS w trybie aktywny/pasywny z rozwiązań klastrowych trybu failover, aby zapewnić ochronę przed awarią infrastruktury ani oprogramowania. Rozwiązanie klastra trybu failover może być specyficzne dla systemu DBMS framework trybu failover systemu Windows Server Failover Clustering i program Pacemaker.

Aby wdrożyć taką samą architekturę przy użyciu strefy dostępności platformy Azure, musisz wprowadzić pewne zmiany na architekturę opisane wcześniej. W tym artykule opisano te zmiany.

## <a name="considerations-for-deploying-across-availability-zones"></a>Zagadnienia dotyczące wdrażania w różnych strefach dostępności


Gdy używasz strefy dostępności, należy wziąć pod uwagę następujące czynności:

- Nie ma żadnych gwarancji dotyczących odległości między różnych strefach dostępności w obrębie regionu platformy Azure.
- Strefy dostępności nie są idealnym rozwiązaniem odzyskiwania po awarii. Klęskami żywiołowymi może spowodować zniszczenia w świecie regionów, w tym duże uszkodzenia infrastruktury w programie power. Odległości między różnymi strefami może nie być wystarczająco duży, aby stanowić odpowiednie rozwiązanie odzyskiwania po awarii.
- Opóźnienie sieci w różnych strefach dostępności nie jest taka sama we wszystkich regionach platformy Azure. W niektórych przypadkach możesz wdrażać i uruchamiać warstwie aplikacji SAP w różnych strefach, ponieważ opóźnienia sieci z jedną strefę do aktywnej maszyny Wirtualnej systemu DBMS jest dopuszczalna. Jednak w niektórych regionach platformy Azure, opóźnienie między aktywnej maszyny Wirtualnej systemu DBMS i wystąpienia aplikacji SAP, podczas wdrażania w różnych strefach, może nie być możliwa do procesów biznesowych SAP. W takich przypadkach architektura wdrożenia powinien być inny, za pomocą architektury aktywny/aktywny dla aplikacji lub architekturę aktywny/pasywny, gdzie jest zbyt duże opóźnienie sieci między strefami.
- Podejmując decyzję o korzystać ze stref dostępności, oparty na decyzję dotyczącą opóźnienie sieci między strefą. Opóźnienie sieci odgrywa ważną rolę w dwóch obszarach:
    - Opóźnienie między dwoma wystąpieniami systemu DBMS, które muszą mieć Replikacja synchroniczna. Im wyższa opóźnienia sieci, tym bardziej prawdopodobne będzie miało wpływ na skalowalność obciążenia.
    - Różnią się w opóźnienia sieci maszyny Wirtualnej z systemem SAP okna dialogowego wystąpienia w strefie przy użyciu aktywnego wystąpienia systemu DBMS i podobne maszyny Wirtualnej w innej strefie. W miarę zwiększania różnica ta wpływ na czas wykonywania procesów biznesowych i partii zadań również zwiększa zależy od tego, czy działają w strefie z bazami danych lub w innej strefie.

W przypadku wdrażania maszyn wirtualnych platformy Azure w strefach dostępności i ustanowić w tym samym regionie platformy Azure przy użyciu rozwiązania pracy awaryjnej, obowiązują pewne ograniczenia:

- Należy użyć [usługi Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) podczas wdrażania strefy dostępności platformy Azure. 
- Mapowanie wyliczenia strefy dla stref fizycznych jest ustalana na podstawie subskrypcji platformy Azure. Jeśli używasz różnych subskrypcji do wdrażania systemów SAP, musisz zdefiniować stref idealne rozwiązanie dla każdej subskrypcji.
- Nie można wdrożyć zestawy dostępności platformy Azure w ramach strefy dostępności platformy Azure. Wybierz jednego lub drugiego jako struktura wdrożenia dla maszyn wirtualnych.
- Nie można użyć [podstawowa usługi Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) tworzenia rozwiązań klastrów opartych na systemie Windows Server Failover Clustering lub program Pacemaker w systemie Linux, trybu failover. Zamiast tego należy użyć [standardowy SKU modułu równoważenia obciążenia Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).



## <a name="the-ideal-availability-zones-combination"></a>Idealne połączenie strefy dostępności
Zanim zdecydujesz, jak korzystać ze stref dostępności, należy określić:

- Opóźnienie sieci między trzy strefy region platformy Azure. Spowoduje to włączenie można wybrać strefy z najniższych opóźnieniem sieci międzystrefowego ruchu w sieci.
- Różnica między opóźnienia maszyny Wirtualnej do maszyny Wirtualnej w ramach jednej ze stref, wybrane i opóźnienie sieci w dwóch strefach wybrane.
- Określenie tego, czy typy maszyn wirtualnych, które są potrzebne do wdrożenia są dostępne w dwóch strefach, które zostały wybrane. Przy użyciu maszyn wirtualnych, szczególnie maszyny wirtualne serii M, mogą wystąpić sytuacje, w których niektórych jednostek SKU są dostępne w dwóch trzy strefy.

## <a name="network-latency-between-and-within-zones"></a>Opóźnienie sieci między oraz w obrębie strefy
Aby określić opóźnienie między różnymi strefami, należy:

- Wdrażanie jednostki SKU maszyny Wirtualnej, którego chcesz użyć wystąpienia systemu DBMS w wszystkie trzy strefy. Upewnij się, że [usługę Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jest włączane, gdy zapoznasz się z tym miary.
- Po znalezieniu dwie strefy z najniższych opóźnieniem sieci, należy wdrożyć kolejnych trzech maszyn wirtualnych jednostki SKU maszyny Wirtualnej, który chcesz użyć jako warstwa aplikacji maszyn wirtualnych w trzech strefach dostępności. Zmierz opóźnienia sieci dla dwóch maszyn wirtualnych systemu DBMS, w dwóch strefach DBMS, które zostały wybrane. 
- Użyj **niping** jako narzędzie pomiarowych. Tego narzędzia, SAP, opisano w sekcji SAP notes pomocy technicznej [#500235](https://launchpad.support.sap.com/#/notes/500235) i [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Skup się na polecenia udokumentowane do pomiarów opóźnienia. Ponieważ **ping** nie działa poprzez usługę Azure Accelerated Networking ścieżki kodu, nie zaleca się używania jej.

Na podstawie miar i dostępność swojej wersji SKU maszyn wirtualnych w strefach dostępności, należy podjąć pewne decyzje:

- Zdefiniuj stref idealne rozwiązanie dla warstwy system DBMS.
- Określ, czy chcesz rozprowadzić active warstwie aplikacji SAP na jednej, dwóch lub wszystkie trzy strefy, oparte na różnice sieci opóźnienia w strefie i w różnych strefach.
- Określ, czy chcesz wdrożyć w konfiguracji aktywny/pasywny lub w konfiguracji aktywny/aktywny z punktu widzenia w aplikacji. (Te konfiguracje są wyjaśnione w dalszej części tego artykułu).

Podejmując decyzje te, również wziąć pod uwagę SAP zalecenia dotyczące opóźnienia sieci, zgodnie z opisem w Uwaga SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> Pomiary i decyzje podjęte przez użytkownika są prawidłowe dla subskrypcji platformy Azure użytą podczas trwania pomiarów. Jeśli używasz innej subskrypcji platformy Azure, należy powtórzyć pomiarów. Mapowania stref wyliczenia mogą być różne dla innej subskrypcji platformy Azure.


> [!IMPORTANT]
> Oczekuje się, zapewnia pomiary, opisanych wcześniej różne wyniki w każdym regionie platformy Azure, która obsługuje [strefy dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Nawet jeśli wymagań opóźnienia sieci są takie same, może być konieczne przyjęcie strategii wdrażania różne w różnych regionach platformy Azure, ponieważ opóźnienie sieci między strefami mogą być różne. W niektórych regionach platformy Azure opóźnienie sieci między trzy strefy może być bardzo różne. W innych regionach opóźnienie sieci między trzy strefy może być bardziej jednolite. Oświadczenie, które zawsze istnieje opóźnienie sieciowe między 1 i 2 milisekund jest nieprawidłowy. Opóźnienie sieci w różnych strefach dostępności w regionach platformy Azure nie może być uogólniony.

## <a name="activeactive-deployment"></a>Wdrożenie aktywny/aktywny
Ta architektura wdrożenia jest nazywany aktywny/aktywny, ponieważ wdrożenia aktywnych serwerów aplikacji SAP w dwóch lub trzech strefach. Wystąpienie usług SAP Central Services, który używa umieścić w kolejce replikacji będą wdrażane między dwiema strefami. Dotyczy to także warstwy system DBMS, która zostanie wdrożony w tej samej strefach jako usługi centralne SAP.

Rozważając tę konfigurację, trzeba znaleźć dwóch strefach dostępności w Twoim regionie tego opóźnienia sieci między strefami oferty, który jest dopuszczalny dla obciążenia i usługi replikacji synchronicznej DBMS. Należy zawsze mieć pewność, że różnica między opóźnienia sieci w ramach stref, które wybrano i opóźnienie sieci między strefami nie jest zbyt duży. Jest to, ponieważ nie ma duże różnice, w zależności od tego, czy zadanie jest uruchamiane w strefie z serwerem systemu DBMS lub na strefy, w razie uruchomione zadania wsadowe lub procesami biznesowymi. Niektóre zmiany są akceptowane, ale nie czynniki różnica.

Uproszczony schemat wdrożenia aktywny/aktywny w dwóch strefach może wyglądać następująco:

![Wdrożenie strefy aktywny/aktywny](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

W przypadku tej konfiguracji obowiązują następujące zastrzeżenia:

- Możesz traktować strefy dostępności platformy Azure jako domenach błędów i aktualizacji dla wszystkich maszyn wirtualnych, ponieważ nie można wdrożyć zestawy dostępności w strefach dostępności platformy Azure.
- Dla klastrów trybu failover usług SAP Central Services i warstwy system DBMS usług równoważenia obciążenia, należy użyć [standardowych jednostek SKU usługi Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Podstawowy moduł równoważenia obciążenia nie będą działać w różnych strefach.
- Sieć wirtualna platformy Azure, która została wdrożona do hostowania systemu SAP, wraz z jej podsieci jest rozciągnięty w różnych strefach. Nie potrzebujesz oddzielnych sieci wirtualnych w każdej strefie.
- Dla wszystkich maszyn wirtualnych, wdrażania, należy użyć [usługi Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Dyski niezarządzane nie są obsługiwane w przypadku wdrożeń strefowych.
- Usługa Azure Premium Storage i [magazyn Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nie obsługuje żadnego typu replikacji magazynu w różnych strefach. Ważne dane muszą być replikowane w aplikacji (DBMS lub usług SAP Central Services).
- Dotyczy to do katalogu udostępnionego sapmnt, który jest udostępniony dysk (Windows), udział CIFS (Windows) lub udziału NFS (Linux). Należy użyć technologii, która replikuje tych udostępnionych dysków lub udziałów między strefą. Te technologie są obsługiwane:
  - Dla Windows, rozwiązanie klastra, używa oprogramowanie SIOS DataKeeper zgodnie z opisem w [klastra wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udostępnionego dysku klastra na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
  - W systemie SUSE Linux, systemu plików NFS udostępniania, które jest skompilowane, zgodnie z opisem w [wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
    Obecnie rozwiązania, który używa serwera plików skalowalnego w poziomie firmy Microsoft, zgodnie z opisem w [przygotować infrastrukturę platformy Azure SAP wysokiej dostępności przy użyciu Windows trybu failover klastra i udział plików dla wystąpień SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), nie jest obsługiwane w różnych strefach.
- Trzeci strefa jest używana do obsługi urządzeń interwencja w przypadku, gdy tworzysz [klastra program Pacemaker systemu Linux SUSE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) lub wystąpień dodatkowych aplikacji.
- Aby osiągnąć spójność kluczowych procesów biznesowych w czasie wykonywania, możesz spróbować bezpośrednie niektórych zadań wsadowych i użytkowników do wystąpienia aplikacji, które są w strefie przy użyciu aktywnego wystąpienia systemu DBMS, za pomocą SAP batch grup serwerów, grup logowania lub RFC grup. Jednak w przypadku strefowych przejściu w tryb failover, należy ręcznie przenieść te grupy do wystąpień działających na maszynach wirtualnych, które są w strefie przy użyciu aktywnej maszyny Wirtualnej bazy danych.  
- Można wdrożyć jedno okno nieaktywni we wszystkich strefach. To jest umożliwienie natychmiastowy powrót do pojemności zasobu poprzedniej wersji portalu w przypadku strefy używane przez części wystąpień aplikacji usługi.


## <a name="activepassive-deployment"></a>Aktywny/pasywny wdrożenia
Jeśli nie możesz znaleźć dopuszczalne różnica między opóźnienie sieci, w jednej strefie i opóźnienia w ruchu sieciowego między strefami, można wdrożyć architekturę, która zawiera znak aktywny/pasywny z SAP aplikacji warstwy punktu widzenia. Należy zdefiniować *active* strefy, który jest strefy, w przypadku, gdy wdrażanie warstwy kompletnej aplikacji, a w przypadku, gdy użytkownik spróbuje uruchomić zarówno active DBMS, jak i wystąpienia usług SAP Central Services. Dzięki takiej konfiguracji musisz upewnij się, że nie masz czasu wykonywania extreme odmiany w zależności od tego, czy zadanie jest uruchamiane w strefie z aktywnego wystąpienia systemu DBMS lub nie za transakcje biznesowe i zadania usługi batch.

Podstawowy układ architektury wygląda następująco:

![Wdrożenie strefy aktywny/pasywny](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

W przypadku tej konfiguracji obowiązują następujące zastrzeżenia:

- Zestawy dostępności nie można wdrożyć w strefach dostępności platformy Azure. Tak w tym przypadku masz jedną domenę aktualizacji i błędów na warstwie aplikacji. To, ponieważ jest ona wdrożona tylko w jednej strefie. Ta konfiguracja jest pożądane, nieco mniej niż architektury referencyjnej sugeruje wdrażania warstwy aplikacji w zestawie dostępności platformy Azure.
- Gdy używasz tej architektury, musisz uważnie monitorować stan i próbować zachować aktywnych wystąpień system DBMS i usług SAP Central Services w tej samej strefie jako warstwa Twojej wdrożonej aplikacji. W przypadku pracy awaryjnej usługi centralne SAP lub wystąpienia systemu DBMS chcesz upewnić się, że możesz ręcznie przełączać do strefy z warstwą aplikacji SAP, które są wdrażane tak szybko, jak to możliwe.
- Dla klastrów trybu failover usług SAP Central Services i warstwy system DBMS usług równoważenia obciążenia, należy użyć [standardowych jednostek SKU usługi Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Podstawowy moduł równoważenia obciążenia nie będą działać w różnych strefach.
- Sieć wirtualna platformy Azure, która została wdrożona do hostowania systemu SAP, wraz z jej podsieci jest rozciągnięty w różnych strefach. Nie potrzebujesz oddzielnych sieci wirtualnych w każdej strefie.
- Dla wszystkich maszyn wirtualnych, wdrażania, należy użyć [usługi Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Dyski niezarządzane nie są obsługiwane w przypadku wdrożeń strefowych.
- Usługa Azure Premium Storage i [magazyn Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nie obsługuje żadnego typu replikacji magazynu w różnych strefach. Ważne dane muszą być replikowane w aplikacji (DBMS lub usług SAP Central Services).
- Dotyczy to do katalogu udostępnionego sapmnt, który jest udostępniony dysk (Windows), udział CIFS (Windows) lub udziału NFS (Linux). Należy użyć technologii, która replikuje tych udostępnionych dysków lub udziałów między strefą. Te technologie są obsługiwane:
    - Dla Windows, rozwiązanie klastra, używa oprogramowanie SIOS DataKeeper zgodnie z opisem w [klastra wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udostępnionego dysku klastra na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - W systemie SUSE Linux, systemu plików NFS udostępniania, które jest skompilowane, zgodnie z opisem w [wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
  Obecnie rozwiązania, który używa serwera plików skalowalnego w poziomie firmy Microsoft, zgodnie z opisem w [przygotować infrastrukturę platformy Azure SAP wysokiej dostępności przy użyciu Windows trybu failover klastra i udział plików dla wystąpień SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), nie jest obsługiwane w różnych strefach.
- Trzeci strefa jest używana do obsługi urządzeń interwencja w przypadku, gdy tworzysz [klastra program Pacemaker systemu Linux SUSE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) lub wystąpień dodatkowych aplikacji.
- Należy wdrożyć nieaktywni maszyn wirtualnych na platformie pasywne strefy (z punktu widzenia DBMS), aby można było zacząć zasobów aplikacji w razie awarii strefy.
    - [Usługa Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) nie może obecnie aktywne maszyny wirtualne można replikować do nieaktywni maszyn wirtualnych między strefami. 
- Kolejności należy zainwestować w usłudze automation, która pozwala, w razie awarii strefy, aby automatycznie rozpocząć warstwie aplikacji SAP w drugiej strefie.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Połączone wysokiej dostępności i Konfiguracja odzyskiwania po awarii
Firma Microsoft nie udostępnia żadnych informacji na temat geograficznej odległości między obiektami tego hosta innej strefy dostępności platformy Azure w regionie platformy Azure. Jednak niektórzy klienci używają stref połączone konfiguracji wysokiej dostępności i odzyskiwania po awarii, która zapewnia cel punktu odzyskiwania (RPO) o wartości zero. Oznacza to, że nie może utracić wszelkie transakcje zatwierdzone bazy danych, nawet w przypadku odzyskiwania po awarii. 

> [!NOTE]
> Zaleca się użycie konfiguracji takich jak to tylko w pewnych okolicznościach. Na przykład użytkownik może użyć, jeśli dane nie mogą opuścić region platformy Azure ze względów bezpieczeństwa i zgodności. 

Poniżej przedstawiono przykładowy wygląd takiej konfiguracji:

![Łączny koszt wysokiej dostępności w strefach](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

W przypadku tej konfiguracji obowiązują następujące zastrzeżenia:

- Jesteś jedną przy założeniu, że ma znaczną odległość między obiektami hostujące strefę dostępności lub jest wymuszone w celu pozostania w danym regionie platformy Azure. Zestawy dostępności nie można wdrożyć w strefach dostępności platformy Azure. Tak w tym przypadku masz jedną domenę aktualizacji i błędów na warstwie aplikacji. To, ponieważ jest ona wdrożona tylko w jednej strefie. Ta konfiguracja jest pożądane, nieco mniej niż architektury referencyjnej sugeruje wdrażania warstwy aplikacji w zestawie dostępności platformy Azure.
- Gdy używasz tej architektury, musisz uważnie monitorować stan i próbować zachować aktywnych wystąpień system DBMS i usług SAP Central Services w tej samej strefie jako warstwa Twojej wdrożonej aplikacji. W przypadku pracy awaryjnej usługi centralne SAP lub wystąpienia systemu DBMS chcesz upewnić się, że możesz ręcznie przełączać do strefy z warstwą aplikacji SAP, które są wdrażane tak szybko, jak to możliwe.
- Musisz mieć wystąpienia aplikacji produkcyjnych wstępnie zainstalowane na maszynach wirtualnych, systemem aktywnych wystąpień pytań i odpowiedzi w aplikacji.
- W przypadku awarii strefy wyłączenie wystąpień aplikacji pytań i odpowiedzi, a zamiast tego uruchomić wystąpień produkcyjnych. Należy pamiętać o tym, czy należy użyć nazwy wirtualnego wystąpienia aplikacji, aby umożliwić.
- Dla klastrów trybu failover usług SAP Central Services i warstwy system DBMS usług równoważenia obciążenia, należy użyć [standardowych jednostek SKU usługi Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Podstawowy moduł równoważenia obciążenia nie będą działać w różnych strefach.
- Sieć wirtualna platformy Azure, która została wdrożona do hostowania systemu SAP, wraz z jej podsieci jest rozciągnięty w różnych strefach. Nie potrzebujesz oddzielnych sieci wirtualnych w każdej strefie.
- Dla wszystkich maszyn wirtualnych, wdrażania, należy użyć [usługi Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Dyski niezarządzane nie są obsługiwane w przypadku wdrożeń strefowych.
- Usługa Azure Premium Storage i [magazyn Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nie obsługuje żadnego typu replikacji magazynu w różnych strefach. Ważne dane muszą być replikowane w aplikacji (DBMS lub usług SAP Central Services).
- Dotyczy to do katalogu udostępnionego sapmnt, który jest udostępniony dysk (Windows), udział CIFS (Windows) lub udziału NFS (Linux). Należy użyć technologii, która replikuje tych udostępnionych dysków lub udziałów między strefą. Te technologie są obsługiwane:
    - Dla Windows, rozwiązanie klastra, używa oprogramowanie SIOS DataKeeper zgodnie z opisem w [klastra wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udostępnionego dysku klastra na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - W systemie SUSE Linux, systemu plików NFS udostępniania, które jest skompilowane, zgodnie z opisem w [wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).

  Obecnie rozwiązania, który używa serwera plików skalowalnego w poziomie firmy Microsoft, zgodnie z opisem w [przygotować infrastrukturę platformy Azure SAP wysokiej dostępności przy użyciu Windows trybu failover klastra i udział plików dla wystąpień SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), nie jest obsługiwane w różnych strefach.
- Trzeci strefa jest używana do obsługi urządzeń interwencja w przypadku, gdy tworzysz [klastra program Pacemaker systemu Linux SUSE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) lub wystąpień dodatkowych aplikacji.





## <a name="next-steps"></a>Kolejne kroki
Poniżej przedstawiono niektóre kolejne kroki dotyczące wdrażania w różnych strefach dostępności platformy Azure:

- [Klaster wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udostępnionego dysku klastra na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Przygotowywanie infrastruktury platformy Azure SAP wysokiej dostępności przy użyciu Windows trybu failover klastra i udział plików dla wystąpień SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






