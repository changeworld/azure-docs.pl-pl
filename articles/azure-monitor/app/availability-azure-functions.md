---
title: Tworzenie i uruchamianie niestandardowych testów dostępności przy użyciu Azure Functions
description: Ten dokument zawiera informacje dotyczące sposobu tworzenia funkcji platformy Azure z TrackAvailability (), która będzie uruchamiana okresowo zgodnie z konfiguracją podaną w funkcji TimerTrigger. Wyniki tego testu zostaną wysłane do zasobu Application Insights, w którym będzie można wykonywać zapytania o dane dotyczące wyników dostępności i je otrzymywać. Dostosowane testy umożliwiają pisanie bardziej złożonych testów dostępności niż jest to możliwe za pomocą interfejsu użytkownika portalu, monitorowania aplikacji wewnątrz sieci wirtualnej platformy Azure, zmiany adresu punktu końcowego lub tworzenia testu dostępności, jeśli nie jest on dostępny w Twoim regionie.
services: application-insights
documentationcenter: ''
author: morgangrobin
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: mogrobin
ms.openlocfilehash: 38a83169a7d1ffa03416f5947ada703bcba5017a
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301375"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Tworzenie i uruchamianie niestandardowych testów dostępności przy użyciu Azure Functions

W tym artykule opisano sposób tworzenia funkcji platformy Azure z TrackAvailability (), która będzie okresowo uruchamiana zgodnie z konfiguracją podaną w funkcji TimerTrigger. Wyniki tego testu zostaną wysłane do zasobu Application Insights, w którym będzie można wykonywać zapytania o dane dotyczące wyników dostępności i je otrzymywać. Dzięki temu można tworzyć niestandardowe testy podobne do tego, co można zrobić za pośrednictwem [monitorowania dostępności](../../azure-monitor/app/monitor-web-app-availability.md) w portalu. Dostosowane testy umożliwiają pisanie bardziej złożonych testów dostępności niż jest to możliwe za pomocą interfejsu użytkownika portalu, monitorowania aplikacji wewnątrz sieci wirtualnej platformy Azure, zmiany adresu punktu końcowego lub tworzenia testu dostępności, nawet jeśli ta funkcja nie jest dostępna w Twoim regionie.


## <a name="create-timer-triggered-function"></a>Utwórz funkcję wyzwalaną przez czasomierz

