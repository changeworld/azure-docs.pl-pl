---
title: Eksplorowanie metryk w usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Jak interpretować wykresy w eksploratorze metryk i jak dostosować bloki eksploratora metryk.
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: f85a8fe79e7f4f820d7c0e5b942730305e892095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275895"
---
# <a name="exploring-metrics-in-application-insights"></a>Eksplorowanie metryk w usłudze Application Insights
Metryki w [usłudze Application Insights][start] są mierzone wartości i liczby zdarzeń, które są wysyłane w telemetrii z aplikacji. Ułatwiają one wykrywanie problemów z wydajnością i obserwowanie trendów w sposobie użycia aplikacji. Istnieje szeroki zakres standardowych metryk, a także można tworzyć własne niestandardowe metryki i zdarzenia.

> [!NOTE]
> W tym artykule opisano klasyczne środowisko eksploratora metryk, które jest obecnie przestarzałe i ostatecznie zostanie wycofane. Zalecamy zapoznanie się z nowym doświadczeniem, które jest opisane w [tym artykule](../platform/metrics-charts.md).

Metryki i liczby zdarzeń są wyświetlane na wykresach zagregowanych wartości, takich jak sumy, średnie lub liczby.

Oto przykładowy zestaw wykresów:

![](./media/metrics-explorer/01-overview.png)

Wykresy metryk można znaleźć wszędzie w portalu usługi Application Insights. W większości przypadków można je dostosować i można dodać więcej wykresów do bloku. W bloku Przegląd kliknij, aby uzyskać bardziej szczegółowe wykresy (które mają tytuły, takie jak "Serwery") lub kliknij **Metryki Explorer,** aby otworzyć nowy blok, w którym można tworzyć niestandardowe wykresy.

## <a name="time-range"></a>Przedział czasu
Można zmienić zakres czasu objęty wykresami lub siatkami na dowolnym bloku.

![Otwórz blok przeglądu aplikacji w witrynie Azure portal](./media/metrics-explorer/03-range.png)

Jeśli spodziewasz się danych, które jeszcze nie pojawiły się, kliknij przycisk Odśwież. Wykresy odświeżają się w odstępach czasu, ale interwały są dłuższe dla większych zakresów czasu. Może upłynąć trochę czasu dla danych, aby przejść przez potok analizy na wykresie.

Aby powiększyć część wykresu, przeciągnij nad nią kursor:

![Przeciągnij między częścią wykresu.](./media/metrics-explorer/12-drag.png)

Kliknij przycisk Cofnij powiększenie, aby go przywrócić.

## <a name="granularity-and-point-values"></a>Ziarnistość i wartości punktowe
Umieść wskaźnik myszy na wykresie, aby wyświetlić wartości metryk w tym momencie.

![Najedź kursorem myszy na wykres](./media/metrics-explorer/02-focus.png)

Wartość metryki w określonym punkcie jest agregowana w poprzednim przedziale próbkowania.

Interwał pobierania próbek lub "ziarnistość" jest wyświetlany w górnej części ostrza.

![Nagłówek bloku.](./media/metrics-explorer/11-grain.png)

Ziarnistość można regulować w bloku zakres czasu:

![Nagłówek bloku.](./media/metrics-explorer/grain.png)

Dostępne ziarnistości zależą od wybranego zakresu czasu. Jawne ziarnistość są alternatywami dla "automatycznego" szczegółowości dla zakresu czasu.


## <a name="editing-charts-and-grids"></a>Edytowanie wykresów i siatek
Aby dodać nowy wykres do bloku:

![W Eksploratorze metryk wybierz pozycję Dodaj wykres](./media/metrics-explorer/04-add.png)

Wybierz **pozycję Edytuj** na istniejącym lub nowym wykresie, aby edytować to, co pokazuje:

![Wybierz jedną lub więcej danych](./media/metrics-explorer/08-select.png)

Na wykresie można wyświetlić więcej niż jedną metrykę, ale istnieją ograniczenia dotyczące kombinacji, które mogą być wyświetlane razem. Jak tylko wybierzesz jedną metrykę, niektóre inne są wyłączone.

Jeśli zakodowane [metryki niestandardowe][track] w aplikacji (wywołania TrackMetric i TrackEvent) zostaną one wymienione tutaj.

## <a name="segment-your-data"></a>Segmentowanie danych
Metrykę można podzielić według właściwości — na przykład, aby porównać widoki strony na klientach z różnymi systemami operacyjnymi.

Wybierz wykres lub siatkę, włącz grupowanie i wybierz właściwość do zgrupowania według:

