---
title: Migrowanie przy użyciu zrzutu i przywracania — usługa Azure Database dla mysql
description: W tym artykule opisano dwa typowe sposoby tworzenie kopii zapasowych i przywracanie baz danych w bazie danych platformy Azure dla mysql przy użyciu narzędzi, takich jak mysqldump, MySQL Workbench i PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: b15da2aa83231bfdc8732995888349b06ab56d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163781"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrowanie bazy danych MySQL do usługi Azure Database for MySQL przy użyciu zrzutu i przywracania
W tym artykule opisano dwa typowe sposoby tworzenie kopii zapasowych i przywracanie baz danych w bazie danych platformy Azure dla mysql
- Zrzut i przywracanie z wiersza polecenia (za pomocą mysqldump) 
- Zrzut i przywracanie za pomocą PHPMyAdmin 

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby przejść przez ten przewodnik, musisz mieć:
- [tworzenie serwera usługi Azure Database for MySQL za pomocą witryny Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) narzędzie wiersza polecenia zainstalowany na komputerze.
- MySQL Workbench [MySQL Workbench Pobierz](https://dev.mysql.com/downloads/workbench/) lub inne narzędzie MySQL innej firmy do zrzucania i przywracania poleceń.

## <a name="use-common-tools"></a>Korzystanie z typowych narzędzi
Użyj typowych narzędzi i narzędzi, takich jak MySQL Workbench lub mysqldump, aby zdalnie połączyć i przywrócić dane do usługi Azure Database for MySQL. Użyj takich narzędzi na komputerze klienckim z połączeniem internetowym, aby połączyć się z usługą Azure Database for MySQL. Użyj szyfrowanego połączenia SSL, aby uzyskać najlepsze rozwiązania w zakresie zabezpieczeń, zobacz też [Konfigurowanie łączności SSL w bazie danych Azure database dla mysql](concepts-ssl-connection-security.md). Nie trzeba przenosić plików zrzutu do żadnej specjalnej lokalizacji w chmurze podczas migracji do usługi Azure Database dla mysql. 

## <a name="common-uses-for-dump-and-restore"></a>Typowe zastosowania do zrzutu i przywracania
Narzędzia MySQL, takie jak mysqldump i mysqlpump, można użyć do zrzutu i załadowania baz danych do bazy danych Azure MySQL w kilku typowych scenariuszach. W innych scenariuszach można użyć [importu i eksportu](concepts-migrate-import-export.md) podejście zamiast tego.

- Użyj zrzutów bazy danych podczas migracji całej bazy danych. To zalecenie jest wstrzymywany podczas przenoszenia dużej ilości danych MySQL lub gdy chcesz zminimalizować przerwy w świadczeniu usług dla witryn lub aplikacji na żywo. 
-  Upewnij się, że podczas ładowania danych do usługi Azure Database for MySQL wszystkie tabele w bazie danych korzystają z aparatu magazynu InnoDB. Usługa Azure Database for MySQL obsługuje tylko aparat magazynu InnoDB i dlatego nie obsługuje alternatywnych aparatów magazynu. Jeśli tabele są skonfigurowane z innymi aparatami magazynu, przekonwertuj je na format aparatu InnoDB przed migracją do usługi Azure Database for MySQL.
   Na przykład jeśli masz WordPress lub WebApp przy użyciu tabel MyISAM, najpierw przekonwertować te tabele, migrując do formatu InnoDB przed przywróceniem do usługi Azure Database dla MySQL. Użyj klauzuli, `ENGINE=InnoDB` aby ustawić aparat używany podczas tworzenia nowej tabeli, a następnie przenieść dane do zgodnej tabeli przed przywróceniem. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Aby uniknąć problemów ze zgodnością, upewnij się, że w przypadku zrzucania baz danych w systemach źródłowym i docelowym jest używana ta sama wersja programu MySQL. Na przykład jeśli istniejący serwer MySQL jest w wersji 5.7, należy przeprowadzić migrację do usługi Azure Database for MySQL skonfigurowany do uruchamiania w wersji 5.7. Polecenie `mysql_upgrade` nie działa w bazie danych usługi Azure Database dla serwera MySQL i nie jest obsługiwane. Jeśli chcesz uaktualnić wersje MySQL, najpierw zrzuć lub wyeksportuj do niższej wersji bazy danych do wyższej wersji MySQL we własnym środowisku. Następnie `mysql_upgrade`uruchom program , przed podjęciem próby migracji do bazy danych platformy Azure dla mysql.

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
-   Użyj `skip-definer` tej opcji w mysqlpump, aby pominąć klauzule definiatora i SQL SECURITY z instrukcji create dla widoków i procedur przechowywanych.  Po ponownym załadowaniu pliku zrzutu tworzy obiekty, które używają domyślnych wartości DEFINER i SQL SECURITY.
-   Skopiuj pliki kopii zapasowej do obiektu blob/store platformy Azure i wykonaj przywracanie stamtąd, co powinno być znacznie szybsze niż wykonywanie przywracania w Internecie.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Tworzenie pliku kopii zapasowej z wiersza polecenia przy użyciu mysqldump
Aby przeprowadzić tworzenie kopii zapasowej istniejącej bazy danych MySQL na lokalnym serwerze lokalnym lub na maszynie wirtualnej, uruchom następujące polecenie: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Parametry, które mają być następujące:
- [uname] Nazwa użytkownika bazy danych 
- [pass] Hasło do bazy danych (uwaga nie ma spacji między -p a hasłem) 
- [dbname] Nazwa bazy danych 
- [backupfile.sql] Nazwa pliku kopii zapasowej bazy danych 
- [--opt] Opcja mysqldump 

Na przykład, aby uzyskać kopii zapasowej bazy danych o nazwie "testdb" na serwerze MySQL z nazwą użytkownika "testuser" i bez hasła do pliku testdb_backup.sql, użyj następującego polecenia. Polecenie tworzy zapasową `testdb` bazy danych `testdb_backup.sql`w pliku o nazwie , który zawiera wszystkie instrukcje SQL potrzebne do ponownego utworzenia bazy danych. 

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

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Tworzenie bazy danych na docelowej bazie danych platformy Azure dla serwera MySQL
Utwórz pustą bazę danych na docelowej bazie danych platformy Azure dla serwera MySQL, na którym chcesz przeprowadzić migrację danych. Użyj narzędzia, takiego jak MySQL Workbench, aby utworzyć bazę danych. Baza danych może mieć taką samą nazwę jak baza danych, która zawiera dane po cenach dumpingowych lub można utworzyć bazę danych o innej nazwie.

Aby nawiązać połączenie, zlokalizuj informacje o połączeniu w **przeglądzie** usługi Azure Database dla mysql.

![Znajdowanie informacji o połączeniu w witrynie Azure portal](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Dodaj informacje o połączeniu do warsztatu MySQL.

![Ciąg połączenia mysql workbench](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Przywracanie bazy danych MySQL przy użyciu wiersza polecenia lub systemu MySQL Workbench
Po utworzeniu docelowej bazy danych można użyć polecenia mysql lub MySQL Workbench, aby przywrócić dane do konkretnej nowo utworzonej bazy danych z pliku zrzutu.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
W tym przykładzie przywróć dane do nowo utworzonej bazy danych na docelowej bazie danych platformy Azure dla serwera MySQL.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Eksportowanie za pomocą PHPMyAdmin
Aby wyeksportować, można użyć wspólnego narzędzia phpMyAdmin, które być może zostały już zainstalowane lokalnie w swoim środowisku. Aby wyeksportować bazę danych MySQL za pomocą PHPMyAdmin:
1. Otwórz phpMyAdmin.
2. Wybierz bazę danych. Kliknij nazwę bazy danych na liście po lewej stronie. 
3. Kliknij **łącze Eksportuj.** Pojawi się nowa strona, aby wyświetlić zrzut bazy danych.
4. W obszarze Eksportuj kliknij łącze **Wybierz wszystko,** aby wybrać tabele w bazie danych. 
5. W obszarze opcje SQL kliknij odpowiednie opcje. 
6. Kliknij opcję **Zapisz jako plik** i odpowiednią opcję kompresji, a następnie kliknij przycisk **Przejdź.** Powinno pojawić się okno dialogowe z monitem o zapisanie pliku lokalnie.

## <a name="import-using-phpmyadmin"></a>Importuj za pomocą PHPMyAdmin
Importowanie bazy danych jest podobne do eksportowania. Wykonaj następujące czynności:
1. Otwórz phpMyAdmin. 
2. Na stronie konfiguracji phpMyAdmin kliknij przycisk **Dodaj,** aby dodać usługę Azure Database dla serwera MySQL. Podaj szczegóły połączenia i dane logowania.
3. Utwórz odpowiednio nazwaną bazę danych i wybierz ją po lewej stronie ekranu. Aby przepisać istniejącą bazę danych, kliknij nazwę bazy danych, zaznacz wszystkie pola wyboru obok nazw tabel i wybierz **pozycję Upuść,** aby usunąć istniejące tabele. 
4. Kliknij łącze **SQL,** aby wyświetlić stronę, na której można wpisywać polecenia SQL, lub przekazać plik SQL. 
5. Użyj przycisku **przeglądaj,** aby znaleźć plik bazy danych. 
6. Kliknij przycisk **Przejdź,** aby wyeksportować kopię zapasową, wykonać polecenia SQL i ponownie utworzyć bazę danych.

## <a name="next-steps"></a>Następne kroki
- [Łączenie aplikacji z usługą Azure Database for MySQL](./howto-connection-string.md).
- Aby uzyskać więcej informacji na temat migracji baz danych do bazy danych usługi Azure Database for MySQL, zobacz [Przewodnik migracji bazy danych](https://aka.ms/datamigration).
