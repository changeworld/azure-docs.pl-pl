---
title: Pakiety NuGet usługi Azure Monitor application insights
description: Listy pakietów NuGet usługi Azure Monitor aplikacji dla ASP.NET, ASP.NET Core, Python
ms.topic: reference
ms.date: 10/16/2018
ms.openlocfilehash: 5675c0fad61f1b75aa7e93db02246eed43967ec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670002"
---
# <a name="application-insights-nuget-packages"></a>Pakiety NuGet usługi Application Insights

Poniżej znajduje się aktualna lista stabilnej wersji Pakiety NuGet dla usługi Application Insights.

## <a name="common-packages-for-aspnet"></a>Typowe pakiety dla ASP.NET

| Nazwa pakietu | Stabilna wersja | Opis | Pobierz |
|-------------------------------|-----------------------|------------|----|
| Statystyki aplikacji firmy Microsoft.Application | 2.12.0 | Zapewnia podstawowe funkcje przesyłania wszystkich typów telemetrii usługi Application Insights i jest pakietem zależnym dla wszystkich innych pakietów usługi Application Insights | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Umożliwia przechwytywanie wywołań metod | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Moduł zbierający zależności usługi Application Insights dla aplikacji .NET. Jest to pakiet zależny dla pakietów specyficznych dla platformy usługi Application Insights i zapewnia automatyczne zbieranie danych telemetrycznych zależności. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Moduł zbierający liczniki wydajności usługi Application Insights umożliwia wysyłanie danych zebranych przez liczniki wydajności do usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Witryna Microsoft.ApplicationInsights.Web | 2.12.0 | Usługa Application Insights dla aplikacji sieci Web .NET | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Serwer Windows.ApplicationInsights.WindowsServer firmy Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Pakiet NuGet usługi Application Insights w systemie Windows Server zapewnia automatyczne zbieranie danych telemetrycznych szczegółowych informacji o aplikacjach dla aplikacji .NET. Ten pakiet może służyć jako pakiet zależny dla pakietów specyficznych dla platformy usługi Application Insights lub jako pakiet autonomiczny dla aplikacji platformy .NET, które nie są objęte pakietami specyficznymi dla platformy (na przykład dla ról procesu roboczego platformy .NET). | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Plik Microsoft.ApplicationInsights.WindowsServer.TelemetryKannel | 2.12.0 | Udostępnia kanał telemetryczny do sdk systemu Windows Server usługi Application Insights, który zachowa dane telemetryczne w scenariuszach trybu offline. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Typowe pakiety dla ASP.NET Core

