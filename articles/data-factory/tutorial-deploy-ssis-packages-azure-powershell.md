---
title: Aprowizowanie środowiska Azure-SSIS Integration Runtime za pomocą programu PowerShell | Microsoft Docs
description: Dowiedz się, jak aprowizować środowisko Azure-SSIS Integration Runtime w usłudze Azure Data Factory za pomocą programu PowerShell w celu wdrażania i uruchamiania pakietów SSIS na platformie Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bbbbc45bd050b8f68499febeed6285ad1aa883c4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484776"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Aprowizowanie środowiska Azure-SSIS Integration Runtime w usłudze Azure Data Factory za pomocą programu PowerShell

Ten samouczek zawiera instrukcje aprowizacji usługi Azure SQL Server Integration Services (SSIS) Integration Runtime (IR) w usłudze Azure Data Factory (ADF). Azure-SSIS IR obsługuje uruchamianie pakietów wdrażana w katalogu usług SSIS (SSISDB) hostowanych przez usługi Azure SQL Database server/zarządzanego wystąpienia (Model wdrażania projektu), a te wdrożone do systemów/plik udziałów/usługi Azure Files (Model wdrażania pakietu). Po aprowizacji środowiska Azure-SSIS IR możesz następnie użyć znanych narzędzi, takich jak SQL Server Data Tools (SSDT) / SQL Server Management Studio (SSMS), a polecenia narzędzia wiersza, takich jak `dtinstall` / `dtutil` / `dtexec`, wdrażanie i uruchamianie pakietów na platformie Azure. W tym samouczku wykonasz następujące czynności:

