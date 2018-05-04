---
title: Korzystanie z języka Python do wykonywania zapytań w bazie danych Azure SQL | Microsoft Docs
description: W tym temacie przedstawiono sposób użycia języka Python do utworzenia programu, który nawiązuje połączenie z bazą danych SQL Azure i wykonuje zapytania za pomocą instrukcji języka Transact-SQL.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: python
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: carlrab
ms.openlocfilehash: e8928a69436cfa0fc601c4a0c544433f3b87a90f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="use-python-to-query-an-azure-sql-database"></a>Korzystanie z języka Python do wykonywania zapytań w bazie danych Azure SQL

 W tym przewodniku Szybki start pokazano, jak używać języka [Python](https://python.org) w celu nawiązywania połączenia z usługą Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane. Aby uzyskać dalsze informacje na temat zestawu SDK, zapoznaj się z naszą dokumentacją [referencyjną](https://docs.microsoft.com/python/api/overview/azure/sql), [przykładem](https://github.com/mkleehammer/pyodbc/wiki/Getting-started) pyodbc i repozytorium GitHub [pyodbc](https://github.com/mkleehammer/pyodbc/wiki/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Reguła zapory poziomu serwera](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) dla publicznego adresu IP komputera, który będzie używany w tym przewodniku Szybki start.

- W systemie operacyjnym zainstalowano język Python i związane z nim oprogramowanie:

    - **System MacOS**: zainstaluj oprogramowania Homebrew i Python, zainstaluj sterownik ODBC i pakiet SQLCMD, a następnie zainstaluj sterownik języka Python dla programu SQL Server. Zobacz [kroki 1.2, 1.3 i 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).
    - **System Ubuntu**: zainstaluj język Python i inne wymagane pakiety, a następnie zainstaluj sterownik języka Python dla programu SQL Server. Zobacz [kroki 1.2, 1.3 i 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu/).
    - **System Windows**: zainstaluj najnowszą wersję języka Python (zmienna środowiskowa została skonfigurowana), zainstaluj sterownik ODBC i pakiet SQLCMD, a następnie zainstaluj sterownik języka Python dla programu SQL Server. Zobacz [kroki 1.2, 1.3 i 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/windows/). 

## <a name="sql-server-connection-information"></a>Informacje o połączeniu z serwerem SQL

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>Wstawianie kodu zapytania bazy danych SQL 

1. W swoim ulubionym edytorze tekstów utwórz nowy plik o nazwie **sqltest.rb**.  

2. Zastąp jego zawartość następującym kodem i dodaj odpowiednie wartości dla serwera, bazy danych, użytkownika i hasła.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print (str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()
```

## <a name="run-the-code"></a>Uruchamianie kodu

1. W wierszu polecenia uruchom następujące polecenia:

   ```Python
   python sqltest.py
   ```

2. Sprawdź, czy zostało zwróconych 20 pierwszych wierszy, a następnie zamknij okno aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Projektowanie pierwszej bazy danych SQL na platformie Azure](sql-database-design-first-database.md)
- [Sterowniki Microsoft Python dla platformy SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/?v=17.23h)

