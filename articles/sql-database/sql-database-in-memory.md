---
title: Technologie usługi Azure SQL bazy danych w pamięci | Dokumentacja firmy Microsoft
description: Technologie usługi Azure SQL bazy danych w pamięci znacznie zwiększyć wydajność transakcyjne i obciążeń analizy.
services: sql-database
author: jodebrui
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: jodebrui
ms.openlocfilehash: 89e5497c10f3e24dd84333f095a9bd7c0017ea88
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647871"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optymalizacja wydajności za pomocą technologii w pamięci w bazie danych SQL

Dzięki użyciu technologii w pamięci w bazie danych SQL Azure, można osiągnąć ulepszenia wydajności z różnych obciążeń: transakcyjna (transakcyjnego przetwarzania online (OLTP)), analytics (online analytical processing (OLAP)) i mieszanego (hybrydowe przetwarzanie analityczne/transakcji (HTAP)). Ze względu na większą wydajność zapytań i przetwarzania transakcji technologie w pamięci też pomóc Ci będzie zmniejszenie kosztów. Zwykle nie trzeba uaktualnić warstwę cenową bazy danych, aby osiągnąć wzrost wydajności. W niektórych przypadkach, nawet można zmniejszyć warstwę cenową, podczas nadal występuje ulepszenia wydajności z technologiami w pamięci.

Poniżej przedstawiono dwa przykłady sposobu pomógł znacznie poprawić wydajność OLTP w pamięci:

- Za pomocą OLTP w pamięci [rozwiązań biznesowych kworum był w stanie dwukrotnie ich obciążenie poprawienie Dtu 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).
    - Oznacza jednostek dtu w warstwie *jednostka transakcji bazy danych*, i zawiera pomiaru wykorzystania zasobów.
