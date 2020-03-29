---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78941807"
---
W poniższej tabeli opisano cele dotyczące pojemności, skalowalności i wydajności magazynu tabel.

| Zasób | Środowisko docelowe |
|----------|---------------|
| Liczba tabel na koncie magazynu platformy Azure | Ograniczone tylko pojemnością konta magazynu |
| Liczba partycji w tabeli | Ograniczone tylko pojemnością konta magazynu |
| Liczba jednostek w partycji | Ograniczone tylko pojemnością konta magazynu |
| Maksymalny rozmiar pojedynczej tabeli | 500 TiB |
| Maksymalny rozmiar pojedynczej jednostki, w tym wszystkie wartości właściwości | 1 MiB |
| Maksymalna liczba właściwości w encji tabeli | 255 (w tym trzy właściwości systemu, **PartitionKey,** **RowKey**i **Timestamp)** |
| Maksymalny całkowity rozmiar pojedynczej nieruchomości w jednostce | Różni się w zależności od typu właściwości. Aby uzyskać więcej informacji, zobacz **Typy właściwości** w [opisie modelu danych usługi tabel](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Rozmiar **klucza partycji** | Łańcuch o rozmiarze do 1 KiB |
| Rozmiar **klawisza RowKey** | Łańcuch o rozmiarze do 1 KiB |
| Rozmiar transakcji grupy jednostek | Transakcja może zawierać co najwyżej 100 jednostek, a ładunek musi mieć rozmiar mniejszy niż 4 MiB. Transakcja grupy jednostek może zawierać aktualizację do jednostki tylko raz. |
| Maksymalna liczba zasad dostępu przechowywanego na tabelę | 5 |
| Maksymalna stawka żądania na konto magazynu | 20 000 transakcji na sekundę, co zakłada rozmiar jednostki 1-KiB |
| Przepływność docelowa dla partycji pojedynczej tabeli (1 jednostki KiB) | Do 2000 jednostek na sekundę |