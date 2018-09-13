---
title: Rozpoczynanie pracy z usługą Azure Application Insights za pomocą języka Java w środowisku Eclipse | Dokumentacja firmy Microsoft
description: Użyj wtyczki środowiska Eclipse, aby dodać monitorowanie wydajności i użycia do witryny sieci Web Java za pomocą usługi Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: e88c9f53-cd90-4abc-b097-1f170937908e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 12/12/2016
ms.author: mbullwin
ms.openlocfilehash: ebcfe02eb8d969af26f5121bda85e4610302e838
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647592"
---
# <a name="get-started-with-application-insights-with-java-in-eclipse"></a>Rozpoczynanie pracy z usługą Application Insights za pomocą języka Java w środowisku Eclipse
Zestaw SDK usługi Application Insights wysyła dane telemetryczne z aplikacji internetowej Java, dzięki czemu można analizować użycie i wydajność. Eclipse wtyczki dla usługi Application Insights automatycznie instaluje zestaw SDK w projekcie, dzięki czemu można uzyskiwać dostęp do najważniejszych danych telemetrycznych pole, a także interfejsu API, który umożliwia zapisywanie niestandardowych danych telemetrycznych.   

## <a name="prerequisites"></a>Wymagania wstępne
Aktualnie wtyczki sprawdza w przypadku projektami Maven oraz dynamiczne projekty sieci Web w środowisku Eclipse.
([Dodaj usługę Application Insights na inne typy projektów Java][java].)

Będą potrzebne:

* Środowisko JRE w wersji 1.7 lub 1.8
* Subskrypcja platformy [Microsoft Azure](https://azure.microsoft.com/).
* [Eclipse IDE for Java EE Developers](http://www.eclipse.org/downloads/), Indigo lub nowszej.
* Windows 7 lub nowszym lub Windows Server 2008 lub nowszy

Jeśli wolisz używać struktury Spring, spróbuj znaleźć potrzebne informacje w [przewodniku dotyczącym konfiguracji aplikacji inicjatora Spring Boot w celu korzystania z usługi Application Insights](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights)

## <a name="install-the-sdk-on-eclipse-one-time"></a>Zainstaluj zestaw SDK w środowisku Eclipse (raz)
Wystarczy zrobić to raz dla każdego komputera. Ta czynność powoduje zainstalowanie narzędzi, który można następnie dodać zestaw SDK do każdego dynamicznego projektu sieci Web.

1. W środowisku Eclipse kliknij przycisk Pomoc, zainstaluj nowe oprogramowanie.

    ![Pomoc, zainstaluj nowe oprogramowanie](./media/app-insights-java-eclipse/0-plugin.png)
2. Zestaw SDK jest w http://dl.microsoft.com/eclipse, w ramach zestawu narzędzi platformy Azure.
3. Usuń zaznaczenie pola wyboru **skontaktuj się z wszystkich witrynach aktualizacji...**

    ![Dla usługi Application Insights SDK, wyczyść skontaktuj się z wszystkimi witrynami aktualizacji](./media/app-insights-java-eclipse/1-plugin.png)

Wykonaj pozostałe kroki dla każdego projektu języka Java.

## <a name="create-an-application-insights-resource-in-azure"></a>Tworzenie zasobu usługi Application Insights na platformie Azure
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Utwórz nowy zasób usługi Application Insights. Jako typ aplikacji ustaw wartość Aplikacja internetowa Java.  

    ![Kliknij + i wybierz opcję Application Insights](./media/app-insights-java-eclipse/01-create.png)  

4. Znajdź klucz instrumentacji nowego zasobu. Wkrótce będzie trzeba wkleić go do projektu kodu.  

    ![W opisie nowego zasobu kliknij opcję Właściwości i skopiuj klucz instrumentacji](./media/app-insights-java-eclipse/03-key.png)  

## <a name="add-application-insights-to-your-project"></a>Dodaj usługę Application Insights do swojego projektu
1. Dodaj usługę Application Insights z menu kontekstowego projektu sieci web Java.

    ![W opisie nowego zasobu kliknij opcję Właściwości i skopiuj klucz instrumentacji](./media/app-insights-java-eclipse/02-context-menu.png)
2. Wklej klucz instrumentacji, która jest wyświetlana w witrynie Azure portal.

    ![W opisie nowego zasobu kliknij opcję Właściwości i skopiuj klucz instrumentacji](./media/app-insights-java-eclipse/03-ikey.png)

Klucz jest wysyłany wraz z każdym elementem telemetrii i informuje usługę Application Insights w celu wyświetlenia go w swoim zasobie.

## <a name="run-the-application-and-see-metrics"></a>Uruchom aplikację i zobaczyć metryki
Uruchom aplikację.

Wróć do zasobu usługi Application Insights w systemie Microsoft Azure.

W bloku przeglądu zostaną wyświetlone dane żądań HTTP. (Jeśli ich tam nie ma, odczekaj kilka sekund, a następnie kliknij przycisk Odśwież).

![Odpowiedź z serwera, liczby żądań i błędów ](./media/app-insights-java-eclipse/5-results.png)

Klikaj elementy wykresów, aby wyświetlać bardziej szczegółowe metryki.

![Liczby żądań według nazwy](./media/app-insights-java-eclipse/6-barchart.png)

[Dowiedz się więcej o metrykach.][metrics]

Podczas wyświetlania właściwości żądania, może uzyskać dostęp i zdarzenia telemetrii, skojarzone z nim, takie jak żądania i wyjątki.

![Wszystkie ślady dla tego żądania](./media/app-insights-java-eclipse/7-instance.png)

## <a name="client-side-telemetry"></a>Telemetria po stronie klienta
W bloku Szybki Start kliknij przycisk Pobierz kod umożliwiający monitorowanie stron sieci web:

![W bloku przeglądu aplikacji wybierz kolejno opcje Szybki start, Pobierz kod umożliwiający monitorowanie stron sieci Web. Skopiuj skrypt.](./media/app-insights-java-eclipse/02-monitor-web-page.png)

Wstaw fragment kodu w nagłówku pliki HTML.

#### <a name="view-client-side-data"></a>Wyświetlanie danych po stronie klienta
Otwórz zaktualizowane stron sieci web i używać ich. Poczekaj minutę lub dwie, a następnie wróć do usługi Application Insights i otwórz blok użycia. (Z poziomu bloku Przegląd przewiń w dół i kliknij pozycję użycia).

Strona widoku, użytkowników i sesji metryki pojawi się na blok użycia:

![Sesji, użytkowników i wyświetleń stron](./media/app-insights-java-eclipse/appinsights-47usage-2.png)

[Dowiedz się więcej o konfigurowaniu telemetrii po stronie klienta.][usage]

## <a name="publish-your-application"></a>Publikowanie aplikacji
Teraz opublikuj aplikację na serwerze, pozwól z niej korzystać innym osobom, a następnie obejrzyj telemetrię wyświetlaną w portalu.

* Upewnij się, że zapora pozwala aplikacji na wysłanie telemetrii do tych portów:

  * dc.services.visualstudio.com:443
  * dc.services.visualstudio.com:80
  * f5.services.visualstudio.com:443
  * f5.services.visualstudio.com:80
* Na serwerach systemu Windows zainstaluj:

  * [Pakiet Microsoft Visual C++ Redistributable](http://www.microsoft.com/download/details.aspx?id=40784)

    (Umożliwi to działanie liczników wydajności).

## <a name="exceptions-and-request-failures"></a>Wyjątki i błędy żądań
Nieobsługiwane wyjątki są zbierane automatycznie:

![](./media/app-insights-java-eclipse/21-exceptions.png)

Istnieją dwie opcje zbierania danych o innych wyjątkach:

* [Wstawianie wywołań metoda TrackException w kodzie](app-insights-api-custom-events-metrics.md#trackexception).
* [Instalacja agenta Java na serwerze](app-insights-java-agent.md). Trzeba określić metody, które chcesz śledzić.

## <a name="monitor-method-calls-and-external-dependencies"></a>Monitorowanie wywołań metod i zależności zewnętrznych
[Zainstaluj agenta programu Java](app-insights-java-agent.md) w celu rejestrowania określonych metod wewnętrznych i wywołań za pośrednictwem JDBC z danymi chronometrażu.

## <a name="performance-counters"></a>Liczniki wydajności
W bloku Przegląd, przewiń w dół i kliknij przycisk **serwerów** kafelka. Zobaczysz zakres liczników wydajności.

![Przewiń w dół, kliknij Kafelek serwery](./media/app-insights-java-eclipse/11-perf-counters.png)

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

![](./media/app-insights-java-eclipse/12-custom-perfs.png)

### <a name="unix-performance-counters"></a>Liczniki wydajności sytemu Unix
* [Zainstaluj program collectd z wtyczką Application Insights](app-insights-java-collectd.md), aby uzyskać szeroką gamę danych na temat systemu i sieci.

## <a name="availability-web-tests"></a>Testy dostępności sieci Web
Usługa Application Insights może służyć do testowania witryny sieci Web w regularnych odstępach czasu, aby sprawdzić, czy witryna działa i odpowiada poprawnie. [Aby skonfigurować][availability], przewiń w dół, kliknij opcję dostępność.

![Przewiń w dół, kliknij opcję Dostępność, a następnie opcję Dodaj test sieci Web](./media/app-insights-java-eclipse/31-config-web-test.png)

Uzyskasz wykresy czasów odpowiedzi oraz powiadomienia e-mail w razie wyłączenia witryny.

![Przykład testu sieci Web](./media/app-insights-java-eclipse/appinsights-10webtestresult.png)

[Dowiedz się więcej o testach dostępności sieci Web.][availability]

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Jeśli używasz, Logback lub Log4J (wersja 1.2 lub 2.0) do śledzenia, może mieć dzienników śledzenia automatycznie przesyłane do usługi Application Insights, gdzie możesz eksplorować i wyszukiwania na nich.

[Dowiedz się więcej na temat dzienników diagnostycznych][javalogs]

## <a name="custom-telemetry"></a>Telemetria niestandardowa
Wstaw kilka wierszy kodu w aplikację sieci web Java, aby dowiedzieć się, jak użytkownicy korzystają z nim lub aby łatwiej diagnozować problemy.

Wystarczy wstawić kod, zarówno w stronę sieci web języka JavaScript i Java po stronie serwera.

[Dowiedz się więcej o niestandardowych danych telemetrycznych][track]

## <a name="next-steps"></a>Kolejne kroki
#### <a name="detect-and-diagnose-issues"></a>Wykrywanie i diagnozowanie problemów
* [Dodawanie telemetrii klienta sieci web] [ usage] można pobrać danych telemetrycznych wydajności z klienta sieci web.
* [Konfigurowanie testów sieci Web][availability], aby upewnić się, że aplikacja stale działa i odpowiada.
* [Wyszukiwanie zdarzeń i dzienników][diagnostic], aby łatwiej diagnozować problemy.
* [Przechwytywanie danych śledzenia Log4J lub Logback][javalogs]

#### <a name="track-usage"></a>Śledzenie użycia
* [Dodawanie telemetrii klienta sieci web] [ usage] Aby monitorować wyświetlenia stron i metryki użytkowników podstawowych.
* [Śledź niestandardowe zdarzenia i metryki](app-insights-web-track-usage.md) Aby dowiedzieć się więcej o sposobie korzystania z aplikacji, zarówno na kliencie i serwerze.

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md
