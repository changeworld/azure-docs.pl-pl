---
title: Skonfiguruj odzyskiwanie po awarii na platformie Azure dla maszyn wirtualnych funkcji Hyper-V przy użyciu programu PowerShell i Azure Resource Manager | Microsoft Docs
description: Automatyzuj odzyskiwanie po awarii maszyn wirtualnych funkcji Hyper-V na platformie Azure za pomocą usługi Azure Site Recovery przy użyciu programu PowerShell i Azure Resource Manager.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 06/18/2019
ms.author: sutalasi
ms.openlocfilehash: 1779a33e4ac021c1807ce10dc224e0b8c8c53ebb
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200534"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych funkcji Hyper-V przy użyciu programu PowerShell i Azure Resource Manager

[Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii ciągłości działania i odzyskiwania po awarii (BCDR) przez organizowanie replikacji, trybu failover i odzyskiwania maszyn wirtualnych platformy Azure oraz lokalnych maszyn wirtualnych i serwerów fizycznych.

W tym artykule opisano sposób korzystania z programu Windows PowerShell wraz z Azure Resource Manager, aby replikować maszyny wirtualne funkcji Hyper-V do platformy Azure. W przykładzie użytym w tym artykule przedstawiono sposób replikowania pojedynczej maszyny wirtualnej uruchomionej na hoście funkcji Hyper-V do platformy Azure.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell udostępnia polecenia cmdlet do zarządzania platformą Azure przy użyciu programu Windows PowerShell. Site Recovery polecenia cmdlet programu PowerShell, dostępne w Azure PowerShell dla Azure Resource Manager, pomagają chronić i odzyskiwać serwery na platformie Azure.

Nie musisz być ekspertem programu PowerShell, aby korzystać z tego artykułu, ale musisz zrozumieć podstawowe pojęcia, takie jak moduły, polecenia cmdlet i sesje. Przeczytaj [temat Wprowadzenie do programu Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)i [używanie Azure PowerShell z Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Partnerzy firmy Microsoft w programie w programie Cloud Solution Provider (CSP) mogą konfigurować i zarządzać ochroną serwerów klientów do odpowiednich subskrypcji programu CSP (subskrypcje dzierżawców).
>
>

## <a name="before-you-start"></a>Przed rozpoczęciem
Upewnij się, że są spełnione następujące wymagania wstępne:

* Konto [Microsoft Azure](https://azure.microsoft.com/) . Możesz rozpocząć od [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/). Ponadto możesz przeczytać o [cenniku programu Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell. Aby uzyskać informacje o tej wersji i sposobie jej instalowania, zobacz [install Azure PowerShell](/powershell/azure/install-az-ps).

Ponadto w konkretnym przykładzie opisanym w tym artykule są spełnione następujące wymagania wstępne:

* Host funkcji Hyper-V z systemem Windows Server 2012 R2 lub Microsoft Hyper-V Server 2012 R2 zawierający co najmniej jedną maszynę wirtualną. Serwery funkcji Hyper-V powinny być połączone z Internetem bezpośrednio lub za pomocą serwera proxy.
* Maszyny wirtualne, które mają być replikowane, powinny być zgodne z [wymaganiami wstępnymi](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Krok 1: Zaloguj się do swojego konta platformy Azure

1. Otwórz konsolę programu PowerShell i Uruchom to polecenie, aby zalogować się do konta platformy Azure. Polecenie cmdlet wyświetla stronę internetową z prośbą o poświadczenia konta: **Connect-AzAccount**.
    - Alternatywnie można uwzględnić poświadczenia konta jako parametr w poleceniu cmdlet **Connect-AzAccount** przy użyciu parametru **-Credential** .
    - Jeśli jesteś partnerem programu CSP działającym w imieniu dzierżawy, określ klienta jako dzierżawcę przy użyciu nazwy domeny głównej dzierżawy tenantID lub. Na przykład: **Connect-AzAccount-dzierżawca "fabrikam.com"**
2. Skojarz subskrypcję, której chcesz używać z kontem, ponieważ konto może mieć kilka subskrypcji:

    `Select-AzSubscription -SubscriptionName $SubscriptionName`

3. Sprawdź, czy subskrypcja jest zarejestrowana, aby korzystać z dostawców platformy Azure dla Recovery Services i Site Recovery przy użyciu następujących poleceń:

    `Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`

4. Sprawdź, czy w danych wyjściowych polecenia **RegistrationState** jest ustawiona na **zarejestrowane**, możesz przejść do kroku 2. Jeśli nie, należy zarejestrować brakującego dostawcę w subskrypcji, uruchamiając następujące polecenia:

    `Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Sprawdź, czy dostawcy zarejestrowali się pomyślnie, używając następujących poleceń:

    `Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`

## <a name="step-2-set-up-the-vault"></a>Krok 2: Konfigurowanie magazynu

1. Utwórz Azure Resource Manager grupę zasobów, w której chcesz utworzyć magazyn, lub Użyj istniejącej grupy zasobów. Utwórz nową grupę zasobów w następujący sposób. Zmienna $ResourceGroupName zawiera nazwę grupy zasobów, którą chcesz utworzyć, a zmienna $Geo zawiera region świadczenia usługi Azure, w którym ma zostać utworzona grupa zasobów (na przykład "Brazylia Południowa").

    `New-AzResourceGroup -Name $ResourceGroupName -Location $Geo`

2. Aby uzyskać listę grup zasobów w subskrypcji, uruchom polecenie cmdlet **Get-AzResourceGroup** .
2. Utwórz nowy magazyn Recovery Services platformy Azure w następujący sposób:

        $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Listę istniejących magazynów można pobrać przy użyciu polecenia cmdlet **Get-AzRecoveryServicesVault** .


## <a name="step-3-set-the-recovery-services-vault-context"></a>Krok 3: Ustawianie kontekstu magazynu Recovery Services

Ustaw kontekst magazynu w następujący sposób:

`Set-AsrVaultSettings -Vault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>Krok 4: Tworzenie lokacji funkcji Hyper-V

1. Utwórz nową lokację funkcji Hyper-V w następujący sposób:

        $sitename = "MySite"                #Specify site friendly name
        New-AsrFabric -Type HyperVSite -Name $sitename

2. To polecenie cmdlet uruchamia zadanie Site Recovery, aby utworzyć lokację, i zwraca obiekt zadania Site Recovery. Poczekaj na zakończenie zadania i sprawdź, czy zadanie zostało ukończone pomyślnie.
3. Użyj **polecenia cmdlet Get-AsrJob**, aby pobrać obiekt zadania i sprawdzić bieżący stan zadania.
4. Wygeneruj i Pobierz klucz rejestracji dla lokacji w następujący sposób:

    ```
    $SiteIdentifier = Get-AsrFabric -Name $sitename | Select -ExpandProperty SiteIdentifier
    $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
    ```

5. Skopiuj pobrany klucz do hosta funkcji Hyper-V. Klucz jest potrzebny do zarejestrowania hosta funkcji Hyper-V w lokacji programu.

## <a name="step-5-install-the-provider-and-agent"></a>Krok 5. Instalowanie dostawcy i agenta

1. Pobierz Instalatora dla najnowszej wersji dostawcy [firmy Microsoft](https://aka.ms/downloaddra).
2. Uruchom Instalatora na hoście funkcji Hyper-V.
3. Po zakończeniu instalacji przejdź do kroku rejestracji.
4. Po wyświetleniu monitu podaj pobrany klucz i wykonaj rejestrację hosta funkcji Hyper-V.
5. Sprawdź, czy host funkcji Hyper-V jest zarejestrowany w lokacji w następujący sposób:

        $server =  Get-AsrFabric -Name $siteName | Get-AsrServicesProvider -FriendlyName $server-friendlyname

Jeśli używasz serwera podstawowego funkcji Hyper-V, Pobierz plik Instalatora i wykonaj następujące kroki:
1. Wyodrębnij pliki z AzureSiteRecoveryProvider. exe do katalogu lokalnego, uruchamiając następujące polecenie:```AzureSiteRecoveryProvider.exe /x:. /q```
2. Wyniki ```.\setupdr.exe /i``` przebiegu są rejestrowane w%ProgramData%\ASRLogs\DRASetupWizard.log.

3. Zarejestruj serwer, uruchamiając następujące polecenie:

    ```cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"```


## <a name="step-6-create-a-replication-policy"></a>Krok 6: Tworzenie zasad replikacji

Przed rozpoczęciem należy zauważyć, że określone konto magazynu powinno znajdować się w tym samym regionie świadczenia usługi Azure co magazyn i powinna mieć włączoną replikację geograficzną.

1. Utwórz zasady replikacji w następujący sposób:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Sprawdź zwrócone zadanie, aby upewnić się, że tworzenie zasad replikacji powiodło się.

3. Pobierz kontener ochrony odnoszący się do lokacji w następujący sposób:

        $protectionContainer = Get-AsrProtectionContainer
3. Skojarz kontener ochrony z zasadami replikacji w następujący sposób:

        $Policy = Get-AsrPolicy -FriendlyName $PolicyName
        $associationJob  = New-AsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
4. Poczekaj na pomyślne ukończenie zadania skojarzenia.

5. Pobierz Mapowanie kontenera ochrony.

        $ProtectionContainerMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $protectionContainer

## <a name="step-7-enable-vm-protection"></a>Krok 7: Włącz ochronę maszyny wirtualnej

1. Pobierz element objęty ochroną, który odnosi się do maszyny wirtualnej, która ma być chroniona, w następujący sposób:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $ProtectableItem = Get-AsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Ochrona maszyny wirtualnej. Jeśli do chronionej maszyny wirtualnej jest dołączony więcej niż jeden dysk, określ dysk systemu operacyjnego za pomocą parametru *OSDiskName* .

        $OSType = "Windows"                                 # "Windows" or "Linux"
        $DRjob = New-AsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID

3. Poczekaj, aż maszyny wirtualne osiągną stan chroniony po replikacji początkowej. Może to chwilę potrwać, w zależności od takich czynników, jak ilość danych, które mają być replikowane, oraz dostępną przepustowość w strumieniu do platformy Azure. W przypadku stanu chronionego stan zadania i StateDescription są aktualizowane w następujący sposób:

        PS C:\> $DRjob = Get-AsrJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Zaktualizuj właściwości odzyskiwania (takie jak rozmiar roli maszyny wirtualnej) i sieć platformy Azure, do której ma zostać dołączona karta sieciowa maszyny wirtualnej po przejściu do trybu failover.

        PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AsrReplicationProtectedItem --InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AsrJob -Job $UpdateJob

        PS C:\> $UpdateJob| select -ExpandProperty state
        Get-AsrJob -Job $job | select -ExpandProperty state

        Succeeded



## <a name="step-8-run-a-test-failover"></a>Krok 8: Wykonywanie próby przejścia w tryb failover
1. Uruchom test pracy w trybie failover w następujący sposób:

        $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        $TFjob =Start-AsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Sprawdź, czy testowa maszyna wirtualna została utworzona na platformie Azure. Zadanie testowej pracy w trybie failover jest zawieszone po utworzeniu testowej maszyny wirtualnej na platformie Azure.
3. Aby wyczyścić i ukończyć test pracy w trybie failover, uruchom polecenie:

        $TFjob = Start-AsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej](https://docs.microsoft.com/powershell/module/az.recoveryservices) na temat Azure Site Recovery za pomocą Azure Resource Manager poleceń cmdlet programu PowerShell.