| Nazwa pakietu | Stabilna wersja | Opis | Pobierz |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Usługa Application Insights dla ASP.NET podstawowych aplikacji sieci web. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Statystyki aplikacji firmy Microsoft.Application | 2.12.0 | Ten pakiet zapewnia podstawowe funkcje przesyłania wszystkich typów telemetrii usługi Application Insights i jest pakietem zależnym dla wszystkich innych pakietów usługi Application Insights | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Moduł zbierający zależności usługi Application Insights dla aplikacji .NET. Jest to pakiet zależny dla pakietów specyficznych dla platformy usługi Application Insights i zapewnia automatyczne zbieranie danych telemetrycznych zależności. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Moduł zbierający liczniki wydajności usługi Application Insights umożliwia wysyłanie danych zebranych przez liczniki wydajności do usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Serwer Windows.ApplicationInsights.WindowsServer firmy Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Pakiet NuGet usługi Application Insights w systemie Windows Server zapewnia automatyczne zbieranie danych telemetrycznych szczegółowych informacji o aplikacjach dla aplikacji .NET. Ten pakiet może służyć jako pakiet zależny dla pakietów specyficznych dla platformy usługi Application Insights lub jako pakiet autonomiczny dla aplikacji platformy .NET, które nie są objęte pakietami specyficznymi dla platformy (na przykład dla ról procesu roboczego platformy .NET). | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Plik Microsoft.ApplicationInsights.WindowsServer.TelemetryKannel | 2.12.0 | Udostępnia kanał telemetryczny do sdk systemu Windows Server usługi Application Insights, który zachowa dane telemetryczne w scenariuszach trybu offline. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Typowe pakiety dla Języka Python przy użyciu OpenCensus
| Nazwa pakietu | Stabilna wersja | Opis | Pobierz |
|-------------------------------|-----------------------|------------|----|
| opencensus-ext-azure | 1.0.0 | Usługa Application Insights dla aplikacji języka Python w usłudze Azure Monitor za pośrednictwem usługi OpenCensus. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-ext-django | 0.7.2 | Ten pakiet zapewnia integrację z biblioteką [django](https://pypi.org/project/django/) Pythona. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-django/) |
| opencensus-ext-kolby | 0.7.3 | Ten pakiet zapewnia integrację z biblioteką [kolby](https://pypi.org/project/flask/) Pythona. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-ext-httplib | 0.7.2 | Ten pakiet zapewnia integrację z biblioteką [http.client](https://docs.python.org/3/library/http.client.html) języka Python dla języka Python3 i [httplib](https://docs.python.org/2/library/httplib.html) dla języka Python2. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-httplib/) |
| opencensus-ext-logging | 0.1.0 | Ten pakiet wzbogaca rekordy dziennika o dane śledzenia. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-ext-mysql | 0.1.2 | Ten pakiet zapewnia integrację z biblioteką łączników języka Python mysql.This package provides integration with the Python [mysql-connector](https://pypi.org/project/mysql-connector/) library. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-ext-postgresql | 0.1.2 | Ten pakiet zapewnia integrację z biblioteką [Python psycopg2.](https://pypi.org/project/psycopg2/) | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-ext-pymongo | 0.7.1 | Ten pakiet zapewnia integrację z biblioteką [python pymongo.](https://pypi.org/project/pymongo/) | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-ext-pymysql | 0.1.2 | Ten pakiet zapewnia integrację z biblioteką Python [PyMySQL.](https://pypi.org/project/PyMySQL/) | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus-ext-piramida | 0.7.1 | Ten pakiet zapewnia integrację z biblioteką [piramidy](https://pypi.org/project/pyramid/) języka Python. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus-ext-requests | 0.7.2 | Ten pakiet zapewnia integrację z biblioteką [żądań](https://pypi.org/project/requests/) języka Python. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-ext-sqlalchemy | 0.1.2 | Ten pakiet zapewnia integrację z biblioteką [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) języka Python. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Słuchacze/kolekcjonerzy/appenders

| Nazwa pakietu | Stabilna wersja | Opis | Pobierz |
|-------------------------------|-----------------------|------------|----|
| Witryna Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Umożliwia przekazywanie zdarzeń z DiagnosticSource do application insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Usługa EventSourceListener usługi Application Insights umożliwia wysyłanie danych ze zdarzeń EventSource do usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Usługa Application Insights EtwCollector umożliwia wysyłanie danych z śledzenia zdarzeń dla systemu Windows (ETW) do usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Witryna Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Niestandardowy TraceListener umożliwiający wysyłanie komunikatów dziennika śledzenia do usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Witryna Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Niestandardowy appender umożliwiający wysyłanie komunikatów dziennika Log4Net do usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  niestandardowy cel umożliwiający wysyłanie wiadomości dziennika NLog do usługi Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Monitoruje wyjątki w aplikacji i automatycznie zbiera migawki do analizy w trybie offline. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Nazwa pakietu | Stabilna wersja | Opis | Pobierz |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Ten pakiet zapewnia automatyczną dekorację danych telemetrycznych z kontekstem sieci szkieletowej usług, w który działa aplikacja. Nie należy używać tego NuGet dla aplikacji macierzystej sieci szkieletowej usług. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Moduł application insights dla aplikacji sieci szkieletowej usług. Użyj tego NuGet tylko dla aplikacji macierzystej sieci szkieletowej usług. W przypadku aplikacji działających w kontenerach należy użyć pakietu Microsoft.ApplicationInsights.ServiceFabric. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Monitor stanu

| Nazwa pakietu | Stabilna wersja | Opis | Pobierz |
|-------------------------------|-----------------------|------------|----|
| Witryna Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Umożliwia zbieranie danych w czasie wykonywania dla aplikacji x64 | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Witryna Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Umożliwia zbieranie danych w czasie wykonywania dla aplikacji x86. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Pakiety te stanowią część podstawowych funkcji monitorowania środowiska wykonawczego w [Monitorze stanu](../../azure-monitor/app/monitor-performance-live-website-now.md). Nie musisz pobierać tych pakietów bezpośrednio, po prostu użyj instalatora Monitora stanu. Jeśli chcesz dowiedzieć się więcej o tym, jak te pakiety działają pod maską ten [wpis na blogu](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) od jednego z naszych programistów jest dobry początek.

## <a name="additional-packages"></a>Dodatkowe pakiety

| Nazwa pakietu | Stabilna wersja | Opis | Pobierz |
|-------------------------------|-----------------------|------------|----|
| Witryny microsoft.applicationinsights.AzureWebSites | 2.6.5 | To rozszerzenie umożliwia monitorowanie usługi Application Insights w usłudze Azure App Service. SDK w wersji 2.6.1. Instrukcje: Dodaj ustawienia aplikacji "APPINSIGHTS_INSTRUMENTATIONKEY" za pomocą ikeya i uruchom ponownie aplikację internetową, aby uzyskać efekt.| [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Ten pakiet zawiera pliki wymagane do iniekcji usługi Application Insights bez kodu | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Następne kroki

- [Monitoruj ASP.NET Core](../../azure-monitor/app/asp-net-core.md).
- Profil ASP.NET [aplikacje internetowe Core Azure Linux](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- Debugowanie [migawek](../../azure-monitor/app/snapshot-debugger.md)ASP.NET .
