---
title: Przykład z usługą Azure SQL Database In-Memory | Dokumentacja firmy Microsoft
description: Wypróbuj usługi Azure SQL Database w pamięci technologii przykład OLTP i magazynu kolumn.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 2aa98c3958f1dffeb8adbad5e91a11f397d4a9fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035744"
---
# <a name="in-memory-sample"></a>Przykładowe w pamięci

Technologie w pamięci w usłudze Azure SQL Database pozwalają zwiększyć wydajność aplikacji i zmniejszyć koszt bazy danych. Przy użyciu technologii w pamięci w usłudze Azure SQL Database, można osiągnąć ulepszenia wydajności z różnych obciążeń.

W tym artykule zobaczysz dwa przykłady ilustrujące użycie OLTP w pamięci, a także indeksach magazynu kolumn w usłudze Azure SQL Database.

Aby uzyskać więcej informacji, zobacz:
- [Omówienie OLTP w pamięci i scenariusze użycia](https://msdn.microsoft.com/library/mt774593.aspx) (zawiera odwołania do analizy przypadków klientów i informacji, aby rozpocząć)
- [Dokumentacja dla OLTP w pamięci](https://msdn.microsoft.com/library/dn133186.aspx)
- [Przewodnik po indeksach magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybrydowe transakcyjnych/przetwarzanie analityczne (HTAP), nazywany także [analizy operacyjnej w czasie rzeczywistym](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Instalowanie przykładowej OLTP w pamięci

Można utworzyć przykładowej bazy danych AdventureWorksLT za pomocą kilku kliknięć w [witryny Azure portal](https://portal.azure.com/). Następnie w tej sekcji opisano sposób wzbogacanie bazy danych AdventureWorksLT przy użyciu obiektów OLTP w pamięci i pokazują korzyści wydajności.

Aby uzyskać bardziej uproszczony, ale bardziej atrakcyjne wizualnie demonstrację wydajności OLTP w pamięci Zobacz:

- Release: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Kod źródłowy: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Kroki instalacji

1. W [witryny Azure portal](https://portal.azure.com/), tworzenie bazy danych Premium lub krytyczne dla działania firmy na serwerze. Ustaw **źródła** do przykładowej bazy danych AdventureWorksLT. Aby uzyskać szczegółowe instrukcje, zobacz [tworzenie pierwszej bazy danych Azure SQL](sql-database-single-database-get-started.md).

2. Połączenia z bazą danych przy użyciu programu SQL Server Management Studio [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx).

3. Kopiuj [OLTP w pamięci języka Transact-SQL skrypt](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) do Schowka. Skrypt T-SQL tworzy obiekty niezbędne w pamięci w bazie danych AdventureWorksLT utworzonego w kroku 1.

4. Wklej skryptu T-SQL w programie SSMS, a następnie uruchom skrypt. `MEMORY_OPTIMIZED = ON` Instrukcji CREATE TABLE klauzuli są niezwykle istotne. Na przykład:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Błąd 40536


Jeśli wystąpi błąd 40536 po uruchomieniu skryptu T-SQL, uruchom następujący skrypt języka T-SQL, aby sprawdzić, czy baza danych obsługuje w pamięci:


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Wynikiem **0** oznacza, że w pamięci nie jest obsługiwane, oraz **1** oznacza, że jest ono obsługiwane. Aby zdiagnozować problem, upewnij się, że baza danych znajduje się w warstwie usług Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Temat utworzonych elementów zoptymalizowane pod kątem pamięci

**Tabele**: Przykład zawiera następujące tabele zoptymalizowane pod kątem pamięci:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Można sprawdzić tabele zoptymalizowane pod kątem pamięci, za pośrednictwem **Eksplorator obiektów** w programie SSMS. Kliknij prawym przyciskiem myszy **tabel** > **filtru** > **ustawienia filtra** > **jest zoptymalizowana pod kątem pamięci**. Wartość jest równa 1.


Lub możesz zbadać widoków katalogów, takich jak:


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Skompilowane w sposób macierzysty procedura składowana**: SalesLT.usp_InsertSalesOrder_inmem można sprawdzić za pomocą widoku wykazu kwerendy:


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Uruchom przykładowe obciążenie OLTP

Jedyną różnicą między tymi dwoma następujące *procedur składowanych* jest, że pierwsza procedura korzysta z wersji tabel zoptymalizowanych pod kątem pamięci, podczas gdy drugi procedury używa regularnego tabel na dysku:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


W tej sekcji możesz zobaczyć sposób użycia przydatną **ostress.exe** narzędzie do wykonywania dwóch procedur składowanych na poziomach stresujące. Można porównać, jak długo trwa do uruchamiania dwa obciążenia zakończyć.


Po uruchomieniu ostress.exe, zaleca się, aby były przekazywane wartości parametrów, przeznaczone dla obu z następujących czynności:

- Uruchamianie dużej liczby równoczesnych połączeń przy użyciu - n100.
- Dzięki mają każdej pętli połączenia setki razy, za pomocą parametru-r500.


Można uruchomić wartościami znacznie mniejszy, takich jak - n10 i - r50 upewnić się, że wszystko działa.


### <a name="script-for-ostressexe"></a>Skrypt ostress.exe


Ta sekcja wyświetla skryptu T-SQL, osadzonego w naszym ostress.exe wiersza polecenia. Skrypt używa elementów, które zostały utworzone przez skrypt języka T-SQL, który został wcześniej zainstalowany.


Poniższy skrypt wstawia przykładowe zamówienie sprzedaży z pięcioma elementami linii do następujących, zoptymalizowane pod kątem pamięci *tabel*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Aby *_ondisk* wersji ostress.exe poprzedniego skryptu T-SQL, należy zastąpić oba wystąpienia *_inmem* podciąg z *_ondisk*. Te zmiany mają wpływ na nazwy tabel i procedur składowanych.


### <a name="install-rml-utilities-and-ostress"></a>Zainstaluj narzędzia RML i `ostress`


W idealnym przypadku będzie planuje się uruchamianie ostress.exe na maszynie wirtualnej (VM) platformy Azure. Należy utworzyć [maszyny Wirtualnej platformy Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) w tym samym regionie geograficznym platformy Azure zawierającej bazy danych AdventureWorksLT. Ale możesz zamiast tego uruchomić ostress.exe na laptopie.


Na maszynie Wirtualnej lub na hoście niezależnie od rodzaju wybierz, zainstaluj narzędzia powtarzania Markup Language (RML). Narzędzia te obejmują ostress.exe.

Aby uzyskać więcej informacji, zobacz:
- Omówienie ostress.exe w [przykładowa baza danych dla OLTP w pamięci](https://msdn.microsoft.com/library/mt465764.aspx).
- [Przykładowe bazy danych do OLTP w pamięci](https://msdn.microsoft.com/library/mt465764.aspx).
- [Na blogu poświęconym instalacji ostress.exe](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Uruchom *_inmem* najpierw podkreślają obciążenia


Możesz użyć *RML Cmd monitu* okna, aby uruchomić wiersz polecenia naszych ostress.exe. Bezpośrednie parametry wiersza polecenia `ostress` do:

- Jednoczesne uruchamianie 100 połączeń (-n100).
- Mieć każde połączenie, uruchom skrypt języka T-SQL, 50 godzin (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Aby uruchomić poprzedni wiersz polecenia ostress.exe:


1. Resetuj zawartość danych bazy danych, uruchamiając następujące polecenie w programie SSMS, aby usunąć wszystkie dane, które został wstawiony przez wszystkie poprzednie uruchomienia:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Skopiuj tekst poprzedni wiersz polecenia ostress.exe do Schowka.

3. Zastąp `<placeholders>` parametrów -S - U -P - d przy użyciu prawidłowych wartości rzeczywistych.

4. Edytowany wiersza polecenia są uruchamiane w oknie polecenia RML.


#### <a name="result-is-a-duration"></a>Wynikiem jest wartość typu duration


Gdy `ostress.exe` zostanie zakończone, zapisuje czas trwania testu jako jego ostatni wiersz danych wyjściowych w oknie polecenia RML. Na przykład krótsze przebieg testu trwało około 1,5 minuty:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Przywróć, edytować *_ondisk*, uruchom ponownie


Po utworzeniu wynik *_inmem* uruchamiania, wykonaj następujące kroki, aby uzyskać *_ondisk* Uruchom:


1. Resetuj bazy danych, uruchamiając następujące polecenie w programie SSMS umożliwia usunięcie wszystkich danych, który został wstawiony przez poprzedniego uruchomienia:
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Edytowanie wiersza polecenia ostress.exe, aby zamienić wszystkie *_inmem* z *_ondisk*.

3. Uruchom ponownie ostress.exe po raz drugi i przechwytywania wyników czasu trwania.

4. Ponownie Zresetuj bazy danych (związanych z usuwaniem odpowiedzialnego, jakie mogą być duże ilości danych testowych).


#### <a name="expected-comparison-results"></a>Porównanie oczekiwanych wyników

Nasze testy w pamięci wykazały, że wydajność poprawia **dziewięciokrotnie** dla tego obciążenia uproszczony, za pomocą `ostress` uruchomionych na maszynie Wirtualnej platformy Azure, w tym samym regionie platformy Azure jako bazy danych.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Zainstaluj próbki analizy w pamięci


W tej sekcji możesz porównać wyniki operacji We/Wy i statystyk podczas korzystania z indeksu magazynu kolumn a indeksem tradycyjnych b drzewa.


Analiza w czasie rzeczywistym na obciążenia OLTP często jest najlepiej użyć nieklastrowany indeks magazynu kolumn. Aby uzyskać więcej informacji, zobacz [opisane indeksów magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Przygotowanie testu analizy magazynu kolumn


1. Użyj witryny Azure portal, aby utworzyć nową bazą danych AdventureWorksLT z próbki.
   - Użyj takiej samej nazwie.
   - Wybierz wszystkie warstwy Premium.

2. Kopiuj [sql_in memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) do Schowka.
   - Skrypt T-SQL tworzy obiekty niezbędne w pamięci w bazie danych AdventureWorksLT utworzonego w kroku 1.
   - Skrypt utworzy w tabeli wymiarów i dwóch tabel faktów. Tabele faktów są wypełniane przy użyciu 3,5 miliona wierszy.
   - Skrypt może potrwać 15 minut.

3. Wklej skryptu T-SQL w programie SSMS, a następnie uruchom skrypt. **Magazynu kolumn** — słowo kluczowe w **CREATE INDEX** instrukcji ma kluczowe znaczenie, jak:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Ustaw poziom zgodności 130 AdventureWorksLT:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    130 poziom nie jest bezpośrednio związane z funkcjami w pamięci. Jednak poziom 130 zwykle zapewnia lepszą wydajność zapytań, niż 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Kluczowe tabele i indeksy magazynu kolumn


- dbo. FactResellerSalesXL_CCI jest Tabela mająca klastrowany indeks magazynu kolumn, który udostępnia zaawansowane kompresję przy *danych* poziom.

- dbo. FactResellerSalesXL_PageCompressed jest tabeli, która zawiera równoważne regularne indeks klastrowany, które są kompresowane tylko w *strony* poziom.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Klucza zapytania, aby porównać indeksu magazynu kolumn


Istnieją [kilka typów zapytań T-SQL, które można uruchomić](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) się poprawę wydajności. W kroku 2 w skrypcie języka T-SQL należy zwrócić uwagę na to para zapytania. Różnią się tylko w jednym wierszu:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Klastrowany indeks magazynu kolumn jest FactResellerSalesXL\_tabeli z indeksem CCI.

Poniższy fragment skryptu T-SQL wyświetla statystyki dla operacji We/Wy i czasu dla zapytania w każdej tabeli.


```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

W bazie danych przy użyciu warstwy cenowej P2 można oczekiwać, że około dziewięć razy przyrost wydajności dla tego zapytania przy użyciu indeksu klastrowanego magazynu kolumn w porównaniu z tradycyjnym indeksu. Za pomocą P15 można oczekiwać, że około 57 razy przyrost wydajności przy użyciu indeksu magazynu kolumn.



## <a name="next-steps"></a>Kolejne kroki

- [Szybki Start 1: Technologie OLTP w pamięci, aby zwiększyć wydajność języka T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)

- [Użyj OLTP w pamięci w istniejącej aplikacji usługi Azure SQL](sql-database-in-memory-oltp-migration.md)

- [Pojemność magazynu OLTP w pamięci Monitor](sql-database-in-memory-oltp-monitoring.md) dla OLTP w pamięci


## <a name="additional-resources"></a>Dodatkowe zasoby

#### <a name="deeper-information"></a>Bardziej szczegółowych informacji

- [Dowiedz się, jak kworum rozwiązanie quorum zwiększa dwukrotnie obciążenie klucza bazy danych przy zmniejszeniu liczby jednostek DTU o 70% z OLTP w pamięci w bazie danych SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [OLTP w pamięci z wpisu w blogu bazy danych Azure SQL](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Dowiedz się więcej o OLTP w pamięci](https://msdn.microsoft.com/library/dn133186.aspx)

- [Dowiedz się więcej o indeksach magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx)

- [Więcej informacji na temat analizy operacyjnej w czasie rzeczywistym](https://msdn.microsoft.com/library/dn817827.aspx)

- Zobacz [typowych wzorców obciążenia i zagadnienia dotyczące migracji](https://msdn.microsoft.com/library/dn673538.aspx) (która opisuje wzorce obciążenia, w którym OLTP w pamięci zapewnia często znaczący wzrost wydajności)

#### <a name="application-design"></a>Projekt aplikacji

- [(Optymalizacja w pamięci) OLTP w pamięci](https://msdn.microsoft.com/library/dn133186.aspx)

- [Użyj OLTP w pamięci w istniejącej aplikacji usługi Azure SQL](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Narzędzia

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
