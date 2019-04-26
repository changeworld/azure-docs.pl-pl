---
title: Tworzenie interaktywnych raportów, skoroszytów w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Uprość złożone raportowanie za pomocą wbudowanych i niestandardowych sparametryzowane skoroszytów
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: bcd4ae5b7092aafc452c5af3d17fd22c263b5d35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60373627"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Tworzenie interaktywnych raportów, skoroszytów w usłudze Azure Monitor

Skoroszyty połączenie tekstu, [zapytań analitycznych](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), metryk usługi Azure i parametrów w zaawansowanych interaktywnych raportów. Skoroszyty są edytowalne przez innych członków zespołu, którzy mają dostęp do tych samych zasobów platformy Azure.

Skoroszyty są przydatne w scenariuszach, takich jak:

* Eksplorowanie użycia aplikacji, kiedy nie wiesz wcześniej istotne metryki: liczby użytkowników, danych, współczynniki konwersji itp. W przeciwieństwie do innych narzędzi analitycznych użycia skoroszytów pozwalają połączyć wiele rodzajów wizualizacji i analiz, dzięki czemu idealne narzędzie do tego rodzaju dowolnych eksploracji.
* Do zespołu wyjaśniające, jak działa funkcja nowo wydanego, przedstawiający użytkownika Liczba interakcji kluczy i innych metryk.
* Udostępnianie wyników, a / B eksperymentów w swojej aplikacji z innymi członkami zespołu. Można wyjaśnić cele eksperymentu z tekstem, a następnie Pokaż każdego metryki użycia i zapytania usługi Analytics używane do oceny eksperymentów, wraz z wyczyść wezwaniem dla tego, czy wszystkie metryki został powyżej lub poniżej docelowymi.
* Raportowanie wpływ awarii na użycie aplikacji, łącząc dane, tekst wyjaśnienie i dyskusję na temat następnych kroków, aby uniknąć przerw w przyszłości.

## <a name="starting-with-a-template-or-saved-workbook"></a>Uruchamianie przy użyciu szablonu lub zapisany skoroszyt

Skoroszyt składa się z sekcji składający się z można edytować niezależnie wykresów, tabel, tekst, a kontrolki wejściowe. Aby lepiej zrozumieć skoroszyty, najlepiej otwórz je w. 

Wybierz **skoroszyty** z menu po lewej stronie z wewnątrz usługi Application Insights środowisko dla aplikacji.

![Zrzut ekranu przedstawiający nawigacji do skoroszytów](./media/usage-workbooks/001-workbooks.png)

Spowoduje to uruchomienie galerii skoroszytu, wprowadzając szereg wbudowanych skoroszyty, aby pomóc Ci rozpocząć pracę.

![Zrzut ekranu przedstawiający galerii skoroszytu](./media/usage-workbooks/002-workbook-gallery.png)

Rozpoczniemy od **domyślnego szablonu**, który znajduje się pod nagłówkiem **— szybki start**.

