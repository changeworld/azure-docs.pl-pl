---
title: Usługa Azure Cosmos DB SQL .NET Standard API, zasoby & SDK
description: Poznaj wszystkie informacje o interfejsie API SQL i zestawie .NET SDK, w tym daty wydania, daty wycofania i zmiany wprowadzone między poszczególnymi wersjami Azure Cosmos DB .NET SDK.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 07/12/2019
ms.author: dech
ms.openlocfilehash: 01645e66907e01b2e8fcb771976b50e5bbc8abf4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229026"
---
# <a name="azure-cosmos-db-net-standard-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET Standard SDK dla interfejsu API SQL: Informacje dotyczące pobierania i zwalniania
> [!div class="op_single_selector"]
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
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
> * [Bulkexecutor — platforma .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkexecutor — platforma Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Zestaw SDK do pobrania**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API platformy .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Przykłady**|[Przykłady kodu platformy .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**Wprowadzenie**|[Wprowadzenie do zestawu SDK platformy Azure Cosmos DB .NET](sql-api-get-started.md)|
|**Samouczek dotyczący aplikacji sieci Web**|[Opracowywanie aplikacji sieci Web za pomocą usługi Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Bieżącej struktury obsługiwanej**|[Microsoft .NET Standard 2,0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0 
* Ogólna dostępność [wersji 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) zestawu .NET SDK
* Targets .NET Standard 2,0, który obsługuje program .NET Framework 4.6.1 + i .NET Core 2.0 +
* Nowy model obiektów z CosmosClient i metodami najwyższego poziomu, które dzielą się na odpowiednie klasy baz danych i kontenerów
* Nowe wysoce wydajne interfejsy API przesyłania strumieniowego
* Wbudowana obsługa interfejsów API procesora źródła zmian
* Interfejsy API programu Fluent Builder dla CosmosClient, kontenera i procesora kanału informacyjnego
* Interfejsy API zarządzania przepływem idiomatyczne
* Szczegółowe RequestOptions i ResponseTypes dla żądań bazy danych, kontenerów, elementów, zapytań i przepływności
* Możliwość skalowania kontenerów bez partycjonowania 
* Rozszerzalny i dostosowywalny serializator
* Rozszerzalny potok żądań z obsługą niestandardowych programów obsługi


## <a name="release--retirement-dates"></a>Data wycofania &
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** przed WYCOFANIEM zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji.

Nowe funkcje i funkcje i optymalizacje są dodawane tylko do bieżącego zestawu SDK, dlatego zaleca się, aby zawsze uaktualnić do najnowszej wersji zestawu SDK tak szybko, jak to możliwe. 

Wszystkie żądania do Azure Cosmos DB za pomocą wycofanego zestawu SDK są odrzucane przez usługę.

<br/>

| Version | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [3.0.0](#3.0.0) |15 lipca 2019 |--- |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stronę usługi. 

