---
title: Dostępność sap hana w jednym regionie platformy Azure | Dokumenty firmy Microsoft
description: Opisuje operacje SAP HANA na natywnych maszynach wirtualnych platformy Azure w jednym regionie platformy Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ef7161e653ec582708f242b67c643d960d75e27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255475"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Dostępność sap hana w jednym regionie platformy Azure
W tym artykule opisano kilka scenariuszy dostępności w jednym regionie platformy Azure. Platforma Azure ma wiele regionów, rozsianych po całym świecie. Aby zapoznać się z listą regionów platformy Azure, zobacz [Regiony platformy Azure](https://azure.microsoft.com/regions/). W celu wdrożenia sap HANA na maszynach wirtualnych w jednym regionie platformy Azure firma Microsoft oferuje wdrożenie pojedynczej maszyny Wirtualnej z wystąpieniem HANA. Aby zwiększyć dostępność, można wdrożyć dwie maszyny wirtualne z dwoma wystąpieniami HANA w [ramach zestawu dostępności platformy Azure,](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) który używa replikacji systemu HANA dla dostępności. 

Obecnie platforma Azure oferuje [strefy dostępności platformy Azure.](https://docs.microsoft.com/azure/availability-zones/az-overview) W tym artykule nie opisano szczegółowo stref dostępności. Ale zawiera ogólną dyskusję na temat używania zestawów dostępności w porównaniu do stref dostępności.

Regiony platformy Azure, w których oferowane są strefy dostępności, mają wiele centrów danych. Centra danych są niezależne w dostarczaniu źródła zasilania, chłodzenia i sieci. Powodem oferowania różnych stref w jednym regionie platformy Azure jest wdrażanie aplikacji w dwóch lub trzech strefach dostępności, które są oferowane. Wdrażanie w różnych strefach, problemy z zasilaniem i siecią wpływające tylko na jedną infrastrukturę strefy dostępności platformy Azure, wdrażanie aplikacji w regionie platformy Azure jest nadal funkcjonalne. Może wystąpić ograniczona pojemność. Na przykład maszyny wirtualne w jednej strefie mogą zostać utracone, ale maszyny wirtualne w pozostałych dwóch strefach będą nadal uruchomione. 
 
Zestaw dostępności platformy Azure to logiczna funkcja grupowania, która pomaga zapewnić, że zasoby maszyny Wirtualnej, które umieszczasz w zestawie dostępności, są odizolowane od siebie, gdy są wdrażane w centrum danych platformy Azure. Maszyny wirtualne platformy Azure umieszczone w zestawie dostępności korzystają z wielu serwerów fizycznych, regałów obliczeniowych, jednostek magazynowych i przełączników sieciowych. W niektórych dokumentach platformy Azure ta konfiguracja jest określana jako miejsca docelowe w różnych [domenach aktualizacji i błędów.](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) Te miejsca docelowe zwykle znajdują się w centrum danych platformy Azure. Zakładając, że problemy ze źródłem zasilania i siecią będą miały wpływ na centrum danych, które wdrażasz, cała twoja pojemność w jednym regionie platformy Azure będzie miała wpływ.

Rozmieszczenie centrów danych, które reprezentują strefy dostępności platformy Azure jest kompromisem między dostarczaniem dopuszczalnych opóźnień sieci między usługami wdrożonymi w różnych strefach, a odległością między centrami danych. Klęski żywiołowe idealnie nie wpłynęłyby na zasilanie, zasilanie sieci i infrastrukturę dla wszystkich stref dostępności w tym regionie. Jednak, jak pokazały monumentalne katastrofy naturalne, strefy dostępności mogą nie zawsze zapewniać dostępność, którą chcesz w jednym regionie. Pomyśl o huraganie Maria, który uderzył w wyspę Puerto Rico 20 września 2017 roku. Huragan spowodował prawie 100 procent zaciemnienia na wyspie o szerokości 90 mil.

## <a name="single-vm-scenario"></a>Scenariusz pojedynczej maszyny Wirtualnej

W scenariuszu jednej maszyny Wirtualnej należy utworzyć maszynę wirtualną platformy Azure dla wystąpienia SAP HANA. Usługa Azure Premium Storage służy do obsługi dysku systemu operacyjnego i wszystkich dysków z danymi. Umowy SLA dotyczące dostępności platformy Azure w wysokości 99,9 procent i umowy SLA innych składników platformy Azure są wystarczające do spełnienia umowy SLA dostępności dla klientów. W tym scenariuszu nie trzeba korzystać z zestawu dostępności platformy Azure dla maszyn wirtualnych, które uruchamiają warstwę DBMS. W tym scenariuszu można polegać na dwóch różnych funkcji:

- Automatyczne ponowne uruchamianie maszyny Wirtualnej platformy Azure (nazywane również goleniem usługi platformy Azure)
- Automatyczne ponowne uruchamianie systemu SAP HANA

Automatyczne ponowne uruchamianie maszyny Wirtualnej platformy Azure lub goleczenie usługi to funkcja na platformie Azure, która działa na dwóch poziomach:

- Host serwera platformy Azure sprawdza kondycję maszyny Wirtualnej, która jest hostowana na hoście serwera.
- Kontroler sieci szkieletowej platformy Azure monitoruje kondycję i dostępność hosta serwera.

Funkcja sprawdzania kondycji monitoruje kondycję każdej maszyny Wirtualnej, która jest hostowana na hoście serwera platformy Azure. Jeśli maszyna wirtualna wpadnie w stan nierozwiązywał się, ponowne uruchomienie maszyny Wirtualnej może być zainicjowane przez agenta hosta platformy Azure, który sprawdza kondycję maszyny Wirtualnej. Kontroler sieci szkieletowej sprawdza kondycję hosta, sprawdzając wiele różnych parametrów, które mogą wskazywać na problemy ze sprzętem hosta. Sprawdza również dostępność hosta za pośrednictwem sieci. Wskazanie problemów z gospodarzem może prowadzić do następujących zdarzeń:

- Jeśli host sygnalizuje zły stan kondycji, wyzwalany jest ponowne uruchomienie hosta i ponowne uruchomienie maszyn wirtualnych uruchomionych na hoście.
- Jeśli host nie jest w dobrej kondycji po pomyślnym ponownym uruchomieniu, inicjuje się ponowne wdrożenie maszyn wirtualnych, które pierwotnie znajdowały się w węźle w złej kondycji na serwerze hosta w dobrej kondycji. W takim przypadku oryginalny host jest oznaczony jako niezłomny. Nie będzie używany do dalszych wdrożeń, dopóki nie zostanie wyczyszczone lub zastąpione.
- Jeśli w złej kondycji host ma problemy podczas procesu ponownego uruchamiania, natychmiastowe ponowne uruchomienie maszyn wirtualnych na hosta w dobrej kondycji jest wyzwalany. 

Dzięki monitorowaniu hosta i maszyny wirtualnej udostępnianemu przez platformę Azure maszyny wirtualne platformy Azure, które doświadczają problemów z hostem, są automatycznie uruchamiane ponownie na zdrowym hoście platformy Azure. 

>[!IMPORTANT]
>Leczenie usługi platformy Azure nie uruchomi ponownie maszyn wirtualnych systemu Linux, gdzie system operacyjny gościa jest w stanie paniki jądra. Domyślne ustawienia powszechnie używanych wersji Systemu Linux nie są automatycznie ponowne uruchamianie maszyn wirtualnych lub serwera, gdzie jądro Linuksa jest w stanie paniki. Zamiast tego domyślnie przewiduje, aby utrzymać system operacyjny w stanie wpadki jądra, aby móc dołączyć debuger jądra do analizy. Platforma Azure honoruje to zachowanie, nie uruchamiając automatycznie maszyny Wirtualnej z usługą operacyjną gościa w takim stanie. Założenie jest takie, że takie zdarzenia są niezwykle rzadkie. Można zastąpić domyślne zachowanie, aby włączyć ponowne uruchomienie maszyny Wirtualnej. Aby zmienić domyślne zachowanie, włącz parametr 'kernel.panic' w /etc/sysctl.conf. Czas ustawiony dla tego parametru jest w sekundach. Typowe zalecane wartości są czekać na 20-30 sekund przed wyzwoleniem ponownego uruchomienia za pomocą tego parametru. Zobacz też <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>.

Drugą funkcją, na której polegasz w tym scenariuszu, jest fakt, że usługa HANA uruchamiana na zrestartowanej maszynie wirtualnej uruchamia się automatycznie po ponownym uruchomieniu maszyny Wirtualnej. Można skonfigurować [automatyczne ponowne uruchamianie usługi HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) za pośrednictwem usług watchdog różnych usług HANA.

Można poprawić ten scenariusz pojedynczej maszyny Wirtualnej, dodając węzeł pracy awaryjnej przez zimno do konfiguracji SAP HANA. W dokumentacji SAP HANA ta konfiguracja jest nazywana [automatyczną przewijaną w tryb failover hosta.](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html) Ta konfiguracja może mieć sens w sytuacji wdrażania lokalnego, w której sprzęt serwera jest ograniczony, a węzeł jednego serwera jest przeznaczony jako węzeł automatycznego pracy awaryjnej hosta dla zestawu hostów produkcyjnych. Jednak na platformie Azure, gdzie podstawowa infrastruktura platformy Azure zapewnia zdrowy serwer docelowy dla pomyślnego ponownego uruchomienia maszyny Wirtualnej, nie ma sensu wdrażać automatycznego trybu failover hosta SAP HANA. Ze względu na goleczenie usługi platformy Azure nie ma architektury referencyjnej, która przewiduje węzeł gotowości dla automatycznego trybu failover hosta HANA. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Szczególny przypadek konfiguracji skalowania w poziomie SAP HANA na platformie Azure
Wysoka dostępność konfiguracji skalowania w poziomie SAP HANA polega na usłudze gojenia maszyn wirtualnych platformy Azure i ponownym uruchomieniu wystąpienia SAP HANA, gdy maszyna wirtualna jest uruchomiona ponownie. Architektury wysokiej dostępności oparte na replikacji systemu HANA zostaną wprowadzone w późniejszym czasie. 


## <a name="availability-scenarios-for-two-different-vms"></a>Scenariusze dostępności dla dwóch różnych maszyn wirtualnych

Jeśli używasz dwóch maszyn wirtualnych platformy Azure w ramach zestawu dostępności platformy Azure, można zwiększyć czas pracy między tymi dwoma maszynami wirtualnymi, jeśli są one umieszczone w zestawie dostępności platformy Azure w jednym regionie platformy Azure. Konfiguracja podstawowa na platformie Azure będzie wyglądać następująco:

![Diagram dwóch maszyn wirtualnych ze wszystkimi warstwami](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Aby zilustrować różne scenariusze dostępności, kilka warstw na diagramie są pomijane. Diagram przedstawia tylko warstwy, które przedstawiają maszyny wirtualne, hosty, zestawy dostępności i regiony platformy Azure. Wystąpienia, grupy zasobów i subskrypcje usługi Azure Virtual Network nie odgrywają roli w scenariuszach opisanych w tej sekcji.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replikowanie kopii zapasowych na drugą maszynę wirtualną

Jednym z najbardziej podstawowych ustawień jest użycie kopii zapasowych. W szczególności może być masz kopie zapasowe dziennika transakcji wysłane z jednej maszyny Wirtualnej do innej maszyny Wirtualnej platformy Azure. Można wybrać typ usługi Azure Storage. W tej konfiguracji jesteś odpowiedzialny za skrypty kopii zaplanowanych kopii zapasowych, które są wykonywane na pierwszej maszynie Wirtualnej do drugiej maszyny Wirtualnej. Jeśli trzeba użyć drugich wystąpień maszyny Wirtualnej, należy przywrócić pełne, przyrostowe/różnicowe i kopie zapasowe dziennika transakcji do punktu, który jest potrzebny. 

Architektura wygląda następująco:

![Diagram dwóch maszyn wirtualnych z replikacją magazynu](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Ta konfiguracja nie jest dobrze nadaje się do osiągnięcia wielki cel punktu odzyskiwania (RPO) i czasy cel czasu odzyskiwania (RTO). Czasy RTO szczególnie ucierpiałyby z powodu konieczności pełnego przywrócenia pełnej bazy danych przy użyciu skopiowanych kopii zapasowych. Jednak ta konfiguracja jest przydatna do odzyskiwania po usunięciu niezamierzonych danych w wystąpieniach głównych. Dzięki tej konfiguracji w dowolnym momencie można przywrócić do pewnego momentu w czasie, wyodrębnić dane i zaimportować usunięte dane do głównego wystąpienia. W związku z tym może mieć sens, aby użyć metody kopiowania kopii zapasowej w połączeniu z innymi funkcjami wysokiej dostępności. 

Podczas kopiowania kopii zapasowych można użyć mniejszej maszyny Wirtualnej niż głównej maszyny Wirtualnej, na której jest uruchomione wystąpienie SAP HANA. Należy pamiętać, że można dołączyć mniejszą liczbę wirtualnych maszyn wirtualnych do mniejszych maszyn wirtualnych. Aby uzyskać informacje na temat limitów poszczególnych typów maszyn [wirtualnych, zobacz Rozmiary maszyn wirtualnych systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replikacja systemu SAP HANA bez automatycznego trybu failover

Scenariusze opisane w tej sekcji używają replikacji systemu SAP HANA. Aby zapoznać się z dokumentacją SAP, zobacz [Replikacja systemu](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Scenariusze bez automatycznego trybu failover nie są typowe dla konfiguracji w jednym regionie platformy Azure. Konfiguracja bez automatycznego trybu failover, choć pozwala uniknąć konfiguracji rozrusznika serca, zobowiązuje do ręcznego monitorowania i pracy awaryjnej. Ponieważ wymaga to i wysiłki, jak również, większość klientów są opierając się na usługi Azure uzdrowienie zamiast. Istnieją pewne przypadki krawędzi, w których ta konfiguracja może pomóc w scenariuszach awarii. Lub, w niektórych przypadkach, klient może chcieć zrealizować większą wydajność.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>Replikacja systemu SAP HANA bez automatycznego trybu failover i bez wstępnego ładowania danych

W tym scenariuszu używasz replikacji systemu SAP HANA do przenoszenia danych w sposób synchroniczne, aby osiągnąć cel cel 0. Z drugiej strony masz wystarczająco długi rto, że nie trzeba albo pracy awaryjnej lub danych wstępnego ładowania do pamięci podręcznej wystąpienia HANA. W takim przypadku możliwe jest osiągnięcie dalszej oszczędności w konfiguracji, wykonując następujące działania:

- Uruchom inne wystąpienie SAP HANA w drugiej maszynie wirtualnej. Wystąpienie SAP HANA w drugiej maszynie wirtualnej zajmuje większość pamięci maszyny wirtualnej. W przypadku, gdy przewijanie awaryjne do drugiej maszyny Wirtualnej, należy zamknąć uruchomione wystąpienie SAP HANA, które ma dane w pełni załadowane w drugiej maszynie Wirtualnej, dzięki czemu replikowane dane mogą być ładowane do pamięci podręcznej docelowego wystąpienia HANA w drugim maszynie wirtualnej.
- Użyj mniejszego rozmiaru maszyny Wirtualnej na drugiej maszynie wirtualnej. Jeśli nastąpi praca awaryjna, masz dodatkowy krok przed ręcznym przełączeniem w tryb failover. W tym kroku można zmienić rozmiar maszyny Wirtualnej do rozmiaru źródłowej maszyny Wirtualnej. 
 
Scenariusz wygląda następująco:

![Diagram dwóch maszyn wirtualnych z replikacją magazynu](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Nawet jeśli nie używasz wstępnego ładowania danych w docelowym poziomie replikacji systemu HANA, potrzebujesz co najmniej 64 GB pamięci. Potrzebujesz również wystarczającej ilości pamięci oprócz 64 GB, aby zachować dane magazynu wierszy w pamięci wystąpienia docelowego.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>Replikacja systemu SAP HANA bez automatycznego trybu failover i wstępnego ładowania danych

W tym scenariuszu dane, które są replikowane do wystąpienia HANA w drugiej maszynie wirtualnej jest wstępnie załadowany. Eliminuje to dwie zalety nie ładowania danych. W takim przypadku nie można uruchomić innego systemu SAP HANA na drugiej maszynie wirtualnej. Nie można również użyć mniejszego rozmiaru maszyny Wirtualnej. W związku z tym klienci rzadko implementują ten scenariusz.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replikacja systemu SAP HANA z automatyczną trybem failover

W standardowej i najbardziej typowej konfiguracji dostępności w jednym regionie platformy Azure dwie maszyny wirtualne platformy Azure z systemem SLES Linux mają zdefiniowany klaster trybu failover. Klaster SLES Linux jest oparty na platformie [Pacemaker,](/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) w połączeniu z urządzeniem [STONITH.](/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) 

Z punktu widzenia SAP HANA używany tryb replikacji jest synchronizowany i skonfigurowano automatyczną tryb pracy awaryjnej. W drugiej maszynie wirtualnej wystąpienie SAP HANA działa jako węzeł gotowości gorącej. Węzeł wstrzymania odbiera synchronikowy strumień rekordów zmian z podstawowego wystąpienia SAP HANA. Ponieważ transakcje są zatwierdzane przez aplikację w węźle podstawowym HANA, podstawowy węzeł HANA czeka, aby potwierdzić zatwierdzenie do aplikacji, dopóki pomocniczy węzeł SAP HANA potwierdzi, że otrzymał rekord zatwierdzenia. SAP HANA oferuje dwa tryby replikacji synchroniczowej. Aby uzyskać szczegółowe informacje i opis różnic między tymi dwoma trybami replikacji synchroniczowej, zobacz artykuł SAP [Tryby replikacji systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

Ogólna konfiguracja wygląda następująco:

![Diagram dwóch maszyn wirtualnych z replikacją magazynu i pracy awaryjnej](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Można wybrać to rozwiązanie, ponieważ umożliwia osiągnięcie RPO = 0 i niski cel rto. Skonfiguruj łączność klienta SAP HANA, tak aby klienci SAP HANA używali wirtualnego adresu IP do łączenia się z konfiguracją replikacji systemu HANA. Taka konfiguracja eliminuje konieczność ponownego skonfigurowania aplikacji w przypadku pracy awaryjnej w węźle pomocniczym. W tym scenariuszu jednostki SKU maszyn wirtualnych platformy Azure dla podstawowych i pomocniczych maszyn wirtualnych muszą być takie same.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki krok po kroku dotyczące konfigurowania tych konfiguracji na platformie Azure, zobacz:

- [Konfigurowanie replikacji systemu SAP HANA na maszynach wirtualnych platformy Azure](sap-hana-high-availability.md)
- [Wysoka dostępność systemu SAP HANA przy użyciu replikacji systemu](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Aby uzyskać więcej informacji na temat dostępności sap HANA w regionach platformy Azure, zobacz:

- [Dostępność sap hana w regionach platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

