---
title: Tworzenie środowiska uruchomieniowego integracji usług SSIS Azure w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć środowiska uruchomieniowego integracji usług SSIS Azure, dzięki czemu można uruchomić pakiet SSIS w chmurze Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: ae906712b6169f2d352013bf2b786c5cc772aa9a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34618559"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Tworzenie środowiska uruchomieniowego integracji usług SSIS Azure w fabryce danych Azure
Ten artykuł zawiera kroki do inicjowania obsługi środowiska uruchomieniowego integracji usług SSIS Azure w fabryce danych Azure. Następnie możesz użyć programu SQL Server Data Tools (SSDT) lub SQL Server Management Studio (SSMS) do wdrożenia pakietów usług SQL Server Integration Services (SSIS) w tym środowisku uruchomieniowym na platformie Azure.

Samouczek: [samouczek: Wdrażanie pakietów usług SQL Server Integration Services (SSIS) na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md) pokazano, jak utworzyć środowiska uruchomieniowego integracji usług SSIS Azure (IR) przy użyciu bazy danych SQL Azure do przechowywania dla katalogu usług SSIS. W tym artykule rozszerzenie samouczka i pokazuje, jak wykonać następujące czynności: 

- Użyj wystąpienia zarządzane Azure SQL (wersja zapoznawcza) do hostowania usług SSIS katalogu (baza danych usług SSIS).
- Dołącz IR Azure SSIS do sieci wirtualnej platformy Azure (VNet). Aby uzyskać informacje koncepcyjne na dołączenie IR Azure SSIS do sieci wirtualnej i konfigurowanie sieci wirtualnej w portalu Azure, zobacz [Join IR Azure SSIS do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md). 

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz dostępnej ogólnie wersji 1 usługi Data Factory, zobacz [dokumentację dotyczącą usługi Data Factory w wersji 1](v1/data-factory-introduction.md).


