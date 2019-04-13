---
title: Instalowanie platformy SAP HANA na platformie SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Jak zainstalować oprogramowanie SAP HANA na platformie SAP HANA na platformie Azure (duże wystąpienia).
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
ms.date: 03/05/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 96acb2e7af797f2777cc751417f50eb21faa46da
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522558"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Jak zainstalować i skonfigurować oprogramowanie SAP HANA (duże wystąpienia) na platformie Azure

Przed przeczytaniem tego artykułu, zapoznaj się z [dużych wystąpień HANA typowe terminy](hana-know-terms.md) i [jednostek SKU platformy HANA dużych wystąpień](hana-available-skus.md).

Instalacja oprogramowania SAP Hana jest odpowiedzialny za. Możesz rozpocząć instalowanie nowego oprogramowania SAP HANA na serwerze Azure (duże wystąpienia), po nawiązaniu połączenia między sieciami wirtualnymi platformy Azure i jednostek dużych wystąpień HANA. 

> [!Note]
> Zgodnie z zasadami SAP instalacji oprogramowania SAP HANA muszą być wykonywane przez osobę, który został przekazany certyfikat skojarzyć technologii SAP egzaminu, instalacja programu SAP HANA egzaminu certyfikacyjnego lub będącego integratora systemów SAP (SI).

