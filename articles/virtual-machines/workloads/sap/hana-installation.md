---
title: Instalowanie platformy SAP HANA na platformie SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Jak zainstalować oprogramowanie SAP HANA na platformie SAP HANA na platformie Azure (duże wystąpienie).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/27/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d335e135551b7b6faed8ee566acb14b46fd6c81
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43107515"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Jak zainstalować i skonfigurować oprogramowanie SAP HANA (duże wystąpienia) na platformie Azure

Poniżej przedstawiono kilkoma ważnymi definicjami, należy wiedzieć przed przeczytaniem tego przewodnika. W [platformy SAP HANA (duże wystąpienia) — omówienie i architektura na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) wprowadziliśmy dwóch różnych klas jednostek dużych wystąpień HANA:

- S72, S72m, S144, S144m, platformie S192, platformie S192m i S192xm, który nazywamy "Type I klasy" jednostek SKU.
- S384 S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm i S960m, który nazywamy "class typu II" jednostki SKU.

Specyfikator klasy będzie służyć w całej dokumentacji dużych wystąpień HANA się ostatecznie różne możliwości i wymagania, w oparciu o jednostki SKU HANA duże wystąpienia.

Inne definicje, które firma Microsoft często używane są:
- **Duże wystąpienia sygnatury:** stos infrastruktury sprzętu, który jest SAP HANA TDI certyfikat i do uruchamiania wystąpień oprogramowania SAP HANA w obrębie platformy Azure w wersji dedykowanej.
- **Oprogramowanie SAP HANA na platformie Azure (duże wystąpienia):** wystąpień oficjalną nazwą języka dla oferty na platformie Azure do uruchamiania oprogramowania HANA w SAP HANA TDI certyfikowane sprzętu, które zostało wdrożone w dużych wystąpień sygnatury w różnych regionach platformy Azure. Powiązane termin **dużych wystąpień HANA** jest mała w przypadku oprogramowania SAP HANA na platformie Azure (duże wystąpienia) i jest powszechnie używany ten przewodnik wdrożenia technicznego.


Instalacja oprogramowania SAP HANA jest odpowiedzialny za i uruchomieniem działania po przekazywanie nowego oprogramowania SAP HANA na platformie Azure (duże wystąpienia) serwera. I po jego nawiązaniu stało się łączność między swoje sieciach wirtualnych platformy Azure i jednostek dużych wystąpień HANA. 

> [!Note]
> Zgodnie z zasadami SAP instalacji oprogramowania SAP HANA, muszą być wykonywane przez osoby z certyfikatami poświadczającymi zgodność ze przeprowadzać instalacje oprogramowania SAP HANA. Osoba, został przekazany certyfikat skojarzyć technologii SAP egzaminu, egzaminu certyfikacyjnego instalacja programu SAP HANA, lub przez integratora systemów SAP (SI).

