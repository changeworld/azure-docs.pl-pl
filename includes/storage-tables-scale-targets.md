---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 2319a7620b70547d186a4ef5cb96f5ca6684c62c
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305317"
---
| Resource | Cel |
|----------|---------------|
| Maksymalny rozmiar pojedynczej tabeli | 500 TiB |
| Maksymalny rozmiar jednostkę tabeli | 1 MiB |
| Maksymalna liczba właściwości w obiekcie tabeli | 255, która zawiera trzy właściwości systemowe: Właściwości PartitionKey i RowKey, znacznik czasu: |
| Maksymalny całkowity rozmiar wartości właściwości w obiekcie | 1 MiB |
| Maksymalny całkowity rozmiar pojedynczej właściwości w obiekcie | Zmienia typ właściwości. Aby uzyskać więcej informacji, zobacz **typy właściwości** w [opis modelu danych usługi Table Service](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Maksymalna liczba przechowywanych zasad dostępu na tabelę | 5 |
| Maksymalna liczba żądań na konto magazynu | 20 000 transakcji na sekundę, która zakłada, rozmiar jednostki 1 KiB |
| Docelowa przepustowość partycji pojedynczej tabeli (1 jednostki KiB) | Do 2000 jednostek na sekundę |
