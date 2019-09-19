---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "67457466"
---
| Resource | Cel |
|----------|---------------|
| Maksymalny rozmiar pojedynczej tabeli | 500 TiB |
| Maksymalny rozmiar jednostki tabeli | 1 MiB |
| Maksymalna liczba właściwości w jednostce tabeli | 255, która obejmuje trzy właściwości systemowe: PartitionKey, RowKey i timestamp |
| Maksymalny łączny rozmiar wartości właściwości w jednostce | 1 MiB |
| Maksymalny łączny rozmiar pojedynczej właściwości w jednostce | Różni się w zależności od typu właściwości. Aby uzyskać więcej informacji, zobacz **typy właściwości** w temacie [Omówienie modelu danych usługi Table Service](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Maksymalna liczba przechowywanych zasad dostępu na tabelę | 5 |
| Maksymalna liczba żądań na konto magazynu | 20 000 transakcji na sekundę, które zakładają, że rozmiar jednostki wynosi 1 KiB |
| Docelowa przepływność dla pojedynczej partycji tabeli (1 KiB-Entities) | Do 2 000 jednostek na sekundę |