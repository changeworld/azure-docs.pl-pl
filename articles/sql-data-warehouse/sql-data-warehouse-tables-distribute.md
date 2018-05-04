---
title: Tabele rozproszonej projektowania wskazówki - Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zalecenia dotyczące projektowania rozpowszechniane skrót i okrężnego rozproszonej tabel w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: d65ca91fc4cffa53adf3a7c56c7919e46c5037d9
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="guidance-for-designing-distributed-tables-in-azure-sql-data-warehouse"></a>Wskazówki dotyczące projektowania rozproszonych tabel w magazynie danych SQL Azure
Zalecenia dotyczące projektowania rozpowszechniane skrót i okrężnego rozproszonej tabel w usłudze Azure SQL Data Warehouse.

W tym artykule przyjęto założenie, że znasz z danych dystrybucji i koncepcje przepływu danych w usłudze SQL Data Warehouse.  Aby uzyskać więcej informacji, zobacz [magazyn danych SQL Azure - Massively Parallel Processing () architektura MPP](massively-parallel-processing-mpp-architecture.md). 

## <a name="what-is-a-distributed-table"></a>Co to jest tabela rozproszona?
Tabela rozproszona jest wyświetlany jako pojedynczej tabeli, ale wiersze są przechowywane przez 60 dystrybucji. Wiersze są dystrybuowane za pomocą skrótu lub okrężnego algorytmu.  

**Tabele rozpowszechniane skrót** poprawić wydajność kwerend w tabelach faktów dużych i koncentrują się na części tego artykułu. **Tabele okrężnego** służą do poprawy szybkości ładowania. Tych decyzji projektowych mieć znaczący wpływ na poprawę zapytania i ładowanie wydajności.

Inną opcją magazynu tabeli jest replikowanie małej tabeli na wszystkich węzłów obliczeniowych. Aby uzyskać więcej informacji, zobacz [projekt, wskazówki dotyczące zreplikowanych tabel](design-guidance-for-replicated-tables.md). Aby szybko wybrać spośród trzech opcji, zobacz tabele rozproszonych w [Omówienie tabel](sql-data-warehouse-tables-overview.md). 

W ramach projektowaniu tabel Dowiedz się, jak to możliwe danych i jak danych jest poddawany kwerendzie.  Na przykład wziąć pod uwagę następujące pytania:

- Jak duże jest tabela?   
- Częstotliwość odświeżania tabeli?   
- Tabele faktów i wymiarów są dostępne w magazynie danych?   


