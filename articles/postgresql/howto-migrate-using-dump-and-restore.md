---
title: Zrzut i przywracanie — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób wyodrębniania bazy danych PostgreSQL do pliku zrzutu i przywracania z pliku utworzonego przez pg_dump w usłudze Azure Database for PostgreSQL — Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 4365338efa56593e80edcc19cba5944b213d2b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770241"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrowanie bazy danych PostgreSQL metodą zrzutu i przywracania
Za pomocą [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) można wyodrębnić bazę danych PostgreSQL do pliku zrzutu i [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) przywrócić bazę danych PostgreSQL z pliku archiwum utworzonego przez pg_dump.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz:
- [Serwer usługi Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) z regułami zapory zezwalają na dostęp i bazę danych.
- [Zainstalowano pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) i [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) narzędzia wiersza polecenia

Wykonaj następujące kroki, aby zrzucić i przywrócić bazę danych PostgreSQL:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Tworzenie pliku zrzutu przy użyciu pg_dump zawierającego dane, które mają zostać załadowane
Aby przeprowadzić tworzenie kopii zapasowej istniejącej bazy danych PostgreSQL lokalnie lub na maszynie wirtualnej, uruchom następujące polecenie:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Na przykład, jeśli masz serwer lokalny i bazę danych o nazwie **testdb** w nim
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>Przywracanie danych do docelowej bazy danych platformy Azure dla postgreSQL przy użyciu pg_restore
Po utworzeniu docelowej bazy danych można użyć polecenia pg_restore i parametru -d, --dbname, aby przywrócić dane do docelowej bazy danych z pliku zrzutu.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Uwzględnienie parametru --no-owner powoduje, że wszystkie obiekty utworzone podczas przywracania mają na własność użytkownika określonego za pomocą --username. Aby uzyskać więcej informacji, zobacz oficjalną dokumentację PostgreSQL na [temat pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Jeśli serwer PostgreSQL wymaga połączeń SSL (domyślnie włączonych w usłudze Azure Database `PGSSLMODE=require` dla serwerów PostgreSQL), ustaw zmienną środowiskową, aby narzędzie pg_restore łączyło się z SSL. Bez SSL błąd może zostać`FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> W wierszu polecenia systemu `SET PGSSLMODE=require` Windows uruchom polecenie przed uruchomieniem polecenia pg_restore. W systemie Linux lub `export PGSSLMODE=require` Bash uruchom polecenie przed uruchomieniem polecenia pg_restore.
>

W tym przykładzie przywróć dane z pliku zrzutu **testdb.dump** do bazy danych **mypgsqldb** na serwerze docelowym **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Optymalizacja procesu migracji

Jednym ze sposobów migracji istniejącej bazy danych PostgreSQL do usługi Azure Database for PostgreSQL jest utworzenie kopii zapasowej bazy danych w źródle i przywrócenie jej na platformie Azure. Aby zminimalizować czas wymagany do ukończenia migracji, należy rozważyć użycie następujących parametrów z poleceniami tworzenia kopii zapasowej i przywracania.

> [!NOTE]
> Aby uzyskać szczegółowe informacje o składni, zobacz artykuły [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) i [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Dla kopii zapasowej
- Wykonaj kopię zapasową za pomocą przełącznika -Fc, aby można było wykonać przywracanie równolegle, aby przyspieszyć. Przykład:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName -f Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>Dla przywracania
- Firma Microsoft sugeruje przeniesienie pliku kopii zapasowej do maszyny Wirtualnej platformy Azure w tym samym regionie co usługa Azure Database dla serwera PostgreSQL, do którego przeprowadzasz migrację, i wykonaj pg_restore z tej maszyny Wirtualnej, aby zmniejszyć opóźnienia sieciowe. Zaleca się również, że maszyna wirtualna jest tworzona z [włączoną obsługą sieci przyspieszonej.](../virtual-network/create-vm-accelerated-networking-powershell.md)

- Powinno być już wykonane domyślnie, ale otwórz plik zrzutu, aby sprawdzić, czy instrukcje indeksu tworzenia są po wstawieniu danych. Jeśli tak nie jest, przenieść instrukcje tworzenia indeksu po wstawieniu danych.

- Przywróć za pomocą przełączników -Fc i -j, *#* aby zrównać przywracanie. *#* to liczba rdzeni na serwerze docelowym. Można również spróbować *#* z ustawić dwukrotnie liczbę rdzeni serwera docelowego, aby zobaczyć wpływ. Przykład:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- Można również edytować plik zrzutu, dodając zestaw poleceń *synchronous_commit = off;* na początku i zestaw poleceń *synchronous_commit = on; na* końcu. Nie włączenie go na końcu, zanim aplikacje zmienią dane, może spowodować późniejszą utratę danych.

- Na docelowym serwerze usługi Azure Database for PostgreSQL należy rozważyć wykonanie następujących czynności przed przywróceniem:
    - Wyłącz śledzenie wydajności kwerend, ponieważ te statystyki nie są potrzebne podczas migracji. Można to zrobić, ustawiając pg_stat_statements.track, pg_qs.query_capture_mode i pgms_wait_sampling.query_capture_mode na NONE.

    - Użyj wysokiej mocy obliczeniowej i wysokiej pamięci sku, takich jak 32 vCore Memory Optimized, aby przyspieszyć migrację. Po zakończeniu przywracania można łatwo skalować z powrotem do preferowanego sku. Im wyższy sku, tym więcej równoległości można `-j` osiągnąć, zwiększając odpowiedni parametr w pg_restore polecenia. 

    - Więcej usług We/Wy na serwerze docelowym może poprawić wydajność przywracania. Można aprowizować więcej usług We/Wy, zwiększając rozmiar magazynu serwera. To ustawienie nie jest odwracalne, ale należy wziąć pod uwagę, czy wyższe we/wy będą korzystne dla rzeczywistego obciążenia w przyszłości.

Pamiętaj, aby przetestować i sprawdzić poprawność tych poleceń w środowisku testowym przed użyciem ich w środowisku produkcyjnym.

## <a name="next-steps"></a>Następne kroki
- Aby przeprowadzić migrację bazy danych PostgreSQL przy użyciu eksportu i importu, zobacz [Migrowanie bazy danych PostgreSQL przy użyciu eksportu i importowania](howto-migrate-using-export-and-import.md).
- Aby uzyskać więcej informacji na temat migracji baz danych do bazy danych usługi Azure Database for PostgreSQL, zobacz [Przewodnik migracji bazy danych](https://aka.ms/datamigration).
