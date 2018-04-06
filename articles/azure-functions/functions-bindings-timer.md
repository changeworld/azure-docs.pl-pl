---
title: Wyzwalacz czasomierza dla usługi Azure Functions
description: Zrozumienie, jak używać czasomierza Wyzwalacze w funkcji Azure.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: funkcje usługi Azure, funkcje, przetwarzania zdarzeń, dynamiczne obliczeń niekorzystającą architektury
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: ''
ms.openlocfilehash: 2bc2559dc1cf737e018895ffae61d0da0e56fc85
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="timer-trigger-for-azure-functions"></a>Wyzwalacz czasomierza dla usługi Azure Functions 

W tym artykule opisano sposób pracy z wyzwalaczy czasomierza w usługi Azure Functions. Wyzwalacz czasomierza umożliwia uruchamianie funkcji zgodnie z harmonogramem. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Pakiety

Wyzwalacz czasomierza znajduje się w [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) pakietu NuGet. Kod źródłowy dla pakietu jest w [azure-zadań webjob sdk rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>Przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) co pięć minut:

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>Przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono wyzwalacza bazującego na czasomierzu powiązanie w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja zapisuje dziennik wskazującą, czy to wywołanie funkcji jest z powodu wystąpienia brakujących harmonogramu.

W tym miejscu jest powiązanie danych *function.json* pliku:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Oto kod skryptu C#:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>Przykład F #

