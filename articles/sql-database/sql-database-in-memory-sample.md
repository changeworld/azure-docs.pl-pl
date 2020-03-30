---
title: Próbka w pamięci
description: Wypróbuj technologie w pamięci bazy danych SQL azure z OLTP i przykładem magazynu kolumn.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: e7e7fc44d5f8b46a66c698d3a33ceeab5b8625c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73810324"
---
# <a name="in-memory-sample"></a>Próbka w pamięci

Technologie w pamięci w usłudze Azure SQL Database umożliwiają zwiększenie wydajności aplikacji i potencjalnie obniżenie kosztów bazy danych. Korzystając z technologii w pamięci w usłudze Azure SQL Database, można osiągnąć poprawę wydajności przy różnych obciążeniach.

W tym artykule zobaczysz dwa przykłady, które ilustrują użycie oltp w pamięci, a także indeksy magazynu kolumn w usłudze Azure SQL Database.

Aby uzyskać więcej informacji, zobacz:
- [Omówienie oltp w pamięci i scenariusze użycia](https://msdn.microsoft.com/library/mt774593.aspx) (zawiera odwołania do studiów przypadku klienta i informacje, aby rozpocząć)
- [Dokumentacja dla oltp w pamięci](https://msdn.microsoft.com/library/dn133186.aspx)
- [Przewodnik po indeksach magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybrydowe przetwarzanie transakcyjne/analityczne (HTAP), znane również jako [analiza operacyjna w czasie rzeczywistym](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Zainstaluj przykład OLTP w pamięci

Przykładową bazę danych AdventureWorksLT można utworzyć za pomocą kilku kliknięć w [witrynie Azure portal](https://portal.azure.com/). Następnie kroki opisane w tej sekcji wyjaśniają, jak można wzbogacić bazę danych AdventureWorksLT za pomocą obiektów OLTP w pamięci i zademonstrować korzyści z wydajności.

Aby uzyskać bardziej uproszczone, ale bardziej atrakcyjne wizualnie demo wydajności dla oltp w pamięci, zobacz:

- Wydanie: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Kod źródłowy: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Kroki instalacji

1. W [witrynie Azure portal](https://portal.azure.com/)utwórz bazę danych Premium lub Business Critical na serwerze. Ustaw **źródło** do adventureworkslt przykładowej bazy danych. Aby uzyskać szczegółowe instrukcje, zobacz [Tworzenie pierwszej bazy danych SQL platformy Azure](sql-database-single-database-get-started.md).

2. Połącz się z bazą danych za pomocą programu SQL Server Management Studio [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx).

3. Skopiuj [skrypt Transact-SQL W pamięci](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) do schowka. Skrypt T-SQL tworzy niezbędne obiekty w pamięci w przykładowej bazie danych AdventureWorksLT, która została utworzona w kroku 1.

4. Wklej skrypt T-SQL do programu SSMS, a następnie wykonaj skrypt. Klauzula `MEMORY_OPTIMIZED = ON` CREATE TABLE instrukcje są kluczowe. Przykład:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Błąd 40536


Jeśli podczas uruchamiania skryptu T-SQL pojawia się błąd 40536, uruchom następujący skrypt T-SQL, aby sprawdzić, czy baza danych obsługuje pamięć w pamięci:


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Wynik **0** oznacza, że w pamięci nie jest obsługiwany, a **1** oznacza, że jest obsługiwany. Aby zdiagnozować problem, upewnij się, że baza danych znajduje się w warstwie usługi Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Informacje o utworzonych elementach zoptymalizowanych pod kątem pamięci

**Tabele**: Przykład zawiera następujące tabele zoptymalizowane pod kątem pamięci:

- Product_inmem SalesLT
- SalesOrderHeader_inmem SalesLT
- SalesOrderDetail_inmem SalesLT
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Tabele zoptymalizowane pod kątem pamięci można sprawdzić za pośrednictwem **Eksploratora obiektów** w programie SSMS. Kliknij prawym **Tables** > przyciskiem myszy pozycję > Ustawienia**filtru filtrów** > **Filter Settings**tabel**jest zoptymalizowany pod kątem pamięci**. Wartość jest równa 1.


Możesz też zbadać widoki katalogu, takie jak:


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Natywnie skompilowana procedura składowana:** Można sprawdzić saleslt.usp_InsertSalesOrder_inmem za pomocą kwerendy widoku katalogu:


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Uruchamianie przykładowego obciążenia OLTP

Jedyną różnicą między następującymi dwiema *procedurami składowanym* jest to, że pierwsza procedura używa wersji tabel zoptymalizowanych pod kątem pamięci, podczas gdy druga procedura używa zwykłych tabel na dysku:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** _ondisk**usp_InsertSalesOrder**


W tej sekcji, widać, jak korzystać z poręcznego **narzędzia ostress.exe** do wykonywania dwóch procedur przechowywanych na stresujących poziomach. Można porównać, jak długo trwa do dwóch przebiegów naprężenia, aby zakończyć.


Po uruchomieniu pliku ostress.exe zaleca się przekazywanie wartości parametrów przeznaczonych dla obu następujących elementów:

- Uruchom dużą liczbę równoczesnych połączeń, używając -n100.
- Mieć każdą pętlę połączenia setki razy, używając -r500.


Warto jednak zacząć od znacznie mniejszych wartości, takich jak -n10 i -r50, aby upewnić się, że wszystko działa.


### <a name="script-for-ostressexe"></a>Skrypt dla ostress.exe


W tej sekcji jest wyświetlany skrypt T-SQL osadzony w naszym wierszu polecenia ostress.exe. Skrypt używa elementów, które zostały utworzone przez skrypt T-SQL, który został zainstalowany wcześniej.


Poniższy skrypt wstawia przykładowe zamówienie sprzedaży z pięcioma elementami zamówienia do następujących *tabel*zoptymalizowanych pod kątem pamięci:

- SalesOrderHeader_inmem SalesLT
- SalesOrderDetail_inmem SalesLT


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


Aby *_ondisk* wersja poprzedniego skryptu T-SQL dla ostress.exe, należy zastąpić oba wystąpienia podciągu *_inmem* *_ondisk*. Te zamienniki wpływają na nazwy tabel i procedur przechowywanych.


### <a name="install-rml-utilities-and-ostress"></a>Instalowanie narzędzi RML i`ostress`


W idealnym przypadku można zaplanować uruchomienie pliku ostress.exe na maszynie wirtualnej platformy Azure (VM). Maszynę [wirtualną platformy Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) można utworzyć w tym samym regionie geograficznym platformy Azure, w którym znajduje się baza danych AdventureWorksLT. Ale zamiast tego możesz uruchomić ostress.exe na swoim laptopie.


Na maszynie wirtualnej lub na dowolnym hostze zainstaluj narzędzia języka RML (Replay Markup Language). Narzędzia obejmują ostress.exe.

Aby uzyskać więcej informacji, zobacz:
- Ostress.exe dyskusji w [przykładowej bazie danych dla in-memory OLTP](https://msdn.microsoft.com/library/mt465764.aspx).
- [Przykładowa baza danych dla protokołu OLTP w pamięci](https://msdn.microsoft.com/library/mt465764.aspx).
- [Blog do instalacji ostress.exe](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-_inmem-stress-workload-first"></a>Najpierw uruchom *obciążenie _inmem* stresem


Do uruchomienia naszego wiersza polecenia ostress.exe można użyć okna *RML Cmd Prompt.* Parametry wiersza polecenia `ostress` bezpośrednio do:

- Uruchom jednocześnie 100 połączeń (-n100).
- Każde połączenie uruchomi skrypt T-SQL 50 razy (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Aby uruchomić poprzedni wiersz polecenia ostress.exe:


1. Zresetuj zawartość danych bazy danych, uruchamiając następujące polecenie w ssms, aby usunąć wszystkie dane wstawione przez poprzednie przebiegi:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Skopiuj tekst poprzedniego wiersza polecenia ostress.exe do schowka.

3. Zastąp `<placeholders>` dla parametrów -S -U -P -d z prawidłowymi wartościami rzeczywistymi.

4. Uruchom edytowany wiersz polecenia w oknie cmd RML.


#### <a name="result-is-a-duration"></a>Wynik jest czasem trwania


Po `ostress.exe` zakończeniu zapisuje czas trwania uruchomienia jako końcowy wiersz danych wyjściowych w oknie Cmd RML. Na przykład krótszy przebieg testowy trwał około 1,5 minuty:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Resetuj, edytuj dla *_ondisk,* a następnie uruchom ponownie


Po wykonaniu wyniku z *_inmem* uruchomienia wykonaj następujące czynności dla *_ondisk* uruchomienia:


1. Zresetuj bazę danych, uruchamiając następujące polecenie w ssms, aby usunąć wszystkie dane wstawione przez poprzednie uruchomienie:
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Edytuj wiersz polecenia ostress.exe, aby zastąpić wszystkie *_inmem* *_ondisk*.

3. Uruchom ponownie plik ostress.exe po raz drugi i przechwyć wynik czasu trwania.

4. Ponownie zresetuj bazę danych (w celu odpowiedzialnego usunięcia, co może być dużą ilością danych testowych).


#### <a name="expected-comparison-results"></a>Oczekiwane wyniki porównania

Nasze testy w pamięci wykazały, że wydajność poprawiła się o `ostress` dziewięć **razy** dla tego uproszczonego obciążenia, z uruchomieniem na maszynie Wirtualnej platformy Azure w tym samym regionie platformy Azure co baza danych.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Zainstaluj przykład analizy w pamięci


W tej sekcji można porównać we/wy i wyniki statystyk, gdy używasz indeksu magazynu kolumn w porównaniu z tradycyjnym indeksem drzewa b.


W przypadku analizy w czasie rzeczywistym na obciążenie OLTP często najlepiej jest użyć indeksu magazynu kolumn nieklastrowanych. Aby uzyskać szczegółowe informacje, zobacz [Indeksy magazynu kolumn opisane](https://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Przygotowanie testu analizy magazynu kolumn


1. Użyj witryny Azure portal, aby utworzyć nową bazę danych AdventureWorksLT z przykładu.
   - Użyj tej dokładnej nazwy.
   - Wybierz dowolną warstwę usług Premium.

2. Skopiuj [sql_in memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) do schowka.
   - Skrypt T-SQL tworzy niezbędne obiekty w pamięci w przykładowej bazie danych AdventureWorksLT, która została utworzona w kroku 1.
   - Skrypt tworzy tabelę Wymiar i dwie tabele faktów. Tabele faktów są wypełniane 3,5 miliona wierszy każdy.
   - Wykonanie skryptu może potrwać 15 minut.

3. Wklej skrypt T-SQL do programu SSMS, a następnie wykonaj skrypt. Słowo kluczowe **COLUMNSTORE** w **instrukcji CREATE INDEX** ma kluczowe znaczenie, tak jak w:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Ustaw AdventureWorksLT na poziom zgodności 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Poziom 130 nie jest bezpośrednio związany z funkcjami w pamięci. Ale poziom 130 zazwyczaj zapewnia szybszą wydajność kwerendy niż 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Kluczowe tabele i indeksy magazynu kolumn


- Dbo. FactResellerSalesXL_CCI jest tabelą, która ma indeks klastrowanego magazynu kolumn, który ma zaawansowaną kompresję na poziomie *danych.*

- Dbo. FactResellerSalesXL_PageCompressed jest tabelą, która ma równoważny indeks klastrowane regularne, który jest skompresowany tylko na poziomie *strony.*


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Kluczowe zapytania do porównania indeksu magazynu kolumn


Istnieje [kilka typów zapytań T-SQL, które można uruchomić,](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) aby zobaczyć ulepszenia wydajności. W kroku 2 w skrypcie T-SQL należy zwrócić uwagę na tę parę zapytań. Różnią się one tylko w jednej linii:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Indeks klastrowanego magazynu kolumn znajduje się w tabeli\_FactResellerSalesXL CCI.

Poniższy fragment skryptu T-SQL drukuje statystyki dla IO i TIME dla kwerendy każdej tabeli.


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

W bazie danych z warstwy cenowej P2 można oczekiwać około dziewięć razy przyrost wydajności dla tej kwerendy przy użyciu indeksu magazynu kolumn klastrowanych w porównaniu z tradycyjnym indeksem. Z P15, można oczekiwać około 57 razy wzrost wydajności przy użyciu indeksu magazynu kolumn.



## <a name="next-steps"></a>Następne kroki

- [Szybki start 1: Technologie OLTP w pamięci dla szybszej wydajności T-SQL](https://msdn.microsoft.com/library/mt694156.aspx)

- [Używanie protokołu OLTP w pamięci w istniejącej aplikacji SQL platformy Azure](sql-database-in-memory-oltp-migration.md)

- [Monitorowanie pamięci OLTP w pamięci](sql-database-in-memory-oltp-monitoring.md) dla oltp w pamięci


## <a name="additional-resources"></a>Zasoby dodatkowe

#### <a name="deeper-information"></a>Głębsze informacje

- [Dowiedz się, jak kworum podwaja obciążenie bazy danych kluczy przy jednoczesnym obniżeniu jednostki DTU o 70% dzięki oltpowi w pamięci w bazie danych SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Wpis w blogu OTP w pamięci w programie Azure SQL Database](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Dowiedz się więcej o oltp w pamięci](https://msdn.microsoft.com/library/dn133186.aspx)

- [Dowiedz się więcej o indeksach magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx)

- [Dowiedz się więcej o analizie operacyjnej w czasie rzeczywistym](https://msdn.microsoft.com/library/dn817827.aspx)

- Zobacz [typowe wzorce obciążeń i zagadnienia dotyczące migracji](https://msdn.microsoft.com/library/dn673538.aspx) (w którym opisano wzorce obciążeń, w których oltp w pamięci często zapewnia znaczny wzrost wydajności)

#### <a name="application-design"></a>Projekt aplikacji

- [Oltp w pamięci (optymalizacja w pamięci)](https://msdn.microsoft.com/library/dn133186.aspx)

- [Używanie protokołu OLTP w pamięci w istniejącej aplikacji SQL platformy Azure](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Narzędzia

- [Portal Azure](https://portal.azure.com/)

- [Sql Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
