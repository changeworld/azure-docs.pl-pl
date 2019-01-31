---
title: Korzystanie z języka PHP do wykonywania zapytań w bazie danych Azure SQL | Microsoft Docs
description: Sposób użycia języka PHP do utworzenia programu, który nawiązuje połączenie z bazą danych Azure SQL i wykonuje zapytania za pomocą instrukcji języka T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/28/2018
ms.openlocfilehash: b3fe6e0249143b27cb763401a8d328922ed1fe99
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173925"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Szybki start: Korzystanie z języka PHP do wykonywania zapytań w bazie danych Azure SQL

W tym przewodniku Szybki start przedstawiono sposób nawiązywania połączeń z bazą danych Azure SQL za pomocą języka [PHP](http://php.net/manual/en/intro-whatis.php). Następnie można użyć instrukcji języka T-SQL w celu wykonywania zapytań o dane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przykład, upewnij się, że dysponujesz następującymi elementami:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Zainstalowane oprogramowanie związane z językiem PHP odpowiednie dla systemu operacyjnego:

    - W systemie **MacOS** zainstaluj oprogramowanie PHP i sterownik ODBC, a następnie zainstaluj sterownik języka PHP dla programu SQL Server. Zobacz [kroki 1, 2 i 3](/sql/connect/php/installation-tutorial-linux-mac).

    - W systemie **Linux** zainstaluj oprogramowanie PHP i sterownik ODBC, a następnie zainstaluj sterownik języka PHP dla programu SQL Server. Zobacz [kroki 1, 2 i 3](/sql/connect/php/installation-tutorial-linux-mac).

    - W systemie **Windows** zainstaluj język PHP dla usług IIS Express i oprogramowanie Chocolatey, a następnie zainstaluj sterownik ODBC i program SQLCMD. Zobacz [kroki 1.2 i 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-database-connection"></a>Uzyskiwanie połączenia bazy danych

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="add-code-to-query-database"></a>Dodawanie kodu umożliwiającego wykonywanie zapytań w bazie danych

1. W swoim ulubionym edytorze tekstów utwórz nowy plik o nazwie *sqltest.php*.  

1. Zastąp jego zawartość poniższym kodem. Następnie dodaj odpowiednie wartości dla swojego serwera, bazy danych, użytkownika i hasła.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>Uruchamianie kodu

1. W wierszu polecenia uruchom aplikację.

   ```bash
   php sqltest.php
   ```

1. Sprawdź, czy zostało zwróconych 20 pierwszych wierszy, i zamknij okno aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Projektowanie pierwszej bazy danych SQL na platformie Azure](sql-database-design-first-database.md)

- Sterowniki [PHP firmy Microsoft dla programu SQL Server](https://github.com/Microsoft/msphpsql/)

- [Zgłaszanie problemów/zadawanie pytań](https://github.com/Microsoft/msphpsql/issues)

- [Przykład logiki ponowień: nawiązywanie połączeń odpornych na błędy z bazą danych SQL za pomocą języka PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php).
