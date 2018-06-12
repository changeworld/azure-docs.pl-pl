---
title: Za pomocą wyszukiwania w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Wyszukaj i Filtruj nieprzetworzone dane telemetryczne wysyłane przez aplikację sieci web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: c6a94fd1cebff4aa657ad5293715550161003d21
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294388"
---
# <a name="using-search-in-application-insights"></a>Za pomocą wyszukiwania w usłudze Application Insights
Wyszukiwanie jest funkcją [usługi Application Insights](app-insights-overview.md) służącego do wyszukania i eksplorować dane telemetryczne poszczególnych elementów, takich jak wyświetleń strony, wyjątki lub żądania sieci web. I ślady dziennika i zdarzenia, które mają być kodowane można wyświetlić.

(W przypadku bardziej złożonych kwerend za pośrednictwem danych użyj [Analytics](app-insights-analytics-tour.md).)

## <a name="where-do-you-see-search"></a>Gdzie możesz zobaczyć wyszukiwania?
### <a name="in-the-azure-portal"></a>W portalu Azure
Diagnostycznych wyszukiwania można otworzyć jawnie za pomocą bloku Application Insights Omówienie aplikacji:

![Otwórz wyszukiwanie diagnostycznych](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

Otwiera również po kliknięciu przez niektóre wykresów i elementów siatki. W takim przypadku filtrów są wstępnie ustawioną skupić się od typu wybranego elementu. 

Na przykład w bloku omówienie istnieje wykres słupkowy żądań sklasyfikowane przez czas odpowiedzi. Kliknij w zakresie wydajności, aby wyświetlić listę poszczególnych żądań w tym przedziale czasu odpowiedzi:

![Kliknij przycisk za pomocą żądania wydajności](./media/app-insights-diagnostic-search/07-open-from-filters.png)

Główną diagnostycznych wyszukiwania znajduje się listę elementów telemetrii - żądań serwera strony widoki, niestandardowych zdarzeń, które mają być zakodowane i tak dalej. W górnej części listy jest podsumowanie wykres przedstawiający liczby zdarzeń w czasie.

Kliknij przycisk Odśwież, aby uzyskać nowe zdarzenia.

### <a name="in-visual-studio"></a>W programie Visual Studio

W programie Visual Studio jest również okno wyszukiwania usługi Application Insights. Jest to najbardziej przydatne do wyświetlania danych telemetrycznych zdarzenia generowane przez debugowanej aplikacji. Ale może także wyświetlać zdarzenia zebrane z opublikowanych aplikacji w portalu Azure.

Otwórz okno wyszukiwania w programie Visual Studio:

![Visual Studio Otwórz wyszukiwanie usługi Application Insights](./media/app-insights-diagnostic-search/32.png)

Okno wyszukiwania zawiera funkcje podobne do portalu sieci web:

![Okno wyszukiwania w usłudze Visual Studio Application Insights](./media/app-insights-diagnostic-search/34.png)

Karta śledzenie operacji jest dostępna, po otwarciu żądania lub widok strony. "Operacji" jest sekwencję zdarzeń, która jest skojarzony z widoku pojedynczego żądania lub strony. Na przykład wywołania zależności, wyjątki dzienników śledzenia i niestandardowych zdarzeń może być częścią jednej operacji. Karta śledzenie operacji wyświetlane w formie graficznej czas i czas trwania tych zdarzeń w odniesieniu do widoku żądania lub strony. 

## <a name="inspect-individual-items"></a>Przejrzyj poszczególne elementy
Wybierz dowolny element telemetrii, aby wyświetlić pola klucza i elementy powiązane. Jeśli chcesz wyświetlić pełny zestaw pól, kliknij przycisk "...". 

![Kliknij nowy element roboczy, Edytuj pola, a następnie kliknij przycisk OK.](./media/app-insights-diagnostic-search/10-detail.png)

## <a name="filter-event-types"></a>Typy zdarzeń filtru
Otwarcie bloku filtru, a następnie wybierz typy zdarzeń, które mają być wyświetlane. (Jeśli później, chcesz przywrócić filtry, z którymi możesz otworzyć bloku, kliknij Resetuj).

![Wybierz filtr, a następnie wybierz typy telemetrii](./media/app-insights-diagnostic-search/02-filter-req.png)

Typy zdarzeń, które są:

* **Śledzenia** - [dzienniki diagnostyczne](app-insights-asp-net-trace-logs.md) tym TrackTrace, log4Net, NLog i System.Diagnostic.Trace wywołania.
* **Żądanie** -żądania HTTP odebrane przez aplikacji serwera, w tym stron, skryptów, obrazów, plików w stylu i danych. Te zdarzenia są używane do tworzenia żądań i odpowiedzi wykresów omówienie.
* **Widok strony** - [danych Telemetrycznych wysłanych przez klienta sieci web](app-insights-javascript.md), używana do tworzenia raportów widoku strony. 
* **Zdarzenie niestandardowe** — Jeśli dodaje wywołania funkcji TrackEvent() w celu [monitorowanie użycia](app-insights-api-custom-events-metrics.md), można je znaleźć tutaj.
* **Wyjątek** — nieprzechwyconych [wyjątki na serwerze](app-insights-asp-net-exceptions.md)oraz te, które możesz zalogować się przy użyciu funkcji TrackException().
* **Zależności** - [wywołania z aplikacji serwera](app-insights-asp-net-dependencies.md) do innych usług, takich jak interfejsów API REST lub baz danych i AJAX wymaga od użytkownika [kodu klienta](app-insights-javascript.md).
* **Dostępność** -wyniki [testów dostępności](app-insights-monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrowanie według wartości właściwości
Można filtrować zdarzenia na wartościach ich właściwości. Dostępne właściwości zależą od wybranych typów zdarzeń. 

Na przykład wybierz limit żądań z kodem określoną odpowiedź. 

![Rozwiń węzeł właściwości i wybierz wartość](./media/app-insights-diagnostic-search/03-response500.png)

Wybieranie wartości właściwości określonego działa tak samo jak wszystkie wartości. Przełączenie filtrowanie dla tej właściwości jest wyłączone.

### <a name="narrow-your-search"></a>Zawęzić kryteria wyszukiwania
Należy zauważyć, że liczby po prawej stronie wartości filtru Pokaż liczbę wystąpień są w bieżącym zestawie filtrowane. 

W tym przykładzie jest jasne, czy 'Rpt/pracowników' żądania wyniki w większości "500" błędów:

![Rozwiń węzeł właściwości i wybierz wartość](./media/app-insights-diagnostic-search/04-failingReq.png)




## <a name="find-events-with-the-same-property"></a>Znajdź zdarzenia z tej samej właściwości
Znajdź wszystkie elementy z taką samą wartość właściwości:

![Kliknij prawym przyciskiem myszy właściwości](./media/app-insights-diagnostic-search/12-samevalue.png)


## <a name="search-the-data"></a>Wyszukiwanie danych

> [!NOTE]
> Aby zapisać bardziej złożonych zapytań, otwórz [ **Analytics** ](app-insights-analytics-tour.md) od górnej krawędzi bloku wyszukiwania.
> 

Możesz wyszukać warunków w dowolnym wartości właściwości. Jest to szczególnie przydatne, jeśli w języku [zdarzeń niestandardowych](app-insights-api-custom-events-metrics.md) z wartości właściwości. 

Można ustawić czas zakresu, są szybsze jako wyszukiwania w zakresie krótszy. 

![Otwórz wyszukiwanie diagnostycznych](./media/app-insights-diagnostic-search/appinsights-311search.png)

Wyszukiwanie słów pełną, nie podciągów. Użyj znaków cudzysłowu, należy ująć w znaki specjalne.

| ciąg | jest *nie* został znaleziony przez klasę | te znaleźć |
| --- | --- | --- |
| HomeController.About |strona główna<br/>Kontrolera<br/>limit | homecontroller<br/>informacje<br/>"homecontroller.about"|
|Stany Zjednoczone|Sygnalizowanie UNI<br/>obcięta|Zjednoczone<br/>Stany<br/>Stany Zjednoczone i<br/>"Stanów Zjednoczonych"

Poniżej przedstawiono wyrażeniach wyszukiwania, których można użyć:

| Przykładowe zapytanie | Efekt |
| --- | --- |
| `apple` |Znajdź wszystkie zdarzenia w zakresie czasu, w których pola Dołącz słowo "apple" |
| `apple AND banana` |Znajdź zdarzenia, które zawierają oba słowa. Użyj kapitału "i", nie "i". |
| `apple OR banana`<br/>`apple banana` |Znajdź zdarzenia, które zawierają program Microsoft word. Użyj "Lub", nie "lub".<br/>Krótka forma. |
| `apple NOT banana` |Znajdź zdarzenia zawierające o jedno słowo, ale nie dla drugiego. |



## <a name="sampling"></a>Próbkowanie
Jeśli aplikacja generuje wiele telemetrii (i używasz 2.0.0-beta3 wersji zestawu SDK platformy ASP.NET lub nowszym), moduł adaptacyjną próbkowania automatycznie zmniejsza woluminu, który jest wysyłane do portalu, wysyłając reprezentatywny część zdarzeń. Zdarzenia, które są związane z tym samym żądania są zaznaczone lub, zostanie usunięte zaznaczenie jako grupa, dzięki czemu można przechodzić między powiązanych zdarzeń. 

[Więcej informacji na temat próbkowania](app-insights-sampling.md).



## <a name="create-work-item"></a>Utwórz element roboczy
Szczegóły z dowolnego elementu danych telemetrycznych można utworzyć usterki w witrynie GitHub lub Visual Studio Team Services. 

![Kliknij nowy element roboczy, Edytuj pola, a następnie kliknij przycisk OK.](./media/app-insights-diagnostic-search/42.png)

Można to zrobić, po raz pierwszy, zostanie wyświetlona prośba o skonfigurować łącze do konta usługi Team Services i projektu.

![Wypełnienie adres URL serwera usługi Team Services i nazwę projektu, a następnie kliknij przycisk Autoryzuj](./media/app-insights-diagnostic-search/41.png)

(Można również skonfigurować łącze w bloku elementów roboczych.)

## <a name="save-your-search"></a>Zapisz wyszukiwanie
Po ustawieniu wszystkie filtry, które mają wyszukiwania można zapisać jako ulubione. Jeśli współpracujesz konta organizacyjne, możesz wybrać, czy na udostępnianie innych członków zespołu.

![Kliknij ulubiony, ustaw nazwę, a następnie kliknij przycisk Zapisz](./media/app-insights-diagnostic-search/08-favorite-save.png)

Aby ponownie wyświetlić wyszukiwania **przejdź do bloku omówienie** , a następnie otwórz Ulubione:

![Ulubione kafelka](./media/app-insights-diagnostic-search/09-favorite-get.png)

Jeśli został zapisany z zakresem względne czasu, ponownie otworzyć bloku ma najnowsze dane. Zapisanie z zakresem czasu bezwzględnego, możesz zobaczyć te same dane zawsze. (Jeśli 'Relative' nie jest dostępna, gdy chcesz zapisać element ulubiony, kliknij zakres czasu w nagłówku i ustaw zakres czasu nie niestandardowego zakresu.)

## <a name="send-more-telemetry-to-application-insights"></a>Wysyłanie danych telemetrycznych więcej do usługi Application Insights
Oprócz poza pola danych telemetrycznych wysłanych przez zestaw SDK usługi Application Insights można:

* Śledzenie dziennika z Twojego struktury rejestrowania ulubionych w [.NET](app-insights-asp-net-trace-logs.md) lub [Java](app-insights-java-trace-logs.md). Oznacza to, można przeszukiwać ślady dziennika i skorelowania je z wyświetleń strony, wyjątków i inne zdarzenia. 
* [Pisanie kodu](app-insights-api-custom-events-metrics.md) do wysyłania zdarzeń niestandardowych, wyświetleń strony i wyjątki. 

[Wysyłanie dzienników oraz telemetrii niestandardowej z usługą Application Insights](app-insights-asp-net-trace-logs.md).

## <a name="questions"></a>FUNKCJA PYTANIA I ODPOWIEDZI
### <a name="limits"></a>Jak dużo danych jest zachowywana?

Zobacz [podsumowanie limity](app-insights-pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Jak wyświetlić dane POST w Moje żądania serwera
Dane POST nie rejestrowane automatycznie, ale może użyć [TrackTrace lub dziennika wywołań](app-insights-asp-net-trace-logs.md). Umieszczanie danych POST w parametrze wiadomości. Nie można filtrować wiadomości w taki sam sposób, który można filtrować według właściwości, ale jest dłużej limit rozmiaru.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Następne kroki
* [Zapis złożonych zapytań w module analiz](app-insights-analytics-tour.md)
* [Wysyłanie dzienników oraz telemetrii niestandardowej z usługą Application Insights](app-insights-asp-net-trace-logs.md)
* [Konfigurowanie dostępności i testy czasu odpowiedzi](app-insights-monitor-web-app-availability.md)
* [Rozwiązywanie problemów](app-insights-troubleshoot-faq.md)
