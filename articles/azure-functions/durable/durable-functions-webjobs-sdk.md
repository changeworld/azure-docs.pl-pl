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
ms.openlocfilehash: df12639aaafaf3df7ae2b755d635d4fba83d846e
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905096"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Jak uruchamiać funkcje trwałe jako zadania Webjob

Domyślnie funkcje trwałe używa środowiska uruchomieniowego usługi Azure Functions do aranżacji hosta. Jednak może być korzystniejsze, gdzie możesz muszą większa kontrola nad kodem nasłuchującym zdarzeń. W tym artykule dowiesz się, jak wdrożyć swoje aranżacji przy użyciu zestawu SDK usługi WebJobs. Aby wyświetlić bardziej szczegółowe porównanie usługi Functions i WebJobs, zobacz [porównania Functions i WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Usługa Azure Functions](../functions-overview.md) i [funkcje trwałe](durable-functions-overview.md) rozszerzenia są oparte na [zestawu SDK usługi WebJobs](../../app-service/webjobs-sdk-how-to.md). Host zadania w zestawie SDK usługi WebJobs jest środowisko uruchomieniowe w usłudze Azure Functions. Jeśli potrzebujesz do sterowania zachowaniem w sposób nie były możliwe w usłudze Azure Functions, mogą tworzyć i uruchamiać funkcje trwałe przy użyciu zestawu SDK usługi WebJobs, samodzielnie.

W wersji 3.x zestaw SDK zadań Webjob, host jest implementacją `IHost`i w wersji 2.x, możesz użyć `JobHost` obiektu.

