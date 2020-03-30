---
title: 'Znane problemy: migracje online do bazy danych platformy Azure dla mysql'
titleSuffix: Azure Database Migration Service
description: Dowiedz się więcej o znanych problemach i ograniczeniach migracji z migracjami online do usługi Azure Database for MySQL podczas korzystania z usługi migracji bazy danych Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 8c3de28ea934302086a5b14e61482e6a4ab9a7ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235273"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Problemy z migracją online & ograniczenia dotyczące usługi Azure DB dla mysql za pomocą usługi migracji bazy danych platformy Azure

Znane problemy i ograniczenia związane z migracjami online z MySQL do usługi Azure Database for MySQL opisano w poniższych sekcjach.

## <a name="online-migration-configuration"></a>Konfiguracja migracji online


- Źródło mysql server wersja musi być wersja 5.6.35, 5.7.18 lub nowsze
- Usługa Azure Database for MySQL obsługuje:
  - Edycja społecznościowa MySQL
  - Silnik InnoDB
- Migracja tej samej wersji. Migracja mysql 5.6 do usługi Azure Database dla mysql 5.7 nie jest obsługiwana.
- Włącz logowanie binarne w my.ini (Windows) lub my.cnf (Unix)
  - Ustaw Server_id na dowolną liczbę większą lub równą 1, na przykład Server_id=1 (tylko dla MySQL 5.6)
  - Ustaw pojemnik na \<dziennik = ścieżka> (tylko dla MySQL 5.6)
  - Ustaw binlog_format = wiersz
  - Expire_logs_days = 5 (zalecane - tylko dla MySQL 5.6)
- Użytkownik musi mieć rolę ReplicationAdmin.
- Sortowania zdefiniowane dla źródłowej bazy danych MySQL są takie same, jak te zdefiniowane w docelowej bazie danych platformy Azure dla MySQL.
- Schemat musi być zgodny ze źródłową bazą danych MySQL a docelową bazą danych w usłudze Azure Database for MySQL.
- Schemat w docelowej bazie danych platformy Azure dla mysql nie może mieć kluczy obcych. Użyj następującej kwerendy, aby upuścić klucze obce:
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
- Schemat w docelowej bazie danych platformy Azure dla mysql nie może mieć żadnych wyzwalaczy. Aby usunąć wyzwalacze w docelowej bazie danych:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Ograniczenia typu danych

- **Ograniczenie:** Jeśli w źródłowej bazie danych MySQL znajduje się typ danych JSON, migracja zakończy się niepowodzeniem podczas ciągłej synchronizacji.

    **Obejście**: Zmodyfikuj typ danych JSON do średniego tekstu lub longtexta w źródłowej bazie danych MySQL.

- **Ograniczenie:** Jeśli w tabelach nie ma klucza podstawowego, synchronizacja ciągła zakończy się niepowodzeniem.

    **Obejście:** Tymczasowo ustaw klucz podstawowy dla tabeli, aby migracja była kontynuowana. Klucz podstawowy można usunąć po zakończeniu migracji danych.

## <a name="lob-limitations"></a>Ograniczenia LOB

Kolumny lob (Lob) to kolumny, które mogą powiększać rozmiary. Dla MySQL, Średni tekst, Longtext, Blob, Mediumblob, Longblob, itp., to tylko niektóre z typów danych LOB.

- **Ograniczenie:** Jeśli typy danych LOB są używane jako klucze podstawowe, migracja zakończy się niepowodzeniem.

    **Obejście:** Zastąp klucz podstawowy innymi typami danych lub kolumnami, które nie są lobami.

