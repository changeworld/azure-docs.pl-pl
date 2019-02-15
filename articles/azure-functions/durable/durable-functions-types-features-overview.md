---
title: Omówienie typy funkcji i funkcji dla funkcje trwałe - Azure
description: Dowiedz się, jakiego rodzaju funkcje i role, które umożliwiają komunikację funkcji do funkcji w ramach aranżacji trwałe — funkcja
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 2885ce740fab58e675c529dfab8d0dadeed2904c
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301625"
---
# <a name="overview-of-function-types-and-features-for-durable-functions-azure-functions"></a>Omówienie typy funkcji i funkcji dla funkcje trwałe (usługi Azure Functions)

Trwałe funkcje zapewnia aranżacji stanowej wykonywania funkcji. Trwałe funkcji to rozwiązanie składa się z różnych funkcji platformy Azure. Każda z tych funkcji można odtworzyć różne role w ramach aranżacji. Ten dokument zawiera omówienie typy funkcji, które są zaangażowane w aranżacji funkcji trwałe. Zawiera również niektóre typowe wzorce w łączenie funkcji.  Aby teraz rozpocząć pracę, należy utworzyć pierwszą funkcję trwałego w [ C# ](durable-functions-create-first-csharp.md) lub [JavaScript](quickstart-js-vscode.md).

![Typy trwałe funkcje][1]  

## <a name="types-of-functions"></a>Typy funkcji

### <a name="activity-functions"></a>Działanie funkcji

Działanie funkcji to podstawowa jednostka pracy w trwałych aranżacji.  Działanie funkcji są funkcje i zadania są zorganizowanych w procesie.  Może na przykład tworzenie trwałych funkcji do przetwarzania zamówienia — Sprawdź plik spisu, klienta i utworzyć wydanie.  Każdej z nich te zadania będą funkcję działania.  Działanie funkcji nie ma żadnych ograniczeń w typie praca wykonywana w nich.  Mogą być napisane w dowolnym [języków obsługiwanych przez funkcje trwałe](durable-functions-overview.md#language-support). Trwałe Framework zadań gwarantuje, że każdej funkcji, działania o nazwie zostanie uruchomiony przynajmniej raz podczas aranżacji.

Działanie funkcji musi zostać wyzwolone przez [działania wyzwalacza](durable-functions-bindings.md#activity-triggers).  Funkcje platformy .NET będzie otrzymywać [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) jako parametr. Możesz również powiązać wyzwalacza do innego obiektu, aby przekazać dane wejściowe do funkcji. W języku JavaScript, danych wejściowych jest możliwy za pośrednictwem `<activity trigger binding name>` właściwość [ `context.bindings` obiektu](../functions-reference-node.md#bindings).

Działanie funkcji może również zwracać wartości, wróć do programu orchestrator.  Jeśli wysyłanie lub zwracanie wielu wartości z funkcją działań, możesz to zrobić [korzystaj z krotek lub tablic](durable-functions-bindings.md#passing-multiple-parameters).  Działanie funkcji mogą być wywoływane tylko z wystąpienia aranżacji.  Podczas gdy jakiś kod może być współużytkowane między funkcję działania i inną funkcję (np. funkcję wyzwalaną przez protokół HTTP), każda funkcja może mieć tylko jeden wyzwalacz.

Więcej informacji i przykładów można znaleźć w [funkcje trwałe powiązanie artykułu](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Funkcje programu orchestrator

Funkcje programu orchestrator są niezwykle niezawodne funkcji.  Funkcje programu orchestrator opisują sposób i kolejności, w jakiej są wykonywane akcje.  Funkcje programu orchestrator opisują aranżacji w kodzie (C# czy języka JavaScript) jak pokazano na [wzorce funkcje trwałe i zagadnienia techniczne](durable-functions-concepts.md).  Organizacja może mieć wiele różnych rodzajów działań, takie jak [działania funkcji](#activity-functions), [podrzędnych aranżacji](#sub-orchestrations), [oczekiwanie na zdarzenia zewnętrzne](#external-events), i [ czasomierze](#durable-timers).  

Funkcja orkiestratora musi zostać wyzwolone przez [wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-triggers).

Program orchestrator jest uruchamiany przez [klienta programu orchestrator](#client-functions) która sama można wywołać z dowolnego źródła (HTTP, kolejek, strumienie zdarzeń).  Każde wystąpienie aranżacji ma identyfikator wystąpienia, które mogą być generowane automatycznie (zalecane) lub wygenerowanej przez użytkowników.  Ten identyfikator może być używany do [Zarządzanie wystąpieniami](durable-functions-instance-management.md) aranżacji.

Więcej informacji i przykładów można znaleźć w [funkcje trwałe powiązanie artykułu](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Funkcje klienta

Funkcje klienta są wyzwalane funkcje, które spowoduje utworzenie nowych wystąpień aranżacji.  Są one punkt wejścia dla tworzenia wystąpienia obiektu trwałego aranżacji.  Funkcje klienta można wyzwolone przez dowolnego wyzwalacza (HTTP, kolejek, strumienie zdarzeń, itp.) i napisane w dowolnym języku, obsługiwanych przez aplikację.  Oprócz wyzwalacza ma funkcje klienta [klient orkiestracji](durable-functions-bindings.md#orchestration-client) powiązania, który pozwala na tworzenie i zarządzanie nimi trwałe aranżacji.  Przykład najbardziej podstawowa funkcja klienta jest funkcją wyzwalaną przez protokół HTTP, rozpoczyna się funkcja orkiestratora, która zwraca odpowiedź dotycząca stanu wyboru jako [pokazano w poniższym przykładzie następujące](durable-functions-http-api.md#http-api-url-discovery).

Więcej informacji i przykładów można znaleźć w [funkcje trwałe powiązanie artykułu](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funkcje i wzorców

### <a name="sub-orchestrations"></a>Orkiestracje podrzędne

Oprócz wywoływanie funkcji działania, funkcje programu orchestrator może wywoływać innych funkcji programu orchestrator. Można na przykład, utworzyć większy aranżacji poza bibliotekę funkcji programu orchestrator. Lub można uruchomić wiele wystąpień funkcji orkiestratora równolegle.

Więcej informacji i przykładów można znaleźć w [artykułu podrzędnych aranżacji](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Czasomierze trwałe

[Trwałe funkcje](durable-functions-overview.md) zapewnia *trwałe czasomierzy* do użytku w funkcjach programu orchestrator do zaimplementowania opóźnienia lub skonfigurować przekroczeń limitu czasu na operacje asynchroniczne. Czasomierze trwałe powinien być używany w funkcji programu orchestrator, zamiast `Thread.Sleep` i `Task.Delay` (C#) lub `setTimeout()` i `setInterval()` (JavaScript).

Więcej informacji i przykładów czasomierzy trwałych, można znaleźć w [artykułu czasomierzy trwałe](durable-functions-timers.md)

### <a name="external-events"></a>Zdarzenia zewnętrzne

Funkcje programu orchestrator poczekać, aż zdarzenia zewnętrzne, można zaktualizować wystąpienia aranżacji. Ta funkcja funkcje trwałe jest często przydatny w przypadku obsługi z reakcji człowieka lub innych zewnętrznych wywołań zwrotnych.

Więcej informacji i przykładów można znaleźć w [artykułu zdarzenia zewnętrzne](durable-functions-external-events.md).

### <a name="error-handling"></a>Obsługa błędów

Trwałe aranżacji funkcji są implementowane w kodzie i można korzystać z funkcji obsługi błędów języka programowania.  Oznacza to, wzorców, takich jak "try/catch" będzie działać w Twojej orkiestracji.  Trwałe funkcje mają również niektórych wbudowanych zasad ponawiania.  Akcję można opóźnić i ponów próbę działania automatycznie na wyjątki.  Ponowne próby umożliwiają obsługę przejściowych wyjątków bez konieczności porzucania aranżacji.

Więcej informacji i przykładów można znaleźć w [dodanymi komentarzami artykułu](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Aplikacja dla wielu funkcji komunikacji

Trwałe orchestration znajduje się zwykle w kontekście aplikacji jednej funkcji, są wzorce umożliwiają do koordynowania aranżacji wielu aplikacji funkcji.  Mimo że komunikacji między aplikacjami, może być wykonywane za pośrednictwem protokołu HTTP, za pomocą trwałych framework dla każdego działania tak oznacza, że nadal można zachować procesu trwałe w dwóch aplikacjach.

Przykłady aranżacji aplikacji dla wielu funkcji, w C# i języka JavaScript są przedstawione poniżej.  Jedno działanie zostanie uruchomione zewnętrznych aranżacji. Następnie pobierze i przywrócenie stanu innego działania.  Koordynatora będzie czekać na stanie, które mają być ukończone przed kontynuowaniem.

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
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

    // Call a remote orchestration
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.azurewebsites.net/orchestrations/${orchestratorName}`,
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

> [!div class="nextstepaction"]
> [Kontynuuj czytanie dokumentacji trwałe funkcje](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