Łańcucha Przykładowe funkcje trwałe jest dostępna w wersji 2.x zestawu SDK usługi WebJobs: Pobierz lub sklonuj [repozytorium funkcje trwałe](https://github.com/azure/azure-functions-durable-extension/)i przejdź do *przykłady\\webjobssdk\\łańcucha* folderu.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że znasz podstawowe informacje dotyczące zestawu SDK usługi WebJobs, C# klasy Tworzenie bibliotek dla usługi Azure Functions i funkcje trwałe. Jeśli potrzebujesz, aby zapoznać się z wprowadzeniem do tych tematów, zobacz następujące zasoby:

* [Rozpoczynanie pracy z zestawem SDK usługi WebJobs](../../app-service/webjobs-sdk-get-started.md)
* [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Trwałe funkcje](durable-functions-sequence.md)

Wykonanie czynności opisanych w tym artykule:

* [Instalowanie programu Visual Studio 2017 w wersji 15.6 lub nowszej](https://docs.microsoft.com/visualstudio/install/) z **programowanie na platformie Azure** obciążenia.

  Jeśli już zainstalowano oprogramowania Visual Studio, ale nie ma tego obciążenia, dodać obciążenie, wybierając **narzędzia** > **Pobierz narzędzia i funkcje**.

  (Możesz użyć [programu Visual Studio Code](https://code.visualstudio.com/) zamiast tego, ale niektóre instrukcje są specyficzne dla programu Visual Studio.)

* Zainstaluj i uruchom [emulatora usługi Azure Storage](../../storage/common/storage-use-emulator.md) wersji 5.2 lub nowszej. Alternatywą jest zaktualizowanie *App.config* pliku przy użyciu parametrów połączenia usługi Azure Storage.

## <a name="webjobs-sdk-versions"></a>Wersje zestawu SDK usługi WebJobs

W tym artykule wyjaśniono, jak opracować Projekt 2.x zestawu SDK usługi WebJobs (równoważne z usługi Azure Functions w wersji 1.x). Aby uzyskać informacje o wersji 3.x, zobacz [zestawu SDK usługi WebJobs 3.x](#webjobs-sdk-3x) w dalszej części tego artykułu.

## <a name="create-a-console-app"></a>Tworzenie aplikacji konsolowej

Aby uruchomić funkcje trwałe jako zadania Webjob, należy najpierw utworzyć aplikację konsoli. Projekt zestawu SDK usługi WebJobs jest po prostu projekt aplikacji konsoli przy użyciu odpowiednich zainstalowanych pakietów NuGet.

W programie Visual Studio **nowy projekt** okno dialogowe, wybierz opcję **Windows Classic Desktop** > **Aplikacja konsoli (.NET Framework)**. W pliku projektu `TargetFrameworkVersion` powinien być `v4.6.1`.

Program Visual Studio zawiera również szablonu projektu zadania WebJob, którego można użyć, wybierając **chmury** > **zadań WebJob platformy Azure (.NET Framework)**. Ten szablon instaluje wielu pakietów, z których część może nie być konieczny.

## <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

Pakiety NuGet jest wymagany dla zestawu WebJobs SDK, podstawowe powiązania, struktury rejestrowania i rozszerzenie trwałe zadań. Poniżej przedstawiono **Konsola Menedżera pakietów** polecenia dla tych pakietów, numerami najnowszej stabilnej wersji w dniu, w tym artykule został napisany:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Należy również rejestrowania dostawców. Następujące polecenia instaluje się dostawcę usługi Azure Application Insights i `ConfigurationManager`. `ConfigurationManager` Pozwala uzyskać klucz Instrumentacji usługi Application Insights z aplikacji ustawienia.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Poniższe polecenie instaluje dostawcę konsoli:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Kod JobHost

Istnienie utworzyliśmy aplikację konsoli i zainstalowanych pakietów NuGet należy, wszystko jest gotowe do użycia funkcje trwałe. Możesz to zrobić przy użyciu kodu JobHost.

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

Trwałe funkcje w kontekście zadania Webjob nieco różne od funkcje trwałe w kontekście usługi Azure Functions. Należy mieć świadomość różnice podczas pisania kodu.

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

W projekcie zestawu SDK usługi WebJobs może wywoływać metody dla obiektu klienta aranżacji, zamiast, przez wysłanie żądania HTTP. Następujące metody odnoszą się do trzech zadań, które można wykonać za pomocą interfejsu API zarządzania HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Funkcja klienta aranżacji w przykładowym projekcie rozpoczyna się funkcja orkiestratora, a następnie przechodzi w pętli, która wywołuje `GetStatusAsync` co 2 sekundy:

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

Masz funkcje trwałe uruchamiane jako zadanie WebJob, i masz teraz zrozumienia, jak będzie to różni się od działających funkcji trwałych jako autonomicznej usługi Azure Functions. W tym momencie niewidoczny Praca w próbce mogą być pomocne.

Ta sekcja zawiera omówienie sposobu uruchamiania [przykładowy projekt](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Aby uzyskać szczegółowe instrukcje, które wyjaśniają, jak uruchomić lokalnie projekt zestawu SDK zadań Webjob i wdrożyć ją w zadanie WebJob systemu Azure, zobacz [Rozpoczynanie pracy z zestawem SDK usługi WebJobs](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Uruchamianie lokalnie

1. Upewnij się, emulator magazynu jest uruchomiona (zobacz [wymagania wstępne](#prerequisites)).

1. Jeśli chcesz zobaczyć dzienników w usłudze Application Insights, gdy lokalne uruchamianie projektu:

    a. Utworzenie zasobu usługi Application Insights i użycie **ogólne** typu aplikacji.

    b. Zapisz klucz Instrumentacji w *App.config* pliku.

1. Uruchom projekt.

### <a name="run-in-azure"></a>Uruchamianie na platformie Azure

1. Tworzenie aplikacji sieci web i konta magazynu.

1. W aplikacji sieci web, Zapisz parametry połączenia magazynu w aplikacji, ustawienie o nazwie `AzureWebJobsStorage`.

1. Utworzenie zasobu usługi Application Insights i użycie **ogólne** typu aplikacji.

1. Zapisz klucz Instrumentacji w aplikacji, ustawienie o nazwie `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Wdróż jako zadanie WebJob.

## <a name="webjobs-sdk-3x"></a>Zestaw SDK usługi WebJobs 3.x

W tym artykule wyjaśniono, jak opracować Projekt 2.x zestawu SDK usługi WebJobs. Jeżeli projektujesz [zestawu SDK usługi WebJobs 3.x](../../app-service/webjobs-sdk-get-started.md) projektu, w tej sekcji, ułatwi Ci zrozumienie różnic.

Główne zmiana wprowadzona polega na użyciu platformy .NET Core, a nie .NET Framework. Aby utworzyć projekt 3.x zestawu SDK usługi WebJobs, instrukcje są takie same, z uwzględnieniem poniższych wyjątków:

1. Tworzenie aplikacji konsolowej .NET Core. W programie Visual Studio **nowy projekt** okno dialogowe, wybierz opcję **platformy .NET Core** > **Aplikacja konsoli (.NET Core)**. Plik projektu określa, że `TargetFramework` jest `netcoreapp2.x`.

1. Wybierz wersję zestawu SDK usługi WebJobs 3.x następujące pakiety:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Ustaw parametry połączenia magazynu i klucz Instrumentacji usługi Application Insights w *appsettings.json* pliku przy użyciu platformy .NET Core framework konfiguracji. Oto przykład:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Zmiana `Main` kod metody, aby to zrobić. Oto przykład:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat zestawu SDK zadań Webjob, zobacz [jak używać zestawu SDK usługi WebJobs](../../app-service/webjobs-sdk-how-to.md).
