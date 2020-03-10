---
title: Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu
description: Jak skonfigurować maszyny do zarządzania przy użyciu konfiguracji stanu Azure Automation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: 89e86a6702be7314b99975cac90818252eb07df7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372304"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Dołączanie maszyn w celu zarządzania przez Azure Automation konfigurację stanu

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Dlaczego warto zarządzać maszynami z użyciem Azure Automation stanu konfiguracji?

Azure Automation konfiguracja stanu to usługa zarządzania konfiguracją dla węzłów konfiguracji żądanego stanu (DSC) w dowolnym chmurze lub lokalnym centrum danych.
Umożliwia szybkie i łatwe skalowanie na tysiącach maszyn z poziomu centralnej, bezpiecznej lokalizacji.
Można łatwo dołączać maszyny, przypisywać do nich konfiguracje deklaracyjne i wyświetlać raporty pokazujące zgodność poszczególnych maszyn z wybranym określonym stanem.
Usługa konfiguracji stanu Azure Automation to DSC, co Azure Automation elementów Runbook do obsługi skryptów programu PowerShell.
Innymi słowy, w taki sam sposób, jaki Azure Automation ułatwia zarządzanie skryptami programu PowerShell, ułatwia również zarządzanie konfiguracjami DSC.
Aby dowiedzieć się więcej na temat korzyści z używania konfiguracji stanu Azure Automation, zobacz [Konfiguracja stanu Azure Automation — Omówienie](automation-dsc-overview.md).

Azure Automation konfiguracja stanu może służyć do zarządzania różnymi maszynami:

- Maszyny wirtualne platformy Azure
- Azure Virtual Machines (klasyczny)
- Fizyczne/wirtualne maszyny z systemem Windows w środowisku lokalnym lub w chmurze innej niż Azure (w tym wystąpienia AWS EC2)
- Fizyczne/wirtualne maszyny z systemem Linux lokalnie, na platformie Azure lub w chmurze innej niż Azure

Ponadto jeśli nie jesteś gotowy do zarządzania konfiguracją komputera z chmury, Azure Automation konfiguracja stanu może być również używana jako punkt końcowy tylko do raportowania.
Umożliwia to Ustawianie (wypychania) konfiguracji za poorednictwem DSC i wyświetlanie szczegółów raportowania w Azure Automation.

> [!NOTE]
> Zarządzanie maszynami wirtualnymi platformy Azure z konfiguracją stanu jest bezpłatne, jeśli zainstalowane rozszerzenie DSC maszyny wirtualnej jest większe niż 2,70. Aby uzyskać więcej informacji, zobacz [**stronę z cennikiem usługi Automation**](https://azure.microsoft.com/pricing/details/automation/).

W poniższych sekcjach opisano, jak można dołączyć każdy typ maszyny do Azure Automation konfiguracji stanu.

> [!NOTE]
>Wdrożenie DSC w węźle systemu Linux korzysta z folderu `/tmp` i modułów, takich jak **nxAutomation** , są tymczasowo pobierane do weryfikacji przed ich zainstalowaniem w odpowiedniej lokalizacji. Aby upewnić się, że moduły są poprawnie zainstalowane, Agent Log Analytics dla systemu Linux musi mieć uprawnienia do odczytu/zapisu w folderze `/tmp`. Agent Log Analytics dla systemu Linux działa jako użytkownik `omsagent`. 
>
>Aby udzielić uprawnienia do zapisu `omsagent` użytkownika, uruchom następujące polecenia: `setfacl -m u:omsagent:rwx /tmp`
>

## <a name="azure-virtual-machines"></a>Maszyny wirtualne platformy Azure

Azure Automation konfiguracja stanu umożliwia łatwe dołączanie maszyn wirtualnych platformy Azure w celu zarządzania konfiguracją przy użyciu Azure Portal, szablonów Azure Resource Manager lub programu PowerShell. W obszarze okapu i bez administratora, który ma zdalny dostęp do maszyny wirtualnej, rozszerzenie konfiguracji żądanego stanu maszyny wirtualnej platformy Azure rejestruje maszynę wirtualną z konfiguracją stanu Azure Automation.
Ponieważ rozszerzenie konfiguracji żądanego stanu maszyny wirtualnej platformy Azure jest uruchamiane asynchronicznie, należy wykonać kroki w celu śledzenia postępu lub rozwiązywania problemów, które znajdują się w sekcji [**Rozwiązywanie problemów z dołączaniem do maszyny wirtualnej platformy Azure**](#troubleshooting-azure-virtual-machine-onboarding) .

### <a name="azure-portal"></a>Portalu Azure

W [Azure Portal](https://portal.azure.com/)przejdź do konta Azure Automation, do którego chcesz dołączyć maszyny wirtualne. Na stronie Konfiguracja stanu i na karcie **węzły** kliknij pozycję **+ Dodaj**.

Wybierz maszynę wirtualną platformy Azure do dołączenia.

Jeśli na komputerze nie zainstalowano rozszerzenia żądanego stanu programu PowerShell, a stan zasilacza jest uruchomiony, kliknij przycisk **Połącz**.

W obszarze **rejestracja**wprowadź [wartości lokalnych Configuration Manager programu PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig) , które są wymagane dla przypadku użycia, i opcjonalnie konfigurację węzła do przypisania do maszyny wirtualnej.

![dołączania](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Usługi Azure Virtual Machines można wdrażać i dołączać do konfiguracji stanu Azure Automation za pośrednictwem Azure Resource Manager szablonów. Zobacz temat [serwer zarządzany przez usługę konfiguracji żądanego stanu](https://azure.microsoft.com/resources/templates/101-automation-configuration/) , aby zapoznać się z przykładowym szablonem służącym do dołączania istniejącej maszyny wirtualnej w celu Azure Automation konfiguracji stanu.
Jeśli zarządzasz zestawem skalowania maszyn wirtualnych, zapoznaj się z przykładową [konfiguracją zestawu skalowania maszyn wirtualnych za pomocą Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>Program PowerShell

Polecenie cmdlet [register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) może służyć do dołączania maszyn wirtualnych na platformie Azure przy użyciu programu PowerShell.
Jednak jest to obecnie tylko zaimplementowane dla maszyn z systemem Windows (polecenie cmdlet wyzwala tylko rozszerzenie systemu Windows).

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Rejestrowanie maszyn wirtualnych w ramach subskrypcji platformy Azure

Najlepszym sposobem na zarejestrowanie maszyn wirtualnych z innych subskrypcji platformy Azure jest użycie rozszerzenia DSC w szablonie wdrażania Azure Resource Manager.
Przykłady są dostępne w [rozszerzeniu konfiguracji żądanego stanu z szablonami Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).
Aby znaleźć klucz rejestracji i adres URL rejestracji do użycia jako parametry w szablonie, zobacz następującą sekcję [**bezpieczna Rejestracja**](#secure-registration) .

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances"></a>Fizyczne/wirtualne maszyny z systemem Windows w środowisku lokalnym lub w chmurze innej niż Azure (w tym wystąpienia AWS EC2)

Serwery z systemem Windows działające lokalnie lub w innych środowiskach w chmurze można również dołączać do konfiguracji stanu Azure Automation, o ile mają [dostęp wychodzący do platformy Azure](automation-dsc-overview.md#network-planning):

1. Upewnij się, że na maszynach, które mają zostać dołączone do Azure Automation konfiguracji stanu, zainstalowano najnowszą wersję programu [WMF 5](https://aka.ms/wmf5latest) .
1. Postępuj zgodnie z instrukcjami w poniższej sekcji [**generowanie konfiguracji NADMIAROWOŚCI DSC**](#generating-dsc-metaconfigurations) w celu wygenerowania folderu zawierającego wymaganą konfigurację konfiguracji DSC.
1. Zdalnie Zastosuj konfigurację konfiguracji DSC programu PowerShell do maszyn, które chcesz dołączyć. **Na komputerze, z którego jest uruchamiane to polecenie, musi być zainstalowana najnowsza wersja programu [WMF 5](https://aka.ms/wmf5latest)** :

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Jeśli nie można zastosować zdalnego konfigurowania konfiguracji DSC programu PowerShell, skopiuj folder konfiguracji z kroku 2 do każdego komputera do dołączenia. Następnie Wywołaj polecenie **Set-DscLocalConfigurationManager** lokalnie na każdym komputerze, aby dołączyć.
1. Za pomocą Azure Portal lub poleceń cmdlet Sprawdź, czy maszyny do dołączenia są wyświetlane jako węzły konfiguracji stanu zarejestrowane na koncie usługi Azure Automation.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Fizyczne/wirtualne maszyny z systemem Linux w środowisku lokalnym lub w chmurze innej niż Azure

Serwery z systemem Linux działające lokalnie lub w innych środowiskach w chmurze można również dołączać do konfiguracji stanu Azure Automation, o ile mają [dostęp wychodzący do platformy Azure](automation-dsc-overview.md#network-planning):

1. Upewnij się, że Najnowsza wersja [konfiguracji żądanego stanu programu PowerShell dla systemu Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) jest zainstalowana na komputerach, które chcesz dołączyć do Azure Automation konfiguracji stanu.
2. Jeśli [lokalne Configuration Manager konfiguracji DSC programu PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig4) są zgodne z przypadkiem użycia i chcesz dołączyć do nich maszyny, takie jak **zarówno** ściągają, jak i raportują Azure Automationą konfigurację stanu:

   - Na każdym komputerze z systemem Linux, aby dołączać Azure Automation konfigurację stanu, należy użyć `Register.py` do dołączenia przy użyciu lokalnych Configuration Manager ustawień DSC programu PowerShell:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Aby znaleźć klucz rejestracji i adres URL rejestracji dla konta usługi Automation, zobacz następującą sekcję [**bezpieczna Rejestracja**](#secure-registration) .

     Jeśli lokalne Configuration Manager konfiguracji DSC środowiska PowerShell **nie są zgodne z** przypadkiem użycia lub chcesz dołączyć maszyny, aby były one tylko zgłaszane do Azure Automation konfiguracji stanu, wykonaj kroki 3-6. W przeciwnym razie przejdź bezpośrednio do kroku 6.

3. Postępuj zgodnie z instrukcjami w poniższej sekcji [**generowanie konfiguracyjnych konfiguracji DSC**](#generating-dsc-metaconfigurations) w celu wygenerowania folderu zawierającego wymaganą konfigurację konfiguracji DSC.
4. Zdalnie Zastosuj konfigurację konfiguracji DSC programu PowerShell do maszyn, które chcesz dołączyć:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Na komputerze, z którego jest uruchamiane to polecenie, musi być zainstalowana najnowsza wersja programu [WMF 5](https://aka.ms/wmf5latest) .

1. Jeśli nie można zastosować zdalnego konfigurowania konfiguracji DSC programu PowerShell, skopiuj konfigurację z folderu w kroku 5 na maszynę z systemem Linux. Następnie Wywołaj `SetDscLocalConfigurationManager.py` lokalnie na każdej maszynie z systemem Linux, którą chcesz dołączyć do Azure Automation konfiguracji stanu:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Za pomocą Azure Portal lub poleceń cmdlet Sprawdź, czy maszyny do dołączenia są teraz wyświetlane jako węzły DSC zarejestrowane na koncie usługi Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Generowanie konfiguracji DSC

Aby określić ogólny sposób dołączania maszyn do Azure Automation konfiguracji stanu, można wygenerować konfigurację wciąganą [DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) , która nakazuje AGENTowi DSC pobranie z i/lub raport do Azure Automation konfiguracji stanu. Konfiguracje konfiguracji DSC konfiguracji stanu Azure Automation można generować przy użyciu konfiguracji DSC programu PowerShell lub poleceń cmdlet programu PowerShell Azure Automation.

> [!NOTE]
> Konfiguracje konfiguracji DSC zawierają wpisy tajne, które są konieczne do dołączenia komputera do konta usługi Automation na potrzeby zarządzania. Upewnij się, że wszystkie utworzone konfiguracje konfiguracji DSC są prawidłowo chronione, lub usuń je po użyciu.

### <a name="using-a-dsc-configuration"></a>Korzystanie z konfiguracji DSC

1. Otwórz programu vscode (lub ulubiony Edytor) jako administrator na komputerze w środowisku lokalnym. Na komputerze musi być zainstalowana najnowsza wersja programu [WMF 5](https://aka.ms/wmf5latest) .
1. Skopiuj następujący skrypt lokalnie. Ten skrypt zawiera konfigurację DSC programu PowerShell służącą do tworzenia konfiguracji, a także polecenie do uruchamiania tworzenia konfiguracji.

> [!NOTE]
> Nazwy konfiguracji węzła konfiguracji stanu uwzględniają wielkość liter w portalu. Jeśli wielkość liter jest niezgodna, węzeł nie zostanie wyświetlony na karcie **węzły** .

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

1. Wprowadź klucz rejestracji i adres URL dla konta usługi Automation, a także nazwy maszyn do dołączenia. Wszystkie inne parametry są opcjonalne. Aby znaleźć klucz rejestracji i adres URL rejestracji dla konta usługi Automation, zobacz następującą sekcję [**bezpieczna Rejestracja**](#secure-registration) .
1. Jeśli chcesz, aby maszyny raportują informacje o stanie DSC do Azure Automation konfiguracji stanu, ale nie pobieraj konfiguracji ani modułów programu PowerShell, ustaw dla parametru **ReportOnly** wartość true.
1. Uruchom skrypt. Teraz powinien znajdować się folder o nazwie **DscMetaConfigs** w katalogu roboczym zawierający konfiguracje konfiguracji DSC programu PowerShell dla maszyn, które mają zostać dołączone (jako administrator):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Korzystanie z Azure Automation poleceń cmdlet

Jeśli lokalne Configuration Manager konfiguracji DSC programu PowerShell są zgodne z przypadkiem użycia i chcesz dołączyć do nich maszyny, takie jak ściąganie i raportowanie do Azure Automation konfiguracja stanu, polecenia cmdlet Azure Automation zapewniają uproszczoną metodę generowania wymagana konfiguracja konfiguracji DSC:

1. Otwórz konsolę programu PowerShell lub programu vscode jako administrator na komputerze w środowisku lokalnym.
2. Nawiązywanie połączenia z Azure Resource Manager przy użyciu `Connect-AzAccount`
3. Pobierz konfiguracje konfiguracji DSC programu PowerShell dla maszyn, które chcesz dołączyć, z konta usługi Automation, do którego chcesz dołączyć węzły:

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Teraz powinien znajdować się folder o nazwie ***DscMetaConfigs***, zawierający konfiguracje konfiguracji DSC programu PowerShell dla maszyn, które mają zostać dołączone (jako administrator):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Bezpieczna Rejestracja

Maszyny mogą bezpiecznie dołączać do konta Azure Automation przy użyciu protokołu rejestracji konfiguracji DSC WMF 5, który umożliwia węzłowi DSC uwierzytelnianie na serwerze ściągania lub raportowania DSC programu PowerShell (łącznie z konfiguracją stanu Azure Automation). Węzeł rejestruje na serwerze w **adresie URL rejestracji**, uwierzytelniając przy użyciu **klucza rejestracji**. Podczas rejestracji węzeł DSC oraz serwer ściągania/raportowania DSC negocjują unikatowy certyfikat używany do uwierzytelniania na serwerze po rejestracji. Ten proces uniemożliwia dołączenie węzłów od siebie nawzajem, na przykład w przypadku naruszenia bezpieczeństwa i niebezpiecznego węzła. Po rejestracji klucz rejestracji nie jest używany do uwierzytelniania ponownie i jest usuwany z węzła.

Informacje wymagane do protokołu rejestracji konfiguracji stanu można uzyskać z **kluczy** w obszarze **ustawienia konta** w Azure Portal. Otwórz ten blok, klikając ikonę klucza w panelu **podstawy** dla konta usługi Automation.

![Klucze i adres URL usługi Azure Automation](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Adres URL rejestracji to pole adresu URL w bloku Zarządzaj kluczami.
- Klucz rejestracji to podstawowy klucz dostępu lub pomocniczy klucz dostępu w bloku Zarządzaj kluczami. Można użyć dowolnego klucza.

Aby zwiększyć bezpieczeństwo, należy ponownie wygenerować podstawowe i pomocnicze klucze dostępu konta usługi Automation (na stronie **Zarządzanie kluczami** ), aby uniemożliwić rejestracje w przyszłości przy użyciu poprzednich kluczy.

## <a name="certificate-expiration-and-re-registration"></a>Wygaśnięcie i ponowne rejestrowanie certyfikatu

Po zarejestrowaniu maszyny jako węzła DSC w konfiguracji stanu Azure Automation istnieje kilka powodów, dla których może być konieczne ponowne zarejestrowanie tego węzła w przyszłości:

- W przypadku wersji systemu Windows Server starszych niż Windows Server 2019 każdy węzeł automatycznie negocjuje unikatowy certyfikat na potrzeby uwierzytelniania, który wygaśnie po upływie jednego roku. Obecnie protokół rejestracji DSC programu PowerShell nie może automatycznie odnawiać certyfikatów, gdy zbliżają się do wygaśnięcia, więc należy ponownie zarejestrować węzły po upływie roku. Przed ponownym zarejestrowaniem upewnij się, że w każdym węźle jest uruchomiony system Windows Management Framework 5,0 RTM. Jeśli certyfikat uwierzytelniania węzła wygaśnie i węzeł nie zostanie ponownie zarejestrowany, węzeł nie będzie mógł komunikować się z Azure Automation i jest oznaczony jako "nie odpowiada". Ponowne zarejestrowanie wykonane przez 90 dni lub mniej od czasu wygaśnięcia certyfikatu lub w dowolnym momencie po upływie czasu wygaśnięcia certyfikatu spowoduje wygenerowanie i użycie nowego certyfikatu.  Rozwiązanie tego problemu jest zawarte w systemie Windows Server 2019 lub nowszym.
- Aby zmienić [wartości wszystkich lokalnych Configuration Manager środowiska PowerShell](/powershell/scripting/dsc/managing-nodes/metaConfig4) , które zostały ustawione podczas początkowej rejestracji węzła, na przykład ConfigurationMode. Obecnie te wartości agenta DSC można zmienić tylko przez ponowną rejestrację. Jedynym wyjątkiem jest konfiguracja węzła przypisana do węzła — można ją zmienić w Azure Automation DSC bezpośrednio.

Ponowna rejestracja może być wykonywana w taki sam sposób, jak początkowo zarejestrowano węzeł przy użyciu dowolnej metody dołączania opisanej w tym dokumencie. Nie musisz wyrejestrować węzła z konfiguracji stanu Azure Automation przed jego ponownym zarejestrowaniem.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Rozwiązywanie problemów z dołączaniem do maszyny wirtualnej platformy Azure

Azure Automation konfiguracja stanu umożliwia łatwe dołączanie maszyn wirtualnych z systemem Windows Azure na potrzeby zarządzania konfiguracją. Pod wyciągnięciem, rozszerzenie konfiguracji żądanego stanu maszyny wirtualnej platformy Azure służy do rejestrowania maszyny wirtualnej z konfiguracją stanu Azure Automation. Ponieważ rozszerzenie konfiguracji żądanego stanu maszyny wirtualnej platformy Azure jest uruchamiane asynchronicznie, śledzenie jego postępu i rozwiązywanie problemów może być ważne.

> [!NOTE]
> Dowolna metoda dołączania maszyny wirtualnej platformy Azure z systemem Windows w celu Azure Automation konfiguracji stanu korzystającej z rozszerzenia konfiguracji żądanego stanu maszyny wirtualnej platformy Azure może potrwać do godziny, aż węzeł zostanie wyświetlony w Azure Automation. Jest to spowodowane instalacją systemu Windows Management Framework 5,0 na maszynie wirtualnej przez rozszerzenie DSC maszyny wirtualnej platformy Azure, które jest wymagane do dołączenia maszyny wirtualnej do konfiguracji stanu Azure Automation.

Aby rozwiązać problem lub wyświetlić stan rozszerzenia konfiguracji żądanego stanu maszyny wirtualnej platformy Azure, w Azure Portal przejdź do dołączanej maszyny wirtualnej, a następnie kliknij pozycję **rozszerzenia** w obszarze **Ustawienia**. Następnie kliknij pozycję **DSC** lub **DSCForLinux** w zależności od używanego systemu operacyjnego. Aby uzyskać więcej informacji, kliknij pozycję **Wyświetl szczegółowy stan**.

Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz [Rozwiązywanie problemów przy użyciu konfiguracji żądanego stanu Azure Automation (DSC)](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz [wprowadzenie do konfiguracji stanu Azure Automation](automation-dsc-getting-started.md)
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu Azure Automation](automation-dsc-compile.md)
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz temat [polecenia cmdlet konfiguracji stanu Azure Automation](/powershell/module/az.automation#automation)
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [wdrażanie ciągłe przy użyciu konfiguracji stanu Azure Automation i czekolady](automation-dsc-cd-chocolatey.md)
