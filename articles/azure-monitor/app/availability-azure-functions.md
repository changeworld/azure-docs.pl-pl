---
title: Tworzenie i uruchamianie testów dostępności niestandardowej przy użyciu usługi Azure Functions
description: Ten doc obejmie jak utworzyć funkcję platformy Azure z TrackAvailability(), który będzie uruchamiany okresowo zgodnie z konfiguracją podana w TimerTrigger funkcji. Wyniki tego testu zostaną wysłane do zasobu usługi Application Insights, gdzie będzie można wyszukiwać i ostrzegać o danych wyników dostępności. Dostosowane testy umożliwiają pisanie bardziej złożonych testów dostępności niż jest to możliwe przy użyciu interfejsu użytkownika portalu, monitorowanie aplikacji wewnątrz sieci wirtualnej platformy Azure, zmienianie adresu punktu końcowego lub tworzenie testu dostępności, jeśli nie jest on dostępny w Twoim regionie.
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665803"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Tworzenie i uruchamianie testów dostępności niestandardowej przy użyciu usługi Azure Functions

W tym artykule opisano, jak utworzyć funkcję platformy Azure z TrackAvailability(), która będzie działać okresowo zgodnie z konfiguracją podana w funkcji TimerTrigger z własną logiką biznesową. Wyniki tego testu zostaną wysłane do zasobu usługi Application Insights, gdzie będzie można wyszukiwać i ostrzegać o danych wyników dostępności. Dzięki temu można utworzyć dostosowane testy podobne do tego, co można zrobić za pomocą [monitorowania dostępności](../../azure-monitor/app/monitor-web-app-availability.md) w portalu. Dostosowane testy pozwolą ci napisać bardziej złożone testy dostępności, niż jest to możliwe przy użyciu interfejsu użytkownika portalu, monitorować aplikację wewnątrz sieci wirtualnej platformy Azure, zmieniać adres punktu końcowego lub utworzyć test dostępności, nawet jeśli ta funkcja nie jest dostępna w Twoim regionie.

> [!NOTE]
> W tym przykładzie jest przeznaczony wyłącznie do pokazania mechaniki, jak TrackAvailability() wywołanie interfejsu API działa w ramach funkcji platformy Azure. Nie jak napisać podstawowy kod testu HTTP/logiki biznesowej, które byłyby wymagane, aby przekształcić to w pełni funkcjonalny test dostępności. Domyślnie, jeśli przejdziesz przez ten przykład, zostaniesz wygenerowany test dostępności, który zawsze wygeneruje błąd.

## <a name="create-timer-triggered-function"></a>Tworzenie funkcji wyzwalania czasomierza

- Jeśli masz zasób usługi Application Insights:
    - Domyślnie usługa Azure Functions tworzy zasób usługi Application Insights, ale jeśli chcesz użyć jednego z już utworzonych zasobów, musisz określić to podczas tworzenia.
    - Postępuj zgodnie z instrukcjami dotyczącymi [tworzenia zasobów usługi Azure Functions i funkcji wyzwalanej czasomierzem](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (zatrzymaj przed oczyszczeniem) z następującymi wyborami.
        -  Wybierz kartę **Monitorowanie** u góry.

            ![ Tworzenie aplikacji usługi Azure Functions przy za pomocą własnego zasobu usługi App Insights](media/availability-azure-functions/create-function-app.png)

        - Zaznacz pole listy rozwijanej Usługi Application Insights i wpisz lub wybierz nazwę zasobu.

            ![Wybieranie istniejącego zasobu usługi Application Insights](media/availability-azure-functions/app-insights-resource.png)

        - Wybierz **pozycję Recenzja + utwórz**
- Jeśli nie masz jeszcze utworzonego zasobu usługi Application Insights dla funkcji wyzwalanej czasomierzem:
    - Domyślnie podczas tworzenia aplikacji usługi Azure Functions utworzy zasób usługi Application Insights dla Ciebie.
    - Postępuj zgodnie z instrukcjami dotyczącymi [tworzenia zasobów usługi Azure Functions i funkcji wyzwalanej timera](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (zatrzymaj przed czyszczeniem).

## <a name="sample-code"></a>Przykładowy kod

Skopiuj poniższy kod do pliku run.csx (zastąpi to istniejący kod). Aby to zrobić, przejdź do aplikacji usługi Azure Functions i wybierz funkcję wyzwalacza czasomierza po lewej stronie.

>[!div class="mx-imgBorder"]
>![Działanie.csx funkcji platformy Azure w witrynie Azure portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> W przypadku adresu punktu końcowego, którego używasz: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. Chyba że zasób znajduje się w regionie, takim jak Azure Government lub Azure China, w którym to przypadku zapoznaj się z tym artykułem na [temat zastępowania domyślnych punktów końcowych](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) i wybierz odpowiedni punkt końcowy kanału telemetrii dla danego regionu.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
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
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

Po prawej stronie w obszarze pliki widoku wybierz pozycję **Dodaj**. Wywołanie nowego pliku **function.proj** z następującą konfiguracją.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![Po prawej stronie wybierz dodaj. Nazwij plik function.proj](media/availability-azure-functions/addfile.png)

Po prawej stronie w obszarze pliki widoku wybierz pozycję **Dodaj**. Wywołanie nowego **pliku runAvailabilityTest.csx** z następującą konfiguracją.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Sprawdź dostępność

Aby upewnić się, że wszystko działa, można spojrzeć na wykres na karcie Dostępność zasobu usługi Application Insights.

> [!NOTE]
> Jeśli zaimplementowałeś własną logikę biznesową w runAvailabilityTest.csx, zobaczysz pomyślne wyniki, takie jak na poniższych zrzutach ekranu, jeśli nie, zobaczysz nieudane wyniki.

>[!div class="mx-imgBorder"]
>![Karta Dostępność z wynikami pomyślnymi](media/availability-azure-functions/availtab.png)

Po skonfigurowaniu testu przy użyciu usługi Azure Functions można zauważyć, że w przeciwieństwie do korzystania **z testu Dodaj** na karcie Dostępność, nazwa testu nie pojawi się i nie będzie można z nim współpracować. Wyniki są wizualizowane, ale otrzymasz widok podsumowania zamiast tego samego szczegółowego widoku, który można uzyskać podczas tworzenia testu dostępności za pośrednictwem portalu.

Aby wyświetlić szczegóły transakcji end-to-end, wybierz **opcję Udane** lub **Nie powiodło się** w obszarze przechodzenia do szczegółów, a następnie wybierz przykład. Można również uzyskać informacje o transakcji end-to-end, wybierając punkt danych na wykresie.

>[!div class="mx-imgBorder"]
>![Wybierz przykładowy test dostępności](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![Szczegółowe informacje o transakcji typu end-to-end](media/availability-azure-functions/end-to-end.png)

Jeśli uruchomiono wszystko tak, jak jest (bez dodawania logiki biznesowej), zobaczysz, że test nie powiódł się.

## <a name="query-in-logs-analytics"></a>Kwerenda w dziennikach (Analytics)

Za pomocą logów(analizy) można wyświetlić wyniki dostępności, zależności i inne. Aby dowiedzieć się więcej o dziennikach, odwiedź stronę [Omówienie kwerendy dziennika](../../azure-monitor/log-query/log-query-overview.md).

>[!div class="mx-imgBorder"]
>![Wyniki dostępności](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Zależności](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Następne kroki

- [Mapa aplikacji](../../azure-monitor/app/app-map.md)
- [Diagnostyka transakcji](../../azure-monitor/app/transaction-diagnostics.md)