W poniższym przykładzie przedstawiono wyzwalacza bazującego na czasomierzu powiązanie w *function.json* pliku i [funkcji skryptu języka F #](functions-reference-fsharp.md) używającą powiązania. Funkcja zapisuje dziennik wskazującą, czy to wywołanie funkcji jest z powodu wystąpienia brakujących harmonogramu.

W tym miejscu jest powiązanie danych *function.json* pliku:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Oto kod skryptu języka F #:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>Przykład JavaScript

W poniższym przykładzie przedstawiono wyzwalacza bazującego na czasomierzu powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja zapisuje dziennik wskazującą, czy to wywołanie funkcji jest z powodu wystąpienia brakujących harmonogramu.

W tym miejscu jest powiązanie danych *function.json* pliku:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Oto kod skryptu JavaScript:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="attributes"></a>Atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Konstruktor atrybutu ma wyrażenie CRON lub `TimeSpan`. Można użyć `TimeSpan` tylko wtedy, gdy aplikacja funkcja działa na plan usługi aplikacji. W poniższym przykładzie przedstawiono wyrażenie CRON:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if (myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
 ```

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `TimerTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa "timerTrigger". Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure.|
|**direction** | Nie dotyczy | Należy wybrać opcję "w". Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiekt czasomierza w kodzie funkcji. | 
|**schedule**|**ScheduleExpression**|A [wyrażenie CRON](#cron-expressions) lub [TimeSpan](#timespan) wartość. A `TimeSpan` można używać tylko dla aplikacji funkcja, która działa na Plan usługi App Service. Można umieścić wyrażenia harmonogramu w ustawieniu aplikacji i ustawić tę właściwość na aplikacji, nazwa otoczona ustawienia **%** znaków, jak w poniższym przykładzie: "% ScheduleAppSetting %". |
|**runOnStartup**|**RunOnStartup**|Jeśli `true`, funkcja jest wywoływana po uruchomieniu środowiska uruchomieniowego. Na przykład środowiska uruchomieniowego rozpoczyna się, gdy funkcja aplikacji zostanie wznowiona po przejście w stan bezczynności z powodu nieaktywności. gdy aplikacji funkcji uruchamia się ponownie, z powodu zmian funkcji i gdy aplikacji funkcji skaluje się. Dlatego **runOnStartup** powinna rzadko Jeśli kiedykolwiek ustawić `true`, ponieważ spowoduje to, że kod wykonać w czasie wysokiej nieprzewidywalne.|
|**useMonitor**|**UseMonitor**|Ustaw `true` lub `false` wskazująca, czy harmonogram powinny być monitorowane. Harmonogram monitorowania utrzymuje wystąpień harmonogram, aby pomóc w zapewnieniu, że harmonogram jest zachowywane poprawnie, nawet w przypadku ponownego uruchomienia wystąpienia aplikacji funkcji. Jeśli nie został ustawiony jawnie, wartością domyślną jest `true` harmonogramów, które mają większy niż 1 minuta interwał cyklu. Harmonogramy, które mogą powodować więcej niż raz na minutę, wartością domyślną jest `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

Po wywołaniu funkcji wyzwalacza czasomierza [obiekt czasomierza](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) została przekazana do funkcji. Następujący kod JSON jest przykład reprezentację obiekt czasomierza. 

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

`IsPastDue` Właściwość jest `true` po jest późniejsza niż zaplanowane bieżącego wywołania funkcji. Na przykład ponowne uruchomienie aplikacji funkcji może spowodować wywołania, aby być pominięte.

## <a name="cron-expressions"></a>Wyrażenia usługi CRON 

Wyrażenie CRON wyzwalacza czasomierza usługi Azure Functions obejmuje sześć pól: 

`{second} {minute} {hour} {day} {month} {day-of-week}`

Każde pole może mieć jeden z następujących typów wartości:

|Typ  |Przykład  |Po wyzwoleniu  |
|---------|---------|---------|
|Określona wartość |<nobr>"0 5 * * * *"</nobr>|w hh:05:00, gdzie hh wynosi godzinę (godzinę)|
|Wszystkie wartości (`*`)|<nobr>"0 * 5 * * *"</nobr>|w 5:mm: 00 każdego dnia, gdzie jest mm co minutę godziny (60 razy dziennie)|
|Zakres (`-` operator)|<nobr>"5-7 * * * * *"</nobr>|hh:mm:05, hh:mm:06 i hh:mm:07, gdzie hh: mm to co minutę co godzinę (3 razy minuty)|  
|Zestaw wartości (`,` operator)|<nobr>"5,8,10 * * * * *"</nobr>|hh:mm:05, hh:mm:08 i hh:mm:10, gdzie hh: mm to co minutę co godzinę (3 razy minuty)|
|Wartość interwału (`/` operator)|<nobr>"0 */5 * * * *"</nobr>|hh:05:00, hh:10:00, hh:15:00 i tak dalej za pośrednictwem hh:55:00 gdzie hh wynosi godzinę (12 razy godziny)|

### <a name="cron-examples"></a>Przykłady usługi CRON

Oto kilka przykładów CRON wyrażeń, które można użyć wyzwalacza czasomierza w funkcji platformy Azure.

|Przykład|Po wyzwoleniu  |
|---------|---------|
|"0 */5 * * * *"|co pięć minut|
|"0 0 * * * *"|raz na początku co godzinę|
|"0 0 */2 * * *"|co dwie godziny|
|"0 0 9-17 * * *"|co godzinę z 9 AM do 17: 00|
|"0 30 9 * * *"|w 9:30 AM codziennie|
|"0 30 9 * * 1-5"|w 9:30 AM każdy dzień tygodnia|

>[!NOTE]   
>Przykłady wyrażeń CRON online można znaleźć, ale wiele z nich Pomiń `{second}` pola. Po skopiowaniu jednego z nich, Dodaj brakujące `{second}` pola. Zazwyczaj należy zero w tym polu nie gwiazdkę.

### <a name="cron-time-zones"></a>Strefy czasowe usługi CRON

Numery w wyrażeniu CRON odwoływać się do czasu i daty, nie przedział czasu. Na przykład 5 w `hour` pole odwołuje się do 5:00:00, nie co 5 godzin.

Domyślna strefa czasowa używane w wyrażeniach CRON jest uniwersalny czas koordynowany (UTC). Aby wymusić wyrażenie CRON oparte na innej strefie czasowej, Utwórz ustawienia aplikacji dla aplikacji funkcja o nazwie `WEBSITE_TIME_ZONE`. Ustaw wartość na nazwę odpowiednie strefy czasowej, jak pokazano w [indeksu strefy czasowej Microsoft](https://technet.microsoft.com/library/cc749073). 

Na przykład *wschodni czas standardowy* jest UTC-05:00. Aby Twoje czasomierza wyzwolenia fire na 10:00 CET codziennie, użyj następujących kont dla strefy czasowej UTC wyrażenie CRON:

```json
"schedule": "0 0 15 * * *",
``` 

Lub Utwórz ustawienia aplikacji dla aplikacji funkcja o nazwie `WEBSITE_TIME_ZONE` i ustaw wartość **wschodni czas standardowy**.  Następnie używa następującego wyrażenia CRON: 

```json
"schedule": "0 0 10 * * *",
``` 

## <a name="timespan"></a>TimeSpan

 A `TimeSpan` można używać tylko dla aplikacji funkcja, która działa na Plan usługi App Service.

W przeciwieństwie do wyrażenia CRON `TimeSpan` wartość Określa interwał między każdego wywołania funkcji. Po zakończeniu działania funkcji po uruchomieniu dłuższy niż określony interwał czasomierza natychmiast wywołuje funkcję ponownie.

Wyrażonej w postaci ciągu, `TimeSpan` format jest `hh:mm:ss` podczas `hh` jest mniejsza niż 24. Po pierwsze dwie cyfry są, 24 lub większą, format jest `dd:hh:mm`. Oto kilka przykładów:

|Przykład |Po wyzwoleniu  |
|---------|---------|
|"01:00:00" | Co godzinę        |
|"00:01:00"|co minutę         |
|"24:00:00" | co 24 dni        |

## <a name="scale-out"></a>Skalowanie w poziomie

Jeśli aplikacja funkcji skaluje się do wielu wystąpień, tylko jedno wystąpienie funkcji wyzwalany przez czasomierz jest uruchamiane we wszystkich wystąpieniach.

## <a name="function-apps-sharing-storage"></a>Funkcja aplikacji do udostępniania magazynu

Jeśli konto magazynu możesz udostępnić wielu aplikacjom funkcji, upewnij się, że każda aplikacja funkcji ma inną `id` w *host.json*. Można pominąć `id` właściwości lub ręcznie ustawić każdej funkcji aplikacji `id` innej wartości. Wyzwalacza czasomierza używa blokady magazynu, aby upewnić się, że będą istnieć tylko jedno wystąpienie czasomierza podczas aplikacji funkcji skaluje się do wielu wystąpień. Jeśli dwie aplikacje funkcja mają takie same `id` i każda używa wyzwalacza bazującego na czasomierzu, tylko jeden czasomierz będzie uruchamiany.

## <a name="retry-behavior"></a>Sposób ponawiania próby

W odróżnieniu od wyzwalacza kolejki wyzwalacza czasomierza nie ponownie po funkcji nie powiedzie się. W przypadku awarii funkcji go is't o nazwie ponownie do czasu zgodnie z harmonogramem.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przejdź do szybkiego startu, który korzysta z wyzwalacza bazującego na czasomierzu](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)
