---
title: Artykuł na temat znanych problemów/migracja ograniczeń dotyczących migracji online do usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Więcej informacji na temat znanych problemów/migracja ograniczeń dotyczących migracji online do usługi Azure SQL Database.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: a8d91c0410d2a59e4b22866aa48d36c026f3ee8e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956299"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-db"></a>Znane problemy dotyczące/migracja ograniczeń dotyczących migracji online do usługi Azure SQL DB

Poniżej opisano znane problemy i ograniczenia związane z usługą online migracje z programu SQL Server do usługi Azure SQL Database.

### <a name="migration-of-temporal-tables-not-supported"></a>Migracja tabele danych czasowych nie jest obsługiwane

**Objaw**

Jeśli źródłowej bazy danych składa się z co najmniej jedna tabela danych czasowych, migracji bazy danych nie powiodło się podczas operacji "pełne ładowanie danych" i może zostać wyświetlony następujący komunikat:

{"resourceId": "/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType": "Błąd migracji bazy danych", "errorEvents": "[" funkcje przechwytywania nie można ustawić. RetCode: Wartość SQL_ERROR SqlState: 42000 NativeError: 13570 komunikatu: [Microsoft] [SQL Server Native Client 11.0] [SQL Server] użycie replikacji nie jest obsługiwane w tabeli danych czasowych z systemową obsługą "[aplikacji. Miast] "wiersza: 1 kolumna: wartość -1"] "}
 
 ![Przykład błędów w tabeli danych czasowych](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Obejście problemu**

1. Znajdź tabele danych czasowych w schemacie źródła przy użyciu poniższe zapytanie.
     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```
2. Wyklucz te tabele z **Konfiguruj ustawienia migracji** bloku, w którym określisz tabel dla migracji.

3. Należy ponownie uruchomić działanie migracji.

**Zasoby**

Aby uzyskać więcej informacji, zobacz artykuł [tabele danych czasowych](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).
 
### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Migracja tabel obejmuje co najmniej jedną kolumnę o typie danych identyfikator hierarchii

**Objaw**

Może zostać wyświetlony sugerowanie "ntext jest niezgodny z hierarchyid" podczas operacji "pełne ładowanie danych" wyjątku SQL:
     
![przykład błędy hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Obejście problemu**

1. Znajdź tabel użytkownika, które zawierają kolumny z typem danych identyfikator hierarchii przy użyciu poniższe zapytanie.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ``` 

 2. Wyklucz te tabele z **Konfiguruj ustawienia migracji** bloku, w którym określisz tabel dla migracji.

 3. Należy ponownie uruchomić działanie migracji.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Błędy migracji z różnych naruszenia integralności za pomocą wyzwalaczy active w schemacie podczas "pełne ładowanie danych" lub "Przyrostowa synchronizacja danych"

**Obejście problemu**
1. Znajdź wyzwalacze, które są aktualnie aktywne w źródłowej bazy danych przy użyciu poniższe zapytanie:
     ```
     select * from sys.triggers where is_disabled =0
     ```
2. Wyłącz Wyzwalacze w źródłowej bazy danych przy użyciu z krokami opisanymi w artykule [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Uruchom ponownie działanie migracji.

### <a name="support-for-lob-data-types"></a>Obsługa typów danych biznesowych

**Objaw**

Jeśli długość kolumny duży obiekt (LOB) jest większy niż 32 KB, dane mogą uzyskać obcięte do miejsca docelowego. Możesz sprawdzić długość kolumny obiektów LOB przy użyciu poniższe zapytanie: 

``` 
SELECT max(len(ColumnName)) as LEN from TableName
```

**Obejście problemu**

Jeśli masz kolumną LOB, który jest większy niż 32 KB, skontaktuj się z zespołu inżynieryjnego w [ dmsfeedback@microsoft.com ](mailto:dmsfeedback@microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problemy związane z kolumn sygnatur czasowych

**Objaw**

Usługa DMS nie migrować wartość sygnatury czasowej źródła; Zamiast tego usługa DMS generuje nową wartość sygnatury czasowej w tabeli docelowej.

**Obejście problemu**

Usługa DMS do migracji wartości dokładny znacznik czasu: przechowywane w tabeli źródłowej, należy skontaktować się z zespołu inżynieryjnego w [ dmsfeedback@microsoft.com ](mailto:dmsfeedback@microsoft.com).

### <a name="data-migration-errors-do-not-provide-additional-details-on-the-database-detailed-status-blade"></a>Błędy migracji danych nie są oferowane dodatkowe informacje w bloku szczegółowy stan bazy danych.

**Objaw**

Jeśli wystąpią błędy migracji w widoku stanu Szczegóły bazy danych, wybierając **błędy migracji danych** łącze na Wstążce najważniejsze nie mogą zawierać dodatkowe szczegóły specyficzne dla błędów migracji.

![błędy migracji danych nie przykład szczegółów](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Obejście problemu**

Aby przejść do szczegółów błąd, wykonaj poniższe kroki.

1. Zamknij blok szczegółowy stan bazy danych, aby wyświetlić ekran działanie migracji.

     ![ekran działanie migracji](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Wybierz **patrz szczegóły błędu** Aby wyświetlić określone komunikaty o błędach, które ułatwiają rozwiązywanie problemów z błędami migracji.