- Poniżej film wideo przedstawia znaczne ulepszenia w zużycie zasobów z przykładowe obciążenie: [OLTP w pamięci wideo bazy danych SQL Azure](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
    - Aby uzyskać więcej informacji, zobacz w blogu: [OLTP w pamięci w blogu blogu bazy danych SQL Azure](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

Technologie w pamięci są dostępne wszystkie bazy danych w warstwie Premium, w tym baz danych w puli elastycznej Premium.

Poniższe wideo objaśniono potencjalny wzrost wydajności z technologiami w pamięci w bazie danych SQL Azure. Należy pamiętać, że bardziej wydajne, zawsze wyświetlany zależy od wielu czynników, takich jak rodzaju obciążenia i dane, wzorca dostępu do bazy danych i tak dalej.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Baza danych SQL Azure zawiera następujące technologie w pamięci:

- *OLTP w pamięci* zwiększa transakcji i zmniejsza jej opóźnienie przetwarzania transakcji. Scenariusze, w których warto skorzystać z OLTP w pamięci są: przetwarzanie takich jak handlowych i gier, wprowadzanie danych z urządzeń IoT, buforowanie ładowania danych i tabeli tymczasowej i scenariusze zmiennej tabeli lub zdarzenia transakcji wysokiej przepustowości.
- *Klastrowane indeksy magazynu kolumn* ograniczyć wpływ sieci magazynowania (maksymalnie 10 razy) i zwiększyć wydajność dla raportowania i zapytań analiz. Możesz może być używany z tabel faktów w Twojej składnic danych programów do dopasowania większej ilości danych w bazie danych i zwiększyć wydajność. Ponadto służy go z danych historycznych w operacyjnej bazie danych do archiwizacji i można zbadać maksymalnie 10 razy więcej danych.
- *Klastrowanych indeksów magazynu kolumn* HTAP pomocy można uzyskać wgląd w czasie rzeczywistym w firmie za pomocą zapytań operacyjnej bazy danych bezpośrednio, bez potrzeby uruchamiania kosztowne wyodrębniania, przekształcania i ładowania (ETL) proces i poczekaj, aż Magazyn danych, który będzie zapełniony. Klastrowanych indeksów magazynu kolumn bardzo szybkie wykonywanie zapytania analityczne w bazie danych OLTP, przy jednoczesnym umożliwianiu zmniejsza wpływ na obciążenie operacyjną.
- Można również mieć kombinację tabeli zoptymalizowanej pod kątem pamięci z indeksem magazynu kolumn. To połączenie umożliwia przetwarzanie transakcji bardzo szybko, a *jednocześnie* bardzo szybko uruchomić zapytania analityczne na tych samych danych.

Zarówno indeksy magazynu kolumn i OLTP w pamięci zostały część produktu SQL Server od 2012 i 2014 r. odpowiednio. Azure SQL Database i programu SQL Server udostępnianie tego samego wykonania technologii w pamięci. Idąc dalej, nowych funkcji do tych technologii są wydawane w bazie danych SQL Azure, przed wprowadzeniem w programie SQL Server.

W tym artykule opisano aspekty indeksy OLTP w pamięci i magazynu kolumn, które są specyficzne dla bazy danych SQL Azure i zawiera również przykłady:
- Zostanie wyświetlony wpływu tych technologii limity rozmiaru magazynu i danych.
- Zobaczysz jak zarządzać Przenoszenie baz danych używających tych technologii między różnych warstw cenowych.
- Zostanie wyświetlone dwa — przykłady ilustrujące stosowania OLTP w pamięci, a także indeksy magazynu kolumn w bazie danych SQL Azure.

Skorzystaj z następujących zasobów, aby uzyskać więcej informacji.

Aby uzyskać szczegółowe informacje na temat technologii sieci:

- [Omówienie OLTP w pamięci i scenariusze użycia](https://msdn.microsoft.com/library/mt774593.aspx) (zawiera odwołania do analizy przypadków i informacje, aby rozpocząć)
- [Dokumentacja OLTP w pamięci](http://msdn.microsoft.com/library/dn133186.aspx)
- [Przewodnik indeksy magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybrydowe transakcyjnej/przetwarzanie analityczne (HTAP), nazywany także [operacyjne analiz w czasie rzeczywistym](https://msdn.microsoft.com/library/dn817827.aspx)

Szybkie Elementarz na OLTP w pamięci: [Szybki Start 1: technologii OLTP w pamięci szybciej T-SQL wydajności](http://msdn.microsoft.com/library/mt694156.aspx) (inny artykuł, aby rozpocząć pracę)

Szczegółowe wideo na temat technologii sieci:

- [OLTP w pamięci w usłudze Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (zawierającą pokaz zwiększenia wydajności i kroki do odtworzenia te wyniki samodzielnie)
- [Wideo OLTP w pamięci: Co to jest i gdy/jak z niego korzystać](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)
- [Indeks magazynu kolumn: Analiza w pamięci wideo z konferencji Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

## <a name="storage-and-data-size"></a>Rozmiar magazynu i danych

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Limit rozmiaru i magazynu danych dla OLTP w pamięci

OLTP w pamięci zawiera tabele zoptymalizowane pod kątem pamięci, które są używane do przechowywania danych użytkownika. Te tabele są wymagane, aby zmieścić ją w pamięci. Ponieważ zarządzanie pamięci bezpośrednio w usłudze SQL Database, mamy pojęcie limit przydziału dla danych użytkownika. Tę koncepcję jest określany jako *magazynu OLTP w pamięci*.

Każda baza danych z obsługiwanych autonomiczny warstwa cenowa i każda pula elastyczna warstwa cenowa zawiera pewne magazynu OLTP w pamięci. Zobacz [limity zasobów na podstawie jednostek dtu w warstwie](sql-database-dtu-resource-limits.md) i [limity zasobów na podstawie vCore](sql-database-vcore-resource-limits.md).

Następujące elementy są wliczane do Twojej zakończenia magazynu OLTP w pamięci:

- Wiersze danych aktywnego użytkownika w tabelach zoptymalizowanych pod kątem pamięci i zmiennych tabel. Należy pamiętać, że stare wersje wiersza nie są wliczane do centralnych zasad dostępu.
- Indeksów tabel zoptymalizowanych pod kątem pamięci.
- Nakłady operacyjne operacji ALTER TABLE.

Jeśli naciśniesz centralnych zasad dostępu, komunikat o błędzie "limit przydziału" i nie jesteś już możliwość wstawiania lub aktualizowania danych. Aby uniknąć tego błędu, Usuń dane, lub zwiększ warstwy cenowej bazy danych lub puli.

Aby uzyskać więcej informacji dotyczących monitorowania użycia magazynu OLTP w pamięci i konfigurowania alertów, gdy naciśniesz prawie centralnych zasad dostępu, zobacz [Monitor w pamięci magazynu](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Temat pul elastycznych

O elastycznych pulach magazynu OLTP w pamięci jest współużytkowana przez wszystkie bazy danych w puli. W związku z tym użycia w jednej bazie danych może wpłynąć na innych baz danych. Są dwa środki zaradcze dla tego:

- Skonfiguruj `Max-eDTU` lub `MaxvCore` dla baz danych, które jest mniejszy niż liczba jednostek eDTU lub vCore na pulę jako całość. Maksymalna caps wykorzystanie magazynu OLTP w pamięci, w dowolnej bazy danych w puli, rozmiar, umożliwiająca liczby jednostek eDTU.
- Skonfiguruj `Min-eDTU` lub `MinvCore` większą niż 0. Ta minimalna wielkość gwarantuje, że każda baza danych w puli ma ilość dostępnego magazynu OLTP w pamięci, umożliwiająca skonfigurowanego `Min-eDTU` lub `vCore`.

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Rozmiar danych i magazynu dla indeksów magazynu kolumn

Indeksy magazynu kolumn nie są wymagane do mieści się w pamięci. W związku z tym tylko limit na rozmiar indeksów jest maksymalny rozmiar bazy danych ogólnej, które opisano w [na podstawie jednostek dtu w warstwie model kupna](sql-database-service-tiers-dtu.md) i [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers-vcore.md) artykułów.

Gdy używasz klastrowane indeksy magazynu kolumn, kolumnowy kompresji jest używane do przechowywania tabeli podstawowej. Kompresja ta może znacznie ograniczyć wpływ magazynu danych użytkownika, co oznacza, że można zmieścić większej ilości danych w bazie danych. I kompresji można go zwiększyć z [kolumnowy kompresji archiwizacji](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression). Stopień kompresji, które pozwalają osiągnąć zależy od rodzaju dane, ale 10 razy kompresji nie jest nietypowa sytuacja.

Na przykład jeśli baza danych o maksymalnym rozmiarze 1 terabajtów (TB) i osiągnąć 10 razy kompresji za pomocą indeksy magazynu kolumn, można umieścić łącznie 10 TB danych użytkownika w bazie danych.

Gdy używasz klastrowanych indeksów magazynu kolumn tabeli podstawowej jest nadal przechowywane w formacie tradycyjnego magazynu wierszy. W związku z tym oszczędności pojemności magazynu nie są big z klastrowane indeksy magazynu kolumn. Jednak jeśli liczba indeksów nieklastrowanych tradycyjnych z indeksem magazynu kolumn w jednym, również widzieć ogólną oszczędności rozmiaru magazynu dla tabeli.

## <a name="moving-databases-that-use-in-memory-technologies-between-pricing-tiers"></a>Przenoszenie baz danych korzystających z technologii w pamięci między warstw cenowych

Brak Nigdy nie wszelkie niezgodności lub inne problemy podczas uaktualniania do wyższej warstwy cenowej, takich jak Standard do wersji Premium. Dostępne funkcje i zasoby tylko zwiększyć.

Jednak zmiana wersji na starszą warstwy cenowej może niekorzystnie wpłynąć na bazy danych. Wpływ są szczególnie widoczne, gdy można obniżyć z Premium Standard lub podstawowa, gdy baza danych zawiera obiekty OLTP w pamięci. Tabele zoptymalizowane pod kątem pamięci są niedostępne po obniżania (nawet jeśli są one widoczne). Te same kwestie podczas opuszczania warstwy cenowej puli elastycznej, lub przenoszenia bazy danych z technologiami w pamięci w standardowej lub podstawowa puli elastycznej.

### <a name="in-memory-oltp"></a>Przetwarzanie OLTP w pamięci

*Zmiana wersji na starszą Basic/standard*: OLTP w pamięci nie jest obsługiwane w bazach danych w warstwie standardowa lub Basic. Ponadto nie można przenieść bazę danych, która zawiera wszystkie obiekty OLTP w pamięci do warstwy standardowa lub Basic.

Brak programowy sposób zrozumieć, czy dany bazy danych obsługuje OLTP w pamięci. Można wykonywać następujące zapytanie języka Transact-SQL:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Jeśli zapytanie zwraca **1**, OLTP w pamięci jest obsługiwana w tej bazie danych.

Przed obniżyć bazy danych na podstawowy/Standard, Usuń wszystkie tabele zoptymalizowane pod kątem pamięci i typy tabel, a także wszystkich modułów skompilowanych w sposób macierzysty T-SQL. Następujące kwerendy Zidentyfikuj wszystkie obiekty, które muszą zostać usunięte przed bazy danych można zmienić na standardowy/Basic:

```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

*Zmiana wersji na starszą do dolnej warstwy Premium*: dane w tabelach zoptymalizowanych pod kątem pamięci musi mieścić się w pamięci OLTP w pamięci, która jest skojarzona z warstwy cenowej bazy danych lub jest dostępny w puli elastycznej. Jeśli użytkownik próbuje zmniejszyć warstwę cenową lub przenieść bazę danych do puli, która nie ma wystarczająco dużo dostępnego magazynu OLTP w pamięci, kończy się niepowodzeniem.

### <a name="columnstore-indexes"></a>Indeksy magazynu kolumn

*Zmiana wersji na starszą Basic lub Standard*: indeksy magazynu kolumn są obsługiwane tylko w warstwie cenowej Premium i w warstwie standardowa S3 i powyżej, a nie na warstwie podstawowej. Obniżyć bazy danych do warstwy nieobsługiwany lub poziom, indeksu magazynu kolumn staje się niedostępna. System przechowuje indeksu magazynu kolumn, ale nigdy nie wykorzystuje indeksu. Jeśli później uaktualnienie do warstwy obsługiwanych lub poziom indeksu magazynu kolumn jest od razu gotowy do można użyć ponownie.

Jeśli masz **klastrowanych** indeksu magazynu kolumn po obniżania niedostępny całej tabeli. Dlatego zaleca się usunąć wszystkich *klastrowanych* indeksy magazynu kolumn przed obniżyć bazy danych do warstwy nieobsługiwany lub poziom.

*Powrót do dolnej warstwy obsługiwanych lub na poziomie subskrypcji*: ten obniżenia poziomu zakończy się pomyślnie, jeśli całej bazy danych mieści się w obrębie maksymalny rozmiar bazy danych dla elementu docelowego warstwy cenowej lub w ramach dostępnej pojemności magazynu w puli elastycznej. Nie ma żadnego określonego wpływu z indeksów magazynu kolumn.


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Instalowanie przykładowej OLTP w pamięci

Przykładową bazę danych AdventureWorksLT można utworzyć za pomocą kilku kliknięć w [portalu Azure](https://portal.azure.com/). Następnie w tej sekcji opisano sposób można wzbogacić bazy danych AdventureWorksLT z obiektami OLTP w pamięci i Wykaż zwiększenia wydajności.

Aby uzyskać więcej simplistic, ale atrakcyjność wizualną demonstrację wydajności OLTP w pamięci Zobacz:

- Wersja: [w pamięci — oltp pokaz-wersja 1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Kod źródłowy: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Kroki instalacji

1. W [portalu Azure](https://portal.azure.com/), utworzyć Premium lub biznesowe krytyczne (wersja zapoznawcza) bazy danych na serwerze. Ustaw **źródła** do przykładową bazę danych AdventureWorksLT. Aby uzyskać szczegółowe instrukcje, zobacz [utworzyć pierwszą bazę danych Azure SQL](sql-database-get-started-portal.md).

2. Połączenie z bazą danych z programu SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Kopiuj [skryptu OLTP w pamięci języka Transact-SQL](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) do Schowka. Skryptu T-SQL tworzy obiekty niezbędne w pamięci w bazie danych AdventureWorksLT utworzonego w kroku 1.

4. Wkleić skryptu T-SQL w programie SSMS, a następnie uruchom skrypt. `MEMORY_OPTIMIZED = ON` Instrukcji CREATE TABLE klauzuli są niezwykle istotne. Na przykład:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Błąd 40536


Jeśli zostanie wyświetlony błąd 40536 po uruchomieniu skryptu T-SQL, uruchom następujący skrypt T-SQL, aby sprawdzić, czy baza danych obsługuje w pamięci:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


W wyniku **0** oznacza, że w pamięci nie jest obsługiwany, i **1** oznacza, że jest obsługiwana. Aby zdiagnozować problem, upewnij się, że baza danych znajduje się na warstwę Premium.


#### <a name="about-the-created-memory-optimized-items"></a>O utworzonych elementów zoptymalizowanych pod kątem pamięci

**Tabele**: próbka zawiera następujących tabel zoptymalizowanych pod kątem pamięci:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Możesz sprawdzić tabel zoptymalizowanych pod kątem pamięci przy użyciu **Eksplorator obiektów** w programie SSMS. Kliknij prawym przyciskiem myszy **tabel** > **filtru** > **ustawienia filtrowania** > **jest zoptymalizowana pod kątem pamięci**. Wartość jest równa 1.


Lub możesz zbadać widoków katalogów, takich jak:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Procedura składowana skompilowanych w sposób macierzysty**: SalesLT.usp_InsertSalesOrder_inmem można sprawdzić za pomocą widoku wykazu kwerendy:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Uruchom przykładowe obciążenie OLTP

Jedyną różnicą między dwa *procedur składowanych* Pierwsza procedura używa wersji tabel zoptymalizowanych pod kątem pamięci, a druga procedura wykorzystuje zwykłych tabelach na dysku:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


W tej sekcji, zobacz sposób użycia przydatną **ostress.exe** narzędzie do wykonywania dwóch procedur składowanych na poziomach stressful. Możesz porównać, jak długo trwa dla przebiegów dwóch obciążenia zakończyć.


Po uruchomieniu ostress.exe zaleca się, że przekazujesz przeznaczony dla obu z następujących wartości parametrów:

- Uruchamianie dużej liczby równoczesnych połączeń przy użyciu - n100.
- Ma przy każdej pętli połączenia setki razy, za pomocą parametru-r500.


Można jednak zaczynać dużo mniejsze wartości podobnie jak - n10 i - r50 upewnić się, że wszystko działa.


### <a name="script-for-ostressexe"></a>Skrypt dla ostress.exe


Ta sekcja wyświetla skryptu T-SQL, który jest osadzony w naszym ostress.exe wiersza polecenia. Skrypt używa elementów, które zostały utworzone za pomocą skryptu T-SQL, który został wcześniej zainstalowany.


Poniższy skrypt wstawia przykładowe zamówienia sprzedaży z pięciu pozycji do następujących zoptymalizowanych pod kątem pamięci *tabel*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
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


Aby *_ondisk* wersji ostress.exe poprzedniego skryptu T-SQL, należy zastąpić zarówno wystąpień *_inmem* podciąg z *_ondisk*. Te elementy zastępcze wpływa na nazwy tabel i procedur składowanych.


### <a name="install-rml-utilities-and-ostress"></a>Zainstaluj narzędzia RML i ostress


W idealnym przypadku będzie planowane uruchamianie ostress.exe na maszynie wirtualnej platformy Azure (VM). Należy utworzyć [maszyny Wirtualnej Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) w tym samym regionie geograficznym Azure którym znajduje się baza danych AdventureWorksLT. Ale może uruchamiać ostress.exe na laptopie zamiast tego.


Na maszynie Wirtualnej lub na niezależnie od hosta, możesz wybrać, zainstaluj narzędzia powtarzania Markup Language (RML). Narzędzia obejmują ostress.exe.

Aby uzyskać więcej informacji, zobacz:
- Omówienie ostress.exe w [przykładowej bazy danych OLTP w pamięci](http://msdn.microsoft.com/library/mt465764.aspx).
- [Przykładowe bazy danych do OLTP w pamięci](http://msdn.microsoft.com/library/mt465764.aspx).
- [Blogu instalowania ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Uruchom *_inmem* najpierw podkreślają obciążenia


Można użyć *RML Cmd monitu* okno, aby uruchomić wiersz polecenia naszych ostress.exe. Parametry wiersza polecenia bezpośrednie ostress do:

- Równoczesne uruchamianie połączenia o szybkości 100 (-n100).
- Każdy połączenia uruchomienia skryptu T-SQL 50 razy (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Do uruchamiania powyższej ostress.exe wiersza polecenia:


1. Resetowanie zawartości danych bazy danych, uruchamiając następujące polecenie w programie SSMS, aby usunąć wszystkie dane, które został wstawiony przez wszystkie poprzednie działa:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Skopiuj tekst poprzedniego wiersza polecenia ostress.exe do Schowka.

3. Zastąp `<placeholders>` dla parametrów -S - U -P -d przy użyciu prawidłowych wartości rzeczywistych.

4. Edytowany linii polecenia są uruchamiane w oknie RML Cmd.


#### <a name="result-is-a-duration"></a>Wynik jest czas trwania


Po zakończeniu pracy ostress.exe zapisuje czas trwania testu jako jego ostatnim wierszu danych wyjściowych w oknie RML Cmd. Na przykład krótszą uruchomienia testu trwała około 1,5 minuty:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Resetuj, edytowanie *_ondisk*, uruchom ponownie


Po utworzeniu wynik *_inmem* uruchomić, wykonaj następujące kroki dla *_ondisk* Uruchom:


1. Resetuj bazy danych, uruchamiając następujące polecenie w programie SSMS do usuwania wszystkich danych, który został wstawiony przez poprzedniego uruchomienia:
```
EXECUTE Demo.usp_DemoReset;
```

2. Edytuj wiersz polecenia ostress.exe, aby zamienić wszystkie *_inmem* z *_ondisk*.

3. Uruchom ponownie ostress.exe po raz drugi i przechwytywania wynik czasu trwania.

4. Ponownie Zresetuj bazy danych (w przypadku usuwania odpowiedzialne, które mogą być duże ilości danych testowych).


#### <a name="expected-comparison-results"></a>Porównanie oczekiwanego wyników

Nasze testy w pamięci wykazały, że wydajność poprawia **dziewięciokrotnie** tego simplistic obciążenia pracą z ostress uruchomione na maszynie Wirtualnej platformy Azure, w tym samym regionie Azure, ponieważ baza danych.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Instalowanie przykładowej analityka w pamięci


W tej sekcji można porównywać We/Wy i statystyki wyników podczas korzystania z indeksu magazynu kolumn lub indeksu b drzewa tradycyjnych.


Analiza w czasie rzeczywistym na obciążenia OLTP często jest najlepiej użyć nieklastrowany indeks magazynu kolumn. Aby uzyskać więcej informacji, zobacz [opisane indeksy magazynu kolumn](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Przygotowanie testu analityka magazynu kolumn


1. Użyj portalu Azure, aby utworzyć nową bazę danych AdventureWorksLT na podstawie próbki.
 - Użyj takiej samej nazwie.
 - Wybierz wszystkie warstwy usług Premium.

2. Kopiuj [sql_in memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) do Schowka.
 - Skryptu T-SQL tworzy obiekty niezbędne w pamięci w bazie danych AdventureWorksLT utworzonego w kroku 1.
 - Skrypt tworzy tabeli wymiarów i tabel faktów. Tabele faktów są wypełniane przy użyciu 3.5 milion wierszy.
 - Skrypt może potrwać od 15 minut.

3. Wkleić skryptu T-SQL w programie SSMS, a następnie uruchom skrypt. **Magazynu kolumn** — słowo kluczowe w **CREATE INDEX** instrukcji ma kluczowe znaczenie, jak:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Ustaw poziom zgodności 130 AdventureWorksLT:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Poziom 130 nie jest bezpośrednio związane z funkcjami w pamięci. Jednak poziom 130 zwykle zapewnia lepszą wydajność zapytań, niż 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Tabele klucza i indeksy magazynu kolumn


- dbo. FactResellerSalesXL_CCI jest tabeli, która ma klastrowany indeks magazynu kolumn, który udostępnia zaawansowane kompresji w *danych* poziom.

- dbo. FactResellerSalesXL_PageCompressed jest tabeli, która ma równoważne regularne indeks klastrowany, które są kompresowane tylko w *strony* poziom.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Klucza zapytania do porównania indeksu magazynu kolumn


Brak [kilka typów zapytania T-SQL, które można uruchomić](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) wyświetlić ulepszenia wydajności. W kroku 2 w skrypcie T-SQL należy zwrócić uwagę na to pary zapytania. Różnią się tylko w jednym wierszu:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Klastrowany indeks magazynu kolumn jest FactResellerSalesXL\_WIK tabeli.

Poniższy fragment skryptu T-SQL wyświetla statystyki dla We/Wy i godziny dla każdej tabeli zapytania.


```
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

W bazie danych z warstwy cenowej P2 może spodziewać się o dziewięciokrotnie bardziej wydajne dla tego zapytania przy użyciu klastrowanego indeksu magazynu kolumn w porównaniu z tradycyjnym indeksu. Z P15 z replikacją może spodziewać się około 57 razy bardziej wydajne przy użyciu indeksu magazynu kolumn.



## <a name="next-steps"></a>Kolejne kroki

- [Szybki Start 1: Technologii OLTP w pamięci, aby zwiększyć wydajność T-SQL](http://msdn.microsoft.com/library/mt694156.aspx)

- [Użyj OLTP w pamięci w istniejącej aplikacji usługi Azure SQL](sql-database-in-memory-oltp-migration.md)

- [Monitor OLTP w pamięci magazynu](sql-database-in-memory-oltp-monitoring.md) dla OLTP w pamięci


## <a name="additional-resources"></a>Zasoby dodatkowe

#### <a name="deeper-information"></a>Więcej informacji

- [Dowiedz się, jak kworum podwaja obciążenia klucza bazy danych podczas opuszczania jednostek dtu w warstwie 70% z OLTP w pamięci w bazie danych SQL](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [OLTP w pamięci w bazie danych Azure SQL wpis w blogu](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Dowiedz się więcej o OLTP w pamięci](http://msdn.microsoft.com/library/dn133186.aspx)

- [Dowiedz się więcej o indeksy magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx)

- [Dowiedz się więcej o operacyjne analiz w czasie rzeczywistym](http://msdn.microsoft.com/library/dn817827.aspx)

- Zobacz [typowe wzorce obciążeń i zagadnienia dotyczące migracji](http://msdn.microsoft.com/library/dn673538.aspx) (w którym opisano wzorców obciążenia, gdzie OLTP w pamięci zapewnia często znaczący wzrost wydajności)

#### <a name="application-design"></a>Aplikacja — projekt

- [(Optymalizacja w pamięci) OLTP w pamięci](http://msdn.microsoft.com/library/dn133186.aspx)

- [Użyj OLTP w pamięci w istniejącej aplikacji usługi Azure SQL](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Narzędzia

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)
