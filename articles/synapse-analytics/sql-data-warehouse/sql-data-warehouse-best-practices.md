---
title: Najważniejsze wskazówki dotyczące puli SQL synapse w usłudze Azure Synapse Analytics (dawniej SQL DW)
description: Zalecenia i najlepsze rozwiązania dotyczące tworzenia rozwiązań dla puli SQL w usłudze Azure Synapse Analytics (dawniej SQL DW).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 0a2a49546a31f6d767b5e89348dc6b703278d877
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633631"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Najważniejsze wskazówki dotyczące puli SQL synapse w usłudze Azure Synapse Analytics (dawniej SQL DW)

Ten artykuł jest zbiorem najlepszych rozwiązań, które pomogą Ci osiągnąć optymalną wydajność z wdrożenia [puli SQL.](sql-data-warehouse-overview-what-is.md)  Celem tego artykułu jest udzielenie kilku podstawowych wskazówek i wyróżnienie ważnych obszarów zainteresowania.  

## <a name="reduce-cost-with-pause-and-scale"></a>Obniżenie kosztów dzięki wstrzymaniu i skalowaniu

Aby uzyskać więcej informacji dotyczących obniżania kosztów za pomocą wstrzymywania i skalowania, zobacz [Zarządzanie obliczeniami](sql-data-warehouse-manage-compute-overview.md).

## <a name="maintain-statistics"></a>Prowadzenie statystyk

Pula SQL można skonfigurować do automatycznego wykrywania i tworzenia statystyk kolumn.  Plany kwerend utworzonych przez optymalizator są tylko tak dobre, jak dostępne statystyki.  

Zaleca się włączenie AUTO_CREATE_STATISTICS dla baz danych i aktualizowanie statystyk codziennie lub po każdym obciążeniu, aby upewnić się, że statystyki dotyczące kolumn używanych w kwerendach są zawsze aktualne.

Jeśli okaże się, że aktualizacja wszystkich statystyk trwa zbyt długo, możesz spróbować być bardziej selektywny, jeśli chodzi o kolumny wymagające częstych aktualizacji statystyk. Można na przykład aktualizować kolumny danych, w których codziennie mogą być dodawane nowe wartości.

> [!TIP]
> Najwięcej korzyści można uzyskać dzięki zaktualizowanym statystykom kolumn biorących udział w sprzężeniach, kolumnach używanych w klauzuli WHERE i kolumnach znalezionych w polu GROUP BY.

