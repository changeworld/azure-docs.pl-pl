---
title: Artykuł na temat znanych problemów/migracja ograniczenia online migracji do usługi Azure Database for MySQL | Dokumentacja firmy Microsoft
description: Więcej informacji na temat znanych problemów/migracja ograniczenia online migracji do usługi Azure Database for MySQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 0641545c10d7f59cb1874659eae9c7e7bf65932e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532263"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-mysql"></a>Ograniczenia znanych problemów/migracja online migracji do usługi Azure DB dla MySQL

W poniższych sekcjach opisano znane problemy i ograniczenia związane z usługą online migracji z MySQL do usługi Azure Database for MySQL. 

## <a name="online-migration-configuration"></a>Konfiguracja migracji online
- Źródłowy serwer MySQL Server musi być w wersji 5.6.35, 5.7.18 lub nowszej
- Usługa Azure Database for MySQL obsługuje:
    - MySQL community edition
    - Aparat InnoDB
- Migracja tej samej wersji. Migracja MySQL 5.6 do usługi Azure Database for MySQL 5.7 nie jest obsługiwane.
- Włącz rejestrowanie binarne w pliku my.ini (Windows) lub My.cnf (system Unix)
    - Ustaw Server_id na dowolną liczbę większa lub równa 1, na przykład Server_id = 1 (tylko w przypadku MySQL 5.6)
    - Ustaw log-bin = \<ścieżka > (tylko w przypadku MySQL 5.6)
    - Ustaw binlog_format = wiersz
    - Expire_logs_days = 5 (zalecane — tylko w przypadku MySQL 5.6)
- Użytkownik musi mieć rolę ReplicationAdmin.
- Zdefiniowane dla źródłowej bazy danych MySQL sortowania są takie same jak te zdefiniowane w lokalizacji docelowej usługi Azure Database for MySQL.
- Schemat musi odpowiadać między bazy danych MySQL źródłowej i docelowej bazy danych w usłudze Azure Database for MySQL.
- Schemat w lokalizacji docelowej usługi Azure Database for MySQL nie może mieć klucze obce. Użyj następującego zapytania, można usunąć kluczy obcych:
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Uruchom docelowy klucz obcy (znajduje się w drugiej kolumnie) w wyniku zapytania.
- Schemat w lokalizacji docelowej usługi Azure Database for MySQL nie może mieć żadnych wyzwalaczy. Aby porzucić wyzwalaczy w docelowej bazie danych:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Ograniczenia typu danych
- **Ograniczenie**: W przypadku typu danych JSON w źródłowej bazy danych MySQL, migracja zakończy się niepowodzeniem podczas ciągłej synchronizacji.

    **Obejście**: Modyfikowanie danych JSON do średnich tekstu lub longtext w bazie danych MySQL źródła.

- **Ograniczenie**: Jeśli istnieje nie klucza podstawowego w tabelach, ciągłej synchronizacji nie powiedzie się.
 
    **Obejście**: Tymczasowo ustawić klucza podstawowego w tabeli migracji kontynuować. Po zakończeniu migracji danych, można usunąć klucza podstawowego.

## <a name="lob-limitations"></a>Ograniczenia LOB
Duże kolumny obiektu (LOB) to kolumn, które można powiększać dużych rozmiaru. Programu MySQL, średnie tekstu Longtext obiektów Blob, Mediumblob, Longblob, itp. przedstawiono niektóre typy danych obiektu LOB.

- **Ograniczenie**: Typy danych obiektów LOB są używane jako klucze podstawowe, migracji zakończy się niepowodzeniem.

    **Obejście**: Zamień na klucz podstawowy inne typy danych lub kolumny, które nie są LOB.

- **Ograniczenie**: Jeśli długość kolumny duży obiekt (LOB) jest większy niż 32 KB, danych może zostać obcięta do miejsca docelowego. Możesz sprawdzić długość kolumny obiektów LOB przy użyciu tego zapytania:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Obejście**: Jeśli obiekt LOB, który jest większy niż 32 KB, skontaktuj się z inżynierami w [poproś migracje baz danych Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com). 

## <a name="other-limitations"></a>Pozostałe ograniczenia
- Ciąg hasła, który ma otwierający i zamykający nawiasy klamrowe {} na początku i końca ciągu hasła nie jest obsługiwane. To ograniczenie dotyczy zarówno nawiązywania połączenia z źródłowy MySQL i docelową usługę Azure Database for MySQL.
- Nie są obsługiwane następujące DDLs:
    - Wszystkich partycji DDLs
    - Usunięcie tabeli
    - Zmień nazwę tabeli
- Za pomocą *alter table < nazwa_tabeli > Dodaj kolumnę < nazwa_kolumny >* instrukcję, aby dodać kolumny na początku lub w środku tabeli nie jest obsługiwane. *Alter table < nazwa_tabeli > Dodaj kolumnę < nazwa_kolumny >* dodaje kolumnę na koniec tabeli.
- Indeksy utworzone na tylko część danych kolumny nie są obsługiwane. Poniższa instrukcja to przykład tworzenia indeksu za pomocą tylko część danych kolumny:
    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- W systemie limit bazy danych do migracji w działaniu jedną migrację jednej to cztery.
