---
title: Tworzenie interaktywnych raportów za pomocą skoroszytów usługi Azure Monitor | Dokumenty firmy Microsoft
description: Uprość składanie złożonych raportów dzięki wstępnie utworzonym i niestandardowych sparametryzowanym skoroszytom
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 15543f7f761c707e8eff8e0cc0a0e4532475ddf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671005"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Tworzenie interaktywnych raportów za pomocą skoroszytów usługi Azure Monitor

Skoroszyty łączą tekst, [zapytania Analytics,](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)metryki platformy Azure i parametry w zaawansowane raporty interaktywne. Skoroszyty mogą być edytowane przez innych członków zespołu, którzy mają dostęp do tych samych zasobów platformy Azure.

Skoroszyty są przydatne w scenariuszach, takich jak:

* Eksplorowanie użycia aplikacji, gdy nie znasz z wyprzedzeniem interesujących danych: liczby użytkowników, współczynników retencji, współczynników konwersji itp. W przeciwieństwie do innych narzędzi do analizy użycia skoroszyty umożliwiają łączenie wielu rodzajów wizualizacji i analiz, dzięki czemu doskonale nadają się do tego rodzaju eksploracji swobodnej.
* Wyjaśniając zespołowi, jak działa nowo wydana funkcja, pokazując liczbę użytkowników dla kluczowych interakcji i innych metryk.
* Udostępnianie wyników eksperymentu A/B w aplikacji innym członkom zespołu. Możesz wyjaśnić cele eksperymentu za pomocą tekstu, a następnie wyświetlić każdy metryk użycia i zapytanie Analytics używane do oceny eksperymentu, a także wyraźne wezwania do określania, czy każda metryka była powyżej, czy poniżej celu.
* Raportowanie wpływu awarii na korzystanie z aplikacji, łączenie danych, wyjaśnienie tekstu i omówienie kolejnych kroków, aby zapobiec awariom w przyszłości.

## <a name="starting-with-a-template-or-saved-workbook"></a>Rozpoczynanie pracy od szablonu lub zapisanego skoroszytu

Skoroszyt składa się z sekcji składających się z niezależnie edytowalnych wykresów, tabel, tekstu i formantów wejściowych. Aby lepiej zrozumieć skoroszyty, najlepiej je otworzyć. 

Wybierz **skoroszyty** z menu po lewej stronie z wnętrza usługi Application Insights dla aplikacji.

![Zrzut ekranu przedstawiający nawigację do skoroszytów](./media/usage-workbooks/001-workbooks.png)

Spowoduje to uruchomienie galerii skoroszytu z wieloma skoroszytami, które ułatwiają rozpoczęcie pracy.

![Zrzut ekranu przedstawiający galerię skoroszytu](./media/usage-workbooks/002-workbook-gallery.png)

Zaczniemy od **domyślnego szablonu,** który znajduje się pod nagłówkiem **Szybki start**.

