---
title: Rozwiązywanie problemów z aplikacjami w projekcie sieci Web języka Java
description: Przewodnik rozwiązywania problemów — monitorowanie żywych aplikacji Java za pomocą usługi Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 04e98938bc5dd17816ae873f122073212275a414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657184"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Rozwiązywanie problemów oraz pytania i odpowiedzi dotyczące usługi Application Insights dla języka Java
Pytania lub problemy z [usługą Azure Application Insights w języku Java?][java] Oto kilka wskazówek.

## <a name="build-errors"></a>Błędy kompilacji
**W Eclipse lub Intellij Idea, podczas dodawania application insights SDK przez Maven lub Gradle, otrzymuję błędy sprawdzania poprawności kompilacji lub sumy kontrolnej.**

* Jeśli `<version>` element zależności używa wzorca ze znakami wieloznacznym (np. (Maven) `<version>[2.0,)</version>` lub (Gradle), `version:'2.0.+'`spróbuj `2.0.1`określić określoną wersję, a nie taką jak . Zobacz [informacje o wersji](https://github.com/Microsoft/ApplicationInsights-Java/releases) najnowszej wersji.

## <a name="no-data"></a>Brak danych
**Pomyślnie dodałem aplikację Application Insights i uruchomiłem aplikację, ale nigdy nie widziałem danych w portalu.**

* Zaczekaj chwilę i kliknij przycisk Odśwież. Wykresy odświeżają się okresowo, ale można również odświeżać ręcznie. Interwał odświeżania zależy od zakresu czasu wykresu.
* Sprawdź, czy w pliku ApplicationInsights.xml (w folderze zasobów w projekcie) zdefiniowano klucz instrumentacji lub skonfigurowano go jako zmienną środowiskową.
* Sprawdź, czy `<DisableTelemetry>true</DisableTelemetry>` w pliku xml nie ma węzła.
* W zaporze może być wymagane otwarcie portów TCP 80 i 443 w celu uzyskania ruchu wychodzącego do dc.services.visualstudio.com. Zobacz [pełną listę wyjątków zapory](../../azure-monitor/app/ip-addresses.md)
* Na tablicy startowej platformy Microsoft Azure przyjrzyj się mapie stanu usługi. Jeśli istnieją pewne wskazania alertów, poczekaj, aż powrócą do OK, a następnie zamknij i ponownie otwórz blok aplikacji usługi Application Insights.
* [Włącz rejestrowanie,](#debug-data-from-the-sdk) dodając `<SDKLogger />` element pod węzłem głównym w pliku ApplicationInsights.xml (w folderze zasobów w projekcie) i sprawdź wpisy poprzedzone AI: INFO/WARN/ERROR dla podejrzanych dzienników. 
* Upewnij się, że poprawny plik ApplicationInsights.xml został pomyślnie załadowany przez dysk Java SDK, patrząc na komunikaty wyjściowe konsoli dla instrukcji "Plik konfiguracji został pomyślnie znaleziony".
* Jeśli plik konfiguracyjny nie zostanie znaleziony, sprawdź komunikaty wyjściowe, aby zobaczyć, gdzie jest przeszukiwany plik konfiguracyjny, i upewnij się, że plik ApplicationInsights.xml znajduje się w jednej z tych lokalizacji wyszukiwania. Z reguły można umieścić plik konfiguracyjny w pobliżu jar SDK sdk aplikacji. Na przykład: w Tomcat oznaczałoby to web-INF/classes folderu. Podczas tworzenia można umieścić ApplicationInsights.xml w folderze zasobów projektu sieci web.
* Zajrzyj również na [stronę problemów z githubem, aby](https://github.com/Microsoft/ApplicationInsights-Java/issues) uzyskać znane problemy z plikiem SDK.
* Upewnij się, że używasz tej samej wersji aplikacji Application Insights core, web, agent i rejestracji aplikacji, aby uniknąć problemów z konfliktem wersji.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Kiedyś widziałem dane, ale przestał
* Sprawdź [blog o stanie](https://blogs.msdn.com/b/applicationinsights-status/).
* Czy osiągnąłeś miesięczny limit punktów danych? Otwórz ustawienia/przydział i ceny, aby się dowiedzieć. Jeśli tak, możesz uaktualnić plan lub zapłacić za dodatkową pojemność. Zobacz [schemat cenowy](https://azure.microsoft.com/pricing/details/application-insights/).
* Czy niedawno uaktualniono pakiet SDK? Upewnij się, że w katalogu projektu znajdują się tylko unikatowe słoiki SDK. Nie powinny być dwie różne wersje SDK obecne.
* Szukasz właściwego zasobu AI? Wprowadź poziom iKey aplikacji do zasobu, w którym oczekujesz danych telemetrycznych. Powinny być takie same.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Nie widzę wszystkich danych, których się spodziewam
* Otwórz stronę Użycie i szacowany koszt i sprawdź, czy [próbkowanie](../../azure-monitor/app/sampling.md) jest w użyciu. (100% transmisja oznacza, że pobieranie próbek nie działa.) Usługa application insights można ustawić, aby zaakceptować tylko ułamek danych telemetrycznych, który pochodzi z aplikacji. Pomaga to zachować w ramach miesięcznego przydziału danych telemetrycznych.
* Czy masz włączone pobieranie próbek SDK? Jeśli tak, dane będą pobierane z szybkością określoną dla wszystkich odpowiednich typów.
* Czy korzystasz ze starszej wersji java sdk? Począwszy od wersji 2.0.1, wprowadziliśmy mechanizm odporności na uszkodzenia do obsługi awarii sieci przerywanej i wewnętrznej bazy danych, a także trwałości danych na dyskach lokalnych.
* Czy otrzymujesz ograniczanie z powodu nadmiernej telemetrii? Jeśli włączysz rejestrowanie INFORMACJI, zostanie wyświetlony komunikat dziennika "Aplikacja jest ograniczona". Nasz obecny limit wynosi 32k elementów telemetrycznych na sekundę.

### <a name="java-agent-cannot-capture-dependency-data"></a>Agent Java nie może przechwytywać danych zależności
* Czy skonfigurowano agenta Java, wykonując [konfigurowanie programu Java Agent?](java-agent.md)
* Upewnij się, że zarówno jar agenta java, jak i plik AI-Agent.xml są umieszczone w tym samym folderze.
* Upewnij się, że zależność, którą próbujesz automatycznie zbierać, jest obsługiwana dla automatycznej kolekcji. Obecnie obsługujemy tylko MySQL, MsSQL, Oracle DB i Azure Cache dla kolekcji zależności Redis.

## <a name="no-usage-data"></a>Brak danych użycia
**Widzę dane o żądaniach i czasach odpowiedzi, ale nie ma widoku strony, przeglądarki ani danych użytkownika.**

Pomyślnie skonfigurowano aplikację do wysyłania danych telemetrycznych z serwera. Teraz następnym krokiem jest [skonfigurowanie stron internetowych do wysyłania danych telemetrycznych z przeglądarki internetowej.][usage]

Alternatywnie, jeśli klient jest aplikacją w [telefonie lub innym urządzeniu,][platforms]możesz wysłać z tego miejsca dane telemetryczne.

Użyj tego samego klucza instrumentacji, aby skonfigurować dane telemetryczne klienta i serwera. Dane pojawią się w tym samym zasobie usługi Application Insights i będzie można skorelować zdarzenia z klienta i serwera.


## <a name="disabling-telemetry"></a>Wyłączanie danych telemetrycznych
**Jak wyłączyć zbieranie danych telemetrycznych?**

W kodzie:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Lub**

Zaktualizuj plik ApplicationInsights.xml (w folderze zasobów w projekcie). Dodaj następujące elementy w węźle głównym:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Za pomocą metody XML, należy ponownie uruchomić aplikację po zmianie wartości.

## <a name="changing-the-target"></a>Zmiana celu
**Jak mogę zmienić zasób platformy Azure, do którego mój projekt wysyła dane?**

* [Pobierz klucz instrumentacji nowego zasobu.][java]
* Jeśli usługa Application Insights została dodana do projektu przy użyciu zestawu narzędzi Azure toolkit for Eclipse, kliknij prawym przyciskiem myszy projekt sieci Web, wybierz pozycję **Azure**, **Configure Application Insights**i zmień klucz.
* Jeśli klucz instrumentacji został skonfigurowany jako zmienna środowiskowa, zaktualizuj wartość zmiennej środowiskowej za pomocą nowego iKeya.
* W przeciwnym razie zaktualizuj klucz w pliku ApplicationInsights.xml w folderze zasobów w projekcie.

## <a name="debug-data-from-the-sdk"></a>Debugowanie danych z SDK

**Jak mogę dowiedzieć się, co robi SDK?**

Aby uzyskać więcej informacji na temat tego, `<SDKLogger/>` co dzieje się w interfejsie API, dodaj w węźle głównym pliku konfiguracji ApplicationInsights.xml.

### <a name="applicationinsightsxml"></a>Plik ApplicationInsights.xml

Można również poinstruować rejestratora, aby wyprowadził do pliku:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Rozrusznik sprężynowy

Aby włączyć rejestrowanie SDK za pomocą aplikacji Spring Boot apps przy `application.properties` użyciu aplikacji Application Insights Spring Boot Starter, dodaj do pliku następujące elementy:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

lub wydrukować do błędu standardowego:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Agent środowiska Java

Aby włączyć rejestrowanie agenta JVM zaktualizować [plik AI-Agent.xml:](java-agent.md)

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Właściwości wiersza polecenia Java
_Od wersji 2.4.0_

Aby włączyć rejestrowanie przy użyciu opcji wiersza polecenia, bez zmiany plików konfiguracyjnych:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

lub wydrukować do błędu standardowego:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Ekran startowy platformy Azure
**Patrzę na [portal Azure](https://portal.azure.com). Czy mapa mówi mi coś o mojej aplikacji?**

Nie, pokazuje kondycję serwerów platformy Azure na całym świecie.

*Jak znaleźć dane dotyczące mojej aplikacji na tablicy startowej platformy Azure (ekran główny).*

Zakładając, że [aplikacja została skonfigurowana dla usługi Application Insights,][java]kliknij przycisk Przeglądaj, wybierz pozycję Usługa Application Insights i wybierz zasób aplikacji utworzony dla aplikacji. Aby szybciej się tam dostać w przyszłości, możesz przypiąć aplikację do tablicy startowej.

## <a name="intranet-servers"></a>Serwery intranetowe
**Czy mogę monitorować serwer w intranecie?**

Tak, pod warunkiem, że serwer może wysyłać dane telemetryczne do portalu usługi Application Insights za pośrednictwem publicznego Internetu.

W zaporze może być wymagane otwarcie portów TCP 80 i 443 w celu uzyskania ruchu wychodzącego do dc.services.visualstudio.com i f5.services.visualstudio.com.

## <a name="data-retention"></a>Przechowywanie danych
**Jak długo dane są przechowywane w portalu? Czy jest to bezpieczne?**

Zobacz [Przechowywanie danych i prywatność][data].

## <a name="debug-logging"></a>Rejestrowanie debugowania
Aplikacja Insights `org.apache.http`używa . Jest to przenoszone do podstawowych słoików usługi Application Insights w obszarze nazw `com.microsoft.applicationinsights.core.dependencies.http`. Dzięki temu usługa Application Insights do obsługi `org.apache.http` scenariuszy, w których różne wersje tego samego istnieją w jednej bazie kodu.

>[!NOTE]
>Jeśli włączysz rejestrowanie poziomu DEBUG dla wszystkich obszarów nazw w aplikacji, będzie `org.apache.http` on `com.microsoft.applicationinsights.core.dependencies.http`honorowany przez wszystkie moduły wykonujące, w tym zmieniono nazwę na . Usługa Application Insights nie będzie mogła zastosować filtrowania dla tych wywołań, ponieważ wywołanie dziennika jest wywoływane przez bibliotekę Apache. Rejestrowanie poziomu DEBUGprodukuje znaczną ilość danych dziennika i nie jest zalecane dla wystąpień produkcji na żywo.


## <a name="next-steps"></a>Następne kroki
**Skonfiguruję usługę Application Insights dla aplikacji serwera Java. Co jeszcze mogę zrobić?**

* [Monitorowanie dostępności stron internetowych][availability]
* [Monitorowanie korzystania ze strony internetowej][usage]
* [Śledzenie użycia i diagnozowanie problemów w aplikacjach urządzeń][platforms]
* [Napisz kod, aby śledzić użycie aplikacji][track]
* [Przechwytywanie dzienników diagnostycznych][javalogs]

## <a name="get-help"></a>Uzyskiwanie pomocy
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Zgładzanie problemu w usłudze GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