Jeśli planowane jest instalowanie oprogramowania HANA w wersji 2.0, zobacz [Uwaga SAP pomocy technicznej 2235581 # — SAP HANA: Obsługiwane systemy operacyjne](https://launchpad.support.sap.com/#/notes/2235581/E) aby upewnić się, że system operacyjny jest obsługiwany przy użyciu oprogramowania SAP HANA wersji, w przypadku instalacji. Obsługiwany system operacyjny na potrzeby oprogramowania HANA w wersji 2.0 jest bardziej restrykcyjny niż obsługiwany system operacyjny na potrzeby platformy HANA 1.0. 

> [!IMPORTANT] 
> Dla typu II jednostek, obecnie jest obsługiwany tylko wersję systemu operacyjnego do programu systemu SLES 12 z dodatkiem SP2. 

Przed rozpoczęciem instalacji oprogramowania HANA, należy sprawdzić następujące czynności:
- [Jedn HLI](#validate-the-hana-large-instance-units)
- [Konfiguracja systemu operacyjnego](#operating-system)
- [Konfiguracja sieci](#networking)
- [Konfiguracja usługi Storage](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Sprawdź poprawność jedn dużych wystąpień HANA

Po otrzymaniu jednostki dużych wystąpień HANA od firmy Microsoft, sprawdzanie poprawności następujących ustawień i dopasować, gdy jest to konieczne.

**Pierwszego kroku** po odbieranie dużych wystąpień HANA i ustanowić dostępu i łączności do wystąpień zarejestrować się wystąpienie systemu operacyjnego z dostawcą systemu operacyjnego. Ten krok obejmuje rejestrowanie system operacyjny SUSE Linux w wystąpieniu SMT SUSE, które zostało wdrożone w maszynie Wirtualnej na platformie Azure. 

Jednostka dużych wystąpień HANA mogą łączyć się tego wystąpienia SMT. (Aby uzyskać więcej informacji, zobacz [sposobu konfigurowania serwera SMT SUSE Linux](hana-setup-smt.md)). Alternatywnie system operacyjny Red Hat muszą być zarejestrowane przy użyciu Red Hat subskrypcji Menedżera, musisz nawiązać połączenie. Aby uzyskać więcej informacji, zobacz uwagi w [co to jest oprogramowanie SAP HANA na platformie Azure (duże wystąpienia)?](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Ten krok jest niezbędny do stosowania poprawek systemu operacyjnego, który jest odpowiedzialny za klienta. SUSE, znaleźć dokumentację dotyczące instalowania i konfigurowania SMT na tej stronie o [instalacji SMT](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**Drugi etap** jest sprawdzenie, czy nowe poprawki i poprawki wersji/wersji określonego systemu operacyjnego. Sprawdź poziom poprawki duże wystąpienie oprogramowania HANA w najnowszy stan. Może to być przypadki, w którym najnowsze poprawki nie są uwzględniane. Po podjęciu przez jednostkę dużych wystąpień HANA, jest obowiązkowa, aby sprawdzić, czy mają zostać zastosowane poprawki.

**Trzeci krok** jest zapoznaj się z odpowiednimi SAP notes dotyczące instalowania i konfigurowania platformy SAP HANA w określonej wersji systemu operacyjnego/wersji. Ze względu na zmianę zaleceń lub zmiany, SAP, notatki lub konfiguracje, które są zależne od instalacji poszczególnych scenariuszy, Microsoft nie będzie zawsze można skonfigurować jednostkę dużych wystąpień HANA doskonale. 

Dlatego jest niezbędne do klienta, aby zapoznać się z SAP o związane z platformy SAP HANA dokładnie wydania Linux. Ponadto konfiguracje wydania/wersja systemu operacyjnego i zastosować ustawienia konfiguracji, jeśli jeszcze go.

W szczególności następujące parametry i ostatecznie dostosowują się do:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Począwszy od SLES12 z dodatkiem SP1 i systemu RHEL 7.2, te należy ustawić parametry w pliku konfiguracji w katalogu /etc/sysctl.d. Na przykład można utworzyć pliku konfiguracji, za pomocą nazwy 91-NetApp-HANA.conf. Dla starszych wersji w systemie SLES i RHEL te parametry muszą być in/etc/sysctl.conf zestawu.

Dla wszystkich wersji systemu RHEL, począwszy od systemu RHEL 6.3 należy przestrzegać następujących czynności: 
- Sunrpc.tcp_slot_table_entries = 128 parametr musi być ustawiony in/etc/modprobe.d/sunrpc-local.conf. Jeśli plik nie istnieje, należy najpierw utworzyć, dodając następujący wpis: 
    - Opcje sunrpc tcp_max_slot_table_entries = 128

**Czwarty krok** jest sprawdzenie czas systemowy urządzenia dużych wystąpień HANA. Wystąpienia są wdrażane ze strefą czasową systemu. Tej strefy czasowej reprezentuje lokalizację region platformy Azure, w którym znajduje się sygnatura dużych wystąpień HANA. Możesz zmienić czas systemowy lub strefy czasowej wystąpień, których jesteś właścicielem. 

W przypadku większej liczby wystąpień jest kolejność w dzierżawie, konieczne jest dostosowanie strefy czasowej nowo poprawne działanie jest gwarantowane wystąpień. Firma Microsoft nie wgląd w strefę czasową, że skonfigurowane z wystąpieniami po przekazania. W związku z tym nowo wdrożone wystąpienia nie może być ustawione w tej samej strefie czasowej, który został zmieniony na. Przedstawiono w nim jest odpowiedzialny za jako klient korzystający z dostosowanie strefę czasową wystąpienia, które były przekazywane, jeśli to konieczne. 

**Krok piąty** jest sprawdzenie etc/hosts. Jak uzyskać przekazywany bloki, mają różne adresy IP, które są przypisane do różnych celów. Sprawdź plik etc/hosts. Gdy jednostki są dodawane do istniejącej dzierżawy, nie będziesz już mieć etc/hosty systemów nowo wdrożony prawidłowo obsługiwane przy użyciu adresów IP, systemów, które zostały dostarczone wcześniej. Jest odpowiedzialny za jak klientowi upewnia się, czy nowo wdrożonego wystąpienia można wchodzić w interakcje i rozpoznawanie nazw jednostek, które są wdrożone wcześniej w tej dzierżawie. 


## <a name="operating-system"></a>System operacyjny

> [!IMPORTANT] 
> W przypadku urządzeń z typu II tylko wersję systemu operacyjnego do programu systemu SLES 12 z dodatkiem SP2 jest obecnie obsługiwane. 

Obszar wymiany poprawne działanie jest gwarantowane obrazu systemu operacyjnego jest równa 2 GB na podstawie położenia [Uwaga SAP pomocy technicznej 1999997 # — często zadawane pytania: SAP HANA pamięci](https://launchpad.support.sap.com/#/notes/1999997/E). Jako klient korzystający z Jeśli chcesz, aby różne ustawienia, należy ustawić je samodzielnie.

[SUSE Linux Enterprise Server 12 z dodatkiem SP1 dla aplikacji SAP](https://www.suse.com/products/sles-for-sap/download/) to dystrybucja systemu Linux jest instalowany dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia). Tej konkretnej dystrybucji zapewnia możliwości specyficznych dla SAP "fabrycznej" (w tym wstępnie ustawionych parametrów efektywne uruchamianie oprogramowania SAP w systemie SLES).

Zobacz [zasobów biblioteki/oficjalne dokumenty](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) w witrynie sieci Web SUSE i [SAP w systemie SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) na sieć społeczności SAP (SCN) dla kilka przydatnych zasobów związanych z wdrożeniem oprogramowania SAP HANA w systemie SLES (w tym konfiguracji o wysokiej dostępność, zwiększanie zabezpieczeń, które są specyficzne dla operacji SAP i inne).

Poniżej przedstawiono dodatkowe i przydatne SAP na związane z systemem SUSE łącza:

- [SAP HANA w systemie SUSE Linux lokacji](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Najlepsze rozwiązania dla programu SAP: Umieścić w kolejce replikacji — oprogramowanie SAP NetWeaver w systemie SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP — ochrona przed wirusami SLES dla rozwiązania SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (w tym systemu SLES 12 dla aplikacji SAP)

Poniżej przedstawiono informacje o pomocy technicznej SAP, które mają zastosowanie do wdrażania oprogramowania SAP HANA w systemie SLES 12:

- [SAP Uwaga pomocy technicznej 1944799 # — SAP HANA wskazówki dotyczące instalacji systemu operacyjnego w systemie SLES](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [Uwaga pomocy technicznej SAP #2205917 — bazy danych SAP HANA zalecanych ustawień systemu operacyjnego systemu SLES 12 dla aplikacji SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Uwaga SAP pomocy technicznej 1984787 # — SUSE Linux Enterprise Server 12: uwagi dotyczące instalacji](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP Uwaga pomocy technicznej 171356 # — oprogramowanie SAP w systemie Linux:  Informacje ogólne](https://launchpad.support.sap.com/#/notes/1984787)
- [Uwaga SAP pomocy technicznej 1391070 # — Linux UUID rozwiązania](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) jest inną ofertę do uruchamiania oprogramowania SAP HANA w dużych wystąpieniach HANA. Dostępne są wersje systemu RHEL 6.7 i 7.2. Należy pamiętać, przeciwnym do natywnych maszynach wirtualnych platformy Azure, których obsługiwane są tylko systemu RHEL 7.2 i nowsze wersje, dużych wystąpień HANA obsługują RHEL 6.7 także. Firma Microsoft zaleca jednak przy użyciu wersji 7.x systemu RHEL.

Poniżej przedstawiono dodatkowe przydatne SAP na łącza pokrewne firmy Red Hat:
- [SAP HANA w systemie Red Hat Linux lokacji](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Poniżej przedstawiono informacje o pomocy technicznej SAP, które mają zastosowanie do wdrażania oprogramowania SAP HANA w systemie Red Hat:

- [SAP Uwaga pomocy technicznej 2009879 # — wskazówki dotyczące platformy SAP HANA, systemu operacyjnego Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [Obsługa Uwaga SAP #2292690 - bazy danych SAP HANA: Zalecane ustawienia systemu operacyjnego RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [Obsługa uwagę #2247020 — bazy danych SAP HANA: Zalecane ustawienia systemu operacyjnego RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020)
- [Uwaga SAP pomocy technicznej 1391070 # — Linux UUID rozwiązania](https://launchpad.support.sap.com/#/notes/1391070)
- [Obsługa Uwaga SAP 2228351 # — Linux: SAP HANA Database SPS 11 poprawki 110 (lub nowszego) na zobowiązaniu 6-RHEL lub SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [Obsługa Uwaga SAP 2397039 # — często zadawane pytania: SAP na RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [Uwaga SAP pomocy technicznej #1496410 - Red Hat Enterprise Linux 6.x: Instalowanie i uaktualnianie](https://launchpad.support.sap.com/#/notes/1496410)
- [Uwaga SAP pomocy technicznej #2002167 - Red Hat Enterprise Linux 7.x: Instalowanie i uaktualnianie](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Synchronizacja czasu

Aplikacje SAP, które są wbudowane w architekturze oprogramowania SAP NetWeaver są wrażliwe na różnice czasu dla różnych składników wchodzących w skład systemu SAP. SAP ABAP krótki zrzuty z tytułu błąd ZDATE\_duże\_czasu\_prawdopodobnie zaczynasz RÓŻNIC. To, ponieważ te krótkie zrzuty są wyświetlane, gdy czas systemowy różnych serwerach lub maszynach wirtualnych przemieszcza się zbyt daleko od siebie.

For SAP HANA na platformie Azure (duże wystąpienia) i synchronizacji czasu, który ma być wykonywane na platformie Azure nie ma zastosowania do jednostek obliczeniowych w sygnatury dużego wystąpienia. Wykonanie synchronizacji nie ma zastosowania do uruchamiania aplikacji SAP na natywnych maszynach wirtualnych Azure, ponieważ platforma Azure zapewnia, że czas systemowy jest zsynchronizowany prawidłowo. 

W rezultacie należy skonfigurować oddzielny serwer czasu, który może być używany przez serwery aplikacji SAP, które są uruchomione na maszynach wirtualnych platformy Azure i wystąpienia bazy danych SAP HANA, które są uruchomione w dużych wystąpieniach HANA. Infrastruktury magazynu w sygnatury duże wystąpienie jest czas zsynchronizowany z serwerów NTP.


## <a name="networking"></a>Networking
Przyjęto założenie, że zostały wykonane zalecenia dotyczące projektowania sieci wirtualnych platformy Azure i łączenie tych sieci wirtualnych do dużych wystąpień HANA, zgodnie z opisem w następujących dokumentach:

- [Omówienie platformy SAP HANA (duże wystąpienie) i architektury na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastrukturę SAP HANA (duże wystąpienia) i łączności na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Istnieją pewne szczegóły warte wymienienie dotyczących sieci w pojedynczej jednostki. Każda jednostka dużych wystąpień HANA jest dostarczany z dwóch lub trzech adresów IP, które są przypisane do dwóch lub trzech portów karty Sieciowej. Trzy adresy IP są używane w konfiguracji skalowania HANA i scenariusz replikacji systemu HANA. Jeden z adresów IP, które jest przypisane do karty Sieciowej jednostki jest poza serwerem puli adresów IP, który jest opisany w [platformy SAP HANA (duże wystąpienia) — omówienie i architektura na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Aby uzyskać więcej informacji na temat Ethernet szczegóły dotyczące architektury, zobacz [HLI obsługiwane scenariusze](hana-supported-scenario.md).

## <a name="storage"></a>Magazyn

Układ magazynu dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) skonfigurowano przez platformę SAP HANA zarządzania usługami platformy Azure za pośrednictwem SAP zalecane wytyczne. Te wytyczne są udokumentowane w artykule [wymagania dotyczące magazynu oprogramowania SAP HANA](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) oficjalny dokument. 

Nierównej rozmiary różnych woluminach za pomocą różnych HANA dużych wystąpień jednostek SKU jest udokumentowany w [platformy SAP HANA (duże wystąpienia) — omówienie i architektura na platformie Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Konwencje nazewnictwa woluminy magazynu są wymienione w poniższej tabeli:

| Użycie magazynu | Nazwa instalacji | Nazwa woluminu | 
| --- | --- | ---|
| Dane oprogramowania HANA | /hana/data/SID/mnt0000\<m> | Magazyn IP: / hana_data_SID_mnt00001_tenant_vol |
| Dziennik HANA | /Hana/log/SID/mnt0000\<m > | Magazyn IP: / hana_log_SID_mnt00001_tenant_vol |
| Kopia zapasowa dziennika HANA | /Hana/log/Backups | Magazyn IP: / hana_log_backups_SID_mnt00001_tenant_vol |
| HANA udostępnione | /Hana/Shared/SID | Magazyn IP: / hana_shared_SID_mnt00001_tenant_vol/udostępnione |
| usr/sap | /usr/sap/SID | Magazyn IP: / hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*Identyfikator SID* jest wystąpienie oprogramowania HANA identyfikatora systemu. 

*Dzierżawy* jest wyliczeniem wewnętrznej operacji, podczas wdrażania dzierżawcy.

Udostępnianie usr/sap HANA tego samego woluminu. Nomenklatura punkty instalacji zawiera identyfikator systemu wystąpień HANA, a także liczbę instalacji. W przypadku wdrożeń skalowanie w górę istnieje tylko jeden instalacji, takich jak mnt00001. W przypadku wdrożeń skalowalnego w poziomie z drugiej strony, zobaczysz instaluje dowolną liczbę węzłów procesu roboczego i wzorzec dostępne dla Ciebie. 

Skalowalnego w poziomie środowisk, danych, dzienników i dziennika woluminach kopii zapasowej są udostępniane i dołączony do każdego węzła w konfiguracji skalowania w poziomie. W przypadku konfiguracji znajdujących się na wiele wystąpień SAP inny zbiór woluminów jest utworzone i dołączone do jednostki dużych wystąpień HANA. Szczegółowe informacje układu magazynu dla danego scenariusza, zobacz [HLI obsługiwane scenariusze](hana-supported-scenario.md).

Po wyświetleniu jednostki dużych wystąpień HANA zakładane jednostki pochodzą HANA/danych z woluminu dysku atrakcyjne i czy jest woluminem HANA/log/kopia zapasowa. Wprowadziliśmy HANA/danych tak dużych przyczyna jest wolumin dysku migawek magazynu, firma Microsoft oferuje czy jako klient jest używana taka sama. Więcej migawek magazynu, które należy wykonać, więcej miejsca jest wykorzystywana przez migawek w woluminach przydzielonych magazynu. 

Wolumin HANA/log/kopia zapasowa nie powinien być woluminem dla kopii zapasowych bazy danych. Jest on wielkości ma być używany jako wolumin kopii zapasowej dla kopie zapasowe dziennika transakcji platformy HANA. Aby uzyskać więcej informacji, zobacz [platformy SAP HANA (duże wystąpienia) o wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Oprócz magazynu, który jest dostarczany możesz zakupić dodatkową pojemność wielokrotność 1 TB. Ten dodatkowy magazyn można dodać jako nowe woluminy do dużego wystąpienia platformy HANA.

Zestawu dokumentacji podczas dołączania z platformą SAP HANA w usłudze Azure service management klient określa identyfikator (UID) użytkownika i grupy (GID) identyfikator dla grupy użytkowników i sapsys sidadm (na przykład: 1000,500). Podczas instalacji systemu SAP HANA należy użyć te same wartości. Ponieważ użytkownik chce wdrożyć wiele wystąpień HANA w jednostce, uzyskasz wiele zestawów woluminów (jeden zestaw dla każdego wystąpienia). W rezultacie w czasie wdrażania należy zdefiniować następujące czynności:

- Identyfikator SID różnych wystąpieniach HANA (sidadm pochodzi od niego).
- Rozmiary pamięci różnych wystąpień platformy HANA. Rozmiar pamięci na wystąpienie definiuje rozmiar woluminów w każdym zestawie pojedynczy wolumin.

Oparte na zalecenia dotyczące dostawcy magazynu, następujące opcje instalacji są skonfigurowane dla wszystkich woluminów zainstalowanych (z wyłączeniem rozruchu jednostki LUN):

- rw systemu plików NFS vers = 4, sprzęt, timeo = 600, rsize = 1048576, wsize = 1048576, grupa, noatime, zablokować 0 0

Te instalacji punkty są skonfigurowane w/etc/fstab, jak pokazano w poniższej grafice:

![fstab zainstalowane woluminy w jednostce dużych wystąpień HANA](./media/hana-installation/image1_fstab.PNG)

Dane wyjściowe polecenia df -h w jednostce dużych wystąpień HANA S72m wygląda następująco:

![fstab zainstalowane woluminy w jednostce dużych wystąpień HANA](./media/hana-installation/image2_df_output.PNG)


Kontroler magazynu i węzły w sygnatury dużych wystąpień są synchronizowane serwerów NTP. Po zsynchronizowaniu SAP HANA na platformie Azure (duże wystąpienia) jednostki maszyn wirtualnych platformy Azure względem serwera NTP, powinna być nie dryfu znaczną ilość czasu między infrastrukturą i jednostki obliczeniowe w platformie Azure lub duże wystąpienia sygnatury.

W celu zoptymalizowania platformy SAP HANA w magazynie używane poniżej, ustawić następujące parametry konfiguracji SAP HANA:

- max_parallel_io_requests 128
- async_read_submit na
- async_write_submit_active na
- async_write_submit_blocks all
 
W przypadku wersji SAP HANA 1.0 maksymalnie SPS12, te parametry można ustawić podczas instalacji bazy danych SAP HANA, zgodnie z opisem w [Uwaga SAP #2267798 — Konfiguracja bazy danych SAP HANA](https://launchpad.support.sap.com/#/notes/2267798).

Można również skonfigurować parametry po instalacji baza danych SAP HANA przy użyciu struktury hdbparam. 

Magazyn używany w dużych wystąpień HANA ma ograniczenie rozmiaru pliku. [Ograniczenie rozmiaru jest 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) każdego pliku. W przeciwieństwie do ograniczenia rozmiaru plików w systemach plików EXT3 HANA nie jest świadomość niejawnie ograniczenia pamięci masowej, wymuszane przez Magazyn dużych wystąpień HANA. w rezultacie HANA nie automatycznie utworzy nowy plik danych po osiągnięciu limitu rozmiaru pliku o rozmiarze 16TB. Jako HANA próby rośnie pliku ponad 16 TB, platformy HANA będzie raportów błędów i serwerze indeksowania ulegnie awarii na końcu.

> [!IMPORTANT]
> Aby zapobiec próby rozwój pliki danych przekracza limit rozmiaru pliku 16 TB magazynu oprogramowania HANA, duże wystąpienie oprogramowania HANA, należy ustawić następujące parametry w pliku konfiguracyjnym global.ini platformy SAP HANA
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - Zobacz też SAP Uwaga [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Należy pamiętać o Uwaga SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)


SAP HANA 2.0 z hdbparam framework jest przestarzała. W rezultacie należy ustawić parametry przy użyciu poleceń SQL. Aby uzyskać więcej informacji, zobacz [Uwaga SAP #2399079: Eliminacja hdbparam HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Zapoznaj się [HLI obsługiwane scenariusze](hana-supported-scenario.md) dowiedzieć się więcej o układ magazynu dla architektury.


**Następne kroki**

- Zapoznaj się [instalacji oprogramowania HANA na HLI](hana-example-installation.md)










































 







 




