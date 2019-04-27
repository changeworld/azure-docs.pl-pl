---
title: Przykłady kodu Python dla interfejsu SQL API usługi Azure Cosmos DB
description: Znajdź w witrynie GitHub przykłady kodu Python służące do wykonywania typowych zadań w usłudze Azure Cosmos DB, w tym operacji CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: cf296d8bb494307dbb58b9de522d55a83892c6d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60546635"
---
# <a name="azure-cosmos-db-python-examples"></a>Przykłady kodu Python dla usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Przykłady dla platformy .NET](sql-api-dotnet-samples.md)
> * [Przykłady kodu Java](sql-api-java-samples.md)
> * [Przykłady asynchronicznego kodu Java](sql-api-async-java-samples.md)
> * [Przykłady dla platformy Node.js](sql-api-nodejs-samples.md)
> * [Przykłady kodu Python](sql-api-python-samples.md)
> * [Galeria przykładów kodu dla platformy Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Przykładowe rozwiązania do wykonywania operacji CRUD i innych typowych działań na zasobach usługi Azure Cosmos DB można znaleźć w repozytorium [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) w witrynie GitHub. Ten artykuł zawiera:

* Linki do zadań w poszczególnych plikach projektów przykładowych w języku Python. 
* Linki do powiązanej dokumentacji interfejsu API.

**Wymagania wstępne**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Możesz [aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): W ramach subskrypcji programu Visual Studio co miesiąc otrzymasz środki, które możesz przeznaczyć na płatne usługi platformy Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Potrzebny będzie również [zestaw SDK dla języka Python](sql-api-sdk-python.md). 
   
   > [!NOTE]
   > Przykłady są niezależne — każdy z nich jest automatycznie konfigurowany i automatycznie czyszczony po użyciu. Przykłady obejmują wiele wywołań elementu [CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createcontainer-database-link--collection--options-none-). Każde z tych wywołań wiąże się z dodaniem jednej godziny użycia do rozliczenia za subskrypcję. Aby uzyskać więcej informacji na temat rozliczeń w usłudze Azure Cosmos DB, zobacz [Azure Cosmos DB — cennik](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Przykłady dotyczące baz danych
[Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) pliku [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) projekt pokazuje, jak wykonać następujące zadania. Aby dowiedzieć się więcej o bazach danych Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [pracy z bazami danych, kontenerów i elementy](databases-containers-items.md) artykuł koncepcyjny. 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie bazy danych](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L65-L76) |[CosmosClient.CreateDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createdatabase-database--options-none-) |
| [Odczytywanie bazy danych na podstawie identyfikatora](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L79-L96) |[CosmosClient.ReadDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readdatabase-database-link--options-none-) |
| [Wyświetlanie listy baz danych na koncie](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L99-L110) |[CosmosClient.ReadDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readdatabases-options-none-) |
| [Usuwanie bazy danych](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L113-L126) |[CosmosClient.DeleteDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#deletedatabase-database-link--options-none-) |

## <a name="collection-examples"></a>Przykłady dotyczące kolekcji
[Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) pliku [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) projekt pokazuje, jak wykonać następujące zadania. Informacje na temat kolekcji usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [pracy z bazami danych, kontenerów i elementy](databases-containers-items.md) artykuł koncepcyjny. 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie kolekcji](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L84-L135) |[CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createcontainer-database-link--collection--options-none-) |
| [Odczytywanie listy wszystkich kolekcji w bazie danych](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L210-L222) |[CosmosClient.ReadContainers](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readcontainers-database-link--options-none-) |
| [Pobieranie kolekcji na podstawie identyfikatora](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L190-L208) |[CosmosClient.ReadContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readcontainer-collection-link--options-none-) |
| [Zmienianie przepływności kolekcji](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L184-L188) | [CosmosClient.ReplaceOffer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#replaceoffer-offer-link--offer-)|
| [Usuwanie kolekcji](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L224-L238) |[CosmosClient.DeleteContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#deletecontainer-collection-link--options-none-) |

## <a name="document-examples"></a>Przykłady dotyczące dokumentów
[Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) pliku [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) projekt pokazuje, jak wykonać następujące zadania. Aby uzyskać informacje o dokumentach w usłudze Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [pracy z bazami danych, kontenerów i elementy](databases-containers-items.md) artykuł koncepcyjny. 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie dokumentu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createitem-database-or-container-link--document--options-none-) |
| [Tworzenie kolekcji dokumentów](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createitem-database-or-container-link--document--options-none-) |
| [Odczytywanie dokumentu na podstawie identyfikatora](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[CosmosClient.ReadItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readitem-document-link--options-none-) |
| [Odczytywanie wszystkich dokumentów w kolekcji](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[CosmosClient.ReadItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readitems-collection-link--feed-options-none-) |
| [Zastępowanie dokumentu przy użyciu warunkowego sprawdzenia ETag](https://github.com/Azure/azure-cosmos-python/blob/a21f6fb4bad3f59909ef43558b598f9fb476b7bc/test/crud_tests.py#L1216-L1218) | [CosmosClient.ReplaceItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#replaceitem-document-link--new-document--options-none-) |

## <a name="indexing-examples"></a>Przykłady dotyczące indeksowania
[Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) pliku [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) projekt pokazuje, jak wykonać następujące zadania.  Aby uzyskać informacje dotyczące indeksowania w usłudze Azure Cosmos DB, przed uruchomieniem poniższych przykładów, zobacz [zasady indeksowania](index-policy.md), [indeksowania typy](index-types.md), i [indeksowania ścieżki](index-paths.md) artykułów koncepcyjnych. 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Używanie indeksowania ręcznego (zamiast automatycznego)](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) | Zasady automatycznego indeksowania |
| [Wyłączanie określonych ścieżek dokumentów z indeksu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) | Zasady indeksowania z wykluczonymi ścieżkami|
| [Wyłączanie dokumentu z indeksu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[IndexingDirective.Exclude](/python/api/azure-cosmos/azure.cosmos.documents.indexingdirective#exclude) |
| [Ustawianie trybu indeksowania](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[IndexingMode](/python/api/azure-cosmos/azure.cosmos.documents.indexingmode) |
| [Używanie indeksów zakresu z ciągami](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) | Zasady indeksowania z uwzględnionymi ścieżkami|
| [Przekształcanie indeksu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[CosmosClient.ReplaceContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#replacecontainer-collection-link--collection--options-none-) |

## <a name="query-examples"></a>Przykłady zapytań
Przykładowe projekty pokazują również jak wykonywać następujące zadania związane z zapytania. Aby poznać dokumentacja zapytań SQL w usłudze Azure Cosmos DB, przed uruchomieniem poniższych przykładów, zobacz temat [przykłady zapytań SQL](how-to-sql-query.md) artykuł koncepcyjny. Aby poznać dokumentacja zapytań SQL w usłudze Azure Cosmos DB, przed uruchomieniem poniższych przykładów, zobacz temat [przykłady zapytań SQL](how-to-sql-query.md) artykuł koncepcyjny. 


| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Wykonywanie zapytania dotyczącego bazy danych na koncie](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L49-L62) |[CosmosClient.QueryDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#querydatabases-query--options-none-) |
| [Wykonywanie zapytań dotyczących dokumentów](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[CosmosClient.QueryItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#queryitems-database-or-container-link--query--options-none--partition-key-none-) |
| [Wymuszanie operacji skanowania zakresu na ścieżce z indeksem skrótu](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[HttpHeaders.EnableScanInQuery](/python/api/azure-cosmos/azure.cosmos.http_constants.httpheaders#enablescaninquery) |

