---
title: Importowanie i eksportowanie w bazie danych Azure dla programu MySQL
description: W tym artykule opisano typowe sposoby importowania i eksportowania baz danych w bazie danych Azure dla programu MySQL, za pomocą narzędzi, takich jak MySQL Workbench.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 6a4d5fd2bb649b2a0b0d23f4f752442f854e5098
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264905"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrowanie bazy danych MySQL za pomocą importowania i eksportowania
W tym artykule opisano dwa podejścia wspólnej importowanie i eksportowanie danych do bazy danych MySQL serwera Azure przy użyciu narzędzia MySQL Workbench. 

## <a name="before-you-begin"></a>Przed rozpoczęciem
Do wykonania kroków opisanych ten przewodnik, potrzebne są:
- Bazy danych Azure dla serwera MySQL, wykonując [utworzenia bazy danych Azure dla serwera MySQL przy użyciu portalu Azure](quickstart-create-mysql-server-database-using-azure-portal.md).
- MySQL Workbench [pobrane](https://dev.mysql.com/downloads/workbench/), lub innego narzędzia MySQL importowania i eksportowania.

## <a name="use-common-tools"></a>Użyj standardowych narzędzi
Użyj typowych narzędzi, takich jak MySQL Workbench, Toad lub Navicat zdalnie nawiązania połączenia i importowanie lub eksportowanie danych do bazy danych Azure dla programu MySQL. 

Narzędzia takie na komputerze klienckim z połączeniem internetowym nawiązać połączenia z bazą danych Azure dla programu MySQL. Użyj połączenia z protokołem szyfrowania SSL dla najlepsze rozwiązania w zakresie zabezpieczeń, zgodnie z opisem w [łączności Konfigurowanie protokołu SSL w bazie danych Azure dla programu MySQL](concepts-ssl-connection-security.md).

Nie trzeba przenieść importowanie i eksportowanie plików do dowolnej lokalizacji w chmurze specjalnych podczas migracji do bazy danych platformy Azure dla programu MySQL. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Utwórz bazę danych w bazie danych Azure MySQL serwera
Utwórz pustą bazę danych w bazie danych Azure, dla której chcesz przeprowadzić migrację danych serwera MySQL. Utwórz bazę danych za pomocą narzędzia MySQL Workbench, Toad lub Navicat. Baza danych może mieć taką samą nazwę jak bazy danych, który zawiera zrzut danych lub utworzysz bazę danych pod inną nazwą.

Aby skontaktować się, Znajdź informacje o połączeniu w **omówienie** bazy danych Azure dla programu MySQL.

![Znajdź informacje o połączeniu w portalu Azure](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Dodaj informacje o połączeniu do MySQL Workbench.

![Parametry połączenia MySQL Workbench](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Ustalanie, kiedy należy używać importu i eksportu techniki zamiast zrzutu i przywracania
Narzędzia MySQL umożliwia importowanie i eksportowanie bazy danych do bazy danych MySQL Azure w następujących scenariuszach. W innych sytuacjach mogą korzystać z [zrzutu i przywrócić](concepts-migrate-dump-restore.md) zamiast tego podejścia. 

- Musisz wybrać selektywnie kilka tabele, aby importować z istniejącej bazy danych MySQL Azure baza danych MySQL, najlepiej użyj opcji importowania i eksportowania technik.  W ten sposób można pominąć wszystkie zbędne tabele z migracji, aby zaoszczędzić czas i zasoby. Na przykład użyć `--include-tables` lub `--exclude-tables` przełącznik z [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) i `--tables` przełącznik z [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Podczas przenoszenia obiektów bazy danych innej niż tabel, Utwórz jawnie tych obiektów. Obejmują ograniczenia (klucz podstawowy, klucz obcy, indeksy), widoki, funkcje, procedur, wyzwalaczy i inne obiekty bazy danych, które chcesz migrować.
- Podczas migracji danych z zewnętrznych źródeł danych innego niż bazy danych MySQL, tworzenia plików prostych i zaimportuj je za pomocą [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Upewnij się, że wszystkie tabele w bazie danych użyj aparatu magazynu InnoDB podczas ładowania danych do bazy danych Azure dla programu MySQL. Bazy danych platformy Azure dla programu MySQL obsługuje tylko InnoDB aparatu magazynu, więc nie obsługuje aparaty alternatywnych magazynu. Tabele wymagają magazynu alternatywnych aparatów, należy przekonwertować je na potrzeby InnoDB aparat formacie przed migracją do bazy danych Azure dla programu MySQL. 

Na przykład jeśli masz WordPress lub sieci web aplikacji, który używa aparatu MyISAM, najpierw przekonwertuj tabele przy użyciu funkcji migracji danych do tabel InnoDB. Następnie przywróć bazę danych Azure dla programu MySQL. Użyj klauzuli `ENGINE=INNODB` aparatu tworzenia spisu, a następnie przenieść dane do tabeli zgodne przed migracją. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Zalecenia dotyczące wydajności importowania i eksportowania
-   Przed załadowaniem danych, należy utworzyć indeksy klastrowane i klucze podstawowe. Ładowanie danych w kolejności klucza podstawowego. 
-   Opóźnienie tworzenia indeksów pomocniczych dopiero po danych została załadowana. Tworzenie wszystkich indeksów pomocniczych po załadowaniu. 
-   Wyłącz ograniczeń klucza obcego przed załadowaniem. Wyłączenie sprawdzania klucza obcego zapewnia znaczący wzrost wydajności. Włącz ograniczenia i zweryfikować po załadowaniu w celu zapewnienia integralności danych.
-   Ładowanie danych równolegle. Należy unikać zbyt dużo równoległości, które mogłyby spowodować osiągnęła limit zasobów i monitorowanie zasobów przy użyciu metryk, które są dostępne w portalu Azure. 
-   Użyj podzielonych tabel, gdy jest to konieczne.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importowanie i eksportowanie przy użyciu narzędzia MySQL Workbench
Istnieją dwa sposoby eksportowania i importowania danych w MySQL Workbench. Każdy służy do innych celów. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Dane tabeli eksportować i importować kreatorów z menu kontekstowego przeglądarki obiektów
![Kreatorzy MySQL Workbench w menu kontekstowym w przeglądarce obiektów](./media/concepts-migrate-import-export/p1.png)

Kreatorów dla tabeli danych obsługuje importowania i eksportowania operacje przy użyciu plików CSV i JSON. Obejmują one kilka opcji konfiguracji, takich jak separatorów, wybór kolumn i wybór kodowania. Można wykonywać każdego kreatora względem lokalne lub zdalne połączonych serwerów MySQL. Akcja importu zawiera tabeli, kolumny i mapowania typu. 

Dostępne z tych kreatorów z menu kontekstowego przeglądarki obiektów, klikając prawym przyciskiem myszy tabelę. Następnie wybierz opcję **Kreatora eksportu danych tabeli** lub **Kreatora importu danych tabeli**. 

#### <a name="table-data-export-wizard"></a>Kreator eksportu danych tabeli
Poniższy przykład umożliwia wyeksportowanie tabeli do pliku CSV: 
1. Kliknij prawym przyciskiem myszy tabeli bazy danych do wyeksportowania. 
2. Wybierz **tabeli Kreator eksportu danych**. Wybierz kolumny do wyeksportowania, wiersza przesunięcie (jeśli istnieje), a liczba (jeśli istnieje). 
3. Na **wybierz dane do wyeksportowania** kliknij przycisk **dalej**. Wybierz ścieżkę pliku, typu pliku CSV lub JSON. Wybierz również linii separatora, metody otaczającej ciągów i separator pola. 
4. Na **lokalizacja pliku wyjściowego wybierz** kliknij przycisk **dalej**. 
5. Na **eksportować dane** kliknij przycisk **dalej**.

#### <a name="table-data-import-wizard"></a>Kreator importu tabeli danych
Poniższy przykład importuje tabeli z pliku CSV:
1. Kliknij prawym przyciskiem myszy tabeli bazy danych do zaimportowania. 
2. Wyszukaj i wybierz plik CSV do zaimportowania, a następnie kliknij przycisk **dalej**. 
3. Wybierz tabelę docelową (nowego lub istniejącego) i wybierz lub wyczyść **Truncate table przed rozpoczęciem importowania** pole wyboru. Kliknij przycisk **Dalej**.
4. Wybierz kodowanie i kolumn do zaimportowania, a następnie kliknij przycisk **dalej**. 
5. Na **importowania danych** kliknij przycisk **dalej**. Kreator odpowiednio importuje dane.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Danych SQL eksportować i importować kreatorów z poziomu okienka Navigator
Za pomocą Kreatora eksportu lub importu SQL generowane na podstawie MySQL Workbench lub wygenerować polecenia mysqldump. Dostęp do tych kreatorów z **Nawigator** okienku lub wybierając **serwera** z poziomu menu głównego. Następnie wybierz **eksportu danych** lub **importowania danych**. 

#### <a name="data-export"></a>Eksportowanie danych
![Eksportowanie danych MySQL Workbench za pomocą okienka Navigator](./media/concepts-migrate-import-export/p2.png)

Można użyć **eksportu danych** kartę do eksportowania danych MySQL. 
1. Wybierz każdego schematu, który chcesz wyeksportować, opcjonalnie wybierz schematu obiektów/tabel z każdym ze schematów i generowanie eksportu. Opcje konfiguracji obejmują eksportu do folderu projektu lub niezależnym pliku SQL, zrzutu procedury składowane i zdarzenia lub pominąć danych tabeli. 
 
   Można również użyć **wyeksportować zestawu wyników** można wyeksportować określonych zestaw wyników, w edytorze SQL do innego formatu CSV, JSON, HTML i XML. 
3. Wybierz obiekty bazy danych do wyeksportowania, a następnie skonfigurować powiązane opcje.
4. Kliknij przycisk **Odśwież** załadować bieżącego obiektów.
5. Opcjonalnie można otworzyć **zaawansowane opcje** kartę, aby doprecyzować operacji eksportowania. Na przykład dodać blokady tabeli, Zastąp Użyj zamiast instrukcji insert i identyfikatory oferty backtick znaków.
6. Kliknij przycisk **Uruchom eksportowanie** do rozpoczęcia procesu eksportowania.


#### <a name="data-import"></a>Importowanie danych
![Importowanie danych Workbench MySQL przy użyciu nawigatora zarządzania](./media/concepts-migrate-import-export/p3.png)

Można użyć **importu danych** kartę, aby zaimportuj lub Przywróć wyeksportowane dane z operacji eksportowania danych lub polecenie mysqldump. 
1. Wybieranie folderu projektu lub niezależne pliku SQL, schemat, aby zaimportować do lub wybierz **nowy** do definiowania nowego schematu. 
2. Kliknij przycisk **Start zaimportować** do rozpoczęcia procesu importowania.

## <a name="next-steps"></a>Kolejne kroki
- Jako innego sposobu migracji, należy przeczytać [migracji, używając bazy danych MySQL zrzutu i przywrócić w bazie danych Azure dla programu MySQL](concepts-migrate-dump-restore.md).
- Aby uzyskać więcej informacji na temat migracji baz danych do bazy danych Azure dla programu MySQL, zobacz [Przewodnik po migracji bazy danych](http://aka.ms/datamigration). 