Zobacz też [Zarządzanie statystykami tabeli](sql-data-warehouse-tables-statistics.md), [TWORZENIE STATYSTYK](https://msdn.microsoft.com/library/ms188038.aspx)i [AKTUALIZOWANIE STATYSTYK](https://msdn.microsoft.com/library/ms187348.aspx).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Korzystanie z widoków DMV do monitorowania i optymalizowania zapytań

Pula SQL ma kilka dmvs, które mogą służyć do monitorowania wykonywania kwerend.  Monitoruj obciążenie przy użyciu dmvs szczegóły artykułu instrukcje krok po kroku, jak spojrzeć na szczegóły kwerendy wykonującej.  

Aby szybko wyszukać zapytania w tych widokach DMV, z zapytaniami można użyć opcji LABEL.

Zobacz także [Monitorowanie obciążenia za pomocą DMVs](sql-data-warehouse-manage-monitor.md), [LABEL](sql-data-warehouse-develop-label.md), [OPCJA](https://msdn.microsoft.com/library/ms190322.aspx), [sys.dm_exec_sessions]( https://msdn.microsoft.com/library/ms176013.aspx), [sys.dm_pdw_exec_requests](https://msdn.microsoft.com/library/mt203887.aspx), [sys.dm_pdw_request_steps](https://msdn.microsoft.com/library/mt203913.aspx), [sys.dm_pdw_sql_requests](https://msdn.microsoft.com/library/mt203889.aspx), [sys.dm_pdw_dms_workers](https://msdn.microsoft.com/library/mt203878.aspx), [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx), i [sys.dm_pdw_waits](https://msdn.microsoft.com/library/mt203893.aspx).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Dostosowywanie wydajności zapytań za pomocą nowych ulepszeń produktów

- [Strojenie wydajności za pomocą zmaterializowanych widoków](performance-tuning-materialized-views.md)
- [Strojenie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn](performance-tuning-ordered-cci.md)
- [Strojenie wydajności za pomocą buforowania zestawu wyników](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>Grupowanie instrukcji INSERT w partie

Jednorazowe obciążenie do małej tabeli z instrukcją INSERT lub nawet okresowym przeładowaniem wyszukiwania może działać `INSERT INTO MyLookup VALUES (1, 'Type 1')`dobrze dla Twoich potrzeb za pomocą instrukcji, takiej jak .  

Jeśli jednak wymagane jest ładowanie tysięcy lub milionów wierszy na dzień, może okazać się, że same instrukcje INSERT zwyczajnie nie nadążą z działaniem.  Zamiast tego należy opracować procesy w taki sposób, aby zapisywały dane do pliku, po czym będzie następować okresowe uruchamianie innego procesu, który będzie ładować wspomniany plik.

Zobacz też [INSERT](https://msdn.microsoft.com/library/ms174335.aspx).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Korzystanie z funkcji PolyBase do szybkiego ładowania i eksportowania danych

Pula SQL obsługuje ładowanie i eksportowanie danych za pomocą kilku narzędzi, w tym usługi Azure Data Factory, PolyBase i BCP.  W przypadku małych ilości danych, gdy wydajność nie ma decydującego znaczenia, można zastosować dowolne narzędzie.  Jednak podczas ładowania lub eksportowania dużych ilości danych lub w przypadku, gdy wymagana jest wysoka wydajność, najlepszym wyborem jest funkcja PolyBase.  

PolyBase został zaprojektowany do wykorzystania architektury MPP (Massively Parallel Processing) i ładuje i eksportuje wielkości danych szybciej niż jakiekolwiek inne narzędzie.  Obciążenia funkcji PolyBase można uruchomić za pomocą instrukcji CTAS lub INSERT INTO.  

> [!TIP]
> Użycie instrukcji CTAS minimalizuje rejestrowanie transakcji i stanowi najszybszy sposób ładowania danych.

Usługa Azure Data Factory obsługuje również obciążenia PolyBase i może osiągnąć podobną wydajność jak CTAS.  Funkcja PolyBase obsługuje wiele formatów plików, w tym pliki Gzip.  
  
> [!NOTE]
> Aby zmaksymalizować przepustowość podczas korzystania z plików tekstowych gzip, podziel pliki na 60 lub więcej plików, aby zmaksymalizować równoległość obciążenia.  W celu uzyskania szybszej całkowitej przepływności warto rozważyć równoległe ładowania danych.

Zobacz też [Ładowanie danych](design-elt-data-loading.md), Przewodnik [dotyczący używania polybase](guidance-for-loading-data.md), [wzorce i strategie ładowania puli SQL](https://blogs.msdn.microsoft.com/sqlcat/20../../), Ładowanie danych za pomocą usługi Azure Data [Factory]( ../../data-factory/load-azure-sql-data-warehouse.md), [Przenoszenie danych za pomocą usługi Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md)(https://msdn.microsoft.com/library/dn935026.aspx)i Tworzenie [tabeli jako wybierz (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Ładowanie i przesyłanie zapytań dotyczących tabel zewnętrznych

O ile funkcja Polybase (nazywana też tabelami zewnętrznymi) może być najszybszym sposobem załadowania danych, nie jest to narzędzie zoptymalizowane pod kątem zapytań. Tabele polybase obecnie obsługują tylko pliki obiektów blob platformy Azure i magazyn usługi Azure Data Lake. Te pliki nie mają żadnych zasobów obliczeniowych stanowiących ich zaplecze.  

W rezultacie pula SQL nie może odciążyć tej pracy i dlatego musi odczytać cały plik, ładując go do tempdb w celu odczytu danych.  W związku z tym mając kilka kwerend, które będą wykonywać zapytania dotyczące tych danych, lepiej jest załadować te dane jeden raz i zapewnić możliwość obejmowania zapytaniem lokalnej tabeli.

Zobacz też [Przewodnik dotyczący korzystania z polybase](guidance-for-loading-data.md).

## <a name="hash-distribute-large-tables"></a>Dystrybucja dużych tabel z użyciem skrótów

Domyślnym sposobem dystrybucji tabel jest działanie okrężne.  Dzięki temu użytkownicy mogą z łatwością rozpocząć tworzenie tabel bez konieczności podejmowania decyzji o tym, jak będzie przebiegać dystrybucja tabel.  Tabele wykorzystujące działanie okrężne mogą zapewniać wydajność wystarczającą dla niektórych obciążeń, ale w większości przypadków wybór kolumny dystrybucji sprawdzi się znacznie lepiej.  

Najbardziej typowym przykładem sytuacji, w której zastosowanie dla tabeli dystrybucji według kolumny przyniesie znacznie lepsze wyniki niż zastosowanie tabeli z działaniem okrężnym, jest połączenie dwóch dużych tabel faktów.  

Na przykład w przypadku tabeli z zamówieniami, która jest rozłożona według wartości id_zamowienia, oraz tabeli z transakcjami, która także jest rozłożona według wartości id_zamowienia, po połączeniu tabeli z zamówieniami z tabelą z transakcjami poprzez pola id_zamowienia, to zapytanie staje się kwerendą przekazującą, co oznacza, że zostają wyeliminowane operacje przenoszenia danych.  Mniej kroków to szybsze kwerendy.  Mniejsza konieczność przenoszenia danych wpływa także na skrócenie czasu działania kwerend.  

> [!TIP]
> Podczas ładowania dystrybuowanej tabeli należy upewnić się, że dane przychodzące nie są sortowane według klucza dystrybucji, ponieważ mogłoby to spowolnić ładowanie.  

Zobacz poniższe łącza, aby uzyskać więcej informacji na temat sposobu wyboru kolumny dystrybucji może zwiększyć wydajność, a także jak zdefiniować tabelę rozproszoną w with klauzuli instrukcji CREATE TABLE.

Zobacz też [omówienie tabeli](sql-data-warehouse-tables-overview.md), [Rozkład tabeli](sql-data-warehouse-tables-distribute.md), [Wybieranie rozkładu tabeli,](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/) [TWORZENIE TABELI](https://msdn.microsoft.com/library/mt203953.aspx), [TWORZENIE TABELI JAKO WYBIERZ](https://msdn.microsoft.com/library/mt204041.aspx).

## <a name="do-not-over-partition"></a>Unikanie nadmiernego partycjonowania

Podczas partycjonowania danych może być skuteczne dla utrzymania danych za pośrednictwem przełączania partycji lub optymalizacji skanowania za pomocą eliminacji partycji, o zbyt wiele partycji może spowolnić zapytania.  Często strategia partycjonowania o wysokiej szczegółowości, która może działać dobrze na programie SQL Server, może nie działać dobrze w puli SQL.  

Zbyt duża liczba partycji danych może także zmniejszyć skuteczność indeksów klastrowanego magazynu kolumn, jeśli każda partycja ma mniej niż milion wierszy.  Należy pamiętać, że w kulisach puli SQL partycje danych dla Ciebie do 60 baz danych, więc jeśli utworzysz tabelę ze 100 partycji, to faktycznie powoduje 6000 partycji.  

Każde obciążenie jest inne, więc warto eksperymentować z podziałem na partycje — w ten sposób można przekonać się, jakie rozwiązanie sprawdzi się najlepiej w przypadku danego obciążenia.  Warto rozważyć stopień szczegółowości niższy od pomyślnie zastosowanego w programie SQL Server.  Można na przykład rozważyć wykorzystanie partycji cotygodniowych lub comiesięcznych zamiast partycji codziennych.

Zobacz też [Partycjonowanie tabeli](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimalizowanie rozmiarów transakcji

Instrukcje INSERT, UPDATE i DELETE działają w obrębie transakcji i muszą zostać wycofane, jeśli zakończą się niepowodzeniem.  Aby zminimalizować ryzyko długiego czasu wycofywania, warto minimalizować rozmiary transakcji, gdy tylko jest to możliwe.  Można to zrobić poprzez podział instrukcji INSERT, UPDATE i DELETE na części.  

Na przykład, jeśli masz INSERT, które mają potrwać 1 godzinę, jeśli to możliwe, rozbić INSERT na cztery części, które będą uruchamiane w ciągu 15 minut.  Wykorzystaj specjalne przypadki minimalnego rejestrowania, takie jak CTAS, TRUNCATE, DROP TABLE lub INSERT do pustych tabel, aby zmniejszyć ryzyko wycofywania.  

Innym sposobem na eliminację procesu wycofywania zmian jest korzystanie z operacji z właściwościami Tylko metadane, takich jak przełączanie partycji pod kątem zarządzania danymi.  Na przykład zamiast wykonać instrukcję DELETE, aby usunąć wszystkie wiersze w tabeli, w której order_date był w październiku 2001 r., można podzielić dane co miesiąc, a następnie przełączyć partycję z danymi dla pustej partycji z innej tabeli (zobacz przykłady [TABELI ALTER).](https://msdn.microsoft.com/library/ms190273.aspx)  

W przypadku tabel bez partycjonowania należy rozważyć użycie CTAS do zapisania danych, które mają być zachowane w tabeli, a nie przy użyciu delete.  Jeśli CTAS zajmuje taką samą ilość czasu, jest to znacznie bezpieczniejsza operacja do uruchomienia, ponieważ ma minimalny rejestrowanie transakcji i może zostać szybko anulowana w razie potrzeby.

Zobacz też [Opis transakcji](sql-data-warehouse-develop-transactions.md), [Optymalizacja transakcji](sql-data-warehouse-develop-best-practices-transactions.md), [Partycjonowanie tabeli,](sql-data-warehouse-tables-partition.md) [OBCINANIE TABELI,](https://msdn.microsoft.com/library/ms177570.aspx) [ZMIEŃ TABELĘ](https://msdn.microsoft.com/library/ms190273.aspx)i [Utwórz tabelę jako wybierz (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>Zmniejszanie rozmiarów wyników kwerendy

Ten krok pomaga uniknąć problemów po stronie klienta spowodowanych przez wynik dużych kwerend.  Kwerendę można edytować, aby zmniejszyć liczbę zwróconych wierszy. Niektóre narzędzia generowania zapytań umożliwiają dodawanie składni "top N" do każdej kwerendy.  Można również CETAS wynik kwerendy do tabeli tymczasowej, a następnie użyć PolyBase eksportu do przetwarzania downlevel.

## <a name="use-the-smallest-possible-column-size"></a>Użycie możliwie najmniejszego rozmiaru kolumny

Podczas definiowania DDL przy użyciu najmniejszego typu danych, który będzie obsługiwać dane poprawi wydajność kwerendy.  Takie podejście jest szczególnie ważne w przypadku kolumn CHAR i VARCHAR.  

Jeśli najdłuższa wartość w kolumnie ma 25 znaków, należy zdefiniować typ kolumny jako VARCHAR(25).  Należy unikać domyślnego definiowania wszystkich kolumn znaków jako kolumn długich wartości.  Ponadto należy unikać stosowania kolumn NVARCHAR, jeśli zastosowanie typu VARCHAR spełni wymagania danego zastosowania.

Zobacz też [omówienie tabeli](sql-data-warehouse-tables-overview.md), [Typy danych tabeli](sql-data-warehouse-tables-data-types.md), [TWORZENIE TABELI](https://msdn.microsoft.com/library/mt203953.aspx).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Korzystanie z tymczasowych tabel stosów dla danych przejściowych

Podczas tymczasowego lądowania danych, może się okazać, że przy użyciu tabeli sterty sprawi, że ogólny proces szybciej.  Jeśli dane są ładowane tylko pod kątem przygotowania do uruchomienia kolejnych przekształceń, załadowanie tabeli do stosu będzie znacznie szybsze niż załadowanie danych do tabeli klastrowanego magazynu kolumn.  

Ponadto załadowanie danych do tabeli tymczasowej będzie także znacznie szybsze niż załadowanie tabeli do pamięci trwałej.  Tabele tymczasowe zaczynają się od "#" i są dostępne tylko przez sesję, która go utworzyła, więc mogą działać tylko w ograniczonych scenariuszach.

Tabele stosu definiuje się przy użyciu klauzuli WITH instrukcji CREATE TABLE.  W przypadku użycia tabeli tymczasowej należy pamiętać o utworzeniu dla niej statystyk.

Zobacz też [Tabele tymczasowe](sql-data-warehouse-tables-temporary.md), [TWORZENIE TABELI](https://msdn.microsoft.com/library/mt203953.aspx), [TWORZENIE TABELI JAKO WYBIERZ](https://msdn.microsoft.com/library/mt204041.aspx).

## <a name="optimize-clustered-columnstore-tables"></a>Optymalizowanie tabel klastrowanego magazynu kolumn

Indeksy klastrowanego magazynu kolumn są jednym z najbardziej efektywnych sposobów przechowywania danych w puli SQL.  Domyślnie tabele w puli SQL są tworzone jako klastrowane ColumnStore.  Dla uzyskania najlepszej wydajności kwerend w odniesieniu do tabel magazynu kolumn ważne jest zapewnienie dobrej jakości segmentów.  

Jeśli wiersze są zapisywane w tabelach magazynu kolumn przy dużym wykorzystaniu pamięci, może to spowodować obniżenie jakości segmentów w magazynie kolumn.  Jakość segmentu określa się na podstawie liczby wierszy w skompresowanej grupie wierszy.  Zobacz [przyczyny niskiej jakości indeksu magazynu kolumn](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) w [tabeli indeksy](sql-data-warehouse-tables-index.md) artykułu instrukcje krok po kroku dotyczące wykrywania i poprawy jakości segmentu dla klastrowanych tabel magazynu kolumn.  

Ponieważ segmenty magazynu kolumn wysokiej jakości są ważne, warto używać identyfikatorów użytkowników, które znajdują się w klasie średnich lub dużych zasobów do ładowania danych. Korzystanie z [niższych jednostek magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) oznacza, że chcesz przypisać większą klasę zasobów do użytkownika ładującego.

Ponieważ tabele magazynu kolumn zazwyczaj nie wypchnie danych do skompresowanego segmentu magazynu kolumn, dopóki nie będzie więcej niż 1 milion wierszy na tabelę, a każda tabela puli SQL jest podzielona na 60 tabel, zgodnie z zasadą tabele magazynu kolumn nie będą korzystać z kwerendy, chyba że tabela ma więcej niż 60 milionów wierszy.  Stosowanie indeksu magazynu kolumn może nie mieć sensu w przypadku tabel z mniej niż 60 milionami wierszy.  Jego użycie nie przyniesie też jednak niekorzystnych skutków.  

Ponadto w przypadku partycjonowania danych warto wziąć pod uwagę, że każda partycja będzie musiała mieć milion wierszy, aby można było odnieść korzyść z zastosowania klastrowanego indeksu magazynu kolumn.  Jeśli tabela ma 100 partycji, to będzie musiał mieć co najmniej 6 miliardów wierszy, aby korzystać z magazynu kolumn klastrowanych (60 dystrybucji *100 partycji* 1 milion wierszy).  

Jeśli tabela w tym przykładzie nie ma 6 miliardów wierszy, zmniejsz liczbę partycji lub rozważ zastąpienie jej tabelą stosu.  Ponadto warto poeksperymentować, aby sprawdzić, czy lepszą wydajność można uzyskać, korzystając z tabeli stosu z indeksami pomocniczymi zamiast z tabeli magazynu kolumn.

> [!TIP]
> Podczas wykonywania zapytania odnoszącego się do tabeli magazynu kolumn kwerendy będą uruchamiane szybciej, jeśli wybrane zostaną tylko niezbędne kolumny.  

Zobacz także artykuły [Indeksy tabel](sql-data-warehouse-tables-index.md), [Przewodnik po indeksach magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx) i [Ponowne tworzenie indeksów magazynu kolumn](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Użycie większej klasy zasobów w celu poprawy wydajność przesyłania zapytań

Pula SQL używa grup zasobów jako sposobu przydzielania pamięci do kwerend.  Po wyjęciu z pudełka wszyscy użytkownicy są przypisywani do małej klasy zasobów, która przyznaje 100 MB pamięci na dystrybucję.  Jako że zawsze jest 60 dystrybucji i każda z nich ma przydzielone co najmniej 100 MB, całkowita alokacji pamięci w systemie wynosi 6000 MB lub nieco poniżej 6 GB.  

Stosowanie wybranych kwerend, takich jak duże sprzężenia lub obciążenia odnoszące się do klastrowanych tabel magazynu kolumn, pozwolą korzystać z większych alokacji pamięci.  Niektóre zapytania, takie jak czyste skany, nie przyniosą żadnych korzyści.  Jednak korzystanie z większych klas zasobów zmniejsza współbieżność, więc należy wziąć pod uwagę ten wpływ przed przeniesieniem wszystkich użytkowników do klasy dużych zasobów.

Zobacz też [Klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Użycie mniejszej klasy zasobów w celu zwiększenia współbieżności

Jeśli zauważysz, że kwerendy użytkowników wydają się mieć duże opóźnienie, może to być, że użytkownicy są uruchomione w większych klasach zasobów i zużywają wiele gniazd współbieżności, powodując inne zapytania do kolejki w kolejce.  Uruchom polecenie `SELECT * FROM sys.dm_pdw_waits`, aby to sprawdzić i zobaczyć, czy żadne wiersze nie są zwracane.

Zobacz też [Klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md), [sys.dm_pdw_waits](https://msdn.microsoft.com/library/mt203893.aspx).

## <a name="other-resources"></a>Inne zasoby

Zobacz artykuł [Rozwiązywanie problemów](sql-data-warehouse-troubleshoot.md), w którym omówiono typowe problemy i ich rozwiązania.

Jeśli nie znalazłeś tego, czego szukasz w tym artykule, spróbuj użyć "Wyszukaj dokumenty" po lewej stronie tej strony, aby przeszukać wszystkie dokumenty usługi Azure Synapse.  [Forum Synapse platformy Azure](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) to miejsce, w które można publikować pytania innym użytkownikom i grupie produktów Usługi Azure Synapse. Firma Microsoft aktywnie monitoruje to forum, aby mieć pewność, że użytkownicy uzyskują odpowiedzi od innych użytkowników lub pracowników firmy Microsoft.  

Jeśli wolisz zadać pytania na przepełnienie stosu, mamy również [Forum Przepełnienia stosu w stosie Synapse.](https://stackoverflow.com/questions/tagged/azure-sqldw)

Użyj [strony Opinie usługi Azure Synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse) do żądania funkcji.  Dodawanie konkretnych propozycji lub głosowanie na już zgłoszone sugestie bardzo pomaga nam określać priorytety funkcji.
