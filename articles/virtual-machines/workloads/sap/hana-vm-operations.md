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
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: acccc553c5b63b2acd0f9793b0397b25145449dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477440"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Konfiguracje infrastruktury SAP HANA i operacje na platformie Azure
Ten artykuł zawiera wskazówki dotyczące sposobu konfigurowania infrastruktury platformy Azure i obsługi systemów SAP HANA, które zostały wdrożone na natywnych maszynach wirtualnych platformy Azure (maszyny wirtualne). Ten artykuł zawiera także informacje o konfiguracji dla oprogramowania SAP HANA skalowalnego w poziomie dla jednostki SKU maszyny Wirtualnej M128s. W tym artykule nie ma na celu zastąpienia dokumentacji SAP standardowa obejmuje następującą zawartością:

- [Przewodnik administrowania SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Przewodników po instalacji SAP](https://service.sap.com/instguides)
- [Uwagi SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć tego przewodnika, potrzebujesz podstawową wiedzę na temat następujących składników platformy Azure:

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Sieci platformy Azure i sieciami wirtualnymi](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Aby dowiedzieć się więcej na temat oprogramowania SAP NetWeaver i inne składniki SAP na platformie Azure, zobacz [SAP na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) części [dokumentacji platformy Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Zagadnienia dotyczące konfiguracja podstawowa
Konfiguracja podstawowa zagadnienia dotyczące wdrażania systemów SAP HANA na maszynach wirtualnych platformy Azure można znaleźć w poniższych sekcjach.

### <a name="connect-to-azure-virtual-machines"></a>Łączenie maszyn wirtualnych platformy Azure
Zgodnie z opisem w [maszyn wirtualnych platformy Azure Podręcznik planowania](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), dwie podstawowe metody są używane do łączenia maszynach wirtualnych platformy Azure:

- Łączenie się za pośrednictwem Internetu oraz publiczne punkty końcowe na maszynie Wirtualnej serwera Przesiadkowego, lub na maszynie Wirtualnej z systemem SAP HANA.
- Łączenie się za pośrednictwem [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) lub na platformie Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Połączeń lokacja lokacja za pomocą sieci VPN lub usługi ExpressRoute jest wymagane na potrzeby scenariuszy produkcyjnych. Ten typ połączenia jest również niezbędny do obniżenia scenariuszy, w których źródła danych do scenariuszy produkcyjnych oprogramowania SAP jest używane w sytuacji. Na poniższej ilustracji przedstawiono przykład korzystać z łączności między lokacjami:

![Łączność między lokacjami](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Wybierz typy maszyn wirtualnych platformy Azure
Typy maszyn wirtualnych platformy Azure na potrzeby scenariuszy produkcyjnych są wymienione w [dokumentacji SAP IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). W przypadku scenariuszy obniżenia szerszy zakres natywnych typów maszyn wirtualnych platformy Azure jest dostępna.

>[!NOTE]
> W przypadku scenariuszy obniżenia korzystaj typy maszyn wirtualnych, które są wymienione w [1928533 # Uwaga SAP](https://launchpad.support.sap.com/#/notes/1928533). Korzystanie z maszyn wirtualnych platformy Azure na potrzeby scenariuszy produkcyjnych, sprawdź, czy maszyny wirtualne w systemie SAP opublikowane z certyfikatem SAP HANA [certyfikowane listy platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Aby wdrożyć maszyny wirtualne na platformie Azure, należy użyć:

- Witryna Azure portal.
- Polecenia cmdlet programu PowerShell platformy Azure.
- Interfejs wiersza polecenia platformy Azure.

Możesz również wdrożyć pełną zainstalowanej platformy SAP HANA w usługach maszyny Wirtualnej platformy Azure za pośrednictwem [platformy SAP cloud platform](https://cal.sap.com/). Aby uzyskać informacje na temat procesu instalacji, zobacz [wdrażanie rozwiązania SAP S/4HANA lub BW/4HANA na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Informacje na temat automatyzacji ogólnie można zobaczyć [w tym artykule w witrynie GitHub](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-an-azure-storage-type"></a>Wybierz typ usługi Azure Storage
Platforma Azure udostępnia dwa typy magazynów, które są odpowiednie na potrzeby maszyn wirtualnych platformy Azure, która uruchamiaj oprogramowanie SAP HANA:  

- Standardowych dysków twardych (HDD)
- Dysków SSD w warstwie Premium (SSD)

Aby dowiedzieć się więcej na temat tych typów dysków, zobacz [wybierz typ dysku](../../windows/disks-types.md).

Platforma Azure oferuje dwie metody wdrażania dla wirtualnych dysków twardych na magazynu platformy Azure w warstwie standardowa i premium storage. Pozwala na ogólną scenariusz, korzystać z zalet [dysk zarządzany platformy Azure](https://azure.microsoft.com/services/managed-disks/) wdrożeń.

Aby uzyskać listę typów magazynów i umowach SLA, przepustowość operacji We/Wy i magazynu, zobacz [dokumentacji platformy Azure dla dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configure-the-storage-for-azure-virtual-machines"></a>Konfigurowanie magazynu dla maszyn wirtualnych platformy Azure

Użytkownik najprawdopodobniej nigdy nie niezależnej o podsystemów we/wy i możliwości ponieważ dostawcą urządzenia upewnienie się, że wymagania dotyczące magazynu minimalne zostały spełnione dla oprogramowania SAP HANA. Podczas tworzenia infrastruktury platformy Azure, samodzielnie, należy mieć świadomość niektóre z tych wymagań. Należy również uwzględnić wymagania dotyczące konfiguracji sugerowane w poniższych sekcjach. W przypadkach, w którym możesz skonfigurować maszyny wirtualne mają platformy SAP HANA do uruchomienia, konieczne może być:

- Włącz odczytu/zapisu na woluminie /hana/log co najmniej 250 MB/s dla rozmiarów operacji We/Wy 1 MB.
- Włącz co najmniej 400 MB/s dla /hana/data dla 16 MB i 64 MB rozmiar operacji We/Wy odczytu aktywności.
- Włącz zapisu działania co najmniej 250 MB/s dla /hana/data z 16 MB i 64 MB rozmiar operacji We/Wy.

Pamięci masowej niskiego opóźnienia jest krytyczny dla systemów DBMS, mimo że DBMS, takich jak SAP HANA przechowuje dane w pamięci. Ścieżki krytycznej w magazynie jest zazwyczaj wokół zapisu dziennika transakcji systemy DBMS. Jednak operacje, takie jak zapisywanie punktów zapisu lub ładowania danych w pamięci, po odzyskiwanie po awarii może być również niezbędna. 

Korzystanie z dysków magazynu premium platformy Azure dla woluminów /hana/data i /hana/log jest obowiązkowy. Aby osiągnąć minimalna przepływność /hana/log i /hana/data zgodnie z potrzebami, SAP, twórz RAID 0 przy użyciu mdadm lub Menedżer woluminów logicznych (LVM) za pośrednictwem wielu dysków magazynu Azure premium storage. Również użyć woluminów RAID jako /hana/data i /hana/log woluminów. Firma Microsoft zaleca, użyj następujących rozmiarów stripe macierzy RAID 0:

- 64 KB lub 128 KB danych/hana /
- 32 KB dla dziennika/hana /

> [!NOTE]
> Nie ma potrzeby konfigurowania żadnych poziom nadmiarowości przy użyciu woluminów RAID, ponieważ magazynu Azure premium i standardowa należy zachować trzy obrazy wirtualnego dysku twardego. Korzystanie z woluminu RAID jest wyłącznie Konfigurowanie woluminów, które zapewniają wystarczającą przepływnością wejścia/wyjścia.

Liczba wirtualnych dysków twardych platformy Azure, poniżej RAID grupowania nie jest kumulacyjne ze strony przepustowość operacji We/Wy i magazynu. Jeśli umieścisz RAID 0 za pośrednictwem 3 dysków magazynu x P30 Azure premium storage udostępnia trzy razy na SEKUNDĘ i trzy razy przepływności dysku jednej usługi Azure premium storage P30.

Poniższe zalecenia buforowania założono charakterystyk we/wy platformy SAP Hana:

- Istnieje bardzo trudno dowolnego obciążenia odczytu względem plików danych HANA. Wyjątki są duży rozmiar operacji We/Wy, po ponownym uruchomieniu wystąpienie oprogramowania HANA, lub gdy dane są ładowane do platformy HANA. Inną wielkość liter w większych odczytu operacji We/Wy na plikach danych może być kopie zapasowe bazy danych HANA. W rezultacie buforowanie odczytu nie ma sensu ponieważ w większości przypadków, wszystkie woluminy plik danych musi być odczytywana całkowicie.
- Zapisywanie względem plików danych mają bogate doświadczenie w wzrosty na podstawie punktów zapisu HANA i odzyskiwanie po awarii platformy HANA. Zapisywanie punktów zapisu jest asynchroniczny i nie wstrzymać wszystkich transakcji użytkownika. Zapisywania danych podczas odzyskiwania awaryjnego jest wydajność krytycznych, aby uruchomić system odpowiada szybko ponownie. Odzyskiwanie po awarii powinna być raczej wyjątkowych sytuacji.
- Istnieje bardzo trudno wszelkie odczyty z plików ponawiania platformy HANA. Wyjątki są duże operacji We/Wy, gdy wykonujesz kopie zapasowe dziennika transakcji, odzyskiwanie po awarii lub fazy ponownego uruchamiania wystąpienie oprogramowania HANA.  
- Obciążenie główny plik dziennika ponawiania platformy SAP HANA jest zapisy. W zależności od charakteru obciążenie może mieć operacji We/Wy tak małej, jako 4 KB, lub w innych przypadkach operacji We/Wy rozmiar 1 MB lub większej. Zapisu opóźnienie przed dziennika ponawiania platformy SAP HANA jest wydajność krytycznych.
- Wszystkie operacje zapisu musi zostać utrwalony na dysku w sposób niezawodny.

W wyniku tych obserwowanych wzorce operacji We/Wy przez platformę SAP HANA należy ustawić buforowania dla różnych woluminach, które używają usługi Azure premium storage do:

- **dane/hana/**: Brak buforowania.
- **/ hana/log**: Brak buforowania, z wyjątkiem w przypadku maszyn wirtualnych serii M (zobacz kolejnych w przyszłości, w tym artykule).
- **/ hana/udostępnione**: Buforowania zapisu.


Ponadto należy pamiętać, ogólną przepustowość we/wy maszyny Wirtualnej, gdy rozmiar lub zdecydować się na maszynie Wirtualnej. Ogólną przepływność magazynu maszyny Wirtualnej jest udokumentowany w [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem pamięci](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="linux-io-scheduler-mode"></a>Tryb harmonogram We/Wy systemu Linux
Linux ma kilku różnych trybach planowania operacji We/Wy. Ogólne zalecenie dostawcy systemu Linux i SAP jest ustawiony tryb harmonogram we/wy dla woluminów dysku z **cfq** tryb **aktualizujący nie działa** trybu. Aby uzyskać więcej informacji, zobacz [1984798 # Uwaga SAP](https://launchpad.support.sap.com/#/notes/1984787). 


#### <a name="storage-solution-with-write-accelerator-for-azure-m-series-virtual-machines"></a>Rozwiązanie do magazynowania z akceleratorem zapisu na platformie Azure maszyn wirtualnych z serii M
 Akcelerator to nowa funkcja platformy Azure, która wprowadza dla maszyn wirtualnych platformy Azure serii M wyłącznie zapisu. Funkcjonalność ma na celu poprawę opóźnień We/Wy zapisu względem usługi Azure premium storage. Dla oprogramowania SAP HANA akceleratorem zapisu powinien zostać użyty dla woluminu /hana/log tylko. Z tego powodu można zmienić konfiguracji przedstawionych do tej pory. Zmiany głównej jest rozpad między /hana/data i /hana/log, aby można było używać akceleratorem zapisu w odniesieniu do wielkości /hana/log. 

> [!IMPORTANT]
> Certyfikacja platformy SAP HANA na platformie Azure maszyn wirtualnych jest dostępna wyłącznie dla akceleratorem zapisu dla woluminu dziennika/hana/serii M. Dzięki temu usługa scenariuszu produkcyjnym wdrożenia oprogramowania SAP HANA na platformie Azure maszyn wirtualnych powinny być skonfigurowane z akceleratorem zapisu dla/hana/serii M dziennika woluminu.

> [!NOTE]
> Na potrzeby scenariuszy produkcyjnych, sprawdź, czy typ maszyny Wirtualnej jest obsługiwana w przypadku oprogramowania SAP HANA SAP w [dokumentacji SAP IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

W poniższej tabeli przedstawiono zalecane konfiguracje.

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalna maszyny Wirtualnej we/wy<br /> Przepływność | /hana/data | / hana/log | / hana/udostępnione | wolumin/root | /usr/sap | Hana/kopia zapasowa |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 giB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1,750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 giB | 2000 MB na sekundę |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 giB | 2000 MB na sekundę | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Sprawdź, czy przepływności różnych woluminach sugerowane spełnia obciążenia, które chcesz uruchomić. Jeśli obciążenie wymaga większej ilości /hana/data i /hana/log, należy zwiększyć liczbę usługi Azure premium storage wirtualnych dysków twardych. Zmiany rozmiaru woluminu z kolejnych wirtualnych dysków twardych niż wymienione zwiększa operacje We/Wy i przepływność operacji We/Wy w ramach typu maszyny wirtualnej platformy Azure.

Zapis akceleratora działa tylko w połączeniu z [usługi Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Co najmniej Azure dysków magazynu premium storage, które tworzą woluminu /hana/log musi zostać wdrożony jako dysków zarządzanych.

Istnieją limity dla usługi Azure premium storage wirtualne dyski twarde dla maszyny Wirtualnej, który może obsługiwać akceleratorem zapisu. Bieżące limity są:

- 16 dysków VHD dla M128xx maszyny Wirtualnej.
- 8 wirtualnych dyskach twardych dla M64xx maszyny Wirtualnej.
- 4 wirtualne dyski twarde dla M32xx maszyny Wirtualnej.

Aby uzyskać więcej informacji o sposobie włączania akceleratorem zapisu, zobacz [akceleratorem zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Więcej informacji na temat i ograniczenia dotyczące akceleratorem zapisu można znaleźć w dokumentacji tych samych.


#### <a name="cost-conscious-azure-storage-configuration"></a>Koszt świadome konfigurację usługi Azure Storage
W poniższej tabeli przedstawiono konfigurację typy maszyn wirtualnych, których klienci często używają do hosta SAP HANA na maszynach wirtualnych platformy Azure. Być może niektóre typy maszyn wirtualnych, które nie spełniają wszystkie kryteria minimalnej platformy SAP Hana. Może istnieć pewne typy maszyn wirtualnych, które oficjalnie nie są obsługiwane z platformą SAP HANA, SAP. Do tej pory tych maszyn wirtualnych wykonano prawidłowo obniżenia scenariuszach. 

> [!NOTE]
> Na potrzeby scenariuszy produkcyjnych, sprawdź, czy typ maszyny Wirtualnej jest obsługiwana w przypadku oprogramowania SAP HANA SAP w [dokumentacji SAP IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalna maszyny Wirtualnej we/wy<br /> Przepływność | / hana/danych i dziennika/hana /<br /> Rozłożona z LVM lub mdadm | / hana/udostępnione | wolumin/root | /usr/sap | Hana/kopia zapasowa |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB na sekundę | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1000 giB | 1000 MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1,750 GiB | 1000 MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 giB | 2000 MB na sekundę |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 giB | 2000 MB na sekundę | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Dyski, które są zalecane dla mniejszych maszyny Wirtualnej typów z 3 x P20 oversize woluminy rozumieniu zalecenia miejsca, które są określone w [oficjalny dokument dotyczący SAP TDI magazynu](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Wybór tabeli została wprowadzona do zapewnienia wystarczającej przepływności dysku platformy SAP Hana. Jeśli zachodzi potrzeba zmiany **/hana/kopia zapasowa** wolumin, który został tak zaprojektowany, aby przechowywać kopie zapasowe, które reprezentują dwa razy woluminie pamięci, możesz wprowadzać zmiany. 

Sprawdź, czy przepływności różnych woluminach sugerowane spełnia obciążenia, które chcesz uruchomić. Jeśli obciążenie wymaga większej ilości /hana/data i /hana/log, należy zwiększyć liczbę usługi Azure premium storage wirtualnych dysków twardych. Zmiany rozmiaru woluminu z kolejnych wirtualnych dysków twardych niż wymienione zwiększa operacje We/Wy i przepływność operacji We/Wy w ramach typu maszyny wirtualnej platformy Azure. 

> [!NOTE]
> Poprzednie konfiguracje nie korzystają z [maszyny wirtualnej platformy Azure pojedynczy umowę SLA maszyn wirtualnych](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) ponieważ używa kombinacji usługi Azure premium storage i Azure standard storage. Wybór została wybrana, aby zoptymalizować koszty. Wybierz usługę premium storage dla wszystkich dysków w tabeli, które są wyświetlane jako usługi Azure standard storage (Sxx) do zapewnienia zgodności z SLA pojedynczej maszyny Wirtualnej platformy Azure do konfiguracji maszyny Wirtualnej.


> [!NOTE]
> Zalecenia dotyczące konfiguracji dysków docelowych minimalne wymagania, które oprogramowanie SAP udostępnia jej dostawców infrastruktury. Wdrożenia klienta rzeczywiste i scenariusze obciążeń te zalecenia nie zapewniają wystarczające możliwości w niektórych sytuacjach. Na przykład klient potrzebne szybciej ponowne załadowanie danych po ponownym uruchomieniu HANA lub kopii zapasowej konfiguracji wymagane większą przepustowość do magazynu. Inne przypadki obejmują /hana/log, gdzie 5000 operacji We/Wy nie były wystarczające dla określonego obciążenia. Potraktuj te zalecenia jako punkt początkowy i dostosować je na podstawie wymagań obciążenia.
>  

### <a name="set-up-azure-virtual-networks"></a>Konfigurowanie sieci wirtualnych platformy Azure
Jeśli masz połączenie lokacja lokacja na platformie Azure za pośrednictwem sieci VPN lub usługi Azure ExpressRoute, musisz mieć co najmniej jedną sieć wirtualna platformy Azure jest połączona za pośrednictwem bramy sieci wirtualnej z obwodem usługi VPN lub usługi ExpressRoute. W przypadku prostych wdrożeń bramy wirtualnej można wdrożyć w podsieci sieci wirtualnej platformy Azure, obsługujący zbyt wystąpieniami platformy SAP HANA. 

Aby zainstalować oprogramowanie SAP HANA, należy utworzyć dwa dodatkowe podsieci w sieci wirtualnej platformy Azure. W jednej podsieci hostuje maszyny wirtualne do uruchamiania wystąpień oprogramowania SAP HANA. Innych podsieci jest uruchomiony serwer Przesiadkowy lub maszyny wirtualne zarządzania hosta SAP HANA Studio, inne oprogramowanie do zarządzania lub oprogramowanie aplikacji.

> [!IMPORTANT]
> Konfigurowanie [Azure sieciowych urządzeń wirtualnych](https://azure.microsoft.com/solutions/network-appliances/) w ścieżce komunikacji między aplikacją SAP DBMS warstwy programu SAP NetWeaver - Hybris- lub system SAP S/4HANA-based nie jest obsługiwane. To ograniczenie jest powodów funkcjonalność i wydajność. Ścieżka komunikacji między warstwą aplikacji SAP i warstwy system DBMS musi być bezpośrednich. Ograniczenie nie obejmuje [grupy zabezpieczeń aplikacji (ASG) i zabezpieczeń sieciowych grup reguł (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) Jeśli te reguły ASG i sieciowej grupy zabezpieczeń zezwalają ścieżki bezpośrednią komunikację. 
>
> Inne scenariusze, w których wirtualne urządzenia sieciowe nie są obsługiwane należą: 
>
> - Ścieżki komunikacji między maszynami wirtualnymi platformy Azure, które reprezentują program Pacemaker w systemie Linux węzłów i klastra interwencja urządzeń zgodnie z opisem w [wysoką dostępność środowiska SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server dla aplikacji SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> - Ścieżki komunikacji między maszynami wirtualnymi platformy Azure i serwera plików skalowalnego w poziomie serwera dla systemu Windows ustaw zgodnie z opisem w górę [klastra wystąpienie SAP ASCS/SCS na klastrze pracy awaryjnej Windows przy użyciu udziału plików na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Wirtualne urządzenia sieciowe w zaufanych ścieżek komunikacji, można łatwo double opóźnienie sieci między partnerami dwóch komunikacji. Mogą również ograniczyć przepustowość w krytyczne ścieżki między warstwą aplikacji SAP i warstwy system DBMS. W niektórych przypadkach klient wirtualnych urządzeń sieciowych może spowodować program Pacemaker w systemie Linux klastrów nie powiedzie się. Są to przypadki, gdzie komunikacji między węzłami klastra program Pacemaker w systemie Linux, komunikują się na ich urządzenia interwencja za pośrednictwem wirtualnego urządzenia sieciowego.  
> 

> [!IMPORTANT]
> Innego projektu, który firmy *nie* jest oddzielenie warstwy aplikacji SAP i warstwą DBMS w różnych sieciach wirtualnych platformy Azure, które nie są obsługiwane [skomunikowane równorzędnie](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ze sobą. Firma Microsoft zaleca segregowanie warstwy aplikacji SAP i warstwy system DBMS za pomocą podsieci w sieci wirtualnej platformy Azure zamiast przy użyciu różnych sieciach wirtualnych platformy Azure. 
>
>Jeśli zdecydujesz się na zalecenia i zamiast tego oddzielenie czynności związanych z dwoma warstwami w różnych sieciach wirtualnych, musi być dwie sieci wirtualne [skomunikowane równorzędnie](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Należy pamiętać, że ruchem sieciowym między dwoma [skomunikowane równorzędnie](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) sieciami wirtualnymi platformy Azure podlega postanowieniom koszty transferu. Wolumin olbrzymich ilościach danych, który składa się z wielu terabajtów, są wymieniane między warstwy aplikacji SAP i warstwy system DBMS. Jeśli warstwa aplikacji SAP i warstwy system DBMS są rozdzielone między dwiema równorzędnymi sieciami wirtualnymi platformy Azure, może wzrosnąć znaczne koszty. 

Po zainstalowaniu maszyn wirtualnych do uruchamiania oprogramowania SAP HANA, potrzebne są maszyny wirtualne:

- Dwie wirtualne karty sieciowe zainstalowane. Łączy się podsieci zarządzania z jedną kartą Sieciową. Karta sieciowa umożliwia nawiązywanie połączeń z siecią lokalną lub innymi sieciami wystąpienie SAP HANA na maszynie Wirtualnej platformy Azure.
- Statyczne prywatne adresy IP, które zostały wdrożone dla obu wirtualnych kart sieciowych.

> [!NOTE]
> Oznacza przypisanie statycznych adresów IP za pośrednictwem platformy Azure przypisać je do poszczególnych wirtualnych kart sieciowych. Nie przypisuj statycznych adresów IP w ramach systemu operacyjnego gościa do wirtualnej karty sieciowej. Niektórych usług platformy Azure, takich jak usługa Azure Backup opierają się na fakcie, że w co najmniej podstawowego wirtualnej karty Sieciowej jest ustawiony, DHCP, a nie statycznych adresów IP. Aby uzyskać więcej informacji, zobacz [kopii zapasowej maszyny wirtualnej Azure Rozwiązywanie problemów z](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Aby przypisać wiele statycznych adresów IP do maszyny Wirtualnej, należy przypisać wiele wirtualnych kart sieciowych do maszyny Wirtualnej.
>
>

W przypadku wdrożeń, które są trwałe tworzenie architektury sieci wirtualne centrum danych na platformie Azure. Ta architektura zaleca się rozdzielenie bramy sieci wirtualnej platformy Azure, która łączy do środowiska lokalnego do oddzielnych sieci wirtualnej platformy Azure. Ta sieć wirtualna oddzielne obsługuje cały ruch, który opuszcza lokalną lub Internetem. Za pomocą tej metody, można wdrożyć oprogramowania do inspekcji i ruchu dziennika, który wprowadza wirtualnego centrum danych na platformie Azure w tej sieci wirtualnej osobnego koncentratora. Dzięki temu masz jedną sieć wirtualną obsługujący całe oprogramowanie i konfiguracje, które odnosi się do użytego i wychodzący ruch na wdrożenie systemu Azure.


Aby uzyskać więcej informacji na temat podejścia wirtualnego centrum danych i projektowania powiązane sieci wirtualnej platformy Azure zobacz: 

- [Wirtualne centrum danych Azure: Perspektywa sieci](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter).
- [Wirtualne centrum danych Azure i enterprise control plane](https://docs.microsoft.com/azure/architecture/vdc/).


>[!NOTE]
>Ruch przepływa między centralnej sieci wirtualnej i sieci wirtualnej szprychy za pomocą [komunikacji równorzędnej sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) podlega dodatkowe [koszty](https://azure.microsoft.com/pricing/details/virtual-network/). Na podstawie tych kosztów, może być konieczne upewnić kompromisów między wykonywania projektowania strict — Gwiazda sieci i wykonywania wielu [bramy usługi Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) łączenie do szprychy w celu obejścia komunikacji równorzędnej sieci wirtualnej. 
>
> Bramy usługi ExpressRoute platformy Azure, wprowadzenie dodatkowych [koszty](https://azure.microsoft.com/pricing/details/vpn-gateway/) zbyt. Może wystąpić również dodatkowe koszty oprogramowania innych firm, którego używasz do logowania, inspekcji i monitorowania ruchu sieciowego. Należy wziąć pod uwagę koszty wymianę danych za pośrednictwem wirtualnej sieci równorzędnej i koszty, utworzone przez dodatkowe bramy usługi ExpressRoute i licencji na oprogramowanie. Można zdecydować się na mikrosegmentację w ramach jednej sieci wirtualnej za pomocą podsieci jako jednostki izolacji zamiast sieci wirtualnych.


Aby uzyskać przegląd różnych metod, które służy do przypisywania adresów IP, zobacz [typy adresów IP i metody alokacji na platformie Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

W przypadku maszyn wirtualnych z systemem SAP HANA pracy przy użyciu statycznych adresów IP, które są przypisane. Przyczyną jest to, że niektóre atrybuty konfiguracji platformy Hana odwoływać się do adresów IP.

[Sieciowe grupy zabezpieczeń w usłudze Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) są używane do kierowania ruchu, który jest kierowany do serwera Przesiadkowego lub wystąpienia platformy SAP HANA. Sieciowe grupy zabezpieczeń i ostatecznie [grupy zabezpieczeń aplikacji](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) są skojarzone z podsiecią platformy SAP HANA i podsieci zarządzania.

Na poniższej ilustracji przedstawiono omówienie schematu nierównej wdrożenia platformy SAP Hana, występującego architektury Gwiazda — sieci wirtualnej:

![Przybliżony wdrożenia schematu dla oprogramowania SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

Aby wdrożyć oprogramowanie SAP HANA na platformie Azure bez połączenia lokacja lokacja, włączyć osłonę wystąpienie SAP HANA z publicznego Internetu i je ukryć za serwerem proxy do przodu. W tym scenariuszu podstawowego wdrożenia zależy od systemu Azure wbudowanych usług DNS do rozpoznawania nazw hostów. W bardziej złożonym wdrożeniu, gdzie publicznych adresów IP są używane wbudowanej usługi DNS platformy Azure są szczególnie ważne. Użyj sieciowych grup zabezpieczeń platformy Azure i [Azure sieciowych urządzeń wirtualnych](https://azure.microsoft.com/solutions/network-appliances/) do monitorowania, routing z Internetu do architektury sieci wirtualnej platformy Azure na platformie Azure. 

Na poniższej ilustracji przedstawiono nierównej schematu dotyczące wdrażania oprogramowania SAP HANA bez połączenia lokacja lokacja w ramach architektury Gwiazda — sieci wirtualnej:
  
![Przybliżony wdrożenia schematu dla oprogramowania SAP HANA bez połączenia lokacja lokacja](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Inny opis sposobu używania wirtualnych urządzeń sieciowych platformy Azure do kontrolę i monitorowanie dostępu z Internetu bez architektury sieci wirtualnej — Gwiazda, zobacz [Wdrażanie wirtualnych urządzeń sieciowych o wysokiej dostępności](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configure-azure-infrastructure-for-sap-hana-scale-out"></a>Konfigurowanie infrastruktury platformy Azure dla oprogramowania SAP HANA skalowalnego w poziomie
Firma Microsoft ma jeden SKU maszyn wirtualnych serii M, który jest certyfikowany dla skalowalnego w poziomie konfiguracji SAP HANA. Typ maszyny Wirtualnej M128s jest certyfikowany dla skalowalnego w poziomie z maksymalnie 16 węzłach. Zmiany w certyfikaty skalowalnego w poziomie SAP HANA na maszynach wirtualnych platformy Azure, zobacz [certyfikowane listy platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Minimalnej wersji systemu operacyjnego służące do wdrażania skalowalnego w poziomie konfiguracji maszyn wirtualnych platformy Azure są następujące:

- SUSE Linux 12 SP3.
- Red Hat Linux 7.4.

Do 16 węzłami certyfikacji skalowalnego w poziomie:

- Jeden węzeł jest węzłem głównym.
- Maksymalnie 15 węzłami są węzły procesu roboczego.

>[!NOTE]
>W przypadku wdrożeń skalowalnego w poziomie maszyny Wirtualnej platformy Azure nie jest możliwe za pomocą węzła wstrzymania.
>

Istnieją dwie przyczyny, dlaczego nie można skonfigurować wstrzymania węzła:

- Platforma Azure ma na tym etapie nie macierzystej usługi systemu plików NFS. W rezultacie udziałów NFS musi być skonfigurowany za pomocą funkcji innych firm.
- Brak konfiguracji systemu plików NFS firm spełniają kryteria opóźnienie magazynu dla oprogramowania SAP HANA, za pomocą swoich rozwiązań wdrożonych na platformie Azure.

W rezultacie nie można udostępnić /hana/data i /hana/log woluminy. Nie współużytkuje te woluminy pojedynczych węzłów uniemożliwia korzystanie z węzłem wstrzymania platformy SAP HANA w konfiguracji skalowania w poziomie.

Na poniższej ilustracji przedstawiono podstawowe projektowania dla pojedynczego węzła w konfiguracji skalowania w poziomie:

![Podstawy skalowalnego w poziomie z jednego węzła](media/hana-vm-operations/scale-out-basics.PNG)

Konfiguracji podstawowej maszyny Wirtualnej węzła dla oprogramowania SAP HANA skalowalnego w poziomie wygląda następująco:

- Dla /hana/shared możesz tworzyć się klaster systemu plików NFS o wysokiej dostępności oparte na systemie SUSE Linux 12 z dodatkiem SP3. Ten klaster hostuje udziały NFS /hana/shared konfiguracji skalowania w poziomie i oprogramowania SAP NetWeaver ani usług centralnej BW/4HANA. Aby uzyskać informacje na temat tworzenia takiej konfiguracji, zobacz [wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
- Wszystkie woluminy dysków nie są współużytkowane przez różne węzły i nie są oparte na systemu plików NFS. Konfiguracje instalacji i procedury dotyczące instalacji platformy HANA skalowalnego w poziomie za pomocą nieudostępnionych /hana/data i /hana/log znajdują się w dalszej części tego artykułu.

>[!NOTE]
>O wysokiej dostępności klastra systemu plików NFS wyświetlaną w grafiki do tej pory jest obsługiwany w przypadku systemu SUSE Linux tylko. Rozwiązanie systemu plików NFS o wysokiej dostępności oparte na systemie Red Hat zostanie poinformowany później.

Ustalanie rozmiaru woluminów dla węzłów jest taka sama, jak w przypadku skalowania w górę, z wyjątkiem /hana/shared. W poniższej tabeli przedstawiono sugerowany rozmiary i typy dla jednostki SKU maszyny Wirtualnej M128s.

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalna maszyny Wirtualnej we/wy<br /> Przepływność | /hana/data | / hana/log | wolumin/root | /usr/sap | Hana/kopia zapasowa |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 giB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Sprawdź, czy przepływności różnych woluminach sugerowane spełnia obciążenia, które chcesz uruchomić. Jeśli obciążenie wymaga większej ilości /hana/data i /hana/log, należy zwiększyć liczbę usługi Azure premium storage wirtualnych dysków twardych. Zmiany rozmiaru woluminu z kolejnych wirtualnych dysków twardych niż wymienione zwiększa operacje We/Wy i przepływność operacji We/Wy w ramach typu maszyny wirtualnej platformy Azure. Mają zastosowanie również akceleratorem zapisu na dyskach, które tworzą woluminu /hana/log.
 

Aby uzyskać formułę, która definiuje rozmiar woluminu/hana/udostępnione dla skalowalnego w poziomie jako rozmiar pamięci węzła pojedynczego procesu roboczego na czterech węzłach procesu roboczego, zobacz [wymagania dotyczące magazynu SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Przy założeniu, że skorzystasz z platformy SAP HANA skalowalnego w poziomie certyfikowanych M128s maszyny Wirtualnej platformy Azure z około 2 TB pamięci, zalecenia SAP są podsumowywane jako:

- Dla jednego węzła głównego i maksymalnie cztery węzły procesu roboczego, rozmiar woluminu /hana/shared jest 2 TB.
- Jeden węzeł główny i pięć i osiem węzłów procesu roboczego rozmiar woluminu /hana/shared to 4 TB.
- Jeden węzeł główny i węzły procesu roboczego 9-12 rozmiar woluminu /hana/shared to 6 TB.
- Jeden węzeł główny i węzły procesu roboczego 12 – 15 rozmiar woluminu /hana/shared to 8 TB.

Ważnych projektu, wyświetlanego w grafiki Konfiguracja z pojedynczym węzłem dla maszyn wirtualnych skalowania w poziomie SAP HANA jest sieć wirtualna, za pomocą konfiguracji podsieci. SAP zaleca rozdzielenie ruchu i aplikacji — ukierunkowane na klienta z łączności między węzłami platformy HANA. 

Na osiągnięcie tego celu, należy dołączyć dwa różne wirtualne karty sieciowe z maszyną wirtualną, jak pokazano na grafiki. Zarówno wirtualne karty sieciowe są w różnych podsieciach i ma dwa różne adresy IP. Następnie sterowanie przepływem ruchu przy użyciu reguł routingu przy użyciu sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika.

Szczególnie na platformie Azure istnieją nie oznacza, że i metody, aby wymusić jakości usług i przydziałów na określonym wirtualnych kart sieciowych. W rezultacie rozdzielenie komunikacji wewnątrz węzła i aplikacji — ukierunkowane na klienta nie otwiera żadnych możliwości priorytety jeden strumień ruchu za pośrednictwem drugiego. Zamiast tego oddzielenie pozostaje zabezpieczeń w osłony komunikacji wewnątrz węzła konfiguracji skalowania w poziomie.  

>[!IMPORTANT]
>SAP zaleca odseparowania ruch sieciowy do klienta i aplikację po stronie wewnątrz węzła ruchu i zgodnie z opisem w tym artykule. Zaleca się umieścić architekturę w miejscu, jak pokazano na poprzednim grafiki.
>

Na poniższej ilustracji przedstawiono architekturę minimalna wymagana sieć z punktu widzenia sieci:

![Podstawy skalowalnego w poziomie z jednego węzła](media/hana-vm-operations/scale-out-networking-overview.PNG)

Limity do tej pory obsługiwane są 15 węzłów procesu roboczego, oprócz jednego węzła głównego.

Na poniższej ilustracji przedstawiono architekturę magazynu, z punktu widzenia magazynu:


![Podstawy skalowalnego w poziomie z jednego węzła](media/hana-vm-operations/scale-out-storage-overview.PNG)

Wolumin /hana/shared znajduje się w konfiguracji wysokiej dostępności udziału NFS. Wszystkie inne dyski lokalne są instalowane dla poszczególnych maszyn wirtualnych. 

### <a name="highly-available-nfs-share"></a>Udziału NFS o wysokiej dostępności
Do tej pory o wysokiej dostępności klastra systemu plików NFS pracuje z systemem SUSE Linux tylko. Aby uzyskać informacje o instalacji, zobacz [wysoka dostępność systemu NFS na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Jeśli nie udostępniasz klastra systemu plików NFS inne konfiguracje HANA spoza sieci wirtualnej platformy Azure z systemem wystąpieniami platformy SAP HANA, należy go zainstalować w tej samej sieci wirtualnej. Zainstaluj go w swojej własnej podsieci, a następnie upewnij się, że nie wszystkie dowolnego ruchu mogą uzyskiwać dostęp do tej podsieci. Zamiast tego należy ograniczyć ruch do tej podsieci do adresów IP maszyny Wirtualnej, które są wykonywane ruchu do /hana/shared woluminu.

Zalecenia związane z wirtualnej karty Sieciowej, która kieruje ruch /hana/shared maszyny wirtualnej skalowalnego w poziomie HANA, są:

- Ponieważ ruch do /hana/shared umiarkowany, należy go rozesłać za pośrednictwem wirtualnej karty Sieciowej, która jest przypisana do sieci klienta w minimalnej konfiguracji.
- Po pewnym czasie dla ruchu do/hana/udostępnionego, należy wdrożyć trzeciej podsieci w sieci wirtualnej, gdy wdrażanie skalowalnego w poziomie konfiguracji SAP HANA. Przypisz trzeci wirtualną kartę Sieciową, która znajduje się w tej podsieci. Użyj trzeci wirtualnej karty Sieciowej i skojarzony adres IP dla ruchu do udziału NFS. Następnie można zastosować oddzielne dostęp i reguły routingu.

>[!IMPORTANT]
>Ruch sieciowy między maszyny wirtualne, które mają platformy SAP HANA w sposób skalowalny w poziomie wdrożonych i NFS o wysokiej dostępności w żadnym wypadku nie może przechodzić przez [wirtualnego urządzenia sieciowego](https://azure.microsoft.com/solutions/network-appliances/) lub podobne urządzenie wirtualne. Sieciowe grupy zabezpieczeń w usłudze Azure nie jest takie urządzenie. Sprawdź swoje reguły routingu, aby upewnić się, że wirtualnych urządzeń sieciowych lub podobne urządzenia wirtualne są detoured podczas uzyskiwania dostępu do systemu plików NFS o wysokiej dostępności korzystają z maszyn wirtualnych z systemem SAP HANA.
> 

Jeśli chcesz udostępnić klaster systemu plików NFS o wysokiej dostępności między konfiguracjami oprogramowania SAP HANA, należy przenieść te konfiguracje HANA do tej samej sieci wirtualnej. 
 

### <a name="install-an-sap-hana-scale-out-in-azure"></a>Instalowanie platformy SAP HANA skalowalnego w poziomie na platformie Azure
Aby zainstalować konfiguracji SAP skalowalnego w poziomie, wykonaj następujące kroki ogólne:

- Wdrażanie nowych lub dostosowania nowej infrastruktury sieci wirtualnej platformy Azure.
- Wdrażanie nowych maszyn wirtualnych przy użyciu woluminów magazynu usługi Azure managed premium.
- Wdrażaj nową lub dostosowanie istniejącego klastra systemu plików NFS o wysokiej dostępności.
- Dostosowanie routing w sieci, aby upewnić się, że na przykład komunikacji wewnątrz węzła między maszynami wirtualnymi nie jest kierowany przez [wirtualnego urządzenia sieciowego](https://azure.microsoft.com/solutions/network-appliances/). To samo dotyczy ruchu między maszynami wirtualnymi i o wysokiej dostępności klastra systemu plików NFS.
- Zainstaluj oprogramowanie SAP HANA węzła głównego.
- Dostosuj parametry konfiguracji węzła głównego platformy SAP HANA.
- Kontynuuj instalację węzłów procesu roboczego platformy SAP HANA.

#### <a name="install-sap-hana-in-a-scale-out-configuration"></a>Instalowanie platformy SAP HANA w konfiguracji skalowania w poziomie
Wdrożono infrastrukturę maszyny Wirtualnej platformy Azure, a wszystkie pozostałe zostaną zakończone. Teraz Aby zainstalować skalowalnego w poziomie konfiguracji SAP HANA, wykonaj następujące kroki:

- Zainstaluj oprogramowanie SAP HANA węzła głównego zgodnie z dokumentacją firmy SAP.
- *Po zakończeniu instalacji, zmiany pliku global.ini, a następnie dodaj parametr "basepath_shared = nie" do global.ini*. Ten parametr umożliwia uruchamianie w skalowalny w poziomie bez udostępnionego /hana/data i woluminy /hana/log między węzłami oprogramowania SAP HANA. Aby uzyskać więcej informacji, zobacz [2080991 # Uwaga SAP](https://launchpad.support.sap.com/#/notes/2080991).
- Po zmianie parametrów global.ini, ponownie uruchom wystąpienie SAP HANA.
- Dodaj dodatkowe węzły procesów roboczych. Aby uzyskać więcej informacji, zobacz [Przewodnik administrowania SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html). Określ sieci wewnętrznej dla platformy SAP HANA komunikacji między węzłami podczas instalacji lub później, za pomocą, na przykład hdblcm lokalnego. Aby uzyskać więcej informacji, zobacz [2183363 # Uwaga SAP](https://launchpad.support.sap.com/#/notes/2183363). 

Następujące tę procedurę instalacji konfiguracji skalowania w poziomie, który został zainstalowany używa nieudostępnionych dysków do uruchomienia /hana/data i /hana/log. Wolumin/hana/udostępnione znajduje się na wysokiej dostępności udziału NFS.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA warstw dynamicznych w wersji 2.0 dla maszyn wirtualnych platformy Azure

Oprócz certyfikaty SAP HANA na maszynach wirtualnych platformy Azure serii M SAP HANA, dynamicznej obsługi warstw w wersji 2.0 (DT 2.0) jest również obsługiwany w systemie Microsoft Azure. Łącza do dynamicznego dokumentacji warstw oprogramowania SAP HANA sekcja "Łączy DT dokumentacji 2.0" w dalszej części tego artykułu. Nie ma żadnej różnicy w instalującą produkt lub operacyjne, na przykład za pośrednictwem SAP HANA z Panelu sterowania wewnątrz maszyny Wirtualnej platformy Azure, ale kilka ważnych elementów są obowiązkowe oficjalne pomocy technicznej dotyczącej platformy Azure. Tych kluczowych zagadnieniach są opisane w poniższych sekcjach. 

SAP HANA DT w wersji 2.0 nie jest obsługiwana przez system SAP BW lub S4HANA. Przypadki użycia główne są teraz macierzyste aplikacje HANA.


### <a name="overview"></a>Omówienie

Poniższa ilustracja zawiera omówienie obsługi DT 2.0 na Microsoft Azure. Wykonaj te obowiązkowe wymagania do wykonania oficjalnych certyfikatów:

- DT 2.0 musi być zainstalowana na dedykowanym maszyny Wirtualnej platformy Azure. Może nie działać w tej samej maszyny Wirtualnej, której działa oprogramowanie SAP HANA.
- SAP HANA i maszyny wirtualne w wersji 2.0 DT musi zostać wdrożony w ramach tej samej sieci wirtualnej platformy Azure.
- SAP HANA i maszyny wirtualne w wersji 2.0 DT musi zostać wdrożony przy użyciu Azure Accelerated Networking włączone.
- Typ magazynu dla maszyn wirtualnych w wersji 2.0 DT musi być usługi Azure premium storage.
- Wiele dysków platformy Azure musi być dołączony do maszyny Wirtualnej w wersji 2.0 DT.
- Tworzenie oprogramowania RAID lub woluminu rozłożonego za pośrednictwem LVM lub mdadm, wymagane jest przy użyciu rozkładanie na dyskach platformy Azure.

Uzyskać więcej informacji można znaleźć w poniższych sekcjach.

![Omówienie architektury SAP HANA DT w wersji 2.0](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Dla programu SAP HANA DT 2.0 w wersji dedykowanej maszynie Wirtualnej platformy Azure

W usłudze Azure IaaS DT 2.0 jest obsługiwana tylko w dedykowanym maszyny Wirtualnej. DT w wersji 2.0 nie mogą być uruchamiane w tej samej maszyny Wirtualnej platformy Azure, gdzie jest uruchomione wystąpienie oprogramowania HANA. Początkowo dwa typy maszyn wirtualnych może służyć do uruchamiania programu SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Aby uzyskać opisy typów maszyn wirtualnych, zobacz [rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem pamięci](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

Biorąc pod uwagę podstawowa koncepcja DT w wersji 2.0, czyli o odciążaniu ciepło danych w celu obniżenia kosztów, warto użyć odpowiednich rozmiarów maszyn wirtualnych. Nie ma rygorystyczne reguły dla możliwych kombinacji. To zależy od obciążenia pracą danego klienta.

W poniższej tabeli przedstawiono zalecane konfiguracje.

| Typ maszyny Wirtualnej programu SAP HANA | Typ maszyny Wirtualnej w wersji 2.0 DT |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Wszystkie kombinacje z certyfikatem SAP HANA maszyny wirtualne serii M z obsługiwanych maszynach wirtualnych w wersji 2.0 DT, takie jak M64-32ms i E32sv3, są możliwe.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Sieci platformy Azure i SAP HANA DT w wersji 2.0

Instalowanie DT 2.0 na dedykowanej maszynie Wirtualnej wymaga przepustowość sieci między maszyny Wirtualnej w wersji 2.0 DT i SAP HANA maszyny Wirtualnej przy użyciu co najmniej 10 GB. W rezultacie jest wymagane, aby umieścić wszystkie maszyny wirtualne w tej samej sieci wirtualnej platformy Azure i włącz usługę Azure Accelerated Networking.

Aby uzyskać więcej informacji o usługę Azure Accelerated Networking, zobacz [Utwórz maszynę wirtualną systemu Linux przy użyciu Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

### <a name="vm-storage-for-sap-hana-dt-20"></a>Magazyn maszyny Wirtualnej dla programu SAP HANA DT w wersji 2.0

Zgodnie z wskazówki dotyczące najlepszych DT w wersji 2.0 minimalna przepływność operacji We/Wy dysku jest 50 MB na sekundę na każdy rdzeń fizyczny. Dysk maksymalny limit przepływności operacji We/Wy dla maszyny Wirtualnej spojrzenie na specyfikacji dwóch typów maszyn wirtualnych platformy Azure, które są obsługiwane dla DT w wersji 2.0, to:

- **E32sv3**:   768 MB/s, niebuforowanych, co oznacza stosunek 48 MB na sekundę na każdy rdzeń fizyczny
- **M64-32ms**:  1000 MB/s, niebuforowanych, co oznacza stosunek 62,5 MB na sekundę na każdy rdzeń fizyczny

Dołączając wiele dysków platformy Azure do maszyny Wirtualnej w wersji 2.0 DT i utworzenie macierzy RAID oprogramowania przy użyciu rozkładanie na poziomie systemu operacyjnego, aby osiągnąć maksymalny limit przepustowości dysku na maszynie Wirtualnej jest wymagana. Pojedynczy dysk platformy Azure nie może dostarczyć przepływności do osiągnięcia maksymalnego limitu maszyny Wirtualnej. Usługa Azure premium storage jest obowiązkowe, aby uruchomić DT w wersji 2.0. 

- Aby uzyskać więcej informacji na temat typów dostępnych dysków platformy Azure, zobacz [wybierz typ dysku dla maszyn wirtualnych Windows IaaS Azure](../../windows/disks-types.md).
- Aby uzyskać więcej informacji na temat tworzenia macierzy RAID oprogramowania za pośrednictwem mdadm zobacz [Konfigurowanie programowej macierzy RAID w systemie Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
- Aby uzyskać więcej informacji o sposobie konfigurowania LVM do utworzenia woluminu rozłożonego maksymalnej przepływności, zobacz [skonfigurować LVM na maszynie Wirtualnej systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).

W zależności od wymagań dotyczących rozmiaru istnieją różne opcje, aby osiągnąć maksymalną przepływność maszyny Wirtualnej. Oto konfiguracje dysków wolumin danych dla każdego typu maszyny Wirtualnej w wersji 2.0 DT do osiągnięcia górnego limitu przepływności maszyny Wirtualnej. Maszyna wirtualna E32sv3 jest traktowany jako poziomem wpis dla obciążeń mniejszych. Jeśli nie jest wystarczająco szybko, może być konieczne zmienić rozmiar maszyny Wirtualnej, aby M64-32ms.

Ponieważ maszyna wirtualna M64-32ms ma większą ilość pamięci, limit, szczególnie w przypadku obciążeń intensywnie korzystających z odczytu nie może skontaktować się obciążenie We/Wy. Mniejszą liczbę dysków w zestawie usługi stripe może być wystarczające, w zależności od obciążenia pracą właściwe dla klienta. Jako bezpieczne po stronie następujące konfiguracje dysków dobrano w celu zagwarantowania maksymalną przepustowość.


| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Konfiguracja dysku 1 | Konfiguracja dysku 2 | Konfiguracja dysku 3 | Konfiguracja dysku 4 | Konfiguracja dysku 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1,5 TB | 


Szczególnie w przypadku obciążenia intensywnego odczytu, włączając ustawienie "" tylko do odczytu pamięci podręcznej hosta platformy Azure, zgodnie z zaleceniami woluminy danych oprogramowania bazy danych może zwiększyć wydajność operacji We/Wy. Dla dziennika transakcji pamięci podręcznej dysku hosta platformy Azure musi być "none". 

Punkt początkowy, które firma Microsoft zaleca, aby uzyskać rozmiar woluminu dziennika jest o heurystykę 15 procent rozmiaru danych. Aby utworzyć wolumin dziennika, należy używać typów inny dysk platformy Azure, w zależności od wymagań kosztów i przepływności. Wolumin dziennika wysoką przepływnością wejścia/wyjścia jest wymagane. 

Jeśli używasz maszyny Wirtualnej wpisz M64-32ms, firma Microsoft zaleca, aby włączyć [akceleratorem zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Akcelerator zapisu to nowa funkcja platformy Azure, która zapewnia opóźnienia zapisu optymalne dysku dla dziennika transakcji. Jest ona dostępna tylko w przypadku serii M. Istnieje kilka elementów, które należy wziąć pod uwagę, takie jak maksymalna liczba dysków na typ maszyny Wirtualnej. Aby uzyskać więcej informacji na temat akceleratorem zapisu, zobacz [włączyć akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


W poniższej tabeli przedstawiono kilka przykładów, aby pomóc Ci rozmiar woluminu dziennika.

| Typ dysk i rozmiar woluminu danych | wolumin dziennika i dysk typu konfiguracji 1 | wolumin dziennika i dysk typu konfiguracji 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Podobnie jak oprogramowanie SAP HANA skalowalnego w poziomie, katalog /hana/shared muszą współużytkować maszyna wirtualna SAP HANA i DT maszyny Wirtualnej w wersji 2.0. Zalecamy użycie takiej samej architekturze, jak w przypadku oprogramowania SAP HANA skalowalnego w poziomie przy użyciu dedykowanych maszyn wirtualnych, które działają jako serwer systemu plików NFS o wysokiej dostępności. Zapewnienie udostępnionego woluminu kopii zapasowej, należy użyć identycznych projektu. Ale to Ty decydujesz o tym, czy konieczne jest wysoka dostępność czy jest wystarczająca do działania jako serwer kopii zapasowych za pomocą dedykowanego maszyny Wirtualnej z wystarczającą pojemnością magazynu.



### <a name="links-to-dt-20-documentation"></a>Linki do dokumentacji DT w wersji 2.0 

- [Przewodnika po aktualizacji i dynamiczne warstw instalacja SAP HANA](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA dynamiczne warstw samouczki i zasoby](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
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
Jeśli masz połączenie lokacja lokacja między lokalizacjami lokalnymi i platformy Azure i uruchamiasz składniki SAP, prawdopodobnie używasz SAProuter. W takim wypadku wykonaj następujące kroki dla zdalnej pomocy technicznej:

- Obsługa prywatnych i statyczny adres IP maszyny Wirtualnej obsługującego platformy SAP HANA w konfiguracji SAProuter.
- Konfigurowanie sieciowej grupy zabezpieczeń w podsieci, który jest hostem maszyny Wirtualnej platformy HANA, aby zezwalać na ruch przez TCP/IP port 3299.

Jeśli nie masz SAP router dla maszyny Wirtualnej z platformą SAP HANA nawiązaniu połączenia z platformą Azure za pośrednictwem Internetu, zainstaluj składnik. Zainstaluj SAProuter w oddzielnych maszyny Wirtualnej w podsieci zarządzania. 

Na poniższej ilustracji przedstawiono nierównej schematu do wdrażania oprogramowania SAP HANA z SAProuter i bez połączenia lokacja lokacja:

![Przybliżony wdrożenia schematu dla oprogramowania SAP HANA bez połączenia lokacja lokacja i SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Pamiętaj zainstalować SAProuter w oddzielnych maszyny Wirtualnej i nie z maszyną Wirtualną serwera Przesiadkowego. Oddzielne maszyny Wirtualnej musi mieć statyczny adres IP. Aby połączyć Twoje SAProuter SAProuter, która jest obsługiwana przez oprogramowanie SAP, skontaktuj się z SAP dla adresu IP. SAProuter, która jest obsługiwana przez oprogramowanie SAP jest odpowiednikiem SAProuter wystąpienia, które zainstalujesz na swojej maszynie Wirtualnej. Umożliwia skonfigurowanie wystąpienia SAProuter przez adres IP z systemu SAP. W ustawieniach konfiguracji tylko niezbędne jest TCP port 3299.

Aby uzyskać więcej informacji na temat konfiguracji i utrzymania zdalną obsługę połączeń za pośrednictwem SAProuter, zobacz [dokumentacji SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Wysoka dostępność oprogramowania SAP HANA na natywnych maszynach wirtualnych platformy Azure
Jeśli uruchamiasz SUSE Linux Enterprise Server dla programu SAP aplikacji 12 z dodatkiem SP1 lub nowszego, można ustanowić klastra program Pacemaker ze pomocą metody STONITH urządzeniami. Aby ustawić konfigurację platformy SAP HANA, korzystającej z replikacji synchronicznej replikacji systemu HANA i automatycznej pracy awaryjnej, należy użyć urządzenia. Aby uzyskać więcej informacji na temat procedury instalacyjnej, zobacz [platformy SAP HANA przewodnik wysokiej dostępności dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
