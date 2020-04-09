---
title: Zasoby & interfejsu SDK & tabeli usługi Azure Cosmos DB
description: Dowiedz się wszystkiego o interfejsie API tabeli usługi Azure Cosmos DB, w tym datach wydania, datach wycofania i zmianach wprowadzonych między każdą wersją.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: 9356ab0aec375a046a8f200838df296b2cf74bce
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984984"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Interfejs API .NET tabeli usługi Azure Cosmos DB: informacje o pobieraniu i wydawaniu

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Pobieranie SDK**|[Nuget](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)|
|**Szybki start**|[Usługa Azure Cosmos DB: Tworzenie aplikacji za pomocą platformy .NET i interfejsu API tabeli](create-table-dotnet.md)|
|**Samouczek**|[Azure Cosmos DB: programowanie za pomocą interfejsu Table API na platformie .NET](tutorial-develop-table-dotnet.md)|
|**Obecne obsługiwane ramy**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) jest w trybie konserwacji i wkrótce zostanie przestarzały. Uaktualnij do nowej biblioteki .NET Standard [Microsoft.Azure.Cosmos.Table,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) aby kontynuować dostęp do najnowszych funkcji obsługiwanych przez interfejs API tabeli.

> Jeśli utworzono konto interfejsu API tabel w trakcie okresu próbnego, utwórz [nowe konto interfejsu API tabel](create-table-dotnet.md#create-a-database-account), aby móc korzystać z ogólnie dostępnych zestawów SDK interfejsu API tabel.
>

## <a name="release-notes"></a>Informacje o wersji

### <a name="212"></a><a name="2.1.2"/>2.1.2

* Poprawki błędów

### <a name="210"></a><a name="2.1.0"/>2.1.0

* Poprawki błędów

### <a name="200"></a><a name="2.0.0"/>2.0.0

* Dodano obsługę zapisu w wielu regionach
* Poprawiono zależności pakietów NuGet w witrynie Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm, Microsoft.SpatialDB

### <a name="113"></a><a name="1.1.3"/>1.1.3

* Poprawiono zależności pakietów NuGet w witrynach Microsoft.Azure.Storage.Common i Microsoft.Azure.DocumentDB.
* Poprawki błędów w serializacji tabeli, gdy JsonConvert.DefaultSettings są skonfigurowane.

### <a name="111"></a><a name="1.1.1"/>1.1.1

* Dodano walidację nieprawidłowo sformułowanych EMG w trybie bezpośrednim.
* Naprawiono błąd zapytania LINQ w trybie bramy.
* Synchroniczne interfejsy API są teraz uruchamiane w puli wątków z SynchronizationContext.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* Dodaj TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism i TableQueryContinuationTokenLimitInKb do TableRequestOptions
* Poprawki błędów

### <a name="100"></a><a name="1.0.0"/>1.0.0

* Ogólna wersja dostępności

### <a name="090-preview"></a><a name="0.1.0-preview"/>0.9.0-podgląd

* Początkowa wersja zapoznawcza

## <a name="release-and-retirement-dates"></a>Daty wydania i przejścia na emeryturę

Firma Microsoft powiadamia o wycofaniu sdk sdk z co najmniej **12-miesięcznym** wyprzedzeniem w celu usprawnienia przejścia do nowszej/obsługiwanej wersji.

Biblioteka `Microsoft.Azure.CosmosDB.Table` jest obecnie dostępna tylko dla platformy .NET Framework i jest w trybie konserwacji i wkrótce zostanie przestarzała. Nowe funkcje i funkcje oraz optymalizacje są dodawane tylko do biblioteki .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), w związku z czym zaleca się uaktualnienie do usługi [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

Pakiet [windowsazure.storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) w wersji zapoznawczej został przestarzały. WindowsAzure.Storage-PremiumTable SDK zostanie wycofany 15 listopada 2018 r., w którym to czasie żądania do wycofanego SDK nie będą dozwolone. 

Wszelkie żądania do usługi Azure Cosmos DB przy użyciu wycofanego zestawu SDK są odrzucane przez usługę.
<br/>

| Wersja | Data wydania | Data przejścia na emeryturę |
| --- | --- | --- |
| [2.1.2](#2.1.2) |16 września 2019 r.| |
| [2.1.0](#2.1.0) |22 stycznia 2019 r.|1 kwietnia 2020 r. |
| [2.0.0](#2.0.0) |26 września 2018 r.|1 marca 2020 r. |
| [1.1.3](#1.1.3) |17 lipca 2018 r.|1 grudnia 2019 r. |
| [1.1.1](#1.1.1) |26 marca 2018 r.|1 grudnia 2019 r. |
| [1.1.0](#1.1.0) |21 lutego 2018 r.|1 grudnia 2019 r. |
| [1.0.0](#1.0.0) |15 listopada 2017 r.|15 listopada 2019 r. |
| 0.9.0-podgląd |11 listopada 2017 r. |11 listopada 2019 r. |

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony błąd 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

podczas próby użycia pakietu Microsoft.Azure.CosmosDB.Table NuGet, masz dwie opcje, aby rozwiązać problem:

* Użyj konsoli zarządzanie pakietami, aby zainstalować pakiet Microsoft.Azure.CosmosDB.Table i jego zależności. Aby to zrobić, wpisz następujące elementy w konsoli Menedżera pakietów dla rozwiązania. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Korzystając z preferowanego narzędzia do zarządzania pakietami NuGet, zainstaluj pakiet Microsoft.Azure.Storage.Common NuGet przed zainstalowaniem witryny Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też

Aby dowiedzieć się więcej o interfejsie API tabeli usługi Azure Cosmos DB, zobacz [Wprowadzenie do interfejsu API tabeli usługi Azure Cosmos DB](table-introduction.md). 
