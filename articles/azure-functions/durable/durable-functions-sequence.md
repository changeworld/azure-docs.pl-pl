---
title: Funkcja łańcucha w funkcje trwałe - Azure
description: Dowiedz się, jak uruchomić przykładowe funkcje trwałe, wykonującą sekwencję funkcji.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4657bd136592c66b5dab9a712f5f1d6df898876c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730547"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Funkcja łańcucha w funkcje trwałe — przykładowy sekwencja Hello

Funkcja łańcucha odnosi się do wzorca wykonywanie sekwencji funkcji w określonej kolejności. Często dane wyjściowe jedna funkcja musi zostać zastosowana na dane wejściowe innej funkcji. W tym artykule opisano łańcucha sekwencji, utworzony po ukończeniu tego przewodnika Szybki Start funkcje trwałe ([ C# ](durable-functions-create-first-csharp.md) lub [JavaScript](quickstart-js-vscode.md)). Aby uzyskać więcej informacji na temat funkcje trwałe, zobacz [wzorce funkcje trwałe i zagadnienia techniczne](durable-functions-concepts.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funkcje

W tym artykule opisano następujące funkcje w przykładowej aplikacji:

* `E1_HelloSequence`: Funkcja orkiestratora, która wywołuje `E1_SayHello` wiele razy w sekwencji. Przechowuje dane wyjściowe z `E1_SayHello` wywołuje i rejestruje wyniki.
* `E1_SayHello`: Funkcja działanie, która dołącza ciąg "Hello".

W poniższych sekcjach opisano konfigurację i kod, które są używane dla skryptów języka C# i JavaScript. Kod dla rozwoju programu Visual Studio jest wyświetlany na końcu tego artykułu.

> [!NOTE]
> Trwałe funkcje języka JavaScript są dostępne dla funkcji Środowisko uruchomieniowe 2.x tylko.

## <a name="e1hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>Plik Function.JSON

Jeśli używasz programu Visual Studio Code lub w portalu Azure do tworzenia aplikacji, w tym miejscu znajduje się odpowiednia zawartość z *function.json* plików dla funkcji programu orchestrator. Większość orchestrator *function.json* pliki wyglądał prawie dokładnie tak jak poniżej.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Ważne jest `orchestrationTrigger` typ powiązania. Wszystkie funkcje programu orchestrator, należy użyć tego typu wyzwalacza.

> [!WARNING]
> Przestrzeganie zasad "nie we/wy" funkcje programu orchestrator, nie wszystkie dane wejściowe używane lub powiązania danych wyjściowych, korzystając z `orchestrationTrigger` wyzwolić powiązania.  Jeśli inne dane wejściowe lub powiązania danych wyjściowych są potrzebne, zamiast tego powinny być używane w kontekście `activityTrigger` funkcji, które są wywoływane przez koordynatora.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>Skrypt języka C# (kod przykładowy portal programu Visual Studio Code i platformy Azure)

Poniżej przedstawiono kod źródłowy:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Wszystkie funkcje języka C# aranżacji musi mieć parametr typu `DurableOrchestrationContext`, w którym występuje `Microsoft.Azure.WebJobs.Extensions.DurableTask` zestawu. Jeśli używasz skrypt języka C#, zestaw może znajdować się za pomocą `#r` notacji. Tego obiektu kontekstu pozwala wywoływać innych *działania* funkcje i przekaż parametry wejściowe za pomocą jego [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) metody.

Kod wywołuje `E1_SayHello` trzy razy w sekwencji z wartościami różnych parametrów. Wartość zwracana przez każde wywołanie jest dodawany do `outputs` listę, która jest zwracana na końcu funkcji.

### <a name="javascript"></a>Javascript

Poniżej przedstawiono kod źródłowy:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Musi zawierać wszystkie funkcje aranżacji JavaScript [ `durable-functions` modułu](https://www.npmjs.com/package/durable-functions). Jest to biblioteka, która umożliwia zapisanie trwałe funkcje w języku JavaScript. Istnieją trzy znaczne różnice między funkcją aranżacji i inne funkcje języka JavaScript:

1. Funkcja jest [funkcja generatora.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. Funkcja jest opakowywany w wywołaniu `durable-functions` modułu `orchestrator` — metoda (w tym miejscu `df`).
3. Funkcja musi być synchroniczne. Ponieważ metoda "orchestrator" obsługuje wywoływania context.done, funkcja należy po prostu "return".

`context` Obiekt zawiera `df` obiekt umożliwia wywoływanie innych *działania* funkcje i przekaż parametry wejściowe za pomocą jego `callActivity` metody. Kod wywołuje `E1_SayHello` trzy razy w sekwencji z wartościami różnych parametrów, za pomocą `yield` do wskazania wykonywania powinien zaczekać na wywołania funkcji działań asynchronicznych do zwrócenia. Wartość zwracana przez każde wywołanie jest dodawany do `outputs` listę, która jest zwracana na końcu funkcji.

## <a name="e1sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>Plik Function.JSON

*Function.json* plików dla funkcji działań `E1_SayHello` jest podobny do `E1_HelloSequence` z tą różnicą, że używa `activityTrigger` powiązania zamiast `orchestrationTrigger` typ powiązania.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Należy użyć dowolnej funkcji wywoływanych przez funkcję aranżacji `activityTrigger` powiązania.

Implementacja `E1_SayHello` jest stosunkowo prosta ciągiem formatowania operacji.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Funkcja ta ma parametr typu [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), która jest używana, aby uzyskać dane wejściowe, która została przekazana przez wywołanie funkcji programu orchestrator [ `CallActivityAsync<T>` ](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

W przeciwieństwie do funkcji aranżacji JavaScript funkcja działania wymaga nie specjalnej konfiguracji. Dane wejściowe przekazane do niej przy użyciu funkcji programu orchestrator znajduje się na `context.bindings` obiektu pod nazwą `activityTrigger` powiązania — w tym przypadku `context.bindings.name`. Nazwa powiązania można ustawić jako parametr wyeksportowanej funkcji i uzyskać dostęp bezpośredni, czyli co to jest przykładowy kod.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Aby wykonać `E1_HelloSequence` aranżacji, Wyślij żądanie następujących HTTP POST.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> W poprzednim fragmencie kodu HTTP przyjęto założenie, istnieje wpis w `host.json` pliku, co spowoduje usunięcie domyślnie `api/` prefiks z wszystkie adresy URL funkcji wyzwalacza HTTP. Znaczniki można znaleźć w tej konfiguracji w `host.json` pliku w przykładach.

Na przykład jeśli korzystasz z przykładu w aplikacji funkcji o nazwie "myfunctionapp", Zamień "{host}" "myfunctionapp.azurewebsites.net".

Wynik jest odpowiedź HTTP 202 następująco (spacje dla zwięzłości):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

W tym momencie orchestration jest umieszczone w kolejce i rozpoczyna się natychmiast. Adres URL w `Location` nagłówek może służyć do sprawdzania stanu wykonywania.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Wynik jest stan aranżacji. Uruchamia i kończy się szybko, dzięki czemu można przeglądać *Ukończono* stanu odpowiedzi, które wyglądają następująco (spacje dla zwięzłości):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Jak widać, `runtimeStatus` wystąpienia jest *Ukończono* i `output` zawiera wynik wykonania funkcji programu orchestrator serializacji JSON.

> [!NOTE]
> Punkt końcowy HTTP POST, który uruchomiona funkcja orkiestratora jest zaimplementowana w przykładowej aplikacji jako HTTP wyzwalanie funkcji o nazwie "HttpStart". Jak zaimplementować podobnej logiki początkowy dla innych typów wyzwalaczy `queueTrigger`, `eventHubTrigger`, lub `timerTrigger`.

Sprawdź dzienniki wykonywania funkcji. `E1_HelloSequence` Funkcji i kończy się wiele razy ze względu na zachowanie powtarzania opisane w [Przegląd](durable-functions-concepts.md). Z drugiej strony, było tylko dla trzech wykonaniami `E1_SayHello` od czasu wykonania tych funkcji nie uzyskać odtwarzany.

## <a name="visual-studio-sample-code"></a>Kod przykładowy w usłudze Visual Studio

Oto aranżacji jako pojedynczy plik języka C# w projekcie programu Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Kolejne kroki

W tym przykładzie wykazała prostego organizowania łańcucha funkcji. Następny przykład pokazuje, jak zaimplementować wzorzec fan-wyjściowego/fan-w.

> [!div class="nextstepaction"]
> [Uruchamianie aplikacji przykładowej Fan-wyjściowego/fan-w](durable-functions-cloud-backup.md)
