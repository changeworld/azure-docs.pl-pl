---
title: Artykuł dotyczący znanych problemów/ograniczeń migracji z migracją online do Azure Database for MySQL | Microsoft Docs
description: Informacje o znanych problemach/ograniczeniach migracji z migracją online do Azure Database for MySQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 08/06/2019
ms.openlocfilehash: fc5565ab9e3be21b96ce5aa5a938cf22ec3caeb0
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848481"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-mysql"></a>Znane problemy/ograniczenia migracji z migracją online do usługi Azure DB for MySQL

Znane problemy i ograniczenia związane z migracją w trybie online z programu MySQL do Azure Database for MySQL są opisane w poniższych sekcjach.

## <a name="online-migration-configuration"></a>Konfiguracja migracji w trybie online

- Źródłowa wersja serwera MySQL musi być w wersji 5.6.35, 5.7.18 lub nowszej
- Azure Database for MySQL obsługuje:
  - MySQL Community Edition
  - Aparat InnoDB
- Migracja tej samej wersji. Migrowanie MySQL 5,6 do Azure Database for MySQL 5,7 nie jest obsługiwane.
- Włącz logowanie binarne w pliku my. ini (Windows) lub My. cnf (UNIX)
  - Ustaw wartość Server_id na dowolną liczbę większą lub równą 1, na przykład Server_id = 1 (tylko w przypadku programu MySQL 5,6)
  - Ustaw wartość log-bin \<= Path > (tylko dla programu MySQL 5,6)
  - Ustaw binlog_format = wiersz
  - Expire_logs_days = 5 (zalecane-tylko dla MySQL 5,6)
- Użytkownik musi mieć rolę ReplicationAdmin.
- Ustawienia sortowania zdefiniowane dla źródłowej bazy danych MySQL są takie same jak te zdefiniowane w Azure Database for MySQL docelowym.
- Schemat musi pasować do źródłowej bazy danych MySQL i docelowej bazy danych w Azure Database for MySQL.
- Schemat w Azure Database for MySQL docelowym nie może mieć kluczy obcych. Użyj następującego zapytania, aby porzucić klucze obce:
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
- Schemat w Azure Database for MySQL docelowym nie może mieć żadnych wyzwalaczy. Aby porzucić Wyzwalacze w docelowej bazie danych:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Ograniczenia typów danych

- **Ograniczenie**: Jeśli istnieje typ danych JSON w źródłowej bazie danych MySQL, migracja nie powiedzie się podczas synchronizacji ciągłej.

    **Obejście problemu**: Zmodyfikuj element DataType JSON na średni tekst lub LONGTEXT w źródłowej bazie danych MySQL.

- **Ograniczenie**: Jeśli nie ma klucza podstawowego w tabelach, synchronizacja ciągła zakończy się niepowodzeniem.

    **Obejście problemu**: Tymczasowo Ustaw klucz podstawowy dla tabeli do migracji, aby kontynuować. Klucz podstawowy można usunąć po zakończeniu migracji danych.

## <a name="lob-limitations"></a>Ograniczenia dotyczące obiektów LOB

Kolumny dużego obiektu (LOB) są kolumnami, które mogą mieć duży rozmiar. W przypadku bazy danych MySQL, medium text, LONGTEXT, BLOB, mediumblob, Longblob itp., są częścią typów danych LOB.

- **Ograniczenie**: Jeśli typy danych LOB są używane jako klucze podstawowe, migracja zakończy się niepowodzeniem.

    **Obejście problemu**: Zastąp klucz podstawowy innymi rodzajami danych lub kolumnami, które nie są LOB.

- **Ograniczenie**: Jeśli długość kolumny dużego obiektu (LOB) jest większa niż 32 KB, dane mogą być obcinane w miejscu docelowym. Możesz sprawdzić długość kolumny LOB przy użyciu tego zapytania:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Obejście problemu**: Jeśli masz obiekt LOB o rozmiarze większym niż 32 KB, skontaktuj się z zespołem inżynieryjnym w poproszeniu do [migracji bazy danych platformy Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com). 

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Ograniczenia podczas migrowania w trybie online z AWS RDS MySQL

