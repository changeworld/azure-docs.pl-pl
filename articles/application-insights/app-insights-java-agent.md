---
title: Monitorowanie wydajności aplikacji sieci web w języku Java w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Rozszerzone monitorowanie wydajności i użycia witryny sieci Web Java za pomocą usługi Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: a62c6cbb6594aaf7c2c57d7efee1832d7b1430b7
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000725"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitoruj zależności, wyjątki przechwycony i czasy wykonania metody w aplikacji sieci web w języku Java


Jeśli masz [instrumentacji aplikacji sieci web Java za pomocą usługi Application Insights][java], agenta programu Java można użyć, aby uzyskać lepszy wgląd w dane, bez żadnych zmian w kodzie:

* **Zależności:** dane dotyczące wywołań wykonywanych przez aplikację do innych składników, w tym:
  * **Wywołania REST** wprowadzone za pośrednictwem HttpClient, OkHttp i RestTemplate (Spring) są przechwytywane.
  * **Redis** wywołania za pośrednictwem klient Jedis są przechwytywane.
  * **[Wywołania JDBC](https://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**  — polecenia MySQL, SQL Server i Oracle DB automatycznie są przechwytywane. Jeśli wywołanie trwa dłużej niż 10s, agent programu MySQL, raporty planu zapytania.
* **Przechwycono wyjątkami:** informacji o wyjątkach, które są obsługiwane w kodzie.
* **Czas wykonania metody:** informacji na temat czas potrzebny do wykonania konkretnych metod.

Aby użyć agenta programu Java, zainstaluj na serwerze. Aplikacje sieci web musi być wyposażone w [zestawu SDK Java usługi Application Insights][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Zainstaluj agenta usługi Application Insights dla środowiska Java
1. Na maszynie z uruchomioną serwer Java [Pobierz agenta](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Pamiętaj, aby pobrać ten sam verson agenta Java jako pakiety zestawu SDK Java usługi Application Insights core i sieć web.
2. Edytuj skrypt uruchamiania serwera aplikacji, a następnie dodaj poniższe maszyny JVM:
   
    `javaagent:`*Pełna ścieżka do pliku JAR agenta*
   
    Na przykład na serwerze Tomcat na maszynie z systemem Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Uruchom ponownie serwer aplikacji.

## <a name="configure-the-agent"></a>Konfigurowanie agenta
Utwórz plik o nazwie `AI-Agent.xml` i umieść go w tym samym folderze co plik JAR agenta.

Ustaw zawartość pliku xml. Zmodyfikuj poniższy przykład, aby uwzględnić lub pominąć funkcje, że chcesz.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

Należy włączyć raporty wyjątek i metoda chronometrażu dla poszczególnych metod.

Domyślnie `reportExecutionTime` ma wartość true i `reportCaughtExceptions` ma wartość false.

## <a name="view-the-data"></a>Wyświetlanie danych
W zasobie usługi Application Insights jest wyświetlana zagregowane zdalnego zależności i metoda czasy wykonania [w obszarze kafelka wydajności][metrics].

Aby wyszukać poszczególne wystąpienia zależności, wyjątek i metoda raporty, należy otworzyć [wyszukiwania][diagnostic].

[Diagnozowanie problemów z zależnością — Dowiedz się więcej](app-insights-asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Pytania? Problemy?
* Brak danych? [Wyjątki zapory dla zestawu](app-insights-ip-addresses.md)
* [Rozwiązywanie problemów z technologią Java](app-insights-java-troubleshoot.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
