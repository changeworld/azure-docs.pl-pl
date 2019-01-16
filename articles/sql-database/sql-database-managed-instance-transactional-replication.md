---
title: Replikacja transakcyjna z serwerem logicznym SQL platformy Azure i wystąpienia zarządzanego Azure SQL | Dokumentacja firmy Microsoft"
description: Informacje o używaniu replikację transakcyjną programu SQL Server z serwerami logicznymi usługi Azure SQL Database i wystąpienia zarządzanego SQL.
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
ms.date: 01/08/2019
ms.openlocfilehash: 1839ca0d2495a07f6fc734501540cddcdcb28e18
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332873"
---
# <a name="transactional-replication-with-azure-sql-logical-server-and-azure-sql-managed-instance"></a>Replikacja transakcyjna z serwerem logicznym SQL platformy Azure i wystąpienia zarządzanego Azure SQL

Replikacja transakcyjna to funkcja usługi Azure SQL Database, wystąpienia zarządzanego i programu SQL Server, która pozwala na replikowanie danych z tabeli w usłudze Azure SQL Database lub SQL Server do tabel umieszczone na zdalne bazy danych. Ta funkcja służy do synchronizowania wielu tabel w różnych bazach danych.

## <a name="overview"></a>Przegląd 
Główne składniki replikacji transakcyjnej przedstawiono na poniższej ilustracji:  

![Replikacja za pomocą bazy danych SQL](media/replication-to-sql-database/replication-to-sql-database.png)


**Wydawcy** to wystąpienie lub serwer, który publikuje zmiany wprowadzone na niektórych tabel (artykuły), wysyłając aktualizacje z dystrybutorem. Publikowanie do usługi Azure SQL Database z lokalnego programu SQL Server jest obsługiwana w następujących wersjach programu SQL Server:
    - 2019 r programu SQL Server (wersja zapoznawcza)
    - SQL Server 2016 do programu SQL 2017
    - SQL Server 2014 SP1 CU3 lub większa (12.00.4427)
    - SQL Server 2014 RTM CU10 (12.00.2556)
    - SQL Server 2012 SP3 lub większa (11.0.6020)
    - SQL Server 2012 SP2 CU8 (11.0.5634.0)
    - W przypadku innych wersji programu SQL Server, które nie obsługują publikowania do obiektów na platformie Azure, jest możliwe wykorzystanie [ponowne publikowanie danych](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) metodę, aby przenieść dane do nowszych wersji programu SQL Server. 

**Dystrybutora** to wystąpienie lub serwer, który zbiera zmian w artykułach z wydawcą i przesyła je do subskrybentów. Dystrybutor może być wystąpienia zarządzanego Azure SQL Database lub SQL Server (dowolna wersja, jak długie go równą lub większą niż wersja, wydawca). 

**Subskrybenta** to wystąpienie lub serwerze, który odbiera zmiany wprowadzone na wydawcy. Subskrybenci mogą być Azure SQL Database logiczne serwera/wystąpienia zarządzanego lub SQL Server. Subskrybent na serwerze logicznym musi być skonfigurowany jako subskrybent wypychania. 

| Rola | Serwer logiczny | Wystąpienie zarządzane |
| :----| :------------- | :--------------- |
| **Wydawca** | Nie | Yes | 
| **Dystrybutor** | Nie | Yes|
| **Ściągnij subskrybenta** | Nie | Yes|
| **Wypychanie subskrybenta**| Yes | Yes|
| &nbsp; | &nbsp; | &nbsp; |

Istnieją różne [typy replikacji](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication?view=sql-server-2017):


| Replikacja | Serwer logiczny | Wystąpienie zarządzane |
| :----| :------------- | :--------------- |
| [**transakcyjne**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Tak (tylko jako subskrybenta) | Yes | 
| [**Snapshot**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Tak (tylko jako subskrybenta) | Yes|
| [**Replikacja scalająca**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nie | Nie|
| [**Sieć równorzędna**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nie | Nie|
| **One-way** | Yes | Yes|
| [**Dwukierunkowe**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nie | Yes|
| [**Aktualizowalne subskrypcje**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nie | Nie|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Próba skonfigurowania replikacji za pomocą starszej wersji może spowodować błąd, numer MSSQL_REPL20084 (ten proces nie może połączyć się subskrybenta.) i MSSQ_REPL40532 (nie można otworzyć serwera <name> żądanego podczas logowania. Logowanie nie powiodło się.)
  > - Aby korzystać ze wszystkich funkcji usługi Azure SQL Database, należy używać najnowszej wersji [programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) i [programu SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017).

## <a name="requirements"></a>Wymagania
- Połączenie korzysta z uwierzytelniania programu SQL między uczestnikami replikacji. 
- Udział konta usługi Azure Storage katalog roboczy używany przez funkcję replikacji. 
- Port 445 (ruch wychodzący protokołu TCP) musi być otwarte w regułach zabezpieczeń w podsieci wystąpienia zarządzanego dostępu do udziału plików platformy Azure. 
- Port 1433 (ruch wychodzący protokołu TCP) musi zostać otwarte w przypadku wydawcy/dystrybutora znajdują się na wystąpienie zarządzane, a subskrybent działa lokalnie. 

## <a name="common-configurations"></a>Typowe konfiguracje
Ogólnie rzecz biorąc wydawcy i dystrybutora musi być w chmurze lub lokalnie. Obsługiwane są następujące konfiguracje: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Wydawcy o dystrybutor lokalny na wystąpienie zarządzane

![Pojedyncze wystąpienie jako wydawcą i dystrybutorem ](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Wydawcą i dystrybutorem są skonfigurowane w ramach jednego wystąpienia zarządzanego. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Wydawcy o dystrybutorze zdalnym na wystąpienie zarządzane

![Oddzielne wystąpienia dla wydawcy i dystrybucji](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Wydawcą i dystrybutorem są konfigurowane na dwa wystąpienia zarządzanego. W tej konfiguracji

- Oba wystąpienia zarządzane przez usługę znajdują się na tej samej sieci wirtualnej.
- Oba wystąpienia zarządzane przez usługę znajdują się w tej samej lokalizacji. 

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-managed-instance-or-logical-server"></a>Wydawcą i dystrybutorem lokalnie przy użyciu subskrybenta na wystąpieniu zarządzanym usługi lub serwera logicznego 

![Usługa Azure SQL DB subskrybenta](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
W tej konfiguracji usługi Azure SQL Database (wystąpienie zarządzane usługi lub serwera logicznego) jest subskrybentem. Ta konfiguracja obsługuje migracji ze środowiska lokalnego na platformę Azure. Jeśli subskrybent znajduje się na serwerze logicznym, musi być w trybie wypychania.  

## <a name="next-steps"></a>Kolejne kroki
1. [Konfigurowanie replikacji transakcyjnej dla wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance#configure-publishing-and-distribution-example). 
1. [Utwórz publikację](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
1. [Tworzenie subskrypcji wypychanej](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) przy użyciu nazwy serwera logicznego usługi Azure SQL Database jako subskrybenta (na przykład `N'azuresqldbdns.database.windows.net` i nazwę bazy danych SQL Azure jako docelowej bazy danych (na przykład **Adventureworks**. )


## <a name="see-also"></a>Zobacz też  

- [Replikacja do bazy danych SQL](replication-to-sql-database.md)
- [Replikacja do wystąpienia zarządzanego](replication-with-sql-database-managed-instance.md)
- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Tworzenie subskrypcji wypychanej](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typy replikacji](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorowanie (replikacja)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicjowanie subskrypcji](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
