---
title: 'Samouczek: Dodawanie Azure SQL Database pojedynczej bazy danych do grupy trybu failover | Microsoft Docs'
description: Dodaj Azure SQL Database pojedynczą bazę danych do grupy trybu failover przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: a80dc8ccaa72a57986ed6c64f7ab7050ab4c7de5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099015"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Samouczek: Dodawanie Azure SQL Database pojedynczej bazy danych do grupy trybu failover

Skonfiguruj grupę trybu failover dla Azure SQL Database pojedynczej bazy danych i przetestuj tryb failover przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.  Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Utwórz Azure SQL Databaseą pojedynczą bazę danych.
> - Utwórz [grupę trybu failover](sql-database-auto-failover-group.md) dla pojedynczej bazy danych między dwoma logicznymi serwerami SQL.
> - Testowanie pracy w trybie failover.

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Aby ukończyć kroki tego samouczka, upewnij się, że dysponujesz następującymi elementami: 

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) , jeśli jeszcze go nie masz.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Aby ukończyć ten samouczek, upewnij się, że masz następujące elementy:

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) , jeśli jeszcze go nie masz.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby ukończyć ten samouczek, upewnij się, że masz następujące elementy:

- Subskrypcja platformy Azure. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) , jeśli jeszcze go nie masz.
- Najnowsza wersja [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1 — Tworzenie pojedynczej bazy danych 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 — Tworzenie grupy trybu failover 
W tym kroku utworzysz [grupę trybu failover](sql-database-auto-failover-group.md) między istniejącym serwerem Azure SQL i nowym serwerem Azure SQL w innym regionie. Następnie Dodaj przykładową bazę danych do grupy trybu failover. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Utwórz grupę trybu failover i Dodaj do niej pojedynczą bazę danych przy użyciu Azure Portal. 


1. Wybierz pozycję **Azure SQL** w menu po lewej stronie [Azure Portal](https://portal.azure.com). Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz SQL Azure w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz pojedynczą bazę danych utworzoną w sekcji 2, na przykład `mySampleDatbase`. 
1. Wybierz nazwę serwera w polu **Nazwa serwera** , aby otworzyć ustawienia serwera.

   ![Otwórz serwer dla pojedynczej bazy danych](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Wybierz pozycję **grupy trybu failover** w okienku **Ustawienia** , a następnie wybierz pozycję **Dodaj grupę** , aby utworzyć nową grupę trybu failover. 

    ![Dodaj nową grupę trybu failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na stronie **Grupa trybu failover** wprowadź lub wybierz następujące wartości, a następnie wybierz pozycję **Utwórz**:
    - **Nazwa grupy trybu failover**: Wpisz unikatową nazwę grupy trybu failover, `failovergrouptutorial`na przykład. 
    - **Serwer pomocniczy**: Wybierz opcję *konfigurowania wymaganych ustawień* , a następnie wybierz opcję **utworzenia nowego serwera**. Alternatywnie można wybrać istniejący serwer jako serwer pomocniczy. Po wprowadzeniu następujących wartości wybierz pozycję **Wybierz**. 
        - **Nazwa serwera**: Wpisz unikatową nazwę serwera pomocniczego, `mysqlsecondary`na przykład. 
        - **Identyfikator logowania administratora serwera**: Wprowadź`azureuser`
        - **Hasło**: Wpisz złożone hasło spełniające wymagania dotyczące haseł.
        - **Lokalizacja**: Wybierz lokalizację z listy rozwijanej, na przykład `East US`. Ta lokalizacja nie może być taka sama jak lokalizacja serwera podstawowego.

    > [!NOTE]
    > Ustawienia logowania serwera i zapory muszą być zgodne z serwerem podstawowym. 
    
      ![Tworzenie serwera pomocniczego dla grupy trybu failover](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Bazy danych w grupie**: Po wybraniu serwera pomocniczego ta opcja zostanie odblokowana. Wybierz je, aby **wybrać bazy danych do dodania** , a następnie wybierz bazę danych utworzoną w sekcji 1. Dodanie bazy danych do grupy trybu failover spowoduje automatyczne uruchomienie procesu replikacji geograficznej. 
        
    ![Dodawanie bazy danych SQL do grupy trybu failover](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Utwórz grupę trybu failover i Dodaj do niej pojedynczą bazę danych przy użyciu programu PowerShell. 

   > [!NOTE]
   > Ustawienia logowania serwera i zapory muszą być zgodne z serwerem podstawowym. 

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

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Utwórz grupę trybu failover i Dodaj do niej pojedynczą bazę danych przy użyciu polecenia AZ CLI. 

   > [!NOTE]
   > Ustawienia logowania serwera i zapory muszą być zgodne z serwerem podstawowym. 

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   # subscriptionID=<SubscriptionID>
   # resourceGroupName=myResourceGroup-$RANDOM
   # location=SouthCentralUS
   # adminLogin=azureuser
   # password="PWD27!"+`openssl rand -base64 18`
   # serverName=mysqlserver-$RANDOM
   # databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --add-db $databaseName
      --failover-policy Automatic
   ```

---

## <a name="3---test-failover"></a>3 — Testowanie pracy w trybie failover 
W tym kroku nastąpi niepowodzenie grupy trybu failover na serwerze pomocniczym, a następnie powrót po awarii przy użyciu Azure Portal. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Testowanie pracy w trybie failover przy użyciu Azure Portal. 

1. Wybierz pozycję **Azure SQL** w menu po lewej stronie [Azure Portal](https://portal.azure.com). Jeśli na liście nie ma **usługi Azure SQL** , wybierz pozycję **wszystkie usługi**, a następnie wpisz SQL Azure w polu wyszukiwania. Obowiązkowe Wybierz gwiazdkę obok pozycji **Azure SQL** , aby ją dodać do ulubionych, i Dodaj ją jako element w nawigacji po lewej stronie. 
1. Wybierz pojedynczą bazę danych utworzoną w sekcji 2, na przykład `mySampleDatbase`. 
1. Wybierz nazwę serwera w polu **Nazwa serwera** , aby otworzyć ustawienia serwera.

   ![Otwórz serwer dla pojedynczej bazy danych](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Wybierz pozycję **grupy trybu failover** w okienku **Ustawienia** , a następnie wybierz grupę trybu failover utworzoną w sekcji 2. 
  
   ![Wybieranie grupy trybu failover z portalu](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Sprawdź, który serwer jest podstawowy i który serwer jest serwerem pomocniczym. 
1. Wybierz pozycję **tryb failover** z okienka zadań, aby przełączyć grupę trybu failover do trybu failover zawierającej przykładową pojedynczą bazę danych. 
1. Na ostrzeżeniu wybierz pozycję **tak** , aby powiadomić, że sesje TDS zostaną rozłączone. 

   ![Praca awaryjna grupy trybu failover zawierającej bazę danych SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Sprawdź, który serwer jest teraz podstawowy i który serwer jest serwerem pomocniczym. Jeśli przełączenie w tryb failover powiodło się, te dwa serwery powinny mieć zamienione role. 
1. Wybierz ponownie **tryb failover** , aby powrócić do awarii serwerów z powrotem do ich pierwotnych ról. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Testowanie pracy w trybie failover przy użyciu programu PowerShell. 


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


Przejdź do trybu failover na serwerze pomocniczym: 

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
   Write-host "Failed failover group to sucessfully to" $drServerName 
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
   Write-host "Failed failover group to successfully to back to" $serverName
   ```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Testowanie pracy w trybie failover przy użyciu polecenia AZ CLI. 


Sprawdź, który serwer jest serwerem pomocniczym:

   
   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

Przejdź do trybu failover na serwerze pomocniczym: 

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # drServerName=mysqlsecondary-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

Przywróć grupę trybu failover z powrotem do serwera podstawowego:

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
Wyczyść zasoby, usuwając grupę zasobów. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Usuń grupę zasobów przy użyciu Azure Portal. 


1. Przejdź do grupy zasobów w [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Usuń grupę zasobów** , aby usunąć wszystkie zasoby w grupie, a także samą grupę zasobów. 
1. Wpisz nazwę grupy zasobów, `myResourceGroup`w polu tekstowym, a następnie wybierz pozycję **Usuń** , aby usunąć grupę zasobów.  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Usuń grupę zasobów przy użyciu programu PowerShell. 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Usuń grupę zasobów za pomocą polecenia AZ CLI. 


   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   
   # Clean up resources by removing the resource group
   echo "Cleaning up resources by removing the resource group..."
   az group delete \
     --name $resourceGroupName
   echo "Successfully removed resource group" $resourceGroupName
   ```

---


## <a name="full-scripts"></a>Pełne skrypty

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Create SQL Database")]

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Brak skryptów dostępnych dla Azure Portal.
 
---

Inne skrypty Azure SQL Database można znaleźć tutaj: [Azure PowerShell](sql-database-powershell-samples.md) i [interfejs wiersza polecenia platformy Azure](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano Azure SQL Database pojedynczą bazę danych do grupy trybu failover i przetestowano tryb failover. W tym samouczku omówiono:

> [!div class="checklist"]
> - Utwórz Azure SQL Databaseą pojedynczą bazę danych. 
> - Utwórz [grupę trybu failover](sql-database-auto-failover-group.md) dla pojedynczej bazy danych między dwoma logicznymi serwerami SQL.
> - Testowanie pracy w trybie failover.

Przejdź do następnego samouczka dotyczącego dodawania puli elastycznej do grupy trybu failover. 

> [!div class="nextstepaction"]
> [Samouczek: Dodawanie Azure SQL Database elastycznej puli do grupy trybu failover](sql-database-elastic-pool-failover-group-tutorial.md)
