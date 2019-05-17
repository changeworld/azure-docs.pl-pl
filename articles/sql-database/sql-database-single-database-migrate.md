---
title: Migracja bazy danych programu SQL Server do pojedynczej/puli bazy danych w usłudze Azure SQL Database | Dokumentacja firmy Microsoft
description: Więcej informacji na temat migracji bazy danych programu SQL Server do pojedynczej bazy danych lub elastycznej puli w usłudze Azure SQL Database.
keywords: migracja bazy danych,migracja bazy danych programu sql server,narzędzia migracji bazy danych,migracja bazy danych,migracja bazy danych sql
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: 1dd7d2fa413a6502dcd5c9db59e376c6586b5fea
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790311"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Migracja bazy danych programu SQL Server do usługi Azure SQL Database

W tym artykule poznasz podstawowe metody migracji programu SQL Server 2005 lub nowszej wersji bazy danych do jednej lub w puli bazy danych w usłudze Azure SQL Database. Aby uzyskać informacje na temat migracji do wystąpienia zarządzanego, zobacz [Przeprowadź migrację do wystąpienia programu SQL Server do wystąpienia zarządzanego Azure SQL Database](sql-database-managed-instance-migrate.md). Migracja informacji o migracji z innych platform, zobacz [Przewodnik po migracji bazy danych Azure](https://datamigration.microsoft.com/).

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrowanie do pojedynczej bazy danych lub baza danych w puli

Istnieją dwie podstawowe metody migracji programu SQL Server 2005 lub nowszej wersji bazy danych do jednej lub w puli bazy danych w usłudze Azure SQL Database. Pierwsza metoda jest prostsza, ale wymaga pewnego, prawdopodobnie znaczącego, przestoju podczas migracji. Druga metoda jest bardziej skomplikowana, ale znacznie eliminuje przestój podczas migracji.

W obu przypadkach należy się upewnić, że źródłowa baza danych jest zgodny z usługi Azure SQL Database przy użyciu [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). Zbliża się do bazy danych SQL Database V12 [równowaga](sql-database-features.md) z wyjątkiem kwestii związanych z operacjami na poziomie serwera i między bazami danych programu SQL Server. Bazy danych i aplikacje oparte na [częściowo obsługiwanych lub nieobsługiwanych funkcjach](sql-database-transact-sql-information.md) muszą zostać w pewnym stopniu [przeprojektowane, aby usunąć niezgodności](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues) i umożliwić migrację bazy danych programu SQL Server.

> [!NOTE]
> Aby wykonać migrację bazy danych innej niż baza danych programu SQL Server, w tym bazy danych Microsoft Access, Sybase, MySQL Oracle i DB2, do usługi Azure SQL Database, zobacz temat [Asystent migracji programu SQL Server](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Metoda 1. Migracja z przestojem podczas migracji

 Ta metoda umożliwia migrację jednej lub bazy danych w puli, jeśli użytkownik przestój jest dopuszczalny lub wykonywana jest testowa migracja produkcyjnej bazy danych celów późniejszej migracji. Aby zapoznać się z samouczkiem, zobacz [Migrowanie bazy danych programu SQL Server](../dms/tutorial-sql-server-to-azure-sql.md).

Poniższa lista zawiera ogólny przepływ pracy migracji bazy danych programu SQL Server w pojedynczej lub bazy danych w puli za pomocą tej metody. W przypadku migracji do wystąpienia zarządzanego zobacz [migracji do wystąpienia zarządzanego](sql-database-managed-instance-migrate.md).

  ![Diagram migracji VSSSDT](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Ocena](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) bazy danych dla zgodności przy użyciu najnowszej wersji [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Przygotowanie wszelkich niezbędnych poprawek jako skryptów języka Transact-SQL.
3. Utwórz kopię transakcyjnie spójne źródłowej bazy danych podlegających migracji lub zatrzymanie nowych transakcji z występującymi w źródłowej bazy danych w czasie trwania migracji. Metody wykonywania tej opcji ostatnie obejmują wyłączenia łączności klientów lub tworzenia [migawki bazy danych](https://msdn.microsoft.com/library/ms175876.aspx). Po zakończeniu migracji można używać replikacji transakcyjnej do aktualizacji migrowanych baz danych ze zmianami, które wystąpiły po Punkt odcięcia do migracji. Zobacz [przeprowadzić migrację za pomocą transakcyjnych migracji](sql-database-single-database-migrate.md#method-2-use-transactional-replication).  
4. Wdrożenie skryptów języka Transact-SQL w celu zastosowania poprawek do kopii bazy danych.
5. [Migrowanie](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) kopii bazy danych do nowej bazy danych SQL Azure, za pomocą programu Data Migration Assistant.

> [!NOTE]
> Zamiast przy użyciu narzędzia DMA, można również użyć pliku BACPAC. Zobacz [Importowanie pliku BACPAC do nowej bazy danych SQL Azure](sql-database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Optymalizowanie wydajności transferu danych podczas migracji

Poniższa lista zawiera zalecenia pozwalające uzyskać najlepszą wydajność podczas procesu importowania.

- Wybierz najwyższej warstwy usługi i obliczenia rozmiaru, które pozwala Twój budżet, aby zmaksymalizować wydajność transferu. Po zakończeniu migracji będzie można dokonać skalowania w dół, aby nie ponosić nadmiernych kosztów.
- Zmniejsz odległość między plik BACPAC i docelowym centrum danych.
- Wyłącz automatyczne statystyki na czas migracji.
- Partycjonuj tabele i indeksy.
- Usuń poindeksowane widoki i utwórz je ponownie po zakończeniu.
- Przenieś rzadko wyszukiwane dane historyczne do innej bazy danych i zmigruj te dane historyczne do oddzielnej bazy danych Azure SQL Database. Następnie będzie można wykonywać zapytania o te dane historyczne za pomocą [zapytań elastycznych](sql-database-elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Optymalizacja wydajności po zakończeniu migracji

[Zaktualizuj statystyki](https://msdn.microsoft.com/library/ms187348.aspx) poprzez pełne skanowanie po zakończeniu migracji.

## <a name="method-2-use-transactional-replication"></a>Metoda 2. Korzystanie z replikacji transakcyjnej

Gdy nie możesz sobie pozwolić na usunięcie bazy danych programu SQL Server ze środowiska produkcyjnego na czas migracji, jako rozwiązania do migracji możesz wykorzystać replikację transakcyjną programu SQL Server. Aby użyć tej metody, źródłowa baza danych musi spełniać [wymagania dotyczące replikacji transakcyjnej](https://msdn.microsoft.com/library/mt589530.aspx) i być zgodna z usługą Azure SQL Database. Dla informacji o replikacji SQL przy użyciu zawsze włączonych [skonfigurować replikacji dla zawsze włączonych grup dostępności (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Aby użyć tego rozwiązania, usługę Azure SQL Database konfiguruje się jako subskrybenta wystąpienia programu SQL Server do migracji. Dystrybutor replikacji transakcyjnej synchronizuje dane z bazy danych do synchronizacji (wydawcy), podczas gdy cały czas realizowane są nowe transakcje.

Przy replikacji transakcyjnej wszystkie zmiany wprowadzane do danych lub schematu pojawiają się w usłudze Azure SQL Database. Gdy synchronizacja zostanie zakończona i wszystko będzie gotowe do migracji, zmień parametry połączenia swoich aplikacji w taki sposób, aby wskazać im usługę Azure SQL Database. Gdy w wyniku replikacji transakcyjnej wszystkie zmiany pozostałe w źródłowej bazie danych zostaną pobrane, a wszystkie aplikacje będą wskazywać bazę danych platformy Azure, można odinstalować replikację transakcyjną. Usługa Azure SQL Database stanie się Twoim systemem produkcyjnym.

 ![Diagram SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> Replikacji transakcyjnej możesz także używać do migrowania podzestawu źródłowej bazy danych. Publikacja replikowana do usługi Azure SQL Database może być ograniczona do podzbioru tabel w replikowanej bazie danych. Dla każdej replikowanej tabeli dane można ograniczyć do podzestawu wierszy i/lub podzestawu kolumn.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Przepływ pracy migracji do usługi SQL Database za pomocą replikacji transakcyjnej

> [!IMPORTANT]
> Używaj najnowszej wersji programu SQL Server Management Studio, aby zachować synchronizację z aktualizacjami platformy Microsoft Azure i usługi SQL Database. Starsze wersje programu SQL Server Management Studio nie mogą skonfigurować usługi SQL Database jako subskrybenta. [Zaktualizuj program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

1. Konfigurowanie dystrybucji
   - [Przy użyciu programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   - [Przy użyciu języka Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)

2. Tworzenie publikacji
   - [Przy użyciu programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   - [Przy użyciu języka Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Tworzenie subskrypcji
   - [Przy użyciu programu SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   - [Przy użyciu języka Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

Niektóre wskazówki i różnice dotyczące migracji do usługi SQL Database

- Użycie dystrybutora lokalnego:
  - To powoduje, że wpływ na wydajność na serwerze.
  - Jeśli negatywny wpływ na wydajność jest nieakceptowalny, można użyć innego serwera, ale komplikuje to zarządzanie i administrowanie.
- Wybierając folder migawki, upewnij się, że wybrany folder jest wystarczająco duży, aby pomieścić kopię BCP każdej tabeli, którą chcesz zreplikować.
- Tworzenie migawki powoduje zablokowanie skojarzonych tabel do czasu zakończenia operacji, dlatego tworzenie migawek należy odpowiednio zaplanować.
- Usługa Azure SQL Database obsługuje wyłącznie subskrypcje wypychane. Subskrybentów można dodawać wyłącznie ze źródłowej bazy danych.

## <a name="resolving-database-migration-compatibility-issues"></a>Rozwiązywanie problemów ze zgodnością migracji bazy danych

Może wystąpić wiele różnych problemów ze zgodnością, w zależności od wersji programu SQL Server w źródłowej bazie danych oraz złożoności migrowanej bazy danych. Starsze wersje programu SQL Server mają więcej problemów ze zgodnością. Oprócz ukierunkowanego wyszukiwania w Internecie za pomocą preferowanej wyszukiwarki użyj następujących zasobów:

- [Funkcje bazy danych programu SQL Server nieobsługiwane przez usługę Azure SQL Database](sql-database-transact-sql-information.md)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Oprócz wyszukiwania w Internecie i użycia wymienionych zasobów, możesz skorzystać z [forów społeczności programu SQL Server w sieci MSDN](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) lub witryny [StackOverflow](https://stackoverflow.com/).

> [!IMPORTANT]
> Wystąpienie zarządzane usługi SQL Database umożliwia migrację istniejącego wystąpienia programu SQL Server i jego bazami danych przy użyciu minimalnego do żadnych problemów ze zgodnością. Zobacz [co to jest wystąpienie zarządzane](sql-database-managed-instance.md).

## <a name="next-steps"></a>Kolejne kroki

- Użyj skryptu z blogu SQL EMEA Engineers, aby [monitorować użycie bazy danych tempdb podczas migracji](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
- Użyj skryptu z blogu SQL EMEA Engineers, aby [monitorować obszar rejestrowania transakcji Twojej bazy danych w czasie trwania migracji](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0).
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby uzyskać informacje na temat pracy z czasem UTC po migracji, zobacz [Modifying the default time zone for your local time zone](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/) (Modyfikowanie domyślnej strefy czasowej dla Twojej lokalnej strefy czasowej).
- Aby uzyskać informacje na temat zmieniania domyślnego języka bazy danych po migracji, zobacz [How to change the default language of Azure SQL Database](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/) (Jak zmienić domyślny język usługi Azure SQL Database).
