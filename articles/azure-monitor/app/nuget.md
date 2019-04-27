---
title: Usługa Azure Application Insights — zależności automatycznego zbierania | Dokumentacja firmy Microsoft
description: Usługa Application Insights automatycznie gromadzenie i wizualizowanie zależności
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/16/2018
ms.author: mbullwin
ms.openlocfilehash: 3ad2f4788a765366066023724772f5432d0d56eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699209"
---
# <a name="application-insights-nuget-packages"></a>Pakiety Insights NuGet aplikacji

Poniżej znajduje się bieżąca lista stabilnej wersji pakietów NuGet dla usługi Application Insights.

## <a name="common-packages-for-aspnet"></a>Popularne pakiety innych dla platformy ASP.NET

| Nazwa pakietu | Stabilną wersję | Opis | Do pobrania |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.8.0 | Zawiera podstawowe funkcje transmisji wszystkie typy Telemetrii szczegółowych informacji w aplikacji zaś pakietu zależnego dla wszystkich innych pakietów usługi Application Insights | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Umożliwia przechwycenie wywołania metody | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Application Insights modułu zbierającego zależności dla aplikacji .NET. To jest zależny pakiet usługi Application Insights specyficzne dla platformy pakietów i zapewnia automatyczne zbieranie danych telemetrycznych zależności. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Modułu zbierającego liczniki wydajności od szczegółowych informacji w aplikacji pozwala wysyłać dane zbierane przez liczniki wydajności do usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.8.0 | Aplikacje sieci web usługi Application Insights dla platformy .NET | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Pakiet NuGet serwera Windows Insights aplikacji zapewnia automatyczne zbieranie telemetria usługi application insights dla aplikacji .NET. Ten pakiet może służyć jako zależny pakiet usługi Application Insights specyficzne dla platformy pakietów lub jako pakiet autonomicznej aplikacji .NET, które nie są objęte specyficzne dla platformy pakietów (na przykład dla ról procesów roboczych platformy .NET). | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Udostępnia kanał danych telemetrycznych do systemu Windows Server SDK usługi Application Insights, zachowa dane telemetryczne w scenariuszach w trybie offline. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Popularne pakiety innych dla platformy ASP.NET Core

| Nazwa pakietu | Stabilną wersję | Opis | Do pobrania |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Aplikacje sieci web usługi Application Insights dla platformy ASP.NET Core. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.8.0 | Ten pakiet zawiera podstawowe funkcje dla transmisji wszystkie typy Telemetrii szczegółowych informacji w aplikacji i jest pakietu zależnego dla wszystkich innych pakietów usługi Application Insights | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | Application Insights modułu zbierającego zależności dla aplikacji .NET. To jest zależny pakiet usługi Application Insights specyficzne dla platformy pakietów i zapewnia automatyczne zbieranie danych telemetrycznych zależności. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Modułu zbierającego liczniki wydajności od szczegółowych informacji w aplikacji pozwala wysyłać dane zbierane przez liczniki wydajności do usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Pakiet NuGet serwera Windows Insights aplikacji zapewnia automatyczne zbieranie telemetria usługi application insights dla aplikacji .NET. Ten pakiet może służyć jako zależny pakiet usługi Application Insights specyficzne dla platformy pakietów lub jako pakiet autonomicznej aplikacji .NET, które nie są objęte specyficzne dla platformy pakietów (na przykład dla ról procesów roboczych platformy .NET). | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Udostępnia kanał danych telemetrycznych do systemu Windows Server SDK usługi Application Insights, zachowa dane telemetryczne w scenariuszach w trybie offline. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="listenerscollectorsappenders"></a>Odbiorniki/moduły zbierające/appenders

| Nazwa pakietu | Stabilną wersję | Opis | Do pobrania |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Umożliwia przesyłanie dalej zdarzeń z DiagnosticSource do usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener umożliwia wysyłanie danych ze źródła zdarzeń zdarzeń do usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector umożliwia wysyłanie danych z śledzenie zdarzeń dla Windows (ETW) do usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Niestandardowe TraceListener, dzięki czemu możesz wysyłać komunikaty w dzienniku śledzenia usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Niestandardowe appendera, co umożliwia wysyłanie komunikatów dziennika Log4Net do usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  niestandardowy cel, co umożliwia wysyłanie komunikatów dziennika NLog do usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Monitoruje wyjątków w aplikacji i automatycznie zbiera dane migawki w celu przeprowadzenia analizy offline. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Nazwa pakietu | Stabilną wersję | Opis | Do pobrania |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Ten pakiet obejmuje automatyczne dekoracja telemetrii za pomocą usługi Service fabric kontekstu w aplikacji jest uruchamiany w. Nie używaj tego NuGet aplikacji natywnej usługi Service Fabric. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Application Insights modułu dla aplikacje usługi Service fabric. Użyj tego NuGet tylko w przypadku aplikacji natywnej usługi Service Fabric. Dla aplikacji działających w kontenerach należy użyć pakietu Microsoft.ApplicationInsights.ServiceFabric. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Monitor stanu usługi

| Nazwa pakietu | Stabilną wersję | Opis | Do pobrania |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Umożliwia zbieranie danych środowiska uruchomieniowego x64 aplikacji | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Umożliwia zbieranie danych środowiska uruchomieniowego dla x86 aplikacji. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Te pakiety tworzą część podstawowe funkcje monitorowania środowiska uruchomieniowego w [Monitora stanu](../../azure-monitor/app/monitor-performance-live-website-now.md). Nie trzeba bezpośrednio pobrać te pakiety, wystarczy użyć Instalator Monitora stanu. Jeśli chcesz dowiedzieć się więcej o tych pakietów działa pod maską to [wpis w blogu](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) z jednego z naszych deweloperów stanowi dobry początek.

## <a name="additional-packages"></a>Dodatkowe pakiety

| Nazwa pakietu | Stabilną wersję | Opis | Do pobrania |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | To rozszerzenie umożliwia monitorowanie usługi Application Insights w usłudze Azure App Service. Zestaw SDK w wersji 2.6.1. Instrukcje: Dodawanie ustawienia aplikacji "APPINSIGHTS_INSTRUMENTATIONKEY" za pomocą ikey i ponownego uruchomienia aplikacji sieci Web, aby zastosować zmiany.| [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Ten pakiet zawiera pliki wymagane do uaktualniać iniekcji usługi Application Insights | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Kolejne kroki

- Monitor [platformy ASP.NET Core](../../azure-monitor/app/asp-net-core.md).
- Profil programu ASP.NET Core [aplikacje internetowe systemu Linux platformy Azure](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- Debugowanie projektów platformy ASP.NET [migawek](../../azure-monitor/app/snapshot-debugger.md).