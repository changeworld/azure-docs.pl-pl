---
title: 'Znane problemy: Migracje online do bazy danych SQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się więcej o znanych problemach/ograniczeniach migracji z migracjami online do usługi Azure SQL Database przy użyciu usługi migracji bazy danych Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: e7efdb7244e2c7e4651a4507b538123f8d320c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650779"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Znane problemy/ograniczenia migracji z migracjami online do bazy danych SQL usługi Azure

Znane problemy i ograniczenia związane z migracjami online z programu SQL Server do bazy danych SQL Azure są opisane poniżej.

> [!IMPORTANT]
> W przypadku migracji online programu SQL Server do bazy danych SQL Azure migracja typów danych SQL_variant nie jest obsługiwana.

### <a name="migration-of-temporal-tables-not-supported"></a>Migracja tabel czasowych nie jest obsługiwana

**Objawem**

Jeśli źródłowa baza danych składa się z co najmniej jednej tabely czasowej, migracja bazy danych kończy się niepowodzeniem podczas operacji "Pełne ładowanie danych" i może zostać wyświetlony następujący komunikat:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Przykład błędów tabeli czasowej](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Obejście**

Wykonaj następujące kroki.

1. Znajdź tabele czasowe w schemacie źródłowym przy użyciu poniższej kwerendy.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Wyklucz te tabele z bloku **Konfigurowanie ustawień migracji,** w którym określa się tabele migracji.

3. Uruchom ponownie działanie migracji.

**Zasoby**

Aby uzyskać więcej informacji, zobacz artykuł [Tabele czasowe](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Migracja tabel zawiera jedną lub więcej kolumn z typem danych hierarchyid

**Objawem**

Podczas operacji "Pełne ładowanie danych" może zostać wyświetlony wyjątek SQL sugerujący, że "ntext jest niezgodny z hierarchą":

![przykład błędów hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Obejście**

Wykonaj następujące kroki.

1. Znajdź tabele użytkowników, które zawierają kolumny z typem danych hierarchyid przy użyciu poniższej kwerendy.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Wyklucz te tabele z bloku **Konfigurowanie ustawień migracji,** w którym określa się tabele migracji.

3. Uruchom ponownie działanie migracji.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Błędy migracji z różnymi naruszeniami integralności z aktywnymi wyzwalaczami w schemacie podczas "Pełnego ładowania danych" lub "Przyrostowa synchronizacja danych"

**Obejście**

Wykonaj następujące kroki.

1. Znajdź wyzwalacze, które są obecnie aktywne w źródłowej bazie danych, korzystając z poniższej kwerendy:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Wyłącz wyzwalacze w źródłowej bazie danych, wykonując czynności opisane w artykule [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Uruchom ponownie działanie migracji.

### <a name="support-for-lob-data-types"></a>Obsługa typów danych LOB

**Objawem**

Jeśli długość kolumny Lob (Large Object) jest większa niż 32 KB, dane mogą zostać obcięty na miejsce docelowe. Długość kolumny LOB można sprawdzić za pomocą poniższej kwerendy:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Obejście**

Jeśli masz kolumnę LOB, która jest większa niż 32 KB, skontaktuj się z zespołem inżynierów w [Ask Azure Database Migracji](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problemy z kolumnami sygnatury czasowej

**Objawem**

Usługa migracji bazy danych platformy Azure nie migruje źródłowej wartości sygnatury czasowej; Zamiast tego usługa migracji bazy danych platformy Azure generuje nową wartość sygnatury czasowej w tabeli docelowej.

**Obejście**

Jeśli potrzebujesz usługi migracji bazy danych platformy Azure do migracji dokładnej wartości sygnatury czasowej przechowywanej w tabeli źródłowej, skontaktuj się z zespołem inżynierów pod [adresem Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Błędy migracji danych nie zawierają dodatkowych szczegółów w bloku stanu szczegółowego bazy danych

**Objawem**

W przypadku wystąpienia błędów migracji w widoku stanu Szczegóły baz danych wybranie łącza **Błędy migracji danych** na górnym wstążce może nie zawierać dodatkowych szczegółów specyficznych dla błędów migracji.

![błędy migracji danych brak przykładu szczegółów](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Obejście**

Aby uzyskać szczegółowe informacje o konkretnych błędach, należy wykonać następujące kroki.

1. Zamknij blok stanu szczegółów bazy danych, aby wyświetlić ekran działania migracji.

     ![ekran aktywności migracji](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Wybierz **pozycję Zobacz szczegóły błędów,** aby wyświetlić określone komunikaty o błędach ułatwiające rozwiązywanie problemów z błędami migracji.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Typ danych geografii nie jest obsługiwany w migracji online usługi SQLDB

**Objawem**

Migracja kończy się niepowodzeniem, a komunikat o błędzie zawierający następujący tekst:

     “** encountered a fatal error”, "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode."

**Obejście**

Usługa migracji bazy danych Azure obsługuje typ danych Geografia dla migracji w trybie offline do bazy danych SQL Azure, w przypadku migracji online typ danych geografii nie jest obsługiwany. Spróbuj alternatywnych metod, aby zmienić typ danych u źródła do obsługiwanego typu przed podjęciem próby użycia usługi migracji usługi Azure Database do migracji online tej bazy danych.

### <a name="supported-editions"></a>Obsługiwane wersje

**Objawem**

Migracja kończy się niepowodzeniem, a komunikat o błędzie zawierający następujący tekst:

    Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].

**Obejście**

Obsługa migracji online do usługi Azure SQL Database przy użyciu usługi migracji bazy danych Azure jest dostępna tylko w wersjach Enterprise, Standard i Developer. Upewnij się, że używasz obsługiwanej wersji przed rozpoczęciem procesu migracji.
