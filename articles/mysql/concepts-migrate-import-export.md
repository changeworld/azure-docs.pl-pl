---
title: Importowanie i eksportowanie — usługa Azure Database for MySQL
description: W tym artykule opisano typowe sposoby importowania i eksportowania baz danych w usłudze Azure Database for MySQL przy użyciu narzędzi, takich jak MySQL Workbench.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 83b0a69e063e9427c726216ef873f5a1c97f9582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163730"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrowanie bazy danych MySQL przy użyciu importu i eksportowania
W tym artykule opisano dwa typowe podejścia do importowania i eksportowania danych do usługi Azure Database dla serwera MySQL przy użyciu mysql workbench. 

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby przejść przez ten przewodnik, potrzebujesz:
- Usługa Azure Database dla serwera MySQL, wykonując [tworzenie usługi Azure Database dla serwera MySQL przy użyciu portalu Azure.](quickstart-create-mysql-server-database-using-azure-portal.md)
- MySQL Workbench [MySQL Workbench Pobierz](https://dev.mysql.com/downloads/workbench/) lub inne narzędzie MySQL innej firmy do importu /eksportu.

## <a name="use-common-tools"></a>Korzystanie z typowych narzędzi
Użyj typowych narzędzi i narzędzi, takich jak MySQL Workbench lub mysqldump, aby zdalnie łączyć się i importować lub eksportować dane do usługi Azure Database for MySQL. 

Użyj takich narzędzi na komputerze klienckim z połączeniem internetowym, aby połączyć się z usługą Azure Database for MySQL. Użyj połączenia szyfrowanego SSL, aby uzyskać najlepsze rozwiązania w zakresie zabezpieczeń, zgodnie z opisem w [temacie Konfigurowanie łączności SSL w bazie danych Azure database dla mysql](concepts-ssl-connection-security.md).

Podczas migracji do bazy danych Azure Database dla mysql nie trzeba przenosić plików importu i eksportu do żadnej specjalnej lokalizacji w chmurze. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Tworzenie bazy danych na serwerze Usługi Azure Database dla MySQL
Utwórz pustą bazę danych na serwerze Azure Database for MySQL, na którym chcesz przeprowadzić migrację danych. Użyj narzędzia, takiego jak MySQL Workbench, Toad lub Navicat, aby utworzyć bazę danych. Baza danych może mieć taką samą nazwę jak baza danych zawierająca dane po cenach dumpingowych lub można utworzyć bazę danych o innej nazwie.

Aby nawiązać połączenie, zlokalizuj informacje o połączeniu w **przeglądzie** usługi Azure Database dla mysql.

![Znajdowanie informacji o połączeniu w witrynie Azure portal](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Dodaj informacje o połączeniu do mysql workbench.

![Ciąg połączenia MySQL Workbench](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Określanie, kiedy mają być używane techniki importowania i eksportowania zamiast zrzutu i przywracania
Narzędzia MySQL służy do importowania i eksportowania baz danych do bazy danych MySQL platformy Azure w następujących scenariuszach. W innych scenariuszach może korzystać z przy użyciu [dump and restore](concepts-migrate-dump-restore.md) podejście zamiast. 

- Jeśli musisz selektywnie wybrać kilka tabel do zaimportowania z istniejącej bazy danych MySQL do bazy danych MySQL platformy Azure, najlepiej użyć techniki importowania i eksportowania.  W ten sposób można pominąć niepotrzebne tabele z migracji, aby zaoszczędzić czas i zasoby. Na przykład użyj `--include-tables` `--exclude-tables` przełącznika lub przełącznika z `--tables` [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) i przełącznika z [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Podczas przenoszenia obiektów bazy danych innych niż tabele jawnie utworzyć te obiekty. Uwzględnij ograniczenia (klucz podstawowy, klucz obcy, indeksy), widoki, funkcje, procedury, wyzwalacze i inne obiekty bazy danych, które chcesz przeprowadzić migrację.
- Podczas migracji danych z zewnętrznych źródeł danych innych niż baza danych MySQL należy tworzyć pliki płaskie i importować je za pomocą [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Upewnij się, że wszystkie tabele w bazie danych używają aparatu magazynu InnoDB podczas ładowania danych do usługi Azure Database dla MySQL. Usługa Azure Database for MySQL obsługuje tylko aparat magazynu InnoDB, więc nie obsługuje alternatywnych aparatów magazynu. Jeśli tabele wymagają alternatywnych aparatów magazynu, należy je przekonwertować do korzystania z formatu aparatu InnoDB przed migracją do usługi Azure Database for MySQL. 

Na przykład, jeśli masz WordPress lub aplikacji sieci web, która używa aparatu MyISAM, najpierw przekonwertować tabele, migrując dane do tabel InnoDB. Następnie przywróć do bazy danych platformy Azure dla mysql. Użyj klauzuli, `ENGINE=INNODB` aby ustawić aparat do tworzenia tabeli, a następnie przenieść dane do zgodnej tabeli przed migracją. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Zalecenia dotyczące wydajności dotyczące importu i eksportu
-   Przed załadowaniem danych należy utworzyć indeksy klastrowane i klucze podstawowe. Załaduj dane w kolejności klucza podstawowego. 
-   Opóźnij tworzenie indeksów pomocniczych, dopóki po załadowaniu danych. Utwórz wszystkie indeksy pomocnicze po załadowaniu. 
-   Wyłącz ograniczenia klucza obcego przed załadowaniem. Wyłączenie kontroli klucza obcego zapewnia znaczny wzrost wydajności. Włącz ograniczenia i sprawdź dane po załadowaniu, aby zapewnić integralność referencyjną.
-   Ładuj dane równolegle. Unikaj zbyt wiele równoległości, które mogłyby spowodować, aby osiągnąć limit zasobów i monitorować zasoby przy użyciu metryk dostępnych w witrynie Azure portal. 
-   W razie potrzeby należy użyć tabel podzielonych na partycje.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importowanie i eksportowanie przy użyciu programu MySQL Workbench
Istnieją dwa sposoby eksportowania i importowania danych w mysql workbench. Każdy z nich służy innemu celowi. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Kreatorzy eksportowania i importowania danych tabeli z menu kontekstowego przeglądarki obiektów
![Kreatorzy mysql workbench w menu kontekstowym przeglądarki obiektów](./media/concepts-migrate-import-export/p1.png)

Kreatorzy danych tabeli obsługują operacje importowania i eksportowania przy użyciu plików CSV i JSON. Obejmują one kilka opcji konfiguracji, takich jak separatory, wybór kolumny i wybór kodowania. Każdy kreator można wykonać na lokalnych lub zdalnie połączonych serwerach MySQL. Akcja importu obejmuje mapowanie tabel, kolumn i typów. 

Dostęp do tych kreatorów można uzyskać z menu kontekstowego przeglądarki obiektów, klikając prawym przyciskiem myszy tabelę. Następnie wybierz **kreatora eksportu danych tabeli** lub **Kreatora importu danych tabeli**. 

#### <a name="table-data-export-wizard"></a>Kreator eksportu danych tabeli
Poniższy przykład eksportuje tabelę do pliku CSV: 
1. Kliknij prawym przyciskiem myszy tabelę bazy danych, która ma zostać wyeksportowana. 
2. Wybierz **Kreatora eksportu danych tabeli**. Wybierz kolumny do wyeksportowania, przesunięcie wiersza (jeśli istnieją) i zlicz (jeśli istnieją). 
3. Na stronie **Wybierz dane do eksportu** kliknij przycisk **Dalej**. Wybierz ścieżkę pliku, plik CSV lub typ pliku JSON. Należy również wybrać separator linii, metodę załączania ciągów i separator pól. 
4. Na stronie **Wybierz lokalizację pliku wyjściowego** kliknij przycisk **Dalej**. 
5. Na stronie **Eksportowanie danych** kliknij przycisk **Dalej**.

#### <a name="table-data-import-wizard"></a>Kreator importu danych tabeli
Poniższy przykład importuje tabelę z pliku CSV:
1. Kliknij prawym przyciskiem myszy tabelę bazy danych, która ma zostać zaimportowana. 
2. Przejdź do pliku CSV do zaimportowania i wybierz go, a następnie kliknij przycisk **Dalej**. 
3. Zaznacz tabelę docelową (nową lub istniejącą) i zaznacz lub wyczyść pole wyboru **Obcinaj tabelę przed importem.** Kliknij przycisk **alej**.
4. Wybierz kodowanie i kolumny do zaimportowania, a następnie kliknij przycisk **Dalej**. 
5. Na stronie **Importowanie danych** kliknij przycisk **Dalej**. Kreator odpowiednio importuje dane.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Kreatorzy eksportowania i importowania danych SQL z okienka Nawigator
Użyj kreatora do eksportowania lub importowania języka SQL wygenerowanego z mySQL Workbench lub wygenerowanego na podstawie polecenia mysqldump. Dostęp do tych kreatorów można uzyskać z **okienka Nawigator** lub wybierając **pozycję Serwer** z menu głównego. Następnie wybierz pozycję **Eksportowanie danych** lub **Importowanie danych**. 

#### <a name="data-export"></a>Eksport danych
![Eksportowanie danych programu MySQL Workbench za pomocą okienka Nawigator](./media/concepts-migrate-import-export/p2.png)

Za pomocą karty **Eksportowanie danych** można wyeksportować dane MySQL. 
1. Wybierz każdy schemat, który chcesz wyeksportować, opcjonalnie wybierz określone obiekty schematu/tabele z każdego schematu i wygeneruj eksport. Opcje konfiguracji obejmują eksport do folderu projektu lub samodzielny plik SQL, zrzucanie przechowywanych procedur i zdarzeń lub pomijanie danych tabeli. 
 
   Alternatywnie należy użyć **eksportu zestawu wyników,** aby wyeksportować określony zestaw wyników w edytorze SQL do innego formatu, takiego jak CSV, JSON, HTML i XML. 
3. Wybierz obiekty bazy danych do wyeksportowania i skonfiguruj powiązane opcje.
4. Kliknij **przycisk Odśwież,** aby załadować bieżące obiekty.
5. Opcjonalnie otwórz kartę **Opcje zaawansowane,** aby zawęzić operację eksportowania. Na przykład dodaj blokady tabeli, użyj zamień zamiast instrukcji wstawiania i identyfikatorów cudzysłowów ze znakami zaplecza.
6. Kliknij **przycisk Rozpocznij eksport,** aby rozpocząć proces eksportowania.


#### <a name="data-import"></a>Import danych
![Import danych programu MySQL Workbench przy użyciu nawigatora zarządzania](./media/concepts-migrate-import-export/p3.png)

Za pomocą karty **Import danych** można importować lub przywracać eksportowane dane z operacji eksportowania danych lub z polecenia mysqldump. 
1. Wybierz folder projektu lub samodzielny plik SQL, wybierz schemat do zaimportowania lub wybierz pozycję **Nowy,** aby zdefiniować nowy schemat. 
2. Kliknij **przycisk Rozpocznij importowanie,** aby rozpocząć proces importowania.

## <a name="next-steps"></a>Następne kroki
- Jako inne podejście do migracji przeczytaj artykuł [Migrowanie bazy danych MySQL przy użyciu zrzutu i przywracania w usłudze Azure Database for MySQL.](concepts-migrate-dump-restore.md)
- Aby uzyskać więcej informacji na temat migracji baz danych do bazy danych usługi Azure Database for MySQL, zobacz [Przewodnik migracji bazy danych](https://aka.ms/datamigration). 