![Wybierz pozycję Grupowanie włączone, a następnie ustaw właściwość w grupuj według](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> Podczas grupowania typy wykresów obszarowych i słupkowych zapewniają skumulowany ekran. Jest to odpowiednie, gdy metoda agregacji jest Suma. Ale gdy typem agregacji jest Średnia, wybierz typy wyświetlania Linia lub Siatka.
>
>

Jeśli zakodowane [metryki niestandardowe][track] w aplikacji i zawierają wartości właściwości, będzie można wybrać właściwość na liście.

Czy wykres jest za mały dla danych segmentowanych? Dostosuj jego wysokość:

![Wyreguluj suwak](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Typy agregacji
Legenda z boku domyślnie pokazuje wartość zagregowane w okresie wykresu. Jeśli najedziesz kursorem na wykres, wartość w tym momencie zostanie wyświetle.

Każdy punkt danych na wykresie jest agregatem wartości danych otrzymanych w poprzednim przedziale próbkowania lub "ziarnistość". Ziarnistość jest wyświetlana w górnej części bloku i różni się w zależności od ogólnej skali czasu wykresu.

Metryki można agregować na różne sposoby:

* **Liczba** jest liczbą zdarzeń odebranych w interwale próbkowania. Jest on używany dla zdarzeń, takich jak żądania. Różnice w wysokości wykresu wskazują zmiany szybkości, z jaką występują zdarzenia. Należy jednak zauważyć, że wartość liczbowa zmienia się po zmianie interwału próbkowania.
* **Suma** sumuje wartości wszystkich punktów danych otrzymanych w interwale próbkowania lub w okresie wykresu.
* **Średnia** dzieli sumę przez liczbę punktów danych odebranych w danym przedziale.
* **Unikatowe** liczby są używane dla liczby użytkowników i kont. W interwale próbkowania lub w okresie wykresu rysunek pokazuje liczbę różnych użytkowników widzianych w tym czasie.
* **%**- procentowe wersje każdej agregacji są używane tylko z wykresami segmentowymi. Suma zawsze sumuje się do 100%, a na wykresie przedstawiono względny udział różnych składników sumy.

    ![Agregacja procentowa](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Zmienianie typu agregacji

![Edytowanie wykresu, a następnie wybieranie opcji Agregacja](./media/metrics-explorer/05-aggregation.png)

Metoda domyślna dla każdej metryki jest wyświetlana podczas tworzenia nowego wykresu lub gdy wszystkie metryki są odznaczone:

![Usuń zaznaczenie wszystkich danych, aby wyświetlić wartości domyślne](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Oś Styku Y 
Domyślnie wykres pokazuje wartości osi Y, zaczynając od zera do wartości maksymalnych w zakresie danych, aby dać wizualną reprezentację kwantowej wartości. Ale w niektórych przypadkach bardziej niż kwant może być interesujące wizualnie sprawdzić drobne zmiany wartości. W przypadku takich dostosowań użyj funkcji edycji zakresu osi Y, aby przypiąć minimalną lub maksymalną wartość osi Y w żądanym miejscu.
Kliknij pole wyboru "Ustawienia zaawansowane", aby przywołać ustawienia zakresu osi Y

![Kliknij pozycję Ustawienia zaawansowane, wybierz pozycję Zakres niestandardowy i określ wartości minimalne max](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrowanie danych
Aby wyświetlić tylko metryki dla wybranego zestawu wartości właściwości:

![Kliknij pozycję Filtruj, rozwiń właściwość i sprawdź niektóre wartości](./media/metrics-explorer/19-filter.png)

Jeśli nie wybierzesz żadnych wartości dla określonej właściwości, jest taka sama jak wybranie ich wszystkich: nie ma filtru na tej właściwości.

Zwróć uwagę na liczbę zdarzeń obok każdej wartości właściwości. Po wybraniu wartości jednej właściwości, liczby wraz z innymi wartościami właściwości są korygowane.

Filtry mają zastosowanie do wszystkich wykresów na bloku. Jeśli chcesz, aby różne filtry były stosowane do różnych wykresów, należy utworzyć i zapisać różne metryki bloków. Jeśli chcesz, możesz przypiąć wykresy z różnych bloków do pulpitu nawigacyjnego, aby można je było zobaczyć obok siebie.

### <a name="remove-bot-and-web-test-traffic"></a>Usuwanie ruchu testowego botów i stron internetowych
Użyj filtru **Real lub syntetycznego ruchu** i sprawdź **Real**.

Można również filtrować według **źródła ruchu syntetycznego**.

### <a name="to-add-properties-to-the-filter-list"></a>Aby dodać właściwości do listy filtrów
Czy chcesz filtrować dane telemetryczne według wybranej przez siebie kategorii? Na przykład możesz podzielić użytkowników na różne kategorie i chcesz podzielić dane według tych kategorii.

[Stwórz własną nieruchomość](../../azure-monitor/app/api-custom-events-metrics.md#properties). Ustaw go w [inicjatora telemetrii,](../../azure-monitor/app/api-custom-events-metrics.md#defaults) aby był wyświetlany we wszystkich danych telemetrycznych — w tym w standardowej telemetrii wysyłanej przez różne moduły zestawu SDK.

## <a name="edit-the-chart-type"></a>Edytowanie typu wykresu
Należy zauważyć, że można przełączać się między siatkami i wykresami:

![Zaznacz siatkę lub wykres, a następnie wybierz typ wykresu](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Zapisz ostrze metryki
Po utworzeniu niektórych wykresów zapisz je jako ulubione. Można wybrać, czy chcesz udostępnić go innym członkom zespołu, jeśli używasz konta instytucji.

![Wybierz pozycję Ulubione](./media/metrics-explorer/21-favorite-save.png)

Aby ponownie wyświetlić ostrze, **przejdź do bloku przeglądu** i otwórz ulubione:

![W bloku Przegląd wybierz pozycję Ulubione](./media/metrics-explorer/22-favorite-get.png)

Jeśli podczas zapisywania wybrano względny zakres czasu, blok zostanie zaktualizowany o najnowsze metryki. Jeśli wybrano bezwzględny zakres czasu, będzie ono wyświetlane za każdym razem.

## <a name="reset-the-blade"></a>Resetowanie ostrza
Jeśli edytujesz ostrze, ale chcesz wrócić do oryginalnego zapisanego zestawu, po prostu kliknij przycisk Resetuj.

![W przyciskach u góry Eksploratora metryk](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Transmisja na żywo

Aby uzyskać znacznie bardziej natychmiastowy widok danych telemetrycznych, otwórz live [stream](live-stream.md). Większość metryk trwa kilka minut, aby pojawić się, ze względu na proces agregacji. Z drugiej strony metryki na żywo są zoptymalizowane pod kątem małych opóźnień. 

## <a name="set-alerts"></a>Ustawianie alertów
Aby otrzymywać powiadomienia pocztą e-mail o nietypowych wartościach dowolnej metryki, dodaj alert. Możesz wysłać wiadomość e-mail do administratorów konta lub na określone adresy e-mail.

![W Eksploratorze metryk wybierz pozycję Reguły alertów, Dodaj alert](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[Dowiedz się więcej o alertach][alerts].


## <a name="continuous-export"></a>Ciągły eksport
Jeśli chcesz, aby dane były eksportowane w sposób ciągły, aby można było przetwarzać je zewnętrznie, należy rozważyć użycie [funkcji Eksport ciągły](../../azure-monitor/app/export-telemetry.md).

### <a name="power-bi"></a>Power BI
Jeśli chcesz jeszcze bogatsze widoki danych, możesz [wyeksportować do usługi Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Analiza
[Analytics](../../azure-monitor/app/analytics.md) to bardziej wszechstronny sposób analizowania danych telemetrycznych przy użyciu zaawansowanego języka zapytań. Użyj go, jeśli chcesz połączyć lub obliczyć wyniki z metryk lub przeprowadzić dogłębną analizę najnowszej wydajności aplikacji. 

Na wykresie metrycznym możesz kliknąć ikonę Analytics, aby przejść bezpośrednio do równoważnej kwerendy Analytics.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
*Nie widzę żadnych danych na wykresie.*

* Filtry mają zastosowanie do wszystkich wykresów na bloku. Upewnij się, że podczas ustawiania ostrości na jednym wykresie nie ustawiono filtru, który wyklucza wszystkie dane na innym.

    Jeśli chcesz ustawić różne filtry na różnych wykresach, utwórz je w różnych ostrzach, zapisz je jako oddzielne ulubione. Jeśli chcesz, możesz przypiąć je do pulpitu nawigacyjnego, aby można je było zobaczyć obok siebie.
* Jeśli pogrupujesz wykres według właściwości, która nie jest zdefiniowana na metryki, na wykresie nie będzie nic. Spróbuj wyczyścić opcję "grupuj według" lub wybierz inną właściwość grupowania.
* Dane dotyczące wydajności (procesora CPU, szybkość we/wy itd.) są dostępne dla usług internetowych Java, aplikacji klasycznych systemu Windows, [aplikacji internetowych i usług usług IIS po zainstalowaniu monitora stanu](../../azure-monitor/app/monitor-performance-live-website-now.md)oraz usług Azure Cloud [Services](../../azure-monitor/app/app-insights-overview.md). Nie jest dostępna dla witryn sieci Web platformy Azure.

## <a name="video"></a>Film wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie użycia za pomocą usługi Application Insights](../../azure-monitor/app/usage-overview.md)
* [Korzystanie z wyszukiwania diagnostycznego](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
