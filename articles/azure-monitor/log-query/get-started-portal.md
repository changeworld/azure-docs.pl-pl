---
title: Wprowadzenie do Azure Monitor Log Analytics | Microsoft Docs
description: Ten artykuł zawiera samouczek dotyczący używania Log Analytics w Azure Portal do zapisywania zapytań.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: bwren
ms.openlocfilehash: 950768326228960192f48d99e5c5fa849b2c2bda
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076822"
---
# <a name="get-started-with-log-analytics-in-azure-monitor"></a>Wprowadzenie do Log Analytics w Azure Monitor

> [!NOTE]
> Można to zrobić w Twoim środowisku, jeśli zbierasz dane z co najmniej jednej maszyny wirtualnej. Jeśli nie, użyj naszego [środowiska demonstracyjnego](https://portal.loganalytics.io/demo), co obejmuje wiele przykładowych danych.

W tym samouczku dowiesz się, jak używać Log Analytics w Azure Portal do pisania zapytań dzienników Azure Monitor. Pouczysz się, jak:

- Użyj Log Analytics, aby napisać proste zapytanie
- Zrozumienie schematu danych
- Filtrowanie, sortowanie i grupowanie wyników
- Zastosuj zakres czasu
- Tworzenie wykresów
- Zapisz i Załaduj zapytania
- Eksportowanie i udostępnianie zapytań

Aby zapoznać się z samouczkiem dotyczącym pisania zapytań dzienników, zobacz [Rozpoczynanie pracy z dziennikami w Azure monitor](get-started-queries.md).<br>
Aby uzyskać więcej informacji o zapytaniach dziennika, zobacz [Omówienie zapytań dzienników w Azure monitor](log-query-overview.md).

## <a name="meet-log-analytics"></a>Poznaj Log Analytics
Log Analytics jest narzędziem sieci Web służącym do zapisywania i wykonywania zapytań dzienników Azure Monitor. Otwórz go, wybierając pozycję **dzienniki** w menu Azure monitor. Rozpoczyna się od nowego pustego zapytania.

![Strona główna](media/get-started-portal/homepage.png)

## <a name="firewall-requirements"></a>Wymagania dotyczące zapory
Aby można było używać Log Analytics, przeglądarka wymaga dostępu do następujących adresów. Jeśli przeglądarka uzyskuje dostęp do Azure Portal przez zaporę, należy włączyć dostęp do tych adresów.

| Identyfikator URI | IP | Porty |
|:---|:---|:---|
| portal.loganalytics.io | Dynamiczne | 80,443 |
| api.loganalytics.io | Dynamiczne | 80,443 |
| docs.loganalytics.io | Dynamiczne | 80,443 |

## <a name="basic-queries"></a>Zapytania podstawowe
Zapytania mogą służyć do wyszukiwania terminów, identyfikowania trendów, analizowania wzorców i udostępniania wielu szczegółowych informacji na podstawie danych. Zacznij od podstawowego zapytania:

```Kusto
Event | search "error"
```

To zapytanie wyszukuje w tabeli _zdarzeń_ rekordy zawierające _błąd_ terminu w dowolnej właściwości.

Zapytania mogą rozpoczynać się od nazwy tabeli lub polecenia [wyszukiwania](/azure/kusto/query/searchoperator) . Powyższy przykład rozpoczyna się od _zdarzenia_nazwa tabeli, które pobiera wszystkie rekordy z tabeli zdarzeń. Znak potoku (|) oddziela polecenia, aby dane wyjściowe pierwszego z nich służyły jako dane wejściowe następującego polecenia. Do pojedynczego zapytania można dodać dowolną liczbę poleceń.

Innym sposobem zapisu tego samego zapytania będzie:

```Kusto
search in (Event) "error"
```

W tym przykładzie **Wyszukiwanie** jest objęte zakresem tabeli _zdarzeń_ , a wszystkie rekordy w tej tabeli są przeszukiwane pod kątem _błędu_terminu.

## <a name="running-a-query"></a>Uruchamianie zapytania
Uruchom zapytanie, klikając przycisk **Run (Uruchom** ) lub naciskając **klawisze SHIFT + ENTER**. Należy wziąć pod uwagę następujące szczegóły, które określają kod, który zostanie uruchomiony i zwrócone dane:

- Podziały wierszy: Pojedynczy podział sprawia, że zapytanie jest łatwiejsze do odczytania. Wiele podziałów wierszy dzieli je na osobne zapytania.
- Biera Umieść kursor w miejscu wewnątrz zapytania, aby go wykonać. Bieżące zapytanie jest uznawane za kod w górę do momentu znalezienia pustego wiersza.
- Zakres czasu — domyślnie jest ustawiony zakres czasu z _ostatnich 24 godzin_ . Aby użyć innego zakresu, użyj selektora czasu lub Dodaj jawny filtr zakresu czasu do zapytania.


## <a name="understand-the-schema"></a>Informacje o schemacie
Schemat jest kolekcją tabel zgrupowanych wizualnie w ramach kategorii logicznej. Niektóre kategorie pochodzą z rozwiązań monitorowania. Kategoria _LogManagement_ zawiera typowe dane, takie jak zdarzenia systemu Windows i dziennika systemowego, dane wydajności i pulsy agentów.

![Schemat](media/get-started-portal/schema.png)

W każdej tabeli dane są zorganizowane w kolumnach z różnymi typami danych, wskazanymi przez ikony obok nazwy kolumny. Na przykład _zdarzeń_ tabeli pokazano na zrzucie ekranu zawiera kolumny, takie jak _komputera_ czyli tekstu, _EventCategory_ która jest liczbą, i _TimeGenerated_ czyli daty/godziny.

## <a name="filter-the-results"></a>Filtrowanie wyników
Zacznij od pobrania wszystkiego w tabeli _zdarzeń_ .

```Kusto
Event
```

Log Analytics automatycznie zakresy wyników według:

- Zakres czasu:  Domyślnie zapytania są ograniczone do ostatnich 24 godzin.
- Liczba wyników: Wyniki są ograniczone do maksymalnie 10 000 rekordów.

To zapytanie jest bardzo ogólne i zwraca zbyt wiele wyników, aby być przydatne. Wyniki można filtrować przez elementy tabeli lub przez jawne dodanie filtru do zapytania. Filtrowanie wyników przez elementy tabeli ma zastosowanie do istniejącego zestawu wyników, podczas gdy filtr do samego zapytania zwróci nowy zestaw wyników filtrowanych i w związku z tym może generować dokładniejsze wyniki.

### <a name="add-a-filter-to-the-query"></a>Dodawanie filtru do zapytania
Obok każdego rekordu znajduje się strzałka z lewej strony. Strzałki można otworzyć szczegóły dla określonego rekordu.

Umieść kursor nad nazwą kolumny ikon "+" i "-", aby wyświetlić. Aby dodać filtr, który zwróci tylko rekordy o tej samej wartości, kliknij znak "+". Kliknij przycisk "-", aby wykluczyć rekordy z tą wartością, a następnie kliknij przycisk **Uruchom** , aby ponownie uruchomić zapytanie.

![Dodaj filtr do zapytania](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Filtruj elementy tabeli
Teraz skupmy się na zdarzeniach o ważności _błędu_. Ta wartość jest określona w kolumnie o nazwie _EventLevelName_. Musisz przewinąć w prawo, aby wyświetlić tę kolumnę.

Kliknij ikonę filtru obok tytułu kolumny, a w oknie podręcznym wybierz wartości, które zaczynają się od _błędu_tekstu:

![Filtr](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Sortuj i Grupuj wyniki
Wyniki są teraz zawężane w celu uwzględnienia tylko zdarzeń błędów z SQL Server utworzonych w ciągu ostatnich 24 godzin. Jednak wyniki nie są sortowane w żaden sposób. Aby posortować wyniki według określonej kolumny, takiej jak _sygnatura czasowa_ na przykład, kliknij tytuł kolumny. Jednym kliknięciem sortuje się w kolejności rosnącej, a drugie kliknięcie sortuje malejąco.

![Sortuj kolumnę](media/get-started-portal/sort-column.png)

Inny sposób organizowania wyników odbywa się według grup. Aby pogrupować wyniki według określonej kolumny, wystarczy przeciągnąć Nagłówek kolumny powyżej innych kolumn. Aby utworzyć podgrupy, przeciągnij pozostałe kolumny na górnym pasku.

![Grupy](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Wybierz kolumny do wyświetlenia
Tabela wyników często zawiera wiele kolumn. Może się okazać, że niektóre z zwracanych kolumn nie są wyświetlane domyślnie lub można usunąć niektóre z wyświetlanych kolumn. Aby wybrać kolumny do wyświetlenia, kliknij przycisk kolumny:

![Wybieranie kolumn](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Wybierz zakres czasu
Domyślnie Log Analytics stosuje zakres czasu w _ostatnich 24 godzinach_ . Aby użyć innego zakresu, wybierz inną wartość za pomocą selektora godziny, a następnie kliknij przycisk **Uruchom**. Oprócz wartości wstępnie ustawionych można użyć opcji _niestandardowy zakres czasu_ , aby wybrać zakres bezwzględny dla zapytania.

![Wybór godziny](media/get-started-portal/time-picker.png)

W przypadku wybrania niestandardowego zakresu czasu wybrane wartości są w formacie UTC, który może różnić się od lokalnej strefy czasowej.

Jeśli zapytanie jawnie zawiera filtr dla _TimeGenerated_, tytuł selektora czas będzie wyświetlany _w zapytaniu_. Wybór ręczny zostanie wyłączony w celu uniknięcia konfliktu.


## <a name="charts"></a>Wykresy
Oprócz zwracania wyników w tabeli wyniki zapytania mogą być prezentowane w formatach wizualnych. Użyj poniższego zapytania jako przykładu:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Domyślnie wyniki są wyświetlane w tabeli. Kliknij _Wykres_ , aby wyświetlić wyniki w widoku graficznym:

![Wykres słupkowy](media/get-started-portal/bar-chart.png)

Wyniki są wyświetlane na skumulowanym wykresie słupkowym. Kliknij pozycję _skumulowany kolumnowy_ i wybierz pozycję _kołowy_ , aby wyświetlić inny widok wyników:

![Wykres kołowy](media/get-started-portal/pie-chart.png)

Różne właściwości widoku, takie jak osie x i y lub Preferencje grupowania i dzielenia, można zmienić ręcznie z poziomu paska sterowania.

Możesz również ustawić preferowany widok w samej kwerendzie przy użyciu operatora renderowania.

### <a name="smart-diagnostics"></a>Inteligentna diagnostyka
W timechart, jeśli w danych wystąpi nagły skok lub krok, w wierszu może zostać wyświetlony wyróżniony punkt. Oznacza to, że _Inteligentna diagnostyka_ zidentyfikował kombinację właściwości, które odfiltrują nagłą zmianę. Kliknij punkt, aby uzyskać więcej szczegółów na temat filtru i wyświetlić przefiltrowaną wersję. Może to pomóc w ustaleniu, co spowodowało zmianę:

![Inteligentna diagnostyka](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Przypnij do pulpitu nawigacyjnego
Aby przypiąć diagram lub tabelę do jednego z udostępnionych pulpitów nawigacyjnych platformy Azure, kliknij ikonę pinezki. Należy zauważyć, że ta ikona została przeniesiona na górę okna Log Analytics inną niż Poniższy zrzut ekranu.

![Przypnij do pulpitu nawigacyjnego](media/get-started-portal/pin-dashboard.png)

Niektóre uproszczenia są stosowane do wykresu po przypięciu do pulpitu nawigacyjnego:

- Kolumny i wiersze tabeli: Aby można było przypiąć tabelę do pulpitu nawigacyjnego, musi ona mieć co najmniej cztery kolumny. Wyświetlane są tylko pierwsze siedem wierszy.
- Ograniczenie czasu: Zapytania są automatycznie ograniczone do ostatnich 14 dni.
- Ograniczenie liczby bin: Jeśli zostanie wyświetlony wykres, który zawiera wiele dyskretnych pojemników, mniej wypełnione pojemniki są automatycznie pogrupowane w jeden zasobnik.

## <a name="save-queries"></a>Zapisywanie zapytań
Po utworzeniu przydatnego zapytania warto je zapisać lub udostępnić innym osobom. Ikona **Zapisz** znajduje się na górnym pasku.

Można zapisać całą stronę zapytania lub pojedyncze zapytanie jako funkcję. Funkcje to kwerendy, do których można odwoływać się inne zapytania. Aby zapisać zapytanie jako funkcję, należy podać alias funkcji, który jest nazwą używaną do wywołania tego zapytania, w odniesieniu do innych zapytań.

![Funkcja Save](media/get-started-portal/save-function.png)

>[!NOTE]
>Następujące znaki są obsługiwane — `a–z, A–Z, 0-9, -, _, ., <space>, (, ), |` w polu **Nazwa** podczas zapisywania lub edytowania zapisanego zapytania.

Zapytania Log Analytics są zawsze zapisywane w wybranym obszarze roboczym i udostępniane innym użytkownikom tego obszaru roboczego.

## <a name="load-queries"></a>Załaduj zapytania
Ikona Eksplorator zapytań znajduje się w prawym górnym rogu. Ta lista zawiera wszystkie zapisane zapytania według kategorii. Umożliwia również oznaczenie określonych zapytań jako ulubionych, aby szybko je znaleźć w przyszłości. Kliknij dwukrotnie zapisane zapytanie, aby dodać je do bieżącego okna.

![Eksplorator zapytań](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Eksportuj i Udostępnij jako link
Log Analytics obsługuje kilka metod eksportowania:

- Excel: Zapisz wyniki jako plik CSV.
- Power BI: Wyeksportuj wyniki do Power BI. Aby uzyskać szczegółowe informacje, zobacz [importowanie Azure monitor danych dziennika do Power BI](../../azure-monitor/platform/powerbi.md) .
- Udostępnianie linku: Zapytanie może być udostępniane jako link, który można następnie wysłać i wykonać przez innych użytkowników, którzy mają dostęp do tego samego obszaru roboczego.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [pisania zapytań dziennika Azure monitor](get-started-queries.md).
