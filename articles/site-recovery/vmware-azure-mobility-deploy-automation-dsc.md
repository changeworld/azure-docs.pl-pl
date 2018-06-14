---
title: Wdróż usługę mobilności odzyskiwania lokacji za pomocą usługi Konfiguracja DSC automatyzacji Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób korzystania z usługi Konfiguracja DSC automatyzacji Azure do automatycznego wdrożenia usługi Azure lokacji odzyskiwania mobilności i agenta platformy Azure dla maszyny Wirtualnej VMware i replikacji serwera fizycznego na platformie Azure
services: site-recovery
author: krnese
manager: lorenr
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: krnese
ms.openlocfilehash: 0bf1b551ba578cd152201c131bd60470bdc9d86a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
ms.locfileid: "29812852"
---
# <a name="deploy-the-mobility-service-with-azure-automation-dsc"></a>Wdrażanie usługi mobilności przy użyciu usługi Azure Automation DSC

Wdrażanie usługi mobilności na maszynach wirtualnych VMware i serwerów fizycznych, które chcesz replikować do platformy Azure, przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).

W tym artykule opisano sposób wdrażania usługi na komputerach z systemem Windows, przy użyciu żądanego stanu konfiguracji (Konfiguracja DSC automatyzacji Azure), aby upewnić się, że:


## <a name="prerequisites"></a>Wymagania wstępne

