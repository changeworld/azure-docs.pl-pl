---
title: Dołączania komputerów do zarządzania przez Konfiguracja DSC automatyzacji Azure
description: Konfigurowanie komputerów do zarządzania w usłudze Konfiguracja DSC automatyzacji Azure
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4493f9da0de12fbdfffdf0f4da0dd581ac3b589f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195560"
---
# <a name="onboarding-machines-for-management-by-azure-automation-dsc"></a>Dołączania komputerów do zarządzania przez Konfiguracja DSC automatyzacji Azure

## <a name="why-manage-machines-with-azure-automation-dsc"></a>Dlaczego zarządzanie maszynami z usługi Konfiguracja DSC automatyzacji Azure?

Podobnie jak [konfiguracji żądanego stanu środowiska PowerShell](https://technet.microsoft.com/library/dn249912.aspx), konfiguracji żądanego stanu programu Azure automatyzacji jest proste, ale wydajne, usługi do zarządzania konfiguracją dla węzłów DSC (fizycznych i maszyn wirtualnych) w dowolnego centrum danych chmury lub lokalnie. Umożliwia on skalowalność między maszyn szybko i łatwo z lokalizacji centralnej, bezpieczne. Możesz łatwo dodać maszyny, przypisz je deklaratywne konfiguracje i wyświetlanie raportów przedstawiający każdego komputera zgodności do pożądanego stanu, który można określić. Warstwa zarządzania Konfiguracja DSC automatyzacji Azure jest DSC warstwa zarządzania usługi Automatyzacja Azure jest do wykonywania skryptów PowerShell. Innymi słowy w taki sam sposób jak usługi Automatyzacja Azure ułatwia zarządzanie skryptów programu PowerShell, pomaga również zarządzać konfiguracji DSC. Aby dowiedzieć się więcej o zaletach usługi Konfiguracja DSC automatyzacji Azure, zobacz [omówienie Konfiguracja DSC automatyzacji Azure](automation-dsc-overview.md).

Konfiguracja DSC automatyzacji Azure mogą służyć do zarządzania różnych komputerach:

* Maszyny wirtualne platformy Azure (klasyczne)
* Maszyny wirtualne platformy Azure
* Maszyny wirtualne Amazon Web Services (AWS)
* Fizyczna/wirtualna systemu Windows maszyny lokalnej, lub w chmurze innych niż Azure/AWS
* W infrastrukturze lokalnej, na platformie Azure lub w chmurze innych niż Azure maszyny fizycznej/wirtualną systemu Linux

Ponadto jeśli firma nie jest gotowa do zarządzania konfiguracji komputera z chmury, konfiguracja DSC automatyzacji Azure mogą służyć jako punktu końcowego tylko do raportu. Dzięki temu można ustawić żądaną konfiguracją (push) za pośrednictwem usługi Konfiguracja DSC lokalnego oraz sformatowanego Szczegóły raportowania zgodności węzła z żądanego stanu w automatyzacji Azure.

> [!NOTE]
> Zarządzanie maszynach wirtualnych platformy Azure w usłudze Konfiguracja DSC jest dołączony bez dodatkowych opłat, jeśli zainstalowane rozszerzenia maszyny wirtualnej DSC jest większa niż 2.70. Zapoznaj się [ **automatyzacji cennikiem** ](https://azure.microsoft.com/pricing/details/automation/) więcej szczegółów.


W poniższych sekcjach opisano, jak można dołączyć każdego typu maszyny do usługi Konfiguracja DSC automatyzacji Azure.

## <a name="azure-virtual-machines-classic"></a>Maszyny wirtualne platformy Azure (klasyczne)

Z usługi Konfiguracja DSC automatyzacji Azure można łatwo dodać maszyn wirtualnych platformy Azure (klasyczne) dla zarządzania konfiguracją przy użyciu portalu Azure, lub programu PowerShell. Pod maską i bez konieczności zdalnego do maszyny Wirtualnej administratora rozszerzenia konfiguracji żądanego stanu programu Azure VM rejestruje maszyny Wirtualnej z usługi Konfiguracja DSC automatyzacji Azure. Ponieważ rozszerzenia konfiguracji żądanego stanu programu Azure VM uruchamia asynchronicznie, kroki, aby śledzić postęp lub Rozwiązywanie problemów znajdują się w następujących [ **dołączania maszyny wirtualnej Azure Rozwiązywanie problemów z** ](#troubleshooting-azure-virtual-machine-onboarding) sekcji.

### <a name="azure-portal"></a>Azure Portal

W [portalu Azure](http://portal.azure.com/), kliknij przycisk **Przeglądaj** -> **maszyn wirtualnych (klasyczne)**. Wybierz maszynę Wirtualną systemu Windows, które chcesz dołączyć. W bloku pulpitu nawigacyjnego maszyny wirtualnej, kliknij **wszystkie ustawienia** -> **rozszerzenia** -> **Dodaj** -> **Konfiguracja DSC automatyzacji Azure** -> **Utwórz**. Wprowadź [wartości środowiska PowerShell DSC lokalny program Configuration Manager](https://msdn.microsoft.com/powershell/dsc/metaconfig4) wymagane dla Twojego przypadek użycia, Twoje konto usługi Automatyzacja klucz rejestracji i adresu URL rejestracji i opcjonalnie konfiguracji węzła można przypisać do maszyny Wirtualnej.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

Aby znaleźć adres URL rejestracji i klucza dla konta usługi Automatyzacja, aby dołączyć maszynę, aby wyświetlić następujące [ **Secure rejestracji** ](#secure-registration) sekcji:

### <a name="powershell"></a>PowerShell

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Connect-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ""
$ServiceName = ""
$AutomationAccountName = ""
$AutomationAccountResourceGroup = ""

# fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
# NOTE: DSC Node Configuration names are case sensitive in the portal.
$NodeConfigName = ""

# get Azure Automation DSC registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation DSC
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ""
    ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
    ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
    RegistrationKey = @{
        UserName = 'notused'
        Password = 'PrivateSettingsRef:RegistrationKey'
    }
    RegistrationUrl = $RegistrationInfo.Endpoint
    NodeConfigurationName = $NodeConfigName
    ConfigurationMode = "ApplyAndMonitor"
    ConfigurationModeFrequencyMins = 15
    RefreshFrequencyMins = 30
    RebootNodeIfNeeded = $False
    ActionAfterReboot = "ContinueConfiguration"
    AllowModuleOverwrite = $False
    }
}

$PrivateConfiguration = ConvertTo-Json -Depth 8 @{
    Items = @{
        RegistrationKey = $RegistrationInfo.PrimaryKey
    }
}

$VM = Set-AzureVMExtension `
    -VM $vm `
    -Publisher Microsoft.Powershell `
    -ExtensionName DSC `
    -Version 2.19 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

> [!NOTE]
> Nazwy konfiguracji węzła DSC jest uwzględniana wielkość liter w portalu. Jeśli wielkość liter jest niezgodne węzeł nie będą widoczne w obszarze węzłów DSC.

## <a name="azure-virtual-machines"></a>Maszyny wirtualne platformy Azure

Konfiguracja DSC usługi Automatyzacja Azure pozwala łatwo dodać maszyn wirtualnych platformy Azure do zarządzania konfiguracją przy użyciu portalu Azure, szablonów usługi Azure Resource Manager lub programu PowerShell. Pod maską i bez konieczności zdalnego do maszyny Wirtualnej administratora rozszerzenia konfiguracji żądanego stanu programu Azure VM rejestruje maszyny Wirtualnej z usługi Konfiguracja DSC automatyzacji Azure. Ponieważ rozszerzenia konfiguracji żądanego stanu programu Azure VM uruchamia asynchronicznie, kroki, aby śledzić postęp lub Rozwiązywanie problemów znajdują się w następujących [ **dołączania maszyny wirtualnej Azure Rozwiązywanie problemów z** ](#troubleshooting-azure-virtual-machine-onboarding) sekcji.

### <a name="azure-portal"></a>Azure Portal

W [portalu Azure](https://portal.azure.com/), przejdź do konta usługi Automatyzacja Azure, które chcesz dołączyć maszyny wirtualnej. Na pulpicie nawigacyjnym konta automatyzacji, kliknij przycisk **węzłów DSC** -> **+ Dodaj maszyny Wirtualnej Azure**.

Wybierz maszynę wirtualną platformy Azure do dołączenia.

Jeśli komputer nie ma programu PowerShell desired zainstalowane rozszerzenia stan i stan zasilania jest uruchomiona, kliknij przycisk **Connect**.

W obszarze **rejestracji**, wprowadź [wartości środowiska PowerShell DSC lokalny program Configuration Manager](https://msdn.microsoft.com/powershell/dsc/metaconfig4) wymagany dla tej sprawy używany oraz opcjonalnie konfiguracji węzła można przypisać do maszyny Wirtualnej.

![](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Można wdrożyć maszyn wirtualnych platformy Azure i dołączać do konfiguracji DSC automatyzacji Azure za pomocą szablonów usługi Azure Resource Manager. Zobacz [skonfigurować Maszynę wirtualną za pomocą rozszerzenia usługi Konfiguracja DSC i konfiguracja DSC automatyzacji Azure](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) szablonu przykład tego onboards istniejącej maszyny Wirtualnej do usługi Konfiguracja DSC automatyzacji Azure. Aby znaleźć klucz rejestracji i adresu URL rejestracji podjęte jako danych wejściowych w tym szablonie, zobacz następujące tematy [ **Secure rejestracji** ](#secure-registration) sekcji.

### <a name="powershell"></a>PowerShell

[AzureRmAutomationDscNode rejestru](/powershell/module/azurerm.automation/register-azurermautomationdscnode) polecenia cmdlet można używać do dodawania maszyn wirtualnych w portalu Azure za pomocą programu PowerShell.

## <a name="amazon-web-services-aws-virtual-machines"></a>Maszyny wirtualne Amazon Web Services (AWS)

Możesz łatwo dodać usług Amazon Web Services maszyn wirtualnych do zarządzania konfiguracji DSC automatyzacji Azure przy użyciu zestawu narzędzi usług AWS DSC. Dowiedz się więcej o zestawie narzędzi programu [tutaj](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Fizyczna/wirtualna systemu Windows maszyny lokalnej, lub w chmurze innych niż Azure/AWS

Lokalnymi maszynami z systemem Windows i maszyny z systemem Windows w chmurze innych niż Azure (np. Amazon Web Services) także można dołączać do usługi Konfiguracja DSC automatyzacji Azure, jak długo mają połączenia wychodzące z Internetem za pośrednictwem kilku prostych kroków:

1. Upewnij się, najnowsza wersja [platformy WMF 5](http://aka.ms/wmf5latest) jest zainstalowany na komputerach, do których chcesz dołączyć do usługi Konfiguracja DSC automatyzacji Azure.
2. Postępuj zgodnie z instrukcjami w poniższej sekcji [ **metaconfigurations generowania DSC** ](#generating-dsc-metaconfigurations) można wygenerować folderu zawierającego potrzebne metaconfigurations DSC.
3. Zdalnie Zastosuj metakonfigurację DSC programu PowerShell na maszynach, które chcesz dołączyć. **To polecenie jest uruchamiane maszyny musi mieć najnowszą wersję [platformy WMF 5](http://aka.ms/wmf5latest) zainstalowane**:

    ```powershell
    Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
    ```

4. Jeśli nie można zastosować metaconfigurations DSC środowiska PowerShell zdalnie, należy skopiować metaconfigurations folder w kroku 2 na każdej maszynie do dołączenia. Następnie wywołaj **DscLocalConfigurationManager zestaw** lokalnie na każdym komputerze do dołączenia.
5. Za pomocą portalu Azure lub poleceń cmdlet, sprawdź, czy maszyny do dołączenia teraz wyświetlane jako węzły DSC zarejestrowana na koncie usługi Automatyzacja Azure.

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>W infrastrukturze lokalnej, na platformie Azure lub w chmurze innych niż Azure maszyny fizycznej/wirtualną systemu Linux

Maszyny z systemem Linux lokalnymi, maszyny z systemem Linux na platformie Azure i maszyny z systemem Linux w chmurze Azure z systemem innym niż także można dołączać do usługi Konfiguracja DSC automatyzacji Azure, jak długo mają połączenia wychodzące z Internetem za pośrednictwem kilku prostych kroków:

1. Upewnij się, najnowsza wersja [PowerShell konfiguracji żądanego stanu dla systemu Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) jest zainstalowany na komputerach, do których chcesz dołączyć do usługi Konfiguracja DSC automatyzacji Azure.
2. Jeśli [ustawienia domyślne programu PowerShell DSC lokalny program Configuration Manager](https://msdn.microsoft.com/powershell/dsc/metaconfig4) liter użycia i chcesz dołączyć maszyn takie że **zarówno** ściąganie danych z i raporty do usługi Konfiguracja DSC automatyzacji Azure:

   + Na każdej maszynie systemu Linux, aby rozpocząć korzystanie z usługi Konfiguracja DSC automatyzacji Azure Aby dołączyć przy użyciu ustawień domyślnych programu PowerShell DSC lokalny program Configuration Manager, użyj Register.py:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   + Aby znaleźć klucz rejestracji i adresu URL rejestracji Twoje konto usługi Automatyzacja, zobacz następujące tematy [ **Secure rejestracji** ](#secure-registration) sekcji.

     Jeśli ustawienia domyślne programu Configuration Manager lokalnego DSC środowiska PowerShell **czy** **nie** dopasowania sieci przypadek użycia, lub chcesz dołączyć maszyn, tak aby tylko raport, aby konfiguracja DSC automatyzacji Azure, ale zrobić nie ściągania konfiguracji lub moduły programu PowerShell z niego, należy wykonać kroki od 3 do 6. W przeciwnym razie bezpośrednio przejść do kroku 6.

3. Postępuj zgodnie z instrukcjami poniżej [ **metaconfigurations generowania DSC** ](#generating-dsc-metaconfigurations) sekcji, aby wygenerować folderu zawierającego potrzebne metaconfigurations DSC.
4. Na maszynach, które chcesz dołączyć zdalnie Zastosuj metakonfigurację DSC programu PowerShell:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard

    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

To polecenie jest uruchamiane maszyny musi mieć najnowszą wersję [platformy WMF 5](http://aka.ms/wmf5latest) zainstalowane.

1. Jeśli nie można zastosować metaconfigurations DSC środowiska PowerShell zdalnie, dla każdej maszyny Linux do dołączenia, skopiuj metakonfigurację odpowiadającego danej maszynie z folderu w kroku 5 na komputerze systemu Linux. Następnie wywołaj `SetDscLocalConfigurationManager.py` lokalnie na każdym komputerze z systemem Linux których chcesz dołączyć do usługi Konfiguracja DSC automatyzacji Azure:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Za pomocą portalu Azure lub poleceń cmdlet, sprawdź, czy maszyny do dołączenia teraz wyświetlane jako węzły DSC zarejestrowana na koncie usługi Automatyzacja Azure.

## <a name="generating-dsc-metaconfigurations"></a>Trwa generowanie metaconfigurations DSC

Można tu dodać dowolnego komputera do usługi Konfiguracja DSC automatyzacji Azure, [metakonfigurację DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig) mogą być generowane, gdy stosowane, informuje DSC agenta na komputerze ściąganie danych z i/lub raportować do usługi Konfiguracja DSC automatyzacji Azure. Metaconfigurations DSC dla konfiguracji DSC automatyzacji Azure mogą być generowane przy użyciu konfiguracji DSC środowiska PowerShell lub polecenia cmdlet programu PowerShell automatyzacji Azure.

> [!NOTE]
> DSC metaconfigurations zawierają kluczy tajnych potrzebne do dołączenia komputera do automatyzacji konto do zarządzania. Upewnij się, że odpowiednio chronić żadnych metaconfigurations DSC utworzone, lub usuń je po użyciu.

### <a name="using-a-dsc-configuration"></a>Za pomocą konfiguracji DSC

1. Otwórz program PowerShell ISE z uprawnieniami administratora na maszynie w środowisku lokalnym. Komputer musi mieć najnowszą wersję [platformy WMF 5](http://aka.ms/wmf5latest) zainstalowane.
2. Skopiuj poniższy skrypt lokalnie. Ten skrypt zawiera konfiguracji DSC środowiska PowerShell do tworzenia metaconfigurations i polecenie, aby rozpocząć poza tworzenia metakonfigurację.

> [!NOTE]
> Nazwy konfiguracji węzła DSC jest uwzględniana wielkość liter w portalu. Jeśli wielkość liter jest niezgodne węzeł nie będą widoczne w obszarze węzłów DSC.

    ```powershell
    # The DSC configuration that will generate metaconfigurations
    [DscLocalConfigurationManager()]
    Configuration DscMetaConfigs
    {

        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = "ApplyAndMonitor",

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = "ContinueConfiguration",

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq "")
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
        $RefreshMode = "PUSH"
        }
        else
        {
        $RefreshMode = "PULL"
        }

        Node $ComputerName
        {

            Settings
            {
                RefreshFrequencyMins = $RefreshFrequencyMins
                RefreshMode = $RefreshMode
                ConfigurationMode = $ConfigurationMode
                AllowModuleOverwrite = $AllowModuleOverwrite
                RebootNodeIfNeeded = $RebootNodeIfNeeded
                ActionAfterReboot = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationDSC
                {
                    ServerUrl = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationDSC
                {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationDSC
            {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
    }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
    $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
    }

    # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    DscMetaConfigs @Params
    ```

3. Wprowadź klucz rejestracji i adres URL dla konta automatyzacji, jak również nazwy maszyny do dołączenia. Wszystkie inne parametry są opcjonalne. Aby znaleźć klucz rejestracji i adresu URL rejestracji Twoje konto usługi Automatyzacja, zobacz następujące tematy [ **Secure rejestracji** ](#secure-registration) sekcji.
4. Maszyny zgłoszenia DSC informacje o stanie do usługi Konfiguracja DSC automatyzacji Azure, ale nie ściągania konfiguracji lub moduły programu PowerShell, ustawić **ReportOnly** parametru na wartość true.
5. Uruchom skrypt. Teraz powinno istnieć folder o nazwie **DscMetaConfigs** w katalogu roboczym zawierający metaconfigurations DSC środowiska PowerShell dla maszyn dołączyć (jako administrator):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Za pomocą poleceń cmdlet usługi Automatyzacja Azure

Jeśli ustawienia domyślne programu PowerShell DSC lokalny program Configuration Manager są zgodne z przypadek użycia, i chcesz dołączyć maszyn tak, aby mogli zarówno ściąganie danych z i raporty do usługi Konfiguracja DSC automatyzacji Azure, poleceń cmdlet usługi Automatyzacja Azure umożliwiają uproszczony generowania metaconfigurations DSC potrzebne:

1. Otwórz konsolę lub środowisko ISE programu PowerShell jako administrator na maszynie w środowisku lokalnym.
2. Nawiązywanie połączenia przy użyciu usługi Azure Resource Manager **Connect-AzureRmAccount**
3. Pobierz metaconfigurations DSC środowiska PowerShell dla maszyn, które mają do dołączenia z konta automatyzacji, do której chcesz dodać węzły:

    ```powershell
    # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
    $Params = @{

        ResourceGroupName = 'ContosoResources'; # The name of the ARM Resource Group that contains your Azure Automation Account
        AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
        ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
        OutputFolder = "$env:UserProfile\Desktop\";
    }
    # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    Get-AzureRmAutomationDscOnboardingMetaconfig @Params
    ```
    
4. Teraz powinno istnieć folder o nazwie ***DscMetaConfigs***, zawierający metaconfigurations DSC środowiska PowerShell dla maszyn dołączyć (jako administrator):
    
    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Bezpieczne rejestracji

Maszyny można bezpiecznie dołączyć do konta usługi Automatyzacja Azure za pośrednictwem protokołu rejestracji platformy WMF 5 DSC, dzięki czemu węzła DSC do uwierzytelniania na serwerze ściągania usługi Konfiguracja DSC programu PowerShell lub serwer raportowania (w tym konfiguracja DSC automatyzacji Azure). Rejestruje węzła do serwera w **adresu URL rejestracji**, uwierzytelniania przy użyciu **klucz rejestracji**. Podczas rejestracji, węzła DSC i serwera ściągania usługi Konfiguracja DSC/raportowania negocjowania unikatowy certyfikat dla tego węzła na potrzeby uwierzytelniania serwera po rejestracji. Dzięki temu węzłów został załadowany z personifikacja jeden inny, na przykład jeśli węzeł zostanie naruszone bezpieczeństwo i zachowuje się złośliwe. Po rejestracji klucz rejestracji nie jest używany do uwierzytelniania ponownie i są usuwane z węzła.

Można uzyskać informacji wymaganych dla protokołu rejestrację DSC z **klucze** w obszarze **ustawienia konta** w portalu Azure. Otwórz ten blok, klikając ikonę klucza **Essentials** panelu dla konta automatyzacji.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

* Adres URL rejestracji jest pole adresu URL w bloku zarządzanie kluczami.
* Klucz rejestracji jest klucz dostępu podstawowy lub pomocniczy klucz dostępu w bloku zarządzanie kluczami. Można użyć albo klucza.

Aby zwiększyć bezpieczeństwo, klucze podstawowe i pomocnicze dostępu konta automatyzacji można ponownie wygenerować w dowolnym momencie (na **zarządzanie kluczami** bloku) aby zapobiec węzła przyszłych rejestracji przy użyciu poprzednich kluczy.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Rozwiązywanie problemów z dołączania maszyny wirtualnej platformy Azure

Konfiguracja DSC usługi Automatyzacja Azure pozwala łatwo dołączać maszyny wirtualne systemu Windows Azure dla zarządzania konfiguracją. Pod maską rozszerzenia konfiguracji żądanego stanu programu Azure VM służy do rejestrowania maszyny Wirtualnej z usługi Konfiguracja DSC automatyzacji Azure. Ponieważ rozszerzenia konfiguracji żądanego stanu programu Azure VM uruchamia asynchronicznie, śledzenie postępu i rozwiązywanie problemów z jej wykonanie może być ważne.

> [!NOTE]
> Każda metoda dołączania maszyny Wirtualnej systemu Windows Azure do konfiguracji DSC automatyzacji Azure używający rozszerzenia konfiguracji żądanego stanu programu Azure maszyny Wirtualnej może potrwać do godziny na węzeł, aby wyświetlić się zarejestrować w usłudze Automatyzacja Azure. Jest to spowodowane instalacji systemu Windows Management Framework 5.0 na maszynie Wirtualnej przez rozszerzenia DSC maszyny Wirtualnej Azure, który jest wymagany do dołączenia do usługi Konfiguracja DSC automatyzacji Azure maszyny Wirtualnej.

Do rozwiązania lub wyświetlić stan rozszerzenia konfiguracji żądanego stanu programu Azure maszyny Wirtualnej, w portalu Azure przejdź do maszyny Wirtualnej trwa został załadowany, a następnie kliknij przycisk **rozszerzenia** w obszarze **ustawienia**. Następnie kliknij przycisk **DSC** lub **DSCForLinux** w zależności od używanego systemu operacyjnego. Aby uzyskać więcej informacji, kliknij **wyświetlić szczegółowy stan**.

## <a name="certificate-expiration-and-reregistration"></a>Wygaśnięcie certyfikatu i ponowna rejestracja

Po zarejestrowaniu komputera jako węzła DSC w konfiguracji DSC automatyzacji Azure istnieją istnieje wiele możliwych przyczyn, dlaczego należy zarejestrować ten węzeł w przyszłości:

* Po zarejestrowaniu każdego węzła automatycznie negocjuje unikatowy certyfikat do uwierzytelniania, która wygasa po roku. Obecnie protokołu rejestrację DSC programu PowerShell nie automatycznego odnawiania certyfikatów, po ich wkrótce wygasną, więc należy ponownie zarejestrować węzły po roku. Przed ponownie zarejestrować, upewnij się, że każdy węzeł działa system Windows Management Framework 5.0 RTM. Jeśli certyfikat uwierzytelniania węzła wygaśnie, a węzeł nie jest zarejestrowane, węzeł nie może nawiązać połączenia z usługi Automatyzacja Azure i jest oznaczony jako "Unresponsive." Ponowna rejestracja wykonać 90 dni lub mniejsza od czasu wygaśnięcia certyfikatu lub w dowolnym momencie po czas wygaśnięcia certyfikatu spowoduje powstanie nowego certyfikatu Trwa generowanie i używanie.
* Aby zmienić dowolne [wartości środowiska PowerShell DSC lokalny program Configuration Manager](https://msdn.microsoft.com/powershell/dsc/metaconfig4) które zostały określone podczas początkowej rejestracji węzła, takich jak ConfigurationMode. Obecnie te wartości agenta DSC można zmienić tylko za pośrednictwem ponowna rejestracja. Jedynym wyjątkiem jest konfiguracji węzła przypisanej do węzła — można to zmienić w konfiguracji DSC automatyzacji Azure bezpośrednio.

Ponowna rejestracja można wykonać w taki sam sposób, w zarejestrowany węzeł początkowo przy użyciu dowolnej z metod dołączania opisanych w tym dokumencie. Nie trzeba wyrejestrować węzła z usługi Konfiguracja DSC automatyzacji Azure przed ponownie go zarejestrować.

## <a name="related-articles"></a>Pokrewne artykuły

* [Omówienie usługi Konfiguracja DSC automatyzacji Azure](automation-dsc-overview.md)
* [Polecenia cmdlet systemu Azure Automation DSC](/powershell/module/azurerm.automation/#automation)
* [Cennik usługi Konfiguracja DSC automatyzacji Azure](https://azure.microsoft.com/pricing/details/automation/)
