---
title: Migracja bazy danych programu SQL Server do usługi Azure SQL Database | Microsoft Docs
description: Dowiedz się więcej na temat migracji bazy danych programu SQL Server do usługi Azure SQL Database w chmurze.
keywords: migracja bazy danych,migracja bazy danych programu sql server,narzędzia migracji bazy danych,migracja bazy danych,migracja bazy danych sql
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: carlrab
ms.openlocfilehash: 73f7c9ae9a3badd9d9dc3628145132238c8670d0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645926"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Migracja bazy danych programu SQL Server z bazą danych SQL Azure

W tym artykule opisano podstawowe metody migracji programu SQL Server 2005 lub nowszego na bazę danych do jednej lub puli bazy danych w bazie danych SQL Azure. Aby uzyskać informacje na temat migracji do wystąpienia zarządzane, zobacz [Migrowanie do wystąpienia programu SQL Server do usługi Azure bazy danych zarządzanych wystąpienia SQL (wersja zapoznawcza)](sql-database-managed-instance-migrate.md). 

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrowanie do pojedynczej bazy danych lub bazę danych w puli
Istnieją dwie podstawowe metody migracji programu SQL Server 2005 lub nowszego na bazę danych do jednej lub puli bazy danych w bazie danych SQL Azure. Pierwsza metoda jest prostsza, ale wymaga pewnego, prawdopodobnie znaczącego, przestoju podczas migracji. Druga metoda jest bardziej skomplikowana, ale znacznie eliminuje przestój podczas migracji.

