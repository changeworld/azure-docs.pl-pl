---
title: SAP HANA Konfiguracja magazynu maszyn wirtualnych platformy Azure | Microsoft Docs
description: Zalecenia dotyczące magazynu dla maszyny wirtualnej, w której wdrożono SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/11/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0ab25b7a6d723ed5f2e74ad60ff54f9bf6d0fe4c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300547"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Konfiguracje magazynu maszyn wirtualnych platformy Azure SAP HANA

Platforma Azure oferuje różne typy magazynów, które są odpowiednie dla maszyn wirtualnych platformy Azure z systemem SAP HANA. Typy magazynów platformy Azure, które mogą być brane pod uwagę na listę wdrożeń SAP HANA, takich jak: 

- SSD w warstwie Standardowa stacje dysków (SSD)
- Dyski półprzewodnikowe Premium (SSD)
- [Ultra Disk](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Aby dowiedzieć się więcej o tych typach dysków, zobacz artykuł [Wybieranie typu dysku](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Platforma Azure oferuje dwie metody wdrażania dysków VHD w usłudze Azure Standard i Premium Storage. Jeśli ogólny scenariusz zezwala na korzystanie z [usługi Azure Managed Disk](https://azure.microsoft.com/services/managed-disks/) Deployments. 

Aby uzyskać listę typów magazynu i ich umowy SLA w przypadku operacji we/wy i przepływności magazynu, zapoznaj się z [dokumentacją platformy Azure dla dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/).

W przypadku użycia z platformą HANA te trzy typy magazynów są certyfikowane przy użyciu oprogramowania SAP:

- Usługa Azure Premium Storage —/Hana/log musi być buforowana przy użyciu usługi Azure [Akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)
- Azure Ultra Disk
- Woluminy NFS v 4.1 na Azure NetApp Files dla/Hana/log i/Hana/Data

Niektóre typy magazynów można łączyć. Na przykład możliwe jest umieszczenie/Hana/Data na Premium Storage i/Hana/log może zostać umieszczony w magazynie Ultra Disk w celu uzyskania wymaganego niskiego opóźnienia. Nie zaleca się jednak mieszania woluminów NFS dla np./Hana/Data i używania jednego z innych certyfikowanych typów magazynów dla/Hana/log

W lokalnym świecie rzadko trzeba zadbać o podsystemy we/wy i możliwości. Przyczyną jest to, że dostawca urządzenia jest wymagany do upewnienia się, że minimalne wymagania dotyczące magazynu są spełnione dla SAP HANA. Podczas tworzenia infrastruktury platformy Azure należy pamiętać o niektórych wymaganiach. Niektóre z minimalnych wymagań dotyczących przepływności są następujące:

- Włącz odczyt/zapis na **/Hana/log** z 250 MB/s z 1 MB we/wy
- Włącz działanie odczytu przez co najmniej 400 MB/s dla **/Hana/Data** dla 16 mb i 64 MB we/wy
- Włącz działanie zapisu przez co najmniej 250 MB/s dla **/Hana/Data** z 16 mb i 64 MB we/wy

Uwzględniając, że niskie opóźnienie magazynu ma krytyczne znaczenie dla systemów DBMS, nawet jako DBMS, takie jak SAP HANA, Zachowaj dane w pamięci. Ścieżka krytyczna w magazynie jest zwykle wokół zapisów w dzienniku transakcji w systemach DBMS. Ale również operacje, takie jak zapisywanie punktów zapisu lub ładowanie danych w pamięci po awarii, mogą być krytyczne. W związku z tym, **obowiązkowe** jest korzystanie z dysków Azure Premium dla woluminów **/Hana/Data** i **/Hana/log** . Aby zapewnić minimalną przepływność **/Hana/log** i **/Hana/Data** zgodnie z wymaganiami SAP, należy skompilować RAID 0 za pomocą MDADM lub LVM na wielu dyskach Premium Storage platformy Azure. I używaj woluminów RAID jako woluminów **/Hana/Data** i **/Hana/log** . 

**Zalecenie: jako rozmiary rozłożenia dla RAID 0 zaleca się użycie:**

- 64 KB lub 128 KB dla **/Hana/Data**
- 32 KB dla **/Hana/log**

> [!NOTE]
> Nie musisz konfigurować żadnego poziomu nadmiarowości przy użyciu woluminów RAID, ponieważ usługa Azure Premium i magazyn w warstwie Standardowa przechowują trzy obrazy wirtualnego dysku twardego. Użycie woluminu RAID ma charakter wyłącznie skonfigurowany w celu zapewnienia wystarczającej przepływności we/wy.

Gromadzenie wielu wirtualnych dysków twardych platformy Azure poniżej macierzy RAID powoduje gromadzenie informacji o przepływności operacji we/wy na sekundę. Dzięki temu, jeśli umieścisz macierz RAID 0 przez 3 x P30 Azure Premium Storage, powinno to dać trzy razy większą liczbę operacji we/wy i trzykrotnie przekroczyć przepływność magazynu pojedynczego dysku P30 Azure Premium Storage.

Należy również pamiętać o ogólnej przepływności we/wy maszyny wirtualnej podczas ustalania rozmiarów lub podejmowania decyzji dotyczących maszyny wirtualnej. Ogólna przepływność magazynu maszyny wirtualnej jest udokumentowana w artykule [zoptymalizowane rozmiary maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Tryb harmonogramu we/wy systemu Linux
System Linux ma kilka różnych trybów planowania operacji we/wy. Typowym zaleceniem za pośrednictwem dostawców systemu Linux i SAP jest ponowne skonfigurowanie trybu harmonogramu we/wy dla woluminów dysku z trybu **CFQ** do trybu **aktualizujący nie działa** . Szczegółowe informacje znajdują się w [#1984798 uwagi SAP](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Rozwiązania z Premium Storage i akcelerator zapisu platformy Azure dla maszyn wirtualnych z serii M platformy Azure
Usługa Azure akcelerator zapisu to funkcja dostępna wyłącznie dla maszyn wirtualnych z serii M platformy Azure. Podobnie jak w przypadku nazw, celem funkcji jest poprawa opóźnień operacji we/wy w odniesieniu do Premium Storage platformy Azure. W przypadku SAP HANA akcelerator zapisu powinna być używana tylko w odniesieniu do woluminu **/Hana/log** . W związku z tym **/Hana/Data** i **/Hana/log** to oddzielne woluminy z platformą Azure akcelerator zapisu obsługują tylko wolumin **/Hana/log** . 

> [!IMPORTANT]
> W przypadku korzystania z usługi Azure Premium Storage użycie usługi Azure [Akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) lub woluminu/Hana/log jest obowiązkowe. Akcelerator zapisu jest dostępny tylko dla maszyn wirtualnych w warstwie Premium i serii M i serii Mv2.

W poniższych zaleceń dotyczących buforowania przyjęto założenie, że właściwości we/wy dla SAP HANA są takie jak:

- Istnieją trudne do odczytania obciążenie dla plików danych platformy HANA. Wyjątki są duże I/OS po ponownym uruchomieniu wystąpienia HANA lub podczas ładowania danych do platformy HANA. Kolejną wielkością operacji odczytu I/OS dla plików danych może być kopia zapasowa bazy danych HANA. Ze względu na to, że buforowanie odczytu nie ma sensu, ponieważ w większości przypadków, wszystkie woluminy plików danych muszą zostać całkowicie odczytane.
- Zapisywanie względem plików danych jest zainstalowano na podstawie obciążeń opartych na platformie HANA punktów zapisu i HANA. Pisanie punktów zapisu jest asynchroniczne i nie utrzymuje żadnych transakcji użytkownika. Zapisywanie danych podczas odzyskiwania po awarii ma kluczowe znaczenie dla wydajności w celu szybkiego reagowania na system. Jednak odzyskiwanie awaryjne powinno być raczej wyjątkowymi sytuacjami
- Istnieją twarde operacje odczytu z plików. Wyjątki są duże we/wy podczas wykonywania kopii zapasowych dziennika transakcji, odzyskiwania po awarii lub w fazie ponownego uruchomienia wystąpienia platformy HANA.  
- Główne obciążenie w odniesieniu do pliku dziennika SAP HANA wykonaj ponownie jest zapisem. Zależnie od charakteru obciążenia można korzystać z operacji we/wy na małą wartość 4 KB lub w innych przypadkach, w których rozmiar operacji wejścia/wyjścia wynosi 1 MB lub więcej. Opóźnienie zapisu w dzienniku SAP HANA ponownego wykonywania ma krytyczne znaczenie dla wydajności.
- Wszystkie zapisy muszą być utrwalane na dysku w niezawodny sposób

**Zalecenie: w wyniku tych zaobserwowanych wzorców we/wy według SAP HANA, buforowanie dla różnych woluminów przy użyciu usługi Azure Premium Storage powinno być ustawione na takie:**

- **/Hana/Data** — brak buforowania
- **/Hana/log** — brak buforowania — wyjątek dla serii M i Mv2, gdzie akcelerator zapisu jest włączona jako funkcja buforowania
- **/Hana/Shared** — buforowanie odczytu

### <a name="production-recommended-storage-solution"></a>Zalecane rozwiązanie do produkcji magazynu

> [!IMPORTANT]
> Certyfikat SAP HANA dla maszyn wirtualnych z serii M systemu Azure jest oparty wyłącznie na platformie Azure akcelerator zapisu dla woluminu **/Hana/log** . W efekcie scenariusz produkcyjny SAP HANA wdrożenia na maszynach wirtualnych z serii M na platformie Azure powinien zostać skonfigurowany za pomocą usługi Azure akcelerator zapisu dla woluminu **/Hana/log** .  

> [!NOTE]
> W przypadku scenariuszy produkcyjnych Sprawdź, czy określony typ maszyny wirtualnej jest obsługiwany dla SAP HANA przez SAP w [dokumentacji SAP dla IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).



**Zalecenie: zalecane konfiguracje dla scenariuszy produkcyjnych wyglądają następująco:**

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | /hana/data | /hana/log | /hana/shared | wolumin/root | /usr/sap | Hana/kopia zapasowa |
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

Typy maszyn wirtualnych M416xx_v2 nie są jeszcze udostępniane publicznie przez firmę Microsoft. Sprawdź, czy przepływność magazynu dla różnych sugerowanych woluminów spełnia obciążenie, które chcesz uruchomić. Jeśli obciążenie wymaga wyższych woluminów dla **/Hana/Data** i **/Hana/log**, należy zwiększyć liczbę wirtualnych dysków twardych Premium Storage platformy Azure. Ustalanie wielkości woluminu o większej liczbie dysków VHD nie powoduje zwiększenia przepływności operacji we/wy w ramach limitów typu maszyny wirtualnej platformy Azure.

Usługa Azure akcelerator zapisu działa tylko w połączeniu z usługą [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Tak więc co najmniej dyski usługi Azure Premium Storage tworzące wolumin **/Hana/log** muszą zostać wdrożone jako dyski zarządzane.

Istnieją limity wirtualnych dysków twardych Premium Storage platformy Azure dla maszyny wirtualnej, które mogą być obsługiwane przez usługę Azure akcelerator zapisu. Bieżące limity to:

- 16 wirtualnych dysków twardych dla maszyny wirtualnej z M128xx i M416xx
- 8 wirtualnych dysków twardych dla maszyny wirtualnej z M64xx i M208xx
- 4 wirtualne dyski twarde dla maszyny wirtualnej M32xx

Więcej szczegółowych instrukcji dotyczących włączania usługi Azure akcelerator zapisu można znaleźć w artykule [Akcelerator zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Szczegółowe informacje i ograniczenia dotyczące usługi Azure akcelerator zapisu można znaleźć w tej samej dokumentacji.

**Zalecenie: należy używać akcelerator zapisu dla dysków tworzących wolumin/Hana/log**


### <a name="cost-conscious-azure-storage-configuration"></a>Przystępna cena konfiguracja usługi Azure Storage
W poniższej tabeli przedstawiono konfigurację typów maszyn wirtualnych używanych również przez klientów do hostowania SAP HANA na maszynach wirtualnych platformy Azure. Mogą istnieć typy maszyn wirtualnych, które mogą nie spełniać wszystkich minimalnych kryteriów magazynowania dla SAP HANA lub nie są oficjalnie obsługiwane w SAP HANA przez SAP. Jednak te maszyny wirtualne były przeznaczone do użycia w scenariuszach nieprodukcyjnych. **/Hana/Data** i **/Hana/log** są połączone z jednym woluminem. W związku z tym użycie usługi Azure akcelerator zapisu może stać się ograniczeniem w zakresie operacji we/wy na sekundę.

> [!NOTE]
> W przypadku scenariuszy obsługiwanych przez oprogramowanie SAP Sprawdź, czy określony typ maszyny wirtualnej jest obsługiwany dla SAP HANA przez SAP w [dokumentacji SAP dla IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Zmiana z wcześniejszych zaleceń dotyczących rozwiązania z ekonomicznego kosztu polega na przejściu z [usługi azure HDD w warstwie Standardowa disks](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) w celu lepszego wykonywania i bardziej niezawodnego działania [dysków SSD w warstwie Standardowa platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | /Hana/Data i/Hana/log<br /> rozłożone z LVM lub MDADM | /hana/shared | wolumin/root | /usr/sap | Hana/kopia zapasowa |
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


Typy maszyn wirtualnych M416xx_v2 nie są jeszcze udostępniane publicznie przez firmę Microsoft. Dyski zalecane dla mniejszych typów maszyn wirtualnych z 3 x P20 zalewaniu woluminy dotyczące zaleceń dotyczących miejsca zgodnie z oficjalnym [dokumentem magazynu SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Jednak wybór wyświetlany w tabeli został utworzony w celu zapewnienia wystarczającej przepływności dysku dla SAP HANA. Jeśli potrzebujesz zmian w woluminie **/Hana/Backup** , który miał rozmiar do przechowywania kopii zapasowych, które reprezentują dwa razy wolumin pamięci, możesz go dostosować.   
Sprawdź, czy przepływność magazynu dla różnych sugerowanych woluminów spełnia obciążenie, które chcesz uruchomić. Jeśli obciążenie wymaga wyższych woluminów dla **/Hana/Data** i **/Hana/log**, należy zwiększyć liczbę wirtualnych dysków twardych Premium Storage platformy Azure. Ustalanie wielkości woluminu o większej liczbie dysków VHD nie powoduje zwiększenia przepływności operacji we/wy w ramach limitów typu maszyny wirtualnej platformy Azure. 

> [!NOTE]
> Powyższe konfiguracje nie korzystają z [umowy SLA dla jednej maszyny wirtualnej platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) , ponieważ używa ona kombinacji usługi Azure Premium Storage i usługi Azure Standard Storage. Jednak wybór został wybrany w celu optymalizacji kosztów. Aby zapewnić zgodność konfiguracji maszyny wirtualnej z umową SLA platformy Azure, należy wybrać Premium Storage dla wszystkich powyższych dysków wymienionych jako usługa Azure Standard Storage (SXX).


> [!NOTE]
> Podane zalecenia dotyczące konfiguracji dysków dotyczą minimalnych wymagań SAP Express na rzecz dostawców infrastruktury. W rzeczywistych wdrożeniach klientów i scenariuszach obciążeń napotkano sytuacje, w których te zalecenia nadal nie zapewniały wystarczających możliwości. Mogą to być sytuacje, w których klient wymagał szybszego załadowania danych po ponownym uruchomieniu platformy HANA lub w przypadku, gdy konfiguracje kopii zapasowych wymagały wyższego poziomu przepustowości dla magazynu. Inne przypadki uwzględnione w **/Hana/log** , gdzie 5000 IOPS nie są wystarczające dla określonego obciążenia. W związku z tym zapoznaj się z tymi zaleceniami jako punkt wyjścia i Dostosuj się na podstawie wymagań obciążeń.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Konfiguracja usługi Azure Ultra Disk Storage dla SAP HANA
Firma Microsoft jest w trakcie wdrażania nowego typu usługi Azure Storage o nazwie [Azure Ultra Disk](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). Duże różnice między usługą Azure Storage zaoferowano do tej pory i niezwykle dyskiem jest to, że możliwości dysków nie są już powiązane z rozmiarem dysku. Jako klient można zdefiniować te możliwości dla Ultra Disk:

- Rozmiar dysku z zakresu od 4 GiB do 65 536 GiB
- Zakres IOPS z 100 operacji we/wy na sekundę (wartość maksymalna zależy również od typów maszyn wirtualnych)
- Przepływność magazynu z 300 MB/s do 2 000 MB/s

Niezwykle dysk daje możliwość zdefiniowania jednego dysku, który spełnia rozmiar, operacje we/wy i zakres przepływności dysku. Zamiast używać menedżerów woluminów logicznych, takich jak LVM lub MDADM na platformie Azure Premium Storage, do konstruowania woluminów, które spełniają wymagania dotyczące przepustowości operacji wejścia/wyjścia na sekundę. Konfigurację można uruchomić między dyskami Ultra i Premium Storage. W związku z tym można ograniczyć użycie Ultra Disk do krytycznych woluminów/Hana/Data i/Hana/log, a także pokryć inne woluminy za pomocą Premium Storage platformy Azure

Inne zalety programu Ultra Disk mogą być lepszym opóźnieniem odczytu w porównaniu do Premium Storage. Szybsze odczyty mogą mieć zalety, gdy chcesz zmniejszyć czas uruchamiania platformy HANA i kolejne obciążenie danymi do pamięci. Zalety magazynu Ultra Disk można również wypróbować, gdy HANA zapisuje punktów zapisu. Ponieważ Premium Storage dyski dla/Hana/Data nie są zwykle akcelerator zapisu pamięci podręcznej, opóźnienie zapisu do/Hana/Data na Premium Storage w porównaniu z dyskiem Ultra jest wyższe. Można spodziewać się, że zapis w punkcie zapisu za pomocą Ultra Disk działa lepiej na obudowie Ultra Disk.

> [!IMPORTANT]
> Niezwykle dysk nie jest jeszcze obecny we wszystkich regionach świadczenia usługi Azure i nie obsługuje jeszcze wszystkich typów maszyn wirtualnych wymienionych poniżej. Aby uzyskać szczegółowe informacje o tym, gdzie jest dostępna funkcja Ultra Disk i które rodziny maszyn wirtualnych są obsługiwane, zobacz artykuł [jakie typy dysków są dostępne na platformie Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Zalecane rozwiązanie do produkcji magazynu z czystym konfiguracją Ultra Disk
W tej konfiguracji należy osobno przechowywać woluminy/Hana/Data i/Hana/log. Sugerowane wartości są wyprowadzane z kluczowych wskaźników wydajności, które SAP muszą certyfikowanie typów maszyn wirtualnych dla konfiguracji SAP HANA i magazynu zgodnie z zaleceniami w [dokumentacji magazynu SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | wolumin/Hana/Data | przepływność we/wy/Hana/Data | /Hana/Data IOPS | wolumin/Hana/log | przepływność we/wy/Hana/log | /Hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 600 GB | 700 MB/s | 7500 | 512 GB | 500 MB/s  | 2000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MB/s | 7500 | 256 GB | 250 MB/s  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MB/s | 7500 | 256 GB | 250 MB/s  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 600 MB/s | 7500 | 512 GB | 400 MB/s  | 2500 |
| M64s | 1000 GiB | 1 000 MB/s |  1200 GB | 600 MB/s | 7500 | 512 GB | 400 MB/s  | 2500 |
| M64ms | 1750 GiB | 1 000 MB/s | 2100 GB | 600 MB/s | 7500 | 512 GB | 400 MB/s  | 2500 |
| M128s | 2000 GiB | 2 000 MB/s |2400 GB | 1200 Mb/s |9000 | 512 GB | 800 MB/s  | 3000 | 
| M128ms | 3800 GiB | 2 000 MB/s | 4800 GB | 1200 Mb/s |9000 | 512 GB | 800 MB/s  | 3000 | 
| M208s_v2 | 2850 GiB | 1 000 MB/s | 3500 GB | 1000 MB/s | 9000 | 512 GB | 400 MB/s  | 2500 | 
| M208ms_v2 | 5700 GiB | 1 000 MB/s | 7200 GB | 1000 MB/s | 9000 | 512 GB | 400 MB/s  | 2500 | 
| M416s_v2 | 5700 GiB | 2 000 MB/s | 7200 GB | 1500MBps | 9000 | 512 GB | 800 MB/s  | 3000 | 
| M416ms_v2 | 11400 GiB | 2 000 MB/s | 14400 GB | 1500 MB/s | 9000 | 512 GB | 800 MB/s  | 3000 |   

Typy maszyn wirtualnych M416xx_v2 nie są jeszcze udostępniane publicznie przez firmę Microsoft. Wymienione wartości mają być punktem początkowym i muszą być oceniane względem rzeczywistych wymagań. Zalety korzystania z usługi Azure Ultra Disk polega na tym, że wartości operacji we/wy i przepływności można dostosować bez konieczności wyłączania maszyny wirtualnej lub zatrzymania obciążenia zastosowanego do systemu.   

> [!NOTE]
> Do tej pory migawki magazynu z magazynem Ultra Disk nie są dostępne. Spowoduje to zablokowanie użycia migawek maszyn wirtualnych za pomocą usług Azure Backup

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Zalecane rozwiązanie do produkcji magazynu z czystym konfiguracją Ultra Disk
W tej konfiguracji woluminy/Hana/Data i/Hana/log są na tym samym dysku. Sugerowane wartości są wyprowadzane z kluczowych wskaźników wydajności, które SAP muszą certyfikowanie typów maszyn wirtualnych dla konfiguracji SAP HANA i magazynu zgodnie z zaleceniami w [dokumentacji magazynu SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Pamięć RAM | Maksymalnie z WE/WY MASZYNY WIRTUALNEJ<br /> Przepływność | Wolumin dla/Hana/Data i/log | /Hana/Data i przepływność we/wy dziennika | /Hana/Data i operacje we/wy dziennika |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 1 200 GB | 1 200 MB/s | 9 500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 MB/s | 9 500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 MB/s | 9 500 | 
| M64ls | 512 GiB | 1 000 MB/s | 1 100 GB | 900 MB/s | 10 000 | 
| M64s | 1000 GiB | 1 000 MB/s |  1 700 GB | 900 MB/s | 10 000 | 
| M64ms | 1750 GiB | 1 000 MB/s | 2 600 GB | 900 MB/s | 10 000 | 
| M128s | 2000 GiB | 2 000 MB/s |2 900 GB | 1 800 MB/s |12 000 | 
| M128ms | 3800 GiB | 2 000 MB/s | 5 300 GB | 1 800 MB/s |12 000 |  
| M208s_v2 | 2850 GiB | 1 000 MB/s | 4 000 GB | 900 MB/s | 10 000 |  
| M208ms_v2 | 5700 GiB | 1 000 MB/s | 7 700 GB | 900 MB/s | 10 000 | 
| M416s_v2 | 5700 GiB | 2 000 MB/s | 7 700 GB | 1, 800MBps | 12 000 |  
| M416ms_v2 | 11400 GiB | 2 000 MB/s | 15 000 GB | 1 800 MB/s | 12 000 |    

Typy maszyn wirtualnych M416xx_v2 nie są jeszcze udostępniane publicznie przez firmę Microsoft. Wymienione wartości mają być punktem początkowym i muszą być oceniane względem rzeczywistych wymagań. Zalety korzystania z usługi Azure Ultra Disk polega na tym, że wartości operacji we/wy i przepływności można dostosować bez konieczności wyłączania maszyny wirtualnej lub zatrzymania obciążenia zastosowanego do systemu.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Woluminy NFS v 4.1 na Azure NetApp Files
Azure NetApp Files zapewnia natywne udziały NFS, które mogą być używane dla woluminów/Hana/Shared,/Hana/Data i/Hana/log. Używanie udziałów systemu plików NFS opartych na ANF dla tych woluminów wymaga użycia protokołu systemu plików NFS w wersji 4.1. Protokół NFS w wersji 3 nie jest obsługiwany w przypadku użycia woluminów związanych z platformą HANA podczas bazowania udziałów w ANF. 

> [!IMPORTANT]
> Protokół NFS v3 zaimplementowany w Azure NetApp Files nie jest obsługiwany do użycia dla/Hana/Shared,/Hana/Data i/Hana/log

Aby spełnić wymagania dotyczące opóźnień magazynu, ważne jest, aby maszyny wirtualne, które używają tych woluminów NFS dla SAP HANA, znajdują się w pobliżu infrastruktury ANF. Aby to osiągnąć, maszyny wirtualne muszą być umieszczone z pomocą techniczną firmy Microsoft w sąsiedztwie infrastruktury ANF. Aby umożliwić firmie Microsoft wykonywanie takich rozmieszczenia w sąsiedztwie, firma Microsoft publikuje formularz, który będzie pytał o dane i pusty zestaw dostępności platformy Azure. W razie potrzeby firma Microsoft umieści zestaw dostępności blisko infrastruktury ANF. 

Infrastruktura ANF zapewnia różne kategorie wydajności. Te kategorie są udokumentowane na [poziomach usługi dla Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

> [!NOTE]
> Zalecane jest użycie kategorii ANF Ultra Storage dla/Hana/Data i/Hana/log. W przypadku/Hana/Shared jest wystarczająca Kategoria standardowa lub Premium

Zalecenia dotyczące zalecanej przepływności woluminów NFS opartych na ANF są publikowane wkrótce.

Dokumentacja opisująca sposób tworzenia konfiguracji programu dla skalowania w poziomie n + m zostanie wkrótce opublikowana.


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz:

- [SAP HANA Przewodnik wysokiej dostępności dla maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
