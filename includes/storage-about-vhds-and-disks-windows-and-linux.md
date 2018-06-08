---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: rogara
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f64645db782b055e1c544f257149411f29fc99d7
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "34806319"
---
## <a name="about-vhds"></a>Informacje o wirtualnych dyskach twardych
Wirtualne dyski twarde używane na platformie Azure to pliki vhd przechowywane jako stronicowe obiekty blob na koncie magazynu w warstwie Standardowa lub Premium na platformie Azure. Aby uzyskać informacje na temat stronicowych obiektów blob, zobacz [Understanding block blobs and page blobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/) (Omówienie blokowych i stronicowych obiektów blob). Aby uzyskać szczegółowe informacje na temat magazynu w warstwie Premium, zobacz [High-performance premium storage and Azure VMs](../articles/virtual-machines/windows/premium-storage.md) (Magazyn w warstwie Premium o wysokiej wydajności i maszyny wirtualne platformy Azure).

Platforma Azure obsługuje stały format VHD dysków. W przypadku stałego formatu dysk logiczny jest zapisywany w pliku w sposób liniowy w taki sposób, że przesunięcie X na dysku jest zapisywane w obiekcie blob z przesunięciem X. Niewielka stopka na końcu obiektu blob opisuje właściwości wirtualnego dysku twardego. Często odpady ustalonym formacie miejsca, ponieważ większość dysków ma duże zakresy nieużywane w nich. Platforma Azure jednak przechowuje pliki vhd w formacie rozrzedzonym, więc jednocześnie uzyskuje się zalety dysków stałych i dynamicznych. Aby uzyskać więcej informacji, zobacz [wprowadzenie do korzystania z wirtualnymi dyskami twardymi](https://technet.microsoft.com/library/dd979539.aspx).

Wszystkie pliki VHD na platformie Azure, który ma być używany jako źródło do utworzenia dysków lub obrazy są tylko do odczytu, z wyjątkiem plików VHD przekazany lub kopiowane do magazynu Azure przez użytkownika (która może być do odczytu / zapisu lub tylko do odczytu). Po utworzeniu dysku lub obrazie Azure tworzy kopie źródła pliki VHD. Te kopie mogą być tylko do odczytu lub do odczytu i zapisu, zależnie od tego, jak korzystasz z wirtualnego dysku twardego.

Gdy tworzysz maszynę wirtualną na podstawie obrazu, platforma Azure tworzy dysk dla maszyny wirtualnej, który jest kopią źródłowego pliku vhd. Platforma Azure umieszcza dzierżawę na każdym pliku vhd używanym do utworzenia obrazu, dysku systemu operacyjnego lub dysku danych, aby chronić ten plik przed przypadkowym usunięciem.

Zanim będzie możliwe usunięcie źródłowego pliku vhd, trzeba będzie usunąć tę dzierżawę, usuwając dysk lub obraz. Aby usunąć plik vhd, który jest używany przez maszynę wirtualną jako dysk systemu operacyjnego, można jednocześnie usunąć maszynę wirtualną, dysk systemu operacyjnego i źródłowy plik vhd, usuwając maszynę wirtualną i wszystkie skojarzone dyski. Usunięcie pliku vhd będącego źródłem dysku danych wymaga jednak wykonania kilku kroków w ustalonej kolejności. Najpierw należy odłączyć dysk od maszyny wirtualnej, później usunąć dysk, a następnie usunąć plik vhd.

> [!WARNING]
> Jeśli usuniesz źródłowy plik vhd z magazynu albo usuniesz swoje konto magazynu, firma Microsoft nie będzie mogła odzyskać usuniętych danych.

## <a name="types-of-disks"></a>Typy dysków 
Dyski platformy Azure zaprojektowano tak, aby zapewniały 99,999% dostępności. Dysku systemu Azure zostały spójnie dostarczone trwałości korporacyjnej, z branży, ZERO % Annualized częstość niepowodzeń.

Istnieją trzy warstwy wydajności dla magazynu, który można wybrać podczas tworzenia dysków--dysków SSD w warstwie Premium, standardowych dysków SSD (wersja zapoznawcza) i magazynu w warstwie standardowa dysk twardy. Ponadto istnieją dwa typy dysków--niezarządzane, a zarządzane.

### <a name="standard-hdd-disks"></a>Dyski standardowe dysk twardy
Dyski standardowe HDD bazują na dyskach HDD i dostarczać ekonomicznego magazynowania. Standardowe magazynu dysku twardego mogą być replikowane lokalnie w centrum danych z jednego lub być geograficznie nadmiarowego z centrów danych podstawowych i pomocniczych. Aby uzyskać więcej informacji na temat replikacji magazynu, zobacz [replikacja usługi Azure Storage](../articles/storage/common/storage-redundancy.md). 

Aby uzyskać więcej informacji o korzystaniu z dysków standardowych dysków Twardych, zobacz [Standard Storage i dysków](../articles/virtual-machines/windows/standard-storage.md).

### <a name="standard-ssd-disks-preview"></a>Dyski SSD standardowe (wersja zapoznawcza)
Dyski SSD standardowe są przeznaczone do adresów tego samego rodzaju obciążenia jako dyski standardowe dysku twardego, ale oferuje bardziej spójny wydajność i niezawodność niż dysk twardy. Dyski SSD standardowe połączyć elementy dyski Premium SSD i HDD standardowa do formularza ekonomicznym rozwiązaniem, które najlepiej nadaje się do aplikacji, takich jak serwery sieci web, które nie wymagają wysokiej IOPS na dyskach. Jeśli jest dostępna, dyski SSD standardowe są opcji wdrażania zalecane dla większości obciążeń. Dyski SSD standardowe są dostępne tylko jako zarządzane dysków i while w podglądzie są dostępne tylko w [wybierz regionów](../articles/virtual-machines/windows/faq-for-disks.md) i z typem odporności magazyn lokalnie nadmiarowy (LRS).

### <a name="premium-ssd-disks"></a>Dyski SSD w warstwie Premium 
Dyski SSD w warstwie Premium bazują na dyskach SSD, a także zapewnia obsługę dysków o wysokiej wydajności i małych opóźnieniach/O wykonujących obciążeń uruchomionych maszyn wirtualnych. Zazwyczaj można dysków Premium SSD o rozmiarze, które obejmują "s" w nazwie serii. Na przykład brak serii Dv3 i Dsv3 serii, serie Dsv3 może być używany z dysków Premium SSD.  Aby uzyskać więcej informacji, zobacz [Premium Storage](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Dyski niezarządzane
Dyski niezarządzane to tradycyjny typ dysków używany przez maszyny wirtualne. Z tych dysków Tworzenie konta magazynu i konto magazynu można określić podczas tworzenia dysku. Upewnij się, nie umieszczaj zbyt wiele dysków z tego samego konta magazynu, ponieważ mogła przekroczyć [wartości docelowe skalowalności](../articles/storage/common/storage-scalability-targets.md) konta magazynu (20 000 IOPS, na przykład), co w maszynach wirtualnych ograniczane. Gdy korzystasz z dysków niezarządzanych, musisz opracować sposób zmaksymalizowania użycia jednego lub większej liczby kont magazynu w celu uzyskania najlepszej wydajności maszyn wirtualnych.

### <a name="managed-disks"></a>Dyski zarządzane 
W przypadku dysków zarządzanych tworzenie konta magazynu i zarządzanie nim odbywa się automatycznie w tle i nie trzeba martwić się o limity skalowalności konta magazynu. Wystarczy określić rozmiar dysku i warstwę wydajności (Standardowa/Premium), a platforma Azure sama utworzy dysk i będzie nim zarządzać. O używanym magazynie nie trzeba myśleć przy dodawaniu dysków albo skalowaniu maszyny wirtualnej w górę i w dół. 

Można też zarządzać własnymi obrazami niestandardowymi na jednym koncie magazynu na region platformy Azure i używać ich do tworzenia setek maszyn wirtualnych w tej samej subskrypcji. Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [Omówienie usługi Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

Aby czerpać korzyści z wielu funkcji dysków zarządzanych, zalecamy używanie usługi Azure Managed Disks dla nowych maszyn wirtualnych i przekonwertowanie wcześniej używanych dysków niezarządzanych na dyski zarządzane.

### <a name="disk-comparison"></a>Porównanie dysków
W poniższej tabeli przedstawiono porównanie standardowych dysków Twardych, SSD standardowa i Premium SSD dysków niezarządzane i zarządzane pomocne w podejmowaniu decyzji co do użycia.

|    | Dysk platformy Azure w warstwie Premium |Azure SSD — dysk standardowy (wersja zapoznawcza)| Dysk Azure standardowych dysków Twardych 
|--- | ------------------ | ------------------------------- | ----------------------- 
| Typ dysku | Dyski półprzewodnikowe (SSD) | Dyski półprzewodnikowe (SSD) | Dyski twarde (HDD)  
| Przegląd  | Pamięć dyskowa o wysokiej wydajności i małych opóźnieniach, bazująca na dyskach SSD, przeznaczona dla maszyn wirtualnych uruchamiających obciążenia intensywnie korzystające z operacji wejścia/wyjścia lub hostujących środowisko produkcyjne o znaczeniu krytycznym |Bardziej spójny wydajność i niezawodność niż dysk twardy. Zoptymalizowana pod kątem obciążeń IOPS niski| Dysku ekonomiczne rzadkim dostępu opartego na dysk twardy
| Scenariusz  | Obciążenia produkcyjne i wrażliwe na wydajność |Serwery sieci Web, aplikacje dla przedsiębiorstw lekkim używane i tworzenie/testowanie oprogramowania| Kopia zapasowa, niekrytyczny, rzadko dostępu 
| Rozmiar dysku | P4: 32 GiB (tylko w przypadku dysków zarządzane)<br>P6: 64 GiB (tylko w przypadku dysków zarządzane)<br>P10: GiB 128<br>P15: 256 GiB (tylko w przypadku dysków zarządzane)<br>P20: GiB 512<br>P30: GiB 1024<br>P40: GiB 2048<br>P50: GiB 4095 |Zarządzane tylko dyski:<br>E10: GiB 128<br>E15: GiB 256<br>E20: GiB 512<br>E30: GiB 1024<br>E40: GiB 2048<br>E 50: GiB 4095 | Niezarządzane dysków: GiB 1 – 4 TiB (4095 GiB) <br><br>Dyski zarządzane:<br> S4: GiB 32 <br>S6: GiB 64 <br>S10 w warstwie: GiB 128 <br>S15: GiB 256 <br>S20: GiB 512 <br>S30: GiB 1024 <br>S40: GiB 2048<br>S50: GiB 4095
| Maksymalna przepływność na dysk | 250 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s 
| Maksymalna liczba operacji wejścia/wyjścia na dysk | 7500 IOPS | Maksymalnie 500 IOPS | Maksymalnie 500 IOPS 