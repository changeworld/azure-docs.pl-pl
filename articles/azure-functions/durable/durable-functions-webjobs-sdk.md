---
title: Jak uruchamiać funkcje trwałe jako WebJobs — platforma Azure
description: Dowiedz się, jak kod i Konfiguruj funkcje trwałe do uruchamiania w zadań Webjob za pomocą zestawu SDK usługi WebJobs.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 427c3601d6a4ca65407a98d54b0206cde9af4235
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52643278"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Jak uruchamiać funkcje trwałe jako zadania Webjob

[Usługa Azure Functions](../functions-overview.md) i [funkcje trwałe](durable-functions-overview.md) rozszerzenia są oparte na [zestawu SDK usługi WebJobs](../../app-service/web-sites-create-web-jobs.md). `JobHost` w zestawie SDK usługi WebJobs jest środowisko uruchomieniowe w usłudze Azure Functions. Jeśli potrzebujesz do kontroli `JobHost` zachowanie w sposób nie były możliwe w usłudze Azure Functions, mogą tworzyć i uruchamiać funkcje trwałe przy użyciu zestawu SDK usługi WebJobs, samodzielnie. Następnie możesz uruchomić swoje funkcje trwałe w zadaniu Azure WebJob lub gdziekolwiek uruchamia aplikację konsolową w języku.

