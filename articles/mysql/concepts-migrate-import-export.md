---
title: Importowanie i eksportowanie w usłudze Azure Database for MySQL
description: W tym artykule opisano typowe sposoby importowania i eksportowania bazy danych w usłudze Azure Database for MySQL za pomocą narzędzi, takich jak połączenia aplikacji MySQL Workbench.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/01/2018
ms.openlocfilehash: fa72037c8f54271f5651667765c5d5e2e9c03619
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60838121"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrowanie bazy danych MySQL za pomocą importowania i eksportowania
W tym artykule opisano dwie metody wspólne do importowania i eksportowania danych do serwera Azure Database for MySQL za pomocą aplikacji MySQL Workbench. 

## <a name="before-you-begin"></a>Przed rozpoczęciem
Do wykonania kroków w tym przewodniku, potrzebne są:
- Azure Database for MySQL server, wykonując [serwera Azure Database for MySQL za pomocą witryny Azure portal utworzyć](quickstart-create-mysql-server-database-using-azure-portal.md).
- Aplikację MySQL Workbench [pobrane](https://dev.mysql.com/downloads/workbench/), lub inne narzędzie MySQL do importowania i eksportowania.

## <a name="use-common-tools"></a>Użyj standardowych narzędzi
Typowe narzędzia, takie jak połączenia aplikacji MySQL Workbench, Toad lub Navicat umożliwia zdalne łączenie i importowanie i eksportowanie danych do usługi Azure Database for MySQL. 

Użyj takich narzędzi na komputerze klienckim przy użyciu połączenia internetowego do połączenia z usługą Azure Database for MySQL. Użyj połączenia z protokołem szyfrowania SSL dla najlepsze rozwiązania w zakresie zabezpieczeń, zgodnie z opisem w [Konfigurowanie łączności SSL w usłudze Azure Database for MySQL](concepts-ssl-connection-security.md).

Nie trzeba przenosić importowanie i eksportowanie plików w dowolne miejsce specjalne chmury podczas migracji do usługi Azure Database for MySQL. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Tworzenie bazy danych w usłudze Azure Database dla serwera MySQL
Utwórz pustą bazę danych w usłudze Azure Database dla serwera MySQL, w którym chcesz przeprowadzić migrację danych. Użyj narzędzia takiego jak połączenia aplikacji MySQL Workbench, Toad lub Navicat utworzyć bazę danych. Baza danych może mieć taką samą nazwę jak baza danych zawiera zrzut danych lub można utworzyć bazę danych pod inną nazwą.

Nawiązanie połączenia, Znajdź informacje o połączeniu w **Przegląd** z usługi Azure Database for MySQL.

![Znajdź informacje o połączeniu w witrynie Azure portal](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Dodaj informacje o połączeniu do połączenia aplikacji MySQL Workbench.

![Parametry połączenia aplikacji MySQL Workbench](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Określa, kiedy użyj opcji importowania i eksportowania technik zamiast zrzutu i przywracania
Użyj narzędzia MySQL do importowania i eksportowania bazy danych do bazy danych MySQL w usłudze Azure w następujących scenariuszach. W innych sytuacjach mogą korzystać z [zrzucanie i przywracanie](concepts-migrate-dump-restore.md) zamiast tego podejścia. 

- Gdy zachodzi potrzeba selektywnie wskazać kilka tabel do zaimportowania z istniejącej bazy danych MySQL do usługi Azure MySQL Database, najlepiej jest Użyj importu i eksportu techniki.  Dzięki temu można pominąć wszystkie zbędne tabele z migracji, aby oszczędzić czas i zasoby. Na przykład użyć `--include-tables` lub `--exclude-tables` przełącznik z [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) i `--tables` przełącznik z [polecenia mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- W przypadku przenoszenia obiektów bazy danych inne niż tabele, jawnie tworzyć tych obiektów. Obejmują ograniczenia (klucz podstawowy, klucz obcy, indeksów), widoki, funkcje, procedur, wyzwalaczy i innych obiektów bazy danych, które mają zostać zmigrowane.
- Podczas migrowania danych z zewnętrznych źródeł danych innych niż bazy danych MySQL, tworzenie plików prostych i zaimportuj je za pomocą [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Upewnij się, że wszystkie tabele w bazie danych użyj aparatu InnoDB aparatu magazynu podczas ładowania danych do usługi Azure Database for MySQL. Usługa Azure Database for MySQL obsługuje tylko magazyn aparat InnoDB, więc nie obsługuje magazynu alternatywnych aparatów. Jeśli tabele wymagają magazynu alternatywnych aparatów, pamiętaj przekonwertować do formatu aparatu InnoDB aparatu przed migracją do usługi Azure Database for MySQL. 

Na przykład jeśli masz aplikację WordPress lub sieci web, która używa aparatu MyISAM, najpierw przekonwertuj tabel przy użyciu funkcji migracji danych do aparatu InnoDB tabel. Następnie przywrócić do usługi Azure Database for MySQL. Użyj klauzuli `ENGINE=INNODB` aparat do tworzenia tabeli, a następnie przenieść dane do tabeli zgodny przed migracją. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Zalecenia dotyczące wydajności w celu importowania i eksportowania
-   Przed załadowaniem danych, należy utworzyć indeksy klastrowane i kluczy podstawowych. Załaduj dane w kolejności klucza podstawowego. 
-   Opóźnienie tworzenia indeksów pomocniczych, dopóki dane są ładowane. Utwórz wszystkie indeksy pomocnicze po załadowaniu. 
-   Wyłączanie ograniczeń klucza obcego przed załadowaniem. Wyłączenie sprawdzania klucza obcego zapewnia znaczący wzrost wydajności. Włącz ograniczenia i sprawdź dane po załadowaniu do zapewnienia więzów integralności.
-   Ładowanie danych w sposób równoległy. Unikaj zbyt dużo równoległości, które mogłyby spowodować osiągnął limit zasobów i monitorować zasoby za pomocą metryk dostępnych w witrynie Azure portal. 
-   Użyj podzielonych tabel, gdy jest to konieczne.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importowanie i eksportowanie przy użyciu aplikacji MySQL Workbench
Istnieją dwa sposoby eksportowania i importowania danych w aplikacji MySQL Workbench. Każdy pełni inną funkcję. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Dane tabeli eksportowanie i importowanie kreatorów z poziomu menu kontekstowego przeglądarki obiektów
![Kreatorzy aplikacji MySQL Workbench w menu kontekstowym w przeglądarce obiektów](./media/concepts-migrate-import-export/p1.png)

Kreatorzy dla tabeli danych obsługuje import i operacji eksportu przy użyciu plików CSV i JSON. Obejmują one kilka opcji konfiguracji, takich jak separatory, wybór kolumn i wybór kodowania. Możesz wykonać każdego kreatora przed lokalnym lub zdalnie połączonych serwerów MySQL. Akcja importu obejmuje tabeli, kolumny i mapowania typów. 

Dostępne tych kreatorów z poziomu menu kontekstowego przeglądarki obiektów, klikając prawym przyciskiem myszy tabelę. Następnie wybierz **Kreatora eksportu danych tabeli** lub **Kreatora importu danych tabeli**. 

#### <a name="table-data-export-wizard"></a>Kreator eksportu danych tabeli
Następujący przykład eksportuje tabeli do pliku CSV: 
1. Kliknij prawym przyciskiem myszy tabelę bazy danych do wyeksportowania. 
2. Wybierz **tabeli Kreator eksportu danych**. Wybierz kolumny do wyeksportowania, wiersz przesunięcie (jeśli istnieje) i count (jeśli istnieje). 
3. Na **wybieraniu danych na potrzeby eksportu** kliknij **dalej**. Wybierz ścieżkę pliku, typu pliku CSV lub JSON. Również wybrać separator wiersza, metoda otaczającej ciągów i separator pola. 
4. Na **lokalizacja pliku wyjściowego wybierz** kliknij **dalej**. 
5. Na **eksportowanie danych** kliknij **dalej**.

#### <a name="table-data-import-wizard"></a>Kreator importu danych tabeli
Następujący przykład importuje tabeli z pliku CSV:
1. Kliknij prawym przyciskiem myszy tabelę bazy danych do zaimportowania. 
2. Wyszukaj i wybierz plik CSV do zaimportowania, a następnie kliknij przycisk **dalej**. 
3. Wybierz tabelę docelową (Nowa lub istniejąca) i zaznacz lub wyczyść **Truncate table przed zaimportowaniem** pole wyboru. Kliknij przycisk **Dalej**.
4. Wybierz kodowanie i kolumn, które mają zostać zaimportowane, a następnie kliknij przycisk **dalej**. 
5. Na **importowania danych** kliknij **dalej**. Kreator importuje dane, odpowiednio.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Dane SQL eksportowanie i importowanie kreatorów w okienku Nawigatora
Eksportowanie lub importowanie SQL wygenerowane za pomocą aplikacji MySQL Workbench lub wygenerowanej na podstawie polecenie polecenia mysqldump za pomocą kreatora. Dostęp do tych kreatorów z **Nawigator** okienka lub wybierając **serwera** z menu głównego. Następnie wybierz pozycję **eksportu danych** lub **importowania danych**. 

#### <a name="data-export"></a>Eksport danych
![Aplikację MySQL Workbench eksportu przy użyciu okienko Nawigatora](./media/concepts-migrate-import-export/p2.png)

Możesz użyć **eksportu danych** kartę, aby wyeksportować dane bazy danych MySQL. 
1. Wybierz każdym ze schematów, którą chcesz wyeksportować, opcjonalnie wybierz określonego schematu obiektów/tabel z każdym ze schematów i wygenerować eksportu. Opcje konfiguracji obejmują eksportu do folderu projektu lub niezależna plik SQL, zrzutu procedur składowanych i zdarzenia lub pominąć danych tabeli. 
 
   Można również użyć **Eksportuj zestaw wyników** można wyeksportować określonych zestaw wyników, w edytorze języka SQL na inny format, np. CSV, JSON, HTML i XML. 
3. Wybierz obiekty bazy danych do wyeksportowania, a następnie skonfigurować powiązane opcje.
4. Kliknij przycisk **Odśwież** załadować bieżącego obiektów.
5. Opcjonalnie można otworzyć **zaawansowane opcje** kartę, aby doprecyzować operacji eksportowania. Na przykład można dodać blokady tabeli, Zastąp użycia zamiast instrukcji insert i identyfikatory cudzysłów znaków początkowych.
6. Kliknij przycisk **rozpocząć eksportowanie** do rozpoczęcia procesu eksportu.


#### <a name="data-import"></a>Importowanie danych
![Importowanie danych z aplikacji Workbench MySQL przy użyciu nawigatora zarządzania](./media/concepts-migrate-import-export/p3.png)

Możesz użyć **importu danych** kartę, aby zaimportować lub Przywróć wyeksportowanych danych z operacji eksportowania danych lub polecenie polecenia mysqldump. 
1. Wybierz folder projektu lub pliku SQL niezależna, wybierz schemat, aby importować do lub wybierz **New** do definiowania nowego schematu. 
2. Kliknij przycisk **Rozpocznij importowanie** aby rozpocząć proces importowania.

## <a name="next-steps"></a>Kolejne kroki
- Jako innego podejście do migracji, przeczytaj [migracji usługi MySQL bazy danych przy użyciu zrzutu i przywracania w usłudze Azure Database for MySQL](concepts-migrate-dump-restore.md).
- Aby uzyskać więcej informacji na temat migrowania baz danych do usługi Azure Database for MySQL, zobacz [Przewodnik po migracji bazy danych](https://aka.ms/datamigration). 
