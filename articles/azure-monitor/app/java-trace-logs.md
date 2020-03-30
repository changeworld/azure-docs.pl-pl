---
title: Eksplorowanie dzienników śledzenia języka Java w usłudze Azure Application Insights
description: Śledzenie dzienników logowania lub logowania w aplikacji
ms.topic: conceptual
ms.date: 05/18/2019
ms.openlocfilehash: da1b76d52ab93f4d1be7196d6eb7286579481119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657218"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Eksplorowanie dzienników śledzenia języka Java w usłudze Application Insights
Jeśli używasz Logback lub Log4J (wersja 1.2 lub 2.0) do śledzenia, można mieć dzienniki śledzenia wysyłane automatycznie do usługi Application Insights, gdzie można eksplorować i wyszukiwać na nich.

> [!TIP]
> Wystarczy tylko raz ustawić klucz instrumentacji usługi Application Insights dla aplikacji. Jeśli używasz struktury, takiej jak Java Spring, być może klucz został już zarejestrowany w innym miejscu konfiguracji aplikacji.

## <a name="using-the-application-insights-java-agent"></a>Korzystanie z agenta Java usługi Application Insights

Domyślnie agent Java usługi Application Insights automatycznie przechwytuje `WARN` rejestrowanie wykonywane na poziomie i powyżej.

Można zmienić próg rejestrowania przechwycony `AI-Agent.xml` przy użyciu pliku:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging threshold="info"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

Przechwytywanie rejestrowania agenta Java można `AI-Agent.xml` wyłączyć za pomocą pliku:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging enabled="false"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>Alternatywnie (w przeciwieństwie do korzystania z agenta Java), można postępować zgodnie z poniższymi instrukcjami

### <a name="install-the-java-sdk"></a>Instalowanie pakietu Java SDK

Postępuj zgodnie z instrukcjami, aby zainstalować [zestaw SDK usługi Application Insights dla języka Java,][java]jeśli jeszcze tego nie zrobiono.

### <a name="add-logging-libraries-to-your-project"></a>Dodawanie bibliotek rejestrowania do projektu
*Wybierz odpowiedni sposób dla danego projektu.*

#### <a name="if-youre-using-maven"></a>Jeśli używasz narzędzia Maven...
Jeśli projekt jest już skonfigurowany do używania maven do kompilacji, scal jeden z następujących fragmentów kodu do pliku pom.xml.

Następnie odśwież zależności projektu, aby pobrać pliki binarne.

*Logowanie*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J w wersji 2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J w wersji 1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Jeśli używasz narzędzia Gradle...
Jeśli projekt jest już skonfigurowany do używania Gradle for build, dodaj jeden z następujących wierszy do `dependencies` grupy w pliku build.gradle:

Następnie odśwież zależności projektu, aby pobrać pliki binarne.

**Logowanie**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J w wersji 2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J w wersji 1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>W innym przypadku...
Postępuj zgodnie z wytycznymi, aby ręcznie zainstalować Application Insights Java SDK, pobierz słoik (Po przybyciu do Maven Central Page kliknij na link "jar" w sekcji pobierania) dla odpowiedniego appendera i dodaj pobrany słoik appender do projektu.

| Rejestratora | Pobierz | Biblioteka |
| --- | --- | --- |
| Logowanie |[Logback appender Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J w wersji 2.0 |[Log4J v2 appender Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j w wersji 1.2 |[Log4J v1.2 appender Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>Dodawanie aplikacji do struktury rejestrowania
Aby rozpocząć uzyskiwanie śladów, należy scalić odpowiedni fragment kodu do pliku konfiguracji Log4J lub Logback: 

*Logowanie*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J w wersji 2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J w wersji 1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Aplikacje usługi Application Insights mogą być odwoływane przez dowolny skonfigurowany rejestrator, a niekoniecznie przez rejestratora głównego (jak pokazano w przykładach kodu powyżej).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Eksploruj ślady w portalu usługi Application Insights
Teraz, gdy projekt został skonfigurowany do wysyłania śladów do usługi Application Insights, można wyświetlać i przeszukiwać te ślady w portalu usługi Application Insights w bloku [wyszukiwania.][diagnostic]

Wyjątki przesłane za pośrednictwem rejestratorów będą wyświetlane w portalu jako dane telemetryczne wyjątków.

![W portalu usługi Application Insights otwórz](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Następne kroki
[Wyszukiwanie diagnostyczne][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


