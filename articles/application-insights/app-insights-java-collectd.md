---
title: Monitorowanie wydajności aplikacji sieci web Java w systemie Linux - Azure | Dokumentacja firmy Microsoft
description: Rozszerzone monitorowanie wydajności aplikacji witryny sieci Web Java z CollectD wtyczki dla usługi Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: 56e30ff4ee7765cd0950293999d93a9cf1fe5d36
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd: metryki wydajności systemu Linux w usłudze Application Insights


Aby zapoznać się z metryki wydajności systemu Linux w [usługi Application Insights](app-insights-overview.md), zainstaluj [collectd](http://collectd.org/), wraz z jej usługi Application Insights wtyczki. To rozwiązanie open source zbiera dane statystyczne różnych systemów i sieci.

Zazwyczaj będzie używać collectd, jeśli masz już [zinstrumentowane usługi sieci web Java z usługą Application Insights][java]. Udostępnia większej ilości danych, aby pomóc zwiększyć wydajność aplikacji lub diagnozowania problemów. 

![Wykresy próbki](./media/app-insights-java-collectd/sample.png)

## <a name="get-your-instrumentation-key"></a>Uzyskaj swój klucz Instrumentacji
W [portalu Microsoft Azure](https://portal.azure.com), otwórz [usługi Application Insights](app-insights-overview.md) zasobów, w którym ma być wyświetlana data. (Lub [utworzyć nowy zasób](app-insights-create-new-resource.md).)

Należy wykonać kopię klucza instrumentacji, która identyfikuje zasobu.

![Przeglądaj wszystkie, otwórz zasobu, a następnie w Essentials listy rozwijanej, zaznacz i skopiuj klucz Instrumentacji](./media/app-insights-java-collectd/02-props.png)

## <a name="install-collectd-and-the-plug-in"></a>Zainstaluj collectd i wtyczki
Na komputerach serwerów Linux:

1. Zainstaluj [collectd](http://collectd.org/) wersji 5.4.0 lub nowszej.
2. Pobierz [usługi Application Insights collectd zapisywania wtyczki](https://aka.ms/aijavasdk). Zanotuj numer wersji.
3. Kopiuj wtyczki JAR w `/usr/share/collectd/java`.
4. Edytuj `/etc/collectd/collectd.conf`:
   * Upewnij się, że [wtyczki Java](https://collectd.org/wiki/index.php/Plugin:Java) jest włączona.
   * Zaktualizuj JVMArg dla java.class.path uwzględnienie następujących JAR. Numer wersji odpowiadające ten, który można pobrać aktualizacji:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Dodaj następujący fragment kodu przy użyciu klucza Instrumentacji z zasobu:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

W tym miejscu jest częścią przykładowy plik konfiguracji:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Konfigurowanie innych [wtyczek collectd](https://collectd.org/wiki/index.php/Table_of_Plugins), które może zbierać różne dane z różnych źródeł.

Uruchom ponownie collectd zgodnie z jego [ręczne](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Wyświetl dane w usłudze Application Insights
Zasób usługi Application Insights, otwórz [Eksploratora metryk i Dodaj wykresy][metrics], wybierając metryki, aby wyświetlić z kategorię niestandardową.

![](./media/app-insights-java-collectd/result.png)

Domyślnie metryki są agregowane na wszystkich komputerach hosta, z których zebrano metryki. Aby wyświetlić metryki na hoście, w bloku szczegóły wykresu, włączyć grupowanie, a następnie wybierz pozycję do grupowania CollectD hosta.

## <a name="to-exclude-upload-of-specific-statistics"></a>Aby wykluczyć przekazywania poszczególnych statystyk
Domyślnie wtyczkę usługi Application Insights wysyła wszystkie dane zebrane przez wszystkie włączone collectd odczytu wtyczek. 

Aby wykluczyć danych z konkretnych źródeł danych lub wtyczek:

* Przeprowadź edycję pliku konfiguracji. 
* W `<Plugin ApplicationInsightsWriter>`, Dodaj dyrektywy wiersze następująco:

| Dyrektywy | Efekt |
| --- | --- |
| `Exclude disk` |Wyklucz wszystkie dane zebrane przez `disk` wtyczki |
| `Exclude disk:read,write` |Wyklucz źródła o nazwie `read` i `write` z `disk` wtyczki. |

Oddzielne dyrektywy z nowym wierszem.

## <a name="problems"></a>Problemy?
*Nie widać danych w portalu*

* Otwórz [wyszukiwania] [ diagnostic] aby zobaczyć, jeśli następować zdarzenia pierwotnych. Czasami dłużej wyświetlane w Eksploratorze metryk.
* Konieczne może być [Ustaw wyjątki zapory dla danych wychodzących](app-insights-ip-addresses.md)
* Włącz śledzenie w wtyczkę usługi Application Insights. Dodaj następujący wiersz w `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Otwórz terminal i uruchom collectd w trybie informacji pełnej, aby wyświetlić jego to jest raportowanie problemów:
  * `sudo collectd -f`

## <a name="known-issue"></a>Znany problem

Dodatek Application Insights zapisu jest niezgodna z niektórych wtyczek odczytu. Niektóre wtyczki czasami wysyłania "NaN", gdzie wtyczkę usługi Application Insights oczekuje liczba zmiennoprzecinkowa.

Objaw: Collectd dziennik zawiera informacje o błędach, które obejmują "AI:... SyntaxError: nieoczekiwany token N ".

Obejście problemu: Wykluczanie danych zbieranych przez dodatki zapisu problem. 

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md


