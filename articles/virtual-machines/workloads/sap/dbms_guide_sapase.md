---
title: Wdrożenie systemu SAP ASE Azure DBMS dla obciążenia SAP | Dokumenty firmy Microsoft
description: Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP
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
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25d911869c95baba6ac9db3b893292e702e9c0e9
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273209"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP

W tym dokumencie obejmuje kilka różnych obszarów, które należy wziąć pod uwagę podczas wdrażania sap ASE w usłudze Azure IaaS. Jako warunek wstępny tego dokumentu należy przeczytać informacje o dokumencie [Zagadnienia dotyczące wdrażania dbms maszyn wirtualnych platformy Azure dla obciążenia SAP](dbms_guide_general.md) i innych przewodników w [obciążeniu SAP w dokumentacji platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) Ten dokument obejmuje SAP ASE działający w systemie Linux i windows operating systems. Minimalna obsługiwana wersja na platformie Azure to SAP ASE 16.0.02 (Release 16 Support Pack 2). Zaleca się wdrożenie najnowszej wersji systemu SAP i najnowszego poziomu poprawek.  Jako minimum SAP ASE 16.0.03.07 (Release 16 Support Pack 3 Patch Level 7) jest zalecane.  Najnowszą wersję programu SAP można znaleźć w [docelowym harmonogramie wersji PROGRAMU ASE 16.0 i informacji o liście CR](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information).

Dodatkowe informacje dotyczące obsługi wersji za pomocą aplikacji SAP lub lokalizacji nośników instalacyjnych znajdują się, poza tym w macierzy dostępności produktów SAP w następujących lokalizacjach:

