---
title: Usługa Azure Application Insights — automatyczna kolekcja zależności | Dokumenty firmy Microsoft
description: Usługa Application Insights automatycznie zbiera i wizualizuje zależności
ms.topic: reference
author: nikmd23
ms.author: nimolnar
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.openlocfilehash: eaafe19f5112b433d50a34aa551aa84d196726a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665820"
---
# <a name="dependency-auto-collection"></a>Automatyczne zbieranie zależności

Poniżej znajduje się aktualnie obsługiwana lista wywołań zależności, które są automatycznie wykrywane jako zależności bez konieczności dodatkowej modyfikacji kodu aplikacji. Te zależności są wizualizowane w [mapach aplikacji](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) usługi Application Insights i w [widokach diagnostyki transakcji.](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) Jeśli zależności nie ma na poniższej liście, nadal można śledzić ją ręcznie za pomocą [wywołania zależności ścieżki](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Struktury aplikacji| Wersje |
| ------------------------|----------|
| ASP.NET Webforms | 4,5+ |
| ASP.NET MVC | 4+ |
| ASP.NET WebAPI | 4,5+ |
| ASP.NET Core | 1,1+ |
| <b>Biblioteki komunikacyjne</b> |
| [Funkcja HttpClient](https://www.microsoft.com/net/) | 4.5+, .NET Core 1.1+ |
| [Sqlclient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+, NuGet 4.3.0 |
| [SDK klienta EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [SDK klienta usługi ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Klienci magazynu</b>|  |
| ADO.NET | 4,5+ |

## <a name="java"></a>Java
| Serwery aplikacji | Wersje |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Struktury aplikacji</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Serwetą Jawaj | 3.1+ |
| <b>Biblioteki komunikacyjne</b> |  |
| [Klient Apache Http](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4,3+<sup>†</sup> |
| <b>Klienci magazynu</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [PostgreSQL (wsparcie beta)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [Mysql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>Rejestrowanie bibliotek</b> | |
| [Logowanie](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1,2+ |
| <b>Biblioteki metryk</b> |  |
| Jmx | 1,0+ |

> [!NOTE]
> * Z wyjątkiem reaktywnej obsługi programowania.
> <br>†Wymaga instalacji [agenta JVM.](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java)

## <a name="nodejs"></a>Node.js

| Biblioteki komunikacyjne | Wersje |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0,10+ |
| <b>Klienci magazynu</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [Rdzeń MongoDb](https://www.npmjs.com/package/mongodb-core) | 2.x - 3.x |
| [Mysql](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.16.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x - 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x - 2.x |
| <b>Rejestrowanie bibliotek</b> | |
| [Konsoli](https://nodejs.org/api/console.html) | 0,10+ |
| [Okręg wyborczy Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x - 3.x |

## <a name="javascript"></a>JavaScript

| Biblioteki komunikacyjne | Wersje |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Wszystkie |

## <a name="next-steps"></a>Następne kroki

- Skonfiguruj niestandardowe śledzenie zależności dla [platformy .NET](../../azure-monitor/app/asp-net-dependencies.md).
- Skonfiguruj niestandardowe śledzenie zależności dla [oprogramowania Java](../../azure-monitor/app/java-agent.md).
- Skonfiguruj niestandardowe śledzenie zależności dla [Języka OpenCensus Python](../../azure-monitor/app/opencensus-python-dependency.md).
- [Zapisywanie danych telemetrycznych zależności niestandardowych](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Zobacz [model danych](../../azure-monitor/app/data-model.md) dla typów usługi Application Insights i modelu danych.
- Zapoznaj się z [platformami](../../azure-monitor/app/platforms.md) obsługiwanymi przez usługa Application Insights.
