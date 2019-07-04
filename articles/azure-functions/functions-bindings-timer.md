---
title: Wyzwalacz czasomierza dla usługi Azure Functions
description: Zrozumienie, jak użyć wyzwalaczy czasomierza w usłudze Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 1a26950f355fd10d9dd502851886a8b8101d4a83
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508290"
---
# <a name="timer-trigger-for-azure-functions"></a>Wyzwalacz czasomierza dla usługi Azure Functions 

W tym artykule wyjaśniono, jak pracować z wyzwalaczami czasomierza w usłudze Azure Functions. Wyzwalacz czasomierza umożliwia uruchamianie funkcji zgodnie z harmonogramem. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Wyzwalacz czasomierza znajduje się w [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) pakietu NuGet w wersji 2.x. Kod źródłowy dla pakietu znajduje się w [zestawu sdk rozszerzenia, usługi azure webjobs w-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Wyzwalacz czasomierza znajduje się w [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) pakietu NuGet w wersji 3.x. Kod źródłowy dla pakietu znajduje się w [zestawu sdk rozszerzenia, usługi azure webjobs w-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Przykład

Zobacz przykład specyficzny dla języka:

* [C#](#c-example)
* [Skryptu C# (csx)](#c-script-example)
* [F#](#f-example)
* [Java](#java-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

### <a name="c-example"></a>Przykład w języku C#

W poniższym przykładzie przedstawiono [ C# funkcja](functions-dotnet-class-library.md) , jest wykonywane za każdym razem, minuty mają wartość podzielna przez pięć (na przykład jeśli funkcja rozpoczyna się od 18:57:00, następna wydajności będzie 19:00:00). [ `TimerInfo` ](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) Obiekt jest przekazywany do funkcji.

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

### <a name="c-script-example"></a>Przykładowy skrypt w języku C#

W poniższym przykładzie pokazano wyzwalacza czasomierza, powiązanie w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja zapisuje dziennik wskazującą, czy to wywołanie funkcji jest ze względu na wystąpienie harmonogramu brakujących. [ `TimerInfo` ](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) Obiekt jest przekazywany do funkcji.

Oto powiązanie danych w *function.json* pliku:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Poniżej przedstawiono kod skryptu języka C#:

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

### <a name="f-example"></a>F#przykład

W poniższym przykładzie pokazano wyzwalacza czasomierza, powiązanie w *function.json* pliku i [ F# skryptu funkcja](functions-reference-fsharp.md) powiązania, który używa. Funkcja zapisuje dziennik wskazującą, czy to wywołanie funkcji jest ze względu na wystąpienie harmonogramu brakujących. [ `TimerInfo` ](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) Obiekt jest przekazywany do funkcji.

Oto powiązanie danych w *function.json* pliku:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Oto F# kod skryptu:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger ) =
    if (myTimer.IsPastDue) then
        log.LogInformation("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.LogInformation(sprintf "F# function executed at %s!" now)
```

### <a name="java-example"></a>Przykładzie w języku Java

Poniższy przykład funkcja wyzwala i wykonuje co pięć minut. `@TimerTrigger` Adnotacja dla funkcji definiuje harmonogramu przy użyciu tego samego formatu ciągu jako [wyrażeń CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

### <a name="javascript-example"></a>Przykład JavaScript

W poniższym przykładzie pokazano wyzwalacza czasomierza, powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja zapisuje dziennik wskazującą, czy to wywołanie funkcji jest ze względu na wystąpienie harmonogramu brakujących. A [obiektu timer](#usage) jest przekazywany do funkcji.

Oto powiązanie danych w *function.json* pliku:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Poniżej przedstawiono kod JavaScript:

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

### <a name="python-example"></a>Przykładem w języku Python

W poniższym przykładzie użyto wyzwalacza czasomierza, powiązanie, którego konfiguracja jest opisana w *function.json* pliku. Rzeczywiste [funkce Pythonu](functions-reference-python.md) , używa powiązanie, jest opisana w  *__init__PY* pliku. Typ obiektu przekazanego do funkcji to [obiektu azure.functions.TimerRequest](/python/api/azure-functions/azure.functions.timerrequest). Logiki funkcji zapisuje w dziennikach, wskazującą, czy bieżące wywołanie jest ze względu na wystąpienie brakujących harmonogramu. 

Oto powiązanie danych w *function.json* pliku:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Poniżej przedstawiono kod języka Python:

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

## <a name="attributes"></a>Atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Konstruktor atrybutu ma wyrażenie narzędzia CRON lub `TimeSpan`. Możesz użyć `TimeSpan` tylko wtedy, gdy aplikacja funkcji jest uruchomiona na plan usługi App Service. Poniższy przykład pokazuje wyrażenie CRON:

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

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `TimerTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa "timerTrigger". Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Nie dotyczy | Musi być równa "in". Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiekt czasomierza w kodzie funkcji. | 
|**schedule**|**ScheduleExpression**|A [wyrażenie CRON](#cron-expressions) lub [TimeSpan](#timespan) wartość. A `TimeSpan` mogą służyć tylko do aplikacji funkcji, które jest uruchamiane na Plan usługi App Service. Można umieścić wyrażenia harmonogramu w ustawieniach aplikacji i ustawić tę właściwość na ustawienie Nazwa otoczona aplikacji **%** znaków, jak w poniższym przykładzie: "% ScheduleAppSetting %". |
|**runOnStartup**|**RunOnStartup**|Jeśli `true`, funkcja jest wywoływana po uruchomieniu środowiska uruchomieniowego. Na przykład środowisko uruchomieniowe rozpoczyna się, gdy aplikacja funkcji zostanie wznowiona po bezczynności z powodu braku aktywności. gdy funkcja liczby ponownych uruchomień aplikacji z powodu zmiany funkcji oraz podczas skalowania aplikacji funkcji. Dlatego **runOnStartup** powinna rzadko, jeśli nigdy nie być równa `true`, szczególnie w środowisku produkcyjnym. |
|**useMonitor**|**UseMonitor**|Ustaw `true` lub `false` do wskazania, czy harmonogram powinny być monitorowane. Harmonogram monitorowania są utrwalane wystąpień harmonogramu do pomocy w celu zapewnienia, że harmonogram jest obsługiwane poprawnie, nawet wtedy, gdy ponowne uruchomienie wystąpienia aplikacji funkcji. Jeśli nie jawnie ustawiona, wartość domyślna to `true` harmonogramów, które mają większe niż 1 minuta interwał cyklu. W przypadku harmonogramów, które mogą powodować więcej niż jeden raz na minutę, wartość domyślna to `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Zaleca się ustawienie **runOnStartup** do `true` w środowisku produkcyjnym. Przy użyciu tego ustawienia powoduje, że kod, wykonaj w godzinach wysoce nieprzewidywalne. W niektórych środowiskach produkcyjnych te dodatkowe wykonania może spowodować znacząco wyższe koszty dla aplikacji hostowanych w ramach planów zużycia. Na przykład za pomocą **runOnStartup** włączone, wyzwalacz jest wywoływane zawsze wtedy, gdy aplikacja funkcji jest skalowany. Upewnij się, w pełni zrozumieć zachowanie produkcji funkcji przed włączeniem **runOnStartup** w środowisku produkcyjnym.   

## <a name="usage"></a>Sposób użycia

Po wywołaniu funkcji wyzwalacza czasomierza obiektu czasomierza jest przekazywany do funkcji. Następujący kod JSON jest przykład reprezentację obiektu czasomierza.

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

`IsPastDue` Właściwość `true` gdy bieżącego wywołania funkcji jest późniejsza niż zaplanowane. Na przykład ponowne uruchomienie aplikacji funkcji może spowodować wywołania, aby zostać pominięci.

## <a name="cron-expressions"></a>Wyrażenia CRON 

Usługa Azure Functions korzysta [NCronTab](https://github.com/atifaziz/NCrontab) biblioteki interpretowanie wyrażeń CRON. Wyrażenie narzędzia CRON obejmuje sześć pól:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Każde pole może mieć jedną z następujących typów wartości:

|Typ  |Przykład  |Po wyzwoleniu  |
|---------|---------|---------|
|Określona wartość |<nobr>"0 5 * * * *"</nobr>|w hh:05:00, gdzie hh oznacza co godzinę (co godzinę)|
|Wszystkie wartości (`*`)|<nobr>"0 * 5 * * *"</nobr>|w 5:mm: 00 każdego dnia, gdzie jest mm co minutę godziny (60 razy dziennie)|
|Zakres (`-` operatora)|<nobr>"5-7 * * * * *"</nobr>|hh:mm:05, hh:mm:06 i hh:mm:07, gdzie hh: mm to co minutę na godzinę (3 razy minuty)|  
|Zestaw wartości (`,` operatora)|<nobr>"5,8,10 * * * * *"</nobr>|hh:mm:05, hh:mm:08 i hh:mm:10, gdzie hh: mm to co minutę na godzinę (3 razy minuty)|
|Wartość interwału (`/` operatora)|<nobr>"0 */5 * * * *"</nobr>|co hh:05:00 hh:10:00 hh:15:00 i tak dalej za pośrednictwem hh:55:00 gdzie hh oznacza co godzinę (12-krotnie godzina)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Przykłady usługi CRON

Poniżej przedstawiono kilka przykładów wyrażeń CRON, których można użyć wyzwalacza czasomierza w usłudze Azure Functions.

|Przykład|Po wyzwoleniu  |
|---------|---------|
|`"0 */5 * * * *"`|co pięć minut|
|`"0 0 * * * *"`|jeden raz w górnej części co godzinę|
|`"0 0 */2 * * *"`|co dwie godziny|
|`"0 0 9-17 * * *"`|co godzinę od 9: 00 do 17: 00|
|`"0 30 9 * * *"`|o 9:30:00 każdego dnia|
|`"0 30 9 * * 1-5"`|o 9:30 w każdy dzień tygodnia|
|`"0 30 9 * Jan Mon"`|o 9:30 w każdy poniedziałek w styczniu|
>[!NOTE]   
>Można znaleźć przykłady wyrażeń CRON w trybie online, ale wiele z nich Pomiń `{second}` pola. W przypadku kopiowania z jednego z nich, Dodaj brakujący `{second}` pola. Zazwyczaj chcesz zero, w tym polu nie gwiazdki.

### <a name="cron-time-zones"></a>Strefy czasowe CRON

Liczby w postaci wyrażenia CRON dotyczą godziny i daty, nie przedział czasu. Na przykład 5 w `hour` pole odwołuje się do 5:00:00, nie co 5 godzin.

Domyślna strefa czasowa używane za pomocą wyrażeń CRON jest uniwersalny czas koordynowany (UTC). Aby wyrażenie CRON oparte na innej strefie czasowej, należy utworzyć ustawienia aplikacji dla aplikacji funkcji o nazwie `WEBSITE_TIME_ZONE`. Ustaw wartość na nazwę żądanej strefa czasowa, jak pokazano na [spis produktów Microsoft strefy czasowej](https://technet.microsoft.com/library/cc749073). 

Na przykład *wschodni czas standardowy* jest UTC-05:00. Zapewnienie usługi czasomierza wyzwalacza fire w 10:00 EST codziennie, użyj następującego wyrażenia CRON, które konta dla strefy czasowej UTC:

```json
"schedule": "0 0 15 * * *"
``` 

Lub Utwórz ustawienia aplikacji dla aplikacji funkcji o nazwie `WEBSITE_TIME_ZONE` i ustaw wartość **wschodni czas standardowy**.  Następnie używa następującego wyrażenia CRON: 

```json
"schedule": "0 0 10 * * *"
``` 

Kiedy używasz `WEBSITE_TIME_ZONE`, czas jest uwzględniany czas zmiany w określonej strefie czasowej, takie jak czas letni. 

## <a name="timespan"></a>TimeSpan

 A `TimeSpan` mogą służyć tylko do aplikacji funkcji, które jest uruchamiane na Plan usługi App Service.

W odróżnieniu od wyrażenie narzędzia CRON `TimeSpan` wartość Określa odstęp czasu między każdego wywołania funkcji. Po zakończeniu funkcji po uruchomieniu dłuższy niż określony interwał, czasomierz natychmiast wywołuje funkcję ponownie.

Wyrażonej w postaci ciągu, `TimeSpan` format `hh:mm:ss` podczas `hh` wynosi mniej niż 24. Po pierwsze dwie cyfry są mniejsze niż 24, format jest `dd:hh:mm`. Oto kilka przykładów:

|Przykład |Po wyzwoleniu  |
|---------|---------|
|"01:00:00" | co godzinę        |
|"00:01:00"|co minutę         |
|"24:00:00" | codziennie        |

## <a name="scale-out"></a>Skalowanie w poziomie

Jeśli aplikacja funkcji skalowania do wielu wystąpień, tylko jedno wystąpienie funkcji wyzwalanej przez czasomierz zostanie uruchomiony we wszystkich wystąpieniach.

## <a name="function-apps-sharing-storage"></a>Aplikacje funkcji udostępniania magazynu

Jeśli udostępniasz konta magazynu przez wiele aplikacji funkcji, upewnij się, że każda aplikacja funkcji ma inną `id` w *host.json*. Możesz pominąć `id` właściwości lub ręcznym ustawieniu każda aplikacja funkcji `id` na inną wartość. Wyzwalacz czasomierza stosowana jest blokada, magazynu, aby upewnić się, że będą istnieć tylko jedno wystąpienie czasomierza w przypadku aplikacji funkcji skalowania do wielu wystąpień. Jeśli dwie aplikacje funkcji współużytkować ten sam `id` i każdy z nich korzysta z wyzwalacza czasomierza, uruchomi timer tylko jeden.

## <a name="retry-behavior"></a>Zachowanie przy ponowieniu próby

W odróżnieniu od wyzwalacz kolejki wyzwalacza czasomierza nie Ponów, gdy funkcja kończy się niepowodzeniem. W przypadku niepowodzenia funkcja nie jest wywoływana ponownie aż do następnego zgodnie z harmonogramem.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby uzyskać informacje o tym, co zrobić, gdy wyzwalacz czasomierza nie działa zgodnie z oczekiwaniami, zobacz [Investigating i zgłaszania problemów dotyczących z czasomierzem wyzwalane funkcji nie](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przejdź do przewodnika Szybki Start, która korzysta z wyzwalacza czasomierza](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
