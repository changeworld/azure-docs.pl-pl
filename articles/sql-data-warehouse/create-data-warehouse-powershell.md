---
title: 'Szybki Start: Utwórz magazyn danych Azure SQL - programu Azure Powershell | Dokumentacja firmy Microsoft'
description: Szybkie tworzenie serwera logicznego SQL Database, regułę zapory poziomu serwera i magazyn danych przy użyciu programu Azure PowerShell.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e0bb014ec0706d458ff2f38e409efba5d66aaf18
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31529520"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Szybki Start: Tworzenie i zapytań usługi Azure SQL data warehouse przy użyciu programu Azure PowerShell

Szybkie tworzenie usługi Azure SQL data warehouse przy użyciu programu Azure PowerShell.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.1.1 lub nowszym. Uruchom `Get-Module -ListAvailable AzureRM` można znaleźć wersji masz obecnie. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). 


> [!NOTE]
> Utworzenie bazy danych w usłudze SQL Data Warehouse może skutkować powstaniem nowej usługi płatnej.  Aby uzyskać więcej informacji, zobacz [Cennik usługi SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Add-AzureRmAccount
```

Aby wyświetlić subskrypcji, której używasz, uruchom [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Jeśli musisz użyć innej subskrypcji niż domyślna, uruchom [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription).

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Tworzenie zmiennych

Zdefiniuj zmienne do wykorzystania w skryptach w tym przewodniku Szybki start.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Tworzenie serwera logicznego

Utwórz [serwera logicznego Azure SQL](../sql-database/sql-database-servers-databases.md#what-is-an-azure-sql-logical-server) przy użyciu [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) polecenia. Serwer logiczny zawiera grupę baz danych zarządzanych jako grupa. Poniższy przykład obejmuje tworzenie serwera o losowo wybranej nazwie w grupie zasobów za pomocą identyfikatora logowania administratora o nazwie `ServerAdmin` i z hasłem `ChangeYourAdminPassword1`. Zastąp te wstępnie zdefiniowane wartości zgodnie z potrzebami.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurowanie reguły zapory serwera

Utwórz [regułę zapory poziomu serwera Azure SQL](../sql-database/sql-database-firewall-configure.md) przy użyciu [AzureRmSqlServerFirewallRule nowy](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) polecenia. Regułę zapory poziomu serwera umożliwia aplikacji zewnętrznych, takich jak SQL Server Management Studio lub narzędzia SQLCMD, aby nawiązać połączenie SQL data warehouse za pośrednictwem zapory usługi SQL Data Warehouse. W poniższym przykładzie zapora jest otwarta tylko dla innych zasobów platformy Azure. Aby włączyć łączność zewnętrzną, zmień adres IP na adres odpowiedni dla danego środowiska. Aby otworzyć wszystkie adresy IP, użyj wartości 0.0.0.0 jako początkowego adresu IP i wartości 255.255.255.255 jako adresu końcowego.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Baza danych SQL i magazyn danych SQL do komunikacji za pośrednictwem portu 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być zablokowany przez firmową zaporę. Jeśli tak, nie można połączyć się z serwerem Azure SQL, chyba że dział IT otwiera port 1433.
>


## <a name="create-a-data-warehouse-with-sample-data"></a>Utwórz magazyn danych z przykładowymi danymi
W tym przykładzie tworzy hurtowni danych przy użyciu wcześniej zdefiniowanych zmiennych.  Określa cel usługi jako DW400, który stanowi punkt wyjścia tanie dla magazynu danych. 

```Powershell
New-AzureRmSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW400" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Wymagane parametry:

* **RequestedServiceObjectiveName**: ilość [jednostki magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) żądania dostępu. Po zwiększeniu tej kwoty koszt obliczeń. Aby uzyskać listę obsługiwanych wartości, zobacz [limity pamięci i](memory-and-concurrency-limits.md).
* **DatabaseName**: nazwa tworzonej usługi SQL Data Warehouse.
* **ServerName**: Nazwa serwera używanego do utworzenia.
* **ResourceGroupName**: używana grupa zasobów. Aby znaleźć grupy zasobów dostępne w ramach subskrypcji, użyj polecenia cmdlet Get-AzureResource.
* **Edition**: musi mieć wartość „DataWarehouse”, aby utworzyć magazyn SQL Data Warehouse.

Opcjonalne parametry:

- **CollationName**: sortowanie domyślne, gdy sortowanie nie jest określone, to COLLATE SQL_Latin1_General_CP1_CI_AS. Nie można zmienić sortowania bazy danych.
- **MaxSizeBytes**: domyślny maksymalny rozmiar bazy danych to 10 GB.

Aby uzyskać więcej informacji na temat opcji parametrów, zobacz [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne samouczki Szybki start w tej kolekcji bazują na tym przewodniku Szybki start. 

> [!TIP]
> Jeśli planujesz kontynuować pracę z kolejnymi samouczkami Szybki start, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj następujące czynności, aby usunąć wszystkie zasoby utworzone w witrynie Azure Portal w ramach tego przewodnika Szybki start.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Kolejne kroki

Został utworzony magazyn danych i reguła zapory, nawiązano połączenie z magazynem danych i uruchomiono kilka zapytań. Aby dowiedzieć się więcej na temat usługi Azure SQL Data Warehouse, przejdź do samouczka na temat ładowania danych.
> [!div class="nextstepaction"]
>[Ładowanie danych do magazynu danych SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
