---
title: Eksplorowanie metryk w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Jak interpretować wykresów na Eksplorator metryk i dostosowywanie bloków Eksplorator metryk.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/08/2017
ms.author: mbullwin
ms.openlocfilehash: 66ef3330a435574405dbfb8b8c82d984bb877efe
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649627"
---
# <a name="exploring-metrics-in-application-insights"></a>Eksplorowanie metryk w usłudze Application Insights
Metryki w [usługi Application Insights] [ start] są mierzone wartości i liczby zdarzeń, które są wysyłane w danych telemetrycznych z Twojej aplikacji. Ułatwiają one wykryć problemy z wydajnością i obserwuj trendy w sposobu korzystania z aplikacji. Istnieje szereg standardowych metryk i można również utworzyć własne niestandardowe metryki i zdarzenia.

Liczby metryk i zdarzeń są wyświetlane na wykresach zagregowane wartości, takich jak sum, średnie lub liczby.

Poniżej przedstawiono przykładowy zestaw wykresy:

![](./media/app-insights-metrics-explorer/01-overview.png)

Wykresy metryk wszędzie, gdzie można znaleźć w portalu Application Insights. W większości przypadków można dostosowywać, a następnie można dodać więcej wykresów do bloku. Z poziomu bloku Przegląd kliknij, aby bardziej szczegółowe wykresy, (które mają tytułów, takich jak "Serwery z"), lub kliknij przycisk **Eksploratora metryk** aby otworzyć nowy blok, w którym można utworzyć niestandardowe wykresy.

## <a name="time-range"></a>Przedział czasu
Można zmienić zakres czasu objętych wykresy lub siatkach dowolnego bloku.

![Otwórz blok przeglądu aplikacji w witrynie Azure portal](./media/app-insights-metrics-explorer/03-range.png)

Jeśli oczekujesz części danych, które nie pojawiły się jeszcze, kliknij przycisk Odśwież. Wykresy odświeżane w odstępach czasu, ale interwały są dłuższe dla większych zakresy czasu. Może upłynąć trochę czasu dane docierają za pośrednictwem potoku analizy na wykres.

Umożliwiającą powiększenie fragmentu częścią wykresu, przeciągnij nad nią:

![Przeciągnij kursor nad częścią wykresu.](./media/app-insights-metrics-explorer/12-drag.png)

Kliknij przycisk Cofnij Powiększ, aby go przywrócić.

## <a name="granularity-and-point-values"></a>Poziom szczegółowości oraz punkt wartości
Umieść kursor myszy na wykresie, aby wyświetlić wartości metryk w tym momencie.

![Umieść kursor myszy nad wykresem](./media/app-insights-metrics-explorer/02-focus.png)

Wartość metryki w określonym punkcie jest agregowana w przedziałach próbkowania.

Interwał próbkowania lub "stopnia szczegółowości" jest wyświetlana w górnej części bloku.

![Nagłówek bloku.](./media/app-insights-metrics-explorer/11-grain.png)

Można dostosować poziom szczegółowości, w bloku zakres czasu:

![Nagłówek bloku.](./media/app-insights-metrics-explorer/grain.png)

Dostępne szczegółowości zależą od wybranego zakresu czasu. Jawne stopniach szczegółowości są alternatywy dla "Automatyczny" poziom szczegółowości dla zakresu czasu.


## <a name="editing-charts-and-grids"></a>Edytowanie siatki i wykresy
Aby dodać nowy wykres do bloku:

![W Eksploratorze metryk wybierz opcję Dodaj wykres](./media/app-insights-metrics-explorer/04-add.png)

Wybierz **Edytuj** na istniejącym lub nowym wykres, aby edytować jego zawartość:

![Wybierz co najmniej jedną metrykę](./media/app-insights-metrics-explorer/08-select.png)

Więcej niż jedną metrykę można wyświetlić na wykresie, chociaż istnieją ograniczenia dotyczące połączenia, które mogą być wyświetlane ze sobą. Tak szybko, jak zostanie wybrana jedna metryka, niektóre inne są wyłączone.

Jeśli zakodowane [metryki niestandardowe] [ track] z Twoją aplikacją (wywołania TrackMetric i TrackEvent) zostaną one wyświetlone w tym miejscu.

## <a name="segment-your-data"></a>Segmentacji danych
Możesz podzielić metryki przez właściwość — na przykład, aby porównać wyświetleń strony na klientach w różnych systemach operacyjnych.

Wybierz wykres lub siatkę, przejdź na grupowanie, a następnie wybierz właściwość, która ma Grupuj według:

![Wybierz grupowanie w, a następnie ustaw wybierz właściwość Group by](./media/app-insights-metrics-explorer/15-segment.png)

> [!NOTE]
> Gdy używasz grupowania, typy obszaru i wykres słupkowy zapewniają skumulowany wyświetlania. Jest to odpowiednia, gdzie to metoda agregacji to suma. Ale typ agregacji w przypadku średniej, typy ekranu wiersza lub siatkę.
>
>

