---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78941807"
---
W poniższej tabeli opisano elementy docelowe pojemności, skalowalności i wydajności dla magazynu tabel.

| Zasób | Środowisko docelowe |
|----------|---------------|
| Liczba tabel na koncie magazynu platformy Azure | Ograniczona tylko pojemnością konta magazynu |
| Liczba partycji w tabeli | Ograniczona tylko pojemnością konta magazynu |
| Liczba jednostek w partycji | Ograniczona tylko pojemnością konta magazynu |
| Maksymalny rozmiar pojedynczej tabeli | 500 TiB |
| Maksymalny rozmiar pojedynczej jednostki, w tym wszystkie wartości właściwości | 1 MiB |
| Maksymalna liczba właściwości w jednostce tabeli | 255 (w tym trzy właściwości systemu: **PartitionKey**, **RowKey** i **Timestamp**) |
| Maksymalny łączny rozmiar pojedynczej właściwości w jednostce | Różni się w zależności od typu właściwości. Aby uzyskać więcej informacji, zobacz sekcję **Typy właściwości** w artykule [Omówienie modelu danych usługi Table Service](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Rozmiar właściwości **PartitionKey** | Ciąg o rozmiarze do 1 KiB |
| Rozmiar właściwości **RowKey** | Ciąg o rozmiarze do 1 KiB |
| Rozmiar transakcji grupy jednostek | Transakcja może obejmować maksymalnie 100 jednostek, a rozmiar ładunku musi być mniejszy niż 4 MiB. Transakcja grupy jednostek może zawierać aktualizację jednostki tylko jeden raz. |
| Maksymalna liczba przechowywanych zasad dostępu na tabelę | 5 |
| Maksymalna częstotliwość żądań na konto magazynu | 20 000 transakcji na sekundę przy założeniu, że jednostki mają rozmiar 1 KiB |
| Docelowa przepływność dla pojedynczej partycji tabeli (jednostki o rozmiarze 1 KiB) | Do 2 000 jednostek na sekundę |