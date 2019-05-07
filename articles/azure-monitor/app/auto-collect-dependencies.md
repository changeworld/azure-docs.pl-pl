---
title: Usługa Azure Application Insights — zależności automatycznego zbierania | Dokumentacja firmy Microsoft
description: Usługa Application Insights automatycznie gromadzenie i wizualizowanie zależności
services: application-insights
documentationcenter: .net
author: nikmd23
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.author: nimolnar
ms.openlocfilehash: 832f927f81b57fd16c202b855d8f1dbe0617ad56
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149894"
---
# <a name="dependency-auto-collection"></a>Automatyczne zbieranie zależności

Poniżej przedstawiono listę obsługiwanych obecnie wywołania zależności, które są automatycznie wykrywane jako zależności bez żadnych dodatkowych modyfikacji kodu aplikacji. Składa się z połączenia wychodzące do bibliotek komunikacji, klienci usług magazynowych, rejestrowanie i metryki biblioteki, a także połączenia przychodzące do struktur aplikacji i serwerów. Te zależności są wizualizowane w usłudze Application Insights [mapy aplikacji](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) i [Diagnostyka transakcji](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) widoków. Jeśli zależność nie znajduje się na poniższej liście, można nadal śledzić je ręcznie przy użyciu [śledzić wywołanie zależności](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Struktury aplikacji| Wersje |
| ------------------------|----------|
| ASP.NET Webforms | 4.5+ |
| ASP.NET MVC | 4+ |
| ASP.NET WebAPI | 4.5+ |
| ASP.NET Core | 1.1+ |
| <b> Biblioteki łączności</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5+, .NET Core 1.1+ |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+, NuGet 4.3.0 |
| [Zestaw SDK klienta EventHubs.](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [Zestaw SDK klienta usługi ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Klienci usług magazynowych</b>|  |
| ADO.NET | 4.5+ |
| <b>Rejestrowanie bibliotek</b> |  |
| ILogger | 1.1+ |
| System.Diagnostics.Trace | 4.5+ |
| [nLog](https://www.nuget.org/packages/NLog/) | 4.4.12+ |
| [log4net](https://www.nuget.org/packages/log4net/) | 2.0.8+ on NetStandard  1.3, 2.0.6+ on .NET 4.5+ |

## <a name="java"></a>Java
| Serwery aplikacji | Wersje |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Struktury aplikacji </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>Biblioteki łączności</b> |  |
| [Klient Http Apache](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3+<sup>†</sup> |
| <b>Klienci usług magazynowych</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [PostgreSQL (Obsługa wersji Beta)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>Rejestrowanie bibliotek</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>Biblioteki metryki</b> |  |
| JMX | 1.0+ |

> [!NOTE]
> * Z wyjątkiem reaktywne wsparcie programistyczne.
> <br>Instalacja †requires [agenta maszyny JVM](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Biblioteki łączności | Wersje |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0.10+ |
| <b>Klienci usług magazynowych</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.x - 3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.16.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | w wersji 6.x - 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x - 2.x |
| <b>Rejestrowanie bibliotek</b> | |
| [console](https://nodejs.org/api/console.html) | 0.10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x - 3.x |

## <a name="javascript"></a>JavaScript

| Biblioteki łączności | Wersje |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Wszyscy |

## <a name="next-steps"></a>Kolejne kroki

- Konfigurowanie zależności niestandardowej śledzenie [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Konfigurowanie zależności niestandardowej śledzenie [Java](../../azure-monitor/app/java-agent.md).
- [Zapisywanie danych telemetrycznych zależność niestandardową](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Zobacz [modelu danych](../../azure-monitor/app/data-model.md) dla usługi Application Insights typów i danych modelu.
- Zapoznaj się z [platform](../../azure-monitor/app/platforms.md) obsługiwane przez usługę Application Insights.