![Zrzut ekranu przedstawiający galerię skoroszytu](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Edytowanie, zmienianie rozmieszczenia, klonowanie i usuwanie sekcji skoroszytu

Skoroszyty mają dwa tryby: **tryb edycji**i **tryb czytania**. Po pierwszym uruchomieniu domyślnego skoroszytu otwiera się on w **trybie edycji**. Spowoduje to wyświetlenie całej zawartości skoroszytu, w tym wszystkich kroków i parametrów, które w przeciwnym razie są ukryte. **Tryb odczytu** przedstawia uproszczony widok stylu raportu. Pozwala to na streszczenie złożoności, która poszła do tworzenia raportu, a jednocześnie o podstawowej mechaniki tylko kilka kliknięć, gdy jest to konieczne do modyfikacji.

![Kontrolki edycji sekcji Statystyki aplikacji](./media/usage-workbooks/editing-controls-new.png)

1. Po zakończeniu edytowania sekcji kliknij pozycję **Edycja w** lewym dolnym rogu sekcji.

2. Aby utworzyć duplikat sekcji, kliknij ikonę **Sklonuj tę sekcję.** Tworzenie zduplikowanych sekcji jest doskonałym sposobem iteracji kwerendy bez utraty poprzednich iteracji.

3. Aby przenieść sekcję w górę skoroszytu, kliknij ikonę **Przenieś w górę** lub Przenieś w **dół.**

4. Aby trwale usunąć sekcję, kliknij ikonę **Usuń.**

## <a name="adding-text-and-markdown-sections"></a>Dodawanie sekcji tekstu i znaczników

Dodanie nagłówków, wyjaśnień i komentarzy do skoroszytów pomaga przekształcić zestaw tabel i wykresów w narrację. Sekcje tekstu w skoroszytach obsługują [składnię Markdown](https://daringfireball.net/projects/markdown/) dla formatowania tekstu, takich jak nagłówki, pogrubienie, kursywa i listy punktowane.

Aby dodać sekcję tekstową do skoroszytu, użyj przycisku **Dodaj tekst** u dołu skoroszytu lub u dołu dowolnej sekcji.

## <a name="adding-query-sections"></a>Dodawanie sekcji zapytań

![Sekcja Kwerenda w skoroszytach](./media/usage-workbooks/analytics-section-new.png)

Aby dodać sekcję kwerendy do skoroszytu, użyj przycisku **Dodaj zapytanie** u dołu skoroszytu lub u dołu dowolnej sekcji.

Sekcje zapytań są bardzo elastyczne i mogą być używane do odpowiadania na pytania, takie jak:

* Ile wyjątków zgłaszała Twoja witryna w tym samym okresie co spadek użycia?
* Jaki był rozkład czasu ładowania strony dla użytkowników przeglądających niektóre strony?
* Ilu użytkowników wyświetliło jakiś zestaw stron w Witrynie, ale nie jakiś inny zestaw stron? Może to być przydatne do zrozumienia, jeśli masz klastry użytkowników, którzy korzystają z `join` różnych `kind=leftanti` podzbiorów funkcjonalności witryny (użyj operatora z modyfikatorem w [języku zapytania Kusto](/azure/kusto/query/)).

Nie ograniczasz się również tylko do wykonywania zapytań z kontekstu aplikacji, z której uruchomiono skoroszyt. Można wyszukiwać w wielu aplikacjach monitorowanych usługi Application Insights, a także w obszarach roboczych usługi Log Analytics, o ile masz uprawnienia dostępu do tych zasobów.

Aby zbadać z dodatkowych zasobów zewnętrznych aplikacji insights użyć **identyfikatora aplikacji.**

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Ta kwerenda łączy żądania z trzech różnych aplikacji. Aplikacja o nazwie app01, aplikacja o nazwie app02 i żądania z lokalnego zasobu usługi Application Insights.

Aby pobierać dane z zewnętrznego obszaru roboczego usługi Log Analytics, należy użyć identyfikatora **obszaru roboczego.**

Aby dowiedzieć się więcej na temat zapytań między zasobami, zapoznaj się z [oficjalnymi wytycznymi](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Zaawansowane ustawienia kwerend analitycznych

Każda sekcja ma własne ustawienia zaawansowane, które ![są dostępne za pośrednictwem ikony ustawień Sekcji Skoroszytów aplikacji Insights sekcji elementów sterujących](./media/usage-workbooks/005-settings.png) znajdujących się po prawej stronie przycisku Dodaj **parametry.**

![Kontrolki edycji sekcji Statystyki aplikacji](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Szerokość niestandardowa**    | Ustaw to tak, aby element był dowolny, aby zmieścić wiele elementów w jednym wierszu, co pozwala lepiej organizować wykresy i tabele w rozszerzone interaktywne raporty.  |
   | **Warunkowo widoczne** | Służy do ukrywania kroków opartych na parametrze w trybie odczytu. |
   | **Eksportowanie parametru**| Dzięki temu wybrany wiersz w siatce lub na wykresie powoduje późniejsze kroki zmiany wartości lub stają się widoczne.  |
   | **Pokaż kwerendę, gdy nie edytujesz** | Spowoduje to wyświetlenia kwerendy nad wykresem lub tabelą, nawet w trybie odczytu.
   | **Pokaż przycisk Otwórz w analizie, gdy nie edytujesz** | Spowoduje to dodanie niebieskiej ikony Analytics do prawego rogu wykresu, aby umożliwić dostęp jednym kliknięciem.|

Większość z tych ustawień są dość intuicyjne, ale aby zrozumieć **Eksportuj parametr,** lepiej jest sprawdzić skoroszyt, który korzysta z tej funkcji.

Jeden ze wstępnie utworzonych skoroszytów zawiera informacje o aktywnych użytkownikach.

Pierwsza sekcja skoroszytu jest oparta na danych kwerendy analitycznej:

![Kontrolki edycji sekcji Statystyki aplikacji](./media/usage-workbooks/003-active-users.png)

Druga sekcja jest również oparta na danych zapytania analitycznego, ale wybranie wiersza w pierwszej tabeli spowoduje interaktywną aktualizację zawartości wykresu:

![Kontrolki edycji sekcji Statystyki aplikacji](./media/usage-workbooks/004-active-users-trend.png)

 Jest to możliwe za pomocą **gdy element jest zaznaczony, wyeksportować parametr** zaawansowane ustawienia, które są włączone w tabeli Analytics kwerendy.

![Kontrolki edycji sekcji Statystyki aplikacji](./media/usage-workbooks/007-settings-export.png)

Drugie zapytanie analityczne następnie wykorzystuje eksportowane wartości, gdy wybrano wiersz. Jeśli nie wybrano żadnego wiersza, domyślnie jest to wiersz reprezentujący wartości ogólne. 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>Dodawanie sekcji metryk

Sekcje metryki zapewniają pełny dostęp do uwzględnienia danych metryk usługi Azure Monitor w interaktywnych raportach. Wiele wstępnie utworzonych skoroszytów będzie zawierać zarówno dane zapytań analitycznych, jak i dane metryki, co pozwala w pełni wykorzystać najlepsze z obu funkcji w jednym miejscu. Masz również możliwość ściągania danych metryk z zasobów w dowolnej subskrypcji, do których masz dostęp.

Oto przykład danych maszyny wirtualnej pobieranych do skoroszytu w celu zapewnienia wizualizacji wydajności procesora CPU w siatce:

![Kontrolki edycji sekcji Statystyki aplikacji](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Dodawanie sekcji parametrów

Parametry skoroszytu umożliwiają zmianę wartości w skoroszycie bez konieczności ręcznej edycji kwerendy lub sekcji tekstowych.  Eliminuje to wymóg konieczności zrozumienia podstawowego języka zapytań analitycznych i znacznie rozszerza potencjalnych odbiorców raportowania opartego na skoroszycie.

Wartości parametrów są zastępowane w sekcji kwerendy, tekstu lub innych parametrów ``{parameterName}``przez umieszczenie nazwy parametru w nawiasach klamrowych, takich jak .  Nazwy parametrów są ograniczone do podobnych reguł, jak identyfikatory JavaScript, w zasadzie znaki alfabetyczne lub podkreślenia, a następnie znaki alfanumeryczne lub podkreślenia. Na przykład **a1** jest dozwolone, ale **1a** nie jest dozwolone.

Parametry są liniowe, zaczynając od góry skoroszytu i spływając do późniejszych kroków.  Parametry zadeklarowane później w skoroszycie można zastąpić te, które zostały zadeklarowane dalej w górę.  Umożliwia to również parametry, które używają kwerend, aby uzyskać dostęp do wartości z parametrów zdefiniowanych dalej w górę.  W samym kroku parametru parametry są również liniowe, od lewej do prawej, gdzie parametry po prawej stronie mogą zależeć od parametru zadeklarowanego wcześniej w tym samym kroku.
 
Istnieją cztery różne typy parametrów, które są obecnie obsługiwane:

  |         |          |
   | ---------------- |:-----|
   | **Tekst**    | użytkownik będzie edytować pole tekstowe i opcjonalnie można podać kwerendę, aby wypełnić wartość domyślną. |
   | **Lista rozwijana** | Użytkownik wybierze jeden z zestawów wartości. |
   | **Selektor zakresu czasu**| Użytkownik wybierze ze wstępnie zdefiniowanego zestawu wartości zakresu czasu lub wybierze niestandardowy zakres czasu.|
   | **Selektor zasobów** | Użytkownik wybierze spośród zasobów wybranych dla skoroszytu.|

### <a name="using-a-text-parameter"></a>Korzystanie z parametru tekstowego

Wartość wpisywania przez użytkownika w polu tekstowym jest zastępowana bezpośrednio w kwerendzie, bez uciekania lub cytowanie. Jeśli potrzebna wartość jest ciągiem, kwerenda powinna mieć cudzysłowy wokół parametru **(np. '{parameter}'**).

Dzięki temu wartość w polu tekstowym ma być używana w dowolnym miejscu. Może to być nazwa tabeli, nazwa kolumny, nazwa funkcji, operator itp.

Typ parametru tekstowego ma ustawienie **Uzyskaj wartość domyślną z kwerendy analitycznej,** która umożliwia autorowi skoroszytu użycie kwerendy do wypełniania wartości domyślnej dla tego pola tekstowego.

W przypadku korzystania z wartości domyślnej z kwerendy analitycznej jako wartość domyślna jest używana tylko pierwsza wartość pierwszego wiersza (wiersz 0, kolumna 0). W związku z tym zaleca się ograniczyć kwerendę, aby zwrócić tylko jeden wiersz i jedną kolumnę. Wszystkie inne dane zwrócone przez kwerendę są ignorowane. 

Niezależnie od wartości zwraca kwerendy zostaną zastąpione bezpośrednio bez ucieczki lub cytowanie. Jeśli kwerenda zwraca żadnych wierszy, wynikiem parametru jest pusty ciąg (jeśli parametr nie jest wymagany) lub niezdefiniowany (jeśli parametr jest wymagany).

### <a name="using-a-dropdown"></a>Korzystanie z listy rozwijanej

Typ parametru rozwijanego umożliwia utworzenie formantu rozwijanego, umożliwiającego wybór jednej lub wielu wartości.

Rozwijana jest wypełniana przez zapytanie analityczne. Jeśli kwerenda zwraca jedną kolumnę, wartości w tej kolumnie są zarówno **wartością,** jak i **etykietą** w formancie rozwijanym. Jeśli kwerenda zwraca dwie kolumny, pierwszą kolumną jest **wartość**, a druga kolumna jest **etykietą** wyświetlaną na rozwijaniu.  Jeśli kwerenda zwraca trzy kolumny, trzecia kolumna jest używana do wskazania domyślnego zaznaczenia w tej listy rozwijanej.  Ta kolumna może być dowolnego typu, ale najprostsze jest użycie bool lub typy liczbowe, gdzie 0 jest false, a 1 jest true.

 Jeśli kolumna jest typem ciągu, ciąg zerowy/pusty jest uważany za fałszywy, a każda inna wartość jest uważana za true. W przypadku pojedynczych rozwijanych zaznaczeń pierwsza wartość z wartością rzeczywistą jest używana jako wybór domyślny.  W przypadku wielu rozwijanych zaznaczeń wszystkie wartości o wartości rzeczywistej są używane jako domyślny zestaw zaznaczony. Elementy z listy rozwijanej są wyświetlane w dowolnej kolejności kwerendy zwrócił wiersze. 

Przyjrzyjmy się parametrom obecnym w raporcie Aktywni użytkownicy. Kliknij symbol edycji obok **pozycji TimeRange**.

![Kontrolki edycji sekcji Statystyki aplikacji](./media/usage-workbooks/009-time-range.png)

Spowoduje to uruchomienie elementu menu Edytuj parametr:

![Kontrolki edycji sekcji Statystyki aplikacji](./media/usage-workbooks/010-time-range-edit.png)

Kwerenda używa funkcji języka zapytań analitycznych o nazwie **datatable,** która pozwala na generowanie dowolnej tabeli, pełnej zawartości, z powietrza! Na przykład następująca kwerenda analityczna:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Generuje wynik:

![Kontrolki edycji sekcji Statystyki aplikacji](./media/usage-workbooks/011-data-table.png)

Bardziej stosowanym przykładem jest użycie listy rozwijanej do wybierania z zestawu krajów/regionów według nazwy:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

Kwerenda wyświetli wyniki w następujący sposób:

![Rozwijanie kraju](./media/usage-workbooks/012-country-dropdown.png)

Listy rozwijane to niezwykle zaawansowane narzędzia do dostosowywania i tworzenia interaktywnych raportów.

### <a name="time-range-parameters"></a>Parametry zakresu czasu

Chociaż można tworzyć własne parametry niestandardowego zakresu czasu za pomocą typu parametru rozwijanego, można również użyć out-of-box typ parametru zakresu czasu, jeśli nie potrzebujesz tego samego stopnia elastyczności. 

Typy parametrów zakresu czasu mają 15 domyślnych zakresów, które trwają od pięciu minut do ostatnich 90 dni. Istnieje również opcja zezwalania na wybór niestandardowego zakresu czasu, co umożliwia operatorowi raportu wybranie jawnych wartości początkowych i zatrzymania dla zakresu czasu.

### <a name="resource-picker"></a>Selektor zasobów

Typ parametru selektora zasobów umożliwia zakres raportu do niektórych typów zasobów. Przykładem skoroszytu wstępnie utworzonego, który wykorzystuje typ selektora zasobów, jest skoroszyt **usługi Aplikacji Aplikacji Aplikacji Błąd** Insights.

![Rozwijanie kraju](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Zapisywanie skoroszytów i udostępnianie ich zespołowi

Skoroszyty są zapisywane w zasobie usługi Application Insights w sekcji **Moje raporty,** która jest prywatna dla Użytkownika, lub w sekcji **Raporty udostępnione,** dostępnej dla wszystkich osób z dostępem do zasobu usługi Application Insights. Aby wyświetlić wszystkie skoroszyty w zasobie, kliknij przycisk **Otwórz** na pasku akcji.

Aby udostępnić skoroszyt aktualnie w **moich raportach:**

1. Kliknij **przycisk Otwórz** na pasku akcji
2. Kliknij "..." obok skoroszytu, który chcesz udostępnić
3. Kliknij **pozycję Przenieś do raportów udostępnionych**.

Aby udostępnić skoroszyt za pomocą łącza lub wiadomości e-mail, kliknij pozycję **Udostępnij** na pasku akcji. Należy pamiętać, że adresaci łącza potrzebują dostępu do tego zasobu w witrynie Azure Portal, aby wyświetlić skoroszyt. Aby wprowadzić zmiany, adresaci potrzebują co najmniej uprawnień współautora dla zasobu.

Aby przypiąć łącze do skoroszytu do pulpitu nawigacyjnego platformy Azure:

1. Kliknij **przycisk Otwórz** na pasku akcji
2. Kliknij "..." obok skoroszytu, który chcesz przypiąć
3. Kliknij pozycję **Przypnij do pulpitu nawigacyjnego**.

## <a name="contributing-workbook-templates"></a>Szablony skoroszytów przyczyniających się

Czy utworzyłeś niesamowity szablon skoroszytu i chcesz udostępnić go społeczności? Aby dowiedzieć się więcej, odwiedź nasze [repozytorium GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Następne kroki
- Aby włączyć środowisko użycia, zacznij wysyłać [niestandardowe zdarzenia](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [widoki strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeśli wysyłasz już niestandardowe zdarzenia lub widoki stron, zapoznaj się z narzędziami Użycia, aby dowiedzieć się, jak użytkownicy korzystają z usługi.
    - [Użytkownicy, sesje, zdarzenia](../../azure-monitor/app/usage-segmentation.md)
    - [Lejki](../../azure-monitor/app/usage-funnels.md)
    - [Przechowywanie](../../azure-monitor/app/usage-retention.md)
    - [User Flows (Przepływy użytkowników)](../../azure-monitor/app/usage-flows.md)
    - [Dodawanie kontekstu użytkownika](../../azure-monitor/app/usage-send-user-context.md)
