---
title: Najlepsze praktyki programistyczne
description: Zalecenia i najlepsze rozwiązania, które należy znać podczas opracowywania rozwiązań dla puli SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9c4f08b143ab4a0d3e780f68f8d5ab823d4eae12
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745365"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Najlepsze rozwiązania dotyczące programowania puli sql synapse

W tym artykule opisano wskazówki i najlepsze rozwiązania podczas opracowywania rozwiązania puli SQL.

## <a name="tune-query-performance-with-new-product-enhancements"></a>Dostosowywanie wydajności zapytań za pomocą nowych ulepszeń produktów

- [Strojenie wydajności za pomocą zmaterializowanych widoków](performance-tuning-materialized-views.md)
- [Strojenie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn](performance-tuning-ordered-cci.md)
- [Strojenie wydajności za pomocą buforowania zestawu wyników](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Obniżenie kosztów dzięki wstrzymaniu i skalowaniu

Aby uzyskać więcej informacji na temat zmniejszania kosztów poprzez wstrzymywanie i skalowanie, zobacz artykuł [Zarządzanie obliczeniami.](sql-data-warehouse-manage-compute-overview.md)

## <a name="maintain-statistics"></a>Prowadzenie statystyk

Pula SQL można skonfigurować do automatycznego wykrywania i tworzenia statystyk kolumn.  Plany kwerend utworzonych przez optymalizator są tylko tak dobre, jak dostępne statystyki.  

Zaleca się włączenie AUTO_CREATE_STATISTICS dla baz danych i aktualizowanie statystyk codziennie lub po każdym obciążeniu, aby upewnić się, że statystyki dotyczące kolumn używanych w kwerendach są zawsze aktualne.

Jeśli okaże się, że aktualizacja wszystkich statystyk trwa zbyt długo, możesz spróbować być bardziej selektywny, jeśli chodzi o kolumny wymagające częstych aktualizacji statystyk. Można na przykład aktualizować kolumny danych, w których codziennie mogą być dodawane nowe wartości.

> [!TIP]
> Najwięcej korzyści można uzyskać dzięki zaktualizowanym statystykom kolumn zaangażowanych w sprzężenia, kolumnom używanym w klauzuli WHERE i kolumnach znalezionych w polu GROUP BY.

Zobacz też [Zarządzanie statystykami tabeli](sql-data-warehouse-tables-statistics.md), [TWORZENIE STATYSTYK](sql-data-warehouse-tables-statistics.md)i [AKTUALIZOWANIE STATYSTYK](sql-data-warehouse-tables-statistics.md#update-statistics).

## <a name="hash-distribute-large-tables"></a>Dystrybucja dużych tabel z użyciem skrótów

Domyślnym sposobem dystrybucji tabel jest działanie okrężne.  Ten projekt ułatwia użytkownikom rozpoczęcie tworzenia tabel bez konieczności decydowania o sposobie dystrybucji ich tabel.  

Tabele wykorzystujące działanie okrężne mogą zapewniać wydajność wystarczającą dla niektórych obciążeń, ale w większości przypadków wybór kolumny dystrybucji sprawdzi się znacznie lepiej.  Najbardziej typowym przykładem sytuacji, w której zastosowanie dla tabeli dystrybucji według kolumny przyniesie znacznie lepsze wyniki niż zastosowanie tabeli z działaniem okrężnym, jest połączenie dwóch dużych tabel faktów.  

Na przykład w przypadku tabeli z zamówieniami, która jest rozłożona według wartości id_zamowienia, oraz tabeli z transakcjami, która także jest rozłożona według wartości id_zamowienia, po połączeniu tabeli z zamówieniami z tabelą z transakcjami poprzez pola id_zamowienia, to zapytanie staje się kwerendą przekazującą, co oznacza, że zostają wyeliminowane operacje przenoszenia danych.  Mniej kroków to szybsze kwerendy.  Mniejsza konieczność przenoszenia danych wpływa także na skrócenie czasu działania kwerend.  

Podczas ładowania dystrybuowanej tabeli należy upewnić się, że dane przychodzące nie są sortowane według klucza dystrybucji, ponieważ mogłoby to spowolnić ładowanie.  Poniższe artykuły podają dalsze szczegóły dotyczące zwiększania wydajności, wybierając kolumnę dystrybucyjną i jak zdefiniować tabelę rozproszoną w klauzuli WITH instrukcji CREATE TABLES.

Zobacz też [omówienie tabeli](sql-data-warehouse-tables-overview.md), [Rozkład tabeli](sql-data-warehouse-tables-distribute.md), [Wybieranie rozkładu tabeli,](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/) [TWORZENIE TABELI](sql-data-warehouse-tables-overview.md)i [TWORZENIE TABELI JAKO WYBIERZ](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>Unikanie nadmiernego partycjonowania

Podczas partycjonowania danych może być skuteczne dla utrzymania danych za pośrednictwem przełączania partycji lub optymalizacji skanowania za pomocą eliminacji partycji, o zbyt wiele partycji może spowolnić zapytania.  

Często strategia partycjonowania o wysokiej szczegółowości, która może działać dobrze na programie SQL Server, może nie działać dobrze w puli SQL.  Zbyt duża liczba partycji danych może także zmniejszyć skuteczność indeksów klastrowanego magazynu kolumn, jeśli każda partycja ma mniej niż milion wierszy.  

Należy pamiętać, że w kulisach puli SQL partycje danych dla Ciebie do 60 baz danych, więc jeśli utworzysz tabelę ze 100 partycji, to faktycznie powoduje 6000 partycji.  Każde obciążenie jest inne, więc warto eksperymentować z podziałem na partycje — w ten sposób można przekonać się, jakie rozwiązanie sprawdzi się najlepiej w przypadku danego obciążenia.  

> [!TIP]
> Należy rozważyć użycie niższej szczegółowości niż to, co może pracował dla Ciebie w programie SQL Server.  Można na przykład rozważyć wykorzystanie partycji cotygodniowych lub comiesięcznych zamiast partycji codziennych.

Zobacz też [Partycjonowanie tabeli](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimalizowanie rozmiarów transakcji

Instrukcje INSERT, UPDATE i DELETE działają w obrębie transakcji i muszą zostać wycofane, jeśli zakończą się niepowodzeniem.  Aby zminimalizować ryzyko długiego czasu wycofywania, warto minimalizować rozmiary transakcji, gdy tylko jest to możliwe.  Można to zrobić poprzez podział instrukcji INSERT, UPDATE i DELETE na części.  

Na przykład, jeśli masz INSERT, które spodziewasz się potrwać 1 godzinę, jeśli to możliwe, rozbić INSERT na cztery części, które będą uruchamiane w ciągu 15 minut.  Wykorzystaj specjalne przypadki minimalnego rejestrowania, takie jak CTAS, TRUNCATE, DROP TABLE lub INSERT do pustych tabel, aby zmniejszyć ryzyko wycofywania.  

Innym sposobem na eliminację procesu wycofywania zmian jest korzystanie z operacji z właściwościami Tylko metadane, takich jak przełączanie partycji pod kątem zarządzania danymi.  Na przykład zamiast wykonywać instrukcję DELETE w celu usunięcia z tabeli wszystkich wierszy, dla których wartość data_zamowienia to październik 2001, można rozdzielić dane na miesiące, aby następnie przełączyć wybraną partycję na pustą partycję z innej tabeli (zobacz przykłady instrukcji ALTER TABLE).  

W przypadku tabel bez partycjonowania należy rozważyć użycie CTAS do zapisania danych, które mają być zachowane w tabeli, a nie przy użyciu delete.  Jeśli CTAS zajmuje taką samą ilość czasu, jest to znacznie bezpieczniejsza operacja do uruchomienia, ponieważ ma minimalny rejestrowanie transakcji i może zostać szybko anulowana w razie potrzeby.

Zobacz też [Opis transakcji](sql-data-warehouse-develop-transactions.md), [Optymalizacja transakcji](sql-data-warehouse-develop-best-practices-transactions.md), [Partycjonowanie tabeli,](sql-data-warehouse-tables-partition.md) [OBCINANIE TABELI,](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) [ZMIEŃ TABELĘ](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)i [Utwórz tabelę jako wybierz (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="use-the-smallest-possible-column-size"></a>Użycie możliwie najmniejszego rozmiaru kolumny

Podczas definiowania DDL przy użyciu najmniejszego typu danych, który będzie obsługiwać dane poprawi wydajność kwerendy.  Takie podejście jest szczególnie ważne w przypadku kolumn CHAR i VARCHAR.  

Jeśli najdłuższa wartość w kolumnie ma 25 znaków, należy zdefiniować typ kolumny jako VARCHAR(25).  Należy unikać domyślnego definiowania wszystkich kolumn znaków jako kolumn długich wartości.  Ponadto należy unikać stosowania kolumn NVARCHAR, jeśli zastosowanie typu VARCHAR spełni wymagania danego zastosowania.

Zobacz też [omówienie tabeli](sql-data-warehouse-tables-overview.md), [Typy danych tabeli](sql-data-warehouse-tables-data-types.md)i [TWORZENIE TABELI](sql-data-warehouse-tables-overview.md).

## <a name="optimize-clustered-columnstore-tables"></a>Optymalizowanie tabel klastrowanego magazynu kolumn

Indeksy klastrowanego magazynu kolumn są jednym z najbardziej efektywnych sposobów przechowywania danych w puli SQL.  Domyślnie tabele w puli SQL są tworzone jako klastrowane ColumnStore.  

> [!NOTE]
> Aby uzyskać optymalną wydajność zapytań w tabelach magazynu kolumn, ważna jest dobra jakość segmentu.  

Jeśli wiersze są zapisywane w tabelach magazynu kolumn przy dużym wykorzystaniu pamięci, może to spowodować obniżenie jakości segmentów w magazynie kolumn.  Jakość segmentu określa się na podstawie liczby wierszy w skompresowanej grupie wierszy.  

Zobacz [przyczyny niskiej jakości indeksu magazynu kolumn](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) w [tabeli indeksy](sql-data-warehouse-tables-index.md) artykułu instrukcje krok po kroku dotyczące wykrywania i poprawy jakości segmentu dla klastrowanych tabel magazynu kolumn.  

Ponieważ segmenty magazynu kolumn wysokiej jakości są ważne, warto używać identyfikatorów użytkowników, które znajdują się w klasie średnich lub dużych zasobów do ładowania danych. Korzystanie z [niższych jednostek magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) oznacza, że chcesz przypisać większą klasę zasobów do użytkownika ładującego.

Ponieważ tabele magazynu kolumn zazwyczaj nie wypychają danych do skompresowanego segmentu magazynu kolumn, dopóki nie będzie więcej niż 1 miliona wierszy na tabelę, a każda tabela puli SQL jest podzielona na 60 tabel, ogólnie rzecz biorąc, tabele magazynu kolumn nie będą korzystać z kwerendy, chyba że tabela ma więcej niż 60 milionów wierszy.  

Dla tabeli z mniej niż 60 milionów wierszy, może nie mieć żadnego sensu, aby mieć indeks magazynu kolumn.  Jego użycie nie przyniesie też jednak niekorzystnych skutków.  

Ponadto w przypadku partycjonowania danych warto wziąć pod uwagę, że każda partycja będzie musiała mieć milion wierszy, aby można było odnieść korzyść z zastosowania klastrowanego indeksu magazynu kolumn.  Jeśli tabela ma 100 partycji, to będzie musiał mieć co najmniej 6 miliardów wierszy, aby korzystać z magazynu kolumn klastrowanych (60 dystrybucji *100 partycji* 1 milion wierszy).  

Jeśli tabela w tym przykładzie nie ma 6 miliardów wierszy, zmniejsz liczbę partycji lub rozważ zastąpienie jej tabelą stosu.  Ponadto warto poeksperymentować, aby sprawdzić, czy lepszą wydajność można uzyskać, korzystając z tabeli stosu z indeksami pomocniczymi zamiast z tabeli magazynu kolumn.

> [!TIP]
> Podczas wykonywania zapytania odnoszącego się do tabeli magazynu kolumn kwerendy będą uruchamiane szybciej, jeśli wybrane zostaną tylko niezbędne kolumny.  

Zobacz też [Indeksy tabel](sql-data-warehouse-tables-index.md), [Przewodnik po indeksach magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)i Przebudowa [indeksów magazynu kolumn](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="next-steps"></a>Następne kroki

Jeśli nie znajdziesz tego, czego szukasz w tym artykule, spróbuj użyć "Wyszukaj dokumenty" po lewej stronie tej strony, aby przeszukać wszystkie dokumenty usługi Azure Synapse.  

[Forum Synapse platformy Azure](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) to miejsce, w które można publikować pytania innym użytkownikom i grupie produktów Usługi Azure Synapse.  Firma Microsoft aktywnie monitoruje to forum, aby mieć pewność, że użytkownicy uzyskują odpowiedzi od innych użytkowników lub pracowników firmy Microsoft.  

Jeśli wolisz zadać pytanie w witrynie Stack Overflow, możesz także skorzystać z [tego forum usługi Azure SQL Data Warehouse](https://stackoverflow.com/questions/tagged/azure-sqldw).

Strona [Opinie o synapsie platformy Azure](https://feedback.azure.com/forums/307516-sql-data-warehouse) służy do tworzenia żądań funkcji.  Dodawanie konkretnych propozycji lub głosowanie na już zgłoszone sugestie bardzo pomaga nam określać priorytety funkcji.
