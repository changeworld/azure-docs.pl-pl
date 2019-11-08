---
title: Replikacja
description: Informacje o korzystaniu z SQL Server replikacji z Azure SQL Database pojedynczymi bazami danych i bazami danych w pulach elastycznych
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
ms.openlocfilehash: ac198ed8eac6221831fbb280129b76e5fa4e3413
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73815776"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replikacja do SQL Database jednej i puli baz danych

Replikację SQL Server można skonfigurować na pojedynczej i w puli bazach danych na [serwerze SQL Database](sql-database-servers.md) w Azure SQL Database.  

## <a name="supported-configurations"></a>**Obsługiwane konfiguracje:**
  
- SQL Server może być wystąpieniem SQL Server działającego lokalnie lub wystąpieniem SQL Server uruchomionego na maszynie wirtualnej platformy Azure w chmurze. Aby uzyskać więcej informacji, zobacz [SQL Server na platformie Azure — omówienie Virtual Machines](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- Baza danych SQL Azure musi być subskrybentem wypychania wydawcy SQL Server.  
- Bazy danych dystrybucji i agentów replikacji nie można umieścić w usłudze Azure SQL Database.  
- Migawki i jednokierunkowa replikacja transakcyjna są obsługiwane. Replikacja transakcyjna równorzędna i replikacja scalająca nie są obsługiwane.
- Replikacja jest dostępna dla publicznej wersji zapoznawczej Azure SQL Database wystąpienia zarządzanego. Wystąpienie zarządzane może hostować bazy danych wydawcy, dystrybutora i subskrybentów. Aby uzyskać więcej informacji, zobacz [replikacja z wystąpieniem zarządzanym SQL Database](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Wersje  

- Wydawca i dystrybutor muszą mieć co najmniej jedną z następujących wersji:  
- SQL Server 2017 (14. x)
- SQL Server 2016 (13. x)
- SQL Server 2014 (12. x) CU3 SP1
- SQL Server 2014 (12. x) RTM CU10
- SQL Server 2012 (11. x) SP2 CU8 lub SP3
- Próba skonfigurowania replikacji przy użyciu starszej wersji może spowodować wystąpienie błędu MSSQL_REPL20084 (proces nie mógł nawiązać połączenia z subskrybentem) i MSSQL_REPL40532 (nie można otworzyć \<nazwy serwera > żądanego podczas logowania. Logowanie nie powiodło się.  
- Aby korzystać ze wszystkich funkcji Azure SQL Database, musisz używać najnowszych wersji narzędzi [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  
  
## <a name="remarks"></a>Uwagi

- Replikację można skonfigurować za pomocą [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub przez wykonanie instrukcji języka Transact-SQL na wydawcy. Nie można skonfigurować replikacji przy użyciu Azure Portal.  
- Replikacja może być SQL Server używana tylko do nawiązywania połączeń z bazą danych Azure SQL.
- Zreplikowane tabele muszą mieć klucz podstawowy.  
- Musisz mieć istniejącą subskrypcję platformy Azure.  
- Subskrybent usługi Azure SQL Database może znajdować się w dowolnym regionie.  
- Jedna publikacja na SQL Server może obsługiwać zarówno Azure SQL Database, jak i SQL Server (lokalnie i SQL Server w ramach maszyn wirtualnych platformy Azure).  
- Zarządzanie replikacją, monitorowanie i rozwiązywanie problemów należy wykonać z SQL Server lokalnych.  
- Obsługiwane są tylko subskrypcje wypychane do Azure SQL Database.  
- W **sp_addsubscription** dla SQL Database obsługiwane są tylko `@subscriber_type = 0`.  
- Azure SQL Database nie obsługuje dwukierunkowej, natychmiastowej lub aktualizowalnej replikacji równorzędnej.

## <a name="replication-architecture"></a>Architektura replikacji  

![replikacja do bazy danych SQL](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scenariusze  

### <a name="typical-replication-scenario"></a>Typowy scenariusz replikacji  

1. Utwórz publikację z replikacją transakcyjną w lokalnej bazie danych SQL Server.  
2. Na SQL Server lokalnym Użyj **Kreatora nowej subskrypcji** lub instrukcji języka Transact-SQL, aby utworzyć wypychanie do subskrypcji Azure SQL Database.  
3. W przypadku baz danych o pojedynczej i puli w Azure SQL Database początkowy zestaw danych jest migawką utworzoną przez agenta migawek i dystrybuowaną i stosowaną przez agenta dystrybucji. W przypadku bazy danych wystąpienia zarządzanego można także użyć kopii zapasowej bazy danych do wypełniania bazy danych subskrybenta.

### <a name="data-migration-scenario"></a>Scenariusz migracji danych  

1. Replikacja transakcyjna umożliwia replikowanie danych z lokalnej bazy danych SQL Server do Azure SQL Database.  
2. Przekieruj klienta lub aplikacje warstwy środkowej, aby zaktualizować kopię bazy danych Azure SQL Database.  
3. Zatrzymaj aktualizację wersji SQL Server tabeli i Usuń publikację.  

## <a name="limitations"></a>Ograniczenia

Następujące opcje nie są obsługiwane w przypadku subskrypcji Azure SQL Database:

- Kopiuj skojarzenie grup plików  
- Kopiuj schematy partycjonowania tabel  
- Kopiuj schematy partycjonowania indeksów  
- Kopiowanie statystyk zdefiniowanych przez użytkownika  
- Kopiuj domyślne powiązania  
- Kopiuj powiązania reguły  
- Kopiuj indeksy pełnotekstowy  
- Kopiuj plik XSD XML  
- Kopiuj indeksy XML  
- Uprawnienia do kopiowania  
- Kopiuj indeksy przestrzenne  
- Kopiuj filtrowane indeksy  
- Kopiuj atrybut kompresji danych  
- Kopiuj atrybut kolumny rozrzedzonej  
- Konwertuj dane FILESTREAM na maksymalne typy danych  
- Konwertuj hierarchyid na maksymalną liczbę typów danych  
- Konwertuj dane przestrzenne na wartości typu MAX  
- Kopiuj właściwości rozszerzone  
- Uprawnienia do kopiowania  

### <a name="limitations-to-be-determined"></a>Ograniczenia do określenia

- Kopiuj sortowanie  
- Wykonywanie w serializowanej transakcji programu SP  

## <a name="examples"></a>Przykłady

Utwórz publikację i subskrypcję wypychaną. Aby uzyskać więcej informacji, zobacz:
  
- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Utwórz subskrypcję wypychaną](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) przy użyciu nazwy serwera Azure SQL Database jako subskrybenta (na przykład **N'azuresqldbdns. Database. Windows. NET**) i nazwy bazy danych SQL Azure jako docelowej bazy danych (na przykład **AdventureWorks** ).  

## <a name="see-also"></a>Zobacz też  

- [Replikacja transakcyjna](sql-database-managed-instance-transactional-replication.md)
- [Tworzenie publikacji](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Tworzenie subskrypcji wypychanej](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication (Typy replikacji)](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorowanie (replikacja)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicjowanie subskrypcji](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
