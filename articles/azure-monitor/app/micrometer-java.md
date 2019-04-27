---
title: Jak używać mikrometryczne z zestawu SDK usługi Azure Application Insights Java | Dokumentacja firmy Microsoft
description: 'Przewodnik krok po kroku na temat używania mikrometryczne przy użyciu aplikacji Application Insights platformy Spring Boot i innych aplikacji Spring Boot. '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: lagayhar
ms.openlocfilehash: 778690fb2796cea3154b3acbb662341fdaea87da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699142"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Jak używać mikrometryczne z zestawu SDK usługi Azure Application Insights Java
Mikrometryczne aplikacji środki metryki monitorowania dla aplikacji w oparciu o JVM kodu i pozwala wyeksportować dane do systemów monitorowania Ulubione. Ten artykuł nauczy Cię sposób użycia mikrometryczne za pomocą usługi Application Insights dla aplikacji Spring Boot i aplikacje inne niż platformy Spring Boot.

## <a name="using-spring-boot-15x"></a>Przy użyciu platformy Spring rozruchu 1,5 x
Dodaj następujące zależności do pliku pom.xml lub build.gradle: 
* [Szczegółowe informacje aplikacji spring-boot-starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter)1.1.0-BETA lub nowszy
* Mikrometryczne rejestru Azure 1.1.0 lub nowszy
* [Starsza wersja Spring mikrometryczne](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 lub nowszej (jest to backports, kod autokonfiguracji Spring framework).
* [ApplicationInsights Resource](../../azure-monitor/app/create-new-resource.md )

Kroki

1. Zaktualizuj plik pom.xml aplikacji Spring Boot i dodaj następujące zależności w nim:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>1.1.0-BETA</version>
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
2. Zaktualizuj plik pliku application.properties lub yml klucz Instrumentacja usługi Application Insights przy użyciu następującej właściwości:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Skompiluj aplikację i uruchom
2. Powyższe powinien rozpocząć pracę, a dzięki automatycznej wstępnie zagregowane metryki zebrane do usługi Azure Monitor. Szczegółowe informacje na temat dostrajania Application Insights platformy Spring Boot starter można znaleźć na [readme w serwisie GitHub](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Za pomocą Spring 2.x

Dodaj następujące zależności do pliku pom.xml lub build.gradle:

* Application Insights Spring boot starter 2.1.2 lub nowszy
* Azure — platformy spring — rozruchu metryki uruchamiających 2.0.7 lub nowszy  
* [Zasób usługi Application Insights](../../azure-monitor/app/create-new-resource.md )

Kroki:

1. Zaktualizuj plik pom.xml aplikacji Spring Boot i dodaj następującą zależność w nim:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Zaktualizuj plik pliku application.properties lub yml klucz Instrumentacja usługi Application Insights przy użyciu następującej właściwości:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Skompiluj aplikację i uruchom
4. Powyższe powinna pojawić się został uruchomiony z funkcją automatycznego wstępnie zagregowane metryki zebrane do usługi Azure Monitor. Szczegółowe informacje na temat dostrajania Application Insights platformy Spring Boot starter można znaleźć na [readme w serwisie GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Domyślne metryki:

*    Automatycznie skonfigurowane metryki dla serwera Tomcat, JVM, Logback metryki, metryki Log4J, metryki czasu, metryki procesora, FileDescriptorMetrics.
*    Na przykład jeśli netflix hystrix znajduje się w ścieżce klasy uzyskujemy te metryki, jak również. 
*    Przez dodanie odpowiednich fasoli mogą być dostępne następujące metryki. 
        - CacheMetrics (CaffeineCache EhCache2, GuavaCache, HazelcaseCache, Jcache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - Metryki OkHttp3 
        - Kafka Metrics 

 

Jak wyłączyć zbieranie metryk automatycznych: 
 
- Metryki maszyny JVM: 
    - management.metrics.binders.jvm.enabled=false 
- Metryki Logback: 
    - management.metrics.binders.logback.enabled=false
- Metryki czasu działania: 
    - management.metrics.binders.uptime.enabled=false 
- Metryki procesora:
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics:
    - management.metrics.binders.files.enabled=false 
- Metryki Hystrix Jeśli biblioteki w ścieżce: 
    - management.metrics.binders.hystrix.enabled=false 
- Metryki AspectJ Jeśli biblioteki w ścieżce: 
    - spring.aop.enabled=false 

> [!NOTE]
> Określ właściwości powyżej w pliku application.properties lub application.yml pliku aplikacji Spring Boot

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Mikrometryczne za pomocą aplikacji sieci web - Spring Boot

Dodaj następujące zależności do pliku pom.xml lub build.gradle:
 
* [Podstawowe szczegółowe informacje o aplikacji 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.2.0) lub nowszy
* [Usługa Application Insights Web 2.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/2.2.0) lub nowszy
* [Rejestrowanie filtru sieci Web](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* Mikrometryczne rejestru Azure 1.1.0 lub nowszy
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
            <artifactId>applicationinsights-web</artifactId>
            <version>2.2.0</version>
        </dependency
     ```

2. Umieść Insights.xml aplikację w folderze zasobów

    Przykładowe klasy Serwletu (emituje metryki czasomierza):

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

      Przykładową klasę konfiguracji:

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

Aby dowiedzieć się więcej o wskaźnikach, zobacz [dokumentacji mikrometryczne](https://micrometer.io/docs/).

Inne przykładowy kod na temat tworzenia różnego rodzaju metryki można znaleźć w[oficjalne repozytorium GitHub mikrometryczne](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Jak powiązać dodatkowe metryki kolekcji

### <a name="springbootspring"></a>SpringBoot/Spring

Utwórz bean odpowiedniej kategorii metryki. Załóżmy na przykład, potrzebujemy guawa metryki pamięci podręcznej:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Istnieje kilka metryk, które nie są domyślnie włączone, ale może być powiązana w sposób powyżej. Aby uzyskać pełną listę, zobacz [oficjalne repozytorium GitHub mikrometryczne](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Aplikacje inne niż Spring
Dodaj następujący kod powiązania do pliku konfiguracji:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat mikrometryczne odnoszą się do oficjalnej [dokumentacji mikrometryczne](https://micrometer.io/docs).
* Aby dowiedzieć się więcej na temat platformy Spring na platformie Azure można znaleźć w oficjalnej [Spring w dokumentacji platformy Azure](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable).
