---
title: Łańcuch funkcji w Durable Functions — Azure
description: Dowiedz się, jak uruchomić próbkę Durable Functions, która wykonuje sekwencję funkcji.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ee5b18ddc734335ddac2a7d3352de0e4388f445d
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933255"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Łańcuch funkcji w Durable Functions-Hello Sequence przykład

Łańcuch funkcji odnosi się do wzorca wykonywania sekwencji funkcji w określonej kolejności. Często dane wyjściowe jednej funkcji muszą zostać zastosowane do danych wejściowych innej funkcji. W tym artykule opisano sekwencję łańcucha utworzoną podczas wykonywania Durable Functions przewodnika Szybki Start ([C#](durable-functions-create-first-csharp.md) lub [JavaScript](quickstart-js-vscode.md)). Aby uzyskać więcej informacji na temat Durable Functions, zobacz [Durable Functions Omówienie](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funkcje

W tym artykule wyjaśniono następujące funkcje w przykładowej aplikacji:

* `E1_HelloSequence`: Funkcja programu Orchestrator, która `E1_SayHello` wywołuje wiele razy w sekwencji. Zapisuje dane wyjściowe `E1_SayHello` wywołań i rejestruje wyniki.
* `E1_SayHello`: Funkcja działania, która dołącza ciąg z "Hello".

W poniższych sekcjach opisano konfigurację i kod, które są używane C# do obsługi skryptów i języka JavaScript. Kod dla projektowania programu Visual Studio znajduje się na końcu artykułu.

> [!NOTE]
> Durable Functions JavaScript są dostępne tylko w przypadku środowiska uruchomieniowego usługi Functions 2. x.

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>Function. JSON — plik

Jeśli używasz Visual Studio Code lub Azure Portal do programowania, poniżej przedstawiono zawartość pliku *Function. JSON* dla funkcji programu Orchestrator. Większość plików *JSON funkcji* programu Orchestrator wygląda niemal dokładnie tak samo.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Istotną kwestią jest `orchestrationTrigger` typ powiązania. Wszystkie funkcje programu Orchestrator muszą używać tego typu wyzwalacza.

> [!WARNING]
> Aby przestrzegać reguły "No we/wy" funkcji programu Orchestrator, nie używaj żadnych powiązań wejściowych ani wyjściowych w `orchestrationTrigger` przypadku używania powiązania wyzwalacza.  Jeśli wymagane są inne powiązania wejściowe lub wyjściowe, powinny one być używane w kontekście `activityTrigger` funkcji, które są wywoływane przez program Orchestrator.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C#skrypt (kod przykładowy Visual Studio Code i Azure Portal)

Oto kod źródłowy:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Wszystkie C# funkcje aranżacji muszą mieć parametr typu `DurableOrchestrationContext`, `Microsoft.Azure.WebJobs.Extensions.DurableTask` który istnieje w zestawie. Jeśli używasz C# skryptu, zestaw może być przywoływany przy użyciu `#r` notacji. Ten obiekt kontekstu umożliwia wywoływanie innych funkcji *działania* i przekazywanie parametrów wejściowych przy użyciu metody [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) .

Kod wywołuje `E1_SayHello` trzy razy sekwencję z różnymi wartościami parametrów. Wartość zwracana każdego wywołania jest dodawana do `outputs` listy, która jest zwracana na końcu funkcji.

### <a name="javascript"></a>Javascript

Oto kod źródłowy:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Wszystkie funkcje aranżacji języka JavaScript muszą zawierać [ `durable-functions` moduł](https://www.npmjs.com/package/durable-functions). To jest biblioteka, która umożliwia pisanie Durable Functions w języku JavaScript. Istnieją trzy znaczące różnice między funkcją aranżacji i innymi funkcjami języka JavaScript:

1. Funkcja jest [funkcją generatora.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. Funkcja jest opakowana w wywołaniu `durable-functions` `orchestrator` metody modułu (tutaj `df`).
3. Funkcja musi być synchroniczna. Ponieważ metoda "Orchestrator" obsługuje wywoływanie elementu "Context. Done", funkcja powinna po prostu zwracać.

Obiekt zawiera obiekt umożliwia wywoływanie innych `callActivity` funkcji działania i przekazywanie parametrów wejściowych przy użyciu metody. `context` `df` Kod wywołuje `E1_SayHello` trzy razy sekwencję z różnymi wartościami parametrów, przy użyciu `yield` do wskazania, że wykonanie powinno oczekiwać na zwrócenie wywołania funkcji działania asynchronicznego. Wartość zwracana każdego wywołania jest dodawana do `outputs` listy, która jest zwracana na końcu funkcji.

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>Function. JSON — plik

Plik *Function. JSON* dla funkcji `E1_SayHello` Activity jest podobny do tego, z `E1_HelloSequence` wyjątkiem tego, że `orchestrationTrigger` używa `activityTrigger` typu powiązania zamiast typu powiązania.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Każda funkcja wywołana przez funkcję aranżacji musi używać `activityTrigger` powiązania.

Implementacja `E1_SayHello` jest stosunkowo prostej operacji formatowania ciągu.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Ta funkcja ma parametr typu [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), który jest używany w celu uzyskania danych wejściowych, które zostały do niego przesłane przez wywołanie funkcji programu Orchestrator do [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

W przeciwieństwie do funkcji aranżacji języka JavaScript funkcja działania nie wymaga żadnej specjalnej konfiguracji. Dane wejściowe przesłane przez funkcję programu Orchestrator znajdują się na `context.bindings` obiekcie pod nazwą `activityTrigger` powiązania `context.bindings.name`— w tym przypadku. Nazwę powiązania można ustawić jako parametr funkcji wyeksportowanej i uzyskać do niej dostęp bezpośrednio, czyli do czego służy przykładowy kod.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Aby wykonać `E1_HelloSequence` aranżację, wyślij następujące żądanie HTTP POST.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> W poprzednim fragmencie kodu http założono, że w `host.json` pliku znajduje się wpis, który usuwa prefiks domyślny `api/` ze wszystkich adresów URL funkcji wyzwalacza http. Znaczniki tej konfiguracji można znaleźć w `host.json` pliku w przykładach.

Jeśli na przykład używasz przykładu w aplikacji funkcji o nazwie "myfunctionapp", Zastąp ciąg "{host}" opcją "myfunctionapp.azurewebsites.net".

Wynikiem jest odpowiedź HTTP 202, na przykład (przycięty do zwięzłości):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

W tym momencie aranżacja została umieszczona w kolejce i rozpocznie się natychmiastowo. Aby sprawdzić stan wykonania `Location` , można użyć adresu URL w nagłówku.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Wynikiem jest stan aranżacji. Jest ona uruchamiana i trwa szybko, więc zobaczysz ją w stanie *ukończenia* z odpowiedzią, która wygląda następująco (przycięty do zwięzłości):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Jak widać, `runtimeStatus` wystąpienie jest `output` *zakończone* i zawiera wynik Zserializowany notacją JSON wykonywania funkcji programu Orchestrator.

> [!NOTE]
> Punkt końcowy POST protokołu HTTP, który uruchomił funkcję programu Orchestrator, jest implementowany w przykładowej aplikacji jako funkcja wyzwalacza HTTP o nazwie "HttpStart". Możesz zaimplementować podobną, rozglądającą logikę dla innych `queueTrigger`typów `eventHubTrigger`wyzwalaczy, `timerTrigger`takich jak,, lub.

Sprawdź dzienniki wykonywania funkcji. Funkcja została uruchomiona i wykonana wiele razy ze względu na zachowanie odtwarzania opisane w temacie [niezawodność aranżacji.](durable-functions-orchestrations.md#reliability) `E1_HelloSequence` Z drugiej strony istniały tylko trzy wykonania `E1_SayHello` , ponieważ te wykonania funkcji nie są odtwarzane.

## <a name="visual-studio-sample-code"></a>Przykładowy kod programu Visual Studio

Oto aranżacja jako pojedynczy C# plik w projekcie programu Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Następne kroki

Ten przykład pokazuje prostą aranżację łańcucha funkcji. Następny przykład pokazuje, jak zaimplementować wzorzec wentylatorów/wentylatorów.

> [!div class="nextstepaction"]
> [Uruchamianie przykładowego Wentylatoru/wentylatoru](durable-functions-cloud-backup.md)
