---
title: Jak uruchomić trwałe działa jako zadań Webjob - Azure
description: Dowiedz się, jak kod i skonfigurować trwałe funkcje do uruchomienia w zadań Webjob przy użyciu zestawu SDK zadań Webjob.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 3fc84d1492d2855ffa3bb5538226da049a928339
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33767322"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Jak uruchomić trwałe działa jako zadań Webjob

[Środowisko Azure Functions](functions-overview.md) i [trwałe funkcji](durable-functions-overview.md) rozszerzenia są tworzone [zestaw SDK zadań Webjob](../app-service/web-sites-create-web-jobs.md). `JobHost` w zestawie SDK zadań Webjob jest środowiska uruchomieniowego w funkcji platformy Azure. Jeśli potrzebujesz kontroli `JobHost` zachowanie w sposób nie jest możliwe w usługi Azure Functions, mogą tworzyć i uruchamiać funkcje trwałe przy użyciu zestawu SDK WebJobs samodzielnie. Następnie możesz uruchomić trwałe funkcji w zadań WebJob Azure lub gdziekolwiek uruchamia aplikacji konsoli.

CBC przykład trwałe funkcji jest dostępna w wersji zestawu SDK zadań Webjob: pobrać lub sklonować [repozytorium trwałe funkcji](https://github.com/azure/azure-functions-durable-extension/) i przejdź do *przykłady\\webjobssdk\\łańcucha* folderu.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że znasz podstawowe informacje dotyczące zestawu SDK zadań Webjob, C# klasy biblioteki programowanie dla usługi Azure Functions i trwałe funkcji. Jeśli konieczne jest wprowadzenie do tych tematów, zobacz następujące zasoby:

* [Wprowadzenie do zestawu SDK zadań Webjob](../app-service/webjobs-sdk-get-started.md)
* [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio](functions-create-your-first-function-visual-studio.md)
* [Trwałe funkcji](durable-functions-sequence.md)

Aby wykonać kroki opisane w tym artykule:

* [Zainstaluj program Visual Studio 2017 wersji 15.6 lub nowszej](https://docs.microsoft.com/visualstudio/install/) z **Azure programowanie** obciążenia.

  Jeśli już masz program Visual Studio, ale nie ma tego obciążenia, obciążenie dodawać przez zaznaczenie **Narzędzia > Pobierz narzędzia i funkcje**. 

  (Można użyć [Visual Studio Code](https://code.visualstudio.com/) , ale niektóre instrukcji są specyficzne dla programu Visual Studio.)

* Zainstaluj i uruchom [emulatora magazynu Azure](../storage/common/storage-use-emulator.md) w wersji 5.2 lub nowszej. Alternatywą jest aktualizacja *App.config* pliku przy użyciu parametrów połączenia usługi Azure Storage.

## <a name="webjobs-sdk-versions"></a>Wersje zestawu SDK zadań Webjob

W tym artykule wyjaśniono, jak utworzyć projekt 2.x zestaw SDK zadań Webjob (odpowiednikiem usługi Azure Functions wersja 1.x). Aby uzyskać informacje o wersji 3.x, zobacz [zestaw SDK zadań Webjob 3.x](#webjobs-sdk-3x) dalszej części tego artykułu. 

## <a name="create-console-app"></a>Tworzenie aplikacji konsoli

Zestaw SDK zadań Webjob projekt jest właśnie projekt aplikacji konsoli z odpowiednie pakiety NuGet zainstalowane.

W programie Visual Studio **nowy projekt** okno dialogowe, wybierz opcję **klasycznego pulpitu systemu Windows > Aplikacja konsoli (.NET Framework)**. W pliku projektu `TargetFrameworkVersion` powinien być `v4.6.1`.

Visual Studio ma również szablonu projektu zadania WebJob, którego można użyć, wybierając **chmury > zadań WebJob Azure (.NET Framework)**. Ten szablon instaluje wiele pakietów, z których część może nie być konieczny.

## <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

Należy pakietów NuGet dla zestawu SDK zadań Webjob, powiązania core struktury rejestrowania i rozszerzenie zadania trwałe. Poniżej przedstawiono **Konsola Menedżera pakietów** polecenia dla tych pakietów z numerami najnowsza stabilna wersja dniu ten artykuł dotyczy:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Należy również rejestrowanie dostawców. Następujące polecenia Zainstaluj dostawcę usługi Application Insights i `ConfigurationManager`. `ConfigurationManager` Pozwala uzyskać klucz Instrumentacji usługi Application Insights z ustawień aplikacji.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Polecenie instaluje dostawcę konsoli:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Kod JobHost

Aby użyć rozszerzenia funkcji trwałe, należy wywołać `UseDurableTask` na `JobHostConfiguration` obiektu w Twojej `Main` — metoda:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Aby uzyskać listę właściwości, które można ustawić w `DurableTaskExtension` obiektów, zobacz [host.json](functions-host-json.md#durabletask).

`Main` Metoda jest również miejsce, aby skonfigurować rejestrowanie dostawców. Poniższy przykład konfiguruje konsoli i dostawców usługi Application Insights.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Funkcje

Brak niewielkie różnice w tworzonego dla funkcji zestawu SDK zadań Webjob w porównaniu do zapisu dla usługi Azure Functions kodu.

Zestaw SDK zadań Webjob nie obsługuje następujące funkcje usługi Azure Functions:

* [Atrybut FunctionName](#functionname-attribute)
* [Wyzwalacz protokołu HTTP](#http-trigger)
* [Trwałe interfejsu API zarządzania funkcje HTTP](#http-management-api)

### <a name="functionname-attribute"></a>Atrybut FunctionName

W projekcie zestaw SDK zadań Webjob nazwę metody funkcji jest nazwą funkcji. `FunctionName` Atrybut jest używany tylko w przypadku usługi Azure Functions.

### <a name="http-trigger"></a>Wyzwalacz HTTP

Zestaw SDK zadań Webjob nie ma wyzwalacza HTTP. Przykładowy projekt aranżacji klient korzysta z wyzwalacza bazującego na czasomierzu:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>Interfejs API zarządzania HTTP

Ponieważ go nie ma HTTP wyzwalacza, zestaw SDK zadań Webjob nie ma [interfejsu API zarządzania HTTP](durable-functions-http-api.md).

W projekcie zestaw SDK zadań Webjob można wywoływać metod w obiekcie klienta aranżacji, zamiast wysyłać żądania HTTP. Następujące metody odpowiadają trzy zadania, które można wykonać za pomocą interfejsu API zarządzania HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Funkcja klienta aranżacji w przykładowy projekt uruchamiania funkcji programu orchestrator i przejdzie do pętli, które wywołuje `GetStatusAsync` co 2 sekundy:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Ta sekcja zawiera omówienie sposobu uruchamiania [przykładowy projekt](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Aby uzyskać szczegółowe instrukcje, które wyjaśniono, jak uruchomić lokalnie projekt zestawu SDK zadań Webjob i wdrożyć ją do zadań WebJob Azure, zobacz [wprowadzenie do zestawu SDK WebJobs](../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Uruchamianie lokalnie

1. Upewnij się, że jest uruchomiony emulator magazynu (zobacz [wymagania wstępne](#prerequisites)).

1. Jeśli chcesz wyświetlić dzienniki w usłudze Application Insights po uruchomieniu lokalnie:

  a. Tworzenie zasobu usługi Application Insights, typ aplikacji **ogólne**.

  b. Zapisz klucza Instrumentacji w *App.config* pliku.

1. Uruchom projekt.

### <a name="run-in-azure"></a>Uruchamianie na platformie Azure

1. Tworzenie aplikacji sieci web i konto magazynu.

1. Zapisz parametry połączenia magazynu w polu Ustawienie aplikacji o nazwie AzureWebJobsStorage w aplikacji sieci web.

1. Tworzenie zasobu usługi Application Insights, typ aplikacji **ogólne**.

1. Zapisz klucza Instrumentacji w ustawienie aplikacji o nazwie APPINSIGHTS_INSTRUMENTATIONKEY.

1. Wdróż jako zadanie WebJob.

## <a name="webjobs-sdk-3x"></a>Zestaw SDK zadań Webjob 3.x

Główne zmiany wprowadzone przez 3.x jest korzystanie z platformy .NET Core zamiast .NET Framework. Aby utworzyć projekt 3.x instrukcje są takie same, z następującymi wyjątkami:

1. Tworzenie aplikacji konsoli .NET Core. W programie Visual Studio **nowy projekt** okno dialogowe, wybierz opcję **.NET Core > aplikacji konsoli (.NET Core)**. Plik projektu określa, że `TargetFramework` jest `netcoreapp2.0`.

1. Wybierz wersję wstępną 3.x następujących pakietów:

  * `Microsoft.Azure.WebJobs.Extensions`
  * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

2. Zmień `Main` kod metody można pobrać parametry połączenia magazynu i klucza Instrumentacji usługi Application Insights z *appsettings.json* plików przy użyciu programu .NET Core framework configuration.  Oto przykład:

   ```cs
   static void Main(string[] args)
   {
       var builder = new ConfigurationBuilder()
           .SetBasePath(Directory.GetCurrentDirectory())
           .AddJsonFile("appsettings.json");

       var appSettingsConfig = builder.Build();

       using (var loggerFactory = new LoggerFactory())
       {
           var config = new JobHostConfiguration();

           config.DashboardConnectionString = "";
           config.StorageConnectionString = 
               appSettingsConfig.GetConnectionString("AzureWebJobsStorage");
           var instrumentationKey =
               appSettingsConfig["APPINSIGHTS_INSTRUMENTATIONKEY"];

           config.LoggerFactory = loggerFactory
               .AddApplicationInsights(instrumentationKey, null)
               .AddConsole();

           config.UseTimers();
           config.UseDurableTask(new DurableTaskExtension
           {
               HubName = "MyTaskHub",
           });
           var host = new JobHost(config);
           host.RunAndBlock();
       }
   }
   ```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat zestawu SDK zadań Webjob, zobacz [sposobu korzystania z zestawu SDK WebJobs](../app-service/webjobs-sdk-how-to.md).

