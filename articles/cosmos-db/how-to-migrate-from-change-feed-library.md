---
title: Migracja z biblioteki procesorów kanału informacyjnego zmian do sdk usługi Azure Cosmos DB .NET V3
description: Dowiedz się, jak przeprowadzić migrację aplikacji z biblioteki procesorów kanału informacyjnego zmian do dysku SDK V3 usługi Azure Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588887"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migracja z biblioteki procesorów kanału informacyjnego zmian do sdk usługi Azure Cosmos DB .NET V3

W tym artykule opisano wymagane kroki migracji kodu istniejącej aplikacji, który używa [biblioteki procesora kanału informacyjnego zmian](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) do funkcji [źródła danych zmian](change-feed.md) w najnowszej wersji pliku .NET SDK (nazywanego również .NET V3 SDK).

## <a name="required-code-changes"></a>Wymagane zmiany kodu

.NET V3 SDK ma kilka przełomowych zmian, poniżej znajdują się kluczowe kroki migracji aplikacji:

1. Konwertuj `Container` wystąpienia na `DocumentCollectionInfo` odwołania dla kontenerów monitorowanych i dzierżaw.
1. Dostosowania, które `WithProcessorOptions` używają powinny `WithLeaseConfiguration` być `WithPollInterval` aktualizowane `WithStartTime` do użycia i `WithMaxItems` interwałów, dla czasu [rozpoczęcia](how-to-configure-change-feed-start-time.md)i zdefiniować maksymalną liczbę elementów.
1. Ustaw `processorName` włącz, `GetChangeFeedProcessorBuilder` aby dopasować `ChangeFeedProcessorOptions.LeasePrefix`wartość skonfigurowaną na , lub użyć `string.Empty` w inny sposób.
1. Zmiany nie są już `IReadOnlyList<Document>`dostarczane jako , zamiast `IReadOnlyCollection<T>` tego `T` jest to, gdzie jest typ, który należy zdefiniować, nie ma już klasy podstawowego elementu.
1. Aby obsłużyć zmiany, nie trzeba już implementacji, zamiast tego należy [zdefiniować pełnomocnika](change-feed-processor.md#implementing-the-change-feed-processor). Pełnomocnik może być funkcją statyczną lub, jeśli trzeba zachować stan w wykonaniu, można utworzyć własną klasę i przekazać metodę wystąpienia jako delegata.

Na przykład, jeśli oryginalny kod do tworzenia procesora pliku danych zmian wygląda następująco:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Zmigrowany kod będzie wyglądał następująco:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

A delegat, może być metodą statyczną:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Kontener państwowy i dzierżawy

Podobnie jak w bibliotece procesora kanału informacyjnego zmian, funkcja źródła danych zmian w zestawie .NET V3 SDK używa [kontenera dzierżawy](change-feed-processor.md#components-of-the-change-feed-processor) do przechowywania stanu. Jednak schematy są różne.

Procesor kanału informacyjnego SDK V3 wykryje każdy stary stan biblioteki i automatycznie migruje go do nowego schematu przy pierwszym wykonaniu zmigrowanego kodu aplikacji. 

Można bezpiecznie zatrzymać aplikację przy użyciu starego kodu, migracji kodu do nowej wersji, uruchomić zmigrowane aplikacji i wszelkie zmiany, które miały miejsce, gdy aplikacja została zatrzymana, zostaną pobrane i przetworzone przez nową wersję.

> [!NOTE]
> Migracje z aplikacji korzystających z biblioteki do pliku .NET V3 SDK są jednokierunkowe, ponieważ stan (dzierżawy) zostanie przeniesiony do nowego schematu. Migracja nie jest wstecznie zgodna.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Przykłady użycia w usłudze GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Dodatkowe przykłady w usłudze GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Następne kroki

Teraz możesz dowiedzieć się więcej o procesorze pliku danych zmian w następujących artykułach:

* [Omówienie procesora pliku danych zmian](change-feed-processor.md)
* [Korzystanie z estymatora zestawienia zmian](how-to-use-change-feed-estimator.md)
* [Czas rozpoczęcia procesora zestawienia zmian](how-to-configure-change-feed-start-time.md)
