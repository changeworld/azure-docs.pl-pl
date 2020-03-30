---
title: 'Omówienie: Co to jest usługa Azure Time Series Insights? — usługa Azure Time Series Insights | Dokumenty firmy Microsoft'
description: Wprowadzenie do usługi Azure Time Series Insights — nowej usługi do analizy danych szeregów czasowych i obsługi rozwiązań IoT.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: a938dc69b2b0401a062b1db314d71100b4fbbc4f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77024385"
---
# <a name="what-is-azure-time-series-insights"></a>Co to jest usługa Azure Time Series Insights?

Usługa Azure Time Series Insights jest przeznaczona do przechowywania, wizualizowania i wykonywania zapytań o duże ilości danych szeregów czasowych, takich jak dane generowane przez urządzenia IoT. Jeśli chcesz przechowywać, odpytywać i wizualizować dane szeregów czasowych w chmurze oraz zarządzać nimi, usługa Time Series Insights będzie prawdopodobnie odpowiednia dla Ciebie.

[![Schemat blokowy usługi Time Series Insights](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

Usługa Time Series Insights ma cztery kluczowe zadania:

- Jest w pełni zintegrowany z bramami w chmurze, takimi jak Usługa Azure IoT Hub i usługi Azure Event Hubs. Łatwo łączy się z takimi źródłami zdarzeń i analizuje kod JSON z komunikatów i struktur z danymi w postaci czytelnych wierszy i kolumn. Łączy metadane z telemetrią i indeksuje dane w magazynie kolumnowym.
- Usługa Time Series Insights zarządza przechowywaniem danych. Aby upewnić się, że dane są zawsze łatwo dostępne, przechowuje dane w pamięci i ssd przez maksymalnie 400 dni. Możesz interaktywnie wysyłać zapytania o miliardy zdarzeń w sekundach na żądanie.
- Aplikacja Time Series Insights zapewnia wizualizację out-of-the-box za pośrednictwem Eksploratora Usługi Time Series Insights.
- Usługa Time Series Insights zapewnia usługę zapytań, zarówno w eksploratorze usługi Time Series Insights, jak i przy użyciu interfejsów API, które można łatwo zintegrować w celu osadzenia danych szeregów czasowych w aplikacjach niestandardowych.

Jeśli tworzysz aplikację do użytku wewnętrznego lub dla klientów zewnętrznych do użycia, można użyć usługi Time Series Insights jako zaplecza. Można go używać do indeksowania, przechowywania i agregowania danych szeregów czasowych. Aby utworzyć wizualizację niestandardową i środowisko użytkownika na górze, użyj [sdk klienta](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Aplikacja Time Series Insights jest również wyposażona w kilka [interfejsów API zapytań,](how-to-shape-query-json.md) aby włączyć te dostosowane scenariusze.

Dane szeregów czasowych przedstawiają, jak zasób lub proces zmienia się wraz z upływem czasu. Dane szeregów czasowych są indeksowane przez sygnatury czasowe, a czas jest najbardziej miarodajnym osią, wzdłuż której takie dane są zorganizowane. Dane szeregów czasowych zazwyczaj docierają w kolejności, więc są traktowane jako wstawianie, a nie aktualizacja bazy danych.

Może to być wyzwanie do przechowywania, indeksowania, kwerendy, analizowania i wizualizowania danych szeregów czasowych w dużych woluminach.
Usługa Azure Time Series Insights przechwytuje i przechowuje każde nowe zdarzenie jako wiersz, a zmiana jest efektywnie mierzona w czasie. W rezultacie możesz spojrzeć wstecz, aby wyciągnąć wnioski z przeszłości, aby pomóc przewidzieć przyszłe zmiany.

## <a name="video"></a>Film wideo

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Dowiedz się więcej o usłudze Azure Time Series Insights, chmurowej platformie analitycznej IoT.</br>

[![Wideo](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Podstawowe scenariusze

- Przechowuj dane szeregów czasowych w skalowalny sposób.

   Zasadniczo usługa Time Series Insights ma bazę danych zaprojektowaną z myślą o danych szeregów czasowych. Ponieważ jest skalowalny i w pełni zarządzany, usługa Time Series Insights obsługuje pracę przechowywania zdarzeń i zarządzania nimi.

- Eksploruj dane w czasie zbliżonym do rzeczywistego.

   Usługa Time Series Insights udostępnia eksploratora, który wizualizuje wszystkie dane, które strumieniuje do środowiska. Wkrótce po nawiązaniu połączenia ze źródłem zdarzeń można wyświetlać, eksplorować i wyszukiwać dane zdarzeń w aplikacji Time Series Insights. Dane pomagają sprawdzić, czy urządzenie emituje dane zgodnie z oczekiwaniami i monitorować zasoby IoT pod kątem kondycji, wydajności i ogólnej skuteczności.

- Przeprowadzaj analizę przyczyn źródłowych i wykrywaj anomalie.

   Aplikacja Time Series Insights oferuje narzędzia, takie jak wzorce i widoki perspektywy, umożliwiające przeprowadzanie i zapisywanie wieloetapowej analizy przyczyn źródłowych. Usługa Time Series Insights współpracuje również z usługami alertów, takimi jak usługa Azure Stream Analytics, dzięki czemu można wyświetlać alerty i wykryte anomalie w czasie zbliżonym do rzeczywistego w Eksploratorze usługi Time Series Insights.

- Uzyskaj globalny widok danych szeregów czasowych, które są przesyłane strumieniowo z różnych lokalizacji do porównywania wielu zasobów lub lokacji.

   Ze środowiskiem usługi Time Series Insights można połączyć wiele źródeł zdarzeń. W ten sposób można wyświetlać dane, które strumieniują z wielu różnych lokalizacji razem w czasie zbliżonym do rzeczywistego. Użytkownicy mogą korzystać z tej widoczności, aby udostępniać dane liderom biznesowym. Mogą lepiej współpracować z ekspertami w dziedzinie, którzy mogą zastosować swoją wiedzę, aby pomóc w rozwiązywaniu problemów, stosowaniu najlepszych praktyk i udostępniać wnioski.

- Tworzenie aplikacji klienta na podstawie usługi Time Series Insights.

   Usługa Time Series Insights udostępnia interfejsy API kwerendy REST, których można używać do tworzenia aplikacji korzystających z danych szeregów czasowych.

## <a name="capabilities"></a>Możliwości

- **Szybkie rozpoczęcie**pracy: usługa Azure Time Series Insights nie wymaga przygotowania danych z góry, dzięki czemu można szybko połączyć się z milionami zdarzeń w centrum IoT lub centrum zdarzeń. Po nawiązaniu połączenia można wizualizować dane z czujników i wchodzić w interakcje z nimi, aby szybko zweryfikować rozwiązania IoT. Możesz wchodzić w interakcje z danymi bez pisania kodu i nie musisz uczyć się nowego języka. Aplikacja Time Series Insights udostępnia szczegółową powierzchnię zapytań tekstowych dla zaawansowanych użytkowników oraz eksplorację wskaż i kliknij.

- **Informacje w czasie zbliżonym do rzeczywistego:** aplikacja Time Series Insights może spożywać miliony zdarzeń z czujników dziennie z jednominutowym opóźnieniem. Usługa Time Series Insights ułatwia uzyskiwanie wglądu w dane z czujników. Użyj go, aby dostrzec trendy i anomalie, przeprowadzić analizy przyczyn i uniknąć kosztownych przestojów. Krzyżowa korelacja między danymi w czasie rzeczywistym a historycznymi pomaga znaleźć ukryte trendy w danych.

- **Tworzenie niestandardowych rozwiązań:** Osadzanie danych usługi Azure Time Series Insights w istniejących aplikacjach. Można również tworzyć nowe rozwiązania niestandardowe za pomocą interfejsów API REST usługi Time Series Insights. Utwórz widoki spersonalizowane, które można udostępniać innym, aby umożliwić im eksplorowanie Twoich szczegółowych danych.

- **Skalowalność:** Usługa Time Series Insights została zaprojektowana do obsługi IoT na dużą skalę. Umożliwia pozyskiwanie od 1 miliona do 100 milionów zdarzeń dziennie, które domyślnie są przechowywane przez 31 dni. Możesz wizualizować i analizować strumienie danych na żywo w czasie zbliżonym do rzeczywistego, obok danych historycznych.

## <a name="get-started"></a>Wprowadzenie

Aby rozpocząć, wykonaj następujące kroki.

1. Aprowizuj środowisko usługi Time Series Insights w witrynie Azure portal.
1. Połącz się ze źródłem zdarzeń, takim jak centrum IoT lub centrum zdarzeń.
1. Przekaż dane referencyjne. Nie jest to dodatkowa usługa.
1. Przejrzyj i wyświetlaj dane w ciągu kilku minut za pomocą Eksploratora Usługi Time Series Insights.

## <a name="explorer"></a>Eksplorator

Wyświetlanie, analizowanie i odnajdowanie trendów w danych przy użyciu eksploratora usługi Azure Time Series Insights.

![Eksplorator usługi Time Series Insights](media/overview/time-series-insights-explorer-panel.png)

Dowiedz się, jak korzystać z [eksploratora usługi Azure Time Series Insights](time-series-insights-explorer.md) i czerpać szczegółowe informacje na podstawie danych.

## <a name="next-steps"></a>Następne kroki

- Poznaj ogólne [środowisko demonstracyjne](./time-series-quickstart.md)usługi Azure Time Series Insights.

- Dowiedz się więcej o planowaniu środowiska [usługi Time Series Insights.](time-series-insights-environment-planning.md)
