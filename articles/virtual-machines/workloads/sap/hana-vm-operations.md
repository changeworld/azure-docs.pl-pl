---
title: Konfiguracje infrastruktury SAP HANA i operacji na platformie Azure | Dokumentacja firmy Microsoft
description: Przewodnik obsługi programu systemami SAP HANA, które są wdrażane na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/20/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 39f250a6c989e5208eda071ed543a1045d65580b
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952668"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure
Ten dokument zawiera wskazówki dotyczące konfigurowania infrastruktury platformy Azure i obsługi systemów SAP HANA, które zostały wdrożone na natywnych maszynach wirtualnych platformy Azure (maszyny wirtualne). Dokument zawiera również informacje o konfiguracji dla oprogramowania SAP HANA skalowalnego w poziomie dla jednostki SKU maszyny Wirtualnej M128s. Ten dokument nie jest przeznaczona do zastąpienia dokumentacji SAP standardowa obejmuje następującą zawartością:

- [Przewodnik administrowania SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Przewodników po instalacji SAP](https://service.sap.com/instguides)
- [Uwagi SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć tego przewodnika, potrzebujesz podstawową wiedzę na temat następujących składników platformy Azure:

- [Maszyny wirtualne platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Sieci platformy Azure i sieciami wirtualnymi](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Aby dowiedzieć się więcej na temat oprogramowania SAP NetWeaver i inne składniki SAP na platformie Azure, zobacz [SAP na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) części [dokumentacji platformy Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Zagadnienia dotyczące konfiguracja podstawowa
Konfiguracja podstawowa zagadnienia dotyczące wdrażania systemów SAP HANA na maszynach wirtualnych platformy Azure można znaleźć w poniższych sekcjach.

### <a name="connect-into-azure-virtual-machines"></a>Połączenie z maszynami wirtualnymi platformy Azure
Zgodnie z opisem w [maszyn wirtualnych platformy Azure Podręcznik planowania](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), istnieją dwie podstawowe metody łączenia do maszyn wirtualnych platformy Azure:

- Łączenie się za pośrednictwem Internetu oraz publiczne punkty końcowe na maszynie Wirtualnej szybkiego dostępu lub na maszynie Wirtualnej z systemem SAP HANA.
- Łączenie się za pośrednictwem [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) lub na platformie Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Połączeń lokacja lokacja za pomocą sieci VPN lub usługi ExpressRoute jest wymagane na potrzeby scenariuszy produkcyjnych. Ten typ połączenia jest również wymagany dla scenariuszy nieprodukcyjnych, które źródła danych do scenariuszy produkcyjnych, w którym jest używane oprogramowanie SAP. Na poniższej ilustracji przedstawiono przykład korzystać z łączności między lokacjami:

![Łączność między lokacjami](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Wybierz typy maszyn wirtualnych platformy Azure
Typy maszyn wirtualnych platformy Azure, których można użyć na potrzeby scenariuszy produkcyjnych są wymienione w [dokumentacji SAP IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). W przypadku scenariuszy nieprodukcyjnych szerszy zakres natywnych typów maszyn wirtualnych platformy Azure jest dostępna.

>[!NOTE]
> Dla scenariuszy nieprodukcyjnych, użyj typy maszyn wirtualnych, które są wymienione w [Uwaga SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Użycie maszyn wirtualnych platformy Azure na potrzeby scenariuszy produkcyjnych, sprawdź, czy maszyny wirtualne w systemie SAP opublikowane z certyfikatem SAP HANA [listy certyfikowane platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Wdrażanie maszyn wirtualnych na platformie Azure przy użyciu:

- Witryna Azure portal.
- Polecenia cmdlet programu PowerShell platformy Azure.
- Interfejs wiersza polecenia platformy Azure.

Możesz również wdrożyć pełną zainstalowanej platformy SAP HANA w usługach maszyny Wirtualnej platformy Azure za pośrednictwem [platforma usług w chmurze SAP](https://cal.sap.com/). Proces instalacji został opisany w [wdrażanie rozwiązania SAP S/4HANA lub BW/4HANA na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) lub za pomocą usługi automation wydane [tutaj](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Wybierz typ usługi Azure Storage
Platforma Azure udostępnia dwa typy magazynów, które są odpowiednie na potrzeby maszyn wirtualnych platformy Azure, które są uruchomione oprogramowanie SAP HANA: Standardowych dysków twardych (HDD) i premium dyski półprzewodnikowe (SSD). Aby dowiedzieć się więcej na temat tych typów dysków, zobacz nasze artykuł [wybierz typ dysku](../../windows/disks-types.md)

- Standardowych dysków twardych (HDD)
- Premium dyski półprzewodnikowe (SSD)

Platforma Azure oferuje dwie metody wdrażania dla wirtualnych dysków twardych na Azure Standard i Premium Storage. Pozwala na ogólną scenariusz, korzystać z zalet [dysk zarządzany platformy Azure](https://azure.microsoft.com/services/managed-disks/) wdrożeń.

Aby uzyskać listę typów magazynów i umowach SLA, przepustowość operacji We/Wy i pamięci masowej, przejrzyj [dokumentacji platformy Azure dla dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Konfigurowanie magazynu dla maszyn wirtualnych platformy Azure

Do tej pory nigdy nie było konieczne interesujące podsystemów we/wy i jego możliwości. Przyczyna był dostawcą urządzenia konieczne upewnić się, czy spełnienia wymagań minimalny magazyn dla oprogramowania SAP HANA. Podczas tworzenia infrastruktury platformy Azure, samodzielnie, należy również mieć świadomość niektórych z tych wymagań. A także zrozumienie wymagań dotyczących konfiguracji, sugerowane w poniższych sekcjach. Lub w sytuacjach, gdy konfigurujesz maszyny wirtualne systemem SAP HANA. Niektóre właściwości, które zostaną poproszeni o powodujące konieczność:

- Włącz woluminu odczytu/zapisu na **/hana/log** 250 MB/s co najmniej o rozmiarze operacji We/Wy 1 MB
- Włącz odczyt działania co najmniej 400 MB/s dla **/hana/danych** dla 16 MB i 64 MB rozmiar operacji We/Wy
- Włącz działanie zapisu co najmniej 250 MB/s dla **/hana/danych** z 16 MB i 64 MB rozmiar operacji We/Wy

Biorąc pod uwagę, że magazyn niski czas oczekiwania jest krytyczny dla systemów DBMS, nawet przy wzroście DBMS, takich jak SAP HANA, przechowywać dane w pamięci. Ścieżki krytycznej w magazynie jest zazwyczaj wokół zapisu dziennika transakcji systemy DBMS. Można jednak również operacje, takie jak zapisywanie punktów zapisu lub ładowania danych w pamięci, po odzyskiwanie po awarii może być niezbędna. Dlatego jest wymagane, aby korzystać z dysków magazynu Premium platformy Azure dla **/hana/danych** i **/hana/log** woluminów. W celu osiągnięcia minimalnej przepustowości **/hana/log** i **/hana/danych** zgodnie z potrzebami, SAP, potrzebne do utworzenia RAID 0 za pomocą MDADM lub LVM przez wiele dysków Azure Premium Storage. I korzystać z woluminów RAID jako **/hana/danych** i **/hana/log** woluminów. Zgodnie z usługi stripe rozmiary RAID 0 zalecane jest użycie:

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

W wyniku tych obserwowanych wzorce operacji We/Wy przez platformę SAP HANA buforowanie na różnych woluminach za pomocą usługi Azure Premium Storage należy ustawić takich jak:

- **dane/hana/** — Brak buforowania
- **/ hana/log** — Brak buforowania - wyjątek dla serii M (zobacz w dalszej części tego dokumentu)
- **/ hana/udostępnione** — odczytu pamięci podręcznej


Również pamiętać ogólną przepustowość we/wy maszyny Wirtualnej podczas zmiany rozmiaru lub podejmowania decyzji dla maszyny Wirtualnej. Ogólną przepływność magazynu maszyny Wirtualnej jest udokumentowany w artykule [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem pamięci](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="linux-io-scheduler-mode"></a>Tryb harmonogram We/Wy systemu Linux
Linux ma kilku różnych trybach planowania operacji We/Wy. Ogólne zalecenie za pośrednictwem dostawcy systemu Linux i oprogramowania SAP jest ustawiony tryb harmonogram we/wy dla woluminów dysku z **cfq** tryb **aktualizujący nie działa** trybu. Szczegółowe informacje są wywoływane w [1984798 # Uwaga SAP](https://launchpad.support.sap.com/#/notes/1984787). 


#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Rozwiązanie do magazynowania z akceleratorem zapisu platformy Azure dla maszyn wirtualnych serii M platformy Azure
Akcelerator zapisu platformy Azure jest wprowadzenie wdrażania maszyn wirtualnych platformy Azure serii M wyłącznie funkcjonalność. Jak nazwy stanów, funkcji ma na celu poprawę czas oczekiwania operacji We/Wy zapisu względem usługi Azure Premium Storage. For SAP HANA i akcelerator zapisu powinien zostać użyty dla **/hana/log** tylko woluminów. W związku z tym konfiguracji przedstawionych do tej pory muszą zostać zmienione. Zmiana głównego jest rozpad między **/hana/danych** i **/hana/log** aby można było używać akcelerator zapisu platformy Azure względem **/hana/log** tylko woluminów. 

> [!IMPORTANT]
> Certyfikacji platformy SAP HANA dla maszyn wirtualnych serii M platformy Azure są oparte wyłącznie na programie akcelerator zapisu platformy Azure dla **/hana/log** woluminu. W rezultacie produkcji scenariuszy SAP HANA wdrożenia na maszynach wirtualnych serii M Azure oczekuje się, można skonfigurować za pomocą akcelerator zapisu platformy Azure dla **/hana/log** woluminu.  

> [!NOTE]
> Na potrzeby scenariuszy produkcyjnych, sprawdź, czy typ maszyny Wirtualnej jest obsługiwana w przypadku oprogramowania SAP HANA SAP w [dokumentacji SAP IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Zalecane konfiguracje wyglądać następująco:

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

Sprawdź, czy przepływności różnych woluminach sugerowane spełnia obciążenia, które chcesz uruchomić. Jeśli obciążenie wymaga wyższej woluminów na potrzeby **/hana/danych** i **/hana/log**, należy zwiększyć liczbę Azure Premium Storage wirtualnych dysków twardych. Zmiany rozmiaru woluminu z kolejnych wirtualnych dysków twardych niż wymienione zwiększa operacje We/Wy i przepływność operacji We/Wy w ramach typu maszyny wirtualnej platformy Azure.

Akcelerator zapisu platformy Azure działa tylko w połączeniu z [usługi Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Dlatego co najmniej usługi Azure Premium Storage dyski tworzących **/hana/log** woluminów, które muszą zostać wdrożone jako dysków zarządzanych.

Istnieją limity usługi Azure Premium Storage wirtualne dyski twarde dla maszyny Wirtualnej, które mogą być obsługiwane przez akcelerator zapisu platformy Azure. Bieżące limity są:

- 16 dysków VHD dla maszyny Wirtualnej M128xx i M416xx
- 8 dysków VHD dla maszyny Wirtualnej M64xx i M208xx
- 4 wirtualne dyski twarde dla M32xx maszyny Wirtualnej

Bardziej szczegółowe instrukcje na temat włączania akcelerator zapisu platformy Azure można znaleźć w artykule [akceleratorem zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Szczegółowe informacje i ograniczenia dotyczące akcelerator zapisu platformy Azure można znaleźć w dokumentacji tych samych.


#### <a name="cost-conscious-azure-storage-configuration"></a>Koszt świadome konfigurację usługi Azure Storage
W poniższej tabeli przedstawiono konfigurację typy maszyn wirtualnych, których klienci także używają do hosta SAP HANA na maszynach wirtualnych platformy Azure. Być może niektóre typy maszyn wirtualnych, które nie spełnia wszystkie kryteria minimalny magazyn dla oprogramowania SAP HANA lub oficjalnie nie są obsługiwane z platformą SAP HANA, SAP. Ale do tej pory sprawiał tych maszyn wirtualnych do wykonania prawidłowo dla scenariuszy nieprodukcyjnych. 

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


Dyski zalecane dla mniejszych maszyny Wirtualnej typów z 3 x P20 oversize woluminy dotyczące zalecenia miejsce zgodnie z opisem w [SAP TDI magazynu oficjalny dokument dotyczący](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Jednak wyboru wyświetlane w tabeli została wprowadzona w celu zapewnienia wystarczającej przepływności dysku dla oprogramowania SAP HANA. Jeśli potrzebujesz zmiany **/hana/kopia zapasowa** wolumin, który został o rozmiarze do przechowywania kopii zapasowych, które reprezentują dwa razy woluminie pamięci, możesz ją dostosować.   
Sprawdź, czy przepływności różnych woluminach sugerowane spełnia obciążenia, które chcesz uruchomić. Jeśli obciążenie wymaga wyższej woluminów na potrzeby **/hana/danych** i **/hana/log**, należy zwiększyć liczbę Azure Premium Storage wirtualnych dysków twardych. Zmiany rozmiaru woluminu z kolejnych wirtualnych dysków twardych niż wymienione zwiększa operacje We/Wy i przepływność operacji We/Wy w ramach typu maszyny wirtualnej platformy Azure. 

> [!NOTE]
> Powyższej konfiguracji nie będą korzystać z [maszyny wirtualnej platformy Azure pojedynczy umowę SLA maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) ponieważ ona korzystać z usługi Azure Premium Storage i Azure Standard Storage. Jednak zaznaczenie została wybrana w celu optymalizacji kosztów. Należy wybrać usługi Premium Storage dla wszystkich dysków powyżej, które są wyświetlane jako usługi Azure Standard Storage (Sxx) do zapewnienia zgodności z SLA pojedynczej maszyny Wirtualnej platformy Azure do konfiguracji maszyny Wirtualnej.


> [!NOTE]
> Zalecenia dotyczące konfiguracji dysku, które zostały podane są przeznaczone dla minimalne wymagania, którą SAP wyraża kierunku ich dostawców infrastruktury. Rzeczywiste wdrożeń i scenariusze obciążeń sytuacjach napotkano, których te zalecenia pozwalają nadal nie dostarczył wystarczające możliwości. To może się zdarzyć, gdy klient wymagane szybciej ponowne załadowanie danych po ponownym uruchomieniu HANA lub gdzie kopii zapasowej konfiguracji wymaganych większą przepustowość do magazynu. Czasami uwzględnione **/hana/log** gdzie 5000 operacji We/Wy nie jest wystarczająca dla określonego obciążenia. Dlatego take te zalecenia jako początkowy punkt i dostosować na podstawie wymagań obciążenia.
>  

#### <a name="azure-ultra-ssd-storage-configuration-for-sap-hana"></a>Azure konfiguracji magazynu Ultra SSD platformy SAP Hana
Microsoft jest właśnie wprowadzenie nowego typu magazynu platformy Azure o nazwie [Azure Ultra SSD](https://azure.microsoft.com/updates/ultra-ssd-new-azure-managed-disks-offering-for-your-most-latency-sensitive-workloads/). Istotną różnicą między magazynu platformy Azure, oferowane do tej pory i Ultra SSD to, że możliwości dysku nie są powiązane z rozmiar dysku już. Jako klient korzystający z tych funkcji można zdefiniować Ultra dysku SSD:

- Rozmiar dysku z zakresu od 4 GiB do 65 536 GiB
- Operacje We/Wy z zakresu od 100 operacji We/Wy do 160 KB na SEKUNDĘ (maksymalna zależy również typy maszyn wirtualnych)
- Przepływność magazynu od 300 MB/s do 2000 MB na sekundę

Aby uzyskać szczegółowe informacje wyszukiwanie artykułu [ogłoszenie Ultra dysków SSD — nowej generacji technologii Azure Disks (wersja zapoznawcza)](https://azure.microsoft.com/blog/announcing-ultra-ssd-the-next-generation-of-azure-disks-technology-preview/)

Opcją UltraSSD daje możliwość definiowania pojedynczy dysk spełniający swój rozmiar, operacje We/Wy i dysku zakres przepływności. Zamiast używania menedżerów woluminów logicznych, takich jak LVM lub MDADM na podstawie usługi Azure Premium Storage, aby utworzyć woluminy, które spełniają wymagania dotyczące przepływności operacji We/Wy i magazynu. Mieszane konfiguracji można uruchomić między opcją UltraSSD i Magazyn w warstwie Premium. W efekcie ograniczyć użycie opcją UltraSSD /hana/data krytycznych dla wydajności i woluminy /hana/log i obejmuje inne woluminy z usługą Azure Premium Storage

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

Wartości Pomyśleliśmy jako punkt początkowy i muszą zostać obliczone dla rzeczywistego zapotrzebowania. Korzystać z dysków SSD Ultra Azure jest, że wartości dla operacji We/Wy i przepływność, można dostosować bez konieczności zamykania maszyny Wirtualnej lub zatrzymaniu obciążenie stosowane do systemu.   

> [!NOTE]
> Do tej pory migawek magazynu z magazynem opcją UltraSSD nie jest dostępna. Uniemożliwia to użycie migawki maszyny Wirtualnej za pomocą usługi Kopia zapasowa Azure


### <a name="set-up-azure-virtual-networks"></a>Konfigurowanie sieci wirtualnych platformy Azure
Jeśli masz połączenie lokacja lokacja na platformie Azure za pośrednictwem sieci VPN lub usługi ExpressRoute, musisz mieć co najmniej jedną sieć wirtualna platformy Azure jest połączona za pośrednictwem bramy sieci wirtualnej z obwodem usługi VPN lub usługi ExpressRoute. W przypadku prostych wdrożeń bramy wirtualnej można wdrożyć w podsieci sieci wirtualnej platformy Azure (VNet), który obsługuje również wystąpieniami platformy SAP HANA. Aby zainstalować oprogramowanie SAP HANA, należy utworzyć dwa dodatkowe podsieci w sieci wirtualnej platformy Azure. W jednej podsieci hostuje maszyny wirtualne do uruchamiania wystąpień oprogramowania SAP HANA. Innych podsieci jest uruchamiany serwer Przesiadkowy lub maszyny wirtualne zarządzania hosta SAP HANA Studio i inne oprogramowanie do zarządzania, oprogramowanie aplikacji.

> [!IMPORTANT]
> Z funkcji, ale bardziej ważne poza ze względu na wydajność nie jest obsługiwane do skonfigurowania [wirtualne urządzenia sieciowe Azure](https://azure.microsoft.com/solutions/network-appliances/) ścieżki komunikacji między aplikacją SAP DBMS warstwa oprogramowania SAP NetWeaver Hybris lub S/4HANA na podstawie systemu SAP. Komunikacja między warstwy aplikacji SAP i jej systemu DBMS musi bezpośrednie jednego. Nie ma ograniczenia [ASG platformy Azure i sieciowej grupy zabezpieczeń reguły](https://docs.microsoft.com/azure/virtual-network/security-overview) tak długo, jak te reguły ASG i sieciowej grupy zabezpieczeń umożliwiają bezpośrednią komunikację. Dodatkowe scenariusze, w których urządzeń WUS nie są obsługiwane znajdują się w zaufanych ścieżek komunikacji między maszynami wirtualnymi platformy Azure, reprezentujące interwencja urządzeń i węzłów klastra program Pacemaker w systemie Linux, zgodnie z opisem w [wysoką dostępność środowiska SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server w przypadku aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Lub w ramach komunikacji ścieżek między maszynami wirtualnymi platformy Azure i systemu Windows serwer SOFS nawet zgodnie z opisem w [klastra wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udziału plików na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). Urządzenia WUS w komunikacji ścieżki mogą łatwo dwukrotnie opóźnienie sieciowe między dwoma partnerami komunikacji, można ograniczyć przepustowość w krytyczne ścieżki między warstwą aplikacji SAP i warstwy system DBMS. W niektórych scenariuszach zaobserwowane ze swoimi klientami urządzeń WUS może spowodować klastry program Pacemaker w systemie Linux, aby zakończyć się niepowodzeniem w przypadku których komunikacji między węzłami klastra program Pacemaker w systemie Linux muszą komunikować się na ich urządzenia interwencja za pośrednictwem urządzenia NVA.  
> 

> [!IMPORTANT]
> Inny projekt, który jest **nie** jest oddzielenie warstwy aplikacji SAP i warstwą DBMS w różnych sieciach wirtualnych platformy Azure, które nie są obsługiwane [skomunikowane równorzędnie](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ze sobą. Zaleca się oddzielenie czynności związanych z warstwy aplikacji SAP i warstwy system DBMS, za pomocą podsieci w sieci wirtualnej platformy Azure, zamiast korzystać z różnych sieci wirtualnych platformy Azure. Jeśli zdecydujesz się na zalecenia, a zamiast tego oddzielenie czynności związanych z dwoma warstwami w innej sieci wirtualnej, dwie sieci wirtualne muszą być [skomunikowane równorzędnie](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Należy pamiętać, że ruchem sieciowym między dwoma [skomunikowane równorzędnie](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) sieciami wirtualnymi platformy Azure są przedmiotem koszty transferu. Z woluminem olbrzymich ilościach danych w wielu terabajtów wymieniane między warstwy aplikacji SAP i warstwy system DBMS znaczne koszty mogą zgromadzonych w przypadku warstwy aplikacji SAP i DBMS warstwę można podzielić między dwiema równorzędnymi sieciami wirtualnymi platformy Azure. 

Po zainstalowaniu maszyn wirtualnych do uruchamiania oprogramowania SAP HANA, potrzebne są maszyny wirtualne:

- Zainstalowane dwie wirtualne karty sieciowe: jedną kartą Sieciową, aby nawiązać połączenie z podsieci zarządzania i jedną kartą Sieciową, aby nawiązać połączenie z siecią lokalną lub innymi sieciami wystąpienie SAP HANA na maszynie Wirtualnej platformy Azure.
- Statyczne prywatne adresy IP, które zostały wdrożone dla obu wirtualnych kart sieciowych.

> [!NOTE]
> Należy przypisać statyczne adresy IP za pośrednictwem platformy Azure oznacza, że do poszczególnych kart sieciowych. Nie należy przypisywać statyczne adresy IP w ramach systemu operacyjnego gościa do wirtualnej karty sieciowej. Niektórych usług platformy Azure, takich jak usługa Kopia zapasowa Azure opierają się na fakcie, że w co najmniej podstawowego wirtualnej karty sieciowej jest ustawiony, DHCP, a nie statycznych adresów IP. Zobacz też dokumentu [kopii zapasowej maszyny wirtualnej Azure Rozwiązywanie problemów z](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Musisz przypisać wiele statycznych adresów IP do maszyny Wirtualnej, należy przypisać wiele kart sieciowych do maszyny Wirtualnej.
>
>

Jednak w przypadku wdrożeń, które są trwałe, musisz utworzyć architektury sieci wirtualne centrum danych na platformie Azure. Ta architektura zaleca się rozdzielenie bramy sieci wirtualnej platformy Azure, który nawiązuje połączenie z środowiska lokalnego do oddzielnych sieci wirtualnej platformy Azure. Ta oddzielną sieć wirtualną powinien obsługiwać cały ruch, powodują, że do środowiska lokalnego lub z Internetem. Takie podejście umożliwia wdrażanie oprogramowania do inspekcji i ruchu rejestrowania, który wprowadzi wirtualnego centrum danych na platformie Azure w tej sieci wirtualnej serwera centralnego oddzielne. Dlatego należy jednej sieci wirtualnej obsługującego całe oprogramowanie i konfiguracje związanego z in - ruch wychodzący i aby wdrożenie systemu Azure.

Artykuły [wirtualnego centrum danych Azure: Perspektywa sieci](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) i [Azure Virtual Datacenter i Enterprise Control Plane](https://docs.microsoft.com/azure/architecture/vdc/) podać więcej informacji na temat podejścia wirtualnego centrum danych i powiązane projektowania sieci wirtualnej platformy Azure.


>[!NOTE]
>Ruch przepływa między piastą i przy użyciu sieci wirtualnej szprychy [komunikacji równorzędnej sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) jest przedmiotem dodatkowe [koszty](https://azure.microsoft.com/pricing/details/virtual-network/). Na podstawie tych kosztów, może być konieczne dokonanie kompromisów między wykonywania strict gwiazdy projektu sieci i wykonywania wielu [bramy usługi ExpressRoute platformy Azure](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) połączyć do "szprychy" w celu obejścia, komunikacja równorzędna sieci wirtualnych. Jednak wprowadzenie dodatkowych bramy usługi ExpressRoute platformy Azure [koszty](https://azure.microsoft.com/pricing/details/vpn-gateway/) także. Może również wystąpić żadnymi dodatkowymi kosztami oprogramowania innych firm, używane dla ruchu sieciowego logowania, inspekcji i monitorowania. Zależne od kosztów wymiany danych za pomocą komunikacji równorzędnej sieci wirtualnych na jednej stronie i utworzone przez dodatkowe bramy usługi ExpressRoute platformy Azure i licencji na oprogramowanie dodatkowych kosztów, może się okazać dla mikrosegmentację w ramach jednej sieci wirtualnej za pomocą podsieci jako jednostka izolacji zamiast sieci wirtualnych.


Aby uzyskać przegląd różnych metod do przypisywania adresów IP, zobacz [typy adresów IP i metody alokacji na platformie Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Dla maszyn wirtualnych z uruchamiania oprogramowania SAP HANA powinny działać przy użyciu statycznych adresów IP przypisane. Przyczyną jest to, że niektóre atrybuty konfiguracji platformy Hana odwoływać się do adresów IP.

[Usługa Azure sieciowych grup zabezpieczeń (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) są używane do kierowania ruchu, który jest kierowany do serwera przesiadkowego lub wystąpienia platformy SAP HANA. Sieciowe grupy zabezpieczeń i ostatecznie [grupy zabezpieczeń aplikacji](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) są skojarzone z podsiecią platformy SAP HANA i podsieci zarządzania.

Na poniższej ilustracji przedstawiono omówienie schematu nierównej wdrożenia dla oprogramowania SAP HANA po gwiazdy architektury sieci wirtualnej:

![Przybliżony wdrożenia schematu dla oprogramowania SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

Aby wdrożyć oprogramowanie SAP HANA na platformie Azure bez połączenia lokacja lokacja, nadal chcesz włączyć osłonę wystąpienie SAP HANA z publicznego Internetu i Ukryj go za serwerem proxy do przodu. W tym scenariuszu podstawowego wdrożenia zależy od systemu Azure wbudowanych usług DNS można rozpoznać nazwy hostów. W bardziej złożonym wdrożeniu, gdzie publicznych adresów IP są używane wbudowanej usługi DNS platformy Azure są szczególnie ważne. Użyj sieciowych grup zabezpieczeń platformy Azure i [urządzeń WUS Azure](https://azure.microsoft.com/solutions/network-appliances/) do sterowania, monitorowania, routing z Internetu do architektury sieci wirtualnej platformy Azure na platformie Azure. Na poniższej ilustracji przedstawiono nierównej schematu do wdrażania oprogramowania SAP HANA bez połączenia lokacja lokacja w gwiazdy architektury sieci wirtualnej:
  
![Przybliżony wdrożenia schematu dla oprogramowania SAP HANA bez połączenia lokacja lokacja](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Inny opis o tym, jak za pomocą urządzeń WUS Azure kontrolę i monitorowanie dostępu z Internetu bez koncentratora i typu gwiazda architektury sieci wirtualnej można znaleźć w artykule [Wdrażanie wirtualnych urządzeń sieciowych o wysokiej dostępności](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Konfigurowanie infrastruktury platformy Azure dla oprogramowania SAP HANA skalowalnego w poziomie
Firma Microsoft ma jeden SKU maszyn wirtualnych serii M, który jest certyfikowany dla skalowalnego w poziomie konfiguracji SAP HANA. Typ maszyny Wirtualnej M128s stało się certyfikaty na skalowalnym w poziomie z maksymalnie 16 węzłach. Zmiany w certyfikaty skalowalnego w poziomie SAP HANA na maszynach wirtualnych platformy Azure, sprawdź [listy certyfikowane platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Minimalnej wersji systemu operacyjnego do wdrażania skalowalnego w poziomie konfiguracji maszyn wirtualnych platformy Azure są następujące:

- SUSE Linux 12 z dodatkiem SP3
- Red hat Linux 7.4

Węzeł 16 certyfikacji skalowalnego w poziomie

- Jeden węzeł jest węzłem głównym
- Maksymalnie 15 węzłami są węzły procesu roboczego

>[!NOTE]
>W przypadku wdrożeń skalowalnego w poziomie maszyny Wirtualnej platformy Azure jest możliwość wstrzymania węzeł
>

Powód braku możliwości konfiguracji rezerwy węzła są dwa cele:

- Platforma Azure ma na tym etapie nie macierzystej usługi systemu plików NFS. W rezultacie udziałów NFS należy skonfigurować przy pomocy funkcje innych firm.
- Brak konfiguracji systemu plików NFS innych firm mogą spełniają kryteria opóźnienie magazynu dla oprogramowania SAP HANA przy użyciu swoich rozwiązań wdrożonych na platformie Azure.

W rezultacie **/hana/danych** i **/hana/log** woluminy nie mogą być udostępniane. Nie współużytkuje te woluminy pojedynczych węzłów, eliminuje konieczność użycia wstrzymania węzła programu SAP HANA w konfiguracji skalowania w poziomie.

W wyniku podstawowy projekt dla pojedynczego węzła w konfiguracji skalowania w poziomie ma następującą postać:

![Podstawy skalowalnego w poziomie z jednego węzła](media/hana-vm-operations/scale-out-basics.PNG)

Konfiguracji podstawowej maszyny Wirtualnej węzła dla oprogramowania SAP HANA skalowalnego w poziomie wygląda następująco:

- Aby uzyskać **/hana/udostępnione**, budować klastra systemu plików NFS o wysokiej dostępności oparte na systemie SUSE Linux 12 z dodatkiem SP3. Ten klaster hostów **/hana/udostępnione** udziałów NFS w konfiguracji skalowania w poziomie i oprogramowania SAP NetWeaver ani usług centralnej BW/4HANA. Dokumentacja do tworzenia takiej konfiguracji znajduje się w artykule [wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Wszystkie woluminy dysku są **nie** współużytkowanych przez różne węzły i są **nie** oparte na systemu plików NFS. Konfiguracje instalacji i procedury dotyczące instalacji platformy HANA skalowalnego w poziomie z nieudostępnione **/hana/danych** i **/hana/log** podano więcej szczegółów, w tym dokumencie.

>[!NOTE]
>O wysokiej dostępności klastra systemu plików NFS wyświetlaną w grafiki do tej pory jest obsługiwany w przypadku systemu SUSE Linux tylko. Rozwiązanie systemu plików NFS o wysokiej dostępności oparte na systemie Red Hat będzie później poinformowany.

Części ustalanie rozmiarów woluminów dla węzłów jest taka sama, jak w przypadku skalowania w górę, z wyjątkiem **/hana/udostępnione**. W ramach jednostki SKU maszyny Wirtualnej M128s sugerowanych rozmiarów i typów wyglądać następująco:

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z VM I/O<br /> Przepływność | /hana/data | / hana/log | wolumin/root | /usr/sap | Hana/kopia zapasowa |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Sprawdź, czy przepływności różnych woluminach sugerowane spełnia obciążenia, które chcesz uruchomić. Jeśli obciążenie wymaga wyższej woluminów na potrzeby **/hana/danych** i **/hana/log**, należy zwiększyć liczbę Azure Premium Storage wirtualnych dysków twardych. Zmiany rozmiaru woluminu z kolejnych wirtualnych dysków twardych niż wymienione zwiększa operacje We/Wy i przepływność operacji We/Wy w ramach typu maszyny wirtualnej platformy Azure. Mają zastosowanie również akcelerator zapisu platformy Azure do dysków tworzące **/hana/log** woluminu.
 
W dokumencie [SAP HANA TDI dyskowej](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), formuła o nazwie definiujący rozmiar **/hana/udostępnione** wolumin na potrzeby skalowania w poziomie jako rozmiar pamięci węzła pojedynczego procesu roboczego na czterech węzłach procesu roboczego.

Przy założeniu, że wykonujesz platformy SAP HANA skalowalnego w poziomie certyfikowanych M128s maszyny Wirtualnej platformy Azure z około 2 TB pamięci, zaleceń dotyczących SAP można podsumować takich jak:

- Jeden węzeł główny i maksymalnie węzła procesu roboczego cztery **/hana/udostępnione** wolumin musi mieć rozmiar 2 TB. 
- Jeden węzeł główny i węzły procesu roboczego pięć i osiem, rozmiar **/hana/udostępnione** powinny być 4 TB. 
- Jeden węzeł główny i węzły procesu roboczego 9-12, rozmiar 6 TB dla **/hana/udostępnione** będą wymagane. 
- Jeden węzeł główny i z użyciem od 12 do węzłów procesu roboczego 15, są wymagane do **/hana/udostępnione** wolumin, który ma rozmiar 8 TB.

Ważnych projektu, wyświetlanego w grafiki Konfiguracja z pojedynczym węzłem dla maszyn wirtualnych skalowania w poziomie SAP HANA jest sieć wirtualna lub lepiej konfigurację podsieci. SAP zaleca rozdzielenie ruchu umożliwiający dostęp do Internetu klienta/aplikacji, od komunikacji między węzłami platformy HANA. Jak pokazano na grafiki, w tym celu należy przestrzegać o dwóch różnych kart sieciowych dołączonych do maszyny Wirtualnej. Obu kart sieciowych znajdują się w różnych podsieciach, ma dwa różne adresy IP. Następnie możesz kontrolować przepływ ruchu z regułami routingu przy użyciu sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika.

Szczególnie na platformie Azure istnieją nie oznacza, że i metody, aby wymusić jakości usług i przydziałów w określonych kart sieciowych. Co w efekcie rozdzielenie klienta/aplikacji dostępnego z wewnątrz węzła komunikacji nie powoduje otwarcia żadnych priorytety jeden strumień ruchu za pośrednictwem innych możliwości. Zamiast tego oddzielenie pozostaje zabezpieczeń w osłony komunikacji wewnątrz węzła konfiguracji skalowania w poziomie.  

>[!IMPORTANT]
>SAP zaleca, oddzielając ruchu sieciowego po stronie klienta/aplikacji i ruchu wewnątrz węzła zgodnie z opisem w tym dokumencie. W związku z tym umieszczenie architekturę w miejscu, jak pokazano w ostatnim grafiki jest zdecydowanie zalecane.
>

Z punktu widzenia sieci architektury minimalna wymagana sieć będzie wyglądać:

![Podstawy skalowalnego w poziomie z jednego węzła](media/hana-vm-operations/scale-out-networking-overview.PNG)

Limity do tej pory obsługiwane są 15 procesów roboczych, które są dodatkowe do jednego węzła głównego.

Z punktu widzenia magazynu architektury magazynu powinien wyglądać podobnie jak:


![Podstawy skalowalnego w poziomie z jednego węzła](media/hana-vm-operations/scale-out-storage-overview.PNG)

**/Hana/udostępnione** woluminów znajduje się w konfiguracji wysokiej dostępności udziału NFS. Natomiast wszystkie inne dyski "lokalnie" są instalowane dla poszczególnych maszyn wirtualnych. 

### <a name="highly-available-nfs-share"></a>Udziału NFS o wysokiej dostępności
Klaster systemu plików NFS o wysokiej dostępności do tej pory pracuje z systemem SUSE Linux tylko. Dokument [wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) opisano, jak je skonfigurować. Jeśli nie udostępniasz klastra systemu plików NFS inne konfiguracje HANA poza siecią wirtualną platformy azure, uruchamianego z wystąpieniami platformy SAP HANA, należy go zainstalować w tej samej sieci wirtualnej. Zainstaluj go w swojej własnej podsieci i upewnij się, że nie wszystkie dowolnego ruchu mogą uzyskiwać dostęp do tej podsieci. Zamiast tego chcesz ograniczyć ruch do tej podsieci do adresów IP maszyny wirtualnej, wykonaj ruch do **/hana/udostępnione** woluminu.

Powiązane z wirtualnej karty sieciowej maszyny wirtualnej skalowalnego w poziomie HANA, należy przekierować **/hana/udostępnione** ruchu, zalecenia są:

- Ponieważ ruch do **/hana/udostępnione** średni, kierowania go za pośrednictwem wirtualnej karty sieciowej, która jest przypisana do sieci klienta w minimalnej konfiguracji
- Po pewnym czasie dla ruchu do **/hana/udostępnione**, wdrożyć w trzeciej podsieci w sieci wirtualnej, wdrażanie oprogramowania SAP HANA skalowalnego w poziomie konfiguracji i przypisz trzecią wirtualną kartę sieciową, która znajduje się w tej podsieci. Użyj trzeci wirtualnej karty sieciowej i skojarzony adres IP dla ruchu do udziału NFS. Następnie można zastosować oddzielne dostęp i reguły routingu.

>[!IMPORTANT]
>Ruch sieciowy między maszyny wirtualne, które mają platformy SAP HANA w sposób skalowalny w poziomie wdrożonych i o wysokiej dostępności NFS mogą w żadnym kierowane za pośrednictwem [urządzenie WUS](https://azure.microsoft.com/solutions/network-appliances/) lub podobne urządzenie wirtualne. Sieciowe grupy zabezpieczeń platformy Azure są żadnych takich urządzeń. Przejrzyj swoje reguły routingu, aby upewnić się, detoured urządzeń WUS ani podobne urządzenia wirtualnego podczas dostępu do udziału NFS o wysokiej dostępności na maszynach wirtualnych, do uruchamiania oprogramowania SAP HANA.
> 

Jeśli chcesz udostępnić klaster systemu plików NFS o wysokiej dostępności między konfiguracjami oprogramowania SAP HANA, należy przenieść te konfiguracje HANA do tej samej sieci wirtualnej. 
 

### <a name="installing-sap-hana-scale-out-n-azure"></a>Instalowanie platformy SAP HANA skalowalnego w poziomie n Azure
Instalowanie konfiguracji SAP skalowalnego w poziomie, należy wykonać ogólne kroki:

- Wdrażanie nowych lub dostosowanie nowej infrastruktury sieci wirtualnej platformy Azure
- Wdrażanie nowych maszyn wirtualnych przy użyciu woluminów magazynu Premium zarządzanych platformy Azure
- Wdrażanie nowej lub dostosowanie istniejącego klastra systemu plików NFS o wysokiej dostępności
- Dostosowanie routing w sieci, aby upewnić się, że na przykład komunikacji wewnątrz węzła między maszynami wirtualnymi nie odbywa się za pośrednictwem [urządzenie WUS](https://azure.microsoft.com/solutions/network-appliances/). Dotyczy to także ruch między maszynami wirtualnymi i o wysokiej dostępności klastra systemu plików NFS.
- Zainstaluj oprogramowanie SAP HANA węzła głównego.
- Dostosowanie parametrów konfiguracji węzła głównego platformy SAP HANA
- Kontynuuj instalację węzłów procesu roboczego platformy SAP HANA

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Instalacja oprogramowania SAP Hana w konfiguracji skalowania w poziomie
Wdrożono infrastrukturę maszyny Wirtualnej platformy Azure, a wszystkie pozostałe czynności są wykonywane, musisz zainstalować skalowalnego w poziomie konfiguracji SAP HANA w tych krokach:

- Instalowanie platformy SAP HANA węzła głównego zgodnie z dokumentacją firmy SAP
- **Po zakończeniu instalacji, należy zmienić plik global.ini, a następnie dodaj parametr "basepath_shared = nie" do global.ini**. Ten parametr umożliwia uruchamianie w skali w poziomie bez "udostępnione" oprogramowania SAP HANA **/hana/danych** i **/hana/log** woluminów między węzłami. Szczegółowe informacje są udokumentowane w artykule [2080991 # Uwaga SAP](https://launchpad.support.sap.com/#/notes/2080991).
- Po zmianie parametrów global.ini, ponownie uruchom wystąpienie SAP HANA
- Dodaj dodatkowe węzły procesów roboczych. Zobacz też <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Określ sieci wewnętrznej dla oprogramowania SAP HANA komunikacji między węzłami podczas instalacji lub później za pomocą, na przykład, hdblcm lokalnego. Aby uzyskać bardziej szczegółowe dokumentacji, zobacz też [2183363 # Uwaga SAP](https://launchpad.support.sap.com/#/notes/2183363). 

Następujące tę procedurę instalacji, konfiguracji skalowania w poziomie został zainstalowany zamierza użyć nieudostępnione dysków do uruchomienia **/hana/danych** i **/hana/log**. Natomiast **/hana/udostępnione** wolumin ma zostać umieszczone na o wysokiej dostępności udziału NFS.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA dynamiczna obsługa warstw w wersji 2.0 maszyn wirtualnych platformy Azure

Oprócz certyfikaty SAP HANA na maszynach wirtualnych platformy Azure serii M, SAP HANA dynamiczna obsługa warstw w wersji 2.0 obsługiwana jest również na Microsoft Azure (zobacz dokumentacja SAP HANA dynamiczna obsługa warstw na łączy dalszych szczegółów). Choć nie ma różnicy w instalacji produktu lub jego działania, na przykład za pomocą Panelu sterowania programu SAP HANA w maszynie wirtualnej platformy Azure, istnieje kilka istotnych elementów, które są obowiązkowe oficjalne pomocy technicznej dotyczącej platformy Azure. Tych kluczowych zagadnieniach zostały opisane poniżej. W artykule skrót od "Tw 2.0" będzie można używać zamiast pełnej nazwy dynamiczna obsługa warstw w wersji 2.0.

SAP HANA dynamiczna obsługa warstw w wersji 2.0 nie jest obsługiwana przez system SAP BW lub S4HANA. Przypadki użycia główne są teraz macierzyste aplikacje HANA.


### <a name="overview"></a>Przegląd

Na ilustracji poniżej zawiera omówienie dotyczące obsługi DT 2.0 w systemie Microsoft Azure. Istnieje zestaw obowiązkowe wymagania, które ma być stosowana do wykonania oficjalnych certyfikatów:

- DT 2.0 musi być zainstalowana na dedykowanym maszyny Wirtualnej platformy Azure. Może nie działać w tej samej maszyny Wirtualnej, której działa oprogramowanie SAP HANA
- SAP HANA i maszyny wirtualne w wersji 2.0 DT musi zostać wdrożony w ramach tej samej sieci wirtualnej platformy Azure
- SAP HANA i maszyny wirtualne w wersji 2.0 DT musi zostać wdrożony za pomocą usługi Azure obsługą przyspieszonej sieci
- Typ magazynu dla maszyn wirtualnych w wersji 2.0 DT musi być usługi Azure Premium Storage
- Wiele dysków platformy Azure musi być dołączony do maszyny Wirtualnej w wersji 2.0 DT
- Jest to wymagane do utworzenia raid oprogramowania / woluminu rozłożonego (za pośrednictwem lvm lub mdadm) przy użyciu rozkładanie na dyskach platformy Azure

Więcej szczegółów mają są szczegółowo opisane w poniższych sekcjach.

![Omówienie architektury 2.0 programu SAP HANA DT](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Dla programu SAP HANA DT 2.0 w wersji dedykowanej maszynie Wirtualnej platformy Azure

W usłudze Azure IaaS DT 2.0 jest obsługiwana tylko na dedykowanej maszynie Wirtualnej. Nie można uruchomić DT 2.0 w tej samej maszyny wirtualnej platformy Azure, gdzie jest uruchomione wystąpienie oprogramowania HANA. Początkowo dwa typy maszyn wirtualnych może służyć do uruchamiania programu SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Zobacz opis typu maszyny Wirtualnej [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Biorąc pod uwagę podstawowa koncepcja DT w wersji 2.0, czyli o odciążaniu "ciepłych" dane w celu obniżenia kosztów warto użyć odpowiednich rozmiarów maszyn wirtualnych. Nie ma rygorystyczne reguły chociaż dotyczące możliwych kombinacji. To zależy od obciążenia pracą danego klienta.

Zalecane konfiguracje będą:

| Typ maszyny Wirtualnej programu SAP HANA | Typ maszyny Wirtualnej w wersji 2.0 DT |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Możliwe są wszystkie kombinacje z certyfikatem SAP HANA maszyny wirtualne serii M z obsługiwanych DT 2.0 maszyn wirtualnych (M64-32ms i E32sv3).


### <a name="azure-networking-and-sap-hana-dt-20"></a>Sieci platformy Azure i SAP HANA DT w wersji 2.0

Instalowanie DT 2.0 na dedykowanej maszynie Wirtualnej wymaga przepustowość sieci między maszyny Wirtualnej w wersji 2.0 DT i SAP HANA maszyny Wirtualnej co najmniej 10 Gb. Dlatego jest wymagane, aby umieścić wszystkie maszyny wirtualne w tej samej sieci wirtualnej platformy Azure i Włącz Azure przyspieszonej łączności sieciowej.

Zobacz dodatkowe informacje na temat platformy Azure przyspieszonej łączności sieciowej [tutaj](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Magazyn maszyny Wirtualnej dla programu SAP HANA DT 2.0

Zgodnie z DT 2.0 najlepszych rozwiązań przepływność operacji We/Wy dysku powinna być minimalna 50 MB na sekundę na każdy rdzeń fizyczny. Patrząc Specyfikacja dwóch typów maszyn wirtualnych platformy Azure, które są obsługiwane dla wersji 2.0 DT dysku maksymalny limit przepływności operacji We/Wy do wyszukiwania maszyny Wirtualnej, takie jak:

- E32sv3:   768 MB/s (niebuforowanych) co oznacza, że stosunek 48 MB na sekundę na każdy rdzeń fizyczny
- M64-32MS:  1000 MB/s (niebuforowanych) co oznacza, że stosunek 62,5 MB na sekundę na każdy rdzeń fizyczny

Konieczne jest dołączenie wielu dysków platformy Azure do maszyny Wirtualnej w wersji 2.0 DT i Utwórz raid oprogramowania (rozkładanie) na poziomie systemu operacyjnego w celu osiągnięcia maksymalnego limitu przepustowości dysku na maszynie Wirtualnej. Pojedynczy dysk platformy Azure nie może dostarczyć przepływności do osiągnięcia maksymalnego limitu maszyn wirtualnych w tym zakresie. Usługa Azure Premium storage jest obowiązkowe, aby uruchomić DT w wersji 2.0. 

- Szczegółowe informacje o typach dostępnych dysków platformy Azure można znaleźć [tutaj](../../windows/disks-types.md)
- Można znaleźć szczegółowe informacje o tworzeniu raid oprogramowania za pośrednictwem mdadm [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Szczegółowe informacje o konfigurowaniu LVM do utworzenia woluminu rozłożonego dla maksymalna przepływność można znaleźć [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

W zależności od wymagań dotyczących rozmiaru istnieją różne opcje, aby osiągnąć maksymalną przepływność maszyny Wirtualnej. Oto konfiguracje dysków wolumin danych dla każdego typu maszyny Wirtualnej w wersji 2.0 DT do osiągnięcia górnego limitu przepływności maszyny Wirtualnej. Maszyna wirtualna E32sv3 powinny być uważane poziomem wpis dla obciążeń mniejszych. W przypadku, gdy jej Włącz się, że nie jest szybkie wystarczająco może być konieczne zmienić rozmiar maszyny Wirtualnej, aby M64-32ms.
Jako maszyna wirtualna M64-32ms ma dużej ilości pamięci, obciążenie We/Wy nie może nawiązać połączenia limit, szczególnie w przypadku obciążeń intensywnie korzystających z odczytu. W związku z tym mniejszą liczbę dysków w zestawie usługi stripe są wystarczające w zależności od określonego obciążenia klientów. Jednak znajdować się w bezpiecznej stronie dysku poniższych konfiguracji dobrano w celu zagwarantowania maksymalną przepływność:


| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Disk Config 1 | Konfiguracja dysku 2 | Disk Config 3 | Konfiguracja dysku 4 | Disk Config 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1,5 TB | 


Szczególnie w przypadku, gdy obciążenie jest intensywnego odczytu go może zwiększyć wydajność operacji We/Wy włączenie pamięci podręcznej hosta platformy Azure "tylko do odczytu" zgodnie z zaleceniami woluminy danych oprogramowania bazy danych. Dla transakcji dziennika pamięci podręcznej dysku platformy Azure hosta musi być "none". 

Dotyczące rozmiaru woluminu dziennika zalecane punkt początkowy jest o heurystykę 15% rozmiaru danych. Tworzenie woluminu dziennika można osiągnąć przy użyciu typów inny dysk platformy Azure, w zależności od wymagań kosztów i przepływności. W dzienniku wysokiej przepływności operacji We/Wy woluminu jest wymagana.  W przypadku używania maszyny Wirtualnej wpisz M64-32ms zdecydowanie zalecane jest włączanie [akceleratorem zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Akcelerator zapisu platformy Azure zapewnia opóźnienia zapisu optymalne dysku dla dziennika transakcji (dostępne tylko dla serii M). Istnieje kilka elementów, które należy wziąć pod uwagę, chociaż takie jak maksymalna liczba dysków na typ maszyny Wirtualnej. Można znaleźć szczegółowe informacje o akceleratorem zapisu [tutaj](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)


Poniżej przedstawiono kilka przykładów dotyczących rozmiaru woluminu dziennika:

| Typ dysk i rozmiar woluminu danych | wolumin dziennika i dysk typu konfiguracji 1 | wolumin dziennika i dysk typu konfiguracji 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


W takich jak SAP HANA skalowalnego w poziomie, katalog /hana/shared ma być współużytkowane przez maszynę Wirtualną SAP HANA i DT maszyny Wirtualnej w wersji 2.0. Taką samą architekturę, jak w przypadku platformy SAP HANA w skali w poziomie dedykowane maszyny wirtualne, które działają jako serwer systemu plików NFS o wysokiej dostępności jest zalecane. W celu zapewnienia udostępnionego woluminu kopii zapasowej, można identyczne projektu. Ale czy konieczna może być wysokiej dostępności, czy wystarczy używać tylko dedykowanych maszyn wirtualnych z wystarczającą pojemnością magazynu na działanie jako serwer zapasowy jest do klienta.



### <a name="links-to-dt-20-documentation"></a>Linki do dokumentacji DT w wersji 2.0 

- [SAP HANA dynamiczna obsługa warstw na Przewodnik instalacji i aktualizacji](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA dynamiczna obsługa warstw na samouczki i zasoby](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA dynamiczne warstw weryfikacji koncepcji](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA w wersji 2.0 SPS 02 dynamiczne ulepszenia obsługi warstw](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operacje dotyczące wdrażania oprogramowania SAP HANA na maszynach wirtualnych platformy Azure
Poniższych sekcjach opisano niektóre operacje związane z wdrażaniem systemów SAP HANA na maszynach wirtualnych platformy Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Tworzenie kopii zapasowej i przywracanie operacji na maszynach wirtualnych platformy Azure
Następujące dokumenty opisano, jak wykonać kopię zapasową i przywrócić wdrożenie oprogramowania SAP HANA:

- [Omówienie kopii zapasowych oprogramowania SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA poziomie plików z kopii zapasowej](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Test porównawczy migawek magazynu oprogramowania SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Uruchamianie i ponowne uruchamianie maszyn wirtualnych, które zawierają oprogramowanie SAP HANA
Widocznym funkcji chmury publicznej platformy Azure jest, że opłaty są naliczane tylko za usługi obliczeniowe minut. Na przykład podczas zamykania maszyny Wirtualnej z systemem SAP HANA są naliczane tylko za koszty magazynowania w tym samym czasie. Kolejną funkcją jest dostępne po określeniu statycznych adresów IP dla maszyn wirtualnych we wdrożeniu wstępnym. Po ponownym uruchomieniu maszyny Wirtualnej, która ma platformy SAP HANA z jego wcześniejszego adresami IP powoduje ponowne uruchomienie maszyny Wirtualnej. 


### <a name="use-saprouter-for-sap-remote-support"></a>Użyj SAProuter dla zdalnej pomocy technicznej SAP
Jeśli masz połączenie lokacja lokacja między lokalizacjami lokalnymi i platformy Azure, a używasz składniki SAP, następnie prawdopodobnie już używasz SAProuter. W takim przypadku należy wykonać następujące elementy dla zdalnej pomocy technicznej:

- Obsługa prywatnych i statyczny adres IP maszyny Wirtualnej obsługującego platformy SAP HANA w konfiguracji SAProuter.
- Konfigurowanie sieciowej grupy zabezpieczeń w podsieci, który jest hostem maszyny Wirtualnej platformy HANA, aby zezwalać na ruch przez TCP/IP port 3299.

Jeśli łączysz się z platformą Azure za pośrednictwem Internetu, a nie masz SAP router dla maszyny Wirtualnej z platformą SAP HANA, należy zainstalować składnik. Zainstaluj SAProuter w oddzielnych maszyny Wirtualnej w podsieci zarządzania. Na poniższej ilustracji przedstawiono nierównej schematu do wdrażania oprogramowania SAP HANA z SAProuter i bez połączenia lokacja lokacja:

![Przybliżony wdrożenia schematu dla oprogramowania SAP HANA bez połączenia lokacja lokacja i SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Pamiętaj zainstalować SAProuter w oddzielnych maszyny Wirtualnej i nie z maszyną Wirtualną serwera Przesiadkowego. Oddzielne maszyny Wirtualnej musi mieć statyczny adres IP. Aby połączyć Twoje SAProuter SAProuter, która jest obsługiwana przez oprogramowanie SAP, skontaktuj się z SAP dla adresu IP. (SAProuter, która jest obsługiwana przez oprogramowanie SAP jest odpowiednikiem SAProuter wystąpienia, które zainstalujesz na swojej maszynie Wirtualnej). Umożliwia skonfigurowanie wystąpienia SAProuter przez adres IP z systemu SAP. W ustawieniach konfiguracji tylko niezbędne jest TCP port 3299.

Aby uzyskać więcej informacji na temat konfiguracji i utrzymania zdalną obsługę połączeń za pośrednictwem SAProuter, zobacz [dokumentacji SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Wysokiej dostępności przy użyciu oprogramowania SAP HANA na natywnych maszynach wirtualnych platformy Azure
Jeśli pracujesz w systemie SUSE Linux Enterprise Server dla programu SAP aplikacji 12 z dodatkiem SP1 lub nowszego, można ustanowić klastra program Pacemaker ze pomocą metody STONITH urządzeniami. Mogą używać urządzeń, aby ustawić konfigurację platformy SAP HANA, korzystającej z replikacji synchronicznej replikacji systemu HANA i automatycznej pracy awaryjnej. Aby uzyskać więcej informacji na temat procedury instalacyjnej, zobacz [przewodnik SAP HANA wysoką dostępność dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
