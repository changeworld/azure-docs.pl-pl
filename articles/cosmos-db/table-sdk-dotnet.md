---
title: Zasoby & tabeli platformy Azure CosmosDB interfejsu API zestawu SDK programu .NET | Dokumentacja firmy Microsoft
description: Dowiedz się wszystkiego o rozwiązania Cosmos DB tabeli interfejsu API Azure tym daty wydania, daty wycofania i zmiany wprowadzone od każdej wersji.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/26/2018
ms.author: rnagpal
ms.openlocfilehash: 7e012e07b8f93554ea44404c611a7bc0eb64a0d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure rozwiązania Cosmos interfejs API .NET tabeli bazy danych: Pobierz i informacje o wersji
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Pobierz zestaw SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Dokumentacja interfejsu API**|[Dokumentacji interfejsu API platformy .NET](https://aka.ms/acdbtableapiref)|
|**Szybki start**|[Azure rozwiązania Cosmos bazy danych: Tworzenie aplikacji .NET i tabelę interfejsu API](create-table-dotnet.md)|
|**Samouczek**|[Azure rozwiązania Cosmos bazy danych: Tworzenie tabeli interfejsu API programu .NET](tutorial-develop-table-dotnet.md)|
|**Bieżąca platforma obsługiwane**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Jeśli utworzono konto interfejsu API tabel w trakcie okresu próbnego, utwórz [nowe konto interfejsu API tabel](create-table-dotnet.md#create-a-database-account), aby móc korzystać z ogólnie dostępnych zestawów SDK interfejsu API tabel.
>

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Dodano weryfikację źle sformułowane elementy etag w trybie bezpośredniego.
* Usunięte usterki zapytań LINQ w trybie bramy.
* Interfejsy API synchroniczne teraz uruchomić w puli wątków z obiektu SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Dodaj TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism i TableQueryContinuationTokenLimitInKb do TableRequestOptions
* Poprawki błędów

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Ogólnie zlecenia

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-Preview
* Początkowa wersja zapoznawcza

## <a name="release-and-retirement-dates"></a>Wersja i wycofania dat
Firma Microsoft udostępnia powiadomienia co najmniej **12 miesięcy** klienta z wyprzedzeniem wycofanie SDK w celu złagodzenia przejścia do nowszej/nieobsługiwaną wersję.

[WindowsAzure.Storage PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) pakietu w wersji zapoznawczej został przestarzałe i zastępowane [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) pakietu. Zestaw SDK WindowsAzure.Storage PremiumTable zostaną wycofane na 15 listopada 2018, w tym czasie żądania do wycofane zestawu SDK nie otrzyma zezwolenia.

Nowe funkcje i funkcjonalność i optymalizację, które są dodawane tylko do bieżącego zestawu SDK, w związku jest zalecane, zawsze Uaktualnij zestaw SDK najnowszą tak szybko jak to możliwe. 

Wszystkie żądania dotyczące bazy danych rozwiązania Cosmos Azure przy użyciu wycofane zestawu SDK są odrzucane przez usługę.
<br/>

| Wersja | Data wydania | Dacie wycofania |
| --- | --- | --- |
| [1.1.1](#1.1.1) |26 marca 2018|--- |
| [1.1.0](#1.1.0) |21 lutego 2018 r.|--- |
| [1.0.0](#1.0.0) |15 listopada 2017 r.|--- |
| [0.9.0-Preview](#0.9.0-preview) |11 listopada 2017 r. |--- |

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli komunikat o błędzie 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

Podczas próby użycia pakietu Microsoft.Azure.CosmosDB.Table NuGet, masz dwie opcje do rozwiązania problemu:

* Użyj konsoli Zarządzanie pakietu do zainstalowania pakietu Microsoft.Azure.CosmosDB.Table i jego zależności. Aby to zrobić, wpisz następujące polecenie w konsoli Menedżera pakietów do rozwiązania. 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* Używając preferowanego narzędzia zarządzania pakietu Nuget, zainstaluj pakiet Microsoft.Azure.Storage.Common Nuget przed zainstalowaniem Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat interfejsu API Azure rozwiązania Cosmos DB tabeli, zobacz [wprowadzenie do interfejsu API Azure rozwiązania Cosmos DB tabeli](table-introduction.md). 