Jeśli zakodowane [metryki niestandardowe] [ track] w swojej aplikacji i obejmują one wartości właściwości, będzie można wybrać właściwość na liście.

Wykres jest zbyt mały dla segmentowane dane? Dopasuj wysokość:

![Ustaw suwak](./media/app-insights-metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Typy agregacji
Legenda po stronie domyślnie zwykle zawiera zagregowaną wartość w okresie wykresu. Po umieszczeniu wskaźnika myszy nad wykresem pokazuje wartość w tym momencie.

Każdy punkt danych na wykresie jest agregacją wartości danych odebranych w poprzednim interwał próbkowania lub "stopnia szczegółowości". Stopień szczegółowości jest wyświetlany w górnej części bloku i zależy od ogólnej skali czasu wykresu.

Metryki może być agregowany na różne sposoby:

* **Liczba** jest liczba zdarzeń otrzymanych w interwale próbkowania. Służy do zdarzenia, takie jak żądania. Różnice w wysokość wykresu wskazuje wahania kursu, jaką wystąpienia zdarzenia. Jednak należy pamiętać, że wartość liczbowa zmienia się po zmianie interwału próbkowania.
* **Suma** dodaje wartości punktów danych odebranych za pośrednictwem interwał próbkowania lub okres wykresu.
* **Średnia** dzieli sumy przez liczbę punktów danych odebranych za pośrednictwem interwału.
* **Unikatowe** liczby są używane dla liczby użytkowników i kont. Dla interwału próbkowania lub w okresie wykresu ilustracji liczba różnych użytkowników, w tym czasie.
* **%** -Procent wersje każdej agregacji są używane tylko z wykresów segmentu. Suma zawsze dodaje do 100%, a wykres przedstawia względnego udziału różnych składników łącznie.

    ![Wartość procentowa agregacji](./media/app-insights-metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Zmień typ agregacji

![Edytuj wykres, a następnie wybierz pozycję agregacji](./media/app-insights-metrics-explorer/05-aggregation.png)

Domyślną metodą wszystkie metryki jest wyświetlana podczas tworzenia nowego wykresu lub kiedy są wyczyszczone wszystkie metryki:

![Usuń zaznaczenie wszystkich metryk, aby zobaczyć wartości domyślne](./media/app-insights-metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Numer PIN osi y 
Domyślnie wykres pokazuje wartości osi Y, zaczynając od zera do maksymalnej wartości w zakresie danych, aby zapewnić reprezentację wizualną quantum wartości. Jednak w niektórych przypadkach więcej niż quantum mogą być interesujące dla wizualnie badać drobne zmiany w wartościach. Dla dostosowania, takie jak to wykorzystania osi y w zakresie funkcji edycji, przypinanie osi y minimalną i maksymalną wartość w odpowiednim miejscu.
Kliknij pole wyboru "Zaawansowane ustawienia", aby wyświetlić zakres osi y ustawienia

![Kliknij przycisk Zaawansowane ustawienia, wybierz niestandardowy zakres i określ maksymalną wartości minimalnej](./media/app-insights-metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrowanie danych
Aby wyświetlić tylko metryk dla wybranego zestawu wartości właściwości:

![Kliknij filtr, rozwiń właściwość, a także sprawdzić niektóre wartości](./media/app-insights-metrics-explorer/19-filter.png)

Jeśli nie zaznaczysz żadnych wartości dla określonej właściwości jest taka sama jak wybierając je wszystkie: istnieje żaden filtr dla tej właściwości.

Zwróć uwagę, liczby zdarzeń, wraz z każdej wartości właściwości. Po wybraniu wartości jedną właściwość, zostaną skorygowane Liczby obok wartości innych właściwości.

Filtry mają zastosowanie do wszystkich schematów w bloku. Jeśli chcesz, aby różne filtrów stosowanych do różnych wykresów, Utwórz i Zapisz bloków różne metryki. Jeśli chcesz, możesz przypiąć wykresów z różnych bloków do pulpitu nawigacyjnego, tak, aby można je wyświetlić obok siebie.

### <a name="remove-bot-and-web-test-traffic"></a>Usuń przesyłów testu sieci web i bot
Użyj filtru **ruch rzeczywisty lub syntetyczny** i sprawdź **rzeczywistych**.

Można również filtrować według **źródło ruchu syntetycznego**.

### <a name="to-add-properties-to-the-filter-list"></a>Aby dodać właściwości do listy filtrów
Czy chcesz filtrowanie danych telemetrycznych na kategorii w wybranej przez użytkownika? Na przykład może podzielić się użytkowników w różnych kategoriach, a chcesz się podzielić dane według tych kategorii.

[Utwórz własną właściwość](app-insights-api-custom-events-metrics.md#properties). Ustaw go w [inicjatora Telemetrii](app-insights-api-custom-events-metrics.md#defaults) aby były wyświetlane wszystkie dane telemetryczne — w tym standardowe dane telemetryczne wysyłane przez różne moduły zestawu SDK.

## <a name="edit-the-chart-type"></a>Edytuj typ wykresu
Zwróć uwagę, że można przełączać się między siatki i wykresy:

![Wybierz w siatce lub na wykresie, a następnie wybierz typ wykresu](./media/app-insights-metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Zapisz blok metryk
Po utworzeniu Niektóre wykresy, zapisz je jako ulubione. Czy chcesz udostępnić go innym członkom zespołu, można wybrać, jeśli używasz konta organizacyjnego.

![Wybierz ulubiony](./media/app-insights-metrics-explorer/21-favorite-save.png)

Aby wyświetlić blok ponownie, **przejdź do bloku przeglądu** , a następnie otwórz Ulubione:

![W bloku Przegląd wybierz Ulubione](./media/app-insights-metrics-explorer/22-favorite-get.png)

Jeśli została wybrana opcja względny zakres czasu, po zapisaniu, bloku zostaną zaktualizowane przy użyciu najnowszych metryki. Jeśli została wybrana opcja bezwzględny zakres czasu, widoczna będzie te same dane co czasu.

## <a name="reset-the-blade"></a>Zresetować blok
Jeśli edytujesz blok, ale następnie chcesz wrócić do oryginalnego zapisano zestaw, wystarczy kliknąć resetowania.

![Na liście przyciski w górnej części Eksploratora metryk](./media/app-insights-metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Live Metrics Stream

Znacznie więcej natychmiastowej widok danych telemetrycznych, można otworzyć [Live Stream](app-insights-live-stream.md). Większość metryk potrwać kilka minut, ze względu na proces agregacji. Z drugiej strony metryki na żywo są optymalizowane w celu uzyskania niskich opóźnień. 

## <a name="set-alerts"></a>Ustawianie alertów
Aby otrzymywać powiadomienia pocztą e-mail wyjątkowe wartości dowolnej metryce, należy dodać alert. Możesz wybrać albo Wyślij wiadomość e-mail do administratorów konta lub adresów e-mail określonych.

![W Eksploratorze metryk Wybierz reguły alertów, Dodaj Alert](./media/app-insights-metrics-explorer/appinsights-413setMetricAlert.png)

[Dowiedz się więcej o alertach][alerts].


## <a name="continuous-export"></a>Ciągły eksport
Jeśli chcesz, aby dane wyeksportowane stale, dzięki czemu można przetwarzać go zewnętrznie, rozważ użycie [Eksport ciągły](app-insights-export-telemetry.md).

### <a name="power-bi"></a>Power BI
Jeśli chcesz, aby jeszcze dokładniej widoków danych, możesz to zrobić [eksportowanie do usługi Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Analiza
[Analiza](app-insights-analytics.md) jest bardziej wszechstronna sposób analizować dane telemetryczne za pomocą zaawansowanego języka zapytań. Użyj go, jeśli chcesz połączyć i obliczenia wyników metryk spośród lub wykonywać szczegółowe eksploracji ostatnią wydajność Twojej aplikacji. 

Z wykresu metryki możesz kliknąć ikonę analiza, aby przejść bezpośrednio do równoważne zapytania usługi Analytics.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
*Nie widzę żadnych danych na wykresie.*

* Filtry mają zastosowanie do wszystkich schematów w bloku. Upewnij się, że podczas, gdy są najbardziej istotne na jeden wykres, nie został ustawiony filtr, który nie obejmuje wszystkich danych na innym.

    Jeśli chcesz ustawić różne filtry na różnych wykresów, należy je utworzyć w różnych bloków, zapisać je jako osobne Ulubione. Jeśli chcesz, możesz przypiąć je do pulpitu nawigacyjnego tak, aby można je wyświetlić obok siebie.
* Jeśli wykres jest Grupuj według właściwości, która nie jest zdefiniowana w metryki, będą nic na wykresie. Spróbuj wyczyścić "Grupuj według", lub wybierz właściwość grupowania.
* Dane dotyczące wydajności (procesora CPU, we/wy i tak dalej) jest dostępna dla usług sieci web Java, aplikacjach klasycznych Windows [usług IIS i aplikacje sieci web usług jeśli instalowany jest monitor stanu](app-insights-monitor-performance-live-website-now.md), i [usług Azure Cloud Services](app-insights-azure.md). Nie jest dostępne dla usługi Azure websites.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Kolejne kroki
* [Monitorowanie użycia za pomocą usługi Application Insights](app-insights-web-track-usage.md)
* [Za pomocą wyszukiwanie diagnostyczne](app-insights-diagnostic-search.md)

<!--Link references-->

[alerts]: app-insights-alerts.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md
