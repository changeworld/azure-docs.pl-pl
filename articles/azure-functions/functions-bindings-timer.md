---
title: Wyzwalacz czasomierza dla Azure Functions
description: Dowiedz się, jak używać wyzwalaczy czasomierzy w Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: azure-functions
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 77fa97cd339b6498263e12eae9cea50187493a89
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097117"
---
# <a name="timer-trigger-for-azure-functions"></a>Wyzwalacz czasomierza dla Azure Functions 

W tym artykule opisano sposób pracy z wyzwalaczami czasomierza w Azure Functions. Wyzwalacz Timer pozwala uruchamiać funkcję zgodnie z harmonogramem. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Wyzwalacz czasomierza jest dostępny w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) w wersji 2. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Wyzwalacz czasomierza jest dostępny w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) w wersji 3. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Przykład

Zobacz przykład specyficzny dla języka:

* [C#](#c-example)
* [Skryptu C# (csx)](#c-script-example)
* [F#](#f-example)
* [Java](#java-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

### <a name="c-example"></a>C#przyklad

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która jest wykonywana za każdym razem, gdy minuty mają wartość widoczną przez pięć (na przykład jeśli funkcja zaczyna się od 18:57:00, następna wydajność będzie równa 19:00:00). [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) Obiekt jest przesyłany do funkcji.

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

### <a name="c-script-example"></a>C#przykład skryptu

Poniższy przykład przedstawia powiązanie wyzwalacza czasomierza w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja zapisuje dziennik wskazujący, czy to wywołanie funkcji jest spowodowane pominiętym wystąpieniem harmonogramu. [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) Obiekt jest przesyłany do funkcji.

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

### <a name="f-example"></a>F#przyklad

Poniższy przykład przedstawia powiązanie wyzwalacza czasomierza w pliku *Function. JSON* i [ F# funkcji skryptu](functions-reference-fsharp.md) , która używa powiązania. Funkcja zapisuje dziennik wskazujący, czy to wywołanie funkcji jest spowodowane pominiętym wystąpieniem harmonogramu. [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) Obiekt jest przesyłany do funkcji.

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

### <a name="java-example"></a>Przykład Java

Następująca przykładowa funkcja wyzwala i wykonuje co pięć minut. Adnotacja w funkcji definiuje harmonogram przy użyciu tego samego formatu ciągu co [cronus.](https://en.wikipedia.org/wiki/Cron#CRON_expression) `@TimerTrigger`

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

### <a name="javascript-example"></a>Przykład JavaScript

W poniższym przykładzie pokazano powiązanie wyzwalacza czasomierza w pliku *Function. JSON* oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja zapisuje dziennik wskazujący, czy to wywołanie funkcji jest spowodowane pominiętym wystąpieniem harmonogramu. [Obiekt Timer](#usage) jest przenoszona do funkcji.

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

### <a name="python-example"></a>Przykład języka Python

Poniższy przykład używa powiązania wyzwalacza czasomierza, którego konfiguracja została opisana w pliku *Function. JSON* . Rzeczywista [funkcja języka Python](functions-reference-python.md) , która używa powiązania, jest opisana w pliku  *__init__. PR* . Obiekt przesłany do funkcji jest obiektem typu [Azure. Functions. TimerRequest](/python/api/azure-functions/azure.functions.timerrequest). Logika funkcji zapisuje w dziennikach wskazujący, czy bieżące wywołanie jest spowodowane pominiętym wystąpieniem harmonogramu. 

Oto powiązanie danych w *function.json* pliku:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Oto kod języka Python:

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

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Konstruktor atrybutu przyjmuje wyrażenie typu CRONUS lub `TimeSpan`. Można używać `TimeSpan` tylko wtedy, gdy aplikacja funkcji jest uruchomiona w planie App Service. Poniższy przykład przedstawia wyrażenie firmy CRONUS:

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
|**type** | Nie dotyczy | Musi być ustawiona na wartość "timerTrigger". Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Nie dotyczy | Musi być równa "in". Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiekt timer w kodzie funkcji. | 
|**schedule**|**ScheduleExpression**|[Wyrażenie CRONUS](#ncrontab-expressions) lub wartość [TimeSpan](#timespan) . Elementu `TimeSpan` można używać tylko w przypadku aplikacji funkcji działającej w planie App Service. Możesz umieścić wyrażenie harmonogramu w ustawieniu aplikacji i ustawić tę właściwość na nazwę ustawienia aplikacji ujętą w **%** znaki, jak w tym przykładzie: "% ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Jeśli `true`, funkcja jest wywoływana po uruchomieniu środowiska uruchomieniowego. Na przykład środowisko uruchomieniowe jest uruchamiane, gdy aplikacja funkcji zostanie wznowiona po przejściu w stan bezczynności z powodu braku aktywności. gdy aplikacja funkcji zostanie ponownie uruchomiona z powodu zmiany funkcji i gdy aplikacja funkcji jest skalowana w dół. Tak więc **runOnStartup** powinna rzadko, jeśli kiedykolwiek jest `true`ustawiona na, szczególnie w środowisku produkcyjnym. |
|**useMonitor**|**UseMonitor**|Ustaw wartość `false` lub, aby wskazać, czy harmonogram ma być monitorowany. `true` Harmonogram monitorowania utrzymuje harmonogramy, aby pomóc w zapewnieniu, że harmonogram jest prawidłowo obsługiwany nawet po ponownym uruchomieniu wystąpień aplikacji funkcji. Jeśli nie ustawiono jawnie, wartość domyślna to `true` harmonogramy, które mają interwał cyklu większy niż 1 minutę. W przypadku harmonogramów, które wyzwalają więcej niż raz na minutę `false`, wartość domyślna to.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Zalecamy ustawienie **runOnStartup** na `true` w środowisku produkcyjnym. Użycie tego ustawienia sprawia, że kod jest wykonywany w bardzo nieprzewidywalnym czasie. W niektórych ustawieniach produkcyjnych te dodatkowe wykonania mogą spowodować znacznie wyższe koszty dla aplikacji hostowanych w planach zużycia. Na przykład po włączeniu **runOnStartup** wyzwalacz jest wywoływany za każdym razem, gdy aplikacja funkcji jest skalowana. Upewnij się, że w pełni zrozumiesz zachowanie środowiska produkcyjnego przed włączeniem **runOnStartup** w środowisku produkcyjnym.   

## <a name="usage"></a>Użycie

Po wywołaniu funkcji wyzwalacza czasomierza obiekt Timer jest przenoszona do funkcji. Poniższy kod JSON to przykładowa reprezentacja obiektu Timer.

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

`IsPastDue` Właściwość jest`true` , gdy bieżące wywołanie funkcji jest późniejsze niż zaplanowana. Na przykład ponowne uruchomienie aplikacji funkcji może spowodować utratę wywołania.

## <a name="ncrontab-expressions"></a>Wyrażenia NCRONTAB 

Azure Functions rozpoznaje wyrażenia NCRONTAB przy użyciu biblioteki [NCronTab](https://github.com/atifaziz/NCrontab) . NCRONTAB exppression jest podobny do wyrażenia CRONUS, z tą różnicą, że zawiera dodatkowe szóste pole na początku do użycia dla dokładności czasu w sekundach:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Każde pole może mieć jeden z następujących typów wartości:

|Type  |Przykład  |Po wyzwoleniu  |
|---------|---------|---------|
|Określona wartość |<nobr>"0 5 * * * *"</nobr>|at hh: 05:00, gdzie HH jest co godzinę (raz na godzinę)|
|Wszystkie wartości (`*`)|<nobr>"0 * 5 * * *"</nobr>|o godzinie 5: mm: 00 codziennie, gdzie mm jest co minutę godziny (60 razy dziennie)|
|Zakres (`-` operator)|<nobr>"5-7 * * * * *"</nobr>|w hh: mm: 05, gg: mm: 06, i hh: mm: 07, gdzie hh: mm jest co minutę co godzinę (3 razy na minutę)|  
|Zestaw wartości (`,` operator)|<nobr>"5, 8, 10 * * * * *"</nobr>|w hh: mm: 05, gg: mm: 08, i hh: mm: 10, gdzie hh: mm jest co minutę co godzinę (3 razy na minutę)|
|Wartość interwału (`/` operator)|<nobr>"0 */5 * * * *"</nobr>|w hh: 05:00, gg: 10:00, gg: 15:00, i tak dalej, do hh: 55:00, gdzie HH jest co godzinę (12 razy w ciągu godziny)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>Przykłady NCRONTAB

Poniżej przedstawiono kilka przykładów wyrażeń NCRONTAB, których można użyć dla wyzwalacza czasomierza w Azure Functions.

|Przykład|Po wyzwoleniu  |
|---------|---------|
|`"0 */5 * * * *"`|co pięć minut|
|`"0 0 * * * *"`|raz w górnej części co godzinę|
|`"0 0 */2 * * *"`|co dwie godziny|
|`"0 0 9-17 * * *"`|co godzinę od 9 do 5 PM|
|`"0 30 9 * * *"`|Codziennie o godzinie 9:30|
|`"0 30 9 * * 1-5"`|at 9:30 AM każdego dnia tygodnia|
|`"0 30 9 * Jan Mon"`|o godzinie 9:30, co poniedziałek w styczniu|


### <a name="ncrontab-time-zones"></a>NCRONTAB strefy czasowe

Liczby w wyrażeniu firmy CRONUS odwołują się do daty i godziny, a nie przedziału czasu. Na przykład 5 w `hour` polu odnosi się do 5:00 am, nie co 5 godzin.

Domyślna strefa czasowa używana z wyrażeniami firmy CRONUS jest uniwersalnym czasem koordynowanym (UTC). Aby wyrażenie firmy CRONUS było oparte na innej strefie czasowej, należy utworzyć ustawienie aplikacji dla aplikacji funkcji o `WEBSITE_TIME_ZONE`nazwie. Ustaw wartość na nazwę żądanej strefy czasowej, jak pokazano w [indeksie strefy czasowej firmy Microsoft](https://technet.microsoft.com/library/cc749073). 

Na przykład *Wschodni czas standardowy* to UTC-05:00. Aby wyzwalacz czasomierza był wyzwalany codziennie o godzinie 10:00, należy użyć następującego wyrażenia NCRONTAB, które jest kontem dla strefy czasowej UTC:

```
"0 0 15 * * *"
``` 

Lub Utwórz ustawienie aplikacji dla aplikacji funkcji o nazwie `WEBSITE_TIME_ZONE` i ustaw wartość na **Wschodni czas standardowy**.  Następnie używa następującego wyrażenia NCRONTAB: 

```
"0 0 10 * * *"
``` 

Gdy używasz `WEBSITE_TIME_ZONE`, czas jest dostosowywany do zmian czasu w określonej strefie czasowej, na przykład czasu letniego. 

## <a name="timespan"></a>TimeSpan

 Elementu `TimeSpan` można używać tylko w przypadku aplikacji funkcji działającej w planie App Service.

W przeciwieństwie do wyrażenia CRONUS, `TimeSpan` wartość określa przedział czasu między poszczególnymi wywołaniami funkcji. Gdy funkcja kończy działanie po upływie określonego interwału, czasomierz natychmiast wywołuje funkcję.

`TimeSpan` Jest tociąg,któryjestmniejszyniż24.`hh:mm:ss` `hh` Gdy dwie pierwsze cyfry mają wartość 24 lub większą, format to `dd:hh:mm`. Oto kilka przykładów:

|Przykład |Po wyzwoleniu  |
|---------|---------|
|"01:00:00" | co godzinę        |
|"00:01:00"|co minutę         |
|"24:00:00" | co 24 dni        |
|"1,00:00:00" | codziennie        |

## <a name="scale-out"></a>Skalowanie w poziomie

Jeśli aplikacja funkcji jest skalowana do wielu wystąpień, tylko jedno wystąpienie funkcji wyzwalanej przez czasomierz jest uruchamiane we wszystkich wystąpieniach.

## <a name="function-apps-sharing-storage"></a>Magazyn udostępniania aplikacji funkcji

W przypadku udostępniania konta magazynu w wielu aplikacjach funkcji upewnij się, że każda aplikacja funkcji ma inną `id` wartość w pliku *host. JSON*. Możesz pominąć `id` właściwość lub ręcznie ustawić każdą `id` aplikację funkcji na inną wartość. Wyzwalacz czasomierza korzysta z blokady magazynu, aby upewnić się, że po przeskalowaniu aplikacji funkcji na wiele wystąpień będzie istnieć tylko jedno wystąpienie czasomierza. Jeśli dwie aplikacje funkcji współużytkują `id` te same i każdy z nich używa wyzwalacza czasomierza, zostanie uruchomiony tylko jeden czasomierz.

## <a name="retry-behavior"></a>Zachowanie przy ponowieniu próby

W przeciwieństwie do wyzwalacza kolejki wyzwalacz czasomierza nie ponawia próby po awarii funkcji. Gdy funkcja nie powiedzie się, nie zostanie wywołana ponownie do następnego czasu zgodnie z harmonogramem.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby uzyskać informacje o tym, co zrobić, gdy wyzwalacz czasomierza nie działa zgodnie z oczekiwaniami, zobacz temat [badanie i zgłaszanie problemów z wyzwalaczami wyzwalanymi przez funkcję Timer](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing), które nie są wyzwalane.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przejdź do przewodnika Szybki Start korzystającego z wyzwalacza czasomierza](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
