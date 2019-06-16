---
title: Rozproszone tabele projektowania wskazówki — Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zalecenia dotyczące projektowania rozproszonych wyznaczania wartości skrótu i działanie okrężne rozproszone tabele w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: b101a4e19d00d44805c7eb5f44d449a18d756804
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65851617"
---
# <a name="guidance-for-designing-distributed-tables-in-azure-sql-data-warehouse"></a>Wskazówki dotyczące projektowania rozproszone tabele w usłudze Azure SQL Data Warehouse
Zalecenia dotyczące projektowania rozproszonych wyznaczania wartości skrótu i działanie okrężne rozproszone tabele w usłudze Azure SQL Data Warehouse.

W tym artykule przyjęto założenie, że czytelnik zna dystrybucję danych i pojęcia przenoszenia danych w usłudze SQL Data Warehouse.  Aby uzyskać więcej informacji, zobacz [usługi Azure SQL Data Warehouse — architektura masowego równoległego przetwarzania (MPP)](massively-parallel-processing-mpp-architecture.md). 

## <a name="what-is-a-distributed-table"></a>Co to jest dystrybuowanej tabeli?
Rozproszona tabela jest wyświetlany jako pojedynczej tabeli, ale wiersze są przechowywane przez 60 dystrybucji. Wiersze są dystrybuowane za pomocą skrótu lub algorytm działanie okrężne.  

**Tabele rozproszone wyznaczania wartości skrótu** poprawić wydajność zapytań na dużych tabel faktów i są celem tego artykułu. **Tabele wykorzystujące działanie okrężne** są przydatne do poprawiania szybkość ładowania. Tych decyzji projektowych mieć znaczący wpływ na zapytania i rozszerzają zakres wydajność ładowania.

Inną opcja magazynowania w tabeli jest replikowanie małej tabeli we wszystkich węzłach obliczeniowych. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące zreplikowane tabele projektowania](design-guidance-for-replicated-tables.md). Aby szybko wybrać spośród trzech opcji, zobacz tabele rozproszone w [Omówienie tabel](sql-data-warehouse-tables-overview.md). 

W ramach projektowaniu tabel Dowiedz się, jak to możliwe, o danych i jak jest wykonywane zapytanie danych.  Na przykład należy wziąć pod uwagę następujące pytania:

- Jak duże jest tabela?   
- Częstotliwość odświeżania tabeli?   
- Czy mam tabelami faktów i wymiarów w magazynie danych?   


