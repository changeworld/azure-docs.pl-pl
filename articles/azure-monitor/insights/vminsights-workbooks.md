---
title: Tworzenie interaktywnych raportów, skoroszytów w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Uprość złożone raportowanie za pomocą wstępnie zdefiniowanych i niestandardowych sparametryzowane skoroszyty dla usługi Azure Monitor dla maszyn wirtualnych.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2019
ms.author: magoedte
ms.openlocfilehash: 90c236347380bb5d5e51db56d0f431d2659a7258
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61387280"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Tworzenie interaktywnych raportów, skoroszytów w usłudze Azure Monitor

Skoroszyty połączenie tekstu, [rejestrowania zapytań](../log-query/query-language.md), metryki i parametrów w zaawansowanych interaktywnych raportów. Skoroszyty są edytowalne przez innych członków zespołu, którzy mają dostęp do tych samych zasobów platformy Azure.

Skoroszyty są przydatne w scenariuszach takich jak:

* Eksplorowanie użytkowania maszyny wirtualnej, kiedy nie wiesz wcześniej istotne metryki: Użycie procesora CPU, miejsca na dysku, pamięci, zależności sieci itp. W przeciwieństwie do innych narzędzi analitycznych użycia skoroszytów pozwalają połączyć wiele rodzajów wizualizacji i analiz, dzięki czemu idealne narzędzie do tego rodzaju dowolnych eksploracji.
* Do zespołu wyjaśniające, jak niedawno maszyna wirtualna działa, poprzez wyświetlanie metryk kluczowych liczników i inne zdarzenia dziennika.
* Udostępnianie wyników eksperyment zmiany rozmiaru maszyny wirtualnej z innymi członkami zespołu. Można wyjaśnić cele eksperymentu z tekstem, a następnie wyświetlić każdego użycie metryki i zapytań analiz używane do oceny eksperymentów, wraz z wyczyść wezwaniem dla tego, czy wszystkie metryki został powyżej lub poniżej docelowymi.
* Raportowanie wpływ awarii na użycie maszyny Wirtualnej, łącząc dane, tekst wyjaśnienie i dyskusję na temat następnych kroków, aby uniknąć przerw w przyszłości.

Usługa Azure Monitor dla maszyn wirtualnych zawiera kilka skoroszyty, które ułatwią Ci rozpoczęcie pracy i w poniższej tabeli podsumowano je.

| Skoroszyt | Opis | Zakres |
|----------|-------------|-------|
| Wydajność | Udostępnia dostosowywalne wersję naszych pierwszych N listy i widok wykresów w jednym skoroszyt, który wykorzystuje wszystkie liczniki wydajności usługi Log Analytics, które mają włączone.| W dużej skali |
| Liczniki wydajności | Widok wykresu górnego N w szeroki zestaw liczników wydajności. | W dużej skali |
| Połączenia | Połączenia zapewniają uzyskać szczegółowy widok połączenia przychodzące i wychodzące z monitorowanych maszyn wirtualnych. | W dużej skali |
| Aktywne porty | Zawiera listę procesów, które mają powiązane porty na monitorowanych maszynach wirtualnych i ich działania w wybranym przedziale czasu. | W dużej skali |
| Otwarte porty | Zawiera liczbę portów, Otwórz na monitorowanych maszynach wirtualnych i szczegóły na te Otwieranie portów. | W dużej skali |
| Połączenia zakończone niepowodzeniem | Wyświetlanie łącznej liczby połączenia zakończone niepowodzeniem na monitorowanych maszynach wirtualnych, tendencje błędów, a jeśli wartość procentowa błędów zwiększa się wraz z upływem czasu. | W dużej skali |
| Security and Audit | Analiza ruchu protokołu TCP/IP zawierający raporty dotyczące połączeń ogólną złośliwego połączeń, których punkty końcowe adresów IP znajdują się globalnie.  Aby włączyć wszystkie funkcje, należy włączyć wykrywanie zabezpieczeń. | W dużej skali |
| Ruch TCP | Rangi raport dotyczący monitorowane maszyny wirtualne i sieci wysłane, odebranych i łączna liczba ruchu w siatce i wyświetlane jako linia trendu. | W dużej skali |
| Porównanie ruchu | Skoroszyty ten umożliwia porównywanie trendów ruchu sieciowego dla jednego komputera lub grupy komputerów. | W dużej skali |
| Wydajność | Udostępnia dostosowywalne wersję naszych widok wydajności, która wykorzystuje wszystkie liczniki wydajności usługi Log Analytics, które zostało włączone. | Pojedyncza maszyna wirtualna | 
| Połączenia | Połączenia zapewniają uzyskać szczegółowy widok połączeń przychodzących i wychodzących z maszyny Wirtualnej. | Pojedyncza maszyna wirtualna |
 
