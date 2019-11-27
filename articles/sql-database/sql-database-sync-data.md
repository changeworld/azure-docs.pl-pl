---
title: Synchronizacja danych
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
ms.date: 08/20/2019
ms.openlocfilehash: 1ee2efbb8aebfc2f1a94c89edef6166898946d8a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422525"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Synchronizowanie danych w wielu bazach danych w chmurze i lokalnych z usługą SQL Data Sync

SQL Data Sync to usługa oparta na usłudze Azure SQL Database, która umożliwia synchronizowanie danych, wybrana dwukierunkowo między wieloma bazami danych SQL i wystąpienia programu SQL Server.

> [!IMPORTANT]
> Usługa Azure SQL Data Sync nie obsługuje w tym momencie Azure SQL Database wystąpienia zarządzanego.

## <a name="when-to-use-data-sync"></a>Kiedy należy używać synchronizacji danych

Synchronizacja danych jest przydatna w przypadkach, gdy dane muszą być aktualizowane w kilku bazach danych Azure SQL lub SQL Server bazach danych. Poniżej przedstawiono przypadków głównie do celów synchronizacji danych:

- **Synchronizacja danych hybrydowych:** Dzięki funkcji synchronizacji danych można synchronizować dane między lokalnymi bazami danych i bazami danych Azure SQL, aby umożliwić aplikacjom hybrydowym. Ta funkcja może odwołać się do klientów, którzy rozważają przejście do chmury i chcesz umieścić niektóre z aplikacji na platformie Azure.
- **Aplikacje rozproszone:** W wielu przypadkach korzystne jest oddzielenie różnych obciążeń między różnymi bazami danych. Na przykład jeśli masz duży produkcyjnej bazy danych, ale trzeba będzie również uruchomić raportowania lub analizy obciążenia oparte na tych danych, warto drugi bazy danych to dodatkowe obciążenie. To podejście minimalizuje wpływu na obciążenia produkcyjne. Aby zachować te dwie bazy danych synchronizacji, można użyć synchronizacji danych.
- **Aplikacje rozproszone globalnie:** Wiele firm obejmuje kilka regionów, a nawet kilka krajów/regionów. Aby zminimalizować opóźnienie sieci, najlepiej jest mieć swoje dane w regionie bliską. Z opcją synchronizacji danych można pracować z bazami danych w regionach na całym świecie zsynchronizowane.

Synchronizacja danych nie jest preferowanym rozwiązaniem dla następujących scenariuszy:

