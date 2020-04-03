---
title: Klasy zasobów do zarządzania obciążeniem
description: Wskazówki dotyczące używania klas zasobów do zarządzania współbieżnością i zasobów obliczeniowych dla zapytań w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 8ac9ff1f46e1d2d0ddaa313499340b4723c7da07
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584265"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>Zarządzanie obciążeniami z klasami zasobów w usłudze Azure Synapse Analytics

Wskazówki dotyczące używania klas zasobów do zarządzania pamięcią i współbieżnością zapytań puli SQL synapse w usłudze Azure Synapse.  

## <a name="what-are-resource-classes"></a>Co to są klasy zasobów

Pojemność wydajności kwerendy jest określana przez klasę zasobów użytkownika.  Klasy zasobów są wstępnie określone limity zasobów w puli SQL Synapse, które regulują zasoby obliczeniowe i współbieżności do wykonywania kwerend. Klasy zasobów mogą pomóc w skonfigurowaniu zasobów dla kwerend, ustawiając limity liczby zapytań uruchamianych jednocześnie i zasobów obliczeniowych przypisanych do każdej kwerendy.  Istnieje kompromis między pamięcią a współbieżnością.

- Mniejsze klasy zasobów zmniejszyć maksymalną pamięć na kwerendę, ale zwiększyć współbieżność.
- Większe klasy zasobów zwiększają maksymalną ilość pamięci na kwerendę, ale zmniejszają współbieżność.

Istnieją dwa typy klas zasobów:

- Klasy zasobów statycznych, które są dobrze przystosowane do zwiększonej współbieżności na rozmiar zestawu danych, który jest stały.
- Dynamiczne klasy zasobów, które są dobrze dostosowane do zestawów danych, które powiększają się i wymagają zwiększonej wydajności w miarę skalowania poziomu usług.

