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
ms.date: 04/23/2019
ms.openlocfilehash: 2c8a3f36e04fbedfdd127939d55fab376e3e6b30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "64691954"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Ograniczenia znanych problemów/migracja online migracja do bazy danych Azure database for PostgreSQL

W poniższych sekcjach opisano znane problemy i ograniczenia związane z usługą online migracji z PostgreSQL do usługi Azure Database for PostgreSQL. 

## <a name="online-migration-configuration"></a>Konfiguracja migracji online
- Źródłowy serwer PostgreSQL musi działać wersja 9.5.11, 9.6.7 lub 10.3 lub nowszym. Aby uzyskać więcej informacji, zobacz artykuł [Obsługiwane wersje bazy danych PostgreSQL](../postgresql/concepts-supported-versions.md).
- Obsługiwane są tylko migracje tej samej wersji. Na przykład PostgreSQL Migrowanie 9.5.11 do usługi Azure Database for PostgreSQL 9.6.7 nie jest obsługiwana.

    > [!NOTE]
    > Database for PostgreSQL w wersji 10 aktualnie DMS obsługuje tylko migrację w wersji 10.3 do usługi Azure Database for PostgreSQL. Planujemy do obsługi nowszych wersji PostgreSQL bardzo szybko.

- Aby włączyć replikację logicznych w **źródła PostgreSQL postgresql.conf** plików, ustaw następujące parametry:
    - **wal_level** = logical
    - **max_replication_slots** = [maksymalna liczba baz danych do migracji]; Jeśli chcesz przeprowadzić migrację 4 baz danych, ustaw wartość 4
    - **max_wal_senders** = [liczba baz danych działających równocześnie]; zalecana wartość wynosi 10
- Dodano adres IP agenta usługi DMS do pg_hba.conf PostgresSQL źródła
    1. Zanotuj adres IP usługi DMS po zakończeniu aprowizacji wystąpienia usługi DMS.
    2. Dodaj adres IP do pliku pg_hba.conf, jak pokazano:

        hostowanie wszystkich 172.16.136.18/10 md5 hosta replikacji postgres 172.16.136.18/10 md5

- Użytkownik musi mieć uprawnienia administratora na serwerze hostującym źródłowej bazy danych
- Oprócz zainstalowanej Wyliczenia w schemacie bazy danych źródła, schematy bazy danych źródłowa i docelowa musi być zgodna.
- Schemat w elemencie docelowym — Azure Database for postgresql w warstwie nie może mieć klucze obce. Użyj następującego zapytania, można usunąć kluczy obcych:

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

- Schemat w lokalizacji docelowej usługi Azure Database for postgresql w warstwie nie może mieć żadnych wyzwalaczy. Aby wyłączyć wyzwalaczy w docelowej bazie danych, należy użyć następujących:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Ograniczenia typu danych

- **Ograniczenie**: W przypadku typu danych Wyliczenia do źródłowej bazy danych postgresql w warstwie, migracji zakończy się niepowodzeniem podczas ciągłej synchronizacji.

    **Obejście**: Zmodyfikuj Wyliczenia typu danych, do znaku zmieniającego się w usłudze Azure Database for PostgreSQL.

- **Ograniczenie**: Jeśli istnieje nie klucza podstawowego w tabelach, ciągłej synchronizacji nie powiedzie się.

    **Obejście**: Tymczasowo ustawić klucza podstawowego w tabeli migracji kontynuować. Po zakończeniu migracji danych, można usunąć klucza podstawowego.

## <a name="lob-limitations"></a>Ograniczenia LOB
Duże kolumny obiektu (LOB) to kolumn, które można powiększać dużych. Database for PostgreSQL typy danych obiektów LOB przykładami XML, JSON, obraz, tekst itd.

- **Ograniczenie**: Typy danych obiektów LOB są używane jako klucze podstawowe, migracji zakończy się niepowodzeniem.

    **Obejście**: Zamień na klucz podstawowy inne typy danych lub kolumn, które nie są LOB.

- **Ograniczenie**: Jeśli długość kolumny duży obiekt (LOB) jest większy niż 32 KB, danych może zostać obcięta do miejsca docelowego. Możesz sprawdzić długość kolumny obiektów LOB przy użyciu tego zapytania:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Obejście**: Jeśli obiekt LOB, który jest większy niż 32 KB, skontaktuj się z inżynierami w [poproś migracje baz danych Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

- **Ograniczenie**: Jeśli w tabeli znajdują się kolumny obiektów LOB, a nie jest podstawowego klucza dla tabeli, danych nie może być migracji dla tej tabeli.

    **Obejście**: Tymczasowo ustawić klucza podstawowego w tabeli migracji kontynuować. Po zakończeniu migracji danych, można usunąć klucza podstawowego.

## <a name="postgresql10-workaround"></a>Obejście PostgreSQL10
PostgreSQL 10.x powoduje różne zmiany nazw folderów pg_xlog i dlatego powodują, że migracja nie działa zgodnie z oczekiwaniami. W przypadku migrowania z PostgreSQL 10.x do usługi Azure Database for postgresql w warstwie 10.3, uruchom następujący skrypt źródłowej bazy danych postgresql — Tworzenie funkcji otokę wokół pg_xlog funkcji.

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

## <a name="other-limitations"></a>Pozostałe ograniczenia
- Nazwa bazy danych nie może zawierać rozdzielonych średnikami (;).
- Ciąg hasła, który ma {} nawiasy nawias otwierający i zamykający nie jest obsługiwane. To ograniczenie dotyczy zarówno nawiązywania połączenia z źródłowy PostgreSQL i docelową usługę Azure Database for PostgreSQL.
- Przechwycone tabela musi mieć klucz podstawowy. Jeśli tabela nie ma klucza podstawowego, wynik operacji usuwania i aktualizacji rekordów będzie nieprzewidywalny.
- Aktualizowanie segment klucza podstawowego jest ignorowany. W takich przypadkach stosowania tych aktualizacji zostaną zidentyfikowane przez element docelowy jako aktualizacja nie została zaktualizowana wszystkie wiersze, która spowoduje w rekordzie zapisywane w tabeli wyjątków.
- Migracja wielu tabel z tej samej nazwie, ale inną wielkość liter (np. table1, TABLE1 i Tabela1) może spowodować nieprzewidywalne zachowanie i dlatego nie jest obsługiwana.
- Zmień przetwarzanie [Utwórz | INSTRUKCJA ALTER | DDLs tabeli docelowej] są obsługiwane, o ile nie muszą być przechowywane w bloku treści funkcji/procedury, wewnętrzny lub w innych zagnieżdżonej konstrukcji. Na przykład nie będą przechwytywane następujące zmiany:

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

- Przetwarzanie zmian (ciągła synchronizacja) TRUNCATE operacji nie jest obsługiwane. Migracja podzielonych tabel nie jest obsługiwana. Po wykryciu tabeli partycjonowanej wykonywane są następujące elementy:
    - Bazy danych są raportowane listę tabel nadrzędnych i podrzędnych.
    - Tabela zostanie utworzony w elemencie docelowym jak zwykłą tabelę z tymi samymi właściwościami co wybrane tabele.
    - Jeśli tabela nadrzędna do źródłowej bazy danych ma taką samą wartość klucza podstawowego, jak jego tabele podrzędne, zostanie wygenerowany błąd "zduplikowany klucz".
- W systemie limit bazy danych do migracji w działaniu jedną migrację jednej to cztery.