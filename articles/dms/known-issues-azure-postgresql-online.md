---
title: 'Znane problemy: migracje online z postgreSQL do usługi Azure Database dla postgreSQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się więcej o znanych problemach i ograniczeniach migracji z migracji online z PostgreSQL do bazy danych Platformy Azure dla postgreSQL przy użyciu usługi migracji bazy danych azure.
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
ms.openlocfilehash: 3d1bc627ccb8814ab2dfb61fb0653ef0ac644038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235261"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Znane problemy/ograniczenia migracji z migracjami online z PostgreSQL do usługi Azure DB dla postgreSQL

Znane problemy i ograniczenia związane z migracjami online z PostgreSQL do usługi Azure Database for PostgreSQL opisano w poniższych sekcjach.

## <a name="online-migration-configuration"></a>Konfiguracja migracji online

- Źródłowy serwer PostgreSQL musi być uruchomiony w wersji 9.4, 9.5, 9.6, 10 lub 11. Aby uzyskać więcej informacji, zobacz artykuł [Obsługiwane wersje bazy danych PostgreSQL](../postgresql/concepts-supported-versions.md).
- Obsługiwane są tylko migracje do tej samej lub wyższej wersji. Na przykład migracja PostgreSQL 9.5 do usługi Azure Database dla PostgreSQL 9.6 lub 10 jest obsługiwana, ale migracja z PostgreSQL 11 do PostgreSQL 9.6 nie jest obsługiwana.
- Aby włączyć replikację logiczną w źródłowym pliku **postgresql.conf,** należy ustawić następujące parametry:
  - **wal_level** = logiczne
  - **max_replication_slots** = [co najmniej maksymalna liczba baz danych do migracji]; jeśli chcesz przeprowadzić migrację czterech baz danych, ustaw wartość co najmniej 4.
  - **max_wal_senders** = [liczba baz danych uruchomionych jednocześnie]; zalecana wartość wynosi 10
- Dodaj adres IP agenta DMS do źródła PostgreSQL pg_hba.conf
  1. Zanotuj adres IP DMS po zakończeniu inicjowania obsługi administracyjnej wystąpienia usługi migracji bazy danych platformy Azure.
  2. Dodaj adres IP do pliku pg_hba.conf, jak pokazano na rysunku:

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- Użytkownik musi mieć rolę REPLIKACJA na serwerze obsługującym źródłową bazę danych.
- Schematy źródłowej i docelowej bazy danych muszą być zgodne.
- Schemat w docelowej bazie danych platformy Azure dla serwera PostgreSQL-Single nie może mieć kluczy obcych. Użyj następującej kwerendy, aby upuścić klucze obce:

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Uruchom docelowy klucz obcy (znajduje się w drugiej kolumnie) w wyniku zapytania.

- Schemat w docelowej bazie danych platformy Azure dla serwera PostgreSQL-Single nie może mieć żadnych wyzwalaczy. Aby wyłączyć wyzwalacze w docelowej bazie danych, należy użyć następujących funkcji:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Ograniczenia typu danych

  **Ograniczenie:** Jeśli w tabelach nie ma klucza podstawowego, zmiany mogą nie zostać zsynchronizowane z docelową bazą danych.

  **Obejście:** Tymczasowo ustaw klucz podstawowy dla tabeli, aby migracja była kontynuowana. Klucz podstawowy można usunąć po zakończeniu migracji danych.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Ograniczenia podczas migracji online z AWS RDS PostgreSQL

Podczas próby przeprowadzenia migracji online z usługi AWS RDS PostgreSQL do usługi Azure Database dla postgreSQL, mogą wystąpić następujące błędy.

- **Błąd:** Domyślna wartość kolumny "{column}" w tabeli "{table}" w bazie danych "{database}" różni się na serwerach źródłowych i docelowych. Na serwerze źródłowym jest to „{value on source}”, a na serwerze docelowym — „{value on target}”.

  **Ograniczenie:** Ten błąd występuje, gdy wartość domyślna w schemacie kolumny różni się między źródłowymi i docelowymi bazami danych.
  **Obejście:** Upewnij się, że schemat w docelowym pasuje do schematu w źródle. Szczegółowe informacje na temat migracji schematu można znaleźć w [dokumentacji migracji online usługi Azure PostgreSQL.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

- **Błąd:** Docelowa baza danych "{database}" ma tabele "{liczba tabel}", w których jako źródłowna baza danych "{database}" ma tabele "{liczba tabel}". Liczba tabel w źródłowej i docelowej bazie danych powinna być taka sama.

  **Ograniczenie:** Ten błąd występuje, gdy liczba tabel jest różna między źródłowymi i docelowymi bazami danych.

  **Obejście:** Upewnij się, że schemat w docelowym pasuje do schematu w źródle. Szczegółowe informacje na temat migracji schematu można znaleźć w [dokumentacji migracji online usługi Azure PostgreSQL.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

- **Błąd:** Źródłowa baza danych {database} jest pusta.

  **Ograniczenie:** Ten błąd występuje, gdy źródłowa baza danych jest pusta. Jest to najprawdopodobniej dlatego, że wybrano niewłaściwą bazę danych jako źródło.

  **Obejście:** Sprawdź dwukrotnie źródłową bazę danych wybraną do migracji, a następnie spróbuj ponownie.

- **Błąd:** Docelowa baza danych {database} jest pusta. Przeprowadź migrację schematu.

  **Ograniczenie:** Ten błąd występuje, gdy nie ma schematu w docelowej bazie danych. Upewnij się, że schemat w docelowym pasuje schematu w źródle.
  **Obejście:** Upewnij się, że schemat w docelowym pasuje do schematu w źródle. Szczegółowe informacje na temat migracji schematu można znaleźć w [dokumentacji migracji online usługi Azure PostgreSQL.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

## <a name="other-limitations"></a>Inne ograniczenia

- Nazwa bazy danych nie może zawierać średnika (;).
- Przechwycona tabela musi mieć klucz podstawowy. Jeśli tabela nie ma klucza podstawowego, wynik operacji usuwania i aktualizacji rekordów będzie nieprzewidywalny.
- Aktualizowanie segmentu klucza podstawowego jest ignorowane. W takich przypadkach zastosowanie takiej aktualizacji zostanie zidentyfikowane przez obiekt docelowy jako aktualizacja, która nie zaktualizowała żadnych wierszy i spowoduje rekord zapisany w tabeli wyjątków.
- Migracja wielu tabel o tej samej nazwie, ale w innym przypadku (np. table1, TABLE1 i Table1) może powodować nieprzewidywalne zachowanie i dlatego nie jest obsługiwana.
- Zmiana przetwarzania [CREATE | ALTER | KROPLA | TRUNCATE] tabela DDLs tabeli nie jest obsługiwany.
- W usłudze migracji bazy danych azure jedno działanie migracji może pomieścić tylko do czterech baz danych.
