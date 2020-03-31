---
title: 'Samouczek: Dodawanie puli elastycznej do grupy trybu failover'
description: Dodaj pulę elastyczną bazy danych SQL platformy Azure do grupy trybu failover przy użyciu witryny Azure portal, powershell lub interfejsu wiersza polecenia platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: c57f9eed2147504dd7b3313d58468fb76ab40caa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268979"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Samouczek: Dodawanie puli elastycznej bazy danych SQL platformy Azure do grupy trybu failover

Konfigurowanie grupy trybu failover dla puli elastycznej bazy danych SQL platformy Azure i testowanie pracy awaryjnej przy użyciu witryny Azure portal.  Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie pojedynczej bazy danych usługi Azure SQL Database.
> - Dodaj pojedynczą bazę danych do puli elastycznej. 
> - Utwórz [grupę trybu failover](sql-database-auto-failover-group.md) dla dwóch pul elastycznych między dwoma logicznymi serwerami SQL.
> - Test pracy awaryjnej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami: 

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto,](https://azure.microsoft.com/free/) jeśli jeszcze go nie masz.


## <a name="1---create-a-single-database"></a>1 - Tworzenie pojedynczej bazy danych 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 - Dodawanie pojedynczej bazy danych do puli elastycznej
W tym kroku utworzysz pulę elastyczną i dodasz do niej pojedynczą bazę danych. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz elastyczną pulę za pomocą witryny Azure Portal. 


1. Wybierz **sql platformy Azure** w menu po lewej stronie witryny Azure portal. Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql azure w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok **programu Azure SQL,** aby ją ulubieć, i dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz **+ Dodaj,** aby otworzyć stronę **opcji Wybierz wdrożenie SQL.** Dodatkowe informacje o różnych bazach danych można wyświetlić, wybierając pozycję Pokaż szczegóły na kafelku Bazy danych.
1. Wybierz **pulę elastyczną** z listy rozwijanej **Typ zasobu** na **kafelku Bazy danych SQL.** Wybierz **pozycję Utwórz,** aby utworzyć pulę elastyczną. 

    ![Wybierz basen elastyczny](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Skonfiguruj pulę elastyczną z następującymi wartościami:
   - **Nazwa**: Podaj unikatową nazwę `myElasticPool`dla basenu elastycznego, na przykład . 
   - **Subskrypcja**: Wybierz subskrypcję z listy rozwijanej.
   - **ResourceGroup**: `myResourceGroup` Wybierz z listy rozwijanej, grupy zasobów utworzonej w sekcji 1. 
   - **Serwer**: Wybierz serwer utworzony w sekcji 1 z listy rozwijanej.  

       ![Tworzenie nowego serwera dla puli elastycznej](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Compute + storage**: Wybierz **opcję Konfiguruj pulę elastyczną,** aby skonfigurować obliczeń, magazyn i dodać pojedynczą bazę danych do puli elastycznej. Na karcie **Ustawienia puli** pozostaw domyślną wartość Gen5 z 2 vcorami i 32 gb. 

1. Na stronie **Konfigurowanie** wybierz kartę **Bazy danych,** a następnie wybierz pozycję **Dodaj bazę danych**. Wybierz bazę danych utworzoną w sekcji 1, a następnie wybierz pozycję **Zastosuj,** aby dodać ją do puli elastycznej. Wybierz pozycję **Zastosuj** ponownie, aby zastosować ustawienia puli elastycznej i zamknij stronę **Konfiguruj.** 

    ![Dodawanie bazy danych SQL do puli elastycznej](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Wybierz **pozycję Przejrzyj + utwórz,** aby przejrzeć ustawienia puli elastycznej, a następnie wybierz pozycję **Utwórz,** aby utworzyć pulę elastyczną. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Tworzenie pul elastycznych i serwera pomocniczego przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # The ip address range that you want to allow to access your server 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"
   
   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

Ta część samouczka używa następujących poleceń cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Nowy-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Tworzy pulę elastycznej bazy danych dla bazy danych SQL azure.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Ustawia właściwości bazy danych lub przenosi istniejącą bazę danych do puli elastycznej. | 

---

## <a name="3---create-the-failover-group"></a>3 - Tworzenie grupy trybu failover 
W tym kroku utworzysz [grupę trybu failover](sql-database-auto-failover-group.md) między istniejącym serwerem SQL platformy Azure a nowym serwerem SQL platformy Azure w innym regionie. Następnie dodaj pulę elastyczną do grupy trybu failover. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Utwórz grupę trybu failover przy użyciu witryny Azure portal. 

1. Wybierz **sql platformy Azure** w menu po lewej stronie [witryny Azure portal](https://portal.azure.com). Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql azure w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok **programu Azure SQL,** aby ją ulubieć, i dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz pulę elastyczną utworzoną `myElasticPool`w poprzedniej sekcji, na przykład . 
1. W okienku **Przegląd** wybierz nazwę serwera w obszarze **Nazwa serwera,** aby otworzyć ustawienia serwera.
  
    ![Otwarty serwer dla puli elastycznej](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Wybierz **pozycję Grupy trybu failover** w okienku **Ustawienia,** a następnie wybierz pozycję **Dodaj grupę,** aby utworzyć nową grupę trybu failover. 

    ![Dodawanie nowej grupy trybu failover](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. Na stronie **Grupa trybu failover** wprowadź lub wybierz następujące wartości, a następnie wybierz pozycję **Utwórz:**
    - **Nazwa grupy trybu failover:** Wpisz unikatową nazwę `failovergrouptutorial`grupy trybu failover, taką jak . 
    - **Serwer pomocniczy**: Wybierz opcję *konfigurowania wymaganych ustawień,* a następnie wybierz **pozycję Utwórz nowy serwer**. Alternatywnie można wybrać już istniejący serwer jako serwer pomocniczy. Po wprowadzeniu następujących wartości dla nowego serwera pomocniczego wybierz pozycję **Wybierz**. 
        - **Nazwa serwera**: Wpisz unikatową nazwę serwera `mysqlsecondary`pomocniczego, taką jak . 
        - **Logowanie administratora serwera**: Typ`azureuser`
        - **Hasło:** Wpisz złożone hasło spełniające wymagania dotyczące hasła.
        - **Lokalizacja**: Wybierz lokalizację z listy rozwijanej, na przykład `East US`. Ta lokalizacja nie może być tą samą lokalizacją co serwer podstawowy.

       > [!NOTE]
       > Ustawienia logowania i zapory serwera muszą być zgodne z ustawieniami serwera podstawowego. 
    
       ![Tworzenie serwera pomocniczego dla grupy trybu failover](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Wybierz **bazy danych w grupie,** a następnie wybierz pulę elastyczną utworzoną w sekcji 2. Powinno pojawić się ostrzeżenie z monitem o utworzenie puli elastycznej na serwerze pomocniczym. Wybierz ostrzeżenie, a następnie wybierz **przycisk OK,** aby utworzyć pulę elastyczną na serwerze pomocniczym. 
        
    ![Dodawanie puli elastycznej do grupy trybu failover](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Wybierz **pozycję Wybierz,** aby zastosować ustawienia puli elastycznej do grupy trybu failover, a następnie wybierz pozycję **Utwórz,** aby utworzyć grupę trybu failover. Dodanie puli elastycznej do grupy trybu failover spowoduje automatyczne rozpoczęcie procesu replikacji geograficznej.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Utwórz grupę trybu failover przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary logical server =" $drServerName
   
   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured" 
   
   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 
   
   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   $failoverGroup
   ```

Ta część samouczka używa następujących poleceń cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Nowy serwer AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tworzy regułę zapory dla serwera logicznego. | 
| [Nowy-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Tworzy pulę elastycznej bazy danych dla bazy danych SQL azure.| 
| [Nowa grupa AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Tworzy nową grupę trybu failover. |
| [Grupa Add-AzSqlDatabaseToFailover](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Dodaje jedną lub więcej baz danych SQL platformy Azure do grupy trybu failover. |
| [Grupa Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera lub wyświetla listę grup trybu failover usługi SQL Database platformy Azure. |

---


## <a name="4---test-failover"></a>4 - Test pracy awaryjnej 
W tym kroku zakończy się niepowodzeniem grupy trybu failover na serwerze pomocniczym, a następnie po awarii przy użyciu witryny Azure portal. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Testowanie pracy awaryjnej grupy trybu failover przy użyciu witryny Azure portal. 

1. Wybierz **sql platformy Azure** w menu po lewej stronie [witryny Azure portal](https://portal.azure.com). Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql azure w polu wyszukiwania. (Opcjonalnie) Wybierz gwiazdkę obok **programu Azure SQL,** aby ją ulubieć, i dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz pulę elastyczną utworzoną `myElasticPool`w poprzedniej sekcji, na przykład . 
1. Wybierz nazwę serwera w obszarze **Nazwa serwera,** aby otworzyć ustawienia serwera.

    ![Otwarty serwer dla puli elastycznej](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Wybierz **pozycję Grupy trybu failover** w okienku **Ustawienia,** a następnie wybierz grupę trybu failover utworzoną w sekcji 2. 
  
   ![Wybieranie grupy trybu failover z portalu](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Sprawdź, który serwer jest podstawowy, a który pomocniczy. 
1. Wybierz opcję **Praca awaryjna** z okienka zadań, aby przejść w stan failover grupy trybu failover zawierającej pulę elastyczną. 
1. Wybierz **pozycję Tak** w ostrzeżeniu, które powiadamia o rozłączeniu sesji TDS. 

   ![Praca awaryjna grupy trybu failover zawierającej bazę danych SQL](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Sprawdź, który serwer jest podstawowy, który serwer jest pomocniczy. Jeśli przewija się w trybie failover, dwa serwery powinny mieć zamienione role. 
1. Wybierz ponownie **opcję Praca awaryjna,** aby grupa trybu failover wróciła do oryginalnych ustawień. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Przetestuj przewijaniec awaryjny grupy trybu failover przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName 
   ```

Powiększąc grupę trybu failover na serwerze pomocniczym, a następnie wróć po awarii przy użyciu programu PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary" 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName 
   ```

Ta część samouczka używa następujących poleceń cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Grupa Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera lub wyświetla listę grup trybu failover usługi SQL Database platformy Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Wykonuje przebłaję awaryjną grupy trybu failover usługi Azure SQL Database. |


---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Oczyść zasoby, usuwając grupę zasobów. 


# <a name="portal"></a>[Portal](#tab/azure-portal)


1. Przejdź do grupy zasobów w [witrynie Azure portal](https://portal.azure.com).
1. Wybierz **pozycję Usuń grupę zasobów,** aby usunąć wszystkie zasoby w grupie, a także samą grupę zasobów. 
1. Wpisz nazwę grupy zasobów `myResourceGroup`w obszarze tekstowym, a następnie wybierz pozycję **Usuń,** aby usunąć grupę zasobów. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Wyczyść zasoby za pomocą programu PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Ta część samouczka używa następującego polecenia cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów | 

---

> [!IMPORTANT]
> Jeśli chcesz zachować grupę zasobów, ale usunąć pomocniczą bazę danych, usuń ją z grupy trybu failover przed jej usunięciem. Usunięcie pomocniczej bazy danych przed jej usunięciem z grupy trybu failover może spowodować nieprzewidywalne zachowanie. 

## <a name="full-script"></a>Pełny skrypt

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Nowy serwer AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tworzy regułę zapory dla serwera logicznego. | 
| [Nowa baza danych AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Tworzy nową pojedynczą bazę danych usługi Azure SQL Database. | 
| [Nowy-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Tworzy pulę elastycznej bazy danych dla bazy danych SQL azure.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Ustawia właściwości bazy danych lub przenosi istniejącą bazę danych do puli elastycznej. | 
| [Nowa grupa AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Tworzy nową grupę trybu failover. |
| [Baza danych Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Pobiera jedną lub więcej baz danych SQL. |
| [Grupa Add-AzSqlDatabaseToFailover](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Dodaje jedną lub więcej baz danych SQL platformy Azure do grupy trybu failover. |
| [Grupa Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Pobiera lub wyświetla listę grup trybu failover usługi SQL Database platformy Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Wykonuje przebłaję awaryjną grupy trybu failover usługi Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów | 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Nie ma żadnych skryptów dostępnych dla witryny Azure portal.

---

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano pulę elastyczną bazy danych SQL platformy Azure do grupy trybu failover i przetestowano tryb failover. W tym samouczku omówiono:

> [!div class="checklist"]
> - Tworzenie pojedynczej bazy danych usługi Azure SQL Database.
> - Dodaj pojedynczą bazę danych do puli elastycznej. 
> - Utwórz [grupę trybu failover](sql-database-auto-failover-group.md) dla dwóch pul elastycznych między dwoma logicznymi serwerami SQL.
> - Test pracy awaryjnej.

Przejdź do następnego samouczka na temat migracji przy użyciu dms.

> [!div class="nextstepaction"]
> [Samouczek: Migrowanie programu SQL Server do puli bazy danych przy użyciu usługi DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
