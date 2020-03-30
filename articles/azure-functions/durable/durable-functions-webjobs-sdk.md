---
title: Jak uruchomić funkcje trwałe jako WebJobs - Azure
description: Dowiedz się, jak kodować i konfigurować funkcje trwałe do uruchamiania w aplikacjach WebJobs przy użyciu sdk WebJobs.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232737"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Jak uruchomić trwałe funkcje jako WebJobs

Domyślnie funkcje trwałe używa środowiska wykonawczego usługi Azure Functions do hostowania aranżacji. Jednak mogą istnieć pewne scenariusze, w których potrzebujesz większej kontroli nad kodem, który nasłuchuje zdarzeń. W tym artykule pokazano, jak zaimplementować aranżacji przy użyciu webjobs SDK. Aby wyświetlić bardziej szczegółowe porównanie funkcji i webjobs, zobacz [Porównanie funkcji i WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Usługi Azure Functions](../functions-overview.md) i rozszerzenie [Funkcje trwałe](durable-functions-overview.md) są oparte na [sdk WebJobs.](../../app-service/webjobs-sdk-how-to.md) Host zadań w składniku WebJobs SDK jest środowisko wykonawcze w usłudze Azure Functions. Jeśli trzeba kontrolować zachowanie w sposób nie jest możliwe w usłudze Azure Functions, można opracować i uruchomić funkcje trwałe przy użyciu webjobs SDK samodzielnie.

W wersji 3.x webjobs SDK, host jest `IHost`implementacją , a w wersji `JobHost` 2.x używasz obiektu.

