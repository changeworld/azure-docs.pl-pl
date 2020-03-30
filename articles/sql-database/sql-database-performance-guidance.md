---
title: Wskazówki dotyczące dostrajania wydajności aplikacji i baz danych
description: Dowiedz się więcej o dostrajaniu aplikacji i baz danych bazy danych w celu uzyskania wydajności w usłudze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab; jrasnick
ms.date: 03/10/2020
ms.openlocfilehash: 4f30ebe39d86db7076baa8c29b2a5cf060b07bf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255953"
---
# <a name="tune-applications-and-databases-for-performance-in-azure-sql-database"></a>Dostosowywanie aplikacji i baz danych pod kątem wydajności w bazie danych SQL usługi Azure

Po zidentyfikowaniu problemu z wydajnością, z którym masz do czynienia z bazą danych SQL, ten artykuł jest przeznaczony do pomocy:

- Dostroić aplikację i zastosować kilka najlepszych rozwiązań, które mogą poprawić wydajność.
- Dostrajanie bazy danych przez zmianę indeksów i zapytań, aby bardziej efektywnie pracować z danymi.

W tym artykule założono, że zostały już przepracowane za pośrednictwem [zaleceń doradcy bazy danych](sql-database-advisor.md) usługi Azure SQL Database i [zalecenia dotyczące automatycznego dostrajania](sql-database-automatic-tuning.md)bazy danych azure SQL Database. Zakłada się również, że zostały przejrzane [Przegląd monitorowania i dostrajania](sql-database-monitor-tune-overview.md) i jego powiązanych artykułów związanych z rozwiązywaniem problemów z wydajnością. Ponadto w tym artykule przyjęto założenie, że nie masz zasobów procesora CPU, problem z wydajnością związane z uruchomieniem, które można rozwiązać przez zwiększenie rozmiaru obliczeń lub warstwy usług, aby zapewnić więcej zasobów do bazy danych.

## <a name="tune-your-application"></a>Dostrajanie aplikacji

W tradycyjnych lokalnych SQL Server proces planowania zdolności produkcyjnych początkowe często jest oddzielony od procesu uruchamiania aplikacji w środowisku produkcyjnym. Licencje na sprzęt i produkty są kupowane jako pierwsze, a następnie dostrajanie wydajności. Korzystając z usługi Azure SQL Database, warto przeplatać proces uruchamiania aplikacji i dostrajania jej. Z modelu płacenia za pojemność na żądanie, można dostroić aplikacji do korzystania z minimalnych zasobów potrzebnych teraz, zamiast nadmiernej inicjowania obsługi administracyjnej na sprzęcie na podstawie domysłów przyszłych planów wzrostu dla aplikacji, które często są niepoprawne. Niektórzy klienci mogą zdecydować się nie dostrajać aplikacji, a zamiast tego wybrać opcję nadmiernej aprowizacji zasobów sprzętowych. Takie podejście może być dobrym pomysłem, jeśli nie chcesz zmieniać aplikacji klucza w okresie zajęty. Ale dostrajanie aplikacji można zminimalizować wymagania dotyczące zasobów i obniżyć miesięczne rachunki podczas korzystania z warstw usług w usłudze Azure SQL Database.

### <a name="application-characteristics"></a>Charakterystyka aplikacji

Chociaż warstwy usług Azure SQL Database zostały zaprojektowane w celu zwiększenia stabilności wydajności i przewidywalności aplikacji, niektóre najlepsze rozwiązania mogą pomóc w dostrojenia aplikacji, aby lepiej wykorzystać zasoby w rozmiarze obliczeniowym. Chociaż wiele aplikacji ma znaczny wzrost wydajności po prostu przełączając się na wyższy rozmiar obliczeń lub warstwę usług, niektóre aplikacje wymagają dodatkowego dostrajania, aby korzystać z wyższego poziomu usług. Aby zwiększyć wydajność, należy wziąć pod uwagę dodatkowe dostrajanie aplikacji dla aplikacji, które mają następujące cechy:

- **Aplikacje, które mają powolną wydajność z powodu zachowania "chatty"**

  Aplikacje Chatty wykonać nadmierne operacje dostępu do danych, które są wrażliwe na opóźnienie sieci. Może być konieczne zmodyfikowanie tego rodzaju aplikacji, aby zmniejszyć liczbę operacji dostępu do danych do bazy danych SQL. Na przykład można zwiększyć wydajność aplikacji przy użyciu technik, takich jak przetwarzanie wsadowe kwerend ad hoc lub przenoszenie zapytań do procedur przechowywanych. Aby uzyskać więcej informacji, zobacz [Kwerendy partii](#batch-queries).

- **Bazy danych z intensywnym obciążeniem, które nie mogą być obsługiwane przez cały pojedynczy komputer**

   Bazy danych, które przekraczają zasoby o najwyższym rozmiarze obliczeń w udziale w udziale w udziale w wersji Premium, mogą korzystać ze skalowania obciążenia. Aby uzyskać więcej informacji, zobacz [dzielenie na fragmenty między bazami danych](#cross-database-sharding) i [partycjonowanie funkcjonalne](#functional-partitioning).

- **Aplikacje, które mają kwerendy nieoptymalne**

  Aplikacje, zwłaszcza te w warstwie dostępu do danych, które mają słabo dostrojone zapytania, mogą nie korzystać z wyższego rozmiaru obliczeń. Obejmuje to zapytania, które nie mają klauzuli WHERE, mają brakujące indeksy lub mają przestarzałe statystyki. Te aplikacje korzystają ze standardowych technik dostrajania wydajności zapytań. Aby uzyskać więcej informacji, zobacz [Brakujące indeksy](#identifying-and-adding-missing-indexes) i [dostrajanie i podpowiedzi kwerendy](#query-tuning-and-hinting).

- **Aplikacje, które mają nieoptymalną konstrukcję dostępu do danych**

   Aplikacje, które mają nieodłączne problemy współbieżności dostępu do danych, na przykład zakleszczenie, może nie korzystać z wyższego rozmiaru obliczeń. Należy rozważyć zmniejszenie rund w stosunku do usługi Azure SQL Database przez buforowanie danych po stronie klienta z usługi Azure buforowania lub innej technologii buforowania. Zobacz [Buforowanie warstwy aplikacji](#application-tier-caching).

## <a name="tune-your-database"></a>Dostrajanie bazy danych

W tej sekcji przyjrzymy się niektórym technikom, których można użyć do dostrojenia usługi Azure SQL Database, aby uzyskać najlepszą wydajność dla aplikacji i uruchomić ją przy najniższym możliwym rozmiarze obliczeń. Niektóre z tych technik są zgodne z tradycyjnymi najlepszymi praktykami dostrajania programu SQL Server, ale inne są specyficzne dla usługi Azure SQL Database. W niektórych przypadkach można sprawdzić zużyte zasoby dla bazy danych, aby znaleźć obszary, aby dalej dostrajać i rozszerzać tradycyjne techniki programu SQL Server do pracy w bazie danych SQL Azure.

### <a name="identifying-and-adding-missing-indexes"></a>Identyfikowanie i dodawanie brakujących indeksów

Typowy problem w wydajności bazy danych OLTP dotyczy projektu fizycznej bazy danych. Często schematy bazy danych są projektowane i wysyłane bez testowania na dużą skalę (w ładunku lub w woluminie danych). Niestety wydajność planu kwerend może być dopuszczalne na małą skalę, ale znacznie obniżyć w obszarze wielkości danych na poziomie produkcji. Najczęstszym źródłem tego problemu jest brak odpowiednich indeksów w celu spełnienia filtrów lub innych ograniczeń w kwerendzie. Często brakujące indeksy manifestuje się jako skanowanie tabeli, gdy szukanie indeksu może wystarczyć.

W tym przykładzie wybrany plan kwerend używa skanowania, gdy wystarczy poszukiwanie:

```sql
DROP TABLE dbo.missingindex;
CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;
```

![Plan kwerend z brakującymi indeksami](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Usługa Azure SQL Database może pomóc w znalezieniu i rozwiązaniu typowych brakujących warunków indeksu. DmVs, które są wbudowane w usłudze Azure SQL Database spojrzeć na kompilacje zapytań, w którym indeks znacznie zmniejszyć szacowany koszt uruchomienia kwerendy. Podczas wykonywania kwerendy bazy danych SQL śledzi, jak często każdy plan kwerend jest wykonywany i śledzi szacowaną lukę między planem kwerend wykonywania i wyobrażone, gdzie ten indeks istniał. Za pomocą tych dmvs szybko odgadnąć, które zmiany w projekcie fizycznej bazy danych może poprawić ogólny koszt obciążenia bazy danych i jego rzeczywistego obciążenia.

Za pomocą tej kwerendy można ocenić potencjalne brakujące indeksy:

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

W tym przykładzie kwerenda spowodowało tę sugestię:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

Po jego utworzeniu ta sama instrukcja SELECT wybiera inny plan, który używa wyszukiwania zamiast skanowania, a następnie wykonuje plan bardziej efektywnie:

![Plan kwerend z poprawionymi indeksami](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

Kluczowym spostrzeżeniem jest to, że pojemność we/wy współdzielonego systemu towarowego jest bardziej ograniczona niż w przypadku dedykowanego komputera serwerowego. Istnieje premia na minimalizowanie niepotrzebnych we/wy, aby maksymalnie wykorzystać system w jednostce DTU każdego rozmiaru obliczeń warstw usług Azure SQL Database. Odpowiednie wybory projektu bazy danych fizycznych może znacznie poprawić opóźnienie dla poszczególnych zapytań, poprawić przepływność równoczesnych żądań obsługiwanych na jednostkę skali i zminimalizować koszty wymagane do spełnienia kwerendy. Aby uzyskać więcej informacji na temat brakujących dmvs indeksu, zobacz [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Dostrajanie i podpowiedzi dotyczące zapytań

Optymalizator kwerend w usłudze Azure SQL Database jest podobny do tradycyjnego optymalizatora zapytań programu SQL Server. Większość najlepszych rozwiązań dotyczących dostrajania zapytań i zrozumienia ograniczeń modelu rozumowania dla optymalizatora zapytań dotyczy również usługi Azure SQL Database. Jeśli dostroisz zapytania w usłudze Azure SQL Database, możesz uzyskać dodatkowe korzyści wynikające z zmniejszenia zapotrzebowania na zasoby agregujące. Aplikacja może być w stanie uruchomić przy niższych kosztach niż odpowiednik un-tuned, ponieważ można uruchomić przy mniejszym rozmiarze obliczeń.

Przykład, który jest typowy w programie SQL Server i który ma również zastosowanie do usługi Azure SQL Database jest jak optymalizator kwerendy "wącha" parametry. Podczas kompilacji optymalizator kwerendy ocenia bieżącą wartość parametru, aby ustalić, czy może generować bardziej optymalny plan kwerend. Chociaż ta strategia często może prowadzić do planu kwerend, który jest znacznie szybszy niż plan skompilowany bez znanych wartości parametrów, obecnie działa niedoskonale zarówno w programie SQL Server, jak i w bazie danych SQL Azure. Czasami parametr nie jest wąchany, a czasami parametr jest wąchany, ale wygenerowany plan jest nieoptymalny dla pełnego zestawu wartości parametrów w obciążeniu. Firma Microsoft zawiera wskazówki dotyczące zapytań (dyrektywy), dzięki czemu można określić intencji bardziej celowo i zastąpić domyślne zachowanie parametru wąchania. Często jeśli używasz wskazówek, można naprawić przypadki, w których domyślne zachowanie programu SQL Server lub usługi Azure SQL Database jest niedoskonałe dla określonego obciążenia klienta.

W następnym przykładzie pokazano, jak procesor zapytań może wygenerować plan, który jest nieoptymalny zarówno dla wymagań dotyczących wydajności, jak i zasobów. W tym przykładzie pokazano również, że jeśli używasz wskazówki kwerendy, można zmniejszyć czas wykonywania kwerendy i wymagania dotyczące zasobów dla bazy danych SQL:

```sql
DROP TABLE psptest1;
CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
   WHILE @a < 20000
   BEGIN
     INSERT INTO psptest1(col2) values (1);
     INSERT INTO psptest1(col2) values (@a);
     SET @a += 1;
   END
   COMMIT TRANSACTION
   CREATE INDEX i1 on psptest1(col2);
GO

CREATE PROCEDURE psp1 (@param1 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1
      WHERE col2 = @param1
      ORDER BY col2;
    END
    GO

CREATE PROCEDURE psp2 (@param2 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
      ORDER BY col2
      OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
   END
   GO

CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
GO
```

Kod konfiguracji tworzy tabelę, która ma pochylony rozkład danych. Optymalny plan kwerend różni się w zależności od wybranego parametru. Niestety zachowanie buforowania planu nie zawsze ponownie skompilować kwerendę na podstawie najbardziej typowej wartości parametru. Tak więc jest możliwe dla planu nieoptykonytowego, które mają być buforowane i używane dla wielu wartości, nawet wtedy, gdy inny plan może być lepszym wyborem planu średnio. Następnie plan kwerend tworzy dwie procedury przechowywane, które są identyczne, z tą różnicą, że jeden ma specjalną wskazówkę kwerendy.

```sql
-- Prime Procedure Cache with scan plan
EXEC psp1 @param1=1;
TRUNCATE TABLE t1;

-- Iterate multiple times to show the performance difference
DECLARE @i int = 0;
WHILE @i < 1000
   BEGIN
      EXEC psp1 @param1=2;
      TRUNCATE TABLE t1;
      SET @i += 1;
    END
```

Zaleca się odczekanie co najmniej 10 minut przed rozpoczęciem części 2 przykładu, tak aby wyniki były różne w wynikowych danych telemetrycznych.

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

Każda część tego przykładu próbuje uruchomić sparametryzowaną instrukcję wstawiania 1000 razy (aby wygenerować wystarczające obciążenie do użycia jako zestaw danych testowych). Podczas wykonywania procedur składowanych procesor zapytań sprawdza wartość parametru, która jest przekazywana do procedury podczas jej pierwszej kompilacji (parametr "wąchania"). Procesor buforuje wynikowy plan i używa go do późniejszych wywołań, nawet jeśli wartość parametru jest inna. Optymalny plan może nie być używany we wszystkich przypadkach. Czasami trzeba poprowadzić optymalizatora, aby wybrać plan, który jest lepszy dla przeciętnego przypadku, a nie konkretnego przypadku, od kiedy kwerenda została skompilowana po raz pierwszy. W tym przykładzie plan początkowy generuje plan "skanowania", który odczytuje wszystkie wiersze, aby znaleźć każdą wartość, która pasuje do parametru:

![Dostrajanie kwerend przy użyciu planu skanowania](./media/sql-database-performance-guidance/query_tuning_1.png)

Ponieważ wykonaliśmy procedurę przy użyciu wartości 1, wynikowy plan był optymalny dla wartości 1, ale był nieoptymalny dla wszystkich innych wartości w tabeli. Wynik prawdopodobnie nie jest to, co chcesz, jeśli były wybrać każdy plan losowo, ponieważ plan działa wolniej i zużywa więcej zasobów.

Jeśli uruchomisz test `SET STATISTICS IO` z `ON`zestawem do , praca skanowania logicznego w tym przykładzie odbywa się za kulisami. Widać, że istnieje 1148 odczytów wykonanych przez plan (co jest nieefektywne, jeśli średnia sprawa ma zwrócić tylko jeden wiersz):

![Dostrajanie kwerend przy użyciu skanowania logicznego](./media/sql-database-performance-guidance/query_tuning_2.png)

Druga część przykładu używa wskazówki kwerendy, aby poinformować optymalizatora, aby użyć określonej wartości podczas procesu kompilacji. W takim przypadku wymusza procesor kwerend do ignorowania wartości, która jest `UNKNOWN`przekazywana jako parametr, a zamiast tego zakłada . Odnosi się to do wartości, która ma średnią częstotliwość w tabeli (ignorując pochylenie). Wynikowy plan jest planem opartym na poszukiwaniu, który jest szybszy i zużywa średnio mniej zasobów niż plan w części 1 tego przykładu:

![Dostrajanie kwerend przy użyciu wskazówki dotyczące kwerendy](./media/sql-database-performance-guidance/query_tuning_3.png)

Efekt można zobaczyć w tabeli **sys.resource_stats** (występuje opóźnienie od czasu wykonania testu i gdy dane wypełnia tabelę). W tym przykładzie część 1 wykonana w okresie czasu 22:25:00 i część 2 wykonana o godzinie 22:35:00. Wcześniejsze przedział czasu używane więcej zasobów w tym oknie czasu niż później (ze względu na poprawę wydajności planu).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Przykładowe wyniki dostrajania kwerend](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Chociaż wolumin w tym przykładzie jest celowo mały, wpływ parametrów nieoptykonytowych może być znaczny, szczególnie w większych bazach danych. Różnica, w skrajnych przypadkach, może wynosić między sekundami dla szybkich przypadków i godzin w powolnych przypadkach.

Można sprawdzić **sys.resource_stats,** aby ustalić, czy zasób dla testu zużywa więcej lub mniej zasobów niż inny test. Podczas porównywania danych należy oddzielić czas testów, tak aby nie były one w tym samym 5-minutowym oknie w widoku **sys.resource_stats.** Celem ćwiczenia jest zminimalizowanie całkowitej ilości użytych zasobów, a nie zminimalizowanie szczytowych zasobów. Ogólnie rzecz biorąc, optymalizacja fragmentu kodu pod kątem opóźnienia zmniejsza również zużycie zasobów. Upewnij się, że zmiany wprowadzone w aplikacji są konieczne i że zmiany nie mają negatywnego wpływu na środowisko klienta dla osoby, która może używać wskazówek dotyczących zapytań w aplikacji.

Jeśli obciążenie ma zestaw powtarzających się zapytań, często ma sens przechwytywanie i sprawdzanie poprawności optymalności opcji planu, ponieważ napędza jednostkę minimalnego rozmiaru zasobów wymaganą do hostowania bazy danych. Po sprawdzeniu poprawności, od czasu do czasu ponownie zbadać plany, które pomogą Ci upewnić się, że nie zostały one zdegradowane. Możesz dowiedzieć się więcej o [wskazówkach dotyczących zapytań (Transact-SQL).](https://msdn.microsoft.com/library/ms181714.aspx)

### <a name="very-large-database-architectures"></a>Bardzo duże architektury baz danych

Przed wydaniem warstwy usług [hiperskala dla](sql-database-service-tier-hyperscale.md) pojedynczych baz danych w usłudze Azure SQL Database klienci służy do osiągnięcia limitów pojemności dla poszczególnych baz danych. Te limity pojemności nadal istnieją dla puli baz danych w pulach elastycznych i bazy danych wystąpień w wystąpieniach zarządzanych. W poniższych dwóch sekcjach omówiono dwie opcje rozwiązywania problemów z bardzo dużymi bazami danych w usłudze Azure SQL Database, gdy nie można użyć warstwy usługi hiperskali.

### <a name="cross-database-sharding"></a>Dzielenie na fragmenty między bazami danych

Ponieważ usługa Azure SQL Database działa na sprzęcie towarowym, limity pojemności dla pojedynczej bazy danych są niższe niż w przypadku tradycyjnej lokalnej instalacji programu SQL Server. Niektórzy klienci używają technik dzielenia na fragmenty do rozmieszczania operacji bazy danych w wielu bazach danych, gdy operacje nie mieszczą się w granicach pojedynczej bazy danych w bazie danych SQL Azure. Większość klientów korzystających z technik dzielenia na fragmenty w usłudze Azure SQL Database dzieli swoje dane w jednym wymiarze na wiele baz danych. W tym podejściu należy zrozumieć, że aplikacje OLTP często wykonują transakcje, które mają zastosowanie tylko do jednego wiersza lub do małej grupy wierszy w schemacie.

> [!NOTE]
> Baza danych SQL udostępnia teraz bibliotekę ułatwiającą dzielenie na fragmenty. Aby uzyskać więcej informacji, zobacz [omówienie biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md).

Na przykład jeśli baza danych ma nazwę klienta, zamówienie i szczegóły zamówienia (np. tradycyjna przykładowa baza danych Northwind dostarczana z programem SQL Server), można podzielić te dane na wiele baz danych, grupując klienta ze szczegółami powiązanego zamówienia i zamówienia. Informacji. Możesz zagwarantować, że dane klienta pozostaną w indywidualnej bazie danych. Aplikacja będzie dzielić różnych klientów między bazami danych, skutecznie rozłożenie obciążenia na wiele baz danych. Dzięki dzieleniu na fragmenty klienci nie tylko mogą uniknąć maksymalnego limitu rozmiaru bazy danych, ale usługa Azure SQL Database może również przetwarzać obciążenia, które są znacznie większe niż limity różnych rozmiarów obliczeń, o ile każda pojedyncza baza danych mieści się w jej jednostce DTU.

Chociaż dzielenie na fragmenty bazy danych nie zmniejsza pojemności zasobów agregujących dla rozwiązania, jest wysoce skuteczne w obsłudze bardzo dużych rozwiązań, które są rozłożone na wiele baz danych. Każda baza danych może działać w innym rozmiarze obliczeń do obsługi bardzo dużych, "skutecznych" baz danych o wysokich wymaganiach dotyczących zasobów.

#### <a name="functional-partitioning"></a>Partycjonowanie funkcjonalne

Użytkownicy programu SQL Server często łączą wiele funkcji w pojedynczej bazie danych. Na przykład jeśli aplikacja ma logikę do zarządzania zapasami dla magazynu, ta baza danych może mieć logikę skojarzoną z zapasami, śledzeniem zamówień zakupu, procedurami przechowywanymi i widokami indeksowanymi lub zmaterializowanymi, które zarządzają raportowaniem na koniec miesiąca. Ta technika ułatwia administrowanie bazą danych dla operacji, takich jak kopia zapasowa, ale wymaga również rozmiaru sprzętu do obsługi obciążenia szczytowego we wszystkich funkcjach aplikacji.

Jeśli używasz architektury skalowania w poziomie w usłudze Azure SQL Database, dobrym pomysłem jest podzielenie różnych funkcji aplikacji na różne bazy danych. Przy użyciu tej techniki, każda aplikacja skaluje się niezależnie. Gdy aplikacja staje się bardziej ruchliwa (i zwiększa się obciążenie bazy danych), administrator może wybrać niezależne rozmiary obliczeń dla każdej funkcji w aplikacji. Przy limicie, z tej architektury, aplikacja może być większa niż jedna maszyna towarowa może obsłużyć, ponieważ obciążenie jest rozłożone na wielu komputerach.

### <a name="batch-queries"></a>Kwerendy wsadowe

W przypadku aplikacji, które uzyskują dostęp do danych przy użyciu dużych woluminów, częste, ad hoc kwerendy, znaczna ilość czasu odpowiedzi jest spędzana na komunikacji sieciowej między warstwą aplikacji i warstwy usługi Azure SQL Database. Nawet wtedy, gdy zarówno aplikacji i usługi Azure SQL Database znajdują się w tym samym centrum danych, opóźnienie sieci między nimi może być powiększony przez dużą liczbę operacji dostępu do danych. Aby zmniejszyć liczbę rund sieciowych dla operacji dostępu do danych, należy rozważyć użycie opcji albo partii kwerend ad hoc lub skompilować je jako procedury przechowywane. Jeśli partia kwerend ad hoc, można wysłać wiele zapytań jako jedną dużą partię w jednej podróży do usługi Azure SQL Database. Jeśli skompilować zapytania ad hoc w procedurze składowanej, można osiągnąć taki sam wynik, jak w przypadku ich partii. Za pomocą procedury składowanej również daje korzyści zwiększenie szans na buforowanie planów kwerend w usłudze Azure SQL Database, dzięki czemu można użyć procedury składowanej ponownie.

Niektóre aplikacje są intensywnie zapisu. Czasami można zmniejszyć całkowite obciążenie we/wy bazy danych, biorąc pod uwagę sposób wsadowania zapisuje razem. Często jest to tak proste, jak przy użyciu jawnych transakcji zamiast automatycznego zatwierdzania transakcji w procedurach przechowywanych i partii ad hoc. Aby uzyskać ocenę różnych technik, których można użyć, zobacz [Techniki przetwarzania wsadowego dla aplikacji bazy danych SQL na platformie Azure](sql-database-use-batching-to-improve-performance.md). Poeksperymentuj z własnym obciążeniem, aby znaleźć odpowiedni model do przetwarzania wsadowego. Należy pamiętać, że model może mieć nieco inne gwarancje spójności transakcyjnej. Znalezienie odpowiedniego obciążenia, które minimalizuje wykorzystanie zasobów wymaga znalezienia odpowiedniej kombinacji kompromisów spójności i wydajności.

### <a name="application-tier-caching"></a>Buforowanie w warstwie aplikacji

Niektóre aplikacje bazy danych mają obciążenia odczytu. Buforowanie warstw może zmniejszyć obciążenie bazy danych i może potencjalnie zmniejszyć rozmiar obliczeń wymaganych do obsługi bazy danych przy użyciu usługi Azure SQL Database. Za pomocą [usługi Azure Cache for Redis](https://azure.microsoft.com/services/cache/), jeśli masz obciążenie do odczytu, można odczytać dane raz (lub być może raz na komputerze warstwy aplikacji, w zależności od sposobu jego skonfigurowania), a następnie przechowywać te dane poza bazą danych SQL. Jest to sposób, aby zmniejszyć obciążenie bazy danych (CPU i odczyt we/wy), ale istnieje wpływ na spójność transakcyjną, ponieważ dane odczytywane z pamięci podręcznej może być zsynchronizowany z danymi w bazie danych. Chociaż w wielu aplikacjach pewien poziom niespójności jest dopuszczalne, to nie dotyczy wszystkich obciążeń. Przed wdrożeniem strategii buforowania w warstwie aplikacji należy w pełni zrozumieć wszelkie wymagania aplikacji.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat warstw usług opartych na USŁUDZE DTU, zobacz [model zakupów oparty na jednostka DTU](sql-database-service-tiers-dtu.md).
- Aby uzyskać więcej informacji na temat warstw usług opartych nacorach wirtualnych, zobacz [model zakupów oparty na łanie wirtualnej](sql-database-service-tiers-vcore.md).
- Aby uzyskać więcej informacji na temat pul elastycznych, zobacz [Co to jest pula elastyczna platformy Azure?](sql-database-elastic-pool.md)
- Aby uzyskać informacje na temat wydajności i basenów elastycznych, zobacz [Kiedy wziąć pod uwagę basen elastyczny](sql-database-elastic-pool-guidance.md)
