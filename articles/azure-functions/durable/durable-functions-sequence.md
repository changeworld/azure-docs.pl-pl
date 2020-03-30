---
title: Łączenie funkcji w funkcjach trwałych — Azure
description: Dowiedz się, jak uruchomić przykład funkcji trwałych, który wykonuje sekwencję funkcji.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562069"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Łączenie funkcji w funkcji trwałych funkcji - Hello próbki sekwencji

Łączenie funkcji odnosi się do wzorca wykonywania sekwencji funkcji w określonej kolejności. Często dane wyjściowe jednej funkcji muszą być stosowane do wejścia innej funkcji. W tym artykule opisano sekwencję łańcucha, którą tworzysz po zakończeniu szybkiego startu funkcji trwałych ([C#](durable-functions-create-first-csharp.md) lub [JavaScript](quickstart-js-vscode.md)). Aby uzyskać więcej informacji na temat funkcji trwałych, zobacz [omówienie funkcji trwałych](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funkcje

W tym artykule opisano następujące funkcje w przykładowej aplikacji:

* `E1_HelloSequence`: [Funkcja koordynatora,](durable-functions-bindings.md#orchestration-trigger) która wywołuje `E1_SayHello` wiele razy w sekwencji. Przechowuje dane wyjściowe `E1_SayHello` z wywołań i rejestruje wyniki.
* `E1_SayHello`: [Funkcja działania,](durable-functions-bindings.md#activity-trigger) która poprzedza ciąg z "Hello".
* `HttpStart`: Funkcja wyzwalana przez protokół HTTP, która uruchamia wystąpienie koordynatora.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence funkcja orkiestry

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Wszystkie funkcje aranżacji języka `DurableOrchestrationContext`C# musi mieć `Microsoft.Azure.WebJobs.Extensions.DurableTask` parametr typu , który istnieje w zestawie. Ten obiekt kontekstu *activity* umożliwia wywoływanie innych funkcji `CallActivityAsync` działania i przekazywanie parametrów wejściowych przy użyciu jego metody.

Kod wywołuje `E1_SayHello` trzy razy w sekwencji z różnych wartości parametrów. Zwracana wartość każdego wywołania `outputs` jest dodawana do listy, która jest zwracana na końcu funkcji.

# <a name="javascript"></a>[Javascript](#tab/javascript)

> [!NOTE]
> Funkcje trwałe JavaScript są dostępne tylko dla środowiska uruchomieniowego functions 2.0.

#### <a name="functionjson"></a>function.json

Jeśli używasz programu Visual Studio Code lub witryny Azure portal do tworzenia, oto zawartość pliku *function.json* dla funkcji koordynatora. Większość plików *orchestrator function.json* wygląda prawie dokładnie tak.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Ważną rzeczą jest `orchestrationTrigger` typ wiązania. Wszystkie funkcje koordynatora muszą używać tego typu wyzwalacza.

> [!WARNING]
> Aby przestrzegać reguły "nie we/wy" funkcji koordynatora, nie należy używać żadnych powiązań wejściowych lub wyjściowych podczas korzystania z powiązania `orchestrationTrigger` wyzwalacza.  Jeśli potrzebne są inne powiązania danych wejściowych lub wyjściowych, `activityTrigger` powinny one być używane w kontekście funkcji, które są wywoływane przez koordynatora. Aby uzyskać więcej informacji, zobacz ograniczenie [kodu funkcji koordynatora.](durable-functions-code-constraints.md)

#### <a name="indexjs"></a>index.js


Oto funkcja:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Wszystkie funkcje aranżacji JavaScript muszą zawierać [ `durable-functions` moduł](https://www.npmjs.com/package/durable-functions). Jest to biblioteka, która umożliwia pisanie trwałych funkcji w języku JavaScript. Istnieją trzy istotne różnice między funkcją aranżacji a innymi funkcjami JavaScript:

1. Funkcja jest [funkcją generatora.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript).
2. Funkcja jest zawijana w `durable-functions` wywołaniu `orchestrator` metody `df`modułu (tutaj ).
3. Funkcja musi być synchroniczowa. Ponieważ metoda 'orchestrator' obsługuje wywołanie "context.done", funkcja powinna po prostu "return".

Obiekt `context` zawiera `df` obiekt kontekstu aranżacji trwałe, który umożliwia wywołanie innych funkcji *działania* i przekazać parametry wejściowe przy użyciu jego `callActivity` metody. Kod wywołuje `E1_SayHello` trzy razy w sekwencji `yield` z różnych wartości parametrów, za pomocą wskazać wykonanie należy czekać na wywołania funkcji asynchronii, które mają być zwrócone. Zwracana wartość każdego wywołania `outputs` jest dodawany do tablicy, która jest zwracana na końcu funkcji.

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello funkcja aktywności

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Działania używają `ActivityTrigger` atrybutu. Użyj podanych `IDurableActivityContext` do wykonywania akcji związanych z działaniem, takich jak uzyskiwanie dostępu do wartości wejściowej za pomocą programu `GetInput<T>`.

Implementacja `E1_SayHello` jest stosunkowo trywialna operacja formatowania ciągów.

Zamiast powiązania z `IDurableActivityContext`, można powiązać bezpośrednio z typem, który jest przekazywany do funkcji działania. Przykład:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.json

Plik *function.json* dla funkcji `E1_SayHello` działania jest `E1_HelloSequence` podobny do tego, `activityTrigger` z wyjątkiem tego, że używa typu powiązania zamiast typu `orchestrationTrigger` powiązania.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Każda funkcja wywoływana przez funkcję `activityTrigger` aranżacji musi używać powiązania.

Implementacja `E1_SayHello` jest stosunkowo trywialna operacja formatowania ciągów.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

W przeciwieństwie do funkcji aranżacji JavaScript funkcja działania nie wymaga specjalnej konfiguracji. Dane wejściowe przekazywane do niego przez funkcję `context.bindings` koordynatora znajduje `activityTrigger` się na obiekcie `context.bindings.name`pod nazwą powiązania - w tym przypadku. Nazwa powiązania można ustawić jako parametr eksportowane funkcji i uzyskać dostęp bezpośrednio, co jest, co robi przykładowy kod.

---

### <a name="httpstart-client-function"></a>Funkcja klienta HttpStart

Można uruchomić wystąpienie funkcji koordynatora przy użyciu funkcji klienta. Użyjesz `HttpStart` funkcji wyzwalanej HTTP, aby `E1_HelloSequence`uruchomić wystąpienia programu .

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Aby wchodzić w interakcje z orchestrators, funkcja musi zawierać powiązania `DurableClient` wejściowego. Klienta można użyć do uruchomienia aranżacji. Może również pomóc w powrocie odpowiedzi HTTP zawierającej adresy URL w celu sprawdzenia stanu nowej aranżacji.

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Aby wchodzić w interakcje z orchestrators, funkcja musi zawierać powiązania `durableClient` wejściowego.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Służy `df.getClient` do `DurableOrchestrationClient` uzyskania obiektu. Klienta można użyć do uruchomienia aranżacji. Może również pomóc w powrocie odpowiedzi HTTP zawierającej adresy URL w celu sprawdzenia stanu nowej aranżacji.

---

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Aby wykonać `E1_HelloSequence` aranżację, wyślij następujące `HttpStart` żądanie HTTP POST do funkcji.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Poprzedni fragment kodu HTTP zakłada, że w `host.json` pliku znajduje się `api/` wpis, który usuwa domyślny prefiks ze wszystkich adresów URL funkcji wyzwalania HTTP. Znaczniki dla tej konfiguracji można `host.json` znaleźć w pliku w przykładach.

Jeśli na przykład uruchamiasz przykład w aplikacji funkcji o nazwie "myfunctionapp", zamień "{host}" na "myfunctionapp.azurewebsites.net".

Wynikiem jest odpowiedź HTTP 202, jak ta (przycięte dla zwięzłości):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

W tym momencie aranżacji jest w kolejce i zaczyna działać natychmiast. Adres URL `Location` w nagłówku może służyć do sprawdzania stanu wykonania.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Wynikiem jest stan aranżacji. Działa i kończy się szybko, więc widać go w *zakończonym* stanie z odpowiedzią, która wygląda tak (przycięte dla zwięzłości):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Jak widać, `runtimeStatus` wystąpienie jest *zakończone* i `output` zawiera JSON-serialized wynik wykonania funkcji aranżatora.

> [!NOTE]
> Można zaimplementować podobną logikę rozrusznika dla innych typów wyzwalaczy, takich jak `queueTrigger`, `eventHubTrigger`lub `timerTrigger`.

Spójrz na dzienniki wykonywania funkcji. Funkcja `E1_HelloSequence` została uruchomiona i ukończona wiele razy ze względu na zachowanie powtarzania opisane w temacie [niezawodności aranżacji.](durable-functions-orchestrations.md#reliability) Z drugiej strony były tylko trzy `E1_SayHello` egzekucje, ponieważ te wykonania funkcji nie są odtwarzane.

## <a name="next-steps"></a>Następne kroki

W tym przykładzie zademonstrowano prostą aranżację łańcucha funkcji. W następnym przykładzie pokazano, jak zaimplementować wzór fan-out/fan-in.

> [!div class="nextstepaction"]
> [Uruchom próbkę Wentylatora/wentylatora](durable-functions-cloud-backup.md)
