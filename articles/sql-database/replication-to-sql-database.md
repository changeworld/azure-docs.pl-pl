---
title: Replikacja usługi Azure SQL Database | Dokumentacja firmy Microsoft"
description: Dowiedz się więcej o za pomocą replikacji programu SQL Server za pomocą usługi Azure SQL Database pojedynczych baz danych i baz danych w elastycznej puli
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b9d6569504b5352c6187afe12d903c986019c517
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60646816"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replikacja bazy danych SQL pojedynczych i puli baz danych

Można skonfigurować replikacji programu SQL Server do pojedynczych i puli baz danych na [bazy danych programu SQL server](sql-database-servers.md) w usłudze Azure SQL Database.  

## <a name="supported-configurations"></a>**Obsługiwane konfiguracje:**
  
- Program SQL Server może być wystąpienie programu SQL Server działającego w środowisku lokalnym lub wystąpienie serwera SQL uruchomionego na maszynie wirtualnej platformy Azure w chmurze. Aby uzyskać więcej informacji, zobacz [programu SQL Server na maszynach wirtualnych platformy Azure — omówienie](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- Bazy danych Azure SQL musi być subskrybentem wypychania wydawcy z programem SQL Server.  
- Baza danych dystrybucji i agentów replikacji nie można umieścić na bazę danych Azure SQL database.  
- Migawki i replikacja jednokierunkowa transakcyjne są obsługiwane. Peer-to-peer replikacji transakcyjnej i replikacji scalającej nie są obsługiwane.
- Replikacja jest dostępna w publicznej wersji zapoznawczej na wystąpieniu zarządzanym bazy danych SQL Azure. Wystąpienie zarządzane umożliwia hostowanie bazy danych wydawcy dystrybutora i subskrybenta. Aby uzyskać więcej informacji, zobacz [replikację za pomocą wystąpienia zarządzanego usługi SQL Database](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Wersje  

- Wydawcy i dystrybutora musi wynosić co najmniej jednej z następujących wersji:  
- SQL Server 2017 (14.x)
- SQL Server 2016 (13.x)
- SQL Server 2014 (12.x) z dodatkiem SP1 CU3
- CU10 RTM programu SQL Server 2014 (12.x)
- SQL Server 2012 (11.x) z dodatkiem SP2 CU8 lub z dodatkiem SP3
- Próba skonfigurowania replikacji za pomocą starszej wersji może spowodować błąd, numer MSSQL_REPL20084 (ten proces nie może połączyć się subskrybenta.) i MSSQL_REPL40532 (nie można otworzyć serwera \<name > żądanego podczas logowania. Logowanie powiodło się.).  
- Aby korzystać ze wszystkich funkcji usługi Azure SQL Database, należy używać najnowszej wersji [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  
  
## <a name="remarks"></a>Uwagi

- Można skonfigurować replikacji przy użyciu [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub przez wykonanie instrukcji języka Transact-SQL na wydawcy. Nie można skonfigurować replikacji przy użyciu witryny Azure portal.  
- Aby nawiązać połączenie z bazą danych Azure SQL database replikacji można używać tylko logowania uwierzytelniania programu SQL Server.
- Zreplikowane tabele musi mieć klucz podstawowy.  
- Masz istniejącą subskrypcję platformy Azure.  
- Subskrybent bazy danych SQL platformy Azure można w dowolnym regionie.  
- Jedna publikacja w programie SQL Server może obsługiwać zarówno usługi Azure SQL Database, jak i subskrybentów programu SQL Server (w środowisku lokalnym i programu SQL Server na maszynie wirtualnej platformy Azure).  
- Zarządzanie replikacją, monitorowania i rozwiązywania problemów, należy wykonać z lokalnym serwerem SQL.  
- Obsługiwane są wyłącznie Subskrypcje wypychane do usługi Azure SQL Database.  
- Tylko `@subscriber_type = 0` jest obsługiwana w **procedury składowanej sp_addsubscription** dla bazy danych SQL.  
- Usługa Azure SQL Database nie obsługuje replikację dwukierunkową, natychmiastowe, nadaje się do aktualizacji lub równorzędnych.

## <a name="replication-architecture"></a>Architektura replikacji  

![replication-to-sql-database](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scenariusze  

### <a name="typical-replication-scenario"></a>Scenariusz typowe replikacji  

1. Utwórz publikację Replikacja transakcyjna bazy danych programu SQL Server w środowisku lokalnym.  
2. W programie SQL Server w środowisku lokalnym, użyj **Kreatora nowej subskrypcji** lub instrukcji Transact-SQL, aby utworzyć powiadomienie wypychane do subskrypcji usługi Azure SQL Database.  
3. Z jednej puli baz danych i w usłudze Azure SQL Database początkowy zestaw danych jest migawką, tworzonego przez agenta migawek i rozproszonych i stosowane przez agenta dystrybucji. Wystąpienie zarządzane bazy danych również użyć kopii zapasowej bazy danych Aby zapełnić bazę danych subskrybenta.

### <a name="data-migration-scenario"></a>Scenariusz migracji danych  

1. Korzystanie z replikacji transakcyjnej do replikowania danych z lokalnej bazy danych programu SQL Server do usługi Azure SQL Database.  
2. Przekierowania klienta lub aplikacji warstwy środkowej, aby zaktualizować kopię bazy danych Azure SQL.  
3. Zatrzymaj aktualizowanie wersji programu SQL Server w tabeli i usuwanie publikacji.  

## <a name="limitations"></a>Ograniczenia

Następujące opcje nie są obsługiwane w przypadku subskrypcji usługi Azure SQL Database:

- Skopiuj skojarzenia grup plików  
- Kopiuj tabelę schematy partycjonowania  
- Skopiuj schematy partycjonowania indeksu  
- Skopiuj dane statystyczne zdefiniowane przez użytkownika  
- Skopiuj domyślne powiązania  
- Kopiuj regułę powiązania  
- Skopiuj pełnotekstowy indeksów  
- Skopiuj XML XSD  
- Skopiuj indeksów XML  
- Skopiuj uprawnienia  
- Skopiuj indeksów przestrzennych  
- Skopiuj Filtrowane indeksy  
- Skopiuj atrybut kompresji danych  
- Skopiuj atrybut zestawu kolumn rozrzedzonych  
- Konwertuj filestream do typów danych maksymalna  
- Konwertuj hierarchyid maksymalna liczba typów danych  
- Konwertuj przestrzenne maksymalna liczba typów danych  
- Kopiowanie właściwości rozszerzone  
- Skopiuj uprawnienia  

### <a name="limitations-to-be-determined"></a>Ograniczenia ustalonym

- Kopiowanie sortowania  
- Wykonywanie w ramach transakcji serializacji PS  

## <a name="examples"></a>Przykłady

Tworzenie publikacji i subskrypcji wypychanej. Aby uzyskać więcej informacji, zobacz:
  
- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Tworzenie subskrypcji wypychanych](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) przy użyciu nazwy serwera Azure SQL Database jako subskrybenta (na przykład **N'azuresqldbdns.database.windows.net "** ) i nazwę bazy danych Azure SQL jako docelowej bazy danych (w przypadku przykład **AdventureWorks**).  

## <a name="see-also"></a>Zobacz też  

- [Replikacja transakcyjna](sql-database-managed-instance-transactional-replication.md)
- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Tworzenie subskrypcji wypychanej](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typy replikacji](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorowanie (replikacja)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicjowanie subskrypcji](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
