---
title: Rozwiązywanie problemów z typowymi błędami w interfejsie API usługi Azure Cosmos DB dla usługi Mongo DB
description: W tym doc omówiono sposoby rozwiązywania typowych problemów napotkanych w interfejsie API usługi Azure Cosmos DB dla mongodb.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941845"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Rozwiązywanie typowych problemów w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB

Usługa Azure Cosmos DB implementuje protokoły przewodowe typowych baz danych NoSQL, w tym mongodb. Ze względu na implementację protokołu sieciowego można w sposób przejrzysty wchodzić w interakcje z usługą Azure Cosmos DB przy użyciu istniejących zestawów SDK klienta, sterowników i narzędzi, które działają z bazami danych NoSQL. Usługa Azure Cosmos DB nie używa żadnego kodu źródłowego baz danych do dostarczania interfejsów API zgodnych z przewodem dla żadnej z baz danych NoSQL. Każdy sterownik klienta mongodb, który rozumie wersje protokołu przewodowego można połączyć się z usługi Azure Cosmos DB.

Podczas gdy interfejs API usługi Azure Cosmos DB dla mongodb jest zgodny z wersją 3.2 protokołu przewodowego MongoDB (operatory zapytań i funkcje dodane w wersji 3.4 są obecnie dostępne w wersji zapoznawczej), istnieją pewne niestandardowe kody błędów, które odpowiadają usłudze Azure Cosmos DB konkretnych błędów. W tym artykule opisano różne błędy, kody błędów i kroki, aby rozwiązać te błędy.

## <a name="common-errors-and-solutions"></a>Typowe błędy i rozwiązania

| Błąd               | Code  | Opis  | Rozwiązanie  |
|---------------------|-------|--------------|-----------|
| TooManyRequests (Prośby o nie)     | 16500 | Całkowita liczba jednostek żądań zużyte jest więcej niż aprowizowana stawka jednostkowa żądania dla kolekcji i został ograniczony. | Należy rozważyć skalowanie przepływności przypisanej do kontenera lub zestawu kontenerów z witryny Azure portal lub można ponowić próbę wykonania operacji. |
| ExceededMemoryLimit | 16501 | Jako usługa wielodostępna operacja przeszła przez przydział pamięci klienta. | Zmniejsz zakres operacji za pomocą bardziej restrykcyjnych kryteriów kwerendy lub skontaktuj się z pomocą techniczną z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Przykład: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Ścieżka indeksu odpowiadająca określonemu item-by zamówienia jest wykluczona / Kolejność według kwerendy nie ma odpowiedniego indeksu złożonego, z którego może być obsługiwany. | 2 | Kwerenda żąda sortowania w polu, które nie jest indeksowane. | Utwórz pasujący indeks (lub indeks złożony) dla kwerendy sortującej, która jest podejmowana. |
| Problemy dotyczące wersji protokołu Wire bazy danych MongoDB | - | Starsze wersje sterowników mongodb nie są w stanie wykryć nazwę konta usługi Azure Cosmos w ciągu połączenia. | Dołącz *appName=@**accountName** @ * na końcu interfejsu API usługi Cosmos DB dla ciągu połączenia MongoDB, gdzie ***accountName*** jest nazwą konta usługi Cosmos DB. |


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

