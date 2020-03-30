---
title: Tworzenie interakcyjnych raportów usługi Azure Monitor dla maszyn wirtualnych za pomocą skoroszytów
description: Uprość składanie złożonych raportów dzięki wstępnie zdefiniowanym i niestandardowych sparametryzowanym skoroszytom dla usługi Azure Monitor dla maszyn wirtualnych.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: a6ab126c3a5b0d2a82b17fac42dcc9e20f6aba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480457"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Tworzenie interakcyjnych raportów usługi Azure Monitor dla maszyn wirtualnych za pomocą skoroszytów

Skoroszyty łączą tekst, [zapytania dziennika,](../log-query/query-language.md)metryki i parametry w zaawansowane raporty interaktywne. Skoroszyty mogą być edytowane przez innych członków zespołu, którzy mają dostęp do tych samych zasobów platformy Azure.

Skoroszyty są przydatne w scenariuszach, takich jak:

* Eksplorowanie użycia maszyny wirtualnej, gdy nie znasz metryki zainteresowania z wyprzedzeniem: wykorzystanie procesora CPU, miejsce na dysku, pamięci, zależności sieciowe, itp. W przeciwieństwie do innych narzędzi do analizy użycia skoroszyty umożliwiają łączenie wielu rodzajów wizualizacji i analiz, dzięki czemu doskonale nadają się do tego rodzaju eksploracji swobodnej.
* Wyjaśniając zespołowi, jak działa niedawno aprowizowana maszyna wirtualna, pokazując metryki dla liczników kluczy i innych zdarzeń dziennika.
* Udostępnianie wyników eksperymentu zmiany rozmiaru maszyny wirtualnej innym członkom zespołu. Można wyjaśnić cele eksperymentu z tekstem, a następnie wyświetlić wszystkie metryki użycia i zapytania analityczne używane do oceny eksperymentu, wraz z wyraźnymi wezwaniami dla tego, czy każda metryka była powyżej lub poniżej celu.
* Raportowanie wpływu awarii na użycie maszyny Wirtualnej, łączenie danych, wyjaśnienie tekstu i omówienie kolejnych kroków, aby zapobiec awariom w przyszłości.

W poniższej tabeli podsumowano skoroszyty, które usługa Azure Monitor dla maszyn wirtualnych zawiera w celu rozpoczęcia pracy.

| skoroszyt | Opis | Zakres |
|----------|-------------|-------|
| Wydajność | Udostępnia konfigurowalną wersję naszego widoku Lista N i Wykresy w jednym skoroszycie, który wykorzystuje wszystkie liczniki wydajności usługi Log Analytics, które zostały włączone.| Na dużą skalę |
| Liczniki wydajności | Widok wykresu Top N w szerokim zestawie liczników wydajności. | Na dużą skalę |
| Połączenia | Połączenia zapewniają szczegółowy widok połączeń przychodzących i wychodzących z monitorowanych maszyn wirtualnych. | Na dużą skalę |
| Aktywne porty | Zawiera listę procesów, które mają powiązane z portami na monitorowanych maszynach wirtualnych i ich działania w wybranym przedziale czasu. | Na dużą skalę |
| Otwarte porty | Zawiera liczbę portów otwartych na monitorowanych maszynach wirtualnych oraz szczegółowe informacje na temat tych otwartych portów. | Na dużą skalę |
| Połączenia zakończone niepowodzeniem | Wyświetlanie liczby nieudanych połączeń na monitorowanych maszynach wirtualnych, trend awarii i jeśli procent niepowodzeń wzrasta wraz z czasem. | Na dużą skalę |
| Security and Audit | Analiza ruchu TCP/IP, który raportuje ogólne połączenia, złośliwe połączenia, gdzie punkty końcowe IP znajdują się globalnie.  Aby włączyć wszystkie funkcje, należy włączyć wykrywanie zabezpieczeń. | Na dużą skalę |
| Ruch TCP | Raport rankingowy dla monitorowanych maszyn wirtualnych i ich wysłane, odebrane i całkowity ruch sieciowy w siatce i wyświetlany jako linia trendu. | Na dużą skalę |
| Porównanie ruchu | Ten skoroszyt umożliwia porównywanie trendów ruchu sieciowego dla pojedynczego komputera lub grupy maszyn. | Na dużą skalę |
| Wydajność | Udostępnia konfigurowalną wersję naszego widoku wydajności, który wykorzystuje wszystkie liczniki wydajności usługi Log Analytics, które zostały włączone. | Pojedyncza maszyna wirtualna | 
| Połączenia | Połączenia zapewnia szczegółowy widok połączeń przychodzących i wychodzących z maszyny Wirtualnej. | Pojedyncza maszyna wirtualna |
 
