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
ms.openlocfilehash: a4902d2817c98b002b9767ad55d7ac60f5cb0c93
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350705"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Najlepsze rozwiązania dotyczące programowania puli sql synapse
W tym artykule opisano wskazówki i najlepsze rozwiązania podczas opracowywania rozwiązania magazynu danych. 

## <a name="tune-query-performance-with-new-product-enhancements"></a>Dostosowywanie wydajności zapytań za pomocą nowych ulepszeń produktów  
- [Strojenie wydajności za pomocą zmaterializowanych widoków](performance-tuning-materialized-views.md)
- [Strojenie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn](performance-tuning-ordered-cci.md)
- [Strojenie wydajności za pomocą buforowania zestawu wyników](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Obniżenie kosztów dzięki wstrzymaniu i skalowaniu
Aby uzyskać więcej informacji dotyczących obniżania kosztów za pomocą wstrzymywania i skalowania, zobacz [Zarządzanie obliczeniami](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>Prowadzenie statystyk
Pulę SQL synapse można skonfigurować do automatycznego wykrywania i tworzenia statystyk kolumn.  Plany kwerend utworzonych przez optymalizator są tylko tak dobre, jak dostępne statystyki.  Zaleca się włączenie AUTO_CREATE_STATISTICS dla baz danych i aktualizowanie statystyk codziennie lub po każdym obciążeniu, aby upewnić się, że statystyki dotyczące kolumn używanych w kwerendach są zawsze aktualne. 

Jeśli okaże się, że aktualizacja wszystkich statystyk trwa zbyt długo, możesz spróbować być bardziej selektywny, jeśli chodzi o kolumny wymagające częstych aktualizacji statystyk. Można na przykład aktualizować kolumny danych, w których codziennie mogą być dodawane nowe wartości. **Najwięcej korzyści można uzyskać dzięki zaktualizowanym statystykom kolumn biorących udział w sprzężeniach, kolumnach używanych w klauzuli WHERE i kolumnach znalezionych w polu GROUP BY.**

Zobacz też artykuły [Zarządzanie statystykami tabel](sql-data-warehouse-tables-statistics.md), [CREATE STATISTICS](sql-data-warehouse-tables-statistics.md) i [UPDATE STATISTICS](sql-data-warehouse-tables-statistics.md#updating-statistics).

## <a name="hash-distribute-large-tables"></a>Dystrybucja dużych tabel z użyciem skrótów
Domyślnym sposobem dystrybucji tabel jest działanie okrężne.  Ten projekt ułatwia użytkownikom rozpoczęcie tworzenia tabel bez konieczności decydowania o sposobie dystrybucji ich tabel.  Tabele wykorzystujące działanie okrężne mogą zapewniać wydajność wystarczającą dla niektórych obciążeń, ale w większości przypadków wybór kolumny dystrybucji sprawdzi się znacznie lepiej.  Najbardziej typowym przykładem sytuacji, w której zastosowanie dla tabeli dystrybucji według kolumny przyniesie znacznie lepsze wyniki niż zastosowanie tabeli z działaniem okrężnym, jest połączenie dwóch dużych tabel faktów.  Na przykład w przypadku tabeli z zamówieniami, która jest rozłożona według wartości id_zamowienia, oraz tabeli z transakcjami, która także jest rozłożona według wartości id_zamowienia, po połączeniu tabeli z zamówieniami z tabelą z transakcjami poprzez pola id_zamowienia, to zapytanie staje się kwerendą przekazującą, co oznacza, że zostają wyeliminowane operacje przenoszenia danych.  Mniej kroków to szybsze kwerendy.  Mniejsza konieczność przenoszenia danych wpływa także na skrócenie czasu działania kwerend.  To wyjaśnienie tylko zarysować powierzchnię. Podczas ładowania dystrybuowanej tabeli należy upewnić się, że dane przychodzące nie są sortowane według klucza dystrybucji, ponieważ mogłoby to spowolnić ładowanie.  Zobacz poniższe łącza, aby uzyskać wiele więcej szczegółów na temat sposobu wyboru kolumny dystrybucji może zwiększyć wydajność, a także jak zdefiniować tabelę rozproszoną w with klauzuli tworzenia tabel instrukcji.

Zobacz także [Omówienie tabel](sql-data-warehouse-tables-overview.md), [Dystrybucja tabel](sql-data-warehouse-tables-distribute.md), [Wybór dystrybucji tabel](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](sql-data-warehouse-tables-overview.md) i [CREATE TABLE AS SELECT](sql-data-warehouse-develop-ctas.md).

## <a name="do-not-over-partition"></a>Unikanie nadmiernego partycjonowania
Podczas partycjonowania danych może być skuteczne dla utrzymania danych za pośrednictwem przełączania partycji lub optymalizacji skanowania za pomocą eliminacji partycji, o zbyt wiele partycji może spowolnić zapytania.  Często strategia partycjonowania o wysokiej szczegółowości, która może działać dobrze na programie SQL Server, może nie działać dobrze w puli SQL.  Zbyt duża liczba partycji danych może także zmniejszyć skuteczność indeksów klastrowanego magazynu kolumn, jeśli każda partycja ma mniej niż milion wierszy.  Należy pamiętać, że w kulisach puli SQL partycje danych dla Ciebie do 60 baz danych, więc jeśli utworzysz tabelę ze 100 partycji, to faktycznie powoduje 6000 partycji.  Każde obciążenie jest inne, więc warto eksperymentować z podziałem na partycje — w ten sposób można przekonać się, jakie rozwiązanie sprawdzi się najlepiej w przypadku danego obciążenia.  Warto rozważyć stopień szczegółowości niższy od pomyślnie zastosowanego w programie SQL Server.  Można na przykład rozważyć wykorzystanie partycji cotygodniowych lub comiesięcznych zamiast partycji codziennych.

Zobacz też artykuł [Partycjonowanie tabel](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimalizowanie rozmiarów transakcji
Instrukcje INSERT, UPDATE i DELETE działają w obrębie transakcji i muszą zostać wycofane, jeśli zakończą się niepowodzeniem.  Aby zminimalizować ryzyko długiego czasu wycofywania, warto minimalizować rozmiary transakcji, gdy tylko jest to możliwe.  Można to zrobić poprzez podział instrukcji INSERT, UPDATE i DELETE na części.  Na przykład, jeśli masz INSERT, które spodziewasz się potrwać 1 godzinę, jeśli to możliwe, rozbić INSERT na cztery części, które będą uruchamiane w ciągu 15 minut.  Stosuj szczególne przypadki minimalnego rejestrowania, takie jak CTAS, TRUNCATE, DROP TABLE lub INSERT, do opróżniania tabel, aby zmniejszyć ryzyko wycofywania.  Innym sposobem na eliminację procesu wycofywania zmian jest korzystanie z operacji z właściwościami Tylko metadane, takich jak przełączanie partycji pod kątem zarządzania danymi.  Na przykład zamiast wykonywać instrukcję DELETE w celu usunięcia z tabeli wszystkich wierszy, dla których wartość data_zamowienia to październik 2001, można rozdzielić dane na miesiące, aby następnie przełączyć wybraną partycję na pustą partycję z innej tabeli (zobacz przykłady instrukcji ALTER TABLE).  W przypadku tabel niepartycjonowanych zamiast korzystać z instrukcji DELETE, należy rozważyć użycie instrukcji CTAS do zapisu danych, które mają zostać zachowane w tabeli.  Jeśli CTAS zajmuje taką samą ilość czasu, jest to znacznie bezpieczniejsza operacja do uruchomienia, ponieważ ma minimalny rejestrowanie transakcji i może zostać szybko anulowana w razie potrzeby.

Zobacz też [Opis transakcji](sql-data-warehouse-develop-transactions.md), [Optymalizacja transakcji](sql-data-warehouse-develop-best-practices-transactions.md), [Partycjonowanie tabeli,](sql-data-warehouse-tables-partition.md) [OBCINANIE TABELI,](https://msdn.microsoft.com/library/ms177570.aspx) [ZMIEŃ TABELĘ](https://msdn.microsoft.com/library/ms190273.aspx), [Tworzenie tabeli jako wybierz (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="use-the-smallest-possible-column-size"></a>Użycie możliwie najmniejszego rozmiaru kolumny
Podczas definiowania DDL przy użyciu najmniejszego typu danych, który będzie obsługiwać dane poprawi wydajność kwerendy.  Jest to szczególnie ważne w przypadku kolumn CHAR i VARCHAR.  Jeśli najdłuższa wartość w kolumnie ma 25 znaków, należy zdefiniować typ kolumny jako VARCHAR(25).  Należy unikać domyślnego definiowania wszystkich kolumn znaków jako kolumn długich wartości.  Ponadto należy unikać stosowania kolumn NVARCHAR, jeśli zastosowanie typu VARCHAR spełni wymagania danego zastosowania.

Zobacz także artykuły [Omówienie tabel](sql-data-warehouse-tables-overview.md), [Typy danych w tabelach](sql-data-warehouse-tables-data-types.md) i [CREATE TABLE](sql-data-warehouse-tables-overview.md).

## <a name="optimize-clustered-columnstore-tables"></a>Optymalizowanie tabel klastrowanego magazynu kolumn
Indeksy klastrowanego magazynu kolumn są jednym z najbardziej efektywnych sposobów przechowywania danych w puli SQL.  Domyślnie tabele w puli SQL są tworzone jako klastrowane ColumnStore.  Dla uzyskania najlepszej wydajności kwerend w odniesieniu do tabel magazynu kolumn ważne jest zapewnienie dobrej jakości segmentów.  Jeśli wiersze są zapisywane w tabelach magazynu kolumn przy dużym wykorzystaniu pamięci, może to spowodować obniżenie jakości segmentów w magazynie kolumn.  Jakość segmentu określa się na podstawie liczby wierszy w skompresowanej grupie wierszy.  Zobacz sekcję [Przyczyny niskiej jakości indeksu magazynu kolumn](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) w artykule [Indeksy tabel](sql-data-warehouse-tables-index.md), aby uzyskać instrukcje krok po kroku dotyczące sprawdzania i poprawiania jakości segmentu tabel klastrowanego magazynu kolumn.  Ponieważ segmenty magazynu kolumn wysokiej jakości są ważne, warto używać identyfikatorów użytkowników, które znajdują się w klasie średnich lub dużych zasobów do ładowania danych. Korzystanie z [niższych jednostek magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) oznacza, że chcesz przypisać większą klasę zasobów do użytkownika ładującego.

Ponieważ tabele magazynu kolumn zazwyczaj nie wypchują danych do skompresowanego segmentu magazynu kolumn, dopóki nie będzie więcej niż 1 miliona wierszy na tabelę, a każda tabela puli SQL zostanie podzielona na 60 tabel, zgodnie z zasadą tabele magazynu kolumn nie będą korzystać z kwerendy, chyba że tabela ma ponad 60 milionów wierszy.  Stosowanie indeksu magazynu kolumn może nie mieć sensu w przypadku tabel z mniej niż 60 milionami wierszy.  Jego użycie nie przyniesie też jednak niekorzystnych skutków.  Ponadto w przypadku partycjonowania danych warto wziąć pod uwagę, że każda partycja będzie musiała mieć milion wierszy, aby można było odnieść korzyść z zastosowania klastrowanego indeksu magazynu kolumn.  Jeśli tabela ma 100 partycji, będzie musiała mieć co najmniej 6 miliardów wierszy, aby skorzystanie z klastrowanego magazynu kolumn przyniosło korzyść (60 dystrybucji * 100 partycji * 1 milion wierszy).  Jeśli tabela w tym przykładzie nie ma 6 miliardów wierszy, zmniejsz liczbę partycji lub rozważ zastąpienie jej tabelą stosu.  Ponadto warto poeksperymentować, aby sprawdzić, czy lepszą wydajność można uzyskać, korzystając z tabeli stosu z indeksami pomocniczymi zamiast z tabeli magazynu kolumn.

Podczas wykonywania zapytania odnoszącego się do tabeli magazynu kolumn kwerendy będą uruchamiane szybciej, jeśli wybrane zostaną tylko niezbędne kolumny.  

Zobacz także artykuły [Indeksy tabel](sql-data-warehouse-tables-index.md), [Przewodnik po indeksach magazynu kolumn](https://msdn.microsoft.com/library/gg492088.aspx) i [Ponowne tworzenie indeksów magazynu kolumn](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="next-steps"></a>Następne kroki
Jeśli nie znajdziesz tego, czego szukasz w tym artykule, spróbuj użyć "Wyszukaj dokumenty" po lewej stronie tej strony, aby przeszukać wszystkie dokumenty usługi Azure Synapse Analytics.  [Forum usługi Azure SQL Data Warehouse](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) to miejsce, w które można zadawać pytania innym użytkownikom i grupie produktów magazynu danych SQL.  Firma Microsoft aktywnie monitoruje to forum, aby mieć pewność, że użytkownicy uzyskują odpowiedzi od innych użytkowników lub pracowników firmy Microsoft.  Jeśli wolisz zadać pytanie w witrynie Stack Overflow, możesz także skorzystać z [tego forum usługi Azure SQL Data Warehouse](https://stackoverflow.com/questions/tagged/azure-sqldw).

Zachęcamy do korzystania ze strony [opinii na temat usługi Azure SQL Data Warehouse](https://feedback.azure.com/forums/307516-sql-data-warehouse) i zgłaszania propozycji dotyczących funkcji.  Dodawanie konkretnych propozycji lub głosowanie na już zgłoszone sugestie bardzo pomaga nam określać priorytety funkcji.


