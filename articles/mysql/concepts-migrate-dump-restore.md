---
title: Migrowanie bazy danych MySQL przy użyciu zrzutu i przywrócić w bazie danych Azure dla programu MySQL
description: W tym artykule opisano dwa podstawowe sposoby kopie zapasowe i przywracanie baz danych w bazie danych Azure dla programu MySQL, za pomocą narzędzi, takich jak mysqldump, MySQL Workbench i PHPMyAdmin.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/02/2018
ms.openlocfilehash: 9d9b9b5eda110ad7fa66bb99501936dda5d45e52
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737049"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrowanie bazy danych MySQL bazą danych Azure dla programu MySQL przy użyciu zrzutu i przywracania
W tym artykule opisano dwa podstawowe sposoby kopie zapasowe i przywracanie baz danych w bazie danych Azure dla programu MySQL
- Zrzut i przywracania z wiersza polecenia (przy użyciu mysqldump) 
- Zrzut i przywracania przy użyciu PHPMyAdmin 

## <a name="before-you-begin"></a>Przed rozpoczęciem
Kroków opisanych w tym przewodnik, wymagane są:
- [Tworzenie bazy danych platformy Azure dla serwera MySQL - portalu Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) na komputerze zainstalowano narzędzia wiersza polecenia.
- MySQL Workbench [Pobierz Workbench MySQL](https://dev.mysql.com/downloads/workbench/), Toad, Navicat lub innego narzędzia MySQL innych firm zrzutu i przywrócić polecenia.

## <a name="use-common-tools"></a>Użyj standardowych narzędzi
Umożliwia typowe narzędzia i narzędzi, takich jak MySQL Workbench, mysqldump, Toad lub Navicat zdalne połączenia i przywrócić dane do bazy danych Azure dla programu MySQL. Narzędzia takie na komputerze klienckim z połączeniem internetowym nawiązać połączenia z bazą danych Azure dla programu MySQL. Połączenie SSL zaszyfrowane za pomocą najlepsze rozwiązania w zakresie zabezpieczeń, zobacz też [łączności Konfigurowanie protokołu SSL w bazie danych Azure dla programu MySQL](concepts-ssl-connection-security.md). Nie trzeba przenieść pliki zrzutu do dowolnej lokalizacji specjalne chmury, podczas migracji do bazy danych platformy Azure dla programu MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Typowe zastosowania zrzutu i przywracania
Możesz użyć narzędzia MySQL, takie jak mysqldump i mysqlpump do zrzutu i obciążenia bazy danych do bazy danych MySQL na platformie Azure w kilka typowych scenariuszy. W innych sytuacjach może używać [importowanie i eksportowanie](concepts-migrate-import-export.md) zamiast tego podejścia.

- Użyj bazy danych zrzuty podczas migracji całej bazy danych. To zalecenie posiada podczas przenoszenia dużych ilości danych MySQL, jeśli chcesz zminimalizować zakłócenia usługi dla na żywo witryn lub aplikacji. 
-  Upewnij się, że wszystkie tabele w bazie danych, użyj aparatu magazynu InnoDB podczas ładowania danych do bazy danych platformy Azure dla programu MySQL. Bazy danych platformy Azure dla programu MySQL obsługuje tylko aparatu magazynu InnoDB i dlatego nie obsługuje aparaty alternatywnych magazynu. Jeśli tabele są skonfigurowane z innych aparatów magazynu, należy przekonwertować je na format aparatu InnoDB przed migracją do bazy danych Azure dla programu MySQL.
   Na przykład jeśli masz WordPress lub aplikacji sieci Web przy użyciu tabel MyISAM, najpierw przekonwertuj te tabele przy użyciu funkcji migracji do formatu InnoDB przed przywróceniem bazą danych Azure dla programu MySQL. Użyj klauzuli `ENGINE=InnoDB` można ustawić aparat używany podczas tworzenia nowej tabeli, następnie przenieść dane do tabeli zgodne przed przywróceniem. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Aby uniknąć problemów ze zgodnością, upewnij się, że ta sama wersja programu MySQL jest używany w systemach źródłowych i docelowych podczas zrzucanie baz danych. Na przykład jeśli istniejący serwer MySQL jest w wersji 5.7, następnie należy zainstalować program bazy danych Azure dla programu MySQL skonfigurowanego do uruchamiania w wersji 5.7. `mysql_upgrade` Polecenie nie działa w bazie danych Azure MySQL serwera i nie jest obsługiwany. Jeśli potrzebujesz uaktualniania różnych wersji MySQL, najpierw zrzutu lub wyeksportować niższe wersji bazy danych do nowszej wersji programu MySQL we własnym środowisku. Następnie uruchom `mysql_upgrade`, przed podjęciem próby wykonania migracji w bazie danych programu Azure dla programu MySQL.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności
Aby zoptymalizować wydajność, należy wykonać powiadomienia o te zagadnienia podczas zrzucanie dużych baz danych:
-   Użyj `exclude-triggers` opcji w mysqldump podczas zrzucanie baz danych. Wyklucz pliki zrzutu, aby uniknąć polecenia wyzwalacza wyzwalania podczas przywracania danych wyzwalaczy. 
-   Użyj `single-transaction` opcję, aby ustawić tryb izolacji transakcji REPEATABLE READ i wysyła instrukcję SQL uruchomić transakcji do serwera przed zrzucanie danych. Zrzucanie wiele tabel w ramach jednej transakcji powoduje, że niektóre dodatkowe miejsce do magazynowania do użycia podczas przywracania. `single-transaction` Opcji i `lock-tables` opcji wykluczają się wzajemnie tabel blokady powoduje, że wszystkie oczekujące niejawnie można zatwierdzić transakcji. Zrzut dużych tabel, należy połączyć `single-transaction` opcję z `quick` opcji. 
-   Użyj `extended-insert` składni wiele wierszy, która obejmuje kilka listy wartości. To powoduje mniejszy plik zrzutu i przyspiesza wstawia po załadowaniu pliku.
-  Użyj `order-by-primary` opcji w mysqldump podczas zrzucanie baz danych, dzięki czemu dane są tworzone w kolejności klucza podstawowego.
-   Użyj `disable-keys` w mysqldump podczas zrzucanie danych, opcję, aby wyłączyć ograniczeń klucza obcego przed obciążenia. Wyłączenie sprawdzania klucza obcego umożliwia zwiększenie wydajności. Włącz ograniczenia i zweryfikować po załadowaniu w celu zapewnienia integralności danych.
-   Użyj podzielonych tabel, gdy jest to konieczne.
-   Ładowanie danych równolegle. Należy unikać zbyt dużo równoległości, które mogłyby spowodować osiągnęła limit zasobów i monitorowanie zasobów przy użyciu metryk, które są dostępne w portalu Azure. 
-   Użyj `defer-table-indexes` opcji w mysqlpump podczas zrzucanie baz danych, dzięki temu tworzenie indeksów odbywa się po załadowaniu danych tabel.
-   Skopiuj pliki kopii zapasowej do obiektów blob Azure/magazynu i przeprowadzenia operacji przywracania z tego miejsca, która powinna być znacznie szybciej niż wykonaniem operacji przywracania w Internecie.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Utworzenie pliku kopii zapasowej z poziomu wiersza polecenia przy użyciu mysqldump
Aby utworzyć kopię zapasową istniejącej bazy danych MySQL na serwerze lokalnym lokalnego lub na maszynie wirtualnej, uruchom następujące polecenie: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Podaj parametry są:
- [uname] Nazwa użytkownika bazy danych 
- [hasło] Hasło bazy danych (należy pamiętać, nie bez spacji między -p i hasło) 
- [dbname] Nazwa bazy danych 
- [backupfile.sql] nazwę pliku kopii zapasowej bazy danych 
- [--opt] Opcja mysqldump 

Na przykład aby utworzyć kopię zapasową bazy danych o nazwie "programu testdb" na serwerze MySQL "testuser" nazwy użytkownika i bez hasła do testdb_backup.sql pliku, należy użyć następującego polecenia. Polecenie tworzy kopię zapasową `testdb` bazy danych w pliku o nazwie `testdb_backup.sql`, który zawiera instrukcje SQL potrzebne do ponownego utworzenia bazy danych. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Aby wybrać określonych tabel w bazie danych, aby utworzyć kopię zapasową, listy nazw tabel, rozdzielając je spacjami. Na przykład aby utworzyć kopię zapasową tabel table1 i table2 z programu "testdb", należy wykonać w tym przykładzie: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Aby utworzyć kopię zapasową więcej niż jednej bazy danych na raz, użyj bazy danych przełącznika i listy nazw baz danych, rozdzielając je spacjami. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Aby utworzyć kopię zapasową wszystkich baz danych na serwerze w tym samym czasie, należy użyć opcji bazy danych wszystkich.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Utwórz bazę danych w celu Azure bazy danych MySQL serwera
Utwórz pustą bazę danych w celu Azure bazy danych dla serwera MySQL, w którym chcesz przeprowadzić migrację danych. Utwórz bazę danych za pomocą narzędzia MySQL Workbench, Toad lub Navicat. Bazy danych może mieć takiej samej nazwie bazy danych, który jest zawarty zrzut danych lub utworzysz bazę danych pod inną nazwą.

Aby skontaktować się, Znajdź informacje o połączeniu w **omówienie** bazy danych Azure dla programu MySQL.

![Znajdź informacje o połączeniu w portalu Azure](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Dodaj informacje o połączeniu do środowiska roboczego programu MySQL.

![Parametry połączenia MySQL Workbench](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Przywracanie bazy danych MySQL przy użyciu wiersza polecenia lub MySQL Workbench
Po utworzeniu docelowej bazy danych, można użyć polecenia mysql lub MySQL Workbench przywrócenie danych do określonego nowo utworzone bazy danych na podstawie pliku zrzutu.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
W tym przykładzie należy przywrócić te dane do nowo utworzonej bazy danych w celu Azure bazy danych MySQL serwera.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Eksportowanie przy użyciu PHPMyAdmin
Aby wyeksportować, można użyć typowych phpMyAdmin narzędzia, które mogą już zainstalowano lokalnie w danym środowisku. Aby wyeksportować za pomocą PHPMyAdmin bazy danych MySQL:
1. Otwórz phpMyAdmin.
2. Wybierz bazę danych. Kliknij nazwę bazy danych na liście z lewej strony. 
3. Kliknij przycisk **wyeksportować** łącza. Aby wyświetlić zrzut bazy danych zostanie wyświetlona strona nowe.
4. W obszarze eksportu kliknij **Zaznacz wszystko** łącze, aby można było wybrać tabele w bazie danych. 
5. W obszarze Opcje SQL kliknij odpowiednie opcje. 
6. Kliknij przycisk **zapisać jako plik** opcja i odpowiedniej kompresji, a następnie kliknij przycisk **Przejdź** przycisku. Okno dialogowe powinna pojawić się monit o Zapisz plik lokalnie.

## <a name="import-using-phpmyadmin"></a>Import za pomocą PHPMyAdmin
Importowanie bazy danych jest podobny do eksportowania. Wykonaj następujące czynności:
1. Otwórz phpMyAdmin. 
2. Na stronie Instalatora phpMyAdmin kliknij **Dodaj** można dodać bazy danych Azure, aby serwer MySQL. Podaj informacje dotyczące połączenia i informacji o logowaniu.
3. Utwórz bazę danych o odpowiedniej nazwie i zaznacz go po lewej stronie ekranu. Aby zmodyfikować istniejącą bazę danych, kliknij nazwę bazy danych, zaznacz pola wyboru obok nazwy tabel i wybierz **porzucić** usunąć istniejące tabele. 
4. Kliknij przycisk **SQL** łącze do wyświetlania strony, gdzie można wpisać w poleceniach SQL, lub Przekaż plik programu SQL. 
5. Użyj **Przeglądaj** przycisk, aby znaleźć plik bazy danych. 
6. Kliknij przycisk **Przejdź** przycisk, aby wyeksportować kopii zapasowej, wykonaj polecenia SQL i ponownie utworzyć bazę danych.

## <a name="next-steps"></a>Kolejne kroki
- [Łączenie aplikacji bazy danych platformy Azure dla programu MySQL](./howto-connection-string.md).
- Aby uzyskać więcej informacji na temat migracji baz danych do bazy danych Azure dla programu MySQL, zobacz [Przewodnik po migracji bazy danych](http://aka.ms/datamigration).
