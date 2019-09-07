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
ms.openlocfilehash: 56758e2962adb41c9876171c89b37263a70ed0e4
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743540"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Znane problemy/ograniczenia migracji z migracją online do usługi Azure DB for PostgreSQL

Znane problemy i ograniczenia związane z migracją online z PostgreSQL do Azure Database for PostgreSQL są opisane w poniższych sekcjach.

## <a name="online-migration-configuration"></a>Konfiguracja migracji w trybie online

- Źródłowy serwer PostgreSQL musi mieć uruchomioną wersję 9.5.11, 9.6.7 lub 10,3 lub nowszą. Aby uzyskać więcej informacji, zobacz artykuł [Obsługiwane wersje bazy danych PostgreSQL](../postgresql/concepts-supported-versions.md).
- Obsługiwane są tylko te same migracje wersji. Na przykład Migrowanie PostgreSQL 9.5.11 do Azure Database for PostgreSQL 9.6.7 nie jest obsługiwane.

    > [!NOTE]
    > W przypadku PostgreSQL w wersji 10 obecnie DMS obsługuje tylko migrację z wersji 10,3 do Azure Database for PostgreSQL. Wkrótce planujemy obsługiwać nowsze wersje programu PostgreSQL.

- Aby włączyć replikację logiczną w pliku **Source PostgreSQL PostgreSQL. conf** , ustaw następujące parametry:
  - **wal_level** = logiczny
  - **max_replication_slots** = [Maksymalna liczba baz danych do migracji]; Jeśli chcesz przeprowadzić migrację 4 baz danych, ustaw wartość na 4.
  - **max_wal_senders** = [liczba baz danych uruchomionych współbieżnie]; Zalecana wartość to 10
- Dodawanie adresu IP agenta DMS do źródła PostgreSQL pg_hba. conf
  1. Zanotuj adres IP DMS po zakończeniu aprowizacji wystąpienia DMS.
  2. Dodaj adres IP do pliku pg_hba. conf, jak pokazano:

        Hostowanie wszystkich 172.16.136.18/10 MD5 replikacji hosta Postgres 172.16.136.18/10 MD5

- Użytkownik musi mieć uprawnienia administratora na serwerze hostującym źródłową bazę danych
- W schemacie źródłowej bazy danych muszą one być zgodne z wyliczeniem wyliczeniowym źródłowej i docelowej bazy danych.
- Schemat w Azure Database for PostgreSQL docelowym nie może mieć kluczy obcych. Użyj następującego zapytania, aby porzucić klucze obce:

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

- Schemat w Azure Database for PostgreSQL docelowym nie może mieć żadnych wyzwalaczy. Aby wyłączyć Wyzwalacze w docelowej bazie danych, wykonaj następujące czynności:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Ograniczenia typów danych

- **Ograniczenie**: Jeśli w źródłowej bazie danych PostgreSQL istnieje typ danych ENUM, migracja zakończy się niepowodzeniem podczas synchronizacji ciągłej.

    **Obejście problemu**: Zmodyfikuj typ WYLICZENIOWY jako znak zróżnicowany w Azure Database for PostgreSQL.

- **Ograniczenie**: Jeśli nie ma klucza podstawowego w tabelach, synchronizacja ciągła zakończy się niepowodzeniem.

    **Obejście problemu**: Tymczasowo Ustaw klucz podstawowy dla tabeli do migracji, aby kontynuować. Klucz podstawowy można usunąć po zakończeniu migracji danych.

- **Ograniczenie**: Typ JSONB nie jest obsługiwany w przypadku migracji.

## <a name="lob-limitations"></a>Ograniczenia dotyczące obiektów LOB

Kolumny dużego obiektu (LOB) są kolumnami, które mogą rosnąć duże. W przypadku PostgreSQL, przykłady typów danych LOB obejmują XML, JSON, obraz, tekst itd.

- **Ograniczenie**: Jeśli typy danych LOB są używane jako klucze podstawowe, migracja zakończy się niepowodzeniem.

    **Obejście problemu**: Zastąp klucz podstawowy innymi rodzajami danych lub kolumnami, które nie są LOB.