![Zrzut ekranu przedstawiający galerii skoroszytu](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Edytowanie, rozmieszczanie, klonowanie i usuwanie sekcji skoroszytu

Skoroszyty mają dwa tryby: **tryb edycji**, i **trybie do czytania**. Po pierwszym uruchomieniu skoroszyt domyślny zostanie on otwarty w **tryb edycji**. Pokazuje całą zawartość skoroszytu, w tym wszelkie kroki i parametry, które w przeciwnym razie są ukryte. **Tryb odczytu** przedstawia uproszczoną styl widoku. Dzięki temu można natychmiast warstwę abstrakcji złożoności, które pojawiły się w tworzenie raportu przy zachowaniu bazowego mechanics tylko kilka kliknięć, gdy są potrzebne do modyfikacji.

![Application Insights skoroszyty sekcji formanty edycji](./media/usage-workbooks/editing-controls-new.png)

1. Po zakończeniu edycji sekcji, kliknij **przeprowadzić edycję** w lewym dolnym rogu sekcji.

2. Aby utworzyć duplikat sekcji, kliknij przycisk **Sklonuj tę sekcję** ikony. Tworzenie duplikatów sekcje to doskonały sposób iteracji w zapytaniu bez utraty poprzednich iteracji.

3. Aby przenieść w górę do sekcji w skoroszycie, kliknij przycisk **Przenieś w górę** lub **Przenieś w dół** ikony.

4. Aby trwale usunąć sekcję, kliknij przycisk **Usuń** ikony.

## <a name="adding-text-and-markdown-sections"></a>Dodawanie tekstu i sekcje znaczników Markdown

Dodawanie nagłówków, wyjaśnień i komentarz do skoroszyty pomaga przekształcić zbiór tabel i wykresów narracji. Sekcje tekstu w pomocy technicznej skoroszyty [składnię języka znaczników Markdown](https://daringfireball.net/projects/markdown/) formatowania, takich jak nagłówki, pogrubienie, kursywa i list punktowanych tekstu.

Aby dodać sekcję tekstu do skoroszytu, należy użyć **Dodaj tekst** przycisk w dolnej części skoroszytu lub u dołu dowolną sekcję.

## <a name="adding-query-sections"></a>Dodawanie sekcji zapytania

![Sekcja zapytań w skoroszycie](./media/usage-workbooks/analytics-section-new.png)

Aby dodać sekcję zapytania do skoroszytu, użyj **Dodaj zapytanie** przycisk w dolnej części skoroszytu lub u dołu dowolną sekcję.

Sekcje zapytania są bardzo elastyczne i może służyć do odpowiedzi na pytania, takie jak:

* Jak wiele wyjątków throw witryny w przedziale czasu jako spadek użycia?
* Jaki był Rozkład czasów ładowania strony użytkownikom, którzy wyświetlają niektóre strony?
* Ilu użytkowników wyświetlane niektóre zbiór stron w witrynie, ale nie inny zestaw stron? Może to być przydatne do zrozumienia, jeśli masz klastrów użytkowników, którzy korzystają z różnych podzbiory funkcji witryny sieci (Użyj `join` operator `kind=leftanti` modyfikator w [język zapytania Kusto](/azure/kusto/query/)).

Możesz również nie są tylko ograniczone do wykonywania zapytań w kontekście aplikacji uruchomiony ten skoroszyt z. Wiele aplikacji usługi Application Insights monitorowane, jak i obszarów roboczych usługi Log Analytics można badać, tak długo, jak długo mają uprawnienia dostępu do tych zasobów.

Do wykonywania zapytań z użycia zasobów usługi Application Insights zewnętrznego dodatkowe **aplikacji** identyfikatora.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

To zapytanie jest używane do łączenia żądań z trzech różnych aplikacji. Aplikacja o nazwie app01, aplikacji o nazwie app02 i żądań z lokalnego zasobu usługi Application Insights.

Aby pobrać dane z użytku zewnętrznego obszaru roboczego usługi Log Analytics **obszaru roboczego** identyfikatora.

Aby dowiedzieć się więcej na temat zapytania obejmujące wiele zasobów się [oficjalne wskazówki](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Zaawansowane ustawienia zapytania analizy

Każda sekcja ma swoje własne ustawienia zaawansowane, które są dostępne za pośrednictwem ikony ustawień ![Application Insights skoroszyty sekcji formanty edycji](./media/usage-workbooks/005-settings.png) znajdującej się po prawej stronie **Dodaj parametry** przycisku.

![Application Insights skoroszyty sekcji formanty edycji](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Szerokości niestandardowe**    | Ustaw, aby element dowolnego rozmiaru, dzięki czemu można umieścić wiele elementów w jednym wierszu, co pozwala lepiej zorganizować dla wykresów i tabel w zaawansowanych interaktywnych raportów.  |
   | **Jako widoczny warunkowo** | Użyj tego możesz ukrywać kroki na podstawie parametru w trybie do czytania. |
   | **Eksportuj parametr**| Pozwala to na podstawie wiersza wybranego w siatce lub na wykresie, aby spowodować, że dalszych krokach zmienić wartości lub stanie się widoczna.  |
   | **Wyświetl zapytanie poza trybem edycji** | Spowoduje to wyświetlenie zapytania powyżej wykres lub tabelę, nawet w trybie do czytania.
   | **Pokaż przycisk Otwórz w przycisk Analiza poza trybem edycji** | Spowoduje to dodanie niebieską ikoną Analytics rogu wykresu, aby zezwolić na dostęp jednym kliknięciem.|

Większość z tych ustawień jest dość intuicyjne, aby dowiedzieć się, ale **Eksportuj parametr** warto zbadać skoroszytu korzystającej z tej funkcji.

Jedną z wbudowanych skoroszyty zawiera informacje dotyczące aktywnych użytkowników.

Pierwsza część skoroszytu są oparte na danych analitycznych zapytań:

![Application Insights skoroszyty sekcji formanty edycji](./media/usage-workbooks/003-active-users.png)

Druga sekcja również na podstawie zapytania analizy danych, ale wybranie wiersza w pierwszej tabeli interaktywnie aktualizuje zawartość wykresu:

![Application Insights skoroszyty sekcji formanty edycji](./media/usage-workbooks/004-active-users-trend.png)

 Jest to możliwe za pośrednictwem **po wybraniu elementu Eksportuj parametr** Zaawansowane ustawienia, które są włączone w tabeli zapytania usługi Analytics.

![Application Insights skoroszyty sekcji formanty edycji](./media/usage-workbooks/007-settings-export.png)

Drugie zapytanie analizy następnie korzysta z wartości eksportowanych wybranie wiersza. Jeśli żaden wiersz nie jest zaznaczone, domyślnie w wierszu odpowiadającym wartości całkowitej. 

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

## <a name="adding-metrics-sections"></a>Dodawanie sekcji metryki

Metryki sekcje zapewniają pełny dostęp, aby dołączyć dane metryk usługi Azure Monitor do interaktywnych raportów. Wiele wbudowanych skoroszyty będzie zawierać zarówno zapytania analizy danych, jak i dane metryk, umożliwiając w pełni wykorzystać zalety obu funkcji wszystko w jednym miejscu. Istnieje również możliwość ściągać dane metryk z dowolnej subskrypcji, do których masz dostęp do zasobów.

Oto przykład danych maszyny wirtualnej są pobierane do skoroszytu umożliwia wizualizację siatki wydajność procesora CPU:

![Application Insights skoroszyty sekcji formanty edycji](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Dodając parametr sekcje

Parametry skoroszytu umożliwiają zmianę wartości w skoroszycie, bez konieczności ręcznego edytowania sekcje zapytania lub tekstu.  Eliminuje wymóg konieczności zrozumienie podstawowych języka zapytań usługi analytics i znacznie rozszerza raportowanie oparte na skoroszycie potencjalnych odbiorców.

Wartości parametrów są zastępowane w zapytania, tekst lub inne sekcje parametr, umieszczając nazwę parametru w nawiasach klamrowych, takich jak ``{parameterName}``.  Nazwy parametrów są ograniczone do podobnych reguł identyfikatorów języka JavaScript, po prostu alfabetu lub podkreślenia, następują znaki alfanumeryczne oraz podkreślenia. Na przykład **a1** jest dozwolony, ale **1a** jest niedozwolone.

Parametry są liniowe, począwszy od góry skoroszytu i przepływają w dół do dalszych krokach.  Parametrów zadeklarowanych w dalszej części skoroszytu można zastąpić te, które zostały zgłoszone dalsze w górę.  Umożliwia to użycie zapytania, aby uzyskać dostęp do wartości z parametrów zdefiniowanych dalsze Konfigurowanie parametrów.  W ramach kroku parametr sam parametry są również liniowego, od lewej do prawej, w której parametry, z prawej strony mogą być zależne od parametru zadeklarowana we wcześniejszej części tego samego kroku.
 
Istnieją cztery typy parametrów, które są obecnie obsługiwane:

  |         |          |
   | ---------------- |:-----|
   | **Tekst**    | użytkownik będzie edytować pole tekstowe, a opcjonalnie można podać zapytanie, aby podać wartość domyślną. |
   | **Lista rozwijana** | Użytkownik wybierze z zestawu wartości. |
   | **Selektor zakresu czasu**| Użytkownik wybierać zestaw wstępnie zdefiniowanych wartości zakresu czasu lub masz do wyboru niestandardowego zakresu czasu.|
   | **Wybór zasobów** | Użytkownik wybierze z zasobów wybranych do skoroszytu.|

### <a name="using-a-text-parameter"></a>Za pomocą parametru tekstu

Wartość użytkownik wpisze w polu tekstowym jest zastępowany bezpośrednio w zapytaniu bez anulowania zapewnianego element i cytowanie. Jeśli wartość potrzebne jest ciągiem, zapytanie powinien mieć parametru w cudzysłowie (takich jak **'{parameter}'**).

Dzięki temu wartość w polu tekstowym, aby używać w dowolnym miejscu. Może być nazwa tabeli, nazwa kolumny, nazwa funkcji, operatora, itp.

Typ parametru tekst ma ustawienie **Pobierz wartość domyślną z zapytania analizy**, co pozwala autor skoroszytu korzystać z zapytania, aby wypełnić wartością domyślną dla tego pola tekstowego.

Korzystając z wartością domyślną z zapytania analizy, tylko pierwsza wartość pierwszego wiersza (wiersz 0, kolumnę 0) jest używany jako wartość domyślną. W związku z tym zalecane jest aby ograniczyć zapytanie w taki sposób, aby zwrócić tylko jeden wiersz i jedną kolumnę. Inne dane zwracane przez zapytanie jest ignorowany. 

Niezależnie od wartości zwracane przez zapytanie zostanie zastąpione bezpośrednio z nie anulowania zapewnianego element lub cytowanie. Jeśli zapytanie nie zwraca żadnych wierszy, wynik parametru jest ciągiem pustym (Jeśli parametr nie jest wymagane) lub niezdefiniowana (Jeśli parametr jest wymagany).

### <a name="using-a-dropdown"></a>Za pomocą listy rozwijanej

Lista rozwijana typ parametru pozwala utworzyć kontrolkę listy rozwijanej zezwalający na wybór jednego lub wielu wartości.

Listy rozwijanej jest wypełniana przez zapytania usługi analytics. Jeżeli zapytanie zwraca jedną kolumnę, wartości w tej kolumnie są **wartość** i **etykiety** z rozwijanej listy formantów. Jeżeli zapytanie zwraca dwie kolumny, pierwsza kolumna jest **wartość**, a druga kolumna **etykiety** wyświetlane na liście rozwijanej.  Jeżeli zapytanie zwraca trzy kolumny, kolumna 3 jest używana do wskazania wybór domyślny na tej liście rozwijanej.  W tej kolumnie mogą być dowolnego typu, ale najprostszych jest użycie bool lub typów numerycznych, gdzie 0 to wartość false, a 1 to wartość true.

 Jeśli kolumna ma typ ciągu, wartości null lub być pusty ciąg jest uważany za wartość false, a wszelkie inne wartości jest uważany za spełniony. Dla pojedynczego wyboru list rozwijanych pierwsza wartość z wartością true służy jako domyślny wybór.  Dla wielu wybór listy rozwijane wszystkie wartości z wartością true są używane jako zestaw domyślnie wybrana. Elementy na liście rozwijanej są wyświetlane w kolejności, niezależnie od zapytanie zwróciło wierszy. 

Przyjrzyjmy się parametry, które są obecne w raporcie aktywnych użytkowników. Kliknij symbol edycji **TimeRange**.

![Application Insights skoroszyty sekcji formanty edycji](./media/usage-workbooks/009-time-range.png)

Spowoduje to uruchomienie elementu menu Edycja parametru:

![Application Insights skoroszyty sekcji formanty edycji](./media/usage-workbooks/010-time-range-edit.png)

Zapytanie używa funkcji języka zapytań usługi analytics o nazwie **datatable** , która umożliwia generowanie tabeli dowolnego pełnej zawartości, z powietrza alokowania elastycznego! Na przykład, następujące zapytanie usługi analytics:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Generuje wynik:

![Application Insights skoroszyty sekcji formanty edycji](./media/usage-workbooks/011-data-table.png)

Przykład bardziej odpowiednie się za pomocą listy rozwijanej wybierz z zestawu krajów według nazwy:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

Zapytanie spowoduje wyświetlenie wyników w następujący sposób:

![Lista rozwijana krajów](./media/usage-workbooks/012-country-dropdown.png)

Listy rozwijane są bardzo zaawansowanych narzędzi do dostosowywania i tworzenia interakcyjnych raportów.

### <a name="time-range-parameters"></a>Parametry zakresu czasu

Można tworzyć własne parametr zakres czasu niestandardowych za pomocą listy rozwijanej Typ parametru, również można użyć typ parametru zakresu czasu out-of-box, jeśli nie potrzebujesz tego samego stopień elastyczności. 

Typy parametrów zakresu czasu ma 15 zakresów dla domyślnych, które bardziej szczegółowo w ciągu pięciu minut do ostatnich 90 dni. Istnieje również opcję, aby umożliwić wybranie niestandardowego zakresu czasu, który umożliwia operatorowi raportu wybierz przycisk start jawnego i Zatrzymaj wartości zakresu czasu.

### <a name="resource-picker"></a>Selektor zasobów

Typ parametru selektor zasobów daje możliwość zakres raportu do określonych typów zasobów. Na przykład wstępnie skoroszytu, który korzysta z zasobów typu selektora **Insights błąd** skoroszytu.

![Lista rozwijana krajów](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Zapisywanie i udostępnianie skoroszytów swojemu zespołowi

Skoroszyty są zapisywane w ramach zasobu usługi Application Insights w **Moje raporty** sekcja, która jest prywatny, w przypadku lub **udostępnione raporty** sekcja, która jest dostępny dla wszystkich użytkowników z dostępem do Zasób usługi Application Insights. Aby wyświetlić wszystkie skoroszyty w zasobie, kliknij **Otwórz** przycisk na pasku akcji.

Aby udostępnić skoroszytu, w którym znajduje się obecnie w **Moje raporty**:

1. Kliknij przycisk **Otwórz** na pasku akcji
2. Kliknij przycisk "..." obok skoroszytu, który chcesz udostępnić
3. Kliknij przycisk **Przenieś do raportów udostępnionych**.

Aby udostępnić skoroszyt za pomocą łącza lub za pośrednictwem poczty e-mail, kliknij przycisk **udostępnianie** na pasku akcji. Należy pamiętać, że adresaci łącza muszą mieć dostęp do tego zasobu w witrynie Azure portal, aby wyświetlić skoroszyt. Aby dokonać edycji, adresaci muszą dysponować co najmniej uprawnienia współautora dla zasobu.

Aby przypiąć łącze do skoroszytu na pulpicie nawigacyjnym platformy Azure:

1. Kliknij przycisk **Otwórz** na pasku akcji
2. Kliknij przycisk "..." obok skoroszytu, który chcesz przypiąć
3. Kliknij pozycję **Przypnij do pulpitu nawigacyjnego**.

## <a name="contributing-workbook-templates"></a>Współtworzenie szablony skoroszytów

Utworzony szablon awesome skoroszytu i chcesz udostępnić go społeczności? Aby dowiedzieć się więcej, odwiedź nasz [repozytorium GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Kolejne kroki
- Aby umożliwić użycie środowiska, Rozpocznij wysyłanie [zdarzenia niestandardowe](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [wyświetlenia strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeśli już wysyłać niestandardowe zdarzenia lub wyświetlenia stron, zapoznaj się z narzędzia obciążenia, aby dowiedzieć się, jak używać usługi przez użytkowników.
    - [Użytkownicy, sesje, zdarzenia](../../azure-monitor/app/usage-segmentation.md)
    - [Lejki](../../azure-monitor/app/usage-funnels.md)
    - [Przechowywanie](../../azure-monitor/app/usage-retention.md)
    - [User Flows (Przepływy użytkowników)](../../azure-monitor/app/usage-flows.md)
    - [Dodawanie kontekstu użytkownika](../../azure-monitor/app/usage-send-user-context.md)