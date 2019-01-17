---
title: Konfigurowanie replikacji w wystąpieniu zarządzanym usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Informacje na temat konfigurowania replikacji transakcyjnej w wystąpieniu zarządzanym usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 01/16/2019
ms.openlocfilehash: 568b239cf41c802cc5d25b638f6d1501f58eccdf
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360092"
---
# <a name="configure-replication-in-azure-sql-database-managed-instance"></a>Konfigurowanie replikacji w wystąpieniu zarządzanym usługi Azure SQL Database

Replikacja transakcyjna umożliwia replikowanie danych z baz danych programu SQL Server lub wystąpienia zarządzanego Azure SQL Database do wystąpienia zarządzanego lub wypychania zmian w bazach danych w wystąpieniu zarządzanym innych programu SQL Server, pojedynczej bazy danych Azure lub innych Wystąpienie zarządzane. Replikacja jest w publicznej wersji zapoznawczej na [wystąpienia zarządzanego Azure SQL Database](sql-database-managed-instance.md). Wystąpienie zarządzane umożliwia hostowanie bazy danych wydawcy dystrybutora i subskrybenta. Zobacz [konfiguracji replikacji transakcyjnej](sql-database-managed-instance-transactional-replication.md#common-configurations) dla dostępnymi konfiguracjami.

## <a name="requirements"></a>Wymagania

Wymaga wydawcą i dystrybutorem w usłudze Azure SQL Database:

- Usługa Azure wystąpienia zarządzanego SQL Database, która nie znajduje się w konfiguracji Geo-DR.

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

- Jednokierunkowa lub replikację dwukierunkową.

Następujące funkcje nie są obsługiwane:

- Aktualizowalne subskrypcje.

- Replikacja aktywnej replikacji geograficznej.

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
   
## <a name="see-also"></a>Zobacz też

- [Replikacja transakcyjna](sql-database-managed-instance-transactional-replication.md)
- [Co to jest wystąpienie zarządzane?](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)
