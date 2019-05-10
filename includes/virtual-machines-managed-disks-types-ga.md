---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 885bc1c627626ee7ba4f391be31131b18fa1ab39
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65212012"
---
## <a name="premium-ssd"></a>SSD w warstwie Premium

Usługa Azure premium SSD dostarczać obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach dla maszyn wirtualnych (VM) z operacji wejścia/wyjścia (IO)-intensywnych obciążeń. Aby móc korzystać z szybkości i wydajności dysków usługi premium storage, należy przeprowadzić migrację istniejących dysków maszyny Wirtualnej na dyski SSD w warstwie Premium. Dyski SSD w warstwie Premium są odpowiednie dla aplikacji produkcyjnych o kluczowym znaczeniu.

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Podczas aprowizowania dysku magazynu premium, w przeciwieństwie do magazynu w warstwie standardowa jest gwarantowana pojemność, operacje We/Wy i przepływność dysku. Na przykład jeśli tworzysz P50 dysku, platforma Azure udostępnia 4095 GB pojemności, 7500 operacji We/Wy i 250-MB/s przepływności dla tego dysku. Aplikacja może używać w całości lub części pojemności i wydajności. Dyski SSD w warstwie Premium są przeznaczone do dostarczania element docelowy wydajności przez 99,9% czasu.

## <a name="standard-ssd"></a>SSD w warstwie Standardowa

Azure standardowe dyski SSD są opcji ekonomicznego magazynowania, zoptymalizowanych pod kątem obciążeń wymagających spójną wydajność na niższych poziomach operacji We/Wy. SSD w warstwie standardowa oferuje środowisko dobre klasy podstawowej dla osób, które chcesz przenieść do chmury, zwłaszcza, jeśli występują problemy z wariancję obciążeń działających na dysk twardy rozwiązań w środowisku lokalnym. Standardowa SSD zapewniają lepsze dostępności, spójności, niezawodności i opóźnienia w porównaniu do dysków HDD. Standardowe dyski SSD są odpowiednie dla serwerów sieci Web, niski serwerów aplikacji operacje We/Wy, aplikacje dla przedsiębiorstw używana w niewielkim stopniu i obciążeń deweloperskich/testowych.

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standardowe dyski SSD są przeznaczone zapewnienie milisekundowe opóźnienia w przypadku większości operacji We/Wy i operacje We/Wy i Przepływność w granicach opisanego w poprzedniej tabeli 99% czasu. Rzeczywiste operacje We/Wy i przepływność będzie zależeć czasami w zależności od wzorców ruchu. Standardowe dyski SSD będą oferują bardziej spójną wydajność niż dyski HDD, z mniejszymi opóźnieniami.

## <a name="standard-hdd"></a>HDD w warstwie Standardowa

Azure standardowych dysków twardych dostarczanie obsługi niezawodne, niedrogie dysków dla maszyn wirtualnych obciążeń niewrażliwego na opóźnienia. Obsługuje również obiekty BLOB, tabel, kolejek i plików. Dzięki usłudze standard storage dane są przechowywane dyski twarde (HDD). Podczas pracy z maszynami wirtualnymi, używając dysków SSD i HDD w warstwie standardowa dla scenariuszy deweloperskich lub testowych oraz mniej krytycznych obciążeń. Magazynu w warstwie standardowa jest dostępna we wszystkich regionach platformy Azure.

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

## <a name="billing"></a>Informacje billingowe

Korzystając z dysków zarządzanych, zastosuj następujące zagadnienia dotyczące rozliczeń:

- Typ dysku
- dysk zarządzany rozmiar
- Migawki
- Wychodzące transfery danych
- Liczba transakcji

**Rozmiar dysku zarządzanego**: opłaty za dyski zarządzane są naliczane zarezerwowany rozmiar. Usługi Azure maps zaprowizowany rozmiar (z zaokrągleniem) do najbliższej rozmiaru dysku oferowane przez użytkownika. Szczegółowe informacje o oferowanych rozmiary dysków można zobaczyć w poprzednich tabelach. Każdy dysk mapuje do oferty rozmiar dysku obsługiwane i jest rozliczana w związku z tym. Na przykład, jeśli zainicjowano obsługę administracyjną 200 GiB SSD w warstwie standardowa, jest on mapowany ofertę rozmiar dysku E15 (256 GiB). Rozliczenia dla dowolnego dysku jest proporcjonalnie do liczby godzin przy użyciu miesięczna oferta usługi Premium Storage. Na przykład jeśli zainicjowano obsługę administracyjną E10 dysku, a następnie usunięte 20 godzin, są naliczane dla oferty E10 jest naliczany proporcjonalnie do 20 godzin. Jest to niezależnie od ilości rzeczywiste dane zapisane na dysku.

**Migawki**: Migawki są rozliczane na podstawie rozmiaru używane. Na przykład jeśli utworzysz migawkę dysku zarządzanego z zaprowizowaną pojemnością 64 GiB i rzeczywistym użyciem danych rozmiaru 10 GiB migawki jest zapłacisz tylko za użyte dane o rozmiarze od 10 GiB.