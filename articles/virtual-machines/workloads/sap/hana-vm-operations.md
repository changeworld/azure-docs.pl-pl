---
title: Operacje SAP HANA na platformie Azure | Dokumentacja firmy Microsoft
description: Przewodnik obsługi programu SAP HANA systemów, które zostały wdrożone na maszynach wirtualnych Azure.
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
ms.openlocfilehash: 61369fbf864db28ee0a9415bbb87dca2a185ed43
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809679"
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA w podręczniku obsługi platformy Azure
Ten dokument zawiera wskazówki dotyczące systemów SAP HANA, które zostały wdrożone na Azure macierzysty maszynach wirtualnych (VM). Ten dokument nie ma zastąpić dokumentacji SAP standardowe zawiera następującą zawartość:

- [Przewodnik administracji programu SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Przewodnik po instalacji programu SAP](https://service.sap.com/instguides)
- [Uwagi dotyczące SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć tego przewodnika, należy podstawową wiedzę na temat usługi Azure następujące składniki:

- [Maszyny wirtualne platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Sieć platformy Azure i sieci wirtualnych](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Aby dowiedzieć się więcej na temat programu SAP NetWeaver i inne składniki SAP na platformie Azure, zobacz [SAP na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) sekcji [dokumentacji platformy Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Zagadnienia dotyczące konfiguracji
W poniższych sekcjach opisano podstawowe ustawienia zagadnienia dotyczące wdrażania systemów SAP HANA na maszynach wirtualnych Azure.

### <a name="connect-into-azure-virtual-machines"></a>Połączenie z maszyn wirtualnych platformy Azure
Zgodnie z opisem w [maszyn wirtualnych platformy Azure przewodnik planowania](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), istnieją dwie podstawowe metody łączenia w maszynach wirtualnych platformy Azure:

- Łączenie się za pośrednictwem Internetu i publiczne punkty końcowe na maszynie Wirtualnej szybkiego dostępu lub na maszynie Wirtualnej z systemem SAP HANA.
- Łączenie się za pośrednictwem [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) lub Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Połączenie lokacja lokacja za pośrednictwem sieci VPN i ExpressRoute jest niezbędne w scenariuszach produkcji. Dla źródła danych w scenariuszach produkcji, gdzie jest używane oprogramowanie SAP scenariusze nieprodukcyjnych wymagany jest również połączenia tego typu. Na poniższej ilustracji przedstawiono przykład łączności między lokacjami:

![Łączność między lokacjami](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Wybierz typy maszyny Wirtualnej Azure
Typy maszyny Wirtualnej platformy Azure, które mogą być używane w scenariuszach produkcji są wymienione w [SAP dokumentacji IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). W przypadku scenariuszy nieprodukcyjnych różnych typach natywnych maszyny Wirtualnej platformy Azure jest dostępna.

>[!NOTE]
> W przypadku scenariuszy nieprodukcyjnych Użyj typów maszyny Wirtualnej, które są wymienione w [Uwaga SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Z użyciem maszyn wirtualnych platformy Azure dla scenariuszy produkcji, sprawdź, czy SAP HANA certyfikowane maszyn wirtualnych w SAP opublikowane [lista certyfikowanych platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Wdrażanie maszyn wirtualnych na platformie Azure przy użyciu:

- Portalu Azure.
- Polecenia cmdlet programu PowerShell systemu Azure.
- Interfejs wiersza polecenia platformy Azure.

Również można wdrożyć pełną zainstalowanej platformy SAP HANA na usługach maszyny Wirtualnej platformy Azure za pośrednictwem [platformy w chmurze SAP](https://cal.sap.com/). Proces instalacji jest opisana w [wdrożenia SAP S/4HANA lub BW/4HANA na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) lub przy użyciu automatyzacji wydane [tutaj](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Wybierz typ usługi Azure Storage
Platforma Azure oferuje dwa typy magazynu, które są odpowiednie dla maszyn wirtualnych platformy Azure, które działają SAP HANA:

- [Azure Standard Storage](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Magazyn w warstwie Premium systemu Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

System Azure oferuje dwie metody wdrażania dla dysków VHD na Azure Standard i Premium Storage. Jeśli zezwala na scenariusz ogólnej, skorzystać z [Azure zarządzanych dysku](https://azure.microsoft.com/services/managed-disks/) wdrożeń.

Lista typów magazynów i umowach SLA przepływność IOPS i pamięci masowej, [dokumentacji platformy Azure do zarządzanych dysków](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Konfigurowanie magazynu dla maszyn wirtualnych platformy Azure

Ile zakupiono SAP HANA urządzeń do lokalnego nigdy nie musieli interesujących podsystemów we/wy i możliwości, ponieważ dostawcą urządzenia musi się upewnić, że spełnienie wymagań minimalnych magazynu SAP HANA. Podczas tworzenia infrastruktury platformy Azure, samodzielnie, również należy zwrócić uwagę niektóre z tych wymagań można także poznać wymagania dotyczące konfiguracji, które Radzimy zastosować w poniższych sekcjach. Lub w przypadku których konfigurujesz maszyny wirtualne mają systemie SAP HANA. Niektóre właściwości, które są proszeni powodujące konieczność:

- Włącz odczytu/zapisu woluminu na /hana/log 250 MB/s co najmniej o rozmiarze we/wy 1 MB
- Włącz co najmniej 400MB/s dla /hana/data do 16 MB i 64 MB rozmiar operacji We/Wy odczytu działania
- Włącz działania zapisu co najmniej 250MB/s dla /hana/data z 16 MB i 64 MB rozmiar operacji We/Wy

Podany niski magazynu opóźnienia jest krytyczne dla systemów DBMS również w tych, takie jak SAP HANA przechowywać dane w pamięci. Ścieżka krytyczna w magazynie jest zwykle wokół zapisy dziennika transakcji systemu DBMS. Jednak również operacji, takich jak zapisywanie punktów zapisu lub ładowania danych w pamięci po odzyskiwanie po awarii może mieć kluczowe znaczenie. W związku z tym jest to konieczne do wykorzystania dysków Premium Azure /hana/data i /hana/log woluminów. Aby osiągnąć minimalną przepustowość/hana/dzienników i danych/hana/wymaganą przez SAP, należy utworzyć RAID 0 przy użyciu MDADM lub LVM przez wiele dysków Azure Premium Storage oraz używanie woluminów RAID/hana/dane i woluminy dziennika/hana /. Jako stripe rozmiary macierzy RAID 0 zalecane jest użycie:

- 64 KB lub 128 KB/hana/danych
- 32 KB/hana/dziennika

> [!NOTE]
> Nie trzeba skonfigurować wszelkie poziom nadmiarowości za pomocą woluminów RAID, ponieważ Azure Premium i standardowa magazynu zachować trzy obrazy dysku VHD. Użycie woluminu RAID jest całkowicie skonfigurowanie woluminów, które zapewniają wystarczającą przepustowość we/wy.

Grupowania liczba wirtualnych dysków twardych Azure pod RAID, jest kumulacyjne z strony przepływności IOPS i magazynu. Tak Jeśli umieścisz RAID 0 za pośrednictwem 3 x P30 Azure Premium Storage dyski go powinien zapewnić trzy razy IOPS i trzy razy przepływność magazynu Azure Premium magazynu P30 jednego dysku.

Nie Konfiguruj buforowanie na dyskach /hana/data i /hana/log magazyn w warstwie Premium. Wszystkie dyski tworzenia tych woluminów powinien mieć buforowanie tych dysków wartość "None".

Należy również ogólną przepustowość operacji We/Wy maszyny Wirtualnej pamiętać podczas zmiany rozmiaru lub podejmowania decyzji o dla maszyny Wirtualnej. Ogólne przepływności magazynu maszyny Wirtualnej jest udokumentowane w artykule [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem pamięci](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="cost-conscious-azure-storage-configuration"></a>Koszt świadome konfiguracji usługi Azure Storage
W poniższej tabeli przedstawiono konfigurację typach maszyn wirtualnych, których klienci często używają do hosta SAP HANA na maszynach wirtualnych Azure. Może to być niektórych typów maszyny Wirtualnej, które nie mogą spełniać wszystkie kryteria minimalne SAP HANA. Ale do tej pory sprawiał tych maszyn wirtualnych do prawidłowo wykonywania nieprodukcyjnych scenariuszach. 

> [!NOTE]
> W scenariuszach produkcji, sprawdź, czy określonego typu maszyny Wirtualnej jest obsługiwana dla SAP HANA przez SAP w [SAP dokumentacji IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z MASZYNA WIRTUALNA WE/WY<br /> Przepływność | / hana/danych i dziennika/hana /<br /> paski LVM lub MDADM | / hana/udostępnionych | wolumin/root | / usr/sap | Hana lub tworzenia kopii zapasowej |
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


Dyski zalecane dla mniejszych wirtualna typów z 3 x P20 oversize woluminów dotyczących zalecenia miejsca zgodnie z [SAP TDI magazynu w oficjalnym dokumencie](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Jednak wyboru wyświetlane w tabeli zostało przesłane do zapewnienia wystarczającej przepustowości dysku SAP HANA. Jeśli potrzebne zmiany w woluminie /hana/backup, który został o rozmiarze do przechowywania kopii zapasowych, które reprezentują dwukrotność rozmiaru pamięci, możesz dostosować.   
Sprawdź, czy przepływność magazynu dla różnych woluminach sugerowane spełniającą obciążeniem, które chcesz uruchomić. Jeśli obciążenie wymaga większej ilości /hana/data i /hana/log, należy zwiększyć liczbę Azure Premium magazynu wirtualnych dysków twardych. Zmiany rozmiaru woluminu z VHD więcej niż wymienione spowoduje zwiększenie przepływności IOPS i we/wy w ramach typu maszyny wirtualnej platformy Azure. 

> [!NOTE]
> Powyższej konfiguracji nie może korzystać z [maszyny wirtualnej platformy Azure z jednym umowy SLA dla maszyny Wirtualnej](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) od użycia usługi Azure Premium Storage i Azure Standard Storage. Wybrano, aby optymalizować koszty zaznaczenia.


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>Azure konfiguracji magazynu korzyści najlepiej spełniającej pojedynczego umowy SLA dla maszyny Wirtualnej
Jeśli chcesz korzystać z [maszyny wirtualnej platformy Azure z jednym umowy SLA dla maszyny Wirtualnej](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), należy używać wyłącznie Azure Premium magazynu wirtualnych dysków twardych.

> [!NOTE]
> W scenariuszach produkcji, sprawdź, czy określonego typu maszyny Wirtualnej jest obsługiwana dla SAP HANA przez SAP w [SAP dokumentacji IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z MASZYNA WIRTUALNA WE/WY<br /> Przepływność | / hana/danych i dziennika/hana /<br /> paski LVM lub MDADM | / hana/udostępnionych | wolumin/root | / usr/sap | Hana lub tworzenia kopii zapasowej |
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


Dyski zalecane dla mniejszych wirtualna typów z 3 x P20 oversize woluminów dotyczących zalecenia miejsca zgodnie z [SAP TDI magazynu w oficjalnym dokumencie](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Jednak wyboru wyświetlane w tabeli zostało przesłane do zapewnienia wystarczającej przepustowości dysku SAP HANA. Jeśli potrzebne zmiany w woluminie /hana/backup, który został o rozmiarze do przechowywania kopii zapasowych, które reprezentują dwukrotność rozmiaru pamięci, możesz dostosować.  
Sprawdź, czy przepływność magazynu dla różnych woluminach sugerowane spełniającą obciążeniem, które chcesz uruchomić. Jeśli obciążenie wymaga większej ilości /hana/data i /hana/log, należy zwiększyć liczbę Azure Premium magazynu wirtualnych dysków twardych. Zmiany rozmiaru woluminu z VHD więcej niż wymienione spowoduje zwiększenie przepływności IOPS i we/wy w ramach typu maszyny wirtualnej platformy Azure. 



#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Rozwiązanie magazynu z akceleratora zapisu Azure dla maszyn wirtualnych Azure M-Series
Azure akceleratora zapisu jest funkcje, które jest pierwsze wprowadzanie dla maszyn wirtualnych, M serii wyłącznie. Jako nazwa stany, celem funkcji jest zwiększyć czas oczekiwania operacji We/Wy zapisu w usłudze Azure Premium Storage. Dla SAP HANA powinien można użyć w odniesieniu do woluminu /hana/log tylko zapisu akceleratora. W związku z tym konfiguracji przedstawionych wykonanej do tej pory muszą zostać zmienione. Głównym zmiana jest rozpad między /hana/data i /hana/log, aby można było używać akceleratora zapisu Azure dla woluminu /hana/log tylko. 

> [!IMPORTANT]
> Certyfikacja SAP HANA dla maszyn wirtualnych Azure serii M jest wyłącznie z akceleratora zapisu Azure dla woluminu /hana/log. W związku z tym można skonfigurować za pomocą klawiszy skrótów zapisu Azure dla woluminu /hana/log powinny produkcji scenariuszy SAP HANA wdrożenia na maszynach wirtualnych Azure M serii.  

> [!NOTE]
> W scenariuszach produkcji, sprawdź, czy określonego typu maszyny Wirtualnej jest obsługiwana dla SAP HANA przez SAP w [SAP dokumentacji IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Zalecane konfiguracje następującą postać:

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z MASZYNA WIRTUALNA WE/WY<br /> Przepływność | / hana/danych | / hana/dziennika | / hana/udostępnionych | wolumin/root | / usr/sap | Hana lub tworzenia kopii zapasowej |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Sprawdź, czy przepływność magazynu dla różnych woluminach sugerowane spełniającą obciążeniem, które chcesz uruchomić. Jeśli obciążenie wymaga większej ilości /hana/data i /hana/log, należy zwiększyć liczbę Azure Premium magazynu wirtualnych dysków twardych. Zmiany rozmiaru woluminu z VHD więcej niż wymienione spowoduje zwiększenie przepływności IOPS i we/wy w ramach typu maszyny wirtualnej platformy Azure.

Azure akceleratora zapisu działa tylko w połączeniu z [dyskach zarządzanych Azure](https://azure.microsoft.com/services/managed-disks/). Dlatego co najmniej Azure Premium Storage dyski tworzące woluminu /hana/log konieczne można wdrożyć jako dyski zarządzanych.

Istnieją ograniczenia Azure Premium magazynu wirtualne dyski twarde dla maszyny Wirtualnej, która może być obsługiwana przez akceleratora zapisu Azure. Bieżące ograniczenia są:

- 16 dysków VHD dla M128xx maszyny Wirtualnej
- 8 dysków VHD dla M64xx maszyny Wirtualnej
- 4 wirtualne dyski twarde dla M32xx maszyny Wirtualnej

Bardziej szczegółowe instrukcje dotyczące włączania akceleratora zapisu Azure można znaleźć w artykule [zapisu akceleratora](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Szczegółowe informacje i ograniczenia dotyczące akceleratora zapisu Azure znajdują się w tej samej dokumentacji.


### <a name="set-up-azure-virtual-networks"></a>Konfigurowanie sieci wirtualnych platformy Azure
Gdy masz połączenie lokacja lokacja na platformie Azure za pośrednictwem sieci VPN lub usługi ExpressRoute, musi mieć co najmniej jeden [sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) podłączonego za pośrednictwem bramy sieci wirtualnej z obwodem sieci VPN lub usługi ExpressRoute. Wirtualna brama znajduje się w podsieci sieci wirtualnej platformy Azure. Aby zainstalować SAP HANA, należy utworzyć dwa dodatkowe podsieci w sieci wirtualnej. W jednej podsieci hosty maszyn wirtualnych do uruchomienia wystąpień SAP HANA. Inne podsieci uruchamia Jumpbox lub zarządzania maszyn wirtualnych do hostów SAP HANA Studio lub inne oprogramowanie do zarządzania.

Po zainstalowaniu maszyn wirtualnych do uruchomienia SAP HANA maszyn wirtualnych, należy:

- Zainstalowane dwie wirtualne karty sieciowe: jedną kartę Sieciową do nawiązania połączenia z podsiecią zarządzania i jednej karcie Sieciowej do połączenia z siecią lokalną lub innych sieci wystąpieniem SAP HANA w maszynie Wirtualnej platformy Azure.
- Statyczne prywatne adresy IP, które są wdrażane dla obu wirtualnych kart sieciowych.

Aby uzyskać przegląd różnych metod do przypisywania adresów IP, zobacz [adresów IP, typy i metody alokacji na platformie Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Dla maszyn wirtualnych pracujących SAP HANA powinien współpracować z statyczne adresy IP przypisane. Przyczyną jest to, czy niektóre atrybuty konfiguracji HANA odwołują się adresy IP.

[Azure grup zabezpieczeń sieci (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) służą do kierowania ruchu, który jest kierowany do Jumpbox lub wystąpienia SAP HANA. Grupy NSG są skojarzone z podsiecią SAP HANA i podsieci zarządzania.

Na poniższej ilustracji przedstawiono omówienie wdrożenia nierównej schematu dla programu SAP HANA:

![Schemat nierównej wdrożenia SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Aby wdrożyć SAP HANA na platformie Azure, bez połączenia lokacja lokacja, dostępu do wystąpienia SAP HANA, chociaż publicznego adresu IP. Adres IP muszą być przypisane do działającej maszyny Wirtualnej Jumpbox maszyny Wirtualnej Azure. W tym scenariuszu podstawowego wdrożenia zależy od Azure wbudowanych usług DNS do rozpoznawania nazwy hostów. W przypadku bardziej złożonych wdrożenia gdzie publicznych adresów IP są używane usługi Azure DNS wbudowanych są szczególnie istotne. Grupy NSG Azure umożliwia ograniczenie otwartych portów lub zakresów adresów IP, które mogą nawiązywać połączenia w podsieci platformy Azure z zasobów, które mają publicznych adresów IP. Na poniższej ilustracji przedstawiono nierównej schema wdrażania SAP HANA bez połączenia lokacja lokacja:
  
![Schemat nierównej wdrożenia SAP HANA bez połączenia lokacja lokacja](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operacje wdrażania SAP HANA na maszynach wirtualnych Azure
W poniższych sekcjach opisano niektóre operacje związane z wdrażaniem systemów SAP HANA na maszynach wirtualnych platformy Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Tworzenie kopii zapasowej i przywracanie operacji na maszynach wirtualnych Azure
Poniższe dokumenty zawierają opis kopii zapasowej i przywracania wdrożenia SAP HANA:

- [Omówienie kopii zapasowych oprogramowania SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA poziomie plików z kopii zapasowej](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA pamięci masowej migawki testu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Uruchom i ponowne uruchomienie maszyn wirtualnych, które zawierają SAP HANA
Funkcja poświęcone chmurze publicznej Azure jest że są pobierane tylko w przypadku sieci komputerowych minut. Na przykład podczas zamykania maszyny Wirtualnej, który działa SAP HANA opłaty są naliczane tylko w przypadku kosztów magazynowania w tym czasie. Inna funkcja jest dostępna po określeniu statycznych adresów IP dla maszyn wirtualnych we wdrożeniu wstępnym. Po ponownym uruchomieniu maszyny Wirtualnej, która ma SAP HANA maszyny Wirtualnej jest uruchamiany z jego wcześniejszego adresów IP. 


### <a name="use-saprouter-for-sap-remote-support"></a>Użyj SAProuter obsługę zdalnego SAP
Jeśli połączenie lokacja lokacja między lokalnymi lokalizacji i Azure i używasz składniki SAP, jest prawdopodobnie już uruchomione SAProuter. W takim przypadku należy wykonać następujące elementy dla zdalnej pomocy technicznej:

- Obsługa prywatnych i statyczny adres IP maszyny Wirtualnej w konfiguracji SAProuter hosta SAP HANA.
- Skonfiguruj grupy NSG podsieci, który jest hostem maszyny Wirtualnej HANA, aby zezwalać na ruch przez TCP/IP port 3299.

Jeśli łączysz się Azure za pośrednictwem Internetu, a nie masz SAP router dla maszyny Wirtualnej z SAP HANA, następnie należy zainstalować składnik. Zainstaluj SAProuter w oddzielnych maszyny Wirtualnej w ramach zarządzania podsieci. Na poniższej ilustracji przedstawiono nierównej schema wdrażania SAP HANA bez połączenia lokacja lokacja i SAProuter:

![Nieuprawianych schema wdrażania dla SAP HANA bez połączenia lokacja lokacja i SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Pamiętaj zainstalować SAProuter w oddzielnych maszyny Wirtualnej, a nie w Jumpbox maszyny Wirtualnej. Oddzielne maszyna wirtualna musi mieć statyczny adres IP. Aby połączyć Twoje SAProuter SAProuter, która jest hostowana przez SAP, skontaktuj się z programu SAP adresu IP. (SAProuter, która jest hostowana przez SAP jest odpowiednikiem wystąpienia SAProuter, które należy zainstalować na maszynie Wirtualnej). Umożliwia skonfigurowanie wystąpienia SAProuter adres IP z SAP. W ustawieniach konfiguracji portu tylko niezbędne jest TCP port 3299.

Aby uzyskać więcej informacji na temat konfiguracji i utrzymania połączenia zdalnej pomocy technicznej za pośrednictwem SAProuter, zobacz [dokumentacji SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Wysokiej dostępności z SAP HANA na maszynach wirtualnych Azure natywnego
Jeśli pracujesz w systemie SUSE Linux 12 z dodatkiem SP1 lub później, można utworzyć klastra rozrusznik z urządzeniami STONITH. Aby ustawić konfigurację SAP HANA, korzystającego z replikacji synchronicznej replikacji systemu HANA i automatycznej pracy awaryjnej, mogą używać urządzeń. Aby uzyskać więcej informacji na temat procedury instalacji, zobacz [przewodnik SAP HANA wysokiej dostępności dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
