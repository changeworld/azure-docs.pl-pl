---
title: Wizualizacje skoroszytu monitora platformy Azure
description: Dowiedz się więcej o wszystkich składnikach wizualizacji skoroszytu usługi Azure Monitor, w tym — tekstach, wykresach, siatkach, drzewach i wykresach.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658034"
---
# <a name="azure-monitor-workbook-visualizations"></a>Wizualizacje skoroszytu monitora platformy Azure

Skoroszyty usługi Azure Monitor obsługują wiele różnych stylów wizualizacji, aby spełnić twoje potrzeby w zakresie raportowania. W tym artykule przedstawiono przykłady każdego typu wizualizacji.

## <a name="text"></a>Tekst

Skoroszyty umożliwiają autorom dołączanie bloków tekstowych do skoroszytów. Tekst może być ludzką analizą telemetrii, informacjami ułatwiających użytkownikom interpretację danych, nagłówków sekcji itp.

![Zrzut ekranu przedstawiający tabelę tekstu Apdex](./media/workbooks-visualizations/apdex.png)

Tekst jest dodawany za pomocą formantu Markdown, który zapewnia pełną kontrolę formatowania.

![Zrzut ekranu przedstawiający nieprzetworzone oznaczanie, które tworzy renderowane tabele](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Dodawanie formantu tekstowego

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi **Edycja.**
2. Użyj łącza **Dodaj tekst,** aby dodać kontrolkę tekstową do skoroszytu.
3. Dodaj Markdown do formantu.
4. Kliknij przycisk **Gotowe edytowanie,** aby wyświetlić sformatowany tekst.

> [!TIP]
> Użyj tego [arkusza markdown,](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) aby dowiedzieć się o różnych opcji formatowania.

## <a name="charts"></a>Wykresy

Skoroszyty umożliwiają prezentowanie danych monitorowania jako wykresów. Obsługiwane typy wykresów obejmują linię, pasek, pasek kategoryczny, obszar, wykresy punktowe, ciasto i czas. Autorzy mogą dostosować wysokość, szerokość, paletę kolorów, legendę, tytuły, komunikat bez danych itp.

Skoroszyty obsługują wykresy zarówno dla dzienników, jak i źródeł danych metryk. 

### <a name="adding-a-log-chart"></a>Dodawanie wykresu dziennika

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi **Edycja.**
2. Użyj łącza **Dodaj kwerendę,** aby dodać kontrolkę kwerendy dziennika do skoroszytu.
3. Wybierz typ kwerendy jako **Dziennik,** typ zasobu (na przykład Usługa Application Insights) i zasoby docelowe.
4. Użyj edytora zapytań, aby wprowadzić [KQL](https://docs.microsoft.com/azure/kusto/query/) do analizy (na przykład trend żądań).
5. Ustaw wizualizację na jedną z: **Obszar**, **Bar**, **Bar (kategoryczny)**, **Linia**, **Pie**, **Scatter**, lub **Czas**.
6. W razie potrzeby ustaw inne parametry , takie jak zakres czasu, wizualizacja, rozmiar, paleta kolorów i legenda.

![Zrzut ekranu przedstawiający wykres dziennika w trybie edycji](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Rejestrowanie parametrów wykresu

| Parametr | Wyjaśnienie | Przykład |
| ------------- |:-------------|:-------------|
| `Query Type` | Typ kwerendy do użycia | Dziennik, wykres zasobów platformy Azure itp. |
| `Resource Type` | Typ zasobu docelowego | Usługa Application Insights, usługa Log Analytics lub azure-first |
| `Resources` | Zestaw zasobów, aby uzyskać wartość metryki z | MyApp1 (MyApp1) |
| `Time Range` | Okno czasu wyświetlania wykresu dziennika | Ostatnia godzina, Ostatnie 24 godziny itp. |
| `Visualization` | Wizualizacja do użycia | Obszar, Bar, Linia, Pie, Scatter, Czas, bar kategoryczny |
| `Size` | Pionowy rozmiar | Małe, średnie, duże lub pełne |
| `Color palette` | Paleta kolorów do użycia na wykresie. Ignorowane w trybie wielometrycznym lub segmentowym. | Niebieski, zielony, czerwony itp. |
| `Legend` | Funkcja agregacji używana dla legendy | Suma lub średnia wartości lub Maks., Min, Pierwsza, Ostatnia wartość |
| `Query` | Dowolna kwerenda KQL zwracająca dane w formacie oczekiwanym przez wizualizację wykresu | _żądania \| make-series Żądania = count() default = 0 na znacznik czasu z ago(1d) do now() krok 1h_ |

### <a name="adding-a-metric-chart"></a>Dodawanie wykresu metrycznego

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi **Edycja.**
2. Użyj **łącza Dodaj metrykę,** aby dodać kontrolkę metryki do skoroszytu.
3. Wybierz typ zasobu (na przykład konto magazynu), zasoby docelowe, obszar nazw metryki i nazwę oraz agregację do użycia.
4. W razie potrzeby ustaw inne parametry , takie jak zakres czasu, podział według, wizualizacja, rozmiar i paleta kolorów.

![Zrzut ekranu przedstawiający wykres metryczny w trybie edycji](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Parametry wykresu metrycznego

| Parametr | Wyjaśnienie | Przykład |
| ------------- |:-------------|:-------------|
| `Resource Type` | Typ zasobu docelowego | Pamięci masowej lub maszyny wirtualnej. |
| `Resources` | Zestaw zasobów, aby uzyskać wartość metryki z | MyStorage1 |
| `Namespace` | Obszar nazw z metryką | > magazynowania |
| `Metric` | Metryka do wizualizacji | Transakcje > obiektów blob > magazynu |
| `Aggregation` | Funkcja agregacji stosowana do metryki | Suma, liczba, średnia itp. |
| `Time Range` | Przedział czasu wyświetlania danych w | Ostatnia godzina, Ostatnie 24 godziny itp. |
| `Visualization` | Wizualizacja do użycia | Obszar, Bar, Linia, Punktowy, Siatka |
| `Split By` | Opcjonalnie podziel metrykę w wymiarze | Transakcje według typu geograficznego |
| `Size` | Pionowy rozmiar | Małe, średnie lub duże |
| `Color palette` | Paleta kolorów do użycia na wykresie. Ignorowane, `Split by` jeśli parametr jest używany | Niebieski, zielony, czerwony itp. |

## <a name="grids"></a>Siatki

Siatki lub tabele są typowym sposobem prezentowania danych użytkownikom. Skoroszyty umożliwiają użytkownikom indywidualne stylowanie kolumn siatki w celu zapewnienia zaawansowanego interfejsu użytkownika dla swoich raportów.

W poniższym przykładzie przedstawiono siatkę, która łączy ikony, mapy cieplne i paski iskier w celu przedstawienia złożonych informacji. Skoroszyt zapewnia również sortowanie, pole wyszukiwania i przycisk przejdź do analizy.

![Zrzut ekranu przedstawiający siatkę opartą na dzienniku](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Dodawanie siatki opartej na dzienniku

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi **Edycja.**
2. Użyj łącza **Dodaj kwerendę,** aby dodać kontrolkę kwerendy dziennika do skoroszytu.
3. Wybierz typ kwerendy jako **Dziennik,** typ zasobu (na przykład Usługa Application Insights) i zasoby docelowe.
4. Użyj edytora zapytań, aby wprowadzić KQL do analizy (na przykład maszyny wirtualne z pamięcią poniżej progu)
5. Ustawianie wizualizacji na **Siatka**
6. W razie potrzeby ustaw inne parametry , takie jak zakres czasu, rozmiar, paleta kolorów i legenda.

![Zrzut ekranu przedstawiający kwerendę siatki opartą na dzienniku](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Kafelki

Kafelki są bardzo przydatnym sposobem prezentowania danych podsumowania w skoroszytach. Na poniższej ilustracji przedstawiono typowy przypadek użycia kafelków — podsumowanie poziomu aplikacji na wierzchu szczegółowej siatki.

![Zrzut ekranu przedstawiający widok podsumowania kafelków](./media/workbooks-visualizations/tiles-summary.png)

Kafelki skoroszytu obsługują tytuł, podtytuł, duży tekst, ikony, gradienty oparte na metrykach, linię/paski iskrowe, stopkę itp.

### <a name="adding-a-tile"></a>Dodawanie kafelka

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edycja._
2. Użyj łącza **Dodaj kwerendę,** aby dodać kontrolkę kwerendy dziennika do skoroszytu. 
3. Wybierz typ kwerendy jako **Dziennik,** typ zasobu (na przykład Usługa Application Insights) i zasoby docelowe.
4. Wprowadź KQL do analizy za pomocą edytora zapytań
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Ustaw rozmiar na **Pełny**
6. Ustawianie wizualizacji na **Kafelki**
7. Kliknij przycisk **Ustawienia kafelka,** aby otworzyć okienko ustawień
8. W **polach kafelków**, zestaw:
    * Tytuł:`name`
    * Po `Requests`lewej: , `Big Number`Renderer: `Green to Red`, Paleta kolorów: , Wartość min:`0`
    * Dolnej części:`appName`
9. Kliknij przycisk **Zapisz i zamknij** u dołu okienka.

![Zrzut ekranu przedstawiający widok podsumowania kafelków](./media/workbooks-visualizations/tile-settings.png)

Oto jak będą wyglądały kafelki w trybie odczytu:

![Zrzut ekranu przedstawiający widok podsumowania kafelków](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Drzew

Skoroszyty obsługują widoki hierarchiczne za pośrednictwem siatek drzewa. Drzewa umożliwiają niektóre wiersze do rozszerzenia do następnego poziomu dla doświadczenia drążenia.

W poniższym przykładzie przedstawiono metryki kondycji kontenera (rozmiar zestawu roboczego) wizualizowane jako siatka drzewa. Węzły najwyższego poziomu są w tym miejscu węzły usługi Azure Kubernetes Service (AKS), następny poziom to zasobniki, a końcowy poziom są kontenerami. Należy zauważyć, że nadal można formatować kolumny, takie jak w siatce (mapa cieplna, ikony, łącze). Źródłem danych w tym przypadku jest obszar roboczy usługi Log Analytics z dziennikami AKS.

![Zrzut ekranu przedstawiający widok podsumowania kafelków](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Dodawanie siatki drzewa
1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edycja._
2. Użyj łącza **Dodaj kwerendę,** aby dodać kontrolkę kwerendy dziennika do skoroszytu. 
3. Wybierz typ kwerendy jako **Dziennik,** typ zasobu (na przykład Usługa Application Insights) i zasoby docelowe.
4. Wprowadź KQL do analizy za pomocą edytora zapytań
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Ustawianie wizualizacji na **Siatka**
6. Kliknij przycisk **Ustawienia kolumny,** aby otworzyć okienko ustawień
7. W sekcji **Drzewo/Grupuj według ustawień** u dołu ustaw:
    * Typ drzewa:`Parent/Child`
    * Pole Id:`Id`
    * Pole identyfikatora nadrzędnego:`ParentId`
    * Pokaż ekspander na:`Name`
    * Rozwiń najwyższy poziom drzewa:`checked`
8. W sekcji _Kolumny_ u góry ustaw:
    * _Id_ - Moduł renderowania kolumn:`Hidden`
    * _Identyfikator nadrzędny_ — moduł renderowania kolumn:`Hidden`
    * _Żądania_ - Moduł `Bar`renderowania `Blue`kolumn: , Kolor: , Minimalna wartość:`0`
9. Kliknij przycisk _Zapisz i zamknij_ u dołu okienka.    

![Zrzut ekranu przedstawiający widok podsumowania kafelków](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Ustawienia drzewa

| Ustawienie | Wyjaśnienie |
|:------------- |:-------------|
| `Id Field` | Unikalny identyfikator każdego rzędu w siatce |
| `Parent Id Field` | Identyfikator nadrzędnego bieżącego wiersza |
| `Show the expander on` | Kolumna, w której ma być pokazywała ekspander drzewa. Często siatki drzewa ukrywają swoje pole identyfikatora i identyfikatora nadrzędnego, ponieważ nie są one bardzo czytelne. Zamiast tego ekspander pojawia się w polu o bardziej czytelnej wartości — na przykład na nazwie encji |
| `Expand the top level of the tree` | Jeśli zaznaczona, siatka drzewa zostanie rozwinięta na najwyższym poziomie. Przydatne, jeśli chcesz domyślnie wyświetlać więcej informacji |

## <a name="graphs"></a>Wykresy

Skoroszyty obsługują wizualizację dowolnych wykresów na podstawie danych z dzienników, aby pokazać relacje między jednostkami monitorowania.

Poniższy wykres pokazuje dane płynące z komputera przez różne porty do/z komputerów zewnętrznych. Jest pokolorowany według typu (komputer vs port kontra zewnętrzny adres IP), a rozmiary krawędzi odpowiadają ilości danych przepływanych między nimi. Dane źródłowe pochodzą z kwerendy KQL przeznaczonej dla połączeń maszyn wirtualnych.

![Zrzut ekranu przedstawiający widok podsumowania kafelków](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Dodawanie wykresu
1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edycja._
2. Użyj łącza **Dodaj kwerendę,** aby dodać kontrolkę kwerendy dziennika do skoroszytu. 
3. Wybierz typ kwerendy jako **Dziennik,** typ zasobu (na przykład Usługa Application Insights) i zasoby docelowe.
4. Wprowadź KQL do analizy za pomocą edytora zapytań
    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```
7. Ustawianie wizualizacji na **wykres**
8. Kliknij przycisk **Ustawienia wykresu,** aby otworzyć okienko ustawień
9. W _polach układu_ u dołu ustaw:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. W _ustawieniach formatu węzła_ u góry ustaw:
    * _Najpopularniejsza zawartość_ `Name`— użyj kolumny: , Moduł renderowania kolumn:`Text`
    * _Zawartość centrum_— `Calls`użyj kolumny: `Big Number`, Moduł renderowania kolumn: , Paleta kolorów:`None`
    * _Zawartość dolna_- `Kind`Użyj kolumny: , Renderer kolumn:`Text`
10. Kliknij przycisk _Zapisz i zamknij_ u dołu okienka.

![Zrzut ekranu przedstawiający widok podsumowania kafelków](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie](workbooks-automate.md) skoroszytów za pomocą usługi Azure Resource Manager.
* [Kontroluj](workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
