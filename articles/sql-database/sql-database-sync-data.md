---
title: Synchronizacja danych Azure SQL (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym omówieniu przedstawiono synchronizacji danych SQL Azure (wersja zapoznawcza)
services: sql-database
author: douglaslms
manager: craigg
ms.service: sql-database
ms.custom: data-sync
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: aa8f5e3b78a65c42840bbe831f5a4f2984a4a357
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650404"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync-preview"></a>Synchronizowanie danych w wielu w chmurze i lokalnych baz danych z opcją synchronizacji danych SQL (wersja zapoznawcza)

Synchronizacja danych SQL to usługa oparta na bazie danych SQL Azure, która umożliwia synchronizowanie danych, wybranych dwukierunkowo przez wiele baz danych i wystąpień programu SQL Server.

Synchronizacja danych opiera się wokół pojęcia grupy synchronizacji. Grupy synchronizacji jest grupą baz danych, które mają być synchronizowane.

Grupy synchronizacji ma następujące właściwości:

-   **Schematu synchronizacji** w tym artykule opisano, które dane są synchronizowane.

-   **Kierunek synchronizacji** może być dwukierunkowe lub mogą przepływać tylko w jednym kierunku. Oznacza to, może być kierunek synchronizacji *koncentratora do elementu członkowskiego* lub *elementu członkowskiego do Centrum*, lub obie.

-   **Interwał synchronizacji** jest częstotliwość synchronizacji.

-   **Zasady rozpoznawania konfliktu** poziomu zasad grupy, które mogą być *wins Centrum* lub *wins elementu członkowskiego*.

Synchronizacja danych używa topologii gwiazdy do synchronizacji danych. Należy określić jednej bazy danych w grupie co baza danych Centrum. Pozostała część baz danych to element członkowski bazy danych. Synchronizacja występuje tylko między Centrum i indywidualnych elementów członkowskich.
-   **Bazy danych Centrum** musi być bazy danych SQL Azure.
-   **Baz danych elementu członkowskiego** może być bazy danych SQL, lokalnych baz danych programu SQL Server lub wystąpień programu SQL Server na maszynach wirtualnych Azure.
-   **Bazy danych usługi synchronizacji** zawiera metadanych i dziennika dla synchronizacji danych. Bazy danych usługi synchronizacji musi być bazy danych SQL Azure znajduje się w tym samym regionie co baza danych Centrum. Bazy danych usługi synchronizacji jest utworzyć klienta i należące do klientów.

> [!NOTE]
> Jeśli używasz bazy danych na lokalnym, konieczne będzie [Konfigurowanie lokalnego agenta](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synchronizowanie danych między bazami danych](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>Kiedy należy używać synchronizacji danych

Synchronizacja danych jest przydatne w sytuacjach, gdy danych musi być aktualizowany przez wiele baz danych SQL Azure lub baz danych programu SQL Server. Poniżej przedstawiono główne zastosowania synchronizacji danych:

-   **Synchronizacja danych hybrydowego:** z opcją synchronizacji danych można przechowywać dane synchronizowane między lokalnych baz danych i baz danych SQL Azure na potrzeby aplikacji hybrydowych. Ta funkcja może odwołać się do klientów, którzy rozważa przenoszeniu do chmury, a chcesz umieścić niektórych aplikacji na platformie Azure.

-   **Aplikacje rozproszone:** w wielu przypadkach jest przydatne do oddzielania różnych obciążeń w różnych baz danych. Na przykład jeśli masz dużą produkcyjnej bazy danych, ale należy również do uruchamiania obciążeń raportowania lub analityka na tych danych, warto mieć drugi bazy danych dla tego dodatkowe obciążenie. Takie podejście minimalizuje wpływ na wydajność na obciążenie produkcji. Synchronizacji danych można użyć, aby zachować te dwie bazy danych synchronizacji.

-   **Globalny aplikacji rozproszonych:** wiele firm span kilka regionów i nawet kilka krajów. Aby zminimalizować opóźnienie sieci, jest najlepszym rozwiązaniem jest dane w regionie blisko Ciebie. Z opcją synchronizacji danych można pracować z baz danych w regionach na świecie zsynchronizowane.

Synchronizacja danych nie jest odpowiedni dla następujących scenariuszy:

-   Odzyskiwanie po awarii

-   Przeczytaj skali

-   ETL (OLTP do OLAP)

-   Migracja z lokalnego serwera SQL z bazą danych Azure SQL

## <a name="how-does-data-sync-work"></a>Jak działa synchronizacji danych? 

-   **Śledzenie zmian danych:** synchronizacji danych śledzi zmiany przy użyciu wstawiania, aktualizacji i usuwania wyzwalaczy. Zmiany są zapisywane w tabeli po stronie w bazie danych użytkownika.

-   **Synchronizowanie danych:** synchronizacji danych zaprojektowano w model gwiazdy. Koncentrator przeprowadza synchronizację z każdym elemencie członkowskim pojedynczo. Zmiany w Centrum są pobierane do elementu członkowskiego, a następnie zmiany z elementu członkowskiego są przekazywane do koncentratora.

-   **Rozwiązywanie konfliktów:** synchronizacji danych są dostępne dwie opcje do rozwiązywania konfliktów *wins Centrum* lub *wins elementu członkowskiego*.
    -   W przypadku wybrania *wins Centrum*, zmiany w Centrum zawsze zastąpienie zmian w elemencie członkowskim.
    -   W przypadku wybrania *wins elementu członkowskiego*, zmiany w zmian Zastąp elementów członkowskich w Centrum. Jeśli istnieje więcej niż jeden element członkowski, końcowa wartość zależy który element członkowski jest najpierw zsynchronizowane.

## <a name="sync-req-lim"></a> Wymagania i ograniczenia

### <a name="general-considerations"></a>Zagadnienia ogólne

#### <a name="eventual-consistency"></a>Spójność ostateczna
Ponieważ synchronizacji danych na podstawie wyzwalacza, nie gwarantuje spójności transakcyjnej. Microsoft gwarantuje wprowadzone po pewnym czasie wszystkie zmiany i synchronizacji danych nie powoduje utraty danych.

#### <a name="performance-impact"></a>Wpływ na wydajność
Używa synchronizacji danych wstawiania, aktualizowania i usuwania wyzwalaczy do śledzenia zmian. Tworzy tabele w bazie danych użytkownika, śledzenie zmian. Te działania śledzenia zmiany mają wpływ na obciążenie bazy danych. Ocenia warstwę usługi i uaktualnienia, jeśli to konieczne.

### <a name="general-requirements"></a>Wymagania ogólne

-   Każda tabela musi mieć klucz podstawowy. Nie zmieniaj wartości klucza podstawowego w dowolnym wierszu. Jeśli należy zmienić wartość klucza podstawowego, Usuń wiersz i utwórz ją ponownie przy użyciu nowej wartości klucza podstawowego. 

-   Musi być włączona izolacji migawki. Aby uzyskać więcej informacji, zobacz [izolację migawki w programie SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Ogólne ograniczenia

-   Tabela nie może mieć kolumnę tożsamości, która nie jest kluczem podstawowym.

-   Klucz podstawowy nie może mieć typ danych daty i godziny.

-   Nazwy obiektów (baz danych, tabel i kolumn) nie może zawierać znaków drukowalnych kropki (.), lewego nawiasu kwadratowego ([) lub prawo kwadratowa nawiasu (]).

-   Azure uwierzytelniania usługi Active Directory nie jest obsługiwane.

#### <a name="unsupported-data-types"></a>Nieobsługiwane typy danych

-   FileStream

-   UDT SQL/CLR

-   Kolekcji XMLSchemaCollection (XML obsługiwane)

-   Kursor, Timestamp, Hierarchyid

#### <a name="limitations-on-service-and-database-dimensions"></a>Ograniczenia dotyczące wymiarów usługi i bazy danych

| **Wymiary**                                                      | **Limit**              | **Obejście problemu**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maksymalna liczba grup synchronizacji wszystkie bazy danych może należeć do.       | 5                      |                             |
| Maksymalna liczba punktów końcowych w grupie jednej synchronizacji              | 30                     | Utwórz wiele grup synchronizacji |
| Maksymalna liczba punktów końcowych lokalnie w jednej synchronizacji grupy. | 5                      | Utwórz wiele grup synchronizacji |
| Nazwy bazy danych, tabel, schematów i kolumn                       | 50 znaków według nazwy |                             |
| Tabele w grupie synchronizacji                                          | 500                    | Utwórz wiele grup synchronizacji |
| Kolumn w tabeli w grupie synchronizacji                              | 1000                   |                             |
| Rozmiar wiersza danych w tabeli                                        | 24 Mb                  |                             |
| Interwał synchronizacji minimalna                                           | 5 minut              |                             |
|||

## <a name="faq-about-sql-data-sync"></a>Często zadawane pytania dotyczące synchronizacji danych SQL

### <a name="how-much-does-the-sql-data-sync-preview-service-cost"></a>Ile kosztuje usługa synchronizacji danych SQL (wersja zapoznawcza)

W wersji zapoznawczej jest bezpłatna dla samej usługi synchronizacji danych SQL (wersja zapoznawcza).  Jednak możesz nadal Naliczanie opłat za transfer danych do przenoszenia danych i wystąpienie bazy danych SQL. Aby uzyskać więcej informacji, zobacz [SQL Database — cennik](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Jakie regionów obsługi synchronizacji danych?

Synchronizacja danych SQL (wersja zapoznawcza) jest dostępna we wszystkich regionach chmury publicznej.

### <a name="is-a-sql-database-account-required"></a>Jest wymagane konto bazy danych SQL? 

Tak. Musisz mieć konto bazy danych SQL do obsługi bazy danych koncentratora.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Synchronizacja danych można używać do synchronizacji między lokalnymi baz danych serwera SQL tylko? 
Nie bezpośrednio. Można zsynchronizować między bazami danych programu SQL Server lokalne pośrednio, jednak utworzenie Centrum bazy danych na platformie Azure, a następnie dodając lokalnych baz danych do grupy synchronizacji.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Synchronizacja między bazami danych SQL, które należą do różnych subskrypcji można używać synchronizacji danych?
Tak. Można zsynchronizować między bazami danych SQL, które należą do grup zasobów należących do różnych subskrypcji.
-   Jeśli subskrypcje należą do tej samej dzierżawy, a użytkownik ma uprawnienia do wszystkich subskrypcji, grupy synchronizacji można skonfigurować w portalu Azure.
-   W przeciwnym razie należy użyć programu PowerShell do dodawania elementów członkowskich synchronizacji, które należą do różnych subskrypcji.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>Można używać synchronizacji danych do inicjatora danych z bazy danych produkcyjnej do pustej bazy danych i następnie były synchronizowane? 
Tak. Tworzenie schematu ręcznie w nowej bazy danych za pomocą skryptu go od oryginału. Po utworzeniu schemat, dodać tabele do grupy synchronizacji, skopiuj dane i zachować zsynchronizowane.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Synchronizacja danych SQL należy używać kopii zapasowej i przywracanie baz danych?

Nie zaleca się synchronizacja danych SQL (wersja zapoznawcza) umożliwia tworzenie kopii zapasowej danych. Nie można utworzyć kopię zapasową i przywrócić do określonego punktu w czasie, ponieważ synchronizacje synchronizacji danych SQL (wersja zapoznawcza) nie są numerów wersji. Ponadto synchronizacji danych SQL (wersja zapoznawcza) nie kopii zapasowej innych obiektów SQL, takich jak procedury składowane i nie szybko odpowiednikiem operacji przywracania.

Dla jednego zalecane techniki tworzenia kopii zapasowych, zobacz [kopiowania bazy danych Azure SQL](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Synchronizacja danych zsynchronizować zaszyfrowanych tabele i kolumny?

-   Jeśli bazy danych używa zawsze zaszyfrowane, można zsynchronizować tylko tabele i kolumny, które są *nie* zaszyfrowane. Nie można zsynchronizować zaszyfrowanych kolumn, ponieważ synchronizacja danych nie można odszyfrować danych.

-   Jeśli kolumna używa szyfrowania na poziomie kolumny (CLE), można zsynchronizować kolumnę, tak długo, jak rozmiar wiersza jest mniejszy niż maksymalny rozmiar 24 Mb. Synchronizacja danych traktuje kolumny szyfrowany przez klucz (CLE) jako normalne danych binarnych. Aby odszyfrować dane w innych elementach członkowskich synchronizacji, musisz mieć ten sam certyfikat.

### <a name="is-collation-supported-in-sql-data-sync"></a>Sortowanie jest obsługiwane w synchronizacji danych SQL?

Tak. Synchronizacja danych SQL obsługuje sortowanie w następujących scenariuszach:

-   Jeśli synchronizacja wybranego schematu tabele nie są już w bazach danych Centrum lub elementu członkowskiego, a następnie po wdrożeniu grupy synchronizacji, usługa automatycznie tworzy odpowiadających jej tabel i kolumn z ustawień sortowania wybranych w bazach danych puste miejsce docelowe.

-   Jeśli tabel, które można zsynchronizować już istnieje w bazach danych zarówno Centrum, jak i element członkowski, synchronizacji danych SQL musi mieć klucz podstawowy tego samego sortowania między bazami danych Centrum i element członkowski w celu pomyślnego wdrożenia grupy synchronizacji. Nie ma żadnych ograniczeń sortowania dla kolumn innych niż kolumny klucza podstawowego.

### <a name="is-federation-supported-in-sql-data-sync"></a>Federacyjnej jest obsługiwana w synchronizacji danych SQL?

Główna baza danych Federacji może służyć usługi synchronizacji danych SQL (wersja zapoznawcza) nie podlega żadnym ograniczeniom. Nie można dodać punktu końcowego federacyjnych bazy danych do wersji bieżącej synchronizacji danych SQL (wersja zapoznawcza).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi SQL Data Sync, zobacz:

-   [Set up Azure SQL Data Sync (Konfigurowanie usługi Azure SQL Data Sync)](sql-database-get-started-sql-data-sync.md)
-   [Best practices for Azure SQL Data Sync (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)](sql-database-best-practices-data-sync.md)
-   [Monitor Azure SQL Data Sync with Log Analytics (Monitorowanie usługi Azure SQL Data Sync za pomocą usługi Log Analytics)](sql-database-sync-monitor-oms.md)
-   [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](sql-database-troubleshoot-data-sync.md)

-   Pełne przykładowe skrypty programu PowerShell przedstawiające sposób konfigurowania usługi SQL Data Sync:
    -   [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Pobierz dokumentację interfejsu API REST usługi SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Aby uzyskać więcej informacji na temat usługi SQL Database, zobacz:

-   [Omówienie usługi SQL Database](sql-database-technical-overview.md)
-   [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
