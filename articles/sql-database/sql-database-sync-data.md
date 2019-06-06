---
title: Usługa Azure SQL Data Sync | Dokumentacja firmy Microsoft
description: W tym omówieniu przedstawiono usługi Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: cfa94fc1c75bcd1eaa9a076cfe63369f60ce5f1c
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693085"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Synchronizowanie danych w wielu bazach danych w chmurze i lokalnych z usługą SQL Data Sync

SQL Data Sync to usługa oparta na usłudze Azure SQL Database, która umożliwia synchronizowanie danych, wybrana dwukierunkowo między wieloma bazami danych SQL i wystąpienia programu SQL Server.

> [!IMPORTANT]
> Usługa Azure SQL Data Sync **nie** obsługuje obecnie wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="when-to-use-data-sync"></a>Kiedy należy używać synchronizacji danych

Synchronizacja danych jest przydatne w sytuacjach, w którym dane muszą być przechowywane aktualne między kilka baz danych Azure SQL lub bazy danych programu SQL Server. Poniżej przedstawiono przypadków głównie do celów synchronizacji danych:

- **Synchronizacja danych hybrydowych:** Z opcją synchronizacji danych możesz przechowywać dane synchronizowane między lokalnymi bazami danych i baz danych Azure SQL, aby umożliwić aplikacji hybrydowych. Ta funkcja może odwołać się do klientów, którzy rozważają przejście do chmury i chcesz umieścić niektóre z aplikacji na platformie Azure.
- **Aplikacje rozproszone:** W wielu przypadkach jest korzystne różnych obciążeń w różnych bazach danych. Na przykład jeśli masz duży produkcyjnej bazy danych, ale trzeba będzie również uruchomić raportowania lub analizy obciążenia oparte na tych danych, warto drugi bazy danych to dodatkowe obciążenie. To podejście minimalizuje wpływu na obciążenia produkcyjne. Aby zachować te dwie bazy danych synchronizacji, można użyć synchronizacji danych.
- **Globalnie dystrybuowane aplikacje:** Wiele firm span kilku regionach, a nawet kilka krajów/regionów. Aby zminimalizować opóźnienie sieci, najlepiej jest mieć swoje dane w regionie bliską. Z opcją synchronizacji danych można pracować z bazami danych w regionach na całym świecie zsynchronizowane.

Synchronizacja danych nie jest preferowanym rozwiązaniem w następujących scenariuszach:

| Scenariusz | Niektóre zalecane rozwiązania |
|----------|----------------------------|
| Odzyskiwanie po awarii | [Geograficznie nadmiarowych kopii zapasowych Azure](sql-database-automated-backups.md) |
| Odczyt skali | [Ładowanie równoważenie obciążeń związanych z zapytaniami tylko do odczytu (wersja zapoznawcza) przy użyciu repliki tylko do odczytu](sql-database-read-scale-out.md) |
| ETL (OLTP, aby OLAP) | [Usługi Azure Data Factory](https://azure.microsoft.com/services/data-factory/) lub [usług SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migracja z programu on-premises SQL Server do usługi Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Omówienie SQL Data Sync

Synchronizacja danych opiera się wokół koncepcji grupą synchronizacji. Grupa synchronizacji jest grupą baz danych, które mają być synchronizowane.

Synchronizacja danych używa topologii gwiazdy, aby synchronizować dane. Należy zdefiniować jedną z baz danych w grupy synchronizacji jako baza danych koncentratora. Pozostała część bazy danych to element członkowski bazy danych. Synchronizacja występuje tylko między Centrum i poszczególnych elementów członkowskich.

- **Baza danych koncentratora** musi być bazą danych Azure SQL Database.
- **Bazy danych elementu członkowskiego** może być baz danych SQL, bazy danych programu SQL Server w środowisku lokalnym lub wystąpienia programu SQL Server na maszynach wirtualnych platformy Azure.
- **Baza danych synchronizacji** zawiera metadane i dziennika w celu synchronizacji danych. Baza danych synchronizacji ma być bazą danych Azure SQL Database znajduje się w tym samym regionie, co baza danych koncentratora. Baza danych synchronizacji jest klient utworzył i należące do klientów.

> [!NOTE]
> Jeśli używasz w lokalnej bazy danych jako element członkowski bazy danych, musisz [instalowania i konfigurowania agenta synchronizacji lokalnego](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synchronizowanie danych między bazami danych](media/sql-database-sync-data/sync-data-overview.png)

Grupa synchronizacji ma następujące właściwości:

- **Schematu synchronizacji** w tym artykule opisano, które dane są synchronizowane.
- **Kierunek synchronizacji** można dwukierunkową, lub może przepływać tylko w jednym kierunku. Oznacza to, że kierunek synchronizacji może być *koncentratora do elementu członkowskiego*, lub *członka do Centrum*, lub obu.
- **Interwał synchronizacji** opisano, jak często występuje synchronizacji.
- **Zasady rozwiązywania konfliktów** poziomu zasad grupy, który może być *wins Centrum* lub *wins elementu członkowskiego*.

## <a name="how-does-data-sync-work"></a>Jak działa synchronizacja danych

- **Śledzenie zmiany danych:** Synchronizacja danych śledzenia zmian za pomocą insert, update i delete wyzwalaczy. Zmiany są zapisywane w tabeli w bazie danych użytkownika po stronie. Należy pamiętać, że BULK INSERT nie zostać wywołane wyzwalaczy domyślnie. Jeśli nie określono FIRE_TRIGGERS, wyzwalacze wstawiania nie wykonać. Dodaj opcję FIRE_TRIGGERS, aby synchronizacja danych mogą śledzić te operacje wstawiania. 
- **Synchronizowanie danych:** Synchronizacja danych została zaprojektowana w modelu gwiazdy. Centrum przeprowadza synchronizację z każdym elementem członkowskim indywidualnie. Zmiany w Centrum są pobierane do elementu członkowskiego, a następnie zmiany w porównaniu z elementu członkowskiego są przekazywane do Centrum.
- **Rozwiązywanie konfliktów:** Synchronizacja danych udostępnia dwie metody rozwiązywania konfliktów *wins Centrum* lub *wins elementu członkowskiego*.
  - Jeśli wybierzesz *wins Centrum*, zmiany w piaście zawsze mają pierwszeństwo przed zmiany w elemencie członkowskim.
  - Jeśli wybierzesz *wins elementu członkowskiego*, zmiany w zmian Zastąp elementów członkowskich w Centrum. Jeśli istnieje więcej niż jeden element członkowski, końcowa wartość jest zależna od która składowa jest najpierw zsynchronizowane.

## <a name="compare-data-sync-with-transactional-replication"></a>Porównaj Data Sync za pomocą replikacji transakcyjnej

| | Synchronizacja danych | Replikacja transakcyjna |
|---|---|---|
| Zalety | — Obsługa aktywny aktywny<br/>Dwukierunkowej między systemem lokalnym i usługi Azure SQL Database | — Mniejsze opóźnienia<br/>— Poziom spójności transakcyjnej<br/>-Użyj ponownie istniejącą topologię po migracji |
| Wady | -5 minut lub więcej opóźnienia<br/>-Brak spójności transakcyjnej<br/>-Wyższe negatywny wpływ na wydajność | -Nie można opublikować z pojedynczej bazy danych Azure SQL Database lub baza danych w puli<br/>— Koszt eksploatacji wysoka |
| | | |

## <a name="get-started-with-sql-data-sync"></a>Rozpoczynanie pracy z usługą SQL Data Sync

### <a name="set-up-data-sync-in-the-azure-portal"></a>Konfigurowanie synchronizacji danych w witrynie Azure portal

- [Set up Azure SQL Data Sync (Konfigurowanie usługi Azure SQL Data Sync)](sql-database-get-started-sql-data-sync.md)
- Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)

### <a name="set-up-data-sync-with-powershell"></a>Konfiguruj synchronizację danych przy użyciu programu PowerShell

