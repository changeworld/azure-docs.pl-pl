---
title: Monitory w funkcjach trwałe - Azure
description: Dowiedz się, jak wdrożyć przy użyciu rozszerzenia trwałe funkcji dla usługi Azure Functions monitor stanu.
services: functions
author: kashimiz
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
ms.openlocfilehash: 9cb7a076ea922b9868bd439d160aec96f044e3b6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Monitor scenariusz trwałe funkcje — przykład obserwatora pogody

Wzorzec monitor odwołuje się do elastycznych *cyklicznego* procesu w przepływie pracy — na przykład sondowania, dopóki nie zostaną spełnione określone warunki. W tym artykule opisano przykładu korzystającego z [trwałe funkcje](durable-functions-overview.md) do zaimplementowania monitorowania.

## <a name="prerequisites"></a>Wymagania wstępne

* [Instalowanie funkcji trwałe](durable-functions-install.md).
* Zakończenie [Hello sekwencji](durable-functions-sequence.md) wskazówki.

## <a name="scenario-overview"></a>Omówienie scenariusza

W tym przykładzie monitoruje pogodą bieżącej lokalizacji i powiadamia użytkownika za pomocą programu SMS, gdy skies są Wyczyść. Aby sprawdzić informacje o pogodzie i wysyłania alertów można normalne działanie wyzwalany przez czasomierz. Jednak jest jednym z problemów z tą metodą **Zarządzanie okresem istnienia**. Jeśli tylko jeden alert mają być wysyłane, monitor musi sam się wyłączy po wyczyść pogody została wykryta. Wzorzec monitorowania może zakończyć własną wykonywania, między innymi korzyści:

* Monitory, uruchom w odstępach czasu nie planuje: wyzwalacza bazującego na czasomierzu *uruchamia* co godzinę; monitor *czeka* godzinę między działaniami. Akcje monitor będzie nakłada się na o ile nie określono, może być ważne dla długotrwałych zadań.
* Monitorami mogą być dynamiczne odstępach czasu: czas oczekiwania, można zmienić w zależności od spełnienia określonego warunku.
* Monitory mogą z chwilą niektórych warunek jest spełniony lub być został przerwany przez inny proces.
* Monitory mogą przyjmować parametrów. Przykład pokazuje, jak można zastosować ten sam proces monitorowania pogody do żądanej lokalizacji, a numer telefonu.
* Monitory są skalowalne. Ponieważ każdy monitor jest wystąpieniem aranżacji, bez konieczności tworzenia nowych funkcji lub zdefiniuj więcej kodu można tworzyć wiele monitorów.
* Monitory łatwo zintegrować większych przepływów pracy. Monitor może mieć jedną sekcję bardziej złożonych funkcji aranżacji, lub [podrzędne aranżacji](https://docs.microsoft.com/azure/azure-functions/durable-functions-sub-orchestrations).

## <a name="configuring-twilio-integration"></a>Konfigurowanie integracji usługi Twilio

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Konfigurowanie integracji pogody podziemnych

Ten przykład obejmuje sprawdzanie bieżącego pogodą dla lokalizacji przy użyciu interfejsu API podziemnych pogody.

W pierwszej kolejności należy to konto pogody podziemnego. Utworzyć je bezpłatnie w [ https://www.wunderground.com/signup ](https://www.wunderground.com/signup). Po utworzeniu konta, należy uzyskać klucz interfejsu API. Możesz to zrobić, odwiedzając [ https://www.wunderground.com/weather/api ](https://www.wunderground.com/weather/api), a następnie wybranie ustawienia klucza. Plan Stratus dewelopera jest bezpłatny i wystarczające do uruchomienia tego przykładu.

Po klucz interfejsu API, Dodaj następujący **ustawienie aplikacji** do funkcji aplikacji.

| Nazwa ustawienia aplikacji | Opis wartości |
| - | - |
| **WeatherUndergroundApiKey**  | Klucz interfejsu API podziemnych pogody. |

## <a name="the-functions"></a>Funkcje

W tym artykule opisano następujące funkcje w przykładowej aplikacji:

* `E3_Monitor`: Funkcja programu orchestrator, który wywołuje `E3_GetIsClear` okresowo. Wywołuje `E3_SendGoodWeatherAlert` Jeśli `E3_GetIsClear` zwraca wartość true.
* `E3_GetIsClear`: Działanie funkcji, która sprawdza bieżący pogodą dla lokalizacji.
* `E3_SendGoodWeatherAlert`: Funkcja działanie wysyła wiadomość SMS za pośrednictwem usługi Twilio.

W poniższych sekcjach opisano konfigurację i kodu, które są używane dla języka C# skryptów. Kod dla tworzenia Visual Studio jest wyświetlany na końcu tego artykułu.
 
## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Pogody monitorowania orchestration (Visual Studio Code i Azure portal przykładowy kod)

**E3_Monitor** funkcja korzysta ze standardu *function.json* dla funkcji programu orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Oto kod, który implementuje funkcję:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

Ta funkcja orchestrator wykonuje następujące czynności:

1. Pobiera **MonitorRequest** składające się z *lokalizacji* do monitorowania i *numer telefonu* , do którego wysyła wiadomość SMS z powiadomieniem.
2. Określa czas wygaśnięcia monitora. W przykładzie użyto wartość ustalony do skrócenia.
3. Wywołania **E3_GetIsClear** do ustalenia, czy są wyczyść skies w żądanej lokalizacji.
4. Jeśli pogody nie zostanie zaznaczone, wywołuje metodę **E3_SendGoodWeatherAlert** do wysyłania powiadomień SMS pod numer telefonu żądanej.
5. Tworzy trwałe czasomierza wznowienie aranżacji w następnym interwału sondowania. W przykładzie użyto wartość ustalony do skrócenia.
6. Będzie kontynuował działanie aż do [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) przekazuje czas wygaśnięcia monitora lub alertu programu SMS są wysyłane.

Wiele wystąpień programu orchestrator może działać jednocześnie wysyłając wielu **MonitorRequests**. Można określić lokalizacji do monitorowania i numer telefonu do wysyłania alertu programu SMS.

## <a name="strongly-typed-data-transfer"></a>Transfer danych z silną kontrolą typów

Orchestrator wymaga wielu fragmentów danych, więc [udostępnionych obiektów POCO](functions-reference-csharp.md#reusing-csx-code) są używane do transferu danych jednoznacznie: [!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

## <a name="helper-activity-functions"></a>Funkcje działalności pomocy

Z innych próbek funkcje pomocnicze działania są regularnie funkcje programu wykorzystujące `activityTrigger` wyzwolenia powiązania. **E3_GetIsClear** funkcja pobiera bieżący pogodą przy użyciu interfejsu API podziemnych pogody i określa, czy niebo jest wyczyszczone. *Function.json* jest zdefiniowane w następujący sposób:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

A Oto implementacji. Podobnie jak POCOs używanych do transferu danych logiki do obsługi interfejsu API wywołań i przeanalizować odpowiedzi, który JSON jest pobieranej w udostępnionej klasie. Można go znaleźć w ramach [programu Visual Studio przykładowy kod](#run-the-sample).

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

**E3_SendGoodWeatherAlert** funkcja używa powiązania usługi Twilio do wysyłania wiadomości SMS powiadamianie użytkownika końcowego jest dobry moment na przeszukiwania. Jego *function.json* jest prosty:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

A Oto kod, który wysyła wiadomości SMS:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Używanie funkcji wyzwalanej przez HTTP wchodzących w skład próby, możesz uruchomić orchestration wysyłania następujące żądania HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "Location": { "City": "Redmond", "State": "WA" }, "Phone": "+1425XXXXXXX" }
```
```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

**E3_Monitor** wystąpienie rozpoczyna się i wysyła zapytanie do bieżącego pogodą dla żądanej lokalizacji. Jeśli pogody jest wyczyszczone, wywołuje funkcję działania do wysyłania alertu; w przeciwnym razie ustawia czasomierza. Po wygaśnięciu czasomierza orchestration zostanie wznowiona.

Można zauważyć, że działanie aranżacji, analizując funkcji logowania w portalu Azure Functions.

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

Orchestration będzie [przerwanie](durable-functions-instance-management.md#terminating-instances) po jego limit czasu jest osiągnęło lub wyczyść pole wyboru skies są wykrywane. Można również użyć `TerminateAsync` innej funkcji, lub wywołaj **terminatePostUri** elementu webhook HTTP POST, do którego odwołuje się 202 odpowiedzi powyżej, zastępując `{text}` z powodu zakończenia:

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Visual Studio przykładowy kod

Oto aranżacji jako pojedynczy plik C# w projektu programu Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Kolejne kroki

W tym przykładzie wykazała, jak za pomocą funkcji trwałe do monitorowania stanu zewnętrznego źródła przy użyciu [trwałe czasomierze](durable-functions-timers.md) i logikę warunkową. Następny przykład przedstawia sposób użycia zdarzenia zewnętrzne i [trwałe czasomierze](durable-functions-timers.md) do obsługi człowieka.

> [!div class="nextstepaction"]
> [Uruchamianie przykładowych człowieka](durable-functions-phone-verification.md)
