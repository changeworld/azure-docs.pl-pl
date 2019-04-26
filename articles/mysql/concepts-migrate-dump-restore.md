---
title: Migracja bazy danych MySQL przy użyciu zrzutu i przywracania w usłudze Azure Database for MySQL
description: W tym artykule opisano dwa podstawowe sposoby kopii zapasowej i przywracanie baz danych w usłudze Azure Database for MySQL za pomocą narzędzi takich jak polecenia mysqldump połączenia aplikacji MySQL Workbench i narzędzia PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/02/2018
ms.openlocfilehash: e79c83ecb17c4dcd11f7ccbecded59e7d1d13dfd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525625"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migracja bazy danych MySQL do usługi Azure Database for MySQL przy użyciu zrzutu i przywracania
W tym artykule opisano dwa podstawowe sposoby kopii zapasowej i przywracanie baz danych w usłudze Azure Database for MySQL
- Zrzutu i przywracania z wiersza polecenia (przy użyciu polecenia mysqldump) 
- Zrzucanie i przywracanie przy użyciu narzędzia PHPMyAdmin 

## <a name="before-you-begin"></a>Przed rozpoczęciem
Do wykonania kroków w tym przewodniku, musisz mieć:
- [Tworzenie usługi Azure Database for MySQL server — witryna Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [polecenia mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) zainstalowane na komputerze narzędzie wiersza polecenia.
- Aplikację MySQL Workbench [MySQL Workbench Pobierz](https://dev.mysql.com/downloads/workbench/), Toad, Navicat lub inne narzędzie MySQL firm zrzucanie i przywracanie poleceń.

## <a name="use-common-tools"></a>Użyj standardowych narzędzi
Umożliwia wspólne narzędzia i narzędzia, takie jak połączenia aplikacji MySQL Workbench, polecenia mysqldump, Toad lub Navicat zdalne łączenie i przywrócić dane do usługi Azure Database for MySQL. Użyj takich narzędzi na komputerze klienckim przy użyciu połączenia internetowego do połączenia z usługą Azure Database for MySQL. Użyj połączenia zaszyfrowane protokołem SSL najlepsze rozwiązania dotyczące zabezpieczeń, zobacz też [Konfigurowanie łączności SSL w usłudze Azure Database for MySQL](concepts-ssl-connection-security.md). Nie trzeba przenieść pliki zrzutu do dowolnej lokalizacji w chmurze specjalne, podczas migracji do usługi Azure Database for MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Typowe zastosowania zrzutu i przywracania
Możesz użyć narzędzia MySQL, takie jak polecenia mysqldump i mysqlpump instrukcje dump i load baz danych do bazy danych MySQL na platformie Azure w kilka typowych scenariuszy. W innych sytuacjach można używać [importowanie i eksportowanie](concepts-migrate-import-export.md) zamiast tego podejścia.

- Korzystaj z bazy danych zrzuty, migrując całą bazę danych. To zalecenie posiada podczas przenoszenia dużych ilości danych MySQL, jeśli chcesz zminimalizować zakłócenia usługi dla witryny na żywo lub aplikacji. 
-  Upewnij się, że wszystkie tabele w bazie danych, użyj aparatu InnoDB aparatu magazynu podczas ładowania danych do usługi Azure Database for MySQL. Usługa Azure Database for MySQL obsługuje tylko aparatu magazynu aparatu InnoDB i dlatego nie obsługuje magazynu alternatywnych aparatów. Jeśli tabele są skonfigurowane z innymi aparatami magazynu, przekonwertować je na format aparatu aparatu InnoDB przed migracją do usługi Azure Database for MySQL.
   Na przykład jeśli masz WordPress lub aplikacji sieci Web przy użyciu tabele MyISAM, najpierw przekonwertuj tych tabel przy użyciu funkcji migracji do formatu aparatu InnoDB przed przywróceniem do usługi Azure Database for MySQL. Użyj klauzuli `ENGINE=InnoDB` można ustawić aparat użytą podczas tworzenia nowej tabeli, następnie przenieść dane do tabeli zgodny przed przywróceniem. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Aby uniknąć jakichkolwiek problemów ze zgodnością, upewnij się, że taka sama wersja programu MySQL jest używana w systemach źródłowych i docelowych podczas zrzucania baz danych. Na przykład jeśli istniejący serwer MySQL jest wersji 5.7, następnie należy zmigrować do usługi Azure Database for MySQL skonfigurowane do uruchamiania w wersji 5.7. `mysql_upgrade` Polecenie nie działa w usłudze Azure Database dla serwera MySQL i nie jest obsługiwane. Jeśli potrzebujesz uaktualnienia między wersjami MySQL, najpierw zrzutu lub wyeksportować niższych wersji bazy danych do nowszej wersji MySQL we własnym środowisku. Następnie uruchom `mysql_upgrade`, przed podjęciem próby migracji do usługi Azure Database for MySQL.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności
Aby zoptymalizować wydajność, zwróć uwagę na następujące zagadnienia, podczas zrzucania dużych baz danych:
-   Użyj `exclude-triggers` opcji polecenia mysqldump podczas zrzucania baz danych. Wyklucz wyzwalacze z plików zrzutu w celu uniknięcia poleceń wyzwalacza wyzwalania podczas przywracania danych. 
-   Użyj `single-transaction` opcji, aby ustawić tryb izolacji transakcji i REPEATABLE READ i wysyła instrukcję SQL rozpocząć transakcji do serwera przed zrzucanie danych. Zrzucanie wiele tabel w ramach jednej transakcji powoduje, że niektóre dodatkowego magazynu do użycia podczas przywracania. `single-transaction` Opcji i `lock-tables` opcji wykluczają się wzajemnie tabel blokady powoduje, że wszystkie oczekujące transakcje zatwierdzone niejawnie. Aby zrzutu dużych tabel, łączyć `single-transaction` z opcją `quick` opcji. 
-   Użyj `extended-insert` składni wiele wierszy, która zawiera kilka wartości listy. To powoduje mniejszy plik zrzutu i przyspiesza operacje wstawiania po załadowaniu pliku.
-  Użyj `order-by-primary` opcji polecenia mysqldump podczas zrzucania baz danych, dzięki czemu dane są tworzone w kolejności klucza podstawowego.
-   Użyj `disable-keys` wyłączanie ograniczeń klucza obcego przed obciążenia opcja polecenia mysqldump podczas zrzucania danych. Wyłączenie sprawdzania klucza obcego zapewnia zwiększenie wydajności. Włącz ograniczenia i sprawdź dane po załadowaniu do zapewnienia więzów integralności.
-   Użyj podzielonych tabel, gdy jest to konieczne.
-   Ładowanie danych w sposób równoległy. Unikaj zbyt dużo równoległości, które mogłyby spowodować osiągnął limit zasobów i monitorować zasoby za pomocą metryk dostępnych w witrynie Azure portal. 
-   Użyj `defer-table-indexes` opcji mysqlpump podczas zrzucania baz danych, dzięki czemu tworzenie indeksów się dzieje po załadowaniu danych tabel.
-   Skopiuj pliki kopii zapasowej do obiektu blob platformy Azure/magazynu i przeprowadzenia odzyskiwania z tego miejsca, którego wartością powinna być o wiele szybciej, niż wykonywanie przywracania w Internecie.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Utwórz plik kopii zapasowej z wiersza polecenia przy użyciu polecenia mysqldump
Aby utworzyć kopię zapasową istniejącej bazy danych MySQL na serwerze lokalnym w środowisku lokalnym lub maszynie wirtualnej, uruchom następujące polecenie: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Parametry w celu zapewnienia są następujące:
- [uname] Nazwa użytkownika bazy danych 
- [hasło] Hasło dla bazy danych (Zwróć uwagę, nie ma już miejsca między -p i hasło) 
- [nazwa_bazy_danych] Nazwa bazy danych 
- [backupfile.sql] Nazwa pliku do wykonania kopii zapasowej bazy danych 
- [— zoptymalizowany pod kątem] Opcja polecenia mysqldump 

Na przykład aby utworzyć kopię zapasową bazy danych o nazwie "testdb" na Twoim serwerze MySQL przy użyciu nazwy użytkownika "testuser" i bez hasła w celu testdb_backup.sql pliku, należy użyć następującego polecenia. Polecenie tworzy kopię zapasową `testdb` bazy danych do pliku o nazwie `testdb_backup.sql`, który zawiera instrukcje SQL potrzebne do ponownego utworzenia bazy danych. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Aby wybrać określonych tabel w bazie danych, aby utworzyć kopię zapasową, listę nazw tabel, rozdzielone spacjami. Na przykład aby utworzyć kopię zapasową tylko tabel table1 i table2 z testdb, należy wykonać w tym przykładzie: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Aby jednocześnie utworzyć kopię zapasową kilku baz danych, należy użyć — baza danych przełącznika i wyświetlanie listy nazw baz danych, rozdzielone spacjami. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Aby utworzyć kopię zapasową wszystkich baz danych na serwerze jednocześnie, należy użyć opcji--baz danych wszystkich.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Tworzenie bazy danych w elemencie docelowym — Azure Database dla serwera MySQL
Utwórz pustą bazę danych na docelową usługę Azure Database dla serwera MySQL, w którym chcesz przeprowadzić migrację danych. Użyj narzędzia takiego jak połączenia aplikacji MySQL Workbench, Toad lub Navicat utworzyć bazę danych. Baza danych może mieć taką samą nazwę bazy danych, który jest zawarty zrzut danych lub można utworzyć bazę danych pod inną nazwą.

Nawiązanie połączenia, Znajdź informacje o połączeniu w **Przegląd** z usługi Azure Database for MySQL.

![Znajdź informacje o połączeniu w witrynie Azure portal](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Dodaj informacje o połączeniu w Twojej aplikacji MySQL Workbench.

![Parametry połączenia aplikacji MySQL Workbench](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Przywróć swoje bazy danych MySQL przy użyciu wiersza polecenia lub program MySQL Workbench
Po utworzeniu docelowej bazy danych, można użyć polecenia mysql lub połączenia aplikacji MySQL Workbench do przywrócenia danych do określonego nowo utworzoną bazę danych z pliku zrzutu.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
W tym przykładzie należy przywrócić dane do nowo utworzoną bazę danych w elemencie docelowym — Azure Database dla serwera MySQL.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Eksportowanie przy użyciu narzędzia PHPMyAdmin
Aby wyeksportować, można użyć typowych narzędzi Narzędzia phpMyAdmin, które mogą już zainstalowano lokalnie w swoim środowisku. Aby wyeksportować bazy danych MySQL przy użyciu narzędzia PHPMyAdmin:
1. Otwórz narzędzia phpMyAdmin.
2. Wybierz swoją bazę danych. Kliknij nazwę bazy danych na liście po lewej stronie. 
3. Kliknij przycisk **wyeksportować** łącza. Aby wyświetlić zrzut bazy danych pojawi się nowa strona.
4. W obszarze eksportu kliknij **Zaznacz wszystko** łącze, aby wybrać tabele w bazie danych. 
5. W obszarze Opcje SQL kliknij odpowiednie opcje. 
6. Kliknij przycisk **Zapisz jako plik** opcja i odpowiedniej kompresji opcji, a następnie kliknij przycisk **Przejdź** przycisku. Okno dialogowe powinna zostać wyświetlona monitowania o Zapisz plik lokalnie.

## <a name="import-using-phpmyadmin"></a>Importowanie przy użyciu narzędzia PHPMyAdmin
Importowanie bazy danych jest podobne do eksportowania. Wykonaj następujące czynności:
1. Otwórz narzędzia phpMyAdmin. 
2. Na stronie konfiguracji narzędzia phpMyAdmin kliknij **Dodaj** do dodania usługi Azure Database for MySQL server. Podaj szczegóły połączenia i informacje logowania.
3. Utwórz bazę danych poziomu odpowiednio nazwanych i zaznacz go po lewej stronie ekranu. Aby Nadpisz istniejącą bazę danych, kliknij nazwę bazy danych, zaznacz pola wyboru obok nazwy tabel i wybierz **porzucić** można usunąć istniejące tabele. 
4. Kliknij przycisk **SQL** link umożliwiający wyświetlenie strony, w którym można wpisać poleceń SQL, lub Przekaż plik programu SQL. 
5. Użyj **Przeglądaj** przycisk, aby znaleźć plik bazy danych. 
6. Kliknij przycisk **Przejdź** przycisk, aby wyeksportować kopii zapasowej, wykonywanie poleceń SQL i ponownie utworzyć bazę danych.

## <a name="next-steps"></a>Kolejne kroki
- [Łączenie aplikacji do usługi Azure Database for MySQL](./howto-connection-string.md).
- Aby uzyskać więcej informacji na temat migrowania baz danych do usługi Azure Database for MySQL, zobacz [Przewodnik po migracji bazy danych](https://aka.ms/datamigration).