- Jeśli masz zasób Application Insights:
    - Domyślnie Azure Functions tworzy zasób Application Insights, ale jeśli chcesz użyć jednego z już utworzonych zasobów, musisz określić, że podczas tworzenia.
    - Postępuj zgodnie z instrukcjami dotyczącymi sposobu [tworzenia zainicjowanych zasobów Azure Functions i czasomierza](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (Zatrzymaj przed oczyszczeniem) przy użyciu następujących opcji.
        -  Przed wybraniem pozycji **Utwórz**kliknij sekcję Application Insights.

            ![ Tworzenie aplikacji Azure Functions przy użyciu własnego zasobu usługi App Insights](media/availability-azure-functions/create-function-app.png)

        - Kliknij pozycję **Wybierz istniejący zasób** i wpisz nazwę zasobu. Wybierz pozycję **Zastosuj**

            ![Wybieranie istniejącego zasobu Application Insights](media/availability-azure-functions/app-insights-resource.png)

        - Wybierz pozycję **Utwórz**
- Jeśli nie masz jeszcze utworzonego zasobu Application Insights dla funkcji wyzwalanej przez czasomierz:
    - Domyślnie podczas tworzenia aplikacji Azure Functions zostanie utworzony zasób Application Insights.
    - Postępuj zgodnie z instrukcjami dotyczącymi sposobu [tworzenia zasobu Azure Functions i wyzwalanej funkcji timer](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (Zatrzymaj przed oczyszczeniem).

## <a name="sample-code"></a>Przykładowy kod

Skopiuj poniższy kod do pliku Run. CSX (spowoduje to zastąpienie istniejącego kodu). W tym celu przejdź do aplikacji Azure Functions i wybierz funkcję wyzwalacza czasomierz po lewej stronie.

![Funkcja Run. CSX w Azure Portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Adres punktu końcowego, który ma być używany: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. Jeśli zasób nie znajduje się w regionie, takim jak Azure Government lub Chiny platformy Azure, w tym przypadku zapoznaj się z tym artykułem, aby zastąpić [domyślne punkty końcowe](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) i wybrać odpowiedni punkt końcowy kanału telemetrii dla danego regionu.

```C#
using System;
using System.Diagnostics;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

// [CONFIGURATION_REQUIRED] configure test timeout accordingly for which your request should run
private static readonly HttpClient HttpClient = new HttpClient { Timeout = TimeSpan.FromSeconds(30) };

// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
//DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");

// [CONFIGURATION_REQUIRED] Configure EndpointAddress
private static readonly TelemetryConfiguration TelemetryConfiguration = new TelemetryConfiguration(InstrumentationKey, new ServerTelemetryChannel() { EndpointAddress = "<EndpointAddress>" });
private static readonly TelemetryClient TelemetryClient = new TelemetryClient(TelemetryConfiguration);

[FunctionName("Function")]
public static async void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");

    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }

    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";

    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");

    // [CONFIGURATION_REQUIRED] configure {uri} and {contentMatch} accordingly for your web app. {uri} is the website that you are testing the availability of, make sure to include http:// ot https:// in your url. If {contentMatch} is present on the page, the test will succeed, otherwise it will fail.  
    await AvailabilityTestRun(
        name: testName,
        location: location,
        uri: "<http://example.com>",
        contentMatch: "<Enter a short string of text that is present  in the body of the page your are testing>",
        log: log
    );
}

private static async Task AvailabilityTestRun(string name, string location, string uri, string contentMatch, ILogger log)
{
    log.LogInformation($"Executing availability test run for {name} at: {DateTime.Now}");

    string operationId = Guid.NewGuid().ToString("N");

    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = name,
        RunLocation = location,
        Success = false
    };

    var stopwatch = new Stopwatch();
    stopwatch.Start();
    bool isMonitoringFailure = false;

    try
    {
        using (var httpResponse = await HttpClient.GetAsync(uri))
        {
            // add test results to availability telemetry property
            availability.Properties.Add("HttpResponseStatusCode", Convert.ToInt32(httpResponse.StatusCode).ToString());

            // check if response content contains specific text
            string content = httpResponse.Content != null ? await httpResponse.Content.ReadAsStringAsync() : "";
            if (httpResponse.IsSuccessStatusCode && content.Contains(contentMatch))
            {
                availability.Success = true;
                availability.Message = $"Test succeeded with response: {httpResponse.StatusCode}";
                log.LogTrace($"[Verbose]: {availability.Message}");
            }
            else if (!httpResponse.IsSuccessStatusCode)
            {
                availability.Message = $"Test failed with response: {httpResponse.StatusCode}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
            else
            {
                availability.Message = $"Test content does not contain: {contentMatch}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
        }
    }
    catch (TaskCanceledException e)
    {
        availability.Message = $"Test timed out: {e.Message}";
        log.LogWarning($"[Warning]: {availability.Message}");
    }
    catch (Exception ex)
    {
        // track exception when unable to determine the state of web app
        isMonitoringFailure = true;
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", name);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        exceptionTelemetry.Properties.Add("TestUri", uri);
        TelemetryClient.TrackException(exceptionTelemetry);
        log.LogError($"[Error]: {ex.Message}");

        // optional - throw to fail the function
        throw;
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;

        // do not make an assumption as to the state of the web app when monitoring failures occur
        if (!isMonitoringFailure)
        {
            TelemetryClient.TrackAvailability(availability);
            log.LogInformation($"Availability telemetry for {name} is sent.");
        }

        // call flush to ensure telemetries are sent
        TelemetryClient.Flush();
    }
}
```

Po prawej stronie w obszarze Wyświetl pliki wybierz pozycję **Dodaj**. Wywołaj nowy plik **Funkcja. proj** z następującą konfiguracją.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
</Project>

```

![Po prawej stronie wybierz pozycję Dodaj. Nazwij plik Function. proj](media/availability-azure-functions/addfile.png)

## <a name="check-availability"></a>Sprawdź dostępność

Aby upewnić się, że wszystko działa, możesz przyjrzeć się grafowi na karcie dostępność zasobu Application Insights.

![Karta dostępność z pomyślnymi wynikami](media/availability-azure-functions/availtab.png)

Po skonfigurowaniu testu przy użyciu Azure Functions można zauważyć, że w przeciwieństwie do użycia **Dodaj test** na karcie dostępność nazwa testu nie zostanie wyświetlona i nie będzie można z nim korzystać. Wyniki są wizualizowane, ale można uzyskać widok podsumowania, a nie ten sam widok szczegółowy, który uzyskuje się podczas tworzenia testu dostępności za pośrednictwem portalu.

Aby wyświetlić szczegółowe informacje o transakcjach, wybierz pozycję **powodzenie** lub **Niepowodzenie** w obszarze drążenie do, a następnie wybierz przykład. Możesz również uzyskać szczegółowe informacje o transakcjach, wybierając punkt danych na wykresie.

![Wybierz przykładowy Test dostępności](media/availability-azure-functions/sample.png)

![Szczegóły końca transakcji](media/availability-azure-functions/end-to-end.png)

## <a name="query-in-logs-analytics"></a>Zapytanie w dziennikach (analiza)

Dzienników (analiz) można używać do wyświetlania wyników dostępności, zależności i innych. Aby dowiedzieć się więcej o dziennikach, odwiedź stronę [Omówienie zapytania dziennika](../../azure-monitor/log-query/log-query-overview.md).

![Wyniki dostępności](media/availability-azure-functions/availabilityresults.png)

![Zależności](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Następne kroki

- [Mapa aplikacji](../../azure-monitor/app/app-map.md)
- [Diagnostyka transakcji](../../azure-monitor/app/transaction-diagnostics.md)