## <a name="creating-a-new-workbook"></a>Tworzenie nowego skoroszytu

Skoroszyt składa się z sekcji składających się z niezależnie edytowalnych wykresów, tabel, tekstu i formantów wejściowych. Aby lepiej zrozumieć skoroszyty, zacznijmy od otwarcia szablonu i przejdźmy do tworzenia niestandardowego skoroszytu. 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. Wybierz **opcję Maszyny wirtualne**.

3. Z listy wybierz maszynę wirtualną.

4. Na stronie Maszyny Wirtualnej w sekcji **Monitorowanie** wybierz pozycję **Insights**.

5. Na stronie Statystyki maszyn wirtualnych wybierz kartę **Wydajność** lub **Mapy,** a następnie wybierz pozycję **Wyświetl skoroszyty** z łącza na stronie. Z listy rozwijanej wybierz pozycję **Przejdź do galerii**.

    ![Zrzut ekranu przedstawiający listę rozwijaną skoroszytu](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Spowoduje to uruchomienie galerii skoroszytu z szeregiem skoroszytów, które ułatwiają rozpoczęcie pracy.

7. Utwórz nowy skoroszyt, wybierając pozycję **Nowy**.

    ![Zrzut ekranu przedstawiający galerię skoroszytu](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Edytowanie sekcji skoroszytu

Skoroszyty mają dwa tryby: **tryb edycji**i **tryb czytania**. Po pierwszym uruchomieniu nowego skoroszytu otwiera się on w **trybie edycji**. Pokazuje całą zawartość skoroszytu, w tym wszystkie kroki i parametry, które w przeciwnym razie są ukryte. **Tryb odczytu** przedstawia uproszczony widok stylu raportu. Tryb czytania pozwala na abstrakcję złożoności, która poszła do tworzenia raportu, a jednocześnie o podstawowej mechaniki tylko kilka kliknięć od gdy jest to konieczne do modyfikacji.

![Kontrolki edycji sekcji skoroszytów usługi Azure Monitor dla maszyn wirtualnych](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Po zakończeniu edytowania sekcji kliknij pozycję **Edycja w** lewym dolnym rogu sekcji.

2. Aby utworzyć duplikat sekcji, kliknij ikonę **Sklonuj tę sekcję.** Tworzenie zduplikowanych sekcji jest doskonałym sposobem iteracji kwerendy bez utraty poprzednich iteracji.

3. Aby przenieść sekcję w górę skoroszytu, kliknij ikonę **Przenieś w górę** lub Przenieś w **dół.**

4. Aby trwale usunąć sekcję, kliknij ikonę **Usuń.**

## <a name="adding-text-and-markdown-sections"></a>Dodawanie sekcji tekstu i znaczników

Dodanie nagłówków, wyjaśnień i komentarzy do skoroszytów pomaga przekształcić zestaw tabel i wykresów w narrację. Sekcje tekstu w skoroszytach obsługują [składnię Markdown](https://daringfireball.net/projects/markdown/) dla formatowania tekstu, takich jak nagłówki, pogrubienie, kursywa i listy punktowane.

Aby dodać sekcję tekstową do skoroszytu, użyj przycisku **Dodaj tekst** u dołu skoroszytu lub u dołu dowolnej sekcji.

## <a name="adding-query-sections"></a>Dodawanie sekcji zapytań

![Sekcja Kwerenda w skoroszytach](media/vminsights-workbooks/005-workbook-query-section.png)

Aby dodać sekcję kwerendy do skoroszytu, użyj przycisku **Dodaj zapytanie** u dołu skoroszytu lub u dołu dowolnej sekcji.

Sekcje zapytań są bardzo elastyczne i mogą być używane do odpowiadania na pytania, takie jak:

* W jaki sposób moje wykorzystanie procesora w tym samym okresie było wzrostem ruchu sieciowego?
* Jaki był trend w dostępnej przestrzeni dyskowej w ciągu ostatniego miesiąca?
* Ile awarii połączenia sieciowego wystąpiło z moją maszyną wirtualną w ciągu ostatnich dwóch tygodni? 

Nie ograniczasz się również tylko do wykonywania zapytań z kontekstu maszyny wirtualnej, z której uruchomiono skoroszyt. Można wyszukiwać na wielu maszynach wirtualnych, a także w obszarach roboczych usługi Log Analytics, o ile masz uprawnienia dostępu do tych zasobów.

Aby uwzględnić dane z innych obszarów roboczych usługi Log Analytics lub z określonej aplikacji usługi Application Insights przy użyciu identyfikatora **obszaru roboczego.** Aby dowiedzieć się więcej o kwerendach między zasobami, zapoznaj się z [oficjalnymi wytycznymi](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Zaawansowane ustawienia kwerend analitycznych

Każda sekcja ma własne ustawienia zaawansowane, które ![są dostępne za](media/vminsights-workbooks/006-settings.png) pośrednictwem ustawień Sekcji skoroszytów ikona elementów sterujących sekcji znajduje się po prawej stronie przycisku **Dodaj parametry.**

![Kontrolki edycji sekcji skoroszytów usługi Azure Monitor dla maszyn wirtualnych](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Szerokość niestandardowa**    | Nadaje elementowi dowolny rozmiar, dzięki czemu można zmieścić wiele elementów w jednym wierszu, co pozwala lepiej organizować wykresy i tabele w rozszerzone raporty interaktywne.  |
| **Warunkowo widoczne** | Określ, aby ukryć kroki na podstawie parametru w trybie odczytu. |
| **Eksportowanie parametru**| Zezwalaj zaznaczonemu wierszowi w siatce lub wykresie na późniejsze kroki, aby zmienić wartości lub stać się widoczne.  |
| **Pokaż kwerendę, gdy nie edytujesz** | Wyświetla kwerendę nad wykresem lub tabelą nawet w trybie odczytu.
| **Pokaż przycisk Otwórz w analizie, gdy nie edytujesz** | Dodaje niebieską ikonę Analytics do prawego rogu wykresu, aby umożliwić dostęp jednym kliknięciem.|

Większość z tych ustawień są dość intuicyjne, ale aby zrozumieć **Eksportuj parametr,** lepiej jest sprawdzić skoroszyt, który korzysta z tej funkcji.

Jeden ze wstępnie utworzonych skoroszytów — **TCP Traffic**— zawiera informacje o metrykach połączeń z maszyny Wirtualnej.

Pierwsza sekcja skoroszytu jest oparta na danych kwerendy dziennika. Druga sekcja jest również oparta na danych kwerendy dziennika, ale wybranie wiersza w pierwszej tabeli spowoduje interaktywną aktualizację zawartości wykresów:

![Kontrolki edycji sekcji skoroszytów usługi Azure Monitor dla maszyn wirtualnych](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Zachowanie jest możliwe za pomocą **Gdy element jest zaznaczony, wyeksportować parametr** ustawień zaawansowanych, które są włączone w kwerendzie dziennika tabeli.

![Kontrolki edycji sekcji skoroszytów usługi Azure Monitor dla maszyn wirtualnych](media/vminsights-workbooks/009-settings-export.png)

Druga kwerenda dziennika następnie wykorzystuje eksportowane wartości, gdy wiersz jest zaznaczony w celu utworzenia zestawu wartości, które są następnie używane przez nagłówek sekcji i wykresy. Jeśli nie zaznaczono żadnego wiersza, powoduje to ukrycie nagłówka sekcji i wykresów. 

Na przykład ukryty parametr w drugiej sekcji używa następującego odwołania z wiersza wybranego w siatce:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Dodawanie sekcji metryk

Sekcje metryki zapewniają pełny dostęp do uwzględnienia danych metryk usługi Azure Monitor w interaktywnych raportach. W usłudze Azure Monitor dla maszyn wirtualnych wstępnie utworzone skoroszyty zazwyczaj zawierają dane zapytania analitycznego, a nie dane metryki.  Możesz utworzyć skoroszyty z danymi metryk, co pozwoli ci w pełni wykorzystać najlepsze z obu funkcji w jednym miejscu. Masz również możliwość ściągania danych metryk z zasobów w dowolnej subskrypcji, do których masz dostęp.

Oto przykład danych maszyny wirtualnej pobieranych do skoroszytu w celu zapewnienia wizualizacji wydajności procesora CPU w siatce:

![Kontrolki edycji sekcji skoroszytów usługi Azure Monitor dla maszyn wirtualnych](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Dodawanie sekcji parametrów

Parametry skoroszytu umożliwiają zmianę wartości w skoroszycie bez konieczności ręcznej edycji kwerendy lub sekcji tekstowych. Eliminuje to wymóg konieczności zrozumienia podstawowego języka zapytań analitycznych i znacznie rozszerza potencjalnych odbiorców raportowania opartego na skoroszycie.

Wartości parametrów są zastępowane w sekcji kwerendy, tekstu lub innych parametrów ``{parameterName}``przez umieszczenie nazwy parametru w nawiasach klamrowych, takich jak . Nazwy parametrów są ograniczone do podobnych reguł, takich jak identyfikatory JavaScript, znaki alfabetyczne lub podkreślenia, a następnie znaki alfanumeryczne lub podkreślenia. Na przykład **a1** jest dozwolone, ale **1a** nie jest dozwolone.

Parametry są liniowe, zaczynając od góry skoroszytu i spływając do późniejszych kroków.  Parametry zadeklarowane później w skoroszycie mogą zastępować parametry, które zostały zadeklarowane wcześniej. Umożliwia to również parametry, które używają kwerend, aby uzyskać dostęp do wartości z parametrów zdefiniowanych wcześniej. W samym kroku parametru parametry są również liniowe, od lewej do prawej, gdzie parametry po prawej stronie mogą zależeć od parametru zadeklarowanego wcześniej w tym samym kroku.
 
Istnieją cztery różne typy parametrów, które są obecnie obsługiwane:

|                  |      |
| ---------------- |:-----|
| **Tekst**    | Umożliwia użytkownikowi edytowanie pola tekstowego i opcjonalnie można podać kwerendę, aby wypełnić wartość domyślną. |
| **Lista rozwijana** | Umożliwia użytkownikowi wybór spośród zestawu wartości. |
| **Selektor zakresu czasu**| Umożliwia użytkownikowi wybranie wstępnie zdefiniowanego zestawu wartości zakresu czasu lub wybranie niestandardowego zakresu czasu.|
| **Selektor zasobów** | Umożliwia użytkownikowi wybranie spośród zasobów wybranych dla skoroszytu.|

### <a name="using-a-text-parameter"></a>Korzystanie z parametru tekstowego

Wartość wpisywania przez użytkownika w polu tekstowym jest zastępowana bezpośrednio w kwerendzie, bez uciekania lub cytowanie. Jeśli potrzebna wartość jest ciągiem, kwerenda powinna mieć cudzysłowy wokół parametru **(np. '{parameter}'**).

Parametr tekstowy umożliwia użycie wartości w polu tekstowym w dowolnym miejscu. Może to być nazwa tabeli, nazwa kolumny, nazwa funkcji, operator itp.  Typ parametru tekstowego ma ustawienie **Uzyskaj wartość domyślną z kwerendy analitycznej,** która umożliwia autorowi skoroszytu użycie kwerendy do wypełniania wartości domyślnej dla tego pola tekstowego.

W przypadku korzystania z wartości domyślnej z kwerendy dziennika, tylko pierwsza wartość pierwszego wiersza (wiersz 0, kolumna 0) jest używana jako wartość domyślna. W związku z tym zaleca się ograniczyć kwerendę, aby zwrócić tylko jeden wiersz i jedną kolumnę. Wszystkie inne dane zwrócone przez kwerendę są ignorowane. 

Niezależnie od wartości zwraca kwerendy zostaną zastąpione bezpośrednio bez ucieczki lub cytowanie. Jeśli kwerenda zwraca żadnych wierszy, wynikiem parametru jest pusty ciąg (jeśli parametr nie jest wymagany) lub niezdefiniowany (jeśli parametr jest wymagany).

### <a name="using-a-drop-down"></a>Korzystanie z listy rozwijanej

Typ parametru rozwijanego umożliwia utworzenie formantu rozwijanego, umożliwiającego wybór jednej lub wielu wartości.

Rozwijana jest wypełniana przez kwerendę dziennika lub JSON. Jeśli kwerenda zwraca jedną kolumnę, wartości w tej kolumnie są zarówno wartością, jak i etykietą w formancie rozwijanej. Jeśli kwerenda zwraca dwie kolumny, pierwsza kolumna jest wartością, a druga kolumna jest etykietą wyświetlaną w rozwijaniu. Jeśli kwerenda zwraca trzy kolumny, trzecia kolumna jest używana do wskazania domyślnego zaznaczenia w tej listy rozwijanej. Ta kolumna może być dowolnego typu, ale najprostsze jest użycie bool lub typy liczbowe, gdzie 0 jest false, a 1 jest true.

Jeśli kolumna jest typem ciągu, ciąg zerowy/pusty jest uważany za fałszywy, a każda inna wartość jest uważana za true. W przypadku pojedynczych odnośnych odnośnych wartości pierwsza wartość z wartością rzeczywistą jest używana jako wybór domyślny.  W przypadku wielu odnośnych odnośnych wartości wszystkie wartości o wartości rzeczywistej są używane jako domyślny zestaw zaznaczony. Elementy z listy rozwijanej są wyświetlane w dowolnej kolejności kwerendy zwrócił wiersze. 

Przyjrzyjmy się parametrom obecnym w raporcie Omówienie połączeń. Kliknij symbol edycji obok **pozycji Kierunek**.

![Kontrolki edycji sekcji skoroszytów usługi Azure Monitor dla maszyn wirtualnych](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Spowoduje to uruchomienie elementu menu **Edytuj parametr.**

![Kontrolki edycji sekcji skoroszytów usługi Azure Monitor dla maszyn wirtualnych](media/vminsights-workbooks/012-workbook-edit-parameter.png)

JSON umożliwia generowanie dowolnej tabeli wypełnionej zawartością. Na przykład następujący JSON generuje dwie wartości w rozwijanej:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Bardziej stosowanym przykładem jest użycie listy rozwijanej do wybierania z zestawu liczników wydajności według nazwy:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

Kwerenda wyświetli wyniki w następujący sposób:

![Rozwijanie licznika perf](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Listy rozwijane to niezwykle zaawansowane narzędzia do dostosowywania i tworzenia interaktywnych raportów.

### <a name="time-range-parameters"></a>Parametry zakresu czasu

Chociaż można tworzyć własne parametry niestandardowego zakresu czasu za pomocą typu parametru rozwijanego, można również użyć out-of-box typ parametru zakresu czasu, jeśli nie potrzebujesz tego samego stopnia elastyczności. 

Typy parametrów zakresu czasu mają 15 domyślnych zakresów, które trwają od pięciu minut do ostatnich 90 dni. Istnieje również opcja zezwalania na wybór niestandardowego zakresu czasu, co umożliwia operatorowi raportu wybranie jawnych wartości początkowych i zatrzymania dla zakresu czasu.

### <a name="resource-picker"></a>Selektor zasobów

Typ parametru selektora zasobów umożliwia zakres raportu do niektórych typów zasobów. Przykładem skoroszytu wstępnie utworzonego, który wykorzystuje typ selektora zasobów, jest **skoroszyt wydajności.**

![Rozwijanie obszarów roboczych](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Zapisywanie skoroszytów i udostępnianie ich zespołowi

Skoroszyty są zapisywane w obszarze roboczym usługi Log Analytics lub zasobie maszyny wirtualnej, w zależności od sposobu uzyskiwania dostępu do galerii skoroszytów. Skoroszyt można zapisać w sekcji **Moje raporty,** która jest prywatna dla Ciebie lub w sekcji **Raporty udostępnione,** która jest dostępna dla wszystkich osób z dostępem do zasobu. Aby wyświetlić wszystkie skoroszyty w zasobie, kliknij przycisk **Otwórz** na pasku akcji.

Aby udostępnić skoroszyt aktualnie w **moich raportach:**

1. Kliknij **przycisk Otwórz** na pasku akcji
2. Kliknij "..." obok skoroszytu, który chcesz udostępnić
3. Kliknij **pozycję Przenieś do raportów udostępnionych**.

Aby udostępnić skoroszyt za pomocą łącza lub wiadomości e-mail, kliknij pozycję **Udostępnij** na pasku akcji. Należy pamiętać, że adresaci łącza potrzebują dostępu do tego zasobu w witrynie Azure Portal, aby wyświetlić skoroszyt. Aby wprowadzić zmiany, adresaci potrzebują co najmniej uprawnień współautora dla zasobu.

Aby przypiąć łącze do skoroszytu do pulpitu nawigacyjnego platformy Azure:

1. Kliknij **przycisk Otwórz** na pasku akcji
2. Kliknij "..." obok skoroszytu, który chcesz przypiąć
3. Kliknij pozycję **Przypnij do pulpitu nawigacyjnego**.

## <a name="next-steps"></a>Następne kroki

- Aby zidentyfikować ograniczenia i ogólną wydajność maszyn wirtualnych, zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md).

- Aby dowiedzieć się więcej o odnalezionych zależnościach aplikacji, zobacz [Wyświetlanie usługi Azure Monitor dla map maszyn wirtualnych.](vminsights-maps.md)
