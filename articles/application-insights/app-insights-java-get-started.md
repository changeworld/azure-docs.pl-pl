---
title: Analiza aplikacji sieci Web w języku Java za pomocą usługi Application Insights | Microsoft Docs
description: 'Monitorowanie wydajności aplikacji sieci Web w języku Java za pomocą usługi Application Insights. '
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 36c70ff4d6f09faa99db2fdd06c6e2b27816c143
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Wprowadzenie do usługi Application Insights w projekcie sieci Web w języku Java


[Application Insights](https://azure.microsoft.com/services/application-insights/) jest rozszerzalną usługą analizy dla deweloperów sieci Web, która ułatwia zrozumienie wydajności i użycia aktywnej aplikacji. Służy do [wykrywania i diagnozowania problemów z wydajnością i wyjątków](app-insights-detect-triage-diagnose.md) oraz [pisania kodu][api] do śledzenia korzystania z aplikacji przez użytkowników.

![dane przykładowe](./media/app-insights-java-get-started/5-results.png)

Usługa Application Insights obsługuje aplikacje w języku Java działające w systemach Linux, Unix lub Windows.

Potrzebne elementy:

* Środowisko JRE w wersji 1.7 lub 1.8
* Subskrypcja platformy [Microsoft Azure](https://azure.microsoft.com/).

*Jeśli masz aplikację sieci Web, która już działa, możesz wykonać alternatywną procedurę, aby [dodać zestaw SDK w czasie wykonywania na serwerze sieci Web](app-insights-java-live.md). Ta alternatywa pozwala uniknąć ponownego kompilowania kodu, ale nie udostępnia opcji pisania kodu w celu śledzenia działań użytkownika.*

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Uzyskiwanie klucza instrumentacji usługi Application Insights
1. Zaloguj się do [Portalu Microsoft Azure](https://portal.azure.com).
2. Utwórz zasób usługi Application Insights. Jako typ aplikacji ustaw wartość Aplikacja sieci Web Java.

    ![Wypełnij nazwę, wybierz aplikację sieci Web Java i kliknij przycisk Utwórz](./media/app-insights-java-get-started/02-create.png)
3. Znajdź klucz instrumentacji nowego zasobu. Wkrótce będzie trzeba wkleić ten klucz do projektu kodu.

    ![W opisie nowego zasobu kliknij opcję Właściwości i skopiuj klucz instrumentacji](./media/app-insights-java-get-started/03-key.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Dodawanie zestawu SDK usługi Application Insights dla środowiska Java do projektu
*Wybierz odpowiedni sposób dla danego projektu.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Jeśli używasz narzędzia Maven... <a name="maven-setup" />
Jeśli projekt jest już skonfigurowany do używania narzędzia Maven w celu kompilacji, scal następujący kod z plikiem pom.xml.

Następnie odśwież zależności projektu, aby pliki binarne zostały pobrane.

```XML

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[2.0,)</version>
      </dependency>
    </dependencies>
```

* *Błędy kompilacji lub walidacji sumy kontrolnej?* Spróbuj użyć określonej wersji, np.: `<version>2.0.n</version>`. Najbardziej aktualną wersję znajdziesz w [informacjach o wersji zestawu SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) lub wśród [artefaktów Maven](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Trzeba zaktualizować zestaw SDK?* Odśwież zależności projektu.

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Jeśli używasz narzędzia Gradle... <a name="gradle-setup" />
Jeśli projekt jest już skonfigurowany do używania narzędzia Gradle w celu kompilacji, scal następujący kod z plikiem build.gradle.

Następnie odśwież zależności projektu, aby pliki binarne zostały pobrane.

```gradle

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '2.+'
      // or applicationinsights-core for bare API
    }
```

#### <a name="if-youre-using-eclipse-to-create-a-dynamic-web-project-"></a>Jeśli używasz środowiska Eclipse do tworzenia dynamicznego projektu internetowego...
Użyj [wtyczki zestawu SDK Application Insights dla środowiska Java][eclipse]. Uwaga: ta wtyczka pozwala szybciej rozpocząć pracę z usługą Application Insights (przy założeniu, że nie używasz programu Maven/Gradle), ale nie udostępnia funkcji systemu zarządzania zależnościami. Oznacza to, że zaktualizowanie wtyczki nie powoduje automatycznego zaktualizowania bibliotek usługi Application Insights w projekcie.

* *Błędy kompilacji lub walidacji sumy kontrolnej?* Spróbuj użyć określonej wersji, np.: `version:'2.0.n'`. Najbardziej aktualną wersję znajdziesz w [informacjach o wersji zestawu SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) lub wśród [artefaktów Maven](http://search.maven.org/#search%7Cga%7C1%7Capplicationinsights).
* *Aby zaktualizować zestaw SDK do nowej wersji*, odśwież zależności projektu.

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>W innym przypadku, jeśli ręcznie zarządzasz zależnościami...
Pobierz [najnowszą wersję](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) i skopiuj niezbędne pliki do projektu, zastępując wszystkie poprzednie wersje.

### <a name="questions"></a>Pytania...
* *Jaki jest związek między składnikami `-core` i `-web`?*
  * Element `applicationinsights-core` dostarcza podstawowy interfejs API. Ten składnik jest zawsze potrzebny.
  * Element `applicationinsights-web` dostarcza metryki do śledzenia liczby żądań HTTP i czasów odpowiedzi. Możesz pominąć ten składnik, jeśli nie chcesz automatycznie zbierać tych danych telemetrycznych. Na przykład jeśli chcesz zaprogramować zbieranie samodzielnie.
  
* *Jak zaktualizować zestaw SDK do najnowszej wersji?*
  * Jeśli używasz narzędzia Gradle lub Maven...
    * Zaktualizuj plik kompilacji, aby określić najnowszą wersję, lub użyj składni symboli wieloznacznych narzędzia Gradle/Maven, aby automatycznie uwzględnić najnowszą wersję. Następnie odśwież zależności projektu. Składnia symboli wieloznacznych jest widoczna w powyższych przykładach dla narzędzia [Gradle](#gradle-setup) lub [Maven](#maven-setup).
  * Jeśli ręcznie zarządzasz zależnościami...
    * Pobierz najnowszy [Zestaw SDK usługi Application Insights dla środowiska Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) i zastąp nim stary. Zmiany są opisane w [informacjach o wersji zestawu SDK](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-applicationinsightsxml-file"></a>3. Dodawanie pliku ApplicationInsights.xml
Dodaj plik ApplicationInsights.xml do folderu zasobów w projekcie lub upewnij się, że jest dodany do ścieżki klas wdrażania projektu. Skopiuj do niego następujący kod XML.

Zastąp klucz instrumentacji kluczem pobranym z portalu Azure.

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

Opcjonalnie plik konfiguracji może znajdować się w dowolnym miejscu, które jest dostępne dla aplikacji.  Właściwość systemu `-Dapplicationinsights.configurationDirectory` określa katalog, który zawiera plik ApplicationInsights.xml. Na przykład plik konfiguracji znajdujący się w lokalizacji `E:\myconfigs\appinsights\ApplicationInsights.xml` można skonfigurować za pomocą właściwości `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* Klucz instrumentacji jest wysyłany wraz z każdym elementem telemetrii i dzięki temu te elementy mogą być wyświetlane dla odpowiedniego zasobu usługi Application Insights.
* Składnik żądań HTTP jest opcjonalny. Powoduje automatyczne wysyłanie telemetrii dotyczącej żądań i czasów odpowiedzi do portalu.
* Korelacja zdarzenia jest dodatkiem do składnika żądań HTTP. Przypisuje identyfikator do każdego żądania odebranego przez serwer i dodaje go do każdego elementu telemetrii jako właściwość „Operation.Id”. Umożliwia korelowanie telemetrii skojarzonej z każdym żądaniem przez ustawienie filtru w [wyszukiwaniu diagnostycznym][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Alternatywne sposoby ustawienia klucza instrumentacji
Zestaw SDK usługi Application Insights szuka klucza w następującej kolejności:

1. Właściwość systemu: -DAPPLICATION_INSIGHTS_IKEY=Twój_klucz_ikey
2. Zmienna środowiskowa: APPLICATION_INSIGHTS_IKEY
3. Plik konfiguracji: ApplicationInsights.xml

Możesz również [ustawić klucz w kodzie](app-insights-api-custom-events-metrics.md#ikey):

```Java
    TelemetryConfiguration.getActive().setInstrumentationKey(iKey);
```

## <a name="4-add-an-http-filter"></a>4. Dodawanie filtru HTTP
Ostatni krok konfiguracji umożliwia składnikowi żądania HTTP rejestrowanie wszystkich żądań sieci Web. (Nie jest to wymagane, jeśli potrzebujesz tylko podstawowego interfejsu API).

### <a name="spring-boot-applications"></a>Aplikacje Spring Boot
Zarejestruj element `WebRequestTrackingFilter` usługi Application Insights w klasie konfiguracji:

```Java
package devCamp.WebApp.configurations;

    import javax.servlet.Filter;

    import org.springframework.boot.context.embedded.FilterRegistrationBean;
    import org.springframework.context.annotation.Bean;
    import org.springframework.core.Ordered;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.context.annotation.Configuration;
    import com.microsoft.applicationinsights.TelemetryConfiguration;
    import com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter;


    @Configuration
    public class AppInsightsConfig {

    //Initialize AI TelemetryConfiguration via Spring Beans
        @Bean
        public String telemetryConfig() {
            String telemetryKey = System.getenv("APPLICATION_INSIGHTS_IKEY");
            if (telemetryKey != null) {
                TelemetryConfiguration.getActive().setInstrumentationKey(telemetryKey);
            }
            return telemetryKey;
        }
    
    //Set AI Web Request Tracking Filter
        @Bean
        public FilterRegistrationBean aiFilterRegistration(@Value("${spring.application.name:application}") String applicationName) {
           FilterRegistrationBean registration = new FilterRegistrationBean();
           registration.setFilter(new WebRequestTrackingFilter(applicationName));
           registration.setName("webRequestTrackingFilter");
           registration.addUrlPatterns("/*");
           registration.setOrder(Ordered.HIGHEST_PRECEDENCE + 10);
           return registration;
       } 

    //Set up AI Web Request Tracking Filter
        @Bean(name = "WebRequestTrackingFilter")
        public Filter webRequestTrackingFilter(@Value("${spring.application.name:application}") String applicationName) {
            return new WebRequestTrackingFilter(applicationName);
        }   
    }
```

Ta klasa pozwala skonfigurować element `WebRequestTrackingFilter` jako pierwszy filtr w łańcuchu filtrów http. Umożliwia ona również pobranie klucza instrumentacji ze zmiennej środowiskowej systemu operacyjnego, jeśli jest dostępna.

> Używamy konfiguracji filtru internetowego http, a nie konfiguracji środowiska Spring MVC, ponieważ aplikacja Spring Boot ma własną konfigurację Spring MVC. Poniższe sekcje zawierają konfigurację specyficzną dla środowiska Spring MVC.


### <a name="applications-using-webxml"></a>Aplikacje używające pliku Web.xml
Znajdź i otwórz plik web.xml w projekcie, a następnie scal poniższy kod w obszarze węzła web-app, w którym skonfigurowano filtry aplikacji.

Aby uzyskać najbardziej dokładne wyniki, ten filtr powinien być mapowany przed wszystkimi innymi filtrami.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```

#### <a name="if-youre-using-spring-web-mvc-31-or-later"></a>Jeśli używasz środowiska Spring Web MVC 3.1 lub nowszego
Edytuj te elementy w pliku *-servlet.xml, aby uwzględnić pakiet usługi Application Insights:

```XML

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>
```

#### <a name="if-youre-using-struts-2"></a>Jeśli używasz środowiska Struts 2
Dodaj ten element do pliku konfiguracyjnego Struts (zwykle o nazwie struts.xml lub struts-default.xml):

```XML

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />
```

Jeśli masz interceptory zdefiniowane w stosie domyślnym, możesz dodać interceptor do tego stosu.

## <a name="5-run-your-application"></a>5. Uruchamianie aplikacji
Uruchom aplikację w trybie debugowania na komputerze deweloperskim albo opublikuj na serwerze.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Wyświetlanie telemetrii w usłudze Application Insights
Wróć do zasobu usługi Application Insights w witrynie [Microsoft Azure Portal](https://portal.azure.com).

W bloku przeglądu zostaną wyświetlone dane żądań HTTP. (Jeśli ich tam nie ma, odczekaj kilka sekund, a następnie kliknij przycisk Odśwież).

![dane przykładowe](./media/app-insights-java-get-started/5-results.png)

[Dowiedz się więcej o metrykach.][metrics]

Klikaj elementy wykresów, aby wyświetlać bardziej szczegółowe metryki zagregowane.

![](./media/app-insights-java-get-started/6-barchart.png)

> Usługa Application Insights zakłada, że format żądania HTTP dla aplikacji MVC to: `VERB controller/action`. Na przykład żądania `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` i `GET Home/Product/sdf96vws` są grupowane w ramach pozycji `GET Home/Product`. To grupowanie umożliwia zrozumiałe agregowanie żądań, na przykład podawanie liczby żądań i średniego czasu ich wykonania.
>
>

### <a name="instance-data"></a>Dane wystąpienia
Kliknij określony typ żądania, aby wyświetlić poszczególne wystąpienia.

W usłudze Application Insights są wyświetlane dwa rodzaje danych: dane zagregowane, przechowywane i wyświetlane jako średnie, liczniki i sumy, oraz dane wystąpienia — indywidualne raporty dotyczące żądań HTTP, wyjątków, wyświetleń stron lub zdarzeń niestandardowych.

Podczas wyświetlania właściwości żądania można wyświetlić skojarzone zdarzenia telemetrii, takie jak żądania i wyjątki.

![](./media/app-insights-java-get-started/7-instance.png)

### <a name="analytics-powerful-query-language"></a>Analiza: zaawansowany język zapytań
W miarę zgromadzenia większej ilości danych można uruchamiać zapytania zarówno w celu agregowania danych, jak i w celu znajdowania poszczególnych wystąpień.  [Analiza](app-insights-analytics.md) jest zaawansowanym narzędziem, którego można używać zarówno w celu poznania wydajności i użycia, jak i do celów diagnostycznych.

![Przykład analizy](./media/app-insights-java-get-started/025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Instalowanie aplikacji na serwerze
Teraz opublikuj aplikację na serwerze, pozwól z niej korzystać innym osobom, a następnie obejrzyj telemetrię wyświetlaną w portalu.

* Upewnij się, że zapora pozwala aplikacji na wysłanie telemetrii do tych portów:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Jeśli ruch wychodzący ma być kierowany przez zaporę, zdefiniuj właściwości systemu `http.proxyHost` i `http.proxyPort`.

* Na serwerach systemu Windows zainstaluj:

  * [Pakiet Microsoft Visual C++ Redistributable](http://www.microsoft.com/download/details.aspx?id=40784)

    Ten składnik umożliwia działanie liczników wydajności.


## <a name="exceptions-and-request-failures"></a>Wyjątki i błędy żądań
Nieobsługiwane wyjątki są zbierane automatycznie:

![Otwórz ustawienia, błędy](./media/app-insights-java-get-started/21-exceptions.png)

Istnieją dwie opcje zbierania danych o innych wyjątkach:

* [Wstawianie wywołań metody trackException() w kodzie][apiexceptions].
* [Instalacja agenta Java na serwerze](app-insights-java-agent.md). Trzeba określić metody, które chcesz śledzić.

## <a name="monitor-method-calls-and-external-dependencies"></a>Monitorowanie wywołań metod i zależności zewnętrznych
[Zainstaluj agenta programu Java](app-insights-java-agent.md) w celu rejestrowania określonych metod wewnętrznych i wywołań za pośrednictwem JDBC z danymi chronometrażu.

## <a name="performance-counters"></a>Liczniki wydajności
Otwórz pozycję **Ustawienia**, **Serwery**, aby wyświetlić zakres liczników wydajności.

![](./media/app-insights-java-get-started/11-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Dostosowywanie zbierania danych liczników wydajności
Aby wyłączyć zbieranie standardowego zestawu liczników wydajności, dodaj następujący kod w węźle głównym pliku ApplicationInsights.xml:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Zbieranie danych dodatkowych liczników wydajności
Możesz określić dodatkowe liczniki wydajności do zbierania danych.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Liczniki JMX (udostępniane przez maszynę wirtualną Java)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` — nazwa wyświetlana w portalu Application Insights.
* `objectName` — nazwa obiektu JMX.
* `attribute` — atrybut nazwy obiektu JMX do pobrania.
* `type` (opcjonalnie) — typ atrybutu obiektu JMX:
  * wartość domyślna: typ prosty, np. int lub long.
  * `composite`: dane licznika wydajności są w formacie „Atrybut.Dane”
  * `tabular`: dane licznika wydajności są w formacie wiersza tabeli

#### <a name="windows-performance-counters"></a>Liczniki wydajności systemu Windows
Każdy [licznik wydajności systemu Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) jest składową kategorii (w taki sam sposób, w jaki pole jest składową klasy). Kategorie mogą być globalne lub mogą mieć wystąpienia numerowane lub nazwane.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName — nazwa wyświetlana w portalu Application Insights.
* categoryName — kategoria licznika wydajności (obiekt wydajności), z którą skojarzony jest ten licznik wydajności.
* counterName — nazwa licznika wydajności.
* instanceName — nazwa wystąpienia kategorii licznika wydajności lub ciąg pusty (""), jeśli kategoria zawiera jedno wystąpienie. Jeśli categoryName to Proces, a licznik wydajności, którego dane chcesz zbierać, pochodzi z bieżącego procesu maszyny JVM, na której jest uruchomiona aplikacja, podaj wartość `"__SELF__"`.

Liczniki wydajności są widoczne jako metryki niestandardowe w [Eksploratorze metryk][metrics].

![](./media/app-insights-java-get-started/12-custom-perfs.png)

### <a name="unix-performance-counters"></a>Liczniki wydajności sytemu Unix
* [Zainstaluj program collectd z wtyczką Application Insights](app-insights-java-collectd.md), aby uzyskać szeroką gamę danych na temat systemu i sieci.

## <a name="get-user-and-session-data"></a>Pobieranie danych użytkownika i sesji
Wysyłasz już telemetrię z serwera sieci Web. Teraz, aby zyskać pełny wgląd w dane aplikacji, możesz dodać więcej opcji monitorowania:

* [Dodaj telemetrię do stron sieci Web][usage], aby monitorować wyświetlenia stron i metryki użytkowników.
* [Konfigurowanie testów sieci Web][availability], aby upewnić się, że aplikacja stale działa i odpowiada.

## <a name="capture-log-traces"></a>Przechwytywanie danych dziennika śledzenia
Usługi Application Insights mogą służyć do analizowania pod różnymi kątami danych z dzienników Log4J, Logback lub innych platform rejestrowania. Dzienniki można skorelować z żądaniami HTTP i inną telemetrią. [Dowiedz się, jak to zrobić][javalogs].

## <a name="send-your-own-telemetry"></a>Wysyłanie własnej telemetrii
Teraz, po zainstalowaniu zestawu SDK, możesz użyć interfejsu API do wysyłania własnej telemetrii.

* [Śledź niestandardowe zdarzenia i metryki][api], aby dowiedzieć się, jak użytkownicy korzystają z aplikacji.
* [Wyszukiwanie zdarzeń i dzienników][diagnostic], aby łatwiej diagnozować problemy.

## <a name="availability-web-tests"></a>Testy dostępności sieci Web
Usługa Application Insights może służyć do testowania witryny sieci Web w regularnych odstępach czasu, aby sprawdzić, czy witryna działa i odpowiada poprawnie. [Aby skonfigurować tę funkcję][availability], kliknij pozycję Testy sieci Web.

![Kliknij pozycję Testy sieci Web, a następnie kliknij pozycję Dodaj test sieci Web](./media/app-insights-java-get-started/31-config-web-test.png)

Uzyskasz wykresy czasów odpowiedzi oraz powiadomienia e-mail w razie wyłączenia witryny.

![Przykład testu sieci Web](./media/app-insights-java-get-started/appinsights-10webtestresult.png)

[Dowiedz się więcej o testach dostępności sieci Web.][availability]

## <a name="questions-problems"></a>Pytania? Problemy?
[Rozwiązywanie problemów z technologią Java](app-insights-java-troubleshoot.md)

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie wywołań zależności](app-insights-java-agent.md)
* [Monitorowanie liczników wydajności sytemu Unix](app-insights-java-collectd.md)
* Dodawanie [monitorowania do stron sieci Web](app-insights-javascript.md) w celu monitorowania czasów ładowania stron, wywołań AJAX i wyjątków przeglądarki
* Zapisywanie [niestandardowych danych telemetrycznych](app-insights-api-custom-events-metrics.md) w celu śledzenia użycia w przeglądarce lub na serwerze.
* Tworzenie [pulpitów nawigacyjnych](app-insights-dashboards.md) umożliwiających przygotowywanie kluczowych wykresów na potrzeby monitorowania systemu
* Tworzenie zaawansowanych zapytań dotyczących telemetrii z poziomu aplikacji przy użyciu usługi [Analytics](app-insights-analytics.md)
* Aby uzyskać więcej informacji, odwiedź stronę [Azure dla deweloperów języka Java](/java/azure).

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#trackexception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-javascript.md
