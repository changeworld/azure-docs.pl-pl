---
title: Testy jednostkowe trwałe funkcji platformy Azure
description: Dowiedz się, jak do jednostki przetestować funkcje trwałe.
services: functions
author: kadimitr
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2018
ms.author: kadimitr
ms.openlocfilehash: 7de9a6f0d4dfcb45932b89504c0d38c3c70283e9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="durable-functions-unit-testing"></a>Testy jednostkowe trwałe funkcji

Testy jednostkowe jest ważną częścią oprogramowania nowoczesnych rozwoju rozwiązań. Testy jednostkowe Sprawdź zachowanie logiki biznesowej i ochronę z jej poziomu wprowadzać zmian, które psuły niezauważalnie w przyszłości. Trwałe funkcji można łatwo zwiększa się złożoności, więc introducing testy jednostkowe pomoże uniknąć fundamentalne zmiany. W poniższych sekcjach opisano sposób do jednostki typy trzech funkcji - aranżacji klienta programu Orchestrator i działania testu funkcji. 

## <a name="prerequisites"></a>Wymagania wstępne

Przykłady w tym artykule wymagają znajomości następujące pojęcia i platform: 

* Testy jednostkowe

* Trwałe funkcje 

* [xUnit](https://xunit.github.io/) — testowanie framework

* [moq](https://github.com/moq/moq4) -Mocking framework


## <a name="base-classes-for-mocking"></a>Klasy podstawowe dla mocking 

Mocking jest obsługiwany za pośrednictwem dwóch klas abstrakcyjnych trwałe funkcji:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) 

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

Te klasy są klasy podstawowe dla [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) i [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) definiującą metody klienta Orchestration i usługi Orchestrator. Mocks ustawi oczekiwane zachowanie metod klasy podstawowej, więc sprawdzić logiki biznesowej testu jednostkowego. Brak dwuetapowej przepływu pracy w celu testowania logiki biznesowej w kliencie aranżacji i Orchestrator:

1. Użyj klasy podstawowe zamiast konkretną implementację podczas definiowania aranżacji klienta i jego Orchestrator podpisów.
2. W testach jednostkowych mock zachowanie klasy podstawowe i sprawdź logiki biznesowej. 

Więcej szczegółów znajduje w sekcjach testowania funkcje korzystające z klienta aranżacji powiązania i orchestrator wyzwolenia powiązania.

## <a name="unit-testing-trigger-functions"></a>Funkcje wyzwalaczy testowania jednostki

W tej sekcji testu jednostkowego zostanie przeprowadzona Weryfikacja logiki następująca funkcja wyzwalacza HTTP do uruchamiania nowego orchestrations.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Jednostka testu nie będzie można zweryfikować wartości `Retry-After` nagłówka w ładunku odpowiedzi. Dzięki testu jednostkowego zostanie mock niektóre [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) metod, które zapewniają przewidywalne zachowanie. 

Po pierwsze, makiety klasy podstawowej jest wymagane, [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Makiety może być nowej klasy, która implementuje [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Jednak przy użyciu platformy mocking jak [moq](https://github.com/moq/moq4) upraszcza proces:    

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Następnie `StartNewAsync` metody jest mocked do zwracania identyfikatora wystąpienia dobrze znany.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Następny `CreateCheckStatusResponse` jest zwracany mocked, aby zawsze pusta odpowiedź 200 protokołu HTTP.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
        });
```

`TraceWriter` jest również mocked:

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

Teraz `Run` metoda jest wywoływana z testu jednostkowego:

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object, 
        functionName,
        traceWriterMock.Object);
 ``` 

 Ostatnim krokiem jest można porównać z oczekiwaną wartością dane wyjściowe:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Po połączeniu wszystkich kroków, testu jednostkowego mają następujący kod: 

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Funkcje programu orchestrator testy jednostkowe

Funkcje programu orchestrator są bardziej interesujące dla jednostki testowania, ponieważ mają one zwykle znacznie większą logiki biznesowej.

W tej sekcji jednostki testów zostanie poprawnie zweryfikowany, dane wyjściowe `E1_HelloSequence` funkcji programu Orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Kod testu jednostkowego rozpoczyna się od tworzenia makiety:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Następnie zostanie mocked wywołania metody działania:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Następnie wywoła test jednostkowy `HelloSequence.Run` metody:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

I na koniec zostanie zweryfikowana dane wyjściowe:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Po połączeniu wszystkich kroków, testu jednostkowego mają następujący kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Testowanie funkcji działania jednostki

Działanie funkcji mogą być testowane w taki sam sposób jak nietrwałe funkcje jednostki. Działanie funkcji nie ma klasy podstawowej dla mocking. Dlatego testów jednostkowych używać typów parametru bezpośrednio.

W tej sekcji testu jednostkowego zostanie przeprowadzona Weryfikacja zachowanie `E1_SayHello` działania funkcji:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

I Sprawdź format danych wyjściowych testu jednostkowego:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o xUnit](http://xunit.github.io/docs/getting-started-dotnet-core)

> [Dowiedz się więcej o moq](https://github.com/Moq/moq4/wiki/Quickstart)