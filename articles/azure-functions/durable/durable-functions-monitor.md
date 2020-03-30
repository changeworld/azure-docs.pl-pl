---
title: Monitory w trwałych funkcjach — Azure
description: Dowiedz się, jak zaimplementować monitor stanu przy użyciu rozszerzenia Funkcje trwałe dla usługi Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ed92156df9d8e1e07b56cea4b1e64edee11d68d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562126"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Scenariusz monitorowania w funkcji trwałe — przykład obserwatora pogody

Wzorzec monitora odnosi się do elastycznego procesu *cyklicznego* w przepływie pracy — na przykład sondowania, dopóki nie zostaną spełnione określone warunki. W tym artykule wyjaśniono przykład, który używa [funkcji trwałe](durable-functions-overview.md) do zaimplementowania monitorowania.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Omówienie scenariusza

Ten przykład monitoruje aktualne warunki pogodowe lokalizacji i ostrzega użytkownika smsem, gdy niebo jest czyste. Można użyć zwykłej funkcji wyzwalanego czasomierza, aby sprawdzić pogodę i wysłać alerty. Jednak jednym z problemów z tym podejściem jest **zarządzanie przez całe życie**. Jeśli należy wysłać tylko jeden alert, monitor musi się wyłączyć po wykryciu jasnej pogody. Wzorzec monitorowania może zakończyć własne wykonanie, między innymi korzyści:

* Monitory są uruchamiane w odstępach czasu, a nie w harmonogramach: wyzwalacz czasomierza *jest uruchamiany* co godzinę; monitor czeka godzinę między *akcjami.* Akcje monitora nie będą się pokrywać, chyba że określono, co może być ważne dla długotrwałych zadań.
* Monitory mogą mieć interwały dynamiczne: czas oczekiwania może ulec zmianie w zależności od pewnego warunku.
* Monitory mogą zakończyć się, gdy spełniony jest jakiś warunek lub zostanie zakończony przez inny proces.
* Monitory mogą przyjmować parametry. Przykład pokazuje, jak ten sam proces monitorowania pogody można zastosować do dowolnej żądanej lokalizacji i numeru telefonu.
* Monitory są skalowalne. Ponieważ każdy monitor jest wystąpieniem aranżacji, można utworzyć wiele monitorów bez konieczności tworzenia nowych funkcji lub definiowania większej ilości kodu.
* Monitory łatwo integrują się z większymi przepływami pracy. Monitor może być jedną sekcją bardziej złożonej funkcji aranżacji lub [podaranacją.](durable-functions-sub-orchestrations.md)

## <a name="configuration"></a>Konfigurowanie

### <a name="configuring-twilio-integration"></a>Konfigurowanie integracji usługi Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

### <a name="configuring-weather-underground-integration"></a>Konfigurowanie integracji Weather Underground

Ten przykład obejmuje przy użyciu Weather Underground INTERFEJSU API, aby sprawdzić bieżące warunki pogodowe dla lokalizacji.

Pierwszą rzeczą, której potrzebujesz, jest konto Weather Underground. Można go utworzyć za [https://www.wunderground.com/signup](https://www.wunderground.com/signup)darmo w . Gdy masz konto, musisz uzyskać klucz interfejsu API. Można to zrobić, [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1)odwiedzając , a następnie wybierając ustawienia klawiszy. Plan dewelopera Stratus jest bezpłatny i wystarczający do uruchomienia tej próbki.

Po utworzeniu klucza interfejsu API dodaj następujące **ustawienie aplikacji** do aplikacji funkcji.

| Nazwa ustawienia aplikacji | Opis wartości |
| - | - |
| **PogodaPodwładniaK**  | Twój klucz INTERFEJSU API Weather Underground. |

## <a name="the-functions"></a>Funkcje

W tym artykule opisano następujące funkcje w przykładowej aplikacji:

* `E3_Monitor`: [Funkcja](durable-functions-bindings.md#orchestration-trigger) koordynatora, `E3_GetIsClear` która jest czasami wymuszana. Wywołuje, `E3_SendGoodWeatherAlert` `E3_GetIsClear` jeśli zwraca true.
* `E3_GetIsClear`: [Funkcja aktywności,](durable-functions-bindings.md#activity-trigger) która sprawdza aktualne warunki pogodowe dla lokalizacji.
* `E3_SendGoodWeatherAlert`: Funkcja działania, która wysyła wiadomość SMS za pośrednictwem usługi Twilio.

### <a name="e3_monitor-orchestrator-function"></a>funkcja E3_Monitor orkiestratora

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=41-78,97-115)]

Koordynator wymaga lokalizacji do monitorowania i numeru telefonu, aby wysłać wiadomość, do kiedy stanie się jasne w lokalizacji. Te dane są przekazywane do koordynatora `MonitorRequest` jako silnie typiwany obiekt.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Funkcja **E3_Monitor** używa standardowego *function.json* dla funkcji orkiestratora.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/function.json)]

