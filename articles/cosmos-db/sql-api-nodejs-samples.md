---
title: Przykłady środowiska node.js dla bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Znajdź przykłady Node.js w usłudze github dla typowych zadań w usłudze Azure DB rozwiązania Cosmos, w tym operacji CRUD.
keywords: Przykłady środowiska node.js
services: cosmos-db
author: moderakh
manager: kfile
editor: monicar
documentationcenter: nodejs
ms.assetid: d87d97be-47a5-4928-8d46-a541fbb33213
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: moderakh
ms.openlocfilehash: 3d18e9bde7c8e5a5b8c3f192678f62eedf5fb693
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Przykłady rozwiązania Cosmos Node.js bazy danych Azure
> [!div class="op_single_selector"]
> * [Przykłady .NET](sql-api-dotnet-samples.md)
> * [Przykłady środowiska node.js](sql-api-nodejs-samples.md)
> * [Przykłady dla języka Python](sql-api-python-samples.md)
> * [Galeria próbki kodu platformy Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Przykładowe rozwiązania, wykonujących operacje CRUD i innymi typowymi operacjami zasobów bazy danych Azure rozwiązania Cosmos znajdują się w [azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) repozytorium GitHub. Ten artykuł zawiera:

* Pliki projektu łącza do zadań w każdym przykładzie Node.js.
* Linki do pokrewnych interfejsu API odwoływać się do zawartości.

**Wymagania wstępne**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Możesz [aktywować korzyści dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Your Visual Studio subskrypcji otrzymasz kredyt, co miesiąc, używanego programu płatnych usług Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Należy również [Node.js SDK](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Każda próbka jest autonomicznym środowiskiem, konfiguruje się i czyści po samej siebie. Tak, przykłady wystawiać wielu wywołań [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection). Zawsze, gdy jest to realizowane subskrypcji będą naliczane za godzinę użycia na warstwę wydajności kolekcji tworzona.
   > 
   > 

## <a name="database-examples"></a>Przykłady bazy danych
[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) pliku [DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement) projektu pokazano, jak wykonać poniższe zadania.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie bazy danych](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) |[DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase) |
| [Zapytania konta bazy danych](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) |[DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabases) |
| [Przeczytaj przez identyfikator bazy danych](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) |[DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase) |
| [Lista baz danych, konta](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) |[DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabases) |
| [Usuwanie bazy danych](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) |[DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase) |

## <a name="collection-examples"></a>Przykłady kolekcji
[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) pliku [CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement) projektu pokazano, jak wykonać poniższe zadania.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie kolekcji](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) |[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection) |
| [Odczytaj listę wszystkich kolekcji w bazie danych](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) |[DocumentClient.readCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollections) |
| [Pobierz kolekcję przez _self](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) |[DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) |
| [Pobierz kolekcję według identyfikatora](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) |[DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) |
| [Pobierz warstwę wydajności kolekcji](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) |[DocumentQueryable.queryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers) |
| [Zmień warstwę wydajności kolekcji](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) |[DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer) |
| [Usuwanie kolekcji](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) |[DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection) |

## <a name="document-examples"></a>Przykłady dokumentu
[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) pliku [DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement) projektu pokazano, jak wykonać poniższe zadania.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie dokumentów](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) |[DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument) |
| [Przeczytaj dokument dla kolekcji](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument) |
| [Przeczytaj dokument według Identyfikatora](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument) |
| [Przeczytaj dokument tylko wtedy, gdy dokument został zmieniony](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Zapytanie dla dokumentów](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) |[DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments) |
| [Zastępowanie dokumentu](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) |[DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument) |
| [Zamień na warunkowego sprawdzenia ETag dokumentu](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) |[DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Usuwanie dokumentu](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) |[DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument) |

## <a name="indexing-examples"></a>Przykłady indeksowania
[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) pliku [IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement) projektu pokazano, jak wykonać poniższe zadania.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Utwórz kolekcję z domyślnego indeksowania](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L657-L701) |[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection) |
| [Ręcznie indeksu określonego dokumentu](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) |[RequestOptions.indexingDirective: "include"](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Ręcznie wykluczyć określony dokument z indeksu](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) |[RequestOptions.indexingDirective: "Wyklucz"](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Użyj opóźnieniem indeksowania importowania zbiorczego lub odczytać duże kolekcje](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) |[IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#IndexingMode) |
| [Dołączenie indeksowania określonych ścieżek dokumentu](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) |[IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy) |
| [Wyklucz określone ścieżki indeksowania](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) |[IndexingPolicy.ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy) |
| [Zezwalaj na skanowanie w ścieżce ciągu podczas operacji zakresu](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347) |[FeedOptions.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions) |
| [Utwórz indeks zakresu na ścieżce ciągu](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) |[IndexKind.Range](http://azure.github.io/azure-documentdb-node/global.html#IndexKind), [IndexingPolicy](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy), [DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument) |
| [Utwórz kolekcję o indexPolicy domyślne, a następnie aktualizować online](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) |[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html) |

Aby uzyskać więcej informacji na temat indeksowania, zobacz [Azure DB rozwiązania Cosmos zasady indeksowania](indexing-policies.md).

## <a name="server-side-programming-examples"></a>Przykłady programowania po stronie serwera
[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) pliku [ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts) projektu pokazano, jak wykonać poniższe zadania.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Utwórz procedurę składowaną](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) |[DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure) |
| [Wykonywanie procedury przechowywanej](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) |[DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure) |

Aby uzyskać więcej informacji na temat programowania po stronie serwera, zobacz [programowanie po stronie serwera bazy danych Azure rozwiązania Cosmos: procedury składowane, wyzwalacze bazy danych i funkcji UDF](programming.md).

## <a name="partitioning-examples"></a>Przykłady partycjonowania
[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) pliku [partycjonowanie](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning) projektu pokazano, jak wykonać poniższe zadania.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Użyj HashPartitionResolver](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) |[HashPartitionResolver](http://azure.github.io/azure-documentdb-node/HashPartitionResolver.html) |

Aby uzyskać więcej informacji na temat partycjonowania danych w usłudze Azure DB rozwiązania Cosmos, zobacz [partycji i skali danych w usłudze Azure DB rozwiązania Cosmos](partition-data.md).