* Repozytorium do przechowywania wymagane ustawienia
* Repozytorium do przechowywania wymagane hasło można zarejestrować na serwerze zarządzania. Unikatowe hasło jest generowane dla określonej konfiguracji serwera. 
* Windows Management Framework (WMF) 5.0, należy zainstalować na komputerach, które chcesz włączyć ochronę. Jest to wymagane dla usługi Konfiguracja DSC automatyzacji.
Jeśli chcesz użyć maszyny DSC dla systemu Windows, które mają zainstalowaną WMF 4.0, zobacz [Użyj DSC w środowiskach rozłączonych](## Use DSC in disconnected environments).
 

## <a name="extract-binaries"></a>Wyodrębnij pliki binarne

Usługa mobilności można zainstalować za pomocą wiersza polecenia i przyjmuje wiele argumentów. Należy pobrać pliki binarne (po wyodrębnianie je z ustawień) i przechowywać je w miejscu, w którym można pobrać je za pomocą konfiguracji DSC.

1. Aby wyodrębnić pliki wymagane dla tej instalacji, przejdź do następującego katalogu na serwerze zarządzania: **\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**
2. W tym folderze, sprawdź, czy jest plik MSI o nazwie: **Microsoft ASR_UA_version_Windows_GA_date_Release.exe**.
3. Użyj następującego polecenia, aby wyodrębnić Instalator: **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**
2. Zaznacz wszystkie pliki i wysyłać je do skompresowanego folderu (zip).

Masz teraz pliki binarne, które są potrzebne do automatyzowania instalacji usługi mobilności przy użyciu usługi Konfiguracja DSC automatyzacji.

## <a name="store-the-passphrase"></a>Hasło jest przechowywane

Należy określić, gdzie chcesz umieścić tego folderu zip. Służy konto magazynu platformy Azure, jak pokazano później, aby przechowywać hasło, które należy do instalacji. Agent zarejestruje się następnie z serwerem zarządzania w ramach procesu.

1. Zapisz hasło, które uzyskano podczas wdrażania serwera Konfiguracja w pliku tekstowym - passphrase.txt.
2. Umieść folderze zip i hasło w dedykowanym kontenera na koncie magazynu Azure.


Jeśli chcesz zachować te pliki w udziale w sieci, możesz to zrobić. Należy zapewnić ma dostęp do zasobów DSC, która będzie używana później i można uzyskać ustawień i hasło.

## <a name="create-the-dsc-configuration"></a>Tworzenie konfiguracji DSC

Instalator zależy od WMF 5.0. Maszyny można zastosować konfiguracji za pomocą usługi Konfiguracja DSC automatyzacji WMF 5.0 musi być obecny.

Poniższa przykładowa konfiguracja DSC korzysta z środowiska:

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
Konfiguracja spowoduje wykonanie następujących czynności:

* Zmienne poinformuje konfiguracji do uzyskania pliki binarne usługi mobilności i agenta maszyny Wirtualnej platformy Azure, skąd uzyskać hasło i miejsca do przechowywania danych wyjściowych.
* Konfiguracji zaimportuje zasobów xPSDesiredStateConfiguration DSC, dzięki czemu można użyć `xRemoteFile` do pobierania plików z repozytorium.
* Konfiguracja utworzy katalog, w którym chcesz przechowywać pliki binarne.
* Zasób archiwum zostanie Wyodrębnij pliki z folderu zip.
* Pakiet zasobów Install instaluje usługi mobilności z UNIFIEDAGENT. Wywołanie pliku EXE Instalatora z określonych argumentów. (Zmiennych, które skonstruować argumenty muszą zostać zmienione aby odzwierciedlić charakter lokalnego środowiska.)
* Pakiet zasobów AzureAgent instaluje agenta maszyny Wirtualnej Azure, co jest zalecane na każdej maszynie Wirtualnej, która działa na platformie Azure. Agent maszyny Wirtualnej Azure również umożliwia dodawanie rozszerzenia do maszyny Wirtualnej po pracy awaryjnej.
* Zapewnia usługi zasobów lub zasobów zawsze uruchomiona powiązane usługi mobilności i usług Azure.

Zapisz konfigurację jako **ASRMobilityService**.

> [!NOTE]
> Pamiętaj, aby zastąpić CSIP w konfiguracji z uwzględnieniem serwera rzeczywiste zarządzanie, dzięki czemu agent będzie poprawnie połączony i użyje poprawne hasło.
>
>

## <a name="upload-to-automation-dsc"></a>Przekaż do Automation DSC
Ponieważ konfiguracji DSC wprowadzone zaimportuje wymagane moduł zasobów DSC (xPSDesiredStateConfiguration), należy zaimportować ten moduł w automatyzacji przed przekazaniem konfiguracji DSC.

1. Zaloguj się do swojego konta automatyzacji, przejdź do **zasoby** > **modułów**i kliknij przycisk **galerii przeglądania**.
2. Wyszukaj modułu, a następnie zaimportuj go do Twojego konta.
3. Gdy skończysz, przejdź do komputera, gdzie masz zainstalowane moduły usługi Azure Resource Manager i przejdź do importowania konfiguracji DSC nowo utworzony.

## <a name="import-cmdlets"></a>Poleceń cmdlet importu

1. W programie PowerShell Zaloguj się do subskrypcji platformy Azure.
2. Modyfikowanie poleceń cmdlet, aby odzwierciedlić charakter lokalnego środowiska i przechwycić informacje o koncie automatyzacji w zmiennej:

    ```powershell
    $AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
    ```

3. Przekazać konfiguracji do usługi Konfiguracja DSC automatyzacji za pomocą następującego polecenia cmdlet:

    ```powershell
    $ImportArgs = @{
        SourcePath = 'C:\ASR\ASRMobilityService.ps1'
        Published = $true
        Description = 'DSC Config for Mobility Service'
    }
    $AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
    ```

## <a name="compile-the-configuration-in-automation-dsc"></a>Kompiluj z konfiguracją w usługi Konfiguracja DSC automatyzacji

1. Kompilacji konfiguracji w DSC usługi Automatyzacja, aby można było zarejestrować węzłów do niego. Możesz osiągnąć, uruchamiając następujące polecenie cmdlet:

    ```    powershell
    $AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
    ```

Może to zająć kilka minut, ponieważ jest zasadniczo wdrażany konfiguracji do usługi hostowanej ściągania usługi Konfiguracja DSC.

2. Po skompilowaniu konfiguracji można pobrać informacji o zadania przy użyciu programu PowerShell (Get-AzureRmAutomationDscCompilationJob) lub za pomocą [portalu Azure](https://portal.azure.com/).

    ![Pobieranie zadania](./media/vmware-azure-mobility-deploy-automation-dsc/retrieve-job.png)

Teraz pomyślnie opublikowany i przekazać do usługi Konfiguracja DSC automatyzacji konfiguracji DSC.

## <a name="onboard-machines-to-automation-dsc"></a>Dodaj maszyny do usługi Konfiguracja DSC automatyzacji


1. Upewnij się, że maszynach z systemem Windows są aktualizowane przy użyciu najnowszej wersji programu WMF. Można pobrać i zainstalować odpowiednią wersję dla danej platformy z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=50395).
2. Utwórz metaconfig dla DSC, która zostanie zastosowana do węzłów. Zakończyła się powodzeniem, z tym, należy pobrać adresu URL punktu końcowego i klucz podstawowy dla wybranego konta usługi Automatyzacja na platformie Azure. Te wartości można znaleźć **klucze** na **wszystkie ustawienia** bloku dla konta automatyzacji.

    ![Wartości klucza](./media/vmware-azure-mobility-deploy-automation-dsc/key-values.png)

W tym przykładzie masz serwera fizycznego systemu Windows Server 2012 R2, który chcesz chronić za pomocą usługi Site Recovery.

## <a name="check-for-any-pending-file-rename-operations"></a>Sprawdź, czy wszystkie oczekujące pliku Zmień nazwę operacji

Przed rozpoczęciem skojarzyć serwer z punktem końcowym usługi Konfiguracja DSC automatyzacji, zaleca się sprawdzanie wszelkie oczekujące operacje zmiany nazwy pliku w rejestrze. Mogą one zabrania instalacji zakończył się z powodu oczekującego ponownego rozruchu.

1. Uruchom następujące polecenie cmdlet, aby sprawdzić, czy istnieje nie oczekuje na ponowne uruchomienie na serwerze:

    ```powershell
    Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
    ```
2. Jeśli ten pokazuje puste, to OK, aby kontynuować. Jeśli nie, należy spełnić to przez ponownego uruchomienia serwera w oknie obsługi.
3. Aby zastosować konfigurację na serwerze, uruchom PowerShell Integrated Scripting Environment (ISE) i uruchom następujący skrypt. Jest to zasadniczo lokalnej konfiguracji DSC, która wyśle instrukcje do aparatu lokalny program Configuration Manager do rejestracji w usłudze Konfiguracja DSC automatyzacji i pobrać określonej konfiguracji (ASRMobilityService.localhost).

    ```powershell
    [DSCLocalConfigurationManager()]
    configuration metaconfig {
        param (
            $URL,
            $Key
        )
        node localhost {
            Settings {
                RefreshFrequencyMins = '30'
                RebootNodeIfNeeded = $true
                RefreshMode = 'PULL'
                ActionAfterReboot = 'ContinueConfiguration'
                ConfigurationMode = 'ApplyAndMonitor'
                AllowModuleOverwrite = $true
            }

            ResourceRepositoryWeb AzureAutomationDSC {
                ServerURL = $URL
                RegistrationKey = $Key
            }

            ConfigurationRepositoryWeb AzureAutomationDSC {
                ServerURL = $URL
                RegistrationKey = $Key
                ConfigurationNames = 'ASRMobilityService.localhost'
            }

            ReportServerWeb AzureAutomationDSC {
                ServerURL = $URL
                RegistrationKey = $Key
            }
        }
    }
    metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'
    
    Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
    ```

Ta konfiguracja spowoduje, że aparat lokalny program Configuration Manager do zarejestrowania się w usłudze Konfiguracja DSC automatyzacji. Również określi, jak powinien działać aparat, co jego zrobić, jeśli istnieje kilka konfiguracji (ApplyAndAutoCorrect) i jak go należy kontynuować konfigurowanie Jeśli wymagany jest ponowny rozruch.

1. Po uruchomieniu tego skryptu do rejestracji w usłudze Konfiguracja DSC automatyzacji rozpocząć węzła.

    ![Trwa rejestrowanie węzła](./media/vmware-azure-mobility-deploy-automation-dsc/register-node.png)

2. Jeśli możesz powrócić do portalu Azure, widać, że nowo zarejestrowanych węzła okazało się teraz w portalu.

    ![Zarejestrowany węzła w portalu](./media/vmware-azure-mobility-deploy-automation-dsc/registered-node.png)

3. Na serwerze można uruchomić następujące polecenie cmdlet programu PowerShell, aby sprawdzić, czy węzeł został zarejestrowany poprawnie:

    ```powershell
    Get-DscLocalConfigurationManager
    ```

4. Po ukończeniu ściągnąć i zastosowane do serwera konfiguracji, można to sprawdzić, uruchamiając następujące polecenie cmdlet:

    ```powershell
    Get-DscConfigurationStatus
    ```

Dane wyjściowe zawierają, czy serwer został pomyślnie pobierane konfiguracji:

![Dane wyjściowe](./media/vmware-azure-mobility-deploy-automation-dsc/successful-config.png)

Ponadto instalacji usługi mobilności ma własny dziennik, który znajduje się w temacie *SystemDrive*\ProgramData\ASRSetupLogs.

To wszystko. Teraz pomyślnie wdrożone i zarejestrowane usługi mobilności na maszynie, która ma być chroniony za pomocą usługi Site Recovery. DSC będzie upewnij się, że wymagane usługi są zawsze uruchomiona.

![Pomyślne wdrożenie](./media/vmware-azure-mobility-deploy-automation-dsc/successful-install.png)

Po serwer zarządzania wykrył pomyślne wdrożenie, można skonfigurować ochrony i włączyć replikację na komputerze przy użyciu usługi Site Recovery.

## <a name="use-dsc-in-disconnected-environments"></a>Użyj usługi Konfiguracja DSC w środowiskach rozłączonych

Jeśli komputery nie jest połączony z Internetem, nadal może polegać na DSC wdrażania i konfigurowania usługi mobilności na obciążeń, które chcesz chronić.

Możliwe jest utworzenie własnych serwera ściągania usługi Konfiguracja DSC w danym środowisku, aby zapewnić zasadniczo takie same możliwości, które można uzyskać z usługi Konfiguracja DSC automatyzacji. Oznacza to, że klienci będzie pobierać konfiguracji (po jej zarejestrowaniu) do punktu końcowego usługi Konfiguracja DSC. Inną opcją jest jednak ręcznie wypychana konfiguracji DSC na maszynach, lokalnie lub zdalnie.

Należy pamiętać, że w tym przykładzie dodano parametr dla nazwy komputera. Pliki zdalne teraz znajdują się w udziale zdalnym, który powinien być dostępny przez komputery, które chcesz chronić. Koniec skryptu enacts konfiguracji, a następnie uruchamia się zastosować konfiguracji DSC na komputerze docelowym.

### <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że jest zainstalowany moduł PowerShell xPSDesiredStateConfiguration. Dla komputerów z systemem Windows zainstalowaną WMF 5.0 można zainstalować moduł xPSDesiredStateConfiguration, uruchamiając następujące polecenie cmdlet na komputerach docelowych:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Można również pobrać i zapisać modułu, w razie potrzeby można rozpowszechniać komputerów z systemem Windows, które mają WMF 4.0. Uruchom to polecenie cmdlet na komputerze, w którym występuje PowerShellGet (WMF 5.0):

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Również WMF 4.0, upewnij się, że [Windows 8.1 update KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) jest zainstalowany na komputerach.

Następująca konfiguracja może zostać umieszczony na maszynach z systemem Windows, które mają WMF 5.0 i programu WMF 4.0:

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Jeśli chcesz utworzyć wystąpienia własne serwera ściągania usługi Konfiguracja DSC w sieci firmowej, aby naśladował możliwości, które można pobrać z usługi Konfiguracja DSC automatyzacji, zobacz [ustawienie serwera ściągania usługi Konfiguracja DSC sieci web](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Opcjonalnie: Wdrażanie konfiguracji DSC przy użyciu szablonu usługi Azure Resource Manager
Ten artykuł ma fokus w sposób tworzenia własnych konfiguracji DSC, aby automatycznie wdrożyć usługę mobilności i Agent maszyny Wirtualnej Azure — i upewnij się, że działają na maszynach, które chcesz chronić. Mamy także szablonu usługi Azure Resource Manager, który zostanie wdrożona tej konfiguracji DSC do nowego lub istniejącego konta usługi Automatyzacja Azure. Szablon zostanie umożliwia utworzenie automatyzacji zasoby, zawierające zmienne środowiska parametrów wejściowych.

Po wdrożeniu szablonu to odwołanie do kroku 4, w tym przewodniku, aby dołączyć po prostu maszynach.

Szablon spowoduje wykonanie następujących czynności:

1. Użyj istniejącego konta automatyzacji lub Utwórz nową
2. Przeprowadzanie parametrów wejściowych:
   * ASRRemoteFile — lokalizacji, w którym są przechowywane instalacji usługi mobilności
   * ASRPassphrase — lokalizacji, w którym są przechowywane w pliku passphrase.txt
   * ASRCSEndpoint — adres IP serwera zarządzania
3. Zaimportuj moduł programu PowerShell xPSDesiredStateConfiguration
4. Tworzenie i kompilowania konfiguracji DSC

Powyższych kroków nastąpi w odpowiedniej kolejności, tak aby można było uruchomić dołączania maszynach do ochrony.

Szablon, instrukcje dotyczące wdrażania, znajduje się na [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Wdrażanie szablonu przy użyciu programu PowerShell:

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Kolejne kroki
Po wdrożeniu agentów usługa mobilności można [włączyć replikację](vmware-azure-tutorial.md) dla maszyn wirtualnych.
