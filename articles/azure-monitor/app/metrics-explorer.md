---
title: Eksplorowanie metryk na platformie Azure Application Insights | Microsoft Docs
description: Jak interpretować wykresy w Eksploratorze metryk oraz jak dostosować bloki w Eksploratorze metryk.
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: f85a8fe79e7f4f820d7c0e5b942730305e892095
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78368269"
---
# <a name="exploring-metrics-in-application-insights"></a>Eksplorowanie metryk w Application Insights
Metryki w [Application Insights][start] są mierzonymi wartościami i liczbami zdarzeń wysyłanych w ramach telemetrii z aplikacji. Pomagają wykrywać problemy z wydajnością i obserwować trendy w sposobie używania aplikacji. Istnieje szeroki zakres metryk standardowych i można również utworzyć własne, niestandardowe metryki i zdarzenia.

> [!NOTE]
> W tym artykule opisano klasyczne środowisko Eksploratora metryk, które jest obecnie przestarzałe i ostatecznie zostanie wycofane. Zalecamy wyewidencjonowanie nowego środowiska opisanego w [tym artykule](../platform/metrics-charts.md).

Liczniki metryk i zdarzeń są wyświetlane na wykresach zagregowanych wartości, takich jak sumy, średnie lub zliczenia.

Oto przykładowy zestaw wykresów:

![](./media/metrics-explorer/01-overview.png)

Wykresy metryk znajdziesz wszędzie w portalu Application Insights. W większości przypadków można je dostosować i dodać więcej wykresów do bloku. W bloku przegląd kliknij pozycję przez, aby wyświetlić bardziej szczegółowe wykresy (które mają tytuły takie jak "serwery"), lub kliknij przycisk **Eksplorator metryk** , aby otworzyć nowy blok, w którym można utworzyć wykresy niestandardowe.

## <a name="time-range"></a>Przedział czasu
Można zmienić zakres czasu obejmujący wykresy lub siatki w dowolnym bloku.

![Otwórz blok przegląd aplikacji w Azure Portal](./media/metrics-explorer/03-range.png)

Jeśli oczekujesz, że niektóre dane jeszcze się nie pojawiły, kliknij przycisk Odśwież. Wykresy są odświeżane w odstępach czasu, ale interwały są dłuższe dla większych zakresów czasu. Może upłynąć trochę czasu, zanim dane przechodzą przez potok analizy na wykres.

Aby powiększyć część wykresu, przeciągnij nad nim:

![Przeciągnij w poprzek części wykresu.](./media/metrics-explorer/12-drag.png)

Kliknij przycisk Cofnij powiększenie, aby go przywrócić.

## <a name="granularity-and-point-values"></a>Stopień szczegółowości i wartości punktów
Umieść wskaźnik myszy na wykresie, aby wyświetlić wartości metryk w tym momencie.

![Umieść wskaźnik myszy na wykresie](./media/metrics-explorer/02-focus.png)

Wartość metryki w konkretnym punkcie jest agregowana w poprzednim interwale próbkowania.

W górnej części bloku zostanie wyświetlony interwał próbkowania lub stopień szczegółowości.

![Nagłówek bloku.](./media/metrics-explorer/11-grain.png)

Stopień szczegółowości można dostosować w bloku zakres czasu:

![Nagłówek bloku.](./media/metrics-explorer/grain.png)

Dostępne szczegóły zależą od wybranego zakresu czasu. Jawne szczegółowości są alternatywami dla stopnia szczegółowości "Automatyczne" dla zakresu czasu.


## <a name="editing-charts-and-grids"></a>Edytowanie wykresów i siatek
Aby dodać nowy wykres do bloku:

![W Eksplorator metryk wybierz pozycję Dodaj wykres](./media/metrics-explorer/04-add.png)

Wybierz pozycję **Edytuj** na istniejącym lub nowym wykresie, aby edytować elementy, które pokazuje:

![Wybierz co najmniej jedną metrykę](./media/metrics-explorer/08-select.png)

Na wykresie można wyświetlić więcej niż jedną metrykę, chociaż istnieją ograniczenia dotyczące kombinacji, które mogą być wyświetlane razem. Po wybraniu jednej metryki niektóre inne są wyłączone.

Jeśli do aplikacji są kodowane [niestandardowe metryki][track] (wywołania do TrackMetric i poleceń trackEvent), zostaną one wyświetlone w tym miejscu.

## <a name="segment-your-data"></a>Segmentowanie danych
Można podzielić metrykę według właściwości — na przykład, aby porównać widok strony na klientach z różnymi systemami operacyjnymi.

Wybierz wykres lub siatkę, Włącz grupowanie i wybierz właściwość do zgrupowania:

![Wybierz pozycję grupowanie na, a następnie ustaw pozycję Wybierz właściwość w polu Grupuj według](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> W przypadku korzystania z grupowania, typy wykresu warstwowego i słupkowego zapewniają skumulowany wyświetlacz. Jest to odpowiednie miejsce, gdzie Metoda agregacji jest sumą. Ale gdzie typ agregacji jest średni, wybierz typy wyświetlania linii lub siatki.
>
>

Jeśli do aplikacji zostały zakodowane [metryki niestandardowe][track] i zawierają one wartości właściwości, będzie można wybrać właściwość z listy.

Czy wykres jest zbyt mały dla danych segmentowych? Dostosuj Wysokość:

![Dostosuj suwak](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Typy agregacji
Legenda po stronie domyślnej zazwyczaj pokazuje zagregowaną wartość w okresie wykresu. Umieszczenie wskaźnika myszy na wykresie powoduje wyświetlenie wartości w tym momencie.

Każdy punkt danych na wykresie to zagregowana wartość danych odebranych w poprzednim interwale próbkowania lub "stopień szczegółowości". Stopień szczegółowości jest pokazywany w górnej części bloku i różni się od ogólnego przedziału czasu wykresu.

Metryki można agregować na różne sposoby:

* **Licznik** jest liczbą zdarzeń odebranych w interwale próbkowania. Jest on używany do zdarzeń, takich jak żądania. Różnice w wysokości wykresu oznaczają różnice w szybkości, w której występują zdarzenia. Należy jednak pamiętać, że wartość liczbowa zmienia się w przypadku zmiany interwału próbkowania.
* **Sum** dodaje wartości wszystkich punktów danych odebranych w interwale próbkowania lub okresu wykresu.
* **Średnia** dzieli sumę przez liczbę punktów danych odebranych w interwale.
* **Unikatowe** zliczenia są używane dla liczników użytkowników i kont. W przypadku interwału próbkowania lub w okresie wykresu rysunek przedstawia liczbę różnych użytkowników, którzy wywidzili w tym czasie.
* wersje **%** poszczególnych agregacji są używane tylko w przypadku wykresów z segmentami. Suma zawsze jest dodawana do 100%, a wykres pokazuje względny udział różnych składników łącznie.

    ![Agregacja procentowa](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Zmień typ agregacji

![Edytuj wykres, a następnie wybierz pozycję agregacja](./media/metrics-explorer/05-aggregation.png)

Domyślna metoda dla każdej metryki jest wyświetlana podczas tworzenia nowego wykresu lub po odzaznaczeniu wszystkich metryk:

![Odznacz wszystkie metryki, aby wyświetlić wartości domyślne](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Przypnij oś Y 
Domyślnie wykres pokazuje wartości osi Y zaczynające się od zera do wartości maksymalnej w zakresie danych, aby przedstawić wizualną reprezentację Quantum wartości. Jednak w niektórych przypadkach może być interesująca Wizualizacja wizualnie drobnych zmian w wartościach. W przypadku dostosowań, takich jak ta, użyj funkcji edycji zakresu osi Y, aby przypiąć wartość minimalną lub maksymalną osi Y w żądanym miejscu.
Kliknij pole wyboru "Ustawienia zaawansowane", aby wyświetlić ustawienia zakresu osi Y

![Kliknij pozycję Ustawienia zaawansowane, wybierz zakres niestandardowy i Określ minimalną maksymalną wartość](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Filtrowanie danych
Aby wyświetlić tylko metryki dla wybranego zestawu wartości właściwości:

![Kliknij pozycję Filtr, rozwiń Właściwość i Sprawdź niektóre wartości.](./media/metrics-explorer/19-filter.png)

Jeśli nie wybrano żadnych wartości dla konkretnej właściwości, jest to taka sama jak w przypadku wybrania opcji wszystkie: nie ma żadnego filtru dla tej właściwości.

Zwróć uwagę na liczbę zdarzeń obok każdej wartości właściwości. Po wybraniu wartości z jednej właściwości, liczby wraz z innymi wartościami właściwości są korygowane.

Filtry stosują się do wszystkich wykresów w bloku. Jeśli chcesz zastosować różne filtry do różnych wykresów, Utwórz i Zapisz różne bloki metryk. Jeśli chcesz, możesz przypiąć wykresy z różnych bloków do pulpitu nawigacyjnego, aby były widoczne obok siebie.

### <a name="remove-bot-and-web-test-traffic"></a>Usuwanie ruchu testowego bot i sieci Web
Użyj filtru **ruchu rzeczywistego lub syntetycznego** i sprawdź **rzeczywistą**wartość.

Można również filtrować według **źródła ruchu syntetycznego**.

### <a name="to-add-properties-to-the-filter-list"></a>Aby dodać właściwości do listy filtrów
Czy chcesz filtrować dane telemetryczne według własnej kategorii? Można na przykład podzielić użytkowników na różne kategorie, a użytkownik chce segmentować dane według tych kategorii.

[Utwórz własną Właściwość](../../azure-monitor/app/api-custom-events-metrics.md#properties). Ustaw go w [inicjatorze telemetrii](../../azure-monitor/app/api-custom-events-metrics.md#defaults) , aby był wyświetlany w całej telemetrii — w tym standardowe dane telemetryczne wysyłane przez różne moduły zestawu SDK.

## <a name="edit-the-chart-type"></a>Edytuj typ wykresu
Należy zauważyć, że można przełączać się między siatkami i wykresami:

![Wybierz siatkę lub Graf, a następnie wybierz typ wykresu](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Zapisz blok metryk
Po utworzeniu niektórych wykresów Zapisz je jako Ulubione. Jeśli używasz konta organizacyjnego, możesz wybrać, czy udostępnić go innym członkom zespołu.

![Wybierz element ulubiony](./media/metrics-explorer/21-favorite-save.png)

Aby ponownie wyświetlić blok, **Przejdź do bloku przegląd** i Otwórz folder Ulubione:

![W bloku przegląd wybierz pozycję Ulubione.](./media/metrics-explorer/22-favorite-get.png)

Jeśli wybrano względny zakres czasu podczas zapisywania, blok zostanie zaktualizowany przy użyciu najnowszych metryk. W przypadku wybrania bezwzględnego zakresu czasu program będzie wyświetlał te same dane co godzinę.

## <a name="reset-the-blade"></a>Zresetuj blok
Jeśli edytujesz blok, ale chcesz wrócić do oryginalnego zapisanego zestawu, po prostu kliknij pozycję Zresetuj.

![Na przyciskach u góry Eksploratora metryk](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Strumień metryk na żywo

Aby uzyskać znacznie bardziej bezpośredni wgląd w dane telemetryczne, Otwórz [Live Stream](live-stream.md). Większość metryk może pojawić się w ciągu kilku minut z powodu procesu agregacji. Z kolei metryki na żywo są zoptymalizowane pod kątem małych opóźnień. 

## <a name="set-alerts"></a>Ustawianie alertów
Aby otrzymywać powiadomienia pocztą e-mail o nietypowych wartościach dowolnej metryki, Dodaj alert. Możesz wybrać opcję wysłania wiadomości e-mail do administratorów konta lub do określonych adresów e-mail.

![W Eksplorator metryk Wybierz reguły alertów, Dodaj alert](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[Dowiedz się więcej o alertach][alerts].


## <a name="continuous-export"></a>Eksport ciągły
Jeśli chcesz, aby dane były stale eksportowane, aby można było je przetwarzać zewnętrznie, rozważ użycie [eksportu ciągłego](../../azure-monitor/app/export-telemetry.md).

### <a name="power-bi"></a>Power BI
Jeśli chcesz, aby jeszcze bardziej bogatsze widoki danych, możesz [wyeksportować je do Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Analiza
[Analiza](../../azure-monitor/app/analytics.md) to bardziej uniwersalny sposób analizowania danych telemetrycznych za pomocą zaawansowanego języka zapytań. Użyj go, jeśli chcesz połączyć lub obliczyć wyniki z metryk lub uzyskać szczegółowe badanie ostatniej wydajności aplikacji. 

Na wykresie metryki możesz kliknąć ikonę analiza, aby przejść bezpośrednio do odpowiedniej kwerendy analizy.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
*Nie widzę żadnych danych na wykresie.*

* Filtry stosują się do wszystkich wykresów w bloku. Upewnij się, że podczas skoncentrowania się na jednym wykresie nie ustawiono filtru, który wyklucza wszystkie dane z innego.

    Jeśli chcesz ustawić inne filtry na różnych wykresach, utwórz je w różnych blokach, a następnie Zapisz je jako osobne Ulubione. Jeśli chcesz, możesz przypiąć je do pulpitu nawigacyjnego, aby były widoczne obok siebie.
* W przypadku grupowania wykresu według właściwości, która nie jest zdefiniowana w metryce, na wykresie nie będzie niczego. Spróbuj wyczyścić polecenie "Grupuj według" lub wybierz inną właściwość grupowania.
* Dane wydajności (procesor CPU, szybkość operacji we/wy itd.) są dostępne dla usług sieci Web Java, aplikacji klasycznych systemu Windows, [aplikacji sieci Web i usług IIS, jeśli jest instalowany Monitor stanu](../../azure-monitor/app/monitor-performance-live-website-now.md)i [usługa Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md). Nie jest ona dostępna dla usługi Azure Websites.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie użycia za pomocą Application Insights](../../azure-monitor/app/usage-overview.md)
* [Korzystanie z wyszukiwania diagnostycznego](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
