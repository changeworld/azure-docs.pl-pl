---
title: Przykłady platformy Node.js dla usługi Azure Cosmos DB
description: Znajdź w witrynie GitHub przykłady kodu Node.js służące do wykonywania typowych zadań w usłudze Azure Cosmos DB, w tym operacji CRUD.
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.topic: sample
ms.date: 09/24/2018
ms.author: deborahc
ms.openlocfilehash: 491e31fecedf52be0ecf2aee470fab4ce168c866
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538450"
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Przykłady dla platformy Node.js w usłudze Azure Cosmos DB
> [!div class="op_single_selector"]
> * [Przykłady dla platformy .NET](sql-api-dotnet-samples.md)
> * [Przykłady kodu Java](sql-api-java-samples.md)
> * [Przykłady asynchronicznego kodu Java](sql-api-async-java-samples.md)
> * [Przykłady dla platformy Node.js](sql-api-nodejs-samples.md)
> * [Przykłady kodu Python](sql-api-python-samples.md)
> * [Galeria przykładów kodu dla platformy Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Przykładowe rozwiązania do wykonywania operacji CRUD i innych typowych działań na zasobach usługi Azure Cosmos DB można znaleźć w repozytorium [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples) w witrynie GitHub. Ten artykuł zawiera:

* Linki do zadań w poszczególnych przykładowych plikach projektów platformy Node.js.
* Linki do powiązanej dokumentacji interfejsu API.

**Wymagania wstępne**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Możesz [aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): W ramach subskrypcji programu Visual Studio co miesiąc otrzymasz środki, które możesz przeznaczyć na płatne usługi platformy Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Potrzebujesz również [zestawu SDK dla języka JavaScript](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Przykłady są niezależne — każdy z nich jest automatycznie konfigurowany i automatycznie czyszczony po użyciu. Przykłady te obejmują wiele wywołań elementu [Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#create). W każdej takiej sytuacji w subskrypcji będzie naliczana opłata za 1 godzinę użycia na warstwę wydajności tworzonego kontenera.
   > 
   > 

## <a name="database-examples"></a>Przykłady dotyczące baz danych
Plik [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement/app.js) w projekcie [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/DatabaseManagement) pokazuje sposób wykonywania poniższych zadań.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie bazy danych, jeśli nie istnieje](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L35-L37) |[Databases.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest#createifnotexists) |
| [Wyświetlanie listy baz danych na koncie](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Databases.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/databases?view=azure-node-latest#readall) |
| [Odczytywanie bazy danych na podstawie identyfikatora](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L40-L42) |[Database.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest#read) |
| [Usuwanie bazy danych](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/DatabaseManagement/app.js#L57-L60) |[Database.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/database?view=azure-node-latest#delete) |

## <a name="container-examples"></a>Przykłady dotyczące kontenerów
Plik [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement/app.js) w projekcie [ContainerManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) pokazuje sposób wykonywania poniższych zadań.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie kontenera, jeśli nie istnieje](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.createIfNotExists](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#createifnotexists) |
| [Wyświetlanie listy kontenerów dla konta](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L36-L37) |[Containers.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#readall) |
| [Odczytywania kolekcji na podstawie identyfikatora](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L47-L51) |[Container.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest#read) |
| [Usuwanie kontenera](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ContainerManagement/app.js#L54-L55) |[Container.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/container?view=azure-node-latest#delete) |

## <a name="item-examples"></a>Przykłady dotyczące elementów
Plik [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement/app.js) w projekcie [ItemManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) pokazuje sposób wykonywania poniższych zadań.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie elementów](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L49-L56) |[Items.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#create) |
| [Odczytywanie wszystkich elementów w kontenerze](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Items.readAll](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#readall) |
| [Odczytywanie elementów na podstawie Identyfikatora](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L59-L64) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#read) |
| [Odczytywanie elementów tylko wtedy, gdy zostały zmienione](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L73-L94) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#read)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Wykonywanie zapytań dotyczących dokumentów](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L97-L118) |[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#query) |
| [Zastępowanie elementu](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L131-L136) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#replace) |
| [Zastępowanie elementu z warunkowym sprawdzaniem elementu ETag](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L139-L160) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#replace)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Usuwanie elementu](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ItemManagement/app.js#L162-L164) |[Item.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest#delete) |

## <a name="indexing-examples"></a>Przykłady dotyczące indeksowania
Plik [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement/app.js) w projekcie [IndexManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/IndexManagement) pokazuje sposób wykonywania poniższych zadań.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Ręczne indeksowanie określonego dokumentu](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L135-L177) |[RequestOptions.indexingDirective: „include”](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Ręczne wykluczanie określonego dokumentu z indeksu](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L90-L131) |[RequestOptions.indexingDirective: „exclude”](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Używanie indeksowania opóźnionego na potrzeby importowania zbiorczego lub odczytywania dużych kontenerów](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L183-L214) |[IndexingMode.Lazy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingmode?view=azure-node-latest) |
| [Wyłączanie ścieżki z indeksu](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L352-L429) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest#excludedpaths) |
| [Zezwalanie na skanowanie na ścieżce ciągu podczas operacji zakresu](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L219-L275) |[FeedOptions.EnableScanInQuery](https://docs.microsoft.com/javascript/api/%40azure/cosmos/feedoptions?view=azure-node-latest#enablescaninquery) |
| [Tworzenie indeksu zakresu na ścieżce ciągu](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L281-L346) |[IndexKind.Range](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexkind?view=azure-node-latest), [IndexingPolicy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest), [Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest#query) |
| [Tworzenie kontenera z domyślnymi zasadami indexPolicy, a następnie aktualizowanie w trybie online](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/IndexManagement/app.js#L435-L507) |[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest#create)


Aby uzyskać więcej informacji na temat indeksowania, zobacz [Azure Cosmos DB indexing policies (Zasady indeksowania w usłudze Azure Cosmos DB)](index-policy.md).

## <a name="server-side-programming-examples"></a>Przykłady programowania po stronie serwera
Plik [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/app.js) w projekcie [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) pokazuje sposób wykonywania poniższych zadań.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie procedury składowanej](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/JS/upsert.js#L12-L72) |[StoredProcedures.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedures?view=azure-node-latest) |
| [Wykonywanie procedury składowanej](https://github.com/Azure/azure-cosmos-js/blob/216672a679ab389e5b341280eeacab1cab3691e4/samples/ServerSideScripts/app.js#L44) |[StoredProcedure.execute](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedure?view=azure-node-latest#execute) |

Aby uzyskać więcej informacji na temat programowania po stronie serwera, zobacz [Azure Cosmos DB server-side programming: Stored procedures, database triggers, and UDF (Programowanie po stronie serwera w usłudze Azure Cosmos DB: procedury składowane, wyzwalacze bazy danych i funkcje UDF)](stored-procedures-triggers-udfs.md).

