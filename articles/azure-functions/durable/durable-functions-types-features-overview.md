---
title: Typy funkcji i funkcji w rozszerzenia funkcji trwałych usługi Azure Functions
description: Informacje o typach funkcji i ról, które obsługują komunikację funkcji do funkcji w aranżacji funkcje trwałe, w usłudze Azure Functions.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 03/19/2019
ms.author: v-junlch
ms.openlocfilehash: 76b6f013333113d5a24b744bc962d36b1c0e21b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731123"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Trwałe funkcje typy i funkcje (usługi Azure Functions)

Funkcje trwałe to rozszerzenie [usługi Azure Functions](../functions-overview.md). Trwałe funkcje służy do aranżacji stanowej wykonywania funkcji. Funkcja trwałe to rozwiązanie, które składa się z różnych funkcji platformy Azure. Funkcje można odtworzyć różne role w aranżacji funkcji trwałe. 

Ten artykuł zawiera omówienie, jakiego rodzaju funkcje, których można używać w aranżacji funkcje trwałe. Artykuł zawiera niektóre typowe wzorce, używane do łączenia z funkcji. Dowiedz się, jak trwałe funkcje mogą pomóc w rozwiązywaniu wyzwaniom związanym z programowaniem aplikacji.

![Obraz, który zawiera typy trwałe funkcje][1]  

## <a name="types-of-durable-functions"></a>Typy trwałe funkcje

Można użyć trzech typów trwałe funkcji w usłudze Azure Functions: działanie programu orchestrator i klienta.

### <a name="activity-functions"></a>Działanie funkcji

Działanie funkcji są podstawową jednostką pracy w aranżacji funkcji trwałych. Działanie funkcji są funkcji i zadań, które są zarządzane w procesie. Na przykład może utworzyć trwałe funkcji do przetwarzania zamówienia. Zadania obejmują sprawdzanie spisu, ładowania klienta i tworzenia wysyłki. Każde zadanie podrzędne może być funkcją działania. 

