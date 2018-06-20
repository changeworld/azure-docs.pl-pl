---
title: Tworzenie środowiska uruchomieniowego integracji usług SSIS Azure w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć środowiska uruchomieniowego integracji usług SSIS Azure w fabryce danych Azure, aby można było wdrożyć i uruchamiania pakietów SSIS na platformie Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f75d08074fc6020541226318d6422da373136a2d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268150"
---
# <a name="create-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Tworzenie środowiska uruchomieniowego integracji usług SSIS Azure w fabryce danych Azure
Ten artykuł zawiera kroki do inicjowania obsługi środowiska uruchomieniowego integracji usług SSIS Azure w fabryce danych Azure. Następnie można użyć programu SQL Server Data Tools (SSDT) lub SQL Server Management Studio (SSMS) do wdrożenia i uruchomienia programu SQL Server Integration Services (SSIS) pakietów w tym środowisku uruchomieniowym na platformie Azure.

Samouczek [samouczek: Wdrażanie pakietów usług SQL Server Integration Services (SSIS) na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md) pokazuje, jak utworzyć środowiska uruchomieniowego integracji usług SSIS Azure (IR) przy użyciu usługi Azure SQL Database do hostowania katalogu usług SSIS. W tym artykule rozszerzenie samouczka i pokazuje, jak wykonać następujące czynności: 

