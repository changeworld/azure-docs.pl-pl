---
title: 'Szybki start: Tworzenie usługi Azure SQL Data Warehouse — Azure Powershell | Dokumentacja firmy Microsoft'
description: Szybko utworzyć serwer logiczny SQL Database, regułę zapory na poziomie serwera i magazyn danych przy użyciu programu Azure PowerShell.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 4/11/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: d76f7ac6c8b60e2dec7d7d95cf419e1352b97f15
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545132"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Szybki start: Tworzenie i wysyłanie zapytań usługi Azure SQL data warehouse przy użyciu programu Azure PowerShell

Szybkie tworzenie usługi Azure SQL data warehouse przy użyciu programu Azure PowerShell.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!NOTE]
> Utworzenie bazy danych w usłudze SQL Data Warehouse może skutkować powstaniem nowej usługi płatnej.  Aby uzyskać więcej informacji, zobacz [Cennik usługi SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do swojej subskrypcji platformy Azure za pomocą [Connect AzAccount](/powershell/module/az.accounts/connect-azaccount) polecenia i postępuj zgodnie z wyświetlanymi na ekranie instrukcjami.

```powershell
Connect-AzAccount
```

Aby zobaczyć, której subskrypcji używasz, uruchom [Get AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Jeśli musisz użyć innej subskrypcji niż domyślna, uruchom [AzContext zestaw](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Tworzenie zmiennych

Zdefiniuj zmienne do wykorzystania w skryptach w tym przewodniku Szybki start.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Tworzenie [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) przy użyciu [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) polecenia. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Tworzenie serwera logicznego

Tworzenie [serwer logiczny Azure SQL](../sql-database/sql-database-logical-servers.md) przy użyciu [New AzSqlServer](/powershell/module/az.sql/new-azsqlserver) polecenia. Serwer logiczny zawiera grupę baz danych zarządzanych jako grupa. Poniższy przykład tworzy serwera o losowo wybranej nazwie w grupie zasobów za pomocą administratora o nazwie `ServerAdmin` i z hasłem `ChangeYourAdminPassword1`. Zastąp te wstępnie zdefiniowane wartości zgodnie z potrzebami.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurowanie reguły zapory serwera

Tworzenie [reguły zapory na poziomie serwera Azure SQL](../sql-database/sql-database-firewall-configure.md) przy użyciu [New AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) polecenia. Regułę zapory na poziomie serwera pozwala aplikacji zewnętrznej, takie jak SQL Server Management Studio lub narzędzie SQLCMD, aby nawiązać połączenie z SQL data warehouse przez zaporę usługi SQL Data Warehouse. W poniższym przykładzie zapora jest otwarta tylko dla innych zasobów platformy Azure. Aby włączyć łączność zewnętrzną, zmień adres IP na adres odpowiedni dla danego środowiska. Aby otworzyć wszystkie adresy IP, użyj wartości 0.0.0.0 jako początkowego adresu IP i wartości 255.255.255.255 jako adresu końcowego.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database i SQL Data Warehouse komunikują się za pośrednictwem portu 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być zablokowany przez zaporę sieciową. Jeśli tak, nie można połączyć się z serwerem Azure SQL, chyba że dział IT otworzy port 1433.
>


## <a name="create-a-data-warehouse"></a>Tworzenie magazynu danych
Ten przykład tworzy magazyn danych, korzystając ze zmiennych zdefiniowanych wcześniej.  Określa cel usługi jako DW100c, czyli niższą cenę punkt początkowy dla magazynu danych. 

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Wymagane parametry:

* **RequestedServiceObjectiveName**: Ilość [jednostkami magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) one żądania. Wydłużenie tę kwotę zwiększa koszt obliczeń. Aby uzyskać listę obsługiwanych wartości, zobacz [limity pamięci i współbieżności](memory-and-concurrency-limits.md).
* **DatabaseName**: Nazwa magazynu danych SQL, który tworzysz.
* **ServerName**: Nazwa serwera, którego używasz do tworzenia.
* **ResourceGroupName**: Grupy zasobów, której używasz. Aby znaleźć grupy zasobów dostępne w ramach subskrypcji, użyj polecenia cmdlet Get-AzureResource.
* **Wersja**: Musi mieć wartość "DataWarehouse" Aby utworzyć magazyn danych SQL.

Opcjonalne parametry:

- **CollationName**: Sortowanie domyślne, jeśli nie zostanie określony, to SQL_Latin1_General_CP1_CI_AS. Nie można zmienić sortowania bazy danych.
- **MaxSizeBytes**: Domyślny maksymalny rozmiar bazy danych jest 240TB. Maksymalny rozmiar ogranicza magazynu wierszy danych. Ma nieograniczony magazyn kolumnowy danych.

Aby uzyskać więcej informacji na temat opcji parametrów, zobacz [New AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne samouczki Szybki start w tej kolekcji bazują na tym przewodniku Szybki start. 

> [!TIP]
> Jeśli planujesz kontynuować pracę z kolejnymi samouczkami Szybki Start, nie usuwaj zasobów utworzonych w tym przewodniku Szybki Start. Jeśli nie planujesz kontynuować pracy, należy użyć następujące kroki, można usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki Start w witrynie Azure portal.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Kolejne kroki

Teraz został utworzony magazyn danych, utworzyć regułę zapory na nawiązanie połączenia z magazynem danych i uruchomiono kilka zapytań. Aby dowiedzieć się więcej na temat usługi Azure SQL Data Warehouse, przejdź do samouczka na temat ładowania danych.
> [!div class="nextstepaction"]
>[Ładowanie danych do magazynu danych SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
