---
title: Przykłady kodu Python dla interfejsu SQL API usługi Azure Cosmos DB
description: Znajdź w witrynie GitHub przykłady kodu Python służące do wykonywania typowych zadań w usłudze Azure Cosmos DB, w tym operacji CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 07/23/2019
ms.author: sngun
ms.openlocfilehash: 1e7c3e06c8aa49f9a23d3cc600ee6f60218c2fbd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72170139"
---
# <a name="azure-cosmos-db-python-examples"></a>Przykłady kodu Python dla usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Przykłady SDK .NET V2](sql-api-dotnet-samples.md)
> * [Przykłady SDK .NET V3](sql-api-dotnet-v3sdk-samples.md)
> * [Przykłady kodu Java](sql-api-java-samples.md)
> * [Przykłady asynchronicznego kodu Java](sql-api-async-java-samples.md)
> * [Przykłady dla platformy Node.js](sql-api-nodejs-samples.md)
> * [Przykłady Pythona](sql-api-python-samples.md)
> * [Galeria przykładów kodu dla platformy Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Przykładowe rozwiązania do wykonywania operacji CRUD i innych typowych działań na zasobach usługi Azure Cosmos DB można znaleźć w repozytorium [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) w witrynie GitHub. Ten artykuł zawiera:

* Linki do zadań w poszczególnych plikach projektów przykładowych w języku Python. 
* Linki do powiązanej dokumentacji interfejsu API.

**Wymagania wstępne**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Możesz [aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): w ramach subskrypcji programu Visual Studio co miesiąc otrzymasz środki, które można przeznaczyć na płatne usługi platformy Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Potrzebny będzie również [zestaw SDK dla języka Python](sql-api-sdk-python.md). 
   
   > [!NOTE]
   > Przykłady są niezależne — każdy z nich jest automatycznie konfigurowany i automatycznie czyszczony po użyciu. Przykłady obejmują wiele wywołań elementu [CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createcontainer-database-link--collection--options-none-). Każde z tych wywołań wiąże się z dodaniem jednej godziny użycia do rozliczenia za subskrypcję. Aby uzyskać więcej informacji na temat rozliczeń w usłudze Azure Cosmos DB, zobacz [Azure Cosmos DB — cennik](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Przykłady dotyczące baz danych
Program.py [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) plik projektu [Zarządzanie bazami danych](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) pokazuje, jak wykonać następujące zadania. Aby dowiedzieć się więcej o bazach danych usługi Azure Cosmos przed uruchomieniem następujących przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) artykułu koncepcyjnego. 

| Zadanie | Odwołanie API |
| --- | --- |
| [Tworzenie bazy danych](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L65-L76) |[CosmosClient.CreateDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createdatabase-database--options-none-) |
| [Odczytywanie bazy danych na podstawie identyfikatora](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L79-L96) |[CosmosClient.ReadDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readdatabase-database-link--options-none-) |
| [Wyświetlanie listy baz danych na koncie](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L99-L110) |[CosmosClient.ReadDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readdatabases-options-none-) |
| [Usuwanie bazy danych](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L113-L126) |[CosmosClient.DeleteDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#deletedatabase-database-link--options-none-) |

## <a name="collection-examples"></a>Przykłady dotyczące kolekcji
Program.py [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) plik projektu [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) pokazuje, jak wykonać następujące zadania. Aby dowiedzieć się więcej o kolekcji usługi Azure Cosmos przed uruchomieniem następujących przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) artykułu koncepcyjnego. 

| Zadanie | Odwołanie API |
| --- | --- |
| [Tworzenie kolekcji](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L84-L135) |[CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createcontainer-database-link--collection--options-none-) |
| [Odczytywanie listy wszystkich kolekcji w bazie danych](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L210-L222) |[CosmosClient.ReadContainers](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readcontainers-database-link--options-none-) |
| [Pobieranie kolekcji na podstawie identyfikatora](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L190-L208) |[CosmosClient.ReadContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readcontainer-collection-link--options-none-) |
| [Zmienianie przepływności kolekcji](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L184-L188) | [CosmosClient.ReplaceOffer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replaceoffer-offer-link--offer-)|
| [Usuwanie kolekcji](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L224-L238) |[CosmosClient.DeleteContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#deletecontainer-collection-link--options-none-) |

## <a name="document-examples"></a>Przykłady dotyczące dokumentów
Program.py [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) plik projektu [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) pokazuje, jak wykonać następujące zadania. Aby dowiedzieć się więcej o dokumentach usługi Azure Cosmos przed uruchomieniem następujących przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) artykułu koncepcyjnego. 

| Zadanie | Odwołanie API |
| --- | --- |
| [Tworzenie dokumentu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createitem-database-or-container-link--document--options-none-) |
| [Tworzenie kolekcji dokumentów](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createitem-database-or-container-link--document--options-none-) |
| [Czytanie dokumentu według identyfikatora](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[CosmosClient.ReadItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readitem-document-link--options-none-) |
| [Odczytywanie wszystkich dokumentów w kolekcji](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[CosmosClient.ReadItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readitems-collection-link--feed-options-none-) |
| [Zastępowanie dokumentu przy użyciu warunkowego sprawdzenia ETag](https://github.com/Azure/azure-cosmos-python/blob/a21f6fb4bad3f59909ef43558b598f9fb476b7bc/test/crud_tests.py#L1216-L1218) | [CosmosClient.ReplaceItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replaceitem-document-link--new-document--options-none-) |

## <a name="indexing-examples"></a>Przykłady dotyczące indeksowania
Plik [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) projektu [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) pokazuje, jak wykonać następujące zadania.  Aby dowiedzieć się więcej o indeksowaniu w usłudze Azure Cosmos DB przed uruchomieniem następujących przykładów, zobacz [zasady indeksowania,](index-policy.md) [typy indeksowania](index-types.md)i [indeksowanie ścieżek](index-paths.md) artykułów koncepcyjnych. 

| Zadanie | Odwołanie API |
| --- | --- |
| [Używanie indeksowania ręcznego (zamiast automatycznego)](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) | Zasady automatycznego indeksowania |
| [Wyłączanie określonych ścieżek dokumentów z indeksu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) | Zasady indeksowania z wykluczonymi ścieżkami|
| [Wyłączanie dokumentu z indeksu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[IndexingDirective.Exclude](/python/api/azure-cosmos/azure.cosmos.documents.indexingdirective#exclude) |
| [Ustawianie trybu indeksowania](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[IndexingMode](/python/api/azure-cosmos/azure.cosmos.documents.indexingmode) |
| [Używanie indeksów zakresu z ciągami](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) | Zasady indeksowania z uwzględnionymi ścieżkami|
| [Przekształcanie indeksu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[CosmosClient.ReplaceContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replacecontainer-collection-link--collection--options-none-) |

## <a name="query-examples"></a>Przykłady zapytań
Przykładowe projekty pokazują również, jak wykonać następujące zadania kwerendy. Aby dowiedzieć się więcej o odwołaniu do kwerendy SQL w usłudze Azure Cosmos DB przed uruchomieniem następujących przykładów, zobacz artykuł koncepcyjny [przykładów zapytań SQL.](how-to-sql-query.md) Aby dowiedzieć się więcej o odwołaniu do kwerendy SQL w usłudze Azure Cosmos DB przed uruchomieniem następujących przykładów, zobacz artykuł koncepcyjny [przykładów zapytań SQL.](how-to-sql-query.md) 


| Zadanie | Odwołanie API |
| --- | --- |
| [Wykonywanie zapytania dotyczącego bazy danych na koncie](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L49-L62) |[CosmosClient.QueryDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#querydatabases-query--options-none-) |
| [Wykonywanie zapytań dotyczących dokumentów](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[CosmosClient.QueryItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#queryitems-database-or-container-link--query--options-none--partition-key-none-) |
| [Wymuszanie operacji skanowania zakresu na ścieżce z indeksem skrótu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[HttpHeaders.EnableScanInQuery](/python/api/azure-cosmos/azure.cosmos.http_constants.httpheaders#enablescaninquery) |

