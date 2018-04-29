---
title: Rozwiązywanie problemów zegara danych za pomocą usługi Azure Data Lake Tools dla programu Visual Studio | Dokumentacja firmy Microsoft
description: Potencjalne rozwiązania problemów zegara danych za pomocą usługi Azure Data Lake Tools dla programu Visual Studio.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/16/2016
ms.author: yanacai
ms.openlocfilehash: 2e1d33b5d2392832899fd30636e9d40231fc74ee
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Rozwiązywanie problemów zegara danych za pomocą usługi Azure Data Lake Tools dla programu Visual Studio

## <a name="what-is-data-skew"></a>Co to są dane pochylanie?

Krótko już wspomniano, Pochylenie danych jest nadmiernie reprezentowanego wartością. Wyobraź sobie przypisano 50 ekspertami podatku inspekcji deklaracji podatkowych, jeden egzaminator dla każdego stanu Stanów Zjednoczonych. Egzaminator Wyoming, ponieważ populacji jest mały, ma mało zrobić. W Kalifornijskiej jednak egzaminator jest przechowywana bardzo zajęty z powodu dużej populacji stanu.
    ![Przykład danych zegara problemu](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

W naszym scenariuszu dane nierównomiernie obejmowała wszystkie ekspertami podatku, co oznacza, że niektóre ekspertami musi działać więcej niż inne. W własne stanowisko często wystąpić sytuacja jak w przykładzie egzaminator podatku. W sposób bardziej technicznych jednego wierzchołka pobiera znacznie więcej danych niż jego elementów równorzędnych, sytuacji, która sprawia, że wierzchołka działać więcej niż pozostałe i który ostatecznie spowalnia całego zadania. Co to jest gorsza, zadanie może zakończyć się niepowodzeniem, ponieważ wierzchołków może być, na przykład ograniczenie 5-godzinnym środowiska uruchomieniowego i ograniczenie 6 GB pamięci RAM.

## <a name="resolving-data-skew-problems"></a>Rozwiązywanie problemów z danych zegara

Azure Data Lake Tools dla programu Visual Studio może pomóc wykryć, czy zadanie ma problem zegara danych. Jeśli istnieje problem, można rozwiązać go za pomocą rozwiązań w tej sekcji.

## <a name="solution-1-improve-table-partitioning"></a>Rozwiązanie 1: Poprawić, Partycjonowanie tabel

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Opcja 1: Filtrowanie wartość klucza spowodowałoby zafałszowanie z wyprzedzeniem

Jeśli nie ma ona wpływu na logice biznesowej, można filtrować wartości wyższej częstotliwości z wyprzedzeniem. Na przykład jeśli istnieje wiele 000 000 000 w kolumnie identyfikator GUID, nie można do agregacji tej wartości. Przed agregacji, można napisać "WHERE GUID! ="ruch 000 000 000"" na wartość o dużej częstotliwości filtru.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Opcja 2: Wybierz inny klucz partycji lub dystrybucji

W poprzednim przykładzie Jeśli chcesz tylko sprawdzić obciążenie podatku inspekcji na całym kraju, można zwiększyć dystrybucji danych, wybierając numer identyfikacyjny jako klucz. Pobrania różnych partycji lub klucza dystrybucji można czasami równomierne danych więcej, ale należy się upewnić, że ten wybór nie ma wpływu na logice biznesowej. Na przykład, aby obliczyć podatku Suma dla każdego stanu, może chcesz wyznaczyć _stanu_ jako klucza partycji. Jeśli nadal wystąpi ten problem, spróbuj użyć opcji 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Opcja 3: Dodaj więcej partycji lub dystrybucji kluczy

Zamiast używać tylko _stanu_ jako klucza partycji, można użyć więcej niż jednego klucza do partycjonowania. Na przykład, Rozważ dodanie _kod POCZTOWY_ jako klucza partycji dodatkowych, aby zmniejszyć rozmiar danych partycji i bardziej równomierne danych.

### <a name="option-4-use-round-robin-distribution"></a>Opcja 4: Użycie rozdzielanie

Jeśli nie możesz znaleźć odpowiedniego klucza partycji i dystrybucji, możesz spróbować użyć rozdzielanie. Rozdzielanie traktuje wszystkie wiersze jednakowo i losowo umieszcza je w odpowiednich zasobników. Równomiernie pobiera dane, ale utraty miejscowości informacji zwrotnych, może również zmniejszyć wydajność zadania dla niektórych operacji. Ponadto jeśli mimo to czynności agregacji dla klucza spowodowałoby zafałszowanie problem zegara danych będzie umieszczony. Aby dowiedzieć się więcej na temat rozdzielanie, zobacz sekcję dystrybucje tabeli U-SQL w [CREATE TABLE (U-SQL): Tworzenie tabeli ze schematem](https://msdn.microsoft.com/library/mt706196.aspx#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>2 — rozwiązanie: Poprawy planu zapytania

### <a name="option-1-use-the-create-statistics-statement"></a>Opcja 1: Użycie instrukcji CREATE STATISTICS

U-SQL zawiera instrukcję CREATE STATISTICS dla tabel. Ta instrukcja zawiera więcej informacji, aby optymalizator zapytań dotyczących właściwości danych, np. rozkład wartości, które są przechowywane w tabeli. Dla większości zapytań Optymalizator zapytań generuje już konieczne statystyki dotyczące planu zapytania wysokiej jakości. Czasami może być konieczne poprawiać wydajność zapytań, tworząc dodatkowe statystyki z instrukcji CREATE STATISTICS lub modyfikując projektu zapytania. Aby uzyskać więcej informacji, zobacz [instrukcji CREATE STATISTICS (U-SQL)](https://msdn.microsoft.com/library/azure/mt771898.aspx) strony.

Przykład kodu:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Statystyki informacje nie są automatycznie aktualizowane. Po zaktualizowaniu danych w tabeli bez konieczności ponownego tworzenia statystyk może zmniejszyć wydajność.

### <a name="option-2-use-skewfactor"></a>Opcja 2: Użycie SKEWFACTOR

Jeśli chcesz Suma podatku dla każdego stanu, należy użyć Grupuj według stanu, metody, która nie uniknąć zegara danych. Jednak może podać wskazówkę danych z zapytania do identyfikowania zegara danych w kluczach, umożliwiając optymalizatorowi można przygotować plan wykonania.

Zazwyczaj można ustawić parametru jako 0,5 i 1, 0,5 oznacza nie dużo zegara duże znaczenie pochylenia i 1. Ponieważ wskazówka wpływa na plan wykonania optymalizacji dla bieżącej instrukcji i wszystkie podrzędne instrukcje, należy dodać wskazówkę dotyczącą przed potencjalnie niesymetryczna key-wise agregacji.

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

### <a name="option-3-use-rowcount"></a>Opcja 3: Użycie ROWCOUNT  
Oprócz SKEWFACTOR w przypadku określonego klucza niesymetryczna sprzężenia, jeśli wiadomo, że zestaw dołączonego do wiersza jest mały, można stwierdzić, optymalizator przez dodanie wskazówkę liczby wierszy w instrukcji U-SQL przed sprzężenia. W ten sposób Optymalizator można wybrać strategii emisji sprzężenia w celu zwiększenia wydajności. Należy pamiętać, że ROWCOUNT nie rozwiązania problemu zegara danych, ale mogą oferować niektórych uzyskać dodatkową pomoc.

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

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Rozwiązanie 3: Poprawa reduktor zdefiniowane przez użytkownika i łączenia

Czasami może zapisywać zdefiniowane przez użytkownika — operator radzenia sobie z procesu złożonej logiki i dobrze napisane reduktor i łączenia może ograniczyć problem zegara danych w niektórych przypadkach.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Opcja 1: Użycie reduktor rekursywny, jeśli to możliwe

Domyślnie reduktor zdefiniowane przez użytkownika jest uruchamiana w trybie niecykliczne, co oznacza, że zmniejszyć pracy dla klucza jest dystrybuowana do jednego wierzchołka. Jednak jeśli danych jest niesymetryczna, dużych zestawów danych może być przetwarzane w jednym wierzchołka i uruchom przez długi czas.

Aby zwiększyć wydajność, można dodać atrybutu w kodzie do definiowania reduktor do pracy w trybie cykliczne. Następnie dużych zestawów danych można dystrybuować do wielu wierzchołków i uruchom równolegle, co przyspiesza zadania.

Aby zmienić reduktor niecykliczne cykliczne, musisz upewnij się, że Twoje algorytm jest asocjacyjnej. Na przykład suma jest asocjacyjnej i medianę nie jest. Należy również upewnić się, że dane wejściowe i wyjściowe dla reduktor zachować ten sam schemat.

Atrybut reduktor cykliczne:

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

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Opcja 2: W trybie łączenia na poziomie wiersza, jeśli to możliwe

Podobnie jak wskazówki ROWCOUNT dla określonego klucza niesymetryczna sprzężenia przypadków, tryb łączenia próbuje rozpowszechnić zestawy ogromnych wartości klucza niesymetryczna do wielu wierzchołków, tak aby pracy mogą być wykonywane jednocześnie. Tryb łączenia nie można rozpoznać danych zegara problemów, ale mogą oferować niektórych uzyskać dodatkową pomoc na wartość klucza niesymetryczna ogromnych zestawów.

Domyślnie tryb łączenia jest pełny, co oznacza, że nie mogą być oddzielone zestaw wiersza lewego i prawego wiersza. Ustawianie trybu jako wewnętrzny/prawej/lewej umożliwia sprzężenia na poziomie wiersza. System oddziela odpowiednich zestawów wierszy i rozpowszechnia je do wielu wierzchołków, które są uruchamiane równolegle. Jednak przed skonfigurowaniem trybie łączenia należy zachować ostrożność upewnić się, można podzielić odpowiednich zestawów wierszy.

W następującym przykładzie zestaw rozdzielonych wiersza po lewej stronie. Zależy od pojedynczy wiersz wejściowych z lewej strony każdego wiersza danych wyjściowych i potencjalnie jest zależna od wszystkie wiersze z prawej strony, z taką samą wartość klucza. Po ustawieniu trybu łączenia od lewej system oddziela ogromnych lewej-zestawu wierszy w małych sieci i przypisuje je do wielu wierzchołków.

![Ilustracja trybie łączenia](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Po ustawieniu trybu łączenia nieprawidłowe połączenie jest mniej wydajne i wyniki mogą być nieprawidłowe.

Atrybuty trybu łączenia:

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Każdy wiersz danych wyjściowych jest zależna od pojedynczy wiersz wejściowych z lewej strony (i potencjalnie wszystkie wiersze z prawej strony, z taką samą wartość klucza).

- qlUserDefinedCombiner(Mode=CombinerMode.Right): każdy wiersz danych wyjściowych jest zależna od pojedynczy wiersz wejściowych z prawej (i potencjalnie wszystkie wiersze z lewej strony z taką samą wartość klucza).

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Każdy wiersz danych wyjściowych jest zależna od pojedynczy wiersz wejściowych z lewej i prawej o tej samej wartości.

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
