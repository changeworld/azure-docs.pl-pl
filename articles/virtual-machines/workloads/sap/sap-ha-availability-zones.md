---
title: Konfiguracje obciążeń SAP o strefy dostępności platformy Azure | Dokumentacja firmy Microsoft
description: Architektura wysokiej dostępności i scenariusze SAP NetWeaver przy użyciu stref dostępności platformy Azure
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
ms.openlocfilehash: e319fb5bc1e0ed3354a09b685405656c7f3131bc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858809"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Konfiguracje obciążeń SAP o strefy dostępności platformy Azure

Jedną z funkcji wysokiej dostępności, platforma Azure oferuje jest [strefy dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Przy użyciu stref dostępności zwiększa ogólną dostępność obciążeń SAP na platformie Azure. Strefy dostępności są oferowane w kilku [regionów platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/) już. Więcej regionów będą zgodne. 

Podstawowa architektura SAP wysokiej dostępności można opisać wyświetlane na poniższej ilustracji:

![Standardowa konfiguracja wysokiej dostępności](./media/sap-ha-availability-zones/standard-ha-config.png)

Warstwa aplikacji SAP jest wdrażana na platformie Azure w jeden [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Aby uzyskać wysoką dostępność usług SAP Central Services wdrożysz dwie maszyny wirtualne w osobnym zestawie dostępności. Jako platforma o wysokiej dostępności za pomocą automatycznej pracy awaryjnej w przypadku infrastruktury ani oprogramowania, problem, należy użyć usługi klastra pracy awaryjnej Windows lub program Pacemaker (Linux). Poniżej przedstawiono dokumentów ze szczegółami dotyczącymi takich wdrożeń:

- [Klaster wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udostępnionego dysku klastra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Klaster wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udziału plików](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Wysoka dostępność środowiska SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Azure maszyny wirtualne wysokiej dostępności dla oprogramowania SAP NetWeaver w systemie Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Podobną architekturę ma zastosowanie do warstwy system DBMS oprogramowanie SAP NetWeaver, S/4HANA lub Hybris systemów. Możesz wdrożyć warstwy system DBMS w trybie aktywny/pasywny z rozwiązań klastrowych trybu failover, aby zapewnić ochronę przed awarią infrastruktury ani oprogramowania. Rozwiązanie klastra trybu failover może być albo określonego trybu failover framework DBMS, a usługi klastra pracy awaryjnej Windows lub program Pacemaker. 

Aby wdrożyć taką samą architekturę przy użyciu stref dostępności platformy Azure, potrzebne są pewne zmiany w architekturze opisanych powyżej. Zmiany te są opisane w tym dokumencie.

## <a name="considerations-deploying-across-availability-zones"></a>Zagadnienia dotyczące wdrażania w różnych strefach dostępności

Korzystając z strefy dostępności, istnieje kilka rzeczy, które należy rozważyć:

- Strefy dostępności platformy Azure nie gwarantuje pewne odległość między różnymi strefami w obrębie jednego regionu platformy Azure.
- Strefy dostępności platformy Azure nie są idealnym rozwiązaniem odzyskiwania po awarii. Klęskami żywiołowymi może spowodować, że szerokiego rozkładu szkody w niektórych regionach świata, w tym duże szkody w infrastrukturze usługi power. Odległość między różnymi strefami może nie być wystarczająco duży, aby zakwalifikować się jako odpowiednie rozwiązanie odzyskiwania po awarii.
- Opóźnienie sieci w różnych strefach dostępności różni się w różnych regionach platformy Azure. Istnieją przypadki, gdzie możesz wdrażać i uruchamiać w różnych strefach warstwie aplikacji SAP, opóźnienia sieci z jedną strefę do aktywnej maszyny Wirtualnej systemu DBMS jest dopuszczalne z procesu wpływ na działalność. Jednak w niektórych regionach platformy Azure opóźnienia między aktywnej maszyny Wirtualnej systemu DBMS i wystąpienie aplikacji SAP, wdrożonych w różnych strefach może być zbyt inwazyjne i nie do przyjęcia dla procesów biznesowych SAP. W rezultacie architektura wdrożenia musi być inne w przypadku architektury aktywny/aktywny dla aplikacji lub architektury aktywny/pasywny, gdzie jest zbyt duże opóźnienie między strefy.
- Zdecyduj, w których konfiguracji można użyć stref dostępności platformy Azure, oparte na opóźnienie sieci, który ma być mierzony między różnymi strefami. Opóźnienie sieci odgrywa ważną rolę w dwóch obszarach:
    - Opóźnienie między tymi wystąpieniami systemu DBMS, które muszą mieć Replikacja synchroniczna ustanowione. Tym większe opóźnienie sieci, tym większe możliwości wpływ na skalowalność obciążeń
    - Różnią się w opóźnienia sieci maszyny Wirtualnej z systemem SAP okna dialogowego wystąpienia w strefie przy użyciu aktywnego wystąpienia systemu DBMS i podobne maszyny Wirtualnej w innej strefie. Im większa różnica ta, im większy wpływ w czasie wykonywania procesów biznesowych i zadań wsadowych, zależy od tego, czy działają w strefie z bazami danych lub w innej strefie.


Istnieją pewne ograniczenia, podczas wdrażania maszyn wirtualnych platformy Azure w różnych strefach dostępności i ustanowienie rozwiązania pracy awaryjnej w tym samym regionie platformy Azure. Te ograniczenia zostały omówione w poniższej liście:

- Za pomocą [usługi Azure managed disks](https://azure.microsoft.com/services/managed-disks/) jest obowiązkowa w przypadku wdrażania w strefach dostępności platformy Azure 
- Mapowanie wyliczenia strefy dla stref fizycznych jest ustalana na podstawie subskrypcji platformy Azure. Korzystając z różnych subskrypcji do wdrażania systemów SAP, musisz zdefiniować stref idealne rozwiązanie dla każdej subskrypcji.
- Nie można wdrożyć zestawy dostępności platformy Azure w strefie dostępności platformy Azure. Wybierz strefę dostępności platformy Azure lub dostępności platformy Azure wynoszą platformy wdrażania w przypadku maszyn wirtualnych.
- Nie można użyć [podstawowa usługi Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) tworzenia rozwiązań klastra na podstawie usługi klastra pracy awaryjnej Windows lub program Pacemaker w systemie Linux, trybu failover. Zamiast tego należy użyć [standardowy SKU modułu równoważenia obciążenia platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)



## <a name="ideal-availability-zones-combination"></a>Strefy dostępności idealne połączenie
Zanim zdecydujesz, jak korzystać z strefy dostępności, należy wykonać badania, który zapewnia:

- Opóźnienie sieci między trzema różnymi strefami region platformy Azure. Tak które można wybrać strefy za pomocą najmniejszego opóźnienia sieci w wielu strefy ruchu sieciowego
- Różnica między opóźnienia maszyny Wirtualnej do maszyny Wirtualnej w ramach jednej ze stref wybrane przez Ciebie i opóźnienie sieci w dwóch strefach została wybrana opcja
- Instrukcja, czy maszyna wirtualna typów, należy wdrożyć są dostępne w dwóch strefach w wybranym. Szczególnie w przypadku maszyn wirtualnych serii M będą występować sytuacje, gdzie różnych jednostek SKU maszyn wirtualnych serii M będą dostępne tylko w dwóch trzy strefy

### <a name="network-latency-between-zones-and-within-zone"></a>Opóźnienie sieci między strefami, jak i w obrębie strefy
Aby dowiedzieć się, jakie jest opóźnienie między różnymi strefami, należy:

- Wdrażanie jednostki SKU maszyny Wirtualnej, którego chcesz użyć wystąpienia systemu DBMS w wszystkie trzy strefy. Upewnij się, że [usługę Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jest włączona, podczas wykonywania tego pomiaru.
- Jak możesz znaleźć dwie strefy z najniższych opóźnieniem sieci, należy wdrożyć kolejnych trzech maszyn wirtualnych ma być używany jako warstwa aplikacji maszyn wirtualnych w trzech strefach dostępności jednostki SKU maszyny Wirtualnej. Pomiar opóźnienia sieci względem dwóch "systemu DBMS na maszynach wirtualnych' w dwóch różnych stref"DBMS"ulubionego. 
- Jako narzędzie do mierzenia użycia **niping**. Narzędzia programu SAP, który działa zgodnie z opisem w sekcji SAP notes pomocy technicznej [#500235](https://launchpad.support.sap.com/#/notes/500235) i [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Skup się na polecenia SAP udokumentowane do pomiarów opóźnienia. Za pomocą **ping** nie jest zalecane, ponieważ **ping** nie działa za pośrednictwem platformy Azure przyspieszonej sieci ścieżek kodu.

Na podstawie pomiarów i dostępność swojej wersji SKU maszyn wirtualnych w strefach dostępności, musisz podjąć decyzje dotyczące:

- Zdefiniuj stref idealne rozwiązanie dla warstwy system DBMS
- Odpowiedź na pytanie, aby rozdystrybuować active warstwie aplikacji SAP z jednego, dwóch lub wszystkie trzy strefy, na podstawie różnice sieci opóźnienia w strefie a w strefach
- Odpowiedz na pytanie, jeśli chcesz wdrożyć aktywny/pasywny lub w konfiguracji aktywny/aktywny z punktu widzenia aplikacji (zobacz w dalszej części)

Również podejmowanie tych decyzji Odwołaj zalecenia dotyczące opóźnienia sieci firmy SAP, zgodnie z opisem w Uwaga SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

### <a name="be-aware-of"></a>Należy pamiętać o

> [!IMPORTANT]
> Pomiary i decyzje podjęte przez użytkownika, są prawidłowe dla subskrypcji platformy Azure została użyta, dzięki czemu te pomiary. Jeśli używasz innej subskrypcji platformy Azure, należy powtórzyć pomiarów, ponieważ mapowania stref wyliczany mogą różnić się w innej subskrypcji platformy Azure.


> [!IMPORTANT]
> Oczekuje się, pomiarów, jak wykonać powyższe są wyświetlane różne wyniki w każdym regionie platformy Azure, która obsługuje [strefy dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Nawet jeśli wymagań opóźnienia sieci są takie same, może być konieczne przyjęcie strategii wdrażania różne w różnych regionach platformy Azure, ponieważ opóźnienie sieci między strefami mogą być różne. Oczekuje się, że w niektórych regionach platformy Azure, opóźnienie sieci między trzema różnymi strefami może być bardzo różne. W innych regionach, opóźnienie sieci między trzema różnymi strefami jest bardziej jednolite. Oświadczenie, ponieważ **zawsze** jest opóźnienie sieci w różnych strefach 1 milisekundy na 2 milisekundy **niewłaściwego**. Opóźnienie sieci w różnych strefach dostępności w regionach platformy Azure nie może być uogólniony.


## <a name="activeactive-deployment"></a>Wdrożenie aktywny/aktywny
Ta architektura wdrożenia jest nazywany aktywny/aktywny, ponieważ wdrażania aktywnych wystąpień okna dialogowego SAP w dwóch lub trzech strefach. Usługi centralne SAP przy użyciu replikacji w kolejce będzie można wdrożyć między dwiema strefami. Dotyczy to także warstwy system DBMS, która ma być wdrażana w tej samej strefy usługi centralne SAP.

Aby rozważają takiej konfiguracji, należy znaleźć dwóch strefach dostępności w Twoim regionie udostępniające opóźnienie sieci między strefy, który jest dopuszczalny dla obciążenia i usługi replikacji synchronicznej DBMS na. Ponadto chcesz różnica między opóźnienia sieci w ramach stref wybrano i w strefie opóźnienie sieci nie jest zbyt duży. Przyczyny dla niego się czy nie ma zbyt duże różnice w czasie wykonywania procesów biznesowych lub zadania zależne od usługi batch, czy zadanie działa w strefę z serwerem systemu DBMS lub w strefie. Niektóre zmiany są akceptowane, ale nie czynniki różnica.

Uproszczony schemat wdrożenia aktywny/aktywny w dwóch strefach może wyglądać następująco:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

W przypadku tej konfiguracji obowiązują następujące zastrzeżenia:

- Można traktować stref dostępności platformy Azure jako domenach błędów i aktualizacji dla wszystkich maszyn wirtualnych, ponieważ nie można wdrożyć zestawy dostępności w strefach dostępności platformy Azure
- Moduły równoważenia obciążenia platformy Azure, użyj dla klastrów trybu failover usług SAP Central Services i warstwy system DBMS, muszą być [modułu równoważenia obciążenia standardowej jednostki SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Podstawowego modułu równoważenia obciążenia nie będzie działać w strefach
- Usługa Azure virtual network i jej podsieci wdrożono SAP system hosta są rozciągnięty w strefach. Możesz **nie ma potrzeby** oddzielnych sieci wirtualne dla każdej strefy
- Możesz wdrożyć wszystkie maszyny wirtualne, można użyć [usługi Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Dyski niezarządzane nie są obsługiwane w przypadku wdrożeń strefowej
- Usługa Azure Premium storage lub [magazyn Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nie obsługuje żadnego typu replikacji magazynu w strefach. Dlatego jest odpowiedzialny za aplikacji (DBMS lub usług SAP Central Services) do replikacji danych
- Samo dotyczy katalogu sapmnt udostępnionego, który jest udostępniony dysk (Windows), udział CIFS (Windows) lub udziału NFS (Linux). Należy użyć technologii replikacji między strefą udostępnionego dysku lub udziału. W tej chwili obsługiwane są następujące technologie:
    - Dla Windows, rozwiązanie klastra, używa oprogramowanie SIOS Datakeeper zgodnie z opisem w [klastra wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udostępnionego dysku klastra na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) jest obsługiwany w strefach
    - W systemie SUSE Linux, systemu plików NFS udostępniania, które jest skompilowane, zgodnie z opisem w [wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) jest obsługiwana
    - Obecnie rozwiązanie za pomocą Windows usługi plików skalowalnego w poziomie (SOFS), zgodnie z opisem w [przygotować infrastrukturę platformy Azure SAP wysokiej dostępności przy użyciu Windows trybu failover klastra i udział plików dla wystąpień SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **nie jest obsługiwany w strefach**
- Trzeci strefa jest używana do obsługi urządzeń interwencja w przypadku, gdy tworzysz [klastra program pacemaker w systemie SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) lub wystąpień dodatkowych aplikacji
- Aby osiągnąć spójność kluczowych procesów biznesowych w czasie wykonywania, możesz spróbować bezpośrednie niektórych zadań wsadowych i użytkowników do wystąpienia aplikacji, które są w strefie przy użyciu aktywnego wystąpienia systemu DBMS, za pomocą serwera przetwarzania wsadowego SAP grup, grup logowania lub grup RFC. Jednak w przypadku przejścia w tryb failover strefowych należy ręcznie przenieść te grupy do wystąpień działających na maszynach wirtualnych w strefie z aktywnej maszyny Wirtualnej bazy danych.  
- Określenie, czy wdrożenie niektórych wystąpień nieaktywni okna dialogowego, w każdej strefy, aby można było od razu przejść do pojemności zasobu poprzedniej wersji portalu, jeśli strefa używane przez części wystąpień aplikacji jest poza eksploatacją


## <a name="activepassive-deployment"></a>Aktywny/pasywny wdrożenia
Jeśli nie możesz znaleźć dopuszczalne różnica między opóźnienia sieci w ramach jednej strefy i wielu ruchu strefy w sieci, architektury, które można wdrożyć ma znak aktywny/pasywny z SAP aplikacji warstwy punktu widzenia. Gdy wdrażanie warstwy kompletnej aplikacji i gdzie próby uruchomienia zarówno aktywnego system DBMS i usług SAP Central Services wystąpienia są definiowane strefie "active", czyli strefy. Dzięki takiej konfiguracji, należy upewnić się, nie masz extreme wykonywania zmian w transakcje biznesowe i zadań wsadowych, zależy od tego, czy zadanie jest uruchamiane w strefę z aktywnego wystąpienia systemu DBMS lub nie.

Podstawowy układ takich architektury wygląda następująco:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

W przypadku tej konfiguracji obowiązują następujące zastrzeżenia:

- Zestawy dostępności nie można wdrożyć w strefach dostępności platformy Azure. W związku z tym w tym przypadku masz jedną domenę aktualizacji i błędów na warstwie aplikacji. Przyczyną jest to, że tylko został wdrożony w jednej strefie. Ta konfiguracja jest niewielkie setback, w porównaniu do architektury referencyjnej, która przewiduje wdrażania warstwy aplikacji w zestawie dostępności platformy Azure.
- Działania takie architekturę, należy ściśle monitorować, a następnie spróbuj przechowywać aktywnych wystąpień system DBMS i SAP Central services w tej samej strefie jako warstwa Twojej wdrożonej aplikacji. W przypadku pracy awaryjnej usługi centralne SAP lub wystąpienia systemu DBMS chcesz upewnić się, że możesz ręcznie przełączać do strefy z warstwy aplikacji SAP, które są wdrożone w możliwie jak najszybciej
- Moduły równoważenia obciążenia platformy Azure, użyj dla klastrów trybu failover usług SAP Central Services i warstwy system DBMS, muszą być [modułu równoważenia obciążenia standardowej jednostki SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Podstawowego modułu równoważenia obciążenia nie będzie działać w strefach
- Usługa Azure virtual network i jej podsieci wdrożono SAP system hosta są rozciągnięty w strefach. Możesz **nie ma potrzeby** oddzielnych sieci wirtualne dla każdej strefy
- Dla wszystkich maszyn wirtualnych, wdrażania, należy użyć [usługi Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Dyski niezarządzane nie są obsługiwane w przypadku wdrożeń strefowej
- Usługa Azure Premium storage lub [magazyn Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nie obsługuje żadnego typu replikacji magazynu w strefach. Dlatego jest odpowiedzialny za aplikacji (DBMS lub usług SAP Central Services) do replikacji danych
- Samo dotyczy katalogu sapmnt udostępnionego, który jest udostępniony dysk (Windows), udział CIFS (Windows) lub udziału NFS (Linux). Należy użyć technologii replikacji między strefą udostępnionego dysku lub udziału. W tej chwili obsługiwane są następujące technologie:
    - Dla Windows, rozwiązanie klastra, używa oprogramowanie SIOS Datakeeper zgodnie z opisem w [klastra wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udostępnionego dysku klastra na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) jest obsługiwany w strefach
    - W systemie SUSE Linux, systemu plików NFS udostępniania, które jest skompilowane, zgodnie z opisem w [wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) jest obsługiwana
    - Obecnie rozwiązanie za pomocą Windows usługi plików skalowalnego w poziomie (SOFS), zgodnie z opisem w [przygotować infrastrukturę platformy Azure SAP wysokiej dostępności przy użyciu Windows trybu failover klastra i udział plików dla wystąpień SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **nie jest obsługiwany w strefach**
- Trzeci strefa jest używana do obsługi urządzeń interwencja w przypadku, gdy tworzysz [klastra program pacemaker w systemie SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) lub wystąpień dodatkowych aplikacji
- Wdrażanie nieaktywni maszyn wirtualnych w strefie pasywnym (z punktu widzenia DBMS) aby można było uruchomić zasobów aplikacji w razie awarii strefy
    - Nie można użyć [usługi Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) do replikowania maszyn wirtualnych active nieaktywni maszyn wirtualnych między strefami. W tym momencie nie jest możliwe do zrealizowania takich funkcji usługi Azure Site Recovery
- Inwestować do usługi automation, która pozwala, w razie awarii strefy, aby automatycznie rozpocząć warstwie aplikacji SAP w drugiej strefie

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Połączone wysokiej dostępności i Konfiguracja odzyskiwania po awarii
Microsoft nie udostępnia żadnych informacji na temat geograficznej odległości między obiektami tego hostowanie stref różnych dostępności platformy Azure w regionie platformy Azure. Pomimo tego faktu, niektórzy klienci korzystają ze strefy połączone konfiguracji wysokiej dostępności i odzyskiwania po awarii, która zapewnia **R**odzyskiwania w trybie **P**awsze **O**celu (RPO) o wartości zero. Oznacza to, że nie stracisz wszystkich transakcji zatwierdzone bazy danych, nawet w przypadku odzyskiwania po awarii. 

> [!NOTE]
> Konfiguracja następująco jest zalecane tylko określonych okolicznościach. Na przykład przypadkach, gdy dane nie mogą opuścić regionu platformy Azure z powodów bezpieczeństwa i zgodności. 

Przykładem, jak może wyglądać taka konfiguracja jest przedstawiona na poniższej ilustracji:

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

W przypadku tej konfiguracji obowiązują następujące zastrzeżenia:

- Są albo przy założeniu, że ma znaczną odległość między obiektami hostujące strefę dostępności. Lub jest wymuszone pozostanie z danym regionie platformy Azure. Zestawy dostępności nie można wdrożyć w strefach dostępności platformy Azure. W związku z tym w tym przypadku ma zostać zakończony z jednej domeny aktualizacji i błędów na warstwie aplikacji. Przyczyną jest to, że tylko został wdrożony w jednej strefie. Jest to setback, w porównaniu do architektury referencyjnej, która przewiduje wdrażania warstwy aplikacji w zestawie dostępności platformy Azure.
- Działania takie architekturę, należy ściśle monitorować, a następnie spróbuj przechowywać aktywnych wystąpień system DBMS i SAP Central services w tej samej strefie jako warstwa Twojej wdrożonej aplikacji. W przypadku pracy awaryjnej usługi centralne SAP lub wystąpienia systemu DBMS chcesz upewnić się, że możesz ręcznie przełączać do strefy z warstwy aplikacji SAP, które są wdrożone w możliwie jak najszybciej
- Masz wystąpień aplikacji produkcyjnych wstępnie zainstalowane na maszynach wirtualnych, systemem aktywnych wystąpień pytań i odpowiedzi w aplikacji.
- W przypadku awarii strefowych wyłączenie wystąpień aplikacji pytań i odpowiedzi, a zamiast tego uruchomić wystąpień produkcyjnych. Należy pamiętać, że potrzebne do pracy za pomocą nazwy wirtualnego wystąpienia aplikacji umożliwić
- Moduły równoważenia obciążenia platformy Azure, użyj dla klastrów trybu failover usług SAP Central Services i warstwy system DBMS, muszą być [modułu równoważenia obciążenia standardowej jednostki SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Podstawowego modułu równoważenia obciążenia nie będzie działać w strefach
- Usługa Azure virtual network i jej podsieci wdrożono SAP system hosta są rozciągnięty w strefach. Możesz **nie ma potrzeby** oddzielnych sieci wirtualne dla każdej strefy
- Dla wszystkich maszyn wirtualnych, wdrażania, należy użyć [usługi Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Dyski niezarządzane nie są obsługiwane w przypadku wdrożeń strefowej
- Usługa Azure Premium storage lub [magazyn Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nie obsługuje żadnego typu replikacji magazynu w strefach. Dlatego jest odpowiedzialny za aplikacji (DBMS lub usług SAP Central Services) do replikacji danych
- Samo dotyczy katalogu sapmnt udostępnionego, który jest udostępniony dysk (Windows), udział CIFS (Windows) lub udziału NFS (Linux). Należy użyć technologii replikacji między strefą udostępnionego dysku lub udziału. W tej chwili obsługiwane są następujące technologie:
    - Dla Windows, rozwiązanie klastra, używa oprogramowanie SIOS Datakeeper zgodnie z opisem w [klastra wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udostępnionego dysku klastra na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) jest obsługiwany w strefach
    - W systemie SUSE Linux, systemu plików NFS udostępniania, które jest skompilowane, zgodnie z opisem w [wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) jest obsługiwana
    - W tym momencie rozwiązanie za pomocą Windows usługi plików skalowalnego w poziomie (SOFS), zgodnie z opisem w [przygotować infrastrukturę platformy Azure SAP wysokiej dostępności przy użyciu Windows trybu failover klastra i udział plików dla wystąpień SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **nie jest obsługiwany w strefach**
- Trzeci strefa jest używana do obsługi urządzeń interwencja w przypadku, gdy tworzysz [klastra program pacemaker w systemie SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) lub wystąpień dodatkowych aplikacji





## <a name="next-steps"></a>Następne kroki
Sprawdź następnych krokach do wdrożenia w różnych strefach dostępności platformy Azure:

- [Klaster wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udostępnionego dysku klastra na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Przygotowywanie infrastruktury platformy Azure SAP wysokiej dostępności przy użyciu Windows trybu failover klastra i udział plików dla wystąpień SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