W obu przypadkach należy się upewnić, że baza danych jest zgodny z bazy danych SQL Azure przy użyciu [Asystenta migracji danych (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). Zbliża się do bazy danych SQL V12 [funkcji parzystości](sql-database-features.md) z programem SQL Server, niż problemy związane z poziomu serwera i bazy danych między operacjami. Bazy danych i aplikacje oparte na [częściowo obsługiwanych lub nieobsługiwanych funkcjach](sql-database-transact-sql-information.md) muszą zostać w pewnym stopniu [przeprojektowane, aby usunąć niezgodności](sql-database-cloud-migrate.md#resolving-database-migration-compatibility-issues) i umożliwić migrację bazy danych programu SQL Server.

> [!NOTE]
> Aby wykonać migrację bazy danych innej niż baza danych programu SQL Server, w tym bazy danych Microsoft Access, Sybase, MySQL Oracle i DB2, do usługi Azure SQL Database, zobacz temat [Asystent migracji programu SQL Server](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).
> 

### <a name="method-1-migration-with-downtime-during-the-migration"></a>Metoda 1. Migracja z przestojem podczas migracji

 Ta metoda umożliwia migrację jedną lub puli bazy danych, jeśli można pozwolić sobie przestój lub nowszym migracji są przeprowadzania migracji testu produkcyjnej bazy danych. Samouczek, zobacz [Migrowanie bazy danych programu SQL Server](sql-database-migrate-your-sql-server-database.md).

Poniższa lista zawiera ogólny przepływ pracy migracji bazy danych programu SQL Server jedną lub puli bazy danych przy użyciu tej metody. W przypadku migracji do wystąpienia zarządzane zobacz [migracji do wystąpienia zarządzane](sql-database-managed-instance-migrate.md).

  ![Diagram migracji VSSSDT](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Oceny](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) bazy danych dla zgodności przy użyciu najnowszej wersji [Asystenta migracji danych (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Przygotowanie wszelkich niezbędnych poprawek jako skryptów języka Transact-SQL.
3. Wykonaj kopię spójna transakcyjnie źródłowej bazy danych podlegających migracji lub zatrzymanie nowych transakcji z występujących w źródłowej bazy danych, gdy jest wykonywana migracja. Metody do wykonania tej opcji ostatnie obejmują wyłączanie łączności klienta i tworzenie [migawki bazy danych](https://msdn.microsoft.com/library/ms175876.aspx). Po zakończeniu migracji można użyć replikacji transakcyjnej, aby zaktualizować migrowanych baz danych ze zmianami, które występują po odcięcia punktu migracji. Zobacz [migracji za pomocą migracji transakcyjne](sql-database-cloud-migrate.md#method-2-use-transactional-replication).  
4. Wdrożenie skryptów języka Transact-SQL w celu zastosowania poprawek do kopii bazy danych.
5. [Migrowanie](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) kopii bazy danych do nowej bazy danych SQL Azure, przy użyciu Asystenta migracji danych.

> [!NOTE]
> Zamiast używania DMA, można również użyć pliku pliku BACPAC. Zobacz [Importowanie pliku pliku BACPAC do nowej bazy danych SQL Azure](sql-database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Optymalizowanie wydajności transferu danych podczas migracji 

Poniższa lista zawiera zalecenia pozwalające uzyskać najlepszą wydajność podczas procesu importowania.

* Aby zmaksymalizować wydajność transferu, wybierz najwyższy poziom usługi i najwyższą warstwę wydajności, na które pozwala Twój budżet. Po zakończeniu migracji będzie można dokonać skalowania w dół, aby nie ponosić nadmiernych kosztów. 
* Minimalizowanie odległości między pliku pliku BACPAC i docelowym centrum danych.
* Wyłącz automatyczne statystyki na czas migracji.
* Partycjonuj tabele i indeksy.
* Usuń poindeksowane widoki i utwórz je ponownie po zakończeniu.
* Przenieś rzadko wyszukiwane dane historyczne do innej bazy danych i zmigruj te dane historyczne do oddzielnej bazy danych SQL platformy Azure. Następnie będzie można wykonywać zapytania o te dane historyczne za pomocą [zapytań elastycznych](sql-database-elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Optymalizacja wydajności po zakończeniu migracji

[Zaktualizuj statystyki](https://msdn.microsoft.com/library/ms187348.aspx) poprzez pełne skanowanie po zakończeniu migracji.

### <a name="method-2-use-transactional-replication"></a>Metoda 2. Użycie replikacji transakcyjnej

Gdy nie możesz sobie pozwolić na usunięcie bazy danych programu SQL Server ze środowiska produkcyjnego na czas migracji, jako rozwiązania do migracji możesz wykorzystać replikację transakcyjną programu SQL Server. Aby użyć tej metody, źródłowa baza danych musi spełniać [wymagania dotyczące replikacji transakcyjnej](https://msdn.microsoft.com/library/mt589530.aspx) i być zgodna z usługą Azure SQL Database. Informacje o replikacji SQL z funkcją AlwaysOn, zobacz [skonfigurować replikację dla zawsze włączonych grup dostępności (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Aby użyć tego rozwiązania, usługę Azure SQL Database konfiguruje się jako subskrybenta wystąpienia programu SQL Server do migracji. Dystrybutor replikacji transakcyjnej synchronizuje dane z bazy danych do synchronizacji (wydawcy), podczas gdy cały czas realizowane są nowe transakcje. 

Przy replikacji transakcyjnej wszystkie zmiany wprowadzane do danych lub schematu pojawiają się w usłudze Azure SQL Database. Gdy synchronizacja zostanie zakończona i wszystko będzie gotowe do migracji, zmień parametry połączenia swoich aplikacji w taki sposób, aby wskazać im usługę Azure SQL Database. Gdy w wyniku replikacji transakcyjnej wszystkie zmiany pozostałe w źródłowej bazie danych zostaną pobrane, a wszystkie aplikacje będą wskazywać bazę danych platformy Azure, można odinstalować replikację transakcyjną. Usługa Azure SQL Database stanie się Twoim systemem produkcyjnym.

 ![Diagram SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> Replikacji transakcyjnej możesz także używać do migrowania podzestawu źródłowej bazy danych. Publikacja replikowana do usługi Azure SQL Database może być ograniczona do podzbioru tabel w replikowanej bazie danych. Dla każdej replikowanej tabeli dane można ograniczyć do podzestawu wierszy i/lub podzestawu kolumn.
>

### <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Przepływ pracy migracji do usługi SQL Database za pomocą replikacji transakcyjnej

> [!IMPORTANT]
> Używaj najnowszej wersji programu SQL Server Management Studio, aby zachować synchronizację z aktualizacjami platformy Microsoft Azure i usługi SQL Database. Starsze wersje programu SQL Server Management Studio nie mogą skonfigurować usługi SQL Database jako subskrybenta. [Zaktualizuj program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 

1. Konfigurowanie dystrybucji
   -  [Przy użyciu programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [Przy użyciu języka Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. Tworzenie publikacji
   -  [Przy użyciu programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [Przy użyciu języka Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Tworzenie subskrypcji
   -  [Przy użyciu programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [Przy użyciu języka Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

Niektóre wskazówki i różnice dotyczące migracji do usługi SQL Database

- Użycie dystrybutora lokalnego: 
   - To powoduje negatywny wpływ na wydajność serwera. 
   - Jeśli negatywny wpływ na wydajność jest nieakceptowalny, można użyć innego serwera, ale komplikuje to zarządzanie i administrowanie.
- Wybierając folder migawki, upewnij się, że wybrany folder jest wystarczająco duży, aby pomieścić kopię BCP każdej tabeli, którą chcesz zreplikować. 
- Tworzenie migawki powoduje zablokowanie skojarzonych tabel do czasu zakończenia operacji, dlatego tworzenie migawek należy odpowiednio zaplanować. 
- Usługa Azure SQL Database obsługuje wyłącznie subskrypcje wypychane. Subskrybentów można dodawać wyłącznie ze źródłowej bazy danych.

### <a name="resolving-database-migration-compatibility-issues"></a>Rozwiązywanie problemów ze zgodnością migracji bazy danych
Może wystąpić wiele różnych problemów ze zgodnością, w zależności od wersji programu SQL Server w źródłowej bazie danych oraz złożoności migrowanej bazy danych. Starsze wersje programu SQL Server mają więcej problemów ze zgodnością. Oprócz ukierunkowanego wyszukiwania w Internecie za pomocą preferowanej wyszukiwarki użyj następujących zasobów:

* [Funkcje bazy danych programu SQL Server nieobsługiwane przez usługę Azure SQL Database](sql-database-transact-sql-information.md)
* [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Oprócz wyszukiwania w Internecie i użycia wymienionych zasobów, możesz skorzystać z [forów społeczności programu SQL Server w sieci MSDN](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) lub witryny [StackOverflow](http://stackoverflow.com/).

> [!IMPORTANT]
> Zarządzane wystąpienia bazy danych SQL umożliwia migrację istniejącego wystąpienia programu SQL Server i jego baz danych z minimalnym do Brak problemów ze zgodnością. Zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md).


## <a name="next-steps"></a>Kolejne kroki
* Użyj skryptu z blogu SQL EMEA Engineers, aby [monitorować użycie bazy danych tempdb podczas migracji](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
* Użyj skryptu z blogu SQL EMEA Engineers, aby [monitorować obszar rejestrowania transakcji Twojej bazy danych w czasie trwania migracji](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0).
* Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
* Aby uzyskać informacje na temat pracy z czasem UTC po migracji, zobacz [Modifying the default time zone for your local time zone](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/) (Modyfikowanie domyślnej strefy czasowej dla Twojej lokalnej strefy czasowej).
* Aby uzyskać informacje na temat zmieniania domyślnego języka bazy danych po migracji, zobacz [How to change the default language of Azure SQL Database](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/) (Jak zmienić domyślny język usługi Azure SQL Database).


