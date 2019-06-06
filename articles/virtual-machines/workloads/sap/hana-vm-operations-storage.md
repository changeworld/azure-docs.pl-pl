---
title: Konfiguracje magazynu maszyny wirtualnej SAP HANA Azure | Dokumentacja firmy Microsoft
description: Zalecenia dotyczące magazynu dla maszyny Wirtualnej, które mają SAP HANA, wdrożyć w nich.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d062b6fff9693d5bda75edd65b8fe88d834eff57
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735514"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Konfiguracje magazynu maszyny wirtualnej SAP HANA Azure

System Azure oferuje różnych typów pamięci masowej, które są odpowiednie na potrzeby maszyn wirtualnych platformy Azure, które są uruchomione oprogramowanie SAP HANA. Typy usługi Azure storage, które mogą zostać uwzględnione w listy wdrożeń SAP HANA, takich jak: 

- Standardowe dyski SSD (SSD)
- Premium dyski półprzewodnikowe (SSD)
- Największa dysków SSD w publicznej wersji zapoznawczej i nie jest jeszcze obsługiwany z aplikacjami SAP w środowisku produkcyjnym

Aby dowiedzieć się więcej na temat tych typów dysków, zobacz artykuł [wybierz typ dysku](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Platforma Azure oferuje dwie metody wdrażania dla wirtualnych dysków twardych na Azure Standard i Premium Storage. Pozwala na ogólną scenariusz, korzystać z zalet [dysk zarządzany platformy Azure](https://azure.microsoft.com/services/managed-disks/) wdrożeń. 

Aby uzyskać listę typów magazynów i umowach SLA, przepustowość operacji We/Wy i pamięci masowej, przejrzyj [dokumentacji platformy Azure dla dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/).

**Zalecenie: Za pomocą usługi Azure Premium Storage w połączeniu akcelerator zapisu platformy Azure i używać usługi Azure Managed Disks dla wdrożenia**

W świecie lokalnych rzadko musiał interesujące podsystemów we/wy i jego możliwości. Przyczyna był dostawcą urządzenia konieczne upewnić się, czy spełnienia wymagań minimalny magazyn dla oprogramowania SAP HANA. Podczas tworzenia infrastruktury platformy Azure, samodzielnie, należy pamiętać o niektórych z tych wymagań. Niektóre właściwości minimalna przepływność, które zostaną poproszeni o powodujące konieczność:

- Włącz odczytu/zapisu na **/hana/log** 250 MB/s o rozmiarach operacji We/Wy 1 MB
- Włącz odczyt działania co najmniej 400 MB/s dla **/hana/danych** dla 16 MB i 64 MB rozmiar operacji We/Wy
- Włącz działanie zapisu co najmniej 250 MB/s dla **/hana/danych** z 16 MB i 64 MB rozmiar operacji We/Wy

Biorąc pod uwagę, że magazyn niski czas oczekiwania jest krytyczny dla systemów DBMS, nawet przy wzroście DBMS, takich jak SAP HANA, przechowywać dane w pamięci. Ścieżki krytycznej w magazynie jest zazwyczaj wokół zapisu dziennika transakcji systemy DBMS. Można jednak również operacje, takie jak zapisywanie punktów zapisu lub ładowania danych w pamięci, po odzyskiwanie po awarii może być niezbędna. Dlatego jest **obowiązkowe** korzystać z dysków magazynu Premium platformy Azure dla **/hana/danych** i **/hana/log** woluminów. W celu osiągnięcia minimalnej przepustowości **/hana/log** i **/hana/danych** zgodnie z potrzebami, SAP, potrzebne do utworzenia RAID 0 za pomocą MDADM lub LVM przez wiele dysków Azure Premium Storage. I korzystać z woluminów RAID jako **/hana/danych** i **/hana/log** woluminów. 

**Zalecenie: Zgodnie z usługi stripe rozmiary RAID 0 zalecane jest użycie:**

- 64 KB lub 128 KB   **/hana/danych**
- 32 KB   **/hana/log**

> [!NOTE]
> Nie ma potrzeby konfigurowania żadnych poziom nadmiarowości przy użyciu woluminów RAID, ponieważ magazynu Azure Premium i standardowa należy zachować trzy obrazy wirtualnego dysku twardego. Użycie woluminu RAID jest wyłącznie Konfigurowanie woluminów, które zapewniają wystarczającą przepływnością wejścia/wyjścia.

Grupowania liczba wirtualnych dysków twardych platformy Azure, poniżej RAID, nie jest kumulacyjne ze strony przepustowość operacji We/Wy i magazynu. Tak Jeżeli umieścisz RAID 0 za pośrednictwem 3 dyski P30 usługi Azure Premium Storage x go powinien zapewnić trzy razy na SEKUNDĘ i trzy razy przepływność magazynu jednego dysku usługi Azure Premium Storage P30.

Zalecenia dotyczące buforowania poniżej są przy założeniu charakterystyk we/wy platformy SAP Hana tej listy, takich jak:

- Istnieje bardzo trudno dowolnego obciążenia odczytu dla plików danych HANA. Wyjątki są duży rozmiar operacji We/Wy, po ponownym uruchomieniu wystąpienie oprogramowania HANA, lub gdy dane są ładowane do platformy HANA. Inną wielkość liter w większych odczytu operacji We/Wy na plikach danych może być kopie zapasowe bazy danych HANA. W rezultacie odczytu przede wszystkim buforowania nie mieć sensu, ponieważ w większości przypadków, wszystkie woluminy plików danych należy całkowicie odczytane.
- Zapisywanie względem plików danych mają bogate doświadczenie w wzrosty na podstawie punktów zapisu HANA i odzyskiwanie po awarii platformy HANA. Zapisywanie punktów zapisu jest asynchroniczny i nie wstrzymują wszystkich transakcji użytkownika. Zapisywania danych podczas odzyskiwania awaryjnego jest wydajność krytycznych, aby uruchomić system odpowiada szybko ponownie. Odzyskiwanie po awarii należy jednak sytuacjach wyjątkowych zamiast
- Istnieje bardzo trudno wszelkie odczyty z plików ponawiania platformy HANA. Wyjątki są duże operacji We/Wy podczas wykonywania kopii zapasowej z dziennika transakcji, odzyskiwanie po awarii, lub w fazie ponownego uruchamiania wystąpienie oprogramowania HANA.  
- Obciążenie głównego pliku dziennika ponawiania platformy SAP HANA jest zapisy. Zależy od rodzaju obciążenia, możesz mieć operacji We/Wy tak małej, jako 4 KB lub w innych przypadkach rozmiarów operacji We/Wy 1 MB lub większej. Zapisu opóźnienie przed dziennika ponawiania platformy SAP HANA jest wydajność krytycznych.
- Wszystkie operacje zapisu muszą zostać utrwalony na dysku w sposób niezawodny

**Zalecenie: W wyniku tych obserwowanych wzorce operacji We/Wy przez platformę SAP HANA buforowanie na różnych woluminach za pomocą usługi Azure Premium Storage należy ustawić takich jak:**

- **dane/hana/** — Brak buforowania
- **/ hana/log** — Brak buforowania - wyjątek dla serii M (zobacz w dalszej części tego dokumentu)
- **/ hana/udostępnione** — odczytu pamięci podręcznej


Również pamiętać ogólną przepustowość we/wy maszyny Wirtualnej podczas zmiany rozmiaru lub podejmowania decyzji dla maszyny Wirtualnej. Ogólną przepływność magazynu maszyny Wirtualnej jest udokumentowany w artykule [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem pamięci](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Tryb harmonogram We/Wy systemu Linux
Linux ma kilku różnych trybach planowania operacji We/Wy. Ogólne zalecenie za pośrednictwem dostawcy systemu Linux i oprogramowania SAP jest ponownie skonfigurować tryb harmonogram we/wy dla woluminów dysku z **cfq** tryb **aktualizujący nie działa** trybu. Szczegółowe informacje są wywoływane w [1984798 # Uwaga SAP](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Rozwiązanie do magazynowania w środowisku produkcyjnym z akceleratorem zapisu platformy Azure dla maszyn wirtualnych serii M platformy Azure
Akcelerator zapisu platformy Azure jest wprowadzenie wdrażania maszyn wirtualnych platformy Azure serii M wyłącznie funkcjonalność. Jak nazwy stanów, funkcji ma na celu poprawę czas oczekiwania operacji We/Wy zapisu względem usługi Azure Premium Storage. For SAP HANA i akcelerator zapisu powinien zostać użyty dla **/hana/log** tylko woluminów. W związku z tym **/hana/danych** i **/hana/log** to osobne woluminy z pomocniczymi akcelerator zapisu platformy Azure **/hana/log** tylko woluminów. 

> [!IMPORTANT]
> Certyfikacji platformy SAP HANA dla maszyn wirtualnych serii M platformy Azure są oparte wyłącznie na programie akcelerator zapisu platformy Azure dla **/hana/log** woluminu. W rezultacie produkcji scenariuszy SAP HANA wdrożenia na maszynach wirtualnych serii M Azure oczekuje się, można skonfigurować za pomocą akcelerator zapisu platformy Azure dla **/hana/log** woluminu.  

> [!NOTE]
> Na potrzeby scenariuszy produkcyjnych, sprawdź, czy typ maszyny Wirtualnej jest obsługiwana w przypadku oprogramowania SAP HANA SAP w [dokumentacji SAP IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Zalecenie: Zalecane konfiguracje na potrzeby scenariuszy produkcyjnych wyglądać następująco:**

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z VM I/O<br /> Przepływność | /hana/data | / hana/log | / hana/udostępnione | wolumin/root | /usr/sap | Hana/kopia zapasowa |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Typy maszyn wirtualnych M416xx_v2 nie jeszcze udostępniane przez firmę Microsoft publicznie. Sprawdź, czy przepływności różnych woluminach sugerowane spełnia obciążenia, które chcesz uruchomić. Jeśli obciążenie wymaga wyższej woluminów na potrzeby **/hana/danych** i **/hana/log**, należy zwiększyć liczbę Azure Premium Storage wirtualnych dysków twardych. Zmiany rozmiaru woluminu z kolejnych wirtualnych dysków twardych niż wymienione zwiększa operacje We/Wy i przepływność operacji We/Wy w ramach typu maszyny wirtualnej platformy Azure.

Akcelerator zapisu platformy Azure działa tylko w połączeniu z [usługi Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Dlatego co najmniej usługi Azure Premium Storage dyski tworzących **/hana/log** woluminów, które muszą zostać wdrożone jako dysków zarządzanych.

Istnieją limity usługi Azure Premium Storage wirtualne dyski twarde dla maszyny Wirtualnej, które mogą być obsługiwane przez akcelerator zapisu platformy Azure. Bieżące limity są:

- 16 dysków VHD dla maszyny Wirtualnej M128xx i M416xx
- 8 dysków VHD dla maszyny Wirtualnej M64xx i M208xx
- 4 wirtualne dyski twarde dla M32xx maszyny Wirtualnej

Bardziej szczegółowe instrukcje na temat włączania akcelerator zapisu platformy Azure można znaleźć w artykule [akceleratorem zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Szczegółowe informacje i ograniczenia dotyczące akcelerator zapisu platformy Azure można znaleźć w dokumentacji tych samych.

**Zalecenie: Należy użyć akceleratorem zapisu dla dysków tworzących woluminu /hana/log**


## <a name="cost-conscious-azure-storage-configuration"></a>Koszt świadome konfigurację usługi Azure Storage
W poniższej tabeli przedstawiono konfigurację typy maszyn wirtualnych, których klienci także używają do hosta SAP HANA na maszynach wirtualnych platformy Azure. Być może niektóre typy maszyn wirtualnych, które nie spełnia wszystkie kryteria minimalny magazyn dla oprogramowania SAP HANA lub oficjalnie nie są obsługiwane z platformą SAP HANA, SAP. Ale do tej pory sprawiał tych maszyn wirtualnych do wykonania prawidłowo dla scenariuszy nieprodukcyjnych. **/Hana/danych** i **/hana/log** są łączone w celu jeden wolumin. W wyniku użycia akcelerator zapisu platformy Azure może stać się ograniczenie pod kątem operacji We/Wy.

> [!NOTE]
> W przypadku scenariuszy SAP obsługiwane Sprawdź, czy określonego typu maszyny Wirtualnej jest obsługiwana dla oprogramowania SAP HANA, SAP w [dokumentacji SAP IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Zmiana z poprzedniej wersji portalu zalecenia dotyczące kosztów rozwiązania świadome, to można przenieść z [dysków standardowych dysków Twardych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) do wykonywania lepszą i bardziej niezawodny [dysków SSD w warstwie standardowa usługi Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z VM I/O<br /> Przepływność | / hana/danych i dziennika/hana /<br /> Rozłożona z LVM lub MDADM | / hana/udostępnione | wolumin/root | /usr/sap | Hana/kopia zapasowa |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Typy maszyn wirtualnych M416xx_v2 nie jeszcze udostępniane przez firmę Microsoft publicznie. Dyski zalecane dla mniejszych maszyny Wirtualnej typów z 3 x P20 oversize woluminy dotyczące zalecenia miejsce zgodnie z opisem w [SAP TDI magazynu oficjalny dokument dotyczący](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Jednak wyboru wyświetlane w tabeli została wprowadzona w celu zapewnienia wystarczającej przepływności dysku dla oprogramowania SAP HANA. Jeśli potrzebujesz zmiany **/hana/kopia zapasowa** wolumin, który został o rozmiarze do przechowywania kopii zapasowych, które reprezentują dwa razy woluminie pamięci, możesz ją dostosować.   
Sprawdź, czy przepływności różnych woluminach sugerowane spełnia obciążenia, które chcesz uruchomić. Jeśli obciążenie wymaga wyższej woluminów na potrzeby **/hana/danych** i **/hana/log**, należy zwiększyć liczbę Azure Premium Storage wirtualnych dysków twardych. Zmiany rozmiaru woluminu z kolejnych wirtualnych dysków twardych niż wymienione zwiększa operacje We/Wy i przepływność operacji We/Wy w ramach typu maszyny wirtualnej platformy Azure. 

> [!NOTE]
> Powyższej konfiguracji nie będą korzystać z [maszyny wirtualnej platformy Azure pojedynczy umowę SLA maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) ponieważ ona korzystać z usługi Azure Premium Storage i Azure Standard Storage. Jednak zaznaczenie została wybrana w celu optymalizacji kosztów. Należy wybrać usługi Premium Storage dla wszystkich dysków powyżej, które są wyświetlane jako usługi Azure Standard Storage (Sxx) do zapewnienia zgodności z SLA pojedynczej maszyny Wirtualnej platformy Azure do konfiguracji maszyny Wirtualnej.


> [!NOTE]
> Zalecenia dotyczące konfiguracji dysku, które zostały podane są przeznaczone dla minimalne wymagania, którą SAP wyraża kierunku ich dostawców infrastruktury. Rzeczywiste wdrożeń i scenariusze obciążeń sytuacjach napotkano, których te zalecenia pozwalają nadal nie dostarczył wystarczające możliwości. To może się zdarzyć, gdy klient wymagane szybciej ponowne załadowanie danych po ponownym uruchomieniu HANA lub gdzie kopii zapasowej konfiguracji wymaganych większą przepustowość do magazynu. Czasami uwzględnione **/hana/log** gdzie 5000 operacji We/Wy nie jest wystarczająca dla określonego obciążenia. Dlatego take te zalecenia jako początkowy punkt i dostosować na podstawie wymagań obciążenia.
>  

## <a name="azure-ultra-ssd-storage-configuration-for-sap-hana"></a>Azure konfiguracji magazynu Ultra SSD platformy SAP Hana
Microsoft jest właśnie wprowadzenie nowego typu magazynu platformy Azure o nazwie [Azure Ultra SSD](https://azure.microsoft.com/updates/ultra-ssd-new-azure-managed-disks-offering-for-your-most-latency-sensitive-workloads/). Istotną różnicą między magazynu platformy Azure, oferowane do tej pory i Ultra SSD to, że możliwości dysku nie są powiązane z rozmiar dysku już. Jako klient korzystający z tych funkcji można zdefiniować Ultra dysku SSD:

- Rozmiar dysku z zakresu od 4 GiB do 65 536 GiB
- Operacje We/Wy z zakresu od 100 operacji We/Wy do 160 KB na SEKUNDĘ (maksymalna zależy również typy maszyn wirtualnych)
- Przepływność magazynu od 300 MB/s do 2000 MB na sekundę

Aby uzyskać szczegółowe informacje wyszukiwanie artykułu [ogłoszenie Ultra dysków SSD — nowej generacji technologii Azure Disks (wersja zapoznawcza)](https://azure.microsoft.com/blog/announcing-ultra-ssd-the-next-generation-of-azure-disks-technology-preview/)

Opcją UltraSSD daje możliwość definiowania jednego dysku, który spełnia Twoje rozmiar, operacje We/Wy i zakres przepływność dysku. Zamiast używania menedżerów woluminów logicznych, takich jak LVM lub MDADM na podstawie usługi Azure Premium Storage, aby utworzyć woluminy, które spełniają wymagania dotyczące przepływności operacji We/Wy i magazynu. Mieszane konfiguracji można uruchomić między opcją UltraSSD i Magazyn w warstwie Premium. W rezultacie można ograniczyć użycie opcją UltraSSD /hana/data krytycznych dla wydajności i woluminów /hana/log i obejmuje inne woluminy z usługą Azure Premium Storage

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z VM I/O<br /> Przepływność | ilość danych/hana / | przepływność danych we/wy/hana / | /hana/data IOPS | wolumin dziennika/hana / | / hana/przepustowość operacji We/Wy dziennika | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 500 MB/s | 7500 | 256 GB | 500 MB/s  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 500 MB/s | 7500 | 256 GB | 500 MB/s  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 500 MB/s | 7500 | 512 GB | 500 MB/s  | 2000 |
| M64s | 1000 GiB | 1000 MB/s |  1200 GB | 500 MB/s | 7500 | 512 GB | 500 MB/s  | 2000 |
| M64ms | 1750 GiB | 1000 MB/s | 2100 GB | 500 MB/s | 7500 | 512 GB | 500 MB/s  | 2000 |
| M128s | 2000 GiB | 2000 MB/s |2400 GB | 1200 MB/s |9000 | 512 GB | 800 MB/s  | 2000 | 
| M128ms | 3800 GiB | 2000 MB/s | 4800 GB | 1200 MB/s |9000 | 512 GB | 800 MB/s  | 2000 | 
| M208s_v2 | 2850 GiB | 1000 MB/s | 3500 GB | 1000 MB/s | 9000 | 512 GB | 500 MB/s  | 2000 | 
| M208ms_v2 | 5700 GiB | 1000 MB/s | 7200 GB | 1000 MB/s | 9000 | 512 GB | 500 MB/s  | 2000 | 
| M416s_v2 | 5700 GiB | 2000 MB/s | 7200 GB | 1500 M b/s | 9000 | 512 GB | 800 MB/s  | 2000 | 
| M416ms_v2 | 11400 GiB | 2000 MB/s | 14400 GB | 1500 MB/s | 9000 | 512 GB | 800 MB/s  | 2000 |   

Typy maszyn wirtualnych M416xx_v2 nie jeszcze udostępniane przez firmę Microsoft publicznie. Wartości wymienione są przeznaczone do jako punkt początkowy i muszą zostać obliczone dla rzeczywistego zapotrzebowania. Korzystać z dysków SSD Ultra Azure jest, że wartości dla operacji We/Wy i przepływność, można dostosować bez konieczności zamykania maszyny Wirtualnej lub zatrzymaniu obciążenie stosowane do systemu.   

> [!NOTE]
> Do tej pory migawek magazynu z magazynem opcją UltraSSD nie jest dostępna. Uniemożliwia to użycie migawki maszyny Wirtualnej za pomocą usługi Kopia zapasowa Azure

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz:

- [Przewodnik SAP HANA wysoką dostępność dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).