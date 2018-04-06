---
title: Dostosowywanie wskazówki dotyczące wydajności bazy danych SQL Azure | Dokumentacja firmy Microsoft
description: Informacje o używaniu zalecenia, aby zwiększyć wydajność przeszukiwania bazy danych SQL Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: 89575f94e95c5ae378d95220d63c162e53158069
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="tuning-performance-in-azure-sql-database"></a>Dostrajanie wydajności w bazie danych SQL Azure

Baza danych SQL Azure udostępnia [zalecenia](sql-database-advisor.md) czy można użyć do zwiększenia wydajności bazy danych, lub możesz pozwolić, aby baza danych SQL Azure [automatycznie dostosowania do aplikacji](sql-database-automatic-tuning.md) i zastosować zmiany, które poprawią wydajność obciążenia.

W przypadku nie ma żadnych odpowiednich zaleceń i nadal masz problemy z wydajnością, usprawniających wydajność może skorzystać z następujących metod:
1. Zwiększ [warstw usług](sql-database-service-tiers.md) i podaj więcej zasobów w bazie danych.
2. Dostrajanie aplikacji i stosować najlepsze rozwiązania, które może poprawić wydajność. 
3. Dostrajanie bazy danych, zmieniając indeksów i zapytań na bardziej efektywną pracę z danymi.

Są to ręcznej metody, ponieważ potrzebne do podejmowania decyzji [warstw usług](sql-database-service-tiers.md) należy wybrać lub trzeba ponownie pisać kodu aplikacji lub bazy danych i wdrażanie zmiany.

## <a name="increasing-performance-tier-of-your-database"></a>Zwiększenie poziomu wydajności bazy danych

