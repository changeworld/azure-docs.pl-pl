---
title: Funkcja łańcucha w funkcjach trwałe - Azure
description: Dowiedz się, jak uruchomić przykład trwałe funkcji, który wykonuje sekwencji funkcji.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: e53b38bf336816ca670fad3ab70a43e5cc8b3437
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Funkcja łańcucha w funkcje trwałe — przykład sekwencji Hello

Funkcja łańcucha odwołuje się do wzorca wykonywanych kolejności funkcji w określonej kolejności. Często dane wyjściowe jedną funkcję musi zostać zastosowana do innej funkcji danych wejściowych. W tym artykule opisano przykładu korzystającego z [trwałe funkcje](durable-functions-overview.md) do implementowania funkcji łańcucha.

## <a name="prerequisites"></a>Wymagania wstępne

* [Instalowanie funkcji trwałe](durable-functions-install.md).

## <a name="the-functions"></a>Funkcje

W tym artykule opisano następujące funkcje w przykładowej aplikacji:

* `E1_HelloSequence`: Funkcja programu orchestrator, który wywołuje `E1_SayHello` wiele razy w sekwencji. Przechowuje dane wyjściowe z `E1_SayHello` wywołuje i rejestruje wyniki.
* `E1_SayHello`: Funkcja działanie dołącza ciąg "Hello".

W poniższych sekcjach opisano konfigurację i kodu, które są używane do skryptów C# i JavaScript. Kod dla tworzenia Visual Studio jest wyświetlany na końcu tego artykułu.

> [!NOTE]
> Trwałe funkcji jest dostępna w języku JavaScript w środowisku wykonawczym funkcje v2 tylko.

## <a name="e1hellosequence"></a>E1_HelloSequence
### <a name="functionjson-file"></a>Plik Function.JSON

Jeśli używasz programu Visual Studio Code lub w portalu Azure do tworzenia aplikacji, w tym miejscu jest zawartość *function.json* plików dla funkcji programu orchestrator. Większość orchestrator *function.json* pliki wygląda prawie dokładnie tak jak to.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Ważne jest `orchestrationTrigger` typ powiązania. Wszystkie funkcje programu orchestrator muszą używać tego typu wyzwalacza.

> [!WARNING]
> Do przestrzegania reguł "nie we/wy" funkcji programu orchestrator, nie używasz żadnych danych wejściowych lub wyjściowych powiązań, korzystając z `orchestrationTrigger` wyzwolenia powiązania.  Jeśli wymagane są powiązania danych wyjściowych innych danych wejściowych, zamiast tego powinny być używane w kontekście `activityTrigger` funkcji, które są wywoływane przez orchestrator.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>Skryptu C# (Visual Studio Code i Azure portal przykładowy kod) 

Oto kod źródłowy:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Wszystkie funkcje C# aranżacji musi mieć parametr typu `DurableOrchestrationContext`, który istnieje w `Microsoft.Azure.WebJobs.Extensions.DurableTask` zestawu. Jeśli używasz skryptu C# zestawu można odwoływać się przy użyciu `#r` notacji. Tego obiektu kontekstu umożliwia wywołanie innych *działania* funkcje i przekaż parametry wejściowe przy użyciu jego [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) metody.

Kod wywołuje `E1_SayHello` trzy razy w sekwencji o wartości różnych parametrów. Wartość zwracana każde wywołanie jest dodane do `outputs` listę, która jest zwracana na końcu funkcji.

### <a name="javascript"></a>Javascript

Oto kod źródłowy:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Obejmuje wszystkie funkcje aranżacji JavaScript `durable-functions` modułu. Jest to biblioteka języka JavaScript, który tłumaczy akcje funkcji aranżacji w jego niezawodny protokół wykonywania dla języków poza procesem. Istnieją trzy istotne różnice między funkcji aranżacji i innych funkcji JavaScript:

1. Funkcja jest [funkcja generatora.](https://docs.microsoft.com/en-us/scripting/javascript/advanced/iterators-and-generators-javascript)
2. Funkcja jest ujęte w wywołaniu `durable-functions` moduł (w tym miejscu `df`).
3. Funkcja kończy się przez wywołanie metody `return`, a nie `context.done`.

`context` Obiekt zawiera `df` obiekt umożliwia wywołanie innych *działania* funkcje i przekaż parametry wejściowe przy użyciu jego `callActivityAsync` metody. Kod wywołuje `E1_SayHello` trzy razy w sekwencji o wartości różnych parametrów, za pomocą `yield` wskazująca wykonywania ma oczekiwać na wywołania asynchroniczne działania funkcji ma zostać zwrócona. Wartość zwracana każde wywołanie jest dodane do `outputs` listę, która jest zwracana na końcu funkcji.

## <a name="e1sayhello"></a>E1_SayHello
### <a name="functionjson-file"></a>Plik Function.JSON

*Function.json* plik dla działania funkcji `E1_SayHello` jest podobny do `E1_HelloSequence` z tą różnicą, że używa `activityTrigger` powiązanie rodzaj zamiast `orchestrationTrigger` typ powiązania.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Należy użyć dowolnej funkcji wywoływanych przez funkcję aranżacji `activityTrigger` powiązania.

Implementacja `E1_SayHello` jest stosunkowo prosta ciągu formatowania operacji.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Ta funkcja ma parametr typu [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), używanym do pobrania danych wejściowych, który został przekazany do niej przez wywołanie funkcji orchestrator [ `CallActivityAsync<T>` ](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

W przeciwieństwie do funkcji aranżacji JavaScript działania funkcji JavaScript wymaga specjalnych ustawień. Dane wejściowe przekazane do niej przez funkcję programu orchestrator znajduje się na `context.bindings` obiektu pod nazwą `activitytrigger` — wiązanie w takim przypadku `context.bindings.name`. Nazwa powiązania można ustawić jako parametr wyeksportowanej funkcji i dostępne bezpośrednio, czyli co to jest przykładowy kod.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Aby wykonać `E1_HelloSequence` aranżacji, Wyślij żądanie następujące HTTP POST.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

Na przykład po uruchomieniu przykładowej aplikacji funkcji o nazwie "myfunctionapp" Zamień "{host}" "myfunctionapp.azurewebsites.net".

Wynik jest odpowiedź HTTP 202 następująco (ograniczona do skrócenia):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

W tym momencie orchestration jest w kolejce i rozpoczyna się natychmiast. Adres URL w `Location` nagłówek może służyć do sprawdzania stanu wykonywania.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Wynik jest stan orchestration. Działa i zakończeniu szybko, tak aby widoczne w *Ukończono* stanu odpowiedzi, który wygląda następująco (ograniczona do skrócenia):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Jak widać, `runtimeStatus` wystąpienia jest *Ukończono* i `output` zawiera serializacji JSON wynik wykonywania funkcji programu orchestrator.

> [!NOTE]
> Punkt końcowy HTTP POST, który uruchomić funkcji programu orchestrator jest zaimplementowana w przykładowej aplikacji jako HTTP wyzwalanie funkcji o nazwie "HttpStart". Podobne logiki początkowy dla innych typów wyzwalacza można zaimplementować tak samo, jak `queueTrigger`, `eventHubTrigger`, lub `timerTrigger`.

Sprawdź dzienniki wykonywania funkcji. `E1_HelloSequence` Funkcja rozpoczęte i zakończone wielokrotnie ze względu na zachowanie powtarzania opisane w [omówienie](durable-functions-overview.md). Z drugiej strony, brak wykonań tylko trzy `E1_SayHello` od czasu wykonania tych funkcji nie pobrać odtwarzany.

## <a name="visual-studio-sample-code"></a>Visual Studio przykładowy kod

Oto aranżacji jako pojedynczy plik C# w projektu programu Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Kolejne kroki

W tym przykładzie wykazała proste aranżacji łańcucha funkcji. Następny przykład przedstawia sposób implementacji wzorca fan-wyjściowego/fan-w. 

> [!div class="nextstepaction"]
> [Uruchamianie przykładowych Fan-wyjściowego/fan-w](durable-functions-cloud-backup.md)
