---
title: Replikacja transakcyjna
description: Dowiedz się więcej o używaniu replikacji transakcyjnej programu SQL Server z pojedynczymi, pulowymi i wystąpieniami baz danych w bazie danych SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 2a048ddefbcd76193436da13cd3ba68b8b6ffb0a
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607593"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Replikacja transakcyjna z pojedynczymi, pulowanymi i wystąpieniami baz danych w bazie danych SQL usługi Azure

Replikacja transakcyjna jest funkcją bazy danych SQL Azure i programu SQL Server, która umożliwia replikowanie danych z tabeli w bazie danych SQL azure lub programu SQL Server do tabel umieszczonych w zdalnych bazach danych. Ta funkcja umożliwia synchronizację wielu tabel w różnych bazach danych.

## <a name="when-to-use-transactional-replication"></a>Kiedy używać replikacji transakcyjnej

Replikacja transakcyjna jest przydatna w następujących scenariuszach:
- Publikowanie zmian wprowadzonych w jednej lub kilku tabelach w bazie danych i rozpowszechnianie ich w jednej lub wielu bazach danych SQL Server lub Azure SQL, które subskrybowane dla zmian.
- Zachowaj kilka rozproszonych baz danych w stanie synchronizowanym.
- Migruj bazy danych z jednego programu SQL Server lub wystąpienia zarządzanego do innej bazy danych, stale publikując zmiany.

## <a name="overview"></a>Omówienie

Kluczowe składniki replikacji transakcyjnej są pokazane na poniższym rysunku:  

![replikacja z bazą danych SQL](media/replication-to-sql-database/replication-to-sql-database.png)

**Wydawca** jest wystąpieniem lub serwerem, który publikuje zmiany wprowadzone w niektórych tabelach (artykułach) przez wysłanie aktualizacji do dystrybutora. Publikowanie w dowolnej bazie danych SQL platformy Azure z lokalnego programu SQL Server jest obsługiwane przez następujące wersje programu SQL Server:

- SQL Server 2019 (wersja zapoznawcza)
- Program SQL Server 2016 do SQL 2017
- SQL Server 2014 SP1 CU3 lub większy (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 z dodatkiem SP3 lub większy (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- W przypadku innych wersji programu SQL Server, które nie obsługują publikowania obiektów na platformie Azure, można użyć metody [ponownego publikowania danych,](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) aby przenieść dane do nowszych wersji programu SQL Server. 

**Dystrybutor** jest wystąpieniem lub serwerem, który zbiera zmiany w artykułach z wydawcy i dystrybuuje je do subskrybentów. Dystrybutorem może być wystąpienie zarządzanej usługi Azure SQL Database lub SQL Server (dowolna wersja o długości, o ile jest równa lub wyższa od wersji programu Publisher). 

**Subskrybent** jest wystąpieniem lub serwerem, który otrzymuje zmiany wprowadzone w wydawcy. Subskrybenci mogą być pojedynczymi, łączone i wystąpienie baz danych w bazie danych sql sql database platformy Azure lub SQL Server. Subskrybent w pojedynczej lub puli bazy danych musi być skonfigurowany jako wypychanego abonenta. 

| Rola | Pojedyncze i połączone bazy danych | Bazy danych wystąpień |
| :----| :------------- | :--------------- |
| **Wydawca** | Nie | Tak | 
| **Dystrybutor** | Nie | Tak|
| **Pociągnij subskrybenta** | Nie | Tak|
| **Zapisz wypychanie**| Tak | Tak|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Subskrypcja ściągania nie jest obsługiwana, gdy dystrybutor jest bazą danych wystąpienia, a subskrybent nie jest. 

Istnieją różne [typy replikacji:](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)


| Replikacja | Pojedyncze i połączone bazy danych | Bazy danych wystąpień|
| :----| :------------- | :--------------- |
| [**Standardowy transakcyjny**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Tak (tylko jako subskrybent) | Tak | 
| [**Migawka**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Tak (tylko jako subskrybent) | Tak|
| [**Scalanie replikacji**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nie | Nie|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nie | Nie|
| [**Dwukierunkowy**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nie | Tak|
| [**Subskrypcje możliwe do aktualizacji**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nie | Nie|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Próba skonfigurowania replikacji przy użyciu starszej wersji może spowodować MSSQL_REPL20084 numer błędu (proces nie może połączyć się z \<subskrybentem.) i MSSQ_REPL40532 (Nie można otworzyć nazwy serwera> żądana przez identyfikator logowania. Logowanie nie powiodło się.)
  > - Aby korzystać ze wszystkich funkcji usługi Azure SQL Database, należy używać najnowszych wersji [programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i SQL Server Data Tools [(SSDT).](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Macierz wsparcia dla baz danych instancji i systemów lokalnych
  Macierz obsługi replikacji dla baz danych wystąpienia jest taka sama jak dla programu SQL Server lokalnie. 
  
| **Wydawca**   | **Dystrybutor** | **Subskrybenta** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Wymagania

- Połączenie korzysta z uwierzytelniania SQL między uczestnikami replikacji. 
- Udział konta usługi Azure Storage dla katalogu roboczego używanego przez replikację. 
- Port 445 (TCP wychodzących) musi być otwarty w regułach zabezpieczeń podsieci wystąpienia zarządzanego, aby uzyskać dostęp do udziału plików platformy Azure. 
- Port 1433 (TCP wychodzących) musi zostać otwarty, jeśli wydawca/dystrybutor znajdują się w wystąpieniu zarządzanym, a subskrybent nie jest. Może być również konieczna zmiana reguły zabezpieczeń wychodzących wychodzących wystąpienia zarządzanego dla `allow_linkedserver_outbound` `virtualnetwork` **znacznika usługi docelowej** portu 1433 z do . `internet` 
- Wszystkie typy uczestników replikacji (Wydawca, Dystrybutor, Pull Subskrybent i Push Subskrybent) mogą być umieszczane w wystąpieniach zarządzanych, ale wydawca i dystrybutor muszą być zarówno w chmurze, jak i lokalnie.
- Jeśli wydawca, dystrybutor i/lub subskrybent istnieją w różnych sieciach wirtualnych, między każdą jednostką musi istnieć komunikacja równorzędna sieci VPN, tak aby między wydawcą a dystrybutorem istniała komunikacja równorzędna sieci VPN i/lub między dystrybutorem a subskrybentem była komunikacja równorzędna sieci VPN. 


>[!NOTE]
> - Podczas nawiązywania połączenia z plikiem usługi Azure Storage można napotkać błąd 53, jeśli port 445 wychodzącej sieciowej grupy zabezpieczeń (NSG) jest zablokowany, gdy dystrybutor jest bazą danych wystąpienia, a subskrybent jest lokalny. [Zaktualizuj sieciowe sieciowe sieci vNet,](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) aby rozwiązać ten problem. 


### <a name="compare-data-sync-with-transactional-replication"></a>Porównaj synchronizację danych z replikacją transakcyjną

| | Synchronizacja danych | Replikacja transakcyjna |
|---|---|---|
| Zalety | - Aktywne-aktywne wsparcie<br/>- Dwukierunkowe między lokalnymi i usługi Azure SQL Database | - Mniejsze opóźnienie<br/>- Spójność transakcyjna<br/>- Ponowne wykorzystanie istniejącej topologii po migracji |
| Wady | - 5 min lub więcej opóźnień<br/>- Brak spójności transakcyjnej<br/>- Większy wpływ na wydajność | - Nie można opublikować z pojedynczej bazy danych usługi Azure SQL Database lub puli bazy danych<br/>- Wysokie koszty konserwacji |
| | | |

## <a name="common-configurations"></a>Typowe konfiguracje

Ogólnie rzecz biorąc wydawca i dystrybutor musi znajdować się w chmurze lub lokalnie. Obsługiwane są następujące konfiguracje: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Wydawca z lokalnym dystrybutorem w wystąpieniu zarządzanym

![Pojedyncze wystąpienie jako wydawca i dystrybutor](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Wydawca i dystrybutor są konfigurowani w ramach jednego wystąpienia zarządzanego i rozpowszechniaj zmiany w innym wystąpieniu zarządzanym, pojedynczej bazie danych, puli bazy danych lub programie SQL Server lokalnie. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Wydawca z dystrybutorem zdalnym w wystąpieniu zarządzanym

W tej konfiguracji jedno wystąpienie zarządzane publikuje zmiany w dystrybutorze umieszczone w innym wystąpieniu zarządzanym, które mogą obsługiwać wiele wystąpień zarządzanych źródła i dystrybuować zmiany do jednego lub wielu obiektów docelowych w wystąpieniu zarządzanym, pojedynczej bazie danych, puli bazy danych lub programie SQL Server.

![Oddzielne wystąpienia wydawcy i dystrybutora](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Wydawca i dystrybutor są skonfigurowani na dwóch wystąpieniach zarządzanych. W tej konfiguracji występują pewne ograniczenia: 

- Oba wystąpienia zarządzane znajdują się w tej samej sieci wirtualnej.
- Oba wystąpienia zarządzane znajdują się w tej samej lokalizacji.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Wydawca i dystrybutor lokalnie z subskrybentem w jednej, puli i bazy danych wystąpień 

![Usługa Azure SQL DB jako subskrybent](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
W tej konfiguracji usługa Azure SQL Database (pojedyncza, pulowana i bazy danych wystąpień) jest subskrybentem. Ta konfiguracja obsługuje migrację z lokalnego na platformę Azure. Jeśli subskrybent znajduje się w jednej lub puli bazy danych, musi być w trybie wypychania.  

## <a name="with-failover-groups"></a>Z grupami trybu failover

Jeśli replikacja geograficzna jest włączona w wystąpieniu **wydawcy** lub **dystrybutora** w [grupie trybu failover,](sql-database-auto-failover-group.md)administrator wystąpienia zarządzanego musi wyczyścić wszystkie publikacje na starym podstawowym i ponownie skonfigurować je w nowym podstawowym po wystąpieniu pracy awaryjnej. W tym scenariuszu są potrzebne następujące działania:

1. Zatrzymaj wszystkie zadania replikacji uruchomione w bazie danych, jeśli istnieją.
2. Upuść metadane subskrypcji od wydawcy, uruchamiając następujący skrypt w bazie danych wydawcy:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Upuść metadane subskrypcji od subskrybenta. Uruchom następujący skrypt w bazie danych subskrypcji w wystąpieniu subskrybenta:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Zdecydowanie upuść wszystkie obiekty replikacji z wydawcy, uruchamiając następujący skrypt w opublikowanej bazie danych:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Zdecydowanie upuść starego dystrybutora z oryginalnego wystąpienia podstawowego (jeśli nie powróci do starego podstawowego, który kiedyś miał dystrybutora). Uruchom następujący skrypt w głównej bazie danych w starym wystąpieniu zarządzanym dystrybutora:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Jeśli replikacja geograficzna jest włączona w wystąpieniu **subskrybenta** w grupie trybu failover, publikacja powinna być skonfigurowana do łączenia się z punktem końcowym odbiornika grupy trybu failover dla wystąpienia zarządzanego subskrybenta. W przypadku pracy awaryjnej kolejne działania administratora wystąpienia zarządzanego zależą od typu trybu failover, który wystąpił: 

- W przypadku pracy awaryjnej bez utraty danych replikacja będzie nadal działać po przepracie awaryjnym. 
- W przypadku pracy awaryjnej z utratą danych replikacja będzie działać również. Będzie ponownie replikować utracone zmiany. 
- W przypadku pracy awaryjnej z utratą danych, ale utrata danych jest poza okresem przechowywania bazy danych dystrybucji, administrator wystąpienia zarządzanego będzie musiał ponownie zainicjować bazę danych subskrypcji. 

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie replikacji między wydawcą MI a subskrybentem](replication-with-sql-database-managed-instance.md)
- [Konfigurowanie replikacji między wydawcą MI, dystrybutorem MI i subskrybentem programu SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)
- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Utwórz subskrypcję wypychaną](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) przy użyciu nazwy serwera bazy `N'azuresqldbdns.database.windows.net` danych SQL azure jako subskrybenta (na przykład i nazwy bazy danych SQL Azure jako docelowej bazy danych (na przykład **Adventureworks**. )


Aby uzyskać więcej informacji na temat konfigurowania replikacji transakcyjnej, zobacz następujące samouczki:



## <a name="see-also"></a>Zobacz też  

- [Replikacja z mi i grupą trybu failover](sql-database-managed-instance-transact-sql-information.md#replication)
- [Replikacja do bazy danych SQL](replication-to-sql-database.md)
- [Replikacja do wystąpienia zarządzanego](replication-with-sql-database-managed-instance.md)
- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Tworzenie subskrypcji wypychanej](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication (Typy replikacji)](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorowanie (replikacja)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicjowanie subskrypcji](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
