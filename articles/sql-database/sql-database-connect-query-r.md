---
title: Używanie języka R z Machine Learning Services do wykonywania zapytań Azure SQL Database
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W tym artykule pokazano, jak używać skryptu języka R z usługą Azure SQL Database Machine Learning Services do nawiązywania połączeń z bazą danych Azure SQL i wykonywania zapytań przy użyciu instrukcji języka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: ff38346a9b3bd14db51383c116240b030d3ee42a
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68514857"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Szybki start: Używanie języka R z Machine Learning Services do wykonywania zapytań w bazie danych Azure SQL Database (wersja zapoznawcza)

Ten przewodnik Szybki Start przedstawia sposób używania [języka R](https://www.r-project.org/) z usługą Machine Learning Services do nawiązywania połączeń z bazą danych Azure SQL i używania instrukcji języka Transact-SQL do wykonywania zapytań dotyczących danych. Machine Learning Services to funkcja Azure SQL Database używana do wykonywania skryptów języka R w bazie danych. Aby uzyskać więcej informacji, zobacz [Azure SQL Database Machine Learning Services przy użyciu języka R (wersja zapoznawcza)](sql-database-machine-learning-services-overview.md).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Baza danych Azure SQL Database. Aby utworzyć, a następnie skonfigurować bazę danych w usłudze Azure SQL Database, można użyć instrukcji z jednego z tych przewodników Szybki start:

<!-- Managed instance is not supported during the preview
  || Single database | Managed instance |
  |:--- |:--- |:---|
  | Create| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configure | [Server-level IP firewall rule](sql-database-server-level-firewall-rule.md) | [Connectivity from a VM](sql-database-managed-instance-configure-vm.md) |
  ||| [Connectivity from on-site](sql-database-managed-instance-configure-p2s.md) |
  | Load data | Adventure Works loaded per quickstart | [Restore Wide World Importers](sql-database-managed-instance-get-started-restore.md) |
  ||| Restore or import Adventure Works from [BACPAC](sql-database-import.md) file from [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) |
  |||
-->

  || Pojedyncza baza danych |
  |:--- |:--- |
  | Create| [Portal](sql-database-single-database-get-started.md) |
  || [Interfejs wiersza polecenia](scripts/sql-database-create-and-configure-database-cli.md) |
  || [Program PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Konfigurowanie | [Reguła zapory bazująca na adresach IP na poziomie serwera](sql-database-server-level-firewall-rule.md) |
  | Ładuj dane | Ładowanie bazy danych Adventure Works na potrzeby samouczka Szybki start |
  |||

  > [!NOTE]
  > W wersji zapoznawczej Azure SQL Database Machine Learning Services z językiem R opcja wdrożenia wystąpienia zarządzanego nie jest obsługiwana.

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

- Machine Learning Services (z R) włączony. W okresie publicznej wersji zapoznawczej firma Microsoft dołączy Cię i włączy usługę Machine Learning dla Twojej istniejącej lub nowej bazy danych. Postępuj zgodnie z instrukcjami w części [Tworzenie konta na potrzeby korzystania z wersji zapoznawczej](sql-database-machine-learning-services-overview.md#signup).

- Najnowsza [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Skrypty języka R można uruchamiać przy użyciu innych narzędzi do zarządzania bazami danych lub zapytań, ale w tym przewodniku szybki start będziesz używać programu SSMS.

## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z bazą danych Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Otwórz stronę **Bazy danych SQL** lub **Wystąpienia zarządzane SQL**.

3. Na stronie **Przegląd** znajdź w pełni kwalifikowaną nazwę serwera obok pola **Nazwa serwera** (w przypadku pojedynczej bazy danych) lub w pełni kwalifikowaną nazwę serwera obok pola **Host** (w przypadku wystąpienia zarządzanego). Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

## <a name="create-code-to-query-your-sql-database"></a>Tworzenie kodu zapytania do bazy danych SQL

1. Otwórz program **SQL Server Management Studio** i nawiąż połączenie z usługą SQL Database.

   Jeśli potrzebujesz pomocy przy nawiązywaniu połączenia [, zobacz Szybki Start: Używanie programu SQL Server Management Studio do nawiązywania połączenia i wykonywania zapytań dotyczących danych w bazie danych Azure SQL Database](sql-database-connect-query-ssms.md).

1. Przekaż kompletny skrypt języka R do procedury składowanej [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

   Skrypt jest przesyłany przez `@script` argument. Wszystkie elementy wewnątrz `@script` argumentu muszą być prawidłowym kodem R.
   
   >[!IMPORTANT]
   >Kod w tym przykładzie używa przykładowych danych AdventureWorksLT, które można wybrać jako źródło podczas tworzenia bazy danych. Jeśli baza danych zawiera różne dane, w zapytaniu SELECT należy użyć tabel z własnej bazy danych. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Jeśli wystąpią błędy, może to być spowodowane tym, że publiczna wersja zapoznawcza usług Machine Learning Services (z językiem R) nie jest włączona dla usługi SQL Database. Zobacz powyższe [wymagania wstępne](#prerequisites) .

## <a name="run-the-code"></a>Uruchamianie kodu

1. Wykonaj procedurę składowaną [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

1. Sprawdź, czy pierwsze 20 wierszy kategorii/produktu jest zwracanych w oknie **komunikaty** .

## <a name="next-steps"></a>Następne kroki

- [Projektowanie pierwszej bazy danych Azure SQL Database](sql-database-design-first-database.md)
- [Azure SQL Database Machine Learning Services (z językiem R)](sql-database-machine-learning-services-overview.md)
- [Twórz i uruchamiaj proste skrypty języka R w Azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-quickstart-r-create-script.md)
- [Zapisuj zaawansowane funkcje języka R w Azure SQL Database przy użyciu Machine Learning Services (wersja zapoznawcza)](sql-database-machine-learning-services-functions.md)
