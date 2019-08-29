---
title: Typy funkcji i funkcje w Durable Functions rozszerzeniu Azure Functions
description: Dowiedz się więcej o typach funkcji i ról, które obsługują komunikację typu "funkcja-funkcja" w Durable Functions aranżacji w Azure Functions.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 0d3087c768a02bb5c647fc0d10db3aa4274804f4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097737"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Durable Functions typy i funkcje (Azure Functions)

Durable Functions jest rozszerzeniem [Azure Functions](../functions-overview.md). Durable Functions służy do organizowania stanowej aranżacji wykonywania funkcji. Funkcja trwałe to rozwiązanie, które składa się z różnych funkcji platformy Azure. Funkcje mogą odtwarzać różne role w trwałej aranżacji funkcji. 

Ten artykuł zawiera omówienie typów funkcji, których można użyć w ramach aranżacji Durable Functions. Artykuł zawiera kilka typowych wzorców, których można użyć do łączenia funkcji. Dowiedz się, jak Durable Functions może pomóc w rozwiązaniu problemów z programowaniem aplikacji.

![Obraz przedstawiający typy funkcji trwałych][1]  

## <a name="types-of-durable-functions"></a>Typy funkcji trwałych

Możesz użyć czterech typów trwałych funkcji w Azure Functions: działania, Orchestrator, Entity i Client.

### <a name="activity-functions"></a>Funkcje działania

Funkcje działania to podstawowa jednostka pracy w ramach aranżacji funkcji trwałych. Funkcje działania to funkcje i zadania, które są zorganizowane w procesie. Na przykład można utworzyć trwałą funkcję w celu przetworzenia zamówienia. Zadania obejmują sprawdzanie spisu, ładowanie klienta i tworzenie wysyłki. Każde zadanie będzie funkcją działania. 

