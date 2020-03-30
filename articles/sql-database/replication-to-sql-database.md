---
title: Replikacja
description: Dowiedz się więcej o używaniu replikacji programu SQL Server z pojedynczymi bazami danych i bazami danych usługi Azure SQL Database w pulach elastycznych
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: f718bc17b987926f4324635f096d5983acdb63fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256473"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replikacja do pojedynczych i puli baz danych bazy danych SQL

Replikację programu SQL Server można skonfigurować do pojedynczych i puli baz danych na [serwerze bazy danych SQL](sql-database-servers.md) w bazie danych SQL.  

## <a name="supported-configurations"></a>**Obsługiwane konfiguracje:**
  
- Program SQL Server może być wystąpieniem programu SQL Server z uruchomionym lokalnie lub wystąpieniem programu SQL Server uruchomionym na maszynie wirtualnej platformy Azure w chmurze. Aby uzyskać więcej informacji, zobacz [SQL Server na maszynach wirtualnych platformy Azure.](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)  
- Baza danych SQL platformy Azure musi być subskrybentem wypychanym wydawcy programu SQL Server.  
- Nie można umieścić bazy danych dystrybucji i agentów replikacji w bazie danych SQL platformy Azure.  
- Migawka i replikacja transakcyjna jednokierunkowa są obsługiwane. Replikacja transakcyjna typu peer-to-peer i replikacja scalania nie są obsługiwane.
- Replikacja jest dostępna w publicznej wersji zapoznawczej w wystąpieniu zarządzanym usługi Azure SQL Database. Wystąpienie zarządzane może obsługiwać bazy danych wydawców, dystrybutorów i subskrybentów. Aby uzyskać więcej informacji, zobacz [Replikacja z wystąpieniem zarządzanym bazy danych SQL](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Wersje  

Lokalni wydawcy i dystrybutorzy programu SQL Server muszą używać (co najmniej) jednej z następujących wersji:  

- SQL Server 2016 i więcej
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) lub [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) lub [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Próba skonfigurowania replikacji przy użyciu nieobsługiwanej wersji może spowodować numer błędu MSSQL_REPL20084 (Proces nie może połączyć się z \<subskrybentem.) i MSSQL_REPL40532 (Nie można otworzyć nazwę serwera> żądane przez logowania. Logowanie nie powiodło się.).  

Aby korzystać ze wszystkich funkcji usługi Azure SQL Database, należy używać najnowszych wersji [programu SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i SQL Server Data [Tools.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)  

  
## <a name="remarks"></a>Uwagi

- Replikację można skonfigurować przy użyciu [programu SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub wykonując instrukcje Transact-SQL na wydawcy. Nie można skonfigurować replikacji przy użyciu witryny Azure portal.  
- Replikacja może używać tylko identyfikatorów logowania uwierzytelniania programu SQL Server do łączenia się z bazą danych SQL platformy Azure.
- Tabele replikowane muszą mieć klucz podstawowy.  
- Musisz mieć istniejącą subskrypcję platformy Azure.  
- Subskrybent bazy danych SQL platformy Azure może znajdować się w dowolnym regionie.  
- Pojedyncza publikacja na programie SQL Server może obsługiwać subskrybentów bazy danych SQL i SQL Server (lokalnego i sql servera na maszynie wirtualnej platformy Azure).  
- Zarządzanie replikacją, monitorowanie i rozwiązywanie problemów muszą być wykonywane z lokalnego programu SQL Server.  
- Obsługiwane są tylko subskrypcje wypychane do usługi Azure SQL Database.  
- Tylko `@subscriber_type = 0` jest obsługiwany w **sp_addsubscription** dla bazy danych SQL.  
- Usługa Azure SQL Database nie obsługuje replikacji dwukierunkowej, natychmiastowej, możliwej do aktualizacji ani replikacji równorzędnej.

## <a name="replication-architecture"></a>Architektura replikacji  

![replikacja do bazy danych sql](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scenariusze  

### <a name="typical-replication-scenario"></a>Typowy scenariusz replikacji  

1. Utwórz publikację replikacji transakcyjnej w lokalnej bazie danych programu SQL Server.  
2. W lokalnym programie SQL Server użyj **Kreatora nowej subskrypcji** lub instrukcji Transact-SQL, aby utworzyć wypychanie do subskrypcji bazy danych SQL Azure.  
3. W przypadku pojedynczych i ujękowych baz danych w bazie danych SQL azure początkowy zestaw danych jest migawką, która jest tworzona przez agenta migawki i rozpowszechniana i stosowana przez agenta dystrybucji. Za pomocą bazy danych wystąpienia zarządzanego można również użyć kopii zapasowej bazy danych do rozstawienia bazy danych subskrybenta.

### <a name="data-migration-scenario"></a>Scenariusz migracji danych  

1. Replikacja transakcyjna służy do replikowania danych z lokalnej bazy danych programu SQL Server do bazy danych SQL Database platformy Azure.  
2. Przekieruj aplikacje klienckie lub średniej warstwy, aby zaktualizować kopię bazy danych SQL platformy Azure.  
3. Zatrzymaj aktualizowanie wersji tabeli programu SQL Server i usuń publikację.  

## <a name="limitations"></a>Ograniczenia

Następujące opcje nie są obsługiwane dla subskrypcji usługi Azure SQL Database:

- Kopiowanie skojarzenia grup plików  
- Kopiowanie schematów partycjonowania tabel  
- Kopiowanie schematów partycjonowania indeksu  
- Kopiowanie statystyk zdefiniowanych przez użytkownika  
- Kopiowanie powiązań domyślnych  
- Kopiowanie powiązań reguł  
- Kopiowanie indeksów pełnotekstowych  
- Kopiowanie XSD XML  
- Kopiowanie indeksów XML  
- Uprawnienia do kopiowania  
- Kopiowanie indeksów przestrzennych  
- Kopiowanie filtrowanych indeksów  
- Kopiowanie atrybutu kompresji danych  
- Kopiowanie atrybutu kolumny rozrzedzone  
- Konwertowanie strumienia plików na typy danych MAX  
- Konwertowanie typów danych hierarchyid na MAX  
- Konwertowanie typów danych przestrzennych na MAX  
- Kopiowanie właściwości rozszerzonych  
- Uprawnienia do kopiowania  

### <a name="limitations-to-be-determined"></a>Ograniczenia, które należy określić

- Kopiowanie sortowania  
- Wykonanie w seryjnej transakcji SP  

## <a name="examples"></a>Przykłady

Tworzenie publikacji i subskrypcji wypychanej. Aby uzyskać więcej informacji, zobacz:
  
- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Utwórz subskrypcję wypychaną](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) przy użyciu nazwy serwera bazy danych Azure SQL database jako subskrybenta (na przykład **N'azuresqldbdns.database.windows.net'**) i nazwy bazy danych SQL platformy Azure jako docelowej bazy danych (na przykład **AdventureWorks).**  

## <a name="see-also"></a>Zobacz też  

- [Replikacja transakcyjna](sql-database-managed-instance-transactional-replication.md)
- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Tworzenie subskrypcji wypychanej](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication (Typy replikacji)](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorowanie (replikacja)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicjowanie subskrypcji](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
