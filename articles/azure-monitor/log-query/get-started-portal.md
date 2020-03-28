---
title: 'Samouczek: Wprowadzenie do zapytań usługi Log Analytics'
description: Dowiedz się z tego samouczka, jak pisać zapytania dziennika usługi Azure Monitor i zarządzać nimi przy użyciu usługi Log Analytics w witrynie Azure portal.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 29e24166218a6757cded9d1b002321800ab0c073
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80055472"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Samouczek: Wprowadzenie do zapytań usługi Log Analytics

W tym samouczku pokazano, jak używać usługi Log Analytics do pisania, wykonywania i zarządzania zapytaniami dziennika usługi Azure Monitor w witrynie Azure portal. Za pomocą zapytań usługi Log Analytics można wyszukiwać terminy, identyfikować trendy, analizować wzorce i dostarczać wiele innych szczegółowych informacji z danych. 

W tym samouczku dowiesz się, jak używać usługi Log Analytics do:

> [!div class="checklist"]
> * Opis schematu danych dziennika
> * Pisanie i uruchamianie prostych zapytań oraz modyfikowanie zakresu czasu dla kwerend
> * Filtrowanie, sortowanie i grupowanie wyników kwerendy
> * Wyświetlanie, modyfikowanie i udostępnianie wizualizacji wyników kwerendy
> * Zapisywanie, ładowanie, eksportowanie i kopiowanie zapytań i wyników

Aby uzyskać więcej informacji na temat zapytań dziennika, zobacz [Omówienie zapytań dziennika w usłudze Azure Monitor](log-query-overview.md).<br/>
Aby uzyskać szczegółowy samouczek dotyczący pisania zapytań dziennika, zobacz [Wprowadzenie do zapytań dziennika w usłudze Azure Monitor](get-started-queries.md).

