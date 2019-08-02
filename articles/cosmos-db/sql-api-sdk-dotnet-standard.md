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
ms.openlocfilehash: 0368e99135dd6e377dd2820b3e673c55182319b9
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663810"
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
> * [Wykonawca zbiorczy — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonawczy zbiorczej — Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Zestaw SDK do pobrania**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API platformy .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Przykłady**|[Przykłady kodu platformy .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**Wprowadzenie**|[Wprowadzenie do zestawu SDK platformy Azure Cosmos DB .NET](sql-api-get-started.md)|
|**Samouczek dotyczący aplikacji sieci Web**|[Opracowywanie aplikacji sieci Web za pomocą usługi Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Bieżącej struktury obsługiwanej**|[Microsoft .NET Standard 2,0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>Informacje o wersji
### <a name="a-name310310"></a><a name="3.1.0"/>3.1.0
#### <a name="added"></a>Dodano
- [#541](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/541) Dodano poziom spójności do opcji klienta i zapytania
- [#544](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/544) Dodano obsługę tokenu kontynuacji dla LINQ
- [#557](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/557) Dodano opcje wyzwalacza do opcji żądania elementu
- [#571](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/571) Dodano domyślny serializator JSON.net z ustawieniami opcjonalnymi
- [#572](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/572) Dodano weryfikację klucza partycji na CreateContainerIfNotExistsAsync
- [#581](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/581) Dodawanie składnika LINQ to QueryDefinition API
- [#592](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/592) Dodano CreateIfNotExistsAsync do konstruktora kontenerów
- [#597](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/597) Dodano właściwość tokenu kontynuacji do ResponseMessage
- [#604](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/604) Dodano metodę rozszerzenia LINQ ToStreamIterator

#### <a name="fixed"></a>Ustalone
- [#548](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/548) Stały komunikat z nieprawidłowo określonym typem w CosmosException. ToString ();
- [#558](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/558) Poprawka rywalizacji o LocationCache ConcurrentDict
- [#561](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/561) GetItemLinqQueryable teraz współpracuje z zapytaniem o wartości null
- [#567](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/567) Zapytanie prawidłowo obsługuje różne kultury języka
- [#574](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/574) Rozwiązano pusty komunikat o błędzie, jeśli analiza zapytania nie powiedzie się z nieoczekiwanego wyjątku
- [#576](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/576) Zapytanie prawidłowo serializować dane wejściowe do strumienia

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
| [3.1.0](#3.1.0) |29 lipca 2019 |--- |
| [3.0.0](#3.0.0) |15 lipca 2019 |--- |


## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stronę usługi. 