Klasy zasobów używają gniazd współbieżności do pomiaru zużycia zasobów.  [Gniazda współbieżności](#concurrency-slots) są wyjaśnione w dalszej części tego artykułu.

- Aby wyświetlić wykorzystanie zasobów dla klas zasobów, zobacz [Limity pamięci i współbieżności](memory-concurrency-limits.md).
- Aby dostosować klasę zasobów, można uruchomić kwerendę pod innym użytkownikiem lub zmienić członkostwo [w klasie zasobów bieżącego użytkownika.](#change-a-users-resource-class)

### <a name="static-resource-classes"></a>Statyczne klasy zasobów

Klasy zasobów statycznych przydzielają taką samą ilość pamięci, niezależnie od bieżącego poziomu wydajności, który jest mierzony w [jednostkach magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md). Ponieważ kwerendy uzyskać taką samą alokację pamięci, niezależnie od poziomu wydajności, [skalowanie w dół magazynu danych](quickstart-scale-compute-portal.md) umożliwia więcej zapytań do uruchomienia w ramach klasy zasobów.  Klasy zasobów statycznych są idealne, jeśli wolumin danych jest znany i stały.

Klasy zasobów statycznych są implementowane przy następujących wstępnie zdefiniowanych rolach bazy danych:

- staticrc10
- staicrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Dynamiczne klasy zasobów

Klasy zasobów dynamicznych przydzielają zmienną ilość pamięci w zależności od bieżącego poziomu usługi. Klasy zasobów statycznych są korzystne dla większej współbieżności i statycznych woluminów danych, dynamiczne klasy zasobów są lepiej dostosowane do rosnącej lub zmiennej ilości danych.  Po skalowaniu do większego poziomu usługi zapytania automatycznie uzyskać więcej pamięci.  

Klasy zasobów dynamicznych są implementowane przy następujących wstępnie zdefiniowanych rolach bazy danych:

- mały
- mediumrc (średni
- większyc
- xlargerc ( xlargerc )

Alokacja pamięci dla każdej klasy zasobów jest następująca. 

| Poziom usług  | mały           | mediumrc (średni               | większyc                | xlargerc ( xlargerc )               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25%               | 25%                    | 25%                    | 70%                    |
| DW200c         | 12.5%             | 12.5%                  | 22%                    | 70%                    |
| DW300c         | 8%                | 10%                    | 22%                    | 70%                    |
| DW400c         | 6.25%             | 10%                    | 22%                    | 70%                    |
| DW500c.         | 5%                | 10%                    | 22%                    | 70%                    |
| DW1000c do<br> DW30000c | 3%       | 10%                    | 22%                    | 70%                    |



### <a name="default-resource-class"></a>Domyślna klasa zasobu

Domyślnie każdy użytkownik jest członkiem klasy zasobów dynamicznych **smallrc**.

Klasa zasobów administratora usługi jest ustalona na smallrc i nie można zmienić.  Administrator usługi jest użytkownikiem utworzonym podczas procesu inicjowania obsługi administracyjnej.  Administrator usługi w tym kontekście jest login określony dla "Logowania administratora serwera" podczas tworzenia nowej puli SYNAPSE SQL z nowym serwerem.

> [!NOTE]
> Użytkownicy lub grupy zdefiniowane jako administrator usługi Active Directory są również administratorami usług.
>
>

## <a name="resource-class-operations"></a>Operacje klasy zasobów

Klasy zasobów mają na celu zwiększenie wydajności dla działań związanych z zarządzaniem danymi i manipulacją. Złożone zapytania mogą również korzystać z uruchamiania w ramach klasy dużych zasobów. Na przykład wydajność kwerendy dla dużych sprzężeń i sortowania można poprawić, gdy klasa zasobów jest wystarczająco duży, aby włączyć kwerendę do wykonania w pamięci.

### <a name="operations-governed-by-resource-classes"></a>Operacje regulowane przez klasy zasobów

Te operacje są regulowane przez klasy zasobów:

- WSTAWIANIE, AKTUALIZOWANIE, USUWANIE
- SELECT (podczas wykonywania zapytań o tabele użytkowników)
- ALTER INDEX - ODBUDUJ lub REORGANIZUJ
- PRZEBUDUJ TABELĘ ZMIAN
- CREATE INDEX
- TWORZENIE INDEKSU MAGAZYNU KOLUMN KLASTROWANYCH
- TWORZENIE TABELI JAKO WYBIERZ (CTAS)
- Ładowanie danych
- Operacje przenoszenia danych prowadzone przez usługę przenoszenia danych (DMS)

> [!NOTE]  
> Instrukcje SELECT dotyczące dynamicznych widoków zarządzania (DMV) lub innych widoków systemowych nie podlegają żadnym limitom współbieżności. System można monitorować niezależnie od liczby zapytań wykonywanych na nim.
>
>

### <a name="operations-not-governed-by-resource-classes"></a>Operacje nie podlegające klasom zasobów

Niektóre kwerendy są zawsze uruchamiane w klasie zasobów smallrc, nawet jeśli użytkownik jest członkiem większej klasy zasobów. Te kwerendy zwolnione nie są wliczane do limitu współbieżności. Na przykład jeśli limit współbieżności wynosi 16, wielu użytkowników może wybierać z widoków systemu bez wpływu na dostępne gniazda współbieżności.

Następujące instrukcje są zwolnione z klas zasobów i zawsze działają w smallrc:

- TWORZENIE LUB UPUSZCZANIE TABELI
- ZMIEŃ TABELĘ ... PRZEŁĄCZ, PODZIEL LUB SCAL PARTYCJĘ
- WYŁĄCZ INDEKS ZMIAN
- DROP INDEX
- TWORZENIE, AKTUALIZOWANIE lub UPUŚĆ STATYSTYKI
- OBCINANIE TABELI
- ZMIEŃ AUTORYZACJĘ
- TWORZENIE LOGOWANIA
- TWORZENIE, ZMIENIANIE lub UPUSZCZANIE UŻYTKOWNIKA
- PROCEDURA TWORZENIA, ZMIANY LUB UPUSZCZANIA
- TWORZENIE lub WIDOK UPUSZCZANIA
- WSTAW WARTOŚCI
- WYBIERZ z widoków systemu i DMV
- Wyjaśnić
- Dbcc

<!--
Removed as these two are not confirmed / supported under SQL DW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Gniazda współbieżności

Gniazda współbieżności są wygodnym sposobem śledzenia zasobów dostępnych do wykonania kwerendy. Są jak bilety, które można kupić, aby zarezerwować miejsca na koncercie, ponieważ liczba miejsc jest ograniczona. Całkowita liczba gniazd współbieżności na magazyn danych jest określana przez poziom usługi. Zanim kwerenda może rozpocząć wykonywanie, musi być w stanie zarezerwować wystarczającą liczbę gniazd współbieżności. Po zakończeniu kwerendy zwalnia swoje gniazda współbieżności.  

- Kwerenda uruchomiona z 10 gniazdami współbieżności może uzyskać dostęp do 5 razy więcej zasobów obliczeniowych niż kwerenda uruchomiona z 2 gniazdami współbieżności.
- Jeśli każde zapytanie wymaga 10 gniazd współbieżności i istnieje 40 gniazd współbieżności, a następnie tylko 4 kwerendy można uruchomić jednocześnie.

Tylko kwerendy regulowane zasobami zużywają gniazda współbieżności. Zapytania systemowe i niektóre kwerendy trywialne nie zużywają żadnych gniazd. Dokładna liczba wykorzystanych gniazd współbieżności jest określana przez klasę zasobów kwerendy.

## <a name="view-the-resource-classes"></a>Wyświetlanie klas zasobów

Klasy zasobów są implementowane jako wstępnie zdefiniowane role bazy danych. Istnieją dwa typy klas zasobów: dynamiczny i statyczny. Aby wyświetlić listę klas zasobów, należy użyć następującej kwerendy:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Zmienianie klasy zasobów użytkownika

Klasy zasobów są implementowane przez przypisanie użytkowników do ról bazy danych. Gdy użytkownik uruchamia kwerendę, kwerenda jest uruchamiana z klasą zasobów użytkownika. Na przykład jeśli użytkownik jest członkiem roli bazy danych staticrc10, ich zapytania są uruchamiane z małymi ilościami pamięci. Jeśli użytkownik bazy danych jest członkiem ról bazy danych xlargerc lub staticrc80, ich zapytania są uruchamiane z dużą ilością pamięci.

Aby zwiększyć klasę zasobów użytkownika, użyj [sp_addrolemember,](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) aby dodać użytkownika do roli bazy danych klasy dużego zasobu.  Poniższy kod dodaje użytkownika do roli bazy danych largerc.  Każde żądanie otrzymuje 22% pamięci systemowej.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Aby zmniejszyć klasę zasobów, użyj [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  Jeśli "loaduser" nie jest członkiem lub innych klas zasobów, przechodzą do domyślnej klasy zasobów smallrc z 3% dotacji pamięci.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Pierwszeństwo klasy zasobu

Użytkownicy mogą być członkami wielu klas zasobów. Gdy użytkownik należy do więcej niż jednej klasy zasobów:

- Dynamiczne klasy zasobów mają pierwszeństwo przed statycznymi klasami zasobów. Na przykład, jeśli użytkownik jest członkiem zarówno mediumrc(dynamic) i staticrc80 (statyczne), kwerendy są uruchamiane z mediumrc.
- Większe klasy zasobów mają pierwszeństwo przed mniejszymi klasami zasobów. Na przykład jeśli użytkownik jest członkiem mediumrc i largerc, kwerendy są uruchamiane z largerc. Podobnie, jeśli użytkownik jest członkiem zarówno staticrc20 i statirc80, kwerendy są uruchamiane z alokacjami zasobów staticrc80.

## <a name="recommendations"></a>Zalecenia

>[!NOTE]
>Należy wziąć pod uwagę wykorzystanie możliwości zarządzania obciążeniem[(izolacja obciążenia,](sql-data-warehouse-workload-isolation.md) [klasyfikacja](sql-data-warehouse-workload-classification.md) i [znaczenie)](sql-data-warehouse-workload-importance.md)dla większej kontroli nad obciążeniem i przewidywalnej wydajności.  
>
>

Zaleca się utworzenie użytkownika, który jest przeznaczony do uruchamiania określonego typu kwerendy lub operacji ładowania. Nadaj użytkownikowi stałą klasę zasobów zamiast częstej zmiany klasy zasobów. Klasy zasobów statycznych zapewniają większą ogólną kontrolę nad obciążeniem, dlatego zalecamy użycie klas zasobów statycznych przed rozważeniem klas zasobów dynamicznych.

### <a name="resource-classes-for-load-users"></a>Klasy zasobów dla użytkowników ładowania

`CREATE TABLE`domyślnie używa indeksów klastrowanego magazynu kolumn. Kompresja danych do indeksu magazynu kolumn jest operacją intensywnie korzystającą z pamięci, a ciśnienie pamięci może zmniejszyć jakość indeksu. Ciśnienie pamięci może prowadzić do konieczności wyższej klasy zasobów podczas ładowania danych. Aby upewnić się, że obciążenia mają wystarczającą ilość pamięci, można utworzyć użytkownika, który jest przeznaczony do uruchamiania obciążeń i przypisać tego użytkownika do wyższej klasy zasobów.

Pamięć wymagana do wydajnego przetwarzania obciążeń zależy od charakteru załadowanej tabeli i rozmiaru danych. Aby uzyskać więcej informacji na temat wymagań dotyczących pamięci, zobacz [Maksymalizacja jakości grupy wierszy](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Po określeniu zapotrzebowania na pamięć wybierz, czy użytkownik ładowania ma być przypisany do statycznej lub dynamicznej klasy zasobów.

- Użyj klasy zasobów statycznych, gdy wymagania dotyczące pamięci tabeli mieszczą się w określonym zakresie. Obciążenia są uruchamiane z odpowiednią pamięcią. Podczas skalowania magazynu danych obciążenia nie potrzebują więcej pamięci. Za pomocą klasy zasobów statycznych alokacje pamięci pozostają stałe. Ta spójność oszczędza pamięć i umożliwia uruchamianie większej liczby zapytań jednocześnie. Zaleca się, aby nowe rozwiązania najpierw używały klas zasobów statycznych, ponieważ zapewniają one większą kontrolę.
- Użyj klasy zasobów dynamicznych, gdy wymagania dotyczące pamięci tabeli różnią się znacznie. Obciążenia mogą wymagać więcej pamięci niż bieżący poziom DWU lub cDWU zapewnia. Skalowanie magazynu danych dodaje więcej pamięci do ładowania operacji, co pozwala na szybsze wykonywanie obciążeń.

### <a name="resource-classes-for-queries"></a>Klasy zasobów dla kwerend

Niektóre zapytania są wymagające dużej mocy obliczeniowej, a niektóre nie.  

- Wybierz klasę zasobów dynamicznych, gdy kwerendy są złożone, ale nie wymagają wysokiej współbieżności.  Na przykład generowanie raportów dziennych lub tygodniowych jest sporadycznym zapotrzebowaniem na zasoby. Jeśli raporty przetwarzają duże ilości danych, skalowanie magazynu danych zapewnia więcej pamięci do istniejącej klasy zasobów użytkownika.
- Wybierz klasę zasobów statycznych, gdy oczekiwania zasobów różnią się w ciągu dnia. Na przykład klasa zasobów statycznych działa dobrze, gdy magazyn danych jest wyszukiwany przez wiele osób. Podczas skalowania magazynu danych ilość pamięci przydzielonej użytkownikowi nie zmienia się. W związku z tym więcej zapytań mogą być wykonywane równolegle w systemie.

Odpowiednie dotacje pamięci zależą od wielu czynników, takich jak ilość danych, których dotyczy kwerenda, charakter schematów tabeli i różne sprzężenia, wybierz i grupuj predykaty. Ogólnie rzecz biorąc przydzielanie większej ilości pamięci umożliwia szybsze wypełnianie zapytań, ale zmniejsza ogólną współbieżność. Jeśli współbieżność nie jest problemem, nadmierne przydzielanie pamięci nie szkodzi przepływności.

Aby dostroić wydajność, należy użyć różnych klas zasobów. Następna sekcja zawiera procedurę składowaną, która pomaga dowiedzieć się najlepszą klasę zasobów.

## <a name="example-code-for-finding-the-best-resource-class"></a>Przykładowy kod do znajdowania najlepszej klasy zasobów

Aby obliczyć współbieżność i przyznanie pamięci na klasę zasobów w danej klasie zasobów, można użyć następującej określonej procedury składowej, aby obliczyć współbieżność i przyznanie pamięci na klasę zasobów w danej klasie zasobów i najlepszą klasę zasobów dla operacji CCI intensywnie korzystających z pamięci w tabeli CCI nieobjętej partycjoną w danej klasie zasobu:

Oto cel tej procedury składowanej:

1. Aby wyświetlić przyznanie współbieżności i pamięci dla klasy zasobu w danej SLO. Użytkownik musi podać wartość NULL dla schematu i nazwy tabeli, jak pokazano w tym przykładzie.  
2. Aby zobaczyć najlepszą klasę zasobów dla operacji CCI intensywnie korzystających z pamięci (ładowanie, kopiowanie tabeli, indeks przebudowy itp.) w tabeli CCI nieobjętej partycjonowania w danej klasie zasobów. Przechowywany proc używa schematu tabeli, aby dowiedzieć się wymagane przyznanie pamięci.

### <a name="dependencies--restrictions"></a>Zależności & ograniczenia

- Ta procedura składowana nie jest przeznaczony do obliczania zapotrzebowania na pamięć dla tabeli cci podzielonych na partycje.
- Ta procedura składowana nie uwzględnia wymagań dotyczących pamięci dla części SELECT CTAS/INSERT-SELECT i zakłada, że jest to select.
- Ta procedura składowana używa tabeli tymczasowej, która jest dostępna w sesji, w której utworzono tę procedurę składowaną.
- Ta procedura składowana zależy od bieżących ofert (na przykład konfiguracji sprzętu, konfiguracji DMS), a jeśli którakolwiek z tych zmian, to przechowywany proc nie będzie działać poprawnie.  
- Ta procedura składowana zależy od istniejących ofert limitu współbieżności i jeśli te zmiany następnie ta procedura składowana nie będzie działać poprawnie.  
- Ta procedura składowana zależy od istniejących ofert klasy zasobów i jeśli te zmiany następnie ta procedura składowana nie będzie działać poprawnie.  

>[!NOTE]  
>Jeśli nie otrzymujesz danych wyjściowych po wykonaniu procedury składowanej z dostarczonymi parametrami, mogą istnieć dwa przypadki.
>
>1. Parametr DW zawiera nieprawidłową wartość SLO
>2. Lub nie ma pasujących klasy zasobów dla operacji CCI w tabeli.
>
>Na przykład w DW100c najwyższa dostępna dotacja pamięci wynosi 1 GB, a jeśli schemat tabeli jest wystarczająco szeroki, aby przekroczyć wymaganie 1 GB.

### <a name="usage-example"></a>Przykład użycia

Składnia:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU:Podaj parametr NULL, aby wyodrębnić bieżącą DWU z bazy danych DW lub podaj obsługiwany dwu w postaci "DW100c"
2. @SCHEMA_NAME:Podaj nazwę schematu tabeli
3. @TABLE_NAME:Podaj nazwę tabeli odsetek

Przykłady wykonywania tego przechowywane proc:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Poniższa instrukcja tworzy Table1, który jest używany w poprzednich przykładach.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Definicja procedury składowanej

```sql
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS

IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500 
  ELSE Mem*100 
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;

-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania użytkownikami bazy danych i zabezpieczeniami, zobacz [Zabezpieczanie bazy danych w synapse SQL](sql-data-warehouse-overview-manage-security.md). Aby uzyskać więcej informacji o tym, jak większe klasy zasobów mogą poprawić jakość indeksu klastrowanego magazynu kolumn, zobacz [Optymalizacje pamięci dotyczące kompresji magazynu kolumn.](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)

