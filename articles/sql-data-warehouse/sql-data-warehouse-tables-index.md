---
title: Indeksowanie tabel w usłudze Azure SQL Data Warehouse | Microsoft Azure
description: Zalecenia i przykłady dla indeksowania tabel w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/18/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seoapril2019
ms.openlocfilehash: eab64d9494ef2d2838e16c55eed6ecf0db9736e9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797945"
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Indeksowanie tabel w usłudze SQL Data Warehouse

Zalecenia i przykłady dla indeksowania tabel w usłudze Azure SQL Data Warehouse.

## <a name="index-types"></a>Typy indeksów

Usługa SQL Data Warehouse oferuje kilka opcji indeksowania, w tym [klastrowane indeksy magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-overview), [klastrowane indeksy i indeksy nieklastrowane](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described), i bez indeksu opcji znany także jako [sterty ](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes).  

Aby utworzyć tabelę z indeksem, zobacz [CREATE TABLE (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) dokumentacji.

## <a name="clustered-columnstore-indexes"></a>Klastrowane indeksy magazynu kolumn

Domyślnie usługa SQL Data Warehouse tworzy klastrowanego indeksu magazynu kolumn, gdy określono bez opcji indeksu dla tabeli. Tabel klastrowanego magazynu kolumn oferują zarówno najwyższy poziom kompresji danych, jak i najlepszą ogólną wydajność zapytań.  Tabel klastrowanego magazynu kolumn zazwyczaj będzie której klastrowany indeks lub sterty tabel i zwykle jest najlepszym wyborem dla dużych tabel.  Z tego względu klastrowanego magazynu kolumn jest najlepszym miejscem, aby uruchomić, gdy wiesz, jak indeksowanie tabeli.  

Aby utworzyć tabelę klastrowanego magazynu kolumn, po prostu Określ KLASTROWANEGO INDEKSU magazynu kolumn w klauzuli WITH lub pozostaw klauzuli WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Istnieje kilka scenariuszy, w którym klastrowanego magazynu kolumn nie może być to dobry wybór:

- Tabele magazynu kolumn nie obsługują, varchar(max), nvarchar(max) i varbinary(max). Zamiast tego należy rozważyć stosu lub indeksu klastrowanego.
- Tabele magazynu kolumn może być mniej wydajne rozwiązanie dla danych przejściowych. Należy wziąć pod uwagę sterty i tabele tymczasowe wręcz Fatalne.
- Małe tabele z mniej niż 60 milionami wierszy. Należy wziąć pod uwagę tabel stosów.

## <a name="heap-tables"></a>Tabele stosu

Gdy dane są tymczasowo kierowanych do magazynu danych SQL, może się okazać, że użycie tabeli stosu sprawia, że cały proces. Jest to spowodowane obciążenia odnoszące się do stert są realizowane szybciej niż tabele indeksów i w niektórych przypadkach kolejny odczyt może odbywać się z pamięci podręcznej.  Jeśli są ładowane tylko dane i przygotowania uruchomienia kolejnych przekształceń, załadowanie tabeli do stosu jest znacznie szybsze niż załadowanie danych do tabeli klastrowanego magazynu kolumn. Ponadto załadowanie danych do [tabeli tymczasowej](sql-data-warehouse-tables-temporary.md) ładuje szybsze niż załadowanie tabeli do pamięci trwałej.  

W przypadku tabel odnośników małych, mniej niż 60 milionami wierszy często tabel stosów sens.  Rozpocznij tabel magazynu kolumn klastra w celu uzyskania optymalnej kompresji po ponad 60 milionów wierszy.

Aby utworzyć tabelę sterty, wystarczy określić STERTY w klauzuli WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Klastrowanych i nieklastrowanych indeksów

Indeksy klastrowane może przewyższyć tabel klastrowanego magazynu kolumn, gdy pojedynczy wiersz musi zostać szybko pobrane. Dla zapytań, gdzie jest wymagany do działania z szybkością extreme jednego środowiska lub bardzo mało wiersza wyszukiwania należy wziąć pod uwagę klastra indeksu lub indeksu nieklastrowanego dodatkowej. Wadą korzystania z indeksu klastrowanego są tylko zapytania, które korzystają te, które Użyj wysoce selektywne filtru na kolumnę indeksu klastrowanego. W celu filtrowania innych kolumn indeksu nieklastrowanego na indeks można dodać do innych kolumn. Jednak każdy indeks, który jest dodawany do tabeli dodaje miejsce i czas przetwarzania do obciążeń.

Aby utworzyć tabelę indeks klastrowany, wystarczy określić INDEKSU KLASTROWANEGO z klauzulą:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Aby dodać indeksu nieklastrowanego dla tabeli, użyj następującej składni:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optymalizacja klastrowane indeksy magazynu kolumn

Tabel klastrowanego magazynu kolumn są zorganizowane w danych na segmenty.  Segment wysokiej jakości ma kluczowe znaczenie dla osiągnięcia zoptymalizować wydajność zapytań w tabeli magazynu kolumn.  Jakość segmentu można zmierzyć przez liczbę wierszy w grupy wierszy skompresowany.  Jakość segmentu jest optymalnie w przypadku, gdy istnieją co najmniej 100 tysięcy wierszy na wiersz skompresowany grupy i uzyskania wydajności jako liczba wierszy podejścia dotyczącego grupy wierszy 1 048 576 wierszy, czyli większość wiersze, które może zawierać grupę wierszy.

Poniżej można tworzyć i używać w systemie do obliczenia średniej wierszy w poszczególnych wierszy grupy i zidentyfikować wszelkie indeksów magazynu kolumn klastra optymalne widoku.  Ostatnia kolumna, w tym widoku generuje instrukcję SQL, która może służyć do odbudowywania indeksów.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Teraz, po utworzeniu widoku, należy uruchomić to zapytanie, aby zidentyfikować tabel z grupami wierszy z mniej niż 100 tysięcy wierszy. Oczywiście można zwiększyć próg wynoszący 100 tysięcy, jeśli potrzebujesz więcej jakości segmentów optymalne.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Po uruchomieniu zapytania, które można rozpocząć Spójrz na dane i przeanalizować wyniki. W następującej tabeli opisano, gdzie jej szukać w analizy grupy wierszy.

| Kolumna | Jak używać tych danych |
| --- | --- |
| [table_partition_count] |Jeśli tabela jest podzielona na partycje, może oczekiwać zobaczyć, że liczba wyższej grupy Otwórz wiersz. Każda partycja w dystrybucji może teoretycznie istnieje grupa Otwórz wiersz skojarzonych z nim. Należy wziąć to do analizy. Małej tabeli, który jest podzielony na partycje można można zoptymalizować, usuwając całkowicie partycjonowania, ponieważ może to poprawić kompresji. |
| [row_count_total] |Całkowita liczba wierszy w tabeli. Na przykład można użyć tej wartości do obliczania wartości procentowej wierszy w skompresowanym stanie. |
| [row_count_per_distribution_MAX] |Jeśli wszystkie wiersze są dystrybuowane równomiernie ta wartość będzie wynosić docelowa liczba wierszy na dystrybucję. Porównaj tę wartość za pomocą compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Całkowita liczba wierszy w formacie magazynu kolumn dla tabeli. |
| [COMPRESSED_rowgroup_rows_AVG] |Jeśli to średnia liczba wierszy jest znacznie mniejsza niż maksymalna liczba wierszy na grupę wierszy, rozważ użycie instrukcji CTAS lub ALTER INDEX REBUILD ponownej kompresji danych |
| [COMPRESSED_rowgroup_count] |Liczba grup wierszy w formacie magazynu kolumn. Jeśli ta liczba jest bardzo duże w odniesieniu do tabeli jest wskaźnikiem brakuje gęstość magazynu kolumn. |
| [COMPRESSED_rowgroup_rows_DELETED] |Wiersze logicznie są usuwane z formatu magazynu kolumn. Jeśli liczba jest wysoka względem rozmiar tabeli, należy rozważyć ponowne tworzenie partycji lub odbudowanie indeksu, ponieważ spowoduje to usunięcie ich fizycznie. |
| [COMPRESSED_rowgroup_rows_MIN] |Umożliwia to w połączeniu z kolumnami, średnia i maksymalna zrozumieć zakres wartości dla grupy wierszy w Twojego magazynu kolumn. Niską wartość powyżej wartości progowej obciążenia (102400 na dystrybucję partycji wyrównane) sugeruje, że optymalizacje są dostępne podczas ładowania danych |
| [COMPRESSED_rowgroup_rows_MAX] |Podobnie jak powyżej |
| [OPEN_rowgroup_count] |Jest to normalne zachowanie otwarte grupy wierszy. Jeden stosowny oczekiwać jednej grupy Otwórz wiersz na dystrybucji tabel (60). Nadmierne numery sugerują ładowania partycji danych. Dokładnie sprawdź strategii partycjonowania i upewnić się, że jest dźwięk |
| [OPEN_rowgroup_rows] |Każda grupa wiersza może mieć 1 048 576 wierszy w niej co najwyżej. Użyj tej wartości, aby zobaczyć, jak otwarte grupy wierszy są obecnie dostępne |
| [OPEN_rowgroup_rows_MIN] |Otwieranie obszaru roboczego grupy wskazują, że dane są strumieniem ładowany do tabeli albo że poprzedniego obciążenia rozlane pozostałe wiersze do tej grupy wierszy. Użyj MIN, MAX, AVG kolumn, aby zobaczyć, jak dużo danych jest znajdowało się w OTWARTYCH wierszy grup. W przypadku małych tabel może być 100% wszystkich danych! W tym przypadku ALTER INDEX REBUILD wymusić danych do magazynu kolumn. |
| [OPEN_rowgroup_rows_MAX] |Podobnie jak powyżej |
| [OPEN_rowgroup_rows_AVG] |Podobnie jak powyżej |
| [CLOSED_rowgroup_rows] |Spójrz na wiersz zamknięte grupowanie wierszy w celu sprawdzenia poprawnością. |
| [CLOSED_rowgroup_count] |Liczba grup wierszy zamknięte powinna być niska, jeśli dowolny są widoczne w ogóle. Grupy wierszy zamknięte można przekonwertować na skompresowanych grup wierszy przy użyciu ALTER INDEX... REORGANIZOWAĆ polecenia. Jednak nie jest to zwykle wymagane. Zamknięte grupy są automatycznie konwertowane na grupy wierszy magazynu kolumn przez proces w tle "krotki modułu przenoszącego spójne kolekcje". |
| [CLOSED_rowgroup_rows_MIN] |Grupy wierszy zamknięte powinien mieć współczynnik wypełnienia bardzo duże. Jeśli szybkość wypełniania grupy zamknięte wiersza jest niska, analizę magazynu kolumn jest wymagana. |
| [CLOSED_rowgroup_rows_MAX] |Podobnie jak powyżej |
| [CLOSED_rowgroup_rows_AVG] |Podobnie jak powyżej |
| [Rebuild_Index_SQL] |SQL, aby odbudować indeksu magazynu kolumn dla tabeli |

## <a name="causes-of-poor-columnstore-index-quality"></a>Przyczyny jakości indeksu magazynu kolumn niska

Po zidentyfikowaniu tabel z segmentu słabą jakością, chcesz odkryć ich główną przyczynę.  Poniżej przedstawiono niektóre typowe przyczyny segmentu niskiej jakości:

1. Wykorzystanie pamięci podczas kompilowania indeksu
2. Duża liczba operacji DML
3. Małe lub ścieknie operacje ładowania
4. Zbyt wiele partycji

Te czynniki mogą powodować indeksu magazynu kolumn, aby znacznie mniejsza niż optymalne 1 milion wierszy na grupę wierszy. Może to powodować również wiersze przejść do grupę wierszy delta, a nie grupą skompresowany wiersza.

### <a name="memory-pressure-when-index-was-built"></a>Wykorzystanie pamięci podczas kompilowania indeksu

Liczba wierszy na grupę wierszy skompresowany są bezpośrednio związane z szerokość wiersza i ilość pamięci przetworzyć grupę wierszy.  Jeśli wiersze są zapisywane w tabelach magazynu kolumn przy dużym wykorzystaniu pamięci, może to spowodować obniżenie jakości segmentów w magazynie kolumn.  W związku z tym najlepszym rozwiązaniem jest zapewnienie sesji, która zapisuje dostępu tabele indeksów magazynu kolumn do tak dużej ilości pamięci, jak to możliwe.  Ponieważ istnieje zależność między pamięcią i współbieżności, wskazówki na temat przydziału pamięci prawo zależy od danych w każdym wierszu tabeli, jednostki magazynu danych, które są przydzielane do systemu i liczby gniazd współbieżności można przekazać sesji której zapisuje dane do tabeli.

### <a name="high-volume-of-dml-operations"></a>Duża liczba operacji DML

Duża liczba operacji DML, aktualizowanie i usuwanie wierszy, które można wprowadzać nieefektywne podejście do magazynu kolumn. Jest to szczególnie istotne, po zmodyfikowaniu większość wierszy w grupy wierszy.

- Usuwanie wiersza z grupy wierszy skompresowany tylko logicznie oznacza wiersza, jako usunięty. Wiersz pozostaje w grupę wierszy skompresowany, dopóki nie zostanie odtworzony partycji lub tabeli.
- Wstawienie wiersza dodaje wiersz do tabeli wewnętrznej magazynu wierszy o nazwie delta grupę wierszy. Wstawionego wiersza nie jest konwertowana do magazynu kolumn, dopóki grupa wierszy delta jest zapełniony i jest oznaczony jako zamknięte. Grupy wierszy są zamykane, gdy osiągną maksymalną pojemność wynoszącą 1 048 576 wierszy.
- Aktualizowanie wierszy w formacie magazynu kolumn są przetwarzane jako logicznych delete, a następnie wstawiania. Wstawionego wiersza mogą być przechowywane w magazynie danych różnicowych.

Aktualizacji wsadowych i operacji wstawiania, które przekraczają próg zbiorcze 102 400 wierszy na dystrybucję wyrównany do partycji przejść bezpośrednio do formatu magazynu kolumn. Jednak przy założeniu równomiernego rozłożenia, musisz być modyfikowanie ponad 6.144 milionów wierszy w ramach jednej operacji, w tym celu. Jeśli liczba wierszy do danego punktu dystrybucji wyrównany do partycji jest mniejsza niż 102400 wiersze przejdź do magazynu delta i pozostają dostępne do momentu wystarczające wierszy zostało wstawionych lub zmodyfikować, aby zamknąć grupę wierszy lub indeks został przebudowany.

### <a name="small-or-trickle-load-operations"></a>Małe lub ścieknie operacje ładowania

Mała liczba godzin ładuje usługi flow do usługi SQL Data Warehouse czasami są znane jako ścieknie obciążeń. Zazwyczaj reprezentują niemal stały strumień danych jest przetwarzanych przez system. Jednak ten strumień jest niemal ciągłego ilości wierszy nie jest szczególnie dużych. Często dane są znacznie poniżej wartości progowej, wymaganych do bezpośredniego obciążenia do formatu magazynu kolumn.

W takich sytuacjach często jest lepszym rozwiązaniem trafić dane najpierw w usłudze Azure blob storage i pozwól mu accumulate przed załadowaniem. Ta technika jest często nazywane *mikropartie*.

### <a name="too-many-partitions"></a>Zbyt wiele partycji

Innym czynnikiem, który należy wziąć pod uwagę jest wpływ partycjonowania na tabel klastrowanego magazynu kolumn.  Przed partycjonowanie, usługa SQL Data Warehouse dzieli dane już na 60 baz danych.  Dodatkowo partycjonowanie dzieli dane.  Jeżeli partycjonowania danych należy rozważyć, **każdego** partycji wymaga co najmniej 1 milion wierszy, aby skorzystanie z klastrowanego indeksu magazynu kolumn.  Jeśli po utworzeniu partycji tabeli do 100 partycji, a następnie tabeli wymaga co najmniej 6 miliardów wierszy, aby skorzystanie z klastrowanego indeksu magazynu kolumn (60 dystrybucji *100 partycji* 1 milion wierszy). Jeśli tabela 100 partycji nie ma 6 miliardów wierszy, Zmniejsz liczbę partycji lub rozważ zamiast tego użycie tabeli stosu.

Po tabele zostały załadowane z danymi, wykonaj poniższe kroki, aby zidentyfikować i Odbuduj tabele zawierające optymalne klastrowane indeksy magazynu kolumn.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Ponowne tworzenie indeksów w celu zwiększenia jakości segmentów

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Krok 1: Określ lub Utwórz użytkownika, który używa klasy właściwy zasób

Szybkim sposobem natychmiast poprawiać jakość segmentu jest odbudowanie indeksu.  SQL zwrócone przez widok powyżej zwraca instrukcji ALTER INDEX REBUILD, która może służyć do odbudowywania indeksów. Podczas odbudowywania indeksów, pamiętaj, że przydzielić wystarczającej ilości pamięci do sesji, która odbudowania indeksu.  Aby to zrobić, należy zwiększyć klasy zasobów użytkownika, który ma uprawnienia do odbudowania indeksu w tej tabeli do minimum zalecane.

Poniżej znajduje się przykład jak przydzielanie większej ilości pamięci do użytkownika, zwiększając ich klasy zasobów. Aby pracować z klasami zasobów, zobacz [klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Krok 2: Odbuduj indeksy klastrowanego magazynu kolumn użytkownikowi wyższe klasy zasobów

Zaloguj się jako użytkownik z kroku 1 (np. LoadUser), która jest teraz przy użyciu z wyższą klasą zasobu, i wykonywanie instrukcji ALTER INDEX. Pamiętaj, że ten użytkownik ma uprawnienie ALTER do tabel, gdzie jest odbudować indeksu. Te przykłady przedstawiają sposób odbudowywania indeksu magazynu kolumn w całej lub sposobie odbudowania jednej partycji. W dużych tabel jest więcej praktyczne, aby ponownie utworzyć indeksy jednej partycji w danym momencie.

Alternatywnie zamiast odbudowanie indeksu, można skopiować tabelę do nowej tabeli [użycie instrukcji CTAS](sql-data-warehouse-develop-ctas.md). Jaki sposób jest najlepsza? Dla dużych ilości danych, jest zwykle szybsze niż CTAS [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql). Dla mniejszych ilości danych ALTER INDEX jest łatwiejsze w użyciu i nie wymagają wymienić w tabeli.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Ponowne tworzenie indeksu w usłudze SQL Data Warehouse jest operacją w trybie offline.  Aby uzyskać więcej informacji na temat ponowne tworzenie indeksów, zobacz sekcję ALTER INDEX REBUILD w [defragmentacji indeksów magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-defragmentation), i [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Krok 3: Upewnij się, że uległa poprawie jakości segmentów klastrowanego magazynu kolumn

Ponownie uruchom zapytanie tabeli, która zidentyfikowanego za pomocą niska jakość segmentu i sprawdź jakości segmentów uległa poprawie.  Jeżeli nie poprawiła się jakość segmentu, może to być, że wiersze w tabeli są bardzo szerokie.  Należy rozważyć użycie wyższą klasą zasobu lub jednostek DWU, gdy ponowne tworzenie indeksów.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Ponowne tworzenie indeksów za pomocą instrukcji CTAS i przełączanie partycji

W tym przykładzie użyto [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) instrukcji i przełączania odbudowania partycji tabeli partycji.

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

Aby uzyskać więcej szczegółów na temat następuje ponowne tworzenie partycji za pomocą instrukcji CTAS zobacz [za pomocą partycji w usłudze SQL Data Warehouse](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat tworzenia tabel, zobacz [tworzenia tabel](sql-data-warehouse-tables-overview.md).
