---
title: Migrowanie systemu SAP HANA na platformie Azure (duże wystąpienia) na maszyny wirtualne platformy Azure| Dokumenty firmy Microsoft
description: Jak przeprowadzić migrację sap HANA na platformie Azure (duże wystąpienia) do maszyn wirtualnych platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd1267711871b3e55f1a6229e46ae27b360322f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617039"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>Migracja sap hana na duże wystąpienie platformy Azure do maszyn wirtualnych platformy Azure
W tym artykule opisano możliwe scenariusze wdrażania dużych wystąpień platformy Azure i oferuje planowanie i migrację z zminimalizowaniem przestojów przejścia

## <a name="overview"></a>Omówienie
Od czasu ogłoszenia dużych wystąpień platformy Azure dla sap HANA (HLI) we wrześniu 2016 r. wielu klientów przyjęło ten sprzęt jako usługę oferującą swoją platformę obliczeniową w pamięci.  W ostatnich latach rozszerzenie rozmiaru maszyny Wirtualnej platformy Azure w połączeniu z obsługą wdrażania skalowania w poziomie HANA przekroczyło zapotrzebowanie większości klientów korporacyjnych na pojemność bazy danych ERP.  Zaczynamy widzieć klientów wyrażających zainteresowanie migracją obciążenia SAP HANA z serwerów fizycznych do maszyn wirtualnych platformy Azure.
Ten przewodnik nie jest dokumentem konfiguracji krok po kroku. Opisano w nim typowe modele wdrażania i oferuje planowanie i migrację.  Celem jest wywołanie niezbędnych zagadnień do przygotowania, aby zminimalizować przestoje przejścia.