| Scenariusz | Niektóre zalecane rozwiązania |
|----------|----------------------------|
| Odzyskiwanie po awarii | [Geograficznie nadmiarowe platformy Azure](sql-database-automated-backups.md) |
| Odczyt skali | [Używanie replik tylko do odczytu do równoważenia obciążenia obciążeń zapytań tylko do odczytu (wersja zapoznawcza)](sql-database-read-scale-out.md) |
| ETL (OLTP, aby OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) lub [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migracja z programu on-premises SQL Server do usługi Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Omówienie SQL Data Sync

Synchronizacja danych opiera się wokół koncepcji grupą synchronizacji. Grupa synchronizacji jest grupą baz danych, które mają być synchronizowane.

Synchronizacja danych używa topologii gwiazdy, aby synchronizować dane. Należy zdefiniować jedną z baz danych w grupy synchronizacji jako baza danych koncentratora. Pozostała część bazy danych to element członkowski bazy danych. Synchronizacja występuje tylko między Centrum i poszczególnych elementów członkowskich.

- **Baza danych centrum** musi być Azure SQL Database.
- **Bazy danych elementów członkowskich** mogą być bazami danych SQL, lokalnymi bazami danych SQL Server lub wystąpieniami SQL Server na maszynach wirtualnych platformy Azure.
- **Baza danych synchronizacji** zawiera metadane i Dziennik synchronizacji danych. Baza danych synchronizacji musi być Azure SQL Database umieszczona w tym samym regionie, w którym znajduje się baza danych centrów. Baza danych synchronizacji jest klient utworzył i należące do klientów.

> [!NOTE]
> W przypadku korzystania z lokalnej bazy danych jako bazy danych będącej członkiem należy [zainstalować i skonfigurować lokalnego agenta synchronizacji](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synchronizowanie danych między bazami danych](media/sql-database-sync-data/sync-data-overview.png)

Grupa synchronizacji ma następujące właściwości:

- **Schemat synchronizacji** opisuje, które dane są synchronizowane.
- **Kierunek synchronizacji** może być dwukierunkowy lub może przepływać tylko w jednym kierunku. Oznacza to, że kierunek synchronizacji może być *centrum do elementu członkowskiego*lub *składowej lub z*obu tych elementów.
- **Interwał synchronizacji** opisuje, jak często odbywa się synchronizacja.
- **Zasady rozwiązywania konfliktów** są zasadami na poziomie grupy, które mogą być *centrami WINS* lub *członkowskimi*.

## <a name="how-does-data-sync-work"></a>Jak działa synchronizacja danych

- **Śledzenie zmian danych:** Synchronizacja danych śledzi zmiany przy użyciu wyzwalaczy INSERT, Update i DELETE. Zmiany są zapisywane w tabeli w bazie danych użytkownika po stronie. Należy zauważyć, że BULK INSERT domyślnie nie uruchamia wyzwalaczy. Jeśli FIRE_TRIGGERS nie jest określony, żadne wyzwalacze wstawiania nie są wykonywane. Dodaj opcję FIRE_TRIGGERS, aby synchronizacja danych mogą śledzić te operacje wstawiania. 
- **Synchronizowanie danych:** Synchronizacja danych została zaprojektowana w modelu gwiazdy. Centrum przeprowadza synchronizację z każdym elementem członkowskim indywidualnie. Zmiany w Centrum są pobierane do elementu członkowskiego, a następnie zmiany w porównaniu z elementu członkowskiego są przekazywane do Centrum.
- **Rozwiązywanie konfliktów:** Synchronizacja danych oferuje dwie opcje rozwiązywania konfliktów, *centrum WINS* lub *członka usługi WINS*.
  - W przypadku wybrania *usługi Hub WINS*zmiany w centrum zawsze zastępują zmiany w elemencie członkowskim.
  - W przypadku wybrania *elementu członkowskiego usługi WINS*zmiany wprowadzone w elemencie członkowskim zastępują zmiany w centrum. Jeśli istnieje więcej niż jeden element członkowski, końcowa wartość jest zależna od która składowa jest najpierw zsynchronizowane.

## <a name="compare-data-sync-with-transactional-replication"></a>Porównywanie synchronizacji danych z replikacją transakcyjną

| | Synchronizacja danych | Replikacja transakcyjna |
|---|---|---|
| Zalety | -Aktywne-aktywne wsparcie<br/>-Dwukierunkowe między środowiskiem lokalnym i Azure SQL Database | -Małe opóźnienia<br/>-Spójności transakcyjnej<br/>-Ponowne użycie istniejącej topologii po migracji |
| Wady | -5 min lub więcej opóźnień<br/>-Brak spójności transakcyjnej<br/>— Wyższy wpływ na wydajność | -Nie można publikować z Azure SQL Database pojedynczej bazy danych lub bazy danych w puli<br/>— Wysoki koszt konserwacji |

## <a name="get-started-with-sql-data-sync"></a>Rozpoczynanie pracy z usługą SQL Data Sync

### <a name="set-up-data-sync-in-the-azure-portal"></a>Konfigurowanie synchronizacji danych w witrynie Azure portal

- [Set up Azure SQL Data Sync (Konfigurowanie usługi Azure SQL Data Sync)](sql-database-get-started-sql-data-sync.md)
- Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)

### <a name="set-up-data-sync-with-powershell"></a>Konfiguruj synchronizację danych przy użyciu programu PowerShell

