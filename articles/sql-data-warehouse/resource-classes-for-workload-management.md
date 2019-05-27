---
title: Klasy zasobów do zarządzania obciążeniem — Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące używania klas zasobów do zarządzania współbieżności i zasoby obliczeniowe dla zapytań w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 05/22/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 75bd6e8071717ba755b71f51afcd884539049489
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165980"
---
# <a name="workload-management-with-resource-classes-in-azure-sql-data-warehouse"></a>Zarządzanie obciążeniami przy użyciu klas zasobów w usłudze Azure SQL Data Warehouse

Wskazówki dotyczące używania klas zasobów do zarządzania pamięci i współbieżności dla zapytań w usłudze Azure SQL Data Warehouse.  

## <a name="what-is-workload-management"></a>Co to jest zarządzanie obciążeniami

Zarządzanie obciążeniami zapewnia możliwość optymalizacji ogólną wydajność wszystkich zapytań. Dobrze dostosowane obciążenia wykonuje zapytania i operacji ładowania efektywne, zarówno mocy obliczeniowej i intensywnie korzystających z operacji We/Wy. Usługa SQL Data Warehouse zapewnia funkcje zarządzania obciążeniem dla środowisk z wieloma użytkownikami. Magazyn danych nie jest przeznaczona dla obciążeń z wieloma dzierżawami.

Pojemność wydajności magazynu danych jest określany przez [jednostkami magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md).

- Aby wyświetlić limity pamięci i we wszystkich profilach wydajności, zobacz [limity pamięci i współbieżności](memory-and-concurrency-limits.md).
- Aby dostosować wydajność pojemność, możesz [skalować w górę lub w dół](quickstart-scale-compute-portal.md).

Pojemność wydajności kwerendy jest określany przez klasy zasobów zapytania. W dalszej części tego artykułu opisano klasy zasobów są i jak je dostosowywać.

## <a name="what-are-resource-classes"></a>Co to są klasami zasobów

Pojemność wydajności kwerendy jest określany przez klasy zasobów użytkownika.  Klasy zasobów są wstępnie określane limity zasobów usługi Azure SQL Data Warehouse, które określają sposób zasoby obliczeniowe i współbieżności w celu wykonywania zapytań. Klasy zasobów ułatwia zarządzanie obciążeniem pracą przez ustawianie limitów liczby zapytań, które uruchamiane równolegle i na zasobów obliczeniowych przypisanych do każdego zapytania.  Istnieje zależność między pamięcią i współbieżności.

- Mniejszej klasy zasobów zmniejszyć maksymalną ilość pamięci na zapytanie, ale także zwiększyć współbieżność.
- Większych klas zasobów zwiększanie maksymalnej pamięci na zapytanie, ale zmniejszyć współbieżność.

Istnieją dwa typy klas zasobów:

- Klasy zasobów statycznych, które są odpowiednie dla zwiększoną współbieżność rozmiaru zestawu danych, który został rozwiązany.
- Zasób dynamiczny klasy, które są odpowiednie dla zestawów danych, stają się rozmiar, które potrzebują zwiększenia wydajności, co jest skalowany w poziomie usługi.

