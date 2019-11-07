---
title: 'Najlepsze rozwiązania dotyczące analiz SQL w usłudze Azure Synapse Analytics (dawniej: SQL DW)'
description: 'Zalecenia i najlepsze rozwiązania dotyczące tworzenia rozwiązań dla analiz SQL w usłudze Azure Synapse Analytics (dawniej: SQL DW).'
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 6a9d7c4ed020f447e9512c7bc2edd0caecf3400d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646174"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Najlepsze rozwiązania dotyczące analiz SQL w usłudze Azure Synapse Analytics (dawniej: SQL DW)

Ten artykuł stanowi zbiór najlepszych rozwiązań ułatwiających osiągnięcie optymalnej wydajności wdrożenia usługi [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) .  Celem tego artykułu jest udostępnienie podstawowych wskazówek i wyróżnienie ważnych obszarów fokusu.  Każda sekcja wprowadza do koncepcji koncepcję, a następnie wskazuje na bardziej szczegółowe artykuły, które obejmują koncepcję bardziej szczegółowo. Sekwencja tematów jest w kolejności ważności. 

## <a name="reduce-cost-with-pause-and-scale"></a>Obniżenie kosztów dzięki wstrzymaniu i skalowaniu

Aby uzyskać więcej informacji dotyczących obniżania kosztów za pomocą wstrzymywania i skalowania, zobacz [Zarządzanie obliczeniami](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Prowadzenie statystyk
Azure SQL Data Warehouse można skonfigurować do automatycznego wykrywania i tworzenia statystyk dotyczących kolumn.  Plany zapytania utworzone przez optymalizator są tak dobre, jak w przypadku dostępnych statystyk.  Zalecamy włączenie AUTO_CREATE_STATISTICS dla baz danych i regularne aktualizowanie statystyk codziennie lub po każdym załadowaniu, aby zapewnić, że statystyki dotyczące kolumn używanych w zapytaniach są zawsze aktualne. 

Jeśli okaże się, że aktualizacja wszystkich statystyk trwa zbyt długo, możesz spróbować bardziej wybiórczo, w których kolumnach są potrzebne częste aktualizacje statystyk. Można na przykład aktualizować kolumny danych, w których codziennie mogą być dodawane nowe wartości. **W celu uzyskania największej korzyści można uzyskać zaktualizowane statystyki dotyczące kolumn w sprzężeniach, kolumn używanych w klauzuli WHERE i kolumnach, które znajdują się w grupie według.**

Zobacz też artykuły [Zarządzanie statystykami tabel][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS] i [UPDATE STATISTICS][UPDATE STATISTICS].

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Korzystanie z widoków DMV do monitorowania i optymalizowania zapytań
Analiza SQL ma kilka widoków DMV, których można użyć do monitorowania wykonywania zapytań.  W poniższym artykule o monitorowaniu podano instrukcje krok po kroku na temat przeglądania szczegółowych informacji dotyczących wykonywanych zapytań.  Aby szybko wyszukać zapytania w tych widokach DMV, z zapytaniami można użyć opcji LABEL.

Zobacz także artykuły [Monitorowanie obciążenia przy użyciu widoków DMV][Monitor your workload using DMVs], [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] oraz [sys.dm_pdw_waits][sys.dm_pdw_waits].

## <a name="tune-query-performance-with-new-product-enhancements"></a>Dostrajanie wydajności zapytań przy użyciu nowych ulepszeń produktu
- [Strojenie wydajności za pomocą zmaterializowanych widoków](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Strojenie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Strojenie wydajności za pomocą buforowania zestawu wyników](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>Grupowanie instrukcji INSERT w partie
Jednorazowe załadowanie do małej tabeli za pomocą instrukcji INSERT lub nawet okresowe przeładowanie wyszukiwania to strategie, które mogą sprawdzić się w przypadku instrukcji takich jak `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Jeśli jednak wymagane jest ładowanie tysięcy lub milionów wierszy na dzień, może okazać się, że same instrukcje INSERT zwyczajnie nie nadążą z działaniem.  Zamiast tego należy opracować procesy w taki sposób, aby zapisywały dane do pliku, po czym będzie następować okresowe uruchamianie innego procesu, który będzie ładować wspomniany plik.

Zobacz też artykuł poświęcony instrukcji [INSERT][INSERT].

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Korzystanie z funkcji PolyBase do szybkiego ładowania i eksportowania danych

 Program SQL Analytics obsługuje ładowanie i eksportowanie danych za poorednictwem kilku narzędzi, takich jak Azure Data Factory, Base i BCP.  W przypadku małych ilości danych, gdy wydajność nie ma decydującego znaczenia, można zastosować dowolne narzędzie.  Jednak podczas ładowania lub eksportowania dużych ilości danych lub w przypadku, gdy wymagana jest wysoka wydajność, najlepszym wyborem jest funkcja PolyBase.  
 
 Baza danych została zaprojektowana tak, aby korzystała z architektury MPP (wysoce równoległego przetwarzania), i będzie ładować i eksportować wielkości dane szybciej niż inne narzędzia.  Obciążenia funkcji PolyBase można uruchomić za pomocą instrukcji CTAS lub INSERT INTO.  **Użycie instrukcji CTAS minimalizuje rejestrowanie transakcji i stanowi najszybszy sposób ładowania danych.** 
 
  Azure Data Factory obsługuje również obciążenia wielopodstawowe i mogą osiągać podobną wydajność jako CTAS.  Funkcja PolyBase obsługuje wiele formatów plików, w tym pliki Gzip.  **Aby zmaksymalizować przepływność przy użyciu plików tekstowych gzip, należy podzielić pliki na 60 lub więcej plików, aby zmaksymalizować równoległość obciążenia.**  W celu uzyskania szybszej całkowitej przepływności warto rozważyć równoległe ładowania danych.

Zobacz też [ładowanie danych][Load data], [Przewodnik po korzystaniu][Guide for using PolyBase]z [wzorców i strategii ładowania puli SQL][Azure SQL Data Warehouse loading patterns and strategies], [ładowania danych przy użyciu Azure Data Factory][Load Data with Azure Data Factory], [przenoszenia danych za pomocą Azure Data Factory][Move data with Azure Data Factory], [tworzenia zewnętrznego formatu pliku][CREATE EXTERNAL FILE FORMAT], [tworzenia tabela jako Select (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Ładowanie i przesyłanie zapytań dotyczących tabel zewnętrznych
O ile funkcja Polybase (nazywana też tabelami zewnętrznymi) może być najszybszym sposobem załadowania danych, nie jest to narzędzie zoptymalizowane pod kątem zapytań. Tabele bazowe obecnie obsługują tylko pliki obiektów blob platformy Azure i Magazyn Azure Data Lake. Te pliki nie mają żadnych zasobów obliczeniowych stanowiących ich zaplecze.  

W związku z tym program SQL Analytics nie może odciążyć tej pracy i dlatego musi odczytać cały plik przez załadowanie go do bazy danych tempdb, aby można było je odczytać.  W związku z tym mając kilka kwerend, które będą wykonywać zapytania dotyczące tych danych, lepiej jest załadować te dane jeden raz i zapewnić możliwość obejmowania zapytaniem lokalnej tabeli.

Zobacz także artykuł [Przewodnik po funkcji PolyBase][Guide for using PolyBase].

## <a name="hash-distribute-large-tables"></a>Dystrybucja dużych tabel z użyciem skrótów
Domyślnym sposobem dystrybucji tabel jest działanie okrężne.  Dzięki temu użytkownicy mogą z łatwością rozpocząć tworzenie tabel bez konieczności podejmowania decyzji o tym, jak będzie przebiegać dystrybucja tabel.  Tabele wykorzystujące działanie okrężne mogą zapewniać wydajność wystarczającą dla niektórych obciążeń, ale w większości przypadków wybór kolumny dystrybucji sprawdzi się znacznie lepiej.  Najbardziej typowym przykładem sytuacji, w której zastosowanie dla tabeli dystrybucji według kolumny przyniesie znacznie lepsze wyniki niż zastosowanie tabeli z działaniem okrężnym, jest połączenie dwóch dużych tabel faktów.  

Na przykład w przypadku tabeli z zamówieniami, która jest rozłożona według wartości id_zamowienia, oraz tabeli z transakcjami, która także jest rozłożona według wartości id_zamowienia, po połączeniu tabeli z zamówieniami z tabelą z transakcjami poprzez pola id_zamowienia, to zapytanie staje się kwerendą przekazującą, co oznacza, że zostają wyeliminowane operacje przenoszenia danych.  Mniej kroków to szybsze kwerendy.  Mniejsza konieczność przenoszenia danych wpływa także na skrócenie czasu działania kwerend.  

Podczas ładowania dystrybuowanej tabeli należy upewnić się, że dane przychodzące nie są sortowane według klucza dystrybucji, ponieważ mogłoby to spowolnić ładowanie.  Zobacz poniższe linki, aby uzyskać więcej szczegółów na temat wybierania kolumny dystrybucji w celu zwiększenia wydajności, a także sposobu definiowania rozproszonej tabeli w klauzuli WITH instrukcji CREATE TABLE.

Zobacz także [Omówienie tabel][Table overview], [Dystrybucja tabel][Table distribution], [Wybór dystrybucji tabel][Selecting table distribution], [CREATE TABLE][CREATE TABLE] i [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="do-not-over-partition"></a>Unikanie nadmiernego partycjonowania
Gdy Partycjonowanie danych może być skuteczne do obsługi danych za pomocą przełączania partycji lub optymalizowania skanowania za pomocą eliminacji partycji, zbyt wiele partycji może spowalniać zapytania.  Często wysoce ziarnista strategia partycjonowania, która może być dobrze włączona SQL Server może nie współpracować z analizą SQL.  

Zbyt duża liczba partycji danych może także zmniejszyć skuteczność indeksów klastrowanego magazynu kolumn, jeśli każda partycja ma mniej niż milion wierszy.  Należy pamiętać, że w tle usługi SQL Analytics dzielą swoje dane na 60 baz danych, dlatego w przypadku tworzenia tabeli z partycjami 100 w rzeczywistości powstaje partycja 6000.  

Każde obciążenie jest inne, więc warto eksperymentować z podziałem na partycje — w ten sposób można przekonać się, jakie rozwiązanie sprawdzi się najlepiej w przypadku danego obciążenia.  Warto rozważyć stopień szczegółowości niższy od pomyślnie zastosowanego w programie SQL Server.  Można na przykład rozważyć wykorzystanie partycji cotygodniowych lub comiesięcznych zamiast partycji codziennych.

Zobacz też artykuł [Partycjonowanie tabel][Table partitioning].

## <a name="minimize-transaction-sizes"></a>Minimalizowanie rozmiarów transakcji
Instrukcje INSERT, UPDATE i DELETE działają w obrębie transakcji i muszą zostać wycofane, jeśli zakończą się niepowodzeniem.  Aby zminimalizować ryzyko długiego czasu wycofywania, warto minimalizować rozmiary transakcji, gdy tylko jest to możliwe.  Można to zrobić poprzez podział instrukcji INSERT, UPDATE i DELETE na części.  

Na przykład jeśli masz WSTAWIENIE, którego oczekujesz 1 godziny, a jeśli to możliwe, Podziel na cztery części, które będą wykonywane w ciągu 15 minut.  Stosuj szczególne przypadki minimalnego rejestrowania, takie jak CTAS, TRUNCATE, DROP TABLE lub INSERT, do opróżniania tabel, aby zmniejszyć ryzyko wycofywania.  

Innym sposobem na eliminację procesu wycofywania zmian jest korzystanie z operacji z właściwościami Tylko metadane, takich jak przełączanie partycji pod kątem zarządzania danymi.  Na przykład zamiast wykonywać instrukcję DELETE w celu usunięcia z tabeli wszystkich wierszy, dla których wartość data_zamowienia to październik 2001, można rozdzielić dane na miesiące, aby następnie przełączyć wybraną partycję na pustą partycję z innej tabeli (zobacz przykłady instrukcji ALTER TABLE).  

W przypadku tabel niepartycjonowanych Rozważ użycie CTAS do zapisania danych, które mają być przechowywane w tabeli, a nie za pomocą polecenia DELETE.  Jeśli CTAS zajmuje ten sam czas, jest to znacznie bezpieczniejsza operacja, ponieważ ma minimalne rejestrowanie transakcji i można ją szybko anulować w razie potrzeby.

Zobacz także artykuły [Omówienie transakcji][Understanding transactions], [Optymalizowanie transakcji][Optimizing transactions], [Partycjonowanie tabel][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE] oraz [Tworzenie tabeli przy użyciu instrukcji Select (CTAS)][Create table as select (CTAS)].

## <a name="reduce-query-result-sizes"></a>Zmniejsz rozmiary wyników zapytania  
Ten krok pozwala uniknąć problemów po stronie klienta spowodowanych przez duży wynik zapytania.  Możesz edytować zapytanie, aby zmniejszyć liczbę zwracanych wierszy. Niektóre narzędzia generowania zapytań umożliwiają dodanie składni "Top N" do każdego zapytania.  Możesz również CETAS wynik zapytania do tabeli tymczasowej, a następnie użyć eksportu bazowego dla przetwarzania niskiego poziomu.

## <a name="use-the-smallest-possible-column-size"></a>Użycie możliwie najmniejszego rozmiaru kolumny
Podczas definiowania kodu DDL przy użyciu najmniejszego typu danych, który będzie obsługiwał dane, poprawi wydajność zapytań.  Jest to szczególnie ważne w przypadku kolumn CHAR i VARCHAR.  Jeśli najdłuższa wartość w kolumnie ma 25 znaków, należy zdefiniować typ kolumny jako VARCHAR(25).  Należy unikać domyślnego definiowania wszystkich kolumn znaków jako kolumn długich wartości.  Ponadto należy unikać stosowania kolumn NVARCHAR, jeśli zastosowanie typu VARCHAR spełni wymagania danego zastosowania.

Zobacz także artykuły [Omówienie tabel][Table overview], [Typy danych w tabelach][Table data types] i [CREATE TABLE][CREATE TABLE].

## <a name="use-temporary-heap-tables-for-transient-data"></a>Korzystanie z tymczasowych tabel stosów dla danych przejściowych
Po tymczasowym wykorzystaniu danych, może się okazać, że użycie tabeli sterty zwiększy cały proces.  Jeśli dane są ładowane tylko pod kątem przygotowania do uruchomienia kolejnych przekształceń, załadowanie tabeli do stosu będzie znacznie szybsze niż załadowanie danych do tabeli klastrowanego magazynu kolumn.  

Ponadto załadowanie danych do tabeli tymczasowej będzie także znacznie szybsze niż załadowanie tabeli do pamięci trwałej.  Tabele tymczasowe zaczynają się od "#" i są dostępne tylko dla sesji, która ją utworzyła, więc mogą być tylko w ograniczonych scenariuszach.   Tabele stosu definiuje się przy użyciu klauzuli WITH instrukcji CREATE TABLE.  W przypadku użycia tabeli tymczasowej należy pamiętać o utworzeniu dla niej statystyk.

Zobacz także artykuły [Tabele tymczasowe][Temporary tables], [CREATE TABLE][CREATE TABLE] i [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="optimize-clustered-columnstore-tables"></a>Optymalizowanie tabel klastrowanego magazynu kolumn
Klastrowane indeksy magazynu kolumn to jeden z najbardziej wydajnych sposobów przechowywania danych w usłudze SQL Analytics.  Domyślnie tabele w analizie SQL są tworzone jako klastrowane magazynu kolumn.  Dla uzyskania najlepszej wydajności kwerend w odniesieniu do tabel magazynu kolumn ważne jest zapewnienie dobrej jakości segmentów.  

Jeśli wiersze są zapisywane w tabelach magazynu kolumn przy dużym wykorzystaniu pamięci, może to spowodować obniżenie jakości segmentów w magazynie kolumn.  Jakość segmentu określa się na podstawie liczby wierszy w skompresowanej grupie wierszy.  Zobacz sekcję [Przyczyny niskiej jakości indeksu magazynu kolumn][Causes of poor columnstore index quality] w artykule [Indeksy tabel][Table indexes], aby uzyskać instrukcje krok po kroku dotyczące sprawdzania i poprawiania jakości segmentu tabel klastrowanego magazynu kolumn.  

Ponieważ duże jakości segmenty magazynu kolumn są ważne, dobrym pomysłem jest użycie identyfikatorów użytkowników, które znajdują się w średniej lub dużej klasie zasobów do ładowania danych. W przypadku korzystania z niższych [jednostek magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) do użytkownika ładującego należy przypisać większą klasę zasobów.

Ponieważ tabele magazynu kolumn zwykle nie przepychają danych do skompresowanego segmentu magazynu kolumn, dopóki nie będzie więcej niż 1 000 000 wierszy na tabelę, a każda tabela analityczna SQL jest podzielona na 60 tabel, a tabela magazynu kolumn nie będzie korzystać z zapytania, chyba że tabela ma więcej niż 60 000 000 wierszy.  Stosowanie indeksu magazynu kolumn może nie mieć sensu w przypadku tabel z mniej niż 60 milionami wierszy.  Jego użycie nie przyniesie też jednak niekorzystnych skutków.  

Ponadto w przypadku partycjonowania danych warto wziąć pod uwagę, że każda partycja będzie musiała mieć milion wierszy, aby można było odnieść korzyść z zastosowania klastrowanego indeksu magazynu kolumn.  Jeśli tabela ma 100 partycji, będzie musiała mieć co najmniej 6 miliardów wierszy, aby skorzystanie z klastrowanego magazynu kolumn przyniosło korzyść (60 dystrybucji * 100 partycji * 1 milion wierszy).  

Jeśli tabela w tym przykładzie nie ma 6 miliardów wierszy, zmniejsz liczbę partycji lub rozważ zastąpienie jej tabelą stosu.  Ponadto warto poeksperymentować, aby sprawdzić, czy lepszą wydajność można uzyskać, korzystając z tabeli stosu z indeksami pomocniczymi zamiast z tabeli magazynu kolumn.

Podczas wykonywania zapytania odnoszącego się do tabeli magazynu kolumn kwerendy będą uruchamiane szybciej, jeśli wybrane zostaną tylko niezbędne kolumny.  

Zobacz także artykuły [Indeksy tabel][Table indexes], [Przewodnik po indeksach magazynu kolumn][Columnstore indexes guide] i [Ponowne tworzenie indeksów magazynu kolumn][Rebuilding columnstore indexes].

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Użycie większej klasy zasobów w celu poprawy wydajność przesyłania zapytań
Analiza SQL używa grup zasobów jako metody przydzielania pamięci do zapytań.  Poza tym wszyscy użytkownicy są przypisani do małej klasy zasobów, która przyznaje 100 MB pamięci na dystrybucję.  Jako że zawsze jest 60 dystrybucji i każda z nich ma przydzielone co najmniej 100 MB, całkowita alokacji pamięci w systemie wynosi 6000 MB lub nieco poniżej 6 GB.  

Stosowanie wybranych kwerend, takich jak duże sprzężenia lub obciążenia odnoszące się do klastrowanych tabel magazynu kolumn, pozwolą korzystać z większych alokacji pamięci.  W przypadku innych kwerend, takich jak czyste skanowanie, korzyść z ich zastosowania będzie niezauważalna.  Na stronie odwracania wykorzystanie większych klas zasobów zmniejsza współbieżność, dlatego warto wziąć pod uwagę ten wpływ przed przeniesieniem wszystkich użytkowników do dużej klasy zasobów.

Zobacz też [Resource classes for workload management (Klasy zasobów do zarządzania obciążeniem)](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Użycie mniejszej klasy zasobów w celu zwiększenia współbieżności
Jeśli obserwowanie, że zapytania użytkowników wydają się długo opóźniać, może to być spowodowane tym, że użytkownicy pracują w większych klasach zasobów i zużywają wiele miejsc współbieżności, co sprawia, że inne zapytania mogą kolejkować się w górę.  Uruchom polecenie `SELECT * FROM sys.dm_pdw_waits`, aby to sprawdzić i zobaczyć, czy żadne wiersze nie są zwracane.

Zobacz też [klasy zasobów dla zarządzania obciążeniami](resource-classes-for-workload-management.md), [sys. DM _pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Inne zasoby
Zobacz artykuł [Rozwiązywanie problemów][Troubleshooting], w którym omówiono typowe problemy i ich rozwiązania.

Jeśli nie możesz znaleźć tego, czego szukasz w tym artykule, spróbuj użyć opcji "Wyszukaj dokumenty" w lewej części tej strony, aby przeszukać wszystkie dokumenty usługi Azure Synapse.  [Forum usługi Azure Synapse][Azure SQL Data Warehouse MSDN Forum] to miejsce, w którym można zadawać pytania innym użytkownikom i grupom produktów platformy Azure Synapse. 

Firma Microsoft aktywnie monitoruje to forum, aby mieć pewność, że użytkownicy uzyskują odpowiedzi od innych użytkowników lub pracowników firmy Microsoft.  Jeśli wolisz zadać pytania na Stack Overflow, oferujemy również [Forum usługi Azure Synapse Stack Overflow][Azure SQL Data Warehouse Stack Overflow Forum].

Na koniec Skorzystaj ze strony [opinii o usłudze Azure Synapse][Azure SQL Data Warehouse Feedback] , aby wykonać żądania dotyczące funkcji.  Dodawanie konkretnych propozycji lub głosowanie na już zgłoszone sugestie bardzo pomaga nam określać priorytety funkcji.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
