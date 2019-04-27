---
title: Usługa Azure wskazówki dotyczące dostrajania wydajności usługi SQL Database | Dokumentacja firmy Microsoft
description: Informacje o używaniu zalecenia należy ręcznie dostosować wydajność zapytań usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: a49d30d3058a6cf3ce82d56076f348861ad631ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585138"
---
# <a name="manual-tune-query-performance-in-azure-sql-database"></a>Ręczne dostosowywanie wydajności zapytań w usłudze Azure SQL Database

Po zidentyfikowaniu problemu z wydajnością, występujący w usłudze SQL Database, w tym artykule jest przeznaczona ułatwią Ci:

- Dostrajanie aplikacji i zastosować niektóre najlepsze rozwiązania, które mogą zwiększyć wydajność.
- Dostosowywanie bazy danych, zmieniając indeksów i zapytań, aby bardziej efektywnie pracować z danymi.

W tym artykule założono, że już pracujesz za pomocą usługi Azure SQL Database [bazy danych zalecenia usługi advisor](sql-database-advisor.md) i Azure SQL Database [zaleceń dotyczących dostrajania automatycznego](sql-database-automatic-tuning.md). Przyjęto również założenie, że użytkownik przejrzał [omówienie monitorowania i dostrajania](sql-database-monitor-tune-overview.md) i jej powiązane artykuły dotyczące rozwiązywania problemów z wydajnością. Ponadto w tym artykule założono, że nie masz zasobów procesora CPU, problem z wydajnością związanych z uruchamiania, który może zostać rozpoznana przez zwiększenie rozmiaru obliczeń lub warstwę, aby zapewnić więcej zasobów do bazy danych usługi.

## <a name="tune-your-application"></a>Dostosowywanie aplikacji

W tradycyjnych lokalnych programu SQL Server proces planowania pojemności początkowej są często oddzielone od procesu uruchamiania aplikacji w środowisku produkcyjnym. Najpierw zakupić licencje sprzętu i produktu i dostrajanie wydajności odbywa się później. Gdy używasz usługi Azure SQL Database, jest dobrym rozwiązaniem interweave procesu uruchamiania aplikacji i dostosowywania. Z modelem, płacąc za pojemność na żądanie możesz określić aplikacji na korzystanie z zasobów minimalne wymagane teraz, zamiast konieczności nadmiernej aprowizacji na sprzęcie, w oparciu o liczbę prób planów przyszłego rozwoju aplikacji, które często są nieprawidłowe. Niektórzy klienci mogą nie dostrojenia aplikacji, a zamiast tego wybrać opcję nadmiernej aprowizacji zasobów sprzętowych. Takie podejście może być dobrym rozwiązaniem, jeśli nie chcesz zmienić klucza aplikacji w trakcie okresu zajęty. Ale można Dostrajanie aplikacji można zminimalizować wymagania dotyczące zasobów i niższych miesięczne rachunki przy użyciu warstwy usług w usłudze Azure SQL Database.

### <a name="application-characteristics"></a>Właściwości aplikacji

Mimo że warstwy usługi Azure SQL Database zaprojektowano w celu zwiększenia stabilności wydajności i stabilności aplikacji, najważniejsze wskazówki mogą pomóc w dostrojenia aplikacji w taki sposób, aby lepiej wykorzystać zasoby w rozmiarze obliczeń. Chociaż wiele aplikacji ma znaczący wzrost wydajności, po prostu przełączając do wyższych obliczenia rozmiaru lub warstwę usługi, niektóre aplikacje potrzebują dodatkowego dostrojenia, aby korzystać z wyższego poziomu usługi. Aby zwiększyć wydajność należy wziąć pod uwagę dostrajania dodatkowych aplikacji dla aplikacji, które mają następującą charakterystykę:

- **Aplikacje, które mają niską wydajność ze względu na zachowanie "duża liczba"**

  Duża liczba aplikacji należy operacji dostępu nadmiernej ilości danych, które są wrażliwe na opóźnienie sieci. Może być konieczne zmodyfikowanie tych rodzajów aplikacji, aby zmniejszyć liczbę operacji dostępu do danych w bazie danych SQL. Na przykład może zwiększyć wydajność aplikacji przy użyciu technik, takich jak przetwarzanie wsadowe, zapytania ad hoc lub przenoszenie zapytania do procedur składowanych. Aby uzyskać więcej informacji, zobacz [partii zapytań](#batch-queries).

- **Bazy danych z intensywnie korzystających z obciążeniem, który nie może być obsługiwana przez cały jednej maszynie**

   Bazy danych, które przekraczają zasoby najwyższego Premium obliczeń rozmiaru organizowanych przez skalowanie obciążenia. Aby uzyskać więcej informacji, zobacz [fragmentowania między bazami danych](#cross-database-sharding) i [partycjonowania funkcjonalnego](#functional-partitioning).

- **Aplikacje, które mają optymalne zapytań**

  Aplikacje, zwłaszcza w warstwie dostępu do danych, które zostały nieprawidłowo dostrojone zapytania nie organizowanych przez większy rozmiar obliczeń. Dotyczy to również zapytania brakuje klauzuli WHERE, brakujące indeksy albo mieć przestarzałe statystyk. Te aplikacje korzystają z technik dostrajanie wydajności standardowej kwerendy. Aby uzyskać więcej informacji, zobacz [brakujące indeksy](#identifying-and-adding-missing-indexes) i [zapytania dostrajanie i uzupełnianiem](#query-tuning-and-hinting).

- **Aplikacje, które zawierają dane optymalne dostęp do projektu**

   Aplikacje, które mają nieprzerwaną pracę dostępu współbieżności problemy związane z danych, na przykład zakleszczenia, nie mogą korzystać z większy rozmiar obliczeń. Rozważ zmniejszenie rund w bazie danych SQL Azure buforowania danych po stronie klienta za pomocą usługi pamięć podręczna systemu Azure lub innej technologii buforowania. Zobacz [buforowanie warstwy aplikacji](#application-tier-caching).

## <a name="tune-your-database"></a>Dostosowywanie bazy danych

W tej sekcji przyjrzymy się kilka technik, które można dostrajanie usługi Azure SQL Database, aby uzyskać najlepszą wydajność aplikacji i uruchom go na najmniejszy rozmiar możliwości obliczeniowych. Niektóre z tych metod dopasowania tradycyjnych dostrajania serwera SQL Server najlepsze rozwiązania, ale inne są specyficzne dla usługi Azure SQL Database. W niektórych przypadkach można sprawdzić wykorzystanych zasobów dla bazy danych można znaleźć obszarów, aby dodatkowo dostosować i rozszerzyć tradycyjnych technik programu SQL Server do pracy w usłudze Azure SQL Database.

### <a name="identifying-and-adding-missing-indexes"></a>Identyfikowanie i dodawanie brakujące indeksy

Typowym problemem w wydajność bazy danych OLTP odnosi się do projektu fizycznej bazy danych. Schematy bazy danych są często zaprojektowane i wysłane bez testowania w dużej skali (lub obciążenia ilości danych). Niestety wydajność planu zapytania może być do zaakceptowania na małą skalę, ale znacznie zmniejsza się w obszarze ilości danych na poziomie produkcyjnym. Najbardziej typowe źródła tego problemu jest brak odpowiednich indeksów do zaspokojenia filtry lub innych ograniczeń w zapytaniu. Często brak manifesty indeksów jako tabelę skanowania może wystarczyć wyszukiwanie indeksu.

W tym przykładzie plan wybrane zapytanie używa skanowania, czy wyszukiwanie może wystarczyć:

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

![Planu zapytania z brakujące indeksy](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Usługa Azure SQL Database może pomóc Znajdź i napraw wspólnej brakuje indeksu warunków. Dynamiczne widoki zarządzania, które są wbudowane w usłudze Azure SQL Database przyjrzeć się kompilacji kwerend, w których indeks może znacznie zmniejszyć szacowany koszt, można uruchomić kwerendy. Podczas wykonywania zapytania bazy danych SQL Database śledzi, jak często jest wykonywana każdego planu zapytania, a śledzi szacowany przerwy między wykonywania planu zapytania, a imagined, gdzie istnieje indeksu. Można użyć tych widokach DMV szybko odgadnąć które zmiany w swoim projekcie fizyczna baza danych może zwiększyć całkowity koszt obciążenie bazy danych i jego rzeczywistego obciążenia.

To zapytanie służy do oceny potencjalnych brakujące indeksy:

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

W tym przykładzie zapytanie spowodowało tę sugestię:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

Po jego utworzeniu tej samej instrukcji SELECT wybiera innego planu, który używa seek zamiast skanowania, a następnie wykonuje wydajniej plan:

![Planu zapytania z indeksami poprawiony](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

Insight klucza jest bardziej ograniczone niż maszyny dedykowany serwer wydajność We/Wy systemu udostępnionego, asortymentu. Jest premium minimalizując niepotrzebnych operacji We/Wy z zalet maksymalna system w DTU rozmiar obliczeń każdej warstwy usługi Azure SQL Database. Odpowiedni fizycznego bazy danych projektu opcji może znacznie zwiększyć czas oczekiwania dla poszczególnych zapytań zwiększyć przepływność jednoczesnych żądań obsługi za jednostkę skalowania i zminimalizowania kosztów, niezbędna do zaspokojenia zapytania. Aby uzyskać więcej informacji na temat brakujący indeks dynamicznych widoków zarządzania zobacz [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Dostosowywanie zapytania i wskazówki

Optymalizator zapytań w usłudze Azure SQL Database jest podobny do tradycyjnego Optymalizator zapytań programu SQL Server. Większość najlepsze rozwiązania dotyczące dostosowywania zapytań i zrozumienie przyczyny, dla których model ograniczenia dotyczące Optymalizator zapytań stosuje się także do usługi Azure SQL Database. Jeśli można dostrajanie zapytań w usłudze Azure SQL Database, możesz otrzymać dodatkowe korzyści, zmniejszenie zapotrzebowania agregacji. Aplikacja może być w stanie do uruchomienia przy niskich kosztach niż odpowiednik niezaznaczone aktualności, ponieważ deduplikacja danych może działać na niższym rozmiaru obliczeń.

Przykładem jest często używany w programie SQL Server i który ma zastosowanie również do usługi Azure SQL Database jest jak optymalizator zapytań "sniffs" parametrów. Podczas kompilacji Optymalizator zapytań ocenia bieżącą wartość parametru do określenia, czy może generować bardziej optymalny plan zapytania. Chociaż ta strategia często może prowadzić do planu zapytania, który jest znacznie szybsze niż planu, który został skompilowany bez wartości parametrów znane, obecnie działa imperfectly zarówno w programie SQL Server i usługi Azure SQL Database. Czasami nie jest ten parametr i czasami jest ten parametr, ale wygenerowanego planu jest optymalne pełny zestaw wartości parametrów w obciążenia. Firmy Microsoft zawiera wskazówki zapytania (dyrektywy), możesz określić przeznaczenie bardziej świadomie i zastąpić domyślne zachowanie wykrywanie parametrów. Często można użyć wskazówki, można naprawić przypadkach domyślne zachowanie programu SQL Server lub usługi Azure SQL Database jest niepełnymi obciążenia określonego klienta.

W następnym przykładzie pokazano, jak procesor zapytań może wygenerować planu, który jest optymalne, wydajności i wymagania dotyczące zasobów. Ten przykład pokazuje również, że jeśli używasz wskazówki zapytania, można zmniejszyć wymagania dotyczące czasu i zasobów uruchomienie zapytania bazy danych SQL:

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

Kod instalacji tworzy tabelę, która ma nierówne dystrybucję danych. Optymalny plan kwerend różni się zależnie od których parametr jest wybrany. Niestety plan zachowanie buforowania nie zawsze należy ponownie skompilować zapytania, w oparciu o najbardziej typowych wartości parametru. Dlatego możliwe jest optymalne planu do pamięci podręcznej i używania dla wielu wartości, nawet wtedy, gdy inny plan może być lepszym rozwiązaniem plan średnio. Następnie w planie zapytania tworzy dwie procedury składowane, które są identyczne, z tą różnicą, że jeden ma wskazówki zapytania specjalne.

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

Zaleca się poczekanie co najmniej 10 minut przed rozpoczęciem część 2 przykładu, tak, aby wyniki różnią się w danych wynikowych danych telemetrycznych.

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

Każdej części w tym przykładzie próbuje uruchomić sparametryzowane instrukcję 1000 razy (do generowania obciążenia wystarczające do użycia jako testowego zestawu danych). Podczas wykonywania procedur składowanych, procesor zapytań sprawdza, czy wartość parametru, który jest przekazywany do procedury podczas jej pierwszego kompilacji ("wykrywanie parametrów"). Procesor buforuje Wynikowy plan i używa go na potrzeby wywołań nowsze nawet, jeśli wartość tego parametru jest inna. Optymalny plan nie mogą być używane we wszystkich przypadkach. Czasami konieczne jest przeprowadzenie Optymalizator wybierz plan, który jest lepszym rozwiązaniem dla średniej przypadek, a nie konkretnego przypadku od kiedy zapytanie najpierw został skompilowany. W tym przykładzie wstępny plan generuje planu "Skanuj", która odczytuje wszystkie wiersze, aby znaleźć każdej wartości, która odpowiada parametrowi:

![Zapytanie, dostrajanie przy użyciu planu skanowania](./media/sql-database-performance-guidance/query_tuning_1.png)

Ponieważ procedury możemy wykonać przy użyciu wartości 1, Wynikowy plan został optymalne dla wartości 1, ale została optymalne dla wszystkich wartości w tabeli. Prawdopodobnie wynik nie jest, co powinno, gdyby można wybrać poszczególnych plan losowo, ponieważ plan wykonuje wolniej i wykorzystuje więcej zasobów.

Po uruchomieniu testów przy użyciu `SET STATISTICS IO` równa `ON`, zadania logiczne skanowania, w tym przykładzie są wykonywane w tle. Aby zobaczyć, że nie istnieją 1,148 odczyty wykonywane przez plan (jest to mało wydajne, jeśli średnia wielkość liter jest zwracać tylko jeden wiersz):

![Zapytanie, dostrajanie przy użyciu logicznych skanowania](./media/sql-database-performance-guidance/query_tuning_2.png)

Druga część przykładu używa wskazówki zapytania mówić Optymalizator użyć określonej wartości podczas procesu kompilacji. W tym przypadku wymusza procesor zapytań, aby zignorować wartość, która jest przekazywana jako parametr, a zamiast tego z nich przejmować `UNKNOWN`. Dotyczy to wartość, która ma średnia częstotliwość w tabeli (bez uwzględnienia niedokładność). Wynikowy planowane jest na podstawie wyszukiwania planu, szybciej i zużywa mniej zasobów, średnio niż plan w części 1 tego przykładu:

![Zapytanie dostrajania za pomocą wskazówki zapytania](./media/sql-database-performance-guidance/query_tuning_3.png)

Możesz zobaczyć efekt **sys.resource_stats** tabeli (występuje opóźnienie od czasu wykonywania testu i kiedy dane wypełnienie tabeli). Dla tego przykładu, część 1, wykonywane przedziale czasu 22:25:00, a część 2 wykonywane 22:35:00. Wcześniej przedział czasu jest używany więcej zasobów w danym przedziale czasu, niż (ze względu na ulepszenia wydajności planu) nowszym.

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Przykładowe wyniki dostosowywania zapytań](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Chociaż wolumin w tym przykładzie jest celowo mały, efekt optymalne parametry mogą być znaczącego, szczególnie w przypadku większych baz danych. W skrajnych przypadkach różnicę może mieć wartość od sekund w przypadku szybkiego przypadków i godziny wolne przypadków.

Można sprawdzić **sys.resource_stats** ustalenie, czy zasób w celu przetestowania używa więcej lub mniej zasobów niż innego testu. Podczas porównywania danych oddzielić chronometraż testy, aby nie były w tym samym oknie 5-minutowych w **sys.resource_stats** widoku. Celem wykonywania to zminimalizować łączną ilość używanych zasobów, a nie zminimalizować zasobów szczytowego. Ogólnie rzecz biorąc Optymalizowanie fragment kodu opóźnienia zmniejsza zużycie zasobów. Upewnij się, że niezbędne są zmiany wprowadzone do aplikacji i że zmiany nie mieć negatywny wpływ na wrażenia dla kogoś, kto może używać wskazówki zapytania w aplikacji.

Jeśli obciążenie ma zbiór powtarzania zapytań, często dobrym pomysłem do przechwytywania i zweryfikować optymalizacji z wybranych planu, ponieważ jej dyski jednostki rozmiar minimalny zasobów wymaganych do hostowania bazy danych. Po sprawdzeniu go od czasu do czasu reexamine plany daje pewność, że nie ma ograniczone. Możesz dowiedzieć się więcej [zapytania wskazówki (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Dzielenie na fragmenty między bazami danych

Ponieważ usługi Azure SQL Database działa na sprzęcie masowym, limity pojemności poszczególnych baz danych są niższe niż w przypadku tradycyjnych lokalnych instalacji programu SQL Server. Niektórzy klienci Użyj dzielenia na fragmenty techniki, aby rozłożyć operacji bazy danych między wieloma bazami danych podczas operacji nie mieści się w granicach poszczególne bazy danych w usłudze Azure SQL Database. Większość klienci, którzy używają techniki dzielenia na fragmenty w usłudze Azure SQL Database Podziel swoje dane w jednym wymiarze, w wielu bazach danych. W tym podejściu, musisz zrozumieć, że aplikacje OLTP często wykonywać transakcje, które dotyczą tylko jeden wiersz lub niewielkiej liczby wierszy w schemacie.

> [!NOTE]
> SQL Database oferuje teraz biblioteki uzyskanymi dzielenia na fragmenty. Aby uzyskać więcej informacji, zobacz [Przegląd biblioteki klienckiej Elastic Database](sql-database-elastic-database-client-library.md).

Na przykład jeśli baza danych ma nazwę klienta, kolejność i szczegóły zamówienia (np. przykład tradycyjne bazy danych Northwind dostarczany z programem SQL Server), można rozdzielić te dane do wielu baz danych za pomocą grupowania klientów dotyczące odpowiednich zamówień i szczegóły zamówienia informacje. Aby zagwarantować, że odbiorcy danych pozostaje w poszczególnych baz danych. Aplikacja będzie podzielić różnych klientów w bazach danych, efektywnie rozłożenie obciążenia na wiele baz danych. Dzielenie na fragmenty klienci nie tylko można uniknąć limitu rozmiaru maksymalnego bazy danych, ale również usługi Azure SQL Database może przetwarzać obciążenia, które są znacznie większe niż limity różnych rozmiarów wystąpień obliczeniowych, tak długo, jak każdej pojedynczej bazy danych jest dopasowywana do jego jednostek DTU.

Fragmentowanie bazy danych nie zmniejsza pojemność zagregowanych danych zasobu dla rozwiązania, ale jest bardzo skuteczne w obsłudze bardzo dużych rozwiązań, które są dystrybuowane między wieloma bazami danych. Każda baza danych można uruchomić w rozmiarze obliczeniowej do obsługi bardzo dużych "od" bazy danych o wysokich wymagań dotyczących zasobów.

### <a name="functional-partitioning"></a>Partycjonowanie funkcjonalne

Użytkownikom programu SQL Server jest często łączyć wiele funkcji w poszczególnych baz danych. Na przykład jeśli aplikacja logiki można zarządzać zapasami magazynu, bazy danych może być logiki skojarzonej z spisu i śledzenie zamówień zakupu, procedury składowane i indeksowane lub zmaterializowanych widoków, które Zarządzanie raportowaniem koniec miesiąca. Ta technika sprawia, że łatwiej zarządzać bazą danych dla operacji takich jak backup, ale również wymaga rozmiaru sprzętu do obsługi szczytowego obciążenia na wszystkie funkcje aplikacji.

Jeśli używasz skalowalność architektury usługi Azure SQL Database, jest dobry pomysł, aby podzielić różne funkcje aplikacji w różnych bazach danych. Korzystając z tej techniki, każda aplikacja jest skalowana niezależnie. Jak aplikacja staje się zajęty i zwiększa obciążenie bazy danych, administrator może wybrać rozmiarów obliczeniowych niezależnie od dla poszczególnych funkcji w aplikacji. Osiągnęło limit, za pomocą tej architektury aplikacji może być większy niż może obsłużyć maszyny pojedynczego towaru, ponieważ obciążenie jest rozłożona się między wieloma maszynami.

### <a name="batch-queries"></a>Wsadowe

W przypadku aplikacji uzyskujących dostęp do danych za pomocą dużej liczby częste wykonywanie zapytań ad hoc, znaczną ilość czasu odpowiedzi odbywa się na komunikację sieciową między warstwami aplikacji i usługi Azure SQL Database. Nawet wtedy, gdy aplikacji i bazy danych SQL Azure znajdują się w tym samym centrum danych, opóźnienie sieci między tymi dwoma może być powiększany przez dużą ilością danych, operacji dostępu. Aby zmniejszyć sieci podczas podróży dla operacji dostępu do danych, należy wziąć pod uwagę przy użyciu opcji albo partii zapytań ad hoc lub do kompilowania ich jako procedury składowane. Jeśli partii jest zapytań ad hoc, możesz wysłać wiele zapytań jako jedna partia duże w jednym podróży do usługi Azure SQL Database. W przypadku kompilacji zapytań ad hoc w procedurze składowanej można osiągnąć ten sam wynik, tak, jakby ich partii. Za pomocą procedury składowanej zapewnia również korzyści zwiększa prawdopodobieństwo buforowania planom zapytań w usłudze Azure SQL Database, aby ponownie użyć procedury składowanej.

Niektóre aplikacje są intensywnie korzystających z zapisu. Czasami może zmniejszyć całkowitego obciążenia We/Wy na bazę danych, biorąc pod uwagę sposób przetwarzania wsadowego zapisów. Często jest to tak proste, jak za pomocą jawnego transakcji zamiast automatycznego zatwierdzania transakcji w procedurach przechowywanych i partie ad-hoc. Aby uzyskać ocenę różnych technik, można użyć, zobacz [dzielenia na partie technik dla aplikacji bazy danych SQL na platformie Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Poeksperymentuj z obciążenia można znaleźć odpowiednie modelu dla przetwarzania wsadowego. Pamiętaj dowiedzieć się, że model może mieć gwarancje spójności transakcyjnej nieco inne. Znajdowanie odpowiednie obciążenie, które minimalizuje wykorzystanie zasobów wymaga znajdowania odpowiedniej kombinacji wad i zalet wydajnością a spójnością.

### <a name="application-tier-caching"></a>Pamięć podręczna warstwy aplikacji

Niektóre aplikacje bazy danych mają odczycie obciążeń. Buforowanie warstw może zmniejszyć obciążenie bazy danych i potencjalnie może zmniejszyć rozmiar obliczeń wymagana do obsługi bazy danych przy użyciu usługi Azure SQL Database. Za pomocą [pamięci podręcznej Redis Azure](https://azure.microsoft.com/services/cache/), w przypadku obciążenia odczycie możesz odczytywać dane raz (lub raz na maszynę z warstwą aplikacji, w zależności od sposobu skonfigurowania), a następnie zapisywania tych danych poza bazy danych SQL. Jest to sposób, aby zmniejszyć obciążenie bazy danych (procesor CPU i odczytu We/Wy), ale ma wpływ na poziom spójności transakcyjnej, ponieważ danych odczytywanych z pamięci podręcznej może być zsynchronizowane z danymi w bazie danych. Chociaż w wielu aplikacjach dopuszczalne jest pewien stopień niespójności, to nie dotyczy wszystkich obciążeń. Wszelkie wymagania aplikacji należy zapoznać się przed wdrożeniem strategii buforowania warstwy aplikacji.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat warstwy usług oparte na jednostkach DTU, zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md).
- Aby uzyskać więcej informacji na temat warstw usług opartych na rdzeniach wirtualnych, zobacz [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).
- Aby uzyskać więcej informacji na temat pul elastycznych, zobacz [co to jest pula elastyczna Azure?](sql-database-elastic-pool.md)
- Aby uzyskać informacji o wydajności i elastycznych pulach, zobacz [kiedy należy rozważyć korzystanie z puli elastycznej](sql-database-elastic-pool-guidance.md)