Przykład funkcji trwałych łańcucha jest dostępny w wersji WebJobs SDK 2.x: pobierz lub sklonuj [repozytorium trwałe funkcje](https://github.com/azure/azure-functions-durable-extension/)i przejdź do folderu *tworzenia łańcucha\\webjobssdk.\\*

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz podstawy zestawów SDK webjobs, tworzenie biblioteki klas C# dla usług Azure Functions i funkcji trwałych. Jeśli potrzebujesz wprowadzenia do tych tematów, zobacz następujące zasoby:

* [Wprowadzenie do sdk WebJobs](../../app-service/webjobs-sdk-get-started.md)
* [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Trwałe funkcje](durable-functions-sequence.md)

Aby wykonać kroki opisane w tym artykule:

* [Zainstaluj program Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) z obciążeniem **dewelopera platformy Azure.**

  Jeśli masz już program Visual Studio, ale nie masz tego obciążenia, dodaj obciążenie, wybierając **pozycję Narzędzia** > **Pobierz narzędzia i funkcje**.

  (Zamiast tego można użyć [programu Visual Studio Code,](https://code.visualstudio.com/) ale niektóre instrukcje są specyficzne dla programu Visual Studio.

* Zainstaluj i uruchom [emulator usługi Azure Storage](../../storage/common/storage-use-emulator.md) w wersji 5.2 lub nowszej. Alternatywą jest zaktualizowanie pliku *App.config* za pomocą ciągu połączenia usługi Azure Storage.

## <a name="webjobs-sdk-versions"></a>Wersje SDK webjobs

W tym artykule wyjaśniono, jak opracować projekt zestawów SDK 2.x webjobs (odpowiednik usługi Azure Functions w wersji 1.x). Aby uzyskać informacje o wersji 3.x, zobacz [WebJobs SDK 3.x](#webjobs-sdk-3x) w dalszej części tego artykułu.

## <a name="create-a-console-app"></a>Tworzenie aplikacji konsolowej

Aby uruchomić funkcje trwałe jako WebJobs, należy najpierw utworzyć aplikację konsoli. Projekt SDK WebJobs to tylko projekt aplikacji konsoli z zainstalowanymi odpowiednimi pakietami NuGet.

W oknie dialogowym **Nowy projekt programu** Visual Studio wybierz pozycję Klasyczna aplikacja konsoli **klasycznej systemu** > Windows **(.NET Framework).** W pliku projektu `TargetFrameworkVersion` powinien `v4.6.1`być .

Program Visual Studio ma również szablon projektu WebJob, którego można użyć, wybierając **usługę Cloud** > **Azure WebJob (.NET Framework).** Ten szablon instaluje wiele pakietów, z których niektóre mogą nie być potrzebne.

## <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

Potrzebujesz pakietów NuGet dla webjobs SDK, powiązania rdzenia, struktury rejestrowania i rozszerzenia trwałe zadanie. Poniżej przedstawiono polecenia **konsoli Menedżera pakietów** dla tych pakietów, z najnowszymi numerami wersji stabilnych na dzień, w którym został napisany ten artykuł:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Potrzebujesz również dostawców rejestrowania. Następujące polecenia instalują dostawcę usługi `ConfigurationManager`Azure Application Insights i . Umożliwia `ConfigurationManager` uzyskanie klucza instrumentacji usługi Application Insights z ustawień aplikacji.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Następujące polecenie instaluje dostawcę konsoli:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Kod JobHost

Po utworzeniu aplikacji konsoli i zainstalowaniu potrzebnych pakietów NuGet, możesz przystąpić do korzystania z funkcji trwałych. Należy to zrobić przy użyciu kodu JobHost.

Aby użyć rozszerzenia Funkcje `UseDurableTask` trwałe, wywołaj `JobHostConfiguration` obiekt w metodzie: `Main`

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Aby uzyskać listę właściwości, które można `DurableTaskExtension` ustawić w obiekcie, zobacz [host.json](../functions-host-json.md#durabletask).

Metoda `Main` jest również miejscem konfigurowania dostawców rejestrowania. Poniższy przykład konfiguruje dostawców konsoli i usługi Application Insights.

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

Funkcje trwałe w kontekście WebJobs różni się nieco od funkcji trwałych w kontekście funkcji platformy Azure. Ważne jest, aby zdawać sobie sprawę z różnic podczas pisania kodu.

Zestaw WebJobs SDK nie obsługuje następujących funkcji usługi Azure Functions:

* [Atrybut FunctionName](#functionname-attribute)
* [Wyzwalacz HTTP](#http-trigger)
* [Interfejs API zarządzania funkcjami trwałymi HTTP](#http-management-api)

### <a name="functionname-attribute"></a>Atrybut FunctionName

W projekcie SDK WebJobs nazwa metody funkcji jest nazwą funkcji. Atrybut `FunctionName` jest używany tylko w usłudze Azure Functions.

### <a name="http-trigger"></a>Wyzwalacz HTTP

WebJobs SDK nie ma wyzwalacza HTTP. Przykładowy klient aranżacji projektu używa wyzwalacza czasomierza:

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

Ponieważ nie ma wyzwalacza HTTP, pakiet WebJobs SDK nie ma [interfejsu API zarządzania HTTP](durable-functions-http-api.md).

W projekcie SDK WebJobs można wywołać metody na obiekcie klienta aranżacji, zamiast wysyłać żądania HTTP. Następujące metody odpowiadają trzem zadaniom, które można wykonać za pomocą interfejsu API zarządzania HTTP:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Funkcja klienta aranżacji w przykładowym projekcie uruchamia funkcję koordynatora, `GetStatusAsync` a następnie przechodzi do pętli, która wywołuje co 2 sekundy:

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

Masz funkcje trwałe skonfigurowane do uruchamiania jako WebJob, a teraz masz wiedzę, jak to będzie się różnić od uruchamiania funkcji trwałych jako autonomiczne usługi Azure. W tym momencie, widząc, że działa w próbce może być pomocne.

W tej sekcji przedstawiono omówienie sposobu uruchamiania [przykładowego projektu](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Aby uzyskać szczegółowe instrukcje wyjaśniające, jak lokalnie uruchomić projekt SDK webjobs i wdrożyć go w usłudze Azure WebJob, zobacz Wprowadzenie do [zestawu SDK webjobs](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Uruchamianie lokalnie

1. Upewnij się, że emulator magazynu jest uruchomiony (patrz [Wymagania wstępne).](#prerequisites)

1. Jeśli chcesz zobaczyć dzienniki w usłudze Application Insights po uruchomieniu projektu lokalnie:

    a. Utwórz zasób usługi Application Insights i użyj dla niego ogólnego typu **aplikacji.**

    b. Zapisz klucz instrumentacji w pliku *App.config.*

1. Uruchom projekt.

### <a name="run-in-azure"></a>Uruchamianie na platformie Azure

1. Utwórz aplikację internetową i konto magazynu.

1. W aplikacji sieci web zapisz ciąg połączenia magazynu `AzureWebJobsStorage`w ustawieniu aplikacji o nazwie .

1. Utwórz zasób usługi Application Insights i użyj dla niego ogólnego typu **aplikacji.**

1. Zapisz klucz instrumentacji w ustawieniach aplikacji o nazwie `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Wdrażanie jako robota sieci Web.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

W tym artykule wyjaśniono, jak opracować projekt SDK 2.x WebJobs. Jeśli tworzysz [webjobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md) projektu, w tej sekcji pomaga zrozumieć różnice.

Główną zmianą wprowadzono użycie programu .NET Core zamiast programu .NET Framework. Aby utworzyć projekt SDK 3.x webjobs, instrukcje są takie same, z tymi wyjątkami:

1. Utwórz aplikację konsoli .NET Core. W oknie dialogowym **Nowy projekt programu** Visual Studio wybierz pozycję **.NET Core** > **Console App (.NET Core)**. Plik projektu określa, `TargetFramework` `netcoreapp2.x`że jest .

1. Wybierz wersję WebJobs SDK 3.x następujących pakietów:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Ustaw parametry połączenia magazynu i klucz instrumentacji usługi Application Insights w pliku *appsettings.json* przy użyciu struktury konfiguracji .NET Core. Oto przykład:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Zmień `Main` kod metody, aby to zrobić. Oto przykład:

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

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sdk WebJobs, zobacz [Jak korzystać z SDK webjobs](../../app-service/webjobs-sdk-how-to.md).