- [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
- [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL Database i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Przeglądanie najlepszych rozwiązań w celu synchronizacji danych

- [Best practices for Azure SQL Data Sync (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Wystąpił problem

- [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Spójnością i wydajnością

### <a name="eventual-consistency"></a>Spójność ostateczna

Ponieważ synchronizacja danych jest oparta na wyzwalaczu, nie ma gwarancji spójności transakcyjnej. Firma Microsoft gwarantuje, że wszystkie zmiany są wprowadzane ostatecznie i synchronizacja danych nie powoduje utraty danych.

### <a name="performance-impact"></a>Wpływ na wydajność

Zastosowań synchronizacji danych Wstawianie, aktualizowanie i usuwanie wyzwalaczy do śledzenia zmian. Tworzy tabele w bazie danych użytkownika, śledzenie zmian. Te działania śledzenia zmiany mają wpływ na obciążenie bazy danych. Oceń warstwie usługi i uaktualnienia, jeśli to konieczne.

Zastrzeganie i cofanie zastrzegania podczas Tworzenie grupy synchronizacji, aktualizacji i usuwania może również wpływać na wydajność bazy danych.

## <a name="sync-req-lim"></a>Wymagania i ograniczenia

### <a name="general-requirements"></a>Wymagania ogólne

- Każda tabela musi mieć klucz podstawowy. Nie zmieniaj wartości klucza podstawowego w dowolnym wierszu. Jeśli trzeba zmienić wartość klucza podstawowego, Usuń wiersz i odtworzyć go przy użyciu nowych wartości klucza podstawowego.

> [!IMPORTANT]
> Zmiana wartości istniejącego klucza podstawowego spowoduje wystąpienie następujących nieprawidłowych zachowań:
> - Dane między centrami i elementami członkowskimi mogą zostać utracone nawet wtedy, gdy synchronizacja nie zgłasza żadnego problemu.
> - Synchronizacja może zakończyć się niepowodzeniem, ponieważ tabela śledzenia ma nieistniejący wiersz ze źródła ze względu na zmianę klucza podstawowego.

- Izolacji migawki musi być włączona. Aby uzyskać więcej informacji, zobacz [Izolacja migawki w programie SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Ogólne ograniczenia

- Tabela nie może mieć kolumny tożsamości, która nie jest kluczem podstawowym.
- Klucz podstawowy nie może mieć następujących typów danych: sql_variant, Binary, varbinary, Image, XML.
- Zachowaj ostrożność korzystając z następujących typów danych jako klucza podstawowego, ponieważ precyzja obsługiwane jest tylko do drugiego: godziny, daty i godziny, datetime2, datetimeoffset.
- Nazwy obiektów (baz danych, tabel i kolumn) nie mogą zawierać kropki do drukowalnego znaku (.), lewego nawiasu kwadratowego ([) lub prawego nawiasu kwadratowego (]).
- Uwierzytelnianie Azure Active Directory nie jest obsługiwane.
- Tabele o tej samej nazwie, ale innym schemacie (na przykład dbo. Customers i Sales. Customers) nie są obsługiwane.
- Kolumny z typami danych zdefiniowanymi przez użytkownika nie są obsługiwane

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

| **Wymiary**                                                  | **Limit**              | **Obejście**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maksymalna liczba grup synchronizacji dowolnej bazy danych może należeć do.       | 5                      |                             |
| Maksymalna liczba punktów końcowych w grupie jednej synchronizacji              | 30                     |                             |
| Maksymalna liczba lokalne punkty końcowe w grupie pojedynczej synchronizacji. | 5                      | Utwórz wiele grup synchronizacji |
| Nazwy bazy danych, tabel, schematu i kolumn                       | 50 znaków w nazwie |                             |
| Tabele w grupie synchronizacji                                          | 500                    | Utwórz wiele grup synchronizacji |
| Kolumny w tabeli w grupie synchronizacji                              | 1000                   |                             |
| Rozmiar wiersza danych w tabeli                                        | 24 Mb                  |                             |
| Interwał synchronizacji minimalne                                           | 5 minut              |                             |

> [!NOTE]
> Może istnieć maksymalnie 30 punktów końcowych w grupie synchronizacji pojedynczego Jeśli istnieje tylko jedna grupa synchronizacji. Jeśli istnieje więcej niż jednej grupy synchronizacji, całkowitej liczby punktów końcowych we wszystkich grupach synchronizacji nie może przekraczać 30. Jeśli bazy danych należy do wielu grup synchronizacji, jest ona traktowana jako wiele punktów końcowych, nie jeden.

## <a name="faq-about-sql-data-sync"></a>Często zadawane pytania dotyczące SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Jak dużo koszt usługi SQL Data Sync

Nie jest naliczana opłata za usługę SQL Data Sync. Jednak nadal są pobierane opłaty za transfer danych w przypadku przenoszenia i wychodzącego wystąpienia SQL Database. Aby uzyskać więcej informacji, zobacz [Cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Jakie regiony obsługują synchronizację danych

SQL Data Sync jest dostępna we wszystkich regionach.

### <a name="is-a-sql-database-account-required"></a>Czy wymagane jest konto SQL Database

Tak. Musi mieć konta usługi SQL Database do hostowania bazy danych Centrum.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Czy można używać synchronizacji danych do synchronizowania tylko SQL Server lokalnych baz danych

Nie bezpośrednio. Można zsynchronizować między bazami danych programu SQL Server w środowisku lokalnym pośrednio, jednak tworzenie bazy danych Centrum na platformie Azure, a następnie dodając lokalnych baz danych do grupy synchronizacji.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Czy można używać synchronizacji danych do synchronizacji między bazami danych SQL, które należą do różnych subskrypcji

Tak. Można przeprowadzić synchronizację między bazami danych SQL, które należą do grup zasobów należących do różnych subskrypcji.

- Jeśli subskrypcje należą do tej samej dzierżawy, a użytkownik ma uprawnienia do wszystkich subskrypcji, grupy synchronizacji można skonfigurować w witrynie Azure portal.
- W przeciwnym razie trzeba dodać elementów członkowskich synchronizacji, które należą do różnych subskrypcji przy użyciu programu PowerShell.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Czy można używać synchronizacji danych do synchronizacji między bazami danych SQL, które należą do różnych chmur (takich jak chmura publiczna platformy Azure i usługa Azure Chiny 21Vianet)

Tak. Można synchronizować między bazami danych SQL, które należą do różnych chmur, należy użyć programu PowerShell do dodawania elementów członkowskich synchronizacji, które należą do różnych subskrypcji.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Czy można używać synchronizacji danych do wypełniania danych z mojej produkcyjnej bazy danych do pustej bazy danych, a następnie synchronizować ją

Tak. Tworzenie schematu ręcznie w nowej bazy danych za pomocą skryptu go od oryginału. Po utworzeniu schematu, należy dodać tabele do grupy synchronizacji, aby skopiować dane i zsynchronizowane.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Czy należy używać SQL Data Sync do tworzenia kopii zapasowych i przywracania baz danych

Nie zaleca się używania SQL Data Sync, aby utworzyć kopię zapasową danych. Nie można utworzyć kopii zapasowej i przywrócić do określonego punktu w czasie, ponieważ synchronizacje SQL Data Sync nie są obsługiwane. Ponadto SQL Data Sync nie wykonuje kopii zapasowych innych obiektów SQL, takich jak procedury składowane, i nie wykonuje operacji przywracania szybko.

Aby poznać jedną z zalecanych technik tworzenia kopii zapasowych, zobacz [Kopiowanie bazy danych Azure SQL Database](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Czy zaszyfrowane tabele i kolumny synchronizacji synchronizacji danych

- Jeśli baza danych używa Always Encrypted, można synchronizować tylko tabele i kolumny, które *nie* są zaszyfrowane. Nie można zsynchronizować zaszyfrowanych kolumn, ponieważ synchronizacja danych nie można odszyfrować danych.
- Jeśli kolumna używa szyfrowania na poziomie kolumny (CLE), można zsynchronizować tę kolumnę, tak długo, jak rozmiar wiersza jest mniejszy niż maksymalny rozmiar 24 Mb. Synchronizacja danych traktuje kolumny szyfrowany przez klucz (CLE) jako normalne dane binarne. Aby odszyfrować dane na innych elementach członkowskich synchronizacji, musisz mieć ten sam certyfikat.

### <a name="is-collation-supported-in-sql-data-sync"></a>Sortowanie jest obsługiwane w SQL Data Sync

Tak. SQL Data Sync obsługuje sortowanie w następujących scenariuszach:

- Jeśli wybrane tabele schematu synchronizacji nie znajdują się już w centrum lub bazach danych, to podczas wdrażania grupy synchronizacji usługa automatycznie tworzy odpowiednie tabele i kolumny z ustawieniami sortowania wybranymi w pustej docelowej bazie danych.
- Jeśli tabele, które mają być synchronizowane z już istnieje w bazach danych zarówno Centrum, jak i element członkowski, SQL Data Sync musi mieć czy kolumny klucza podstawowego takiego samego sortowania baz Centrum i element członkowski danych mogła pomyślnie wdrożyć grupę synchronizacji. Istnieją ograniczenia sortowania dla kolumn innych niż kolumny klucza podstawowego.

### <a name="is-federation-supported-in-sql-data-sync"></a>Czy Federacja jest obsługiwana w SQL Data Sync

Główna baza danych Federacji może służyć w usłudze SQL Data Sync bez żadnych ograniczeń. Nie można dodać punktu końcowego bazy danych federacyjnych do bieżącej wersji SQL Data Sync.

## <a name="next-steps"></a>Następne kroki

### <a name="update-the-schema-of-a-synced-database"></a>Zaktualizuj schemat zsynchronizowanych bazy danych

Czy masz do zaktualizowania schematu bazy danych w grupie synchronizacji? Zmiany schematu nie są automatycznie replikowane. Niektóre rozwiązania na ten temat można znaleźć w następujących artykułach:

- [Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync](sql-database-update-sync-schema.md)
- [Aktualizowanie schematu synchronizacji w istniejącej grupie synchronizacji przy użyciu programu PowerShell](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorowanie i rozwiązywanie problemów

Czy SQL Data Sync działa zgodnie z oczekiwaniami? Aby monitorować aktywność i rozwiązywania problemów, zobacz następujące artykuły:

- [Monitorowanie SQL Data Sync platformy Azure przy użyciu dzienników Azure Monitor](sql-database-sync-monitor-oms.md)
- [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Dowiedz się więcej o usłudze Azure SQL Database

Aby uzyskać więcej informacji o usłudze SQL Database zobacz następujące artykuły:

- [Omówienie usługi SQL Database](sql-database-technical-overview.md)
- [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