Łańcucha Przykładowe funkcje trwałe jest dostępna w wersji zestawu SDK usługi WebJobs: Pobierz lub sklonuj [repozytorium funkcje trwałe](https://github.com/azure/azure-functions-durable-extension/) i przejdź do *przykłady\\webjobssdk\\łańcucha* folderu.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że znasz podstawowe informacje dotyczące zestawu SDK usługi WebJobs, C# klasy Tworzenie bibliotek dla usługi Azure Functions i funkcje trwałe. Jeśli potrzebujesz, aby zapoznać się z wprowadzeniem do tych tematów, zobacz następujące zasoby:

* [Rozpoczynanie pracy z zestawem SDK usługi WebJobs](../../app-service/webjobs-sdk-get-started.md)
* [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Trwałe funkcje](durable-functions-sequence.md)

Wykonanie czynności opisanych w tym artykule:

* [Instalowanie programu Visual Studio 2017 w wersji 15.6 lub nowszej](https://docs.microsoft.com/visualstudio/install/) z **programowanie na platformie Azure** obciążenia.

  Jeśli masz już program Visual Studio, ale nie ma tego obciążenia, dodać obciążenie, wybierając **Narzędzia > Pobierz narzędzia i funkcje**. 

  (Możesz użyć [programu Visual Studio Code](https://code.visualstudio.com/) zamiast tego, ale niektóre instrukcje są specyficzne dla programu Visual Studio.)

* Zainstaluj i uruchom [emulatora usługi Azure Storage](../../storage/common/storage-use-emulator.md) wersji 5.2 lub nowszej. Alternatywą jest zaktualizowanie *App.config* pliku przy użyciu parametrów połączenia usługi Azure Storage.

## <a name="webjobs-sdk-versions"></a>Wersje zestawu SDK usługi WebJobs

W tym artykule wyjaśniono, jak opracować Projekt 2.x zestawu SDK usługi WebJobs (równoważne z usługi Azure Functions w wersji 1.x). Aby uzyskać informacje o wersji 3.x, zobacz [zestawu SDK usługi WebJobs 3.x](#webjobs-sdk-3x) w dalszej części tego artykułu. 

## <a name="create-console-app"></a>Tworzenie aplikacji konsoli

Projekt zestawu SDK usługi WebJobs jest po prostu projekt aplikacji konsoli przy użyciu odpowiednich zainstalowanych pakietów NuGet.

W programie Visual Studio **nowy projekt** okno dialogowe, wybierz opcję **Windows Classic Desktop > Aplikacja Konsolowa (.NET Framework)**. W pliku projektu `TargetFrameworkVersion` powinien być `v4.6.1`.

Program Visual Studio zawiera również szablonu projektu zadania WebJob, którego można użyć, wybierając **chmura > zadań WebJob platformy Azure (.NET Framework)**. Ten szablon instaluje wielu pakietów, z których część może nie być konieczny.

## <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

Pakiety NuGet jest wymagany dla zestawu WebJobs SDK, podstawowe powiązania, struktury rejestrowania i rozszerzenie trwałe zadań. Poniżej przedstawiono **Konsola Menedżera pakietów** poleceń w przypadku tych pakietów z międzynarodowymi numerami identyfikującymi najnowszej stabilnej wersji w dniu, w tym artykule został napisany:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Należy również rejestrowania dostawców. Następujące polecenia instaluje się dostawcę usługi Application Insights i `ConfigurationManager`. `ConfigurationManager` Pozwala uzyskać klucz Instrumentacji usługi Application Insights z aplikacji ustawienia.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Poniższe polecenie instaluje dostawcę konsoli:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Kod JobHost

Aby użyć rozszerzenia funkcji trwałych, należy wywołać `UseDurableTask` na `JobHostConfiguration` obiektu w swojej `Main` metody:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Aby uzyskać listę właściwości, które można ustawić w `DurableTaskExtension` obiektu, zobacz [host.json](../functions-host-json.md#durabletask).

`Main` Metoda jest również miejsce, aby skonfigurować rejestrowanie dostawców. Poniższy przykład umożliwia skonfigurowanie, konsola i dostawców usługi Application Insights.

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

Istnieje kilka różnic w kodzie, który napiszesz funkcji zestaw SDK zadań Webjob w porównaniu do zapisu dla usługi Azure Functions.

Zestaw SDK zadań Webjob nie obsługuje następujących funkcji usługi Azure Functions:

* [Atrybut FunctionName](#functionname-attribute)
* [Wyzwalacz HTTP](#http-trigger)
* [Trwałe funkcje HTTP interfejsu API do zarządzania](#http-management-api)

### <a name="functionname-attribute"></a>Atrybut FunctionName

W projekcie zestawu SDK usługi WebJobs Nazwa metody funkcji jest nazwą funkcji. `FunctionName` Atrybut jest używany tylko w usłudze Azure Functions.

### <a name="http-trigger"></a>Wyzwalacz HTTP

Zestaw SDK zadań Webjob nie ma wyzwalacz HTTP. Klient orkiestracji przykładowy projekt korzysta z wyzwalacza czasomierza:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>Interfejs API zarządzania protokołu HTTP

Ponieważ ma ona żaden wyzwalacz protokołu HTTP, zestaw SDK zadań Webjob nie ma przypisanego [interfejsu API zarządzania HTTP](durable-functions-http-api.md).

W projekcie zestawu SDK usługi WebJobs można wywoływać metody dla obiektu klienta orkiestracji zamiast wysyłania żądań HTTP. Następujące metody odnoszą się do trzech zadań, które można wykonać za pomocą interfejsu API zarządzania HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Funkcja klienta aranżacji w przykładowym projekcie uruchamiania funkcji programu orchestrator i następnie przechodzi w pętli, która wywołuje `GetStatusAsync` co 2 sekundy:

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

Ta sekcja zawiera omówienie sposobu uruchamiania [przykładowy projekt](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Aby uzyskać szczegółowe instrukcje, które wyjaśniają, jak uruchomić lokalnie projekt zestawu SDK zadań Webjob i wdrożyć ją w zadanie WebJob systemu Azure, zobacz [Rozpoczynanie pracy z zestawem SDK usługi WebJobs](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Uruchamianie lokalnie

1. Upewnij się, emulator magazynu jest uruchomiona (zobacz [wymagania wstępne](#prerequisites)).

1. Jeśli chcesz wyświetlić dzienniki w usłudze Application Insights po uruchomieniu lokalnie:

  a. Utwórz zasób usługi Application Insights, typ aplikacji **ogólne**.

  b. Zapisz klucz Instrumentacji w *App.config* pliku.

1. Uruchom projekt.

### <a name="run-in-azure"></a>Uruchamianie na platformie Azure

1. Tworzenie aplikacji sieci web i konta magazynu.

1. Zapisz parametry połączenia magazynu w ustawieniach aplikacji o nazwie AzureWebJobsStorage w aplikacji sieci web.

1. Utwórz zasób usługi Application Insights, typ aplikacji **ogólne**.

1. Zapisz klucz Instrumentacji w ustawieniu aplikacji o nazwie APPINSIGHTS_INSTRUMENTATIONKEY.

1. Wdróż jako zadanie WebJob.

## <a name="webjobs-sdk-3x"></a>Zestaw SDK usługi WebJobs 3.x

Główne zmiany wprowadzone przez 3.x polega na użyciu platformy .NET Core, a nie .NET Framework. Aby utworzyć projekt 3.x instrukcje są takie same, z uwzględnieniem poniższych wyjątków:

1. Tworzenie aplikacji konsolowej .NET Core. W programie Visual Studio **nowy projekt** okno dialogowe, wybierz opcję **platformy .NET Core > Aplikacja Konsolowa (.NET Core)**. Plik projektu określa, że `TargetFramework` jest `netcoreapp2.0`.

1. Wybierz wersję wstępną 3.x następujące pakiety:

  * `Microsoft.Azure.WebJobs.Extensions`
  * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Zmiana `Main` kod metody, aby uzyskać parametry połączenia magazynu oraz klucz Instrumentacji usługi Application Insights z *appsettings.json* plików, za pomocą programu .NET Core framework konfiguracji.  Oto przykład:

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

Aby dowiedzieć się więcej na temat zestawu SDK zadań Webjob, zobacz [jak używać zestawu SDK usługi WebJobs](../../app-service/webjobs-sdk-how-to.md).