## <a name="overview"></a>Przegląd
W tym artykule przedstawiono różne sposoby udostępniania IR Azure SSIS:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Szablon usługi Azure Resource Manager](#azure-resource-manager-template)

Po utworzeniu IR Azure SSIS fabryki danych łączy się z bazy danych SQL Azure do przygotowania bazy danych usług SSIS katalogu (SSISDB). Skrypt konfiguruje również uprawnienia i ustawienia Twojej sieci wirtualnej, jeśli zostanie określona, i dołącza nowe wystąpienie środowiska Azure SSIS Integration Runtime do sieci wirtualnej.

Podczas aprowizowania wystąpienia środowiska Azure-SSIS IR są instalowane również pakiety Azure Feature Pack for SSIS i Access Redistributable. Te składniki zapewniają łączność z plikami programów Excel i Access oraz z różnymi źródłami danych platformy Azure (oprócz źródeł danych obsługiwanych przez wbudowane składniki). Możesz też zainstalować dodatkowe składniki. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji, możesz utworzyć konto [bezpłatnej wersji próbnej](http://azure.microsoft.com/pricing/free-trial/).
- **Serwer bazy danych SQL Azure** lub **zarządzane wystąpienie programu SQL Server (wersja zapoznawcza)**. Jeśli nie masz jeszcze serwera bazy danych, utwórz go w witrynie Azure Portal, zanim zaczniesz. Ten serwer hostuje bazę danych katalogu usług SSIS (SSISDB). Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja pozwala środowisku Integration Runtime zapisywać dzienniki wykonywania SSISDB bez wykraczania poza granice regionów świadczenia usług platformy Azure. Zapamiętaj warstwy cenowej serwera Azure SQL. Aby uzyskać listę obsługiwanych warstw cenowych bazy danych SQL Azure, zobacz [limity zasobów bazy danych SQL](../sql-database/sql-database-resource-limits.md).

    Upewnij się, że serwer bazy danych SQL Azure lub zarządzane wystąpienie programu SQL Server (wersja zapoznawcza) nie ma katalogu SSIS (SSIDB bazy danych). Aprowizacja środowiska IR Azure-SSIS nie obsługuje istniejącego katalogu usług SSIS.
- **Klasycznym lub usługi Azure Resource Manager wirtualnego Network(VNet) (opcjonalnie)**. Jeśli co najmniej jeden z poniższych warunków jest spełniony, musisz mieć sieć Azure Virtual Network:
    - Są obsługującym bazę danych usług SSIS katalogu na wystąpieniu serwera SQL Server zarządzanych (wersja zapoznawcza), który jest częścią sieci wirtualnej.
    - Chcesz połączyć się z lokalnymi magazynami danych z pakietów usług SSIS działającymi w środowisku Azure SSIS Integration Runtime.
- Zainstalowanie programu **Azure PowerShell**. Wykonaj instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps). Program PowerShell służy do uruchamiania skryptu w celu aprowizacji środowiska Azure SSIS Integration Runtime, które uruchamia pakiety SSIS w chmurze. 

> [!NOTE]
> - Fabrykę danych w wersji 2 możesz utworzyć w następujących regionach: Wschodnie stany USA, Wschodnie stany USA 2, Azja Południowo-Wschodnia i Europa Zachodnia. 
> - Środowisko Azure-SSIS IR możesz utworzyć w następujących regionach: Wschodnie stany USA, Wschodnie stany USA 2, Środkowe stany USA, Zachodnie stany USA 2, Europa Północna, Europa Zachodnia, Południowe Zjednoczone Królestwo i Australia Wschodnia.

## <a name="azure-portal"></a>Azure Portal
W tej sekcji Użyj portalu Azure, w szczególności danych fabryki interfejsu użytkownika, można utworzyć IR. Azure SSIS 

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
2. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).    
3. Kliknij przycisk **Nowy** w lewym menu, kliknij pozycję **Dane + analiza**, a następnie kliknij pozycję **Data Factory**. 
   
   ![Nowy-> Fabryka danych](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Na stronie **Nowa fabryka danych** wprowadź jako **nazwę** wartość **MyAzureSsisDataFactory**. 
      
     ![Strona Nowa fabryka danych](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Nazwa fabryki danych platformy Azure musi być **globalnie unikatowa**. Jeśli wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaMyAzureSsisDataFactory) i spróbuj utworzyć ją ponownie. Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
       `Data factory name �MyAzureSsisDataFactory� is not available`

3. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych. 
4. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:
     
      - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
      - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
      Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
4. Wybierz wartość **V2 (wersja zapoznawcza)** dla **wersji**.
5. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście są wyświetlane tylko lokalizacje obsługiwane na potrzeby tworzenia fabryk danych.
6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
7. Kliknij przycisk **Utwórz**.
8. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem: **Wdrażanie fabryki danych**. 

    ![kafelek Wdrażanie fabryki danych](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
   ![Strona główna fabryki danych](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Kliknij pozycję **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Data Factory. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Aprowizowanie środowiska Azure SSIS Integration Runtime

1. Na stronie wprowadzenia kliknij kafelek **Konfigurowanie środowiska SSIS Integration Runtime**. 

   ![Kafelek Konfigurowanie środowiska SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Na stronie **Ustawienia ogólne** kreatora **konfiguracji środowiska Integration Runtime** wykonaj następujące czynności: 

   ![Ustawienia ogólne](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Określ **nazwę** środowiska Integration Runtime.
    2. Wybierz **lokalizację** środowiska Integration Runtime. Wyświetlane są tylko obsługiwane lokalizacje.
    3. Wybierz **rozmiar węzła**, który ma zostać skonfigurowany ze środowiskiem uruchomieniowym usług SSIS.
    4. Określ **liczbę węzłów** w klastrze.
    5. Kliknij przycisk **Dalej**. 
1. W obszarze **Ustawienia SQL** wykonaj następujące czynności: 

    ![Ustawienia SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Określ **subskrypcję** platformy Azure, która ma serwer usługi SQL Azure. 
    2. Wybierz serwer usługi SQL Azure dla **punktu końcowego serwera bazy danych katalogu**.
    3. Wprowadź nazwę użytkownika **administratora**.
    4. Wprowadź **hasło** dla administratora.  
    5. Wybierz **warstwę usług** dla bazy danych SSISDB. Wartość domyślna to Podstawowa.
    6. Kliknij przycisk **Dalej**. 
1.  Na stronie **Ustawienia zaawansowane** wybierz wartość dla ustawienia **Maximum Parallel Executions Per Node** (Maksymalna liczba równoległych wykonań na węzeł).   

    ![Ustawienia zaawansowane](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Ten krok jest **opcjonalny**. Jeśli sieć wirtualna (klasyczna lub usługi Azure Resource Manager), który chcesz dołączyć, wybierz środowiska uruchomieniowego integracji **wybierz sieć wirtualną dla Twojego środowiska uruchomieniowego integracji usług SSIS Azure do przyłączania a Zezwalaj usługom platformy Azure skonfigurować uprawnienia/ustawienia sieci wirtualnej**opcji, a następnie wykonaj następujące czynności: 

    ![Ustawienia zaawansowane za pomocą sieci wirtualnej](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. Aby uzyskać **subskrypcji**, określ **subskrypcji** mający sieci wirtualnej. 
    2. Dla typu określ **typu** sieci wirtualnej (klasyczne sieci wirtualnej lub sieci wirtualnej Azure Resource Manager). 
    3. Dla **nazwa sieci wirtualnej**, wybierz nazwę Twojej **sieci wirtualnej**. 
    4. Aby uzyskać **nazwy podsieci**, wybierz nazwę **podsieci** w sieci wirtualnej.
1. Kliknij pozycję **Zakończ**, aby rozpocząć tworzenie środowiska Azure-SSIS Integration Runtime. 

    > [!IMPORTANT]
    > - Ukończenie tego procesu zajmuje około 20 minut
    > - Usługa Data Factory łączy się z usługą Azure SQL Database w celu przygotowania bazy danych wykazu usług SSIS (SSISDB). Skrypt konfiguruje również uprawnienia i ustawienia Twojej sieci wirtualnej, jeśli zostanie określona, i dołącza nowe wystąpienie środowiska Azure SSIS Integration Runtime do sieci wirtualnej.
7. W oknie **Połączenia** w razie potrzeby przełącz do pozycji **Produkty Integration Runtime**. Kliknij przycisk **Odśwież**, aby odświeżyć status. 

    ![Stan tworzenia](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Użyj łączy w obszarze **akcje** kolumny stop/start, edytować lub usunąć środowiska uruchomieniowego integracji. Użyj ostatniego linku, aby wyświetlić kod JSON dla środowiska Integration Runtime. Przyciski edytowania i usuwania są włączone tylko wtedy, gdy środowisko IR jest zatrzymane. 

    ![Środowisko IR Azure SSIS — akcje](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Środowiska Azure SSIS Integration Runtime w portalu

1. W interfejsie użytkownika usługi Azure Data Factory przejdź na kartę **Edycja**, kliknij pozycję **Połączenia**, a następnie przejdź na kartę **Środowiska Integration Runtime**, aby wyświetlić istniejące środowiska Integration Runtime w fabryce danych. 
    ![Wyświetlanie istniejących środowisk IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Kliknij pozycję **Nowe**, aby utworzyć środowisko IR Azure-SSIS. 

    ![Środowisko Integration Runtime za pomocą menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Aby utworzyć środowisko Azure-SSIS Integration Runtime, kliknij przycisk **Nowe**, jak pokazano na ilustracji. 
3. W oknie Integration Runtime Setup (Konfiguracja środowiska Integration Runtime) wybierz pozycję **Lift-and-shift existing SSIS packages to execute in Azure** (Migruj metodą „lift-and-shift” istniejące pakiety usług SSIS do wykonywania na platformie Azure), a następnie kliknij przycisk **Dalej**.

    ![Określanie typu środowiska Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Zobacz sekcję [Aprowizowanie środowiska Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime), zawierającą pozostałe kroki konfigurowania środowiska IR Azure-SSIS.

## <a name="azure-powershell"></a>Azure PowerShell
W tej sekcji można wykorzystać program Azure PowerShell do utworzenia IR. Azure SSIS

### <a name="create-variables"></a>Tworzenie zmiennych
Zdefiniuj zmienne do wykorzystania w skrypcie w tym samouczku:

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Enterprise Edition supports advanced/premium features

# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL: SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (Preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (Preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet). 
$VnetId = "[your VNet resource ID or leave it empty]" 
$SubnetName = "[your subnet name or leave it empty]" 

```
### <a name="log-in-and-select-subscription"></a>Logowanie i wybieranie subskrypcji
Dodaj następujący kod, skrypt logowania i wybierz subskrypcję platformy Azure: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Weryfikowanie połączenia z bazą danych
Dodaj następujący skrypt w celu weryfikacji Twojej server.database.windows.net serwera bazy danych SQL Azure lub punkt końcowy serwera wystąpienia zarządzane Azure SQL (wersja zapoznawcza). 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

### <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej
Dodaj następujący skrypt, aby automatycznie skonfigurować uprawnienia/ustawienia sieci wirtualnej do przyłączenia do Twojego środowiska Azure SSIS Integration Runtime.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westeurope`.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Uruchom następujące polecenie, aby utworzyć fabrykę danych:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Tworzenie środowiska Integration Runtime
Uruchom następujące polecenie, aby utworzyć środowisko uruchomieniowe integracji usług SSIS Azure, które pakiety usług SSIS działa na platformie Azure: Użyj skryptu z sekcji, na podstawie typu bazy danych (vs bazy danych SQL Azure. Azure wystąpienia zarządzane SQL (wersja zapoznawcza)) jest używany. 

#### <a name="azure-sql-database-to-host-the-ssisdb-database-ssis-catalog"></a>Azure SQL Database do hostowania bazy danych usług SSIS (SSIS katalogu) 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Edition $AzureSSISEdition ` 
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -SetupScriptContainerSasUri $SetupScriptContainerSasUri
```

Nie trzeba przekazać wartości VNetId i podsieć, jeśli nie potrzebujesz dostępu do danych lokalnych, oznacza to, że masz lokalnych źródeł/miejsca docelowe danych w pakietów SSIS. Należy podać wartość parametru CatalogPricingTier. Aby uzyskać listę obsługiwanych warstw cenowych bazy danych SQL Azure, zobacz [limity zasobów bazy danych SQL](../sql-database/sql-database-resource-limits.md).

#### <a name="azure-sql-managed-instance-preview-to-host-the-ssisdb-database"></a>Azure zarządzane wystąpienia SQL (wersja zapoznawcza), aby hostować bazę danych usług SSIS

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -Description $AzureSSISDescription `
                                            -Edition $AzureSSISEdition ` 
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

Należy przekazać wartości parametrów VnetId i podsieci z Azure zarządzane wystąpienia SQL (wersja zapoznawcza), które łączy sieć wirtualną. Parametr CatalogPricingTier nie ma zastosowania dla wystąpienia zarządzane Azure SQL (wersja zapoznawcza). 

### <a name="start-integration-runtime"></a>Uruchamianie środowiska Integration Runtime
Uruchom następujące polecenie, aby uruchomić środowisko Azure-SSIS Integration Runtime: 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Wykonanie tego polecenia trwa od **20 do 30 minut**. 


### <a name="full-script"></a>Pełny skrypt
W tym miejscu jest pełna skrypt, który tworzy IR Azure SSIS i dołącza go do sieci wirtualnej. Ten skrypt odnosi się Azure zarządzane wystąpienia SQL (wersja zapoznawcza) są używane do obsługi katalogu usług SSIS. 

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Enterprise Edition supports advanced/premium features

$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL: SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Edition $AzureSSISEdition ` 
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -SetupScriptContainerSasUri $SetupScriptContainerSasUri

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
W tej sekcji Szablon usługi Azure Resource Manager służy do tworzenia środowiska uruchomieniowego integracji usług SSIS Azure. Poniżej przedstawiono wskazówki próbki: 

1. Utwórz plik JSON zawierający następujący szablon Menedżera zasobów. Zastąp wartości w nawiasach (symbole zastępcze) własne wartości. 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for the Azure SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D1_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 1
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL server>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL user",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
2. Aby wdrożyć szablon usługi Resource Manager, uruchom polecenie New-AzureRmResourceGroupDeployment, jak pokazano w następujących exmaple. W tym przykładzie ADFTutorialResourceGroup jest nazwą grupy zasobów. ADFTutorialARM.json jest plikiem, który zawiera definicję JSON dla fabryki danych i podczerwieni Azure SSIS. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    To polecenie tworzy fabryki danych i tworzy IR Azure SSIS, ale nie uruchamia IR. 
3. Aby uruchomić IR Azure SSIS, uruchom polecenie Start-AzureRmDataFactoryV2IntegrationRuntime: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name> `
                                             -DataFactoryName <Data Factory Name> `
                                             -Name <Azure SSIS IR Name> `
                                             -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS
Teraz użyj programu SQL Server Data Tools (SSDT) lub SQL Server Management Studio (SSMS), aby wdrożyć pakiety usług SSIS na platformie Azure. Połącz się ze swoim serwerem Azure SQL, który hostuje katalog usług SSIS (SSISDB). Nazwa serwera Azure SQL jest w formacie: &lt;nazwa_serwera&gt;.database.windows.net (dla usługi Azure SQL Database). Zobacz artykuł [Deploy packages (Wdrażanie pakietów)](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server), aby uzyskać instrukcje. 

## <a name="next-steps"></a>Kolejne kroki
Inne IR Azure SSIS w tematach w tej dokumentacji:

- [Środowisko uruchomieniowe integracji usług SSIS Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera informacje o pojęciach dotyczących środowisk uruchomieniowych integracji ogólnie tym podczerwieni Azure SSIS. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera szczegółowe instrukcje dotyczące tworzenia środowiska Azure-SSIS IR i używania bazy danych Azure SQL do hostowania wykazu usług SSIS. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i opisów stanów w pobranych informacjach. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania środowiska Azure-SSIS IR. Zawiera on również instrukcje skalowania środowiska Azure-SSIS IR do wewnątrz za pomocą dodawania do niego węzłów. 
- [Join an Azure-SSIS IR to a VNet (Dołączanie środowiska Azure-SSIS IR do sieci wirtualnej)](join-azure-ssis-integration-runtime-virtual-network.md). Ten artykuł zawiera podstawowe informacje na temat dołączania środowiska Azure-SSIS IR do sieci wirtualnej platformy Azure. Opisano w nim kroki konfigurowania sieci wirtualnej za pomocą witryny Azure Portal tak, aby umożliwić dołączanie środowiska Azure-SSIS IR do sieci wirtualnej. 
