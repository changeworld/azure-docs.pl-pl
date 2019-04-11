---
title: Rozwiązywanie problemów niesymetryczność danych przy użyciu narzędzi Azure Data Lake Tools for Visual Studio
description: Potencjalne rozwiązania problemów niesymetryczność danych przy użyciu narzędzi Azure Data Lake Tools for Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: af55c161944447f2e6e2245fbb920803779984ca
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469747"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Rozwiązywanie problemów niesymetryczność danych przy użyciu narzędzi Azure Data Lake Tools for Visual Studio

## <a name="what-is-data-skew"></a>Co to jest data pochylanie?

Krótko wspomniano, niesymetryczność danych jest nadmiernie reprezentowana wartością. Wyobraź sobie, czy użytkownik przypisał 50 ekspertami podatku, aby przeprowadzić inspekcję zwroty podatku, egzaminator jeden dla każdego stanu USA. Egzaminator Wyoming, ponieważ istnieje populacji jest mały, ma mało celu. W Kalifornii jednak egzaminator są przechowywane bardzo zajęty z powodu dużej populacji stanu.
    ![Przykład problemu niesymetryczność danych](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

W naszym scenariuszu dane są nierównomiernie rozłożone między wszystkie ekspertami podatku, co oznacza, że niektóre ekspertami musi działać więcej niż inne. W ramach własnego zadania często wystąpić sytuacja, podobnie jak w przykładzie egzaminator podatku. W sposób bardziej technicznych wierzchołka pobiera znacznie więcej danych niż jego elementów równorzędnych, sytuację, która sprawia, że wierzchołka działać więcej niż pozostałe oraz że po pewnym czasie spowalnia całego zadania. Co to jest niższa, zadanie może zakończyć się niepowodzeniem, ponieważ wierzchołki może być, na przykład ograniczenia środowiska uruchomieniowego 5-godzinnego i ograniczenie 6 GB pamięci RAM.

## <a name="resolving-data-skew-problems"></a>Rozwiązywanie problemów z niesymetryczność danych

Usługa Azure Data Lake Tools for Visual Studio może pomóc wykryć, czy zadanie ma problem niesymetryczność danych. Jeśli istnieje problem, można rozwiązać go za pomocą rozwiązań w tej sekcji.

## <a name="solution-1-improve-table-partitioning"></a>Rozwiązanie 1: Poprawić, Partycjonowanie tabel

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Opcja 1: Filtruj niesymetryczne wartości klucza z wyprzedzeniem

Jeśli nie ma wpływu na logice biznesowej, można filtrować wartości wyższa częstotliwość z wyprzedzeniem. Na przykład jeśli jest dostępnych wiele 000-000-000 w kolumnie identyfikator GUID, nie można zagregować wartości. Przed agregacji, można napisać "GUID gdzie! ="000-000-000"" na wartość o wysokiej częstotliwości filtru.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Opcja 2: Wybierz inny klucz partycji lub dystrybucji

W poprzednim przykładzie Jeśli chcesz tylko sprawdzić obciążenia inspekcji podatku w całym kraju, możesz poprawić dystrybucji danych, wybierając numer identyfikacyjny jako klucz. Pobieranie różnych partycji i klucza dystrybucji może czasami równomierne rozłożenie danych bardziej, ale należy upewnić się, że ten wybór nie ma wpływu na logice biznesowej. Na przykład, aby obliczyć sumę podatku dla każdego stanu, możesz chcieć określić _stanu_ jako klucza partycji. Jeśli nadal występuje ten problem, spróbuj użyć opcji 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Opcja 3: Dodaj więcej klucze partycji lub dystrybucji

Zamiast używania tylko _stanu_ jako klucza partycji, można użyć więcej niż jednego klucza do partycjonowania. Na przykład, należy rozważyć dodanie _kod POCZTOWY_ jako klucza partycji dodatkowe, aby zmniejszyć rozmiar partycji danych i bardziej równomierne rozłożenie danych.

### <a name="option-4-use-round-robin-distribution"></a>Opcja 4: Użyj rozdzielanie

Jeśli nie możesz znaleźć odpowiedniego klucza partycji i dystrybucji, możesz spróbować do użycia dystrybucji działanie okrężne. Rozdzielanie równie traktuje wszystkie wiersze i losowo umieszcza je w odpowiednich przedziały. Równomiernie pobiera dane, ale utracie miejscowość informacji zwrotnych, może także zmniejszyć wydajność zadania dla niektórych operacji. Ponadto jeśli mimo to ustalania agregacji niesymetryczne klucza zostanie utrzymany problem niesymetryczność danych. Aby dowiedzieć się więcej na temat rozdzielanie, zobacz sekcję dystrybucje tabeli U-SQL w [CREATE TABLE (U-SQL): Tworzenie tabeli przy użyciu schematu](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Rozwiązanie 2: Ulepszyć plan zapytania

### <a name="option-1-use-the-create-statistics-statement"></a>Opcja 1: Użyj instrukcji CREATE STATISTICS

U-SQL zawiera instrukcję CREATE STATISTICS dla tabel. Ta instrukcja daje więcej informacji do Optymalizator zapytań dotyczących właściwości danych, np. rozkład wartości, które są przechowywane w tabeli. Dla większości zapytań Optymalizator zapytań generuje już niezbędne statystyki dotyczące planu zapytania o wysokiej jakości. Czasami konieczne może poprawić wydajność zapytań, tworząc dodatkowe statystyki z CREATE STATISTICS lub modyfikując Projekt kwerendy. Aby uzyskać więcej informacji, zobacz [CREATE STATISTICS (U-SQL)](/u-sql/ddl/statistics/create-statistics) strony.

Przykład kodu:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Informacje statystyczne nie jest aktualizowane automatycznie. Jeśli aktualizujesz dane w tabeli bez potrzeby ponownego tworzenia statystyk wydajności zapytań może odrzucić.

### <a name="option-2-use-skewfactor"></a>Opcja 2: Użyj SKEWFACTOR

Jeśli chcesz zsumować podatku dla każdego stanu, należy użyć Grupuj według stanu, metody, która nie uniknąć tego problemu niesymetryczność danych. Może jednak zapewniać wskazówką danych w zapytaniu do identyfikowania niesymetryczność danych w kluczach, więc, że optymalizator można przygotować plan wykonywania.

Zazwyczaj można ustawić parametru jako 0,5 i 1, 0,5 oznacza niesymetryczność mocno nie znacznie znaczenia niesymetryczność, a 1. Ponieważ wskazówka wpływa na optymalizację plan wykonywania bieżącej instrukcji i wszystkie podrzędne instrukcje, pamiętaj dodać wskazówkę dotyczącą przed potencjalną niesymetryczną agregacji.

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

Przykład kodu:

    //Add a SKEWFACTOR hint.
    @Impressions =
        SELECT * FROM
        searchDM.SML.PageView(@start, @end) AS PageView
        OPTION(SKEWFACTOR(Query)=0.5)
        ;

    //Query 1 for key: Query, ClientId
    @Sessions =
        SELECT
            ClientId,
            Query,
            SUM(PageClicks) AS Clicks
        FROM
            @Impressions
        GROUP BY
            Query, ClientId
        ;

    //Query 2 for Key: Query
    @Display =
        SELECT * FROM @Sessions
            INNER JOIN @Campaigns
                ON @Sessions.Query == @Campaigns.Query
        ;   

### <a name="option-3-use-rowcount"></a>Opcja 3: Użyj operacji ROWCOUNT  
Oprócz SKEWFACTOR dla określonych sprzężenia nierówne klucz przypadki, jeśli wiesz, że inny zestaw dołączonym do wiersza jest mała, możesz przekazać Optymalizator, dodając wskazówką ROWCOUNT w instrukcji U-SQL przed sprzężenia. W ten sposób Optymalizator można wybrać strategii emisji, sprzężenia, aby zwiększyć wydajność. Należy pamiętać, że ROWCOUNT nie rozwiązało problemu niesymetryczność danych, ale może on oferować dodatkową pomoc.

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

Przykład kodu:

    //Unstructured (24-hour daily log impressions)
    @Huge   = EXTRACT ClientId int, ...
                FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
                ;

    //Small subset (that is, ForgetMe opt out)
    @Small  = SELECT * FROM @Huge
                WHERE Bing.ForgetMe(x,y,z)
                OPTION(ROWCOUNT=500)
                ;

    //Result (not enough information to determine simple broadcast JOIN)
    @Remove = SELECT * FROM Bing.Sessions
                INNER JOIN @Small ON Sessions.Client == @Small.Client
                ;

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Rozwiązanie 3: Poprawa reduktor zdefiniowany przez użytkownika i łączenia

Czasami można napisać operatora zdefiniowanego przez użytkownika, aby poradzić sobie z logiką skomplikowanych procesów i dobrze napisane reduktor i łączenia może rozwiązać problem niesymetryczność danych, w niektórych przypadkach.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Opcja 1: Użyj reduktora rekursywnego, jeśli to możliwe

Domyślnie reduktor zdefiniowany przez użytkownika jest uruchamiana w trybie niecyklicznego, co oznacza, że ograniczenia pracy dla klucza jest dystrybuowane do poszczególnych wierzchołków. Ale jeśli danych jest niesymetryczna, ogromnych zestawów danych może być przetwarzane w poszczególnych wierzchołków i uruchom przez długi czas.

Aby zwiększyć wydajność, można dodać atrybutu w kodzie, aby zdefiniować reduktor do pracy w trybie cykliczne. Następnie ogromnych zestawów danych można dystrybuować do wielu wierzchołków i uruchom równolegle, co przyspiesza zadania.

Aby zmienić reduktor niecyklicznego cykliczne, musisz upewnij się, że algorytm jest asocjacyjny. Na przykład suma jest asocjacyjny i mediana jest. Należy również upewnić się, że dane wejściowe i wyjściowe dla reduktor zachować ten sam schemat.

Atrybut reduktora rekursywnego:

    [SqlUserDefinedReducer(IsRecursive = true)]

Przykład kodu:

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Opcja 2: Użyj trybu funkcji łączenia na poziomie wiersza, jeśli jest to możliwe

Podobnie jak wskazówki liczby wierszy dla określonych sprzężenia nierówne klucz przypadki, tryb łączenia próbuje rozpowszechniać zestawów olbrzymią wartość klucza nierówne do wielu wierzchołków tak, aby prace, które mogą być wykonywane jednocześnie. Tryb łączenia nie może rozwiązać problemy niesymetryczność danych, ale może on oferować niektóre dodatkową pomoc na temat zestawów olbrzymią wartość klucza nierówne.

Domyślnie tryb łączenia jest pełny, co oznacza, że zestawu wierszy po lewej stronie i prawego wiersza zestawu nie mogą być oddzielone. Ustawianie trybu jako prawo/lewo/wewnętrzny umożliwia sprzężenia na poziomie wiersza. System oddziela odpowiednich zestawów wierszy i przesyła je do wielu wierzchołki, które są uruchamiane równolegle. Jednak przed rozpoczęciem konfigurowania trybu łączenia, uważaj upewnić się, można oddzielić odpowiednich zestawów wierszy.

W poniższym przykładzie pokazuje zestaw rozdzielonych wiersza po lewej stronie. Każdy wiersz danych wyjściowych jest zależna od pojedynczy wiersz danych wejściowych z lewej strony i potencjalnie jest zależna od wszystkie wiersze z prawej strony z taką samą wartość klucza. Jeśli tryb łączenia jest ustawiony jako po lewej stronie, system oddziela ogromna po lewej stronie — zestawu wierszy w małych sieci i przypisuje je do wielu wierzchołków.

![Ilustracja tryb łączenia](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Jeśli ustawiono trybu funkcji łączenia na problem, połączenie jest mniej wydajne rozwiązanie, a wyniki mogą być nieprawidłowe.

Atrybuty trybu funkcji łączenia:

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Każdy wiersz danych wyjściowych jest zależna od pojedynczy wiersz danych wejściowych z lewej strony (i potencjalnie wszystkie wiersze z prawej strony z taką samą wartość klucza).

- qlUserDefinedCombiner(Mode=CombinerMode.Right): Każdy wiersz danych wyjściowych jest zależna od pojedynczy wiersz danych wejściowych z prawej strony (i potencjalnie wszystkie wiersze z lewej strony z taką samą wartość klucza).

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Każdy wiersz danych wyjściowych jest zależna od pojedynczy wiersz danych wejściowych z lewej i prawej strony z taką samą wartość.

Przykład kodu:

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }
