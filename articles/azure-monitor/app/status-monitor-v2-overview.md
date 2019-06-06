---
title: Omówienie usługi Azure Monitor stanu v2 | Dokumentacja firmy Microsoft
description: Omówienie Monitora stanu w wersji 2. Monitorowanie wydajności witryny sieci Web bez konieczności ponownego wdrażania witryny sieci Web. Działa z aplikacjami sieci web platformy ASP.NET hostowanej lokalnie, na maszynach wirtualnych lub na platformie Azure.
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
ms.openlocfilehash: 2126408222433e6339723dc2da0d2611bb234fe8
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734158"
---
# <a name="status-monitor-v2"></a>Monitor stanu w wersji 2

Stan monitora v2 jest opublikowany modułu PowerShell [galerii programu PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Zastępuje on programy [Monitora stanu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Moduł zawiera uaktualniać instrumentacji aplikacji sieci web platformy .NET hostowane w usługach IIS.
Dane telemetryczne są wysyłane do witryny Azure portal, gdzie można [monitor](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) aplikacji.

> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Tej wersji zapoznawczej jest oferowana bez umowy dotyczącej poziomu usług, i firma Microsoft nie jest to zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, a niektóre mogą mieć ograniczone możliwości.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="powershell-gallery"></a>Galeria programu PowerShell

Galeria programu PowerShell znajduje się tutaj: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.


## <a name="instructions"></a>Instrukcje
- Zobacz [instrukcje z wprowadzeniem](status-monitor-v2-get-started.md) można pobrać rozpoczynać przykłady kodu zwięzły.
- Zobacz [szczegółowe instrukcje](status-monitor-v2-detailed-instructions.md) Aby uzyskać szczegółowe informacje o tym, jak rozpocząć pracę.

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

  *Tak*. Istnieje wiele sposobów, aby pobrać Monitora stanu w wersji 2. Jeśli komputer ma dostęp do Internetu, można dołączyć do galerii programu PowerShell przy użyciu `-Proxy` parametrów.
Możesz również ręcznie pobrać moduł i zainstaluj ją na komputerze lub użyj go bezpośrednio.
Każdy z tych opcji jest opisany w [szczegółowe instrukcje](status-monitor-v2-detailed-instructions.md).
  
- Jak zweryfikować, że włączenie zakończyła się pomyślnie?

   Nie ma żadnych polecenia cmdlet, aby sprawdzić włączenie zakończyło się pomyślnie.
Zalecane jest użycie [metryki na żywo](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) Aby szybko określić, jeśli Twoja aplikacja wysyła dane telemetryczne.

   Można również użyć [usługi Log Analytics](../log-query/get-started-portal.md) Aby wyświetlić listę wszystkich ról chmura obecnie wysyłania danych telemetrycznych:
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>Kolejne kroki

Wyświetlanie telemetrii:

* [Eksplorowanie metryk](../../azure-monitor/app/metrics-explorer.md) do monitorowania wydajności i użycia.
* [Wyszukiwanie zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) do diagnozowania problemów.
* [Korzystanie z analizy](../../azure-monitor/app/analytics.md) dla bardziej zaawansowanych zapytań.
* [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).

Dodawanie kolejnych funkcji telemetrii:

* [Tworzenie testów sieci web](monitor-web-app-availability.md) aby upewnić się, że witryna pozostaje aktywna.
* [Dodawanie telemetrii klienta sieci web](../../azure-monitor/app/javascript.md) aby zobaczyć wyjątki pochodzące z kodu strony sieci web i umożliwić wywołania śledzenia.
* [Dodaj zestaw Application Insights SDK do kodu](../../azure-monitor/app/asp-net.md) dzięki czemu można wstawić ślad i rejestrować wywołania.

