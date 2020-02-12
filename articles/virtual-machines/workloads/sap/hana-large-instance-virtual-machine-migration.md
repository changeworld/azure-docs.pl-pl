---
title: Migrowanie SAP HANA na platformie Azure (duże wystąpienia) do usługi Azure Virtual Machines | Microsoft Docs
description: Jak migrować SAP HANA na platformie Azure (duże wystąpienia) do usługi Azure Virtual Machines
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dd01bf60104939fcf1f9bd1ccec0e7d72710041
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154922"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA migracji dużych wystąpień platformy Azure do platformy Azure Virtual Machines
W tym artykule opisano możliwe scenariusze wdrażania dużych wystąpień platformy Azure i zawarto podejście do planowania i migracji z zminimalizowanym przestojem przejścia

## <a name="overview"></a>Omówienie
Ponieważ anonsowanie dużych wystąpień platformy Azure dla SAP HANA (HLI) we wrześniu 2016, wielu klientów przyjęła ten sprzęt jako usługę dla swojej platformy obliczeniowej w pamięci.  W ostatnich latach rozszerzenie skalowania maszyn wirtualnych platformy Azure w połączeniu z obsługą modelu wdrażania w skali HANA przekroczyło największe zapotrzebowanie na wydajność bazy danych ERP klientów korporacyjnych.  Zaczynamy widzieć klientów, którzy wyrażali pragnienie migracji SAP HANA obciążeń z serwerów fizycznych do maszyn wirtualnych platformy Azure.
Ten przewodnik nie jest dokumentem konfiguracji krok po kroku, ale raczej opisuje typowe modele wdrażania i oferuje planowanie, dlatego migracja doradza z zamiarem zamierzonego zagadnienia przygotowania do zminimalizowania przestojów przejścia.

