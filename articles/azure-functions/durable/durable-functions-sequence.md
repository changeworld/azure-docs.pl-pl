---
title: Łańcuch funkcji w Durable Functions — Azure
description: Dowiedz się, jak uruchomić próbkę Durable Functions, która wykonuje sekwencję funkcji.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562069"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Łańcuch funkcji w Durable Functions-Hello Sequence przykład

Łańcuch funkcji odnosi się do wzorca wykonywania sekwencji funkcji w określonej kolejności. Często dane wyjściowe jednej funkcji muszą zostać zastosowane do danych wejściowych innej funkcji. W tym artykule opisano sekwencję łańcucha utworzoną podczas wykonywania Durable Functions przewodnika Szybki Start ([C#](durable-functions-create-first-csharp.md) lub [JavaScript](quickstart-js-vscode.md)). Aby uzyskać więcej informacji na temat Durable Functions, zobacz [Durable Functions Omówienie](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funkcje

W tym artykule wyjaśniono następujące funkcje w przykładowej aplikacji:

* `E1_HelloSequence`: [Funkcja programu Orchestrator](durable-functions-bindings.md#orchestration-trigger) , która wywołuje `E1_SayHello` wiele razy w sekwencji. Przechowuje dane wyjściowe z wywołań `E1_SayHello` i rejestruje wyniki.
* `E1_SayHello`: [Funkcja działania](durable-functions-bindings.md#activity-trigger) , która dołącza ciąg do "Hello".
* `HttpStart`: funkcja wyzwalana przez protokół HTTP, która uruchamia wystąpienie programu Orchestrator.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence funkcja programu Orchestrator

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Wszystkie C# funkcje aranżacji muszą mieć parametr typu `DurableOrchestrationContext`, który istnieje w zestawie `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Ten obiekt kontekstu umożliwia wywoływanie innych funkcji *działania* i przekazywanie parametrów wejściowych przy użyciu metody `CallActivityAsync`.

Kod wywołuje `E1_SayHello` trzy razy w sekwencji z innymi wartościami parametrów. Wartość zwracana każdego wywołania jest dodawana do listy `outputs`, która jest zwracana na końcu funkcji.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> Durable Functions JavaScript są dostępne tylko dla funkcji środowiska uruchomieniowego 2,0.

#### <a name="functionjson"></a>function.json

Jeśli używasz Visual Studio Code lub Azure Portal do programowania, poniżej przedstawiono zawartość pliku *Function. JSON* dla funkcji programu Orchestrator. Większość plików *JSON funkcji* programu Orchestrator wygląda niemal dokładnie tak samo.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Ważną kwestią jest typ powiązania `orchestrationTrigger`. Wszystkie funkcje programu Orchestrator muszą używać tego typu wyzwalacza.

> [!WARNING]
> Aby przestrzegać reguły "No we/wy" funkcji programu Orchestrator, nie używaj żadnych powiązań wejściowych ani wyjściowych w przypadku używania powiązania wyzwalacza `orchestrationTrigger`.  Jeśli wymagane są inne powiązania wejściowe lub wyjściowe, powinny one być używane w kontekście funkcji `activityTrigger`, które są wywoływane przez program Orchestrator. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [ograniczeń kodu funkcji programu Orchestrator](durable-functions-code-constraints.md) .

#### <a name="indexjs"></a>index. js

Oto funkcja:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Wszystkie funkcje aranżacji języka JavaScript muszą zawierać [moduł`durable-functions`](https://www.npmjs.com/package/durable-functions). Jest to biblioteka, która umożliwia pisanie Durable Functions w języku JavaScript. Istnieją trzy znaczące różnice między funkcją aranżacji i innymi funkcjami języka JavaScript:

1. Funkcja jest [funkcją generatora.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript).
2. Funkcja jest opakowana w wywołaniu metody `orchestrator` modułu `durable-functions` (tutaj `df`).
3. Funkcja musi być synchroniczna. Ponieważ metoda "Orchestrator" obsługuje wywoływanie elementu "Context. Done", funkcja powinna po prostu zwracać.

Obiekt `context` zawiera `df` trwałego obiektu kontekstu aranżacji, który umożliwia wywoływanie innych funkcji *działania* i przekazywanie parametrów wejściowych przy użyciu metody `callActivity`. Kod wywołuje `E1_SayHello` trzy razy w sekwencji z innymi wartościami parametrów, przy użyciu `yield`, aby wskazać, że wykonanie powinno oczekiwać na zwrócenie wywołania funkcji działania asynchronicznego. Wartość zwracana każdego wywołania jest dodawana do tablicy `outputs`, która jest zwracana na końcu funkcji.

---

### <a name="e1_sayhello-activity-function"></a>Funkcja działania E1_SayHello

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Działania używają atrybutu `ActivityTrigger`. Użyj podanej `IDurableActivityContext` do wykonania akcji związanych z działaniami, takich jak uzyskiwanie dostępu do wartości wejściowej przy użyciu `GetInput<T>`.

Implementacja `E1_SayHello` jest stosunkowo uproszczoną operacją formatowania ciągu.

Zamiast tworzyć powiązania do `IDurableActivityContext`, można powiązać bezpośrednio z typem, który jest przesyłany do funkcji działania. Na przykład:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/Function.JSON

Plik *Function. JSON* dla funkcji działania `E1_SayHello` jest podobny do `E1_HelloSequence`, z tą różnicą, że używa typu powiązania `activityTrigger` zamiast typu powiązania `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Każda funkcja wywołana przez funkcję aranżacji musi używać powiązania `activityTrigger`.

Implementacja `E1_SayHello` jest stosunkowo uproszczoną operacją formatowania ciągu.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

W przeciwieństwie do funkcji aranżacji języka JavaScript funkcja działania nie wymaga żadnej specjalnej konfiguracji. Dane wejściowe przesłane przez funkcję programu Orchestrator znajdują się na obiekcie `context.bindings` pod nazwą powiązania `activityTrigger` — w tym przypadku `context.bindings.name`. Nazwę powiązania można ustawić jako parametr funkcji wyeksportowanej i uzyskać do niej dostęp bezpośrednio, czyli do czego służy przykładowy kod.

---

### <a name="httpstart-client-function"></a>HttpStart — funkcja klienta

Wystąpienie funkcji programu Orchestrator można uruchomić przy użyciu funkcji klienta. Funkcja wyzwalana przez funkcję `HttpStart` protokołu HTTP zostanie użyta do uruchomienia wystąpień `E1_HelloSequence`.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Aby można było korzystać z programu Orchestrator, funkcja musi zawierać `DurableClient` dane wejściowe. Używasz klienta do uruchomienia aranżacji. Może również pomóc zwrócić odpowiedź HTTP zawierającą adresy URL w celu sprawdzenia stanu nowej aranżacji.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/Function. JSON

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Aby można było korzystać z programu Orchestrator, funkcja musi zawierać `durableClient` dane wejściowe.

#### <a name="httpstartindexjs"></a>HttpStart/index. js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Użyj `df.getClient`, aby uzyskać obiekt `DurableOrchestrationClient`. Używasz klienta do uruchomienia aranżacji. Może również pomóc zwrócić odpowiedź HTTP zawierającą adresy URL w celu sprawdzenia stanu nowej aranżacji.

---

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Aby wykonać aranżację `E1_HelloSequence`, wyślij następujące żądanie HTTP POST do funkcji `HttpStart`.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> W poprzednim fragmencie kodu HTTP założono, że w pliku `host.json` znajduje się wpis, który usuwa domyślny prefiks `api/` ze wszystkich adresów URL funkcji wyzwalacza HTTP. Znaczniki tej konfiguracji można znaleźć w pliku `host.json` w przykładach.

Jeśli na przykład używasz przykładu w aplikacji funkcji o nazwie "myfunctionapp", Zastąp ciąg "{host}" opcją "myfunctionapp.azurewebsites.net".

Wynikiem jest odpowiedź HTTP 202, na przykład (przycięty do zwięzłości):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

W tym momencie aranżacja została umieszczona w kolejce i rozpocznie się natychmiastowo. Adresu URL w nagłówku `Location` można użyć do sprawdzenia stanu wykonania.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Wynikiem jest stan aranżacji. Jest ona uruchamiana i trwa szybko, więc zobaczysz ją w stanie *ukończenia* z odpowiedzią, która wygląda następująco (przycięty do zwięzłości):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Jak widać, `runtimeStatus` wystąpienia zostanie *ukończona* , a `output` zawiera wynik serializacji JSON wykonywania funkcji programu Orchestrator.

> [!NOTE]
> Możesz zaimplementować podobną, rozglądającą logikę dla innych typów wyzwalaczy, takich jak `queueTrigger`, `eventHubTrigger`lub `timerTrigger`.

Sprawdź dzienniki wykonywania funkcji. Funkcja `E1_HelloSequence` została uruchomiona i wykonana wiele razy z powodu zachowania odtwarzania opisanej w temacie [niezawodności aranżacji](durable-functions-orchestrations.md#reliability) . Z drugiej strony istniały tylko trzy wykonania `E1_SayHello`, ponieważ te wykonania funkcji nie są odtwarzane.

## <a name="next-steps"></a>Następne kroki

Ten przykład pokazuje prostą aranżację łańcucha funkcji. Następny przykład pokazuje, jak zaimplementować wzorzec wentylatorów/wentylatorów.

> [!div class="nextstepaction"]
> [Uruchamianie przykładowego Wentylatoru/wentylatoru](durable-functions-cloud-backup.md)
