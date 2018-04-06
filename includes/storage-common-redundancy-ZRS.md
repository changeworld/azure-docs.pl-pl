---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: f6d437e4ad0e05d55c408ad8f9defe5385b52050
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
Strefy magazyn geograficznie nadmiarowy (ZRS) synchronicznie replikuje dane między trzy klastry magazynu w pojedynczym regionie. Każdy klaster magazyn jest fizycznie oddzielona od innych i znajduje się w jej własnej strefie dostępności (AZ). Każdej strefy dostępności i klaster ZRS, to jest autonomiczny z oddzielnych narzędzi i możliwości sieciowych.

Przechowywanie danych w ramach konta ZRS zapewnia, że będzie można uzyskać dostępu i zarządzać danymi w przypadku, gdy strefa jest niedostępny. Magazyn ZRS zapewnia doskonałą wydajność i bardzo małych opóźnień.

Aby uzyskać więcej informacji na temat stref dostępności, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="zrs-for-high-availability"></a>Magazyn ZRS wysokiej dostępności 

W scenariuszach wymagających wysoki poziom spójności, silne trwałości i wysokiej dostępności, nawet w przypadku awarii lub klęski żywiołowej renderuje centrum danych niedostępne, należy wziąć pod uwagę ZRS. Magazyn ZRS zapewnia trwałość obiektów magazynu co najmniej % 99.9999999999 (12 na 9) w danym roku.

Magazyn ZRS obecnie obsługuje standardowe, ogólnego przeznaczenia v2 typy kont (GPv2). Magazyn ZRS jest dostępna dla blokowych obiektów blob, z systemem innym niż dysk stronicowe obiekty BLOB, plików, tabel i kolejek. 

Magazyn ZRS jest ogólnie dostępna w następujących regionach:

- Wschodnie stany USA 2
- Środkowe stany USA
- Europa Północna
- Europa Zachodnia
- Francja Środkowa
- Azja Południowo-Wschodnia

Firma Microsoft w dalszym ciągu włączyć ZRS w różnych regionach platformy Azure.

### <a name="what-happens-when-a-zone-becomes-unavailable"></a>Co się stanie, gdy strefa jest niedostępny?

Dane pozostaną elastyczne, jeśli strefa jest niedostępny. Firma Microsoft zaleca, nadal należy uwzględnić wskazówki dotyczące obsługi, takie jak wdrażanie zasad ponawiania z wykładniczej wycofania wystąpienia błędu przejściowego. Gdy strefa jest niedostępny, Azure zobowiązuje się sieci aktualizacji, na przykład DNS repointing. Te aktualizacje mogą wpływać na aplikację, jeśli przed ukończył uzyskują dostęp do danych.

Magazyn ZRS nie może chronić dane przed regionalnej awarii, gdzie trwale dotyczy wielu strefach. Zamiast tego ZRS zapewnia odporność danych w przypadku niedostępności danych czasowych. Do ochrony przed regionalnej awarii, firma Microsoft zaleca używanie [magazyn geograficznie nadmiarowy (GRS): replikacji między regionalne usługi Azure Storage](../articles/storage/common/storage-redundancy-grs.md).

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>Klasycznym ZRS: Starszych opcji nadmiarowości blokowych obiektów blob
> [!NOTE]
> Amortyzacja i wymagane migracji na 31 marca 2021 planuje się kontami ZRS klasycznego. Firma Microsoft będzie wysyłać szczegółowe klientom ZRS klasycznego przed wycofywanie. Firma Microsoft planuje umożliwiają proces automatycznej migracji z klasycznym ZRS ZRS w przyszłości.

Klasycznym ZRS jest dostępna tylko dla blokowych obiektów blob w ogólnego przeznaczenia V1 kont magazynu (GPv1). Magazyn ZRS klasycznego asynchronicznie replikuje dane między centrami danych w ramach jednej do dwóch regionach. Replika może być niedostępna, chyba że firma Microsoft zainicjuje przejście w tryb failover do regionu pomocniczego. Nie można przekonwertować konta klasycznego magazynu ZRS na konto magazynu LRS lub GRS ani odwrotnie, a ponadto konto klasycznego magazynu ZRS nie ma metryk ani funkcji rejestrowania.

Nie można przekonwertować ZRS klasycznych kont, do lub z LRS, GRS lub RA-GRS. Magazyn ZRS klasycznych kont nie obsługują również metryki lub rejestrowania.

Po ZRS jest ogólnie dostępna w regionie, nie można utworzyć konto klasycznego ZRS z portalu w danym regionie, ale można go utworzyć za pomocą innych środków, takich jak programu PowerShell, interfejsu wiersza polecenia i tak dalej.

Z konta LRS, ZRS klasycznego, GRS lub RA-GRS lub ręcznego przeprowadzania migracji danych konta ZRS, użyj narzędzia AzCopy, Eksploratora usługi Storage platformy Azure, programu Azure PowerShell lub wiersza polecenia platformy Azure. Można również tworzyć własne rozwiązania migracji z jednym z biblioteki klienta magazynu Azure.