---
title: Rozwiązywanie typowych problemów, w usłudze Azure Cosmos DB w interfejsie API usługi Mongo DB
description: Ten dokument w tym artykule omówiono sposoby rozwiązywania typowych problemów napotykanych w interfejsie API usługi Azure Cosmos DB dla bazy danych MongoDB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 5b3d3993a497240c1ea18f0fcf852c0e834f6e79
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735709"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Rozwiązywanie typowych problemów z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB

Usługa Azure Cosmos DB implementuje protokoły przewodowy wspólnej bazy danych NoSQL, łącznie z bazą danych MongoDB. Ze względu na implementacji protokołu o komunikacji sieciowej sposób niewidoczny dla użytkownika mogą współdziałać z usługą Azure Cosmos DB przy użyciu istniejących zestawów SDK klienta, sterowników i narzędzi, które działają z bazy danych NoSQL. Usługa Azure Cosmos DB nie używa żadnych kodu źródłowego, baz danych zapewniające przewodowy zgodnych interfejsów API dla każdej bazy danych NoSQL. Dowolnego sterownika klienta bazy danych MongoDB, obsługującą protokołów sieci mogą łączyć się z usługi Azure Cosmos DB.

Interfejs API usługi Azure Cosmos DB, bazy danych mongodb jest niezgodny z wersją 3.2 programu protokół przewodowy MongoDB (zapytania, operatory i funkcje dodane w wersji 3.4 są obecnie dostępne w wersji zapoznawczej), istnieją niektóre kody błędów niestandardowych, które odnoszą się do usługi Azure Cosmos DB określone błędy. W tym artykule opisano różne błędy, kody błędów i czynności, aby rozwiązać te błędy.

## <a name="common-errors-and-solutions"></a>Typowe błędy i rozwiązania

| Błąd               | Kod  | Opis  | Rozwiązanie  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Całkowita liczba jednostek żądań, używane jest większa niż współczynnik aprowizowane jednostki żądań dla kolekcji i zostały ograniczone. | Rozważ skalowanie przepływności przypisanych do kontenera lub zestaw kontenerów w witrynie Azure portal lub ponowić operację. |
| ExceededMemoryLimit | 16501 | Jako usługa dla wielu dzierżawców operacji stała się za pośrednictwem przydziału pamięci klienta. | Zmniejsz zakres operacji przy użyciu bardziej restrykcyjnych kryteria zapytania lub skontaktuj się z działem pomocy technicznej firmy [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Przykład: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Problemy z wersją przewodowy MongoDB | - | Nie można wykryć nazwy konta usługi Azure Cosmos w parametrach połączenia są starsze wersje sterowników bazy danych MongoDB. | Dołącz *appName = @**accountName** @*  na końcu interfejsu API usługi Cosmos DB parametry połączenia bazy danych MongoDB, gdzie ***accountName*** to nazwa konta usługi Cosmos DB . |


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