Oto kod, który implementuje funkcję:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

---

Ta funkcja koordynatora wykonuje następujące akcje:

1. Pobiera **MonitorRequest** składający się z *lokalizacji* do monitorowania i *numer telefonu,* do którego wyśle powiadomienie SMS.
2. Określa czas wygaśnięcia monitora. W przykładzie użyto wartości zakodowane dla zwięzłości.
3. Połączenia **E3_GetIsClear,** aby ustalić, czy w żądanej lokalizacji jest czyste niebo.
4. Jeśli pogoda jest pogodna, **E3_SendGoodWeatherAlert** zadzwonić, aby wysłać powiadomienie SMS na żądany numer telefonu.
5. Tworzy czasomierz trwałe, aby wznowić aranżacji w następnym interwale sondowania. W przykładzie użyto wartości zakodowane dla zwięzłości.
6. Kontynuuje uruchamianie, dopóki bieżący czas UTC nie upuszczy czasu wygaśnięcia monitora lub zostanie wysłany alert SMS.

Wiele wystąpień orchestrator można uruchomić jednocześnie, wywołując funkcję koordynatora wiele razy. Można określić lokalizację do monitorowania i numer telefonu, do który chcesz wysłać alert SMS.

### <a name="e3_getisclear-activity-function"></a>E3_GetIsClear funkcja aktywności

Podobnie jak w przypadku innych przykładów, funkcje `activityTrigger` działania pomocnika są regularne funkcje, które używają powiązania wyzwalacza. Funkcja **E3_GetIsClear** pobiera aktualne warunki pogodowe za pomocą interfejsu API Weather Underground i określa, czy niebo jest czyste.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=80-85)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

*Function.json* jest zdefiniowany w następujący sposób:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/function.json)]

A oto realizacja.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

---

### <a name="e3_sendgoodweatheralert-activity-function"></a>E3_SendGoodWeatherAlert funkcja aktywności

Funkcja **E3_SendGoodWeatherAlert** używa powiązania usługi Twilio do wysyłania wiadomości SMS z powiadomieniem użytkownika końcowego, że jest to dobry czas na spacer.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=87-96,140-205)]

> [!NOTE]
> Należy zainstalować pakiet `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget, aby uruchomić przykładowy kod.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Jego *function.json* jest prosta:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/function.json)]

A oto kod, który wysyła wiadomość SMS:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

---

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Korzystając z funkcji wyzwalanych przez protokół HTTP zawartych w przykładzie, można rozpocząć aranżację, wysyłając następujące żądanie HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

E3_Monitor **E3_Monitor** instancja uruchamia i wysyła zapytanie o bieżące warunki pogodowe dla żądanej lokalizacji. Jeśli pogoda jest pogodna, wywołuje funkcję aktywności, aby wysłać alert; w przeciwnym razie ustawia czasomierz. Po wygaśnięciu czasomierza zostanie wznowiona aranżacja.

Możesz zobaczyć aranżacji działania, patrząc na dzienniki funkcji w witrynie Azure Functions portal.

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

Aranżacja zakończy się po osiągnięciu limitu czasu lub [wykryciu](durable-functions-instance-management.md) bezchmurne niebo. Można również `TerminateAsync` użyć (.NET) lub `terminate` (JavaScript) wewnątrz innej funkcji lub **wywołać terminatePostUri** HTTP POST webhook `{text}` odwołuje się w 202 odpowiedzi powyżej, zastępując z powodu zakończenia:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Następne kroki

W tym przykładzie pokazano, jak używać funkcji trwałych do monitorowania stanu źródła zewnętrznego przy użyciu [trwałych czasomierzy](durable-functions-timers.md) i logiki warunkowej. W następnym przykładzie pokazano, jak używać zdarzeń zewnętrznych i [czasomierzy trwałe](durable-functions-timers.md) do obsługi interakcji z człowiekiem.

> [!div class="nextstepaction"]
> [Uruchom próbkę interakcji z człowiekiem](durable-functions-phone-verification.md)
