---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa740cfb203f50dc97a06359774dae367a20252b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60406497"
---
## <a name="about-vhds"></a>Informacje o wirtualnych dyskach twardych

Wirtualne dyski twarde używane na platformie Azure to pliki vhd przechowywane jako stronicowe obiekty blob na koncie magazynu w warstwie Standardowa lub Premium na platformie Azure. Aby uzyskać informacje na temat stronicowych obiektów blob, zobacz [Understanding block blobs and page blobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/) (Omówienie blokowych i stronicowych obiektów blob). Aby uzyskać szczegółowe informacje na temat magazynu w warstwie Premium, zobacz [High-performance premium storage and Azure VMs](../articles/virtual-machines/windows/premium-storage.md) (Magazyn w warstwie Premium o wysokiej wydajności i maszyny wirtualne platformy Azure).

Platforma Azure obsługuje stały format VHD dysków. W przypadku stałego formatu dysk logiczny jest zapisywany w pliku w sposób liniowy w taki sposób, że przesunięcie X na dysku jest zapisywane w obiekcie blob z przesunięciem X. Niewielka stopka na końcu obiektu blob opisuje właściwości wirtualnego dysku twardego. Często odpady stałym formacie miejsca, ponieważ większość dysków zawiera duże Nieużywane zakresy w nich. Platforma Azure jednak przechowuje pliki vhd w formacie rozrzedzonym, więc jednocześnie uzyskuje się zalety dysków stałych i dynamicznych. Aby uzyskać więcej informacji, zobacz [rozpoczęcie pracy z wirtualnymi dyskami twardymi](https://technet.microsoft.com/library/dd979539.aspx).

Wszystkie pliki VHD na platformie Azure, który chcesz użyć jako źródła do utworzenia dysków lub obrazów są tylko do odczytu, z wyjątkiem plików VHD przekazane lub kopiowane do magazynu platformy Azure przez użytkownika (co może być odczytu i zapisu lub tylko do odczytu). Kiedy tworzysz dysk lub obraz, platforma Azure tworzy kopie źródła pliki VHD. Te kopie mogą być tylko do odczytu lub do odczytu i zapisu, zależnie od tego, jak korzystasz z wirtualnego dysku twardego.

Gdy tworzysz maszynę wirtualną na podstawie obrazu, platforma Azure tworzy dysk dla maszyny wirtualnej, który jest kopią źródłowego pliku vhd. Platforma Azure umieszcza dzierżawę na każdym pliku vhd używanym do utworzenia obrazu, dysku systemu operacyjnego lub dysku danych, aby chronić ten plik przed przypadkowym usunięciem.

Zanim będzie możliwe usunięcie źródłowego pliku vhd, trzeba będzie usunąć tę dzierżawę, usuwając dysk lub obraz. Aby usunąć plik vhd, który jest używany przez maszynę wirtualną jako dysk systemu operacyjnego, można jednocześnie usunąć maszynę wirtualną, dysk systemu operacyjnego i źródłowy plik vhd, usuwając maszynę wirtualną i wszystkie skojarzone dyski. Usunięcie pliku vhd będącego źródłem dysku danych wymaga jednak wykonania kilku kroków w ustalonej kolejności. Najpierw należy odłączyć dysk od maszyny wirtualnej, później usunąć dysk, a następnie usunąć plik vhd.

> [!WARNING]
> Jeśli usuniesz źródłowy plik vhd z magazynu albo usuniesz swoje konto magazynu, firma Microsoft nie będzie mogła odzyskać usuniętych danych.

## <a name="types-of-disks"></a>Typy dysków

Dyski platformy Azure zaprojektowano tak, aby zapewniały 99,999% dostępności. Dyski Azure stale wydały trwałość przeznaczonych dla przedsiębiorstw, dzięki wiodącej w branży ZERO % Annualized współczynnik błędów.

Istnieją trzy warstwy wydajności pamięci masowej, które można wybierać podczas tworzenia dysków — dyski SSD w warstwie Premium SSD w warstwie standardowa i magazynu w warstwie standardowa dysk twardy. Ponadto istnieją dwa typy dysków — niezarządzane i zarządzane.

### <a name="standard-hdd-disks"></a>Dyski w warstwie standardowa dysk twardy

Dyski standardowe dyski TWARDE są wspierane przez dysków twardych i dostarczaj ekonomicznego magazynowania. Dysk twardy magazynu w warstwie standardowa może być replikowany lokalnie w jednym centrum danych lub być objęty nadmiarowością geograficzną z podstawowym i dodatkowym centrum danych. Aby uzyskać więcej informacji na temat replikowania magazynu, zobacz [replikacja usługi Azure Storage](../articles/storage/common/storage-redundancy.md).

Aby uzyskać więcej informacji na temat korzystania z dysków standardowych dysków Twardych, zobacz [Standard Storage i dyski](../articles/virtual-machines/windows/standard-storage.md).

### <a name="standard-ssd-disks"></a>Dyski SSD w warstwie standardowa

Dyski SSD w warstwie standardowa są przeznaczone do adresu tego samego rodzaju obciążenia jako dyski HDD standardowych, ale oferują bardziej spójną wydajność i niezawodność niż dysk twardy. Dyski SSD w warstwie standardowa łączyć elementy dysków w warstwie Premium SSD i dysków standardowych dysków Twardych do formularza ekonomiczne rozwiązanie najlepiej nadaje się do aplikacji, takich jak serwery sieci web, które nie są wysokie operacje We/Wy na dyskach. Ile to możliwe, dysków SSD w warstwie standardowa to opcja wdrażania zalecane dla większości obciążeń. Dyski SSD w warstwie standardowa są dostępne jako dyski Managed Disks we wszystkich regionach, ale są obecnie dostępne tylko z typem odporności magazyn lokalnie nadmiarowy (LRS).

### <a name="premium-ssd-disks"></a>Dyski SSD w warstwie Premium

Dyski SSD w warstwie Premium są wspierane przez dyski SSD, a także zapewnia obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach dla maszyn wirtualnych uruchomionych wyjścia — dużych obciążeń wejścia /. Zazwyczaj służy dysków SSD w warstwie Premium o rozmiarach, zawierających znak "s" w nazwie serii. Na przykład brak serii Dv3 i Seria Dsv3, seria Dsv3 mogą być używane z dysków SSD w warstwie Premium.  Aby uzyskać więcej informacji, zobacz [Premium Storage](../articles/virtual-machines/windows/premium-storage.md).

### <a name="unmanaged-disks"></a>Dyski niezarządzane

Dyski niezarządzane to tradycyjny typ dysków używany przez maszyny wirtualne. Z tych dysków możesz utworzyć konto magazynu i tego konta magazynu można określić podczas tworzenia dysku. Upewnij się, że nie umieszczasz zbyt wiele dysków w tym samym koncie magazynu, ponieważ możesz przekroczyć [cele skalowalności](../articles/storage/common/storage-scalability-targets.md) konta magazynu (20 000 operacji We/Wy, na przykład), co w przypadku maszyn wirtualnych będzie ograniczona. Gdy korzystasz z dysków niezarządzanych, musisz opracować sposób zmaksymalizowania użycia jednego lub większej liczby kont magazynu w celu uzyskania najlepszej wydajności maszyn wirtualnych.

### <a name="managed-disks"></a>Dyski zarządzane

W przypadku dysków zarządzanych tworzenie konta magazynu i zarządzanie nim odbywa się automatycznie w tle i nie trzeba martwić się o limity skalowalności konta magazynu. Wystarczy określić rozmiar dysku i warstwę wydajności (Standardowa/Premium), a platforma Azure sama utworzy dysk i będzie nim zarządzać. O używanym magazynie nie trzeba myśleć przy dodawaniu dysków albo skalowaniu maszyny wirtualnej w górę i w dół.

Można też zarządzać własnymi obrazami niestandardowymi na jednym koncie magazynu na region platformy Azure i używać ich do tworzenia setek maszyn wirtualnych w tej samej subskrypcji. Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [Omówienie usługi Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

Aby czerpać korzyści z wielu funkcji dysków zarządzanych, zalecamy używanie usługi Azure Managed Disks dla nowych maszyn wirtualnych i przekonwertowanie wcześniej używanych dysków niezarządzanych na dyski zarządzane.

### <a name="disk-comparison"></a>Porównanie dysków

Poniższa tabela zawiera porównanie standardowych dysków Twardych, SSD w warstwie standardowa i dysków niezarządzanych i zarządzanych w warstwie Premium SSD, aby ułatwić wybór rozwiązania do zastosowania. Rozmiary oznaczone gwiazdką są obecnie dostępne w wersji zapoznawczej.

|    | Dysk platformy Azure w warstwie Premium |Dysk SSD w warstwie standardowa platformy Azure | Dysk Azure standardowych dysków Twardych
|--- | ------------------ | ------------------------------- | -----------------------
| Typ dysku | Dyski półprzewodnikowe (SSD) | Dyski półprzewodnikowe (SSD) | Dyski twarde (HDD)  
| Omówienie  | Pamięć dyskowa o wysokiej wydajności i małych opóźnieniach, bazująca na dyskach SSD, przeznaczona dla maszyn wirtualnych uruchamiających obciążenia intensywnie korzystające z operacji wejścia/wyjścia lub hostujących środowisko produkcyjne o znaczeniu krytycznym |Bardziej spójną wydajność i niezawodność niż dysk twardy. Zoptymalizowane pod kątem obciążeń niska-operacje We/Wy| Oparte na dysk twardy ekonomiczne dysku rzadki dostęp
| Scenariusz  | Obciążenia produkcyjne i wrażliwe na wydajność |Serwery sieci Web, aplikacje dla przedsiębiorstw używana w niewielkim stopniu oraz tworzenia i testowania| Kopia zapasowa, niekrytyczne, rzadki dostęp
| Rozmiar dysku | P4: 32 giB (tylko w przypadku dysków zarządzanych)<br>P6: 64 giB (tylko w przypadku dysków zarządzanych)<br>P10: 128 GiB<br>P15: 256 giB (tylko w przypadku dysków zarządzanych)<br>P20: 512 GiB<br>P30: 1024 GiB<br>P40: 2048 GiB<br>P50: 4,095 GiB<br>P60: 8192 giB * (8 TiB)<br>P70: 16,384 GiB * (16 TiB)<br>P80: 32 767 giB * (32 TiB) |Tylko dyski zarządzane:<br>E4: 32 GiB<br>E6: 64 GiB<br>E10: 128 GiB<br>E15: 256 GiB<br>E20: 512 GiB<br>E30: 1024 GiB<br>E40: 2048 GiB<br>E50: 4095 GiB<br>E60: 8192 giB * (8 TiB)<br>E70: 16,384 GiB * (16 TiB)<br> E80: 32 767 giB * (32 TiB) | Dyski niezarządzane: 1 GiB – 4 TiB (4095 GiB) <br><br>Dyski zarządzane:<br> S4: 32 GiB <br>S6: 64 GiB <br>S10: 128 GiB <br>S15: 256 GiB <br>S20: 512 GiB <br>S30: 1024 GiB <br>S40: 2048 GiB<br>S50: 4095 GiB<br>S60: 8192 giB * (8 TiB)<br>S70: 16,384 GiB * (16 TiB)<br>S80: 32 767 giB * (32 TiB)
| Maksymalna przepływność na dysk | P4: 25 MiB/s<br> P6: 50 MiB/s<br> P10: 100 MiB/s<br> P15: 125 MiB/s<br> P20: 150 MiB/s<br> P30: 200 MiB/s<br> P40-P50: 250 MiB/s<br> P60: 480 MiB/s *<br> P70-P80: 750 MiB/s * | E10-E50: Maksymalnie 60 MiB/s<br> E60: Maksymalnie 300 MiB/s *<br> E70-E80: 500 MiB/s *| S4 - S50: O 60 Upt MiB/s<br> S60: Maksymalnie 300 MiB/s *<br> S70-S80: Maks. 500 MiB/s *
| Maksymalna liczba operacji wejścia/wyjścia na dysk | P4: 120 IOPS<br> P6: 240 IOPS<br> P10: 500 IOPS<br> P15: 1100 IOPS<br> P20: 2300 IOPS<br> P30: 5000 IOPS<br> P40-P50: 7500 IOPS<br> P60: 12 500 OPERACJI WE/WY *<br> P70: 15 000 OPERACJI WE/WY *<br> P80: 20 000 OPERACJI WE/WY * | E10-E50: Maks. 500 operacji We/Wy<br> E60: Maksymalnie 1300 operacje We/Wy *<br> E70-E80: Maksymalnie 2000 operacji We/Wy * | S4-S50: Maks. 500 operacji We/Wy<br> S60: Maksymalnie 1300 operacje We/Wy *<br> S70-S80: Maksymalnie 2000 operacji We/Wy *
