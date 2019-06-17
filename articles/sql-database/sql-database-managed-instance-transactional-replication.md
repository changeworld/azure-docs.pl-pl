---
title: Replikacja transakcyjna, za pomocą usługi Azure SQL Database | Dokumentacja firmy Microsoft"
description: Dowiedz się więcej o przy użyciu replikację transakcyjną programu SQL Server za pomocą pojedynczej, puli i wystąpienie bazy danych w usłudze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: c8d5df0f83a7ae37b9f06a5e255e9809288b6d67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64917040"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Replikacja transakcyjna, za pomocą pojedynczej, puli i wystąpienie bazy danych w usłudze Azure SQL Database

Replikacja transakcyjna to funkcja usługi Azure SQL Database i programu SQL Server, która pozwala na replikowanie danych z tabeli w usłudze Azure SQL Database lub SQL Server do tabel umieszczone na zdalne bazy danych. Ta funkcja służy do synchronizowania wielu tabel w różnych bazach danych.

## <a name="when-to-use-transactional-replication"></a>Kiedy należy używać replikacji transakcyjnej

Replikacja transakcyjna jest przydatne w następujących scenariuszach:
- Opublikuj zmiany wprowadzone w co najmniej jedną tabelę w bazie danych, a następnie dystrybuować je do jednej lub wielu programu SQL Server lub Azure SQL baz danych, które subskrybuje zmiany.
- Zachowaj kilka rozproszonych baz danych w stanie zsynchronizowane.
- Migrowanie baz danych z jednego programu SQL Server lub wystąpienia zarządzanego do innej bazy danych, stale publikując zmiany.

## <a name="overview"></a>Omówienie

Główne składniki replikacji transakcyjnej przedstawiono na poniższej ilustracji:  

![Replikacja za pomocą bazy danych SQL](media/replication-to-sql-database/replication-to-sql-database.png)

**Wydawcy** to wystąpienie lub serwer, który publikuje zmiany wprowadzone na niektórych tabel (artykuły), wysyłając aktualizacje z dystrybutorem. Publikowanie do dowolnej usługi Azure SQL bazy danych z lokalnego programu SQL Server jest obsługiwany przez następujące wersje programu SQL Server:

- 2019 r programu SQL Server (wersja zapoznawcza)
- SQL Server 2016 do programu SQL 2017
- SQL Server 2014 SP1 CU3 lub większa (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 lub większa (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- W przypadku innych wersji programu SQL Server, które nie obsługują publikowania do obiektów na platformie Azure, jest możliwe wykorzystanie [ponowne publikowanie danych](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) metodę, aby przenieść dane do nowszych wersji programu SQL Server. 

**Dystrybutora** to wystąpienie lub serwer, który zbiera zmian w artykułach z wydawcą i przesyła je do subskrybentów. Dystrybutor może być wystąpienia zarządzanego Azure SQL Database lub SQL Server (dowolna wersja, jak długie go równą lub większą niż wersja, wydawca). 

**Subskrybenta** to wystąpienie lub serwerze, który odbiera zmiany wprowadzone na wydawcy. Subskrybenci mogą być albo pojedynczej, puli i wystąpienie bazy danych w bazach danych Azure SQL Database lub SQL Server. Subskrybent pojedyncze lub zbiorcze bazy danych musi być skonfigurowany jako subskrybenta wypychania. 

| Rola | Jedno- i puli baz danych | Wystąpienie bazy danych |
| :----| :------------- | :--------------- |
| **Publisher** | Nie | Tak | 
| **Dystrybutor** | Nie | Tak|
| **Ściągnij subskrybenta** | Nie | Yes|
| **Wypychanie subskrybenta**| Yes | Yes|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Subskrypcję wciąganą nie jest obsługiwana w przypadku dystrybutora jest wystąpienie bazy danych i subskrybenta nie jest. 

Istnieją różne [typy replikacji](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):


| Replikacja | Jedno- i puli baz danych | Wystąpienie bazy danych|
| :----| :------------- | :--------------- |
| [**transakcyjne**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Tak (tylko jako subskrybenta) | Tak | 
| [**Snapshot**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Tak (tylko jako subskrybenta) | Tak|
| [**Replikacja scalająca**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nie | Nie|
| [**Sieć równorzędna**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nie | Nie|
| **One-way** | Tak | Tak|
| [**Dwukierunkowe**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nie | Tak|
| [**Aktualizowalne subskrypcje**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nie | Nie|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Próba skonfigurowania replikacji za pomocą starszej wersji może spowodować błąd, numer MSSQL_REPL20084 (ten proces nie może połączyć się subskrybenta.) i MSSQ_REPL40532 (nie można otworzyć serwera \<name > żądanego podczas logowania. Logowanie nie powiodło się.)
  > - Aby korzystać ze wszystkich funkcji usługi Azure SQL Database, należy używać najnowszej wersji [programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i [programu SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Macierz obsługi dla wystąpienia bazy danych i lokalnymi systemami
  Macierz obsługi replikacji dla wystąpienia bazy danych jest taka sama, jak dla programu SQL Server w środowisku lokalnym. 
  
  | **Publisher**   | **Dystrybutor** | **Dla subskrybentów** |
| :------------   | :-------------- | :------------- |
| SQL Server 2017 | SQL Server 2017 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Wymagania

- Połączenie korzysta z uwierzytelniania programu SQL między uczestnikami replikacji. 
- Udział konta usługi Azure Storage katalog roboczy używany przez funkcję replikacji. 
- Port 445 (ruch wychodzący protokołu TCP) musi być otwarte w regułach zabezpieczeń w podsieci wystąpienia zarządzanego dostępu do udziału plików platformy Azure. 
- Port 1433 (ruch wychodzący protokołu TCP) musi zostać otwarte w przypadku wydawcy/dystrybutora znajdują się na wystąpienie zarządzane, a subskrybent działa lokalnie.

  >[!NOTE]
  > Możesz napotkać błąd 53, łącząc się z usługi Azure File Storage, jeśli Sieciowej grupy zabezpieczeń sieciowego ruchu wychodzącego w porcie 445 jest zablokowany, po dystrybutora jest wystąpienie baza danych subskrybenta w środowisku lokalnym. [Aktualizowanie sieci wirtualnej, sieciowej grupy zabezpieczeń](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) Aby rozwiązać ten problem. 

### <a name="compare-data-sync-with-transactional-replication"></a>Porównaj Data Sync za pomocą replikacji transakcyjnej

| | Synchronizacja danych | Replikacja transakcyjna |
|---|---|---|
| Zalety | — Obsługa aktywny aktywny<br/>Dwukierunkowej między systemem lokalnym i usługi Azure SQL Database | — Mniejsze opóźnienia<br/>— Poziom spójności transakcyjnej<br/>-Użyj ponownie istniejącą topologię po migracji |
| Wady | -5 minut lub więcej opóźnienia<br/>-Brak spójności transakcyjnej<br/>-Wyższe negatywny wpływ na wydajność | -Nie można opublikować z pojedynczej bazy danych Azure SQL Database lub baza danych w puli<br/>— Koszt eksploatacji wysoka |
| | | |

## <a name="common-configurations"></a>Typowe konfiguracje

Ogólnie rzecz biorąc wydawcy i dystrybutora musi być w chmurze lub lokalnie. Obsługiwane są następujące konfiguracje: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Wydawcy o dystrybutor lokalny na wystąpienie zarządzane

![Pojedyncze wystąpienie jako wydawcą i dystrybutorem](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Wydawcą i dystrybutorem są skonfigurowane w ramach jednego wystąpienia zarządzanego i rozproszone zmiany do innego wystąpienia zarządzanego, pojedynczej bazy danych, baza danych w puli lub programu SQL Server w środowisku lokalnym. W tej konfiguracji wydawcy/dystrybutora wystąpienia zarządzanego nie można skonfigurować za pomocą [replikacji geograficznej i automatycznie grupy trybu failover](sql-database-auto-failover-group.md).

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Wydawcy o dystrybutorze zdalnym na wystąpienie zarządzane

W tej konfiguracji jednego wystąpienia zarządzanego publikuje zmiany dystrybutora umieszczone w innym wystąpieniu zarządzanym, który może obsługiwać wiele wystąpień zarządzanych źródła i dystrybucję zmian do jednej lub wielu elementów docelowych w wystąpieniu zarządzanym, pojedynczą bazę danych, baza danych w puli, lub Program SQL Server.

![Oddzielne wystąpienia dla wydawcy i dystrybucji](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Wydawcą i dystrybutorem są konfigurowane na dwa wystąpienia zarządzanego. W tej konfiguracji

- Oba wystąpienia zarządzane przez usługę znajdują się na tej samej sieci wirtualnej.
- Oba wystąpienia zarządzane przez usługę znajdują się w tej samej lokalizacji.
- Wystąpienia zarządzane, które hostują opublikowane i baz danych dystrybutora nie może być [replikacją geograficzną za pomocą grupy trybu failover automatycznie](sql-database-auto-failover-group.md).

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Wydawcy i dystrybutora lokalnego z subskrypcją w ramach jednej puli lub wystąpienie bazy danych 

![Usługa Azure SQL DB subskrybenta](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
W tej konfiguracji usługi Azure SQL Database (pojedyncze, puli i wystąpienia bazy danych) jest subskrybentem. Ta konfiguracja obsługuje migracji ze środowiska lokalnego na platformę Azure. Jeśli subskrybent znajduje się w jednym lub w puli bazy danych, należy w trybie wypychania.  


## <a name="next-steps"></a>Kolejne kroki

1. [Konfigurowanie replikacji transakcyjnej dla wystąpienia zarządzanego](replication-with-sql-database-managed-instance.md). 
1. [Utwórz publikację](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
1. [Tworzenie subskrypcji wypychanej](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) przy użyciu nazwy serwera Azure SQL Database jako subskrybenta (na przykład `N'azuresqldbdns.database.windows.net` i nazwę bazy danych SQL Azure jako docelowej bazy danych (na przykład **Adventureworks**. )



## <a name="see-also"></a>Zobacz też  

- [Replikacja do bazy danych SQL](replication-to-sql-database.md)
- [Replikacja do wystąpienia zarządzanego](replication-with-sql-database-managed-instance.md)
- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Tworzenie subskrypcji wypychanej](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typy replikacji](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorowanie (replikacja)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicjowanie subskrypcji](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
