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
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dad088138fea2dd4fadc0cc9eed71245c32a8e0b
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162671"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Jak zainstalować i skonfigurować oprogramowanie SAP HANA (duże wystąpienia) na platformie Azure

Przed przeczytaniem tego artykułu, zapoznaj się z [dużych wystąpień HANA typowe terminy](hana-know-terms.md) i [jednostek SKU platformy HANA dużych wystąpień](hana-available-skus.md).

Instalacja oprogramowania SAP HANA jest odpowiedzialny za i uruchomieniem działania po przekazywanie nowego oprogramowania SAP HANA na platformie Azure (duże wystąpienia) serwera. I po jego nawiązaniu stało się łączność między swoje sieciach wirtualnych platformy Azure i jednostek dużych wystąpień HANA. 

> [!Note]
> Zgodnie z zasadami SAP instalacji oprogramowania SAP HANA, muszą być wykonywane przez osoby z certyfikatami poświadczającymi zgodność ze przeprowadzać instalacje oprogramowania SAP HANA. Osoba, został przekazany certyfikat skojarzyć technologii SAP egzaminu, egzaminu certyfikacyjnego instalacja programu SAP HANA, lub przez integratora systemów SAP (SI).

Sprawdź ponownie, szczególnie w przypadku, gdy planujesz zainstalować oprogramowania HANA w wersji 2.0 [2235581 # Uwaga SAP pomocy technicznej — SAP HANA: obsługiwane systemy operacyjne](https://launchpad.support.sap.com/#/notes/2235581/E) aby upewnić się, że system operacyjny jest obsługiwany z SAP HANA wersji decyzję o zainstalowaniu. Należy pamiętać, że obsługiwany system operacyjny na potrzeby oprogramowania HANA w wersji 2.0 jest bardziej ograniczony system operacyjny obsługiwany HANA 1.0. 

> [!IMPORTANT] 
> Dla typu II jednostek tylko w systemie SLES 12 z dodatkiem SP2 systemu operacyjnego wersja jest obsługiwana w tym momencie. 

Przed rozpoczęciem instalacji oprogramowania HANA, należy sprawdzić następujące czynności:
- [Sprawdź poprawność jedn HLI](#validate-the-hana-large-instance-units)
- [Konfiguracja systemu operacyjnego](#operating-system)
- [Konfiguracja sieci](#networking)
- [Konfiguracja usługi Storage](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Sprawdź poprawność jedn dużych wystąpień HANA

Po otrzymaniu jednostki dużych wystąpień HANA, od firmy Microsoft Sprawdź poprawność poniższych ustawień i dopasować, gdy jest to konieczne.

**Pierwszy krok** po otrzymaniu dużych wystąpień HANA i dostępu ustanowionym i łączności do wystąpień, zarejestrować się wystąpienie systemu operacyjnego z dostawcą systemu operacyjnego. Ten krok obejmuje rejestrowanie system operacyjny SUSE Linux w wystąpieniu SMT SUSE, musisz mieć wdrożony na maszynie wirtualnej na platformie Azure. Jednostka dużych wystąpień HANA można połączyć z tym wystąpieniem SMT (zobacz [instrukcje instalacji SMT serwera dla systemu Linux SUSE](hana-setup-smt.md)). Lub system operacyjny Red Hat muszą być zarejestrowane przy użyciu Red Hat subskrypcji Menedżera musisz nawiązać połączenie. Zobacz również uwagi w tym [dokumentu](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ten krok jest również niezbędne można było stosowanie poprawek systemu operacyjnego. Zadanie, które jest odpowiedzialny za klienta. SUSE, można znaleźć w dokumentacji, aby zainstalować i skonfigurować SMT [tutaj](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

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

### <a name="time-synchronization"></a>Synchronizacja czasu

Aplikacje SAP oparte na architekturze oprogramowania SAP NetWeaver są wrażliwe na różnice czasu dla różnych składników wchodzących w skład systemu SAP. SAP ABAP krótki zrzuty z tytułu błąd ZDATE\_duże\_czasu\_DIFF są prawdopodobnie znany, jak te krótkie zrzuty są wyświetlane, gdy czas systemowy różnych serwerach lub maszynach wirtualnych przemieszcza się zbyt daleko od siebie.

Dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia), czas synchronizacji w usłudze Azure&#39;t dotyczyć jednostek obliczeniowych w sygnatury dużego wystąpienia. Wykonanie synchronizacji nie ma zastosowania do uruchamiania aplikacji SAP na natywnych maszynach wirtualnych Azure, jak platforma Azure zapewnia system&#39;s czasu są poprawnie synchronizowane. W wyniku inny termin można skonfigurować serwera używany przez firmę SAP serwery aplikacji działające na maszynach wirtualnych platformy Azure i platforma SAP HANA bazy danych wystąpień uruchomionych w dużych wystąpieniach HANA. Infrastruktury magazynu w sygnatury duże wystąpienie jest zsynchronizowany z serwerów NTP czasu.


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

Przeczytaj dokument dotyczący funkcji i sprawdź jednostki dużych wystąpień HANA, należy weź pod uwagę jednostki pochodzą HANA/danych z woluminu dysku zamiast atrakcyjnych i że mamy woluminu HANA/log/kopia zapasowa. Powód, dlaczego firma Microsoft o rozmiarze HANA/dane tak dużych to, że migawek magazynu, oferowanych przez firmę Microsoft dla Ciebie jako klient korzystający z używają tego samego woluminu dysku. Oznacza to, więcej pamięci masowej migawki, należy wykonać, więcej miejsca jest wykorzystywana przez migawek w woluminach przydzielonych magazynu. Wolumin HANA/log/kopia zapasowa nie jest uważany za woluminu, aby umieścić kopie zapasowe bazy danych w. Jest on wielkości ma być używany jako wolumin kopii zapasowej dla kopie zapasowe dziennika transakcji platformy HANA. Szczegółowe informacje w [platformy SAP HANA (duże wystąpienia) wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

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


**Następne kroki**

- Zapoznaj się [instalacji oprogramowania HANA na HLI](hana-example-installation.md)










































 







 




