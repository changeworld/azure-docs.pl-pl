---
title: Eksploruj dzienniki śledzenia w usłudze Azure Application Insights w języku Java | Dokumentacja firmy Microsoft
description: Wyszukiwanie narzędzia Log4J lub Logback dane śledzenia w usłudze Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: mbullwin
ms.openlocfilehash: fae3269e21d0f760ae77a70333047306c07c2961
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Eksploruj dzienniki śledzenia w usłudze Application Insights w języku Java
Jeśli używasz Logback lub Log4J (1.2 lub 2.0) śledzenia, może mieć dzienników śledzenia automatycznie przesyłane do usługi Application Insights, gdzie można eksplorować i ich wyszukiwanie.

## <a name="install-the-java-sdk"></a>Instalowanie oprogramowania Java SDK

Postępuj zgodnie z instrukcjami, aby zainstalować [zestaw SDK usługi Application Insights dla języka Java][java], jeśli jeszcze nie który.

## <a name="add-logging-libraries-to-your-project"></a>Dodaj do projektu biblioteki rejestrowania
*Wybierz odpowiedni sposób dla danego projektu.*

#### <a name="if-youre-using-maven"></a>Jeśli używasz narzędzia Maven...
Projekt jest skonfigurowana do używania Maven dla kompilacji, jeden z następujących fragmentów kodu scalić plik pom.xml.

Następnie Odśwież zależności projektu, aby uzyskać pobierane pliki binarne.

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
Jeśli projekt jest skonfigurowana do używania narzędzia Gradle dla kompilacji, Dodaj jedną z poniższych wierszy, aby `dependencies` w pliku build.gradle:

Następnie Odśwież zależności projektu, aby uzyskać pobierane pliki binarne.

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
Postępuj zgodnie z wytycznymi, aby ręcznie zainstalować zestaw SDK Java usługi Application Insights, Pobierz jar (po uzgadnianiu Maven centralnej strony kliknij łącze "jar" w sekcji pobierania) dla odpowiednich appendera i Dodaj jar pobrany appendera do projektu.

| Logger | Do pobrania | Biblioteka |
| --- | --- | --- |
| Logback |[Appendera Logback Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Log4J v2 appendera Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j v1.2 |[Log4J 1.2 appendera Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>Dodawanie appendera do Twojej struktury rejestrowania
Aby rozpocząć pobieranie danych śledzenia, Scal odpowiedni fragment kodu do pliku konfiguracji narzędzia Log4J lub Logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" />
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
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Appenders usługi Application Insights można odwoływać się przez wszystkie skonfigurowane rejestratora i niekoniecznie rejestratora głównego (jak pokazano w powyższych przykładach kodu).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Eksploruj dane śledzenia w portalu usługi Application Insights
Teraz, gdy skonfigurowano z projektem, aby wysyłał informacje śledzenia do usługi Application Insights, można wyświetlać i wyszukać te operacje śledzenia w portalu usługi Application Insights w [wyszukiwania] [ diagnostic] bloku.

Wyjątki przesłano za pośrednictwem rejestratorów będą wyświetlane w portalu jako dane telemetryczne wyjątku.

![Otwórz wyszukiwanie w portalu usługi Application Insights](./media/app-insights-java-trace-logs/10-diagnostics.png)

## <a name="next-steps"></a>Kolejne kroki
[Wyszukiwanie diagnostycznych][diagnostic]

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md


