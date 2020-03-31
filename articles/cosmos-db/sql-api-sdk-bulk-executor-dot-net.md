---
title: 'Usługa Azure Cosmos DB: zbiorczy executor .NET API, zestaw SDK & zasoby'
description: Dowiedz się wszystkiego o zbiorczym executerze .NET API i SDK, w tym datach wydania, datach wycofania i zmianach wprowadzonych między każdą wersją zbiorczego executora usługi Azure Cosmos DB .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169397"
---
# <a name="net-bulk-executor-library-download-information"></a>Biblioteka executora zbiorczego .NET: Pobierz informacje 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Plik danych o zmianach w sieci .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Reszta](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Wykonawca luzem - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Opis**| Biblioteka executorów zbiorczych .Net umożliwia aplikacjom klienckim wykonywanie operacji zbiorczych na kontach usługi Azure Cosmos DB. Ta biblioteka zawiera bulkimport, BulkUpdate i BulkDelete przestrzeni nazw. BulkImport moduł można zbiorczo pozyskiwania dokumentów w sposób zoptymalizowany, tak, że przepływność aprowizowana dla kolekcji jest zużywana w maksymalnym zakresie. Moduł BulkUpdate można zbiorczo aktualizować istniejące dane w kontenerach usługi Azure Cosmos jako poprawki. BulkDelete moduł można zbiorczo usunąć dokumenty w sposób zoptymalizowany, tak, że przepływność aprowizowana dla kolekcji jest zużywana w maksymalnym stopniu.|
|**Pobieranie SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Biblioteka wykonawców zbiorczych w usłudze GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Dokumentacja interfejsu API**|[Dokumentacja referencyjna interfejsu API platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Wprowadzenie**|[Wprowadzenie do biblioteki zbiorczej biblioteki wykonawczej .NET SDK](bulk-executor-dot-net.md)|
| **Obecne obsługiwane ramy**| Microsoft .NET Framework 4.5.2, 4.6.1 i .NET Standard 2.0 |

> [!NOTE]
> Jeśli używasz executora zbiorczego, zobacz najnowszą wersję 3.x [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md), który ma wbudowany wykonawca zbiorczy w SDK. 

## <a name="release-notes"></a>Informacje o wersji

### <a name="241-preview"></a><a name="2.4.1-preview"/>2.4.1-podgląd

* Naprawiono TotalElapsedTime w odpowiedzi BulkDelete poprawnie zmierzyć całkowity czas, w tym wszelkie ponownych prób.

### <a name="240-preview"></a><a name="2.4.0-preview"/>2.4.0-podgląd

* Zmieniono zależność SDK na >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-podgląd2

* Dodano obsługę wykonywania zbiorczego wykresu, aby zaakceptować ttl na wierzchołkach i krawędziach

### <a name="220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-podgląd2

* Rozwiązaliśmy problem, który powodował wyjątki podczas skalowania elastycznego usługi Azure Cosmos DB podczas uruchamiania w trybie bramy. Ta poprawka sprawia, że funkcjonalnie odpowiada wersji 1.4.1.

### <a name="210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-podgląd2

* Dodano obsługę BulkDelete dla kont interfejsu API SQL, aby zaakceptować klucz partycji, krotki identyfikatora dokumentu do usunięcia. Ta zmiana sprawia, że funkcjonalnie odpowiada wersji 1.4.0.

### <a name="200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-podgląd2

* W tym MongoBulkExecutor do obsługi .NET Standard 2.0. Ta funkcja sprawia, że funkcjonalnie odpowiednik wersji 1.3.0, z dodatkiem obsługi .NET Standard 2.0 jako platformy docelowej.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-podgląd

* Dodano .NET Standard 2.0 jako jedną z obsługiwanych platform docelowych, aby biblioteka zbiorczego executora działała z aplikacjami .NET Core.

### <a name="188"></a><a name="1.8.8"/>1.8.8

* Naprawiono błąd na MongoBulkExecutor, który nieoczekiwanie zwiększał rozmiar dokumentu, dodając dopełnienie, a w niektórych przypadkach przekraczając maksymalny limit rozmiaru dokumentu.

### <a name="187"></a><a name="1.8.7"/>1.8.7

* Rozwiązano problem z BulkDeleteAsync, gdy kolekcja ma zagnieżdżone ścieżki klucza partycji.

### <a name="186"></a><a name="1.8.6"/>1.8.6

* MongoBulkExecutor teraz implementuje IDisposable i oczekuje się, że zostaną usunięte po użyciu.

### <a name="185"></a><a name="1.8.5"/>1.8.5

* Usunięto blokadę w wersji SDK. Pakiet jest teraz zależny od zestawu SDK >= 2.5.1.

### <a name="184"></a><a name="1.8.4"/>1.8.4

* Poprawiono obsługę identyfikatorów podczas wywoływania BulkImport z listą obiektów POCO z wartościami liczbowymi.

### <a name="183"></a><a name="1.8.3"/>1.8.3

* Naprawiono TotalElapsedTime w odpowiedzi BulkDelete poprawnie zmierzyć całkowity czas, w tym wszelkie ponownych prób.

### <a name="182"></a><a name="1.8.2"/>1.8.2

* Poprawiono wysokie zużycie procesora CPU w niektórych scenariuszach.
* Śledzenie teraz używa TraceSource. Użytkownicy mogą definiować odbiorniki `BulkExecutorTrace` dla źródła.
* Naprawiono rzadki scenariusz, który mógł powodować blokadę podczas wysyłania dokumentów o rozmiarze zbliżonym do 2 Mb.

### <a name="160"></a><a name="1.6.0"/>1.6.0

* Zaktualizowano zbiorczy wykonawca, aby teraz używał najnowszej wersji narzędzia Azure Cosmos DB .NET SDK (2.4.0)

### <a name="150"></a><a name="1.5.0"/>1.5.0

* Dodano obsługę wykonywania zbiorczego wykresu, aby zaakceptować ttl na wierzchołkach i krawędziach

### <a name="141"></a><a name="1.4.1"/>1.4.1

* Rozwiązaliśmy problem, który powodował wyjątki podczas skalowania elastycznego usługi Azure Cosmos DB podczas uruchamiania w trybie bramy.

### <a name="140"></a><a name="1.4.0"/>1.4.0

* Dodano obsługę BulkDelete dla kont interfejsu API SQL, aby zaakceptować klucz partycji, krotki identyfikatora dokumentu do usunięcia.

### <a name="130"></a><a name="1.3.0"/>1.3.0

* Naprawiono błąd, który powodował problem z formatowaniem w agencie użytkownika używanym przez wykonawcę zbiorczego.

### <a name="120"></a><a name="1.2.0"/>1.2.0

* Wprowadzono ulepszenia do zbiorczego importu executor i zaktualizować interfejsy API w sposób przejrzysty dostosować do elastycznego skalowania kontenera usługi Cosmos, gdy magazyn przekracza bieżącą pojemność bez zgłaszania wyjątków.

### <a name="112"></a><a name="1.1.2"/>1.1.2

* Natknął się na zależność SDK .NET documentdb do wersji 2.1.3.

### <a name="111"></a><a name="1.1.1"/>1.1.1

* Naprawiono błąd, który powodował, że wykonawca zbiorczy rzucał błąd JSRT podczas importowania do stałych kolekcji.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* Dodano obsługę operacji BulkDelete dla kont interfejsu API SQL usługi Azure Cosmos DB.
* Dodano obsługę operacji BulkImport dla kont z interfejsem API usługi Azure Cosmos DB dla mongodb.
* Natknął się na zależność SDK .NET documentdb do wersji 2.0.0. 

### <a name="102"></a><a name="1.0.2"/>1.0.2

* Dodano obsługę operacji BulkImport dla kont interfejsu API usługi Azure Cosmos DB Gremlin.

### <a name="101"></a><a name="1.0.1"/>1.0.1

* Drobne poprawki błędów do operacji BulkImport dla kont interfejsu API SQL usługi Azure Cosmos DB.

### <a name="100"></a><a name="1.0.0"/>1.0.0

* Dodano obsługę operacji BulkImport i BulkUpdate dla kont interfejsu API SQL usługi Azure Cosmos DB.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zbiorczej bibliotece java wykonawcy, zobacz następujący artykuł:

[Biblioteka SDK i informacje o wersji zbiorczej biblioteki java](sql-api-sdk-bulk-executor-java.md)