Klasy zasobów umożliwia pomiar zużycia zasobów gniazd współbieżności.  [Gniazd współbieżności](#concurrency-slots) zostały wyjaśnione w dalszej części tego artykułu.

- Aby wyświetlić wykorzystania zasobów dla klas zasobów, zobacz [limity pamięci i współbieżności](memory-and-concurrency-limits.md#concurrency-maximums).
- Aby dostosować klasy zasobów, można uruchomić kwerendę przez innego użytkownika lub [Zmienianie klasy zasobów bieżącego użytkownika](#change-a-users-resource-class) członkostwa.

### <a name="static-resource-classes"></a>Statycznych klas zasobów

Statycznych klas zasobów przydzielić tym samym ilość pamięci, niezależnie od tego, bieżący poziom wydajności, która jest mierzona w [jednostkami magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md). Ponieważ zapytania pobierają ten sam alokacji pamięci, niezależnie od poziomu wydajności [skalowanie w poziomie w magazynie danych](quickstart-scale-compute-portal.md) umożliwia więcej zapytań do działania w ramach klasy zasobów.  Statycznych klas zasobów są idealnym rozwiązaniem, jeśli znane jest ilość danych i stałej.

Statycznych klas zasobów są implementowane za pomocą tych wstępnie zdefiniowanych ról bazy danych:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Zasób dynamiczny klas

Dynamicznych klas zasobów przydzielić zmienną ilość pamięci, w zależności od bieżącego poziomu usługi. Statycznych klas zasobów są przydatne w przypadku woluminów danych statycznych i zapewnienia większej współbieżności, klasach zasobów dynamicznych są lepiej dostosowane do rosnącą lub zmienną ilość danych.  Skalowanie w górę na większy poziom usługi, zapytań automatycznie uzyskują więcej pamięci.  

Klasy zasobów dynamicznych są implementowane za pomocą tych wstępnie zdefiniowanych ról bazy danych:

- smallrc
- mediumrc
- largerc
- xlargerc

### <a name="gen2-dynamic-resource-classes-are-truly-dynamic"></a>Gen2 zasób dynamiczny klasy są naprawdę dynamiczne

Kiedy digging szczegółowych klas zasobów dynamicznych na Gen1, są kilka informacji, które zwiększają złożoność dodatkowe do zrozumienia ich zachowania:

**Na Gen1**
- Klasa zasobów smallrc działa przy użyciu modelu stały pamięci, takich jak klasy zasobów statycznych.  Dynamicznie Smallrc zapytań nie może korzystać więcej pamięci, jaką jest zwiększenie poziomu usługi. 
- Jak zmienić poziomy usług, współbieżności dostępnych zapytań może przejść w górę lub w dół.
- Skalowanie poziomu usług nie zapewnia zmianę proporcjonalny przydział do tej samej klasy zasobów pamięci.

**Na Gen2**, klasy zasobów dynamicznych są naprawdę dynamiczne adresowanie punktów wymienionych powyżej.  Nowa reguła jest 3-10-22 — 70 dla alokacji procent pamięci dla klas zasobów małych medium dużych xlarge **niezależnie od poziomu usługi**.  Poniższa tabela zawiera skonsolidowany szczegółowe informacje o wartości procentowych alokacji pamięci i minimalnej liczby równoczesnych zapytań działających niezależnie od poziomu usługi.

| Klasa zasobów | Procent pamięci | Minimalna liczba jednoczesnych kwerend |
|:--------------:|:-----------------:|:----------------------:|
| smallrc        | 3%                | 32                     |
| mediumrc       | 10%               | 10                     |
| largerc        | 22%               | 4                      |
| xlargerc       | 70%               | 1                      |

### <a name="default-resource-class"></a>Domyślna klasa zasobów

Domyślnie każdy użytkownik jest członkiem klasy zasób dynamiczny **smallrc**.

Klasa zasobu administratora usługi jest ustalony na smallrc i nie można jej zmienić.  Administrator usługi to użytkownik utworzony podczas procesu inicjowania obsługi administracyjnej.  Administrator usługi, w tym kontekście jest nazwą logowania, określony dla "logowania administratora serwera" podczas tworzenia nowego wystąpienia SQL Data Warehouse przy użyciu nowego serwera.

> [!NOTE]
> Użytkownicy lub grupy zdefiniowane jako administratora usługi Active Directory są również administratorów usługi.
>
>

## <a name="resource-class-operations"></a>Operacje klasy zasobów

Klasy zasobów są przeznaczone do zwiększenia wydajności działań zarządzania i modyfikowanie danych. Złożonych zapytań można również korzystać z działania pod dużej klasy zasobów. Na przykład kwerendy wydajności duże sprzężenia i sortuje można poprawić, gdy klasa zasobów jest wystarczająco duży, aby umożliwić kwerenda do wykonania w pamięci.

### <a name="operations-governed-by-resource-classes"></a>Operacje regulowane przez klasy zasobów

Te operacje są regulowane przez klasy zasobów:

- WYBIERZ OPCJĘ INSERT, UPDATE, DELETE
- Wybierz (podczas wykonywania zapytania tabele użytkownika)
- Polecenia ALTER INDEX - ponownej kompilacji lub z opcją REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- UTWÓRZ KLASTROWANY INDEKS MAGAZYNU KOLUMN
- CREATE TABLE AS SELECT (CTAS)
- Ładowanie danych
- Operacje przenoszenia danych prowadzonych przez Data Movement Service (DMS)

> [!NOTE]  
> Wybierz instrukcji na dynamicznych widoków zarządzania (DMV) lub inny system, w których widoki nie są zarządzane przez żaden z limitów współbieżności. Można monitorować system niezależnie od liczby wykonywanie w niej zapytań.

### <a name="operations-not-governed-by-resource-classes"></a>Operacji nie podlega klasy zasobów

Niektóre zapytania są zawsze uruchamiane w klasie zasobów smallrc nawet, jeśli użytkownik jest członkiem większej klasy zasobów. Te zapytania wykluczeni są wliczane ograniczenie współbieżności równe. Na przykład jeśli ograniczenie współbieżności równe 16, wielu użytkowników może wybierając z widoków systemowych bez wywierania wpływu na gniazd współbieżności dostępne.

Poniższe instrukcje są wykluczone z klasami zasobów i są zawsze uruchamiane w smallrc:

- Utwórz lub DROP TABLE
- INSTRUKCJA ALTER TABLE... PRZEŁĄCZNIK, dzielenia lub scalania PARTYCJI
- INSTRUKCJA ALTER INDEX WYŁĄCZ
- DROP INDEX
- TWORZENIA, aktualizacji i DROP STATISTICS
- OBCINANIE TABELI
- INSTRUKCJA ALTER AUTORYZACJI
- UTWÓRZ NAZWĘ LOGOWANIA
- CREATE, ALTER i DROP USER
- CREATE, ALTER lub PORZUCIĆ procedury
- Utwórz lub PORZUĆ widok
- WSTAWIANIE WARTOŚCI
- Wybierz z widoków systemowych i dynamicznych widoków zarządzania
- WYJAŚNIONO
- DBCC

<!--
Removed as these two are not confirmed / supported under SQL DW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Gniazd współbieżności

Miejsca współbieżności są wygodnym sposobem śledzenia zasobów dostępnych w celu wykonywania zapytań. Są one takie jak bilety, którzy wykupią celu zarezerwowania miejsc, w połączeniu, ponieważ miejsc siedzących jest ograniczona. Całkowita liczba gniazd współbieżności na magazyn danych jest określany przez poziom usług. Przed rozpoczęciem zapytania podczas wykonywania, musi być w stanie zarezerwować wystarczającej liczby gniazd współbieżności. Po zakończeniu wykonywania zapytania, zwalnia jego gniazd współbieżności.  

- Zapytanie z 10 gniazd współbieżności dostęp 5 razy więcej zasobów obliczeniowych, niż zapytania z 2 gniazd współbieżności.
- Jeśli każde zapytanie wymaga 10 gniazd współbieżności, a ma 40 gniazd współbieżności, tylko 4 zapytania można uruchamiać jednocześnie.

Tylko zasobów podlegają zapytania używanie gniazd współbieżności. Zapytania systemowe i prosta zapytań nie wykorzystasz żadnych gniazd. Dokładna liczba gniazd współbieżności, używane jest ustalana kwerendy klasy zasobów.

## <a name="view-the-resource-classes"></a>Wyświetlanie klas zasobów

Klasy zasobów są implementowane jako wstępnie zdefiniowanych ról bazy danych. Istnieją dwa typy klas zasobów: statycznych i dynamicznych. Aby wyświetlić listę klas zasobów, użyj następującego zapytania:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Zmienianie klasy zasobów użytkownika

Klasy zasobów są implementowane przez przypisywanie użytkowników do ról bazy danych. Gdy użytkownik uruchamia zapytania, kwerenda działa klasy zasobów użytkownika. Na przykład jeśli użytkownik jest członkiem roli bazy danych staticrc10, ich zapytania działały z małej ilości pamięci. Jeśli użytkownik bazy danych jest elementem członkowskim xlargerc lub staticrc80 ról bazy danych, ich zapytania są uruchamiane z dużą ilością pamięci.

Aby zwiększyć klasy zasobów użytkownika, użyj [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) można dodać użytkownika do roli bazy danych o dużej klasy zasobów.  Poniższego kodu, dodaje użytkownika do roli bazy danych largerc.  Każde żądanie pobiera 22% pamięci systemowej.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Aby zmniejszyć klasy zasobów, użyj [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  Jeśli "loaduser" nie jest członkiem lub innych klas zasobów, przejdź do domyślnej klasy zasobów smallrc z przydziałem pamięci 3%.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Pierwszeństwo klasy zasobów

Użytkownicy mogą należeć do wielu klas zasobów. Gdy użytkownik należy do więcej niż jednej klasy zasobów:

- Zasób dynamiczny klasy mają pierwszeństwo przed statycznych klas zasobów. Na przykład jeśli użytkownik jest członkiem mediumrc(dynamic) i staticrc80 (statyczne), zapytania działały z mediumrc.
- Większych klas zasobów mają pierwszeństwo przed mniejszej klasy zasobów. Na przykład jeśli użytkownik jest członkiem mediumrc i largerc, zapytania działały z largerc. Podobnie jeśli użytkownik jest członkiem zarówno staticrc20, jak i statirc80, zapytania działały z staticrc80 alokacji zasobów.

## <a name="recommendations"></a>Rekomendacje

Firma Microsoft zaleca się utworzenie użytkownika, który jest przeznaczony do obsługi określonego typu zapytania lub operacja ładowania. Nadaj tego użytkownika do klasy zasobów trwałe, zamiast Zmienianie klasy zasobów częste. Statycznych klas zasobów sobie większą ogólną kontrolę na obciążenie, dlatego zalecane jest używanie statycznych klas zasobów, zanim będzie można uznać klas dynamicznych zasobów.

### <a name="resource-classes-for-load-users"></a>Klasy zasobów dla użytkowników, obciążenia

`CREATE TABLE` zastosowań klastrowane indeksy magazynu kolumn, domyślnie. Skompresowanie danych do magazynu kolumn indeksu jest operacją wymagającą dużej ilości pamięci i wykorzystanie pamięci może zmniejszyć jakość indeksu. Wykorzystanie pamięci może prowadzić do konieczności wyższej klasie zasobów, podczas ładowania danych. Aby upewnić się, że obciążenie ma za mało pamięci, można utworzyć użytkownika, który jest wyznaczone do uruchamiania obciążeń i przypisz tego użytkownika do wyższej klasie zasobów.

Pamięci, potrzebne do przetwarzania obciążeń efektywnie zależy od charakteru tabeli załadowane i rozmiar danych. Aby uzyskać więcej informacji na temat wymagań dotyczących pamięci, zobacz [jakości i maksymalizując](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Po określeniu wymagania dotyczące pamięci, określ, czy można przypisać użytkownika obciążenia do klasy zasobów statycznych lub dynamicznych.

- Używanie klasy zasobów statycznych, gdy wymagania dotyczące pamięci tabeli mieszczą się w określonym zakresie. Obciążenia są uruchamiane z odpowiedniej ilości pamięci. Skalowanie magazynu danych obciążenia nie ma potrzeby większej ilości pamięci. Za pomocą klasy zasobów statycznych, alokacji pamięci pozostają stałe. Ten spójności oszczędza pamięć i umożliwia więcej zapytań uruchamiać jednocześnie. Zalecane jest użycie statycznych klas zasobów w nowych rozwiązań najpierw te zapewniają większą kontrolę.
- Gdy wymagania dotyczące pamięci tabeli są bardzo zróżnicowane, należy użyć dynamicznej klasy zasobów. Obciążenie może wymagać więcej pamięci niż bieżąca jednostka DWU lub cDWU poziom. Skalowanie magazynu danych dodaje większej ilości pamięci do obsługi obciążenia operacji, umożliwiający obciążenia mogą szybciej.

### <a name="resource-classes-for-queries"></a>Klasy zasobów dla kwerend

Niektóre zapytania są intensywnych obliczeń, a niektóre nie są.  

- Wybierz dynamicznej klasy zasobów, gdy zapytania są skomplikowane, ale nie wysokiej współbieżności.  Na przykład Generowanie raportów codziennie lub co tydzień jest okazjonalne potrzebę zasobów. Jeśli raporty są przetwarzania dużych ilości danych, skalowanie data warehouse zapewnia większej ilości pamięci do istniejącej klasy zasobów użytkownika.
- Wybierz klasy zasobów statycznych, gdy zasób oczekiwania różnią się w ciągu dnia. Na przykład klasa zasób statyczny dobrze działa w przypadku magazynu danych jest wysyłane zapytanie przez wiele osób. Skalowanie magazynu danych, ilość pamięci przydzielonej do użytkownika nie powoduje zmiany. W związku z tym więcej zapytań mogą być wykonywane równolegle w systemie.

Przyznaje właściwe pamięci są zależne od wielu czynników, takich jak ilość danych zniekształcone rodzaju schematy tabeli i różnych sprzężenia, wybierz, a grupa predykatów. Ogólnie rzecz biorąc przydzielenie większej ilości pamięci pozwala szybciej wprowadzać zapytania, ale zmniejsza ogólną współbieżności. Jeśli współbieżność nie jest problemem, nadmiernego przydzielania pamięci nie uszkodzić przepływności.

Do dostrajania wydajności, należy użyć klas innego zasobu. Dalej zapewnia sekcji procedurę składowaną, która ułatwia ustalenie najlepszych klasy zasobów.

## <a name="example-code-for-finding-the-best-resource-class"></a>Przykładowy kod do znajdowania najlepsze klasy zasobów

Można użyć następującej procedury składowanej określony dla [Gen1](#stored-procedure-definition-for-gen1) lub [Gen2](#stored-procedure-definition-for-gen2)— ustalenie pamięci i współbieżności udzielić na klasy zasobów w danym celu SLO i najlepsze klasy zasobów pamięci intensywnie korzystających z indeksem CCI. operacje na niepartycjonowana CCI tabeli w klasie danego zasobu:

W tym miejscu jest celem tej procedury składowanej:

1. Aby zobaczyć współbieżność i przydziału dla klasy zasobów w danym celu SLO pamięci. Użytkownik musi zapewnić NULL tablename i schematu, jak pokazano w poniższym przykładzie.  
2. Aby wyświetlić najważniejsze klasy zasobów dla operacji (obciążenia, tabelę kopiowania, odbudowy indeksu itp.) CCI wymagających dużej ilości pamięci w niepartycjonowana CCI tabeli w klasie danego zasobu. Przechowywanej używa schematu tabeli, aby dowiedzieć się, Przydziel wymaganej ilości pamięci.

### <a name="dependencies--restrictions"></a>Zależności i ograniczeń

- Tej procedury składowanej nie jest przeznaczona do obliczania wymagania dotyczące pamięci dla tabeli partycjonowanej cci.
- Tej procedury składowanej nie przyjmuje pod uwagę wymagania pamięci dla części wybierz CTAS/INSERT-SELECT i przyjęto założenie, że jest SELECT.
- Tę procedurę składowaną używa tabeli tymczasowej, która jest dostępna w sesji, której utworzono tej procedury składowanej.
- Tę procedurę składowaną, zależy od bieżących ofert (na przykład, konfiguracji sprzętu, konfiguracji usługi DMS), a jeśli tej zmiany następnie ta przechowywanej nie będzie działać poprawnie.  
- Tę procedurę składowaną zależy od istniejących ofert limit współbieżności, a w przypadku zmiany są następnie tej procedury składowanej nie będzie działać poprawnie.  
- Tę procedurę składowaną zależy od istniejących ofert klasa zasobów, a w przypadku zmiany są następnie tej procedury składowanej nie będzie działać poprawnie.  

>[!NOTE]  
>Jeśli nie otrzymujesz danych wyjściowych po wykonaniu procedury składowanej z parametrami, pod warunkiem, następnie mogą występować dwa przypadki.
>
>1. Albo parametr magazyn danych zawiera nieprawidłową wartość SLO
>2. Lub brak nie pasującego klasy zasobów dla operacji CCI w tabeli.
>
>Na przykład w DW100, najwyższy dostępny udział pamięci to 400 MB i schemat tabeli jest dostatecznie szeroka, aby między wymaganie 400 MB.

### <a name="usage-example"></a>Przykład użycia

Składnia:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU: Albo podaj parametr o wartości NULL, prowadzenie DWU bieżącej bazie danych Magazynu danych lub dowolnego obsługiwanych jednostek DWU w postaci "DW100"
2. @SCHEMA_NAME: Podaj nazwę schematu tabeli
3. @TABLE_NAME: Podaj nazwę tabeli odsetek

Przykłady wykonywania tej procedury przechowywanej:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Poniższa instrukcja umożliwia utworzenie Table1, który jest używany w powyższych przykładach.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition-for-gen1"></a>Definicja procedury składowanej Gen1

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT -FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
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
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
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
                     WHEN FLOOR(8 -(CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 -(CAST (@DWU_NUM AS FLOAT)/6000))
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
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

### <a name="stored-procedure-definition-for-gen2"></a>Definicja procedury składowanej Gen2

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
  SELECT @DWU = 'DW'+CAST(Nodes*CASE WHEN CPUVer>6 THEN 500 ELSE 100 END AS VARCHAR(10))+CASE WHEN CPUVer>6 THEN 'c' ELSE '' END
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), CPUVer=max(i.cpu_count)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE'
         )A
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
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW1000c', 32, 40, 1, 4, 8, 28, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500c', 32, 60, 1, 6, 13, 42, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000c', 48, 80, 2, 8, 17, 56, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW2500c', 48, 100, 3, 10, 22, 70, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000c', 64, 120, 3, 12, 26, 84, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW5000c', 64, 200, 6, 20, 44, 140, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW6000c', 128, 240, 7, 24, 52, 168, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW7500c', 128, 300, 9, 30, 66, 210, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW10000c', 128, 400, 12, 40, 88, 280, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW15000c', 128, 600, 18, 60, 132, 420, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW30000c', 128, 1200, 36, 120, 264, 840, 1, 2, 4, 8, 16, 32, 64, 128
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
  ,       m1.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc80
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

## <a name="next-step"></a>Następny krok

Aby uzyskać więcej informacji o zarządzaniu bazy danych użytkowników i zabezpieczeń, zobacz [Zabezpieczanie bazy danych w usłudze SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Aby uzyskać więcej informacji na temat sposobu większych klas zasobów może zwiększyć jakość indeksu klastrowanego magazynu kolumn, zobacz [optymalizacji pamięci dla kompresji magazynu kolumn](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
