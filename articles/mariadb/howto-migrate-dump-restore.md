---
title: Migracja bazy danych MariaDB przy użyciu zrzutu i przywracania w usłudze Azure Database dla serwera MariaDB
description: W tym artykule opisano dwa podstawowe sposoby kopii zapasowej i przywracanie baz danych w usłudze Azure Database dla serwera MariaDB, za pomocą narzędzi takich jak polecenia mysqldump połączenia aplikacji MySQL Workbench i narzędzia PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: bcb76fcbba02bf53b48cc462e3dad8f264db02ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745955"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>Migracja bazy danych MariaDB do usługi Azure Database dla serwera MariaDB przy użyciu zrzutu i przywracania
W tym artykule opisano dwa podstawowe sposoby kopii zapasowej i przywracanie baz danych w usłudze Azure Database dla serwera MariaDB
- Zrzutu i przywracania z wiersza polecenia (przy użyciu polecenia mysqldump) 
- Zrzucanie i przywracanie przy użyciu narzędzia PHPMyAdmin

## <a name="before-you-begin"></a>Przed rozpoczęciem
Do wykonania kroków w tym przewodniku, musisz mieć:
- [Tworzenie usługi Azure Database dla serwera MariaDB — witryna Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [polecenia mysqldump](https://mariadb.com/kb/en/library/mysqldump/) zainstalowane na komputerze narzędzie wiersza polecenia.
- Aplikację MySQL Workbench [MySQL Workbench Pobierz](https://dev.mysql.com/downloads/workbench/), Toad, Navicat lub inne narzędzie MySQL firm zrzucanie i przywracanie poleceń.

## <a name="use-common-tools"></a>Użyj standardowych narzędzi
Umożliwia wspólne narzędzia i narzędzia, takie jak połączenia aplikacji MySQL Workbench, polecenia mysqldump, Toad lub Navicat zdalne łączenie i przywrócić dane do usługi Azure Database dla serwera MariaDB. Użyj takich narzędzi na komputerze klienckim przy użyciu połączenia internetowego do łączenia z usługą Azure Database dla serwera MariaDB. Użyj połączenia zaszyfrowane protokołem SSL najlepsze rozwiązania dotyczące zabezpieczeń, zobacz też [Konfigurowanie łączności SSL w usłudze Azure Database dla serwera MariaDB](concepts-ssl-connection-security.md). Nie trzeba przenieść pliki zrzutu do dowolnej lokalizacji w chmurze specjalne, podczas migracji do usługi Azure Database dla serwera MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Typowe zastosowania zrzutu i przywracania
Można użyć narzędzia MySQL, takie jak polecenia mysqldump i mysqlpump instrukcje dump i load baz danych do usługi Azure Database dla serwera MariaDB w kilka typowych scenariuszy. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Korzystaj z bazy danych zrzuty, migrując całą bazę danych. To zalecenie przechowuje podczas przenoszenia dużych ilości danych lub jeśli chcesz zminimalizować zakłócenia usługi dla witryny na żywo lub aplikacji. 
-  Upewnij się, że wszystkie tabele w bazie danych, użyj aparatu InnoDB aparatu magazynu podczas ładowania danych do usługi Azure Database dla serwera MariaDB. Azure Database dla serwera MariaDB obsługuje tylko aparatu magazynu aparatu InnoDB i dlatego nie obsługuje magazynu alternatywnych aparatów. Jeśli tabele są skonfigurowane z innymi aparatami magazynu, należy przekonwertować je na format aparatu aparatu InnoDB przed migracją do usługi Azure Database dla serwera MariaDB.
   Na przykład jeśli masz WordPress lub aplikacji sieci Web przy użyciu tabele MyISAM, najpierw przekonwertuj tych tabel przy użyciu funkcji migracji do formatu aparatu InnoDB przed przywróceniem do usługi Azure Database dla serwera MariaDB. Użyj klauzuli `ENGINE=InnoDB` można ustawić aparat użytą podczas tworzenia nowej tabeli, następnie przenieść dane do tabeli zgodny przed przywróceniem. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Aby uniknąć jakichkolwiek problemów ze zgodnością, upewnij się, że ta sama wersja MariaDB jest używana w systemach źródłowych i docelowych podczas zrzucania baz danych. Na przykład jeśli istniejącego serwera MariaDB jest wersją 10.2, następnie należy zmigrować do usługi Azure Database dla serwera MariaDB skonfigurowany tak, aby uruchomić wersję 10.2. `mysql_upgrade` Polecenie nie działa w usłudze Azure Database dla serwera MariaDB i nie jest obsługiwane. Jeśli potrzebujesz uaktualnienia między wersjami MariaDB, najpierw zrzutu lub wyeksportować niższych wersji bazy danych do nowszej wersji MariaDB we własnym środowisku. Następnie uruchom `mysql_upgrade`, przed podjęciem próby migracji do usługi Azure Database dla serwera MariaDB.

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

## <a name="create-a-backup-file"></a>Utworzenie pliku kopii zapasowej
Aby utworzyć kopię zapasową istniejącej bazy danych MariaDB, na serwerze lokalnym w środowisku lokalnym lub maszynie wirtualnej, uruchom następujące polecenie, przy użyciu polecenia mysqldump: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Parametry w celu zapewnienia są następujące:
- [uname] Nazwa użytkownika bazy danych 
- [hasło] Hasło dla bazy danych (Zwróć uwagę, nie ma już miejsca między -p i hasło) 
- [nazwa_bazy_danych] Nazwa bazy danych 
- [backupfile.sql] Nazwa pliku do wykonania kopii zapasowej bazy danych 
- [— zoptymalizowany pod kątem] Opcja polecenia mysqldump 

Na przykład aby utworzyć kopię zapasową bazy danych o nazwie "testdb" na serwerze MariaDB przy użyciu nazwy użytkownika "testuser" i bez hasła w celu testdb_backup.sql pliku, należy użyć następującego polecenia. Polecenie tworzy kopię zapasową `testdb` bazy danych do pliku o nazwie `testdb_backup.sql`, który zawiera instrukcje SQL potrzebne do ponownego utworzenia bazy danych. 

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

## <a name="create-a-database-on-the-target-server"></a>Tworzenie bazy danych na serwerze docelowym
Utwórz pustą bazę danych na docelową usługę Azure Database dla serwera MariaDB, w którym chcesz przeprowadzić migrację danych. Użyj narzędzia takiego jak połączenia aplikacji MySQL Workbench, Toad lub Navicat utworzyć bazę danych. Baza danych może mieć taką samą nazwę bazy danych, który jest zawarty zrzut danych lub można utworzyć bazę danych pod inną nazwą.

Nawiązanie połączenia, Znajdź informacje o połączeniu w **Przegląd** z usługi Azure Database dla serwera MariaDB.

![Znajdź informacje o połączeniu w witrynie Azure portal](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Dodaj informacje o połączeniu w Twojej aplikacji MySQL Workbench.

![Parametry połączenia aplikacji MySQL Workbench](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Przywracanie bazy danych MariaDB
Po utworzeniu docelowej bazy danych, można użyć polecenia mysql lub połączenia aplikacji MySQL Workbench do przywrócenia danych do określonego nowo utworzoną bazę danych z pliku zrzutu.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
W tym przykładzie należy przywrócić dane do nowo utworzoną bazę danych w elemencie docelowym — Azure Database dla serwera MariaDB.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Eksportowanie przy użyciu narzędzia PHPMyAdmin
Aby wyeksportować, można użyć typowych narzędzi Narzędzia phpMyAdmin, które mogą już zainstalowano lokalnie w swoim środowisku. Aby wyeksportować bazy danych MariaDB przy użyciu narzędzia PHPMyAdmin:
1. Otwórz narzędzia phpMyAdmin.
2. Wybierz swoją bazę danych. Kliknij nazwę bazy danych na liście po lewej stronie. 
3. Kliknij przycisk **wyeksportować** łącza. Aby wyświetlić zrzut bazy danych pojawi się nowa strona.
4. W obszarze eksportu kliknij **Zaznacz wszystko** łącze, aby wybrać tabele w bazie danych. 
5. W obszarze Opcje SQL kliknij odpowiednie opcje. 
6. Kliknij przycisk **Zapisz jako plik** opcja i odpowiedniej kompresji opcji, a następnie kliknij przycisk **Przejdź** przycisku. Okno dialogowe powinna zostać wyświetlona monitowania o Zapisz plik lokalnie.

## <a name="import-using-phpmyadmin"></a>Importowanie przy użyciu narzędzia PHPMyAdmin
Importowanie bazy danych jest podobne do eksportowania. Wykonaj następujące czynności:
1. Otwórz narzędzia phpMyAdmin. 
2. Na stronie konfiguracji narzędzia phpMyAdmin kliknij **Dodaj** do dodania usługi Azure Database dla serwera MariaDB. Podaj szczegóły połączenia i informacje logowania.
3. Utwórz bazę danych poziomu odpowiednio nazwanych i zaznacz go po lewej stronie ekranu. Aby Nadpisz istniejącą bazę danych, kliknij nazwę bazy danych, zaznacz pola wyboru obok nazwy tabel i wybierz **porzucić** można usunąć istniejące tabele. 
4. Kliknij przycisk **SQL** link umożliwiający wyświetlenie strony, w którym można wpisać poleceń SQL, lub Przekaż plik programu SQL. 
5. Użyj **Przeglądaj** przycisk, aby znaleźć plik bazy danych. 
6. Kliknij przycisk **Przejdź** przycisk, aby wyeksportować kopii zapasowej, wykonywanie poleceń SQL i ponownie utworzyć bazę danych.

## <a name="next-steps"></a>Kolejne kroki
- [Łączenie aplikacji do usługi Azure Database dla serwera MariaDB](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