### <a name="hash-distributed"></a>Skrót rozproszonych
Tabelę dystrybucji skrótów dystrybuuje wiersze tabeli w węzłach obliczeniowych za pomocą funkcji skrótu deterministyczne można przypisać każdego wiersza na jedną [dystrybucji](massively-parallel-processing-mpp-architecture.md#distributions). 

![Tabela rozproszonych](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "tabeli rozproszonych")  

Ponieważ identycznych wartości skrótów zawsze do tej samej dystrybucji, w magazynie danych ma wbudowaną wiedzę lokalizacje wiersza. SQL Data Warehouse używa tej wiedzy, aby zminimalizować przenoszenia danych podczas wykonywania kwerend, co zwiększa wydajność zapytań. 

Tabele rozproszone wyznaczania wartości skrótu działa dobrze w przypadku dużych tabel faktów w schemacie gwiazdy. Mogą być bardzo dużą liczbę wierszy i nadal osiąga wysoką wydajność. Istnieją, oczywiście, niektóre zagadnienia dotyczące projektowania, które ułatwiają uzyskanie systemu rozproszonego zaprojektowano pod kątem wydajności. Wybór kolumny dystrybucji dobre jest pierwsza kwestia, która jest opisane w tym artykule. 

Należy wziąć pod uwagę przy użyciu skrótu rozproszonych tabeli, gdy:

- Rozmiar tabeli na dysku jest większy niż 2 GB.
- Tabela ma często wstawiania, aktualizowania i usuwania działań. 

### <a name="round-robin-distributed"></a>Działanie okrężne rozproszonych
Działanie okrężne dystrybuowanej tabeli dystrybuuje wiersze tabeli równomiernie we wszystkich dystrybucjach. Przypisanie wierszy do dystrybucji jest losowa. W przeciwieństwie do wyznaczania wartości skrótu, rozproszone tabele wiersze z równe wartości nie ma gwarancji można przypisać do tego samego dystrybucji. 

W rezultacie system musi czasami do wywołania operacji przenoszenia danych, aby lepiej zorganizować dane, zanim może rozpoznać kwerendy.  Ten dodatkowy krok może spowolnić wykonywanie zapytań. Na przykład przyłączenie tabeli działanie okrężne zwykle wymaga losowego grupowania wierszy, czyli trafień wydajności.

Należy rozważyć zastosowanie rozdzielanie tabeli w następujących scenariuszach:

- Rozpoczynania pracy jako punktu wyjścia proste, ponieważ jest ono domyślnie
- Jeśli oczywiste przyłączany klucz nie istnieje
- Jeśli nie jest dobrym kandydatem kolumny Dystrybucja w tabeli wyznaczania wartości skrótu
- Jeśli tabela nie udostępnia wspólnego klucza łączenia z innymi tabelami
- Jeśli sprzężenia jest mniej istotne niż inne sprzężeń w zapytaniu
- Jeśli tabela jest tabeli tymczasowej

Samouczek [obciążenia Nowy Jork dane dotyczące taksówek w usłudze Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) podaje przykład ładowania danych do tabeli przejściowej działanie okrężne.


## <a name="choosing-a-distribution-column"></a>Wybór kolumny dystrybucji
Rozproszone wyznaczania wartości skrótu tabela ma kolumny dystrybucji, która jest kluczem skrótu. Na przykład poniższy kod tworzy tabelę rozproszonych wyznaczania wartości skrótu z ProductKey jako kolumna dystrybucji.

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

Wybór kolumny dystrybucji jest decyzja projektowa ważne, ponieważ wartości w tej kolumnie określić rozkład wierszy. Najlepszy wybór zależy od wielu czynników i zazwyczaj wymaga stosowania kompromisów. Jednakże, jeśli nie wybierzesz najlepszą kolumny po raz pierwszy, może użyć [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) ponownie utworzyć tabelę z kolumną różnych dystrybucji. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Wybierz kolumnę dystrybucji, który nie wymaga aktualizacji
Nie można zaktualizować kolumny dystrybucji, jeżeli nie można usunąć wiersz i wstawić nowy wiersz ze zaktualizowanymi wartościami. W związku z tym wybierz kolumnę z wartościami statycznymi. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Wybierz kolumnę z danymi, która będzie dystrybuować równomiernie dystrybucji

Aby uzyskać najlepszą wydajność wszystkie dystrybucje powinny mieć mniej więcej taką samą liczbę wierszy. Gdy co najmniej jeden dystrybucji mają nieproporcjonalnie duża liczba wierszy, niektóre dystrybucje zakończone ich część zapytanie równoległe przed pozostałymi. Ponieważ zapytanie nie może ukończyć, dopóki wszystkie dystrybucje zakończeniu przetwarzania, każda kwerenda jest tylko tak szybko, jak najwolniejsze dystrybucji.

- Niesymetryczność danych oznacza, że dane nie są rozkładane równomiernie między dystrybucjami
- Przetwarzanie niesymetryczność oznacza, że niektórych dystrybucji trwać dłużej niż inne, podczas uruchamiania zapytań równoległych. Może to nastąpić, gdy dane są nierówne.
  
Równoważyć przetwarzania równoległego, wybierz kolumnę dystrybucji który:

- **Ma wiele unikatowych wartości.** Kolumna może mieć kilka zduplikowanych wartości. Jednak wszystkie wiersze z tej samej wartości są przypisane do tego samego dystrybucji. Ponieważ 60 dystrybucji, kolumna powinna zawierać co najmniej 60 unikatowe wartości.  Zazwyczaj liczbie unikatowych wartości jest znacznie większa.
- **Nie ma wartości null lub ma tylko kilka wartości null.** Przykład ekstremalnych Jeśli wszystkie wartości w kolumnie ma wartość NULL, wszystkie wiersze są przypisane do tego samego dystrybucji. W wyniku przetwarzania zapytania jest niesymetryczna do dystrybucjami, a nie korzysta z przetwarzania równoległego. 
- **Nie jest kolumną daty**. Wszystkie dane dla tego samego dnia znajdzie się w tej samej dystrybucji. Jeśli wielu użytkowników są filtrowanie tego samego dnia, tylko 1 60 dystrybucji wykonanie całąj pracy przetwarzania. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Wybierz kolumnę dystrybucji, które minimalizuje przenoszenie danych

Aby uzyskać odpowiednie zapytanie wynik zapytania przenieść dane z jednego węzła obliczeniowego do innego. Przenoszenie danych często występuje, gdy zapytania mają sprzęgania i agregacji w tabelach rozproszonych. Wybór kolumny dystrybucji, które minimalizuje przenoszenie danych jest jednym z najważniejszych strategie dotyczące optymalizacji wydajności usługi SQL Data Warehouse.

Aby zminimalizować przenoszenia danych, wybierz kolumnę dystrybucji który:

- Jest używana w `JOIN`, `GROUP BY`, `DISTINCT`, `OVER`, i `HAVING` klauzul. Gdy na dwóch dużych tabel faktów częste sprzężeń, wydajność zapytań zwiększa dystrybucji obu tabel w jednej z kolumn sprzężenia.  Jeśli tabela nie jest używany w połączeniu, należy wziąć pod uwagę dystrybucja tabelę według kolumny, która jest często w `GROUP BY` klauzuli.
- Jest *nie* używane w `WHERE` klauzul. Może to ograniczyć zapytanie, aby nie uruchamiać na wszystkich dystrybucjach. 
- Jest *nie* kolumnę daty. Klauzulach WHERE często filtrowanie według daty.  W takim przypadku przetwarzanie wszystkich można uruchomić w tylko kilka dystrybucjach.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Co zrobić, jeśli żadna z tych kolumn jest kolumną dobrej

Jeśli żadna z kolumn nie ma za mało odrębnych wartości kolumny dystrybucji, możesz utworzyć nową kolumnę jako złożonego z co najmniej jedną wartość. Aby uniknąć przenoszenia danych podczas wykonywania zapytania, należy użyć kolumny dystrybucji złożonego jako kolumnę sprzężenia w zapytaniach.

Po projektowania tabeli dystrybucji skrótów, następnym krokiem jest do ładowania danych do tabeli.  Aby uzyskać wskazówki dotyczące ładowania, zobacz [omówienie ładowania](sql-data-warehouse-overview-load.md). 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Jak sprawdzić, czy to dobry wybór kolumny dystrybucji
Po załadowaniu danych do tabeli rozproszonych wyznaczania wartości skrótu, należy sprawdzić, jak wiersze są równomiernie na 60 dystrybucji. Wiersze na dystrybucję mogą się różnić maksymalnie 10% bez zauważalnego wpływu na wydajność. 

### <a name="determine-if-the-table-has-data-skew"></a>Określa, czy tabela ma wynikające z niesymetryczności danych
Szybko wyszukiwać niesymetryczność danych jest użycie [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). Poniższy kod SQL zwraca liczbę wierszy w tabeli, które są przechowywane w każdym z 60 dystrybucji. Wydajność o zrównoważonym obciążeniu, powinien równomiernie rozłożyć wierszy w tabeli rozproszone we wszystkich dystrybucjach.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Aby zidentyfikować, tabel, które mają więcej niż niesymetryczność danych 10%:

1. Utwórz dbo.vTableSizes widoku, który jest wyświetlany w [Omówienie tabel](sql-data-warehouse-tables-overview.md#table-size-queries) artykułu.  
2. Uruchom następujące zapytanie:

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

### <a name="check-query-plans-for-data-movement"></a>Sprawdź planów zapytań w przypadku przenoszenia danych
Kolumna dystrybucji dobre umożliwia sprzęgania i agregacji do ma minimalny przenoszenia. Ma to wpływ na sposób, w jaki sprzężenia powinny być zapisywane. Aby uzyskać przenoszenia minimalna ilość danych w celu utworzenia sprzężenia w dwóch tabelach rozproszonych wyznaczania wartości skrótu, jedna z kolumn sprzężenia musi być kolumna dystrybucji.  Nowi dwie tabele rozproszone wyznaczania wartości skrótu dla tego samego typu danych kolumny dystrybucji sprzężenia nie wymaga przenoszenia danych. Sprzężenia, można użyć dodatkowych kolumn, bez konieczności przenoszenia danych.

Aby uniknąć przenoszenia danych podczas dołączania do usługi:

- Tabele uwzględnione w sprzężeniu musi być rozproszonymi tabelami skrótów na **jeden** kolumn uczestniczących w sprzężenia.
- Typy danych kolumn sprzężenia muszą być zgodne między obie tabele.
- Kolumny muszą być przyłączone z operatorem równości.
- Typ sprzężenia nie może być `CROSS JOIN`.

Aby zobaczyć, jeśli zapytania występują przenoszenia danych, można sprawdzić w planie zapytania.  


## <a name="resolve-a-distribution-column-problem"></a>Rozwiązywanie problemów kolumny dystrybucji
Nie jest wymagany do rozwiązania, że wszystkie przypadki danych pochylanie. Dystrybuowanie danych polega na znajdowaniu równowagę między minimalizując niesymetryczność danych oraz przenoszenia danych. Nie zawsze jest możliwe zminimalizować niesymetryczność danych i przenoszenia danych. Czasami o konieczności przenoszenia danych minimalny może przeważyć wpływ wynikające z niesymetryczności danych.

Podjęcie decyzji, jeśli należy rozwiązać danych niesymetryczność w tabeli, należy zrozumieć jak najszerzej dotyczące ilości danych i zapytań w obciążenia. Można użyć w krokach w [monitorowanie zapytań](sql-data-warehouse-manage-monitor.md) artykuł, aby monitorować wpływ pochylenia wydajność zapytań. W szczególności Szukaj jak długo zajmie duże kwerendy na poszczególnych dystrybucji.

Ponieważ nie można zmienić kolumny dystrybucji na podstawie istniejącej tabeli, typowy sposób, aby rozwiązać niesymetryczność danych jest ponownie utwórz tabelę z kolumną innej dystrybucji.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Ponownie utwórz tabelę nowa kolumna dystrybucji
W tym przykładzie użyto [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) ponownie utworzyć tabelę z kolumną dystrybucji różne wartości skrótów.

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

Aby utworzyć tabelę z dystrybucją, użyj jednej z tych instrukcji:

- [Tworzenie tabeli (Azure SQL Data Warehouse)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [Utwórz TABLE AS SELECT (Azure SQL Data Warehouse](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


