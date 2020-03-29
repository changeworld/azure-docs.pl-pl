---
title: Migrowanie bazy danych — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób wyodrębniania bazy danych PostgreSQL do pliku skryptu i importowania danych do docelowej bazy danych z tego pliku.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: f7cf5d245383b8a58f03e2e3610750866a2f4b5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770207"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrowanie bazy danych PostgreSQL przy użyciu eksportu i importowania
Za pomocą [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) można wyodrębnić bazę danych PostgreSQL do pliku skryptu i [psql,](https://www.postgresql.org/docs/current/static/app-psql.html) aby zaimportować dane do docelowej bazy danych z tego pliku.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz:
- [Serwer usługi Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) z regułami zapory zezwalają na dostęp i bazę danych.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) zainstalowane narzędzie wiersza polecenia
- [psql](https://www.postgresql.org/docs/current/static/app-psql.html) narzędzie wiersza polecenia zainstalowany

Wykonaj następujące kroki, aby wyeksportować i zaimportować bazę danych PostgreSQL.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Tworzenie pliku skryptu przy użyciu pg_dump zawierającego dane, które mają zostać załadowane
Aby wyeksportować istniejącą bazę danych PostgreSQL lokalnie lub na maszynie Wirtualnej do pliku skryptu sql, uruchom następujące polecenie w istniejącym środowisku:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Na przykład, jeśli masz serwer lokalny i bazę danych o nazwie **testdb** w nim:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importowanie danych w docelowej bazie danych platformy Azure dla postgreSQL
Za pomocą wiersza polecenia psql i parametru --dbname (-d) można zaimportować dane do serwera Usługi Azure Database for PostgreSQL i załadować dane z pliku sql.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
W tym przykładzie użyto narzędzia psql i pliku skryptu o nazwie **testdb.sql** z poprzedniego kroku, aby zaimportować dane do bazy danych **mypgsqldb** na serwerze docelowym **mydemoserver.postgres.database.azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Następne kroki
- Aby przeprowadzić migrację bazy danych PostgreSQL przy użyciu zrzutu i przywracania, zobacz [Migrowanie bazy danych PostgreSQL przy użyciu zrzutu i przywracania](howto-migrate-using-dump-and-restore.md).
- Aby uzyskać więcej informacji na temat migracji baz danych do bazy danych usługi Azure Database for PostgreSQL, zobacz [Przewodnik migracji bazy danych](https://aka.ms/datamigration). 
