---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6740ea320f2d950386da12eb44726e2c826b60a4
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80386093"
---
## <a name="premium-ssd"></a>Dysk SSD w warstwie Premium

Dyski SSD w wersji Premium platformy Azure zapewniają obsługę dysku o wysokiej wydajności i małych opóźnieniach dla maszyn wirtualnych (VM) z obciążeniami intensywnie korzystającymi z nakładów/wyjść (IO). Aby korzystać z szybkości i wydajności dysków magazynu w wersji premium, można migrować istniejące dyski maszyn wirtualnych do dysków SSD w wersji Premium. Najwyższej jakości ssd nadają się do zastosowań produkcyjnych o znaczeniu krytycznym. Dyski SSD premium mogą być używane tylko z serią maszyn wirtualnych, które są zgodne z pamięcią masową w jakości Premium.

Aby dowiedzieć się więcej o poszczególnych typach i rozmiarach maszyn wirtualnych na platformie Azure dla systemu Windows, w tym o rozmiarach zgodnych z magazynu w wersji Premium, zobacz [Rozmiary maszyn wirtualnych systemu Windows](../articles/virtual-machines/windows/sizes.md). Aby dowiedzieć się więcej o poszczególnych typach i rozmiarach maszyn wirtualnych na platformie Azure dla systemu Linux, w tym o rozmiarach zgodnych z pamięcią masową w jakości Premium, zobacz [Rozmiary maszyn wirtualnych z systemem Linux](../articles/virtual-machines/linux/sizes.md). Z jednego z tych artykułów, należy sprawdzić każdy artykuł o rozmiarze poszczególnych maszyn wirtualnych, aby ustalić, czy jest zgodny z magazynu w wersji premium.

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Podczas aprowizowania dysku magazynu w wersji premium, w przeciwieństwie do magazynu standardowego, masz gwarancję pojemności, usługi We/Wy i przepływności tego dysku. Na przykład jeśli utworzysz dysk P50, platforma Azure udostępnia 4095 GB pojemności magazynu, 7500 operacji We/Wy i przepływności 250 MB/s dla tego dysku. Aplikacja może korzystać z całości lub części pojemności i wydajności. Dyski SSD w wersji Premium są przeznaczone do zapewnienia niskich jednocyfrowych opóźnień milisekund oraz docelowych we/wy oraz przepływności opisanych w powyższej tabeli 99,9% czasu.

## <a name="bursting"></a>Pęknięcie

Rozmiary dysków SSD premium mniejsze niż P30 oferują teraz rozerwanie dysku i mogą pęknąć ich IOPS na dysk do 3500, a ich przepustowość do 170 Mb/s. Bursting jest zautomatyzowany i działa w oparciu o system kredytowy. Kredyty są automatycznie gromadzone w zasobniku seryjnym, gdy ruch dysku jest niższy od aprowizowanego celu wydajności, a kredyty są automatycznie zużywane, gdy ruch zostanie osiągnięty poza celem, do maksymalnego limitu serii. Maksymalny limit serii definiuje pułap we/wy dysków & przepustowość, nawet jeśli masz kredyty serii do wykorzystania. Bursting dysku zapewnia lepszą tolerancję na nieprzewidywalne zmiany wzorców we/wy. Możesz najlepiej wykorzystać go do rozruchu dysku operacyjnego i aplikacji z kolczastym ruchem.    

Obsługa dysków bursting zostanie włączona w nowych wdrożeń odpowiednich rozmiarów dysków domyślnie, bez działania użytkownika wymagane. W przypadku istniejących dysków o odpowiednich rozmiarach można włączyć rozerwanie za pomocą jednej z dwóch opcji: odłączyć i ponownie dołączyć dysk lub zatrzymać i ponownie uruchomić dołączoną maszynę wirtualną. Wszystkie rozmiary dysków serii odpowiednie rozpocznie się w pełnej wersji wybuchu zasobnika, gdy dysk jest dołączony do maszyny wirtualnej, która obsługuje maksymalny czas trwania w szczytowym limitu serii 30 minut. Aby dowiedzieć się więcej o tym, jak działa rozerwanie na dyskach platformy Azure, zobacz [Rozerwanie dysków SSD w wersji Premium](../articles/virtual-machines/linux/disk-bursting.md). 

### <a name="transactions"></a>Transakcje

W przypadku najwyższej jakości identyfikatorów SSD każda operacja we/wy mniejsza lub równa 256 KiB przepustowości jest uważana za pojedynczą operację we/wy. Operacje we/wy większe niż 256 KiB przepustowości są uważane za wiele operacji we/wy o rozmiarze 256 KiB.

## <a name="standard-ssd"></a>Dysk SSD w warstwie Standardowa

