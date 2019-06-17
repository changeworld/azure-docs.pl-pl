---
title: Testy jednostkowe trwałe funkcje platformy Azure
description: Dowiedz się, jak do jednostki przetestować funkcje trwałe.
services: functions
author: kadimitr
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: kadimitr
ms.openlocfilehash: 69cf91f1448e36353f83de7a271abb3b53858bb0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60648469"
---
# <a name="durable-functions-unit-testing"></a>Trwałe funkcje testów jednostkowych

Testy jednostkowe jest ważną częścią oprogramowania nowoczesnych wytwarzania oprogramowania. Testy jednostkowe sprawdzić zachowanie logiki biznesowej i ochronę z jej poziomu wprowadzać niezauważona przełomowe zmiany w przyszłości. Trwałe funkcje można łatwo zwiększanie się stopnia skomplikowania, wprowadzenie do testów jednostkowych pomoże w celu uniknięcia istotne zmiany. W poniższych sekcjach opisano jak do jednostki przetestować typy funkcji trzy - aranżacji klienta programu Orchestrator i działania funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

Przykłady w niniejszym artykule wymagają znajomości następujące pojęcia i platform:

* Testy jednostkowe

* Trwałe funkcje

* [xUnit](https://xunit.github.io/) — struktura testowania

* [moq](https://github.com/moq/moq4) -pozorowanie framework

## <a name="base-classes-for-mocking"></a>Klasy bazowe na potrzeby pozorowanie

Pozorowanie jest świadczona za pośrednictwem trzy klasy abstrakcyjnej w trwałe funkcje:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html)

Te klasy są klasy bazowe na potrzeby [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html), [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html), i [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) definiują klient Orkiestracji , Orchestrator i metod działania. Mocks ustawi oczekiwane zachowanie metody klasy bazowej, aby sprawdzić logiki biznesowej testu jednostkowego. Brak dwuetapowej przepływ pracy dla jednostki testowania logiki biznesowej w kliencie aranżacji i Orchestrator:

1. Użyj zamiast konkretną implementację klas bazowych, podczas definiowania podpisów klient Orkiestracji i koordynatora.
2. W testach jednostkowych testowanie zachowania klas bazowych i sprawdź logikę biznesową.

Więcej szczegółowych informacji można znaleźć w sekcjach do testowania funkcji, które klient orkiestracji powiązania i koordynatora wyzwolić powiązania.

## <a name="unit-testing-trigger-functions"></a>Jednostki testowania funkcji wyzwalacza

W tej sekcji test jednostkowy zostanie przeprowadzona Weryfikacja logikę następującą funkcję wyzwalacza HTTP do uruchamiania nowych mechanizmów.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Jednostka testu nie będzie można zweryfikować wartości z `Retry-After` nagłówka w ładunku odpowiedzi. Więc test jednostkowy będzie testowanie część [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) metody, aby zapewnić zachowanie przewidywalne.

Po pierwsze, pozorny klasy bazowej jest wymagany, [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Projekt może być nową klasę, która implementuje [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Jednak przy użyciu pozorowania środowiska, takiego jak [moq](https://github.com/moq/moq4) upraszcza proces:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Następnie `StartNewAsync` metoda jest w postaci makiet do zwrócenia identyfikatora wystąpienia dobrze znane.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Następny `CreateCheckStatusResponse` jest zwracany pozorowane, aby zawsze pustą odpowiedź HTTP 200.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

`TraceWriter` jest również w postaci makiet:

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

 Ostatnim krokiem jest do porównywania danych wyjściowych z oczekiwaną wartością:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Po połączeniu wszystkich kroków, test jednostkowy będzie miał następujący kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Funkcje programu orchestrator testy jednostkowe

Funkcje programu orchestrator są nawet bardziej interesujące dla jednostki, testowania, ponieważ mają one zwykle o wiele więcej logiki biznesowej.

W tej sekcji jednostki testów zostanie przeprowadzona Weryfikacja danych wyjściowych `E1_HelloSequence` funkcji programu Orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Kod testu jednostkowego rozpoczyna się od tworzenia makiety:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Następnie wywołania metody działania będzie można w postaci makiet:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Następnie wywoła test jednostkowy `HelloSequence.Run` metody:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

A na koniec zostanie zweryfikowana dane wyjściowe:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Po połączeniu wszystkich kroków, test jednostkowy będzie miał następujący kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Jednostki testowania funkcji działań

Działanie funkcji mogą być testowane w taki sam sposób jak nietrwałe funkcje jednostki.

W tej sekcji test jednostkowy zostanie przeprowadzona Weryfikacja zachowanie `E1_SayHello` działania funkcji:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

I testy jednostkowe zweryfikuje format danych wyjściowych. Testy jednostkowe można używać typów parametrów, bezpośrednio lub mock [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html) klasy:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Dowiedz się więcej o moq](https://github.com/Moq/moq4/wiki/Quickstart)
