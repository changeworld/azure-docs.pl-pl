---
title: Rozwiązywanie problemów z usługi Application Insights w projekcie internetowym Java
description: Przewodnik rozwiązywania problemów — monitorowanie aplikacji w języku Java na żywo z usługą Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2018
ms.author: mbullwin
ms.openlocfilehash: eaade5f9ec9db7e8d224305147dafc264916d9c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60899503"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Rozwiązywanie problemów oraz pytania i odpowiedzi dotyczące usługi Application Insights dla języka Java
Pytania lub problemy z [usługi Azure Application Insights w języku Java][java]? Poniżej przedstawiono kilka wskazówek.

## <a name="build-errors"></a>Błędy kompilacji
**W środowisku Eclipse lub Intellij Idea, dodając zestaw SDK usługi Application Insights za pomocą narzędzia Maven lub Gradle pojawiają się błędy weryfikacji kompilacji lub sumy kontrolnej.**

* Jeśli zależność `<version>` element jest przy użyciu wzorca przy użyciu symboli wieloznacznych (np. (Maven) `<version>[2.0,)</version>` lub (Gradle) `version:'2.0.+'`), spróbuj określić określoną wersję zamiast tego, jak `2.0.1`. Zobacz [informacje o wersji](https://github.com/Microsoft/ApplicationInsights-Java/releases) dla najnowszej wersji.

## <a name="no-data"></a>Brak danych
**Pomyślnie dodano usługę Application Insights i uruchomiono aplikację, ale nigdy nie przejrzane danych w portalu.**

* Poczekaj chwilę, a następnie kliknij przycisk Odśwież. Odświeżanie samodzielnie okresowo wykresy, ale można również odświeżyć ręcznie. Interwał odświeżania, zależy od przedział czasu wykresu.
* Sprawdź, czy klucz Instrumentacji zdefiniowane w pliku ApplicationInsights.xml (w folderze zasobów w projekcie) lub skonfigurować jako zmienną środowiskową.
* Sprawdź, czy nie `<DisableTelemetry>true</DisableTelemetry>` węzła w pliku xml.
* W zaporze może być otworzyć porty TCP 80 i 443 dla ruchu wychodzącego do witryny dc.services.visualstudio.com. Zobacz [pełnej listy wyjątków zapory](../../azure-monitor/app/ip-addresses.md)
* W systemie Microsoft Azure, Rozpocznij tablicy, Przyjrzyj się mapa stanu usługi. W przypadku niektórych alertów oznaczeń, poczekaj na ich powrotu do OK i następnie zamknięcie i ponowne otwarcie bloku aplikacji usługi Application Insights.
* Włącz rejestrowanie w oknie konsoli środowiska IDE, dodając `<SDKLogger />` elementu w pliku ApplicationInsights.xml (w folderze zasobów w projekcie) i Wyszukaj wpisy poprzedzone znakiem sztucznej Inteligencji w węźle głównym: Informacje o/WARN/błąd dla żadnych dzienników podejrzanych.
* Upewnij się, że właściwy plik ApplicationInsights.xml został pomyślnie załadowany przez zestawu Java SDK, analizując komunikaty wyjściowe konsoli programu dla instrukcji "plik konfiguracji został odnalazł".
* Jeśli plik konfiguracji nie zostanie znaleziony, sprawdzić komunikaty wyjściowe, aby zobaczyć, gdzie są wyszukiwane w pliku config i upewnij się, że plik ApplicationInsights.xml znajduje się w jednej z tych lokalizacji wyszukiwania. Jako ogólną regułę można przyjąć można umieścić w pliku config w pobliżu Application Insights SDK JARs. Na przykład: na serwerze Tomcat, oznaczałoby to folder w sieci WEB-INF/klas. Podczas programowania można umieścić plik ApplicationInsights.xml w folderze Zasoby projektu sieci web.
* Można również znaleźć w [stronie problemów serwisu GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues) pod kątem znanych problemów z zestawem SDK.
* Upewnij się, aby użyć tej samej wersji usługi Application Insights podstawowych, sieci web, agenta i rejestrowanie appenders Aby uniknąć jakichkolwiek problemów konflikt wersji.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Używany do wyświetlania danych, ale została zatrzymana
* Sprawdź [blogu stan](https://blogs.msdn.com/b/applicationinsights-status/).
* Możesz przekroczyć miesięczny limit punktów danych? Otwórz ustawienia/przydział i cennik. Jeśli tak, możesz uaktualnić swój plan lub płacić za dodatkową pojemność. Zobacz [ceny schemat](https://azure.microsoft.com/pricing/details/application-insights/).
* Możesz ostatnio uaktualniono zestaw SDK? Upewnij się, że tylko plikach JAR unikatowy zestaw SDK znajdują się w katalogu projektu. Nie powinien być dwie różne wersje zestawu SDK jest obecny.
* Sprawdzasz zasobu usługi AI poprawne? Można dopasować klucz instrumentacji aplikacji do zasobu, w którym są oczekiwane dane telemetryczne. Powinny one być takie same.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Nie widzę wszystkich danych, których oczekiwano
* Otwórz użycie i szacowane kosztu strony i sprawdź, czy [próbkowania](../../azure-monitor/app/sampling.md) jest używany w operacji. (transmisji 100% oznacza, że pobieranie próbek nie jest w operacji). Usługa Application Insights można ustawić do akceptowania tylko ułamka telemetrii, że nadejściu ze swojej aplikacji. Dzięki temu można zachować w ramach miesięcznego limitu przydziału telemetrii.
* Czy masz próbkowania SDK włączone? Jeśli tak, dane będą próbkowane tak, zgodnie ze stawką określoną dla wszystkich odpowiednich typów.
* Czy używasz starszej wersji zestawu SDK języka Java Począwszy od wersji 2.0.1 wprowadziliśmy odporności na uszkodzenia mechanizm obsługi sieciowych okresowymi i błędy w wewnętrznej bazie danych, a także stan trwały danych na dyskach lokalnych.
* To wprowadzenie ograniczona z powodu nadmiernego telemetrię? Jeśli włączysz rejestrowanie informacji dziennika zostanie wyświetlony komunikat "Aplikacja jest ograniczona". Nasze aktualne ograniczenie wynosi 32 KB danych telemetrycznych elementów na sekundę.

### <a name="java-agent-cannot-capture-dependency-data"></a>Agenta Java nie może przechwytywać dane zależności
* Skonfigurowano usługę agenta Java wykonując [skonfigurować agenta Java](java-agent.md) ?
* Upewnij się, że zarówno jar agenta java, jak i plik Agent.xml sztucznej Inteligencji są umieszczane w tym samym folderze.
* Upewnij się, że zależności, w którym próbujesz się do automatycznego zebrania jest obsługiwana przez automatyczne tworzenie kolekcji. Aktualnie obsługiwany jest tylko MySQL, MsSQL, Oracle DB i pamięć podręczna systemu Azure dla usługi Redis zależności kolekcji.
* Czy używasz zestaw JDK 1.7 lub 1.8? Obecnie nie obsługujemy kolekcję zależności w zestaw JDK 9.

## <a name="no-usage-data"></a>Brak danych użycia
**Czy mogę zobaczyć dane dotyczące żądań, czasy odpowiedzi, ale nie widok strony, przeglądarki lub dane użytkownika.**

Pomyślnie ustawiono aplikacją wysyłającą dane telemetryczne z serwera. Po następnym krokiem jest [Konfigurowanie stron sieci web do wysyłania telemetrii za pomocą przeglądarki sieci web][usage].

Alternatywnie Jeśli Twój klient to aplikacja w [telefonu lub innego urządzenia][platforms], opisano wysyłanie danych telemetrycznych z tego miejsca.

Użyj tego samego klucza Instrumentacji do skonfigurowania klienta i serwera danych telemetrycznych. Dane będą wyświetlane w ten sam zasób usługi Application Insights i będzie można skorelować zdarzenia z klientem i serwerem.


## <a name="disabling-telemetry"></a>Wyłączanie telemetrii
**Jak wyłączyć zbieranie danych telemetrycznych?**

W kodzie:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Or**

Zaktualizuj plik ApplicationInsights.xml (w folderze zasobów w projekcie). Dodaj następujący kod w węźle głównym:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Za pomocą metody XML, należy uruchomić ponownie aplikację po zmianie wartości.

## <a name="changing-the-target"></a>Zmiana docelowej
**Jak zmienić mój projekt wysyła dane do zasobów platformy Azure?**

* [Pobierz klucz Instrumentacji nowego zasobu.][java]
* Jeśli usługa Application Insights jest dodawany do projektu przy użyciu zestawu narzędzi platformy Azure dla środowiska Eclipse, kliknij prawym przyciskiem myszy projekt sieci web, wybierz opcję **Azure**, **Konfiguruj usługę Application Insights**i zmienić wartość klucza.
* Jeśli klucz Instrumentacji ma skonfigurowany jako zmienną środowiskową przy użyciu nowego klucza iKey zaktualizuj wartości zmiennej środowiskowej.
* W przeciwnym razie można zaktualizować klucza w plik ApplicationInsights.xml w folderze zasobów w projekcie.

## <a name="debug-data-from-the-sdk"></a>Dane debugowania z zestawu SDK

**Jak mogę sprawdzić działania zestawu SDK?**

Aby uzyskać więcej informacji o tym, co się dzieje w interfejsie API, należy dodać `<SDKLogger/>` w węźle głównym pliku ApplicationInsights.xml konfiguracji.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

Można również poinstruować rejestratora do wypełniania wyjściowego pliku:

```XML
  <SDKLogger type="FILE">
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Spring Boot Starter

Aby włączyć rejestrowanie zestawu SDK przy użyciu aplikacji rozruchu Spring za pomocą Application Insights Spring Boot Starter, Dodaj następujące polecenie, aby `application.properties` pliku.:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Agenta Java

Aby włączyć rejestrowanie agenta maszyny JVM aktualizację [pliku Agent.xml sztucznej Inteligencji](java-agent.md).

```xml
<AgentLogger type="FILE">
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

## <a name="the-azure-start-screen"></a>Na ekranie startowym platformy Azure
**Szukam w [witryny Azure portal](https://portal.azure.com). Mapa Powiedz mi coś o mojej aplikacji?**

Nie, pokazuje kondycji serwerów platformy Azure na świecie.

*Azure: start tablicy (główną) jak znaleźć dane o mojej aplikacji?*

Zakładając, że możesz [Konfigurowanie aplikacji dla usługi Application Insights][java], kliknij przycisk Przeglądaj, wybierz usługę Application Insights, a następnie wybierz zasób aplikacji została utworzona dla twojej aplikacji. Aby uzyskać istnieje szybciej w przyszłości możesz przypiąć swoją aplikację do tablicy rozpoczęcia.

## <a name="intranet-servers"></a>Intranetowych serwerów
**Czy mogę monitorować serwer w moim intranecie?**

Tak, pod warunkiem, że serwer może wysyłać telemetrię do portalu usługi Application Insights za pośrednictwem publicznej sieci internet.

W zaporze może być otworzyć porty TCP 80 i 443 dla ruchu wychodzącego do witryny dc.services.visualstudio.com i f5.services.visualstudio.com.

## <a name="data-retention"></a>Przechowywanie danych
**Jak długo dane są przechowywane w portalu? Czy jest ono bezpieczne?**

Zobacz [retencji danych i prywatności][data].

## <a name="debug-logging"></a>Rejestrowanie debugowania
Usługa Application Insights używa `org.apache.http`. To jest przenoszony w plikach JAR z podstawowej usługi Application Insights w przestrzeni nazw `com.microsoft.applicationinsights.core.dependencies.http`. Dzięki temu usługa Application Insights do obsługi scenariuszy, w przypadku, gdy różne wersje tego samego `org.apache.http` istnieje w jednej bazy kodu.

>[!NOTE]
>Jeśli włączysz rejestrowanie na poziomie debugowania dla wszystkich przestrzeni nazw w aplikacji, będą uznawane przez wszystkie moduły wykonywanej w tym `org.apache.http` przemianowana `com.microsoft.applicationinsights.core.dependencies.http`. Usługa Application Insights nie będzie można zastosować filtrowanie dla tych wywołań, ponieważ wywołanie dziennika jest wykonywane przez bibliotekę Apache. Rejestrowanie debugowania na poziomie produkcji znaczne ilości danych dziennika i nie jest zalecane w przypadku wystąpienia w środowisku produkcyjnym.


## <a name="next-steps"></a>Kolejne kroki
**Dla mojej aplikacji serwera w języku Java I Konfigurowanie usługi Application Insights. Co jeszcze można zrobić?**

* [Monitorowanie dostępności stron sieci web][availability]
* [Monitorowanie użycia stron sieci web][usage]
* [Śledzenie użycia i diagnozować problemy w aplikacji dla urządzeń][platforms]
* [Napisz kod umożliwiający śledzenie użycia aplikacji][track]
* [Przechwytywanie dziennika diagnostycznego][javalogs]

## <a name="get-help"></a>Uzyskiwanie pomocy
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Prześlij zgłoszenie w witrynie GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

