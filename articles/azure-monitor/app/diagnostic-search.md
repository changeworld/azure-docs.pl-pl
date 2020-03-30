---
title: Korzystanie z wyszukiwania w usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Wyszukiwanie i filtrowanie nieprzetworzonych danych telemetrycznych wysyłanych przez aplikację sieci Web.
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: 8039a55784f63030f330d6c1e2061e99b8b63bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275986"
---
# <a name="using-search-in-application-insights"></a>Korzystanie z wyszukiwania w usłudze Application Insights

Wyszukiwanie jest funkcją [usługi Application Insights,](../../azure-monitor/app/app-insights-overview.md) której używasz do znajdowania i eksplorowania poszczególnych elementów telemetrycznych, takich jak widoki stron, wyjątki lub żądania sieci Web. Można też wyświetlać ślady dziennika i zdarzenia, które zostały zakodowane.

(Aby uzyskać bardziej złożone zapytania dotyczące danych, użyj [Analytics](../../azure-monitor/log-query/get-started-portal.md).)

## <a name="where-do-you-see-search"></a>Gdzie widzisz wyszukiwanie?

### <a name="in-the-azure-portal"></a>W witrynie Azure Portal

Wyszukiwanie diagnostyczne można otworzyć na karcie Przegląd aplikacji (znajdującej się na górnym pasku) lub w obszarze badanie po lewej stronie.

![Karta Wyszukiwanie](./media/diagnostic-search/view-custom-events.png)

Przejdź do menu rozwijanego Typy zdarzeń, aby wyświetlić listę elementów telemetrycznych — żądania serwera, widoki strony, zdarzenia niestandardowe, które zostały zakodowane itd. U góry listy wyników znajduje się wykres podsumowujący pokazujący liczbę zdarzeń w czasie.

Kliknij z menu rozwijanego lub Odśwież, aby uzyskać nowe wydarzenia.

### <a name="in-visual-studio"></a>W programie Visual Studio

W programie Visual Studio istnieje również okno wyszukiwania usługi Application Insights. Jest to najbardziej przydatne do wyświetlania zdarzeń telemetrycznych generowanych przez aplikację, która jest debugowanie. Ale można również wyświetlić zdarzenia zebrane z opublikowanej aplikacji w witrynie Azure portal.

Otwórz okno wyszukiwania w programie Visual Studio:

![Program Visual Studio otwiera wyszukiwanie w aplikacji Usługi Insights](./media/diagnostic-search/32.png)

Okno Wyszukiwanie ma funkcje podobne do portalu internetowego:

![Okno wyszukiwania usługi Visual Studio Application Insights](./media/diagnostic-search/34.png)

Karta Operacja śledzenia jest dostępna po otwarciu żądania lub widoku strony. "Operacja" to sekwencja zdarzeń skojarzonych z pojedynczym żądaniem lub widokiem strony. Na przykład wywołania zależności, wyjątki, dzienniki śledzenia i zdarzenia niestandardowe mogą być częścią pojedynczej operacji. Karta Operacja śledzenia pokazuje graficznie czas i czas trwania tych zdarzeń w odniesieniu do żądania lub widoku strony.

## <a name="inspect-individual-items"></a>Sprawdzanie poszczególnych elementów

Wybierz dowolny element telemetrii, aby wyświetlić pola kluczy i powiązane elementy.

![Zrzut ekranu przedstawiający indywidualne żądanie zależności](./media/diagnostic-search/telemetry-item.png)

Spowoduje to uruchomienie widoku szczegółów transakcji end-to-end.

## <a name="filter-event-types"></a>Filtrowanie typów zdarzeń

Otwórz menu rozwijane Typy zdarzeń i wybierz typy zdarzeń, które chcesz wyświetlić. (Jeśli później chcesz przywrócić filtry, kliknij przycisk Resetuj).

Typy zdarzeń to:

* **Dzienniki** - [diagnostyczne](../../azure-monitor/app/asp-net-trace-logs.md) śledzenia, w tym TrackTrace, log4Net, NLog i System.Diagnostic.Trace wywołania.
* **Żądanie** — żądania HTTP odebrane przez aplikację serwera, w tym strony, skrypty, obrazy, pliki stylów i dane. Te zdarzenia są używane do tworzenia wykresów przeglądu żądania i odpowiedzi.
* **Dane** - [telemetryczne](../../azure-monitor/app/javascript.md)widoku strony wysyłane przez klienta sieci Web, używane do tworzenia raportów widoku strony.
* **Zdarzenie niestandardowe** — jeśli wstawiłeś wywołania trackevent() w celu [monitorowania użycia,](../../azure-monitor/app/api-custom-events-metrics.md)możesz je wyszukać tutaj.
* **Wyjątek** — nieprzechwytowe [wyjątki na serwerze](../../azure-monitor/app/asp-net-exceptions.md)i te, które można zalogować przy użyciu TrackException().
* **Wywołania zależności**[z aplikacji serwera](../../azure-monitor/app/asp-net-dependencies.md) do innych usług, takich jak interfejsy API REST lub bazy danych, a ajax wywołuje z kodu klienta . [client code](../../azure-monitor/app/javascript.md) - 
* **Dostępność** - Wyniki [testów dostępności](../../azure-monitor/app/monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrowanie wartości właściwości

Można filtrować zdarzenia według wartości ich właściwości. Dostępne właściwości zależą od wybranych typów zdarzeń. Kliknij ikonę filtru ![Ikona filtru](./media/diagnostic-search/filter-icon.png) , aby rozpocząć.

Wybór żadnych wartości określonej właściwości ma taki sam efekt jak wybieranie wszystkich wartości. Wyłącza filtrowanie na tej właściwości.

Należy zauważyć, że liczby po prawej stronie wartości filtru pokazują, ile wystąpień znajduje się w bieżącym zestawie filtrowanym.

## <a name="find-events-with-the-same-property"></a>Znajdowanie zdarzeń z tą samą właściwością

Aby znaleźć wszystkie elementy o tej samej wartości właściwości, wpisz je na pasku wyszukiwania lub kliknij pole wyboru podczas wyszukiwania właściwości na karcie filtru.

![Kliknij pole wyboru właściwości na karcie filtru](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Wyszukiwanie danych

> [!NOTE]
> Aby napisać bardziej złożone zapytania, otwórz [**dzienniki (Analytics)**](../../azure-monitor/log-query/get-started-portal.md) od góry bloku wyszukiwania.
>

Terminy można wyszukiwać w dowolnej wartości właściwości. Jest to przydatne, jeśli zostały napisane [zdarzenia niestandardowe](../../azure-monitor/app/api-custom-events-metrics.md) z wartościami właściwości.

Możesz ustawić zakres czasu, ponieważ wyszukiwania w krótszym zakresie są szybsze.

![Otwórz wyszukiwanie diagnostyczne](./media/diagnostic-search/search-property.png)

Wyszukaj pełne słowa, a nie podciągi. Używanie cudzysłowów do ująć znaki specjalne.

| Ciąg | *Nie* znaleziono | Znaleziono |
| --- | --- | --- |
| Strona głównaController.About |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|Stany Zjednoczone|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Oto wyrażenia wyszukiwania, których można użyć:

| Przykładowe zapytanie | Efekt |
| --- | --- |
| `apple` |Znajdź wszystkie zdarzenia w zakresie czasu, których pola zawierają słowo "jabłko" |
| `apple AND banana` <br/>`apple banana` |Znajdź zdarzenia, które zawierają oba słowa. Użyj kapitału "AND", a nie "i". <br/>Krótka forma. |
| `apple OR banana` |Znajdź zdarzenia, które zawierają jedno z tych wyrazów. Użyj "OR", a nie "lub". |
| `apple NOT banana` |Znajdź zdarzenia, które zawierają jedno słowo, ale nie inne. |

## <a name="sampling"></a>Próbkowanie

Jeśli aplikacja generuje dużą ilość danych telemetrycznych (i używasz ASP.NET SDK w wersji 2.0.0-beta3 lub nowszej), adaptacyjny moduł próbkowania automatycznie zmniejsza wolumin, który jest wysyłany do portalu, wysyłając tylko reprezentatywną część zdarzeń. Jednak zdarzenia, które są związane z tym samym żądaniem są zaznaczone lub odznaczone jako grupa, dzięki czemu można nawigować między powiązanymi zdarzeniami.

[Więcej informacji na temat próbkowania](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>Tworzenie elementu pracy

Można utworzyć błąd w GitHub lub Azure DevOps ze szczegółami z dowolnego elementu telemetrii.

Przejdź do widoku szczegółów transakcji typu end-to-end, klikając dowolny element telemetrii, a następnie wybierz pozycję **Utwórz element roboczy**.

![Kliknij pozycję Nowy element roboczy, edytuj pola, a następnie kliknij przycisk OK.](./media/diagnostic-search/work-item.png)

Przy pierwszym uruchomieniu zostanie wyświetlony monit o skonfigurowanie łącza do organizacji i projektu Usługi Azure DevOps.

(Można również skonfigurować łącze na karcie Elementy robocze).

## <a name="send-more-telemetry-to-application-insights"></a>Wysyłanie większej liczby danych telemetrycznych do usługi Application Insights

Oprócz gotowych danych telemetrycznych wysyłanych przez sdk usługi Application Insights można:

* Przechwytywanie śladów dziennika z ulubionej struktury rejestrowania w [.NET](../../azure-monitor/app/asp-net-trace-logs.md) lub [Java](../../azure-monitor/app/java-trace-logs.md). Oznacza to, że można przeszukiwać ślady dziennika i skorelować je z widokami strony, wyjątkami i innymi zdarzeniami.
* [Napisz kod,](../../azure-monitor/app/api-custom-events-metrics.md) aby wysłać niestandardowe zdarzenia, widoki strony i wyjątki.

[Dowiedz się, jak wysyłać dzienniki i niestandardowe dane telemetryczne do usługi Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="q--a"></a><a name="questions"></a>Q & A

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Ile danych jest zachowywanych?

Zobacz [podsumowanie limitów](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Jak mogę zobaczyć dane POST w moich żądaniach serwera?

Nie rejestrujemy danych POST automatycznie, ale możesz użyć [TrackTrace lub logowania połączeń.](../../azure-monitor/app/asp-net-trace-logs.md) Umieść dane POST w parametrze wiadomości. Nie można filtrować wiadomości w taki sam sposób, w jaki można filtrować właściwości, ale limit rozmiaru jest dłuższy.

## <a name="next-steps"></a><a name="add"></a>Następne kroki

* [Pisanie złożonych zapytań w Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Wysyłanie dzienników i niestandardowych danych telemetrycznych do usługi Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [Konfigurowanie testów dostępności i reakcji](../../azure-monitor/app/monitor-web-app-availability.md)
* [Rozwiązywanie problemów](../../azure-monitor/app/troubleshoot-faq.md)