## <a name="open-log-analytics"></a>Otwórz analizę dzienników
Aby korzystać z usługi Log Analytics, musisz zalogować się do konta platformy Azure. Jeśli nie masz konta platformy Azure, [utwórz je bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

Aby wykonać większość kroków w tym samouczku, można użyć [tego środowiska demo,](https://portal.loganalytics.io/demo)który zawiera wiele przykładowych danych. W środowisku demonstracyjnym nie będzie można zapisywać zapytań ani przypinać wyników do pulpitu nawigacyjnego.

Możesz również użyć własnego środowiska, jeśli używasz usługi Azure Monitor do zbierania danych dziennika na co najmniej jednym zasobie platformy Azure. Aby otworzyć obszar roboczy usługi Log Analytics, w lewej nawigacji usługi Azure Monitor wybierz pozycję **Dzienniki**. 

## <a name="understand-the-schema"></a>Informacje o schemacie
*Schemat* jest zbiorem tabel zgrupowanych w kategoriach logicznych. Schemat demo ma kilka kategorii z rozwiązań monitorowania. Na przykład **LogManagement** kategoria zawiera zdarzenia systemu Windows i Syslog, dane dotyczące wydajności i pulsu agenta.

Tabele schematów są wyświetlane na karcie **Tabele** w obszarze roboczym usługi Log Analytics. Tabele zawierają kolumny, z których każda ma typ danych wyświetlany przez ikonę obok nazwy kolumny. Na przykład tabela **Zdarzenie** zawiera kolumny tekstowe, takie jak **Komputer** i kolumny numeryczne, takie jak **Kategoria zdarzeń**.

![Schemat](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Pisanie i uruchamianie podstawowych zapytań

Usługa Log Analytics zostanie otwarta z nową pustą kwerendą w **edytorze zapytań**.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Pisanie kwerendy
Kwerendy dziennika usługi Azure Monitor używają wersji języka zapytań Kusto. Kwerendy można rozpocząć od nazwy tabeli lub polecenia [wyszukiwania.](/azure/kusto/query/searchoperator) 

Następująca kwerenda pobiera wszystkie rekordy z tabeli **Zdarzenia:**

```Kusto
Event
```

Znak potoku (|) oddziela polecenia, więc dane wyjściowe pierwszego polecenia są wejściem następnego polecenia. Do pojedynczej kwerendy można dodać dowolną liczbę poleceń. Następująca kwerenda pobiera rekordy z tabeli **Zdarzenia,** a następnie wyszukuje je pod kątem **błędu** terminu w dowolnej właściwości:

```Kusto
Event 
| search "error"
```

Podział pojedynczego wiersza ułatwia odczytanie zapytań. Więcej niż jeden podział wiersza dzieli kwerendę na oddzielne kwerendy.

Innym sposobem na napisanie tej samej kwerendy jest:

```Kusto
search in (Event) "error"
```

W drugim przykładzie polecenie **wyszukiwania** wyszukuje tylko rekordy w tabeli **Zdarzenia** pod kątem **błędu**terminu .

Domyślnie usługa Log Analytics ogranicza zapytania do zakresu czasu z ostatnich 24 godzin. Aby ustawić inny zakres czasu, można dodać jawny filtr **TimeGenerated** do kwerendy lub użyć formantu **Zakres czasu.**

### <a name="use-the-time-range-control"></a>Użyj kontroli zakresu czasu
Aby użyć formantu **Zakres czasu,** zaznacz ją na górnym pasku, a następnie wybierz wartość z listy rozwijanej lub wybierz **pozycję Niestandardowe,** aby utworzyć niestandardowy zakres czasu.

![Selektor czasu](media/get-started-portal/time-picker.png)

- Wartości zakresu czasu są w czasie UTC, który może być inny niż lokalna strefa czasowa.
- Jeśli kwerenda jawnie ustawia filtr dla **TimeGenerated**, formant selektora czasu pokazuje **Ustaw w kwerendzie**i jest wyłączony, aby zapobiec konfliktowi.

### <a name="run-a-query"></a>Uruchamianie kwerendy
Aby uruchomić kwerendę, umieść kursor w miejscu kwerendy, a następnie wybierz pozycję **Uruchom** na górnym pasku lub naciśnij klawisz **Shift**+**Enter**. Kwerenda jest uruchamiana, dopóki nie znajdzie pustego wiersza.

## <a name="filter-results"></a>Filtrowanie wyników
Usługa Log Analytics ogranicza wyniki do maksymalnie 10 000 rekordów. Ogólne zapytanie, takie jak `Event` zwraca zbyt wiele wyników, aby były przydatne. Można filtrować wyniki kwerendy poprzez ograniczenie elementów tabeli w kwerendzie lub jawnie dodając filtr do wyników. Filtrowanie elementów tabeli zwraca nowy zestaw wyników, podczas gdy filtr jawny ma zastosowanie do istniejącego zestawu wyników.

### <a name="filter-by-restricting-table-elements"></a>Filtrowanie przez ograniczenie elementów tabeli
Aby `Event` filtrować wyniki kwerendy do zdarzeń **błędu,** ograniczając elementy tabeli w kwerendzie:

1. W wynikach kwerendy wybierz strzałkę listy rozwijanej obok dowolnego rekordu, który zawiera **błąd** w kolumnie **EventLevelName.** 
   
1. W rozwiniętych szczegółach umieść wskaźnik myszy na ... **obok** **pozycji EventLevelName**, a następnie wybierz pozycję **Dołącz "Błąd".** 
   
   ![Dodawanie filtru do kwerendy](media/get-started-portal/add-filter.png)
   
1. Należy zauważyć, że kwerenda w **edytorze kwerend** została zmieniona na:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Wybierz **pozycję Uruchom,** aby uruchomić nową kwerendę.

### <a name="filter-by-explicitly-filtering-results"></a>Filtrowanie przez jawne filtrowanie wyników
Aby filtrować `Event` wyniki kwerendy do **zdarzeń błędu,** filtrując wyniki kwerendy:

1. W wynikach kwerendy wybierz ikonę **Filtr obok** nagłówka kolumny **EventLevelName**. 
   
1. W pierwszym polu okna podręcznego wybierz pozycję **Jest równa**, a w następnym polu wprowadź *błąd*. 
   
1. Wybierz **filtr**.
   
   ![Filtr](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Sortowanie, grupowanie i zaznaczanie kolumn
Aby posortować wyniki kwerendy według określonej kolumny, takiej jak **TimeGenerated [UTC],** wybierz nagłówek kolumny. Ponownie wybierz nagłówek, aby przełączać się między kolejnością rosnącą a malejącą.

![Kolumna Sortowanie](media/get-started-portal/sort-column.png)

Innym sposobem organizowania wyników jest grupa. Aby zgrupować wyniki według określonej kolumny, przeciągnij nagłówek kolumny na pasek nad tabelą wyników z **etykietą Przeciągnij nagłówek kolumny i upuść go tutaj, aby pogrupować według tej kolumny**. Aby utworzyć podgrupy, przeciągnij inne kolumny na górny pasek. Można zmienić kolejność hierarchii i sortowanie grup i podgrup na pasku.

![Grupy](media/get-started-portal/groups.png)

Aby ukryć lub wyświetlić kolumny w **wynikach,** wybierz pozycję Kolumny nad tabelą, a następnie zaznacz lub usuń zaznaczenie kolumn z listy rozwijanej.

![Wybieranie kolumn](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Wyświetlanie i modyfikowanie wykresów
Wyniki kwerend można również wyświetlać w formatach wizualnych. Wprowadź na przykład następującą kwerendę:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Domyślnie wyniki są wyświetlane w tabeli. Wybierz **wykres** nad tabelą, aby zobaczyć wyniki w widoku graficznym.

![Wykres słupkowy](media/get-started-portal/bar-chart.png)

Wyniki są wyświetlane na skumulowanym wykresie słupkowym. Wybierz inne opcje, takie jak **Skumulowana kolumna** lub **Ciasto,** aby wyświetlić inne widoki wyników.

![Wykres kołowy](media/get-started-portal/pie-chart.png)

Można ręcznie zmienić właściwości widoku, takie jak osie x i y, lub grupowanie i dzielenie preferencji, ręcznie z paska sterowania.

Można również ustawić widok preferowany w samej kwerendzie, używając operatora [renderowania.](/azure/kusto/query/renderoperator)

## <a name="pin-results-to-a-dashboard"></a>Przypinanie wyników do pulpitu nawigacyjnego
Aby przypiąć tabelę wyników lub wykres z usługi Log Analytics do udostępnionego pulpitu nawigacyjnego platformy Azure, wybierz **pozycję Przypnij do pulpitu nawigacyjnego** na górnym pasku. 

![Przypnij do pulpitu nawigacyjnego](media/get-started-portal/pin-dashboard.png)

W okienku **Przypnij do innego pulpitu nawigacyjnego** zaznacz lub utwórz udostępniony pulpit nawigacyjny, do którym chcesz przypiąć, a następnie wybierz pozycję **Zastosuj**. Tabela lub wykres są wyświetlane na wybranym pulpicie nawigacyjnym platformy Azure.

![Wykres przypięty do pulpitu nawigacyjnego](media/get-started-portal/pin-dashboard2.png)

Tabela lub wykres przypinany do udostępnionego pulpitu nawigacyjnego ma następujące uproszczenia: 

- Dane są ograniczone do ostatnich 14 dni.
- Tabela zawiera tylko maksymalnie cztery kolumny i siedem pierwszych wierszy.
- Wykresy z wieloma osobnymi kategoriami automatycznie grupują mniej zaludnione kategorie w jeden **pojemnik.**

## <a name="save-load-or-export-queries"></a>Zapisywanie, ładowanie lub eksportowanie kwerend
Po utworzeniu kwerendy można zapisać lub udostępnić kwerendę lub wyniki innym osobom. 

### <a name="save-queries"></a>Zapisywanie zapytań
Aby zapisać kwerendę:

1. Wybierz **pozycję Zapisz** na górnym pasku.
   
1. W oknie dialogowym **Zapisz** nadaj kwerendzie **nazwę**, używając znaków a–z, A–Z, 0-9, spacji, łącznika, podkreślenia, kropki, nawiasu lub potoku. 
   
1. Wybierz, czy kwerenda ma być zapisywana jako **kwerenda,** czy **funkcja**. Funkcje są kwerendy, które inne kwerendy mogą odwoływać. 
   
   Aby zapisać kwerendę jako funkcję, podaj **alias funkcji**, który jest krótką nazwą dla innych kwerend do wywołania tej kwerendy.
   
1. Podaj **kategorię** dla **Eksploratora kwerendy** do użycia dla kwerendy.
   
1. Wybierz **pozycję Zapisz**.
   
   ![Zapisz, funkcja](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Ładowanie kwerend
Aby załadować zapisaną kwerendę, wybierz **Eksplorator kwerend** w prawym górnym rogu. Zostanie otwarte okienko **Eksploratora kwerend** z listą wszystkich zapytań według kategorii. Rozwiń kategorie lub wprowadź nazwę kwerendy na pasku wyszukiwania, a następnie wybierz kwerendę, aby załadować ją do **edytora zapytań**. Kwerendę można oznaczyć jako **ulubioną,** wybierając gwiazdkę obok nazwy kwerendy.

![Eksplorator zapytań](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Eksportowanie i udostępnianie zapytań
Aby wyeksportować kwerendę, wybierz pozycję **Eksportuj** na górnym pasku, a następnie wybierz pozycję **Eksportuj do PLIKU CSV — wszystkie kolumny**, **Eksportuj do PLIKU CSV — wyświetlane kolumny**lub **Eksportuj do usługi Power BI (kwerenda M)** z listy rozwijanej.

Aby udostępnić łącze do kwerendy, wybierz pozycję **Kopiuj łącze** na górnym pasku, a następnie wybierz pozycję **Kopiuj łącze do kwerendy,** **kopiuj tekst kwerendy**lub **Kopiuj wyniki kwerendy,** aby skopiować je do schowka. Łącze zapytania można wysłać do innych osób, które mają dostęp do tego samego obszaru roboczego.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego samouczka, aby dowiedzieć się więcej o pisaniu zapytań dziennika usługi Azure Monitor.
> [!div class="nextstepaction"]
> [Pisanie zapytań dziennika usługi Azure Monitor](get-started-queries.md)
