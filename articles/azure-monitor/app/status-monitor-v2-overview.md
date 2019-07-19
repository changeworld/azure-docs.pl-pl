---
title: Omówienie usługi Azure monitor stanu v2 | Microsoft Docs
description: Przegląd monitor stanu v2. Monitorowanie wydajności witryny sieci Web bez ponownego wdrażania witryny sieci Web. Współpracuje z usługą ASP.NET Web Apps hostowaną lokalnie, na maszynach wirtualnych lub na platformie Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 38359858d16a20f73cf845fa547899bdc5ee7fd3
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326247"
---
# <a name="status-monitor-v2"></a>Monitor stanu w wersji 2

Monitor stanu V2 to moduł programu PowerShell opublikowany w [Galeria programu PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Zastępuje [Monitor stanu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Moduł zawiera instrumentację bezkodową aplikacji sieci Web platformy .NET hostowanych za pomocą usług IIS.
Dane telemetryczne są wysyłane do Azure Portal, w którym można [monitorować](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) aplikację.

## <a name="powershell-gallery"></a>Galeria programu PowerShell

Monitor stanu v2 znajduje się tutaj: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![Galeria programu PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instrukcje
- Zobacz [instrukcje wprowadzające](status-monitor-v2-get-started.md) , aby rozpocząć od zwięzłych przykładów kodu.
- Zobacz [szczegółowe instrukcje](status-monitor-v2-detailed-instructions.md) dotyczące głębokiej szczegółowe na temat rozpoczynania pracy.

## <a name="powershell-api-reference"></a>Dokumentacja interfejsu API programu PowerShell
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Rozwiązywanie problemów
- [Rozwiązywanie problemów](status-monitor-v2-troubleshoot.md)
- [Znane problemy](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Często zadawane pytania

- Czy monitor stanu v2 obsługuje instalację serwera proxy?

  *Tak*. Istnieje wiele sposobów pobierania monitor stanu v2. Jeśli komputer ma dostęp do Internetu, możesz dołączyć do Galeria programu PowerShell za pomocą `-Proxy` parametrów.
Możesz również ręcznie pobrać moduł, a następnie zainstalować go na komputerze lub użyć go bezpośrednio.
Każda z tych opcji została opisana w [szczegółowym instrukcji](status-monitor-v2-detailed-instructions.md).
  
- Jak mogę sprawdzić, czy Włączanie zakończyło się pomyślnie?

   Nie istnieje polecenie cmdlet do sprawdzenia, czy Włączanie zakończyło się pomyślnie.
Zalecamy używanie metryk na [żywo](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) , aby szybko określić, czy aplikacja wysyła dane telemetryczne.

   Możesz również użyć [log Analytics](../log-query/get-started-portal.md) , aby wyświetlić listę wszystkich ról w chmurze aktualnie wysyłających dane telemetryczne:
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>Następne kroki

Wyświetlanie telemetrii:

* [Poznaj metryki](../../azure-monitor/app/metrics-explorer.md) , aby monitorować wydajność i użycie.
* [Wyszukaj zdarzenia i dzienniki](../../azure-monitor/app/diagnostic-search.md) , aby zdiagnozować problemy.
* [Użyj analizy](../../azure-monitor/app/analytics.md) , aby uzyskać bardziej zaawansowane zapytania.
* [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).

Dodawanie kolejnych funkcji telemetrii:

* [Utwórz testy sieci Web](monitor-web-app-availability.md) , aby upewnić się, że witryna pozostaje aktywna.
* [Dodaj telemetrię klienta sieci Web](../../azure-monitor/app/javascript.md) , aby zobaczyć wyjątki z kodu strony sieci Web i włączyć wywołania śledzenia.
* [Dodaj do kodu zestaw SDK Application Insights](../../azure-monitor/app/asp-net.md) , aby móc wstawiać wywołania śledzenia i rejestrowania.

