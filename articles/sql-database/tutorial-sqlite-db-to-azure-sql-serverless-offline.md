---
title: 'Samouczek: Jak przeprowadzić migrację bazy danych SQLite do usługi Azure SQL Database Serverless'
description: Dowiedz się, jak przeprowadzić migrację w trybie offline z sqlite do usługi Azure SQL Database Serverless przy użyciu usługi Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c718daa4bc99bffd6fcfeb084299bed6682fe884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780511"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Jak przeprowadzić migrację bazy danych SQLite do usługi Azure SQL Database Serverless
Dla wielu osób SQLite zapewnia pierwsze doświadczenie baz danych i programowania SQL. To włączenie do wielu systemów operacyjnych i popularnych aplikacji sprawia, że SQLite jednym z najczęściej stosowanych i używanych silników baz danych na świecie. A ponieważ jest to prawdopodobnie pierwszy silnik bazy danych, z którego korzysta wiele osób, często może on akcesję do centralnej części projektów lub aplikacji. W takich przypadkach, gdy projekt lub aplikacja przerasta początkową implementację SQLite, deweloperzy mogą być zmuszeni do migracji swoich danych do niezawodnego, scentralizowanego magazynu danych.

Usługa Azure SQL Database serverless to warstwa obliczeniowa dla pojedynczych baz danych, która automatycznie skaluje obliczenia na podstawie zapotrzebowania na obciążenia i rachunki za ilość obliczeń używanych na sekundę. Warstwa obliczeniowa bezserwerowa automatycznie wstrzymuje również bazy danych w okresach nieaktywnych, gdy rozliczany jest tylko magazyn i automatycznie wznawia bazy danych po powrocie aktywności.

Po wykonać poniższe kroki, baza danych zostanie zmigrowana do usługi Azure SQL Database Serverless, co umożliwia udostępnienie bazy danych innym użytkownikom lub aplikacjom w chmurze i płacić tylko za to, co używasz, przy minimalnych zmianach kodu aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure
- Baza danych SQLite2 lub SQLite3, którą chcesz przeprowadzić migrację
- Środowisko systemu Windows
  - Jeśli nie masz lokalnego środowiska systemu Windows, możesz użyć maszyny Wirtualnej systemu Windows na platformie Azure do migracji. Przenoszenie i udostępnianie pliku bazy danych SQLite na maszynie wirtualnej przy użyciu usługi Azure Files and Storage Explorer.

## <a name="steps"></a>Kroki

1. Aprowizuj nową usługę Azure SQL Database w warstwie obliczeniowej bez serwera.

    ![zrzut ekranu z witryny Azure Portal przedstawiający przykład inicjowania obsługi administracyjnej dla bazy danych azure sql bez serwera](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. Upewnij się, że plik bazy danych SQLite jest dostępny w środowisku systemu Windows. Zainstaluj sterownik SQLite ODBC, jeśli jeszcze go nie masz (istnieje wiele http://www.ch-werner.de/sqliteodbc/)dostępnych w Open Source, na przykład.

3. Utwórz systemową sieć DSN dla bazy danych. Upewnij się, że używasz aplikacji Administrator źródła danych, która pasuje do architektury systemu (32-bitowe vs 64-bitowe). W ustawieniach systemowych można znaleźć wersję, którą uruchomiliście.

    - Otwórz administratora źródła danych ODBC w danym środowisku.
    - Kliknij kartę systemowy DSN i kliknij "Dodaj"
    - Wybierz zainstalowany łącznik SQLite ODBC i nadaj połączeniu znaczącą nazwę, na przykład sqlitemigrationsource
    - Ustawianie nazwy bazy danych na plik .db
    - Zapisywanie i wyjmować

4. Pobierz i zainstaluj środowisko uruchomieniowe integracji hostowanego samodzielnie. Najprostszym sposobem, aby to zrobić, jest opcja instalacji ekspresowej, jak opisano w dokumentacji. Jeśli zdecydujesz się na ręczną instalację, musisz podać aplikacji klucz uwierzytelniania, który może znajdować się w wystąpieniu usługi Data Factory przez:

    - Uruchamianie usługi ADF (Autor i monitor z usługi w witrynie Azure portal)
    - Kliknij zakładkę "Autor" (niebieski ołówek) po lewej stronie
    - Kliknij pozycję Połączenia (lewy dolny r.), a następnie środowiska wykonawcze integracji
    - Dodaj nowy własny środowisko uruchomieniowe integracji, nadaj mu nazwę, wybierz *opcję 2*.

5. Utwórz nową usługę połączoną dla źródłowej bazy danych SQLite w fabryce danych.

    ![zrzut ekranu przedstawiający pusty blok połączonych usług w usłudze Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. W obszarze Połączenia w obszarze Usługa połączona kliknij pozycję Nowy

7. Wyszukaj i wybierz łącznik "ODBC"


    ![zrzut ekranu przedstawiający logo łącznika ODBC w bloku połączonych usług w usłudze Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. Nadaj połączonej usłudze znaczącą nazwę, na przykład "sqlite_odbc". Wybierz środowisko uruchomieniowe integracji z listy rozwijanej "Połącz za pośrednictwem środowiska uruchomieniowego integracji". Wprowadź poniżej ciąg połączenia, zastępując zmienną Wykaz początkowy ścieżką pliku .db, a DSN nazwą połączenia systemowego DSN: 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Ustawianie typu uwierzytelniania na anonimowy

10. Testowanie połączenia

    ![zrzut ekranu przedstawiający pomyślne połączenie w usłudze Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. Utwórz inną połączony serwis dla obiektu docelowego SQL bez serwera. Wybierz bazę danych za pomocą kreatora usługi połączonej i podaj poświadczenia uwierzytelniania SQL.

    ![zrzut ekranu przedstawiający usługę Azure SQL Database wybraną w usłudze Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. Wyodrębnij instrukcje CREATE TABLE z bazy danych SQLite. Można to zrobić, wykonując poniższy skrypt Pythona w pliku bazy danych.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. Utwórz tabele docelowe landing w środowisku docelowym SQL bez serwera, kopiując instrukcje tabeli CREATE z pliku CreateTables.sql i uruchamiając instrukcje SQL w Edytorze zapytań w witrynie Azure portal.

14. Wróć do ekranu głównego fabryki danych i kliknij przycisk "Kopiuj dane", aby uruchomić za pomocą kreatora tworzenia zadań.

    ![zrzut ekranu przedstawiający logo Kreatora kopiowania danych w usłudze Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. Wybierz wszystkie tabele ze źródłowej bazy danych SQLite przy użyciu pól wyboru i zamapuj je na tabele docelowe w usłudze Azure SQL. Po uruchomieniu zadania pomyślnie zmigrowano dane z SQLite do usługi Azure SQL!

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz [Szybki start: Tworzenie pojedynczej bazy danych w bazie danych SQL azure przy użyciu witryny Azure portal](sql-database-single-database-get-started.md).
- Aby zapoznać się z limitami zasobów zasobów, zobacz [Limity zasobów warstwy obliczeniowej bez użycia serwera](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