> [!NOTE]
> W tym artykule jest używany program Azure PowerShell do obsługi administracyjnej Azure-SSIS IR. Aby użyć aplikacji portal/ADF platformy Azure do aprowizacji środowiska Azure-SSIS IR, zobacz [samouczka: Aprowizacja środowiska Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie środowiska Azure SSIS Integration Runtime
> * Uruchamianie środowiska Azure SSIS Integration Runtime
> * Przeglądanie kompletnego skryptu
> * Wdrażanie pakietów usług SSIS

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto. Aby uzyskać koncepcyjne informacje dotyczące środowiska Azure SSIS IR, zobacz [Omówienie środowiska Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
- **Serwer bazy danych SQL Azure (opcjonalnie)** . Jeśli nie masz już serwer bazy danych, utworzyć w witrynie Azure portal przed rozpoczęciem pracy. ADF z kolei spowoduje utworzenie bazy danych SSISDB na tym serwerze bazy danych. Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja pozwala środowiska integration runtime zapisywać dzienniki wykonywania do bazy danych SSISDB bez wykraczania poza granice regionów świadczenia usługi Azure. 
    - W zależności od wybranego serwera bazy danych baza danych SSISDB może zostać utworzona w Twoim imieniu jako pojedyncza baza danych, jako część elastycznej puli lub w ramach wystąpienia zarządzanego i może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybranie typu serwera bazy danych do hostowania bazy SSISDB, zobacz [porównanie usługi Azure SQL Database pojedynczej bazy danych/elastycznej puli/zarządzanego wystąpienia](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Jeśli używasz serwera Azure SQL Database przy użyciu sieci wirtualnej usługi punktów końcowych/zarządzanego wystąpienia w sieci wirtualnej do hostowania bazy SSISDB, lub wymagać dostępu do danych lokalnych, musisz dołączyć środowiska Azure-SSIS IR do sieci wirtualnej, zobacz [tworzenie środowiska Azure-SSIS IR w sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone dla serwera bazy danych. To nie jest stosowane podczas korzystania z serwera Azure SQL Database z sieci wirtualnej usługi punktów końcowych/zarządzanego wystąpienia w sieci wirtualnej do hostowania bazy SSISDB. Aby uzyskać więcej informacji, zobacz artykuł [Secure your Azure SQL database (Zabezpieczenia bazy danych Azure SQL Database)](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Aby włączyć to ustawienie przy użyciu programu PowerShell, zobacz [New AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Dodaj adres IP maszyny klienta lub zakres adresów IP, które zawiera adres IP maszyny klienta, listy adresów IP klienta w ustawieniach zapory serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - Można nawiązać połączenia z serwerem bazy danych przy użyciu uwierzytelniania SQL przy użyciu poświadczeń administratora serwera lub uwierzytelniania usługi Azure Active Directory (AAD) za pomocą tożsamości zarządzanej dla usługi ADF.  W przypadku drugiego rozwiązania musisz dodać tożsamość zarządzaną usługi Azure Data Factory do grupy usługi AAD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji na ten temat, zobacz [Tworzenie środowiska Azure-SSIS IR przy użyciu uwierzytelniania usługi AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Upewnij się, że serwer bazy danych nie ma już SSISDB. Aprowizacja środowiska IR Azure-SSIS nie obsługuje przy użyciu istniejącej bazy danych SSISDB.
- Zainstalowanie programu **Azure PowerShell**. Postępuj zgodnie z instrukcjami w [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/install-Az-ps), aby uruchomić skrypt programu PowerShell, aby aprowizować usługi Azure-SSIS IR.

> [!NOTE]
> - Aby uzyskać listę regionów świadczenia usługi Azure, w których są obecnie dostępne ADF i Azure-SSIS IR, zobacz [ADF + SSIS IR Dostępność wg regionu](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="launch-windows-powershell-ise"></a>Uruchamianie programu Windows PowerShell ISE

Uruchom program **Windows PowerShell ISE** przy użyciu uprawnień administracyjnych. 

## <a name="create-variables"></a>Tworzenie zmiennych

Skopiuj i wklej poniższy skrypt - określ wartości zmiennych. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

## <a name="sign-in-and-select-subscription"></a>Zaloguj się i wybierz subskrypcję

Dodaj następujący kod skryptu, które mogą się zalogować, a następnie wybierz swoją subskrypcję platformy Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-database-server"></a>Sprawdzanie poprawności połączenia z serwerem bazy danych

Dodaj następujący skrypt w celu zweryfikowania serwera usługi Azure SQL Database. 

```powershell
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

Aby utworzyć bazy danych SQL Azure jako część skryptu, zobacz poniższy przykład: 

Ustaw wartości dla zmiennych, które nie zostały jeszcze zdefiniowane. Na przykład: SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Tworzenie [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) przy użyciu [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) polecenia. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Jeśli dana grupa zasobów już istnieje, nie Kopiuj tego kodu do skryptu. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Uruchom następujące polecenie, aby utworzyć fabrykę danych:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Tworzenie środowiska Integration Runtime

Uruchom następujące polecenia, aby utworzyć środowiska Azure-SSIS integration runtime, które uruchamia pakiety usług SSIS na platformie Azure.

Jeśli nie zostanie użyta baza SSISDB, można pominąć parametrów CatalogServerEndpoint, CatalogPricingTier i CatalogAdminCredential.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

## <a name="start-integration-runtime"></a>Uruchamianie środowiska Integration Runtime

Uruchom następujące polecenia, aby uruchomić środowisko Azure-SSIS integration runtime.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

> [!NOTE]
> Z wyjątkiem ilekroć instalacja niestandardowa ten proces należy wykonać w ciągu 5 minut.
>
> Jeśli używasz bazy danych SSISDB, usługi ADF połączy się serwer bazy danych w celu przygotowania bazy danych SSISDB. 
> 
> Podczas aprowizowania środowiska IR Azure-SSIS instalowane są również dostęp do dystrybucji i pakiety Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel i Access i różnych źródeł danych na platformie Azure, oprócz źródeł danych, które już obsługiwane przez wbudowane składniki. Można także zainstalować dodatkowe składniki, zobacz [niestandardowa konfiguracja środowiska Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="full-script"></a>Pełny skrypt

Skrypt programu PowerShell w tej sekcji służy do konfigurowania wystąpienie środowiska IR Azure-SSIS, które uruchamia pakiety usług SSIS. Po uruchomieniu tego skryptu pomyślnie można wdrażać i uruchamiać pakiety usług SSIS na platformie Azure.

1. Uruchom środowisko Windows PowerShell Integrated Scripting Environment (ISE).
2. W środowisku ISE uruchom następujące polecenie w wierszu polecenia.  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Skopiuj skrypt programu PowerShell w tej sekcji i wklej go do środowiska ISE.
4. Podaj odpowiednie wartości dla wszystkich parametrów na początku skryptu.
5. Uruchom skrypt. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you want to use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-azure-ssis-ir"></a>Monitorowanie środowiska Azure-SSIS IR

W poniższych artykułach można znaleźć szczegółowe informacje o monitorowaniu środowiska Azure-SSIS IR i zarządzania nim. 

- [Monitorowanie środowiska Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Zarządzanie środowiska Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS

Jeśli używasz bazy danych SSISDB, można wdrażać pakiety do niego i ich uruchamianie w usłudze Azure-SSIS IR, za pomocą narzędzi SSDT/SSMS, łączących się z serwerem bazy danych za pośrednictwem punktu końcowego serwera. W przypadku usługi Azure SQL Database server/wystąpienia zarządzanego z publicznym punktem końcowym, format punktu końcowego serwera jest `<server name>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`, odpowiednio. Jeśli nie zostanie użyta baza SSISDB, można wdrożyć pakietów do pliku systemów/plik udziałów/Azure plików i uruchamiaj je na temat używania środowiska Azure-SSIS IR `dtinstall` / `dtutil` / `dtexec` narzędzia wiersza polecenia. Aby uzyskać więcej informacji, zobacz [pakietów SSIS wdrażanie](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). W obu przypadkach można również uruchomić wdrożonych pakietów na środowisko IR Azure-SSIS przy użyciu działania wykonywania pakietów SSIS w potokach ADF, zobacz [wykonywania jako najwyższej jakości działanie usługi ADF pakietów SSIS wywołania](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Zobacz też następujące artykuły wchodzące w skład dokumentacji usług SSIS: 

- [Wdrażanie, uruchamianie i monitorowanie pakietów usług SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Nawiązać połączenie z bazy danych SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Planowanie wykonywania pakietów na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie środowiska Azure SSIS Integration Runtime
> * Uruchamianie środowiska Azure SSIS Integration Runtime
> * Przeglądanie kompletnego skryptu
> * Wdrażanie pakietów usług SSIS

Aby dowiedzieć się więcej o dostosowywaniu środowiska Azure-SSIS Integration Runtime, przejdź do następującego artykułu:

> [!div class="nextstepaction"]
>[Customize Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) (Dostosowywanie środowiska Azure-SSIS IR)