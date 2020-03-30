---
title: Użyj Ruby do kwerendy
description: W tym temacie przedstawiono sposób użycia języka Ruby do utworzenia programu, który nawiązuje połączenie z usługą Azure SQL Database i wykonuje zapytania za pomocą instrukcji języka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 4bef55b049ee542efdb9d72d13fa196c989c75ec
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826927"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Szybki start: korzystanie z języka Ruby do wykonywania zapytań w bazie danych Azure SQL Database

W tym przewodniku Szybki start pokazano, jak używać języka [Ruby](https://www.ruby-lang.org) w celu nawiązywania połączenia z usługą Azure SQL Database i wysyłać zapytania o dane, korzystając z instrukcji Transact-SQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- Baza danych Azure SQL Database. Aby utworzyć, a następnie skonfigurować bazę danych w usłudze Azure SQL Database, można użyć instrukcji z jednego z tych przewodników Szybki start:

  || Pojedyncza baza danych | Wystąpienie zarządzane |
  |:--- |:--- |:---|
  | Utwórz| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [Cli](scripts/sql-database-create-and-configure-database-cli.md) | [Cli](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Powershell](scripts/sql-database-create-and-configure-database-powershell.md) | [Powershell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurowanie | [Reguła zapory bazująca na adresach IP na poziomie serwera](sql-database-server-level-firewall-rule.md)| [Łączność z maszyny wirtualnej](sql-database-managed-instance-configure-vm.md)|
  |||[Łączność ze środowiska lokalnego](sql-database-managed-instance-configure-p2s.md)
  |Ładowanie danych|Ładowanie bazy danych Adventure Works na potrzeby samouczka Szybki start|[Przywracanie bazy danych Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Przywracanie lub importowanie programów Adventure Works z pliku [BACPAC](sql-database-import.md) z [gitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skrypty zamieszczone w tym artykule korzystają z bazy danych Adventure Works. Za pomocą wystąpienia zarządzanego należy zaimportować bazę danych Adventure Works do bazy danych wystąpienia lub zmodyfikować skrypty znajdujące się w tym artykule, aby korzystały z bazy danych Wide World Importers.
  
- Oprogramowanie związane z językiem Ruby dla Twojego systemu operacyjnego:
  
  - **MacOS**: Zainstaluj Homebrew, rbenv i ruby-build, Ruby, FreeTDS i TinyTDS. Zobacz kroki 1.2, 1.3, 1.4, 1.5 i 2.1 w temacie [Create Ruby apps using SQL Server on macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/) (Tworzenie aplikacji języka Ruby przy użyciu programu SQL Server w systemie macOS).
  
  - **Ubuntu**: Zainstaluj wymagania wstępne dla Ruby, rbenv i ruby-build, Ruby, FreeTDS i TinyTDS. Zobacz kroki 1.2, 1.3, 1.4, 1.5 i 2.1 w temacie [Create Ruby apps using SQL Server on Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/) (Tworzenie aplikacji języka Ruby przy użyciu programu SQL Server w systemie Ubuntu).
  
  - **Windows**: Zainstaluj Ruby, Ruby Devkit i TinyTDS. Zobacz [Configure development environment for Ruby development](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development) (Konfigurowanie środowiska programistycznego pod kątem programowania w języku Ruby).

## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z bazą danych Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

2. Otwórz stronę **Bazy danych SQL** lub **Wystąpienia zarządzane SQL**.

3. Na stronie **Przegląd** znajdź w pełni kwalifikowaną nazwę serwera obok pola **Nazwa serwera** (w przypadku pojedynczej bazy danych) lub w pełni kwalifikowaną nazwę serwera obok pola **Host** (w przypadku wystąpienia zarządzanego). Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**. 

## <a name="create-code-to-query-your-sql-database"></a>Tworzenie kodu zapytania do bazy danych SQL

1. W edytorze tekstów lub kodu utwórz nowy plik o nazwie *sqltest.rb*.
   
1. Dodaj następujący kod. Wartości `<server>`, `<database>`, `<username>` i `<password>` zastąp wartościami ze swojej bazy danych Azure SQL Database.
   
   >[!IMPORTANT]
   >Kod w tym przykładzie używa przykładowych danych AdventureWorksLT, które można wybrać jako źródło podczas tworzenia bazy danych. Jeśli baza danych zawiera różne dane, w zapytaniu SELECT należy użyć tabel z własnej bazy danych. 
   
   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>Uruchamianie kodu

1. W wierszu polecenia uruchom następujące polecenie:

   ```bash
   ruby sqltest.rb
   ```
   
1. Sprawdź, czy jest zwracanych 20 pierwszych wierszy kategorii/produktu z bazy danych. 

## <a name="next-steps"></a>Następne kroki
- [Projektowanie pierwszej bazy danych Azure SQL Database](sql-database-design-first-database.md)
- [Repozytorium GitHub dla TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Zgłaszaj problemy lub zadawaj pytania dotyczące TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Sterownik Ruby dla programu SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
