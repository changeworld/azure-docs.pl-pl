---
title: Jak zrzucanie i przywracanie bazy danych Azure database for PostgreSQL
description: W tym artykule opisano sposób wyodrębniania bazę danych PostgreSQL w pliku zrzutu i przywracania z pliku, który został utworzony przez pg_dump w usłudze Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/19/2018
ms.openlocfilehash: 94d196ceecc0b63b9f0b0fe94f71363dc2086c30
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213654"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrowanie przy użyciu zrzutu i przywracania bazy danych PostgreSQL
Możesz użyć [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) można wyodrębnić bazy danych PostgreSQL w pliku zrzutu i [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) przywrócenie bazy danych PostgreSQL z pliku archiwum, utworzone przez pg_dump.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków w tym przewodniku, potrzebne są:
- [— Azure Database for postgresql w warstwie serwera](quickstart-create-server-database-portal.md) za pomocą reguł zapory, aby zezwolić na dostęp i bazy danych w nim.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) i [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) narzędzia wiersza polecenia, zainstalowane

Wykonaj następujące kroki, aby zrzucanie i przywracanie bazy danych PostgreSQL:

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Tworzenie pliku zrzutu przy użyciu pg_dump, która zawiera dane do załadowania
Do tworzenia kopii zapasowych istniejących postgresql w warstwie bazy danych lokalnie lub na maszynie wirtualnej, uruchom następujące polecenie:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Na przykład w przypadku lokalnego serwera i bazy danych o nazwie **testdb** w nim
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```

> [!IMPORTANT]
> Skopiuj pliki kopii zapasowej do obiektu blob platformy Azure/magazynu i przeprowadzenia odzyskiwania z tego miejsca, którego wartością powinna być o wiele szybciej, niż wykonywanie przywracania w Internecie.
> 

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Przywracanie danych do docelowej bazy danych Azure przy użyciu pg_restore PostrgeSQL
Po utworzeniu docelowej bazy danych, można użyć polecenia pg_restore i -d, parametr--dbname do przywrócenia danych do docelowej bazy danych z pliku zrzutu.
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Tym właściciela nie parametrze powoduje, że wszystkie obiekty stworzone podczas przywracania należy do użytkownika określony za pomocą--username. Aby uzyskać więcej informacji, zobacz oficjalnej dokumentacji PostgreSQL na [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Jeśli Twój serwer PostgreSQL wymaga połączenia SSL (na domyślnie w usłudze Azure Database dla PostgreSQL serwerów), ustaw zmienną środowiskową `PGSSLMODE=require` tak, aby narzędzie pg_restore nawiązuje połączenie z protokołem SSL. Bez protokołu SSL mogą być odczytywane błędu  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> W wierszu polecenia Windows uruchom polecenie `SET PGSSLMODE=require` przed uruchomieniem polecenia pg_restore. W systemie Linux lub Bash Uruchom polecenie `export PGSSLMODE=require` przed uruchomieniem polecenia pg_restore.
>

W tym przykładzie należy przywrócić dane z pliku zrzutu **testdb.dump** do bazy danych **mypgsqldb** na serwerze docelowym **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>Kolejne kroki
- Aby przeprowadzić migrację bazy danych PostgreSQL za pomocą eksportu i importu, zobacz [migracja bazy danych postgresql w warstwie użycie opcji eksportowania i importowania](howto-migrate-using-export-and-import.md).
- Aby uzyskać więcej informacji na temat migrowania baz danych do usługi Azure Database for PostgreSQL, zobacz [Przewodnik po migracji bazy danych](http://aka.ms/datamigration).
