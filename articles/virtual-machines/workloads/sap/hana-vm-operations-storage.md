---
title: Konfiguracje magazynu maszyn wirtualnych platformy SAP HANA Azure | Dokumenty firmy Microsoft
description: Zalecenia dotyczące magazynu dla maszyn wirtualnych, które mają SAP HANA wdrożone w nich.
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
ms.date: 03/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b469c098db4f8d90147b491bcb54bd55d326b03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080312"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Konfiguracje magazynu maszyn wirtualnych platformy Azure SAP HANA

Platforma Azure udostępnia różne typy magazynu, które są odpowiednie dla maszyn wirtualnych platformy Azure z systemem SAP HANA. **Typy magazynu platformy Azure z certyfikatem PLATFORMY AZURE z certyfikatem SAP HANA,** które można rozważyć dla listy wdrożeń SAP HANA, takie jak: 

- Azure Premium SSD  
- [Dysk w warstwie Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Aby dowiedzieć się więcej o tych typach dysków, zobacz artykuł [Wybieranie typu dysku](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Platforma Azure oferuje dwie metody wdrażania dysków VHD w usłudze Azure Standard i w magazynie w wersji Premium. Jeśli ogólny scenariusz na to pozwala, skorzystaj z wdrożenia [dysku zarządzanego platformy Azure.](https://azure.microsoft.com/services/managed-disks/) 

Aby uzyskać listę typów magazynu i ich łas SLA w usługach We/Wy i przepływności magazynu, zapoznaj [się z dokumentacją platformy Azure dla dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Niezależnie od wybranego typu magazynu platformy Azure system plików, który jest używany w tym magazynie, musi być obsługiwany przez sap dla określonego systemu operacyjnego i systemu DBMS. [Uwaga dotycząca obsługi SAP #405827](https://launchpad.support.sap.com/#/notes/405827) zawiera listę obsługiwanych systemów plików dla różnych systemów operacyjnych i baz danych, w tym SAP HANA. Dotyczy to wszystkich woluminów SAP HANA może uzyskać dostęp do odczytu i zapisu dla dowolnego zadania. W szczególności przy użyciu systemu plików NFS na platformie Azure dla SAP HANA, dodatkowe ograniczenia wersji systemu plików NFS mają zastosowanie, jak podano w dalszej części tego artykułu 


Minimalne warunki certyfikowane SAP HANA dla różnych typów magazynu to: 

- Usługa Azure Premium SSD — /hana/log musi być buforowana za pomocą [akceleratora zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)azure. Wolumin /hana/data może zostać umieszczony na dyskach SSD w wersji Premium bez akceleratora zapisu Azure lub na dysku Ultra
- Dysk Azure Ultra co najmniej dla woluminu /hana/log. Wolumin /hana/data można umieścić na dysku SSD premium bez akceleratora usługi Azure Write lub w celu uzyskania szybszego czasu ponownego uruchamiania dysku Ultra
- **Woluminy NFS w wersji 4.1** na górze usługi Azure NetApp Files dla /hana/log **i** /hana/data. Wolumin /hana/shared może używać protokołu NFS v3 lub NFS v4.1. Protokół NFS v4.1 jest obowiązkowy dla woluminów /hana/data i/hana/log.

Niektóre typy magazynu można łączyć. Na przykład możliwe jest umieszczenie /hana/data w magazynie w wersji Premium i /hana/log można umieścić na dysku Ultra w celu uzyskania wymaganego niskiego opóźnienia. Jeśli jednak używasz woluminu NFS v4.1 opartego na anf dla /hana/data, musisz użyć innego woluminu NFS v4.1 opartego na anf dla /hana/log. Korzystanie z usługi NFS na jednym z woluminów (takich jak /hana/data) i dysk Azure Premium Storage lub Ultra dla innego woluminu (np. /hana/log) nie jest **obsługiwane.**

W środowisku lokalnym rzadko trzeba było dbać o podsystemy We/Wy i jego możliwości. Powodem było to, że dostawca urządzenia musiał upewnić się, że spełnione są minimalne wymagania dotyczące magazynowania dla systemu SAP HANA. Podczas samodzielnego tworzenia infrastruktury platformy Azure należy pamiętać o niektórych z tych wymagań dotyczących programu SAP. Niektóre minimalne właściwości przepływności, które są wymagane od sap, powodują potrzebę:

- Włącz odczyt/zapis na **/hana/log** o rozmiarze 250 MB/s z rozmiarami 1 MB we/wy
- Włącz aktywność odczytu o rozmiarze co najmniej 400 MB/s dla **/hana/data** dla rozmiarów we/wy 16 MB i 64 MB
- Włącz aktywność zapisu o rozmiarze co najmniej 250 MB/s dla **/hana/data** o rozmiarze 16 MB i 64 MB we/wy

Biorąc pod uwagę, że małe opóźnienia pamięci masowej ma kluczowe znaczenie dla systemów DBMS, nawet jako DBMS, takich jak SAP HANA, zachować dane w pamięci. Ścieżka krytyczna w magazynie jest zwykle wokół zapisów dziennika transakcji systemów DBMS. Ale również operacje, takie jak zapisywanie punktów zapisu lub ładowanie danych w pamięci po odzyskiwaniu awarii może być krytyczna. W związku z tym jest **obowiązkowe,** aby korzystać z dysków premium platformy Azure dla **/hana/data** i **/hana/log** woluminów. Aby osiągnąć minimalną przepływność **/hana/log** i **/hana/data** zgodnie z wymaganiami SAP, należy utworzyć raid 0 przy użyciu mdadm lub LVM na wielu dyskach usługi Azure Premium Storage. I użyj woluminów RAID jako **/hana/data** i **/hana/log.** 

**Zalecenie: Jako rozmiary pasków dla RAID 0 zaleca się użycie:**

- 256 KB dla **/hana/data**
- 32 KB dla **/hana/log**

> [!IMPORTANT]
> Rozmiar paska dla /hana/data został zmieniony z wcześniejszych zaleceń wzywających do 64 KB lub 128 KB do 256 KB na podstawie doświadczeń klientów z nowszymi wersjami systemu Linux. Rozmiar 256 KB zapewnia nieco lepszą wydajność

> [!NOTE]
> Nie trzeba konfigurować żadnego poziomu nadmiarowości przy użyciu woluminów RAID, ponieważ usługa Azure Premium i magazyn standardowy przechowują trzy obrazy dysku VHD. Użycie woluminu RAID polega wyłącznie na konfigurowaniu woluminów, które zapewniają wystarczającą przepustowość we/wy.

Gromadzenie wielu dysków VHD platformy Azure pod raid, jest akumulacji od strony We/Wy i magazynu przepływności. Tak jeśli umieścisz RAID 0 ponad 3 x P30 Dyski usługi Azure Premium Storage, należy zapewnić trzy razy Usługi We/Wy i trzy razy przepływność magazynu jednego dysku usługi Azure Premium Storage P30.

Również zachować ogólną przepływność we/wy maszyny Wirtualnej pamiętać podczas określania rozmiaru lub podejmowania decyzji o maszynie Wirtualnej. Ogólna przepustowość magazynu maszyn wirtualnych jest udokumentowana w artykule [Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Tryb planowania we/wy systemu Linux
Linux ma kilka różnych trybów planowania we/wy. Wspólne zalecenie za pośrednictwem dostawców linuksa i SAP jest ponowne skonfigurowanie trybu harmonogramu we/wy dla woluminów dyskowych z trybu **cfq** do **noop** (non-multiqueue) lub **żaden** dla (wieloekonkowy) tryb. Szczegóły są przywoły, do których odwołuje się [#1984787 SAP Note](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Rozwiązania z magazynem w wersji Premium i akceleratorem zapisu platformy Azure dla maszyn wirtualnych z serii M platformy Azure
Akcelerator zapisu azure to funkcja, która jest dostępna wyłącznie dla maszyn wirtualnych z serii M platformy Azure. Jak sama nazwa stwierdza, celem funkcji jest zwiększenie opóźnienia we/wy zapisów względem usługi Azure Premium Storage. W przypadku sap HANA akcelerator zapisu ma być używany tylko dla woluminu **/hana/log.** W związku z tym **/hana/data** i **/hana/log** są oddzielne woluminy z akceleratorem zapisu Azure obsługujących tylko wolumin **/hana/log.** 

> [!IMPORTANT]
> Podczas korzystania z usługi Azure Premium Storage użycie [akceleratora zapisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) platformy Azure lub woluminu /hana/log jest obowiązkowe. Akcelerator zapisu jest dostępny tylko dla dysków masowych klasy premium oraz maszyn wirtualnych serii M i Mv2.

Poniższe zalecenia dotyczące buforowania zakładają, że cechy we/wy dla sap hana, które mają taką listę, jak:

- Nie ma prawie żadnego obciążenia odczytu względem plików danych HANA. Wyjątkami są we/wy o dużym rozmiarze po ponownym uruchomieniu wystąpienia HANA lub po załadowaniu danych do hana. Innym przypadkiem większych we/wy odczytu plików danych mogą być kopie zapasowe bazy danych HANA. W rezultacie buforowanie odczytu w większości nie ma sensu, ponieważ w większości przypadków wszystkie woluminy plików danych muszą być odczytywane całkowicie.
- Zapisywanie przed plikami danych jest doświadczenie w seriach opartych na hana savepoints i odzyskiwania awarii HANA. Zapisywanie punktów zapisu jest asynchroniczne i nie wstrzymują żadnych transakcji użytkownika. Zapisywanie danych podczas odzyskiwania po awarii ma kluczowe znaczenie dla wydajności, aby system odpowiedział szybko ponownie. Odzyskiwanie awaryjne powinno być jednak raczej wyjątkowymi sytuacjami
- Nie ma prawie żadnych odczytów z plików ponawiania HANA. Wyjątkami są duże operacje we/wy podczas wykonywania kopii zapasowych dziennika transakcji, odzyskiwania po awarii lub w fazie ponownego uruchamiania wystąpienia HANA.  
- Główne obciążenie względem pliku dziennika ponownego rejestrowaa SAP HANA jest zapisy. W zależności od charakteru obciążenia można mieć we/wy tak małe, jak 4 KB lub w innych przypadkach we/wy rozmiary 1 MB lub więcej. Opóźnienie zapisu względem dziennika ponawienia SAP HANA jest krytyczne pod względem wydajności.
- Wszystkie zapisy muszą być utrwalane na dysku w niezawodny sposób

**Zalecenie: W wyniku tych obserwowanych wzorców we/wy przez SAP HANA buforowanie dla różnych woluminów przy użyciu usługi Azure Premium Storage powinno być ustawione tak:**

- **/hana/data** - brak buforowania
- **/hana/log** - brak buforowania - wyjątek dla serii M- i Mv2, gdzie akcelerator zapisu powinien być włączony bez buforowania odczytu. 
- **/hana/shared** - czytaj buforowanie

### <a name="production-recommended-storage-solution"></a>Zalecane w produkcji rozwiązanie pamięci masowej

> [!IMPORTANT]
> Certyfikat SAP HANA dla maszyn wirtualnych z serii M platformy Azure jest wyłącznie z akceleratorem zapisu azure dla woluminu **/hana/log.** W rezultacie oczekuje się, że scenariusz produkcyjny wdrożenia SAP HANA na maszynach wirtualnych z serii M platformy Azure zostaną skonfigurowane z akceleratorem zapisu Azure dla woluminu **/hana/log.**  

> [!NOTE]
> W przypadku scenariuszy produkcyjnych sprawdź, czy określony typ maszyny Wirtualnej jest obsługiwany dla sap hana przez SAP w [dokumentacji SAP dla usługi IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Zalecenia często przekraczają minimalne wymagania SAP, jak wspomniano wcześniej w tym artykule. Wymienione zalecenia są kompromisem między zaleceniami dotyczącymi rozmiaru przez sap a maksymalną przepływnością magazynu, które zapewniają różne typy maszyn wirtualnych.

**Zalecenie: Zalecane konfiguracje scenariuszy produkcyjnych wyglądają następująco:**

| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z We/Wy maszyny Wirtualnej<br /> Przepływność | /hana/dane | /hana/dziennik | /hana/udostępniony | /wolumin główny | /usr/sap | hana/kopia zapasowa |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts (m32ts) | 192 Gib | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls ( M32ls ) | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls ( M64ls ) | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 Gib | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms ( M64ms ) | 1750 Gib | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | Gib 2000 | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 Gib | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 Gib | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 Gib | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 Gib | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 Gib | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Sprawdź, czy przepływność magazynu dla różnych sugerowanych woluminów spełnia obciążenie, które chcesz uruchomić. Jeśli obciążenie wymaga większych woluminów dla **/hana/data** i **/hana/log**, należy zwiększyć liczbę dysków wirtualnych usługi Azure Premium Storage. Zmiana rozmiaru woluminu z większą liczbą dysków wirtualnych niż wymieniona zwiększa przepływność we/wy i we/wy w granicach typu maszyny wirtualnej platformy Azure.

Akcelerator zapisu azure działa tylko w połączeniu z [dyskami zarządzanymi platformy Azure.](https://azure.microsoft.com/services/managed-disks/) Tak więc co najmniej dyski usługi Azure Premium Storage tworzące wolumin **/hana/log** muszą zostać wdrożone jako dyski zarządzane.

Istnieją limity dysków wirtualnych usługi Azure Premium Storage na maszynę wirtualną, które mogą być obsługiwane przez akcelerator zapisu azure. Obecne limity to:

- 16 VHD dla maszyn wirtualnych M128xx i M416xx
- 8 VHD dla M64xx i M208xx VM
- 4 VHD dla maszyny wirtualnej M32xx

Bardziej szczegółowe instrukcje dotyczące włączania akceleratora zapisu platformy Azure można znaleźć w artykule [Akcelerator zapisów](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Szczegóły i ograniczenia dotyczące akceleratora zapisu platformy Azure można znaleźć w tej samej dokumentacji.

**Zalecenie: Należy użyć Akceleratora zapisu dla dysków tworzących wolumin /hana/log**


### <a name="cost-conscious-azure-storage-configuration"></a>Konfiguracja usługi Azure Storage z dbałości o koszty
W poniższej tabeli przedstawiono konfigurację typów maszyn wirtualnych, których klienci używają również do hosta sap HANA na maszynach wirtualnych platformy Azure. Mogą istnieć niektóre typy maszyn wirtualnych, które mogą nie spełniać wszystkich minimalnych kryteriów magazynowania dla sap hana lub nie są oficjalnie obsługiwane przez SAP HANA przez SAP. Ale do tej pory te maszyny wirtualne wydawały się działać dobrze w przypadku scenariuszy nieprodukcyjnych. **/hana/data** i **/hana/log** są łączone z jednym woluminem. W rezultacie użycie akceleratora zapisu azure może stać się ograniczeniem pod względem usług We/Wy.

> [!NOTE]
> W przypadku scenariuszy obsługiwanych przez sap sprawdź, czy określony typ maszyny Wirtualnej jest obsługiwany dla sap HANA przez SAP w [dokumentacji SAP dla usługi IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Zmiana z poprzednich zaleceń dotyczących rozwiązania ekonomicznego polega na przejściu z [dysków Azure Standard HDD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) na lepiej wydajne i bardziej [niezawodne dyski Azure Standard SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)

Zalecenia często przekraczają minimalne wymagania SAP, jak wspomniano wcześniej w tym artykule. Wymienione zalecenia są kompromisem między zaleceniami dotyczącymi rozmiaru przez sap a maksymalną przepływnością magazynu, które zapewniają różne typy maszyn wirtualnych.

| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z We/Wy maszyny Wirtualnej<br /> Przepływność | /hana/data i /hana/log<br /> paski z LVM lub MDADM | /hana/udostępniony | /wolumin główny | /usr/sap | hana/kopia zapasowa |
| --- | --- | --- | --- | --- | --- | --- | -- |
| Serwer DS14v2 | 112 Gib | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 Gib | 1200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 Gib | 2000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts (m32ts) | 192 Gib | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls ( M32ls ) | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls ( M64ls ) | 512 GiB | 1000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 Gib | 1000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms ( M64ms ) | 1 750 Gib | 1000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 Gib | 2000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3,8 tys. | 2000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2 850 Gib | 1000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5,7 tys. | 1000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5,7 tys. | 2000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 Gib | 2000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Dyski zalecane dla mniejszych typów maszyn wirtualnych o rozmiarze 3 x P20 oversize woluminów dotyczących zaleceń dotyczących miejsca zgodnie z [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Jednak wybór wyświetlany w tabeli został dokonany w celu zapewnienia wystarczającej przepływności dysku dla SAP HANA. Jeśli potrzebujesz zmian na woluminie **/hana/backup,** który został dopasowywał się do przechowywania kopii zapasowych, które reprezentują dwukrotnie wolumin pamięci, możesz się dostosować.   
Sprawdź, czy przepływność magazynu dla różnych sugerowanych woluminów spełnia obciążenie, które chcesz uruchomić. Jeśli obciążenie wymaga większych woluminów dla **/hana/data** i **/hana/log**, należy zwiększyć liczbę dysków wirtualnych usługi Azure Premium Storage. Zmiana rozmiaru woluminu z większą liczbą dysków wirtualnych niż wymieniona zwiększa przepływność we/wy i we/wy w granicach typu maszyny wirtualnej platformy Azure. 

> [!NOTE]
> Powyższe konfiguracje NIE będą korzystać z [pojedynczej umowy SLA maszyny wirtualnej platformy Azure,](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) ponieważ używa ona mieszaniny usługi Azure Premium Storage i usługi Azure Standard Storage. Wybór został jednak wybrany w celu optymalizacji kosztów. Należy wybrać magazyn w klasie premium dla wszystkich dysków wymienionych jako Usługa Azure Standard Storage (Sxx), aby konfiguracja maszyny Wirtualnej była zgodna z pojedynczą umowy SLA platformy Azure.


> [!NOTE]
> Zalecenia dotyczące konfiguracji dysku są przeznaczone dla minimalnych wymagań, które SAP wyraża wobec swoich dostawców infrastruktury. W rzeczywistych scenariuszach wdrożeń klientów i obciążenia napotkano sytuacje, w których te zalecenia nadal nie zapewniały wystarczających możliwości. Mogą to być sytuacje, w których klient wymagał szybszego ponownego ładowania danych po ponownym uruchomieniu hana lub gdy konfiguracje kopii zapasowych wymagały wyższej przepustowości do magazynu. Inne przypadki obejmowały **/hana/log,** gdzie 5000 Operacji We/Wy nie były wystarczające dla określonego obciążenia. Dlatego potraktuj te zalecenia jako punkt wyjścia i dostosuj je w zależności od wymagań obciążenia pracą.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Konfiguracja magazynu dysków Azure Ultra dla systemu SAP HANA
Firma Microsoft jest w trakcie wprowadzania nowego typu magazynu platformy Azure o nazwie [Dysk Azure Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). Istotną różnicą między magazynem platformy Azure oferowanym do tej pory i ultra dysk jest, że możliwości dysku nie są już powiązane z rozmiarem dysku. Jako klient możesz zdefiniować następujące możliwości dla dysku Ultra:

- Rozmiar dysku od 4 GiB do 65 536 GiB
- Zakres IOPS od 100 IOPS do 160K WeOPS (maksymalna zależy od typów maszyn wirtualnych)
- Przepustowość magazynu od 300 MB/s do 2000 MB/s

Dysk ultra umożliwia zdefiniowanie pojedynczego dysku, który spełnia rozmiar, we/wy i zakres przepływności dysku. Zamiast używać menedżerów woluminów logicznych, takich jak LVM lub MDADM na platformie Azure Premium Storage do konstruowania woluminów, które spełniają wymagania dotyczące przepływności we/wy i magazynu. Można uruchomić kombinację konfiguracji między dyskiem Ultra i Magazynem Premium. W rezultacie można ograniczyć użycie dysku Ultra do krytycznych wydajności /hana/data i /hana/log i pokryć inne woluminy za pomocą usługi Azure Premium Storage

Inne zalety dysku Ultra może być lepsze opóźnienie odczytu w porównaniu do magazynu w wersji premium. Szybsze opóźnienie odczytu może mieć zalety, gdy chcesz zmniejszyć czas uruchamiania HANA i późniejsze obciążenie danych do pamięci. Zalety pamięci masowej ultra dysków mogą być również odczuwalne, gdy HANA pisze punkty zapisu. Ponieważ dyski magazynu w warstwie Premium dla /hana/data zwykle nie są buforowane akceleratora zapisu, opóźnienie zapisu do /hana/data w magazynie w warstwie Premium w porównaniu z dyskiem Ultra jest wyższe. Można oczekiwać, że zapisywanie punktów zapisu z ultra dysku działa lepiej na dysku Ultra.

> [!IMPORTANT]
> Dysk ultra nie jest jeszcze obecny we wszystkich regionach platformy Azure i nie obsługuje jeszcze wszystkich typów maszyn wirtualnych wymienionych poniżej. Aby uzyskać szczegółowe informacje, gdzie dysk ultra jest dostępny i które rodziny maszyn wirtualnych są obsługiwane, zapoznaj się z artykułem [Jakie typy dysków są dostępne na platformie Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Zalecane w produkcji rozwiązanie pamięci masowej z czystą konfiguracją dysku Ultra
W tej konfiguracji należy przechowywać woluminy /hana/data i /hana/log oddzielnie. Sugerowane wartości są wyprowadzane z kluczowych wskaźników wydajności, które sap musi certyfikować typy maszyn wirtualnych dla SAP HANA i konfiguracji magazynu zgodnie z zaleceniami w [białym papierie SAP TDI Storage .](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)

Zalecenia często przekraczają minimalne wymagania SAP, jak wspomniano wcześniej w tym artykule. Wymienione zalecenia są kompromisem między zaleceniami dotyczącymi rozmiaru przez sap a maksymalną przepływnością magazynu, które zapewniają różne typy maszyn wirtualnych.

| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z We/Wy maszyny Wirtualnej<br /> Przepływność | /hana/ilość danych | /hana/data Przepływność we/wy | /hana/data WeOPS | /hana/log woluminu | Przepustowość /hana/log We/Wy | /hana/dziennik We/Wy |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 Gib | 1200 MB/s | 600 GB | 700 mb/s | 7500 | 512 GB | 500 mb/s  | 2000 |
| M32ts (m32ts) | 192 Gib | 500 MB/s | 250 GB | 400 mb/s | 7500 | 256 GB | 250 mb/s  | 2000 |
| M32ls ( M32ls ) | 256 GiB | 500 MB/s | 300 GB | 400 mb/s | 7500 | 256 GB | 250 mb/s  | 2000 |
| M64ls ( M64ls ) | 512 GiB | 1000 MB/s | 600 GB | 600 mb/s | 7500 | 512 GB | 400 mb/s  | 2500 |
| M64s | 1000 Gib | 1000 MB/s |  1200 GB | 600 mb/s | 7500 | 512 GB | 400 mb/s  | 2500 |
| M64ms ( M64ms ) | 1 750 Gib | 1000 MB/s | 2100 GB | 600 mb/s | 7500 | 512 GB | 400 mb/s  | 2500 |
| M128s | 2000 Gib | 2000 MB/s |2400 GB | 1200 mb/s |9000 | 512 GB | 800 mb/s  | 3000 | 
| M128ms | 3,8 tys. | 2000 MB/s | 4800 GB | 1200 mb/s |9000 | 512 GB | 800 mb/s  | 3000 | 
| M208s_v2 | 2 850 Gib | 1000 MB/s | 3500 GB | 1000 mb/s | 9000 | 512 GB | 400 mb/s  | 2500 | 
| M208ms_v2 | 5,7 tys. | 1000 MB/s | 7200 GB | 1000 mb/s | 9000 | 512 GB | 400 mb/s  | 2500 | 
| M416s_v2 | 5,7 tys. | 2000 MB/s | 7200 GB | 1500 mb/s | 9000 | 512 GB | 800 mb/s  | 3000 | 
| M416ms_v2 | 11 400 Gib | 2000 MB/s | 14 400 GB | 1500 mb/s | 9000 | 512 GB | 800 mb/s  | 3000 |   

Wymienione wartości mają być punktem wyjścia i muszą być oceniane pod kątem rzeczywistych wymagań. Zaletą dysku Usługi Azure Ultra jest to, że wartości dla usług We/Wy i przepływność można dostosować bez konieczności zamykania maszyny wirtualnej lub zatrzymywania obciążenia zastosowanego do systemu.   

> [!NOTE]
> Do tej pory migawki magazynu z pamięcią ultra dysków nie są dostępne. Blokuje to użycie migawek maszyn wirtualnych za pomocą usług Azure Backup Services

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Ekonomiczne rozwiązanie pamięci masowej z czystą konfiguracją dysku Ultra
W tej konfiguracji woluminy /hana/data i /hana/log na tym samym dysku. Sugerowane wartości są wyprowadzane z kluczowych wskaźników wydajności, które sap musi certyfikować typy maszyn wirtualnych dla SAP HANA i konfiguracji magazynu zgodnie z zaleceniami w [białym papierie SAP TDI Storage .](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 

Zalecenia często przekraczają minimalne wymagania SAP, jak wspomniano wcześniej w tym artykule. Wymienione zalecenia są kompromisem między zaleceniami dotyczącymi rozmiaru przez sap a maksymalną przepływnością magazynu, które zapewniają różne typy maszyn wirtualnych.

| Jednostka SKU maszyny wirtualnej | Pamięć RAM | Maksymalnie z We/Wy maszyny Wirtualnej<br /> Przepływność | Wolumin dla /hana/data i /log | /hana/data i przepustowość we/wy dziennika | /hana/data i dziennik We/Wy |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 Gib | 1200 MB/s | 1200 GB | 1200 mb/s | 9,500 | 
| M32ts (m32ts) | 192 Gib | 500 MB/s | 512 GB | 400 mb/s | 9,500 | 
| M32ls ( M32ls ) | 256 GiB | 500 MB/s | 600 GB | 400 mb/s | 9,500 | 
| M64ls ( M64ls ) | 512 GiB | 1000 MB/s | 1100 GB | 900 mb/s | 10 000 | 
| M64s | 1000 Gib | 1000 MB/s |  1700 GB | 900 mb/s | 10 000 | 
| M64ms ( M64ms ) | 1 750 Gib | 1000 MB/s | 2600 GB | 900 mb/s | 10 000 | 
| M128s | 2000 Gib | 2000 MB/s |2900 GB | 1800 mb/s |12 000 | 
| M128ms | 3,8 tys. | 2000 MB/s | 5300 GB | 1800 mb/s |12 000 |  
| M208s_v2 | 2 850 Gib | 1000 MB/s | 4000 GB | 900 mb/s | 10 000 |  
| M208ms_v2 | 5,7 tys. | 1000 MB/s | 7700 GB | 900 mb/s | 10 000 | 
| M416s_v2 | 5,7 tys. | 2000 MB/s | 7700 GB | 1800 mb/s | 12 000 |  
| M416ms_v2 | 11 400 Gib | 2000 MB/s | 15 000 GB | 1800 mb/s | 12 000 |    

Wymienione wartości mają być punktem wyjścia i muszą być oceniane pod kątem rzeczywistych wymagań. Zaletą dysku Usługi Azure Ultra jest to, że wartości dla usług We/Wy i przepływność można dostosować bez konieczności zamykania maszyny wirtualnej lub zatrzymywania obciążenia zastosowanego do systemu.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Woluminy NFS w wersji 4.1 w plikach NetApp platformy Azure
Usługa Azure NetApp Files udostępnia natywne udziały NFS, które mogą być używane dla woluminów /hana/shared, /hana/data i /hana/log. Korzystanie z udziałów NFS opartych na ugeników anf dla woluminów /hana/data i /hana/log wymaga użycia protokołu NFS w wersji 4.1. Protokół NFS w wersji 3 nie jest obsługiwany przy użyciu woluminów /hana/data i /hana/log podczas bazowania udziałów na treści ANF. 

> [!IMPORTANT]
> Protokół NFS w wersji 3 zaimplementowany w usłudze Azure NetApp Files **nie** jest obsługiwany do użycia dla /hana/data i /hana/log. Użycie NFS 4.1 jest obowiązkowe dla woluminów /hana/data i /hana/log z funkcjonalnego punktu widzenia. Natomiast dla woluminu /hana/shared można używać protokołu NFS v3 lub NFS v4.1.

### <a name="important-considerations"></a>Istotne zagadnienia
Rozważając usługi Azure NetApp Files dla SAP Netweaver i SAP HANA, należy pamiętać o następujących ważnych zagadnieniach:

- Minimalna pula miejsc wynosi 4 TiB.  
- Minimalna wielkość głośności to 100 GiB
- Usługi Azure NetApp Files i wszystkie maszyny wirtualne, na których będą zainstalowane woluminy usługi Azure NetApp Files, muszą znajdować się w tej samej sieci wirtualnej platformy Azure lub w [sieciach wirtualnych równorzędnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) w tym samym regionie.  
- Wybrana sieć wirtualna musi mieć podsieć delegowaną do plików NetApp platformy Azure.
- Przepływność woluminu NetApp platformy Azure jest funkcją przydziału woluminu i poziomu usługi, zgodnie z [poziomem usługi dla usługi Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Podczas zmiany rozmiaru woluminów HANA Azure NetApp upewnij się, że przepływność wynikowa spełnia wymagania systemowe HANA.  
- Usługa Azure NetApp Files oferuje [zasady eksportu:](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)można kontrolować dozwolonych klientów, typ dostępu (odczyt&zapisu, tylko do odczytu itp.). 
- Funkcja usługi Azure NetApp Files nie jest jeszcze świadoma strefy. Obecnie funkcja Usługi Azure NetApp Files nie jest wdrażana we wszystkich strefach dostępności w regionie platformy Azure. Należy pamiętać o potencjalnych implikacje opóźnienia w niektórych regionach platformy Azure.  
- Ważne jest, aby maszyny wirtualne były wdrażane w pobliżu magazynu NetApp platformy Azure ze względu na małe opóźnienia. 
- Identyfikator użytkownika dla <b>sid</b>adm i identyfikator `sapsys` grupy na maszynach wirtualnych musi być zgodny z konfiguracją w plikach NetApp usługi Azure. 

> [!IMPORTANT]
> W przypadku obciążeń SAP HANA małe opóźnienia mają kluczowe znaczenie. Współpracuj z przedstawicielem firmy Microsoft, aby upewnić się, że maszyny wirtualne i woluminy plików NetApp platformy Azure są wdrażane w bliskiej odległości.  

> [!IMPORTANT]
> Jeśli między <b>identyfikatorem</b>użytkownika sid adm a identyfikatorem grupy `sapsys` między maszyną wirtualną a konfiguracją usługi Azure NetApp występuje niezgodność między identyfikatorem użytkownika, `nobody`uprawnienia do plików na woluminach NetApp platformy Azure, zamontowanych na maszynach wirtualnych, będą wyświetlane jako . Podczas [wprowadzania nowego systemu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) do usługi Azure NetApp Files `sapsys`należy określić prawidłowy identyfikator użytkownika dla <b>identyfikatora sid</b>adm i identyfikator grupy.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Rozmiar bazy danych HANA w plikach NetApp usługi Azure

Przepływność woluminu NetApp platformy Azure jest funkcją rozmiaru woluminu i poziomu usługi, zgodnie z poziomem [usługi dla usługi Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Podczas projektowania infrastruktury sap na platformie Azure należy pamiętać o niektórych minimalnych wymagań dotyczących przepływności magazynu przez SAP, które przekładają się na minimalne właściwości przepływności:

- Włącz odczyt/zapis na /hana/log o rozmiarze 250 MB/s z rozmiarami 1 MB we/wy  
- Włącz aktywność odczytu o rozmiarze co najmniej 400 MB/s dla /hana/data dla rozmiarów we/wy 16 MB i 64 MB  
- Włącz aktywność zapisu o rozmiarze co najmniej 250 MB/s dla /hana/data o rozmiarze 16 MB i 64 MB we/wy  

[Limity przepływności plików NetApp platformy Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) na 1 wartość limitu woluminu to:
- Warstwa pamięci masowej w warstwie Premium — 64 MiB/s  
- Poziom ultra pamięci masowej — 128 MiB/s  

> [!IMPORTANT]
> Niezależnie od pojemności wdrażanej na jednym woluminie systemu plików NFS, oczekuje się, że przepustowość ma być płaskowyżowa w zakresie przepustowości 1,2-1,4 GB/s, która jest korzystać przez konsumenta na maszynie wirtualnej. Ma to związek z podstawową architekturą oferty ANF i powiązanymi limitami sesji systemu Linux wokół systemu plików NFS. Numery wydajności i przepływności, zgodnie z dokumentami w artykule [Wyniki testów porównawczych wydajności dla plików NetApp platformy Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-benchmarks) zostały przeprowadzone na jednym udostępnionym woluminie NFS z wieloma maszynami wirtualnymi klienta i w rezultacie z wieloma sesjami. Ten scenariusz różni się od scenariusza, który mierzymy w sap. Gdzie mierzymy przepływność z pojedynczej maszyny Wirtualnej względem woluminu NFS. hostowane na ANF.

Aby spełnić minimalne wymagania SAP dotyczące przepływności danych i dziennika `/hana/shared`oraz zgodnie z wytycznymi dotyczącymi zalecanego rozmiaru będą wyglądać następująco:

| Wolumin | Rozmiar<br /> Warstwa magazynu w warstwie Premium Storage | Rozmiar<br /> Warstwa Ultra Storage | Obsługiwany protokół NFS |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | wersja 4.1 |
| /hana/dane | 6.3 TiB | 3.2 TiB | wersja 4.1 |
| /hana/udostępniony | Maksymalna (512 GB, 1xRAM) na 4 węzły procesu roboczego | Maksymalna (512 GB, 1xRAM) na 4 węzły procesu roboczego | wersja 3 lub v4.1 |


> [!NOTE]
> Zalecenia dotyczące rozmiaru plików NetApp platformy Azure, które zostały przedstawione w tym miejscu, są przeznaczone do spełnienia minimalnych wymagań, które sap wyraża wobec swoich dostawców infrastruktury. W rzeczywistych scenariuszy wdrożeń klientów i obciążenia, które mogą nie być wystarczające. Użyj tych zaleceń jako punktu wyjścia i dostosować, w oparciu o wymagania określonego obciążenia.  

W związku z tym można rozważyć wdrożenie podobnej przepływności dla woluminów ANF, jak wymienione dla magazynu dysków Ultra już. Należy również wziąć pod uwagę rozmiary dla rozmiarów wymienionych dla woluminów dla różnych jednostek SKU maszyny Wirtualnej, jak to miało miejsce w tabelach dysków Ultra.

> [!TIP]
> Woluminy usługi Azure NetApp Files można dynamicznie ponownie `unmount` rozmiarować, bez konieczności woluminów, zatrzymać maszyny wirtualne lub zatrzymać SAP HANA. Dzięki temu elastyczność w celu spełnienia wymagań zarówno oczekiwanych, jak i nieprzewidzianych przepływności jest taka, że jest ona pożądanych.

Dokumentacja dotycząca wdrażania konfiguracji skalowania w poziomie SAP HANA z węzłem rezerwowym przy użyciu woluminów NFS w wersji 4.1 hostowanych w programie ANF jest publikowana w [programie SAP HANA skalowano w poziomie przy użyciu węzła wstrzymania na maszynach wirtualnych platformy Azure z plikami NetApp usługi Azure na serwerze SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz:

- [Przewodnik sap HANA o wysokiej dostępności dla maszyn wirtualnych platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
