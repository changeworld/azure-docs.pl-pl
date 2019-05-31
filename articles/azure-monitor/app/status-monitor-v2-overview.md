---
title: Omówienie usługi Azure Monitor stanu v2 | Dokumentacja firmy Microsoft
description: Omówienie Monitora stanu w wersji 2. Monitorowanie wydajności witryny sieci Web bez konieczności ponownego wdrażania witryny sieci Web. Działa z aplikacjami internetowymi platformy ASP.NET hostowanymi lokalnie na maszynach wirtualnych lub platformie Azure.
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
ms.openlocfilehash: 2adc706c5da4fa53ace2a8a471789e276878c491
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255859"
---
# <a name="status-monitor-v2"></a>Monitor stanu w wersji 2

Stan monitora v2 jest moduł programu PowerShell publikowane [galerii PowerShellGallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor) i zastępuje [Monitora stanu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now). Ten moduł dostarcza kod bez instrumentacji aplikacji sieci web platformy .NET hostowane w usługach IIS.
Dane telemetryczne będą wysyłane do witryny Azure portal można [monitor](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) aplikacji.

> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="powershell-gallery"></a>Galeria programu PowerShell

https://www.powershellgallery.com/packages/Az.ApplicationMonitor


## <a name="instructions"></a>Instrukcje
- Przejrzyj nasze [instrukcje z wprowadzeniem](status-monitor-v2-get-started.md) teraz wprowadzenie przykłady kodu zwięzły.
- Przejrzyj nasze [szczegółowe instrukcje](status-monitor-v2-detailed-instructions.md) Aby uzyskać szczegółowe informacje o tym, jak rozpocząć pracę.

## <a name="powershell-api-reference"></a>Dokumentacja interfejsu API programu PowerShell
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Wyłącz InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>Rozwiązywanie problemów
- [Rozwiązywanie problemów](status-monitor-v2-troubleshoot.md)
- [Znane problemy](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Często zadawane pytania

- Monitor stanu w wersji 2 obsługuje instalacji serwera proxy?

  **Tak**. Istnieje wiele opcji do pobrania Monitora stanu w wersji 2. Jeśli komputer ma dostęp do Internetu, można dołączyć do galerii programu PowerShell przy użyciu `-Proxy` parametrów. Możesz również ręcznie pobrać ten moduł i zainstaluj ją na komputerze lub bezpośrednio za pomocą modułu. Każdy z tych opcji jest opisany w naszym [szczegółowe instrukcje](status-monitor-v2-detailed-instructions.md).
  
- Sprawdzanie włączenia zakończyło się pomyślnie?

   Aby sprawdzić poprawność tej możliwości nie ma polecenia cmdlet. Firma Microsoft zaleca używanie [metryki na żywo](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) szybko obserwować, jeśli aplikacja wysyła nam dane telemetryczne.

   Można również użyć [Analytics](../log-query/get-started-portal.md) Aby wyświetlić listę wszystkich ról chmura obecnie wysyłania danych telemetrycznych.
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>Kolejne kroki

Wyświetlanie telemetrii:

* [Eksplorowanie metryk](../../azure-monitor/app/metrics-explorer.md) w celu monitorowania wydajności i użycia
* [Wyszukiwanie zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) do diagnozowania problemów
* [Analiza](../../azure-monitor/app/analytics.md) dla bardziej zaawansowanych zapytań
* [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md)

Dodawanie kolejnych funkcji telemetrii:

* [Tworzenie testów sieci web](monitor-web-app-availability.md) aby upewnić się, że witryna pozostaje aktywna.
* [Dodawanie telemetrii klienta sieci web](../../azure-monitor/app/javascript.md) aby zobaczyć wyjątki pochodzące z kodu strony sieci web i umożliwić wstawianie wywołań śladu.
* [Dodawanie zestawu SDK usługi Application Insights do kodu](../../azure-monitor/app/asp-net.md) tak, aby wstawić ślad i rejestrować wywołania

