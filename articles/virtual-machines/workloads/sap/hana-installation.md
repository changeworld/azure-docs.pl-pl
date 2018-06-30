---
title: Zainstaluj SAP HANA na SAP HANA na platformie Azure (wystąpienia duże) | Dokumentacja firmy Microsoft
description: Jak zainstalować SAP HANA na SAP HANA na platformie Azure (wystąpienia duże).
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
ms.date: 06/27/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ecef13f0ce97c7cec5a6583479911a08a99b0877
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110732"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Jak zainstalować i skonfigurować SAP HANA (duże wystąpień) w systemie Azure

Poniżej przedstawiono niektóre ważne definicje należy wiedzieć przed przeczytaniem tego przewodnika. W [omówienie SAP HANA (duże wystąpień) i architektury na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) wprowadzono dwóch różnych klas jednostek HANA dużych wystąpienie:

- S72, S72m S144, S144m, S192, S192m i S192xm, który nazywamy "Typ klasy I" z jednostki SKU.
- S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm i S960m, który nazywamy "klasy typu II' SKU.

Specyfikator klasy będzie można całej dokumentacji wystąpienia dużych HANA ostatecznie odwoływać się do różnych funkcji i wymagań oparte na jednostki SKU HANA dużych wystąpienia.

Inne definicje, często używanych są:
- **Sygnatura wystąpienia dużych:** stos infrastruktury sprzętu SAP HANA TDI certyfikowane i do uruchomienia wystąpień SAP HANA w obrębie platformy Azure w wersji dedykowanej.
- **SAP HANA na platformie Azure (duże wystąpienia):** oficjalną nazwą języka dla oferty na platformie Azure uruchomić HANA wystąpień w SAP HANA TDI certyfikowanego sprzętu, które zostało wdrożone w dużych wystąpienia sygnatury w różnych regionach platformy Azure. Powiązane termin **wystąpienia dużych HANA** jest skrót SAP HANA na platformie Azure (wystąpienia duże) i jest powszechnie używany ten przewodnik wdrożenia technicznego.


Instalacja SAP HANA odpowiada i działania można uruchomić po przekazaniem nowe SAP HANA na serwerze Azure (wystąpienia duże). I po ustanowieniu otrzymano łączność między VNet(s) Twojego Azure i HANA dużych wystąpienia jednostki. 

> [!Note]
> Dla poszczególnych zasad SAP instalacji SAP HANA odbywa się przez osobę certyfikowane do przeprowadzenia instalacji SAP HANA. Osoby, która minęła egzaminu certyfikowane skojarzyć technologii SAP, egzaminu certyfikacji SAP HANA instalacji, lub integratora systemów SAP (SI).