## <a name="assumptions"></a>Założenia
W tym artykule przedstawiono następujące założenia:
- Jedynym branym pod uwagę odsetkiem jest jednorodna migracja usługi obliczeniowej bazy danych HANA z hana large instance (HLI) do maszyny Wirtualnej platformy Azure bez znaczącego uaktualnienia lub poprawek oprogramowania. Te drobne aktualizacje obejmują użycie nowszej wersji systemu operacyjnego lub wersji HANA jawnie określonej jako obsługiwanej przez odpowiednie notatki SAP. 
- Wszystkie działania aktualizacji/uaktualnień należy wykonać przed lub po migracji.  Na przykład SAP HANA MCOS konwersji do wdrożenia MDC. 
- Podejście migracji, które oferuje najmniej przestojów jest SAP HANA Replikacja systemu. Inne metody migracji nie są częścią zakresu tego dokumentu.
- Niniejsze wytyczne mają zastosowanie zarówno do jednostek SKU HLI Rev3, jak i Rev4.
- Architektura wdrażania HANA pozostaje przede wszystkim niezmieniona podczas migracji.  Oznacza to, że system z pojedynczego wystąpienia DR pozostanie w ten sam sposób w miejscu docelowym.
- Klienci przejrzeli i zrozumieli umowę dotyczącą poziomu usług (SLA) architektury docelowej (przeznaczonej do sprzedaży). 
- Warunki handlowe między HLI i VM są różne. Klienci powinni monitorować użycie ich maszyn wirtualnych do zarządzania kosztami.
- Klienci rozumieją, że HLI jest dedykowaną platformą obliczeniową, podczas gdy maszyny wirtualne działają na udostępnionej, ale izolowanej infrastrukturze.
- Klienci sprawdowali, czy docelowe maszyny wirtualne obsługują zamierzoną architekturę. Aby wyświetlić wszystkie obsługiwane jednostki SKU maszyn wirtualnych certyfikowane do wdrażania sap hana, zobacz [katalog sprzętu SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Klienci zatwierdzili plan projektowania i migracji.
- Planowanie odzyskiwania po awarii maszyny Wirtualnej wraz z lokacji głównej.  Klienci nie mogą używać HLI jako węzła odzyskiwania po awarii dla lokacji głównej uruchomionej na maszynach wirtualnych po migracji.
- Klienci skopiowali wymagane pliki kopii zapasowych do maszyn wirtualnych, na podstawie wymagań dotyczących odzyskiwania i zgodności biznesowej. Z maszyn wirtualnych dostępnych kopii zapasowych, umożliwia odzyskiwanie punktu w czasie w okresie przejściowym.
- W przypadku HSR HA klienci muszą skonfigurować i skonfigurować urządzenie STONITH na prowadnice SAP HANA HA dla [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) i [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker).  Nie jest wstępnie skonfigurowany jak przypadek HLI.
- To podejście migracji nie obejmuje jednostek SKU HLI z konfiguracją Optane.

## <a name="deployment-scenarios"></a>Scenariusze wdrażania
Typowe modele wdrażania z klientami HLI są podsumowane w poniższej tabeli.  Migracja do maszyn wirtualnych platformy Azure dla wszystkich scenariuszy HLI jest możliwe.  Aby korzystać z dodatkowych usług platformy Azure dostępnych, mogą być wymagane drobne zmiany architektury.

| Identyfikator scenariusza | Scenariusz HLI | Migracji do maszyny Wirtualnej dosłownie? | Uwaga |
| --- | --- | --- | --- |
| 1 | [Pojedynczy węzeł z jednym identyfikatorem SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | Tak | - |
| 2 | [Pojedynczy węzeł z systemem MCOS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | Tak | - |
| 3 | [Pojedynczy węzeł z odzyskiwania po awarii przy użyciu replikacji magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | Nie | Replikacja magazynu nie jest dostępna na platformie wirtualnej platformy Azure, zmień bieżące rozwiązanie odzyskiwania po awarii na HSR lub kopia zapasowa/przywracanie |
| 4 | [Pojedynczy węzeł z modułem ODZYSKIWANIA PO (wielofunkcyjnym) przy użyciu replikacji magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | Nie | Replikacja magazynu nie jest dostępna na platformie wirtualnej platformy Azure, zmień bieżące rozwiązanie odzyskiwania po awarii na HSR lub kopia zapasowa/przywracanie |
| 5 | [HSR z STONITH dla wysokiej dostępności](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | Tak | Brak wstępnie skonfigurowanych identyfikatorów SBD dla docelowych maszyn wirtualnych.  Wybierz i wdrażaj rozwiązanie STONITH.  Możliwe opcje: Azure Fencing Agent (obsługiwany zarówno dla [RHEL,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) [SLES),](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)SBD |
| 6 | [Ha z HSR, dr z replikacją magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | Nie | Zastąp replikację magazynu na potrzeby odzyskiwania po awarii za pomocą modułu HSR lub kopii zapasowej/przywracania |
| 7 | [Automatyczne tryb failover hosta (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | Tak | Używanie anf dla udostępnionego magazynu z maszynami wirtualnymi platformy Azure |
| 8 | [Skalowanie w poziomie ze stanem wstrzymania](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | Tak | BW/4HANA z maszynami wirtualnymi M128s, M416s, M416ms wykorzystującymi anf tylko do przechowywania |
| 9 | [Skalowanie w poziomie bez gotowości](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | Tak | BW/4HANA z maszynami wirtualnymi M128s, M416s, M416ms (z anf lub bez) |
| 10 | [Skalowanie w poziomie przy użyciu odzyskiwania po awarii przy użyciu replikacji magazynu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | Nie | Zastąp replikację magazynu na potrzeby odzyskiwania po awarii za pomocą modułu HSR lub kopii zapasowej/przywracania |
| 11 | [Pojedynczy węzeł z dr przy użyciu HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | Tak | - |
| 12 | [Pojedynczy węzeł HSR do odzyskiwania po awarii (zoptymalizowany pod kątem kosztów)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | Tak | - |
| 13 | [Ha i DR z HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | Tak | - |
| 14 | [Ha i DR z HSR (zoptymalizowane pod względem kosztów)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Tak | - |
| 15 | [Skalowanie w poziomie z dr przy użyciu HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | Tak | BW/4HANA z M128s. Maszyny wirtualne M416s, M416ms (z lub bez użycia ANF do przechowywania) |


## <a name="source-hli-planning"></a>Planowanie źródła (HLI)
Podczas dołączania serwera HLI zarówno zarządzanie usługami firmy Microsoft, jak i klienci przeszli przez planowanie ustawień obliczeniowych, sieci, magazynu i systemu operacyjnego do uruchamiania bazy danych SAP HANA.  Podobne planowanie musi mieć miejsce dla migracji do maszyny Wirtualnej platformy Azure.

### <a name="sap-hana-housekeeping"></a>Sap HANA sprzątanie 
Jest to dobra praktyka operacyjna, aby uporządkować zawartość bazy danych, tak niechciane, nieaktualne dane lub nieaktualne dzienniki nie są migrowane do nowej bazy danych.  Sprzątanie zazwyczaj polega na usuwaniu lub archiwizowaniu starych, wygasłych lub nieaktywnych danych.  Te działania "higieny danych" powinny być testowane w systemach nieprodukcyjnych w celu sprawdzenia ważności ich przycinania danych przed użyciem produkcji.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Zezwalaj na łączność sieciową dla nowych maszyn wirtualnych i sieci wirtualnej 
We wdrożeniu HLI klienta sieć została skonfigurowana na podstawie informacji opisanych w artykule [ARCHITEKTURA SIECI SAP HANA (Duże wystąpienia).](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture) Ponadto routing ruchu sieciowego odbywa się w sposób opisany w sekcji "Routing na platformie Azure".
- Podczas konfigurowania nowej maszyny Wirtualnej jako obiektu docelowego migracji, Jeśli jest ona umieszczona w istniejącej sieci wirtualnej z zakresami adresów IP już dozwolonymi do łączenia się z HLI, nie jest wymagana żadna dalsza aktualizacja łączności.
- Jeśli nowa maszyna wirtualna platformy Azure zostanie umieszczona w nowej sieci wirtualnej platformy Microsoft Azure, może znajdować się w innym regionie i być w równoważeniu z istniejącą siecią wirtualną, klucz usługi Usługi Usługi ExpressRoute i identyfikator zasobu z oryginalnego inicjowania obsługi administracyjnej HLI mogą umożliwiać dostęp do tej nowej sieci wirtualnej zasięgu ip sieci.  Koordynuj z usługą Microsoft Service Management, aby włączyć łączność sieci wirtualnej z HLI.  Uwaga: Aby zminimalizować opóźnienia sieci między warstwami aplikacji i bazy danych, warstwy aplikacji i bazy danych muszą znajdować się w tej samej sieci wirtualnej.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Istniejący zestaw dostępności warstwy aplikacji, strefy dostępności i grupa miejsc docelowych zbliżeniowych (PPG)
Bieżący model wdrażania jest wykonywana w celu spełnienia niektórych celów poziomu usług.  W tym ruchu upewnij się, że infrastruktura docelowa będzie spełniać lub przekraczać wyznaczone cele.  
Bardziej prawdopodobne niż nie, klienci SAP serwery aplikacji są umieszczane w zestawie dostępności.  Jeżeli obecny poziom usług wdrożeniowych jest zadowalający i 
- Jeśli docelowa maszyna wirtualna przyjmuje nazwę hosta nazwy logicznej HLI, aktualizowanie rozpoznawania adresów usługi nazw domen (DNS) wskazujących adres IP maszyny Wirtualnej będzie działać bez aktualizowania żadnych profili SAP
- Jeśli nie używasz usługi PPG, należy umieścić wszystkie serwery aplikacji i bazy danych w tej samej strefie, aby zminimalizować opóźnienia sieci.
- Jeśli używasz ppg, zapoznaj się z sekcją tego dokumentu: "Planowanie miejsca docelowe, zestaw dostępności, strefy dostępności i grupa miejsc docelowych zbliżeniowych (PPG)".

### <a name="storage-replication-discontinuance-process-if-used"></a>Proces wycofania replikacji magazynu (jeśli jest używany)
Jeśli replikacja magazynu jest używana jako rozwiązanie odzyskiwania po awarii, należy ją zakończyć (nieplanowane) po zamknięciu aplikacji SAP.  Ponadto ostatni katalog SAP HANA, plik dziennika i dane Kopie zapasowe zostały zreplikowane na zdalne woluminy pamięci masowej DR HLI.  W ten sposób jako środek ostrożności w przypadku awarii podczas przejścia serwera fizycznego do przejścia maszyny Wirtualnej platformy Azure.

### <a name="data-backups-preservation-consideration"></a>Uwzględnienie zachowania kopii zapasowych danych
Po przejściu do SAP HANA na maszynie Wirtualnej platformy Azure wszystkie kopie zapasowe danych opartych na migawce lub dziennika na HLI nie są łatwo dostępne lub przywracane do maszyny Wirtualnej, jeśli to konieczne. We wczesnym okresie przejściowym, zanim kopia zapasowa oparta na platformie Azure tworzy wystarczającą ilość historii, aby spełnić wymagania odzyskiwania w czasie, zalecamy wykonywanie kopii zapasowych na poziomie pliku oprócz migawek w HLI, dni lub tygodnie przed przecięciem.  Niech te kopie zapasowe zostaną skopiowane do konta usługi Azure Storage dostępnego dla nowej maszyny Wirtualnej SAP HANA.
Oprócz tworzenia kopii zapasowych zawartości HLI, rozsądne jest, aby pełne kopie zapasowe krajobrazu SAP były łatwo dostępne w przypadku konieczności wycofywania.

### <a name="adjusting-system-monitoring"></a>Regulacja monitorowania systemu 
Klienci używają wielu różnych narzędzi do monitorowania i wysyłania powiadomień o alertach dla systemów w ich środowisku SAP.  Ten element jest tylko wezwanie do odpowiednich działań w celu włączenia zmian do monitorowania i aktualizacji adresatów powiadomień alertów w razie potrzeby.

### <a name="microsoft-operations-team-involvement"></a>Zaangażowanie zespołu operacyjnego firmy Microsoft 
Otwórz bilet z witryny Azure portal na podstawie istniejącego wystąpienia HLI.  Po utworzeniu biletu pomocy technicznej, inżynier pomocy technicznej skontaktuje się z Tobą za pośrednictwem poczty e-mail.  

### <a name="engage-microsoft-account-team"></a>Zaangażuj zespół konta Microsoft
Planowanie migracji w pobliżu rocznicowego czasu odnowienia kontraktu HLI w celu zminimalizowania niepotrzebnych wydatków na zasoby obliczeniowe. Aby zlikwidować ostrze HLI, konieczne jest skoordynowanie rozwiązania umowy i faktycznego wyłączenia jednostki.

## <a name="destination-planning"></a>Planowanie miejsca docelowego
Wstawanie nowej infrastruktury, która zajmie miejsce istniejącej, zasługuje na pewne myślenie, aby zapewnić, że nowy dodatek zmieści się w dużym schemacie rzeczy.  Poniżej znajduje się kilka kluczowych punktów do kontemplacji.

### <a name="resource-availability-in-the-target-region"></a>Dostępność zasobów w regionie docelowym 
Region wdrażania bieżących serwerów aplikacji SAP zazwyczaj znajduje się w bliskim sąsiedztwie skojarzonych hli.  Jednak HLI są oferowane w mniejszej liczbie lokalizacji niż dostępne regiony platformy Azure.  Podczas migracji fizycznej maszyny Wirtualnej HLI do platformy Azure jest również dobry moment, aby "dostroić" odległość bliskości wszystkich powiązanych usług w celu optymalizacji wydajności.  W ten sposób jednym z kluczowych czynników jest zapewnienie, że wybrany region ma wszystkie wymagane zasoby.  Na przykład dostępność niektórych rodzin maszyn wirtualnych lub oferty strefy platformy Azure dla konfiguracji wysokiej dostępności.

### <a name="virtual-network"></a>Sieć wirtualna 
Klienci muszą wybrać, czy uruchomić nową bazę danych HANA w istniejącej sieci wirtualnej lub utworzyć nową.  Głównym czynnikiem decydującym jest bieżący układ sieci dla środowiska SAP.  Również wtedy, gdy infrastruktura przechodzi z jednej strefy do dwóch stref wdrożenia i używa PPG, nakłada zmiany architektury. Aby uzyskać więcej informacji, zobacz artykuł [Usługi Azure PPG dla optymalnego opóźnienia sieci z aplikacją SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).   

### <a name="security"></a>Zabezpieczenia
Niezależnie od tego, czy nowa maszyna wirtualna SAP HANA znajduje się na nowej lub istniejącej podsieci vnet/, reprezentuje nową usługę o znaczeniu krytycznym dla firmy, która wymaga zabezpieczenia.  Kontrola dostępu zgodna z zasadami zabezpieczeń informacji firmowych powinna zostać oceniona i wdrożona dla tej nowej klasy usług.

### <a name="vm-sizing-recommendation"></a>Zalecenie dotyczące rozmiaru maszyny Wirtualnej
Ta migracja jest również okazją do odpowiedniego rozmiaru aparatu obliczeniowego HANA.  Można użyć [widoków systemu](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) HANA w połączeniu z HANA Studio, aby zrozumieć zużycie zasobów systemowych, co pozwala na odpowiednie rozmiary w celu zwiększenia wydajności wydatków.

### <a name="storage"></a>Magazyn 
Wydajność magazynu jest jednym z czynników, które wpływają na środowisko użytkownika aplikacji SAP.  Bazuj na danej jednostce SKU maszyny wirtualnej, istnieją minimalne konfiguracje magazynu opublikowane przez [SAP HANA Azure virtual machine storage.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) Firma Microsoft zaleca zapoznanie się z tymi minimalnymi specyfikacjami i porównanie z istniejącymi statystykami systemu HLI, aby zapewnić odpowiednią wydajność operacji we/wy dla nowej maszyny wirtualnej HANA.

Jeśli skonfigurujesz ppg dla nowej maszyny Wirtualnej HANA i skojarzonych z nią severs, prześlij bilet pomocy technicznej, aby sprawdzić i zapewnić kolokedycję magazynu i maszyny Wirtualnej. Ponieważ rozwiązanie do tworzenia kopii zapasowych może wymagać zmiany, koszt magazynu należy również ponownie sprawdzić, aby uniknąć niespodzianek wydatków operacyjnych.

### <a name="storage-replication-for-disaster-recovery"></a>Replikacja magazynu do odzyskiwania po awarii
W przypadku HLI replikacja magazynu była oferowana jako domyślna opcja odzyskiwania po awarii. Ta funkcja nie jest domyślną opcją dla sap HANA na maszynie Wirtualnej platformy Azure. Rozważ HSR, tworzenie kopii zapasowych/przywracanie lub inne obsługiwane rozwiązania spełniające Twoje potrzeby biznesowe.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Zestawy dostępności, strefy dostępności i grupy miejsc docelowych zbliżeniowych 
Aby skrócić odległość między warstwą aplikacji a sap HANA, aby zachować opóźnienie sieci na minimalnym poziomie, nowa maszyna wirtualna bazy danych i bieżące serwery aplikacji SAP powinny być umieszczone w pliku PPG. Zapoznaj się [z grupą miejsc docelowych zbliżeniowych,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) aby dowiedzieć się, jak zestaw dostępności platformy Azure i strefy dostępności współpracują z programem PPG dla wdrożeń SAP.
Jeśli członkowie docelowego systemu HANA są wdrażane w więcej niż jednej strefie platformy Azure, klienci powinni mieć jasny widok profilu opóźnienia wybranych stref. Rozmieszczenie składników systemu SAP jest optymalne w odniesieniu do odległości bliższej między aplikacją SAP a bazą danych.  [Narzędzie do testowania opóźnień strefy dostępności](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) domeny publicznej ułatwia pomiar.  


### <a name="backup-strategy"></a>Strategia tworzenia kopii zapasowych
Wielu klientów korzysta już z rozwiązań do tworzenia kopii zapasowych innych firm dla sap HANA na HLI.  W takim przypadku należy skonfigurować tylko dodatkowe chronione bazy danych maszyn wirtualnych i HANA.  Trwające zadania tworzenia kopii zapasowych HLI można teraz nieplanować, jeśli komputer jest likwidowany po migracji.
Usługa Azure Backup for SAP HANA na maszynie Wirtualnej jest teraz ogólnie dostępna.  Zobacz te łącza, aby uzyskać szczegółowe informacje na temat: [Kopia zapasowa](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database), [Przywracanie](https://docs.microsoft.com/azure/backup/sap-hana-db-restore), Zarządzanie kopią zapasową SAP HANA na maszynach wirtualnych platformy Azure. [Manage](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)

### <a name="dr-strategy"></a>Strategia odzyskiwania po awarii
Jeśli cele poziomu usług pomieścić dłuższy czas odzyskiwania, prosta kopia zapasowa do magazynu obiektów blob i przywrócić w miejscu lub przywrócić do nowej maszyny Wirtualnej jest najprostszą i najtańszą strategią odzyskiwania po awarii.  
Podobnie jak platforma dużych wystąpień, gdzie HANA DR zazwyczaj odbywa się z HSR; Na maszynie wirtualnej platformy Azure hsr jest również najbardziej naturalnym i natywnym rozwiązaniem SAP HANA DR.  Niezależnie od tego, czy wdrożenie źródłowe jest pojedyncze wystąpienie lub klastrowane, replika infrastruktury źródłowej jest wymagana w regionie odzyskiwania po awarii.
Ta replika odzyskiwania po awarii zostanie skonfigurowana po zakończeniu migracji podstawowej sieci HLI do maszyny Wirtualnej.  Baza danych DR HANA zarejestruje się w podstawowym wystąpieniu SAP HANA w wystąpieniu maszyny Wirtualnej jako lokacja replikacji pomocniczej.  

### <a name="sap-application-server-connectivity-destination-change"></a>Zmiana miejsca docelowego łączności serwera aplikacji SAP
Migracja HSR powoduje nowy host bazy danych HANA, a tym samym nową nazwa hosta bazy danych dla warstwy aplikacji, profile SAP muszą zostać zmodyfikowane w celu odzwierciedlenia nowej nazwy hosta.  Jeśli przełączanie odbywa się za pomocą rozpoznawania nazw zachowującej nazwę hosta, nie jest wymagana żadna zmiana profilu.

### <a name="operating-system"></a>System operacyjny
Obrazy systemu operacyjnego dla HLI i maszyny wirtualnej, mimo że są na tym samym poziomie wersji, SLES 12 SP4 na przykład, nie są identyczne. Klienci muszą zweryfikować wymagane pakiety, poprawki, poprawki, jądra i poprawki zabezpieczeń na HLI, aby zainstalować te same pakiety w docelowych.  Jest obsługiwany do używania HSR do replikacji ze starszego systemu operacyjnego na maszynie wirtualnej z nowszą wersją systemu operacyjnego.  Sprawdź konkretne obsługiwane wersje, przeglądając [notatkę SAP 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Nowe żądanie licencji SAP
Proste wywołanie, aby zażądać nowej licencji SAP dla nowego systemu HANA teraz, gdy został zmigrowany do maszyn wirtualnych.

### <a name="service-level-agreement-sla-differences"></a>Różnice w umowie dotyczącej poziomu usług (SLA)
Autorzy lubią wywoływać różnicę w dostępności SLA między HLI i azure maszyny wirtualnej.  Na przykład klastrowane pary HLIs HA oferują dostępność 99,99%. Aby osiągnąć tę samą umowy SLA, należy wdrożyć maszyny wirtualne w strefach dostępności. W tym [artykule](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) opisano dostępność skojarzonych architektur wdrażania, dzięki czemu klienci mogą odpowiednio zaplanować swoją infrastrukturę docelową.


## <a name="migration-strategy"></a>Strategia migracji
W tym dokumencie omówimy tylko podejście replikacji systemu HANA dla migracji z HLI do maszyny Wirtualnej platformy Azure.  Zależy od wdrożonego rozwiązania magazynu docelowego, proces różni się nieznacznie. Kroki wysokiego poziomu są opisane poniżej.

### <a name="vm-with-premiumultra-disks-for-data"></a>Maszyna wirtualna z dyskami premium/ultra-dyskami dla danych
W przypadku maszyn wirtualnych, które są wdrażane z dyskami premium lub ultra-dyskami, standardowa konfiguracja replikacji systemu SAP HANA ma zastosowanie do konfigurowania hsr.  [Artykuł pomocy SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) zawiera omówienie kroków związanych z konfigurowaniem replikacji systemu, przejmowaniem systemu pomocniczego, koniecznością powrotu do podstawowego i wyłączaniem replikacji systemu.  Na potrzeby migracji będziemy potrzebować tylko kroki konfiguracji, przejęcia i wyłączenia replikacji.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>Maszyna wirtualna z anf dla danych i woluminów dziennika
Na wysokim poziomie najnowsze migawki magazynu HLI pełnych woluminów danych i dzienników muszą zostać skopiowane do usługi Azure Storage, gdzie są dostępne i możliwe do odzyskania przez docelową maszynę wirtualną HANA.  Proces kopiowania można wykonać za pomocą dowolnych natywnych narzędzi do kopiowania linuksowych.  

> [!IMPORTANT]
> Kopiowanie i przesyłanie danych może potrwać wiele godzin w zależności od rozmiaru bazy danych HANA i przepustowości sieci.  Większość procesu kopiowania należy wykonać przed podstawowym przestojem bazy danych HANA.

### <a name="mcos-to-mdc-conversion"></a>Konwersja MCOS na MDC
Model wdrażania wielu składników w jednym systemie (MCOS) był używany przez niektórych naszych klientów HLI.  Motywacją było obejście ograniczenia migawki magazynu wielu baz danych (MDC) wcześniejszych wersji systemu SAP HANA.  W modelu MCOS kilka niezależnych wystąpień SAP HANA są ułożone w jednym bloku HLI.  Przy użyciu HSR dla migracji będzie działać poprawnie, ale w wyniku wielu maszyn wirtualnych HANA z jednej dzierżawy DB każdy.  Ten stan końcowy sprawia, że bardziej ruchliwy krajobraz niż to, do czego klient mógł być przyzwyczajony.  Z SAP HANA 2.0 domyślne wdrożenie jest MDC, realną alternatywą jest wykonanie [przeniesienia dzierżawy HANA](https://launchpad.support.sap.com/#/notes/2472478) po migracji HSR.  Ten proces "konsoliduje" te niezależne bazy danych HANA w trybie współużytki w jednym kontenerze HANA.  

### <a name="application-layer-consideration"></a>Uwzględnienie warstwy aplikacji
Serwer bazy danych jest postrzegany jako środek systemu SAP.  Wszystkie serwery aplikacji powinny znajdować się w pobliżu bazy danych SAP HANA.  W niektórych przypadkach, gdy wymagane jest nowe użycie ppg, przeniesienie istniejących serwerów aplikacji do ppg, gdzie może być wymagane maszyny wirtualnej HANA.  Tworzenie nowych serwerów aplikacji może być uznane za łatwiejsze, jeśli masz już przydatne szablony wdrażania.  
Jeśli istniejące serwery aplikacji i nowa maszyna wirtualna HANA są optymalnie zlokalizowane, nie trzeba budować żadnych nowych serwerów aplikacji, chyba że wymagana jest dodatkowa pojemność.  
Jeśli nowa infrastruktura jest zbudowana w celu zwiększenia dostępności usług, istniejące serwery aplikacji mogą stać się niepotrzebne i powinny zostać zamknięte i usunięte.
Jeśli docelowa nazwa hosta maszyny Wirtualnej została zmieniona i różni się od nazwy hosta HLI, profile serwera aplikacji SAP muszą zostać dostosowane do punktu nowego hosta.  Jeśli zmienił się tylko adres IP bazy danych HANA, do prowadzenia połączeń przychodzących do nowej maszyny wirtualnej HANA potrzebna jest aktualizacja rekordu DNS.

### <a name="acceptance-test"></a>Test akceptacyjny
Chociaż migracja z HLI do maszyny Wirtualnej nie wprowadza żadnych istotnych zmian w zawartości bazy danych w porównaniu do migracji heterogeniii, nadal zaleca się sprawdzanie poprawności kluczowych funkcji i aspekt wydajności nowej konfiguracji.  

### <a name="cutover-plan"></a>Plan cutover
Chociaż migracja ta jest prosta, wiąże się jednak z likwidacją istniejącego pb.  Staranne planowanie zachowania systemu źródłowego wraz z skojarzoną z nim zawartością i obrazami kopii zapasowych ma kluczowe znaczenie w przypadku konieczności rezerwowania.  Dobre planowanie oferuje szybsze odwrócenie.   


## <a name="post-migration"></a>Zadania po migracji
Zadanie migracji nie jest wykonywane, dopóki nie bezpiecznie oddzielimy żadnych usług lub łączności zależnej od HLI, aby zapewnić zachowanie integralności danych.  Ponadto zamknij niepotrzebne usługi.  W tej sekcji przywołuje kilka elementów najwyższej klasy.

### <a name="decommissioning-the-hli"></a>Likwidacja HLI
Po pomyślnej migracji bazy danych HANA do maszyny Wirtualnej platformy Azure upewnij się, że w usłudze HLI DB nie są uruchamiane żadne produktywne transakcje biznesowe.  Jednak utrzymanie HLI działa przez pewien czas równa się jego lokalne okno przechowywania kopii zapasowych jest bezpieczną praktyką zapewniając szybsze odzyskiwanie w razie potrzeby.  Dopiero wtedy ostrze HLI powinno zostać wycofane z eksploatacji.  Klienci powinni zawrzeć zobowiązania HLI zawarte w umowie z firmą Microsoft, kontaktując się ze swoimi przedstawicielami firmy Microsoft.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Usuń dowolny serwer proxy (ex: Iptables, BIGIP) skonfigurowany dla HLI 
Jeśli usługa serwera proxy, taka jak IPTables jest używana do kierowania ruchu lokalnego do i z HLI, nie jest już potrzebna po pomyślnej migracji do maszyny Wirtualnej.  Jednak ta usługa łączności powinna być przechowywana tak długo, jak długo blok HLI jest nadal stały.  Usługa jest wyłączona dopiero po całkowitym wycofaniu ostrza HLI.

### <a name="remove-global-reach-for-hli"></a>Usuwanie globalnego zasięgu dla HLI 
Globalny zasięg służy do łączenia bramy usługi ExpressRoute klientów z bramą HLI ExpressRoute.  Umożliwia to lokalnym ruchom klientów dotarcie bezpośrednio do dzierżawy HLI bez korzystania z usługi proxy.  To połączenie nie jest już potrzebne w przypadku braku jednostki HLI po migracji.  Podobnie jak w przypadku usługi proxy IPTables, GlobalReach powinny być również przechowywane do czasu, gdy blok HLI zostanie całkowicie wycofany z eksploatacji.

### <a name="operating-system-subscription--movereuse"></a>Subskrypcja systemu operacyjnego – przenoszenie/ponowne używanie
Ponieważ serwery maszyn wirtualnych są w stanie i ostrza HLI są likwidowane, subskrypcje systemu operacyjnego można zastąpić lub ponownie, aby uniknąć podwójnego płacenia licencji systemu operacyjnego.



## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:
- [Konfiguracje i operacje infrastruktury SAP HANA na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
- [Obciążenia SAP na platformie Azure: lista kontrolna planowania i wdrażania](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
