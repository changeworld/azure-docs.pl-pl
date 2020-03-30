---
title: Testowanie jednostki usług Azure Durable Functions
description: Dowiedz się, jak przetestować funkcje trwałe.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 86733f8b5b80799bad3e52c643ed27465dfc7641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231224"
---
# <a name="durable-functions-unit-testing"></a>Testowanie jednostki funkcji trwałych

Testowanie jednostkowe jest ważną częścią nowoczesnych praktyk w zakresie tworzenia oprogramowania. Testy jednostkowe weryfikują zachowanie logiki biznesowej i chronią przed wprowadzeniem niezauważonych zmian w przyszłości. Funkcje trwałe mogą łatwo rosnąć w złożoności, więc wprowadzenie testów jednostkowych pomoże uniknąć przełomowych zmian. W poniższych sekcjach wyjaśniono, jak jednostki testowania trzech typów funkcji — klient aranżacji, koordynator i funkcje działania.

> [!NOTE]
> Ten artykuł zawiera wskazówki dotyczące testowania jednostek dla aplikacji funkcji trwałych przeznaczonych dla funkcji trwałych 1.x. Nie został jeszcze zaktualizowany w celu uwzględnienia zmian wprowadzonych w funkcji trwałych 2.x. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

Przykłady w tym artykule wymagają znajomości następujących pojęć i struktur:

* Testowanie jednostek

* Trwałe funkcje

* [xUnit](https://xunit.github.io/) — struktura testowania

* [moq](https://github.com/moq/moq4) - Szydercza struktura

## <a name="base-classes-for-mocking"></a>Klasy podstawowe do szyderstwa

Mocking jest obsługiwany przez trzy abstrakcyjne klasy w funkcji trwałych 1.x:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

Klasy te są `DurableOrchestrationClient`klasami podstawowymi dla , `DurableOrchestrationContext`i `DurableActivityContext` które definiują aranżacji klienta, koordynatora i działania metod. Makiety ustawi oczekiwane zachowanie dla metod klasy podstawowej, dzięki czemu test jednostkowy może zweryfikować logikę biznesową. Istnieje dwuetapowy przepływ pracy dla jednostki testowania logiki biznesowej w kliencie aranżacji i koordynatorze:

1. Użyj klas podstawowych zamiast konkretnej implementacji podczas definiowania podpisów funkcji klienta aranżacji i aranżatora.
2. W testach jednostkowych mock zachowanie klas podstawowych i sprawdź logiki biznesowej.

Znajdź więcej szczegółów w poniższych akapitach do testowania funkcji, które używają powiązania klienta aranżacji i powiązania wyzwalacza orchestrator.

## <a name="unit-testing-trigger-functions"></a>Funkcje wyzwalania testowania jednostkowego

W tej sekcji test jednostkowy sprawdzi logikę następującej funkcji wyzwalacza HTTP do uruchamiania nowych aranżacji.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Zadaniem testu jednostkowego będzie sprawdzenie `Retry-After` wartości nagłówka podanego w ładunku odpowiedzi. Dlatego test jednostkowy będzie `DurableOrchestrationClientBase` kpić niektóre metody, aby zapewnić przewidywalne zachowanie.

Po pierwsze, wymagana jest makieta `DurableOrchestrationClientBase`klasy podstawowej, . Makieta może być nową `DurableOrchestrationClientBase`klasą, która implementuje . Jednak przy użyciu szyderczy framework jak [moq](https://github.com/moq/moq4) upraszcza proces:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Następnie `StartNewAsync` metoda jest wyśmiewany, aby zwrócić dobrze znany identyfikator wystąpienia.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Dalej `CreateCheckStatusResponse` jest wyśmiewany, aby zawsze zwracać pustą odpowiedź HTTP 200.

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

`ILogger`jest również wyśmiewany:

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
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
        loggerMock.Object);
 ```

 Ostatnim krokiem jest porównanie danych wyjściowych z wartością oczekiwaną:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Po połączeniu wszystkich kroków test jednostkowy będzie miał następujący kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Funkcje orkiestratora testowania jednostek

Funkcje orchestrator są jeszcze bardziej interesujące dla testowania jednostkowego, ponieważ zwykle mają o wiele więcej logiki biznesowej.

W tej sekcji testy jednostkowe zweryfikują dane wyjściowe funkcji `E1_HelloSequence` Orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Kod testu jednostkowego rozpocznie się od utworzenia makiety:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Następnie wywołania metody działania będą wyśmiewane:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Następnie test jednostkowy `HelloSequence.Run` wywoła metodę:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

I wreszcie dane wyjściowe zostaną zatwierdzone:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Po połączeniu wszystkich kroków test jednostkowy będzie miał następujący kod:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Funkcje działania testowania jednostki

Funkcje działania mogą być testowane w taki sam sposób, jak funkcje nietrwałe.

W tej sekcji test jednostkowy sprawdza `E1_SayHello` zachowanie funkcji Działanie:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Testy jednostkowe zweryfikują format danych wyjściowych. Testy jednostkowe mogą używać typów parametrów bezpośrednio lub klasy makiety: `DurableActivityContextBase`

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Dowiedz się więcej o moq](https://github.com/Moq/moq4/wiki/Quickstart)