Sprawdź ponownie, szczególnie w przypadku planowania instalacji HANA 2.0 [2235581 # SAP Obsługa Uwaga - SAP HANA: systemy operacyjne obsługiwane](https://launchpad.support.sap.com/#/notes/2235581/E) aby mieć pewność, że system operacyjny jest obsługiwany z o wersji należy decyzję o zainstalowaniu SAP HANA. Należy pamiętać, że obsługiwany system operacyjny HANA 2.0 jest bardziej ograniczony system operacyjny obsługiwany HANA 1.0. 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Pierwsze kroki po otrzymaniu jednostek wystąpienia dużych HANA

**Pierwszy krok** po otrzymaniu wystąpienia dużych HANA i ustalonych dostępu i łączności do wystąpień, jest rejestracja systemu operacyjnego wystąpienia u swojego dostawcy systemu operacyjnego. Ten krok obejmuje rejestrowanie system operacyjny SUSE Linux w wystąpieniu SMT SUSE, potrzebne do zostały wdrożone na maszynie wirtualnej na platformie Azure. Jednostka HANA dużych wystąpienia mogą łączyć się tego wystąpienia SMT (patrz niżej w tej dokumentacji). Lub system operacyjny Red Hat muszą być zarejestrowane przy użyciu Red Hat subskrypcji Menedżera wymagane do nawiązania połączenia. Zobacz także uwagi w tym [dokumentu](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ten krok jest także niezbędne można było zastosować poprawki systemu operacyjnego. Zadanie, które jest odpowiedzialny za klienta. SUSE, można znaleźć w dokumentacji, aby zainstalować i skonfigurować SMT [tutaj](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**W drugim kroku** ma Sprawdź nowe poprawki i poprawki określonych wersji systemu operacyjnego/version. Sprawdź, czy poziom poprawki wystąpienia dużych HANA na najnowszy stan. W oparciu o czas na poprawki/poszczególnych wersji systemu operacyjnego i zmiany do obrazu, który można wdrożyć program Microsoft, mogą wystąpić przypadki, w którym najnowsze poprawki nie może być włączony. Dlatego jest obowiązkowa procedurze po podjęciu przez jednostkę wystąpienia dużych HANA można sprawdzić, czy poprawki dotyczące zabezpieczeń, funkcji, dostępności i wydajności zostały wydane w tym samym czasie przez określonego dostawcy systemu Linux i należy zastosować.

**Trzeci krok** jest wyewidencjonować istotne uwagi SAP, instalowania i konfigurowania SAP HANA na określonej wersji systemu operacyjnego/wersji. Z powodu zmiany zalecenia lub zmiany notatki SAP lub konfiguracje, które są zależne od instalacji poszczególnych scenariuszy Microsoft nie zawsze będzie mogą mieć jednostki wystąpienia dużych HANA doskonale skonfigurowane. Dlatego jest niezbędne do klientów, można odczytać uwagi SAP powiązane z SAP HANA na Twoje dokładnej wersji systemu Linux. Również Sprawdź konfiguracje systemu operacyjnego/wersji niezbędne i stosować ustawienia konfiguracji w przypadku, gdy nie jest jeszcze wykonane.

W określonych, sprawdź następujące parametry, a ostatecznie dostosowana do:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Począwszy od SLES12 z dodatkiem SP1 i RHEL 7.2, te należy ustawić parametry w pliku konfiguracji w katalogu /etc/sysctl.d. Na przykład można utworzyć plik konfiguracji o nazwie 91-NetApp-HANA.conf. W starszych wersjach SLES i RHEL te parametry muszą być in/etc/sysctl.conf zestawu.

Dla wszystkich RHEL zwalnia, a począwszy od SLES12, 
- sunrpc.tcp_slot_table_entries = 128

Parametr musi być ustawiony in/etc/modprobe.d/sunrpc-local.conf. Jeśli plik nie istnieje, należy go najpierw utworzyć, dodając następujący wpis: 
- Opcje sunrpc tcp_max_slot_table_entries = 128

**Czwarty krok** jest sprawdzenie czas systemowy HANA dużych wystąpienia jednostki. Wystąpienia zostały wdrożone za pomocą strefę czasową, reprezentujące region platformy Azure, sygnatura wystąpienia dużych HANA znajduje się w lokalizacji. Jesteś swobodę zmiany czasu systemowego lub strefy czasowej wystąpień użytkownika. Dzięki temu i kolejność więcej wystąpień w dzierżawie, można go przygotować konieczne dostosowanie strefy czasowej nowo dostarczonego wystąpień. Operacje Microsoft mieć nie wgląd w strefie czasowej systemu się skonfigurowanie z wystąpieniami po przekazania. Dlatego nowo wdrożonym wystąpień nie może być ustawiona w tej samej strefie czasowej, który zostanie zmieniony na. W związku z tym jest Twoje odpowiedzialność jako klientowi Sprawdź i w razie potrzeby dostosować strefy czasowej wystąpienia przekazywany. 

**Piąty krok** jest sprawdzenie etc/hosts. Jak uzyskać przekazywany bloków, mają one różne adresy IP przypisane do różnych celów (zobacz następną sekcję). Sprawdź plik etc/hosts. W przypadkach, gdy jednostki są dodawane do istniejącej dzierżawy nie powinien mieć itp/hosty systemów nowo wdrożonym obsługiwane poprawnie z adresami IP wcześniej dostarczonego systemów. Dlatego jest w przypadku klientów, aby sprawdzić poprawne ustawienia, czy nowo wdrożone wystąpienie mogą współpracować i rozpoznawania nazw wcześniej wdrożonej jednostki w dzierżawie. 

## <a name="networking"></a>Networking
Przyjęto założenie, że zostały wykonane zalecenia dotyczące projektowania sieciom wirtualnym platformy Azure oraz nawiązywanie połączeń z tych sieci wirtualnych wystąpień dużych HANA zgodnie z opisem w tych dokumentach:

- [Architektura na platformie Azure i SAP HANA (duże wystąpienia) — omówienie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastruktura SAP HANA (duże wystąpień) i łączność na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Istnieją pewne szczegóły warto wspomnieć o dotyczących sieci w jednej jednostki. Każda jednostka HANA dużych wystąpienia jest dostarczany z dwóch lub trzech adresów IP przypisanych do dwóch lub trzech portów kart jednostki. Trzy adresy IP są używane w HANA skalowalnego w poziomie konfiguracji oraz scenariusz HANA replikacji systemu. Jeden z adresów IP przypisanych do karty Sieciowej jednostki jest poza puli adresów IP serwera, który został opisany w [omówienie SAP HANA (duże wystąpienia) i architektury na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Zobacz [HLI obsługiwane scenariusze](hana-supported-scenario.md) Aby poznać szczegóły ethernet dla architektury.

## <a name="storage"></a>Magazyn

Układ magazynu dla SAP HANA na platformie Azure (wystąpienia duże) jest konfigurowana przy SAP HANA na zarządzania usługą Azure za pośrednictwem SAP zalecane zasady, zgodnie z opisem w [SAP HANA przestrzeni dyskowej jest potrzebne](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) oficjalny dokument. Rozmiary nierównej różnych woluminów z SKU różnych wystąpień dużych HANA otrzymano udokumentowane w [omówienie SAP HANA (duże wystąpienia) i architektury na platformie Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Konwencje nazewnictwa woluminów magazynu są wymienione w poniższej tabeli:

| Użycie magazynu | Nazwa instalacji | Nazwa woluminu | 
| --- | --- | ---|
| HANA danych | /hana/data/SID/mnt0000<m> | Magazyn IP: / hana_data_SID_mnt00001_tenant_vol |
| HANA dziennika | /hana/log/SID/mnt0000<m> | Magazyn IP: / hana_log_SID_mnt00001_tenant_vol |
| Kopia zapasowa dziennika HANA | /Hana/log/Backups | Magazyn IP: / hana_log_backups_SID_mnt00001_tenant_vol |
| HANA udostępnionych | /Hana/Shared/SID | Magazyn IP: / hana_shared_SID_mnt00001_tenant_vol/udostępnionych |
| usr/sap | /usr/SAP/SID | Magazyn IP: / hana_shared_SID_mnt00001_tenant_vol/usr_sap |

Gdzie SID = wystąpienie HANA identyfikator systemu 

I dzierżawcy = wewnętrzny wyliczenie operacje podczas wdrażania dzierżawcy.

Jak widać, HANA udostępnionych usr/sap są udostępnianie i tym samym woluminie. Nomenklatura punkty instalacji obejmują identyfikator systemu wystąpień HANA, a także numer instalacji. W przypadku dużych wdrożeń jest tylko jeden instalacji, takich jak mnt00001. We wdrożeniu skalowalnego w poziomie można zobaczyć tyle instalacji, należy mieć węzłów procesu roboczego i wzorzec. Dla środowiska skalowalnego w poziomie, danych, dzienników woluminach kopii zapasowej dziennika są udostępnione i dołączona do każdego węzła w konfiguracji skalowania w poziomie. W przypadku konfiguracji uruchamianie wielu wystąpień SAP inny zestaw woluminów jest utworzony i dołączyć do HAN dużych wystąpienia jednostki. Zobacz [HLI obsługiwane scenariusze](hana-supported-scenario.md) szczegółowe układu magazynu dla danego scenariusza.

Podczas odczytu papieru i Szukaj jednostki wystąpienia dużych HANA można zrealizować jednostki pochodzić HANA/danych z woluminu dysku zamiast atrakcyjne i że mamy woluminu HANA / / kopii zapasowej dziennika. Dlaczego możemy o rozmiarze HANA/danych tak duża dzieje się tak że migawki pamięci masowej, które oferujemy Ci jako klient korzysta z tego samego woluminu dysku. Oznacza to, więcej pamięci masowej migawki, należy wykonać, więcej miejsca jest używane przez migawek w woluminach przydzielonych magazynu. Wolumin HANA / / kopii zapasowej dziennika nie jest uważany za woluminu mają zostać umieszczone w kopii zapasowych bazy danych. Jest on o rozmiarze do użycia jako wolumin kopii zapasowej dla kopii zapasowej dziennika transakcji HANA. W przyszłych wersji magazynu migawek self usług, firma Microsoft będzie obowiązywać tego określonego woluminu, aby częstsze migawki. I z tym częstsze replikacji do lokacji odzyskiwania po awarii jeśli jest to wymagane do opcji w funkcji odzyskiwania po awarii infrastruktury HANA dużych wystąpienia. Zobacz szczegóły w [SAP HANA (duże wystąpień) wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Oprócz magazynu pod warunkiem możesz kupić dodatkowej pojemności w przyrostach 1 TB. To dodatkowe Magazyn można dodać jako nowe woluminy do wystąpień dużych HANA.

Podczas dołączania z SAP HANA na zarządzanie usługami Azure, klient określa identyfikator użytkownika (UID) i identyfikator grupy (GID) dla grupy użytkowników i sapsys sidadm (przykład: 1000,500) jest, że podczas instalacji systemu SAP HANA, te same wartości są używane. Jak chcesz wdrożyć wiele wystąpień HANA jednostki, należy pobrać wiele zestawów woluminów (jeden zestaw dla każdego wystąpienia). W związku z tym w czasie wdrażania należy zdefiniować:

- Identyfikator SID dotyczące różnych wystąpień HANA (sidadm pochodzi od niego).
- Rozmiary pamięci dotyczące różnych wystąpień HANA. Ponieważ rozmiar pamięci na wystąpień Określa rozmiar woluminów w każdym zestawie poszczególnych woluminów.

Na podstawie zaleceń dostawcy magazynu dla wszystkich zainstalowanych woluminów są skonfigurowane następujące opcje instalacji (z wykluczeniem rozruchu LUN):

- NFS rw ver = 4, twardych, timeo = 600, rsize = 1048576, wsize = 1048576, grupa, noatime, zablokować 0 0

Te instalacji punktów skonfigurowano/etc/fstab, jak pokazano na poniższej grafice:

![fstab zainstalowane woluminy w jednostce HANA dużych wystąpienia](./media/hana-installation/image1_fstab.PNG)

Dane wyjściowe polecenia df -h w jednostce wystąpienia dużych HANA S72m wyglądałyby tak jak:

![fstab zainstalowane woluminy w jednostce HANA dużych wystąpienia](./media/hana-installation/image2_df_output.PNG)


Serwery NTP zsynchronizowanie kontrolera magazynu i węzły w dużych wystąpienia sygnatury. Z synchronizacji SAP HANA w jednostkach Azure (wystąpienia duże) i maszyny wirtualne Azure względem serwera NTP, które nie powinno być nie długiego czasu sytuacji odejście od infrastruktury i jednostki obliczeń platformy Azure lub duże wystąpienia sygnatury.

Aby zoptymalizować SAP HANA do magazynu używane poniżej, należy również ustawić następujące parametry konfiguracji SAP HANA:

- max_parallel_io_requests 128
- async_read_submit na
- async_write_submit_active na
- wszystkie async_write_submit_blocks
 
Dla programu SAP HANA 1.0 do wersji SPS12, te parametry można ustawić podczas instalacji bazy danych SAP HANA zgodnie z opisem w [SAP Uwaga #2267798 - konfiguracji bazy danych SAP HANA](https://launchpad.support.sap.com/#/notes/2267798)

Także można skonfigurować parametrów po zakończeniu instalacji bazy danych SAP HANA przy użyciu hdbparam framework. 

SAP HANA 2.0 z hdbparam framework jest przestarzała. W związku z tym należy ustawić parametry za pomocą polecenia SQL. Aby uzyskać więcej informacji, zobacz [2399079 # Uwaga SAP: eliminacji hdbparam 2 HANA](https://launchpad.support.sap.com/#/notes/2399079).

Zobacz [HLI obsługiwane scenariusze](hana-supported-scenario.md) Aby dowiedzieć się układu magazynu dla architektury.

## <a name="operating-system"></a>System operacyjny

Obszar wymiany dostarczonego obrazu systemu operacyjnego jest równa 2 GB zgodnie z [1999997 # SAP Obsługa Uwaga — często zadawane pytania: SAP HANA pamięci](https://launchpad.support.sap.com/#/notes/1999997/E). Wszystkie różne ustawienia żądaną musi być ustawiona przez użytkownika jako klient.

[SUSE Linux Enterprise Server 12 SP1 dla programu SAP aplikacji](https://www.suse.com/products/sles-for-sap/hana) jest dystrybucja systemu Linux zainstalowane dla SAP HANA na platformie Azure (wystąpienia duże). Tej konkretnej dystrybucji zapewnia możliwości specyficznych dla programu SAP &quot;fabrycznej&quot; (w tym systemie SAP SLES skutecznie wstępnie ustawionymi parametrów).

Zobacz [zasobów biblioteki/oficjalne dokumenty](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) w witrynie sieci Web SUSE i [SAP w systemie SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) w sieci społeczności SAP (SCN) dla kilku przydatne zasoby związane z wdrażaniem SAP HANA na SLES (w tym ustawienia Wysoki Dostępność, specyficzne dla operacji SAP wzmocnienie zabezpieczeń i inne).

Dodatkowe i przydatne SAP SUSE związane z łącza:

- [SAP HANA w systemie SUSE Linux lokacji](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Najlepsze praktyki dla SAP: umieścić w kolejce replikacji — SAP NetWeaver w systemie SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113).
- [ClamSAP — ochrony przed wirusami SLES dla SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (w tym SLES 12 SAP aplikacji).

SAP Obsługa uwagi do implementowania SAP HANA na SLES 12:

- [Uwaga Obsługa SAP #1944799 — SAP HANA wskazówki dotyczące instalacji systemu operacyjnego SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
- [Uwaga Obsługa SAP #2205917 — SAP HANA DB zalecane ustawienia systemu operacyjnego dla SLES 12 aplikacje SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
- [Uwaga Obsługa SAP #1984787 — SUSE Linux Enterprise Server 12: Informacje o instalacji](https://launchpad.support.sap.com/#/notes/1984787).
- [Uwaga Obsługa SAP #171356 — oprogramowania SAP w systemie Linux: ogólne informacje](https://launchpad.support.sap.com/#/notes/1984787).
- [Uwaga Obsługa SAP #1391070 — rozwiązania Linux UUID](https://launchpad.support.sap.com/#/notes/1391070).

[Red Hat Enterprise Linux dla programu SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) jest inną ofertę do uruchamiania w wystąpieniach dużych HANA SAP HANA. Dostępne są wersje RHEL 6.7 i 7.2. Sprawdź Uwaga w przeciwnym macierzysty maszynach wirtualnych platformy Azure którym obsługiwane są tylko RHEL 7.2 i nowsze wersje, wystąpień dużych HANA również obsługuje RHEL 6.7. Jednak zaleca się używania wersji 7.x RHEL.

Dodatkowe i przydatne SAP Red Hat łączy pokrewnych:
- [SAP HANA na Red Hat Linux lokacji](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

SAP Obsługa uwagi do implementowania SAP HANA na Red Hat:

- [Uwaga Obsługa SAP 2009879 # - SAP HANA wytyczne dotyczące systemu operacyjnego systemu Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E).
- [Uwaga Obsługa SAP 2292690 # - SAP HANA DB: Ustawienia zalecane systemu operacyjnego dla systemów RHEL 7](https://launchpad.support.sap.com/#/notes/2292690).
- [Uwaga Obsługa SAP 2247020 # - SAP HANA DB: OS zalecane ustawienia dla RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020).
- [Uwaga Obsługa SAP #1391070 — rozwiązania Linux UUID](https://launchpad.support.sap.com/#/notes/1391070).
- [Uwaga Obsługa SAP #2228351 - Linux: SAP HANA bazy danych SPS 11 poprawki 110 (lub nowszej) na RHEL 6 lub SLES 11](https://launchpad.support.sap.com/#/notes/2228351).
- [Uwaga Obsługa SAP #2397039 — często zadawane pytania: SAP na RHEL](https://launchpad.support.sap.com/#/notes/2397039).
- [Uwaga Obsługa SAP 1496410 # - Red Hat Enterprise Linux 6.x: instalacji i uaktualniania](https://launchpad.support.sap.com/#/notes/1496410).
- [Uwaga Obsługa SAP 2002167 # - Red Hat Enterprise Linux 7.x: instalacji i uaktualniania](https://launchpad.support.sap.com/#/notes/2002167).

## <a name="time-synchronization"></a>Czas synchronizacji

Aplikacje SAP oparty na architekturze SAP NetWeaver są wrażliwe na różnice czasu różne składniki wchodzące w skład systemu SAP. Krótki ABAP SAP zrzuty z tytułu błąd ZDATE\_DUŻY\_czasu\_Różnicowy są prawdopodobnie znany, jak te zrzuty krótkich wyglądała czas systemowy na różnych serwerach lub maszyn wirtualnych przemieszcza się zbyt daleko od siebie.

Dla SAP HANA na platformie Azure (wystąpienia duże), czas synchronizacji w Azure&#39;t dotyczą jednostki obliczeń w dużych wystąpienia sygnatury. Synchronizacja nie ma zastosowania do aplikacje SAP w macierzysty maszynach wirtualnych platformy Azure, jak Azure zapewnia system&#39;czasu s jest odpowiednio zsynchronizowane. W związku z tym oddzielny czasu można skonfigurować serwera używanego przez SAP serwerów aplikacji działających na maszynach wirtualnych platformy Azure i SAP HANA bazy danych uruchomionych w wystąpieniach dużych HANA wystąpień. Infrastruktury magazynu w dużych wystąpienia sygnatury jest zsynchronizowany z Serwery NTP czasu.

## <a name="setting-up-smt-server-for-suse-linux"></a>Konfigurowanie serwera SMT SUSE Linux
SAP HANA dużych wystąpień nie ma bezpośredniego połączenia z Internetem. Dlatego nie jest dość proste zarejestrować takiej jednostki u dostawcy systemu operacyjnego oraz do pobierania i stosowania poprawek. W przypadku SUSE Linux jedno rozwiązanie można skonfigurować serwer SMT w maszynie Wirtualnej platformy Azure. Maszyny Wirtualnej Azure powinna być obsługiwana przez sieć wirtualną platformy Azure, którego jest podłączony do wystąpienia dużych HANA. Z takiego serwera SMT jednostka wystąpienia dużych HANA można zarejestrować i pobrania poprawek. 

SUSE zawiera większego przewodnika po po ich [subskrypcji narzędzia do zarządzania z dodatkiem SP2 SLES 12](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Jako warunek wstępny dla instalacji serwera SMT spełniającego zadania dla wystąpienia dużych HANA musisz:

- Sieć wirtualną platformy Azure podłączonej do obwodu HANA dużych ER wystąpienia.
- Konto SUSE, który jest skojarzony z organizacji. Podczas gdy organizacja musi dysponować niektórych ważnej subskrypcji SUSE.

### <a name="installation-of-smt-server-on-azure-vm"></a>Instalacja serwera SMT na maszynie Wirtualnej Azure

W tym kroku należy zainstalować serwer SMT w maszynie Wirtualnej platformy Azure. Pierwsza miara jest logować się do [SUSE Centrum klientów](https://scc.suse.com/)

Jako użytkownik jest zalogowany, przejdź do organizacji--> poświadczeń organizacji. Poświadczenia, które są niezbędne do skonfigurowania serwera SMT powinien znajdować się w tej sekcji.

Trzeci krok polega na zainstalowaniu Maszynę wirtualną systemu Linux SUSE w sieci wirtualnej platformy Azure. Aby wdrożyć maszynę Wirtualną, należy podjąć obraz z dodatkiem SP2 SLES 12 galerii Azure. W procesie wdrażania nie definiuje nazwy DNS i nie należy używać statycznych adresów IP, jak pokazano w tym zrzucie ekranu.

![wdrożenia maszyny wirtualnej dla serwera SMT](./media/hana-installation/image3_vm_deployment.png)

Wdrożonej maszyny Wirtualnej został mniejsze maszyny Wirtualnej i uzyskano wewnętrznego adresu IP w sieci wirtualnej platformy Azure z 10.34.1.4. Nazwa maszyny wirtualnej była smtserver. Po zakończeniu instalacji zaznaczono łączność HANA dużych jednostek wystąpienia. Zależy sposób organizowania rozpoznawania nazw może być konieczne Konfigurowanie rozpoznawania HANA wystąpienia dużych jednostek w itp/hosty maszyny wirtualnej Azure. Dodaj dodatkowy dysk do maszyny Wirtualnej, który ma być używany do przechowywania poprawki. Samego dysku rozruchowego może być za mały. W przypadku wykazać dysk został zainstalowany /srv/www/htdocs, jak pokazano na poniższym zrzucie ekranu. Dysk 100 GB powinny wystarczyć.

![wdrożenia maszyny wirtualnej dla serwera SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Zaloguj się do jednostek HANA dużych wystąpienia, obsługa/etc/hosts i sprawdź, czy można uzyskać dostęp do maszyny Wirtualnej platformy Azure, który ma zostać uruchomiony serwer SMT za pośrednictwem sieci.

Po ten test zakończy się pomyślnie, należy zalogować się do maszyny Wirtualnej platformy Azure, które powinno być ono uruchomione SMT serwera. Jeśli używasz programu putty logować się do maszyny Wirtualnej, należy wykonać ta sekwencja poleceń w oknie bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Po wykonaniu tych poleceń, uruchom ponownie Twoje bash, aby aktywować ustawienia. Następnie uruchom YAST.

W YAST przejdź do obsługi oprogramowania i wyszukaj smt. Wybierz smt, który automatycznie zmienia na yast2 smt, jak pokazano poniżej

![SMT w yast](./media/hana-installation/image5_smt_in_yast.PNG)


Zaakceptuj wybór do instalacji na smtserver. Po zainstalowaniu, przejdź do konfiguracji serwera SMT, a następnie wprowadź poświadczenia organizacji z Centrum klienta SUSE został wcześniej pobrany. Jako adres URL serwera SMT także wprowadzić nazwy hosta z maszyny Wirtualnej platformy Azure. W tej prezentacji było https://smtserver wyświetlane w następnym grafiki.

![Konfiguracja serwera SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Jako następnego kroku należy sprawdzić, czy połączenie z Centrum klientów SUSE działa. Jak widać w poniższej grafice, w przypadku pokaz rozwiązało problemu.

![Test nawiązać połączenia z Centrum klientów SUSE](./media/hana-installation/image7_test_connect.png)

Raz SMT zostanie uruchomiony Instalator, musisz podać hasło bazy danych. Ponieważ chodzi o nowej instalacji, należy określić hasło, jak pokazano w następnym grafiki.

![Określić hasło dla bazy danych](./media/hana-installation/image8_define_db_passwd.PNG)

Dalej interakcji, których masz jest tworzona pobiera certyfikat. Przejdź w oknie dialogowym, jak pokazano w następnym i powinno być kontynuowane w kroku.

![Utworzenie certyfikatu serwera SMT](./media/hana-installation/image9_certificate_creation.PNG)

Może istnieć kilka minut spędzony w kroku "Uruchom sprawdzanie synchronizacji" po zakończeniu konfiguracji. Po instalacji i konfiguracji serwera SMT, należy odnaleźć repozytorium katalogu, w obszarze instalacji punktu /srv/www/htdocs/plus niektórych podkatalogów w repozytorium. 

Uruchom ponownie serwer SMT i jej powiązane usługi przy użyciu następujących poleceń.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>Pobierz pakietów na serwerze SMT

Po uruchomieniu wszystkich usług wybierz odpowiednie pakiety zarządzania SMT przy użyciu Yast. Wybór pakietów zależy od obrazu systemu operacyjnego HANA dużych wystąpienia serwera nie SLES wersji lub wersji uruchamiania serwera SMT maszyny wirtualnej. Poniżej przedstawiono przykład ekranu wyboru.

![Wybierz pakiety](./media/hana-installation/image10_select_packages.PNG)

Po zakończeniu wybór pakietów, musisz uruchomić początkowe kopiowanie Wybierz pakiety do skonfigurowanego serwera SMT. Ta kopia zostanie wywołany w powłoce przy użyciu polecenia smt — dublowanie w sposób przedstawiony poniżej


![Pobierz pakiety SMT serwer](./media/hana-installation/image11_download_packages.PNG)

Jak widać powyżej, pakiety należy pobrać kopiowane do katalogi utworzone na podstawie instalacji punktu /srv/www/htdocs. Ten proces może trochę potrwać. Zależne od liczby pakietów, należy wybrać, może potrwać godzinę lub więcej.
Jak zakończenie tego procesu, musisz przenieść SMT instalacji klienta. 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Konfigurowanie klienta SMT HANA wystąpienia dużych jednostek

W takim przypadku klientów są HANA dużych wystąpienia jednostki. Konfigurowanie serwera SMT kopiowane clientSetup4SMT.sh skryptu do maszyny Wirtualnej platformy Azure. Kopia skryptu przez HANA dużych wystąpienia jednostki można chcesz połączyć się z serwerem SMT. Uruchom skrypt przy użyciu opcji -h i nadaj mu jako parametr nazwy serwera SMT. W tym smtserver przykład.

![Konfigurowanie klienta SMT](./media/hana-installation/image12_configure_client.PNG)

Może to być scenariusz, w których obciążenia certyfikatu z serwera przez klienta zakończyło się pomyślnie, ale rejestracja nie powiodła się, jak pokazano poniżej.

![Rejestracja klienta nie powiedzie się.](./media/hana-installation/image13_registration_failed.PNG)

Jeśli rejestracja nie powiodła się, przeczytaj [SUSE obsługuje dokumentu](https://www.suse.com/de-de/support/kb/doc/?id=7006024) , a następnie wykonaj kroki opisane istnieje.

> [!IMPORTANT] 
> Jako nazwę serwera należy podać nazwę maszyny Wirtualnej w ramach tej sprawy smtserver, bez w pełni kwalifikowaną nazwę domeny. Po prostu działa Nazwa maszyny Wirtualnej. 

Po zostały wykonane następujące kroki, należy wydać następujące polecenie w jednostce HANA dużych wystąpienia

```
SUSEConnect –cleanup
```

> [!Note] 
> Nasze testy zawsze było konieczne Poczekaj kilka minut po wykonaniu tego kroku. ClientSetup4SMT.sh natychmiastowego wykonania po naprawcze opisaną w artykule SUSE zakończył się z wiadomości, że certyfikat nie jest prawidłowym jeszcze. Oczekiwanie na 5 – 10 minut zazwyczaj i wykonywania clientSetup4SMT.sh zostało zakończone w konfiguracji klienta powiodło się.

Wystąpił problem, który niezbędnych do określenia oparte na kroki artykułu SUSE, należy ponownie clientSetup4SMT.sh w jednostce HANA dużych wystąpienia. Teraz go powinna zakończyć się pomyślnie, jak pokazano poniżej.

![Rejestracja klienta powiodło się.](./media/hana-installation/image14_finish_client_config.PNG)

Ten krok należy skonfigurować klienta SMT jednostki wystąpienia dużych HANA nawiązywania połączenia z serwerem SMT, instalowania w maszynie Wirtualnej platformy Azure. Teraz można wykonać "zypper się" lub "zypper w" instalowanie poprawek systemu operacyjnego do wystąpień dużych HANA lub zainstalować dodatkowe pakiety. Przyjmuje się, że tylko ci poprawki, które pobrane przed na serwerze SMT.


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Przykład instalacji SAP HANA na HANA dużych wystąpień
W tej części przedstawiono sposób instalowania SAP HANA HANA dużych wystąpienia jednostki. Stan początkowy będziemy mieć następującą postać:

- Microsoft podane wszystkie dane do wdrożenia można instancji dużych SAP HANA.
- Wystąpienie dużych SAP HANA otrzymanej od firmy Microsoft.
- Możesz utworzyć sieć wirtualną platformy Azure, która jest połączona z siecią lokalną.
- Obwód ExpressRotue jest połączony HANA dużych wystąpień do tej samej sieci wirtualnej platformy Azure.
- Zainstalowano maszyny Wirtualnej platformy Azure są używane jako okno przeskoku HANA dużych wystąpień.
- Wprowadzone można łączenie z pola skok do jednostki wystąpienia dużych HANA i na odwrót.
- Sprawdzić, czy zainstalowano wszystkie niezbędne pakiety i poprawki.
- Przeczytaj uwagi SAP i dokumenty dotyczące instalacji HANA na system operacyjny używany i nie jest upewnienie się, że wersja HANA wybór jest obsługiwana na system operacyjny wersji.

Co to jest wyświetlany w sekwencjach dalej jest pobierania pakietów instalacyjnych HANA okno przeskoku uruchomionych maszyn wirtualnych, w tym przypadku na systemu operacyjnego, kopiowania pakietów HANA dużych wystąpienie jednostki oraz sekwencję Instalatora.

### <a name="download-of-the-sap-hana-installation-bits"></a>Pobieranie usługi bits instalacji SAP HANA
Ponieważ jednostki HANA dużych wystąpienie nie ma bezpośredniego połączenia z Internetem, nie można bezpośrednio pobraniu pakiety instalacyjne z SAP do maszyny Wirtualnej HANA dużych wystąpienia. Aby wyeliminować Brak bezpośrednie połączenie internetowe, należy okno przeskoku. Okno przeskoku maszyny Wirtualnej, należy pobrać pakiety.

Aby pobrać pakiety instalacyjne HANA, należy użytkownik S SAP lub innego użytkownika, dzięki czemu można uzyskać dostępu do witryny Marketplace SAP. Po zalogowaniu, przejdź do tej sekwencji ekrany:

Przejdź do [Marketplace usługi SAP](https://support.sap.com/en/index.html) > kliknij polecenie Pobierz oprogramowanie > instalacje i uaktualnienia > przez indeks alfabetyczny > w obszarze H — SAP HANA wersji platformy > SAP HANA platformy wersji 2.0 > instalacji > Pobierz następujące pliki

![Pobierz HANA instalacji](./media/hana-installation/image16_download_hana.PNG)

W przypadku pokaz pobraliśmy pakietów instalacyjnych SAP HANA 2.0. W oknie skok Azure VM rozwinięciu samowyodrębniający archiwa do katalogu w sposób przedstawiony poniżej.

![Wyodrębnij HANA instalacji](./media/hana-installation/image17_extract_hana.PNG)

Jako archiwum są wyodrębniane, skopiuj Katalog utworzony przez w przypadku powyżej 51052030, wyodrębniania dużych HANA jednostki wystąpienia w wolumin /hana/shared do katalogu, który został utworzony.

> [!Important]
> Nie należy kopiować pakiety instalacyjne do katalogu głównego lub rozruchu jednostki LUN, od miejsca jest ograniczona, a musi zostać użyte przez inne procesy, jak również.


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Zainstaluj SAP HANA w jednostce HANA dużych wystąpienia
Aby można było zainstalować SAP HANA, musisz zalogować się jako użytkownik główny. Tylko główny ma wystarczające uprawnienia do instalowania SAP HANA.
W pierwszej kolejności należy wykonać, jest uprawnienia są kopiowane do/hana/udostępnionego katalogu. Należy określić takich jak uprawnienia

```
chmod –R 744 <Installation bits folder>
```

Jeśli chcesz zainstalować SAP HANA przy użyciu graficznego instalacji pakietu gtk2 trzeba zainstalować w wystąpieniach dużych HANA. Sprawdź, czy jest zainstalowany przy użyciu polecenia

```
rpm –qa | grep gtk2
```

W dalszych krokach możemy są prezentacja konfiguracji SAP HANA z graficznego interfejsu użytkownika. Jako następnego kroku przejdź do katalogu instalacji, a następnie przejdź do katalogu sub HDB_LCM_LINUX_X86_64. Uruchamianie

```
./hdblcmgui 
```
z tego katalogu. Teraz pobierania zapoznasz się sekwencję ekrany gdzie należy podać dane dotyczące instalacji. W przypadku wykazać firma Microsoft jest instalowany serwer bazy danych SAP HANA i składniki klienta programu SAP HANA. W związku z tym naszej zaznaczenie jest "Bazy danych SAP HANA", jak pokazano poniżej

![Wybierz HANA w instalacji](./media/hana-installation/image18_hana_selection.PNG)

Na następnym ekranie użytkownik wybierze opcję "Zainstaluj nowego systemu"

![Wybierz opcję HANA nowa instalacja](./media/hana-installation/image19_select_new.PNG)

Po wykonaniu tego kroku musisz wybrać między kilka dodatkowych składników, które mogą być instalowane dodatkowo na serwerze bazy danych SAP HANA.

![Wybierz dodatkowe składniki HANA](./media/hana-installation/image20_select_components.PNG)

Na potrzeby tej dokumentacji Wybraliśmy SAP HANA klienta i programu SAP HANA Studio. Możemy również zainstalować wystąpienie skalowanie w pionie. Dlatego na następnym ekranie, musisz wybrać "Jednego hosta System" 

![Wybierz opcję instalacji skalowanie w pionie](./media/hana-installation/image21_single_host.PNG)

Na następnym ekranie należy podać niektóre dane

![Podaj SAP HANA SID](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Jako HANA systemu (SID), należy podać ten sam identyfikator SID, jak podane firmy Microsoft, gdy uporządkowane wdrożenia HANA dużych wystąpienia. Wybieranie innym identyfikatorem SID sprawia, że instalacja się nie powieść z powodu problemów z uprawnieniami dostępu na różnych woluminach

Instalacja katalogu Użyj/hana/udostępniony katalog. W następnym kroku należy podać lokalizacje plików danych HANA i plików dziennika HANA


![Podaj lokalizację dziennika HANA](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Należy zdefiniować jako dane i pliki w woluminach, które już dołączone punkty instalacji, które zawierają identyfikator SID w zaznaczenie ekranu przed tym ekranie dziennika. Jeśli identyfikator SID niezgodność, który został wpisany w, na ekranie przed, przejdź wstecz i dopasować identyfikatora SID na wartość, które znajdują się w punktach instalacji.

W następnym kroku Sprawdź nazwę hosta i ostatecznie Popraw go. 

![Przejrzyj nazwy hosta](./media/hana-installation/image24_review_host_name.PNG)

W następnym kroku należy również pobierać dane, które należy nadać do firmy Microsoft uporządkowane wdrożenia HANA dużych wystąpienia. 

![Podaj użytkownika systemu identyfikatorów UID i GID](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Konieczne jest zapewnienie tego samego Identyfikatora użytkownika systemu i Identyfikatora grupy użytkowników jako podane Microsoft jako kolejność wdrażania jednostki. Jeśli nie można nadać bardzo takich samych identyfikatorów, instalacja SAP HANA w jednostce HANA dużych wystąpienie nie powiedzie się.

W dwóch następnych ekranach, które firma Microsoft nie są wyświetlane w tej dokumentacji, musisz podać hasło dla użytkownika SYSTEM bazy danych SAP HANA i hasło użytkownika sapadm, który służy do agenta hosta SAP, które są instalowane jako część datab SAP HANA wystąpienie ASE.

Po zdefiniowaniu hasła, ekran potwierdzenia jest wyświetlane. Sprawdź wszystkie dane na liście i kontynuować instalację. Zostanie wyświetlona ekran postępu dokumentów postęp instalacji, jak poniżej

![Sprawdź postęp instalacji](./media/hana-installation/image27_show_progress.PNG)

Jak zakończeniu instalacji należy obraz podobny do następującego

![Instalacja została zakończona](./media/hana-installation/image28_install_finished.PNG)

W tym momencie z wystąpieniem SAP HANA powinna być maksymalnie i działa prawidłowo i gotowe do użycia. Można się z nią połączyć z SAP HANA Studio. Upewnij się, sprawdź, czy są dostępne najnowsze poprawki SAP HANA, a następnie zastosować te poprawki również.
























































 







 




