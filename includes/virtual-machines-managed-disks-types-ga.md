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
ms.openlocfilehash: 0201776914610ddaca50a670fc500156a65cd734
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2019
ms.locfileid: "58124686"
---
## <a name="premium-ssd"></a>Premium, SSD

Usługa Azure premium SSD dostarczać obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach dla maszyn wirtualnych (VM) z operacji wejścia/wyjścia (IO)-intensywnych obciążeń. Aby móc korzystać z szybkości i wydajności dysków usługi premium storage, należy przeprowadzić migrację istniejących dysków maszyny Wirtualnej na dyski SSD w warstwie Premium. Dyski SSD w warstwie Premium są odpowiednie dla aplikacji produkcyjnych o kluczowym znaczeniu.

### <a name="disk-size"></a>Rozmiar dysku

Rozmiary oznaczone gwiazdką są obecnie dostępne w wersji zapoznawczej.

| Rozmiary dysków SSD — wersja Premium | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60* | P70* | P80* |
|-------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Rozmiar dysku w GiB | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8192 | 16,384 | 32,767 |
| Liczba operacji wejścia/wyjścia na sekundę na dysk | Do 120 | Maksymalnie 240 | Maks. 500 | Maksymalnie 1100 | Maksymalnie 2300 | Maksymalnie 5000 | Maksymalnie 7500 | Maksymalnie 7500 | Maksymalnie 12 500 | Maksymalnie 15 000 | Maksymalnie 20 000 |
| Przepływność na dysk | Maksymalnie 25 MiB/s | Maksymalnie 50 MiB/s | Maksymalnie 100 MiB/s | Maksymalnie 125 MiB/s | Maksymalnie 150 MiB/s | Maksymalnie 200 MiB/s | Do 250 MiB/s | Do 250 MiB/s| Maksymalnie 480 MiB/s | Do 750 MiB/s | Do 750 MiB/s |

## <a name="standard-ssd"></a>Standardowa, SSD

Azure standardowe dyski SSD są opcji ekonomicznego magazynowania, zoptymalizowanych pod kątem obciążeń wymagających spójną wydajność na niższych poziomach operacji We/Wy. SSD w warstwie standardowa oferuje środowisko dobre klasy podstawowej dla osób, które chcesz przenieść do chmury, zwłaszcza, jeśli występują problemy z wariancję obciążeń działających na dysk twardy rozwiązań w środowisku lokalnym. Standardowa SSD zapewniają lepsze dostępności, spójności, niezawodności i opóźnienia w porównaniu do dysków HDD. Standardowe dyski SSD są odpowiednie dla serwerów sieci Web, niski serwerów aplikacji operacje We/Wy, aplikacje dla przedsiębiorstw używana w niewielkim stopniu i obciążeń deweloperskich/testowych.

### <a name="disk-size"></a>Rozmiar dysku

Rozmiary oznaczone gwiazdką są obecnie dostępne w wersji zapoznawczej.

| Standardowe rozmiary dysków SSD | E4 | E6 | E10 | E15 | E20 | E30 | E40 | E50 | E60* | E70* | E80* |
|--------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Rozmiar dysku w GiB | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8192 | 16,384 | 32,767 |
| Liczba operacji wejścia/wyjścia na sekundę na dysk | Do 120 | Maksymalnie 240 | Maks. 500 | Maks. 500 | Maks. 500 | Maks. 500 | Maks. 500 | Maks. 500 | Maksymalnie 1300 | Do 2000 | Do 2000 |
| Przepływność na dysk |  Maksymalnie 25 MiB/s |  Maksymalnie 50 MiB/s  |  Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s| Maksymalnie 300 MiB/s |  Maks. 500 MiB/s | Maks. 500 MiB/s |

## <a name="standard-hdd"></a>Standardowa, dysk twardy

Azure standardowych dysków twardych dostarczanie obsługi niezawodne, niedrogie dysków dla maszyn wirtualnych obciążeń niewrażliwego na opóźnienia. Obsługuje również obiekty BLOB, tabel, kolejek i plików. Dzięki usłudze standard storage dane są przechowywane dyski twarde (HDD). Podczas pracy z maszynami wirtualnymi, używając dysków SSD i HDD w warstwie standardowa dla scenariuszy deweloperskich lub testowych oraz mniej krytycznych obciążeń. Magazynu w warstwie standardowa jest dostępna we wszystkich regionach platformy Azure.

### <a name="disk-size"></a>Rozmiar dysku

Rozmiary oznaczone gwiazdką są obecnie dostępne w wersji zapoznawczej.

| Typ dysku standardowego | S4 | S6 | S10 | S15 | S20 | S30 | S40 | S50 | S60* | S70* | S80* |
|--------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Rozmiar dysku w GiB | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8192 | 16,384 | 32,767 |
| Liczba operacji wejścia/wyjścia na sekundę na dysk | Maks. 500 | Maks. 500 | Maks. 500 | Maks. 500 | Maks. 500 | Maks. 500 | Maks. 500 | Maks. 500 | Maksymalnie 1300 | Do 2000 | Do 2000 |
| Przepływność na dysk | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s| Maksymalnie 300 MiB/s | Maks. 500 MiB/s | Maks. 500 MiB/s |

## <a name="billing"></a>Rozliczenia

Korzystając z dysków zarządzanych, zastosuj następujące zagadnienia dotyczące rozliczeń:

- Typ dysku
- dysk zarządzany rozmiar
- Migawki
- Wychodzące transfery danych
- Liczba transakcji

**Rozmiar dysku zarządzanego**: opłaty za dyski zarządzane są naliczane zarezerwowany rozmiar. Usługi Azure maps zaprowizowany rozmiar (z zaokrągleniem) do najbliższej rozmiaru dysku oferowane przez użytkownika. Szczegółowe informacje o oferowanych rozmiary dysków można zobaczyć w poprzednich tabelach. Każdy dysk mapuje do oferty rozmiar dysku obsługiwane i jest rozliczana w związku z tym. Na przykład, jeśli zainicjowano obsługę administracyjną 200 GiB SSD w warstwie standardowa, jest on mapowany ofertę rozmiar dysku E15 (256 GiB). Rozliczenia dla dowolnego dysku jest proporcjonalnie do liczby godzin przy użyciu miesięczna oferta usługi Premium Storage. Na przykład jeśli zainicjowano obsługę administracyjną E10 dysku, a następnie usunięte 20 godzin, są naliczane dla oferty E10 jest naliczany proporcjonalnie do 20 godzin. Jest to niezależnie od ilości rzeczywiste dane zapisane na dysku.

**Migawki**: Migawki są rozliczane na podstawie rozmiaru używane. Na przykład jeśli utworzysz migawkę dysku zarządzanego z zaprowizowaną pojemnością 64 GiB i rzeczywistym użyciem danych rozmiaru 10 GiB migawki jest zapłacisz tylko za użyte dane o rozmiarze od 10 GiB.