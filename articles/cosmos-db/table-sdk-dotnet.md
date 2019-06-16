---
title: Usługa Azure Cosmos DB Table API .NET SDK i zasoby
description: Dowiedz się wszystkiego o usłudze Azure Cosmos DB interfejsu API tabel tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: db7cc556525ab57f14984232bf1797764865fca3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606260"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB tabel interfejsu API platformy .NET: Pobierz i informacje o wersji

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Zestaw SDK do pobrania**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API platformy .NET](https://aka.ms/acdbtableapiref)|
|**Szybki start**|[Azure Cosmos DB: Tworzenie aplikacji przy użyciu platformy .NET i interfejsu API tabel](create-table-dotnet.md)|
|**Samouczek**|[Azure Cosmos DB: Programowanie za pomocą interfejsu Table API na platformie .NET](tutorial-develop-table-dotnet.md)|
|**Bieżącej struktury obsługiwanej**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) jest w trybie konserwacji i tryb wkrótce staną się przestarzałe. Przeprowadź uaktualnienie do nowej biblioteki .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) w dalszym ciągu uzyskać najnowsze funkcje, które są obsługiwane przez interfejs API tabel.

> Jeśli utworzono konto interfejsu API tabel w trakcie okresu próbnego, utwórz [nowe konto interfejsu API tabel](create-table-dotnet.md#create-a-database-account), aby móc korzystać z ogólnie dostępnych zestawów SDK interfejsu API tabel.
>

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Poprawki błędów

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Obsługa dodano zapisu wielu regionów
* Naprawiono zależności pakietów NuGet Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm, Microsoft.Spatial

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3

* Naprawiono zależności pakietów NuGet Microsoft.Azure.Storage.Common i Microsoft.Azure.DocumentDB.
* Po skonfigurowaniu JsonConvert.DefaultSettings Naprawiono błędy serializacji tabeli.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Dodano sprawdzanie poprawności źle sformułowane elementów etag w trybie bezpośredniego.
* Usunięto usterkę zapytania LINQ w trybie bramy.
* Synchronicznych interfejsów API, która jest teraz uruchamiać w puli wątków o SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Dodaj TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism i TableQueryContinuationTokenLimitInKb do TableRequestOptions
* Poprawki błędów

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Wersji ogólnodostępnej

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-Preview

* Początkowa wersja zapoznawcza

## <a name="release-and-retirement-dates"></a>Daty wydania i wycofania

Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** ewentualnej wycofanie zestawu SDK w celu złagodzenia przejścia do nowszych/obsługiwanych wersji.

`Microsoft.Azure.CosmosDB.Table` Biblioteki jest obecnie dostępna dla środowiska .NET Framework, jest w trybie konserwacji i wkrótce staną się przestarzałe. Nowe funkcje i funkcje i optymalizacje tylko są dodawane do biblioteki .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), jak takie zaleca się uaktualnienie do wersji [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

[WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) pakiet (wersja zapoznawcza) jest przestarzała. Zestaw SDK WindowsAzure.Storage-PremiumTable zostaną wycofane w dniu 15 listopada 2018, w tym czasie żądania do wycofanych zestaw SDK nie może. 

Wszystkie żądania do usługi Azure Cosmos DB przy użyciu wycofane zestawu SDK są odrzucane przez usługę.
<br/>

| Version | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [2.1.0](#2.1.0) |22 stycznia 2019 r.|01 kwietnia 2020 r. |
| [2.0.0](#2.0.0) |26 września 2018 r.|01 marca 2020 r. |
| [1.1.3](#1.1.3) |17 lipca 2018 r.|01 grudnia 2019 r. |
| [1.1.1](#1.1.1) |26 marca 2018 r.|01 grudnia 2019 r. |
| [1.1.0](#1.1.0) |21 lutego 2018 r.|01 grudnia 2019 r. |
| [1.0.0](#1.0.0) |15 listopada 2017 r.|15 listopada 2019 r. |
| 0.9.0-Preview |11 listopada 2017 r. |11 listopada 2019 r. |

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony błąd 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

Podczas próby użycia pakietu Microsoft.Azure.CosmosDB.Table NuGet, masz dwie opcje, aby rozwiązać ten problem:

* Użyj konsoli zarządzania pakietów, aby zainstalować pakiet Microsoft.Azure.CosmosDB.Table i jego zależności. Aby to zrobić, wpisz następujące polecenie w konsoli Menedżera pakietów dla Twojego rozwiązania. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Za pomocą preferowanym narzędziem zarządzania pakietu NuGet, zainstaluj pakiet Microsoft.Azure.Storage.Common NuGet przed zainstalowaniem Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także

Aby dowiedzieć się więcej na temat interfejsu API tabeli usługi Azure Cosmos DB, zobacz [wprowadzenie do interfejsu API tabeli usługi Azure Cosmos DB](table-introduction.md). 