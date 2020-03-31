---
title: Użyj estymatora kanału informacyjnego zmian — Usługa Azure Cosmos DB
description: Dowiedz się, jak za pomocą estymatora pliku danych zmian analizować postęp procesora pliku danych zmian
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: maquaran
ms.openlocfilehash: 0023f68400b36b9abd3b9d4a789895e79f67aa03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585292"
---
# <a name="use-the-change-feed-estimator"></a>Użyj estymatora źródła danych zmian

W tym artykule opisano, jak można monitorować postęp wystąpień [procesora pliku danych o zmianach](./change-feed-processor.md) podczas czytania pliku danych o zmianach.

## <a name="why-is-monitoring-progress-important"></a>Dlaczego monitorowanie postępów jest ważne?

Procesor pliku danych zmian działa jako wskaźnik, który przesuwa się do przodu w [pliku danych o zmianach](./change-feed.md) i dostarcza zmiany do implementacji delegata. 

Wdrożenie procesora pliku danych zmian może przetwarzać zmiany w określonym tempie na podstawie dostępnych zasobów, takich jak procesor, pamięć, sieć i tak dalej.

Jeśli ta szybkość jest wolniejsza niż szybkość, z jaką zmiany się w kontenerze usługi Azure Cosmos, procesor zacznie pozostawać w tyle.

Zidentyfikowanie tego scenariusza pomaga zrozumieć, czy musimy skalować nasze wdrożenie procesora pliku danych zmian.

## <a name="implement-the-change-feed-estimator"></a>Wdrożenie estymatora źródła danych zmian

Podobnie jak [procesor podawania zmian,](./change-feed-processor.md)estymator kanału zmian działa jako model wypychania. Estymator zmierzy różnicę między ostatnim przetworzonym elementem (zdefiniowanym przez stan kontenera dzierżawy) a ostatnią zmianą w kontenerze i wypchnie tę wartość do pełnomocnika. Interwał, w którym pomiar jest pobierany, można również dostosować z domyślną wartością 5 sekund.

Na przykład, jeśli procesor pliku danych zmian jest zdefiniowany w następujący sposób:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

Prawidłowy sposób inicjowania estymatora do `GetChangeFeedEstimatorBuilder` pomiaru, że procesor będzie używał tak:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

W przypadku gdy zarówno procesor, jak `leaseContainer` i estymator mają tę samą nazwę.

Pozostałe dwa parametry to delegat, który otrzyma liczbę reprezentującą **liczbę oczekujących na odczyt zmian** przez procesor i przedział czasu, w którym ma zostać wykonany ten pomiar.

Przykładem delegata, który odbiera oszacowanie jest:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Możesz wysłać tę oszacowanie do rozwiązania monitorowania i użyć go, aby zrozumieć, jak postępy zachowują się w czasie.

> [!NOTE]
> Estymator kanału informacyjnego zmian nie musi być wdrażany jako część procesora pliku danych zmian ani być częścią tego samego projektu. Może być niezależny i działać w zupełnie innym wystąpieniu. Po prostu musi używać tej samej nazwy i konfiguracji dzierżawy.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Przykłady użycia w usłudze GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Dodatkowe przykłady w usłudze GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Następne kroki

Teraz możesz dowiedzieć się więcej o procesorze pliku danych zmian w następujących artykułach:

* [Omówienie procesora pliku danych zmian](change-feed-processor.md)
* [Czas rozpoczęcia procesora zestawienia zmian](how-to-configure-change-feed-start-time.md)
