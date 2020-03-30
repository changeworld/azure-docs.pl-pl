---
title: Wskazówki dotyczące projektowania tabel rozproszonych
description: Zalecenia dotyczące projektowania tabel rozproszonych i okrężnych w usłudze SQL Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 35106e73a3a4a143bf22c72c4fe8ac6798ac5219
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351339"
---
# <a name="guidance-for-designing-distributed-tables-in-sql-analytics"></a>Wskazówki dotyczące projektowania tabel rozproszonych w usłudze SQL Analytics
Zalecenia dotyczące projektowania tabel rozproszonych i okrężnych w usłudze SQL Analytics.

W tym artykule założono, że znasz pojęcia dotyczące dystrybucji danych i przenoszenia danych w usłudze SQL Analytics.Aby uzyskać więcej informacji, zobacz [SQL Analytics masowo równoległego przetwarzania (MPP) architektura](massively-parallel-processing-mpp-architecture.md). 

## <a name="what-is-a-distributed-table"></a>Co to jest tabela rozproszona?
Tabela rozproszona jest wyświetlana jako pojedyncza tabela, ale wiersze są faktycznie przechowywane w 60 dystrybucjach. Wiersze są dystrybuowane za pomocą algorytmu mieszania lub okrężnego.  

**Tabele rozproszone skrótami** zwiększają wydajność kwerend w dużych tabelach faktów i są głównym tematem tego artykułu. **Stoły okrężne** są przydatne do poprawy szybkości ładowania. Te opcje projektowania mają znaczący wpływ na poprawę wydajności zapytań i ładowania.

Inną opcją przechowywania tabeli jest replikowanie małej tabeli we wszystkich węzłach obliczeniowych. Aby uzyskać więcej informacji, zobacz [Wskazówki dotyczące projektowania tabel replikowanych](design-guidance-for-replicated-tables.md). Aby szybko wybrać jedną z trzech opcji, zobacz Tabele rozproszone w [przeglądzie tabel](sql-data-warehouse-tables-overview.md). 

W ramach projektowania tabeli, zrozumieć jak najwięcej o danych i jak dane są poszukiwane.Rozważmy na przykład następujące pytania:

- Jak duży jest stół?   
- Jak często tabela jest odświeżana?   
- Czy w bazie danych usługi SQL Analytics są wylić tabele faktów i wymiarów?   