- **Ograniczenie**: Jeśli długość kolumny dużego obiektu (LOB) jest większa niż 32 KB, dane mogą być obcinane w miejscu docelowym. Możesz sprawdzić długość kolumny LOB przy użyciu tego zapytania:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Obejście problemu**: Jeśli masz obiekt LOB o rozmiarze większym niż 32 KB, skontaktuj się z zespołem inżynieryjnym w [poproszeniu do migracji bazy danych platformy Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

- **Ograniczenie**: Jeśli w tabeli znajdują się kolumny LOB i nie istnieje zestaw kluczy podstawowych dla tej tabeli, dane mogą nie być migrowane dla tej tabeli.

    **Obejście problemu**: Tymczasowo Ustaw klucz podstawowy dla tabeli w celu przeprowadzenia migracji. Klucz podstawowy można usunąć po zakończeniu migracji danych.

## <a name="postgresql10-workaround"></a>Obejście PostgreSQL10

PostgreSQL 10. x wprowadza różne zmiany nazw folderów pg_xlog, co sprawia, że migracja nie działa zgodnie z oczekiwaniami. Jeśli przeprowadzasz migrację z PostgreSQL 10. x do Azure Database for PostgreSQL 10,3, wykonaj następujący skrypt w źródłowej bazie danych PostgreSQL, aby utworzyć funkcję otoki wokół funkcji pg_xlog.

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Ograniczenia dotyczące migracji z usługi AWS RDS PostgreSQL do trybu online

Podczas próby przeprowadzenia migracji w trybie online z AWS RDS PostgreSQL do Azure Database for PostgreSQL mogą wystąpić następujące błędy.

- **Błąd**: Wartość domyślna kolumny „{column}” w tabeli „{table}” w bazie danych „{baza danych}” różni się na serwerze źródłowym i docelowym. Na serwerze źródłowym jest to „{value on source}”, a na serwerze docelowym — „{value on target}”.

  **Ograniczenie**: Ten błąd występuje, gdy wartość domyślna w schemacie kolumny różni się między źródłową i docelową bazą danych.
  **Obejście problemu**: Upewnij się, że schemat na miejscu docelowym jest zgodny ze schematem w źródle. Aby uzyskać szczegółowe informacje na temat migrowania schematu, zapoznaj się z [dokumentacją dotyczącą migracji do usługi Azure PostgreSQL online](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Błąd**: docelowa baza danych „{database}” zawiera następującą liczbę tabel: „{number of tables}”, natomiast źródłowa baza danych „{database}” zawiera następującą liczbę tabel: „{number of tables}”. Liczba tabel w źródłowej i docelowej bazie danych powinna być taka sama.

  **Ograniczenie**: Ten błąd występuje, gdy liczba tabel między źródłową i docelową bazą danych jest różna.
  **Obejście problemu**: Upewnij się, że schemat na miejscu docelowym jest zgodny ze schematem w źródle. Aby uzyskać szczegółowe informacje na temat migrowania schematu, zapoznaj się z [dokumentacją dotyczącą migracji do usługi Azure PostgreSQL online](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Błąd:** Źródłowa baza danych {Database} jest pusta.

  **Ograniczenie**: Ten błąd występuje, gdy źródłowa baza danych jest pusta. Najprawdopodobniej przyczyną jest wybranie niewłaściwej bazy danych jako źródła.
  **Obejście problemu**: Sprawdź dwukrotnie źródłową bazę danych wybraną do migracji, a następnie spróbuj ponownie.

- **Błąd:** Docelowa baza danych {Database} jest pusta. Przeprowadź migrację schematu.

  **Ograniczenie**: Ten błąd występuje, gdy w docelowej bazie danych nie ma schematu. Upewnij się, że schemat na miejscu docelowym jest zgodny ze schematem w źródle.
  **Obejście problemu**: Upewnij się, że schemat na miejscu docelowym jest zgodny ze schematem w źródle. Aby uzyskać szczegółowe informacje na temat migrowania schematu, zapoznaj się z [dokumentacją dotyczącą migracji do usługi Azure PostgreSQL online](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

## <a name="other-limitations"></a>Inne ograniczenia

- Nazwa bazy danych nie może zawierać średnika (;).
- Ciąg hasła, który ma otwierające i zamykające nawiasy klamrowe {} nie jest obsługiwany. To ograniczenie dotyczy zarówno łączenia z PostgreSQL źródłowym, jak i Azure Database for PostgreSQL docelowej.
- Przechwycona tabela musi mieć klucz podstawowy. Jeśli tabela nie ma klucza podstawowego, wynik operacji usuwania i aktualizowania rekordu będzie nieprzewidywalny.
- Aktualizacja segmentu klucza podstawowego jest ignorowana. W takich przypadkach zastosowanie takiej aktualizacji zostanie zidentyfikowane przez obiekt docelowy jako aktualizacja, która nie zaktualizowano żadnych wierszy i spowoduje powstanie rekordu zarejestrowanego w tabeli wyjątków.
- Migracja wielu tabel o takiej samej nazwie, ale innym przypadku (np. Tabela1, TABELA1 i Tabela1) może spowodować nieprzewidywalne zachowanie i dlatego nie jest obsługiwana.
- Przetwarzanie zmian [CREATE | ZMIEŃ | DROP] tabela DDLs jest obsługiwana, chyba że znajdują się w wewnętrznym bloku wewnętrznej funkcji/procedury lub w innych zagnieżdżonych konstrukcjach. Na przykład następująca zmiana nie zostanie przechwycona:

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- Przetwarzanie zmian (ciągła synchronizacja) operacji OBCINAnia nie jest obsługiwane. Migracja partycjonowanych tabel nie jest obsługiwana. Po wykryciu partycjonowanej tabeli zachodzą następujące kwestie:

  - Baza danych będzie raportować listę tabel nadrzędnych i podrzędnych.
  - Tabela zostanie utworzona w miejscu docelowym jako zwykła tabela z tymi samymi właściwościami co wybrane tabele.
  - Jeśli tabela nadrzędna w źródłowej bazie danych ma taką samą wartość klucza podstawowego jak jej tabele podrzędne, zostanie wygenerowany błąd "zduplikowany klucz".

- W programie DMS limit migracji baz danych w jednym działaniu migracji wynosi cztery.
