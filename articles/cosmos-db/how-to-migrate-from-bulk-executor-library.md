---
title: Migracja z biblioteki zbiorczej executor do obsługi zbiorczej w usłudze Azure Cosmos DB .NET V3 SDK
description: Dowiedz się, jak przeprowadzić migrację aplikacji z biblioteki zbiorczego executora do obsługi zbiorczej w witrynie Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: maquaran
ms.openlocfilehash: e1a2a5d849d3c94d62b8645c41f288ba130aa6a4
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479333"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migracja z biblioteki zbiorczej executor do obsługi zbiorczej w usłudze Azure Cosmos DB .NET V3 SDK

W tym artykule opisano wymagane kroki migracji kodu istniejącej aplikacji, który używa [biblioteki executora zbiorczego platformy .NET](bulk-executor-dot-net.md) do funkcji [obsługi zbiorczej](tutorial-sql-api-dotnet-bulk-import.md) w najnowszej wersji pliku .NET SDK.

## <a name="enable-bulk-support"></a>Włącz obsługę zbiorczą

Włącz obsługę zbiorczą `CosmosClient` wystąpienia za pomocą konfiguracji [AllowBulkExecution:](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution)

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Tworzenie zadań dla każdej operacji

Obsługa zbiorcza w programie .NET SDK działa przy wykorzystaniu [biblioteki równoległej zadań](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) i grupowaniu operacji, które występują jednocześnie. 

Nie ma jednej metody, która zajmie listę dokumentów lub operacji jako parametr wejściowy, ale raczej należy utworzyć zadanie dla każdej operacji, które mają być wykonywane zbiorczo.

Na przykład jeśli początkowe dane wejściowe są listą elementów, w których każdy element ma następujący schemat:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Jeśli chcesz zrobić import zbiorczy (podobnie jak przy użyciu BulkExecutor.BulkImportAsync), `CreateItemAsync` musisz mieć równoczesnych wywołań z każdej wartości elementu. Przykład:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Jeśli chcesz zrobić *aktualizację* zbiorczą (podobnie jak przy użyciu [BulkExecutor.BulkUpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)), musisz mieć równoczesnych wywołań metody `ReplaceItemAsync` po zaktualizowaniu wartości elementu. Przykład:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

A jeśli chcesz zrobić zbiorcze *usuwanie* (podobnie jak przy użyciu [BulkExecutor.BulkDeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)), musisz mieć równoczesnych wywołań do `DeleteItemAsync`, z `id` kluczem partycji i każdego elementu. Przykład:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Przechwytywanie stanu wyniku zadania

W poprzednich przykładach kodu utworzono równoczesną listę `CaptureOperationResponse` zadań i wywołano metodę dla każdego z tych zadań. Ta metoda jest rozszerzeniem, które pozwala nam utrzymać *podobny schemat odpowiedzi* jak BulkExecutor, przechwytując wszelkie błędy i śledząc użycie [jednostek żądania.](request-units.md)

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

W `OperationResponse` przypadku gdy jest zadeklarowany jako:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Wykonywanie operacji jednocześnie

Po zdefiniowaniu listy zadań poczekaj, aż wszystkie zostaną ukończone. Można śledzić ukończenie zadań, definiując zakres operacji zbiorczej, jak pokazano w poniższym fragmentie kodu:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Przechwytywanie statystyk

Poprzedni kod czeka, aż wszystkie operacje zostaną zakończone i oblicza wymagane statystyki. Statystyki te są podobne do biblioteki zbiorczego executora [BulkImportResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

Zawiera: `BulkOperationResponse`

1. Całkowity czas przetwarzania listy operacji za pośrednictwem obsługi zbiorczej.
1. Liczba udanych operacji.
1. Łączna liczba zużytych jednostek żądań.
1. Jeśli występują błędy, wyświetla listę krotek, które zawierają wyjątek i skojarzony element do celów rejestrowania i identyfikacji.

## <a name="performance-improvements"></a>Ulepszenia wydajności

Podobnie jak w przypadku innych operacji z .NET SDK, przy użyciu interfejsów API strumienia powoduje lepszą wydajność i pozwala uniknąć niepotrzebnej serializacji. 

Korzystanie z interfejsów API strumienia jest możliwe tylko wtedy, gdy charakter danych, których używasz, odpowiada charakterowi strumienia bajtów (na przykład strumieni plików). W takich przypadkach `CreateItemStreamAsync`użycie `ReplaceItemStreamAsync`, `DeleteItemStreamAsync` lub metody `ResponseMessage` i pracy `ItemResponse`z (zamiast) zwiększa przepustowość, które można osiągnąć.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o wersjach zestawu SDK platformy .NET, zobacz artykuł [zestaw SDK usługi Azure Cosmos DB.](sql-api-sdk-dotnet.md)
* Pobierz pełny [kod źródłowy migracji](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) z gitHub.
* [Dodatkowe próbki zbiorcze w usłudze GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