Standardowe dyski SSD platformy Azure to ekonomiczna opcja magazynu zoptymalizowana pod kątem obciążeń, które wymagają spójnej wydajności przy niższych poziomach we/wy. Standardowy dysk SSD oferuje dobre środowisko podstawowe dla tych, którzy chcą przejść do chmury, zwłaszcza jeśli występują problemy z odchylenia obciążeń działających na rozwiązaniach HDD w środowisku lokalnym. W porównaniu ze standardowymi dyskami twardymi standardowe dyski SSD zapewniają lepszą dostępność, spójność, niezawodność i opóźnienia. Standardowe dyski SSD są odpowiednie dla serwerów sieci Web, serwerów aplikacji o niskiej liczby we/wy, lekko używanych aplikacji korporacyjnych oraz obciążeń deweloperskich/testowych. Podobnie jak standardowe dyski twarde, standardowe dyski SSD są dostępne na wszystkich maszynach wirtualnych platformy Azure.

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standardowe ssd są przeznaczone do zapewnienia jednocyfrowych opóźnień milisekund i We/Wy i przepustowość do limitów opisanych w powyższej tabeli 99% czasu. Rzeczywiste we/wy i przepływność mogą się czasami różnić w zależności od wzorców ruchu. Standardowe dyski SSD zapewniają bardziej spójną wydajność niż dyski twarde z niższym opóźnieniem.

### <a name="transactions"></a>Transakcje

W przypadku standardowych ssd każda operacja we/wy mniejsza lub równa 256 KiB przepływności jest uważana za pojedynczą operację we/wy. Operacje we/wy większe niż 256 KiB przepustowości są uważane za wiele operacji we/wy o rozmiarze 256 KiB. Transakcje te mają wpływ na rozliczenia.

## <a name="standard-hdd"></a>Dysk HDD w warstwie Standardowa

Standardowe dyski twarde platformy Azure zapewniają niezawodną, tanią obsługę dysków dla maszyn wirtualnych z obciążeniami niewrażliwymi na opóźnienia. W przypadku standardowej pamięci masowej dane są przechowywane na dyskach twardych (HDD). Opóźnienie, we/wy i przepustowość standardowych dysków twardych mogą się znacznie różnić w porównaniu z dyskami opartymi na dyskach SSD. Standardowe dyski TWARDE są przeznaczone do dostarczania opóźnień zapisu w 10ms i opóźnienia odczytu w ramach 20ms dla większości operacji we/wy, jednak rzeczywista wydajność może się różnić w zależności od rozmiaru we/wy i wzorca obciążenia. Podczas pracy z maszynami wirtualnymi można używać standardowych dysków twardych do scenariuszy deweloperskich/testowych i mniej krytycznych obciążeń. Standardowe dyski twarde są dostępne we wszystkich regionach platformy Azure i mogą być używane ze wszystkimi maszynami wirtualnymi platformy Azure.

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transakcje

W przypadku standardowych dysków twardych każda operacja operacji we/wy jest traktowana jako pojedyncza transakcja, niezależnie od rozmiaru we/wy. Transakcje te mają wpływ na rozliczenia.

## <a name="billing"></a>Rozliczenia

Podczas korzystania z dysków zarządzanych obowiązują następujące zagadnienia dotyczące rozliczeń:

- Typ dysku
- Rozmiar dysku zarządzanego
- Migawki
- Wychodzące transfery danych
- Liczba transakcji

**Rozmiar dysku zarządzanego:** dyski zarządzane są rozliczane od aprowizowanego rozmiaru. Platforma Azure mapuje aprowizowanym rozmiarem (zaokrąglonym w górę) do najbliższego oferowanego rozmiaru dysku. Szczegółowe informacje na temat oferowanych rozmiarów dysków można znaleźć w poprzednich tabelach. Każdy dysk jest mapowany do obsługiwanej oferty rozmiaru dysku aprowizowanego i jest odpowiednio rozliczany. Na przykład jeśli aprowizowana 200 GiB Standard SSD, mapuje do oferty rozmiaru dysku E15 (256 GiB). Rozliczenia za każdy aprowizowany dysk jest proporcjonalnie ustalana co godzinę przy użyciu miesięcznej ceny oferty magazynu w wersji Premium. Jeśli na przykład udostępniono dysk E10 i usunięto go po 20 godzinach, naliczane są naliczane płatności za ofertę E10 proporcjonalnie do 20 godzin. Jest to niezależnie od ilości rzeczywistych danych zapisanych na dysku.

**Migawki:** Migawki są rozliczane na podstawie używanego rozmiaru. Na przykład jeśli utworzysz migawkę dysku zarządzanego o aprowizowanym pojemności 64 GiB i rzeczywistym rozmiarze używanych danych 10 GiB, migawka jest rozliczana tylko dla używanego rozmiaru danych 10 GiB.
