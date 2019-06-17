---
title: Monitory w funkcje trwałe - Azure
description: Dowiedz się, jak zaimplementować Monitora stanu, za pomocą rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 9d5e06c3d72d87a87b41a52ed4df369ebc04dccd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387091"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Monitorowanie scenariusza w funkcje trwałe — przykład obserwatora pogody

Wzorzec monitora dotyczy elastyczne *cyklicznego* procesu w przepływie pracy — na przykład sondowania, dopóki nie zostaną spełnione określone warunki. W tym artykule opisano przykładowy, który używa [funkcje trwałe](durable-functions-overview.md) do zaimplementowania monitorowania.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Omówienie scenariusza

W tym przykładzie monitoruje warunki pogodowe z bieżącej lokalizacji i powiadamia użytkownika za pomocą programu SMS, gdy skies są jasno. Aby sprawdzić dane pogody i wysyłania alertów, można użyć regularnych funkcji wyzwalanej przez czasomierz. Jednak jeden problem tego podejścia jest **Zarządzanie okresem istnienia**. Jeśli tylko jeden alert powinny być przesyłane, monitor musi sam się wyłączy po wyczyść wykryciu o pogodzie. Wzorzec monitorowania może zakończyć swój własny wykonywania, pozwala między innymi:

* Monitory, uruchom w odstępach czasu nie planuje: wyzwalacz czasomierza *uruchamia* co godzinę; monitor *czeka* jednej godziny między akcjami. Monitorowanie akcji zostanie nakłada się na o ile nie określono, które mogą być ważne w przypadku długotrwałych zadań.
* Monitorami mogą być interwałów dynamiczne: czas oczekiwania, można zmienić w zależności od jakiegoś warunku.
* Monitory można zakończyć, gdy jakiś warunek jest spełniony, lub zostać przerwane przez inny proces.
* Monitory może przyjąć parametry. Przykład pokazuje, jak można zastosować ten sam proces monitorowania pogody do żądanej lokalizacji, a numer telefonu.
* Monitory są skalowalne. Ponieważ każdy monitor jest wystąpieniem aranżacji, bez konieczności tworzenia nowych funkcji lub zdefiniuj więcej kodu można utworzyć wiele monitorów.
* Monitory łatwo zintegrować większych przepływów pracy. Monitor może być jedną sekcję bardziej złożonych funkcji aranżacji, lub [podrzędnych aranżacji](durable-functions-sub-orchestrations.md).

## <a name="configuring-twilio-integration"></a>Konfigurowanie integracji usługi Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Konfigurowanie integracji podziemny pogody

Ten przykład obejmuje przy użyciu podziemny interfejsu API pogody, aby sprawdzić bieżące warunki pogodowe dla lokalizacji.