## <a name="assumptions"></a>Założenia
W tym artykule sprawia, że następujące założenia:
- Jedyne rozważane zainteresowania to niejednorodna Migracja usługi obliczeniowej bazy danych HANA z dużego wystąpienia Hana (HLI) do maszyny wirtualnej platformy Azure bez znaczących uaktualnień lub poprawek oprogramowania. Te drobne aktualizacje obejmują używanie nowszej wersji systemu operacyjnego lub wersji HANA jawnie ustalonej jako obsługiwane przez odpowiednie uwagi SAP. 
- Wszystkie działania Updates/Upgrades należy wykonać przed migracją lub po niej.  Na przykład SAP HANA MCOS konwertowanie do wdrożenia MDC. 
- Podejście do migracji, które będzie oferować najmniejszy przestój, jest SAP HANA replikacji systemu. Inne metody migracji nie są częścią zakresu tego dokumentu.
- Dotyczy to zarówno jednostek SKU rev3, jak i Rev4 z elementu HLI.
- Architektura wdrażania platformy HANA pozostaje przede wszystkim niezmieniona podczas migracji.  Oznacza to, że system z jednym wystąpieniem jest w tej samej lokalizacji w miejscu docelowym.
- Klienci przejrzały i rozumieli Umowa dotycząca poziomu usług (SLA) dla architektury docelowej (do). 
- Ze względu na różnice między HLIs i maszynami wirtualnymi klienci powinni monitorować użycie ich maszyn wirtualnych do zarządzania kosztami.
- Klienci rozumieją i potwierdzają, że jest to dedykowana platforma obliczeniowa. Jednak maszyny wirtualne działają w infrastrukturze udostępnionej, która jest bezpieczna i odizolowana od innych dzierżawców.
- Klienci mają zweryfikowane, że docelowe maszyny wirtualne obsługują zamierzoną architekturę. Aby wyświetlić wszystkie obsługiwane jednostki SKU maszyny wirtualnej certyfikowane do wdrożenia SAP HANA, należy sprawdzić [Katalog sprzętu SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Klienci sprawdziły plan projektu i migracji.
- Zaplanuj węzeł odzyskiwania po awarii wraz z lokacją główną.  Klienci nie będą mogli korzystać z węzła nie można używać w przypadku lokacji głównej działającej na maszynach wirtualnych po migracji.
- Klienci skopiowali wymagane pliki kopii zapasowej do docelowych maszyn wirtualnych w oparciu o wymagania dotyczące możliwości odzyskiwania i zgodności firmy. Ma to na celu pokrycie potrzeb związanych z odzyskiwaniem do punktu w czasie w okresie przejściowym.
- W przypadku HSR HA klienci muszą skonfigurować i skonfigurować urządzenie STONITH na SAP HANA przewodniki o wysokiej dostępności dla [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) i [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker).  Nie jest to wstępnie skonfigurowane, jak w przypadku programu.
- To podejście migracji nie obejmuje jednostek SKU z Optane Configuration.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania
W poniższej tabeli zestawiono typowe modele wdrażania z klientami.  Możliwa jest migracja do maszyn wirtualnych platformy Azure w przypadku wszystkich scenariuszy.  Niektóre scenariusze mogą wymagać drobnej modyfikacji architektury, aby uzyskać zalety bieżących ofert usług platformy Azure.

| Identyfikator scenariusza | HLI — scenariusz | Przeprowadzić migrację do maszyny wirtualnej Verbatim? | Dyskusji |
| --- | --- | --- | --- |
| 1 | [Pojedynczy węzeł z jednym identyfikatorem SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | Yes | - |
| 2 | [Pojedynczy węzeł z MCOS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | Yes | - |
| 3 | [Pojedynczy węzeł z odzyskiwaniem po awarii przy użyciu replikacji magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | Nie | Replikacja magazynu nie jest dostępna w przypadku platformy wirtualnej platformy Azure, Zmień bieżące rozwiązanie DR na HSR lub Backup/Restore |
| 4 | [Pojedynczy węzeł z DR (Multipurpose) przy użyciu replikacji magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | Nie | Replikacja magazynu nie jest dostępna w przypadku platformy wirtualnej platformy Azure, Zmień bieżące rozwiązanie DR na HSR lub Backup/Restore |
| 5 | [HSR z STONITHem w celu zapewnienia wysokiej dostępności](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | Yes | Brak wstępnie skonfigurowanych SBD dla docelowych maszyn wirtualnych.  Wybierz i Wdróż rozwiązanie STONITH.  Możliwe opcje: Agent ogrodzenia platformy Azure (obsługiwany zarówno w [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker), [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)), SBD |
| 6 | [HA z HSR, DR z replikacją magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | Nie | Zastąp replikację magazynu dla potrzeb odzyskiwania po awarii przy użyciu usługi HSR lub tworzenia kopii zapasowej/przywracania |
| 7 | [Przełączenie w tryb failover hosta (1 + 1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | Yes | Korzystanie z usługi ANF dla magazynu udostępnionego z maszynami wirtualnymi platformy Azure |
| 8 | [Skalowanie w poziomie przy użyciu rezerwy](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | Yes | 4HANA M128s, M416s, M416ms VM przy użyciu ANF tylko dla magazynu |
| 9 | [Skalowanie w poziomie bez rezerwy](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | Yes | BW/4HANA z M128s, M416s, M416ms VM (z lub bez użycia ANF for Storage) |
| 10 | [Skalowanie za pomocą odzyskiwania po awarii przy użyciu replikacji magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | Nie | Zastąp replikację magazynu dla potrzeb odzyskiwania po awarii przy użyciu usługi HSR lub tworzenia kopii zapasowej/przywracania |
| 11 | [Pojedynczy węzeł z odzyskiwaniem po awarii przy użyciu HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | Yes | - |
| 12 | [HSR z jednym węzłem do odzyskiwania po awarii (koszt zoptymalizowany)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | Yes | - |
| 13 | [HA i DR z HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | Yes | - |
| 14 | [HA i DR z HSR (zoptymalizowane pod kątem kosztów)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Yes | - |
| 15 | [Skalowanie w poziomie za pomocą narzędzia DR using HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | Yes | BW/4HANA z M128s. M416s, M416ms maszyny wirtualne (z użyciem programu ANF for Storage lub bez niego) |


## <a name="source-hli-planning"></a>Planowanie źródła (HLI)
Podczas dołączania serwera z systemem, zarówno do zarządzania usługami firmy Microsoft, jak i klientów odbywa się zgodnie z planowaniem ustawień dotyczących obliczeń, sieci, magazynu i systemu operacyjnego na potrzeby uruchamiania bazy danych SAP HANA.  Podobne planowanie należy wykonać, aby przeprowadzić migrację na maszynę wirtualną platformy Azure.

### <a name="sap-hana-housekeeping"></a>SAP HANA dla gospodarstw domowych 
Przed przeprowadzeniem migracji bazy danych HANA warto uporządkowanego zawartość bazy danych, tak aby niechciane, nieaktualne dane lub stare dzienniki nie były migrowane do nowej bazy danych.  Dla gospodarstw domowych zazwyczaj polega na usunięciu lub archiwizowaniu starych, wygasłych lub nieaktywnych danych.  W związku z tym te działania dotyczące higieny danych powinny zostać przetestowane w systemach nieprodukcyjnych w celu sprawdzenia poprawności przycinania danych przed użyciem produkcji.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Zezwalaj na łączność sieciową dla nowych maszyn wirtualnych i sieci wirtualnych 
W przypadku wdrażania klienta, połączenie sieciowe z usługi Azure sieci wirtualnych zostało ustanowione na podstawie informacji opisanych w artykule [Architektura sieci SAP HANA (duże wystąpienia)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Ponadto Routing ruchu sieciowego odbywa się w sposób opisany w sekcji "Routing na platformie Azure".
1. Na potrzeby skonfigurowania nowej maszyny wirtualnej jako elementu docelowego HSR migracji, jeśli nowa maszyna wirtualna platformy Azure zostanie umieszczona w istniejącej sieci wirtualnej z zakresami adresów IP, które mają już uprawnienia do nawiązywania połączenia z węzłem.
2. Jeśli nowa maszyna wirtualna platformy Azure zostanie umieszczona w nowej sieci wirtualnej (może znajdować się w innym regionie) i nawiązać połączenie równorzędne z istniejącą siecią wirtualną, można użyć klucza usługi ExpressRoute i identyfikatora zasobu z oryginalnej obsługi protokołu kluczem, aby zezwolić na dostęp do tego nowego zakresu adresów IP sieci wirtualnej.  Koordynuj z usługą Microsoft Service Management, aby umożliwić łączność z siecią wirtualną.  Uwaga: aby zminimalizować opóźnienie sieci między warstwami aplikacji i bazy danych, zarówno warstwy aplikacji, jak i bazy danych muszą znajdować się w tej samej sieci wirtualnej.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group"></a>Istniejący zestaw dostępności warstwy aplikacji, Strefy dostępności i Grupa umieszczania sąsiedztwa
Bieżący model wdrażania jest gotowy do spełnienia określonych celów poziomu usług.  W tym celu upewnij się, że infrastruktura docelowa spełni lub przekroczy określone cele.  
Serwery aplikacji SAP są coraz większe niż nie, a klienci są umieszczani w zestawie dostępności.  Jeśli bieżący poziom usług wdrożenia jest zadowalający i 
- Jeśli zastąpienie serwera SAP HANA odbywa się za pośrednictwem rozpoznawania nazw przez wymianę adresów IP dla nazwy hosta bazy danych, nie trzeba wykonywać dalszych czynności.  
- Jeśli nie korzystasz z programu PPG, pamiętaj, aby umieścić wszystkie serwery aplikacji i bazy danych w tej samej strefie, aby zminimalizować opóźnienie sieci.
- Jeśli używasz programu PPG, zapoznaj się z sekcją tego dokumentu Planowanie docelowe, zestaw dostępności, Strefy dostępności i Grupa umieszczania sąsiedztwa (PPG)

### <a name="storage-replication-discontinuance-process-if-used"></a>Proces zaprzestania replikacji magazynu (jeśli jest używany)
Jeśli replikacja magazynu jest używana jako rozwiązanie odzyskiwania po awarii, replikacja będzie musiała zostać zakończona (nieplanowana) po zamknięciu aplikacji SAP, a ostatni katalog SAP HANA, plik dziennika, kopie zapasowe danych zostały zreplikowane na woluminach magazynu zdalnego.  Ta akcja to taktyką zapobiegawcza, że mamy bieżącą bazę danych na serwerze% HLI w przypadku awarii podczas przejścia do maszyny wirtualnej na platformie Azure.

### <a name="data-backups-preservation-consideration"></a>Zagadnienie dotyczące zachowywania kopii zapasowych danych
Po uruchomienie produkcyjne do SAP HANA na maszynie wirtualnej platformy Azure, wszystkie dane oparte na migawce lub kopie zapasowe dzienników na serwerze HLI nie są łatwo dostępne lub dostępnych maszyny wirtualnej w razie potrzeby. W przypadku wczesnego okresu przejściowego przed utworzeniem kopii zapasowej opartej na platformie Azure w celu spełnienia wymagań związanych z odzyskiwaniem do określonych momentów zalecamy utworzenie kopii zapasowych na poziomie plików oprócz migawek, dni/tygodnie przed uruchomienie produkcyjne.  Te kopie zapasowe są kopiowane do konta usługi Azure Storage dostępnego dla nowej maszyny wirtualnej SAP HANA. Oprócz tworzenia kopii zapasowej zawartości, warto również ostrożnie zastanowić się, że pełne kopie zapasowe oprogramowania SAP są łatwo dostępne w przypadku, gdy konieczne jest wycofanie.

### <a name="adjusting-system-monitoring"></a>Dostosowywanie monitorowania systemu 
Klienci używają wielu różnych narzędzi do monitorowania i wysyłania powiadomień o alertach dla systemów w ramach ich poziomej platformy SAP.  Ten element jest tylko prostym wywołaniem dla odpowiedniej akcji przy dodawaniu nowych maszyn wirtualnych z dowolnymi regulacjami dotyczącymi monitorowania i aktualizowania odbiorców powiadomień o alertach, jeśli jest to konieczne.

### <a name="microsoft-operations-team-involvement"></a>Uczestnictwo zespołu operacyjnego firmy Microsoft 
Otwórz bilet z podstaw Azure Portal na istniejącym wystąpieniu.  Po utworzeniu biletu pomocy technicznej skontaktuje się z Tobą za pośrednictwem poczty e-mail.  

### <a name="engage-microsoft-account-team"></a>Zaangażuj zespół ds. kont Microsoft
Zaplanuj migrację blisko rocznicowego czasu odnowienia kontraktu HLI, aby zminimalizować niepotrzebnie za pośrednictwem kosztów zasobów obliczeniowych. Aby zlikwidować blok HLI, wymagany jest koordynowanie zakończenia kontraktu i rzeczywiste zamknięcie jednostki.

## <a name="destination-planning"></a>Planowanie docelowe
Zaplanuj nową infrastrukturę, aby zadbać o to, aby to zrobić, zapewnią konieczność zagwarantowania, że nowe dodanie będzie pasować do dużego schematu rzeczy.  Poniżej przedstawiono niektóre kluczowe punkty dla Contemplation.

### <a name="resource-availability-in-the-target-region"></a>Dostępność zasobów w regionie docelowym 
Bieżący region wdrażania serwera aplikacji SAP zazwyczaj znajduje się w sąsiedztwie ze skojarzonym HLIs.  HLIs są jednak oferowane w mniejszej liczbie lokalizacji niż dostępne regiony platformy Azure.  W przypadku migrowania z fizycznego elementu HLI na maszynę wirtualną platformy Azure przedstawia ona możliwość "dokładnego dostrajania" odległości wszystkich powiązanych usług na potrzeby optymalizacji wydajności.  W związku z tym kluczową kwestią jest upewnienie się, że wybrany region ma interesujące zasoby.  Na przykład dostępność określonej rodziny maszyn wirtualnych lub oferowanie stref platformy Azure pod kątem wysokiej dostępności.

### <a name="virtual-network"></a>Sieć wirtualna 
Architekt infrastruktury będzie miał możliwość wyboru, czy należy uruchomić nową bazę danych HANA w istniejącej sieci wirtualnej aplikacji SAP lub utworzyć nową.  Podstawowym czynnikiem decydującym jest bieżący układ sieci dla oprogramowania SAP.  W przypadku uwzględnienia zmiany infrastruktury wdrożenia, na przykład przechodzenia z jednej strefy do wdrożenia dwustrefowego i korzystania z usługi PPG. To ulepszenie dostępności nakłada zmianę architektury. Aby uzyskać więcej informacji, zobacz artykuł [Azure PPG w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).   

### <a name="security"></a>Bezpieczeństwo
Czy nowa usługa SAP HANA VM wyląduje w nowej lub istniejącej sieci wirtualnej/podsieci, reprezentuje nową, krytyczną usługę biznesową, która wymaga ochrony.  Właściwa kontrola dostępu zgodna z zasadami zabezpieczeń informacji firmowych dla tej nowej klasy usług powinna zostać oceniona i wdrożona. 

### <a name="vm-sizing-recommendation"></a>Zalecenie dotyczące zmiany wielkości maszyny wirtualnej
Ta migracja jest również okazja do odpowiedniego rozmiaru aparatu obliczeniowego platformy HANA.  Jeden może korzystać z [widoków systemu](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) Hana w połączeniu z platformą Hana Studio, aby zrozumieć użycie zasobów systemowych, co pozwala na prawidłowe ustalanie wielkości wydatków.

### <a name="storage"></a>Storage 
Wydajność magazynu to jeden z czynników wpływających na środowisko użytkownika aplikacji SAP.  Na podstawie danej jednostki SKU maszyny wirtualnej istnieje minimalny zaleceń dotyczących układów magazynu publikowanych [SAP HANA konfiguracjach magazynu maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Zalecamy przejrzenie tych minimalnych danych i porównywanie ich z istniejącymi statystykami systemu HLI w celu zapewnienia odpowiedniej pojemności i wydajności operacji we/wy dla nowej maszyny wirtualnej platformy HANA.

Jeśli skonfigurujesz PPG dla nowej maszyny wirtualnej HANA i skojarzonych z nią serwerów, Prześlij bilet pomocy technicznej, aby sprawdzić i zapewnić kolokację magazynu oraz maszynę wirtualną HANA.  
Ponieważ może zajść konieczność zmiany rozwiązania do tworzenia kopii zapasowych, należy również oglądany koszt magazynu, aby uniknąć niespodziewanych wydatków operacyjnych. 

### <a name="storage-replication-for-disaster-recovery"></a>Replikacja magazynu na potrzeby odzyskiwania po awarii
W polu HLI replikacja magazynu była oferowana jako opcja domyślna dla replikacji odzyskiwania po awarii dla bazy danych HANA. Ta funkcja nie jest opcją domyślną z maszyną wirtualną platformy Azure. Weź pod uwagę HSR, tworzenie kopii zapasowej/przywracanie lub inne obsługiwane rozwiązania spełniające Twoje wymagania biznesowe.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Zestawy dostępności, Strefy dostępności i grupy umieszczania zbliżeniowe 
W przypadku minimalizowania odległości między warstwą aplikacji i SAP HANA, aby zachować co najmniej opóźnienie sieci, nową maszynę wirtualną bazy danych i bieżące serwery aplikacji SAP należy umieścić w grupie umieszczania zbliżeniowego (PPG). Zapoznaj się z [grupą umieszczania w sąsiedztwie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) , aby dowiedzieć się, jak zestaw dostępności platformy Azure i strefy dostępności pracy z PPG dla wdrożeń SAP.
Jeśli elementy docelowe systemu HANA są wdrażane w więcej niż jednej strefie platformy Azure, klienci powinni mieć jasny widok profilu opóźnienia wybranych stref. Rozmieszczenie składników systemu SAP jest optymalne w odniesieniu do proximal odległości między aplikacją SAP a bazą danych.  [Narzędzie Test opóźnienia strefy dostępności](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) domeny publicznej pomaga ułatwić pomiar.  


### <a name="backup-strategy"></a>Strategia tworzenia kopii zapasowych
Wielu klientów korzysta już z rozwiązań do tworzenia kopii zapasowych innych firm, SAP HANA w dniu HLI.  W takim przypadku należy skonfigurować tylko dodatkową chronioną bazę danych maszyny wirtualnej i platformy HANA.  Bieżące zadania tworzenia kopii zapasowej nie są planowane, jeśli komputer jest likwidowany po migracji.
Usługa Azure Backup dla SAP HANA na maszynie wirtualnej jest teraz ogólnie dostępna.  Zobacz te linki, aby uzyskać szczegółowe informacje na temat [tworzenia kopii](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)zapasowych, [przywracania](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)i [zarządzania](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) SAP HANA kopii zapasowych na maszynach wirtualnych platformy Azure.

### <a name="dr-strategy"></a>Strategia odzyskiwania po awarii
Jeśli cele poziomu usług uwzględniają dłuższy czas odzyskiwania prostej kopii zapasowej do usługi BLOB Storage i przywracania na miejscu lub na nową maszynę wirtualną, jest to najprostsza i najtańsza Strategia odzyskiwania po awarii.  
Podobnie jak platforma dużej instancji, w której program HANA DR zwykle odbywa się z HSR; Na maszynie wirtualnej platformy Azure HSR jest to również najbardziej naturalne i natywne rozwiązanie SAP HANA DR.  Niezależnie od tego, czy wdrożenie źródłowe to pojedyncze wystąpienie, klastrowane z lub bez automatycznej pracy awaryjnej, czy wiele węzłów platformy HANA, docelowa replika HSR infrastruktury źródłowej jest wymagana w regionie odzyskiwania po awarii. Ta replika docelowa DR HSR zostanie ustanowiona po ukończeniu podstawowej wartości HLI do migracji maszyny wirtualnej.  Wystąpienie DR HANA zostanie zarejestrowane w podstawowym SAP HANA w wystąpieniu maszyny wirtualnej jako lokacji dodatkowej replikacji.  

### <a name="sap-application-server-connectivity-destination-change"></a>Zmiana miejsca docelowego łączności serwera aplikacji SAP
Podejście migracji HSR skutkuje nowym hostem usługi HANA DB i dlatego nową nazwą hosta bazy danych dla warstwy aplikacji, profile SAP należy zmodyfikować, aby odzwierciedlały nową nazwę hosta.  Jeśli przełącznik jest wykonywany przez rozpoznawanie nazw z zachowaniem nazwy hosta, nie jest wymagana żadna zmiana profilu.

### <a name="operating-system"></a>System operacyjny
Obrazy systemu operacyjnego dla elementu HLI i maszyny wirtualnej, nawet jeśli znajdują się na tym samym poziomie wersji, SLES 11 SP4 na przykład nie są identyczne. Klienci muszą sprawdzić poprawność wymaganych pakietów, poprawek, poprawek, jądra i poprawek zabezpieczeń na serwerze HLI, aby można je było zainstalować w docelowym systemie operacyjnym maszyny wirtualnej.  Dodatkowo klienci mogą skonfigurować nowszą wersję systemu operacyjnego na docelowej maszynie wirtualnej dla oprogramowania SAP HSR.  Jest to obsługiwane w przypadku [uwagi SAP 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Nowe żądanie licencji SAP
Proste żądanie żądania nowej licencji SAP dla nowego systemu HANA teraz, gdy zostanie ono zmigrowane do maszyn wirtualnych.

### <a name="service-level-agreement-sla-differences"></a>Różnice w umowie dotyczącej poziomu usług (SLA)
Autorzy lubią, aby wywoływać różnice między umowami SLA dostępności między elementami.  Na przykład klastrowane pary HLIs HA oferują dostępność na 99,99%. Aby uzyskać tę samą umowę SLA, należy wdrożyć maszyny wirtualne w strefach dostępności. W tym [artykule](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) opisano dostępność ze skojarzonymi architekturami wdrażania, dzięki czemu klienci mogą odpowiednio zaplanować infrastrukturę docelową.


## <a name="migration-strategy"></a>Strategia migracji
W tym dokumencie omówiono tylko podejście do replikacji systemu HANA w przypadku migracji z usługi HLI do maszyny wirtualnej platformy Azure.  Zależy od wdrożonego rozwiązania magazynu docelowego, proces różni się nieznacznie. Kroki wysokiego poziomu opisano poniżej.

### <a name="vm-with-premiumultra-disks-for-data"></a>Maszyna wirtualna z opcją Premium/Ultra-disks dla danych
W przypadku maszyn wirtualnych, które są wdrażane przy użyciu warstwy Premium lub Ultra-disks, Standardowa konfiguracja replikacji systemu SAP HANA ma zastosowanie i może służyć do konfiguracji usługi HSR.  Przywoływany [artykuł pomocy SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) zawiera przegląd kroków związanych z konfigurowaniem replikacji systemu między dwoma systemami, przejęciem do systemu pomocniczego, powrót po awarii do systemu podstawowego i wyłączanie replikacji systemu.  Na potrzeby migracji będziemy potrzebować tylko konfiguracji, przejmowania i wyłączanie replikacji systemu w ramach kroków źródła.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>Maszyna wirtualna z ANF dla woluminów danych i dzienników
Na wysokim poziomie większość migawek magazynów pełnych danych i dzienników należy skopiować do usługi Azure Storage, gdzie są one dostępne i odzyskiwane przez docelową maszynę wirtualną HANA.  Proces kopiowania można wykonać przy użyciu wszystkich natywnych narzędzi do kopiowania systemu Linux.  

>[!Important]
> Kopiowanie i transfer danych może zająć kilka godzin, zależnie od rozmiaru bazy danych HANA i przepustowości sieci.  Większość procesu kopiowania należy wykonać z wyprzedzeniem pierwszego przestoju bazy danych HANA.

### <a name="mcos-to-mdc-conversion"></a>Konwersja MCOS na MDC
Wiele składników w jednym modelu wdrożenia systemu (MCOS) została wybrana przez niektórych naszych klientów.  Motywacja była obejście ograniczenia migawki magazynu wielu baz danych (MDC) wcześniejszych wersji SAP HANA.  W modelu MCOS kilka niezależnych wystąpień SAP HANA jest ułożone w jeden blok.  Użycie HSR do migracji pomoże Ci w efekcie wielu maszyn wirtualnych HANA z jedną bazą danych dzierżawców.  Ten stan końcowy jest przeznaczony dla busier krajobrazu niż w przypadku, gdy klient mógł zostać przyzwyczajony do.  W SAP HANA przypadku MDC 2,0 wdrożenie domyślne jest zdolne do przeprowadzenia [przeniesienia dzierżawy Hana](https://launchpad.support.sap.com/#/notes/2472478) po migracji HSR.  Ten proces "konsoliduje" te niezależne bazy danych HANA do współdzierżawców w jednym kontenerze HANA.  

### <a name="application-layer-consideration"></a>Rozważenia warstwy aplikacji
Serwer bazy danych jest wyświetlany jako środek systemu SAP.  Wszystkie serwery aplikacji powinny znajdować się w sąsiedztwie SAP HANA DB.  W niektórych przypadkach, gdy wymagane jest nowe zastosowanie PPG, przeniesienie istniejących serwerów aplikacji na PPG, gdzie może być wymagana maszyna wirtualna HANA.  Tworzenie nowych serwerów aplikacji może być uznawane za łatwiejsze, jeśli masz już szablony wdrażania.  
Jeśli istniejące serwery aplikacji i Nowa maszyna wirtualna HANA są optymalnie zlokalizowane, nie trzeba kompilować nowych serwerów aplikacji, chyba że jest wymagana dodatkowa pojemność.  
W przypadku skompilowania nowej infrastruktury w celu zwiększenia dostępności usługi istniejące serwery aplikacji mogą stać się niepotrzebne i powinny być wyłączone i usunięte.
Jeśli docelowa nazwa hosta maszyny wirtualnej uległa zmianie i różni się od nazwy hosta protokołu HLI, profile serwera aplikacji SAP należy dostosować, aby wskazywały na nowy host.  Jeśli tylko adres IP bazy danych HANA zostanie zmieniony, do realizacji połączeń przychodzących z nową maszyną wirtualną HANA jest wymagana aktualizacja rekordu DNS.

### <a name="acceptance-test"></a>Test akceptacji
Mimo że migracja z programu HLI do maszyny wirtualnej nie wprowadza żadnych istotnych zmian w zawartości bazy danych w porównaniu do migracji heterogenicznej, nadal zalecamy sprawdzenie poprawności kluczowych funkcji i wydajności nowego Instalatora.  

### <a name="cutover-plan"></a>Plan uruchomienie produkcyjne
Mimo że migracja jest prosta w przód, obejmuje jednak likwidację istniejącej bazy danych.  Należy uważnie zaplanować zachowanie systemu źródłowego przy użyciu skojarzonej zawartości, a obrazy kopii zapasowych mają kluczowe znaczenie w przypadku konieczności powrotu do wielkości liter.  Dobrym planowaniem jest zaoferowanie odwrócenia speedier.   


## <a name="post-migration"></a>Po migracji
Zadanie migracji nie zostanie wykonane, dopóki nie zostanie bezpiecznie oddzielona żadna usługa lub łączność zależna od usługi HLI w celu zapewnienia zachowania integralności danych.  Ponadto należy zamknąć niepotrzebne usługi.  Ta sekcja wywołuje kilka najważniejszych elementów.

### <a name="decommissioning-the-hli"></a>Likwidowanie
Po pomyślnej migracji bazy danych HANA do maszyny wirtualnej platformy Azure nie należy przeprowadzać żadnych transakcji produkcyjnych w bazie danych.  Jednak utrzymywanie i uruchamianie usługi HLI przez okres równy swojemu lokalnemu oknie przechowywania danych kopii zapasowej jest bezpiecznym sposobem zapewnienia speedier odzyskiwania danych w razie potrzeby.  Tylko wtedy powinien zostać zlikwidowany blok HLI.  Oprócz przedsiębiorstwa technicznego klienci w ich najlepszym interesie powinni zawrzeć zobowiązania z firmy Microsoft.  Klienci powinni skontaktować się z przedstawicielami firmy Microsoft w celu przechodzenia przez proces likwidowania.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Usuń wszystkie serwery proxy (np. dołączenie iptables, BIGIP) skonfigurowane dla elementu HLI 
Jeśli usługa serwera proxy, taka jak dołączenie iptables, jest używana do kierowania ruchu lokalnego do i z elementu HLI, ta usługa nie jest już wymagana po pomyślnej migracji do maszyny wirtualnej.  Jednak ta usługa łączności powinna być zachowywana przez cały czas, gdy blok klucz jest nadal ustalany w sposób opisany w temacie likwidowanie.  Tę usługę można wyłączyć po całkowitym zlikwidowaniu bloku HLI. 

### <a name="remove-global-reach-for-hli"></a>Usuń Global Reach dla elementu HLI 
Global Reach jest zwykle używany do nawiązywania połączenia z bramą ExpressRoute klienta z bramą ExpressRoute, która umożliwia klientowi lokalne nawiązywanie połączenia z dzierżawcą, bez konieczności korzystania z usługi proxy.  Podobnie jak w przypadku usługi dołączenie iptables proxy, należy zachować wartość GlobalReach, dopóki blok nie zostanie całkowicie zlikwidowany.

### <a name="operating-system-subscription--movereuse"></a>Subskrypcja systemu operacyjnego — przenoszenie/ponowne używanie
Ponieważ serwery maszyn wirtualnych są postawiliśmy i są likwidowane, subskrypcje systemu operacyjnego mogą zostać zastąpione lub użyte ponownie w celu uniknięcia podwójnego płacenia licencji systemu operacyjnego.



## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:
- [SAP HANA konfiguracje i operacje infrastruktury na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
- [Obciążenia SAP na platformie Azure: planowanie i wdrażanie listy kontrolnej](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
