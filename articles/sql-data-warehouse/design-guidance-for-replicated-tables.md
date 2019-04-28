---
title: Wskazówki dotyczące zreplikowane tabele — Azure SQL Data Warehouse projektowania | Dokumentacja firmy Microsoft
description: Zalecenia dotyczące projektowania zreplikowane tabele w schemacie usługi Azure SQL Data Warehouse. 
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/19/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: acea42f7f4ab986e9828000ab7cfc9e302ed92a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082946"
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Wskazówki dotyczące projektowania dotyczące korzystania z zreplikowane tabele w usłudze Azure SQL Data Warehouse
Ten artykuł zawiera zalecenia dotyczące projektowania zreplikowanych tabel w schemacie usługi SQL Data Warehouse. Użyj tych zaleceń, aby poprawić wydajność zapytań, redukowania poziomu złożoności procesów przepływu i zapytanie danych.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że czytelnik zna dystrybucję danych i pojęcia przenoszenia danych w usłudze SQL Data Warehouse.  Aby uzyskać więcej informacji, zobacz [architektury](massively-parallel-processing-mpp-architecture.md) artykułu. 

W ramach projektowaniu tabel Dowiedz się, jak to możliwe, o danych i jak jest wykonywane zapytanie danych.  Na przykład należy wziąć pod uwagę następujące pytania:

- Jak duże jest tabela?   
- Częstotliwość odświeżania tabeli?   
- Czy mam tabelami faktów i wymiarów w magazynie danych?   

## <a name="what-is-a-replicated-table"></a>Co to jest replikowanej tabeli?
Replikowanej tabeli ma pełną kopię tabeli, które są dostępne na każdym węźle obliczeniowym. Replikowanie tabeli eliminuje potrzebę na przesyłanie danych między węzłami obliczeniowymi przed przystąpieniem do dołączania lub agregacji. Ponieważ tabela ma wiele kopii, zreplikowane tabele działają najlepiej, jeśli rozmiar tabeli jest mniejszy niż 2 GB skompresowany.  2 GB nie jest stały limit.  Jeśli dane są statyczne i nie zmienia się, można replikować większych tabel.

Na poniższym diagramie przedstawiono replikowanej tabeli, która jest dostępna w każdym węźle obliczeniowym. W usłudze SQL Data Warehouse replikowanej tabeli w pełni jest kopiowana do bazy danych dystrybucji, na każdym węźle obliczeniowym. 

![Tabela zreplikowany](media/guidance-for-using-replicated-tables/replicated-table.png "zreplikowany tabeli")  

Replikowane tabele pracy sprawdzają się w przypadku tabel wymiarów w schemacie gwiazdy. Tabele wymiarów zwykle są sprzęgane z tabel faktów, które są rozmieszczone w sposób inny niż tabela wymiarów.  Wymiary są zwykle o rozmiarze, który sprawia, że jest to możliwe do przechowywania i obsługi wielu kopii. Wymiary przechowywania opisowe danych, które zmieniają się powoli, takie jak nazwa klienta i adres i szczegółowe informacje. Wolno zmieniający charakter danych prowadzi do mniej konserwacji replikowanej tabeli. 

Należy rozważyć użycie w replikowanej tabeli, gdy:

- Rozmiar tabeli na dysku jest mniejszy niż 2 GB, niezależnie od liczby wierszy. Aby znaleźć rozmiar tabeli, można użyć [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) polecenia: `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- Tabela jest używana w połączeniu, które w przeciwnym razie wymagają przenoszenia danych. Podczas dołączania tabel, które nie są dystrybuowane na tej samej kolumnie, takiego jak tabela rozproszonych wyznaczania wartości skrótu do tabeli działanie okrężne przenoszenie danych jest wymagany do wykonania zapytania.  Jeśli jednej z tabel jest mały, należy wziąć pod uwagę replikowanej tabeli. Zalecamy używanie zreplikowane tabele zamiast działanie okrężne tabel w większości przypadków. Aby wyświetlić operacje przenoszenia danych w planom zapytań, należy użyć [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).  BroadcastMoveOperation jest operacją przenoszenia typowych danych, które mogą zostać usunięte przy użyciu replikowanej tabeli.  
 
Zreplikowane tabele nie może przynieść najlepszą wydajność zapytań po:

- Tabela ma często wstawiania, aktualizowania i usuwania działań. Te operacje języka (DML) manipulacji danych wymagają odbudowania tego replikowanej tabeli. Ponowne tworzenie często może spowodować niższej wydajności.
- Magazyn danych jest często skalowany. Skalowanie magazynu danych zmienia się liczba węzłów obliczeniowych, którą jest naliczana odbudowywania replikowanej tabeli.
- Tabela ma dużą liczbę kolumn, ale operacje na danych zwykle dostępu do niewielkiej liczby kolumn. W tym scenariuszu, zamiast replikować całą tabelę może być bardziej skuteczne do dystrybucji tabeli, a następnie utwórz indeks dla często używanych kolumn. Gdy zapytanie wymaga przenoszenia danych, usługa SQL Data Warehouse przechodzi tylko dane dla żądanej kolumny. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Zreplikowane tabele za pomocą prostego zapytania predykatów
Zanim zdecydujesz się korzystający dystrybuuje lub replikacji tabeli, należy wziąć pod uwagę typów zapytań, które mają być uruchamiane w tabeli. Jeśli to możliwe,

- Zreplikowane tabele na użytek zapytań za pomocą prostego zapytania predykatów, takich jak równości i nierówności.
- Korzystanie z tabel rozproszonych zapytań za pomocą predykatów złożonego zapytania, takie jak podobne lub nie PODOBA.

Zapytania mocy procesora CPU działają najlepiej, gdy praca jest rozproszona na wszystkich węzłach obliczeniowych. Na przykład zapytania, które wykonywania obliczeń w każdym wierszu tabeli działać lepiej w tabelach rozproszonych niż zreplikowanych tabelach. Ponieważ replikowanej tabeli są przechowywane w całości w każdym węźle obliczeniowym, użycie Procesora CPU na replikowanej tabeli uruchomieniu zapytania względem całej tabeli w każdym węźle obliczeniowym. Dodatkowe obliczeń może zmniejszyć wydajność zapytań.

Na przykład ta kwerenda ma predykatu złożone.  Działa ona szybciej, gdy dane znajdują się w rozproszonych tabeli zamiast replikowanej tabeli. W tym przykładzie dane mogą być okrężnego rozproszonych.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Konwertowanie istniejących tabel działanie okrężne na zreplikowane tabele
Jeśli masz już tabele wykorzystujące działanie okrężne, firma Microsoft zaleca, konwertowania go na potrzeby zreplikowane tabele, jeśli spełniają kryteria opisane w tym artykule. Zreplikowane tabele za pośrednictwem tabele wykorzystujące działanie okrężne poprawić wydajność, ponieważ eliminują potrzebę przenoszenia danych.  Tabela działanie okrężne zawsze wymaga przenoszenia danych dla sprzężeń. 

W tym przykładzie użyto [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) zmiany w tabeli DimSalesTerritory do zreplikowanej tabeli. Ten przykład działa niezależnie od tego, czy DimSalesTerritory rozproszonych wyznaczania wartości skrótu lub działanie okrężne.

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

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Wydajność zapytań na przykład działanie okrężne i replikowane 

Replikowanej tabeli nie wymaga przenoszenia żadnych danych dla sprzężeń, ponieważ cała tabela jest już obecny w każdym węźle obliczeniowym. Tabele wymiarów przypadku okrężnego rozproszonych sprzężenia kopiuje tabeli wymiarów w całości na każdym węźle obliczeniowym. Aby przenieść dane, planu zapytania zawiera operacji o nazwie BroadcastMoveOperation. Ten typ operacji przenoszenia danych zmniejsza wydajność zapytań i wyeliminowania przy użyciu zreplikowanych tabelach. Aby zapoznać się z procedurą planu zapytania, należy użyć [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) widoku wykazu systemu. 

Na przykład, w następujące zapytanie względem schematu AdventureWorks `FactInternetSales` tabela jest dystrybuowane wyznaczania wartości skrótu. `DimDate` i `DimSalesTerritory` tabele to zgodna z mniejszych tabele wymiarów. Ta kwerenda zwraca łączną wartość sprzedaży w Ameryce Północnej, dla roku obrachunkowego 2004:

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
Ponowne utworzenie `DimDate` i `DimSalesTerritory` jako tabele wykorzystujące działanie okrężne. W wyniku zapytania wykazało, że następujący plan zapytania ma wiele emisji operacje są przenoszone: 
 
![Plan zapytania działania okrężnego](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Ponowne utworzenie `DimDate` i `DimSalesTerritory` jako zreplikowane tabele i zostało wykonane zapytanie. Wynikowy planu zapytania jest znacznie krótszy, a nie ma żadnego emituje przenosi.

![Replikowane planu zapytania](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Zagadnienia dotyczące wydajności do modyfikowania zreplikowane tabele
Usługa SQL Data Warehouse implementuje replikowanej tabeli, utrzymując głównej wersji w tabeli. Kopiuje głównej wersji do jednej bazy danych dystrybucji, na każdym węźle obliczeniowym. Kiedy zmiany, usługa SQL Data Warehouse najpierw aktualizuje głównej tabeli. Następnie ponownie kompiluje tabel w każdym węźle obliczeniowym. Odbudowie replikowanej tabeli obejmuje kopiowanie tabeli na każdym węźle obliczeniowym, a następnie tworzenie indeksów.  Na przykład replikowanej tabeli na DW400 ma 5 kopii danych.  Kopia główna i pełnej kopii na każdym węźle obliczeniowym.  Wszystkie dane są przechowywane w bazach danych dystrybucji. Usługa SQL Data Warehouse używa tego modelu do obsługi szybciej instrukcje modyfikacji danych i elastyczne operacje skalowania. 

Ponowne kompilowanie są wymagane po:
- Dane są ładowane lub zmodyfikowany
- Magazyn danych jest skalowany do innego poziomu
- Definicja tabeli jest aktualizowana.

Ponowne kompilowanie nie są wymagane po:
- Wstrzymywanie operacji
- Wznawia działania

Ponowna kompilacja nie jest realizowane natychmiast, po zmodyfikowaniu danych. Zamiast tego rekonstrukcji jest wyzwalany, gdy zapytanie wybiera z tabeli po raz pierwszy.  Zapytanie, która wyzwoliła rekonstrukcji odczytuje natychmiast z wersją główną tabeli podczas asynchronicznie kopiowane są dane na każdym węźle obliczeniowym. Do czasu ukończenia kopiowania danych kolejne zapytania będą nadal używać wersji głównej tabeli.  Jeśli wystąpi dowolne działanie względem replikowanej tabeli, która wymusza innego ponownej kompilacji, kopiowanie danych zostało unieważnione i następną instrukcję select wywoła dane do skopiowania ponownie. 

### <a name="use-indexes-conservatively"></a>Ostrożnie używać indeksów
Standardowe rozwiązania indeksowania dotyczą zreplikowanych tabelach. Usługa SQL Data Warehouse odbudowuje każdy indeks replikowanej tabeli w ramach ponownej kompilacji. Indeksy należy używać tylko, gdy przyrost wydajności przewyższa koszt ponowne tworzenie indeksów.  
 
### <a name="batch-data-loads"></a>Ładowania danych usługi Batch
Podczas ładowania danych w zreplikowanych tabelach, należy spróbować zminimalizować ponowne kompilowanie ze sobą przetwarzanie wsadowe obciążeń. Przed uruchomieniem instrukcji "select", należy wykonać wsadowej operacji obciążenia.

Na przykład ten wzorzec obciążenia ładowania danych z czterech źródeł i wywołuje cztery ponowne kompilowanie. 

- Załaduj ze źródła 1.
- Instrukcja SELECT wyzwalaczy odbudować 1.
- Załaduj ze źródła 2.
- Instrukcja SELECT wyzwalaczy odbudować 2.
- Załaduj ze źródła 3.
- Instrukcja SELECT wyzwalaczy odbudować 3.
- Załaduj ze źródła 4.
- Instrukcja SELECT wyzwalaczy odbudować 4.

Na przykład ten wzorzec obciążenia ładowania danych z czterech źródeł, ale tylko wywołuje jeden ponownej kompilacji.

- Załaduj ze źródła 1.
- Załaduj ze źródła 2.
- Załaduj ze źródła 3.
- Załaduj ze źródła 4.
- Ponownie skompiluj wyzwalaczy instrukcji SELECT.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Ponownie skompiluj replikowanej tabeli po załadowaniu usługi batch
Aby zapewnić czas na wykonanie zapytania spójny, należy wziąć pod uwagę wymuszanie kompilacji zreplikowane tabele po załadowaniu usługi batch. W przeciwnym razie pierwszego zapytania będą nadal używać przenoszenia danych do wykonania zapytania. 

To zapytanie używa [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) DMV, aby wyświetlić listę zreplikowane tabele, które zostały zmodyfikowane, ale nie został odbudowany.

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
 
Aby wyzwolić ponownej kompilacji, uruchom następującą instrukcję w każdej tabeli w powyższym danych wyjściowych. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Kolejne kroki 
Aby utworzyć tabelę replikowaną, użyj jednej z tych instrukcji:

- [Tworzenie tabeli (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [Utwórz TABLE AS SELECT (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Aby uzyskać omówienie rozproszone tabele, zobacz [tabelach rozproszonych](sql-data-warehouse-tables-distribute.md).