- **Ograniczenie:** Jeśli długość kolumny lob (Large Object) jest większa niż 32 KB, dane mogą być obcinane w miejscu docelowym. Długość kolumny LOB można sprawdzić za pomocą tej kwerendy:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Obejście:** Jeśli masz obiekt LOB większy niż 32 KB, skontaktuj się z zespołem inżynierów w [witrynie Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Ograniczenia podczas migracji online z AWS RDS MySQL

Podczas próby przeprowadzenia migracji online z usługi AWS RDS MySQL do usługi Azure Database dla mysql, można natknąć się na następujące błędy.

- **Błąd:** Baza{0}danych ' ' ma klucz(-y) obcy(-e) na cel. Napraw miejsce docelowe i uruchom nowe działanie migracji danych. Wykonaj poniższy skrypt na docelowej, aby wyświetlić listę kluczy obcych

  **Ograniczenie:** Jeśli masz klucze obce w schemacie, początkowe obciążenie i ciągła synchronizacja migracji zakończy się niepowodzeniem.
  **Obejście:** Wykonaj następujący skrypt w warsztacie MySQL, aby wyodrębnić skrypt klucza obcego i dodać skrypt klucza obcego:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Błąd:** Baza{0}danych ' ' nie istnieje na serwerze. Podany serwer źródłowy MySQL uwzględnia wielkość liter. Sprawdź nazwę bazy danych.

  **Ograniczenie:** Podczas migracji bazy danych MySQL na platformę Azure przy użyciu interfejsu wiersza polecenia (CLI) użytkownicy mogą naruszyć ten błąd. Usługa nie może zlokalizować bazy danych na serwerze źródłowym, co może być spowodowane pod warunkiem, że niepoprawna nazwa bazy danych lub baza danych nie istnieje na serwerze notowanym. W nazwach bazy danych notatek rozróżniana jest wielkość liter.

  **Obejście:** Podaj dokładną nazwę bazy danych, a następnie spróbuj ponownie.

- **Błąd:** W bazie danych "{database}" znajdują się tabele o tej samej nazwie. Usługa Azure Database for MySQL nie obsługuje tabel uwzględniających wielkość liter.

  **Ograniczenie:** Ten błąd występuje, gdy masz dwie tabele o tej samej nazwie w źródłowej bazie danych. Usługa Azure Database for MySQL nie obsługuje tabel z uwzględnieniem wielkości liter.

  **Obejście**: Zaktualizuj nazwy tabel, aby były unikatowe, a następnie spróbuj ponownie.

- **Błąd:** Docelowa baza danych {database} jest pusta. Przeprowadź migrację schematu.

  **Ograniczenie:** Ten błąd występuje, gdy docelowa baza danych platformy Azure dla mysql bazy danych nie ma wymaganego schematu. Migracja schematu jest wymagana, aby umożliwić migrację danych do obiektu docelowego.

  **Obejście:** [Migrowanie schematu](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) ze źródłowej bazy danych do docelowej bazy danych.

## <a name="other-limitations"></a>Inne ograniczenia

- Ciąg hasła, który ma otwierające i zamykające nawiasy klamrowe { } na początku i na końcu ciągu hasła, nie jest obsługiwany. To ograniczenie dotyczy zarówno łączenia się ze źródłem MySQL, jak i docelowej bazy danych platformy Azure dla mysql.
- Następujące listy DDLs nie są obsługiwane:
  - Wszystkie ddls partycji
  - Tabela upuszczania
  - Zmienianie nazwy tabeli
- Używanie *tabeli alter <table_name> dodać <kolumn column_name>* instrukcji, aby dodać kolumny na początku lub na środku tabeli, nie jest obsługiwane. *TZemieć tabelę <table_name> dodać kolumnę <column_name>* dodaje kolumnę na końcu tabeli.
- Indeksy utworzone tylko na części danych kolumny nie są obsługiwane. Poniższa instrukcja jest przykładem, który tworzy indeks przy użyciu tylko część danych kolumny:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- W usłudze migracji bazy danych platformy Azure limit baz danych do migracji w jednym działaniu migracji wynosi cztery.

- **Błąd:** Rozmiar wiersza jest za duży (> 8126). Zmiana niektórych kolumn na TEKST lub blob może pomóc. W bieżącym formacie wiersza prefiks obiektu BLOB o bajce 0 bajtów jest przechowywany w wierszu.

  **Ograniczenie:** Ten błąd występuje podczas migracji do usługi Azure Database dla mysql przy użyciu aparatu magazynu InnoDB i rozmiar wiersza tabeli jest zbyt duży (>8126 bajtów).

  **Obejście**: Zaktualizuj schemat tabeli o rozmiarze wiersza większym niż 8126 bajtów. Nie zaleca się zmiany trybu ścisłego, ponieważ dane zostaną obcięty. Zmiana page_size nie jest obsługiwana.
