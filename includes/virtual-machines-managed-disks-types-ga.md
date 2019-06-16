---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2bc5602011ed64b11b1b8c96b7e69a8d5ee9bf32
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133681"
---
## <a name="premium-ssd"></a>Premium, SSD

Usługa Azure premium SSD dostarczać obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach dla maszyn wirtualnych (VM) z operacji wejścia/wyjścia (IO)-intensywnych obciążeń. Aby móc korzystać z szybkości i wydajności dysków usługi premium storage, należy przeprowadzić migrację istniejących dysków maszyny Wirtualnej na dyski SSD w warstwie Premium. Dyski SSD w warstwie Premium są odpowiednie dla aplikacji produkcyjnych o kluczowym znaczeniu. Dyski SSD w warstwie Premium należy używać tylko z serię maszyn wirtualnych, które są zgodne z magazynu w warstwie premium.

Aby dowiedzieć się więcej na temat poszczególnych typów maszyn wirtualnych i rozmiarach na platformie Azure dla Windows, które rozmiary są premium zgodna z magazynem, w tym temacie [rozmiarów maszyn wirtualnych Windows](../articles/virtual-machines/windows/sizes.md). Aby dowiedzieć się więcej o poszczególnych typach maszyn wirtualnych i rozmiarach na platformie Azure dla systemu Linux, w tym, które są premium storage zgodnej zobacz [rozmiarów maszyn wirtualnych systemu Linux](../articles/virtual-machines/linux/sizes.md).

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Podczas aprowizowania dysku magazynu premium, w przeciwieństwie do magazynu w warstwie standardowa jest gwarantowana pojemność, operacje We/Wy i przepływność dysku. Na przykład jeśli tworzysz P50 dysku, platforma Azure udostępnia 4095 GB pojemności, 7500 operacji We/Wy i 250-MB/s przepływności dla tego dysku. Aplikacja może używać w całości lub części pojemności i wydajności. Dyski SSD w warstwie Premium są przeznaczone do zapewnienia niski milisekundowe opóźnienia i obiekt docelowy operacji We/Wy i przepływność, które opisano w poprzedniej tabeli 99,9% czasu.

### <a name="transactions"></a>Transakcje

Dyski SSD w warstwie premium każdej operacji We/Wy mniejszą niż lub równy 256 KiB przepływności są uznawane za jednej operacji We/Wy. Operacje We/Wy jest większy niż 256 KiB przepływności są uważane za wiele operacji We/Wy o rozmiarze 256 KiB.

## <a name="standard-ssd"></a>Standardowa, SSD

Azure standardowe dyski SSD są opcji ekonomicznego magazynowania, zoptymalizowanych pod kątem obciążeń wymagających spójną wydajność na niższych poziomach operacji We/Wy. SSD w warstwie standardowa oferuje środowisko dobre klasy podstawowej dla osób, które chcesz przenieść do chmury, zwłaszcza, jeśli występują problemy z wariancję obciążeń działających na dysk twardy rozwiązań w środowisku lokalnym. W porównaniu do standardowych dysków twardych, dyski SSD standardowa zapewniają lepsze dostępności, spójności, niezawodności i opóźnienie. Standardowe dyski SSD są odpowiednie dla serwerów sieci Web, niski serwerów aplikacji operacje We/Wy, aplikacje dla przedsiębiorstw używana w niewielkim stopniu i obciążeń deweloperskich/testowych. Podobnie jak standardowych dysków twardych standardowe dyski SSD są dostępne na wszystkich maszynach wirtualnych platformy Azure.

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standardowe dyski SSD są przeznaczone do zapewnienia milisekundowe opóźnienia i operacje We/Wy i Przepływność w granicach opisanego w poprzedniej tabeli 99% czasu. Rzeczywiste operacje We/Wy i przepływność będzie zależeć czasami w zależności od wzorców ruchu. Standardowe dyski SSD będą oferują bardziej spójną wydajność niż dyski HDD, z mniejszymi opóźnieniami.

### <a name="transactions"></a>Transakcje

Standardowe dyski SSD każdej operacji We/Wy mniejszą niż lub równy 256 KiB przepływności są uznawane za jednej operacji We/Wy. Operacje We/Wy jest większy niż 256 KiB przepływności są uważane za wiele operacji We/Wy o rozmiarze 256 KiB. Te transakcje mają wpływ rozliczeń.

## <a name="standard-hdd"></a>Standardowa, dysk twardy

Azure standardowych dysków twardych dostarczanie obsługi niezawodne, niedrogie dysków dla maszyn wirtualnych obciążeń niewrażliwego na opóźnienia. Dzięki usłudze standard storage dane są przechowywane dyski twarde (HDD). Opóźnienia, operacje We/Wy i przepływność HDD standardowe dyski mogą się różnić szerzej w porównaniu do dysków na dyskach SSD. Dyski standardowe dyski TWARDE są przeznaczone do dostarczania opóźnienia zapisu, w obszarze 10 MS i opóźnienia w ramach 20 MS w przypadku większości operacji We/Wy odczytu, jednak Rzeczywista wydajność będzie zależeć od rozmiaru i obciążeń wzorzec operacji We/Wy. Podczas pracy z maszynami wirtualnymi, można użyć dysków w warstwie standardowa dysku twardego dla scenariuszy deweloperskich lub testowych oraz mniej krytycznych obciążeń. Standardowych dysków twardych są dostępne we wszystkich regionach platformy Azure i mogą być używane z wszystkich maszyn wirtualnych platformy Azure.

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transakcje

Dla standardowych dysków twardych każda operacja We/Wy jest traktowany jako pojedynczą transakcję, bez względu na rozmiar operacji We/Wy. Te transakcje mają wpływ rozliczeń.

## <a name="billing"></a>Rozliczenia

Korzystając z dysków zarządzanych, zastosuj następujące zagadnienia dotyczące rozliczeń:

- Typ dysku
- dysk zarządzany rozmiar
- Migawki
- Wychodzące transfery danych
- Liczba transakcji

**Rozmiar dysku zarządzanego**: opłaty za dyski zarządzane są naliczane zarezerwowany rozmiar. Usługi Azure maps zaprowizowany rozmiar (z zaokrągleniem) do najbliższej rozmiaru dysku oferowane przez użytkownika. Szczegółowe informacje o oferowanych rozmiary dysków można zobaczyć w poprzednich tabelach. Każdy dysk mapuje do oferty rozmiar dysku obsługiwane i jest rozliczana w związku z tym. Na przykład, jeśli zainicjowano obsługę administracyjną 200 GiB SSD w warstwie standardowa, jest on mapowany ofertę rozmiar dysku E15 (256 GiB). Rozliczenia dla dowolnego dysku jest proporcjonalnie do liczby godzin przy użyciu miesięczna oferta usługi Premium Storage. Na przykład jeśli zainicjowano obsługę administracyjną E10 dysku, a następnie usunięte 20 godzin, są naliczane dla oferty E10 jest naliczany proporcjonalnie do 20 godzin. Jest to niezależnie od ilości rzeczywiste dane zapisane na dysku.

**Migawki**: Migawki są rozliczane na podstawie rozmiaru używane. Na przykład jeśli utworzysz migawkę dysku zarządzanego z zaprowizowaną pojemnością 64 GiB i rzeczywistym użyciem danych rozmiaru 10 GiB migawki jest zapłacisz tylko za użyte dane o rozmiarze od 10 GiB.
