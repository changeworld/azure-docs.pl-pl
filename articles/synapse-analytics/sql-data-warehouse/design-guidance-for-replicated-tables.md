---
title: Wskazówki dotyczące projektu dla tabel replikowanych
description: Zalecenia dotyczące projektowania tabel replikowanych w synapse SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 654aeddbb305124ea00a883dbef9d8b5ad585a36
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990790"
---
# <a name="design-guidance-for-using-replicated-tables-in-sql-analytics"></a>Wskazówki dotyczące projektowania dotyczące korzystania z tabel replikowanych w usłudze SQL Analytics

Ten artykuł zawiera zalecenia dotyczące projektowania tabel replikowanych w schemacie usługi SQL Analytics. Skorzystaj z tych zaleceń, aby zwiększyć wydajność kwerendy, zmniejszając złożoność przenoszenia danych i zapytań.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz pojęcia dotyczące dystrybucji danych i przenoszenia danych w usłudze SQL Analytics.Aby uzyskać więcej informacji, zobacz artykuł [architektury.](massively-parallel-processing-mpp-architecture.md)

W ramach projektowania tabeli, zrozumieć jak najwięcej o danych i jak dane są poszukiwane.Rozważmy na przykład następujące pytania:

- Jak duży jest stół?
- Jak często tabela jest odświeżana?
- Czy w bazie danych usługi SQL Analytics są wylić tabele faktów i wymiarów?

## <a name="what-is-a-replicated-table"></a>Co to jest tabela replikowana?

Zreplikowana tabela ma pełną kopię tabeli dostępną w każdym węźle obliczeniowym. Replikowanie tabeli eliminuje konieczność przesyłania danych między węzłami obliczeniowymi przed operacją sprzężenia lub agregacji. Ponieważ tabela ma wiele kopii, replikowane tabele działają najlepiej, gdy rozmiar tabeli jest mniejszy niż 2 GB skompresowany.  2 GB nie jest twardym limitem.  Jeśli dane są statyczne i nie zmieniają się, można replikować większe tabele.

Na poniższym diagramie przedstawiono replikowaną tabelę, która jest dostępna w każdym węźle obliczeniowym. W usłudze SQL Analytics replikowana tabela jest w pełni kopiowana do bazy danych dystrybucji w każdym węźle obliczeniowym.

![Tabela replikowana](./media/design-guidance-for-replicated-tables/replicated-table.png "Tabela replikowana")  

Tabele replikowane działają dobrze dla tabel wymiarów w schemacie gwiazdy. Tabele wymiarów są zazwyczaj łączone z tabelami faktów, które są rozłożone inaczej niż tabela wymiarów.  Wymiary są zwykle o rozmiarze, który umożliwia przechowywanie i utrzymywanie wielu kopii. Wymiary przechowują dane opisowe, które zmieniają się powoli, takie jak nazwa i adres klienta oraz szczegóły produktu. Powoli zmieniający się charakter danych prowadzi do mniejszej konserwacji tabeli replikowanej.

Należy rozważyć użycie tabeli replikowanej, gdy:

- Rozmiar tabeli na dysku jest mniejszy niż 2 GB, niezależnie od liczby wierszy. Aby znaleźć rozmiar tabeli, można użyć polecenia [DBCC PDW_SHOWSPACEUSED:](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`.
- Tabela jest używana w sprzężeniach, które w przeciwnym razie wymagałyby przenoszenia danych. Podczas łączenia tabel, które nie są dystrybuowane w tej samej kolumnie, takich jak tabela rozproszona mieszania do tabeli okrężnego, do ukończenia kwerendy wymagane jest przesunięcie danych.  Jeśli jedna z tabel jest mała, należy wziąć pod uwagę tabelę replikowaną. W większości przypadków zaleca się używanie tabel replikowanych zamiast tabel okrężnych. Aby wyświetlić operacje przenoszenia danych w planach kwerend, należy użyć [pliku sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  BroadcastMoveOperation jest typową operacją przenoszenia danych, którą można wyeliminować przy użyciu tabeli replikowanej.  

Tabele replikowane mogą nie uzyskać najlepszej wydajności kwerendy, gdy:

- Tabela ma częste operacje wstawiania, aktualizowania i usuwania.Operacje języka manipulowania danymi (DML) wymagają przebudowy replikowanej tabeli.Często przebudowywanie może spowodować mniejszą wydajność.
- Baza danych usługi SQL Analytics jest często skalowana. Skalowanie bazy danych usługi SQL Analytics zmienia liczbę węzłów obliczeniowych, co wiąże się z odbudową replikowanej tabeli.
- Tabela zawiera dużą liczbę kolumn, ale operacje danych zazwyczaj uzyskują dostęp tylko do niewielkiej liczby kolumn. W tym scenariuszu zamiast replikowania całej tabeli, może być bardziej skuteczne do dystrybucji tabeli, a następnie utworzyć indeks na często używanych kolumn. Gdy kwerenda wymaga przenoszenia danych, usługa SQL Analytics przenosi dane tylko dla żądanych kolumn.

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Używanie tabel replikowanych z prostymi predykatami kwerend

Przed wybraniem do dystrybucji lub replikacji tabeli, należy pomyśleć o typach kwerend, które mają być uruchamiane względem tabeli. W miarę możliwości

- Użyj tabel replikowanych dla kwerend z prostymi predykatami zapytań, takimi jak równość lub nierówność.
- Użyj tabel rozproszonych dla kwerend ze złożonymi predykatami zapytań, takimi jak LIKE lub NOT LIKE.

Zapytania intensywnie korzystające z procesora CPU działają najlepiej, gdy praca jest rozproszona we wszystkich węzłach obliczeniowych. Na przykład kwerendy uruchamiane w każdym wierszu tabeli działają lepiej w tabelach rozproszonych niż w tabelach replikowanych. Ponieważ replikowana tabela jest przechowywana w całości w każdym węźle obliczeniowym, kwerenda intensywnie korzystająca z procesora CPU względem replikowanej tabeli jest uruchamiana względem całej tabeli w każdym węźle obliczeniowym. Dodatkowe obliczenia mogą spowolnić wydajność kwerendy.

Na przykład ta kwerenda ma złożony predykat.  Działa szybciej, gdy dane są w tabeli rozproszonej zamiast zreplikowanej tabeli. W tym przykładzie dane mogą być rozproszone okrężne.

```sql

SELECT EnglishProductName
FROM DimProduct
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Konwertowanie istniejących tabel okrężnych na tabele replikowane

Jeśli masz już tabele okrężne, zaleca się przekonwertowanie ich na tabele replikowane, jeśli spełniają kryteria opisane w tym artykule. Tabele replikowane zwiększają wydajność w przypadku tabel okrężnych, ponieważ eliminują potrzebę przenoszenia danych.  Tabela okrężna zawsze wymaga przenoszenia danych dla sprzężeń.

W tym przykładzie użyto [CTAS,](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) aby zmienić Tabelę DimSalesTerritory na tabelę replikowaną. W tym przykładzie działa niezależnie od tego, czy DimSalesTerritory jest rozmieszczonych mieszania lub okrężne.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]
WITH
  (
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE')

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Przykład wydajności kwerendy dla działania okrężnego i replikowanego

Tabela replikowana nie wymaga żadnego przenoszenia danych dla sprzężeń, ponieważ cała tabela jest już obecna w każdym węźle obliczeniowym. Jeśli tabele wymiarów są rozproszone okrężnie, sprzężenie kopiuje tabelę wymiarów w całości do każdego węzła obliczeniowego. Aby przenieść dane, plan kwerend zawiera operację o nazwie BroadcastMoveOperation. Ten typ operacji przenoszenia danych spowalnia wydajność kwerendy i jest eliminowany przy użyciu tabel replikowanych. Aby wyświetlić kroki planu kwerend, użyj widoku katalogu [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) widoku katalogu systemowego.  

Na przykład w następującej kwerendzie względem `FactInternetSales` schematu AdventureWorks tabela jest rozproszona mieszania. `DimDate` Tabele `DimSalesTerritory` i są mniejszymi tabelami wymiarów. Ta kwerenda zwraca całkowitą sprzedaż w Ameryce Północnej dla roku obrachunkowego 2004:

```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```

Odtworzyliśmy `DimDate` i `DimSalesTerritory` jako stoły okrężne. W rezultacie kwerenda pokazała następujący plan kwerendy, który ma wiele operacji przenoszenia emisji:

![Plan kwerend okrężnych](./media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg)

Ponownie `DimDate` utworzyliśmy `DimSalesTerritory` i jako zreplikowane tabele i uruchomiliśmy kwerendę ponownie. Wynikowy plan kwerend jest znacznie krótszy i nie ma żadnych ruchów emisji.

![Zreplikowany plan kwerend](./media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg)

## <a name="performance-considerations-for-modifying-replicated-tables"></a>Zagadnienia dotyczące wydajności modyfikowania tabel replikowanych

Sql Analytics implementuje replikowaną tabelę, utrzymując główną wersję tabeli. Kopiuje wersję główną do pierwszej bazy danych dystrybucji w każdym węźle obliczeniowym. Gdy nastąpi zmiana, SQL Analytics najpierw aktualizuje wersję główną, a następnie odbudowuje tabele w każdym węźle obliczeniowym. Przebudowa replikowanej tabeli obejmuje kopiowanie tabeli do każdego węzła obliczeniowego, a następnie tworzenie indeksów.  Na przykład zreplikowana tabela na DW2000c ma 5 kopii danych.  Kopia wzorcowa i pełna kopia w każdym węźle obliczeniowym.  Wszystkie dane są przechowywane w bazach danych dystrybucji. Usługa SQL Analytics używa tego modelu do obsługi szybszych instrukcji modyfikacji danych i elastycznych operacji skalowania.

Przebudowy są wymagane po:

- Dane są ładowane lub modyfikowane
- Wystąpienie Synapse SQL jest skalowane do innego poziomu
- Definicja tabeli jest aktualizowana

Przebudowy nie są wymagane po:

- Wstrzymanie operacji
- Wznawianie operacji

Przebudowa nie odbywa się natychmiast po zmodyfikowaniu danych. Zamiast tego przebudowa jest wyzwalana przy pierwszym wybraniu kwerendy z tabeli.  Kwerenda, która wyzwoliła przebudowę, odczytuje natychmiast z głównej wersji tabeli, podczas gdy dane są asynchronicznie kopiowane do każdego węzła obliczeniowego. Dopóki kopia danych nie zostanie ukończona, kolejne kwerendy będą nadal używać głównej wersji tabeli.  Jeśli jakiekolwiek działanie występuje w przypadku replikowanej tabeli, która wymusza inną przebudowę, kopia danych zostanie unieważniona, a następna instrukcja select wyzwoli dane do ponownego skopiowania.

### <a name="use-indexes-conservatively"></a>Używaj indeksów zachowawczo

Standardowe praktyki indeksowania mają zastosowanie do tabel replikowanych. SQL Analytics odbudowuje każdy indeks tabeli replikowanej w ramach przebudowy. Indeksy należy używać tylko wtedy, gdy przyrost wydajności przewyższa koszt odbudowy indeksów.

### <a name="batch-data-load"></a>Ładowanie danych partii

Podczas ładowania danych do tabel replikowanych, spróbuj zminimalizować przebudowy przez przetwarzanie wsadowe ładunków razem. Wykonaj wszystkie obciążenia wsadowe przed uruchomieniem instrukcji select.

Na przykład ten wzorzec obciążenia ładuje dane z czterech źródeł i wywołuje cztery przebudowuje.

        Load from source 1.
- Wybierz wyzwalacze instrukcji odbudować 1.
        Obciążenie ze źródła 2.
- Wybierz wyzwalacze instrukcji odbudować 2.
- Obciążenie ze źródła 3.
- Wybierz wyzwalacze instrukcji odbudować 3.
- Obciążenie ze źródła 4.
- Wybierz wyzwalacze instrukcji odbudować 4.

Na przykład ten wzorzec obciążenia ładuje dane z czterech źródeł, ale wywołuje tylko jedną przebudowę.

- Obciążenie ze źródła 1.
- Obciążenie ze źródła 2.
- Obciążenie ze źródła 3.
- Obciążenie ze źródła 4.
- Wybierz wyzwalacze instrukcji przebudowy.

### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Odbudowywać zreplikowaną tabelę po załadowaniu wsadowym

Aby zapewnić spójne czasy wykonywania kwerendy, należy rozważyć wymuszenie kompilacji replikowanych tabel po załadowaniu wsadowym. W przeciwnym razie pierwsza kwerenda będzie nadal używać przenoszenia danych do ukończenia kwerendy.

Ta kwerenda używa [pliku sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) DMV do listy replikowanych tabel, które zostały zmodyfikowane, ale nie zostały przebudowane.

```sql
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id
  JOIN sys.pdw_table_distribution_properties p
    ON p.object_id = t.object_id
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```

Aby wyzwolić przebudowę, uruchom następującą instrukcję dla każdej tabeli w poprzednim wyjściu.

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
```

## <a name="next-steps"></a>Następne kroki

Aby utworzyć tabelę replikowaną, należy użyć jednej z następujących instrukcji:

- [TWORZENIE TABELI (SQL Analytics)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [TWORZENIE TABELI JAKO WYBIERZ (SQL Analytics)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Aby uzyskać przegląd tabel rozproszonych, zobacz [tabele rozproszone](sql-data-warehouse-tables-distribute.md).
