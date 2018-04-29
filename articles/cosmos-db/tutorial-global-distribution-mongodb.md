---
title: Samouczek dotyczący dystrybucji globalnej usługi Azure Cosmos DB dla interfejsu API bazy danych MongoDB | Microsoft Docs
description: Dowiedz się, jak skonfigurować dystrybucję globalną usługi Azure Cosmos DB przy użyciu interfejsu API bazy danych MongoDB.
services: cosmos-db
keywords: global distribution, MongoDB
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 8bd86c5e66fdf2431e3db12a43e953b022a3770a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-mongodb-api"></a>Jak skonfigurować dystrybucję globalną usługi Azure Cosmos DB przy użyciu interfejsu API bazy danych MongoDB

W tym artykule przedstawiono, jak za pomocą witryny Azure Portal skonfigurować dystrybucję globalną usługi Azure Cosmos DB, a następnie nawiązać połączenie przy użyciu interfejsu API bazy danych MongoDB.

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu [interfejsu API bazy danych MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup-using-the-mongodb-api"></a>Weryfikowanie konfiguracji regionalnej przy użyciu interfejsu API bazy danych MongoDB
Najprostszym sposobem dokładnego sprawdzenia konfiguracji globalnej w ramach interfejsu API dla bazy danych MongoDB jest uruchomienie polecenia *isMaster()* z poziomu powłoki Mongo.

Z poziomu powłoki Mongo:

   ```
      db.isMaster()
   ```
   
Przykładowe wyniki:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region-using-the-mongodb-api"></a>Nawiązywanie połączenia z preferowanym regionem przy użyciu interfejsu API bazy danych MongoDB

Interfejs API bazy danych MongoDB umożliwia określenie preferencji odczytu kolekcji dla globalnie rozproszonej bazy danych. Na potrzeby odczytów z małymi opóźnieniami i wysokiej dostępności globalnej zaleca się ustawienie preferencji odczytu kolekcji na wartość *najbliższe*. Preferencja odczytu *najbliższe* jest skonfigurowana do odczytu z najbliższego regionu.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Dla aplikacji z podstawowym regionem odczytu/zapisu i pomocniczym regionem na potrzeby scenariuszy odzyskiwania awaryjnego zalecamy ustawienie preferencji odczytu na wartość *preferowane pomocnicze*. Preferencja odczytu *preferowane pomocnicze* jest skonfigurowana do odczytu z regionu pomocniczego, gdy region podstawowy jest niedostępny.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Ponadto jeśli chcesz ręcznie określić regiony odczytu, możesz ustawić tag regionu w swoich preferencjach odczytu. You can set the region Tag within your read preference.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

To wszystko — na tym kończy się ten samouczek. Aby dowiedzieć się, jak zarządzać spójnością globalnie replikowanego konta, przeczytaj [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md). Natomiast aby uzyskać więcej informacji na temat sposobu działania globalnej replikacji w usłudze Azure Cosmos DB, zobacz [Dystrybuowanie danych globalnie za pomocą usługi Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu interfejsów API SQL

Teraz możesz przejść do następnego samouczka, aby dowiedzieć się, jak programować lokalnie przy użyciu lokalnego emulatora usługi Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Programowanie lokalnie za pomocą emulatora](local-emulator.md)