### <a name="hash-distributed"></a>Mieszanie dystrybuowane
Tabela rozproszona mieszania rozmieszcza wiersze tabeli w węzłach obliczeniowych za pomocą deterministycznej funkcji mieszania, aby przypisać każdy wiersz do jednej [dystrybucji](massively-parallel-processing-mpp-architecture.md#distributions). 

![Tabela rozproszona](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "Tabela rozproszona")  

Ponieważ identyczne wartości zawsze mieszają się do tej samej dystrybucji, usługa SQL Analytics ma wbudowaną wiedzę na temat lokalizacji wierszy. Usługa SQL Analytics wykorzystuje tę wiedzę do zminimalizowania przenoszenia danych podczas kwerend, co zwiększa wydajność zapytań. 

Tabele rozproszone skrótami działają dobrze w przypadku dużych tabel faktów w schemacie gwiazdy. Mogą mieć bardzo dużą liczbę wierszy i nadal osiągnąć wysoką wydajność. Istnieją, oczywiście, pewne zagadnienia projektowe, które pomogą Ci uzyskać wydajność rozproszonego systemu jest przeznaczony do zapewnienia. Wybór dobrej kolumny dystrybucji jest jednym z takich rozważań, które jest opisane w tym artykule. 

Należy rozważyć użycie tabeli rozproszonej skrótu, gdy:

- Rozmiar tabeli na dysku wynosi więcej niż 2 GB.
- Tabela ma częste operacje wstawiania, aktualizowania i usuwania. 

### <a name="round-robin-distributed"></a>Dystrybuowane okrężne
Tabela rozproszona okrężnym rozmieszcza wiersze tabeli równomiernie we wszystkich dystrybucjach. Przypisanie wierszy do dystrybucji jest losowe. W przeciwieństwie do tabel rozproszonych mieszania wiersze o równych wartościach nie są gwarantowane do przypisania do tego samego rozkładu. 

W rezultacie system czasami musi wywołać operację przenoszenia danych, aby lepiej zorganizować dane, zanim będzie można rozwiązać kwerendę.  Ten dodatkowy krok może spowolnić zapytania. Na przykład dołączenie do tabeli okrężnego zwykle wymaga przetasowania wierszy, co jest trafieniem wydajności.

Rozważ użycie dystrybucji okrężkowej dla tabeli w następujących scenariuszach:

- Po rozpoczęciu pracy jako prosty punkt wyjścia, ponieważ jest to
- Jeśli nie ma oczywistego klucza łączącego
- Jeśli nie ma dobrej kolumny kandydata do dystrybucji skrótu tabeli
- Jeśli tabela nie udostępnia wspólnego klucza sprzężenia z innymi tabelami
- Jeśli sprzężenie jest mniej istotne niż inne sprzężenia w kwerendzie
- Gdy tabela jest tymczasową tabelą przemieszczania

Samouczek [Załaduj dane taksówek w Nowym Jorku](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) podaje przykład ładowania danych do tabeli przejściowej okrężnej w usłudze SQL Analytics.


## <a name="choosing-a-distribution-column"></a>Wybieranie kolumny dystrybucji
Tabela rozproszona mieszania ma kolumnę dystrybucyjną, która jest kluczem skrótu. Na przykład poniższy kod tworzy tabelę rozproszoną mieszania z ProductKey jako kolumną dystrybucyjną.

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

Wybranie kolumny dystrybucyjnej jest ważną decyzją projektową, ponieważ wartości w tej kolumnie określają sposób dystrybucji wierszy. Najlepszy wybór zależy od kilku czynników i zwykle wiąże się z kompromisami. Jeśli jednak nie wybierzesz najlepszej kolumny po raz pierwszy, możesz użyć [opcji UTWÓRZ TABELĘ JAKO WYBIERZ (CTAS),](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) aby ponownie utworzyć tabelę z inną kolumną dystrybucyjną. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Wybieranie kolumny dystrybucyjnej, która nie wymaga aktualizacji
Nie można zaktualizować kolumny dystrybucji, chyba że usuniesz wiersz i wstawisz nowy wiersz ze zaktualizowanymi wartościami. W związku z tym należy wybrać kolumnę z wartościami statycznymi. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Wybieranie kolumny dystrybucyjnej z danymi, które rozmieszczają się równomiernie

Aby uzyskać najlepszą wydajność, wszystkie dystrybucje powinny mieć w przybliżeniu taką samą liczbę wierszy. Gdy jeden lub więcej dystrybucji ma nieproporcjonalną liczbę wierszy, niektóre dystrybucje zakończyć ich część zapytania równoległego przed innymi. Ponieważ kwerenda nie może zakończyć, dopóki wszystkie dystrybucje nie zakończą przetwarzania, każde zapytanie jest tylko tak szybkie, jak najwolniejsza dystrybucja.

- Pochylenie danych oznacza, że dane nie są równomiernie rozłożone w dystrybucjach
- Przetwarzanie pochylenia oznacza, że niektóre dystrybucje trwać dłużej niż inne podczas uruchamiania zapytań równoległych. Może się to zdarzyć, gdy dane są wypaczone.
  
Aby zrównoważyć przetwarzanie równoległe, wybierz kolumnę dystrybucyjną, która:

- **Ma wiele unikalnych wartości.** Kolumna może mieć kilka zduplikowanych wartości. Jednak wszystkie wiersze o tej samej wartości są przypisane do tej samej dystrybucji. Ponieważ istnieje 60 dystrybucji, kolumna powinna mieć co najmniej 60 unikatowych wartości.  Zazwyczaj liczba unikatowych wartości jest znacznie większa.
- **Nie ma NULLs, lub ma tylko kilka NULLs.** Na przykład skrajne, jeśli wszystkie wartości w kolumnie są NULL, wszystkie wiersze są przypisane do tego samego rozkładu. W rezultacie przetwarzanie zapytań jest skośny do jednej dystrybucji i nie korzysta z przetwarzania równoległego. 
- **Nie jest kolumną daty**. Wszystkie dane dla tej samej daty lądują w tej samej dystrybucji. Jeśli kilku użytkowników filtruje w tym samym dniu, tylko 1 z 60 dystrybucji wykonuje całą pracę przetwarzania. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Wybieranie kolumny dystrybucyjnej minimalizowanie przenoszenia danych

Aby uzyskać poprawne kwerendy wynik kwerendy mogą przenosić dane z jednego węzła obliczeniowego do innego. Przenoszenie danych często dzieje się, gdy kwerendy mają sprzężenia i agregacji w tabelach rozproszonych. Wybranie kolumny dystrybucyjnej, która pomaga zminimalizować przenoszenie danych, jest jedną z najważniejszych strategii optymalizacji wydajności bazy danych sql analytics.

Aby zminimalizować przenoszenie danych, wybierz kolumnę dystrybucyjną, która:

- Jest używany `JOIN` `GROUP BY`w `DISTINCT` `OVER`, `HAVING` , , i klauzul. Gdy dwie duże tabele faktów mają częste sprzężenia, wydajność kwerendy zwiększa się podczas dystrybucji obu tabel w jednej z kolumn sprzężenia.  Gdy tabela nie jest używana w sprzężeniach, należy rozważyć `GROUP BY` rozmieszczenie tabeli w kolumnie, która jest często w klauzuli.
- *Nie* jest `WHERE` używany w klauzulach. Może to zawęzić kwerendę, aby nie działać na wszystkich dystrybucjach. 
- *Nie* jest kolumną daty. Klauzule WHERE często filtrują według daty.  W takim przypadku wszystkie przetwarzanie może działać tylko na kilka dystrybucji.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Co zrobić, gdy żadna z kolumn nie jest dobrą kolumną dystrybucji

Jeśli żadna z kolumn nie ma wystarczającej liczby odrębnych wartości dla kolumny dystrybucyjnej, można utworzyć nową kolumnę jako złożoną z jednej lub więcej wartości. Aby uniknąć przenoszenia danych podczas wykonywania kwerendy, należy użyć kolumny dystrybucji złożonej jako kolumny sprzężenia w kwerendach.

Po zaprojektowaniu tabeli rozproszonej skrótu następnym krokiem jest załadowanie danych do tabeli.  Aby uzyskać wskazówki dotyczące ładowania, zobacz [Omówienie ładowania](design-elt-data-loading.md). 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Jak sprawdzić, czy kolumna dystrybucji jest dobrym wyborem
Po załadowaniu danych do tabeli rozproszonej mieszania, sprawdź, jak równomiernie wiersze są rozłożone na 60 dystrybucji. Wiersze na dystrybucję mogą się różnić do 10% bez zauważalnego wpływu na wydajność. 

### <a name="determine-if-the-table-has-data-skew"></a>Określanie, czy w tabeli jest skośne dane
Szybkim sposobem sprawdzenia pochylenia danych jest użycie [PDW_SHOWSPACEUSED DBCC](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). Poniższy kod SQL zwraca liczbę wierszy tabeli, które są przechowywane w każdej z 60 dystrybucji. Aby uzyskać zrównoważoną wydajność, wiersze w tabeli rozproszonej powinny być rozłożone równomiernie na wszystkie dystrybucje.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Aby określić, które tabele mają więcej niż 10% pochylenia danych:

1. Utwórz widok dbo.vTableSizes, który jest wyświetlany w artykule [Omówienie tabel.](sql-data-warehouse-tables-overview.md#table-size-queries)  
2. Uruchom zapytanie:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Sprawdzanie planów kwerend dotyczących przenoszenia danych
Dobra kolumna dystrybucji umożliwia sprzężenia i agregacji, aby mieć minimalny ruch danych. Wpływa to na sposób sprzężenia powinny być zapisywane. Aby uzyskać minimalny ruch danych dla sprzężenia w dwóch tabelach rozproszonych mieszania, jedną z kolumn sprzężenia musi być kolumna dystrybucji.  Gdy dwie tabele rozproszone skrótem łączą się w kolumnie dystrybucyjnej tego samego typu danych, sprzężenie nie wymaga przenoszenia danych. Sprzężenia można używać dodatkowych kolumn bez ponoszenia przenoszenia danych.

Aby uniknąć przenoszenia danych podczas sprzężenia:

- Tabele zaangażowane w sprzężenie muszą być rozmieszczone w **jednej z** kolumn uczestniczących w sprzężeniu.
- Typy danych kolumn sprzężenia muszą być zgodne między obiema tabelami.
- Kolumny muszą być połączone z operatorem równym.
- Typ sprzężenia `CROSS JOIN`może nie być .

Aby sprawdzić, czy w kwerendach występują przenoszenie danych, można przyjrzeć się planowi kwerend.  


## <a name="resolve-a-distribution-column-problem"></a>Rozwiązywanie problemu z kolumną dystrybucyjną
Nie jest konieczne, aby rozwiązać wszystkie przypadki pochylenia danych. Dystrybucja danych polega na znalezieniu właściwej równowagi między minimalizowaniem pochylenia danych a przenoszeniem danych. Nie zawsze jest możliwe zminimalizowanie pochylenia danych i przenoszenia danych. Czasami korzyści z minimalnego przenoszenia danych może przeważać nad wpływem konieczności pochylenia danych.

Aby zdecydować, czy należy rozwiązać pochylenie danych w tabeli, należy zrozumieć jak najwięcej o woluminach danych i kwerend w obciążeniu. Kroki opisane w [artykule Monitorowanie kwerend](sql-data-warehouse-manage-monitor.md) można użyć do monitorowania wpływu pochylenia na wydajność kwerendy. W szczególności należy sprawdzić, jak długo trwa duże kwerendy, aby zakończyć na poszczególnych dystrybucjach.

Ponieważ nie można zmienić kolumny dystrybucji w istniejącej tabeli, typowym sposobem rozwiązania pochylenia danych jest ponowne utworzenie tabeli z inną kolumną dystrybucyjną.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Ponowne tworzenie tabeli z nową kolumną dystrybucji
W tym przykładzie użyto [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) do ponownego utworzenia tabeli z inną kolumną dystrybucji mieszania.

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

## <a name="next-steps"></a>Następne kroki

Aby utworzyć tabelę rozproszoną, użyj jednej z następujących instrukcji:

- [TWORZENIE TABELI (SQL Analytics)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [TWORZENIE TABELI JAKO WYBIERZ (SQL Analytics)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