- [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
- [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Przeglądanie najlepszych rozwiązań w celu synchronizacji danych

- [Best practices for Azure SQL Data Sync (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Coś pójdzie nie tak

- [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Spójnością i wydajnością

#### <a name="eventual-consistency"></a>Spójność ostateczna

Ponieważ synchronizacja danych na podstawie wyzwalaczy, nie gwarantuje spójności transakcyjnej. Firma Microsoft gwarantuje więc, że wszystkie zmiany zostały wprowadzone po pewnym czasie i synchronizacji danych nie powoduje utraty danych.

#### <a name="performance-impact"></a>Wpływ na wydajność

Zastosowań synchronizacji danych Wstawianie, aktualizowanie i usuwanie wyzwalaczy do śledzenia zmian. Tworzy tabele w bazie danych użytkownika, śledzenie zmian. Te działania śledzenia zmiany mają wpływ na obciążenie bazy danych. Oceń warstwie usługi i uaktualnienia, jeśli to konieczne.

Zastrzeganie i cofanie zastrzegania podczas Tworzenie grupy synchronizacji, aktualizacji i usuwania może również wpływać na wydajność bazy danych. 

## <a name="sync-req-lim"></a> Wymagania i ograniczenia

### <a name="general-requirements"></a>Wymagania ogólne

- Każda tabela musi mieć klucz podstawowy. Nie zmieniaj wartości klucza podstawowego w dowolnym wierszu. Jeśli trzeba zmienić wartość klucza podstawowego, Usuń wiersz i odtworzyć go przy użyciu nowych wartości klucza podstawowego. 
- Wymaga włączenia izolacji migawki. Aby uzyskać więcej informacji, zobacz [izolację migawki w programie SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Ogólne ograniczenia

- Tabela nie może mieć kolumnę tożsamości, która nie jest kluczem podstawowym.
- Klucz podstawowy nie może mieć następujące typy danych: sql_variant, binary, varbinary, image, xml. 
- Zachowaj ostrożność korzystając z następujących typów danych jako klucza podstawowego, ponieważ precyzja obsługiwane jest tylko do drugiego: godziny, daty i godziny, datetime2, datetimeoffset.
- Nazwy obiektów (baz danych, tabel i kolumn) nie może zawierać znaków drukowalnych kropki (.), lewego nawiasu kwadratowego ([) lub prawo kwadratowa nawiasu (]).
- Uwierzytelnianie usługi Azure Active Directory nie jest obsługiwana.
- Tabele z tej samej nazwie, ale innego schematu (na przykład dbo.customers i sales.customers) nie są obsługiwane.
- Kolumn zawierających typy danych zdefiniowane przez użytkownika nie są obsługiwane.

#### <a name="unsupported-data-types"></a>Nieobsługiwane typy danych

- FileStream
- UDT SQL/CLR
- Użyciu klasy XMLSchemaCollection (XML obsługiwane)
- Kursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Typy kolumn nieobsługiwany

Synchronizacja danych nie można zsynchronizować tylko do odczytu lub generowanych przez system kolumn. Na przykład:

- Kolumny obliczane.
- Wygenerowana przez system kolumny tabel danych czasowych.

#### <a name="limitations-on-service-and-database-dimensions"></a>Ograniczenia dotyczące usługi i bazy danych

| **Wymiary**                                                      | **Limit**              | **Obejście problemu**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maksymalna liczba grup synchronizacji dowolnej bazy danych może należeć do.       | 5                      |                             |
| Maksymalna liczba punktów końcowych w grupie jednej synchronizacji              | 30                     |                             |
| Maksymalna liczba lokalne punkty końcowe w grupie pojedynczej synchronizacji. | 5                      | Utwórz wiele grup synchronizacji |
| Nazwy bazy danych, tabel, schematu i kolumn                       | 50 znaków w nazwie |                             |
| Tabele w grupie synchronizacji                                          | 500                    | Utwórz wiele grup synchronizacji |
| Kolumny w tabeli w grupie synchronizacji                              | 1000                   |                             |
| Rozmiar wiersza danych w tabeli                                        | 24 Mb                  |                             |
| Interwał synchronizacji minimalne                                           | 5 minut              |                             |
|||
> [!NOTE]
> Może istnieć maksymalnie 30 punktów końcowych w grupie synchronizacji pojedynczego Jeśli istnieje tylko jedna grupa synchronizacji. Jeśli istnieje więcej niż jednej grupy synchronizacji, całkowitej liczby punktów końcowych we wszystkich grupach synchronizacji nie może przekraczać 30. Jeśli bazy danych należy do wielu grup synchronizacji, jest ona traktowana jako wiele punktów końcowych, nie jeden.

## <a name="faq-about-sql-data-sync"></a>Często zadawane pytania dotyczące SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Ile kosztuje usługa SQL Data Sync

Nie ma opłat za samą usługę SQL Data Sync.  Jednakże możesz nadal są naliczane opłaty za transfer danych w przypadku przenoszenia danych i wystąpienie bazy danych SQL. Aby uzyskać więcej informacji, zobacz [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Jakie regiony obsługi synchronizacji danych

SQL Data Sync jest dostępna we wszystkich regionach.

### <a name="is-a-sql-database-account-required"></a>Jest wymagane jest konto bazy danych SQL

Tak. Musi mieć konta usługi SQL Database do hostowania bazy danych Centrum.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Synchronizowanie baz danych programu SQL Server w środowisku lokalnym tylko można używać synchronizacji danych

Nie bezpośrednio. Można zsynchronizować między bazami danych programu SQL Server w środowisku lokalnym pośrednio, jednak tworzenie bazy danych Centrum na platformie Azure, a następnie dodając lokalnych baz danych do grupy synchronizacji.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Synchronizacja między bazami danych SQL, które należą do różnych subskrypcji można używać synchronizacji danych

Tak. Można przeprowadzić synchronizację między bazami danych SQL, które należą do grup zasobów należących do różnych subskrypcji.

- Jeśli subskrypcje należą do tej samej dzierżawy, a użytkownik ma uprawnienia do wszystkich subskrypcji, grupy synchronizacji można skonfigurować w witrynie Azure portal.
- W przeciwnym razie trzeba dodać elementów członkowskich synchronizacji, które należą do różnych subskrypcji przy użyciu programu PowerShell.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china"></a>Synchronizacja między bazami danych SQL, które należą do innej chmury (na przykład chmurze publicznej platformy Azure i Azure — Chiny) można używać synchronizacji danych

Tak. Można synchronizować między bazami danych SQL, które należą do różnych chmur, należy użyć programu PowerShell do dodawania elementów członkowskich synchronizacji, które należą do różnych subskrypcji.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Czy mogę używać synchronizacji danych do inicjatora danych z mojego produkcyjnej bazy danych do pustej bazy danych i następnie synchronizować je

Tak. Tworzenie schematu ręcznie w nowej bazy danych za pomocą skryptu go od oryginału. Po utworzeniu schematu, należy dodać tabele do grupy synchronizacji, aby skopiować dane i zsynchronizowane.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>SQL Data Sync w celu należy użyć kopii zapasowej i przywracanie baz danych

Nie jest zalecane na potrzeby tworzenia kopii zapasowych danych SQL Data Sync. Nie można utworzyć kopię zapasową i przywracanie do określonego punktu w czasie, ponieważ nie są wersjonowane synchronizacje SQL Data Sync. Ponadto SQL Data Sync nie kopii zapasowej innych obiektów programu SQL, takich jak procedury składowane i do szybko wielokrotność operacji przywracania.

Dla jednej zalecane techniki tworzenia kopii zapasowych, zobacz [skopiować bazę danych Azure SQL database](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Synchronizacja danych zsynchronizować zaszyfrowanych tabele i kolumny

- Jeśli bazy danych korzysta z funkcji zawsze zaszyfrowane, można zsynchronizować tylko tabele i kolumny, które są *nie* zaszyfrowane. Nie można zsynchronizować zaszyfrowanych kolumn, ponieważ synchronizacja danych nie można odszyfrować danych.
- Jeśli kolumna używa szyfrowania na poziomie kolumny (CLE), można zsynchronizować tę kolumnę, tak długo, jak rozmiar wiersza jest mniejszy niż maksymalny rozmiar 24 Mb. Synchronizacja danych traktuje kolumny szyfrowany przez klucz (CLE) jako normalne dane binarne. Aby odszyfrować dane na innych elementach członkowskich synchronizacji, musisz mieć ten sam certyfikat.

### <a name="is-collation-supported-in-sql-data-sync"></a>Sortowanie jest obsługiwane w SQL Data Sync

Tak. SQL Data Sync obsługuje sortowanie w następujących scenariuszach:

- Jeśli synchronizacja wybranego schematu tabele nie są już w bazach danych koncentratora lub elementu członkowskiego, a następnie po wdrożeniu grupy synchronizacji, usługa automatycznie tworzy odpowiednie tabele i kolumny z ustawienia sortowania wybrano w bazach danych puste miejsce docelowe.
- Jeśli tabele, które mają być synchronizowane z już istnieje w bazach danych zarówno Centrum, jak i element członkowski, SQL Data Sync musi mieć czy kolumny klucza podstawowego takiego samego sortowania baz Centrum i element członkowski danych mogła pomyślnie wdrożyć grupę synchronizacji. Istnieją ograniczenia sortowania dla kolumn innych niż kolumny klucza podstawowego.

### <a name="is-federation-supported-in-sql-data-sync"></a>Czy federacyjny jest obsługiwane w SQL Data Sync

Główna baza danych Federacji może służyć w usłudze SQL Data Sync bez żadnych ograniczeń. Nie można dodać punktu końcowego bazach danych w bieżącej wersji programu SQL Data Sync.

## <a name="next-steps"></a>Kolejne kroki

### <a name="update-the-schema-of-a-synced-database"></a>Zaktualizuj schemat zsynchronizowanych bazy danych

Czy masz do zaktualizowania schematu bazy danych w grupie synchronizacji? Zmiany schematu nie są automatycznie replikowane. Niektóre rozwiązania na ten temat można znaleźć w następujących artykułach:

- [Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync](sql-database-update-sync-schema.md)
- [Aktualizowanie schematu synchronizacji w istniejącej grupie synchronizacji przy użyciu programu PowerShell](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorowanie i rozwiązywanie problemów

SQL Data Sync działa zgodnie z oczekiwaniami? Aby monitorować aktywność i rozwiązywania problemów, zobacz następujące artykuły:

- [Monitor Azure SQL Data Sync za pomocą dzienników usługi Azure Monitor](sql-database-sync-monitor-oms.md)
- [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Dowiedz się więcej o usłudze Azure SQL Database

Aby uzyskać więcej informacji o usłudze SQL Database zobacz następujące artykuły:

- [Omówienie usługi SQL Database](sql-database-technical-overview.md)
- [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
