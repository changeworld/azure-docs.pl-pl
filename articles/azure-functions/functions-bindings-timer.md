---
title: Wyzwalacz czasomierza dla funkcji platformy Azure
description: Dowiedz się, jak używać wyzwalaczy czasomierza w usłudze Azure Functions.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 566d6ccf43024692e19bcd6639fe5cfbbba0660d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056410"
---
# <a name="timer-trigger-for-azure-functions"></a>Wyzwalacz czasomierza dla funkcji platformy Azure 

W tym artykule wyjaśniono, jak pracować z wyzwalaczami czasomierza w usłudze Azure Functions. Wyzwalacz czasomierza umożliwia uruchamianie funkcji zgodnie z harmonogramem. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety - Funkcje 1.x

Wyzwalacz czasomierza znajduje się w pakiecie [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet w wersji 2.x. Kod źródłowy pakietu znajduje się w repozytorium GitHub w [zakresie azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/)

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2.x lub nowsze

Wyzwalacz czasomierza znajduje się w pakiecie [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet w wersji 3.x. Kod źródłowy pakietu znajduje się w repozytorium GitHub w [zakresie azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/)

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Przykład

# <a name="c"></a>[C #](#tab/csharp)

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która jest wykonywana za każdym razem, gdy minuty mają wartość podzielną przez pięć (np. jeśli funkcja rozpoczyna się o 18:57:00, następna wydajność będzie o 19:00:00). Obiekt [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) jest przekazywany do funkcji.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie wyzwalacza czasomierza w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która używa powiązania. Funkcja zapisuje dziennik wskazujący, czy wywołanie tej funkcji jest spowodowane wystąpieniem nieodebranego harmonogramu. Obiekt [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) jest przekazywany do funkcji.

Oto dane powiązania w pliku *function.json:*

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Oto kod skryptu języka C#:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza czasomierza w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania. Funkcja zapisuje dziennik wskazujący, czy wywołanie tej funkcji jest spowodowane wystąpieniem nieodebranego harmonogramu. [Obiekt czasomierza](#usage) jest przekazywany do funkcji.

Oto dane powiązania w pliku *function.json:*

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Oto kod JavaScript:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie użyto powiązania wyzwalacza czasomierza, którego konfiguracja jest opisana w pliku *function.json.* Rzeczywista [funkcja Języka Python,](functions-reference-python.md) która używa powiązania jest opisana w pliku * __.py init.__* Obiekt przekazany do funkcji jest typu [azure.functions.TimerRequest obiektu](/python/api/azure-functions/azure.functions.timerrequest). Logika funkcji zapisuje w dziennikach wskazujących, czy bieżące wywołanie jest spowodowane wystąpieniem nieodebranego harmonogramu. 

Oto dane powiązania w pliku *function.json:*

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Oto kod Pythona:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

# <a name="java"></a>[Java](#tab/java)

Poniższa przykładowa funkcja wyzwala i wykonuje co pięć minut. Adnotacja `@TimerTrigger` w funkcji definiuje harmonogram przy użyciu tego samego formatu ciągu co [wyrażenia CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj [atrybutu TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Konstruktor atrybutu przyjmuje wyrażenie CRON `TimeSpan`lub . Aplikacji można `TimeSpan` używać tylko wtedy, gdy aplikacja funkcji jest uruchomiona w planie usługi app service. `TimeSpan`nie jest obsługiwany dla zużycia lub elastycznych funkcji premium.

W poniższym przykładzie pokazano wyrażenie CRON:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Adnotacja `@TimerTrigger` w funkcji definiuje harmonogram przy użyciu tego samego formatu ciągu co [wyrażenia CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `TimerTrigger` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy | Musi być ustawiona na "timerTrigger". Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal.|
|**Kierunku** | Nie dotyczy | Musi być ustawiona na "in". Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej reprezentującej obiekt czasomierza w kodzie funkcji. | 
|**Harmonogram**|**HarmonogramWyrażenie**|Wyrażenie [CRON](#ncrontab-expressions) lub [timespan](#timespan) wartość. A `TimeSpan` może służyć tylko dla aplikacji funkcji, która działa w planie usługi aplikacji. Wyrażenie harmonogramu można umieścić w ustawieniu aplikacji i ustawić tę **%** właściwość na nazwę ustawienia aplikacji opakowane w znaki, jak w tym przykładzie: "%ScheduleAppSetting%". |
|**uruchamianie runOn**|**Uruchamianie RunOn**|Jeśli `true`funkcja jest wywoływana po uruchomieniu środowiska wykonawczego. Na przykład środowisko wykonawcze uruchamia się, gdy aplikacja funkcji wznawia się po przejściu bezczynności z powodu braku aktywności. po ponownym uruchomieniu aplikacji funkcji z powodu zmiany funkcji i gdy aplikacja funkcji jest skalowana w poziomie. Więc **runOnStartup** rzadko, jeśli `true`kiedykolwiek być ustawione na , zwłaszcza w produkcji. |
|**useMonitor (monitor)**|**UseMonitor (UseMonitor)**|Ustaw `true` lub `false` wskaż, czy harmonogram powinien być monitorowany. Monitorowanie harmonogramu będzie się powtarzać, aby ułatwić prawidłowe zapewnienie prawidłowego zachowania harmonogramu, nawet po ponownym uruchomieniu wystąpień aplikacji funkcji. Jeśli nie jest ustawiona `true` jawnie, wartość domyślna jest dla harmonogramów, które mają interwał cyklu większy lub równy 1 minuta. W przypadku harmonogramów wyzwalających więcej niż `false`raz na minutę wartość domyślna to .

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Zaleca się ustawienie **runOnStartup** do `true` w środowisku produkcyjnym. Za pomocą tego ustawienia sprawia, że kod jest wykonywany w bardzo nieprzewidywalnych czasach. W niektórych ustawieniach produkcji te dodatkowe wykonania mogą spowodować znacznie wyższe koszty dla aplikacji hostowanych w planach zużycia. Na przykład z **runOnStartup** włączone wyzwalacz jest wywoływany za każdym razem, gdy aplikacja funkcji jest skalowany. Upewnij się, że w pełni zrozumieć zachowanie produkcyjne funkcji przed włączeniem **runOnStartup** w produkcji.   

## <a name="usage"></a>Sposób użycia

Po wywołaniu funkcji wyzwalacza czasomierza obiekt czasomierza jest przekazywany do funkcji. Poniższy JSON jest przykładową reprezentacją obiektu czasomierza.

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

Właściwość `IsPastDue` `true` jest, gdy bieżące wywołanie funkcji jest później niż zaplanowano. Na przykład ponowne uruchomienie aplikacji funkcji może spowodować pominięcie wywołania.

## <a name="ncrontab-expressions"></a>Wyrażenia NCRONTAB 

Usługa Azure Functions używa biblioteki [NCronTab](https://github.com/atifaziz/NCrontab) do interpretowania wyrażeń NCRONTAB. Wyrażenie NCRONTAB jest podobne do wyrażenia CRON, z tą różnicą, że zawiera dodatkowe szóste pole na początku, które ma być używane dla precyzji czasu w sekundach:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Każde pole może mieć jeden z następujących typów wartości:

|Typ  |Przykład  |Po wyzwoleniu  |
|---------|---------|---------|
|Określona wartość |<nobr>"0 5 * * * *"</nobr>|o hh:05:00 gdzie hh jest co godzinę (raz na godzinę)|
|Wszystkie wartości`*`( )|<nobr>"0 * 5 * * *"</nobr>|codziennie o 5:mm:00, gdzie mm jest co minutę (60 razy dziennie)|
|Zakres (operator)`-`|<nobr>"5-7 * * * * *"</nobr>|w hh:mm:05,hh:mm:06 i hh:mm:07, gdzie hh:mm jest co minutę co godzinę (3 razy na minutę)|
|Zestaw wartości (operator)`,`|<nobr>"5,8,10 * * * * *"</nobr>|w hh:mm:05,hh:mm:08 i hh:mm:10 gdzie hh:mm jest co minutę co godzinę (3 razy na minutę)|
|Wartość interwału (operator)`/`|<nobr>"0 */5 * * * *"</nobr>|o hh:00:00, hh:05:00, hh:10:00 i tak dalej przez hh:55:00 gdzie hh jest co godzinę (12 razy na godzinę)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>Przykłady NCRONTAB

Oto kilka przykładów wyrażeń NCRONTAB, których można użyć dla wyzwalacza czasomierza w usłudze Azure Functions.

|Przykład|Po wyzwoleniu  |
|---------|---------|
|`"0 */5 * * * *"`|raz na pięć minut|
|`"0 0 * * * *"`|raz na górze co godzinę|
|`"0 0 */2 * * *"`|raz na dwie godziny|
|`"0 0 9-17 * * *"`|raz na godzinę od 9:00 do 17:00|
|`"0 30 9 * * *"`|o 9:30 każdego dnia|
|`"0 30 9 * * 1-5"`|o 9:30 w każdy dzień powszedni|
|`"0 30 9 * Jan Mon"`|o 9:30 w każdy poniedziałek stycznia|


### <a name="ncrontab-time-zones"></a>Strefy czasowe NCRONTAB

Liczby w wyrażeniu CRON odnoszą się do godziny i daty, a nie do przedziału czasu. Na przykład 5 w `hour` polu odnosi się do 5:00 AM, a nie co 5 godzin.

Domyślną strefą czasową używaną z wyrażeniami CRON jest skoordynowany czas uniwersalny (UTC). Aby wyrażenie CRON było oparte na innej strefie czasowej, `WEBSITE_TIME_ZONE`utwórz ustawienie aplikacji dla aplikacji funkcyjnej o nazwie . Ustaw wartość na nazwę żądanej strefy czasowej, jak pokazano w [indeksie strefy czasowej firmy Microsoft](https://technet.microsoft.com/library/cc749073).

  > [!NOTE]
  > `WEBSITE_TIME_ZONE`nie jest obecnie obsługiwana w planie zużycia linuksa.

Na przykład *wschodni czas standardowy* to UTC-05:00. Aby uruchomić wyzwalacz czasomierza codziennie o godzinie 10:00 AM EST, użyj następującego wyrażenia NCRONTAB, które uwzględnia strefę czasową UTC:

```
"0 0 15 * * *"
``` 

Możesz też utworzyć ustawienie aplikacji `WEBSITE_TIME_ZONE` dla aplikacji o nazwie funkcji i ustawić wartość na **Wschodni czas standardowy**.  Następnie używa następującego wyrażenia NCRONTAB: 

```
"0 0 10 * * *"
``` 

Podczas korzystania `WEBSITE_TIME_ZONE`z programu czas jest dostosowywany do zmian czasu w określonej strefie czasowej, takich jak czas letni. 

## <a name="timespan"></a>przedział_czasu

 A `TimeSpan` może służyć tylko dla aplikacji funkcji, która działa w planie usługi aplikacji.

W przeciwieństwie do `TimeSpan` wyrażenia CRON wartość określa przedział czasu między każdym wywołaniem funkcji. Gdy funkcja zostanie ukończona po uruchomieniu dłużej niż określony interwał, czasomierz natychmiast wywołuje funkcję ponownie.

Wyrażony jako ciąg, `TimeSpan` format `hh:mm:ss` `hh` jest, gdy jest mniejsza niż 24. Gdy pierwsze dwie cyfry mają 24 lub `dd:hh:mm`więcej, format jest . Oto kilka przykładów:

|Przykład |Po wyzwoleniu  |
|---------|---------|
|"01:00:00" | co godzinę        |
|"00:01:00"|co minutę         |
|"24:00:00" | co 24 dni        |
|"1.00:00:00" | Codziennie        |

## <a name="scale-out"></a>Skalowanie w poziomie

Jeśli aplikacja funkcji jest skalowana w poziomie do wielu wystąpień, tylko jedno wystąpienie funkcji wyzwalanej czasomierzem jest uruchamiane we wszystkich wystąpieniach.

## <a name="function-apps-sharing-storage"></a>Aplikacje funkcyjne współużytkują Magazyn

Jeśli udostępniasz konta magazynu w aplikacjach funkcji, które nie są wdrażane w usłudze aplikacji, może być konieczne jawne przypisanie identyfikatora hosta do każdej aplikacji.

| Wersja funkcji | Ustawienie                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x (i więcej)  | `AzureFunctionsWebHost__hostid`zmienna środowiskowa |
| 1.x               | `id`w *host.json*                                  |

Można pominąć wartość identyfikującą lub ręcznie ustawić konfigurację identyfikującą każdej aplikacji funkcyjnej na inną wartość.

Wyzwalacz czasomierza używa blokady magazynu, aby upewnić się, że istnieje tylko jedno wystąpienie czasomierza, gdy aplikacja funkcji skaluje się w poziomie do wielu wystąpień. Jeśli dwie aplikacje funkcji współużytkują tę samą konfigurację identyfikującą, a każda używa wyzwalacza czasomierza, uruchamia się tylko jeden czasomierz.

## <a name="retry-behavior"></a>Ponów próbę zachowania

W przeciwieństwie do wyzwalacza kolejki wyzwalacz czasomierza nie ponawia próby po awarii funkcji. Gdy funkcja nie powiedzie się, nie jest wywoływana ponownie, aż następnym razem w harmonogramie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby uzyskać informacje o tym, co zrobić, gdy wyzwalacz czasomierza nie działa zgodnie z oczekiwaniami, zobacz [Badanie i raportowanie problemów z wyzwalanym funkcjami czasomierza.](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przejdź do szybkiego startu, który używa wyzwalacza czasomierza](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach funkcji platformy Azure](functions-triggers-bindings.md)
