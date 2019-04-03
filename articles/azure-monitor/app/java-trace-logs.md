---
title: Zapoznaj się z języka Java, dzienniki śledzenia w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Wyszukiwanie Log4J lub Logback ślady w usłudze Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: 614f9a44f7c699be38906ac00e12f523490ce112
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884299"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Zapoznaj się z języka Java, dzienniki śledzenia w usłudze Application Insights
Jeśli używasz, Logback lub Log4J (wersja 1.2 lub 2.0) do śledzenia, może mieć dzienników śledzenia automatycznie przesyłane do usługi Application Insights, gdzie możesz eksplorować i wyszukiwania na nich.

## <a name="install-the-java-sdk"></a>Instalowanie języka Java SDK

Postępuj zgodnie z instrukcjami, aby zainstalować [zestaw Application Insights SDK dla języka Java][java], jeśli już zrobione.

## <a name="add-logging-libraries-to-your-project"></a>Dodawanie rejestrowania biblioteki do projektu
*Wybierz odpowiedni sposób dla danego projektu.*

#### <a name="if-youre-using-maven"></a>Jeśli używasz narzędzia Maven...
Jeśli projekt jest już skonfigurowany do używania narzędzia Maven dla kompilacji, Scal jedną z następujących fragmenty kodu do pliku pom.xml.

Następnie Odśwież zależności projektu, aby pliki binarne zostały pobrane.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*1.2 Log4J*

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
Jeśli projekt jest już skonfigurowany do używania narzędzia Gradle dla kompilacji, należy dodać jeden z następujące wiersze do `dependencies` w z plikiem build.gradle:

Następnie Odśwież zależności projektu, aby pliki binarne zostały pobrane.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**1.2 Log4J**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>W innym przypadku...
Postępuj zgodnie z zasadami, aby ręcznie zainstalować zestaw SDK Java usługi Application Insights, Pobierz plik jar (po Maven Central kliknij Link "jar" w sekcji pobierania) dla odpowiednich appendera i dodawanie appendera pobrany plik jar do projektu.

| Rejestrator | Do pobrania | Biblioteka |
| --- | --- | --- |
| Logback |[Appendera Logback Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Log4J v2 appendera Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j v1.2 |[Log4J v1.2 appendera Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>Dodawanie appendera do preferowanej struktury rejestrowania
Aby rozpocząć pobieranie ślady, Scal odpowiedni fragment kodu do pliku konfiguracji mechanizmu Log4J lub Logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

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

*1.2 Log4J*

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

Appenders usługi Application Insights mogą być przywoływane przez dowolnego skonfigurowanego rejestratora i nie musi to być Rejestrator głównego (jak pokazano w przykładach kodu powyżej).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Eksploruj Twoje ślady w portalu Application Insights
Teraz, gdy skonfigurowano projekt, aby wysyłał informacje śledzenia do usługi Application Insights, można wyświetlać i wyszukiwać ślady te w portalu usługi Application Insights w [wyszukiwania] [ diagnostic] bloku.

Wyjątki przesłany za pośrednictwem rejestratorów będą wyświetlane w portalu jako dane telemetryczne dotyczące wyjątków.

![W portalu Application Insights Otwórz wyszukiwanie](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Kolejne kroki
[Wyszukiwanie diagnostyczne][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