## <a name="starting-with-a-template-or-saved-workbook"></a>Uruchamianie przy użyciu szablonu lub zapisany skoroszyt

Skoroszyt składa się z sekcji składający się z można edytować niezależnie wykresów, tabel, tekst, a kontrolki wejściowe. Aby lepiej zrozumieć skoroszyty, umożliwia uruchamianie przez otwarcie szablonu i tworzenia niestandardowych skoroszytu. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **maszyn wirtualnych**.

3. Z listy wybierz maszynę wirtualną.

4. Na stronie maszyny Wirtualnej w **monitorowanie** zaznacz **Insights (wersja zapoznawcza)**.

5. Na stronie szczegółowych informacji maszyny Wirtualnej wybierz **wydajności** lub **mapy** , a następnie wybierz pozycję **skoroszyty widoku** przy użyciu linku na stronie. 

    ![Zrzut ekranu przedstawiający nawigacji do skoroszytów](media/vminsights-workbooks/workbook-option-01.png)

6. Wybierz z listy rozwijanej **przejdź do galerii** w dolnej części listy.

    ![Zrzut ekranu z listy rozwijanej skoroszytu](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Jest uruchamiany w galerii skoroszytu, wprowadzając szereg wbudowanych skoroszyty, aby pomóc Ci rozpocząć pracę.

7. Rozpoczniemy od **domyślnego szablonu**, który znajduje się pod nagłówkiem **— szybki start**.

    ![Zrzut ekranu przedstawiający galerii skoroszytu](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Edytowanie skoroszytu sekcje

Skoroszyty mają dwa tryby: **tryb edycji**, i **trybie do czytania**. Po pierwszym uruchomieniu skoroszytu szablonu domyślnego, zostanie on otwarty w **tryb edycji**. Pokazuje całą zawartość skoroszytu, w tym wszelkie kroki i parametry, które w przeciwnym razie są ukryte. **Tryb odczytu** przedstawia uproszczoną styl widoku. Tryb odczytu umożliwia abstrakcji natychmiast złożoności, które pojawiły się w tworzenie raportu przy zachowaniu bazowego mechanics tylko kilka kliknięć, gdy są potrzebne do modyfikacji.

![Usługa Azure Monitor dla sekcji skoroszyty maszyn wirtualnych z formantów edycji](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Po zakończeniu edycji sekcji, kliknij **przeprowadzić edycję** w lewym dolnym rogu sekcji.

2. Aby utworzyć duplikat sekcji, kliknij przycisk **Sklonuj tę sekcję** ikony. Tworzenie duplikatów sekcje to doskonały sposób iteracji w zapytaniu bez utraty poprzednich iteracji.

3. Aby przenieść w górę do sekcji w skoroszycie, kliknij przycisk **Przenieś w górę** lub **Przenieś w dół** ikony.

4. Aby trwale usunąć sekcję, kliknij przycisk **Usuń** ikony.

## <a name="adding-text-and-markdown-sections"></a>Dodawanie tekstu i sekcje znaczników Markdown

Dodawanie nagłówków, wyjaśnień i komentarz do skoroszyty pomaga przekształcić zbiór tabel i wykresów narracji. Sekcje tekstu w pomocy technicznej skoroszyty [składnię języka znaczników Markdown](https://daringfireball.net/projects/markdown/) formatowania, takich jak nagłówki, pogrubienie, kursywa i list punktowanych tekstu.

Aby dodać sekcję tekstu do skoroszytu, należy użyć **Dodaj tekst** przycisk w dolnej części skoroszytu lub u dołu dowolną sekcję.

## <a name="adding-query-sections"></a>Dodawanie sekcji zapytania

![Sekcja zapytań w skoroszycie](media/vminsights-workbooks/005-workbook-query-section.png)

Aby dodać sekcję zapytania do skoroszytu, użyj **Dodaj zapytanie** przycisk w dolnej części skoroszytu lub u dołu dowolną sekcję.

Sekcje zapytania są bardzo elastyczne i może służyć do odpowiedzi na pytania, takie jak:

* Jak oceniasz Moje użycie procesora CPU w przedziale czasu jako zwiększenie ruchu w sieci?
* Jaki był trend w dostępnego miejsca na dysku w ciągu ostatniego miesiąca?
* Ile awarie połączenia sieciowego Moja maszyna wirtualna wystąpić w ciągu ostatnich dwóch tygodni? 

Możesz również nie są tylko ograniczone do wykonywania zapytań z kontekstu maszyna wirtualna uruchomiona skoroszyt programu. W wielu maszyn wirtualnych, a także w przypadku obszarów roboczych usługi Log Analytics można badać, tak długo, jak długo mają uprawnienia dostępu do tych zasobów.

Aby dołączyć dane z innych obszarów roboczych usługi Log Analytics lub z określonej aplikacji usługi Application Insights, za pomocą **obszaru roboczego** identyfikatora. Aby dowiedzieć się więcej na temat zapytania obejmujące wiele zasobów, zapoznaj się [oficjalne wskazówki](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Zaawansowane ustawienia zapytania analizy

Każda sekcja ma swoje własne ustawienia zaawansowane, które są dostępne za pośrednictwem ustawień ![skoroszyty sekcji formanty edycji](media/vminsights-workbooks/006-settings.png) ikony znajdującej się po prawej stronie **Dodaj parametry** przycisku.

![Usługa Azure Monitor dla sekcji skoroszyty maszyn wirtualnych z formantów edycji](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Szerokości niestandardowe**    | Oznacza, że element dowolnego rozmiaru, dzięki czemu można umieścić wiele elementów w jednym wierszu, co pozwala lepiej zorganizować dla wykresów i tabel w zaawansowanych interaktywnych raportów.  |
| **Jako widoczny warunkowo** | Określ możesz ukrywać kroki na podstawie parametru w trybie do czytania. |
| **Eksportuj parametr**| Zezwalaj na podstawie wiersza wybranego w siatce lub na wykresie, aby spowodować, że dalszych krokach zmienić wartości lub stanie się widoczna.  |
| **Wyświetl zapytanie poza trybem edycji** | Wyświetla kwerendy powyżej wykres lub tabelę, nawet w trybie do czytania.
| **Pokaż przycisk Otwórz w przycisk Analiza poza trybem edycji** | Dodaje niebieską ikoną Analytics rogu wykresu, aby zezwolić na dostęp jednym kliknięciem.|

Większość z tych ustawień jest dość intuicyjne, aby dowiedzieć się, ale **Eksportuj parametr** warto zbadać skoroszytu korzystającej z tej funkcji.

Jedną z wbudowanych skoroszyty - **ruchu TCP**, zawiera informacje na temat metryki połączeń z maszyny Wirtualnej.

Pierwsza sekcja skoroszyt jest oparty na zapytania o dane dziennika. Druga sekcja również opiera się na zapytania o dane dziennika, ale wybranie wiersza w pierwszej tabeli interaktywnie aktualizuje zawartość wykresy:

![Usługa Azure Monitor dla sekcji skoroszyty maszyn wirtualnych z formantów edycji](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Zachowanie jest możliwe za pośrednictwem **po wybraniu elementu Eksportuj parametr** Zaawansowane ustawienia, które są włączone w tabeli dzienników zapytań.

![Usługa Azure Monitor dla sekcji skoroszyty maszyn wirtualnych z formantów edycji](media/vminsights-workbooks/009-settings-export.png)

Drugie zapytanie dziennika następnie wykorzystuje wartości eksportowanych, gdy zostanie wybrany wiersz, aby utworzyć zbiór wartości, które są następnie używane przez nagłówek sekcji i wykresy. Jeśli żaden wiersz nie jest zaznaczone, ukrywa nagłówek sekcji i wykresy. 

Na przykład parametr ukryty w drugiej sekcji używa następujące odwołanie z wiersza wybranego w siatce:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Dodawanie sekcji metryki

Metryki sekcje zapewniają pełny dostęp, aby dołączyć dane metryk usługi Azure Monitor do interaktywnych raportów. W usłudze Azure Monitor dla maszyn wirtualnych ze wstępnie utworzonych skoroszyty zwykle zawierają, analityczne zapytania o dane, a nie dane metryk.  Może zdecydujesz się utworzyć skoroszyty z danymi metryk, umożliwiając w pełni wykorzystać zalety obu funkcji wszystko w jednym miejscu. Istnieje również możliwość ściągać dane metryk z dowolnej subskrypcji, do których masz dostęp do zasobów.

Oto przykład danych maszyny wirtualnej są pobierane do skoroszytu umożliwia wizualizację siatki wydajność procesora CPU:

![Usługa Azure Monitor dla sekcji skoroszyty maszyn wirtualnych z formantów edycji](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Dodając parametr sekcje

Parametry skoroszytu umożliwiają zmianę wartości w skoroszycie, bez konieczności ręcznego edytowania sekcje zapytania lub tekstu. Eliminuje wymóg konieczności zrozumienie podstawowych języka zapytań usługi analytics i znacznie rozszerza raportowanie oparte na skoroszycie potencjalnych odbiorców.

Wartości parametrów są zastępowane w zapytania, tekst lub inne sekcje parametr, umieszczając nazwę parametru w nawiasach klamrowych, takich jak ``{parameterName}``. Nazwy parametrów są ograniczone do podobnych reguł identyfikatorów języka JavaScript, znaki alfabetu lub podkreślenia, następują znaki alfanumeryczne oraz podkreślenia. Na przykład **a1** jest dozwolony, ale **1a** jest niedozwolone.

Parametry są liniowe, począwszy od góry skoroszytu i przepływają w dół do dalszych krokach.  Parametrów zadeklarowanych w dalszej części skoroszytu można zastąpić parametry, które zostały zgłoszone wcześniej. Umożliwia to również parametry, które umożliwiają dostęp do wartości z parametrów zdefiniowanych wcześniej zapytania. W ramach kroku parametr sam parametry są również liniowego, od lewej do prawej, w której parametry, z prawej strony mogą być zależne od parametru zadeklarowana we wcześniejszej części tego samego kroku.
 
Istnieją cztery typy parametrów, które są obecnie obsługiwane:

|                  |      |
| ---------------- |:-----|
| **Tekst**    | Umożliwia użytkownikom edytowanie pola tekstowego, a opcjonalnie można podać zapytanie, aby podać wartość domyślną. |
| **Lista rozwijana** | Zezwala użytkownikowi na wybranie z zestawu wartości. |
| **Selektor zakresu czasu**| Umożliwia użytkownikowi wybrać ze wstępnie zdefiniowanego zestawu wartości zakresu czasu lub masz do wyboru niestandardowego zakresu czasu.|
| **Wybór zasobów** | Zezwala użytkownikowi na wybranie z zasobów wybranych do skoroszytu.|

### <a name="using-a-text-parameter"></a>Za pomocą parametru tekstu

Wartość użytkownik wpisze w polu tekstowym jest zastępowany bezpośrednio w zapytaniu bez anulowania zapewnianego element i cytowanie. Jeśli wartość potrzebne jest ciągiem, zapytanie powinien mieć parametru w cudzysłowie (takich jak **'{parameter}'**).

Parametr tekst umożliwia wartość w polu tekstowym, aby używać w dowolnym miejscu. Może być nazwa tabeli, nazwa kolumny, nazwa funkcji, operatora, itp.  Typ parametru tekst ma ustawienie **Pobierz wartość domyślną z zapytania analizy**, co pozwala autor skoroszytu korzystać z zapytania, aby wypełnić wartością domyślną dla tego pola tekstowego.

Korzystając z wartością domyślną z zapytania dziennika, tylko pierwsza wartość pierwszego wiersza (wiersz 0, kolumnę 0) jest używany jako wartość domyślną. W związku z tym zalecane jest aby ograniczyć zapytanie w taki sposób, aby zwrócić tylko jeden wiersz i jedną kolumnę. Inne dane zwracane przez zapytanie jest ignorowany. 

Niezależnie od wartości zwracane przez zapytanie zostanie zastąpione bezpośrednio z nie anulowania zapewnianego element lub cytowanie. Jeśli zapytanie nie zwraca żadnych wierszy, wynik parametru jest ciągiem pustym (Jeśli parametr nie jest wymagane) lub niezdefiniowana (Jeśli parametr jest wymagany).

### <a name="using-a-drop-down"></a>Za pomocą listy rozwijanej

Lista rozwijana typ parametru umożliwia tworzenie kontrolkę listy rozwijanej, umożliwiając wybór jednego lub wielu wartości.

Listy rozwijanej jest wypełniana przez zapytanie dziennika lub JSON. Jeżeli zapytanie zwraca jedną kolumnę, wartości w tej kolumnie są zarówno wartość, jak i etykiety w kontrolce listy rozwijanej. Jeżeli zapytanie zwraca dwie kolumny, pierwsza kolumna zawiera wartości, a druga ma etykietę widoczną w polu listy rozwijanej. Jeżeli zapytanie zwraca trzy kolumny, trzecia kolumna służy do wskazania domyślny wybór tej listy rozwijanej. W tej kolumnie mogą być dowolnego typu, ale najprostszych jest użycie bool lub typów numerycznych, gdzie 0 to wartość false, a 1 to wartość true.

Jeśli kolumna ma typ ciągu, wartości null lub być pusty ciąg jest uważany za wartość false, a wszelkie inne wartości jest uważany za spełniony. Do wybrania jednej pozycji list rozwijanych pierwsza wartość z wartością true jest używany jako domyślny wybór.  Dla wielu wybór list rozwijanych wszystkie wartości z wartością true są używane jako zestaw domyślnie wybrana. Elementy na liście rozwijanej są wyświetlane w kolejności, niezależnie od zapytanie zwróciło wierszy. 

Przyjrzyjmy się parametry, które są obecne w raporcie Omówienie połączeń. Kliknij symbol edycji **kierunek**.

![Usługa Azure Monitor dla sekcji skoroszyty maszyn wirtualnych z formantów edycji](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Spowoduje to uruchomienie **Edytuj parametr** elementu menu.

![Usługa Azure Monitor dla sekcji skoroszyty maszyn wirtualnych z formantów edycji](media/vminsights-workbooks/012-workbook-edit-parameter.png)

Za pomocą pliku JSON umożliwia wygenerowanie tabeli dowolnego wypełniony zawartością. Na przykład następujący kod JSON generuje dwie wartości listy rozwijanej:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Przykład bardziej odpowiednie się za pomocą listy rozwijanej wyboru zestaw liczników wydajności według nazwy:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

Zapytanie spowoduje wyświetlenie wyników w następujący sposób:

![Lista rozwijana licznika wydajności](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Listy rozwijane są bardzo zaawansowanych narzędzi do dostosowywania i tworzenia interakcyjnych raportów.

### <a name="time-range-parameters"></a>Parametry zakresu czasu

Można tworzyć własne parametr zakres czasu niestandardowych za pomocą listy rozwijanej Typ parametru, również można użyć typ parametru zakresu czasu out-of-box, jeśli nie potrzebujesz tego samego stopień elastyczności. 

Typy parametrów zakresu czasu ma 15 zakresów dla domyślnych, które bardziej szczegółowo w ciągu pięciu minut do ostatnich 90 dni. Istnieje również opcję, aby umożliwić wybranie niestandardowego zakresu czasu, który umożliwia operatorowi raportu wybierz przycisk start jawnego i Zatrzymaj wartości zakresu czasu.

### <a name="resource-picker"></a>Selektor zasobów

Typ parametru selektor zasobów daje możliwość zakres raportu do określonych typów zasobów. Na przykład wstępnie skoroszytu, który korzysta z zasobów typu selektora **wydajności** skoroszytu.

![Listę rozwijaną obszarów roboczych](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Zapisywanie i udostępnianie skoroszytów swojemu zespołowi

Skoroszyty są zapisywane w obrębie obszaru roboczego usługi Log Analytics lub zasób maszynę wirtualną w zależności od tego, jak dostęp do galerii skoroszytów. Można zapisać skoroszyt **Moje raporty** sekcja, która jest prywatny, w przypadku lub **udostępnione raporty** sekcja, która jest dostępny dla wszystkich użytkowników z dostępem do zasobów. Aby wyświetlić wszystkie skoroszyty w zasobie, kliknij **Otwórz** przycisk na pasku akcji.

Aby udostępnić skoroszytu, w którym znajduje się obecnie w **Moje raporty**:

1. Kliknij przycisk **Otwórz** na pasku akcji
2. Kliknij przycisk "..." obok skoroszytu, który chcesz udostępnić
3. Kliknij przycisk **Przenieś do raportów udostępnionych**.

Aby udostępnić skoroszyt za pomocą łącza lub za pośrednictwem poczty e-mail, kliknij przycisk **udostępnianie** na pasku akcji. Należy pamiętać, że adresaci łącza muszą mieć dostęp do tego zasobu w witrynie Azure portal, aby wyświetlić skoroszyt. Aby dokonać edycji, adresaci muszą dysponować co najmniej uprawnienia współautora dla zasobu.

Aby przypiąć łącze do skoroszytu na pulpicie nawigacyjnym platformy Azure:

1. Kliknij przycisk **Otwórz** na pasku akcji
2. Kliknij przycisk "..." obok skoroszytu, który chcesz przypiąć
3. Kliknij pozycję **Przypnij do pulpitu nawigacyjnego**.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, jak korzystać z funkcji health, zobacz [Wyświetl kondycję maszyn wirtualnych platformy Azure](vminsights-health.md), lub aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych mapy](vminsights-maps.md). 