---
title: Instalowanie sap hana na SAP HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Jak zainstalować sap HANA na SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca59305b22fcf1e81ef518612910731cb6edea5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617093"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Jak zainstalować i skonfigurować SAP HANA (Duże wystąpienia) na platformie Azure

Przed przeczytaniem tego artykułu zapoznaj się z [typowymi terminami HANA Large Instances](hana-know-terms.md) i [jednostkami SKU dużych instancji HANA.](hana-available-skus.md)

Instalacja SAP HANA jest twoim obowiązkiem. Po ustanowieniu łączności między sieciami wirtualnymi platformy Azure a jednostkami dużych wystąpień platformy HANA można rozpocząć instalowanie nowego serwera SAP HANA na platformie Azure (duże wystąpienia). 

> [!Note]
> Zgodnie z zasadami SAP instalacja SAP HANA musi być wykonywana przez osobę, która zdała egzamin Certified SAP Technology Associate, egzamin certyfikacyjny SAP HANA Installation lub jest integratorem systemów z certyfikatem SAP (SI).

Podczas planowania instalacji HANA 2.0, zobacz [uwaga pomocy technicznej SAP #2235581 — SAP HANA: Obsługiwane systemy operacyjne,](https://launchpad.support.sap.com/#/notes/2235581/E) aby upewnić się, że system operacyjny jest obsługiwany przez sap HANA zwolnić, że jesteś instalowany. Obsługiwany system operacyjny dla HANA 2.0 jest bardziej restrykcyjny niż obsługiwany system operacyjny dla hana 1.0. Należy również sprawdzić, czy wydanie systemu operacyjnego, które Cię interesuje, jest wymienione jako obsługiwane dla konkretnej jednostki HLI na tej [opublikowanej liście](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Kliknij urządzenie, aby uzyskać szczegółowe informacje z listą obsługiwanych os tej jednostki. 

Przed rozpoczęciem instalacji HANA sprawdź poprawność następujących poprawek:
- [Jednostki(-a) HLI](#validate-the-hana-large-instance-units)
- [Konfiguracja systemu operacyjnego](#operating-system)
- [Konfiguracja sieci](#networking)
- [Konfiguracja usługi Storage](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Sprawdzanie poprawności jednostek dużych wystąpień HANA

Po otrzymaniu jednostki hana duże wystąpienie od firmy Microsoft, sprawdź poprawność następujących ustawień i dostosować w razie potrzeby.

**Pierwszym krokiem** po otrzymaniu wystąpienia dużych HANA i ustanowienia dostępu i łączności do wystąpień, jest sprawdzenie w witrynie Azure portal, czy wystąpienia są wyświetlane z poprawnymi jednostkami SKU i OS. Przeczytaj [kontrolę dużych wystąpień platformy Azure HANA za pośrednictwem witryny Azure portal,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal) aby uzyskać kroki niezbędne do przeprowadzenia kontroli.

**Drugim krokiem** po otrzymaniu wystąpienia dużych HANA i ustanowienia dostępu i łączności z wystąpieniami, jest zarejestrowanie systemu operacyjnego wystąpienia u dostawcy systemu operacyjnego. Ten krok obejmuje rejestrowanie systemu operacyjnego SUSE Linux w wystąpieniu SUSE SMT, który jest wdrażany na maszynie wirtualnej na platformie Azure. 

Jednostka dużych wystąpień HANA może łączyć się z tym wystąpieniem SMT. (Aby uzyskać więcej informacji, zobacz [Jak skonfigurować serwer SMT dla SUSE Linux](hana-setup-smt.md)). Alternatywnie, twój system operacyjny Red Hat musi być zarejestrowany w Menedżerze subskrypcji Red Hat, z którego musisz się połączyć. Aby uzyskać więcej informacji, zobacz uwagi w [co to jest SAP HANA na platformie Azure (duże wystąpienia)?](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Ten krok jest niezbędny do poprawki systemu operacyjnego, który jest odpowiedzialny za klienta. W przypadku SUSE znajdź dokumentację instalacji i konfigurowania narzędzia SMT na tej stronie dotyczącej [instalacji SMT](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

**Trzecim krokiem** jest sprawdzenie nowych poprawek i poprawek konkretnej wersji/wersji systemu operacyjnego. Sprawdź, czy poziom poprawki wystąpienia dużych HANA jest w stanie najnowszego. Mogą wystąpić przypadki, w których najnowsze poprawki nie są uwzględniane. Po przejęciu jednostki dużych wystąpień HANA jest obowiązkowe, aby sprawdzić, czy poprawki muszą być stosowane.

**Czwartym krokiem** jest sprawdzenie odpowiednich notatek SAP do instalowania i konfigurowania SAP HANA w określonej wersji/wersji systemu operacyjnego. Ze względu na zmianę zaleceń lub zmian w uwagach lub konfiguracjach SAP, które są zależne od poszczególnych scenariuszy instalacji, firma Microsoft nie zawsze będzie w stanie idealnie skonfigurować jednostkę dużych wystąpień HANA. 

W związku z tym jest obowiązkowe dla Ciebie jako klienta, aby przeczytać notatki SAP związane z SAP HANA dla dokładnej wersji systemu Linux. Sprawdź również konfiguracje wersji/wersji systemu operacyjnego i zastosuj ustawienia konfiguracji, jeśli jeszcze tego nie zrobiłeś.

W szczególności sprawdź następujące parametry i ostatecznie dostosuj do:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Począwszy od SLES12 SP1 i RHEL 7.2, parametry te muszą być ustawione w pliku konfiguracyjnym w katalogu /etc/sysctl.d. Na przykład należy utworzyć plik konfiguracyjny o nazwie 91-NetApp-HANA.conf. W przypadku starszych wersji SLES i RHEL parametry te muszą być ustawione w/etc/sysctl.conf.

W przypadku wszystkich wydań RHEL, począwszy od RHEL 6.3, należy pamiętać: 
- Parametr sunrpc.tcp_slot_table_entries = 128 musi być ustawiony w/etc/modprobe.d/sunrpc-local.conf. Jeśli plik nie istnieje, należy go najpierw utworzyć, dodając wpis: 
    - opcje sunrpc tcp_max_slot_table_entries=128

**Piątym krokiem** jest sprawdzenie czasu systemowego jednostki dużych wystąpień HANA. Wystąpienia są wdrażane z systemową strefą czasową. Ta strefa czasowa reprezentuje lokalizację regionu platformy Azure, w którym znajduje się sygnatura dużego wystąpienia HANA. Można zmienić czas systemowy lub strefę czasową posiadanych wystąpień. 

Jeśli zamówisz więcej wystąpień do dzierżawy, należy dostosować strefę czasową nowo dostarczonych wystąpień. Firma Microsoft nie ma wglądu w strefę czasową systemu skonfigurowaną z wystąpieniami po przekazaniu. W związku z tym nowo wdrożonych wystąpień może nie być ustawiona w tej samej strefie czasowej, jak ten, który został zmieniony. Twoim obowiązkiem jako klienta jest dostosowanie strefy czasowej instancji, które zostały przekazane, jeśli to konieczne. 

**Szóstym krokiem** jest sprawdzenie etc / hosts. Gdy ostrza są przekazywane, mają różne adresy IP, które są przypisane do różnych celów. Sprawdź plik etc/hosts. Gdy jednostki są dodawane do istniejącej dzierżawy, nie oczekuj, że etc/hosts nowo wdrożonych systemów jest poprawnie obsługiwany z adresami IP systemów, które zostały dostarczone wcześniej. Twoim obowiązkiem jako klienta jest upewnienie się, że nowo wdrożone wystąpienie może wchodzić w interakcje i rozwiązywać nazwy jednostek, które zostały wdrożone wcześniej w dzierżawie. 


## <a name="operating-system"></a>System operacyjny

Przestrzeń wymiany dostarczonego obrazu systemu operacyjnego jest ustawiona na 2 GB zgodnie z [uwagą techniczną SAP #1999997 - FAQ: pamięć SAP HANA](https://launchpad.support.sap.com/#/notes/1999997/E). Jako klient, jeśli chcesz innego ustawienia, musisz ustawić je samodzielnie.

[SUSE Linux Enterprise Server 12 SP1 dla aplikacji SAP](https://www.suse.com/products/sles-for-sap/download/) to dystrybucja systemu Linux zainstalowana dla systemu SAP HANA na platformie Azure (duże wystąpienia). Ta szczególna dystrybucja zapewnia możliwości specyficzne dla sap "po wyjęciu z pudełka" (w tym wstępnie ustawione parametry do skutecznego uruchamiania SAP na SLES).

Zobacz [Biblioteka zasobów/białe księgi](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) w witrynie sieci SUSE i [SAP na SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) w sap community network (SCN) dla kilku przydatnych zasobów związanych z wdrażaniem SAP HANA na SLES (w tym konfiguracji wysokiej dostępności, wzmocnienie zabezpieczeń, które są specyficzne dla operacji SAP i więcej).

Poniżej znajduje się dodatkowe i przydatne SAP na linki związane z SUSE:

- [SAP HANA na stronie SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Najważniejsze wskazówki dotyczące sap: Replikacja enqueue — SAP NetWeaver w SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – Ochrona przed wirusami SLES dla SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (w tym SLES 12 dla aplikacji SAP)

Poniżej przedstawiono uwagi dotyczące obsługi SAP, które mają zastosowanie do wdrażania sap hana na SLES 12:

- [Uwaga dotycząca obsługi sap #1944799 — wytyczne SAP HANA dotyczące instalacji systemu operacyjnego SLES](http://service.sap.com/sap/support/notes/1944799)
- [Uwaga dotycząca obsługi SAP #2205917 – zalecane ustawienia systemu operacyjnego SAP HANA DB dla SLES 12 dla aplikacji SAP](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Uwaga dotycząca obsługi sap #1984787 – SUSE Linux Enterprise Server 12: notatki instalacyjne](https://launchpad.support.sap.com/#/notes/1984787)
- [Uwaga dotycząca obsługi SAP #171356 – oprogramowanie SAP w systemie Linux: Ogólne informacje](https://launchpad.support.sap.com/#/notes/1984787)
- [Uwaga dotycząca obsługi SAP #1391070 – rozwiązania UUID systemu Linux](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) to kolejna oferta uruchamiania SAP HANA na dużych wystąpieniach HANA. Dostępne i obsługiwane są wersje RHEL 7.2 i 7.3. 

Poniżej znajdują się dodatkowe przydatne SAP na Red Hat powiązanych linków:
- [SAP HANA na stronie Red Hat Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Poniżej znajdują się uwagi dotyczące pomocy technicznej SAP, które mają zastosowanie do wdrażania SAP HANA na Red Hat:

- [Uwaga dotycząca obsługi SAP #2009879 - Wytyczne SAP HANA dla systemu operacyjnego Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [Uwaga dotycząca obsługi sap #2292690 - SAP HANA DB: Zalecane ustawienia systemu operacyjnego dla RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [Uwaga dotycząca obsługi SAP #1391070 – rozwiązania UUID systemu Linux](https://launchpad.support.sap.com/#/notes/1391070)
- [Uwaga dotycząca obsługi SAP #2228351 - Linux: SAP HANA Database SPS 11 revision 110 (lub nowsze) na RHEL 6 lub SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [Uwaga dotycząca obsługi sap #2397039 — często zadawane pytania: SAP na RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [Uwaga dotycząca obsługi sap #2002167 - Red Hat Enterprise Linux 7.x: Instalacja i uaktualnienie](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Synchronizacja czasu

Aplikacje SAP, które są zbudowane na architekturze SAP NetWeaver są wrażliwe na różnice czasowe dla różnych składników, które składają się na system SAP. Krótkie zrzuty SAP ABAP z tytułem\_\_błędu\_ZDATE LARGE TIME DIFF są prawdopodobnie znane. To dlatego, że te krótkie zrzuty pojawiają się, gdy czas systemowy różnych serwerów lub maszyn wirtualnych dryfuje zbyt daleko od siebie.

W przypadku usługi SAP HANA na platformie Azure (duże wystąpienia) synchronizacja czasu wykonywana na platformie Azure nie ma zastosowania do jednostek obliczeniowych w sygnaturach dużych wystąpień. Ta synchronizacja nie ma zastosowania do uruchamiania aplikacji SAP na natywnych maszynach wirtualnych platformy Azure, ponieważ platforma Azure zapewnia, że czas systemu jest prawidłowo zsynchronizowany. 

W związku z tym należy skonfigurować oddzielny serwer czasu, który może być używany przez serwery aplikacji SAP, które są uruchomione na maszynach wirtualnych platformy Azure i przez wystąpienia bazy danych SAP HANA, które są uruchomione w dużych wystąpieniach HANA. Infrastruktura magazynu w sygnaturach dużych wystąpień jest synchronizowana czasowo z serwerami NTP.


## <a name="networking"></a>Obsługa sieci
Zakładamy, że postępował zgodnie z zaleceniami podczas projektowania sieci wirtualnych platformy Azure i łączenia tych sieci wirtualnych z wystąpieniami dużymi hana, zgodnie z opisem w następujących dokumentach:

- [Omówienie i architektura SAP HANA (duże wystąpienie) na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Infrastruktura i łączność SAP HANA (Duże wystąpienia) na platformie Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Istnieje kilka szczegółów, o których warto wspomnieć o sieci pojedynczych jednostek. Każda jednostka dużych wystąpień HANA jest wyposażona w dwa lub trzy adresy IP przypisane do dwóch lub trzech portów karty sieciowej. Trzy adresy IP są używane w konfiguracjach skalowania w poziomie HANA i scenariusz replikacji systemu HANA. Jeden z adresów IP przypisanych do karty sieciowej urządzenia jest poza pulą adresów IP serwera opisaną w [omówienie SAP HANA (duże wystąpienia) i architekturę na platformie Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

Aby uzyskać więcej informacji na temat szczegółów sieci Ethernet dla architektury, zobacz [scenariusze obsługiwane przez HLI](hana-supported-scenario.md).

## <a name="storage"></a>Magazyn

Układ magazynu dla sap HANA na platformie Azure (duże wystąpienia) `service management` jest skonfigurowany przez SAP HANA na platformie Azure za pomocą zalecanych przez SAP wskazówek. Te wytyczne są udokumentowane w white paper [wymagania dotyczące magazynu SAP HANA.](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 

Przybliżone rozmiary różnych woluminów za pomocą różnych jednostek SKU dużych wystąpień HANA są dokumentowane w [omówienie SAP HANA (Duże wystąpienia) i architektury na platformie Azure.](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Konwencje nazewnictwa woluminów magazynu są wymienione w poniższej tabeli:

| Użycie pamięci masowej | Nazwa montażu | Nazwa woluminu | 
| --- | --- | ---|
| Dane HANA | /hana/data/SID/mnt0000\<m> | Adres IP magazynu:/hana_data_SID_mnt00001_tenant_vol |
| Dziennik HANA | /hana/log/SID/mnt0000\<m> | Adres IP magazynu:/hana_log_SID_mnt00001_tenant_vol |
| Kopia zapasowa dziennika HANA | /hana/log/kopie zapasowe | Adres IP magazynu:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA udostępnił(a) | /hana/udostępniony/SID | Adres IP magazynu:/hana_shared_SID_mnt00001_tenant_vol/udostępniony |
| usr/sap | /usr/sap/SID | Adres IP magazynu:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*Identyfikator SID* jest identyfikatorem systemu wystąpienia HANA. 

*Dzierżawca* jest wewnętrznym wyliczeniem operacji podczas wdrażania dzierżawy.

HANA usr/sap mają ten sam wolumen. Nomenklatura punktów instalacji zawiera identyfikator systemu wystąpień HANA, a także numer instalacji. W wdrożeniach skalowania w górę istnieje tylko jeden uchwyt, takich jak mnt00001. W przypadku wdrożeń skalowanych w poziomie, z drugiej strony, widać tyle instalacji, ile masz węzłów roboczych i głównych. 

W środowiskach skalowanych w poziomie woluminy kopii zapasowych danych, dziennika i dziennika są współużytkowane i dołączane do każdego węzła w konfiguracji skalowano w poziomie. W przypadku konfiguracji, które są wieloma wystąpieniami SAP, tworzony jest inny zestaw woluminów i dołączany do jednostki dużych wystąpień HANA. Aby uzyskać szczegółowe informacje o układzie magazynu dla scenariusza, zobacz [Scenariusze obsługiwane przez HLI](hana-supported-scenario.md).

Gdy spojrzysz na jednostkę dużych wystąpień HANA, zdajesz sobie sprawę, że jednostki są wyposażone w wolumin dysku hojny dla HANA/data i że istnieje wolumin HANA/log/backup. Powodem, dla którego firma HANA/data jest tak duża, jest to, że migawki magazynu, które oferujemy jako klient, używają tego samego woluminu dysku. Im więcej migawek magazynu zostanie wykonaniu, tym więcej miejsca zajmują migawki w przypisanych woluminach magazynu. 

Hana/log/backup wolumin nie ma być woluminem do tworzenia kopii zapasowych bazy danych. Jego rozmiar ma być używany jako wolumin kopii zapasowej dla kopii zapasowych dziennika transakcji HANA. Aby uzyskać więcej informacji, zobacz [WYSOKIEJ DOSTĘPNOŚCI I ODZYSKIWANIA PO AWARII SAP HANA (Duże wystąpienia) na platformie Azure.](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Oprócz dostępnego magazynu można kupić dodatkową pojemność magazynu w odstępach co 1 TB. Ten dodatkowy magazyn można dodać jako nowe woluminy do wystąpienia dużych HANA.

Podczas dołączania z SAP HANA na platformie Azure `service management`klient określa identyfikator użytkownika (UID) i identyfikator grupy (GID) dla użytkownika sidadm i grupy sapsys (na przykład: 1000,500). Podczas instalacji systemu SAP HANA należy używać tych samych wartości. Ponieważ chcesz wdrożyć wiele wystąpień HANA na jednostce, otrzymasz wiele zestawów woluminów (jeden zestaw dla każdego wystąpienia). W rezultacie w czasie wdrażania należy zdefiniować:

- Identyfikator SID różnych wystąpień HANA (sidadm pochodzi od niego).
- Rozmiary pamięci różnych wystąpień HANA. Rozmiar pamięci na wystąpienie definiuje rozmiar woluminów w każdym zestawie woluminów.

Na podstawie zaleceń dostawcy magazynu dla wszystkich zainstalowanych woluminów skonfigurowano następujące opcje instalacji (z wyłączeniem jednostki LUN rozruchu):

- nfs rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Te punkty instalacji są skonfigurowane w /etc/fstab, jak pokazano na poniższej grafice:

![fstab zamontowanych woluminów w jednostce HANA Large Instance](./media/hana-installation/image1_fstab.PNG)

Dane wyjściowe polecenia df -h na jednostce dużego wystąpienia HANA S72m wygląda następująco:

![fstab zamontowanych woluminów w jednostce HANA Large Instance](./media/hana-installation/image2_df_output.PNG)


Kontroler magazynu i węzły w sygnaturach dużych wystąpień są synchronizowane z serwerami NTP. Podczas synchronizowania sap HANA na platformie Azure (duże wystąpienia) jednostek i maszyn wirtualnych platformy Azure z serwerem NTP, nie powinno być znaczący dryf czasu między infrastrukturą i jednostek obliczeniowych w azure lub dużych wystąpień sieka.

Aby zoptymalizować sap HANA do magazynu używanego pod spodem, ustaw następujące parametry konfiguracji SAP HANA:

- max_parallel_io_requests 128
- async_read_submit na
- async_write_submit_active na
- async_write_submit_blocks wszystkie
 
W przypadku wersji SAP HANA 1.0 do SPS12 parametry te można ustawić podczas instalacji bazy danych SAP HANA, zgodnie z opisem w [sap note #2267798 - Konfiguracja bazy danych SAP HANA](https://launchpad.support.sap.com/#/notes/2267798).

Parametry można również skonfigurować po instalacji bazy danych SAP HANA przy użyciu struktury hdbparam. 

Magazyn używany w dużych wystąpieniach HANA ma ograniczenie rozmiaru pliku. [Ograniczenie rozmiaru wynosi 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) na plik. W przeciwieństwie do ograniczeń rozmiaru pliku w systemach plików EXT3 HANA nie jest w sposób niejawny świadomy ograniczenia magazynu wymuszanego przez magazyn dużych wystąpień HANA. W rezultacie HANA nie utworzy automatycznie nowego pliku danych po osiągnięciu limitu rozmiaru pliku wynoszącego 16 TB. Jak HANA próbuje zwiększyć plik powyżej 16 TB, HANA będzie zgłaszać błędy i serwer indeksu upaść na końcu.

> [!IMPORTANT]
> Aby zapobiec próbom hana próbuje rozwijać pliki danych poza limit rozmiaru pliku 16 TB hana dużych wystąpień magazynu, należy ustawić następujące parametry w sap HANA global.ini pliku konfiguracyjnego
> 
> - datavolume_striping=prawda
> - datavolume_striping_size_gb = 15000
> - Zobacz też [#2400005](https://launchpad.support.sap.com/#/notes/2400005) notatki SAP
> - Należy pamiętać o [#2631285](https://launchpad.support.sap.com/#/notes/2631285) notatki SAP


W systemie SAP HANA 2.0 struktura hdbparam została przestarzała. W rezultacie parametry muszą być ustawione przy użyciu poleceń SQL. Aby uzyskać więcej informacji, zobacz [uwaga SAP #2399079: Eliminacja hdbparam w HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Zapoznaj się ze [scenariuszami obsługiwanymi przez HLI,](hana-supported-scenario.md) aby dowiedzieć się więcej o układzie magazynu dla twojej architektury.


**Następne kroki**

- Patrz [instalacja HANA na HLI](hana-example-installation.md)










































 







 




