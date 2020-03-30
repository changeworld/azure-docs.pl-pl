---
title: Używanie języka Python do wykonywania zapytań o bazę danych
description: W tym temacie przedstawiono sposób użycia języka Python do utworzenia programu, który nawiązuje połączenie z bazą danych Azure SQL Database i wykonuje zapytania za pomocą instrukcji języka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: e82f8feae0096202e48a58296dd2e9d21bb61885
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768569"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Szybki start: korzystanie z języka Python do wykonywania zapytań w bazie danych Azure SQL Database

W tym przewodniku Szybki start można użyć języka Python, aby połączyć się z bazą danych SQL platformy Azure i używać instrukcji T-SQL do wykonywania zapytań o dane.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Baza danych SQL platformy Azure](sql-database-single-database-get-started.md)
- [Python](https://python.org/downloads) 3 i powiązane oprogramowanie

  # <a name="macos"></a>[Macos](#tab/macos)

  Aby zainstalować homebrew i Python, sterownik ODBC i SQLCMD oraz sterownik Python dla programu SQL Server, należy wykonać kroki **1.2**, **1.3**i **2.1** w [tworzeniu aplikacji Python przy użyciu programu SQL Server w systemie macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Aby uzyskać więcej informacji, zobacz [Sterownik odbc firmy Microsoft w systemie macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Aby zainstalować Pythona i inne `sudo apt-get install python python-pip gcc g++ build-essential`wymagane pakiety, użyj .

  Aby zainstalować sterownik ODBC, SQLCMD i sterownik Języka Python dla programu SQL Server, zobacz [konfigurowanie środowiska dla środowiska dla środowiska pyodbc Python development](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Aby uzyskać więcej informacji, zobacz [Sterownik MICROSOFT ODBC w systemie Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windows"></a>[Windows](#tab/windows)

  Aby zainstalować język Python, sterownik ODBC i SQLCMD oraz sterownik języka Python dla programu SQL Server, zobacz [konfigurowanie środowiska dla tworzenia pyodbc Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Aby uzyskać więcej informacji, zobacz [Sterownik odbc firmy Microsoft](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---

> [!IMPORTANT]
> Skrypty w tym artykule są zapisywane w celu użycia bazy danych **Adventure Works.**

> [!NOTE]
> Opcjonalnie można wybrać opcję użycia wystąpienia zarządzanego sql platformy Azure.
>
> Aby utworzyć i skonfigurować, należy użyć [usługi Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)lub [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), a następnie skonfigurować łączność [lokacji](sql-database-managed-instance-configure-p2s.md) lub [maszyny Wirtualnej.](sql-database-managed-instance-configure-vm.md)
>
> Aby załadować dane, zobacz [przywracanie za pomocą bacpac](sql-database-import.md) z plikiem [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) lub zobacz [przywracanie bazy danych Szerokich importerów światowych](sql-database-managed-instance-get-started-restore.md).

Aby dalej eksplorować bazę danych Języka Python i sql azure, zobacz [biblioteki bazy danych SQL platformy Azure dla języka Python](/python/api/overview/azure/sql), [repozytorium pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)i [próbki pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z bazą danych Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

2. Przejdź do **bazy danych SQL** lub strony wystąpienia zarządzane **sql.**

3. Na stronie **Przegląd** znajdź w pełni kwalifikowaną nazwę serwera obok pola **Nazwa serwera** (w przypadku pojedynczej bazy danych) lub w pełni kwalifikowaną nazwę serwera obok pola **Host** (w przypadku wystąpienia zarządzanego). Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

## <a name="create-code-to-query-your-sql-database"></a>Tworzenie kodu zapytania do bazy danych SQL 

1. W edytorze tekstów utwórz nowy plik o nazwie *sqltest.py*.  
   
1. Dodaj następujący kod. Zastąp elementy \<server>, \<database>, \<username> i \<password> własnymi wartościami.
   
   >[!IMPORTANT]
   >Kod w tym przykładzie używa przykładowych danych AdventureWorksLT, które można wybrać jako źródło podczas tworzenia bazy danych. Jeśli baza danych zawiera różne dane, w zapytaniu SELECT należy użyć tabel z własnej bazy danych. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Uruchamianie kodu

1. W wierszu polecenia uruchom następujące polecenie:

   ```cmd
   python sqltest.py
   ```

1. Sprawdź, czy jest zwracanych 20 pierwszych wierszy kategorii/produktu, a następnie zamknij okno wiersza polecenia.

## <a name="next-steps"></a>Następne kroki

- [Projektowanie pierwszej bazy danych SQL platformy Azure](sql-database-design-first-database.md)
- [Sterowniki Języka Microsoft Python dla programu SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/?v=17.23h)

