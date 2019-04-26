---
title: Jak zrzucanie i przywracanie bazy danych Azure database for PostgreSQL
description: W tym artykule opisano sposób wyodrębniania bazę danych PostgreSQL w pliku zrzutu i przywracania z pliku, który został utworzony przez pg_dump w usłudze Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: d406132c4e359c78567ae47a3acba5b73aa39820
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60420346"
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

## <a name="optimizing-the-migration-process"></a>Optymalizowanie procesu migracji

Jednym ze sposobów migracji istniejącej bazy danych postgresql w warstwie do usługi Azure Database for postgresql w warstwie usługi jest tworzenie kopii zapasowej bazy danych w źródle i przywróć ją na platformie Azure. Aby zminimalizować czas wymagany do ukończenia migracji, należy wziąć pod uwagę przy użyciu następujących parametrów przy użyciu kopii zapasowej i przywracanie poleceń.

> [!NOTE]
> Aby uzyskać szczegółowe informacje o składni informacji, zobacz artykuły [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) i [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Dla kopii zapasowej
- Utworzyć kopię zapasową z opcją -Fc przywracania można wykonać równolegle je przyspieszyć. Na przykład:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName > Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>Dla przywracania
- Zalecamy przenoszenia pliku kopii zapasowej na maszynie Wirtualnej platformy Azure, w tym samym regionie, co usługi Azure Database for postgresql w warstwie serwera podczas migrowania do usługi i czy pg_restore z tej maszyny Wirtualnej w celu zmniejszenia opóźnienia sieci. Zalecamy również utworzeniu maszyny Wirtualnej za pomocą [przyspieszoną sieć](../virtual-network/create-vm-accelerated-networking-powershell.md) włączone.

- Należy przeprowadzić już domyślnie, ale Otwieranie pliku zrzutu, aby sprawdzić, czy instrukcje tworzenia indeksu po wstawieniu danych. Jeśli nie jest wymagane, należy przenieść instrukcje tworzenia indeksu po wstawieniu danych.

- Przywróć z przełącznikami -Fc i -j *#* równoległe przetwarzanie przywracania. *#* jest to liczba rdzeni na serwerze docelowym. Możesz też spróbować z *#* równa dwa razy liczbę rdzeni serwera docelowego, aby zobaczyć wpływ. Na przykład:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- Można również edytować plik zrzutu, dodając polecenie *Ustaw synchronous_commit = wyłączone;* na początku i polecenia *Ustaw synchronous_commit = on;* na końcu. Nie włączanie go na końcu, przed aplikacje zmiany danych, może spowodować utratę danych w kolejnych.

- W elemencie docelowym — Azure Database dla serwera PostgreSQL należy rozważyć wykonanie następujących czynności przed przywróceniem:
    - Wyłącz zapytania śledzenia wydajności, ponieważ te statystyki nie są wymagane podczas migracji. You can do this by setting pg_stat_statements.track, pg_qs.query_capture_mode, and pgms_wait_sampling.query_capture_mode to NONE.

    - Aby przyspieszyć proces migracji, należy użyć dużą mocą obliczeniową i jednostki sku dużą ilość pamięci, takich jak 32 — zoptymalizowane pod kątem pamięci rdzeń wirtualny. Możesz łatwo skalować wróci ponownie do preferowanego jednostki sku po zakończeniu przywracania. Im większa jednostki sku, więcej równoległości można osiągnąć przez zwiększenie odpowiedniego `-j` parametr w poleceniu pg_restore. 

    - Więcej operacji We/Wy na serwerze docelowym może poprawić wydajność odzyskiwania. Możesz aprowizować więcej operacji We/Wy przez zwiększenie rozmiaru magazynu serwera. To ustawienie jest nieodwracalna, ale należy wziąć pod uwagę, czy nowszej operacje We/Wy będą w przyszłości korzystać rzeczywistego obciążenia.

Należy pamiętać, testować i weryfikować tych poleceń w środowisku testowym przed ich użyciem w środowisku produkcyjnym.

## <a name="next-steps"></a>Kolejne kroki
- Aby przeprowadzić migrację bazy danych PostgreSQL za pomocą eksportu i importu, zobacz [migracja bazy danych postgresql w warstwie użycie opcji eksportowania i importowania](howto-migrate-using-export-and-import.md).
- Aby uzyskać więcej informacji na temat migrowania baz danych do usługi Azure Database for PostgreSQL, zobacz [Przewodnik po migracji bazy danych](https://aka.ms/datamigration).