- Opcjonalnie używać bazy danych SQL Azure z punktów końcowych usługi sieci wirtualnej/zarządzane wystąpienia (wersja zapoznawcza) jako serwera bazy danych katalogu usług SSIS (baza danych usług SSIS). Warunkiem wstępnym, konieczne będzie Twoje IR Azure SSIS należy dołączyć do sieci wirtualnej i skonfigurowanie ustawień i uprawnień sieci wirtualnej jako konieczne, zobacz [sprzężenia IR Azure SSIS do sieci wirtualnej](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  
- Opcjonalnie użyj uwierzytelniania usługi Azure Active Directory (AAD) z programu Azure danych fabryki zarządzane usługi tożsamości (MSI) dla usług SSIS Azure IR do łączenia się z serwerem bazy danych. Jako warunek wstępny, należy dodać do grupy usługi AAD z uprawnieniami dostępu do serwera bazy danych z pliku MSI fabryki danych, zobacz [AAD Włącz uwierzytelnianie dla IR Azure SSIS](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz dostępnej ogólnie wersji 1 usługi Data Factory, zobacz [dokumentację dotyczącą usługi Data Factory w wersji 1](v1/data-factory-introduction.md).


## <a name="overview"></a>Przegląd
W tym artykule przedstawiono różne sposoby udostępniania IR Azure SSIS:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Szablon usługi Azure Resource Manager](#azure-resource-manager-template)

Po utworzeniu IR Azure SSIS fabryki danych łączy się z bazy danych SQL Azure do przygotowania bazy danych usług SSIS katalogu (SSISDB). Skrypt również umożliwia skonfigurowanie uprawnień i ustawień dla sieci wirtualnej, jeśli określona i tworzy sprzężenie nowe wystąpienie klasy środowiska uruchomieniowego integracji usług SSIS Azure do sieci wirtualnej.

Podczas aprowizowania wystąpienia środowiska Azure-SSIS IR są instalowane również pakiety Azure Feature Pack for SSIS i Access Redistributable. Te składniki zapewniają łączność z plikami programów Excel i Access oraz z różnymi źródłami danych platformy Azure (oprócz źródeł danych obsługiwanych przez wbudowane składniki). Możesz też zainstalować dodatkowe składniki. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji, możesz utworzyć konto [bezpłatnej wersji próbnej](http://azure.microsoft.com/pricing/free-trial/).
- **Azure bazy danych SQL server/zarządzane wystąpienia (wersja zapoznawcza)**. Jeśli nie masz jeszcze serwera bazy danych, utwórz go w witrynie Azure Portal, zanim zaczniesz. Ten serwer hostuje bazę danych katalogu usług SSIS (SSISDB). Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja pozwala środowisku Integration Runtime zapisywać dzienniki wykonywania SSISDB bez wykraczania poza granice regionów świadczenia usług platformy Azure. Na podstawie wybranej bazy danych serwera, bazy danych SSISDB może zostać utworzony w Twoim imieniu jako autonomiczna baza danych, część puli elastycznej, lub w przypadku zarządzanych (wersja zapoznawcza) i jest dostępny w sieci publicznej lub przez przyłączenie sieci wirtualnej. Aby uzyskać listę obsługiwanych warstw cenowych bazy danych SQL Azure, zobacz [limity zasobów bazy danych SQL](../sql-database/sql-database-resource-limits.md).

    Upewnij się, czy Twojej bazy danych SQL Azure serwera/zarządzane wystąpienia (wersja zapoznawcza) nie ma katalogu SSIS (SSIDB bazy danych). Aprowizacja środowiska IR Azure-SSIS nie obsługuje istniejącego katalogu usług SSIS.
- **Sieć wirtualna Classic/usługi Azure Resource Manager (opcjonalnie)**. Jeśli co najmniej jeden z następujących warunków jest spełniony, musi mieć sieci wirtualnej platformy Azure:
    - Obsługiwana baza danych usług SSIS katalogu w bazie danych SQL Azure z punktów końcowych usługi sieci wirtualnej/zarządzane wystąpienia (wersja zapoznawcza), który znajduje się w sieci wirtualnej.
    - Chcesz połączyć się z lokalnymi magazynami danych z pakietów usług SSIS działającymi w środowisku Azure SSIS Integration Runtime.
- Zainstalowanie programu **Azure PowerShell**. Postępuj zgodnie z instrukcjami [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-azurerm-ps), jeśli używasz programu PowerShell do uruchamiania skryptu do obsługi integracji świadczenia usług SSIS Azure uruchamiania pakietów SSIS w chmurze. 

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
  
       `Data factory name “MyAzureSsisDataFactory” is not available`

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

    a. Aby uzyskać **nazwa**, wprowadź nazwę użytkownika integracji środowiska uruchomieniowego.
    
    b. Aby uzyskać **opis**, wprowadź opis Twojego środowiska uruchomieniowego integracji.
    
    c. Aby uzyskać **lokalizacji**, wybierz lokalizację użytkownika środowiska uruchomieniowego integracji. Wyświetlane są tylko obsługiwane lokalizacje. Zaleca się, że wybrano tej samej lokalizacji serwera bazy danych do hosta usług SSIS.
    
    d. Aby uzyskać **rozmiaru węzła**, wybierz rozmiar węzła w klastrze środowiska uruchomieniowego integracji. Wyświetlane są tylko węzeł obsługiwanych rozmiarów. Wybierz rozmiar dużych węzła (skalowania w górę), jeśli chcesz uruchamiać wiele obliczeń/pamięci — znacznym pakietów.
    
    e. Aby uzyskać **numer węzła**, wybierz liczbę węzłów w klastrze środowiska uruchomieniowego integracji. Tylko w węźle obsługiwanym liczby są wyświetlane. Wybierz dużych klastra z wielu węzłów (skalowania w poziomie), jeśli chcesz uruchamiać wiele pakietów równolegle.
     
    f. Dla **Edition/licencji**, wybierz program SQL Server edition/licencji dla Twojego środowiska uruchomieniowego integracji: Standard lub Enterprise. Wybierz przedsiębiorstwa, jeśli chcesz używać funkcji zaawansowanego premium na Twojego środowiska uruchomieniowego integracji. 
    
    g. Aby uzyskać **zaoszczędzić**, wybierz opcję korzyści hybrydowe platformy Azure (AHB) dla Twojego środowiska uruchomieniowego integracji: Yes lub No. Wybierz opcję Tak, aby użycie własnej licencji programu SQL Server z Software Assurance skorzystania z oszczędności kosztów przy użyciu hybrydowego.
    
    h. Kliknij przycisk **Dalej**. 
3. Na stronie **Ustawienia SQL** wykonaj następujące czynności: 

    ![Ustawienia SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. Aby uzyskać **subskrypcji**, wybierz subskrypcję platformy Azure, która ma serwer bazy danych do hosta usług SSIS. 
    
    b. Aby uzyskać **lokalizacji**, wybierz lokalizację serwera bazy danych do hosta usług SSIS. Firma Microsoft zaleca, wybierz opcję tej samej lokalizacji Twojego środowiska uruchomieniowego integracji.
    
    c. Aby uzyskać **punkt końcowy serwera bazy danych katalogu**, wybierz punkt końcowy serwera bazy danych do hosta usług SSIS. Na podstawie wybranej bazy danych serwera, bazy danych SSISDB może zostać utworzony w Twoim imieniu jako autonomiczna baza danych, część puli elastycznej, lub w przypadku zarządzanych (wersja zapoznawcza) i jest dostępny w sieci publicznej lub przez przyłączenie sieci wirtualnej.
    
    d. Na **uwierzytelniania w usłudze AAD Użyj...**  pole wyboru, wybierz metodę uwierzytelniania dla serwera bazy danych do hosta usług SSIS: SQL lub Azure Active Directory (AAD) z programu Azure danych fabryki zarządzane usługi tożsamości (MSI). Po zaznaczeniu go, należy dodać do grupy usługi AAD z uprawnieniami dostępu do serwera bazy danych z pliku MSI fabryki danych, zobacz [AAD Włącz uwierzytelnianie dla IR Azure SSIS](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
    
    e. Aby uzyskać **administratora**, wprowadź nazwę użytkownika do uwierzytelnienia SQL dla serwera bazy danych do hosta usług SSIS.
    
    f. Aby uzyskać **hasło administratora**, wprowadź hasło uwierzytelniania SQL dla serwera bazy danych do hosta usług SSIS.  
    
    g. Dla **warstwy usługi bazy danych katalogu**, wybierz warstwy usługi dla serwera bazy danych do hosta usług SSIS: warstwy Basic/Standard/Premium lub nazwę puli elastycznej.
    
    h. Kliknij przycisk **Testuj połączenie** i w razie powodzenia kliknij **dalej**. 
4.  Na **Zaawansowane ustawienia** wykonaj następujące kroki:   

    ![Ustawienia zaawansowane](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
    
    a. Aby uzyskać **maksymalna równoległych wykonaniami każdego węzła**, wybierz maksymalną liczbę pakietów, które można wykonać jednocześnie na węzeł w klastrze środowiska uruchomieniowego integracji. Obsługiwane są tylko pakiet, który liczby są wyświetlane. Wybierz niską wartość, jeśli chcesz użyć więcej niż jednego rdzenia, aby uruchomić pakiet dużych/ciężki pojedynczego compute/pamięci-znacznym. Wybierz dużą liczbą, jeśli chcesz uruchomić co najmniej jednego pakietu małych/lekki w pojedynczego rdzenia. 
    
    b. Aby uzyskać **URI SAS kontenera ustawienia niestandardowe**, opcjonalnie wprowadź dostępu sygnatury dostępu Współdzielonego URI Uniform Resource Identifier () z kontenera obiektu Blob magazynu Azure, gdzie są przechowywane skrypt instalacji i skojarzone z nią pliki, zobacz [Instalacja niestandardowa dla IR Azure SSIS](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 
5. Na **wybierz sieć wirtualną...**  pole wyboru, wybierz, czy chcesz wziąć udział Twojego środowiska uruchomieniowego integracji z siecią wirtualną. Sprawdź go, jeśli używasz bazy danych SQL Azure z punktów końcowych usługi sieci wirtualnej/zarządzane wystąpienia (wersja zapoznawcza) do hostowania bazy danych SSISDB lub wymagają dostępu do danych lokalnych. oznacza to, że ma lokalnego źródła/miejsca docelowe danych w pakietów SSIS, zobacz [Join IR Azure SSIS do sieci wirtualnej](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Jeśli wybierzesz go, wykonaj następujące czynności:


    ![Zaawansowane ustawienia z sieci wirtualnej](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    a. Aby uzyskać **subskrypcji**, wybierz subskrypcję platformy Azure, która ma sieci wirtualnej. 
    
    b. Aby uzyskać **lokalizacji**, wybrano tej samej lokalizacji Twojego środowiska uruchomieniowego integracji.
    
    b. Dla **typu**, wybierz typ sieci wirtualnej: klasycznym lub usługi Azure Resource Manager. Zaleca się, że wybierz sieć wirtualna Azure Resource Manager, ponieważ klasycznej sieci wirtualnej zostaną wycofane wkrótce.
    
    c. Aby uzyskać **nazwa sieci wirtualnej**, wybierz nazwę Twojej sieci wirtualnej. Ta sieć wirtualna powinna być tej samej sieci wirtualnej używać bazy danych SQL Azure z punktów końcowych usługi sieci wirtualnej/zarządzane wystąpienia (wersja zapoznawcza) na potrzeby hostowania bazy danych SSISDB i jednej podłączonej do sieci lokalnej.

    d. Aby uzyskać **nazwy podsieci**, wybierz nazwę podsieci sieci wirtualnej. Ta sieć wirtualna powinna być innej podsieci niż używane dla wystąpienia zarządzane (wersja zapoznawcza) na host usług SSIS.
6. Kliknij przycisk **weryfikacji sieci wirtualnej** i w razie powodzenia kliknij **Zakończ** zacząć od utworzenia Twojego środowiska uruchomieniowego integracji usług SSIS Azure. 

    > [!IMPORTANT]
    > - Ukończenie tego procesu zajmuje około 20 minut
    > - Usługa Data Factory łączy się z usługą Azure SQL Database w celu przygotowania bazy danych wykazu usług SSIS (SSISDB). Skrypt również umożliwia skonfigurowanie uprawnień i ustawień dla sieci wirtualnej, jeśli określona i tworzy sprzężenie nowe wystąpienie klasy środowiska uruchomieniowego integracji usług SSIS Azure do sieci wirtualnej.
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
### Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = “[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = “[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"
```

### <a name="log-in-and-select-subscription"></a>Logowanie i wybieranie subskrypcji
Dodaj następujący kod, skrypt logowania i wybierz subskrypcję platformy Azure: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Weryfikowanie połączenia z bazą danych
Dodaj następujący skrypt, aby sprawdzić poprawność punktu końcowego serwera bazy danych SQL Azure. 

```powershell
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    if(![string]::IsNullOrEmpty($SSISDBPricingTier))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=“ + $SSISDBServerAdminPassword
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_"  ;
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
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
Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Uruchom poniższe polecenie, aby utworzyć fabrykę danych.

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Tworzenie środowiska Integration Runtime
Uruchom następujące polecenia, aby utworzyć środowisko uruchomieniowe integracji usług SSIS Azure, które pakiety usług SSIS działa na platformie Azure. 

Jeśli nie używasz bazy danych SQL Azure z punktów końcowych usługi sieci wirtualnej/zarządzane wystąpienia (wersja zapoznawcza) do hostowania bazy danych SSISDB, ani nie wymagają dostępu do danych lokalnych, można pominąć parametry VNetId i podsieć lub Przekaż puste wartości dla nich. W przeciwnym razie musisz nie Pomiń nich i przekaż prawidłowe wartości z konfiguracji sieci wirtualnej można znaleźć [Join IR Azure SSIS do sieci wirtualnej](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Jeśli używasz wystąpienia zarządzane (wersja zapoznawcza) na hoście usług SSIS można parametr CatalogPricingTier lub przekaże pustą wartość. W przeciwnym razie nie można pominąć go i musi przekazać prawidłową wartość z listy obsługiwanych warstw cenowych bazy danych SQL Azure, zobacz [limity zasobów bazy danych SQL](../sql-database/sql-database-resource-limits.md). 

Jeśli używasz uwierzytelniania usługi Azure Active Directory (AAD) z programu Azure danych fabryki zarządzane usługi tożsamości (MSI) do łączenia się z serwerem bazy danych, w przypadku pominięcia parametru CatalogAdminCredential, ale należy dodać do grupy usługi AAD z dostępem do Twojego MSI fabryki danych uprawnienia na serwerze bazy danych, zobacz [AAD Włącz uwierzytelnianie dla IR Azure SSIS](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

```powershell               
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}
```

Trzeba przekazać wartości parametrów VnetId i podsieci z Azure zarządzane wystąpienia SQL (wersja zapoznawcza), które łączy sieć wirtualną. Parametr CatalogPricingTier nie ma zastosowania dla wystąpienia zarządzane Azure SQL (wersja zapoznawcza). 

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

W tym miejscu jest pełna skrypt, który tworzy środowiska uruchomieniowego integracji usług SSIS Azure. 

```powershell
### Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = “[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = “[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"

### Log in and select subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    if(![string]::IsNullOrEmpty($SSISDBPricingTier))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=“ + $SSISDBServerAdminPassword
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_"  ;
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}

### Configure virtual network
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

### Create a data factory
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}

### Start integration runtime   
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
W tej sekcji można użyć szablonu usługi Azure Resource Manager do utworzenia środowiska uruchomieniowego integracji usług SSIS Azure. Poniżej przedstawiono wskazówki próbki: 

1. Utwórz plik JSON z następującym szablonem usługi Azure Resource Manager. Zastąp wartości w nawiasach (symbole zastępcze) własne wartości. 

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
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D4_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
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
    
2. Do wdrożenia szablonu usługi Azure Resource Manager, uruchom polecenie New-AzureRmResourceGroupDeployment, jak pokazano w poniższym przykładzie, gdzie ADFTutorialResourceGroup to nazwa grupy zasobów, a ADFTutorialARM.json pliku, który zawiera JSON Definicja dla fabryki danych i podczerwieni Azure SSIS. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    To polecenie tworzy z fabryki danych i IR Azure SSIS w nim, ale nie uruchamia IR. 
3. Aby uruchomić z IR Azure SSIS, uruchom polecenie Start-AzureRmDataFactoryV2IntegrationRuntime: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS
Teraz użyj programu SQL Server Data Tools (SSDT) lub SQL Server Management Studio (SSMS), aby wdrożyć pakiety usług SSIS na platformie Azure. Połączyć z serwerem bazy danych, obsługującym katalogu SSIS (SSISDB). Nazwa serwera bazy danych jest w formacie: &lt;nazwę serwera bazy danych SQL Azure&gt;. database.windows.net lub &lt;nazwa wystąpienia zarządzane (wersja zapoznawcza). Prefiks DNS&gt;. database.windows.net. Zobacz artykuł [Deploy packages (Wdrażanie pakietów)](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server), aby uzyskać instrukcje.

## <a name="next-steps"></a>Kolejne kroki
Inne IR Azure SSIS w tematach w tej dokumentacji:

- [Środowisko uruchomieniowe integracji usług SSIS Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera informacje o pojęciach dotyczących środowisk uruchomieniowych integracji ogólnie tym podczerwieni Azure SSIS. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera szczegółowe instrukcje dotyczące tworzenia środowiska Azure-SSIS IR i używania bazy danych Azure SQL do hostowania wykazu usług SSIS. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i opisów stanów w pobranych informacjach. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania środowiska Azure-SSIS IR. Zawiera on również instrukcje skalowania środowiska Azure-SSIS IR do wewnątrz za pomocą dodawania do niego węzłów. 
- [Dołącz IR Azure SSIS do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md). Ten artykuł zawiera informacje o pojęciach dotyczących przyłączania Twojej IR Azure SSIS do sieci wirtualnej platformy Azure. Umożliwia także kroki, aby skonfigurować sieci wirtualnej, tak aby IR Azure SSIS można dołączyć do sieci wirtualnej za pomocą portalu Azure. 