Pierwszą rzeczą, jaką należy to konto pod ziemią o pogodzie. Utworzyć jedno za darmo na [ https://www.wunderground.com/signup ](https://www.wunderground.com/signup). Po utworzeniu konta usługi, należy uzyskać klucz interfejsu API. Możesz to zrobić, odwiedzając [ https://www.wunderground.com/weather/api ](https://www.wunderground.com/weather/api/?MR=1), a następnie wybierając pozycję Ustawienia klucza. Plan Stratus Developer jest programem bezpłatnym i wystarczające do uruchomienia tego przykładu.

Po utworzeniu klucza interfejsu API, Dodaj następujący kod **ustawienia aplikacji** do aplikacji funkcji.

| Nazwa ustawienia aplikacji | Wartość Opis |
| - | - |
| **WeatherUndergroundApiKey**  | Klucz podziemny interfejsu API pogody. |

## <a name="the-functions"></a>Funkcje

W tym artykule opisano następujące funkcje w przykładowej aplikacji:

* `E3_Monitor`: Funkcja orkiestratora, która wywołuje `E3_GetIsClear` okresowo. Wywołuje `E3_SendGoodWeatherAlert` Jeśli `E3_GetIsClear` zwraca wartość true.
* `E3_GetIsClear`: Funkcja działanie, która sprawdza, czy bieżące warunki pogodowe dla lokalizacji.
* `E3_SendGoodWeatherAlert`: Funkcja działanie, która wysyła wiadomość SMS za pośrednictwem usługi Twilio.

W poniższych sekcjach opisano konfigurację i kod, które są używane dla skryptów języka C# i JavaScript. Kod dla rozwoju programu Visual Studio jest wyświetlany na końcu tego artykułu.

## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Pogoda monitorowania aranżacji (kod przykładowy portal programu Visual Studio Code i platformy Azure)

**E3_Monitor** funkcja używa standardowych *function.json* dla funkcji programu orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Poniżej przedstawiono kod, który implementuje funkcję:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

Ta funkcja programu orchestrator wykonuje następujące czynności:

1. Pobiera **MonitorRequest** składający się z *lokalizacji* do monitorowania i *numer telefonu* do którego wysyła wiadomość SMS z powiadomieniem.
2. Określa czas wygaśnięcia monitora. W przykładzie użyto ustaloną wartość w celu skrócenia programu.
3. Wywołania **E3_GetIsClear** ustalenie, czy istnieją wyraźne skies w żądanej lokalizacji.
4. Jeśli nie zostanie zaznaczone dane pogody, wywołuje metodę **E3_SendGoodWeatherAlert** wysłać powiadomienie SMS pod numer telefonu żądanej.
5. Tworzy trwałe czasomierza, aby wznowić aranżacji na kolejnego interwału sondowania. W przykładzie użyto ustaloną wartość w celu skrócenia programu.
6. Będzie kontynuował działanie aż do [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (C#) lub `currentUtcDateTime` (JavaScript) kończy się powodzeniem czas wygaśnięcia monitora lub alertu SMS są wysyłane.

Można jednocześnie uruchomić wiele wystąpień programu orchestrator, wysyłając wielu **MonitorRequests**. Można określić lokalizacji do monitorowania i numer telefonu do wysyłania alertu SMS.

## <a name="strongly-typed-data-transfer-net-only"></a>Transfer danych silnie typizowane (tylko platforma .NET)

Orchestrator wymaga wielu fragmentów danych, więc [udostępnionych obiektów POCO](../functions-reference-csharp.md#reusing-csx-code) są używane do transferu silnie typizowanych danych w języku C# i skrypt języka C#:  
[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

Przykład JavaScript używa regularnego obiekty JSON jako parametry.

## <a name="helper-activity-functions"></a>Pomocnik działania funkcji

Jak z innymi przykładami działania funkcji pomocnika funkcji regularnych, które używają `activityTrigger` wyzwolić powiązania. **E3_GetIsClear** funkcja pobiera bieżące warunki pogodowe przy użyciu interfejsu API pod ziemią pogody i określa, czy nie ma jest wyczyszczone. *Function.json* jest zdefiniowana w następujący sposób:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

A Oto wdrożenia. POCOs używanej do transferu danych, np. logikę w celu obsługi interfejsu API wywołania i przeanalizować odpowiedzi, który JSON jest wyodrębniony do udostępnionego klasy w języku C#. Można je znaleźć w ramach [programu Visual Studio przykładowy kod](#run-the-sample).

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

**E3_SendGoodWeatherAlert** funkcja używa powiązania usługi Twilio, aby wysłać wiadomość SMS, powiadamianie użytkownika końcowego jest dobry moment na przeszukiwania. Jego *function.json* jest prosty:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

A Oto kod, który wysyła wiadomość SMS:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Korzystając z funkcji wyzwalanej przez HTTP, zawarte w przykładzie, można uruchomić aranżacji, wysyłając następujące żądanie HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

**E3_Monitor** wystąpienie rozpoczyna się i wykonuje kwerendę bieżące warunki pogodowe dla żądanej lokalizacji. Jeśli pogody nie zostanie zaznaczone, wywołuje funkcję działania, by wysyłały alert; w przeciwnym wypadku ustawia czasomierz. Po upływie okresu działania czasomierza aranżacji zostanie wznowiona.

Widać, że dzienniki aktywności aranżacji, analizując funkcji w portalu usługi Azure Functions.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

Orchestration będzie [zakończyć](durable-functions-instance-management.md) po jego limit czasu jest osiągnięto lub wyczyść pole wyboru skies są wykrywane. Można również użyć `TerminateAsync` (.NET) lub `terminate` (JavaScript) innej funkcji lub wywołaj **terminatePostUri** elementu webhook POST protokołu HTTP, do którego odwołuje się odpowiedzi 202 powyżej, zastępując `{text}` o przyczynie Zakończenie:

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Kod przykładowy w usłudze Visual Studio

Oto aranżacji jako pojedynczy plik języka C# w projekcie programu Visual Studio:

> [!NOTE]
> Musisz zainstalować `Microsoft.Azure.WebJobs.Extensions.Twilio` pakiet Nuget do uruchamiania kodu przykładu poniżej.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Kolejne kroki

W tym przykładzie ma pokazano sposób używania trwałe funkcje do monitorowania stanu zewnętrznego źródła przy użyciu [trwałe czasomierzy](durable-functions-timers.md) i logiki warunkowej. Następny przykład ilustruje sposób używania zdarzenia zewnętrzne i [trwałe czasomierzy](durable-functions-timers.md) do obsługi interakcji ludzkiej.

> [!div class="nextstepaction"]
> [Uruchom aplikację przykładową z reakcji człowieka](durable-functions-phone-verification.md)
