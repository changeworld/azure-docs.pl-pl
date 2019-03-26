---
title: Konfigurowanie replikacji w bazie danych wystąpienia zarządzanego usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Informacje na temat konfigurowania replikacji transakcyjnej w bazie danych wystąpienia zarządzanego usługi Azure SQL Database
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
ms.date: 02/07/2019
ms.openlocfilehash: b20a119a69ac796bc9ea85083d335f0a7d2fdf2d
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417959"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Konfigurowanie replikacji w bazie danych wystąpienia zarządzanego usługi Azure SQL Database

Replikacja transakcyjna umożliwia replikowanie danych do wystąpienia zarządzanego usługi Azure SQL Database bazy danych z bazy danych programu SQL Server lub innego wystąpienia bazy danych. Replikacja transakcyjna umożliwia również wypychanie zmian wprowadzonych w bazie danych wystąpienia w wystąpieniu zarządzanym usługi Azure SQL Database z bazą danych programu SQL Server w jednej bazie danych Azure SQL Database — do puli bazy danych w puli elastycznej bazy danych SQL Azure. Replikacja transakcyjna jest w publicznej wersji zapoznawczej na [wystąpienie zarządzane usługi Azure SQL Database](sql-database-managed-instance.md). Wystąpienie zarządzane umożliwia hostowanie bazy danych wydawcy dystrybutora i subskrybenta. Zobacz [konfiguracji replikacji transakcyjnej](sql-database-managed-instance-transactional-replication.md#common-configurations) dla dostępnymi konfiguracjami.

## <a name="requirements"></a>Wymagania

Konfigurowanie wystąpienia zarządzanego do działania jako wydawca lub dystrybutora wymaga:

- Czy wystąpienia zarządzanego jest aktualnie nie uczestniczy w relacji replikacji geograficznej.

   >[!NOTE]
   >Pojedynczych baz danych i baz danych w puli usługi Azure SQL Database może być tylko subskrybentów.

- Wszystkie wystąpienia zarządzane musi być w tej samej sieci wirtualnej.

- Połączenie korzysta z uwierzytelniania programu SQL między uczestnikami replikacji.

- Udział konta usługi Azure Storage dla replikacji katalogu roboczego.

- Port 445 (ruch wychodzący protokołu TCP) musi być otwarty w regułach zabezpieczeń w podsieci wystąpienia zarządzanego dostępu do udziału plików platformy Azure

## <a name="features"></a>Funkcje

Obsługuje:

- Replikacji transakcyjnej i migawki różnych lokalnego programu SQL Server i wystąpienia zarządzanego usługi Azure SQL Database.
- Subskrybenci mogą znajdować się w lokalnych baz danych programu SQL Server, pojedynczej bazy danych/wystąpienia zarządzane przez usługę Azure SQL Database lub bazy danych w puli w elastycznych pulach usługi Azure SQL Database.
- Jednokierunkowa lub replikację dwukierunkową.

Następujące funkcje nie są obsługiwane w wystąpieniu zarządzanym usługi Azure SQL Database:

- Aktualizowalne subskrypcje.
- [Replikacji dla aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) i [automatyczny tryb failover grupy](sql-database-auto-failover-group.md) nie powinny być używane, jeśli skonfigurowano replikacji transakcyjnej.

## <a name="configure-publishing-and-distribution-example"></a>Skonfiguruj publikowanie i dystrybucję przykład

1. [Tworzenie wystąpienia zarządzanego usługi Azure SQL Database](sql-database-managed-instance-create-tutorial-portal.md) w portalu.
2. [Tworzenie konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) dla katalogu roboczego.

   Pamiętaj skopiować kluczy magazynu. Zobacz [wyświetlanie i kopiowanie kluczy dostępu do magazynu](../storage/common/storage-account-manage.md#access-keys
).
3. Utwórz wystąpienie bazy danych dla danego wydawcy.

   W poniższych skryptów przykładzie Zastąp `<Publishing_DB>` o nazwie wystąpienia bazy danych.

4. Utwórz użytkownika bazy danych przy użyciu uwierzytelniania SQL dystrybutora. Użyj bezpiecznego hasła.

   W poniższych skryptów przykład `<SQL_USER>` i `<PASSWORD>` przy użyciu tego konta serwera SQL bazy danych użytkownika i hasło.

5. [Nawiąż połączenie z wystąpieniem zarządzanym bazy danych SQL](sql-database-connect-query-ssms.md).

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
   
## <a name="see-also"></a>Zobacz też

- [Replikacja transakcyjna](sql-database-managed-instance-transactional-replication.md)
- [Co to jest wystąpienie zarządzane?](sql-database-managed-instance.md)