Funkcje działania nie są ograniczone do typu pracy, którą można w nich wykonywać. Funkcję działania można napisać w dowolnym [języku, który Durable Functions obsługiwać](durable-functions-overview.md#language-support). Trwała struktura zadań gwarantuje, że każda wywołana funkcja Activity zostanie wykonana co najmniej raz podczas aranżacji.

Użyj [wyzwalacza działania](durable-functions-bindings.md#activity-triggers) , aby wyzwolić funkcję działania. Funkcje programu .NET otrzymują [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) jako parametr. Można również powiązać wyzwalacz z dowolnym innym obiektem, aby przekazać dane wejściowe do funkcji. W języku JavaScript można uzyskać dostęp do danych wejściowych za `<activity trigger binding name>` pośrednictwem właściwości [ `context.bindings` obiektu](../functions-reference-node.md#bindings).

Funkcja Activity może również zwracać wartości do programu Orchestrator. W przypadku wysyłania lub zwracania dużej liczby wartości z funkcji działania można używać [krotek lub tablice](durable-functions-bindings.md#passing-multiple-parameters). Funkcję działania można wyzwolić tylko z wystąpienia aranżacji. Mimo że funkcja działania i inna funkcja (jak funkcja wyzwalana przez protokół HTTP) mogą współużytkować jakiś kod, każda funkcja może mieć tylko jeden wyzwalacz.

Aby uzyskać więcej informacji i zapoznać się z przykładami, zobacz [funkcje działania](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Funkcje programu Orchestrator

Funkcje programu Orchestrator opisują sposób wykonywania akcji i kolejność wykonywania akcji. Funkcje programu Orchestrator opisują aranżację w kodzieC# (lub JavaScript), jak pokazano w [Durable Functions wzorców i koncepcje techniczne](durable-functions-concepts.md). Aranżacja może mieć wiele różnych typów akcji, w tym [funkcje działania](#activity-functions), podwykonawcy, oczekiwanie [na zdarzenia zewnętrzne](#external-events)i czasomierze [](#durable-timers). [](#sub-orchestrations) Funkcje programu Orchestrator mogą również współdziałać z [funkcjami jednostek](#entity-functions).

Funkcja programu Orchestrator musi być wyzwalana przez [wyzwalacz aranżacji](durable-functions-bindings.md#orchestration-triggers).

Program Orchestrator jest uruchamiany przez [klienta programu Orchestrator](#client-functions). Program Orchestrator można wyzwolić z dowolnego źródła (HTTP, Queue, Stream). Każde wystąpienie aranżacji ma identyfikator wystąpienia. Identyfikator wystąpienia może być automatycznie generowany (zalecane) lub generowany przez użytkownika. Identyfikatora wystąpienia można użyć do [zarządzania wystąpieniami](durable-functions-instance-management.md) aranżacji.

Aby uzyskać więcej informacji i zapoznać się z [](durable-functions-bindings.md#orchestration-triggers)przykładami, zobacz wyzwalacze aranżacji.

###  <a name="entity-functions"></a>Funkcje jednostki (wersja zapoznawcza)

Funkcje Entity definiują operacje umożliwiające odczytywanie i aktualizowanie małych fragmentów stanu, znanych jako *jednostek trwałych*. Podobnie jak funkcje programu Orchestrator, funkcje jednostki są funkcjami o specjalnym typie wyzwalacza, *wyzwalaczem jednostki*. W przeciwieństwie do funkcji programu Orchestrator, funkcje jednostek nie mają żadnych ograniczeń związanych z kodem. Funkcje jednostek również zarządzają stanem jawnie, a nie niejawnie reprezentującą stan za pośrednictwem przepływu sterowania.

> [!NOTE]
> Funkcje jednostki i powiązane funkcje są dostępne tylko w Durable Functions 2,0 i nowszych.

Aby uzyskać więcej informacji na temat funkcji jednostki, zobacz dokumentację funkcji [Entity Functions](durable-functions-preview.md#entity-functions) w wersji zapoznawczej.

### <a name="client-functions"></a>Funkcje klienta

Funkcje klienta są wyzwalane funkcje, które tworzą wystąpienia aranżacji i jednostek i zarządzają nimi. Są one efektywnie punktem wejścia do korzystania z Durable Functions. Funkcję klienta można wyzwolić z dowolnego źródła (HTTP, kolejki, strumienia zdarzeń itp.). Funkcja klienta używa [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client) do tworzenia trwałych aranżacji i jednostek oraz zarządzania nimi.

Najbardziej podstawowym przykładem funkcji klienta jest funkcja wyzwalana przez protokół HTTP, która uruchamia funkcję programu Orchestrator, a następnie zwraca odpowiedź stanu sprawdzania. Aby zapoznać się z przykładem, zobacz [odnajdywanie adresów URL interfejsu API protokołu HTTP](durable-functions-http-api.md#http-api-url-discovery).

Aby uzyskać więcej informacji i zapoznać się z przykładami, zobacz [klienta aranżacji](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funkcje i wzorce

W następnych sekcjach opisano funkcje i wzorce typów Durable Functions.

### <a name="sub-orchestrations"></a>Orkiestracje podrzędne

Funkcje programu Orchestrator mogą wywoływać funkcje działania, ale mogą również wywoływać inne funkcje koordynatora. Można na przykład utworzyć większą organizację poza biblioteką funkcji programu Orchestrator. Lub można uruchomić wiele wystąpień funkcji programu Orchestrator równolegle.

Aby uzyskać więcej informacji i zapoznać się z [](durable-functions-sub-orchestrations.md)przykładami, zobacz podorganizowanie.

### <a name="durable-timers"></a>Trwałe czasomierze

[Durable Functions](durable-functions-overview.md) oferuje *trwałe czasomierze* , których można użyć w funkcjach programu Orchestrator do implementowania opóźnień lub skonfigurowania limitów czasu dla akcji asynchronicznych. Używaj trwałych czasomierzy w funkcjach `Thread.Sleep` programu `Task.Delay` OrchestratorC#zamiast i `setTimeout()` ( `setInterval()` ) lub i (JavaScript).

Aby uzyskać więcej informacji i zapoznać się z przykładami, zobacz [trwałe czasomierze](durable-functions-timers.md).

### <a name="external-events"></a>Zdarzenia zewnętrzne

Funkcje programu Orchestrator mogą czekać, aż zdarzenia zewnętrzne zaktualizują wystąpienie aranżacji. Ta funkcja Durable Functions często jest przydatna do obsługi interakcji człowieka lub innych zewnętrznych wywołań zwrotnych.

Aby uzyskać więcej informacji i zapoznać się z przykładami, zobacz [zdarzenia zewnętrzne](durable-functions-external-events.md).

### <a name="error-handling"></a>Obsługa błędów

Użyj kodu, aby zaimplementować Durable Functions aranżacji. Można użyć funkcji obsługi błędów w języku programowania. Wzorce takie `try` jak /pracewaranżacji `catch` . 

Durable Functions również mieć wbudowane zasady ponawiania. Akcja może opóźniać i ponawiać działania automatycznie, gdy wystąpi wyjątek. Można użyć ponownych prób do obsługi wyjątków przejściowych bez porzucenia aranżacji.

Aby uzyskać więcej informacji i zapoznać się z przykładami, zobacz [Obsługa błędów](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Komunikacja aplikacji między funkcjami

Chociaż trwała aranżacja jest uruchamiana w kontekście pojedynczej aplikacji funkcji, można używać wzorców do koordynowania aranżacji w wielu aplikacjach funkcji. Komunikacja między aplikacjami może odbywać się za pośrednictwem protokołu HTTP, ale użycie trwałej struktury dla każdego działania oznacza, że nadal można zachować trwały proces dla dwóch aplikacji.

W poniższych przykładach pokazano aranżację aplikacji dla wielu funkcji C# w programie i JavaScript. W każdym przykładzie jedno działanie uruchamia zewnętrzną aranżację. Inne działanie pobiera i zwraca stan. Program Orchestrator czeka, aż stan będzie się `Complete` utrzymywał.

Oto kilka przykładów aranżacji aplikacji między funkcjami:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

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

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, Utwórz pierwszą trwałą funkcję w [C#](durable-functions-create-first-csharp.md) języku lub [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Przeczytaj więcej na temat Durable Functions](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
