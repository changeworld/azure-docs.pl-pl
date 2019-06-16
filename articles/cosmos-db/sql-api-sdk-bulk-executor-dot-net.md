---
title: Usługa Azure Cosmos DB Zbiorcze interfejsu API platformy .NET wykonawcy, zestaw SDK i zasoby
description: Poznaj interfejs API .NET wykonawca zbiorcze i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami usługi Azure Cosmos DB zbiorcze wykonawca .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: 74eddadd7fd967daa1eebb9d7cb223fdc708025f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66471425"
---
# <a name="net-bulk-executor-library-download-information"></a>Biblioteka przetwarzania zbiorczego .NET: Pobieranie informacji 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Kanał informacyjny zmian .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Zbiorcze wykonawcy — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Zbiorcze wykonawcy — Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Opis**| Biblioteka przetwarzania zbiorczego pozwala aplikacji klienckich wykonywały operacje zbiorcze na kontach usługi Azure Cosmos DB. Biblioteka przetwarzania zbiorczego zawiera przestrzenie nazw elementów BulkImport BulkUpdate i usuwania zbiorczego. Elementów BulkImport, który zbiorczo moduł pozyskiwania dokumenty w sposób zoptymalizowany taki sposób, że przepływnością aprowizowaną dla kolekcji jest używane do jego najszerszym. BulkUpdate, który zbiorczo modułu zaktualizować istniejące dane w kontenerach usługi Azure Cosmos DB jako poprawki. Moduł usuwania zbiorczego zbiorczo delete dokumenty w sposób zoptymalizowany taki sposób, że przepływnością aprowizowaną dla kolekcji jest używane do jego dozwolonym zakresie.|
|**Zestaw SDK do pobrania**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Biblioteka BulkExecutor w usłudze GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Wprowadzenie**|[Wprowadzenie do przetwarzania zbiorczego biblioteki zestawu SDK platformy .NET](bulk-executor-dot-net.md)|
| **Bieżącej struktury obsługiwanej**| Microsoft .NET Framework 4.5.2, 4.6.1 i .NET Standard 2.0 |

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* Dodanie obsługi dla wykonawcy zbiorcze wykresu do akceptowania ttl dla wierzchołków i krawędzi

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* Rozwiązano problem, który spowodował wyjątki podczas elastyczne skalowanie usługi Azure Cosmos DB, podczas pracy w trybie bramy. Ta poprawka sprawia, że funkcjonalnym odpowiednikiem 1.4.1 wydania.

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* Usuwania zbiorczego dodano obsługę interfejsu API SQL kont do akceptowania krotek identyfikatora dokumentu, można usunąć klucza partycji. Ta zmiana sprawia, że funkcjonalnym odpowiednikiem 1.4.0 wydania.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* W tym MongoBulkExecutor do działu pomocy technicznej .NET Standard 2.0. Ta funkcja sprawia, że funkcjonalnym odpowiednikiem 1.3.0 wersji z dodatkiem obsługi .NET Standard 2.0 jako platformę docelową.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* Dodano .NET Standard 2.0 jako jednej platformy docelowej, aby biblioteka BulkExecutor pracy z aplikacjami .NET Core.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Zaktualizowano wykonawca zbiorczo, można teraz używać najnowszej wersji usługi Azure Cosmos DB .NET SDK (2.4.0)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0

* Dodanie obsługi dla wykonawcy zbiorcze wykresu do akceptowania ttl dla wierzchołków i krawędzi

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

* Rozwiązano problem, który spowodował wyjątki podczas elastyczne skalowanie usługi Azure Cosmos DB, podczas pracy w trybie bramy.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

* Usuwania zbiorczego dodano obsługę interfejsu API SQL kont do akceptowania krotek identyfikatora dokumentu, można usunąć klucza partycji.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* Rozwiązano problem, który spowodował problem formatowania w agencie użytkownika używane przez BulkExecutor.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Wprowadzone ulepszenia BulkExecutor importowania i aktualizowania interfejsów API, aby niewidocznie dostosowania do elastyczne skalowanie kontenera usługi Cosmos DB, gdy magazyn przekracza bieżąca pojemność bez zgłaszanie wyjątków.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Zaktualizowany się do wersji 2.1.3 zależności zestawu SDK .NET usługi DocumentDB.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Rozwiązano problem, który spowodował BulkExecutor zgłosić błąd JSRT podczas importowania danych do stałej kolekcji.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Dodano obsługę operacji usuwania zbiorczego dla konta interfejsu API SQL usługi Azure Cosmos DB.
* Dodano obsługę operacji elementów BulkImport dla kont z interfejsem API usługi Azure Cosmos DB dla bazy danych MongoDB.
* Zaktualizowany się zależności zestawu SDK .NET usługi DocumentDB w wersji 2.0.0. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Dodano obsługę operacji elementów BulkImport dla kont usługi Azure Cosmos DB — interfejs API Gremlin.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Drobne poprawki błędu operacji elementów BulkImport dla kont usługi interfejsu API SQL usługi Azure Cosmos DB.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Dodano obsługę dla elementów BulkImport i BulkUpdate operacji konta interfejsu API SQL usługi Azure Cosmos DB.

## <a name="next-steps"></a>Kolejne kroki

Informacje na temat biblioteki zbiorcze wykonawca Java, zobacz następujący artykuł:

[Biblioteka przetwarzania zbiorczego Java SDK oraz informacje o wydaniu](sql-api-sdk-bulk-executor-java.md)