Sprawdź ponownie, szczególnie w przypadku, gdy planujesz zainstalować oprogramowania HANA w wersji 2.0 [2235581 # Uwaga SAP pomocy technicznej — SAP HANA: obsługiwane systemy operacyjne](https://launchpad.support.sap.com/#/notes/2235581/E) aby upewnić się, że system operacyjny jest obsługiwany z SAP HANA wersji decyzję o zainstalowaniu. Należy pamiętać, że obsługiwany system operacyjny na potrzeby oprogramowania HANA w wersji 2.0 jest bardziej ograniczony system operacyjny obsługiwany HANA 1.0. 

> [!IMPORTANT] 
> Dla typu II jednostek tylko w systemie SLES 12 z dodatkiem SP2 systemu operacyjnego wersja jest obsługiwana w tym momencie. 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Pierwsze kroki po otrzymaniu jednostki HANA duże wystąpienie:

**Pierwszy krok** po otrzymaniu dużych wystąpień HANA i dostępu ustanowionym i łączności do wystąpień, zarejestrować się wystąpienie systemu operacyjnego z dostawcą systemu operacyjnego. Ten krok obejmuje rejestrowanie system operacyjny SUSE Linux w wystąpieniu SMT SUSE, musisz mieć wdrożony na maszynie wirtualnej na platformie Azure. Jednostka dużych wystąpień HANA mogą łączyć się tego wystąpienia SMT (zobacz w dalszej części tej dokumentacji). Lub system operacyjny Red Hat muszą być zarejestrowane przy użyciu Red Hat subskrypcji Menedżera musisz nawiązać połączenie. Zobacz również uwagi w tym [dokumentu](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ten krok jest również niezbędne można było stosowanie poprawek systemu operacyjnego. Zadanie, które jest odpowiedzialny za klienta. SUSE, można znaleźć w dokumentacji, aby zainstalować i skonfigurować SMT [tutaj](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**Drugi krok** jest sprawdzenie, czy nowe poprawki i poprawki wersji/wersji określonego systemu operacyjnego. Sprawdź, czy poprawka poziomu dużych wystąpień HANA na najnowszy stan. Oparte na czas wersji poprawki systemu operacyjnego oraz zmian w obrazie, który można wdrożyć program Microsoft, mogą wystąpić przypadki, w którym najnowsze poprawki nie może być włączony. Dlatego jest krok obowiązkowy po podjęciu przez jednostkę dużych wystąpień HANA, sprawdź, czy poprawki istotnych dla zabezpieczeń, funkcji, dostępności i wydajności zostały wydane w międzyczasie przez określonego dostawcę systemu Linux i mają zostać zastosowane.

**Trzeci krok** jest zapoznaj się z odpowiednimi SAP Notes dotyczące instalowania i konfigurowania platformy SAP HANA w określonej wersji systemu operacyjnego/wersji. Ze względu na zmianę zaleceń lub zmiany do uwagi SAP lub konfiguracje, które są zależne od instalacji poszczególnych scenariuszy firmy Microsoft nie zawsze będzie mieć skonfigurowane doskonale jednostka dużych wystąpień HANA. Dlatego jest obowiązkowe dla Ciebie jako klient korzystający z odczytywać SAP Notes związane z platformą SAP HANA na dokładną wersją systemu Linux. Również Sprawdź konfiguracje systemu operacyjnego/wersji niezbędne i zastosować ustawienia konfiguracji w przypadku, gdy nie jest jeszcze zrobione.

W określonych, sprawdź następujące parametry i ostatecznie dostosowana do:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Począwszy od SLES12 z dodatkiem SP1 i systemu RHEL 7.2, te należy ustawić parametry w pliku konfiguracji w katalogu /etc/sysctl.d. Na przykład można utworzyć pliku konfiguracji, za pomocą nazwy 91-NetApp-HANA.conf. Dla starszych wersji w systemie SLES i RHEL te parametry muszą być in/etc/sysctl.conf zestawu.

Wszystkie systemu RHEL zwalnia, a począwszy od SLES12, 
- sunrpc.tcp_slot_table_entries = 128

Parametr musi być ustawiony in/etc/modprobe.d/sunrpc-local.conf. Jeśli plik nie istnieje, najpierw należy utworzyć, dodając następujący wpis: 
- Opcje sunrpc tcp_max_slot_table_entries = 128

**Czwarty krok** jest sprawdzenie czas systemowy urządzenia dużych wystąpień HANA. Wystąpienia są wdrażane przy użyciu strefy czasowej systemu, reprezentujące region platformy Azure, której sygnatura wystąpienia dużych HANA znajduje się w lokalizacji. Mogą zmienić czas systemowy lub strefy czasowej wystąpień, których jesteś właścicielem. Wykonanie tej czynności co zamawianie większej liczby wystąpień w dzierżawie, można przygotować, trzeba dostosować strefy czasowej nowo poprawne działanie jest gwarantowane wystąpień. Operacje firmy Microsoft mają nie szczegółowych informacji o strefie czasowej systemu że skonfigurowane z wystąpieniami po przekazania. Dlatego nowo wdrożonych wystąpień nie może być ustawione w tej samej strefie czasowej, który został zmieniony na. W rezultacie jest odpowiedzialny za jako klient korzystający z Sprawdź i w razie potrzeby dostosować strefę czasową wystąpienia przekazywany. 

**Krok piąty** jest sprawdzenie etc/hosts. Jak uzyskać przekazywany bloki, mają różne adresy IP przypisane do różnych celów (patrz następny rozdział). Sprawdź plik etc/hosts. W przypadkach, gdy jednostki są dodawane do istniejącej dzierżawy nie będziesz już mieć etc/hosty systemów nowo wdrożonym obsługiwane poprawnie z adresami IP wcześniej poprawne działanie jest gwarantowane systemów. Dlatego jest w przypadku jako klient, aby sprawdzić prawidłowe ustawienia tak, że nowo wdrożonego wystąpienia można wchodzić w interakcje i rozpoznawania nazw jednostek wcześniej wdrożonej w swojej dzierżawie. 

## <a name="networking"></a>Networking
Przyjęto założenie, że zostały wykonane zalecenia dotyczące projektowania sieci wirtualnych platformy Azure i łączenia tych sieci wirtualnych do dużych wystąpień HANA, zgodnie z opisem w tych dokumentach:

- [Omówienie platformy SAP HANA (duże wystąpienie) i architektury na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastrukturę SAP HANA (duże wystąpienia) i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Istnieją pewne szczegóły warto wspomnieć o dotyczących sieci w pojedynczej jednostki. Każda jednostka dużych wystąpień HANA jest dostarczany z dwóch lub trzech adresów IP, które są przypisane do dwóch lub trzech portów karty Sieciowej jednostki. Trzy adresy IP są używane w konfiguracji skalowania HANA i scenariusz replikacji systemu HANA. Jeden z adresów IP przypisanych do karty Sieciowej jednostki jest puli adres IP serwera, który został opisany w [platformy SAP HANA (duże wystąpienie) omówienie i architektura na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Zapoznaj się [HLI obsługiwane scenariusze](hana-supported-scenario.md) na naukę szczegółów ethernet dla architektury.

## <a name="storage"></a>Magazyn

Układ magazynu dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) jest skonfigurowana przez platformę SAP HANA na zarządzanie usługami platformy Azure za pośrednictwem SAP zalecane linie prowadnic, zgodnie z opisem w [SAP HANA — wymagania dotyczące magazynu](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) oficjalny dokument. Nierównej rozmiary różnych woluminach za pomocą różnych HANA dużych wystąpień jednostki SKU masz udokumentowane w artykule [platformy SAP HANA (duże wystąpienie) omówienie i architektura na platformie Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Konwencje nazewnictwa woluminy magazynu są wymienione w poniższej tabeli:

| Użycie magazynu | Nazwa instalacji | Nazwa woluminu | 
| --- | --- | ---|
| Dane oprogramowania HANA | /hana/data/SID/mnt0000<m> | Magazyn IP: / hana_data_SID_mnt00001_tenant_vol |
| Dziennik HANA | /hana/log/SID/mnt0000<m> | Magazyn IP: / hana_log_SID_mnt00001_tenant_vol |
| Kopia zapasowa dziennika HANA | /Hana/log/Backups | Magazyn IP: / hana_log_backups_SID_mnt00001_tenant_vol |
| HANA udostępnione | /Hana/Shared/SID | Magazyn IP: / hana_shared_SID_mnt00001_tenant_vol/udostępnione |
| usr/sap | /usr/SAP/SID | Magazyn IP: / hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Gdzie SID = wystąpienie oprogramowania HANA identyfikator systemu 

I dzierżawca = wewnętrznego wyliczenie operacje podczas wdrażania dzierżawcy.

Jak widać, HANA, udostępniony i usr/sap współużytkują tego samego woluminu. Nomenklatura punkty instalacji obejmują identyfikator systemu wystąpień HANA, a także liczbę instalacji. W przypadku wdrożeń skalowanie w górę jest tylko jeden instalacji, takich jak mnt00001. Natomiast podczas wdrażania skalowalnego w poziomie widział tyle instaluje jako masz węzłów procesu roboczego i wzorzec. Dla środowiska skalowalnego w poziomie, danych, dzienników woluminach kopii zapasowej dziennika są udostępniane i dołączony do każdego węzła w konfiguracji skalowania w poziomie. W przypadku konfiguracji z uruchamianie wielu wystąpień SAP inny zbiór woluminów jest utworzone i dołączone do jednostki HANOWI dużego wystąpienia. Zapoznaj się [HLI obsługiwane scenariusze](hana-supported-scenario.md) szczegóły układu magazynu dla danego scenariusza.

Przeczytaj dokument dotyczący funkcji i sprawdź jednostki dużych wystąpień HANA, należy weź pod uwagę jednostki pochodzą HANA/danych z woluminu dysku zamiast atrakcyjnych i że mamy woluminu HANA/log/kopia zapasowa. Powód, dlaczego firma Microsoft o rozmiarze HANA/dane tak dużych to, że migawek magazynu, oferowanych przez firmę Microsoft dla Ciebie jako klient korzystający z używają tego samego woluminu dysku. Oznacza to, więcej pamięci masowej migawki, należy wykonać, więcej miejsca jest wykorzystywana przez migawek w woluminach przydzielonych magazynu. Wolumin HANA/log/kopia zapasowa nie jest uważany za woluminu, aby umieścić kopie zapasowe bazy danych w. Jest on wielkości ma być używany jako wolumin kopii zapasowej dla kopie zapasowe dziennika transakcji platformy HANA. W przyszłych wersjach magazynu migawek własnych usług, firma Microsoft będzie dotyczyć tego określonego woluminu, aby częściej migawki. I z tym częstsze replikacji do lokacji odzyskiwania po awarii w razie potrzeby do opcji w funkcje odzyskiwania po awarii, które są udostępniane przez infrastrukturę dużych wystąpień HANA. Szczegółowe informacje w [platformy SAP HANA (duże wystąpienia) wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Oprócz przechowywania, pod warunkiem możesz zakupić dodatkową pojemność wielokrotność 1 TB. Ten dodatkowy magazyn można dodać jako nowe woluminy do dużych wystąpień HANA.

Podczas dołączania przy użyciu oprogramowania SAP HANA na zarządzanie usługami platformy Azure, klient określa identyfikator użytkownika (UID) i identyfikator grupy (GID) dla grupy użytkowników i sapsys sidadm (np: 1000,500) jest, że podczas instalacji systemu SAP HANA, te same wartości są używane. Zgodnie z którą chcesz wdrożyć wiele wystąpień HANA w jednostce, uzyskasz wiele zestawów woluminów (jeden zestaw dla każdego wystąpienia). W rezultacie w czasie wdrażania należy zdefiniować:

- Identyfikator SID różnych wystąpieniach HANA (sidadm pochodzi z niej).
- Rozmiary różnych wystąpieniach oprogramowania HANA w pamięci. Ponieważ rozmiar pamięci, opłaty za każde wystąpienie definiuje rozmiar woluminów w każdym zestawie pojedynczy wolumin.

Na podstawie zaleceń dostawcy magazynu skonfigurowanych następujących opcji instalacji dla wszystkich woluminów zainstalowanych (z wyłączeniem rozruchu jednostki LUN):

- rw systemu plików NFS vers = 4, sprzęt, timeo = 600, rsize = 1048576, wsize = 1048576, grupa, noatime, zablokować 0 0

Te instalacji punkty są skonfigurowane w/etc/fstab, takich jak pokazano w poniższej grafice:

![fstab zainstalowane woluminy w jednostce dużych wystąpień HANA](./media/hana-installation/image1_fstab.PNG)

Dane wyjściowe polecenia df -h w jednostce dużych wystąpień HANA S72m będzie wyglądać:

![fstab zainstalowane woluminy w jednostce dużych wystąpień HANA](./media/hana-installation/image2_df_output.PNG)


Kontroler magazynu i węzły w sygnatury dużych wystąpień są synchronizowane serwerów NTP. Ci synchronizacji SAP HANA na platformie Azure (duże wystąpienia) jednostek i maszyn wirtualnych platformy Azure względem serwera NTP należy nie występuje kilka znaczną ilość czasu między infrastrukturą i jednostki obliczeniowe w platformie Azure lub duże wystąpienia sygnatury.

Aby zoptymalizować platformy SAP HANA w magazynie używane poniżej, można również ustawić następujące parametry konfiguracji SAP HANA:

- max_parallel_io_requests 128
- async_read_submit na
- async_write_submit_active na
- async_write_submit_blocks wszystkie
 
W przypadku wersji SAP HANA 1.0 maksymalnie SPS12, te parametry można ustawić podczas instalacji bazy danych SAP HANA, zgodnie z opisem w [2267798 # Uwaga SAP — Konfiguracja bazy danych SAP HANA](https://launchpad.support.sap.com/#/notes/2267798)

Ponadto można skonfigurować parametrów po instalacji baza danych SAP HANA przy użyciu struktury hdbparam. 

SAP HANA 2.0 z hdbparam framework jest przestarzała. W rezultacie należy ustawić parametry przy użyciu polecenia SQL. Aby uzyskać więcej informacji, zobacz [2399079 # Uwaga SAP: eliminacja hdbparam HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Zapoznaj się [HLI obsługiwane scenariusze](hana-supported-scenario.md) się układ pamięci masowej dla architektury.

## <a name="operating-system"></a>System operacyjny

> [!IMPORTANT] 
> Dla typu II jednostek tylko w systemie SLES 12 z dodatkiem SP2 systemu operacyjnego wersja jest obsługiwana w tym momencie. 

Obszar wymiany poprawne działanie jest gwarantowane obrazu systemu operacyjnego jest równa 2 GB na podstawie położenia [1999997 # Uwaga SAP pomocy technicznej — często zadawane pytania: oprogramowanie SAP HANA pamięci](https://launchpad.support.sap.com/#/notes/1999997/E). Wszelkie różne ustawienia żądaną musi być ustawiona przez użytkownika jako klient.

[SUSE Linux Enterprise Server 12 z dodatkiem SP1 dla aplikacji SAP](https://www.suse.com/products/sles-for-sap/hana) to dystrybucja systemu Linux zainstalowane dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia). Tej konkretnej dystrybucji zapewnia możliwości specyficznych dla SAP &quot;gotowych&quot; (w tym wstępnie ustawionych parametrów efektywne uruchamianie oprogramowania SAP w systemie SLES).

Zobacz [zasobów biblioteki/oficjalne dokumenty](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) w witrynie sieci Web SUSE i [SAP w systemie SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na sieć społeczności SAP (SCN) dla kilka przydatnych zasobów związanych z wdrożeniem oprogramowania SAP HANA w systemie SLES (w tym konfiguracji o wysokiej Dostępność, wzmocnienie zabezpieczeń określonych operacji SAP i inne).

Dodatkowe i przydatne SAP związane z systemem SUSE łącza:

- [Oprogramowanie SAP HANA w systemie SUSE Linux lokacji](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Najlepsze rozwiązanie dla programu SAP: umieścić w kolejce replikacji — oprogramowanie SAP NetWeaver w systemie SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113).
- [ClamSAP — ochrona przed wirusami SLES dla rozwiązania SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (w tym systemu SLES 12 dla aplikacji SAP).

SAP pomocy technicznej uwagi do wdrażania oprogramowania SAP HANA w systemie SLES 12:

- [Uwaga pomocy technicznej SAP #1944799 — SAP HANA wskazówki dotyczące instalacji systemu operacyjnego w systemie SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
- [Uwaga pomocy technicznej SAP 2205917 # — bazy danych SAP HANA zalecane ustawienia systemu operacyjnego dla systemu SLES 12 dla aplikacji SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
- [Obsługi uwagę #1984787 — SUSE Linux Enterprise Server 12: Uwagi dotyczące instalacji](https://launchpad.support.sap.com/#/notes/1984787).
- [Uwaga pomocy technicznej SAP #171356 — oprogramowanie SAP w systemie Linux: informacje ogólne](https://launchpad.support.sap.com/#/notes/1984787).
- [Uwaga pomocy technicznej SAP 1391070 # — Linux UUID rozwiązania](https://launchpad.support.sap.com/#/notes/1391070).

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) jest inną ofertę do uruchamiania oprogramowania SAP HANA w dużych wystąpieniach HANA. Dostępne są wersje systemu RHEL 6.7 i 7.2. Proszę Uwaga, w przeciwnym do natywnych maszynach wirtualnych platformy Azure których obsługiwane są tylko systemu RHEL 7.2 i nowsze wersje, dużych wystąpień HANA obsługują RHEL 6.7 także. Jednak zalecamy użycie wersji 7.x systemu RHEL.

Dodatkowe i przydatne SAP w systemie Red Hat łącza pokrewne:
- [Oprogramowanie SAP HANA w systemie Red Hat Linux witryny](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

SAP pomocy technicznej uwagi do wdrażania oprogramowania SAP HANA w systemie Red Hat:

- [Uwaga pomocy technicznej SAP #2009879 — SAP HANA wytyczne dotyczące systemu operacyjnego systemu Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E).
- [Obsługa uwagę #2292690 — bazy danych SAP HANA: Zalecane ustawienia systemu operacyjnego dla RHEL 7](https://launchpad.support.sap.com/#/notes/2292690).
- [Obsługa uwagę #2247020 — bazy danych SAP HANA: Zalecane ustawienia systemu operacyjnego dla RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020).
- [Uwaga pomocy technicznej SAP 1391070 # — Linux UUID rozwiązania](https://launchpad.support.sap.com/#/notes/1391070).
- [Uwaga pomocy technicznej SAP 2228351 # — Linux: Oprogramowanie SAP HANA Database SPS 11 poprawki 110 (lub nowszego) na zobowiązaniu 6-RHEL lub SLES 11](https://launchpad.support.sap.com/#/notes/2228351).
- [Obsługa uwagę #2397039 — często zadawane pytania: SAP w systemie RHEL](https://launchpad.support.sap.com/#/notes/2397039).
- [Uwaga pomocy technicznej SAP 1496410 # - Red Hat Enterprise Linux 6.x: Instalowanie i uaktualnianie](https://launchpad.support.sap.com/#/notes/1496410).
- [Uwaga pomocy technicznej SAP 2002167 # - Red Hat Enterprise Linux 7.x: Instalowanie i uaktualnianie](https://launchpad.support.sap.com/#/notes/2002167).

## <a name="time-synchronization"></a>Synchronizacja czasu

Aplikacje SAP oparte na architekturze oprogramowania SAP NetWeaver są wrażliwe na różnice czasu dla różnych składników wchodzących w skład systemu SAP. SAP ABAP krótki zrzuty z tytułu błąd ZDATE\_duże\_czasu\_DIFF są prawdopodobnie znany, jak te krótkie zrzuty są wyświetlane, gdy czas systemowy różnych serwerach lub maszynach wirtualnych przemieszcza się zbyt daleko od siebie.

Dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia), czas synchronizacji w usłudze Azure&#39;t dotyczyć jednostek obliczeniowych w sygnatury dużego wystąpienia. Wykonanie synchronizacji nie ma zastosowania do uruchamiania aplikacji SAP na natywnych maszynach wirtualnych Azure, jak platforma Azure zapewnia system&#39;s czasu są poprawnie synchronizowane. W wyniku inny termin można skonfigurować serwera używany przez firmę SAP serwery aplikacji działające na maszynach wirtualnych platformy Azure i platforma SAP HANA bazy danych wystąpień uruchomionych w dużych wystąpieniach HANA. Infrastruktury magazynu w sygnatury duże wystąpienie jest zsynchronizowany z serwerów NTP czasu.

## <a name="setting-up-smt-server-for-suse-linux"></a>Konfigurowanie serwera SMT w systemie SUSE Linux
Duże wystąpienia SAP HANA nie ma bezpośredniego połączenia z Internetem. Dlatego nie jest dość proste zarejestrować takiej jednostki u dostawcy systemu operacyjnego, a także pobieranie i stosowanie poprawek. W przypadku SUSE Linux jedno rozwiązanie może być do konfigurowania serwera SMT w Maszynie wirtualnej platformy Azure. Maszyna wirtualna platformy Azure musi znajdować się w sieci wirtualnej platformy Azure, który jest podłączony do dużych wystąpień HANA. Za pomocą takiego serwera SMT jednostki dużych wystąpień HANA może zarejestrować, a następnie pobrania poprawek. 

SUSE zawiera większych Przewodnik po ich [subskrypcji narzędzia do zarządzania w systemie SLES 12 z dodatkiem SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Jako warunek wstępny do instalacji serwera SMT spełniającego zadania dla dużych wystąpień HANA będzie potrzebne są:

- Siecią wirtualną platformy Azure podłączonej do obwodu dużych ER wystąpienie oprogramowania HANA.
- Konto SUSE, który jest skojarzony z organizacji. Natomiast organizacji musi mieć pewne ważnej subskrypcji SUSE.

### <a name="installation-of-smt-server-on-azure-vm"></a>Instalacja serwera SMT na maszynie Wirtualnej platformy Azure

W tym kroku należy zainstalować serwer SMT w Maszynie wirtualnej platformy Azure. Pierwsza miara jest zalogować się do [Centrum klienta SUSE](https://scc.suse.com/)

Ponieważ użytkownik jest zalogowany, przejdź do organizacji--> poświadczeń organizacji. Poświadczenia, które są niezbędne do skonfigurowania serwera SMT powinien znajdować się w tej sekcji.

Trzecim krokiem jest instalacji maszyny Wirtualnej z systemem SUSE Linux w sieci wirtualnej platformy Azure. Aby wdrożyć maszynę Wirtualną, należy podjąć w systemie SLES 12 z dodatkiem SP2 obrazu z galerii platformy Azure. W procesie wdrożenia nie należy zdefiniować nazwę DNS i nie używać statycznych adresów IP, jak pokazano na tym zrzucie ekranu.

![Wdrażanie maszyny wirtualnej dla serwera SMT](./media/hana-installation/image3_vm_deployment.png)

Wdrożonej maszyny Wirtualnej było mniejsze maszyny Wirtualnej i masz wewnętrzny adres IP w sieci wirtualnej platformy Azure z 10.34.1.4. Nazwa maszyny wirtualnej była smtserver. Po zakończeniu instalacji łączność z platformy HANA dużych jednostek wystąpienia została sprawdzona. Zależy od sposobu organizowania rozpoznawania nazw użytkownik może być konieczne skonfigurowanie rozpoznawania jednostek dużych wystąpień HANA w etc/hosty, maszyny wirtualnej platformy Azure. Dodaj dodatkowy dysk do maszyny Wirtualnej, która ma być używana do przechowywania poprawki. Dysk rozruchowy, sama może być za mały. W przypadku pokazano dysk zainstalowany stało się do /srv/www/htdocs, jak pokazano na poniższym zrzucie ekranu. Dysk o pojemności 100 GB, powinien wystarczyć.

![Wdrażanie maszyny wirtualnej dla serwera SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Zaloguj się do jednostek dużych wystąpień HANA, obsługa/etc/hosts i sprawdź, czy można uzyskać dostęp do maszyny Wirtualnej platformy Azure, która ma zostać uruchomiony serwer SMT za pośrednictwem sieci.

Po ten test zakończy się pomyślnie, należy zalogować się do maszyny Wirtualnej platformy Azure, które należy uruchomić serwer SMT. Jeśli używasz programu putty, zaloguj się do maszyny Wirtualnej, trzeba wykonać ta sekwencja poleceń w oknie programu bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Po wykonaniu tych poleceń, uruchom ponownie usługi powłokę bash w celu aktywowania ustawienia. Następnie uruchom YAST.

W YAST przejdź do obsługi oprogramowania i wyszukaj smt. Wybierz smt, który automatycznie przełącza się yast2 smt, jak pokazano poniżej

![SMT w yast](./media/hana-installation/image5_smt_in_yast.PNG)


Zaakceptuj wybór instalacji na smtserver. Po zakończeniu instalacji przejdź do sekcji Konfiguracja serwera SMT oraz wprowadzanie poświadczeń w organizacji z Centrum klienta SUSE pobranym wcześniej. Jako adres URL serwera SMT wprowadzenia nazwy hosta usługi maszyny Wirtualnej platformy Azure. W tej prezentacji było https://smtserver wyświetlaną w następnym grafiki.

![Konfiguracja serwera SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

W następnym kroku należy sprawdzić, czy połączenie z Centrum klientów SUSE działa. Jak widać w poniższej grafice w przypadku demonstracji, czy działało.

![Test nawiązać połączenie z Centrum klienta SUSE](./media/hana-installation/image7_test_connect.png)

Po rozpoczęciu instalacji SMT należy podać hasło bazy danych. Ponieważ jest nowa instalacja, musisz zdefiniować to hasło, jak pokazano w następnym grafiki.

![Zdefiniuj hasło dla bazy danych](./media/hana-installation/image8_define_db_passwd.PNG)

Dalej interakcji, które należy jest, gdy certyfikat zostanie utworzona. Przejdź w oknie dialogowym, jak pokazano dalej i ten krok należy kontynuować.

![Utwórz certyfikat z serwera SMT](./media/hana-installation/image9_certificate_creation.PNG)

Może istnieć kilka minut, spędzony w kroku "Uruchom sprawdzanie synchronizacji" na końcu konfiguracji. Po instalacji i konfiguracji serwera SMT, należy odnaleźć repozytorium katalogu w ramach instalacji punktu /srv/www/htdocs/oraz niektóre podkatalogi w repozytorium. 

Uruchom ponownie serwer SMT i powiązanych usług przy użyciu następujących poleceń.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>Pobierz pakiety na serwerze SMT

Po uruchomieniu wszystkich usług wybierz odpowiednie pakiety zarządzania SMT przy użyciu Yast. Wybór pakietów zależy od obrazu systemu operacyjnego serwera dużych wystąpień HANA nie wydania systemu SLES lub wersję maszyny Wirtualnej z systemem SMT server. Poniżej przedstawiono przykład ekranu wyboru.

![Wybierz pakiety](./media/hana-installation/image10_select_packages.PNG)

Po zakończeniu wybór pakietu należy uruchomić początkowe kopiowanie Wybierz pakiety do serwera SMT, z którym można skonfigurować. Ta kopia zostanie wywołany w powłoce, za pomocą polecenia smt — dublowanie pokazany poniżej


![Pobierz pakiety, serwer SMT](./media/hana-installation/image11_download_packages.PNG)

Jak widać powyżej pakietów należy skopiowania do katalogi utworzone w ramach instalacji punktu /srv/www/htdocs. Ten proces może wymagać trochę czasu. Zależy od liczby pakietów, możesz wybrać, może potrwać do godziny lub dłużej.
Po zakończeniu tego procesu, musisz przenieść SMT instalację klienta. 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Konfigurowanie klienta SMT w jednostkach dużych wystąpień HANA

Klientów w tym przypadku są duże wystąpienie oprogramowania HANA. Konfiguracja serwera SMT kopiowane clientSetup4SMT.sh skrypt do maszyny Wirtualnej platformy Azure. Kopia skrypt przez jednostkę dużych wystąpień HANA chcesz połączyć się z serwerem SMT. Uruchom skrypt z opcją -h i nadaj jako parametr nazwy serwera SMT. W tym przykładzie smtserver.

![Konfigurowanie klienta SMT](./media/hana-installation/image12_configure_client.PNG)

Może to być scenariusz, w którym obciążenia certyfikatu z serwera przez klienta zakończyło się pomyślnie, ale rejestracja nie powiodła się, jak pokazano poniżej.

![Rejestracja klienta zakończy się niepowodzeniem](./media/hana-installation/image13_registration_failed.PNG)

Jeśli rejestracja nie powiodła się, przeczytaj ten [SUSE obsługiwała dokumentów](https://www.suse.com/de-de/support/kb/doc/?id=7006024) i wykonaj kroki opisane w.

> [!IMPORTANT] 
> Jako nazwę serwera należy podać nazwę maszyny Wirtualnej, w tym smtserver wielkości liter, bez w pełni kwalifikowaną nazwę domeny. Po prostu działa nazwę maszyny Wirtualnej. 

Po wykonaniu tych kroków, należy wydać następujące polecenie w jednostce dużych wystąpień HANA

```
SUSEConnect –cleanup
```

> [!Note] 
> W naszym testy mieliśmy zawsze Poczekaj kilka minut po wykonaniu tego kroku. ClientSetup4SMT.sh natychmiastowe wykonanie, po naprawcze opisaną w artykule SUSE zakończyła się z wiadomości, że certyfikat nie jest prawidłowym jeszcze. Oczekiwanie na 5 – 10 minut zwykle i wykonywanie clientSetup4SMT.sh została zakończona w konfiguracji klienta się pomyślnie.

Rozwiąże problem, który jest potrzebny do naprawienia oparte na krokach artykułu SUSE należy ponownie uruchomić clientSetup4SMT.sh w jednostce dużych wystąpień HANA. Teraz powinno to zająć pomyślnie, jak pokazano poniżej.

![Rejestracja klienta powiodła się](./media/hana-installation/image14_finish_client_config.PNG)

Ten krok należy skonfigurować klienta SMT jednostki dużych wystąpień HANA, aby połączyć względem serwera SMT, który został zainstalowany w maszynie Wirtualnej platformy Azure. Teraz można wykonać "zypper się" lub "zypper w" instalowania poprawek systemu operacyjnego do dużych wystąpień HANA lub zainstalować dodatkowe pakiety. Przyjmuje się, że tylko ci poprawek, które pobrany przed na serwerze SMT.


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Przykład instalacji oprogramowania SAP HANA w dużych wystąpieniach HANA
W tej sekcji pokazano, jak instalowanie platformy SAP HANA w jednostce dużych wystąpień HANA. Stan początkowy, firma Microsoft ma następującą postać:

- Microsoft jest udostępniane wszystkie dane, aby wdrożyć duże wystąpienie SAP HANA.
- Duże wystąpienie SAP HANA jest otrzymanych od firmy Microsoft.
- Utworzono sieć wirtualną platformy Azure, która jest połączona z siecią lokalną.
- Masz połączenie obwodu ExpressRotue dla dużych wystąpień HANA do tej samej sieci wirtualnej platformy Azure.
- Możesz zainstalować Maszynie wirtualnej platformy Azure są używane jako przesiadkowym dużych wystąpień HANA.
- Możesz upewnienie się, że można połączyć z przesiadkowym jednostkowy dużych wystąpień HANA i na odwrót.
- Sprawdzane, czy zainstalować wszystkie niezbędne pakiety i poprawki.
- Przeczytaj uwagi SAP i dokumentacje dotyczącą dotyczące instalacji platformy HANA w systemie operacyjnym, używania i upewnienie się, że wersja platformy HANA wybór jest obsługiwany w systemie operacyjnym wydania.

Przedstawiono na następnym sekwencji jest pobieranie pakiety instalacyjne oprogramowania HANA w celu przesiadkowym maszyny Wirtualnej, w tym przypadku systemem operacyjnym Windows, kopiowania pakietów do jednostki dużych wystąpień HANA i sekwencji instalacji.

### <a name="download-of-the-sap-hana-installation-bits"></a>Pobieranie usługi bits instalacji oprogramowania SAP HANA
Ponieważ jednostki dużych wystąpień HANA nie mają bezpośredniego połączenia z Internetem, możesz nie można bezpośrednio pobrać pakiety instalacyjne programu SAP do maszyny Wirtualnej platformy HANA duże wystąpienia. Aby przezwyciężyć Brak bezpośrednie połączenie z Internetem, należy przesiadkowym. Możesz pobrać pakiety w celu przesiadkowym maszyny Wirtualnej.

Aby pobrać pakiety instalacyjne oprogramowania HANA, konieczne będzie SAP S-użytkownik lub inny użytkownik, który umożliwia dostęp do portalu Marketplace SAP. Po zalogowaniu się w przechodzą ta sekwencja ekrany:

Przejdź do [SAP Service Marketplace](https://support.sap.com/en/index.html) > kliknij przycisk pobierania oprogramowania > instalacje i uaktualnienia > przez indeks alfabetyczny > w obszarze H — SAP HANA Edition platformy > oprogramowania SAP HANA platformy Edition w wersji 2.0 > instalacji > Pobierz następujące pliki

![Pobierz HANA instalacji](./media/hana-installation/image16_download_hana.PNG)

W przypadku demonstracji pobraliśmy pakietów instalacyjnych SAP HANA w wersji 2.0. Na serwerze Azure przesiadkowym maszyny Wirtualnej należy rozwinąć samowyodrębniający archiwa do katalogu jak pokazano poniżej.

![Wyodrębnij HANA instalacji](./media/hana-installation/image17_extract_hana.PNG)

Archiwum są wyodrębniane, Kopiuj Katalog utworzony przez wyodrębniania, w tym przypadku powyżej 51052030, HANA, duże wystąpienia jednostki do woluminu /hana/shared do katalogu, który został utworzony.

> [!Important]
> Nie należy kopiować pakiety instalacyjne do katalogu głównego lub rozruchu jednostki LUN, ponieważ miejsce jest ograniczone i musi zostać użyte przez inne procesy, jak również.


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalowanie platformy SAP HANA w jednostce dużych wystąpień HANA
Aby zainstalować oprogramowanie SAP HANA, należy zalogować się jako użytkownik główny. Tylko główny ma wystarczających uprawnień, aby zainstalować oprogramowanie SAP HANA.
Pierwszą rzeczą, jaką należy wykonać, jest ustawić uprawnienia dla katalogu w którym zostały skopiowane przez/hana/udostępnione. Uprawnienia muszą ustawić np.

```
chmod –R 744 <Installation bits folder>
```

Jeśli chcesz zainstalować oprogramowanie SAP HANA przy użyciu konfiguracji graficznej pakietu gtk2 należy zainstalować w dużych wystąpieniach HANA. Sprawdź, czy jest ona zainstalowana za pomocą polecenia

```
rpm –qa | grep gtk2
```

W dalszych krokach będziemy są ukazujące instalacji oprogramowania SAP HANA przy użyciu graficznego interfejsu użytkownika. Przejdź do katalogu instalacji i przejdź do podkatalogu HDB_LCM_LINUX_X86_64 następnego kroku. Uruchamianie

```
./hdblcmgui 
```
z tego katalogu. Teraz wprowadzenie zapoznasz się sekwencji ekranów, gdzie należy podać dane dotyczące instalacji. W przypadku wykazać firma Microsoft jest instalowany serwer bazy danych SAP HANA i składniki klienta SAP HANA. W związku z tym nasze zaznaczenie jest "Baza danych SAP HANA", jak pokazano poniżej

![Wybierz platformy HANA w instalacji](./media/hana-installation/image18_hana_selection.PNG)

Na następnym ekranie możesz wybrać opcję "Zainstalować nowy System"

![Wybierz platformy HANA nowa instalacja](./media/hana-installation/image19_select_new.PNG)

Po wykonaniu tego kroku należy wybrać między kilka dodatkowych składników, które mogą być instalowane dodatkowo na serwerze bazy danych SAP HANA.

![Wybierz dodatkowe składniki platformy HANA](./media/hana-installation/image20_select_components.PNG)

Na potrzeby niniejszej dokumentacji Wybraliśmy klienckie HANA SAP i SAP HANA Studio. Instalowany jest także wystąpienie skalowanie w górę. Dlatego na następnym ekranie, musisz wybrać "System jednego hosta" 

![Wybierz opcję instalacji skalowanie w górę](./media/hana-installation/image21_single_host.PNG)

Na następnym ekranie należy podać dane

![Podaj identyfikator SID programu SAP HANA](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Jako identyfikator systemu HANA (SID), należy podać ten sam identyfikator SID jako podane firmy Microsoft, gdy uporządkowane wdrażanie dużych wystąpień HANA. Wybierając inny identyfikator SID sprawia, że instalacja się nie powieść z powodu problemów z uprawnieniami dostępu na różnych woluminach

Jako instalację katalogu, możesz użyć/hana/udostępnionego katalogu. W następnym kroku należy podać lokalizacje plików danych HANA i pliki dziennika HANA


![Podaj lokalizację dziennika HANA](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Należy zdefiniować jako dane i woluminy, które już dołączone punkty instalacji, które zawierają SID w zaznaczenie ekranu przed tym ekranie wybrano plików dziennika. Jeśli identyfikator SID niezgodność, który został wpisany, na ekranie przed wróć i dopasuj identyfikator SID wartość, na punkty instalacji.

W następnym kroku Sprawdź nazwę hosta, a ostatecznie Popraw go. 

![Nazwa hosta przeglądu](./media/hana-installation/image24_review_host_name.PNG)

W następnym kroku, należy również pobierać dane, które należy nadać do firmy Microsoft uporządkowane wdrażanie dużych wystąpień HANA. 

![Podaj użytkownika systemowego identyfikatorów UID i GID](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Należy podać ten sam identyfikator użytkownika systemu i Identyfikatora grupy użytkowników jako podane firmy Microsoft, jak kolejność wdrażania jednostki. Nie można nadać bardzo takich samych identyfikatorów, instalacja oprogramowania SAP HANA w jednostce dużych wystąpień HANA kończy się niepowodzeniem.

W dwóch następnych ekrany, które firma Microsoft nie są wyświetlane w tej dokumentacji, należy podać hasło dla użytkownika SYSTEM bazy danych SAP HANA i hasło dla użytkownika sapadm, używany dla agenta hosta SAP, który pobiera instalowany jako część datab platformy SAP HANA wystąpienie środowiska ASE.

Po zdefiniowaniu hasła, ekran potwierdzenia są wyświetlane. Sprawdź wszystkie dane na liście i kontynuować instalację. Osiągniesz ekran pokazujący postęp, która dokumentuje postęp instalacji, jak ta, poniżej

![Sprawdzanie postępu instalacji](./media/hana-installation/image27_show_progress.PNG)

Po zakończeniu instalacji, należy dodać obraz podobny do następującego

![Instalacja została zakończona.](./media/hana-installation/image28_install_finished.PNG)

W tym momencie wystąpienie SAP HANA, powinny być nawet i działa i gotowy do użycia. Powinien móc połączyć je z SAP HANA Studio. Upewnij się również wyszukać najnowszych poprawek oprogramowanie SAP HANA, a następnie zastosować te poprawki.
























































 







 




