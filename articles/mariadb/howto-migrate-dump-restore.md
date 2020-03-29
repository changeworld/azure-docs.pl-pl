---
title: Migrowanie za pomocą zrzutu i przywracania — usługa Azure Database dla bazy danych MariaDB
description: W tym artykule opisano dwa typowe sposoby tworzenie kopii zapasowych i przywracanie baz danych w bazie danych platformy Azure dla mariadb przy użyciu narzędzi, takich jak mysqldump, MySQL Workbench i PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 72735e83af97fde8377e27daa45501704ef5a3c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164546"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>Migrowanie bazy danych MariaDB do bazy danych Usługi Azure dla bazy danych MariaDB przy użyciu zrzutu i przywracania
W tym artykule opisano dwa typowe sposoby tworzenie kopii zapasowych i przywracanie baz danych w bazie danych platformy Azure dla bazy danych MariaDB
- Zrzut i przywracanie z wiersza polecenia (za pomocą mysqldump) 
- Zrzut i przywracanie za pomocą PHPMyAdmin

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby przejść przez ten przewodnik, musisz mieć:
- [Tworzenie usługi Azure Database dla serwera MariaDB — witryna Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) narzędzie wiersza polecenia zainstalowany na komputerze.
- MySQL Workbench [MySQL Workbench Pobierz](https://dev.mysql.com/downloads/workbench/) lub inne narzędzie MySQL innej firmy do zrzucania i przywracania poleceń.

## <a name="use-common-tools"></a>Korzystanie z typowych narzędzi
Użyj typowych narzędzi i narzędzi, takich jak MySQL Workbench lub mysqldump, aby zdalnie połączyć i przywrócić dane do usługi Azure Database dla MariaDB. Użyj takich narzędzi na komputerze klienckim z połączeniem internetowym, aby połączyć się z usługą Azure Database for MariaDB. Użyj szyfrowanego połączenia SSL, aby uzyskać najlepsze rozwiązania w zakresie zabezpieczeń, zobacz też [Konfigurowanie łączności SSL w bazie danych Azure database dla mariadb](concepts-ssl-connection-security.md). Nie trzeba przenosić plików zrzutu do żadnej specjalnej lokalizacji w chmurze podczas migracji do usługi Azure Database dla MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Typowe zastosowania do zrzutu i przywracania
Narzędzia MySQL, takie jak mysqldump i mysqlpump, można użyć do zrzutu i załadowania baz danych do usługi Azure Database dla serwera MariaDB w kilku typowych scenariuszach. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Użyj zrzutów bazy danych podczas migracji całej bazy danych. To zalecenie jest wtrzymywać podczas przenoszenia dużej ilości danych lub gdy chcesz zminimalizować przerwy w świadczeniu usług dla witryn lub aplikacji na żywo. 
-  Upewnij się, że podczas ładowania danych do usługi Azure Database for MariaDB wszystkie tabele w bazie danych korzystają z aparatu magazynu InnoDB. Usługa Azure Database for MariaDB obsługuje tylko aparat magazynu Usługi InnoDB i dlatego nie obsługuje alternatywnych aparatów magazynu. Jeśli tabele są skonfigurowane z innymi aparatami magazynu, przekonwertuj je na format aparatu InnoDB przed migracją do usługi Azure Database for MariaDB.
   Na przykład jeśli masz WordPress lub WebApp przy użyciu tabel MyISAM, najpierw przekonwertować te tabele, migrując do formatu InnoDB przed przywróceniem do usługi Azure Database dla MariaDB. Użyj klauzuli, `ENGINE=InnoDB` aby ustawić aparat używany podczas tworzenia nowej tabeli, a następnie przenieść dane do zgodnej tabeli przed przywróceniem. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Aby uniknąć problemów ze zgodnością, upewnij się, że w przypadku zrzucania baz danych w systemach źródłowym i docelowym jest używana ta sama wersja programu MariaDB. Na przykład jeśli istniejący serwer MariaDB jest w wersji 10.2, następnie należy przeprowadzić migrację do bazy danych platformy Azure dla MariaDB skonfigurowany do uruchamiania wersji 10.2. Polecenie `mysql_upgrade` nie działa w bazie danych azure dla serwera MariaDB i nie jest obsługiwane. Jeśli chcesz uaktualnić wersje MariaDB, najpierw zrzuć lub wyeksportuj do niższej wersji bazy danych do wyższej wersji MariaDB we własnym środowisku. Następnie `mysql_upgrade`uruchom program , przed podjęciem próby migracji do bazy danych usługi Azure dla bazy danych MariaDB.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności
Aby zoptymalizować wydajność, należy zwrócić uwagę na następujące kwestie podczas dumpingu dużych baz danych:
-   Użyj `exclude-triggers` opcji w mysqldump podczas dumpingu baz danych. Wyklucz wyzwalacze z plików zrzutu, aby uniknąć uruchamiania poleceń wyzwalacza podczas przywracania danych. 
-   Użyj `single-transaction` opcji, aby ustawić tryb izolacji transakcji na POWTARZALNY ODCZYT i wysyła instrukcję SQL transakcji START do serwera przed wyrzuceniem danych. Dumping wielu tabel w ramach jednej transakcji powoduje, że niektóre dodatkowe miejsca do korzystania podczas przywracania. Opcja `single-transaction` i `lock-tables` opcja wzajemnie się wykluczają, ponieważ TABELE BLOKADY powoduje, że wszystkie oczekujące transakcje mają zostać zatwierdzone niejawnie. Aby zrzucić duże `single-transaction` tabele, `quick` połącz tę opcję z opcją. 
-   Użyj `extended-insert` składni wielowierszowej zawierającej kilka list WARTOŚCI. Powoduje to mniejszy plik zrzutu i przyspiesza wstawia, gdy plik jest ponownie załadowany.
-  Użyj `order-by-primary` tej opcji w mysqldump podczas dumpingu baz danych, tak aby dane były skryptowane w kolejności klucza podstawowego.
-   Użyj `disable-keys` opcji w mysqldump podczas dumpingu danych, aby wyłączyć ograniczenia klucza obcego przed załadowaniem. Wyłączenie kontroli klucza obcego zapewnia wzrost wydajności. Włącz ograniczenia i sprawdź dane po załadowaniu, aby zapewnić integralność referencyjną.
-   W razie potrzeby należy użyć tabel podzielonych na partycje.
-   Ładuj dane równolegle. Unikaj zbyt wiele równoległości, które mogłyby spowodować, aby osiągnąć limit zasobów i monitorować zasoby przy użyciu metryk dostępnych w witrynie Azure portal. 
-   Użyj `defer-table-indexes` tej opcji w mysqlpump podczas dumpingu baz danych, tak aby tworzenie indeksu odbywało się po załadowaniu danych tabel.
-   Skopiuj pliki kopii zapasowej do obiektu blob/store platformy Azure i wykonaj przywracanie stamtąd, co powinno być znacznie szybsze niż wykonywanie przywracania w Internecie.

## <a name="create-a-backup-file"></a>Tworzenie pliku kopii zapasowej
Aby przeprowadzić tworzenie kopii zapasowej istniejącej bazy danych MariaDB na lokalnym serwerze lokalnym lub na maszynie wirtualnej, uruchom następujące polecenie przy użyciu mysqldump: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Parametry, które mają być następujące:
- [uname] Nazwa użytkownika bazy danych 
- [pass] Hasło do bazy danych (uwaga nie ma spacji między -p a hasłem) 
- [dbname] Nazwa bazy danych 
- [backupfile.sql] Nazwa pliku kopii zapasowej bazy danych 
- [--opt] Opcja mysqldump 

Na przykład, aby uzyskać kopii zapasowej bazy danych o nazwie "testdb" na serwerze MariaDB z nazwą użytkownika "testuser" i bez hasła do pliku testdb_backup.sql, użyj następującego polecenia. Polecenie tworzy zapasową `testdb` bazy danych `testdb_backup.sql`w pliku o nazwie , który zawiera wszystkie instrukcje SQL potrzebne do ponownego utworzenia bazy danych. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Aby wybrać określone tabele w bazie danych do utworzenia kopii zapasowej, wyświetl nazwy tabel oddzielone spacjami. Na przykład, aby wykonać kopii zapasowej tylko table1 i table2 tabel z "testdb", wykonaj ten przykład: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Aby uzyskać kopii zapasowej więcej niż jednej bazy danych naraz, należy użyć przełącznika --database i wyświetlić listę nazw bazy danych oddzielonych spacjami. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Tworzenie bazy danych na serwerze docelowym
Utwórz pustą bazę danych na docelowej bazie danych platformy Azure dla serwera MariaDB, na którym chcesz przeprowadzić migrację danych. Użyj narzędzia, takiego jak MySQL Workbench, aby utworzyć bazę danych. Baza danych może mieć taką samą nazwę jak baza danych, która zawiera dane po cenach dumpingowych lub można utworzyć bazę danych o innej nazwie.

Aby nawiązać połączenie, zlokalizuj informacje o połączeniu w **przeglądzie** usługi Azure Database dla mariadb.

![Znajdowanie informacji o połączeniu w witrynie Azure portal](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Dodaj informacje o połączeniu do warsztatu MySQL.

![Ciąg połączenia mysql workbench](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Przywracanie bazy danych MariaDB
Po utworzeniu docelowej bazy danych można użyć polecenia mysql lub MySQL Workbench, aby przywrócić dane do konkretnej nowo utworzonej bazy danych z pliku zrzutu.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
W tym przykładzie przywrócić dane do nowo utworzonej bazy danych na docelowej bazie danych platformy Azure dla serwera MariaDB.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Eksportowanie za pomocą PHPMyAdmin
Aby wyeksportować, można użyć wspólnego narzędzia phpMyAdmin, które być może zostały już zainstalowane lokalnie w swoim środowisku. Aby wyeksportować bazę danych MariaDB za pomocą PHPMyAdmin:
1. Otwórz phpMyAdmin.
2. Wybierz bazę danych. Kliknij nazwę bazy danych na liście po lewej stronie. 
3. Kliknij **łącze Eksportuj.** Pojawi się nowa strona, aby wyświetlić zrzut bazy danych.
4. W obszarze Eksportuj kliknij łącze **Wybierz wszystko,** aby wybrać tabele w bazie danych. 
5. W obszarze opcje SQL kliknij odpowiednie opcje. 
6. Kliknij opcję **Zapisz jako plik** i odpowiednią opcję kompresji, a następnie kliknij przycisk **Przejdź.** Powinno pojawić się okno dialogowe z monitem o zapisanie pliku lokalnie.

## <a name="import-using-phpmyadmin"></a>Importuj za pomocą PHPMyAdmin
Importowanie bazy danych jest podobne do eksportowania. Wykonaj następujące czynności:
1. Otwórz phpMyAdmin. 
2. Na stronie konfiguracji phpMyAdmin kliknij przycisk **Dodaj,** aby dodać usługę Azure Database dla serwera MariaDB. Podaj szczegóły połączenia i dane logowania.
3. Utwórz odpowiednio nazwaną bazę danych i wybierz ją po lewej stronie ekranu. Aby przepisać istniejącą bazę danych, kliknij nazwę bazy danych, zaznacz wszystkie pola wyboru obok nazw tabel i wybierz **pozycję Upuść,** aby usunąć istniejące tabele. 
4. Kliknij łącze **SQL,** aby wyświetlić stronę, na której można wpisywać polecenia SQL, lub przekazać plik SQL. 
5. Użyj przycisku **przeglądaj,** aby znaleźć plik bazy danych. 
6. Kliknij przycisk **Przejdź,** aby wyeksportować kopię zapasową, wykonać polecenia SQL i ponownie utworzyć bazę danych.

## <a name="next-steps"></a>Następne kroki
- [Łączenie aplikacji z usługą Azure Database for MariaDB](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
