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
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: ce5f7ab1e6751a9ce68aa2d9c466a112c9cac182
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900612"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitoruj zależności, wyjątki przechwycony i czasy wykonania metody w aplikacji sieci web w języku Java


Jeśli masz [instrumentacji aplikacji sieci web Java za pomocą usługi Application Insights][java], agenta programu Java można użyć, aby uzyskać lepszy wgląd w dane, bez żadnych zmian w kodzie:

* **Zależności:** Dane dotyczące wywołań wykonywanych przez aplikację do innych składników, w tym:
  * **Wywołania REST** wprowadzone za pośrednictwem HttpClient, OkHttp i RestTemplate (Spring) są przechwytywane.
  * **Redis** wywołania za pośrednictwem klient Jedis są przechwytywane.
  * **[Wywołania JDBC](https://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**  — polecenia MySQL, SQL Server i Oracle DB automatycznie są przechwytywane. Jeśli wywołanie trwa dłużej niż 10s, agent programu MySQL, raporty planu zapytania.
* **Przechwycone wyjątki:** Informacje o wyjątkach, które są obsługiwane w kodzie.
* **Czas wykonywania metody:** Informacje o czas potrzebny do wykonania konkretnych metod.

Aby użyć agenta programu Java, zainstaluj na serwerze. Aplikacje sieci web musi być wyposażone w [zestawu SDK Java usługi Application Insights][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Zainstaluj agenta usługi Application Insights dla środowiska Java
1. Na maszynie z uruchomioną serwer Java [Pobierz agenta](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Upewnij się, aby pobrać tę samą wersję agenta Java jako pakiety zestawu SDK Java usługi Application Insights core i sieć web.
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

## <a name="additional-config-spring-boot"></a>Dodatkowe konfiguracji (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Dla usługi Azure App Services, wykonaj następujące czynności:

* Wybierz kolejno pozycje Ustawienia > Ustawienia aplikacji
* W obszarze Ustawienia aplikacji dodaj nową parę klucz-wartość:

Klucz: `JAVA_OPTS` Wartość: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.3.1-SNAPSHOT.jar`

Najnowszą wersję agenta Java na ten temat można znaleźć w wersji [tutaj](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

Agent musi zostać spakowana jako zasób w projekcie w taki sposób, że kończy się ono w D:/home/site/wwwroot/katalog. Można potwierdzić, że agenta jest w poprawnym katalogu usługi App Service, przechodząc do **narzędzia programistyczne** > **Narzędzia zaawansowane** > **konsoli debugowania**i sprawdzając zawartość katalogu witryny.    

* Zapisz ustawienia, a następnie ponownie uruchom aplikację. (Te kroki dotyczą tylko usług aplikacji w systemie Windows.)

> [!NOTE]
> W tym samym folderze należy Agent.xml sztucznej Inteligencji i plik jar agenta. Są one często wprowadzane razem w `/resources` folderu projektu.  

### <a name="spring-rest-template"></a>Spring Rest szablonu

Aby pomyślnie Instrumentacji wywołania HTTP dokonywane przy użyciu szablonu Rest firmy Spring usługi Application Insights Użyj klienta HTTP Apache jest wymagana. Domyślnie szablon Rest Spring firmy nie jest skonfigurowany do korzystania z klienta HTTP Apache. Określając [HttpComponentsClientHttpRequestfactory](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/client/HttpComponentsClientHttpRequestFactory.html) w Konstruktorze szablonu Rest Spring użyje Apache HTTP.

Oto przykład, jak to zrobić przy użyciu fasoli Spring. Jest to bardzo prosty przykład, który używa ustawień domyślnych klasę fabryki.

```java
@bean
public ClientHttpRequestFactory httpRequestFactory() {
return new HttpComponentsClientHttpRequestFactory()
}
@Bean(name = 'myRestTemplate')
public RestTemplate dcrAccessRestTemplate() {
    return new RestTemplate(httpRequestFactory())
}
```

#### <a name="enable-w3c-distributed-tracing"></a>Włącz śledzenie rozproszonych W3C

Dodaj następujący kod do sztucznej Inteligencji — Agent.xml:

```xml
<Instrumentation>
        <BuiltIn enabled="true">
            <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
        </BuiltIn>
    </Instrumentation>
```

> [!NOTE]
> Tryb zgodności z poprzednimi wersjami jest domyślnie włączona i parametr enableW3CBackCompat jest opcjonalne i powinny być używane tylko w przypadku, gdy chcesz wyłączyć tę funkcję. 

Najlepiej będzie to wymagane podczas wszystkie usługi zostały zaktualizowane do nowszej wersji zestawy SDK obsługujące protokół W3C. Zdecydowanie zaleca się przejść do nowszej wersji zestawów SDK z obsługą W3C tak szybko, jak to możliwe.

Upewnij się, że **zarówno [przychodzących](correlation.md#w3c-distributed-tracing) i wychodzące (agent), konfiguracje** są dokładnie takie same.

## <a name="view-the-data"></a>Wyświetlanie danych
W zasobie usługi Application Insights jest wyświetlana zagregowane zdalnego zależności i metoda czasy wykonania [w obszarze kafelka wydajności][metrics].

Aby wyszukać poszczególne wystąpienia zależności, wyjątek i metoda raporty, należy otworzyć [wyszukiwania][diagnostic].

[Diagnozowanie problemów z zależnością — Dowiedz się więcej](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Pytania? Problemy?
* Brak danych? [Wyjątki zapory dla zestawu](../../azure-monitor/app/ip-addresses.md)
* [Rozwiązywanie problemów z technologią Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