Podczas próby przeprowadzenia migracji w trybie online z AWS RDS MySQL do Azure Database for MySQL mogą wystąpić następujące błędy.

- **Błąd:** Baza danych{0}"" ma klucze obce w miejscu docelowym. Napraw miejsce docelowe i uruchom nowe działanie migracji danych. Wykonaj Poniższy skrypt w celu, aby wyświetlić listę kluczy obcych

  **Ograniczenie**: Jeśli w schemacie znajdują się klucze obce, ładowanie początkowe i ciągła synchronizacja migracji zakończą się niepowodzeniem.
  **Obejście problemu**: Uruchom następujący skrypt w środowisku roboczym bazy danych MySQL w celu wyodrębnienia skryptu docelowego klucza obcego, a następnie dodaj skrypt klucza obcego:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Błąd:** Baza danych{0}"" nie istnieje na serwerze. Podany serwer źródłowy MySQL uwzględnia wielkość liter. Sprawdź nazwę bazy danych.

  **Ograniczenie**: Podczas migracji bazy danych MySQL na platformę Azure przy użyciu interfejsu wiersza polecenia użytkownicy mogą napotkać ten błąd. Usługa nie może zlokalizować bazy danych na serwerze źródłowym, co może być spowodowane podaną nieprawidłową nazwą bazy danych lub baza danych nie istnieje na wymienionym serwerze. Uwaga w nazwach baz danych jest rozróżniana wielkość liter.

  **Obejście problemu**: Podaj dokładną nazwę bazy danych, a następnie spróbuj ponownie.

- **Błąd:** W bazie danych "{Database}" istnieją tabele o tej samej nazwie. Usługa Azure Database for MySQL nie obsługuje tabel uwzględniających wielkość liter.

  **Ograniczenie**: Ten błąd występuje, gdy w źródłowej bazie danych istnieją dwie tabele o takiej samej nazwie. Azure Database for MySQL nie obsługuje tabel z uwzględnieniem wielkości liter.

  **Obejście problemu**: Zaktualizuj nazwy tabel, aby były unikatowe, a następnie spróbuj ponownie.

- **Błąd:** Docelowa baza danych {Database} jest pusta. Przeprowadź migrację schematu.

  **Ograniczenie**: Ten błąd występuje, gdy docelowa baza danych Azure Database for MySQL nie ma wymaganego schematu. Migracja schematu jest wymagana, aby umożliwić Migrowanie danych do obiektu docelowego.

  **Obejście problemu**: [Migruj schemat](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) ze źródłowej bazy danych do docelowej bazy danych.

## <a name="other-limitations"></a>Inne ograniczenia

- Ciąg hasła, który ma otwierające i zamykające nawiasy klamrowe {} na początku i na końcu ciągu hasła nie jest obsługiwany. To ograniczenie dotyczy zarówno łączenia z źródłową bazą danych MySQL, jak i Azure Database for MySQL docelowej.
- Następujące DDLs nie są obsługiwane:
  - Wszystkie DDLs partycji
  - Usuń tabelę
  - Zmień nazwę tabeli
- Użycie instrukcji *ALTER table < table_name > Dodawanie kolumny < column_name >* do dodawania kolumn na początku lub w środku tabeli nie jest obsługiwane. *Instrukcja ALTER table < table_name > Dodaj kolumnę < column_name >* dodaje kolumnę na końcu tabeli.
- Indeksy utworzone tylko w części danych kolumn nie są obsługiwane. Poniższa instrukcja to przykład, który tworzy indeks, używając tylko części danych kolumny:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- W programie DMS limit migracji baz danych w jednym działaniu migracji wynosi cztery.
