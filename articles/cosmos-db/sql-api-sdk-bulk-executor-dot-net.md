---
title: 'Azure Cosmos DB: Interfejs API .NET przetwarzania zbiorczego zestawu SDK i zasoby | Dokumentacja firmy Microsoft'
description: Poznaj interfejs API .NET wykonawca zbiorcze i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami usługi Azure Cosmos DB zbiorcze wykonawca .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: ae9560296e37ff5492c07e69e6ba0eb5539915c8
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308499"
---
# <a name="net-bulk-executor-library-download-information"></a>Biblioteka przetwarzania zbiorczego .NET: pobieranie informacji 

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
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [Zbiorcze wykonawcy — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Zbiorcze wykonawcy — Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Opis**</td><td>Biblioteka przetwarzania zbiorczego pozwala aplikacji klienckich wykonywały operacje zbiorcze na kontach usługi Azure Cosmos DB. Biblioteka przetwarzania zbiorczego zawiera przestrzenie nazw elementów BulkImport BulkUpdate i usuwania zbiorczego. Elementów BulkImport, który zbiorczo moduł pozyskiwania dokumenty w sposób zoptymalizowany taki sposób, że przepływnością aprowizowaną dla kolekcji jest używane do jego najszerszym. BulkUpdate, który zbiorczo modułu zaktualizować istniejące dane w kontenerach usługi Azure Cosmos DB jako poprawki. Moduł usuwania zbiorczego zbiorczo delete dokumenty w sposób zoptymalizowany taki sposób, że przepływnością aprowizowaną dla kolekcji jest używane do jego dozwolonym zakresie.</td></tr>

<tr><td>**Zestaw SDK do pobrania**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**Biblioteka BulkExecutor w usłudze GitHub**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**Dokumentacja interfejsu API**</td><td>[Dokumentacja interfejsu API platformy .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**Wprowadzenie**</td><td>[Wprowadzenie do przetwarzania zbiorczego biblioteki zestawu SDK platformy .NET](bulk-executor-dot-net.md)</td></tr>

<tr><td>**Bieżącej struktury obsługiwanej**</td><td>Microsoft .NET Framework 4.5.2, 4.6.1 i .NET Standard 2.0 </td></tr>
</table></br>

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* W tym MongoBulkExecutor do działu pomocy technicznej .NET Standard 2.0. Ta funkcja sprawia, że funkcjonalnym odpowiednikiem 1.3.0 wersji z dodatkiem obsługi .NET Standard 2.0 jako platformę docelową.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* Dodano .NET Standard 2.0 jako jednej platformy docelowej, aby biblioteka BulkExecutor pracy z aplikacjami .NET Core.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* Dodano przeciążenia dla operacji usuwania zbiorczego dla konta interfejsu API SQL do akceptowania krotek identyfikatora dokumentu, można usunąć klucza partycji.
* Dodano przeciążenia dla operacji usuwania zbiorczego dla konta interfejsu API SQL do akceptowania RequestOptions zawierającej klucz partycji, określając wartość klucza partycji, tylko jako filtru w zapytaniu danych wejściowych, określania dokumentów do usunięcia.
* Rozwiązano problem, który spowodował problem formatowania w agencie użytkownika używane przez BulkExecutor.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Wprowadzone ulepszenia BulkExecutor importowania i aktualizowania interfejsów API, aby niewidocznie dostosowania do elastyczne skalowanie kontenera usługi Cosmos DB, gdy magazyn przekracza bieżąca pojemność bez zgłaszanie wyjątków.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Zaktualizowany się do wersji 2.1.3 zależności zestawu SDK .NET usługi DocumentDB.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Rozwiązano problem, który spowodował BulkExecutor zgłosić błąd JSRT podczas importowania danych do stałej kolekcji.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Dodano obsługę operacji usuwania zbiorczego dla konta interfejsu API SQL usługi Azure Cosmos DB.
* Dodano obsługę operacji elementów BulkImport dla kont z interfejsem API MongoDB usługi Azure Cosmos DB.
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