- [Uwaga dotycząca obsługi sap #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [Notatka o obsłudze SAP #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [Notatka o obsłudze SAP #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [Notatka o obsłudze SAP #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Uwaga: W całej dokumentacji w świecie SAP i poza nim nazwa produktu jest określana jako Sybase ASE lub SAP ASE lub w niektórych przypadkach oba. Aby zachować spójność, używamy nazwy **SAP ASE** w tej dokumentacji.

## <a name="operating-system-support"></a>Obsługa systemów operacyjnych
Macierz dostępności produktów SAP zawiera obsługiwane kombinacje systemu operacyjnego i jądra SAP dla każdej aplikacji SAP.  Dystrybucje Linuksa SUSE 12.x, SUSE 15.x, Red Hat 7.x są w pełni obsługiwane.  Oracle Linux jako system operacyjny dla SAP ASE nie jest obsługiwany.  Zaleca się korzystanie z najnowszych dostępnych wersji Linuksa. Klienci systemu Windows powinni używać wersji systemu Windows Server 2016 lub Windows Server 2019.  Starsze wersje systemu Windows, takie jak Windows 2012, są technicznie obsługiwane, ale najnowsza wersja systemu Windows jest zawsze zalecana.


## <a name="specifics-to-sap-ase-on-windows"></a>Specyfika SAP ASE w systemie Windows
Począwszy od platformy Microsoft Azure, można migrować istniejące aplikacje SAP ASE do maszyn wirtualnych platformy Azure. SAP ASE na maszynie wirtualnej platformy Azure umożliwia zmniejszenie całkowitego kosztu posiadania wdrożenia, zarządzania i konserwacji aplikacji dla przedsiębiorstw, łatwo migrując te aplikacje na platformę Microsoft Azure. Dzięki rozwiązaniu SAP ASE na maszynie wirtualnej platformy Azure administratorzy i deweloperzy mogą nadal korzystać z tych samych narzędzi deweloperskich i administracyjnych, które są dostępne lokalnie.

Platforma Microsoft Azure oferuje wiele różnych typów maszyn wirtualnych, które umożliwiają uruchamianie najmniejszych systemów SAP i krajobrazów do dużych systemów SAP i krajobrazów z tysiącami użytkowników. Numery SAP o rozmiarze SAPS różnych jednostek SKU obsługujących maszyny wirtualne z certyfikatem SAP znajdują się w [#1928533.](https://launchpad.support.sap.com/#/notes/1928533)

Dokumentacja dotycząca instalacji SAP ASE w systemie Windows znajduje się w [Podręczniku instalacji SAP ASE dla systemu Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

Zablokuj strony w pamięci jest ustawieniem, które uniemożliwi bufor bazy danych SAP ASE stronicowania.  To ustawienie jest przydatne w przypadku dużych systemów z dużą ilością pamięci. Aby uzyskać więcej informacji, skontaktuj się z firmą BC-DB-SYB. 


## <a name="linux-operating-system-specific-settings"></a>Ustawienia specyficzne dla systemu operacyjnego Linux
Na maszynach wirtualnych `saptune` z systemem Linux, uruchamiane z profilem SAP-ASE Linux Huge Pages powinny być domyślnie włączone i mogą być weryfikowane za pomocą polecenia  

`cat /proc/meminfo` 

Rozmiar strony jest zazwyczaj 2048 KB. Szczegółowe informacje można znaleźć w artykule [Ogromne strony na Linuksie](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Zalecenia dotyczące maszyn wirtualnych i struktury dysków dla wdrożeń SAP ASE

SAP ASE for SAP NetWeaver Applications jest obsługiwany na dowolnym typie maszyny Wirtualnej wymienionym w [nocie pomocy technicznej SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533) typowe typy maszyn wirtualnych używane dla średnich serwerów baz danych SAP ASE obejmują Esv3.  Duże bazy danych z wieloma terabajtami mogą korzystać z typów maszyn wirtualnych z serii M. Wydajność zapisu dysku dziennika transakcji SAP ASE może zostać zwiększona, włączając akcelerator zapisu serii M. Akcelerator zapisu powinien być dokładnie przetestowany za pomocą SAP ASE ze względu na sposób, w jaki SAP ASE wykonuje zapisy dziennika.  Przejrzyj [notatkę techniczną SAP #2816580](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) i rozważ uruchomienie testu wydajności.  
Akcelerator zapisu jest przeznaczony tylko dla dysku dziennika transakcji. Pamięć podręczna na poziomie dysku powinna być ustawiona na BRAK. Nie zdziw się, jeśli akcelerator zapisu usługi Azure nie wykazuje podobnych ulepszeń, jak w przypadku innych usług dbms. Na podstawie sposobu, w jaki SAP ASE zapisuje w dzienniku transakcji, może być, że istnieje niewiele lub nie przyspieszenie przez akceleratora zapisu Azure.
Oddzielne dyski są zalecane dla urządzeń danych i urządzeń dziennika.  Systemowe bazy danych są `saptools` sybsecurity i nie wymagają dedykowanych dysków i mogą być umieszczane na dyskach zawierających dane z bazy danych SAP i urządzeń dziennika 

![Konfiguracja pamięci masowej dla SAP ASE](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Systemy plików, rozmiar paska & równoważenia we/wy 
SAP ASE zapisuje dane sekwencyjnie w urządzeniach magazynujących dyski, chyba że skonfigurowano inaczej. Oznacza to, że pusta baza danych SAP ASE z czterema urządzeniami będzie zapisywać dane tylko na pierwszym urządzeniu.  Inne urządzenia dyskowe będą zapisywane tylko wtedy, gdy pierwsze urządzenie jest zapełnione.  Ilość we/wy odczytu i zapisu do każdego urządzenia SAP ASE może być inna. Aby zrównoważyć we/wy dysku we wszystkich dostępnych dyskach platformy Azure, należy użyć miejsca do magazynowania systemu Windows lub linux LVM2. W systemie Linux zaleca się używanie systemu plików XFS do formatowania dysków. Rozmiar paska LVM powinien być testowany za pomocą testu wydajności. Rozmiar paska 128 KB jest dobrym punktem wyjścia. W systemie Windows należy przetestować rozmiar jednostki alokacji NTFS (AUS). 64 KB może służyć jako wartość początkowa. 

Zaleca się skonfigurowanie automatycznego rozszerzania bazy danych zgodnie z opisem w artykule [Konfigurowanie automatycznego rozszerzania przestrzeni bazy danych w](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) #1815695 pomocy technicznej SAP Adaptive Server Enterprise i [SAP.](https://launchpad.support.sap.com/#/notes/1815695) 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Przykładowe ustawienia SAP ASE na platformie Azure konfiguracji maszyn wirtualnych, dysków i systemów plików 
Poniższe szablony przedstawiają przykładowe konfiguracje zarówno dla systemu Linux, jak i Windows. Przed potwierdzeniem konfiguracji maszyny wirtualnej i dysku upewnij się, że przydziały przepustowości sieci i magazynu poszczególnych maszyn wirtualnych są wystarczające do spełnienia wymagań biznesowych. Należy również pamiętać, że różne typy maszyn wirtualnych platformy Azure mają różne maksymalne liczby dysków, które mogą być dołączone do maszyny Wirtualnej. Na przykład maszyna wirtualna E4s_v3 ma limit przepływności we/wy magazynu 48 MB/s. Jeśli przepływność magazynu wymagana przez działanie kopii zapasowej bazy danych wymaga więcej niż 48 MB/s, większy typ maszyny Wirtualnej z większą przepustowością magazynu jest nieunikniony. Podczas konfigurowania usługi Azure Storage należy również pamiętać, że szczególnie w [przypadku magazynu w usłudze Azure w ramach usługi Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance) zmienia się przepływność i usługi We/Wy na GB pojemności. Zobacz więcej na ten temat w artykule [Jakie typy dysków są dostępne na platformie Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types). Przydziały dla określonych typów maszyn wirtualnych platformy Azure są udokumentowane w artykule [Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci](https://docs.microsoft.com/azure/virtual-machines/sizes-memory) i artykuły połączone z nią. 

> [!NOTE]
>  Jeśli system DBMS jest przenoszony z lokalnego na platformę Azure, zaleca się wykonywanie monitorowania na maszynie Wirtualnej i oceny procesora CPU, pamięci, usług We/Wy i przepływności magazynu. Porównaj wartości szczytowe zaobserwowane z limitami przydziałów maszyn wirtualnych udokumentowanymi w artykułach wymienionych powyżej

Poniższe przykłady służą do celów ilustracyjnych i mogą być modyfikowane w zależności od indywidualnych potrzeb. Ze względu na projekt SAP ASE liczba urządzeń danych nie jest tak krytyczna, jak w przypadku innych baz danych. Liczba urządzeń danych wyszczególniona w tym dokumencie jest tylko przewodnikiem. 

Przykład konfiguracji dla małego serwera SAP ASE DB o rozmiarze bazy danych między 50 GB – 250 GB, takim jak SAP Solution Manager, może wyglądać

| Konfigurowanie | Windows | Linux | Komentarze |
| --- | --- | --- | --- |
| Typ maszyny Wirtualnej | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| Accelerated Networking | Włączanie | Włączanie | ---|
| Wersja SAP ASE | 16.0.03.07 lub wyższy | 16.0.03.07 lub wyższy | --- |
| Liczba urządzeń danych | 4 | 4 | ---|
| liczba urządzeń dziennika | 1 | 1 | --- |
| Liczba urządzeń tymczasowych | 1 | 1 | więcej dla obciążenia SAP BW |
| System operacyjny | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 lub RHEL 7.6 | --- |
| Agregacja dysku | Miejsca do magazynowania | LVM2 (lvm2) | --- |
| System plików | NTFS | Xfs |
| Formatowanie rozmiaru bloku | wymaga testowania obciążenia | wymaga testowania obciążenia | --- |
| # i typ dysków z danymi | Pamięć masowa w wersji premium: 2 x P10 (RAID0) | Pamięć masowa w wersji premium: 2 x P10 (RAID0)| Pamięć podręczna = Tylko do odczytu |
| # i typ dysków dziennika | Pamięć masowa w jakości Premium: 1 x P20  | Pamięć masowa w jakości Premium: 1 x P20 | Pamięć podręczna = BRAK |
| ASE MaxMemory parametr | 90% fizycznej pamięci RAM | 90% fizycznej pamięci RAM | przy założeniu pojedynczego wystąpienia |
| Liczba urządzeń do tworzenia kopii zapasowych | 4 | 4| --- |
| # i typ dysków kopii zapasowych | 1 | 1 | --- |


Przykład konfiguracji dla średniego serwera SAP ASE DB o rozmiarze bazy danych między 250 GB – 750 GB, takim jak mniejszy system SAP Business Suite, może wyglądać

| Konfigurowanie | Windows | Linux | Komentarze |
| --- | --- | --- | --- |
| Typ maszyny Wirtualnej | E16s_v3 (16 vCPU/128 GB RAM) | E16s_v3 (16 vCPU/128 GB RAM) | --- |
| Accelerated Networking | Włączanie | Włączanie | ---|
| Wersja SAP ASE | 16.0.03.07 lub wyższy | 16.0.03.07 lub wyższy | --- |
| Liczba urządzeń danych | 8 | 8 | ---|
| liczba urządzeń dziennika | 1 | 1 | --- |
| Liczba urządzeń tymczasowych | 1 | 1 | więcej dla obciążenia SAP BW |
| System operacyjny | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 lub RHEL 7.6 | --- |
| Agregacja dysku | Miejsca do magazynowania | LVM2 (lvm2) | --- |
| System plików | NTFS | Xfs |
| Formatowanie rozmiaru bloku | wymaga testowania obciążenia | wymaga testowania obciążenia | --- |
| # i typ dysków z danymi | Pamięć masowa w wersji premium: 4 x P20 (RAID0) | Pamięć masowa w wersji premium: 4 x P20 (RAID0)| Pamięć podręczna = Tylko do odczytu |
| # i typ dysków dziennika | Pamięć masowa w jakości Premium: 1 x P20  | Pamięć masowa w jakości Premium: 1 x P20 | Pamięć podręczna = BRAK |
| ASE MaxMemory parametr | 90% fizycznej pamięci RAM | 90% fizycznej pamięci RAM | przy założeniu pojedynczego wystąpienia |
| Liczba urządzeń do tworzenia kopii zapasowych | 4 | 4| --- |
| # i typ dysków kopii zapasowych | 1 | 1 | --- |

Przykład konfiguracji małego serwera SAP ASE DB o rozmiarze bazy danych między 750 GB – 2000 GB, takiej jak większy system SAP Business Suite, może wyglądać

| Konfigurowanie | Windows | Linux | Komentarze |
| --- | --- | --- | --- |
| Typ maszyny Wirtualnej | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| Accelerated Networking | Włączanie | Włączanie | ---|
| Wersja SAP ASE | 16.0.03.07 lub wyższy | 16.0.03.07 lub wyższy | --- |
| Liczba urządzeń danych | 16 | 16 | ---|
| liczba urządzeń dziennika | 1 | 1 | --- |
| Liczba urządzeń tymczasowych | 1 | 1 | więcej dla obciążenia SAP BW |
| System operacyjny | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 lub RHEL 7.6 | --- |
| Agregacja dysku | Miejsca do magazynowania | LVM2 (lvm2) | --- |
| System plików | NTFS | Xfs |
| Formatowanie rozmiaru bloku | wymaga testowania obciążenia | wymaga testowania obciążenia | --- |
| # i typ dysków z danymi | Pamięć masowa w wersji premium: 4 x P30 (RAID0) | Pamięć masowa w wersji premium: 4 x P30 (RAID0)| Pamięć podręczna = Tylko do odczytu |
| # i typ dysków dziennika | Pamięć masowa w jakości Premium: 1 x P20  | Pamięć masowa w jakości Premium: 1 x P20 | Pamięć podręczna = BRAK |
| ASE MaxMemory parametr | 90% fizycznej pamięci RAM | 90% fizycznej pamięci RAM | przy założeniu pojedynczego wystąpienia |
| Liczba urządzeń do tworzenia kopii zapasowych | 4 | 4| --- |
| # i typ dysków kopii zapasowych | 1 | 1 | --- |


Przykład konfiguracji małego serwera SAP ASE DB o rozmiarze bazy danych 2 TB+, takiej jak większy globalnie używany system SAP Business Suite, może wyglądać

| Konfigurowanie | Windows | Linux | Komentarze |
| --- | --- | --- | --- |
| Typ maszyny Wirtualnej | Seria M (1,0 do 4,0 TB pamięci RAM)  | Seria M (1,0 do 4,0 TB pamięci RAM) | --- |
| Accelerated Networking | Włączanie | Włączanie | ---|
| Wersja SAP ASE | 16.0.03.07 lub wyższy | 16.0.03.07 lub wyższy | --- |
| Liczba urządzeń danych | 32 | 32 | ---|
| liczba urządzeń dziennika | 1 | 1 | --- |
| Liczba urządzeń tymczasowych | 1 | 1 | więcej dla obciążenia SAP BW |
| System operacyjny | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 lub RHEL 7.6 | --- |
| Agregacja dysku | Miejsca do magazynowania | LVM2 (lvm2) | --- |
| System plików | NTFS | Xfs |
| Formatowanie rozmiaru bloku | wymaga testowania obciążenia | wymaga testowania obciążenia | --- |
| # i typ dysków z danymi | Pamięć masowa w wersji premium: 4 x P30 (RAID0) | Pamięć masowa w wersji premium: 4 x P30 (RAID0)| Pamięć podręczna = Tylko do odczytu, rozważ dysk Azure Ultra |
| # i typ dysków dziennika | Pamięć masowa w jakości Premium: 1 x P20  | Pamięć masowa w jakości Premium: 1 x P20 | Pamięć podręczna = BRAK, rozważ dysk Azure Ultra |
| ASE MaxMemory parametr | 90% fizycznej pamięci RAM | 90% fizycznej pamięci RAM | przy założeniu pojedynczego wystąpienia |
| Liczba urządzeń do tworzenia kopii zapasowych | 16 | 16 | --- |
| # i typ dysków kopii zapasowych | 4 | 4 | Korzystanie z LVM2/Storage Spaces |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Tworzenie kopii zapasowych & zagadnienia dotyczące przywracania systemu SAP ASE na platformie Azure
Zwiększenie liczby urządzeń do tworzenia kopii zapasowych i danych zwiększa wydajność tworzenia kopii zapasowych i przywracania. Zaleca się rozbierać dyski platformy Azure, które obsługują urządzenie kopii zapasowej SAP ASE, jak pokazano w tabelach pokazanych wcześniej. Należy zadbać o zrównoważenie liczby urządzeń i dysków kopii zapasowych i zapewnienie, że przepływność kopii zapasowej nie powinna przekraczać 40%-50% całkowitego przydziału przepływności maszyny Wirtualnej. Zaleca się używanie kompresji kopii zapasowej SAP jako domyślnej. Więcej szczegółów można znaleźć w artykułach:

- [Notatka o obsłudze SAP #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [Notatka o obsłudze SAP #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [Notatka o obsłudze SAP #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Nie używaj dysku D:\ lub /temp space jako miejsce docelowe bazy danych lub zrzutu dziennika.

### <a name="impact-of-database-compression"></a>Wpływ kompresji bazy danych
W konfiguracjach, w których przepustowość we/wy może stać się czynnikiem ograniczającym, środki, które zmniejszają liczbę we/wy mogą pomóc rozciągnąć obciążenie, które można uruchomić w scenariuszu usługi IaaS, takim jak platforma Azure. W związku z tym zaleca się, aby upewnić się, że kompresja SAP ASE jest używana przed przekazaniem istniejącej bazy danych SAP na platformę Azure.

Zalecenie zastosowania kompresji przed przekazaniem na platformę Azure jest podane z kilku powodów:

* Ilość danych do przesłania na platformę Azure jest niższa
* Czas trwania wykonywania kompresji jest krótszy przy założeniu, że można użyć silniejszego sprzętu z większą przepustowością we/wy lub większą przepustowością we/wy lub mniejszym opóźnieniem we/wy w środowisku lokalnym
* Mniejsze rozmiary baz danych mogą prowadzić do zmniejszenia kosztów alokacji dysku

Kompresja danych i lob działa na maszynie wirtualnej hostowane w maszynach wirtualnych platformy Azure, tak jak to ma miejsce w środowisku lokalnym. Aby uzyskać więcej informacji na temat sprawdzania, czy kompresja jest już używana w istniejącej bazie danych SAP ASE, sprawdź [notę techniczną SAP 1750510](https://launchpad.support.sap.com/#/notes/1750510). Aby uzyskać więcej informacji na temat kompresji bazy danych SAP ASE sprawdź [notatkę techniczną SAP #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Wysoka dostępność sap ASE na platformie Azure 
Przewodnik dla użytkowników HADR szczegółowo opisuje konfigurację i konfigurację rozwiązania SAP ASE "Always-on" 2 węzłów.  Ponadto obsługiwany jest również trzeci węzeł odzyskiwania po awarii. SAP ASE obsługuje wiele konfiguracji o wysokiej dostępności, w tym dysk udostępniony i natywne klastrowanie systemu operacyjnego (przestawny adres IP). Jedyną obsługiwana konfiguracją na platformie Azure jest używanie menedżera błędów bez przestawnego adresu IP.  Metoda pływającego adresu IP nie będzie działać na platformie Azure.  Jądro SAP jest aplikacją "HA Aware" i wie o podstawowych i pomocniczych serwerach SAP ASE. Nie ma żadnych ścisłych integracji między SAP ASE i Azure, wewnętrzny moduł równoważenia obciążenia platformy Azure nie jest używany. W związku z tym należy przestrzegać standardowej dokumentacji SAP ASE, począwszy od [przewodnika dla użytkowników SAP ASE HADR](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 

> [!NOTE]
> Jedyną obsługiwana konfiguracją na platformie Azure jest używanie menedżera błędów bez przestawnego adresu IP.  Metoda pływającego adresu IP nie będzie działać na platformie Azure. 

### <a name="third-node-for-disaster-recovery"></a>Trzeci węzeł do odzyskiwania po awarii
Poza używaniem sap ASE Always-On dla lokalnej wysokiej dostępności, można rozszerzyć konfigurację do węzła replikowanego asynchronicznie w innym regionie platformy Azure. Dokumentację takiego scenariusza można znaleźć [tutaj](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199).

## <a name="sap-ase-database-encryption--ssl"></a>Szyfrowanie bazy danych SAP ASE & SSL 
Menedżer aprowizacji oprogramowania SAP (SWPM) daje możliwość szyfrowania bazy danych podczas instalacji.  Jeśli chcesz używać szyfrowania, zaleca się użycie szyfrowania sap full database.  Zobacz szczegóły udokumentowane w:

- [Notatka o obsłudze SAP #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [Notatka o obsłudze SAP #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [Notatka o obsłudze SAP #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [Uwaga dotycząca obsługi sap #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Jeśli baza danych SAP ASE jest szyfrowana, kompresja zrzutu kopii zapasowych nie będzie działać. Zobacz też [uwaga dotycząca obsługi sap #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>SAP ASE na liście kontrolnej wdrażania platformy Azure
 
- Wdrażanie systemu SAP ASE 16.0.03.07 lub nowszego
- Aktualizacja do najnowszej wersji i poprawki FaultManager i SAPHostAgent
- Wdrażanie w najnowszych certyfikowanych systemach operacyjnych dostępnych, takich jak Windows 2019, Suse 15.1 lub Redhat 7.6 lub nowszych
- Używanie maszyn wirtualnych z certyfikatem SAP — zalecane są jednostki SKU maszyn wirtualnych platformy Azure o wysokiej pamięci, takie jak Es_v3 lub dla dużych układów VM serii X.
- Dopasuj do projektu dysku we/wy we/wy i całkowita agregacja przepływności maszyny Wirtualnej.  Wdrażanie wystarczającej liczby dysków
- Agreguj dyski przy użyciu miejsc do magazynowania systemu Windows lub Linux LVM2 z prawidłowym rozmiarem paska i systemem plików
- Tworzenie wystarczającej liczby urządzeń do celów danych, dzienników, temp i kopii zapasowych
- Rozważ użycie ultradisk dla systemów x-large 
- Uruchom `saptune` SAP-ASE na systemie operacyjnym Linux 
- Zabezpiecz bazę danych za pomocą szyfrowania DB — ręcznie przechowuj klucze w usłudze Azure Key Vault 
- Uzupełnij listę kontrolną [SAP na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) 
- Konfigurowanie kopii zapasowej dziennika i pełnej kopii zapasowej 
- Testowanie analizy odzyskiwania po awarii/odzyskiwania, tworzenie kopii zapasowych i przywracanie oraz wykonywanie testu woluminu & naprężeń 
- Upewnij się, że automatyczne rozszerzenie bazy danych działa 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Używanie DBACockpit do monitorowania wystąpień bazy danych
W przypadku systemów SAP, które używają SAP ASE jako platformy bazy danych, DBACockpit jest dostępny jako osadzone okna przeglądarki w transakcji DBACockpit lub jako Webdynpro. Jednak pełne funkcje monitorowania i administrowania bazy danych jest dostępna w implementacji Webdynpro DBACockpit tylko.

Podobnie jak w przypadku systemów lokalnych, wymagane jest kilka kroków, aby włączyć wszystkie funkcje SAP NetWeaver używane przez implementację Webdynpro DBACockpit. Postępuj zgodnie [z uwagą techniczną SAP #1245200,](https://launchpad.support.sap.com/#/notes/1245200) aby włączyć korzystanie z webdynpros i wygenerować wymagane. Postępując zgodnie z instrukcjami podanymi w powyższych uwagach, należy również skonfigurować Internet Communication Manager (`ICM`) wraz z portami, które mają być używane dla połączeń http i https. Ustawienie domyślne dla http wygląda następująco:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

a linki generowane w transakcji DBACockpit wygląda podobnie do:

> https:\//\<w pełni wykwalifikowananajnaz>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<w pełni wykwalifikowananajnaż>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

W zależności od tego, jak maszyna wirtualna platformy Azure obsługująca system SAP jest połączona z usługą AD i DNS, należy upewnić się, że ICM używa w pełni kwalifikowanej nazwy hosta, która może zostać rozwiązana na komputerze, z którego otwierasz DBACockpit. Zobacz [notatkę pomocy technicznej SAP #773830,](https://launchpad.support.sap.com/#/notes/773830) aby zrozumieć, jak ICM określa w pełni kwalifikowaną nazwę hosta na podstawie parametrów profilu i ustaw parametr icm/host_name_full jawnie, jeśli to konieczne.

Jeśli maszyna wirtualna została wdrożona w scenariuszu tylko do chmury bez łączności między lokalnymi a platformą Azure, `domainlabel`należy zdefiniować publiczny adres IP i plik . Format publicznej nazwy DNS maszyny wirtualnej wygląda następująco:

> `<custom domainlabel`>. `<azure region`>.cloudapp.azure.com
> 
> 

Więcej szczegółów związanych z nazwą DNS można znaleźć [tutaj][virtual-machines-azurerm-versus-azuresm].

Ustawienie parametru profilu SAP icm/host_name_full na nazwę DNS maszyny Wirtualnej platformy Azure, łącze może wyglądać podobnie do:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

W takim przypadku należy upewnić się, że:

* Dodawanie reguł ruchu przychodzącego do sieciowej grupy zabezpieczeń w portalu Azure dla portów TCP/IP używanych do komunikowania się z ICM
* Dodawanie reguł ruchu przychodzącego do konfiguracji Zapory systemu Windows dla portów TCP/IP używanych do komunikowania się z icm

W przypadku automatycznego importowania wszystkich dostępnych poprawek zaleca się okresowe stosowanie kolekcji korekt SAP Note mającej zastosowanie do wersji SAP:

* [Notatka o obsłudze SAP #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [Uwaga dotycząca obsługi sap #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [Notatka o obsłudze SAP #1882376](https://launchpad.support.sap.com/#/notes/1882376)

Więcej informacji na temat DBA Cockpit for SAP ASE można znaleźć w następujących uwagach SAP:

* [Notatka o obsłudze SAP #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [Notatka o obsłudze SAP #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [Uwaga dotycząca obsługi sap #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [Uwaga dotycząca obsługi sap #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [Notatka o obsłudze SAP #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [Notatka o obsłudze SAP #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [Notatka o obsłudze SAP #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [Notatka o obsłudze SAP #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Przydatne linki, notatki & oficjalnych dla SAP ASE
Strona początkowa [sap ASE 16.0.03.07 Dokumentacja](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) zawiera linki do różnych dokumentów, z których dokumenty:

- SAP ASE Learning Journey - Monitoring & administracji
- SAP ASE Learning Journey - Aktualizacja & instalacji

są pomocne. Innym przydatnym dokumentem są [aplikacje SAP dotyczące najlepszych rozwiązań SAP Adaptive Server Enterprise Dla migracji i środowiska wykonawczego.](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf)

Inne pomocne uwagi dotyczące pomocy technicznej SAP to:

- [Uwaga dotycząca obsługi sap #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [Notatka o obsłudze SAP #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [Notatka o obsłudze SAP #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [Notatka o obsłudze SAP #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [Notatka o obsłudze SAP #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [Notatka o obsłudze SAP #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [Notatka o obsłudze SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Notatka o obsłudze SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [Notatka o obsłudze SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [Notatka o obsłudze SAP #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [Notatka o obsłudze SAP #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [Notatka o obsłudze SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Notatka o obsłudze SAP #1588316](https://launchpad.support.sap.com/#/notes/158831) 

Inne informacje są publikowane na 

- [Aplikacje SAP na sap adaptive server enterprise](https://community.sap.com/topics/applications-on-ase)
- [Centrum informacyjne SAP ASE](http://infocenter.sybase.com/help/index.jsp) 
- [SAP ASE Zawsze włączony z 3 konfiguracją węzła DR](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

Miesięczny biuletyn jest publikowany za pośrednictwem [notatki technicznej SAP #2381575](https://launchpad.support.sap.com/#/notes/2381575) 


## <a name="next-steps"></a>Następne kroki
Sprawdź artykuł [Obciążenia SAP na platformie Azure: lista kontrolna planowania i wdrażania](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)

