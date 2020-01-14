---
title: Rozwiązywanie typowych błędów w interfejsie API Azure Cosmos DB dla usługi Mongo DB
description: W tym dokumencie omówiono sposoby rozwiązywania typowych problemów napotykanych w interfejsie API Azure Cosmos DB MongoDB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 41d667f4e90114052a17c5707989634bbb5fc421
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75719838"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Rozwiązywanie typowych problemów z interfejsem API Azure Cosmos DB MongoDB

Azure Cosmos DB implementuje protokoły sieciowe wspólnych baz danych NoSQL, w tym MongoDB. Ze względu na implementację protokołu sieci można w sposób przezroczysty korzystać z Azure Cosmos DB przy użyciu istniejących zestawów SDK klienta, sterowników i narzędzi, które współpracują z bazami danych NoSQL. Azure Cosmos DB nie używa żadnego kodu źródłowego baz danych do udostępniania interfejsów API zgodnych z okablowaniem dla dowolnych baz danych NoSQL. Każdy sterownik klienta MongoDB, który zrozumie wersje protokołu sieci, może łączyć się z Azure Cosmos DB.

Chociaż interfejs API Azure Cosmos DB dla MongoDB jest zgodny z 3,2 wersją protokołu MongoDB (operatory zapytań i funkcje dodane w wersji 3,4 są obecnie dostępne jako wersja zapoznawcza), istnieją pewne niestandardowe kody błędów, które odpowiadają Azure Cosmos DB określone błędy. W tym artykule opisano różne błędy, kody błędów i kroki umożliwiające rozwiązanie tych błędów.

## <a name="common-errors-and-solutions"></a>Typowe błędy i rozwiązania

| Błąd               | Code  | Opis  | Rozwiązanie  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Całkowita liczba jednostek żądań, używane jest większa niż współczynnik aprowizowane jednostki żądań dla kolekcji i zostały ograniczone. | Rozważ przeskalowanie przepływności przypisanej do kontenera lub zestawu kontenerów z Azure Portal lub Ponów próbę wykonania operacji. |
| ExceededMemoryLimit | 16501 | Jako usługa dla wielu dzierżawców operacji stała się za pośrednictwem przydziału pamięci klienta. | Zmniejsz zakres operacji przy użyciu bardziej restrykcyjnych kryteria zapytania lub skontaktuj się z działem pomocy technicznej firmy [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Przykład: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Ścieżka indeksu odpowiadająca określonemu elementowi order-by jest wykluczona/zapytanie order by nie ma odpowiedniego indeksu złożonego, z którego może być obsługiwane. | 2 | Zapytanie żąda sortowania dla pola, które nie jest indeksowane. | Utwórz zgodny indeks (lub indeks złożony) dla podjętych kwerend sortowania. |
| Problemy dotyczące wersji MongoDB Wire | - | Starsze wersje sterowników MongoDB nie mogą wykryć nazwy konta usługi Azure Cosmos w parametrach połączenia. | Dołącz *nazwa_aplikacji = @**accountname**@* na końcu interfejsu API Cosmos DB dla parametrów połączenia MongoDB, gdzie ***accountname*** jest nazwą konta Cosmos DB. |


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

