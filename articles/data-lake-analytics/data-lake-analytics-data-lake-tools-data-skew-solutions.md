---
title: Rozwiązywanie problemów z pochylaniem danych — narzędzia usługi Azure Data Lake tools dla programu Visual Studio
description: Rozwiązywanie problemów z potencjalnymi rozwiązaniami problemów z wypaczaniem danych przy użyciu narzędzi usługi Azure Data Lake tools dla programu Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: 9ff7ba5f04a8c1862f8ef136f8f3f6900f00a431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71802554"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Resolve data-skew problems by using Azure Data Lake Tools for Visual Studio (Rozwiązywanie problemów z niesymetrycznością danych przy użyciu narzędzi Azure Data Lake Tools for Visual Studio)

## <a name="what-is-data-skew"></a>Co to jest pochylenie danych?

Krótko mówiąc, pochylenie danych jest wartością nadprezentalnej. Wyobraź sobie, że masz przypisane 50 ekspertów podatkowych do kontroli zeznań podatkowych, jeden ekspert dla każdego stanu USA. Egzaminator z Wyoming, ponieważ populacja jest mała, ma niewiele do zrobienia. W Kalifornii, jednak, egzaminator jest bardzo zajęty ze względu na dużą populację stanu.
    ![Przykład problemu z wypaczaniem danych](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

W naszym scenariuszu dane są nierównomiernie rozdzielane między wszystkich ekspertów podatkowych, co oznacza, że niektórzy egzaminatorzy muszą pracować więcej niż inni. W swojej pracy często doświadczasz sytuacji, takich jak przykład eksperta podatkowego. W ujęciu bardziej technicznym jeden wierzchołek pobiera znacznie więcej danych niż jego elementy równorzędne, co sprawia, że wierzchołek działa bardziej niż inni i ostatecznie spowalnia całe zadanie. Co gorsza, zadanie może zakończyć się niepowodzeniem, ponieważ wierzchołki mogą mieć na przykład ograniczenie 5-godzinnego czasu wykonywania i ograniczenie pamięci 6 GB.

## <a name="resolving-data-skew-problems"></a>Rozwiązywanie problemów z wypaczaniem danych

Narzędzia usługi Azure Data Lake tools dla programu Visual Studio mogą pomóc wykryć, czy w twoim zadaniu występuje problem z wypaczeniem danych. Jeśli problem istnieje, można go rozwiązać, wypróbowywając rozwiązania w tej sekcji.

## <a name="solution-1-improve-table-partitioning"></a>Rozwiązanie 1: Poprawa partycjonowania tabeli

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Opcja 1: Filtruj przekrzywiony klucz z wyprzedzeniem

Jeśli nie ma to wpływu na logikę biznesową, można filtrować wartości o wyższej częstotliwości z wyprzedzeniem. Na przykład jeśli istnieje dużo 000-000-000 w identyfikatorze GUID kolumny, nie można zagregować tej wartości. Przed agregacji, można napisać "WHERE GUID != "000-000-000"," do filtrowania wartości wysokiej częstotliwości.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Opcja 2: Wybierz inną partycję lub klucz dystrybucji

W poprzednim przykładzie, jeśli chcesz tylko sprawdzić obciążenie inspekcji podatkowej w całym kraju/regionie, możesz poprawić dystrybucję danych, wybierając numer identyfikatora jako klucz. Wybranie innej partycji lub klucza dystrybucji może czasami dystrybuować dane bardziej równomiernie, ale należy upewnić się, że ten wybór nie wpływa na logikę biznesową. Na przykład, aby obliczyć sumę podatku dla każdego stanu, można wyznaczyć _stan_ jako klucz partycji. Jeśli nadal występuje ten problem, spróbuj użyć opcji 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Opcja 3: Dodaj więcej kluczy partycji lub dystrybucji

Zamiast używać tylko _stan_ jako klucz partycji, można użyć więcej niż jeden klucz do partycjonowania. Rozważmy na przykład dodanie _kodu pocztowego_ jako dodatkowego klucza partycji, aby zmniejszyć rozmiary partycji danych i bardziej równomiernie rozpowszechniać dane.

### <a name="option-4-use-round-robin-distribution"></a>Opcja 4: Użyj dystrybucji okrężkowej

Jeśli nie można znaleźć odpowiedni klucz dla partycji i dystrybucji, można spróbować użyć dystrybucji okrężnej. Dystrybucja okrężna traktuje wszystkie wiersze jednakowo i losowo umieszcza je w odpowiednich zasobnikach. Dane są równomiernie rozłożone, ale traci informacje o lokalizacji, co jest wadą, która może również zmniejszyć wydajność zadania w niektórych operacjach. Ponadto jeśli wykonujesz agregację dla pochyłego klucza mimo to problem pochylenia danych będzie się powtarzał. Aby dowiedzieć się więcej o dystrybucji okrężnych, zobacz sekcję Rozkłady tabel U-SQL w [temacie TWORZENIE TABELI (U-SQL): Tworzenie tabeli ze schematem](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Rozwiązanie 2: Ulepszanie planu zapytań

### <a name="option-1-use-the-create-statistics-statement"></a>Opcja 1: Użyj instrukcji CREATE STATISTICS

U-SQL udostępnia instrukcję CREATE STATISTICS w tabelach. Ta instrukcja zawiera więcej informacji do optymalizatora kwerendy o właściwości danych, takich jak dystrybucja wartości, które są przechowywane w tabeli. W przypadku większości zapytań optymalizator kwerend generuje już niezbędne statystyki dla planu zapytań wysokiej jakości. Od czasu do czasu może być konieczne zwiększenie wydajności kwerendy, tworząc dodatkowe statystyki za pomocą CREATE STATISTICS lub modyfikując projekt kwerendy. Aby uzyskać więcej informacji, zobacz stronę [TWORZENIE STATYSTYK (U-SQL).](/u-sql/ddl/statistics/create-statistics)

Przykład kodu:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Informacje statystyczne nie są aktualizowane automatycznie. Jeśli zaktualizujesz dane w tabeli bez ponownego tworzenia statystyk, wydajność kwerendy może zmniejszyć.

### <a name="option-2-use-skewfactor"></a>Opcja 2: Użyj SKEWFACTOR

Jeśli chcesz zsumować podatek dla każdego stanu, należy użyć group by state, podejście, które nie pozwala uniknąć problemu pochylenia danych. Jednak można podać wskazówkę danych w zapytaniu do identyfikowania danych pochylenia w klucze, dzięki czemu optymalizator może przygotować plan wykonania dla Ciebie.

Zazwyczaj można ustawić parametr jako 0,5 i 1, z 0,5 co oznacza, że nie dużo skośne i 1 znaczenie ciężkich pochyleń. Ponieważ wskazówka wpływa na optymalizację planu wykonania dla bieżącej instrukcji i wszystkich instrukcji podrzędnych, należy dodać wskazówkę przed potencjalną agregacją pochylenia kluczy.

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

### <a name="option-3-use-rowcount"></a>Opcja 3: Użyj ROWCOUNT  
Oprócz SKEWFACTOR, dla określonych przypadkach sprzężenia skośnego klucza, jeśli wiesz, że inny zestaw wierszy sprzężony jest mały, można powiedzieć optymalizatora, dodając wskazówkę ROWCOUNT w instrukcji U-SQL przed JOIN. W ten sposób optymalizator może wybrać strategię sprzężenia emisji, aby poprawić wydajność. Należy pamiętać, że ROWCOUNT nie rozwiązuje problemu pochylenia danych, ale może zaoferować dodatkową pomoc.

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

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Rozwiązanie 3: Poprawa zdefiniowanego przez użytkownika reduktora i kombajna

Czasami można napisać operatora zdefiniowanego przez użytkownika, aby poradzić sobie ze skomplikowaną logiką procesu, a dobrze napisany reduktor i kombajnia może w niektórych przypadkach złagodzić problem z wypaczeniem danych.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Opcja 1: Jeśli to możliwe, użyj reduktora cyklicznego

Domyślnie reduktor zdefiniowany przez użytkownika działa w trybie niecyklicznym, co oznacza, że zmniejszenie pracy dla klucza jest rozłożone na jeden wierzchołek. Ale jeśli dane są wypaczone, ogromne zestawy danych mogą być przetwarzane w jednym wierzchołku i działać przez długi czas.

Aby zwiększyć wydajność, można dodać atrybut w kodzie, aby zdefiniować reduktor do uruchomienia w trybie cyklicznym. Następnie ogromne zestawy danych mogą być dystrybuowane do wielu wierzchołków i działać równolegle, co przyspiesza zadanie.

Aby zmienić reduktor niecykliczny na cykliczny, należy upewnić się, że algorytm jest zespolonej. Na przykład suma jest zespolowaty, a mediana nie jest. Należy również upewnić się, że dane wejściowe i wyjściowe dla reduktora zachować ten sam schemat.

Atrybut reduktora cyklicznego:

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

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Opcja 2: W miarę możliwości użyj trybu kombajny na poziomie wiersza

Podobnie jak ROWCOUNT wskazówki dla określonych przypadkach sprzężenia skośny klucz, tryb kombajnia próbuje rozdzielić ogromne zestawy wartości przekrzywiony klucz do wielu wierzchołków, dzięki czemu praca może być wykonywana jednocześnie. Tryb kombajnu nie może rozwiązać problemów z wypaczeniem danych, ale może zaoferować dodatkową pomoc dla ogromnych zestawów wartości przekrzywionych kluczy.

Domyślnie tryb kombajnia jest Pełny, co oznacza, że nie można rozdzielić zestawu lewego wiersza i prawego wiersza. Ustawienie trybu jako Lewy/Prawy/Wewnętrzny umożliwia sprzężenie na poziomie wiersza. System oddziela odpowiednie zestawy wierszy i rozmieszcza je na wiele wierzchołków, które są uruchamiane równolegle. Jednak przed skonfigurowaniem trybu kombajnu należy uważać, aby upewnić się, że odpowiednie zestawy wierszy mogą być oddzielone.

W poniższym przykładzie przedstawiono oddzielny zestaw lewego wiersza. Każdy wiersz wyjściowy zależy od pojedynczego wiersza wejściowego od lewej i potencjalnie zależy od wszystkich wierszy z prawej strony o tej samej wartości klucza. Jeśli ustawisz tryb kombajnia jako lewy, system oddziela ogromny zestaw lewego rzędu na małe i przypisuje je do wielu wierzchołków.

![Ilustracja trybu kombinatora](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Jeśli ustawisz niewłaściwy tryb kombinowania, kombinacja jest mniej wydajna, a wyniki mogą być błędne.

Atrybuty trybu kombinatora:

- SqlUserDefinedCombiner(Mode=CombinerMode.Full): Każdy wiersz danych wyjściowych potencjalnie zależy od wszystkich wierszy wejściowych z lewej i prawej strony o tej samej wartości klucza.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Każdy wiersz danych wyjściowych zależy od pojedynczego wiersza wejściowego od lewej (i potencjalnie wszystkich wierszy z prawej strony o tej samej wartości klucza).

- qlUserDefinedCombiner(Mode=CombinerMode.Right): Każdy wiersz danych wyjściowych zależy od pojedynczego wiersza wejściowego od prawej (i potencjalnie wszystkich wierszy z lewej strony o tej samej wartości klucza).

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Każdy wiersz danych wyjściowych zależy od pojedynczego wiersza wejściowego z lewej i prawej wartości o tej samej wartości.

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
