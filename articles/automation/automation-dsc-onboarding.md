---
title: Dołączanie maszyn w celu zarządzania usługi Azure Automation stanu konfiguracji
description: Jak skonfigurować maszyn w celu zarządzania za pomocą usługi Azure Automation stanu konfiguracji
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: 8a505e88ff92c5227d3b42da2adaf1dce58e6fbb
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441511"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Dołączanie maszyn w celu zarządzania usługi Azure Automation stanu konfiguracji

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Dlaczego zarządzanie maszyn za pomocą usługi Azure Automation stan konfiguracji?

Konfiguracja stanu usługi Azure Automation to usługa zarządzania konfiguracji węzłów DSC w chmurze lub środowisku lokalnym centrum danych.
Umożliwia on skalowalności w tysiącach maszyn szybko i łatwo z lokalizacji centralnej i bezpieczne.
Można łatwo dołączać maszyny, przypisz je deklaratywne konfiguracje i Wyświetl raporty pokazujące każdego komputera zgodności do pożądanego stanu, który określiłeś...
Usługa Azure Automation stan konfiguracji jest DSC, elementy runbook usługi Azure Automation są do obsługi skryptów programu PowerShell.
Innymi słowy w taki sam sposób, że usługi Azure Automation pozwala na Zarządzanie skryptami programu PowerShell, pomaga także zarządzanie konfiguracjami DSC.
Aby dowiedzieć się więcej na temat korzyści z używania usługi Azure Automation stan konfiguracji, zobacz [Omówienie usługi Azure Automation stan konfiguracji](automation-dsc-overview.md).

Konfiguracja stanu usługi Azure Automation można zarządzać różnymi maszynami:

- Maszyny wirtualne platformy Azure
- Maszyny wirtualne platformy Azure (wersja klasyczna)
- Wystąpienia usługi EC2 usług Amazon Web Services (AWS)
- Fizycznymi/wirtualnymi Windows działające lokalnie lub w chmurze innej niż Azure/AWS
- Fizycznymi/wirtualnymi systemu Linux działające w środowisku lokalnym, na platformie Azure lub w chmurze innej niż platformy Azure

Ponadto jeśli nie jesteś gotowy do zarządzanie konfiguracją maszyn w chmurze, Azure Automation State Configuration umożliwia także jako punktu końcowego tylko do raportu.
Dzięki temu można ustawić konfiguracji (push) za pomocą DSC i w widoku raportowanie szczegółów w usłudze Azure Automation.

> [!NOTE]
> Zarządzanie maszynami wirtualnymi platformy Azure za pomocą konfiguracji stanu jest dołączona, bez dodatkowych opłat w przypadku, jeśli zainstalowane rozszerzenie maszyny wirtualnej DSC jest większa niż 2.70. Zapoznaj się [ **stronę z cennikiem usługi Automation** ](https://azure.microsoft.com/pricing/details/automation/) Aby uzyskać więcej informacji.

W poniższych sekcjach opisano, jak można dodać każdego typu maszyny do usługi Azure Automation stan konfiguracji.

## <a name="azure-virtual-machines"></a>Maszyny wirtualne platformy Azure

Konfiguracja stanu usługi Azure Automation umożliwia łatwe dodawanie maszyn wirtualnych platformy Azure do zarządzania konfiguracją za pomocą witryny Azure portal, szablonów usługi Azure Resource Manager lub programu PowerShell. Pod maską i bez konieczności zdalnego z maszyną wirtualną administratora rozszerzenia maszyny Wirtualnej platformy Azure dzięki Desired State Configuration rejestruje maszyny Wirtualnej za pomocą usługi Azure Automation stan konfiguracji.
Ponieważ rozszerzenie maszyny Wirtualnej platformy Azure dzięki Desired State Configuration jest uruchamiane asynchronicznie, kroki, aby śledzić postęp lub rozwiązać problemy znajdują się w następującym [ **dołączania maszyny wirtualnej rozwiązywania problemów Azure** ](#troubleshooting-azure-virtual-machine-onboarding) sekcji.

### <a name="azure-portal"></a>Azure Portal

W [witryny Azure portal](https://portal.azure.com/), przejdź do konta usługi Azure Automation, które chcesz dołączać maszyny wirtualne. Na stronie Konfiguracja stanu i **węzłów** kliknij pozycję **+ Dodaj**.

Wybierz maszynę wirtualną platformy Azure do dołączenia.

Jeśli komputer nie ma programu PowerShell żądanego stanu rozszerzenia zainstalowane i uruchomiono jego stanu zasilania, kliknij przycisk **Connect**.

W obszarze **rejestracji**, wprowadź [wartości PowerShell DSC Local Configuration Manager](/powershell/dsc/metaconfig4) wymagane dla danego przypadku użycia i opcjonalnie konfiguracji węzła można przypisać do maszyny Wirtualnej.

![Dołączanie do](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Można wdrożyć maszyny wirtualne platformy Azure i dołączone do usługi Azure Automation stan konfiguracji za pomocą szablonów usługi Azure Resource Manager. Zobacz [serwer zarządzany przez usługę Desired State Configuration](https://azure.microsoft.com/resources/templates/101-automation-configuration/) dla przykładowy szablon który służy do dołączania istniejącej maszyny Wirtualnej do usługi Azure Automation stan konfiguracji.
Jeśli zarządzasz zestawu skalowania maszyn wirtualnych, zobacz przykładowy szablon [konfiguracji zestawu skalowania maszyn wirtualnych zarządzanych przez usługę Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

[AzureRmAutomationDscNode rejestru](/powershell/module/azurerm.automation/register-azurermautomationdscnode) polecenie cmdlet służy do dołączania maszyn wirtualnych w witrynie Azure portal za pomocą programu PowerShell.

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Rejestrowanie maszyn wirtualnych między subskrypcjami platformy Azure

Najlepszym sposobem rejestrowania maszyn wirtualnych z innych subskrypcji platformy Azure jest użycie rozszerzenia DSC w szablonie wdrożenia usługi Azure Resource Manager.
Przykłady znajdują się w [Desired State Configuration rozszerzenia z szablonami usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).
Aby znaleźć klucz rejestracji i adres URL rejestracji do użycia jako parametry w szablonie, zobacz następującą [ **zabezpieczania rejestracji** ](#secure-registration) sekcji.

## <a name="amazon-web-services-aws-virtual-machines"></a>Maszyn wirtualnych usług Amazon Web Services (AWS)

Można łatwo dołączać maszyny wirtualne usług Amazon Web Services do zarządzania konfiguracją, Konfiguracja stanu automatyzacji platformy Azure przy użyciu zestawu narzędzi DSC usługi AWS. Dowiedz się więcej o zestawie narzędzi programu [tutaj](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Fizycznymi/wirtualnymi Windows działające lokalnie lub w chmurze innej niż Azure/AWS

Serwery z systemem Windows w środowisku lokalnym lub w innych środowiskach chmury można też dołączona do usługi Azure Automation stan konfiguracji, tak długo, jak długo mają [wychodzący dostęp do platformy Azure](automation-dsc-overview.md#network-planning):

1. Upewnij się, że najnowsza wersja [WMF 5](https://aka.ms/wmf5latest) jest zainstalowany na komputerach, na których chcesz dołączyć do usługi Azure Automation stan konfiguracji.
1. Postępuj zgodnie z instrukcjami w poniższej sekcji [ **metaconfigurations generowania DSC** ](#generating-dsc-metaconfigurations) do generowania folder zawierający potrzebne metaconfigurations DSC.
1. Zdalnie zastosować metaconfiguration DSC programu PowerShell na maszynach, które chcesz dołączyć. **Na maszynie, to polecenie jest wykonywane z należy najnowszą wersję [WMF 5](https://aka.ms/wmf5latest) zainstalowane**:

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Jeśli nie zastosujesz zdalnie metaconfigurations DSC programu PowerShell, skopiuj metaconfigurations folder w kroku 2 na każdym komputerze, do dołączenia. Następnie wywołaj **Set-DscLocalConfigurationManager** lokalnie na każdym komputerze, aby dołączyć.
1. Za pomocą witryny Azure portal lub poleceń cmdlet, upewnij się, że maszyny, aby dołączyć teraz są wyświetlane jako Konfiguracja stanu węzły zarejestrowane na koncie usługi Azure Automation.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Fizycznymi/wirtualnymi systemu Linux działające lokalnie lub w chmurze innej niż platformy Azure

Serwery z systemem Linux w środowisku lokalnym lub w innych środowiskach chmury można też dołączona do usługi Azure Automation stan konfiguracji, tak długo, jak długo mają [wychodzący dostęp do platformy Azure](automation-dsc-overview.md#network-planning):

1. Upewnij się, że najnowsza wersja [PowerShell Desired State Configuration dla systemu Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) jest zainstalowany na komputerach, na których chcesz dołączyć do usługi Azure Automation stan konfiguracji.
1. Jeśli [domyślne ustawienia środowiska PowerShell DSC Local Configuration Manager](/powershell/dsc/metaconfig4) dopasowania danego przypadku użycia, a chcesz dołączyć maszyn, takie, **zarówno** ściąganie z i raportować do usługi Azure Automation stan konfiguracji:

   - Na każdym komputerze Linux do dołączenia do usługi Azure Automation stan konfiguracji, należy użyć `Register.py` dołączyć przy użyciu ustawień domyślnych programu PowerShell DSC Local Configuration Manager:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Aby znaleźć klucz rejestracji i adres URL rejestracji dla konta usługi Automation, zobacz następującą [ **zabezpieczania rejestracji** ](#secure-registration) sekcji.

     Jeśli ustawienia domyślne programu PowerShell DSC Local Configuration Manager **nie** zgodne danego przypadku użycia lub mają dołączania maszyn w taki sposób, że tylko raport, do usługi Azure Automation stan konfiguracji, wykonaj kroki 3 – 6. W przeciwnym razie bezpośrednio przejść do kroku 6.

1. Wykonaj czynności podane w następującym [ **metaconfigurations generowania DSC** ](#generating-dsc-metaconfigurations) sekcji, aby wygenerować folder zawierający potrzebne metaconfigurations DSC.
1. Zdalnie zastosować metaconfiguration DSC programu PowerShell na maszynach, które chcesz dołączyć:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Na maszynie, to polecenie jest wykonywane z należy najnowszą wersję [WMF 5](https://aka.ms/wmf5latest) zainstalowane.

1. Jeśli nie zastosujesz zdalnie metaconfigurations DSC programu PowerShell, skopiuj metaconfiguration odpowiadający tej maszyny z folderu w kroku 5 na maszyny z systemem Linux. Następnie wywołaj `SetDscLocalConfigurationManager.py` lokalnie na każdym komputerze z systemem Linux których chcesz dołączyć do usługi Azure Automation stan konfiguracji:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Przy użyciu witryny Azure portal lub poleceń cmdlet, upewnij się, że teraz wyświetlane maszyn do dołączenia jako węzły DSC zarejestrowany na koncie usługi Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Trwa generowanie metaconfigurations DSC

Ogólnie dołączania żadnego komputera na konfiguracji stan automatyzacji platformy Azure, [DSC metaconfiguration](/powershell/dsc/metaconfig) mogą być generowane który nakazuje agentowi DSC ściąganie z i/lub raport do usługi Azure Automation stan konfiguracji. Metaconfigurations DSC usługi Azure Automation stan konfiguracji mogą być generowane przy użyciu konfiguracji DSC programu PowerShell lub polecenia cmdlet programu PowerShell do automatyzacji platformy Azure.

> [!NOTE]
> DSC metaconfigurations zawierać wpisy tajne, potrzebne do dołączenia komputera do usługi Automation konto do zarządzania. Upewnij się, że odpowiednio chronić żadnych metaconfigurations DSC, które można utworzyć lub usunąć je po użyciu.

### <a name="using-a-dsc-configuration"></a>Przy użyciu konfiguracji DSC

1. Otwórz VSCode (lub ulubionym edytorze) z uprawnieniami administratora na maszynie w środowisku lokalnym. Komputer musi mieć najnowszą wersję [WMF 5](https://aka.ms/wmf5latest) zainstalowane.
1. Skopiuj poniższy skrypt lokalnie. Ten skrypt zawiera konfiguracji DSC programu PowerShell do tworzenia metaconfigurations oraz polecenie, aby rozpocząć tworzenie metaconfiguration.

> [!NOTE]
> Stan nazwy konfiguracji węzła konfiguracji jest uwzględniana wielkość liter w portalu. Jeśli wielkość liter jest niezgodny węzła nie będą wyświetlane w obszarze **węzłów** kartę.

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

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
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

1. Wprowadź klucz rejestracji i adres URL dla konta usługi Automation, a także nazwy maszyn do dołączenia. Wszystkie inne parametry są opcjonalne. Aby znaleźć klucz rejestracji i adres URL rejestracji dla konta usługi Automation, zobacz następującą [ **zabezpieczania rejestracji** ](#secure-registration) sekcji.
1. Jeśli maszyn do zgłaszania informacji o stanie DSC usługi Azure Automation stan konfiguracji, ale nie ściągania konfiguracji lub modułów programu PowerShell, należy ustawić **ReportOnly** parametru na wartość true.
1. Uruchom skrypt. Teraz masz folder o nazwie **DscMetaConfigs** w katalogu roboczym zawierający metaconfigurations DSC programu PowerShell dla maszyn dołączyć (jako administrator):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Za pomocą poleceń cmdlet usługi Azure Automation

Jeśli ustawienia domyślne programu PowerShell DSC Local Configuration Manager odpowiada danego przypadku użycia i mają dołączania maszyn w taki sposób, że ich pobierania z i raportować do usługi Azure Automation stan konfiguracji, polecenia cmdlet usługi Azure Automation zapewniają uproszczony sposób generowania metaconfigurations DSC, potrzebne:

1. Otwórz konsolę programu PowerShell lub programu VSCode z uprawnieniami administratora na maszynie w środowisku lokalnym.
2. Nawiązać połączenie za pomocą usługi Azure Resource Manager `Connect-AzureRmAccount`
3. Pobierz metaconfigurations DSC programu PowerShell dla maszyn, które chcesz dołączyć z konta usługi Automation, do którego chcesz dołączyć węzłów:

   ```powershell
   # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzureRmAutomationDscOnboardingMetaconfig @Params
   ```

1. Teraz masz folder o nazwie ***DscMetaConfigs***, zawierający metaconfigurations DSC programu PowerShell dla maszyn dołączyć (jako administrator):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Zabezpieczania rejestracji

Maszyny można bezpiecznie dodać konto usługi Azure Automation za pośrednictwem protokołu rejestracji WMF 5 DSC, który umożliwia węzła DSC uwierzytelniać się na serwerze ściągania DSC programu PowerShell lub serwer raportowania (w tym usługi Azure Automation State Configuration). Węzeł rejestruje się z serwerem w **adres URL rejestracji**, korzystając z uwierzytelniania przy użyciu **klucz rejestracji**. Podczas rejestracji węzła DSC i serwer DSC Pull/raportowania negocjowania unikatowy certyfikat dla tego węzła na potrzeby uwierzytelniania serwera po rejestracji. Dzięki temu dołączono węzłów z personifikacja jeden inny przykład w przypadku, gdy węzeł zostanie naruszony i działa w sposób złośliwy. Po zarejestrowaniu klucz rejestracji nie jest używany do uwierzytelniania ponownie i zostanie usunięty z węzła.

Możesz uzyskać wymaganych informacji dla protokołu rejestracji stanu konfiguracji z **klucze** w obszarze **ustawienia konta** w witrynie Azure portal. Otwórz ten blok, klikając ikonę klucza **Essentials** panelu dla konta usługi Automation.

![Klucze usługi Azure automation i adres URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Adres URL rejestracji jest pole adresu URL w bloku zarządzanie kluczami.
- Klucz rejestracji jest klucz dostępu podstawowy lub pomocniczy klucz dostępu w bloku zarządzanie kluczami. Może służyć żadnego z nich.

Aby zwiększyć bezpieczeństwo, kluczy podstawowych i pomocniczych dostępu konta usługi Automation można ponownie wygenerować w dowolnym momencie (na **zarządzanie kluczami** strony) aby zapobiec rejestracji przyszłych węzła przy użyciu poprzednich kluczy.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Rozwiązywanie problemów z dołączania maszyn wirtualnych platformy Azure

Konfiguracja stanu usługi Azure Automation pozwala łatwo dołączanie maszyn wirtualnych Windows Azure do zarządzania konfiguracją. Kulisy rozszerzenie maszyny Wirtualnej platformy Azure dzięki Desired State Configuration jest używane do rejestrowania maszyny Wirtualnej za pomocą usługi Azure Automation stan konfiguracji. Ponieważ rozszerzenie maszyny Wirtualnej platformy Azure dzięki Desired State Configuration jest uruchamiane asynchronicznie, śledzenia postępu i rozwiązywanie problemów z jej wykonanie jest ważne.

> [!NOTE]
> Każda metoda przechodzenia do maszyny Wirtualnej Windows Azure do usługi Azure Automation stan konfiguracji, która korzysta z rozszerzenia Azure VM Desired State Configuration może potrwać do godziny węzła do wyświetlenia w górę zarejestrowane w usłudze Azure Automation. Jest to spowodowane instalacji systemu Windows Management Framework 5.0 na maszynie Wirtualnej przez rozszerzenie DSC maszyny Wirtualnej platformy Azure, który jest wymagany do dołączenia maszyny Wirtualnej do usługi Azure Automation stan konfiguracji.

Do rozwiązania lub wyświetlanie stanu rozszerzenia Desired State Configuration maszyny Wirtualnej platformy Azure, w witrynie Azure portal przejdź do maszyny Wirtualnej są dołączone, a następnie kliknij przycisk **rozszerzenia** w obszarze **ustawienia**. Następnie kliknij przycisk **DSC** lub **DSCForLinux** w zależności od używanego systemu operacyjnego. Aby uzyskać więcej informacji, kliknij **Wyświetl szczegółowy stan**.

## <a name="certificate-expiration-and-reregistration"></a>Wygaśnięcie certyfikatu i ponownej rejestracji

Po zarejestrowaniu maszynę jako węzeł DSC usługi Azure Automation stan konfiguracji, istnieje kilka powodów dlaczego konieczne może być ponownie zarejestrować ten węzeł w przyszłości:

- Po zarejestrowaniu każdego węzła automatycznie negocjuje unikatowy certyfikat dla uwierzytelniania, która wygasa po roku. Obecnie protokołu rejestrację DSC programu PowerShell nie automatyczne odnawianie certyfikatów wystawionych podczas ich wkrótce wygasną, więc należy ponownie zarejestrować węzłów po roku. Przed ponownego rejestrowania środowiska, upewnij się, że każdy węzeł jest uruchomiona, Windows Management Framework 5.0 RTM. Jeśli węzeł uwierzytelniania certyfikat wygaśnie, a węzeł nie jest zarejestrowane, węzeł nie może komunikować się z usługą Azure Automation i jest oznaczony jako "Unresponsive." Ponowna rejestracja wykonywane 90 dni lub większa od czasu wygaśnięcia certyfikatu, w dowolnym momencie po czas wygaśnięcia certyfikatu spowoduje nowy certyfikat jest generowany i używane.
- Aby zmienić dowolne [wartości PowerShell DSC Local Configuration Manager](/powershell/dsc/metaconfig4) które zostały ustawione podczas wstępnej rejestracji węzła, takich jak ConfigurationMode. Obecnie te wartości agenta DSC można zmienić tylko za pośrednictwem ponownej rejestracji. Jedynym wyjątek polega na konfiguracji węzła przypisanej do węzła — można to zmienić w usłudze Azure Automation DSC bezpośrednio.

Ponowna rejestracja można wykonać w taki sam sposób, jak zarejestrować węzeł początkowo przy użyciu dowolnej z metod dołączania, opisane w tym dokumencie. Nie musisz wyrejestrować węzła z usługi Azure Automation stan konfiguracji przed reregistering go.

## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć pracę, zobacz [wprowadzenie do usługi Azure Automation stanu konfiguracji](automation-dsc-getting-started.md)
- Aby dowiedzieć się więcej na temat kompilowanie konfiguracji DSC, dzięki czemu można je przypisać do węzłów docelowych, zobacz [kompilowanie konfiguracji w konfiguracji stan automatyzacji platformy Azure](automation-dsc-compile.md)
- Dokumentacja poleceń cmdlet programu PowerShell, można zobaczyć [poleceń cmdlet usługi Azure Automation stanu konfiguracji](/powershell/module/azurerm.automation/#automation)
- Aby uzyskać informacje o cenach, zobacz [cennika usługi Azure Automation stanu konfiguracji](https://azure.microsoft.com/pricing/details/automation/)
- Aby wyświetlić przykład użycia usługi Azure Automation stanu konfiguracji w potoku ciągłego wdrażania, zobacz [ciągłe przy użyciu usługi Azure Automation stan konfiguracji wdrożenia i narzędzia Chocolatey](automation-dsc-cd-chocolatey.md)
