---
title: Ustawianie alertów w usłudze Application Insights przy użyciu programu Powershell | Dokumentacja firmy Microsoft
description: Automatyzowanie konfiguracji usługi Application Insights, aby otrzymywać wiadomości e-mail o zmianach metryki.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 05d6a9e0-77a2-4a35-9052-a7768d23a196
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: 5dfbc6fa18b5d1b5b3058db14eb1232be27a0c40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66130984"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Ustawianie alertów w usłudze Application Insights przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Można zautomatyzować konfigurację [alerty](../../azure-monitor/app/alerts.md) w [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md).

Ponadto mogą [Ustaw elementami webhook w celu automatyzowania odpowiedzi na alert](../../azure-monitor/platform/alerts-webhooks.md).

> [!NOTE]
> Jeśli chcesz utworzyć zasoby i alerty w tym samym czasie, należy wziąć pod uwagę [przy użyciu szablonu usługi Azure Resource Manager](powershell.md).

## <a name="one-time-setup"></a>Jednorazowej konfiguracji
Jeśli nie znasz programu PowerShell z subskrypcją platformy Azure przed:

Instalowanie modułu Azure Powershell na komputerze, na którym chcesz uruchomić skrypty.

* Zainstaluj [Instalatora platformy sieci Web firmy Microsoft (w wersji 5 lub nowszej)](https://www.microsoft.com/web/downloads/platform.aspx).
* Użyć go do zainstalowania środowiska Microsoft Azure Powershell

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure
Uruchom program Azure PowerShell i [nawiązać połączenie z subskrypcją](/powershell/azure/overview):

```powershell

    Add-AzAccount
```


## <a name="get-alerts"></a>Otrzymuj alerty
    Get-AzAlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Dodaj alert
    Add-AzMetricAlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US" // must be East US at present
     -RuleType Metric



## <a name="example-1"></a>Przykład 1
Wyślij mi wiadomość e-mail, jeśli odpowiedzi serwera na żądania HTTP, średniej ponad 5 minut, jest mniejsza niż 1 sekunda. Mój zasób usługi Application Insights jest nazywany IceCreamWebApp i jest w grupie zasobów firmy Fabrikam. Jestem właścicielem subskrypcji platformy Azure.

Identyfikator GUID jest identyfikator subskrypcji (nie klucz instrumentacji aplikacji).

    Add-AzMetricAlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Przykład 2
Mam aplikację, w którym mogę użyć [TrackMetric()](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) zgłosić Metryka o nazwie "salesPerHour." Wyślij wiadomość e-mail do moich współpracowników, jeśli "salesPerHour" spadnie poniżej 100, średniej ponad 24 godzin.

    Add-AzMetricAlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

Ta zasada może służyć do metryki zgłoszony za pomocą [parametr miary](../../azure-monitor/app/api-custom-events-metrics.md#properties) wywołania śledzenia innego, takich jak funkcja TrackEvent lub wywołania trackPageView.

## <a name="metric-names"></a>Nazwy metryk
| Nazwa metryki | Nazwa ekranowa | Opis |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Wyjątki przeglądarki |Liczba nieprzechwyconych wyjątków zgłoszonych w przeglądarce. |
| `basicExceptionServer.count` |Wyjątki serwera |Liczba nieobsługiwanych wyjątków zgłoszonych przez aplikację |
| `clientPerformance.clientProcess.value` |Czas przetwarzania klienta |Czas między odebraniem ostatniego bajtu dokumentu, do momentu załadowania modelu DOM. Żądania asynchroniczne mogą nadal być przetwarzane. |
| `clientPerformance.networkConnection.value` |Czas połączenia sieciowego podczas ładowania strony |Czas przyjmuje przeglądarki, aby nawiązać połączenie z siecią. Może być równa 0, jeśli w pamięci podręcznej. |
| `clientPerformance.receiveRequest.value` |Czas odpowiedzi odbierania |Czas między przeglądarką, wysyłając żądanie do uruchamiania na odebranie odpowiedzi. |
| `clientPerformance.sendRequest.value` |Czas żądania wysyłania |Czas poświęcony przez przeglądarkę, aby wysłać żądanie. |
| `clientPerformance.total.value` |Czas ładowania strony przeglądarki |Czas od wysłania żądania użytkownika do załadowania modelu DOM, arkuszy stylów, skryptów i obrazów. |
| `performanceCounter.available_bytes.value` |Dostępna pamięć |Pamięć fizyczna dostępna natychmiast dla procesów lub do użycia przez system. |
| `performanceCounter.io_data_bytes_per_sec.value` |Liczba operacji we/wy procesu |Łączna liczba bajtów odczytanych i zapisanych w ciągu sekundy w plikach i sieci oraz na urządzeniach. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |częstotliwość występowania wyjątków |Wyjątki zgłoszone w ciągu sekundy. |
| `performanceCounter.percentage_processor_time.value` |Procesy — procesor CPU |Wartość procentową czasu wszystkie wątki procesów używana przez procesor do wykonywania instrukcji w procesie aplikacji. |
| `performanceCounter.percentage_processor_total.value` |Czas procesora |Procent czasu poświęconego przez procesor na aktywne wątki. |
| `performanceCounter.process_private_bytes.value` |Prywatne bajty procesu |Pamięć przypisana wyłącznie do procesów monitorowanej aplikacji. |
| `performanceCounter.request_execution_time.value` |Czas wykonywania żądania programu ASP.NET |Czas wykonywania najnowszego żądania. |
| `performanceCounter.requests_in_application_queue.value` |Żądania programu ASP.NET w kolejce do wykonania |Długość kolejki żądań aplikacji. |
| `performanceCounter.requests_per_sec.value` |Liczba żądań programu ASP.NET |Liczba wszystkich żądań wysłanych do aplikacji z platformy ASP.NET na sekundę. |
| `remoteDependencyFailed.durationMetric.count` |Błędy zależności |Liczba wywołań zakończonych niepowodzeniem wykonanych przez aplikację serwera z zasobami zewnętrznymi. |
| `request.duration` |Czas odpowiedzi serwera |Czas między odebraniem żądania HTTP i zakończeniem wysyłania odpowiedzi. |
| `request.rate` |Współczynnik żądań |Liczba wszystkich żądań do aplikacji na sekundę. |
| `requestFailed.count` |Żądania zakończone niepowodzeniem |Żądania liczba HTTP, które spowodowało kod odpowiedzi > = 400 |
| `view.count` |Wyświetlenia strony |Liczba żądań użytkowników klientów dla strony sieci web. Odfiltrowane ruchu syntetycznego. |
| {niestandardowe metryki nazwę} |{Nazwa metryki} |Wartość metryki zgłoszone przez [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) lub [pomiarów parametr wywołania śledzenia](../../azure-monitor/app/api-custom-events-metrics.md#properties). |

Metryki są wysyłane przez moduły różnych danych telemetrycznych:

| Metryki grupy | Moduł zbierający |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>wyświetl |[Kod JavaScript przeglądarki](../../azure-monitor/app/javascript.md) |
| PerformanceCounter |[Wydajność](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Zależności](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| żądania,<br/>requestFailed |[Żądanie serwera](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Elementy webhook
Możesz [automatyzowania odpowiedzi na alert](../../azure-monitor/platform/alerts-webhooks.md). Azure będzie wywoływać wybrany adres w sieci web, gdy zostanie zgłoszony alert.

## <a name="see-also"></a>Zobacz także
* [Skrypt w celu skonfigurowania usługi Application Insights](powershell-script-create-resource.md)
* [Tworzenie usługi Application Insights i zasoby testu sieci web za pomocą szablonów](powershell.md)
* [Automatyzowanie sprzężenia Microsoft Azure Diagnostics do usługi Application Insights](powershell-azure-diagnostics.md)
* [Automatyzowanie odpowiedzi na alert](../../azure-monitor/platform/alerts-webhooks.md)
