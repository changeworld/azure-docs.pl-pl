---
title: Wystąpienie zarządzane w replikacji za pomocą usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o za pomocą replikacji programu SQL Server za pomocą wystąpienia zarządzanego Azure SQL Database
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
ms.date: 01/11/2019
ms.openlocfilehash: 5db9e1f348fed9592d33e9240180db7f2667bb56
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244549"
---
# <a name="replication-with-sql-database-managed-instance"></a>Wystąpienie zarządzane w replikacji z bazy danych SQL

Replikacja jest dostępna w publicznej wersji zapoznawczej na [wystąpienia zarządzanego Azure SQL Database](sql-database-managed-instance.md). Wystąpienie zarządzane umożliwia hostowanie bazy danych wydawcy dystrybutora i subskrybenta.

## <a name="common-configurations"></a>Typowe konfiguracje

Ogólnie rzecz biorąc wydawcą i dystrybutorem muszą być w chmurze lub lokalnie. Obsługiwane są następujące konfiguracje:

- **Wydawcy o dystrybutor lokalny dla wystąpienia zarządzanego**

   ![Replication-with-Azure-SQL-DB-Single-Managed-instance-Publisher-distributor](./media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

   Bazy danych wydawcą i dystrybutorem są konfigurowane w pojedynczym wystąpieniu zarządzanym.

- **Wydawcy o dystrybutorze zdalnym na wystąpienie zarządzane**

   ![Replication-with-Azure-SQL-DB-separate-Managed-instances-Publisher-distributor](./media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

   Wydawcą i dystrybutorem są konfigurowane na dwa wystąpienia zarządzanego. W tej konfiguracji:

  - Oba wystąpienia zarządzanego znajdują się w tej samej sieci wirtualnej.

  - Oba wystąpienia zarządzanego znajdują się w tej samej lokalizacji.

- **Wydawcą i dystrybutorem lokalnie przy użyciu subskrybenta na wystąpienie zarządzane**

   ![Replication-from-on-premises-to-Azure-SQL-DB-Subscriber](./media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)

   W tej konfiguracji usługi Azure SQL database jest subskrybentem. Ta konfiguracja obsługuje migracji ze środowiska lokalnego na platformę Azure. W roli subskrybenta bazy danych SQL nie wymaga wystąpienia zarządzanego, jednak wystąpienie zarządzane bazy danych SQL można użyć jako krok w procesie migracji ze środowiska lokalnego do platformy Azure. Aby uzyskać więcej informacji na temat subskrybentów usługi Azure SQL Database, zobacz [replikacji bazy danych SQL](replication-to-sql-database.md).

## <a name="requirements"></a>Wymagania

Wymaga wydawcą i dystrybutorem w usłudze Azure SQL Database:

- Wystąpienie zarządzane bazy danych Azure SQL.

   >[!NOTE]
   >Azure baz danych SQL, które nie zostały skonfigurowane za pomocą wystąpienia zarządzanego można tylko subskrybentów.

- Wszystkie wystąpienia programu SQL Server muszą znajdować się w tej samej sieci wirtualnej.

- Połączenie korzysta z uwierzytelniania programu SQL między uczestnikami replikacji.

- Udział konta usługi Azure Storage dla replikacji katalogu roboczego.

- Port 445 (ruch wychodzący protokołu TCP) musi być otwarty w regułach zabezpieczeń w podsieci wystąpienia zarządzanego dostępu do udziału plików platformy Azure

## <a name="features"></a>Funkcje

Obsługuje:

- Mieszany replikacji transakcyjnej i migawki lokalne i wystąpień wystąpienia zarządzanego Azure SQL Database.

- Subskrybenci mogą być w środowisku lokalnym, pojedynczych baz danych w usłudze Azure SQL Database lub bazy danych w puli w elastycznych pulach usługi Azure SQL Database.

- Jednokierunkowa lub replikację dwukierunkową

## <a name="configure-publishing-and-distribution-example"></a>Skonfiguruj publikowanie i dystrybucję przykład

1. [Tworzenie wystąpienia usługi Azure SQL Database Managed](sql-database-managed-instance-create-tutorial-portal.md) w portalu.
2. [Tworzenie konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) dla katalogu roboczego.

   Pamiętaj skopiować kluczy magazynu. Zobacz [wyświetlanie i kopiowanie kluczy dostępu do magazynu](../storage/common/storage-account-manage.md#access-keys
).
3. Tworzenie bazy danych wydawcy.

   W poniższych skryptów przykładzie Zastąp `<Publishing_DB>` o nazwie tej bazy danych.

4. Utwórz użytkownika bazy danych przy użyciu uwierzytelniania SQL dystrybutora. Użyj bezpiecznego hasła.

   W poniższych skryptów przykład `<SQL_USER>` i `<PASSWORD>` przy użyciu tego konta serwera SQL bazy danych użytkownika i hasło.

5. [Nawiąż połączenie z wystąpieniem zarządzanym bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms).

6. Uruchom następujące zapytanie, aby dodać dystrybutora i bazy danych dystrybucji.

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. Aby skonfigurować wydawcy do korzystania z bazy danych dystrybucji określonego, zaktualizuj i uruchom następujące zapytanie.

   Zastąp `<SQL_USER>` i `<PASSWORD>` przy użyciu konta programu SQL Server i hasła.

   Zastąp `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>` o wartości konta magazynu.  

   Zastąp `<STORAGE_CONNECTION_STRING>` przy użyciu parametrów połączenia z **klucze dostępu** kartę konta usługi Microsoft Azure storage.

   Po zaktualizowaniu następujące zapytanie, należy ją uruchomić.

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

8. Skonfiguruj wydawcę pod kątem replikacji.

    W następującym zapytaniu Zastąp `<Publishing_DB>` o nazwie bazy danych wydawcy.

    Zastąp `<Publication_Name>` nazwą dla publikacji.

    Zastąp `<SQL_USER>` i `<PASSWORD>` przy użyciu konta programu SQL Server i hasła.

    Po zaktualizowaniu zapytanie, uruchom go, aby utworzyć publikacji.

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

9. Dodaj artykuł, subskrypcji i agentów subskrypcji wypychanych.

   Aby dodać te obiekty, zaktualizuj poniższy skrypt.

   - Zastąp `<Object_Name>` nazwą obiektu publikacji.
   - Zastąp `<Object_Schema>` o nazwie schematu źródła.
   - Zastąp innych parametrów w nawiasy ostre `<>` do odpowiadają wartościom w wcześniejsze skrypty.

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   GO
   ```

## <a name="limitations"></a>Ograniczenia

Następujące funkcje nie są obsługiwane:

- Aktualizowalne subskrypcje

- Replikacji dla aktywnej replikacji geograficznej

## <a name="see-also"></a>Zobacz też

- [Co to jest wystąpienie zarządzane?](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)
