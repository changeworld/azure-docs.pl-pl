---
title: Operacje platformy SAP HANA na platformie Azure | Dokumentacja firmy Microsoft
description: Przewodnik obsługi programu systemami SAP HANA, które są wdrażane na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2480ad464f2fc716cf68672387a189aeb92f5737
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918836"
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA w podręczniku obsługi platformy Azure
Ten dokument zawiera wskazówki dotyczące działania systemów SAP HANA, które zostały wdrożone na natywnych maszynach wirtualnych platformy Azure (maszyny wirtualne). Ten dokument nie jest przeznaczona do zastąpienia dokumentacji SAP standardowa obejmuje następującą zawartością:

- [Przewodnik administrowania SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
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
> Dla scenariuszy nieprodukcyjnych, użyj typy maszyn wirtualnych, które są wymienione w [Uwaga SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Użycie maszyn wirtualnych platformy Azure na potrzeby scenariuszy produkcyjnych, sprawdź, czy maszyny wirtualne w systemie SAP opublikowane z certyfikatem SAP HANA [listy certyfikowane platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Wdrażanie maszyn wirtualnych na platformie Azure przy użyciu:

- Witryna Azure portal.
- Polecenia cmdlet programu PowerShell platformy Azure.
- Interfejs wiersza polecenia platformy Azure.

Możesz również wdrożyć pełną zainstalowanej platformy SAP HANA w usługach maszyny Wirtualnej platformy Azure za pośrednictwem [platforma usług w chmurze SAP](https://cal.sap.com/). Proces instalacji został opisany w [wdrażanie rozwiązania SAP S/4HANA lub BW/4HANA na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) lub za pomocą usługi automation wydane [tutaj](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Wybierz typ usługi Azure Storage
Platforma Azure udostępnia dwa typy magazynów, które są odpowiednie na potrzeby maszyn wirtualnych platformy Azure, które są uruchomione oprogramowanie SAP HANA:

- [Usługa Azure Standard Storage](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Usługi Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Platforma Azure oferuje dwie metody wdrażania dla wirtualnych dysków twardych na Azure Standard i Premium Storage. Pozwala na ogólną scenariusz, korzystać z zalet [dysk zarządzany platformy Azure](https://azure.microsoft.com/services/managed-disks/) wdrożeń.

Aby uzyskać listę typów magazynów i umowach SLA, przepustowość operacji We/Wy i pamięci masowej, przejrzyj [dokumentacji platformy Azure dla dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Konfigurowanie magazynu dla maszyn wirtualnych platformy Azure

Ile zakupiono urządzenia platformy SAP HANA w środowisku lokalnym, nigdy nie było konieczne interesujące podsystemów we/wy i jego możliwości, ponieważ dostawcą urządzenia należy się upewnić, że spełnienia wymagań minimalny magazyn dla oprogramowania SAP HANA. Podczas tworzenia infrastruktury platformy Azure, samodzielnie, należy również mieć świadomość niektórych z tych wymagań można także zrozumienie wymagań dotyczących konfiguracji, które Radzimy zastosować w poniższych sekcjach. Lub w sytuacjach, gdy konfigurujesz maszyny wirtualne systemem SAP HANA. Niektóre właściwości, które zostaną poproszeni o powodujące konieczność:

- Włącz odczytu/zapisu na woluminie /hana/log 250 MB/s co najmniej o rozmiarze operacji We/Wy 1 MB
- Włącz co najmniej 400MB/s dla /hana/data dla 16 MB i 64 MB rozmiar operacji We/Wy odczytu aktywności
- Włącz działanie co najmniej 250MB/s dla /hana/data z 16 MB i 64 MB rozmiar operacji We/Wy zapisu

Biorąc pod uwagę, że magazyn niski czas oczekiwania jest krytyczny dla systemów DBMS, nawet gdy te, takich jak SAP HANA, przechowywać dane w pamięci. Ścieżki krytycznej w magazynie jest zazwyczaj wokół zapisu dziennika transakcji systemy DBMS. Można jednak również operacje, takie jak zapisywanie punktów zapisu lub ładowania danych w pamięci, po odzyskiwanie po awarii może być niezbędna. Dlatego jest wymagane, aby korzystać z dysków w warstwie Premium Azure /hana/data i /hana/log woluminów. Aby osiągnąć minimalna przepływność/hana/dziennika i danych/hana/zgodnie z potrzebami, SAP, potrzebne do tworzenia RAID 0 przy użyciu MDADM lub LVM przez wiele dysków Azure Premium Storage i korzystać z woluminów RAID jako/hana/dane i woluminy dziennika/hana /. Zgodnie z usługi stripe rozmiary RAID 0 zalecane jest użycie:

- 64K lub 128 KB danych/hana /
- 32 KB/hana/log

> [!NOTE]
> Nie ma potrzeby konfigurowania żadnych poziom nadmiarowości przy użyciu woluminów RAID, ponieważ magazynu Azure Premium i standardowa należy zachować trzy obrazy wirtualnego dysku twardego. Użycie woluminu RAID jest wyłącznie Konfigurowanie woluminów, które zapewniają wystarczającą przepływnością wejścia/wyjścia.

Grupowania liczba wirtualnych dysków twardych platformy Azure, poniżej RAID, nie jest kumulacyjne ze strony przepustowość operacji We/Wy i magazynu. Tak Jeżeli umieścisz RAID 0 za pośrednictwem 3 dyski P30 usługi Azure Premium Storage x go powinien zapewnić trzy razy na SEKUNDĘ i trzy razy przepływność magazynu jednego dysku usługi Azure Premium Storage P30.

Zalecenia dotyczące buforowania poniżej są przy założeniu charakterystyk we/wy platformy SAP Hana tej listy, takich jak:

- Istnieje bardzo trudno dowolnego obciążenia odczytu dla plików danych HANA. Wyjątki są duży rozmiar operacji We/Wy po ponownym uruchomieniu wystąpienia HANA lub ponowne uruchomienie maszyny Wirtualnej platformy Azure, gdy dane są ładowane do platformy HANA. Inną wielkość liter w większych odczytu operacji We/Wy na plikach danych może być kopie zapasowe bazy danych HANA. W rezultacie odczytu przede wszystkim buforowania nie mieć sensu, ponieważ w większości przypadków, wszystkie woluminy plików danych należy całkowicie odczytane.
- Zapisywanie względem plików danych mają bogate doświadczenie w wzrosty na podstawie punktów zapisu HANA i odzyskiwanie po awarii platformy HANA. Zapisywanie punktów zapisu jest asynchroniczny i nie wstrzymują wszystkich transakcji użytkownika. Zapisywania danych podczas odzyskiwania awaryjnego jest wydajność krytycznych, aby uruchomić system odpowiada szybko ponownie. Odzyskiwanie po awarii należy jednak sytuacjach wyjątkowych zamiast
- Istnieje bardzo trudno wszelkie odczyty z plików ponawiania platformy HANA. Wyjątki są duże operacji We/Wy podczas wykonywania kopii zapasowej z dziennika transakcji, odzyskiwanie po awarii, lub w fazie ponownego uruchamiania wystąpienie oprogramowania HANA.  
- Obciążenie głównego pliku dziennika ponawiania platformy SAP HANA jest zapisy. Zależy od rodzaju obciążenia, możesz mieć operacji We/Wy tak małej, jako 4 KB lub w innych przypadkach rozmiarów operacji We/Wy 1 MB lub większej. Zapisu opóźnienie przed dziennika ponawiania platformy SAP HANA jest wydajność krytycznych.
- Wszystkie operacje zapisu muszą zostać utrwalony na dysku w sposób niezawodny

W wyniku tych obserwowanych wzorce operacji We/Wy przez platformę SAP HANA buforowanie na różnych woluminach za pomocą usługi Azure Premium Storage należy ustawić takich jak:

- / hana/dane — Brak buforowania
- log — Brak buforowania — wyjątek/hana/serii M (zobacz w dalszej części tego dokumentu)
- /Hana/Shared - odczytu pamięci podręcznej


Również pamiętać ogólną przepustowość we/wy maszyny Wirtualnej podczas zmiany rozmiaru lub podejmowania decyzji dla maszyny Wirtualnej. Ogólną przepływność magazynu maszyny Wirtualnej jest udokumentowany w artykule [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem pamięci](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="cost-conscious-azure-storage-configuration"></a>Koszt świadome konfigurację usługi Azure Storage
W poniższej tabeli przedstawiono konfigurację typy maszyn wirtualnych, których klienci często używają do hosta SAP HANA na maszynach wirtualnych platformy Azure. Może to być pewnych typów maszyn wirtualnych, które nie spełnia wszystkie kryteria minimalnej platformy SAP Hana. Ale do tej pory sprawiał tych maszyn wirtualnych do wykonania prawidłowo dla scenariuszy nieprodukcyjnych. 

> [!NOTE]
> Na potrzeby scenariuszy produkcyjnych, sprawdź, czy typ maszyny Wirtualnej jest obsługiwana w przypadku oprogramowania SAP HANA SAP w [dokumentacji SAP IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | / hana/danych i dziennika/hana /<br /> Rozłożona z LVM lub MDADM | / hana/udostępnione | wolumin/root | / usr/sap | Hana/kopia zapasowa |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Dyski zalecane dla mniejszych maszyny Wirtualnej typów z 3 x P20 oversize woluminy dotyczące zalecenia miejsce zgodnie z opisem w [SAP TDI magazynu oficjalny dokument dotyczący](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Jednak wyboru wyświetlane w tabeli została wprowadzona w celu zapewnienia wystarczającej przepływności dysku dla oprogramowania SAP HANA. Jeśli potrzebujesz zmiany woluminów /hana/backup, co zostało o rozmiarze do przechowywania kopii zapasowych, które reprezentują dwa razy woluminie pamięci, swobodnie dostosować.   
Sprawdź, czy przepływności różnych woluminach sugerowane będzie spełniać obciążenia, które chcesz uruchomić. Jeśli obciążenie wymaga większej ilości /hana/data i /hana/log, należy zwiększyć liczbę Azure Premium Storage wirtualnych dysków twardych. Zmiany rozmiaru woluminu z kolejnych wirtualnych dysków twardych niż wymienione spowoduje zwiększenie przepływności operacji We/Wy i operacje We/Wy w ramach typu maszyny wirtualnej platformy Azure. 

> [!NOTE]
> Powyższej konfiguracji nie będą korzystać z [maszyny wirtualnej platformy Azure pojedynczy umowę SLA maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) ponieważ ona korzystać z usługi Azure Premium Storage i Azure Standard Storage. Jednak zaznaczenie została wybrana w celu optymalizacji kosztów.


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>Azure konfiguracji magazynu można korzystać za wynikających z jednej maszyny Wirtualnej umowy SLA
Jeśli chcesz korzystać z [maszyny wirtualnej platformy Azure pojedynczy umowę SLA maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), należy użyć usługi Azure Premium Storage wirtualne dyski twarde wyłącznie.

> [!NOTE]
> Na potrzeby scenariuszy produkcyjnych, sprawdź, czy typ maszyny Wirtualnej jest obsługiwana w przypadku oprogramowania SAP HANA SAP w [dokumentacji SAP IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | / hana/danych i dziennika/hana /<br /> Rozłożona z LVM lub MDADM | / hana/udostępnione | wolumin/root | / usr/sap | Hana/kopia zapasowa |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


Dyski zalecane dla mniejszych maszyny Wirtualnej typów z 3 x P20 oversize woluminy dotyczące zalecenia miejsce zgodnie z opisem w [SAP TDI magazynu oficjalny dokument dotyczący](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Jednak wyboru wyświetlane w tabeli została wprowadzona w celu zapewnienia wystarczającej przepływności dysku dla oprogramowania SAP HANA. Jeśli potrzebujesz zmiany woluminów /hana/backup, co zostało o rozmiarze do przechowywania kopii zapasowych, które reprezentują dwa razy woluminie pamięci, swobodnie dostosować.  
Sprawdź, czy przepływności różnych woluminach sugerowane będzie spełniać obciążenia, które chcesz uruchomić. Jeśli obciążenie wymaga większej ilości /hana/data i /hana/log, należy zwiększyć liczbę Azure Premium Storage wirtualnych dysków twardych. Zmiany rozmiaru woluminu z kolejnych wirtualnych dysków twardych niż wymienione spowoduje zwiększenie przepływności operacji We/Wy i operacje We/Wy w ramach typu maszyny wirtualnej platformy Azure. 



#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Rozwiązanie do magazynowania z akceleratorem zapisu platformy Azure dla maszyn wirtualnych serii M platformy Azure
Akcelerator zapisu platformy Azure jest funkcji, który jest wprowadzenie udostępniona dla maszyn wirtualnych serii M wyłącznie. Jak nazwy stanów, funkcji ma na celu poprawę czas oczekiwania operacji We/Wy zapisu względem usługi Azure Premium Storage. Dla oprogramowania SAP HANA akceleratorem zapisu powinien zostać użyty dla woluminu /hana/log tylko. W związku z tym konfiguracji przedstawionych do tej pory muszą zostać zmienione. Zmiany głównej jest rozpad między /hana/data i /hana/log, aby można było używać akcelerator zapisu platformy Azure w odniesieniu do wielkości /hana/log. 

> [!IMPORTANT]
> SAP HANA certyfikacji dla maszyn wirtualnych serii M Azure jest oparte wyłącznie na programie akcelerator zapisu platformy Azure dla woluminu /hana/log. W rezultacie produkcji scenariuszy SAP HANA wdrożenia na maszynach wirtualnych serii M Azure oczekuje się, można skonfigurować za pomocą akcelerator zapisu platformy Azure dla woluminu /hana/log.  

> [!NOTE]
> Na potrzeby scenariuszy produkcyjnych, sprawdź, czy typ maszyny Wirtualnej jest obsługiwana w przypadku oprogramowania SAP HANA SAP w [dokumentacji SAP IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Zalecane konfiguracje wyglądać następująco:

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | / hana/danych | / hana/log | / hana/udostępnione | wolumin/root | / usr/sap | Hana/kopia zapasowa |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Sprawdź, czy przepływności różnych woluminach sugerowane będzie spełniać obciążenia, które chcesz uruchomić. Jeśli obciążenie wymaga większej ilości /hana/data i /hana/log, należy zwiększyć liczbę Azure Premium Storage wirtualnych dysków twardych. Zmiany rozmiaru woluminu z kolejnych wirtualnych dysków twardych niż wymienione spowoduje zwiększenie przepływności operacji We/Wy i operacje We/Wy w ramach typu maszyny wirtualnej platformy Azure.

Akcelerator zapisu platformy Azure działa tylko w połączeniu z [usługi Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Dlatego co najmniej usługi Azure Premium Storage dyski tworzących woluminu /hana/log konieczne można wdrożyć jako dysków zarządzanych.

Istnieją limity usługi Azure Premium Storage wirtualne dyski twarde dla maszyny Wirtualnej, które mogą być obsługiwane przez akcelerator zapisu platformy Azure. Bieżące limity są:

- 16 dysków VHD dla M128xx maszyny Wirtualnej
- 8 wirtualnych dyskach twardych dla M64xx maszyny Wirtualnej
- 4 wirtualne dyski twarde dla M32xx maszyny Wirtualnej

Bardziej szczegółowe instrukcje na temat włączania akcelerator zapisu platformy Azure można znaleźć w artykule [akceleratorem zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Szczegółowe informacje i ograniczenia dotyczące akcelerator zapisu platformy Azure można znaleźć w dokumentacji tych samych.


### <a name="set-up-azure-virtual-networks"></a>Konfigurowanie sieci wirtualnych platformy Azure
Jeśli masz połączenie lokacja lokacja na platformie Azure za pośrednictwem sieci VPN lub usługi ExpressRoute, konieczne jest posiadanie co najmniej jeden [sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) połączoną za pośrednictwem bramy sieci wirtualnej z obwodem usługi VPN lub usługi ExpressRoute. Brama wirtualnej znajduje się w podsieci w sieci wirtualnej platformy Azure. Aby zainstalować oprogramowanie SAP HANA, należy utworzyć dwa dodatkowe podsieci w sieci wirtualnej. W jednej podsieci hostuje maszyny wirtualne do uruchamiania wystąpień oprogramowania SAP HANA. Innych podsieci jest uruchamiany serwer Przesiadkowy lub maszyny wirtualne zarządzania hosta SAP HANA Studio lub inne oprogramowanie do zarządzania.

Po zainstalowaniu maszyn wirtualnych do uruchamiania oprogramowania SAP HANA, potrzebne są maszyny wirtualne:

- Zainstalowane dwie wirtualne karty sieciowe: jedną kartą Sieciową, aby nawiązać połączenie z podsieci zarządzania i jedną kartą Sieciową, aby nawiązać połączenie z siecią lokalną lub innymi sieciami wystąpienie SAP HANA na maszynie Wirtualnej platformy Azure.
- Statyczne prywatne adresy IP, które zostały wdrożone dla obu wirtualnych kart sieciowych.

Aby uzyskać przegląd różnych metod do przypisywania adresów IP, zobacz [typy adresów IP i metody alokacji na platformie Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Dla maszyn wirtualnych z uruchamiania oprogramowania SAP HANA powinny działać przy użyciu statycznych adresów IP przypisane. Przyczyną jest to, że niektóre atrybuty konfiguracji platformy Hana odwoływać się do adresów IP.

[Usługa Azure sieciowych grup zabezpieczeń (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) są używane do kierowania ruchu, który jest kierowany do serwera Przesiadkowego lub wystąpienia platformy SAP HANA. Sieciowe grupy zabezpieczeń są skojarzone z podsiecią platformy SAP HANA i podsieci zarządzania.

Na poniższej ilustracji przedstawiono omówienie schematu nierównej wdrożenia platformy SAP Hana:

![Przybliżony wdrożenia schematu dla oprogramowania SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Aby wdrożyć oprogramowanie SAP HANA na platformie Azure bez połączenia lokacja lokacja, uzyskać dostęp do wystąpienie SAP HANA, chociaż publicznego adresu IP. Adres IP muszą być przypisane do maszyny Wirtualnej platformy Azure, która jest uruchomiona maszyna wirtualna serwera Przesiadkowego. W tym scenariuszu podstawowego wdrożenia zależy od systemu Azure wbudowanych usług DNS można rozpoznać nazwy hostów. W bardziej złożonym wdrożeniu, gdzie publicznych adresów IP są używane wbudowanej usługi DNS platformy Azure są szczególnie ważne. Użyj sieciowych grup zabezpieczeń usługi Azure, aby ograniczyć otwartych portów lub zakresów adresów IP, z którymi mogą nawiązywać połączenie w podsieci platformy Azure z zasobami, które ma publicznych adresów IP. Na poniższej ilustracji przedstawiono nierównej schematu do wdrażania oprogramowania SAP HANA bez połączenia lokacja lokacja:
  
![Przybliżony wdrożenia schematu dla oprogramowania SAP HANA bez połączenia lokacja lokacja](media/hana-vm-operations/hana-simple-networking2.PNG)
 


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
Jeśli pracujesz w systemie SUSE Linux 12 z dodatkiem SP1 lub nowszy, można ustanowić klastra program Pacemaker ze pomocą metody STONITH urządzeniami. Mogą używać urządzeń, aby ustawić konfigurację platformy SAP HANA, korzystającej z replikacji synchronicznej replikacji systemu HANA i automatycznej pracy awaryjnej. Aby uzyskać więcej informacji na temat procedury instalacyjnej, zobacz [przewodnik SAP HANA wysoką dostępność dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
