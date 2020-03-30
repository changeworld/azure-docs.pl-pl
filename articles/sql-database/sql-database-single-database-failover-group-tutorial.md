---
title: 'Samouczek: Dodawanie pojedynczej bazy danych do grupy trybu failover'
description: Dodaj pojedynczą bazę danych usługi Azure SQL Database do grupy trybu failover przy użyciu witryny Azure portal, powershell lub interfejsu wiersza polecenia platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: c5ce6a1c2f231d372a2a8113eb9043a236090388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061691"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Samouczek: Dodawanie pojedynczej bazy danych usługi Azure SQL Database do grupy trybu failover

[Grupa trybu failover](sql-database-auto-failover-group.md) to deklaratywny warstwa abstrakcji, która umożliwia grupowanie wieloplikowanych baz danych replikowanych geograficznie. Dowiedz się, jak skonfigurować grupę trybu failover dla pojedynczej bazy danych usługi Azure SQL Database i przetestować tryb failover przy użyciu witryny Azure portal, powershell lub interfejsu wiersza polecenia platformy Azure.  Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie pojedynczej bazy danych usługi Azure SQL Database.
> - Utwórz grupę trybu failover dla pojedynczej bazy danych między dwoma logicznymi serwerami SQL.
> - Test pracy awaryjnej.

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami: 

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto,](https://azure.microsoft.com/free/) jeśli jeszcze go nie masz.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Aby ukończyć samouczek, upewnij się, że masz następujące elementy:

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto,](https://azure.microsoft.com/free/) jeśli jeszcze go nie masz.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby ukończyć samouczek, upewnij się, że masz następujące elementy:

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto,](https://azure.microsoft.com/free/) jeśli jeszcze go nie masz.
- Najnowsza wersja [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1 - Tworzenie pojedynczej bazy danych 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - Tworzenie grupy trybu failover 
W tym kroku utworzysz [grupę trybu failover](sql-database-auto-failover-group.md) między istniejącym serwerem SQL platformy Azure a nowym serwerem SQL platformy Azure w innym regionie. Następnie dodaj przykładową bazę danych do grupy trybu failover. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Utwórz grupę trybu failover i dodaj do niej pojedynczą bazę danych za pomocą witryny Azure Portal. 

1. Wybierz **sql platformy Azure** w menu po lewej stronie [witryny Azure portal](https://portal.azure.com). Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql azure w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok **programu Azure SQL,** aby ją ulubieć, i dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz pojedynczą bazę danych utworzoną w sekcji 1, na przykład `mySampleDatabase`. 
1. Grupy trybu failover można skonfigurować na poziomie serwera. Wybierz nazwę serwera w obszarze **Nazwa serwera,** aby otworzyć ustawienia serwera.

   ![Otwórz serwer dla pojedynczej db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Wybierz **pozycję Grupy trybu failover** w okienku **Ustawienia,** a następnie wybierz pozycję **Dodaj grupę,** aby utworzyć nową grupę trybu failover. 

    ![Dodawanie nowej grupy trybu failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na stronie **Grupa trybu failover** wprowadź lub wybierz następujące wartości, a następnie wybierz pozycję **Utwórz:**
    - **Nazwa grupy trybu failover:** Wpisz unikatową nazwę `failovergrouptutorial`grupy trybu failover, taką jak . 
    - **Serwer pomocniczy**: Wybierz opcję *konfigurowania wymaganych ustawień,* a następnie wybierz **pozycję Utwórz nowy serwer**. Alternatywnie można wybrać już istniejący serwer jako serwer pomocniczy. Po wprowadzeniu następujących wartości wybierz pozycję **Wybierz**. 
        - **Nazwa serwera**: Wpisz unikatową nazwę serwera `mysqlsecondary`pomocniczego, taką jak . 
        - **Logowanie administratora serwera**: Typ`azureuser`
        - **Hasło:** Wpisz złożone hasło spełniające wymagania dotyczące hasła.
        - **Lokalizacja**: Wybierz lokalizację z listy rozwijanej, na przykład `East US`. Ta lokalizacja nie może być tą samą lokalizacją co serwer podstawowy.

    > [!NOTE]
    > Ustawienia logowania i zapory serwera muszą być zgodne z ustawieniami serwera podstawowego. 
    
      ![Tworzenie serwera pomocniczego dla grupy trybu failover](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Bazy danych w grupie**: Po wybraniu serwera pomocniczego ta opcja staje się odblokowana. Wybierz go, aby **wybrać bazy danych do dodania,** a następnie wybierz bazę danych utworzoną w sekcji 1. Dodanie bazy danych do grupy trybu failover spowoduje automatyczne uruchomienie procesu replikacji geograficznej. 
        
    ![Dodawanie bazy danych SQL do grupy trybu failover](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Utwórz grupę trybu failover i dodaj do niej pojedynczą bazę danych za pomocą programu PowerShell. 

   > [!NOTE]
   > Ustawienia logowania i zapory serwera muszą być zgodne z ustawieniami serwera podstawowego. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule   
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

Ta część samouczka używa następujących poleceń cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Nowy serwer AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tworzy regułę zapory dla serwera logicznego. | 
| [Nowa baza danych AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Tworzy nową pojedynczą bazę danych usługi Azure SQL Database. | 
| [Nowa grupa AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Tworzy nową grupę trybu failover. |
| [Baza danych Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Pobiera jedną lub więcej baz danych SQL. |
| [Grupa Add-AzSqlDatabaseToFailover](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Dodaje jedną lub więcej baz danych SQL platformy Azure do grupy trybu failover. |

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Utwórz grupę trybu failover i dodaj do niej pojedynczą bazę danych przy użyciu interfejsu wiersza polecenia AZ. 

   > [!NOTE]
   > Ustawienia logowania i zapory serwera muszą być zgodne z ustawieniami serwera podstawowego. 

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary logical server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password
   
   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

Ta część samouczka używa następujących poleceń cmdlet Az CLI:

| Polecenie | Uwagi |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [az sql server firewall-rule create az sql server firewall-rule create az sql server firewall-rule create az](/cli/azure/sql/server/firewall-rule) | Tworzy reguły zapory serwera. | 
| [az sql tworzenie grupy trybu failover](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Tworzy grupę trybu failover. | 

---

## <a name="3---test-failover"></a>3 - Test pracy awaryjnej 
W tym kroku zakończy się niepowodzeniem grupy trybu failover na serwerze pomocniczym, a następnie po awarii przy użyciu witryny Azure portal. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Przetestuj przewija po awarii przy użyciu witryny Azure portal. 

1. Wybierz **sql platformy Azure** w menu po lewej stronie [witryny Azure portal](https://portal.azure.com). Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql azure w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok **programu Azure SQL,** aby ją ulubieć, i dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz pojedynczą bazę danych utworzoną `mySampleDatbase`w sekcji 2, na przykład . 
1. Wybierz nazwę serwera w obszarze **Nazwa serwera,** aby otworzyć ustawienia serwera.

   ![Otwórz serwer dla pojedynczej db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Wybierz **pozycję Grupy trybu failover** w okienku **Ustawienia,** a następnie wybierz grupę trybu failover utworzoną w sekcji 2. 
  
   ![Wybieranie grupy trybu failover z portalu](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Sprawdź, który serwer jest podstawowy, a który pomocniczy. 
1. Wybierz **opcję Praca awaryjna** z okienka zadań, aby zakończyć po awarii grupę trybu failover zawierającą przykładową pojedynczą bazę danych. 
1. Wybierz **pozycję Tak** w ostrzeżeniu, które powiadamia o rozłączeniu sesji TDS. 

   ![Praca awaryjna grupy trybu failover zawierającej bazę danych SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Sprawdź, który serwer jest teraz podstawowy, a który pomocniczy. Jeśli przeminie w trybie fail over zakończyło się pomyślnie, dwa serwery powinny mieć zamienione role. 
1. Wybierz ponownie **opcję Przewijanie awaryjne,** aby serwery zostały ponownie powięksne do ich pierwotnie ról. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Przetestuj przewijaniec awaryjny przy użyciu programu PowerShell. 


Sprawdź rolę repliki pomocniczej: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Przeładuj awaryjnie do serwera pomocniczego: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName 
   ```

Przywróć grupę trybu failover z powrotem do serwera podstawowego:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

Ta część samouczka używa następujących poleceń cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Grupa Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera lub wyświetla listę grup trybu failover usługi SQL Database platformy Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Wykonuje przebłaję awaryjną grupy trybu failover usługi Azure SQL Database. |



# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Przetestuj przewijalnie awaryjne przy użyciu interfejsu wiersza polecenia AZ. 

Sprawdź, który serwer jest pomocniczy:

   
   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Przeładuj awaryjnie do serwera pomocniczego: 

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Przywróć grupę trybu failover z powrotem do serwera podstawowego:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

Ta część samouczka używa następujących poleceń cmdlet Az CLI:

| Polecenie | Uwagi |
|---|---|
| [az sql lista grup trybu failover](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Wyświetla listę grup trybu failover na serwerze. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Ustaw podstawową grupę trybu failover, korzystając z niepowodzenia wszystkich baz danych z bieżącego serwera podstawowego. | 

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
Oczyść zasoby, usuwając grupę zasobów. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Usuń grupę zasobów przy użyciu witryny Azure portal. 

1. Przejdź do grupy zasobów w [witrynie Azure portal](https://portal.azure.com).
1. Wybierz **pozycję Usuń grupę zasobów,** aby usunąć wszystkie zasoby w grupie, a także samą grupę zasobów. 
1. Wpisz nazwę grupy zasobów `myResourceGroup`w obszarze tekstowym, a następnie wybierz pozycję **Usuń,** aby usunąć grupę zasobów.  

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Usuń grupę zasobów przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Ta część samouczka używa następujących poleceń cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów | 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Usuń grupę zasobów przy użyciu interfejsu wiersza polecenia AZ. 


   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

Ta część samouczka używa następujących poleceń cmdlet Az CLI:

| Polecenie | Uwagi |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

---


> [!IMPORTANT]
> Jeśli chcesz zachować grupę zasobów, ale usunąć pomocniczą bazę danych, usuń ją z grupy trybu failover przed jej usunięciem. Usunięcie pomocniczej bazy danych przed jej usunięciem z grupy trybu failover może spowodować nieprzewidywalne zachowanie. 


## <a name="full-scripts"></a>Pełne skrypty

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Nowy serwer AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tworzy regułę zapory dla serwera logicznego. | 
| [Nowa baza danych AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Tworzy nową pojedynczą bazę danych usługi Azure SQL Database. | 
| [Nowa grupa AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Tworzy nową grupę trybu failover. |
| [Baza danych Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Pobiera jedną lub więcej baz danych SQL. |
| [Grupa Add-AzSqlDatabaseToFailover](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Dodaje jedną lub więcej baz danych SQL platformy Azure do grupy trybu failover. |
| [Grupa Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera lub wyświetla listę grup trybu failover usługi SQL Database platformy Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Wykonuje przebłaję awaryjną grupy trybu failover usługi Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów | 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az zestaw kont](/cli/azure/account?view=azure-cli-latest#az-account-set) | Ustawia subskrypcję jako bieżącą aktywną subskrypcję. | 
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [az sql server firewall-rule create az sql server firewall-rule create az sql server firewall-rule create az](/cli/azure/sql/server/firewall-rule) | Tworzy reguły zapory serwera. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Tworzy bazę danych. | 
| [az sql tworzenie grupy trybu failover](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Tworzy grupę trybu failover. | 
| [az sql lista grup trybu failover](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Wyświetla listę grup trybu failover na serwerze. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Ustaw podstawową grupę trybu failover, korzystając z niepowodzenia wszystkich baz danych z bieżącego serwera podstawowego. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

# <a name="portal"></a>[Portal](#tab/azure-portal)
Nie ma żadnych skryptów dostępnych dla witryny Azure portal. 
 
---

Inne skrypty bazy danych SQL azure można znaleźć tutaj: [Azure PowerShell](sql-database-powershell-samples.md) i [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano pojedynczą bazę danych usługi Azure SQL Database do grupy trybu failover i przetestowano tryb failover. W tym samouczku omówiono: 

> [!div class="checklist"]
> - Tworzenie pojedynczej bazy danych usługi Azure SQL Database. 
> - Utwórz [grupę trybu failover](sql-database-auto-failover-group.md) dla pojedynczej bazy danych między dwoma logicznymi serwerami SQL.
> - Test pracy awaryjnej.

Przejdź do następnego samouczka na temat dodawania puli elastycznej do grupy trybu failover. 

> [!div class="nextstepaction"]
> [Samouczek: Dodawanie puli elastycznej bazy danych SQL platformy Azure do grupy trybu failover](sql-database-elastic-pool-failover-group-tutorial.md)
