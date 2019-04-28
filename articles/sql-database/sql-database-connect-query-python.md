---
title: Korzystanie z języka Python do wykonywania zapytań w bazie danych Azure SQL | Microsoft Docs
description: W tym temacie przedstawiono sposób użycia języka Python do utworzenia programu, który nawiązuje połączenie z bazą danych Azure SQL Database i wykonuje zapytania za pomocą instrukcji języka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 788e21dc5d866bdd6641349ceeeeacbfab98c25c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60866177"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Szybki start: Korzystanie z języka Python do wykonywania zapytań w bazie danych Azure SQL Database

 W tym przewodniku Szybki start pokazano, jak używać języka [Python](https://python.org) w celu nawiązywania połączenia z bazą danych Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane. Aby uzyskać dalsze informacje na temat zestawu SDK, zapoznaj się z naszą dokumentacją [referencyjną](https://docs.microsoft.com/python/api/overview/azure/sql), [repozytorium GitHub pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) i [przykładem pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Baza danych Azure SQL Database. Aby utworzyć, a następnie skonfigurować bazę danych w usłudze Azure SQL Database, można użyć instrukcji z jednego z tych przewodników Szybki start:

  || Pojedyncza baza danych | Wystąpienie zarządzane |
  |:--- |:--- |:---|
  | Przycisk Utwórz| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [Interfejs wiersza polecenia](scripts/sql-database-create-and-configure-database-cli.md) | [Interfejs wiersza polecenia](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Program PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [Program PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurowanie | [Reguła zapory IP na poziomie serwera](sql-database-server-level-firewall-rule.md)| [Łączność z maszyny wirtualnej](sql-database-managed-instance-configure-vm.md)|
  |||[Łączność ze środowiska lokalnego](sql-database-managed-instance-configure-p2s.md)
  |Ładowanie danych|Ładowanie bazy danych Adventure Works na potrzeby samouczka Szybki start|[Przywracanie bazy danych Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Przywróć lub zaimportować Adventure Works z [BACPAC](sql-database-import.md) plik wchodzącej w skład [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skrypty zamieszczone w tym artykule korzystają z bazy danych Adventure Works. Za pomocą wystąpienia zarządzanego należy zaimportować bazę danych Adventure Works do bazy danych wystąpienia lub zmodyfikować skrypty znajdujące się w tym artykule, aby korzystały z bazy danych Wide World Importers.
  
- Oprogramowanie związane z językiem Python dla Twojego systemu operacyjnego:
  
  - **MacOS**: zainstaluj oprogramowanie Homebrew i Python, zainstaluj sterownik ODBC i pakiet SQLCMD, a następnie zainstaluj sterownik języka Python dla programu SQL Server. Zobacz kroki 1.2, 1.3 i 2.1 w temacie [Create Python apps using SQL Server on macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/) (Tworzenie aplikacji języka Python przy użyciu programu SQL Server w systemie macOS). Aby uzyskać więcej informacji, zobacz temat [Install the Microsoft ODBC Driver on Linux and macOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server) (Instalowanie sterownika ODBC firmy Microsoft w systemie Linux i macOS).

  - **Ubuntu**: Zainstaluj język Python i inne wymagane pakiety przy użyciu polecenia `sudo apt-get install python python-pip gcc g++ build-essential`. Pobierz i zainstaluj sterownik ODBC, narzędzie SQLCMD i sterownik języka Python dla programu SQL Server. Aby uzyskać instrukcje, zobacz temat [Configure a development environment for pyodbc Python development](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux) (Konfigurowanie środowiska deweloperskiego na potrzeby programowania w języku Python przy użyciu modułu pyodbc).

  - **Windows**: Zainstaluj oprogramowanie Python, sterownik ODBC, narzędzie SQLCMD i sterownik języka Python dla programu SQL Server. Aby uzyskać instrukcje, zobacz temat [Configure a development environment for pyodbc Python development](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows) (Konfigurowanie środowiska deweloperskiego na potrzeby programowania w języku Python przy użyciu modułu pyodbc).

## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z bazą danych Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Otwórz stronę **Bazy danych SQL** lub **Wystąpienia zarządzane SQL**.

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

## <a name="next-steps"></a>Kolejne kroki

- [Projektowanie pierwszej bazy danych SQL na platformie Azure](sql-database-design-first-database.md)
- [Sterowniki Microsoft Python dla platformy SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/?v=17.23h)