### <a name="hash-distributed"></a>Skrót rozproszonych
Tabela rozpowszechniane skrót dystrybuuje wiersze tabeli na węzły obliczeniowe przy użyciu funkcji skrótu deterministyczne można przypisać każdego wiersza do jednego [dystrybucji](massively-parallel-processing-mpp-architecture.md#distributions). 

![Tabela rozproszone](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "tabeli rozproszonych")  

Ponieważ identyczne wartości skrótów zawsze do tej samej dystrybucji, w magazynie danych ma wbudowane wiedzy na temat lokalizacji wiersza. SQL Data Warehouse używa wiedzy, aby zminimalizować przenoszenia danych podczas wykonywania kwerend, co zwiększa wydajność zapytań. 

Tabele rozpowszechniane skrót działa również dla tabel faktów dużych w schemat gwiazdy. Mogą być bardzo dużą liczbę wierszy i nadal osiągnięcia wysokiej wydajności. Brak, niektóre kwestie, które ułatwiają wydajność, który pozwala uzyskać rozproszonego systemu. Wybór kolumny dobrej jest pierwsza kwestia, które jest opisane w tym artykule. 

Należy rozważyć użycie rozpowszechniane skrót tabeli, gdy:

- Rozmiar tabeli na dysku jest więcej niż 2 GB.
- Tabela zawiera częste wstawiania, aktualizowania i usuwania działań. 

### <a name="round-robin-distributed"></a>Działanie okrężne rozproszonych
Tabela rozproszona okrężnego rozdziela wiersze tabeli równomiernie między wszystkie dystrybucje. Przypisanie wierszy podziału jest losowych. W odróżnieniu od rozpowszechniane skrót tabel wiersze z równe wartości nie ma gwarancji do przypisania do tego samego rozkładu. 

W związku z tym system musi czasami do wywołania operacji przepływu danych w celu lepszej organizacji danych przed może rozpoznać zapytania.  Ten krok dodatkowe może to spowolnić zapytań. Na przykład przyłączenie tabeli okrężnego zwykle wymaga losowego grupowania wierszy, która jest trafień wydajności.

Rozważ użycie rozdzielanie dla tej tabeli w następujących scenariuszach:

- Gdy wprowadzenie jako punktu wyjścia proste, ponieważ jest ono domyślnie
- Jeśli oczywiste łącząca klucz nie istnieje
- Jeśli nie ma kolumny odpowiednimi kandydatami do wyznaczania wartości skrótu dystrybucji tabeli
- Jeśli tabela nie udostępniane wspólny klucz sprzężenia innych tabel
- Jeśli sprzężenie jest mniej istotna niż inne sprzężeń w zapytaniu
- Gdy jest tymczasowe Tabela przemieszczania

Samouczek [obciążenia Nowy Jork taxicab danych Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) przedstawiono przykładowy podczas ładowania danych do tabeli tymczasowej okrężnego.


## <a name="choosing-a-distribution-column"></a>Wybieranie kolumn dystrybucji
Tabela rozpowszechniane skrót ma kolumnę dystrybucji, która jest klucza mieszającego. Na przykład poniższy kod tworzy tabelę rozpowszechniane skrót z ProductKey jako kolumnę dystrybucji.

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
``` 

Wybieranie kolumn dystrybucji jest decyzji projektowych ważne, ponieważ wartości w tej kolumnie określić, jak wiersze są dystrybuowane. Najlepszym rozwiązaniem zależy od wielu czynników i zwykle obejmuje wady i zalety. Jednak jeśli nie wybierzesz najlepsze kolumny po raz pierwszy, można użyć [Tworzenie tabeli jako wybierz (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) ponownie utworzyć tabelę z kolumną różnych dystrybucji. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Wybierz kolumnę dystrybucji, który nie wymaga aktualizacji
Nie można zaktualizować kolumn dystrybucji, chyba że w przypadku usunięcia wiersza i wstawić nowy wiersz przez zaktualizowane wartości. W związku z tym wybrać kolumnę o wartości statyczne. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Wybierz kolumnę z danymi, który równomiernie rozdziela dystrybucji

Aby uzyskać najlepszą wydajność wszystkie dystrybucje ma mniej więcej taką samą liczbę wierszy. Jeśli co najmniej jeden dystrybucje nieproporcjonalnie liczbę wierszy, niektórych dystrybucji kończy się ich część równoległe zapytania przed pozostałymi. Ponieważ zapytanie nie może ukończyć, dopóki wszystkie dystrybucje przetwarzanie zostało ukończone, każdego zapytania jest tylko tak szybko, jak najwolniejsze dystrybucji.

- Pochylenie danych oznacza, że dane nie są równomiernie między dystrybucje
- Przetwarzanie pochylenia oznacza, że niektórych dystrybucji trwać dłużej niż inne, podczas uruchamiania zapytań równoległych. Może to się zdarzyć, gdy danych jest niesymetryczna.
  
Aby równoważenie przetwarzanie równoległe, wybierz kolumnę dystrybucji który:

- **Ma wiele unikatowych wartości.** Kolumna może zawierać niektórych zduplikowane wartości. Jednak wszystkie wiersze z tej samej wartości są przypisane do tego samego rozkładu. Ponieważ 60 dystrybucje, kolumnie powinien mieć co najmniej 60 unikatowe wartości.  Liczba unikatowych wartości jest zwykle znacznie większe.
- **Nie ma wartości null lub zawiera tylko kilka wartości null.** Na przykład skrajne Jeśli wszystkie wartości w kolumnie ma wartość NULL, wszystkie wiersze są przypisane do tego samego rozkładu. W związku z tym przetwarzania zapytania jest niesymetryczna do jednego dystrybucji, a nie korzysta z przetwarzania równoległego. 
- **Nie jest to kolumna data**. Wszystkie dane dla tego samego dnia znajdzie się w tej samej dystrybucji. Jeśli wielu użytkowników są filtrowanie tego samego dnia, tylko 1 60 dystrybucji czy pracy przetwarzania. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Wybierz kolumnę dystrybucji, który minimalizuje przepływu danych

Aby uzyskać odpowiednie zapytanie wyników może przenoszeniu danych z jednego węzła obliczeń. Przenoszenie danych odbywa się często, podczas zapytania ma sprzęgania i agregacji w tabelach rozproszonych. Wybieranie kolumny dystrybucji, który minimalizuje przenoszenia danych jest jednym z najważniejszych strategii dla optymalizacji wydajności magazynu danych SQL.

Aby zminimalizować przenoszenia danych, wybierz kolumnę dystrybucji który:

- Jest używany w `JOIN`, `GROUP BY`, `DISTINCT`, `OVER`, i `HAVING` klauzul. Jeśli dwie tabele faktów dużych częste sprzężenia, wydajność zapytań zwiększa dystrybucję obu tabel w jednej z kolumn sprzężenia.  Jeśli tabela nie jest używana w sprzężeniu, należy wziąć pod uwagę dystrybucja tabelę według kolumny, która jest często w `GROUP BY` klauzuli.
- Jest *nie* używane w `WHERE` klauzul. Może to ograniczyć zapytanie, aby nie uruchamiać na wszystkich dystrybucji. 
- Jest *nie* kolumny daty. Klauzulach WHERE często filtrować według daty.  W takim przypadku wszystkie przetwarzania można uruchomić na tylko kilka dystrybucji.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Co należy zrobić, gdy żaden z kolumn nie jest kolumną dobrej

Jeśli żadna z kolumn nie ma za mało odrębnych wartości w kolumnie dystrybucji, można utworzyć nową kolumnę jako złożone z co najmniej jedną wartość. Aby uniknąć przenoszenia danych podczas wykonywania zapytania, należy użyć kolumny dystrybucji złożonego jako kolumnę sprzężenia w zapytaniach.

Po projektowania tabeli rozpowszechniane skrót, następnym krokiem jest ładowanie danych do tabeli.  Ładowania wskazówki, zobacz [omówienie ładowania](sql-data-warehouse-overview-load.md). 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Jak sprawdzić, czy kolumny dystrybucji jest dobrym rozwiązaniem
Po załadowaniu danych do tabeli rozproszonych wyznaczania wartości skrótu, należy sprawdzić, jak wiersze są równomiernie na 60 dystrybucji. Liczba wierszy przypadających na dystrybucji może się różnić maksymalnie 10% bez zauważalnego wpływu na wydajność. 

### <a name="determine-if-the-table-has-data-skew"></a>Określa, czy tabela ma pochylanie danych
Szybkim sposobem sprawdzenia zegara danych jest użycie [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). Poniższy kod SQL zwraca liczbę wierszy tabeli, które są przechowywane w każdym z 60 dystrybucji. Wydajność ze zrównoważonym wierszy w tabeli rozproszonej należy można rozmieszczone równomiernie w obrębie wszystkie dystrybucje.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Aby zidentyfikować tabel, które mają więcej niż 10% danych zegara:

1. Utwórz dbo.vTableSizes widoku, który jest wyświetlany w obszarze [Omówienie tabel](sql-data-warehouse-tables-overview.md#table-size-queries) artykułu.  
2. Uruchom następującą kwerendę:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Sprawdź planów zapytań dla przepływu danych
Kolumna dobrej umożliwia sprzęgania i agregacji, aby przenoszenie danych minimalny. Ma to wpływ na sposób sprzężenia powinna być zapisana. Uzyskanie przepływu minimalna ilość danych w celu utworzenia sprzężenia w dwóch tabelach rozpowszechniane skrót jednej z kolumn sprzężenia musi być kolumna dystrybucji.  Nowi dwóch tabel rozproszonych wyznaczania wartości skrótu dla tego samego typu danych kolumny dystrybucji sprzężenia nie wymaga przenoszenia danych. Sprzężenia można użyć dodatkowych kolumn bez ponoszenia przenoszenia danych.

Aby uniknąć przenoszenia danych podczas sprzężenia:

- Tabele uwzględnione w sprzężeniu musi być rozproszone na skrót **jeden** kolumn uczestniczących w sprzężeniu.
- Typy danych kolumn sprzężenia musi odpowiadać między obu tabel.
- Kolumny musi być połączony z operatora równości.
- Typ sprzężenia nie może być `CROSS JOIN`.

Aby zobaczyć, czy zapytania występują przenoszenia danych, mogą zobaczyć w planie zapytania.  


## <a name="resolve-a-distribution-column-problem"></a>Rozwiąż problem kolumny dystrybucji
Nie jest konieczne rozpoznać pochylanie wszystkich przypadkach danych. Dystrybucji danych polega na znajdowaniu kompromisu między minimalizując pochylenia danych i przenoszenia danych. Nie zawsze jest możliwe zminimalizować zarówno zegara danych i przenoszenia danych. Czasami o konieczności przeniesienia minimalna ilość danych może być większe niż wpływ danych pochylanie.

Podjęcie decyzji, czy należy poprawić danych pochylenia w tabeli, należy zrozumieć jak to możliwe ilości danych i zapytania w obciążenie. Można użyć procedury w [monitorowania zapytania](sql-data-warehouse-manage-monitor.md) artykuł, aby monitorować wpływ pochylenia wzdłuż wydajności zapytania. W szczególności poszukaj jak długo trwa dużych kwerend w poszczególnych dystrybucji.

Ponieważ nie można zmienić kolumny dystrybucji w istniejącej tabeli, typowy sposób rozwiązania zegara danych jest ponownie utwórz tabelę z kolumną różnych dystrybucji.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Ponownie utwórz tabelę z kolumną nowe dystrybucji
W tym przykładzie użyto [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) ponownie utworzyć tabelę z kolumną dystrybucji różnych wyznaczania wartości skrótu.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

## <a name="next-steps"></a>Kolejne kroki

Aby utworzyć rozproszoną tabelę, użyj jednej z tych instrukcji:

- [Utwórz tabelę (magazyn danych Azure SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [Utwórz TABLE AS SELECT (magazyn danych Azure SQL](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