Baza danych SQL Azure oferuje cztery [warstw usług](sql-database-service-tiers.md) wybraną z: podstawowa, standardowa i Premium (mierzona jest wydajność w jednostek przepływności bazy danych, lub [Dtu](sql-database-what-is-a-dtu.md). Każdej warstwy usług izoluje ściśle zasobów można używać bazy danych SQL i zapewnia przewidywalną wydajność tego poziomu usług. W tym artykule firma Microsoft oferuje wskazówki, które ułatwiają wybieranie warstwy usługi dla aplikacji. Omówiono także sposoby dostroić aplikacji maksymalne z bazy danych SQL Azure.

> [!NOTE]
> Ten artykuł skupia się na wytyczne dotyczące wydajności dla pojedynczej bazy danych w bazie danych SQL Azure. Aby uzyskać wskazówki dotyczące wydajności związane z pule elastyczne, zobacz [zagadnienia dotyczące cen i wydajności dla pul elastycznych](sql-database-elastic-pool-guidance.md). Należy pamiętać, jednak wiele dostrajania zaleceń w tym artykule dotyczą baz danych w puli elastycznej i uzyskiwanie podobnego zwiększenia wydajności.
> 

* **Podstawowe**: podstawowe usługi warstwy oferty dobrą wydajność przewidywalności dla każdej bazy danych, godzinę w ciągu godziny. W bazie danych podstawowych wystarczające zasoby obsługuje dobrą wydajność w małych baz danych, która nie ma wiele równoczesnych żądań. Typowe zastosowania, jeśli używasz warstwy podstawowej usługi są:
  * **Po prostu rozpoczniesz pracę z bazą danych SQL Azure**. Aplikacje, które są często rozwijany nie ma potrzeby poziomy wysokiej wydajności. Podstawowe bazy danych są idealne środowiska do tworzenia bazy danych i testowania, w momencie cen niski.
  * **Masz bazę danych z jednego użytkownika**. Aplikacje, które zwykle kojarzenie jednego użytkownika z bazy danych nie mają wysokie wymagania współbieżności i wydajności. Te aplikacje nadają się do warstwy usług podstawowa.
* **Standardowe**: standardowy warstwy usług oferuje lepszą wydajność przewidywalności i zapewnia dobrą wydajność dla baz danych, które mają wiele współbieżnych żądań, takich jak aplikacje sieci web i grupy roboczej. Po wybraniu warstwy standardowa usługa bazy danych można rozmiar bazy danych aplikacji w oparciu przewidywalną wydajność, minute w ciągu minuty.
  * **Baza danych zawiera wiele równoczesnych żądań**. Aplikacje, które usługi więcej niż jeden użytkownik naraz zwykle muszą wyższego poziomu wydajności. Na przykład aplikacje grupy roboczej lub sieci web, które muszą niski średnia we/wy ruchu wymagania dotyczące obsługi wielu równoczesnych zapytań nadaje się do warstwy standardowych usług.
* **Premium**: warstwę Premium oferuje przewidywalną wydajność, sekundy na sekundę dla każdego Premium lub biznesowe krytyczne (wersja zapoznawcza) bazy danych. Po wybraniu warstwy usług Premium można rozmiar bazy danych aplikacji w oparciu obciążenia szczytowego dla tej bazy danych. Plan usuwa przypadków w wydajności, które wariancję może spowodować małych zapytań do trwać dłużej, niż oczekiwano w operacjach wrażliwy na opóźnienia. Ten model może bardzo uprościć cykli weryfikacji rozwoju i produktu dla aplikacji, które należy podjąć, silne oświadczenia dotyczące szczytowego zapotrzebowania na zasoby, odchylenie wydajności lub opóźnienia zapytania. Większość przypadków użycia warstwy Premium usługi ma co najmniej jeden z tych właściwości:
  * **Obciążenia szczytowego wysokiej**. Aplikacja, która wymaga znacznej procesora CPU, pamięć lub wejścia/wyjścia (IO) do ukończenia operacji wymaga poziomu dedykowanego, wysokiej wydajności. Na przykład znane użycie kilku rdzeni Procesora przez dłuższy czas operacji bazy danych jest kandydatem do warstwy Premium usługi.
  * **Wiele równoczesnych żądań**. Niektóre aplikacje bazy danych usługi dużo współbieżnych żądań, na przykład gdy obsługująca witryny sieci Web, która ma duże natężenie ruchu. Podstawowa i standardowa warstwy usług Ogranicz liczbę równoczesnych żądań dla jednej bazy danych. Aplikacje, które wymagają więcej połączeń należy wybrać rozmiar rezerwacji odpowiednie do obsługi maksymalną liczbę żądań potrzebne.
  * **Małe opóźnienia**. Niektóre aplikacje wymagają do zagwarantowania minimalnego czasu odpowiedzi z bazy danych. Jeśli określone procedury składowanej jest wywoływana w ramach szerszych operacji klienta, może być wymagane są zwracane z tego wywołania w milisekundach nie więcej niż 20 99 procent czasu. Aplikacje tego typu korzysta z warstwę Premium, aby upewnić się, że wymagane mocy obliczeniowej jest dostępny.

Poziom usług, który należy do bazy danych SQL zależy od wymagań obciążenia szczytowego dla każdego wymiaru zasobów. Niektóre aplikacje używane trivial ilość pojedynczego zasobu, ale mieć znaczący wymagania dotyczące innych zasobów.

### <a name="service-tier-capabilities-and-limits"></a>Możliwości warstwy usług i limity

W poszczególnych warstwach usług ustawić poziom wydajności, więc możesz swobodnie płacisz tylko za pojemność, które są potrzebne. Możesz [Dostosuj wydajność](sql-database-service-tiers.md), w górę lub w dół, jak zmiany obciążenia. Na przykład jeśli obciążenie bazy danych jest wysoka w sezonie zakupów wstecz do służbowych, może zwiększyć poziom wydajności bazy danych na określony czas, lipca za pośrednictwem września. Można zmniejszyć ją po zakończeniu Twojej sezonu godzinami szczytu. Można zminimalizować płacisz za Optymalizowanie środowiska chmury do sezonowości firmy. Ten model jest również odpowiedni dla oprogramowania produktu cykle. Zespół może przydzielić pojemności, podczas jej uruchomień testów, a następnie zwolnij wydajność po zakończeniu testowania. W modelu żądania pojemności płacisz za pojemność potrzebny i uniknąć wydatków na dedykowany zasobów, które może być rzadko używane.

### <a name="why-service-tiers"></a>Dlaczego warstw do usług?
Mimo że poszczególnych obciążeń bazy danych może się różnić, celem warstwy usług jest zapewnienie przewidywalność wydajności na różnych poziomach wydajności. Klientów z bazy danych na dużą skalę wymagań dotyczących zasobów można pracować w bardziej dedykowanego środowiska komputerowego.

## <a name="tune-your-application"></a>Dostrajanie aplikacji
W tradycyjnych, lokalnie programu SQL Server proces planowania pojemności początkowej jest często oddzielone od proces uruchomienia aplikacji w środowisku produkcyjnym. Najpierw zakupionych licencji sprzętu i produktu i dostrajania wydajności odbywają się później. Gdy używasz bazy danych SQL Azure jest dobrym pomysłem jest interweave proces uruchamiania aplikacji i dostrajania go. W modelu płatniczych pojemności na żądanie można dostosować w aplikacji umożliwiająca użycie minimalne zasoby potrzebne teraz, zamiast nadmiarowe Inicjowanie obsługi administracyjnej na sprzęcie oparte na prób planów przyszłego rozwoju aplikacji, które często są nieprawidłowe. Niektórzy klienci mogą wybierz nie dostroić aplikacji, a zamiast tego wybrać overprovision zasobów sprzętowych. Takie podejście może być dobrym rozwiązaniem, jeśli nie chcesz zmienić klucza aplikacji okresie zajęty. Jednak Dostrajanie aplikacji można zminimalizować wymagania dotyczące zasobów i niższe opłaty miesięczne użycie warstw usług w bazie danych SQL Azure.

### <a name="application-characteristics"></a>Właściwości aplikacji
Mimo że warstwach usług bazy danych SQL Azure mają na celu poprawę wydajności stabilność i przewidywalności dla aplikacji, najlepsze rozwiązania mogą pomóc dostosować aplikację, aby lepiej wykorzystać zasoby na poziomie wydajności. Mimo że wiele aplikacji ma znaczący wzrost wydajności, po prostu przełączając na wyższy poziom wydajności lub warstwy usługi, niektóre aplikacje potrzebują dodatkowe dostrojenia, aby korzystać z wyższego poziomu usługi. Aby zwiększyć wydajność należy wziąć pod uwagę dostrajania dodatkowych aplikacji dla aplikacji, które mają następujące cechy:

* **Aplikacje, które mają niską wydajność ze względu na zachowanie "chatty"**. Chatty aplikacji należy operacji dostępu nadmiernej ilości danych, które są wrażliwy na opóźnienia sieci. Może być konieczne zmodyfikowanie tego rodzaju aplikacji, aby zmniejszyć liczbę operacji uzyskiwania dostępu do danych w bazie danych SQL. Na przykład może zwiększyć wydajność aplikacji przy użyciu technik, takich jak przetwarzanie wsadowe zapytań ad hoc lub przenoszenie zapytania na procedury składowane. Aby uzyskać więcej informacji, zobacz [partii zapytań](#batch-queries).
* **Bazy danych o znacznym obciążeniem, które nie może być obsługiwana przez cały jednej maszyny**. Bazy danych, które przekraczają zasobów najwyższego poziomu wydajności Premium mogą korzystać z skalowania obciążenia. Aby uzyskać więcej informacji, zobacz [dzielenia na fragmenty między bazami danych](#cross-database-sharding) i [funkcjonalności partycjonowania](#functional-partitioning).
* **Aplikacje, które mają zapytania nieoptymalne**. Aplikacje, zwłaszcza w warstwie dostępu do danych, które zostały nieprawidłowo dopasowane zapytania rozwiązanie nie mogą korzystać z wyższego poziomu wydajności. W tym zapytań, które nie mają w klauzuli WHERE, brakuje indeksów lub mieć przestarzałe statystyki. Te aplikacje korzystają z techniki dostrajania wydajności standardowej kwerendy. Aby uzyskać więcej informacji, zobacz [brakujące indeksy](#identifying-and-adding-missing-indexes) i [zapytania dostrajanie i zalecanych](#query-tuning-and-hinting).
* **Projekt dostęp do aplikacji, które znajdują się dane nieoptymalne**. Aplikacje, które mają związanego z używaniem danych współbieżności problemy z dostępem, na przykład zakleszczenia, nie mogą korzystać z wyższego poziomu wydajności. Rozważ zmniejszenie rund w bazie danych SQL Azure przez buforowania danych po stronie klienta z usługą Azure buforowanie lub innej technologii buforowania. Zobacz [buforowanie warstwy aplikacji](#application-tier-caching).

## <a name="tune-your-database"></a>Dostrajanie bazy danych
W tej sekcji opisano niektóre techniki, które można użyć do dostrajania bazy danych SQL Azure, aby uzyskać najlepszą wydajność aplikacji i uruchom go na najniższym poziomie wydajność. Niektóre z tych metod zgodne tradycyjnych dostrajania serwera SQL Server najlepsze rozwiązania, ale inne są specyficzne dla bazy danych SQL Azure. W niektórych przypadkach należy zbadać wykorzystanych zasobów dla bazy danych można znaleźć obszarów dalsze dostrojenie i rozszerzenia tradycyjnych metod programu SQL Server do pracy w bazie danych SQL Azure.

### <a name="identify-performance-issues-using-azure-portal"></a>Identyfikowanie problemów z wydajnością przy użyciu portalu Azure
Następujące narzędzia w portalu Azure można ułatwić analizowanie i rozwiązywanie problemów z wydajnością z bazy danych SQL:

* [Szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md)
* [SQL Database Advisor](sql-database-advisor.md)

Azure portal ma więcej informacji na temat obu tych narzędzi i sposobu ich używania. Aby wydajnie zdiagnozować i rozwiązać problemy, firma Microsoft zaleca, najpierw spróbuj użyć narzędzi dostępnych w portalu Azure. Firma Microsoft zaleca użycie ręcznego dostrajanie metod, które następnie omówimy brakujących indeksy i dostrajania zapytania, w szczególnych przypadkach.

Więcej informacji o identyfikowanie problemów w usłudze Azure SQL Database na [monitorowania wydajności](sql-database-single-database-monitor.md) artykułu.

### <a name="identifying-and-adding-missing-indexes"></a>Identyfikowanie i dodać brakujące indeksy
To powszechny problem, wydajność bazy danych OLTP odnosi się do projektowania fizycznej bazy danych. Często schematów bazy danych są zaprojektowane i wysłane bez testowania na dużą skalę (albo w obciążenia w woluminie danych). Niestety wydajność planu zapytania może być akceptowane na małą skalę, ale znacznie zmniejsza się w obszarze woluminów danych na poziomie produkcji. Najbardziej typowe źródło tego problemu jest brak odpowiednich indeksów przez filtry lub inne ograniczenia w zapytaniu. Często brakujące indeksy manifestów jako tabelę skanowania może wystarczyć wyszukiwanie indeksu.

W tym przykładzie planu wybrane zapytanie używa skanowania podczas wyszukiwania będzie wystarczająca:

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

![Planu zapytania z brakujące indeksy](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Baza danych SQL Azure może pomóc Znajdź i napraw wspólnej brakujących indeksu warunków. Widoków DMV, które są wbudowane w bazie danych SQL Azure przyjrzeć się kompilacje zapytania, w których indeks może znacznie zmniejszyć szacowany koszt, aby uruchomić kwerendę. Podczas wykonywania zapytania bazy danych SQL śledzenia, jak często jest wykonywana każdego planu zapytania, a śledzi szacowany odstęp między planu wykonywania kwerend i jeden imagined gdy istniał tego indeksu. Użyj tych widoków DMV szybko odgadnąć które zmiany do projektu bazy danych fizycznych może zwiększyć całkowity koszt obciążenie bazy danych i jego rzeczywistego obciążenia.

Skorzystaj z tej kwerendy, aby ocenić potencjalne brakujące indeksy:

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

W tym przykładzie zapytanie spowodowało sugestia ta:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Po jego utworzeniu tej samej instrukcji SELECT wybiera innego planu, który używa wyszukiwania zamiast skanowania, a następnie wykonuje wydajniej plan:

![Planu zapytania z indeksami poprawiony](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

Wiedzę klucza jest bardziej ograniczony niż maszyny dedykowanego serwera pojemności We/Wy systemu udostępnionego, zwykłych. Na minimalizując niepotrzebnych we/wy przeprowadzać maksymalną systemu w DTU poziomu wydajności poszczególnych warstwach usług bazy danych SQL Azure jest premium. Odpowiednie fizyczna baza danych projektu opcji może znacznie zwiększyć czas oczekiwania dla poszczególnych zapytań, poprawienia przepływności równoczesnych żądań obsługi na jednostkę skalowania i zminimalizować koszty, wymaganych do spełnienia zapytania. Aby uzyskać więcej informacji o indeksie Brak widoków DMV, zobacz [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Dostrajanie zapytania i zalecanych
Optymalizator zapytań w bazie danych SQL Azure jest podobny do tradycyjnych Optymalizator zapytań programu SQL Server. Większość najlepsze rozwiązania dotyczące dostrajania zapytania i zrozumienie uzasadnienie modelu ograniczenia dotyczące Optymalizator zapytań mają zastosowanie również do bazy danych SQL Azure. Jeśli dostroić kwerendy w bazie danych SQL Azure, można otrzymać dodatkowe korzyści, zmniejszenie zapotrzebowania na zasoby agregacji. Aplikację można uruchomić niższe koszty niż niewyregulowanym równoważną, ponieważ można uruchomić na niższym poziomie wydajności.

Na przykład typowe w programie SQL Server i które mają zastosowanie również do bazy danych SQL Azure jak optymalizator zapytań "sniffs" parametrów. Podczas kompilacji Optymalizator zapytań oblicza bieżącą wartość parametru, aby określić, czy można generować bardziej optymalne planu zapytania. Mimo że ta strategia często może prowadzić do planu zapytania, który jest znacznie szybsze niż planu, który został skompilowany bez wartości parametrów znane, obecnie działa imperfectly zarówno w programie SQL Server i w bazie danych SQL Azure. Czasami parametr jest nie ten sposób i czasami ten parametr w sposób, ale wygenerowanego planu jest nieoptymalne pełny zestaw wartości parametrów obciążenia pracą. Firma Microsoft udostępnia wskazówki zapytania (dyrektywy), dzięki czemu można określić więcej celowo zamiar i zastąpić domyślne zachowanie wykrywanie parametrów. Często Jeśli używasz wskazówek, można naprawić przypadkach domyślne zachowanie serwera SQL lub bazy danych SQL Azure jest niedoskonała dla obciążenia określonego klienta.

W następnym przykładzie pokazano, jak procesor zapytań może wygenerować planu, który jest nieoptymalne, wydajności i wymagań dotyczących zasobów. W tym przykładzie przedstawiono również, że jeśli używasz wskazówki zapytania, można zmniejszyć wymagania dotyczące czasu i zasobów uruchomienie zapytania bazy danych SQL:

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

Kod instalacji tworzy tabelę, która ma niesymetryczna danych dystrybucji. Plan zapytania optymalne różni się pod względem którego parametr jest wybrany. Niestety ten plan zachowanie buforowania nie zawsze Skompiluj ponownie kwerendy na podstawie wartości typowych parametrów. Tak prawdopodobnie nieoptymalne planu w pamięci podręcznej i używany dla wielu wartości, nawet wtedy, gdy inny plan może być lepszym rozwiązaniem planu średnia. Następnie planu zapytania tworzy dwie procedury składowanej, które są identyczne, z wyjątkiem tego, że ma jedną wskazówkę zapytania specjalnych.

**Przykład, część 1**

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

**Przykład część 2**

(Zaleca się poczekanie co najmniej 10 minut przed rozpoczęciem część 2 przykładzie tak, że wyniki są unikatowe w danych telemetrycznych.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

W tym przykładzie każda część podejmie próbę uruchomienia sparametryzowane instrukcję 1000 razy (Aby wygenerować obciążenie wystarczające do użycia jako testowego zestawu danych). Podczas wykonywania procedur składowanych, procesor zapytań sprawdza, czy wartość parametru przekazywana do procedury podczas swojej pierwszej kompilacji ("wykrywanie parametrów"). Procesor buforuje Wynikowy plan i używa go do nowszej wywołań, nawet wtedy, gdy wartość parametru jest inny. Optymalne planu nie mogą być używane we wszystkich przypadkach. Czasami trzeba przewodnik Optymalizator wybierz plan, który jest lepszym rozwiązaniem dla średnia wielkość liter, a nie konkretnego przypadku od kiedy najpierw skompilowanego zapytania. W tym przykładzie wstępny plan generuje planu "skanowanie", który odczytuje wszystkie wiersze, aby znaleźć wartości, który odpowiada parametrowi:

![Zapytanie dostrajanie przy użyciu planu skanowania](./media/sql-database-performance-guidance/query_tuning_1.png)

Ponieważ możemy wykonać procedurę przy użyciu wartości 1, Wynikowy plan został optymalne dla wartości 1, ale został nieoptymalne dla wszystkich wartości w tabeli. Prawdopodobnie wynik nie, co powinno, gdyby losowo, wybierz każdego planu, ponieważ plan wykonuje wolniej i wykorzystuje więcej zasobów.

Po uruchomieniu testu z `SET STATISTICS IO` ustawioną `ON`, pracy logicznej skanowania w tym przykładzie jest wykonywane w tle. Aby sprawdzić, czy 1,148 odczyty programach planu (która jest nieefektywne, jeśli średnia wielkość liter ma zwrócić tylko jeden wiersz):

![Zapytanie dostrajanie przy użyciu logicznej skanowania](./media/sql-database-performance-guidance/query_tuning_2.png)

Druga część przykładzie używa wskazówki zapytania mówić Optymalizator można użyć określonej wartości podczas procesu kompilacji. W takim przypadku wymusi procesor zapytań, aby zignorować wartość, która została przekazana jako parametr, a zamiast tego do wniosku `UNKNOWN`. Dotyczy to wartość, która ma średnia częstotliwość w tabeli (Ignorowanie zegara). Wynikowy plan jest na podstawie wyszukiwania plan, który jest szybsze i wykorzystuje mniej zasobów, średnio niż planu w część 1 w tym przykładzie:

![Dostrajanie zapytania za pomocą wskazówki zapytania](./media/sql-database-performance-guidance/query_tuning_3.png)

Można zobaczyć efekt **sys.resource_stats** tabeli (jest wyświetlony z pewnym opóźnieniem od czasu wykonywania testu i kiedy dane wypełnienie tabeli). Dla tego przykładu, część 1 wykonywanego w przedział czasu 22:25:00 i część 2 wykonywane 22:35:00. Przedział czasu wcześniej używane więcej zasobów w danym przedziale czasu niż nowszej (ze względu na ulepszenia wydajności plan).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Dostrajanie przykład wyniki zapytania](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Chociaż wolumin w tym przykładzie jest celowo mały, efekt nieoptymalne parametry mogą być istotne, szczególnie w przypadku większych baz danych. Różnica, w ekstremalnych przypadkach mogą należeć do zakresu od sekund w przypadku szybkiego i godziny wolne przypadków.
> 
> 

Można sprawdzić **sys.resource_stats** ustalenie, czy zasób dla testu używa więcej lub mniej zasobów niż innego testu. Podczas porównywania danych oddzielenia czasu testów tak, aby nie były w tym samym oknie 5-minutowy w **sys.resource_stats** widoku. Celem wykonywania jest zminimalizować łączną ilość zasobów używanych i nie, aby zminimalizować zasobów godzinami szczytu. Ogólnie rzecz biorąc Optymalizacja fragment kodu dla opóźnienia zmniejsza zużycie zasobów. Upewnij się, że niezbędne są zmiany wprowadzone do aplikacji i że zmiany nie mieć negatywny wpływ na wrażenia osobie, która może stosować wskazówki zapytania w aplikacji.

Jeśli obciążenie zawiera zestaw powtarzania zapytań, często dobrym rozwiązaniem jest przechwycenie i zweryfikować optymalizacji z wybranych planu, ponieważ jego przebieg jednostki rozmiar minimalny zasobu wymaganego do obsługi bazy danych. Po utworzeniu zweryfikować, czasami reexamine planuje daje pewność, że nie ma obniżoną. Użytkownik może dowiedzieć się więcej o [zapytania wskazówki (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Dzielenia na fragmenty między bazami danych
Ponieważ baza danych SQL Azure jest uruchamiana na standardowym sprzęcie, limity pojemności dla pojedynczej bazy danych są niższe niż w przypadku tradycyjnych, lokalnie instalacji programu SQL Server. Niektórzy klienci użyciu technik dzielenia na fragmenty rozprzestrzeniać się operacje bazy danych wielu baz danych podczas operacji nie mieści się w granicach pojedynczej bazy danych w bazie danych SQL Azure. Większość klientów korzystających z techniki dzielenia na fragmenty w bazie danych SQL Azure podzielone swoje dane na jednym wymiarze między wieloma bazami danych. Takie podejście należy zrozumieć, że aplikacje OLTP często wykonywać transakcje, które dotyczą tylko jeden wiersz lub dla niewielkiej liczby wierszy w schemacie.

> [!NOTE]
> Baza danych SQL zawiera teraz bibliotekę z dzielenia na fragmenty. Aby uzyskać więcej informacji, zobacz [omówienie biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md).
> 
> 

Na przykład jeśli bazy danych ma nazwę odbiorcy, kolejność i szczegółów zamówienia (np. przykład tradycyjne bazy danych Northwind dostarczaną z programem SQL Server), to można podzielić te dane na wiele baz danych przez grupowanie klientowi kolejności pokrewne i szczegółowe informacje o zamówieniu. Aby zagwarantować, że odbiorcy danych pozostaje w pojedynczej bazy danych. Aplikacja będzie podziału różnych klientów dla baz danych, efektywnie rozłożenie obciążenia wielu baz danych. Z dzielenia na fragmenty klienci nie tylko można uniknąć limitu rozmiaru maksymalnego bazy danych, ale baza danych SQL Azure także można przetwarzać obciążeń, które są znacznie większe niż limity poziomów wydajności różnych tak długo, jak długo każdy jedna baza danych jest dopasowywana do jego jednostek dtu w warstwie.

Chociaż dzielenia na fragmenty bazy danych nie zmniejszyć pojemność zagregowanych danych zasobu dla rozwiązania, jest wysoce efektywne Obsługa bardzo dużych rozwiązań, które rozprzestrzeniają się przez wiele baz danych. Każda baza danych można uruchomić na poziomie różnych wydajności do obsługi bardzo duży, "skuteczne" bazy danych o wysokich wymaganiach dotyczących zasobów.

### <a name="functional-partitioning"></a>Partycjonowanie funkcjonalne
Użytkownicy programu SQL Server często łączyć wiele funkcji w jednej bazie danych. Na przykład jeśli aplikacja ma logikę umożliwiającą zarządzanie zapasami magazynu, tej bazy danych może być logiki skojarzonej z magazynu śledzenia zakupów, procedury składowane i indeksowanych lub zmaterializowanych widoków, które Zarządzanie raportowaniem koniec miesiąca. Ta metoda ułatwia administrowanie operacje, takie jak Kopia zapasowa bazy danych, ale również wymaga rozmiaru sprzętu do obsługi obciążenia szczytowego we wszystkich funkcji aplikacji.

Jeśli używasz skalowalność architektury w bazie danych SQL Azure jest dobrym rozwiązaniem jest podział różne funkcje aplikacji do różnych baz danych. Korzystając z tej techniki, każda aplikacja skaluje niezależnie. Aplikacji staje się coraz bardziej zajęty (i zwiększa obciążenie bazy danych), administrator może wybrać poziomy wydajności niezależne dla każdej funkcji aplikacji. Na granicy, z tej architektury aplikacji może być większy niż maszyny pojedynczego towaru może obsłużyć, ponieważ obciążenie pracą rozkłada się na wielu komputerach.

### <a name="batch-queries"></a>Partii zapytań
Dla aplikacji, które uzyskują dostęp do danych przy użyciu dużych częste zapytań ad hoc, znacznej ilości czasu odpowiedzi odbywa się na komunikację sieciową między warstwą aplikacji i warstwy bazy danych SQL Azure. Nawet w przypadku aplikacji i bazy danych SQL Azure znajdują się w tym samym centrum danych, opóźnienie sieci między nimi może być powiększony przez dużą ilością danych, operacje związane z dostępem. Aby zmniejszyć sieci round podróży dla operacji dostępu do danych, należy wziąć pod uwagę przy użyciu opcji albo partii zapytań ad hoc lub kompilowania ich jak przechowywane procedury. Jeśli partii zapytań ad hoc, możesz wysłać wiele zapytań jako jedna duża partia w podróży jednego z bazą danych SQL Azure. Jeśli kompilacja zapytań ad hoc w procedurze składowanej tak, jakby ich partii można osiągnąć ten sam rezultat. Za pomocą procedury składowanej zapewnia również korzyści zwiększa prawdopodobieństwo buforowania plany zapytań w bazie danych SQL Azure, dzięki czemu można ponownie użyć procedury składowanej.

Niektóre aplikacje intensywnie zapisu. Czasami można zmniejszyć całkowitego obciążenia We/Wy w bazie danych, biorąc pod uwagę sposób przetwarzania wsadowego zapisów. Często jest tak proste, jak przy użyciu jawnych transakcji zamiast automatycznego zatwierdzania transakcji w procedur składowanych i partie ad hoc. Do oceny różnych technik można użyć, zobacz [przetwarzanie wsadowe techniki dla aplikacji bazy danych SQL na platformie Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Wypróbuj obciążenia można znaleźć modelu prawo do przetwarzania wsadowego. Pamiętaj zrozumieć, że model może mieć gwarancje spójności transakcyjnej nieco inne. Znajdowanie prawo obciążenia, który minimalizuje wykorzystanie zasobów wymaga znajdowanie odpowiednie połączenie kompromisy wydajnością a spójnością.

### <a name="application-tier-caching"></a>Buforowanie warstwy aplikacji
Niektóre aplikacje bazy danych mają obciążeń intensywnie odczytu. Buforowanie warstwy może zmniejszyć obciążenie bazy danych i może zmniejszyć poziom wydajności, wymagany do obsługi bazy danych przy użyciu bazy danych SQL Azure. Z [pamięć podręczna Redis Azure](https://azure.microsoft.com/services/cache/), jeśli masz obciążenie intensywnie odczytu danych może odczytywać raz (lub raz na maszynie warstwy aplikacji, w zależności od sposobu skonfigurowania) i następnie przechowywania tych danych spoza Twojej bazy danych SQL. Jest to sposób, aby zmniejszyć obciążenie bazy danych (CPU i odczytu We/Wy), ale nie wpływa na spójności transakcyjnej, ponieważ odczytywane z pamięci podręcznej dane mogą być zsynchronizowane z danymi w bazie danych. Chociaż w wielu aplikacjach pewnego poziomu niespójności jest dopuszczalna, który nie jest spełniony dla wszystkich obciążeń. Wszelkie wymagania aplikacji należy zapoznać się przed zaimplementowaniem strategii buforowania warstwy aplikacji.

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji na temat warstwy usług, zobacz [opcje bazy danych SQL i wydajność](sql-database-service-tiers.md)
* Aby uzyskać więcej informacji na temat pule elastyczne, zobacz [co to jest puli elastycznej platformy Azure?](sql-database-elastic-pool.md)
* Aby uzyskać informacje o wydajności i elastyczne pule, zobacz [kiedy należy wziąć pod uwagę puli elastycznej](sql-database-elastic-pool-guidance.md)

