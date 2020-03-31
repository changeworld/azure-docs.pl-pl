---
title: Jak skonfigurować czas rozpoczęcia procesora pliku danych zmian — Usługa Azure Cosmos DB
description: Dowiedz się, jak skonfigurować procesor pliku danych zmian, aby rozpocząć czytanie od określonej daty i godziny
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 600556a06d3f58c4d2ec79a49fdee5e8e04d4036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586278"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Jak skonfigurować czas rozpoczęcia procesora pliku danych zmian

W tym artykule opisano, jak skonfigurować [procesor pliku danych zmian,](./change-feed-processor.md) aby rozpocząć odczyt od określonej daty i godziny.

## <a name="default-behavior"></a>Zachowanie domyślne

Gdy procesor pliku danych zmian zostanie uruchomiony po raz pierwszy, zainisalizuje kontener dzierżawy i rozpocznie [jego cykl życia przetwarzania](./change-feed-processor.md#processing-life-cycle). Wszelkie zmiany, które miały miejsce w kontenerze przed rozpoczęciem przetwarzania danych wejściowych zmian po raz pierwszy nie zostaną wykryte.

## <a name="reading-from-a-previous-date-and-time"></a>Odczyt z poprzedniej daty i godziny

Istnieje możliwość zainicjowania procesora pliku danych zmian w celu odczytu zmian rozpoczynających `DateTime` się `WithStartTime` od **określonej daty i godziny,** przekazując wystąpienie a do rozszerzenia konstruktora:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

Procesor pliku danych zmian zostanie zainicjowany dla określonej daty i godziny i rozpocznie odczytywanie zmian, które nastąpiły później.

## <a name="reading-from-the-beginning"></a>Czytanie od początku

W innych scenariuszach, takich jak migracje danych lub analizowanie całej historii kontenera, musimy odczytać źródło danych zmian od **początku okresu istnienia tego kontenera.** Aby to zrobić, `WithStartTime` możemy użyć na rozszerzenie `DateTime.MinValue.ToUniversalTime()`konstruktora, ale przekazywanie `DateTime` , które generują reprezentację UTC wartości minimalnej, jak tak:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

Procesor pliku danych zmian zostanie zainicjowany i rozpocznie odczyt zmian od początku okresu istnienia kontenera.

> [!NOTE]
> Te opcje dostosowywania działają tylko w celu skonfigurowania punktu początkowego w czasie przetwarzania danych. Po zainicjowaniu kontenera dzierżawy po raz pierwszy, zmiana ich nie ma wpływu.

> [!NOTE]
> Podczas określania punktu w czasie, tylko zmiany dla elementów, które obecnie istnieją w kontenerze będą odczytywane. Jeśli element został usunięty, jego historia w kanale informacyjnym zmiany również zostanie usunięta.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Przykłady użycia w usłudze GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Dodatkowe przykłady w usłudze GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Następne kroki

Teraz możesz dowiedzieć się więcej o procesorze pliku danych zmian w następujących artykułach:

* [Omówienie procesora pliku danych zmian](change-feed-processor.md)
* [Korzystanie z estymatora zestawienia zmian](how-to-use-change-feed-estimator.md)
