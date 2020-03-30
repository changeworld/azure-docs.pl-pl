---
title: Ustawianie alertów w usłudze Application Insights za pomocą programu Powershell | Dokumenty firmy Microsoft
description: Zautomatyzuj konfigurację usługi Application Insights, aby otrzymywać wiadomości e-mail dotyczące zmian metryki.
ms.topic: conceptual
ms.date: 10/31/2016
ms.openlocfilehash: c19cb43d08b44b55c786e750e64a83e6f0c67381
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669849"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Ustawianie alertów w usłudze Application Insights przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Konfigurację [alertów](../../azure-monitor/app/alerts.md) można zautomatyzować w [usłudze Application Insights](../../azure-monitor/app/app-insights-overview.md).

Ponadto można [ustawić elementów webhook, aby zautomatyzować odpowiedź na alert](../../azure-monitor/platform/alerts-webhooks.md).

> [!NOTE]
> Jeśli chcesz tworzyć zasoby i alerty w tym samym czasie, należy rozważyć [użycie szablonu usługi Azure Resource Manager](powershell.md).

## <a name="one-time-setup"></a>Konfiguracja jednorazowa
Jeśli nie używałeś programu PowerShell z subskrypcją platformy Azure przed:

Zainstaluj moduł programu Azure Powershell na komputerze, na którym chcesz uruchomić skrypty.

* Zainstaluj [Instalator microsoft web platform (v5 lub nowszy)](https://www.microsoft.com/web/downloads/platform.aspx).
* Użyj go do zainstalowania programu Microsoft Azure Powershell

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure
Uruchom program Azure PowerShell i [połącz się z subskrypcją:](/powershell/azure/overview)

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
Napisz do mnie, jeśli odpowiedź serwera na żądania HTTP, średnio ponad 5 minut, jest mniejsza niż 1 sekunda. Zasób My Application Insights nosi nazwę IceCreamWebApp i znajduje się w grupie zasobów Fabrikam. Jestem właścicielem subskrypcji platformy Azure.

Identyfikator GUID jest identyfikatorem subskrypcji (nie kluczem instrumentacji aplikacji).

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
Mam aplikację, w której używam [TrackMetric()](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) do zgłaszania metryki o nazwie "salesPerHour". Wyślij e-mail do moich kolegów, jeśli "salesPerHour" spadnie poniżej 100, średnio w ciągu 24 godzin.

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

Tej samej reguły można użyć dla metryki zgłaszane przy użyciu [parametru pomiaru](../../azure-monitor/app/api-custom-events-metrics.md#properties) innego wywołania śledzenia, takich jak TrackEvent lub trackPageView.

## <a name="metric-names"></a>Nazwy metryczne
| Nazwa metryki | Nazwa ekranu | Opis |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Wyjątki przeglądarki |Liczba nieprzechoczonych wyjątków zgłaszanych w przeglądarce. |
| `basicExceptionServer.count` |Wyjątki serwera |Liczba nieobsługiwała wyjątków zgłaszanych przez aplikację |
| `clientPerformance.clientProcess.value` |Czas przetwarzania klienta |Czas między odebraniem ostatniego bajtu dokumentu do załadowania modelu DOM. Żądania asynchronii mogą nadal być przetwarzane. |
| `clientPerformance.networkConnection.value` |Czas ładowania strony sieciowej |Czas potrzebny przeglądarce na połączenie z siecią. Może być 0, jeśli jest buforowany. |
| `clientPerformance.receiveRequest.value` |Odbieranie czasu odpowiedzi |Czas między wysłaniem żądania przeglądarki do rozpoczęcia odbierania odpowiedzi. |
| `clientPerformance.sendRequest.value` |Wyślij czas żądania |Czas przesuwający przeglądarkę na wysłanie żądania. |
| `clientPerformance.total.value` |Czas ładowania strony przeglądarki |Czas od żądania użytkownika do DOM, arkusze stylów, skrypty i obrazy są ładowane. |
| `performanceCounter.available_bytes.value` |Dostępna pamięć |Pamięć fizyczna natychmiast dostępna dla procesu lub do użytku systemowego. |
| `performanceCounter.io_data_bytes_per_sec.value` |Szybkość we/wy procesu |Całkowita liczba bajtów na sekundę odczytu i zapisania w plikach, sieci i urządzeniach. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |wskaźnik wyjątków |Wyjątki generowane na sekundę. |
| `performanceCounter.percentage_processor_time.value` |Przetwarzanie procesora |Procent czasu, jaki upłynął od wszystkich wątków procesu używanych przez procesor do wykonywania instrukcji dla procesu aplikacji. |
| `performanceCounter.percentage_processor_total.value` |Czas procesora |Procent czasu, który procesor spędza w wątkach innych niż bezczynne. |
| `performanceCounter.process_private_bytes.value` |Przetwarzanie bajtów prywatnych |Pamięć przypisana wyłącznie do procesów monitorowanych aplikacji. |
| `performanceCounter.request_execution_time.value` |ASP.NET czas wykonywania żądania |Czas wykonania ostatniego żądania. |
| `performanceCounter.requests_in_application_queue.value` |ASP.NET żądań w kolejce wykonywania |Długość kolejki żądań aplikacji. |
| `performanceCounter.requests_per_sec.value` |ASP.NET stawka za żądanie |Szybkość wszystkich żądań do aplikacji na sekundę z ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Błędy zależności |Liczba nieudanych wywołań wykonanych przez aplikację serwera do zasobów zewnętrznych. |
| `request.duration` |Czas odpowiedzi serwera |Czas między odebraniem żądania HTTP a zakończeniem wysyłania odpowiedzi. |
| `request.rate` |Stawka żądania |Szybkość wszystkich żądań do aplikacji na sekundę. |
| `requestFailed.count` |Żądania zakończone niepowodzeniem |Liczba żądań HTTP, które spowodowały >kodu odpowiedzi = 400 |
| `view.count` |Wyświetlenia strony |Liczba żądań użytkowników klienta dla strony sieci web. Ruch syntetyczny jest odfiltrowywał. |
| {Twoja niestandardowa nazwa metryki} |{Twoja nazwa metryki} |Wartość metryki zgłoszona przez [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) lub w [parametrze pomiarów połączenia śledzącego](../../azure-monitor/app/api-custom-events-metrics.md#properties). |

Metryki są wysyłane przez różne moduły telemetryczne:

| Grupa metryczna | Moduł kolektora |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>widok |[JavaScript przeglądarki](../../azure-monitor/app/javascript.md) |
| Performancecounter |[Wydajność](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Zależność](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| Żądanie<br/>wniosekNieuczony |[Żądanie serwera](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Elementy webhook
Możesz [zautomatyzować odpowiedź na alert](../../azure-monitor/platform/alerts-webhooks.md). Platforma Azure wywoła wybrany adres internetowy po wywołaniu alertu.

## <a name="see-also"></a>Zobacz też
* [Skrypt do konfigurowania usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Tworzenie aplikacji i zasobów testowych w sieci Web na podstawie szablonów](powershell.md)
* [Automatyzacja łączenia diagnostyki platformy Microsoft Azure z aplikacjami](powershell-azure-diagnostics.md)
* [Automatyzacja odpowiedzi na alert](../../azure-monitor/platform/alerts-webhooks.md)
