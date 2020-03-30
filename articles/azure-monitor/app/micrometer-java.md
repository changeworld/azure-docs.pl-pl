---
title: Jak korzystać z mikromierza z zestawem Java SDK usługi Azure Application Insights
description: Przewodnik krok po kroku dotyczący korzystania z mikrometru z wiosennym rozruchem aplikacji Insights i aplikacjami bez rozruchu sprężynowego.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 11/01/2018
ms.openlocfilehash: dd04087db32f0bbfa75dafa7e12c355e5ab7b515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670070"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Jak korzystać z mikromierza z zestawem Java SDK usługi Azure Application Insights
Monitorowanie aplikacji micrometer mierzy metryki dla kodu aplikacji opartego na platformie JVM i umożliwia eksportowanie danych do ulubionych systemów monitorowania. W tym artykule nauczy cię, jak używać mikrometru z aplikacjami Application Insights zarówno dla aplikacji Spring Boot, jak i innych niż Wiosenne rozruchy.

## <a name="using-spring-boot-15x"></a>Korzystanie z spring boot 1.5x
Dodaj następujące zależności do pliku pom.xml lub build.gradle: 
* [Aplikacja Insights spring-boot-starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter) 2.5.0 lub nowsza
* Micrometer Azure Registry 1.1.0 lub wyższy
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 lub powyżej (to backports kod autoconfig w ramach Wiosny).
* [Zasób ApplicationInsights](../../azure-monitor/app/create-new-resource.md )

Kroki

1. Zaktualizuj plik pom.xml aplikacji Spring Boot i dodaj w niej następujące zależności:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Zaktualizuj plik application.properties lub yml za pomocą klucza Instrumentacja usługi Application Insights przy użyciu następującej właściwości:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Tworzenie aplikacji i uruchamianie
2. Powyższe powinny rozpocząć z wstępnie zagregowane metryki automatycznie zbierane do usługi Azure Monitor. Szczegółowe informacje na temat dostrajania sprzężenia aplikacji Insights Spring Boot starter można znaleźć [w pliku readme w serwisie GitHub](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Korzystanie z sprężyny 2.x

Dodaj następujące zależności do pliku pom.xml lub build.gradle:

* Aplikacja Insights Spring-boot-starter 2.1.2 lub powyżej
* Azure-spring-boot-metrics-starters 2.0.7 lub nowsze
* [Zasób usługi Application Insights](../../azure-monitor/app/create-new-resource.md )

Kroki:

1. Zaktualizuj plik pom.xml aplikacji Spring Boot i dodaj w niej następującą zależność:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Zaktualizuj plik application.properties lub yml za pomocą klucza Instrumentacja usługi Application Insights przy użyciu następującej właściwości:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Tworzenie aplikacji i uruchamianie
4. Powyższe powinno cię uruchomić z wstępnie zagregowane metryki automatycznie zbierane do usługi Azure Monitor. Szczegółowe informacje na temat dostrajania sprzężenia aplikacji Insights Spring Boot starter można znaleźć [w pliku readme w serwisie GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Domyślne metryki:

*    Automatycznie skonfigurowane metryki dla Tomcat, JVM, Logback Metrics, Log4J metryki, Metryki uptime, Metryki procesora, Metryki fileDescriptor.
*    Na przykład, jeśli Netflix Hystrix jest obecny na ścieżce klasy, otrzymujemy również te dane. 
*    Następujące metryki mogą być dostępne przez dodanie odpowiednich fasoli. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcastCache, JCache)     
        - DataBaseTableMetrics (DanebaseTableMetrics) 
        - HibernacjaMetria 
        - Pomost Metryki 
        - Ok MetrykiHttp3 
        - Metryki platformy Kafka 

 

Jak wyłączyć automatyczną kolekcję danych: 
 
- Wskaźniki JVM: 
    - plik management.metrics.binders.jvm.enabled=false 
- Logback Metryki: 
    - management.metrics.binders.logback.enabled=false
- Wskaźniki czasu pracy bez przestojów: 
    - management.metrics.binders.uptime.enabled=false 
- Dane procesora:
    -  management.metrics.binders.processor.enabled=false 
- Dane oscriptory plików:
    - management.metrics.binders.files.enabled=false 
- Hystrix Metryki jeśli biblioteka na classpath: 
    - management.metrics.binders.hystrix.enabled=false 
- AspectJ Metryki jeśli biblioteka na classpath: 
    - spring.aop.enabled=false 

> [!NOTE]
> Określ powyższe właściwości w pliku application.properties lub application.yml aplikacji Spring Boot

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Korzystanie z mikromierza z aplikacjami internetowymi bez wiosennego rozruchu

Dodaj następujące zależności do pliku pom.xml lub build.gradle:

* Aplikacja Aplikacji Insights Web Auto 2.5.0 lub nowsze
* Micrometer Azure Registry 1.1.0 lub wyższy
* [Zasób usługi Application Insights](../../azure-monitor/app/create-new-resource.md )

Kroki:

1. Dodaj następujące zależności w pliku pom.xml lub build.gradle:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. Umieść `ApplicationInsights.xml` plik w folderze zasobów:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
    
       <!-- The key from the portal: -->
       <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
    
       <!-- HTTP request component (not required for bare API) -->
       <TelemetryModules>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
       </TelemetryModules>
    
       <!-- Events correlation (not required for bare API) -->
       <!-- These initializers add context data to each event -->
       <TelemetryInitializers>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
       </TelemetryInitializers>
    
    </ApplicationInsights>
    ```

3. Przykładowa klasa Servlet (emituje metrykę czasomierza):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

4. Przykładowa klasa konfiguracji:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

Aby dowiedzieć się więcej o danych, zapoznaj się z [dokumentacją mikrometra](https://micrometer.io/docs/).

Inny przykładowy kod dotyczący tworzenia różnych typów metryk można znaleźć w[oficjalnym repozytorium Micrometer GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Jak powiązać kolekcję dodatkowych metryk

### <a name="springbootspring"></a>SpringBoot/Wiosna

Utwórz fasolę odpowiedniej kategorii metrycznej. Na przykład, powiedzmy, że potrzebujemy metryki pamięci podręcznej Guawy:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Istnieje kilka metryk, które nie są domyślnie włączone, ale mogą być powiązane w powyższy sposób. Pełna lista znajduje się [w oficjalnym repozytorium Micrometer GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Aplikacje inne niż wiosenne
Dodaj następujący kod powiązania do pliku konfiguracyjnego:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o Micrometer, zobacz oficjalną [dokumentację micrometer](https://micrometer.io/docs).
* Aby dowiedzieć się więcej o źródle na platformie Azure, zobacz oficjalną [dokumentację wiosny na platformie Azure.](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable)