Działanie funkcji nie są ograniczone w typie praca wykonywana w nich. Można napisać funkcję działania w dowolnym [język, który obsługuje funkcje trwałe](durable-functions-overview.md#language-support). Framework trwałe zadań gwarantuje, że każdej funkcji, działania o nazwie zostanie uruchomiony przynajmniej raz podczas aranżacji.

Użyj [działania wyzwalacza](durable-functions-bindings.md#activity-triggers) do wyzwolenia funkcji przez działanie. Odbieranie funkcji .NET [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) jako parametr. Możesz również powiązać wyzwalacza do innego obiektu, aby przekazać dane wejściowe do funkcji. W języku JavaScript, możesz uzyskać dostęp za pośrednictwem danych wejściowych `<activity trigger binding name>` właściwość [ `context.bindings` obiektu](../functions-reference-node.md#bindings).

Funkcja działanie może również zwracać wartości do programu orchestrator. Jeśli wysyłanie lub zwrócić dużą liczbę wartości z działania funkcji, możesz użyć [krotek lub tablic](durable-functions-bindings.md#passing-multiple-parameters). Możesz wyzwolić funkcję działanie tylko z wystąpieniem aranżacji. Mimo że funkcja działania i inną funkcję (na przykład funkcji wyzwalanej przez HTTP) może udostępnić kodu, każda funkcja może mieć tylko jeden wyzwalacz.

Aby uzyskać więcej informacji i przykładów, zobacz [działania funkcji](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Funkcje programu orchestrator

Funkcje programu orchestrator opisano, jak akcje są wykonywane i kolejność wykonywania działań. Funkcje programu orchestrator opisują aranżacji w kodzie (C# czy języka JavaScript) jak pokazano na [wzorce funkcje trwałe i zagadnienia techniczne](durable-functions-concepts.md). Organizacja może mieć wiele różnych rodzajów działań, w tym [działania funkcji](#activity-functions), [podrzędnych aranżacji](#sub-orchestrations), [oczekiwanie na zdarzenia zewnętrzne](#external-events)i [czasomierzy](#durable-timers). 

Funkcja orkiestratora musi zostać wyzwolone przez [wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-triggers).

Program orchestrator jest uruchamiany przez [klienta orchestrator](#client-functions). Można uruchomić programu orchestrator z dowolnego źródła (HTTP, kolejki, strumienia zdarzeń). Każde wystąpienie aranżacji ma identyfikator wystąpienia. Identyfikator wystąpienia mogą być automatycznie wygenerowany (zalecane) lub wygenerowanej przez użytkowników. Można użyć identyfikatora wystąpienia, aby [Zarządzanie wystąpieniami](durable-functions-instance-management.md) aranżacji.

Aby uzyskać więcej informacji i przykładów, zobacz [wyzwalaczy aranżacji](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Funkcje klienta

Funkcje klienta są wyzwalane funkcji, służących do tworzenia nowych wystąpień aranżacji. Funkcje klienta są punkt wejścia dla tworzenia wystąpienia obiektu aranżacji funkcje trwałe. Możesz wyzwolić funkcję klienta z dowolnego źródła (HTTP, kolejki, strumienia zdarzeń). Można napisać funkcję klienta w dowolnym języku, który obsługuje aplikacja. 

Funkcje klienta również mieć [klient orkiestracji](durable-functions-bindings.md#orchestration-client) powiązania. Funkcja klienta można użyć klient orkiestracji powiązania do tworzenia i zarządzania trwałego aranżacji. 

Przykład najbardziej podstawowa funkcja klienta jest funkcji wyzwalanej przez HTTP, który rozpoczyna się funkcja orkiestratora, a następnie zwraca odpowiedź dotycząca stanu wyboru. Aby uzyskać przykład, zobacz [Odnajdywanie adresu URL interfejsu API HTTP](durable-functions-http-api.md#http-api-url-discovery).

Aby uzyskać więcej informacji i przykładów, zobacz [klient Orkiestracji](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funkcje i wzorców

W kolejnych sekcjach opisano funkcje i wzorce typów funkcje trwałe.

### <a name="sub-orchestrations"></a>Orkiestracje podrzędne

Funkcje programu orchestrator można wywołać funkcji działań, ale można to również wywołać inne funkcje programu orchestrator. Można na przykład, utworzyć większy aranżacji poza bibliotekę funkcji programu orchestrator. Alternatywnie można uruchomić wiele wystąpień funkcji orkiestratora równolegle.

Aby uzyskać więcej informacji i przykładów, zobacz [podrzędnych aranżacji](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Czasomierze trwałe

[Trwałe funkcje](durable-functions-overview.md) zapewnia *trwałe czasomierzy* używanej w funkcjach programu orchestrator do zaimplementowania opóźnienia lub skonfigurować przekroczeń limitu czasu na operacje asynchroniczne. Używaj czasomierzy trwałe w funkcjach programu orchestrator, zamiast `Thread.Sleep` i `Task.Delay` (C#) lub `setTimeout()` i `setInterval()` (JavaScript).

Aby uzyskać więcej informacji i przykładów, zobacz [trwałe czasomierzy](durable-functions-timers.md).

### <a name="external-events"></a>Zdarzenia zewnętrzne

Funkcje programu orchestrator poczekać, aż zdarzenia zewnętrzne, można zaktualizować wystąpienia aranżacji. Ta funkcja funkcje trwałe często jest przydatny w przypadku obsługi z reakcji człowieka lub innych zewnętrznych wywołań zwrotnych.

Aby uzyskać więcej informacji i przykładów, zobacz [zdarzenia zewnętrzne](durable-functions-external-events.md).

### <a name="error-handling"></a>Obsługa błędów

Użyj kodu, aby zaimplementować funkcje trwałe aranżacji. Można użyć funkcji obsługi błędów języka programowania. Wzorców, takich jak `try` / `catch` działają w Twojej orkiestracji. 

Trwałe funkcje również pochodzić z wbudowanych zasad ponawiania. Akcję można opóźnić i ponów próbę wykonania działania automatycznie po wystąpieniu wyjątku. Ponownych prób służy do obsługi przejściowych wyjątków bez porzucania aranżacji.

Aby uzyskać więcej informacji i przykładów, zobacz [obsługi błędów](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Aplikacja dla wielu funkcji komunikacji

Mimo że trwałe aranżacji działa w kontekście aplikacji jednej funkcji, można użyć wzorców do koordynowania aranżacji wielu aplikacji funkcji. Dla wielu aplikacji może komunikować się za pośrednictwem protokołu HTTP, ale przy użyciu platformy trwałe dla każdego działania oznacza, że nadal można zachować procesu trwałe w dwóch aplikacjach.

W poniższych przykładach pokazano aplikację dla wielu funkcji aranżacji w C# i JavaScript. W każdym przykładzie jedno działanie uruchamia zewnętrznych aranżacji. Pobiera innego działania, a zwraca stan. Koordynatora czeka, aż stan był `Complete` przed kontynuowaniem.

Poniżej przedstawiono kilka przykładów aranżacji aplikacji dla wielu funkcji:

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.chinacloudsites.cn/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.chinacloudsites.cn/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę, należy utworzyć pierwszą funkcję trwałego w [ C# ](durable-functions-create-first-csharp.md) lub [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Dowiedz się więcej o funkcje trwałe](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png

<!-- Update_Description: wording update -->