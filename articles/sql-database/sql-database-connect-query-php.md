---
title: Korzystanie z języka PHP do wykonywania zapytań w bazie danych Azure SQL | Microsoft Docs
description: W tym temacie przedstawiono sposób użycia języka PHP do utworzenia programu, który nawiązuje połączenie z bazą danych SQL Azure i wykonuje zapytania za pomocą instrukcji języka Transact-SQL.
services: sql-database
author: CarlRabeler
manager: jhubbard
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: php
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 238bd98ee4b2c39a30508602384828526c866172
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="use-php-to-query-an-azure-sql-database"></a>Korzystanie z języka PHP do wykonywania zapytań w bazie danych Azure SQL

W tym przewodniku Szybki start pokazano, jak używać [języka PHP](http://php.net/manual/en/intro-whatis.php) w celu utworzenia programu służącego do nawiązywania połączenia z usługą Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Reguła zapory poziomu serwera](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) dla publicznego adresu IP komputera, który będzie używany w tym przewodniku Szybki start.

- W systemie operacyjnym zainstalowano język PHP i związane z nim oprogramowanie:

    - **System MacOS**: zainstaluj oprogramowania Homebrew i PHP, zainstaluj sterownik ODBC i pakiet SQLCMD, a następnie zainstaluj sterownik języka PHP dla programu SQL Server. Zobacz [kroki 1.2, 1.3 i 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/mac/).
    - **System Ubuntu**: zainstaluj język PHP i inne wymagane pakiety, a następnie zainstaluj sterownik języka PHP dla programu SQL Server. Zobacz [kroki 1.2 i 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/).
    - **System Windows**: zainstaluj najnowszą wersję programu PHP dla usług IIS Express, najnowszą wersja sterowników firmy Microsoft dla programu SQL Server w usługach IIS Express, Chocolatey, sterownik ODBC i pakiet SQLCMD. Zobacz [kroki 1.2 i 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).    

## <a name="sql-server-connection-information"></a>Informacje o połączeniu z serwerem SQL

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>Wstawianie kodu zapytania bazy danych SQL

1. W swoim ulubionym edytorze tekstów utwórz nowy plik o nazwie **sqltest.php**.  

2. Zastąp jego zawartość następującym kodem i dodaj odpowiednie wartości dla serwera, bazy danych, użytkownika i hasła.

   ```PHP
   <?php
   $serverName = "your_server.database.windows.net";
   $connectionOptions = array(
       "Database" => "your_database",
       "Uid" => "your_username",
       "PWD" => "your_password"
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

1. W wierszu polecenia uruchom następujące polecenia:

   ```php
   php sqltest.php
   ```

2. Sprawdź, czy zostało zwróconych 20 pierwszych wierszy, a następnie zamknij okno aplikacji.

## <a name="next-steps"></a>Następne kroki
- [Projektowanie pierwszej bazy danych SQL na platformie Azure](sql-database-design-first-database.md)
- Sterowniki [PHP firmy Microsoft dla programu SQL Server](https://github.com/Microsoft/msphpsql/)
- [Zgłaszanie problemów/zadawanie pytań](https://github.com/Microsoft/msphpsql/issues)
- [Przykład logiki ponowień: nawiązywanie połączeń odpornych na błędy z bazą danych SQL za pomocą języka PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

