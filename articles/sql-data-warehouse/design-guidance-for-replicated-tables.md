---
title: Projekt, wskazówki dotyczące zreplikowanych tabelach - Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wskazówek dotyczących projektowania zreplikowane tabele w schemat magazyn danych SQL Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/23/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 1cc796061056ff017e3d778ebb2e50e13d55a4c1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32189568"
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Wskazówki dotyczące projektowania dotyczące używania zreplikowane tabele w magazynie danych SQL Azure
Ten artykuł zawiera zalecenia dotyczące projektowania zreplikowanych tabel w schematu SQL Data Warehouse. Użyj te zalecenia, aby poprawić wydajność zapytań, zmniejsza się złożoność danych przemieszczania i zapytań.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że znasz z danych dystrybucji i koncepcje przepływu danych w usłudze SQL Data Warehouse.  Aby uzyskać więcej informacji, zobacz [architektura](massively-parallel-processing-mpp-architecture.md) artykułu. 

W ramach projektowaniu tabel Dowiedz się, jak to możliwe danych i jak danych jest poddawany kwerendzie.  Na przykład wziąć pod uwagę następujące pytania:

- Jak duże jest tabela?   
- Częstotliwość odświeżania tabeli?   
- Tabele faktów i wymiarów są dostępne w magazynie danych?   

## <a name="what-is-a-replicated-table"></a>Co to jest zreplikowanej tabeli?
Zreplikowanej tabeli ma pełną kopię tabeli dostępny w każdym węźle obliczeń. Replikowanie tabeli eliminuje potrzebę na przesyłanie danych między węzłami obliczeniowymi przed join lub agregacji. Ponieważ tabela ma wiele kopii, zreplikowanych tabelach działają najlepiej, gdy rozmiar tabeli jest mniejszy niż 2 GB skompresowane.

Na poniższym diagramie przedstawiono zreplikowanej tabeli, która jest dostępna w każdym węźle obliczeń. W usłudze SQL Data Warehouse zreplikowanej tabeli pełni jest kopiowany do bazy danych dystrybucji, w każdym węźle obliczeń. 

![Tabela zreplikowane](media/guidance-for-using-replicated-tables/replicated-table.png "zreplikowane tabeli")  

Replikowane tabele pracy efektywne w przypadku tabel wymiarów małych w schemat gwiazdy. Tabele wymiarów zwykle są rozmiaru, dzięki którym możliwe do przechowywania i obsługi wielu kopii. Wymiary przechowywać opisowe dane, które zmienia się powoli, takie jak nazwa klienta i adresem i szczegółowe informacje. Wolno zmieniającego charakter danych prowadzi do mniej odtwarza zreplikowanej tabeli. 

Należy rozważyć użycie zreplikowanej tabeli, gdy:

- Rozmiar tabeli na dysku jest mniejszy niż 2 GB, niezależnie od liczby wierszy. Aby znaleźć rozmiar tabeli, można użyć [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) polecenia: `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- Tabela jest używana w sprzężeniu, które w przeciwnym razie wymaga przenoszenia danych. Podczas sprzęgania tabel, które nie są dystrybuowane na tej samej kolumnie, takich jak tabeli rozpowszechniane skrót do tabeli okrężnego przepływu danych jest wymagany do wykonania zapytania.  Jeśli jedna z tabel jest mały, należy wziąć pod uwagę zreplikowanej tabeli. Zalecamy używanie zamiast okrężnego tabel w większości przypadków zreplikowanych tabelach. Aby wyświetlić operacje przenoszenia danych w planie zapytania, użyj [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).  BroadcastMoveOperation jest operacji przeniesienia typowych danych, które mogą zostać usunięte przy użyciu zreplikowanej tabeli.  
 
Zreplikowane tabele nie mogą użyć instrukcji yield najlepszą wydajność zapytań po:

- Tabela zawiera częste wstawiania, aktualizowania i usuwania działań. Te operacje języka manipulacji danych wymaga odbudowania zreplikowanej tabeli. Ponowne kompilowanie często powodują mniejszą wydajność.
- W magazynie danych często skalowania. Skalowanie hurtowni danych zmienia liczba węzłów obliczeniowych, który wiąże się z kompilowania.
- Tabela ma dużą liczbę kolumn, ale operacje na danych zwykle uzyskują dostęp do niewielkiej liczby kolumn. W tym scenariuszu, zamiast replikować całą tabelę może być bardziej efektywnej dystrybucji tabeli, a następnie utwórz indeks dla często używanych kolumn. Jeśli zapytanie wymaga przenoszenia danych, Magazyn danych SQL tylko przenosi dane dla żądanej kolumny. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Zreplikowane tabele za pomocą prostego zapytania predykatów
Przed wybraniem do dystrybucji lub replikacji tabeli, należy zastanowić typy zapytań, które mają być wykonywane w tabeli. Jeśli to możliwe,

- Użyj zreplikowanych tabel dla zapytań z predykaty prostego zapytania, takie jak równości i nierówności.
- Użyj tabel rozproszone dla zapytań z predykaty złożonego zapytania, takie jak podobne lub nie, takich jak.

Użycie Procesora CPU zapytania wykonywane najlepiej, jeśli praca jest dystrybuowana do wszystkich węzłów obliczeniowych. Na przykład zapytania uruchamiane obliczenia w każdym wierszu tabeli działać lepiej w tabelach rozproszonej niż zreplikowanych tabelach. Ponieważ zreplikowanej tabeli są przechowywane w całości w każdym węźle obliczeń, użycie Procesora CPU zapytanie zreplikowanej tabeli jest uruchamiana dla całej tabeli w każdym węźle obliczeń. Dodatkowe obliczenia może zmniejszyć wydajność kwerend.

Na przykład ta kwerenda zawiera złożone predykatu.  Uruchamia szybciej gdy dostawca jest tabela rozproszona zamiast zreplikowanej tabeli. W tym przykładzie dostawcy może być okrężnego rozproszonych.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Konwertuj istniejące tabele okrężnego zreplikowanych tabelach
Jeśli masz już okrężnego tabel, firma Microsoft zaleca konwersji je na zreplikowane tabele, jeśli spełniają kryteria opisane w tym artykule. Zreplikowane tabele zwiększyć wydajność w przypadku tabel okrężnego, ponieważ eliminuje potrzebę przenoszenia danych.  Tabela okrężnego zawsze wymaga przenoszenia danych sprzężenia. 

W tym przykładzie użyto [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) można zmienić tabeli DimSalesTerritory na zreplikowanej tabeli. W tym przykładzie działa niezależnie od tego, czy DimSalesTerritory rozpowszechniane skrót lub okrężnego.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

--Create statistics on new table
CREATE STATISTICS [SalesTerritoryKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryKey]);
CREATE STATISTICS [SalesTerritoryAlternateKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryAlternateKey]);
CREATE STATISTICS [SalesTerritoryRegion] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryRegion]);
CREATE STATISTICS [SalesTerritoryCountry] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryCountry]);
CREATE STATISTICS [SalesTerritoryGroup] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryGroup]);

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Przykład wydajności kwerendy okrężnego i replikowane 

Zreplikowanej tabeli nie wymaga przenoszenia żadnych danych do sprzężenia, ponieważ cała tabela jest już obecny w każdym węźle obliczeń. Jeśli tabele wymiarów okrężnego rozproszonych, sprzężenia kopiuje tabeli wymiarów w pełni do każdego węzła obliczeń. Aby przenieść dane, plan zapytania zawiera operacji o nazwie BroadcastMoveOperation. Ten typ operacji przenoszenia danych zmniejsza wydajność zapytań i wyeliminowania przy użyciu zreplikowanych tabelach. Aby wyświetlić czynności planu zapytania, użyj [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) widoku wykazu systemu. 

Na przykład w następującej kwerendy względem schematu AdventureWorks ` FactInternetSales` tabela jest dystrybuowane wyznaczania wartości skrótu. `DimDate` i `DimSalesTerritory` tabele są mniejsze tabele wymiarów. Ta kwerenda zwraca całkowitej sprzedaży w Ameryce Północnej, dla roku obrachunkowego 2004:
 
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
Ponownie utworzono `DimDate` i `DimSalesTerritory` jako tabele okrężnego. W związku z tym zapytanie wykazało następujące planu zapytania, który ma wiele emisji operacje są przenoszone: 
 
![Plan zapytania okrężnego](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Ponownie utworzono `DimDate` i `DimSalesTerritory` jako zreplikowane tabele i został uruchomiony ponownie zapytanie. Wynikowy plan zapytania jest znacznie krótsze i nie ma żadnego emituje przenosi.

![Replikowane planu zapytania](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Zagadnienia dotyczące wydajności modyfikowania zreplikowanych tabelach
Usługa SQL Data Warehouse implementuje zreplikowanej tabeli dzięki utrzymywaniu głównej wersji w tabeli. Kopiuje głównej wersji do bazy danych dystrybucji jeden w każdym węźle obliczeń. W przypadku zmiany SQL Data Warehouse najpierw aktualizuje wzorcowej tabeli. Następnie spowoduje odbudowanie tabel w każdym węźle obliczeń. Odbudowywanie zreplikowanej tabeli obejmuje kopiowanie tabeli na każdym węźle obliczeń, a następnie tworzenie indeksów.  Na przykład zreplikowanej tabeli na DW400 ma 5 kopie danych.  Kopia wzorca i pełnej kopii w każdym węźle obliczeń.  Wszystkie dane są przechowywane w bazach danych dystrybucji. Usługi SQL Data Warehouse używa tego modelu do obsługi instrukcji modyfikacji danych szybsze i elastyczny operacji skalowania. 

Odtwarza są wymagane po:
- Dane są ładowane lub zmodyfikowane
- Magazyn danych jest skalowana inny poziom
- Definicja tabeli jest aktualizowany

Odtwarza nie są wymagane po:
- Operację wstrzymywania
- Wznawia działania

Ponowna kompilacja nie odbywa się natychmiast po zmodyfikowaniu danych. Zamiast tego rekonstrukcji zostanie wywołany po raz pierwszy wybiera zapytania z tabeli.  Zapytanie, która wyzwoliła rekonstrukcji odczytuje natychmiast z głównej wersji tabeli podczas asynchronicznego skopiowanie danych do każdego węzła obliczeń. Do czasu ukończenia kopiowania danych kolejne zapytania będzie nadal używać głównej wersji tabeli.  W przypadku żadnego działania względem zreplikowanej tabeli, która wymusza innego odbudowy, unieważnienia kopię danych, a następnie następnej instrukcji select wyzwala dane do skopiowania ponownie. 

### <a name="use-indexes-conservatively"></a>Użyj konserwatywnie indeksów
Rozwiązania w zakresie indeksowania standardowe mają zastosowanie do zreplikowanych tabelach. Usługa SQL Data Warehouse odbudowuje każdy indeks zreplikowanej tabeli jako część rekonstrukcji. Indeksy należy używać tylko w przypadku bardziej wydajne podejścia są większe niż koszty odbudowywania indeksów.  
 
### <a name="batch-data-loads"></a>Ilości danych w partii
Podczas ładowania danych w zreplikowanych tabelach, spróbuj zminimalizować odtwarza przez przetwarzanie wsadowe obciążeń razem. Przed uruchomieniem instrukcji "select", należy wykonać wsadowej obciążenia.

Na przykład tego wzorca obciążenia ładuje dane z czterech źródeł i wywołuje cztery odtwarza. 

- Załaduj ze źródła 1.
- Instrukcja SELECT wyzwalaczy odbudować 1.
- Załaduj ze źródła 2.
- Instrukcja SELECT wyzwalaczy odbudować 2.
- Załaduj ze źródła 3.
- Instrukcja SELECT wyzwalaczy odbudować 3.
- Załaduj ze źródła 4.
- Instrukcja SELECT wyzwalaczy odbudować 4.

Na przykład tego wzorca obciążenia ładuje z czterech źródeł danych, ale tylko wywołuje jeden ponownej kompilacji.

- Załaduj ze źródła 1.
- Załaduj ze źródła 2.
- Załaduj ze źródła 3.
- Załaduj ze źródła 4.
- Odbuduj wyzwalaczy instrukcji SELECT.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Odbuduj zreplikowanej tabeli po załadowaniu partii
Aby zapewnić czas na wykonanie zapytania spójny, należy wziąć pod uwagę wymuszania kompilacji zreplikowanych tabelach po załadowaniu partii. W przeciwnym razie pierwszej kwerendy będą nadal używać przenoszenie danych do wykonania zapytania. 

To zapytanie używa [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) DMV zreplikowanych tabel, które zostały zmodyfikowane, ale nie został odbudowany.

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
 
Aby wyzwolić kompilowania, uruchom następującą instrukcję w każdej tabeli w powyższych danych wyjściowych. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Kolejne kroki 
Aby utworzyć zreplikowanej tabeli, użyj jednej z tych instrukcji:

- [Utwórz tabelę (magazyn danych Azure SQL)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [Utwórz TABLE AS SELECT (magazyn danych Azure SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Omówienie rozproszonej tabel, zobacz [rozproszonych tabel](sql-data-warehouse-tables-distribute.md).



