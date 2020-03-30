---
title: Odzyskiwanie po awarii maszyny Wirtualnej funkcji Hyper V przy użyciu usługi Azure Site Recovery i programu PowerShell
description: Automatyzacja odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V na platformie Azure za pomocą usługi Azure Site Recovery przy użyciu programów PowerShell i usługi Azure Resource Manager.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: 6499c986bef965848303ee9833fd59f5e3f0889c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257994"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych z programem Hyper-V przy użyciu programów PowerShell i usługi Azure Resource Manager

[Usługa Azure Site Recovery](site-recovery-overview.md) przyczynia się do ciągłości biznesowej i odzyskiwania po awarii (BCDR) strategii przez aranżacji replikacji, pracy awaryjnej i odzyskiwania maszyn wirtualnych platformy Azure (VMs) i lokalnych maszyn wirtualnych i serwerów fizycznych.

W tym artykule opisano sposób używania programu Windows PowerShell wraz z usługą Azure Resource Manager do replikowania maszyn wirtualnych funkcji Hyper-V na platformie Azure. W przykładzie użytym w tym artykule pokazano, jak replikować pojedynczą maszynę wirtualną działającą na hoście funkcji Hyper-V na platformie Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Program Azure PowerShell udostępnia polecenia cmdlet do zarządzania platformą Azure przy użyciu programu Windows PowerShell. Polecenia cmdlet programu PowerShell odzyskiwania witryny, dostępne w usłudze Azure PowerShell for Azure Resource Manager, pomagają chronić i odzyskać serwery na platformie Azure.

Nie musisz być ekspertem programu PowerShell, aby korzystać z tego artykułu, ale musisz zrozumieć podstawowe pojęcia, takie jak moduły, polecenia cmdlet i sesje. Aby uzyskać więcej informacji, zobacz [dokumentację programu PowerShell](/powershell) i [korzystanie z programu Azure PowerShell z usługą Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Partnerzy firmy Microsoft w programie Dostawcy rozwiązań w chmurze (CSP) mogą konfigurować i zarządzać ochroną serwerów klientów do odpowiednich subskrypcji dostawcy usług kryptograficznych (subskrypcje dzierżawy).

## <a name="before-you-start"></a>Przed rozpoczęciem

Upewnij się, że masz następujące wymagania wstępne w miejscu:

- Konto [platformy Microsoft Azure.](https://azure.microsoft.com/) Możesz rozpocząć od [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/). Ponadto możesz przeczytać o [cenach usługi Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell. Aby uzyskać informacje na temat tej wersji i sposobu jej zainstalowania, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps).

Ponadto w konkretnym przykładzie opisanym w tym artykule przedstawiono następujące wymagania wstępne:

- Host funkcji Hyper-V z systemem Windows Server 2012 R2 lub Microsoft Hyper-V Server 2012 R2 zawierający co najmniej jedną maszynę wirtualną. Serwery funkcji Hyper-V powinny być połączone z Internetem bezpośrednio lub za pośrednictwem serwera proxy.
- Maszyny wirtualne, które chcesz replikować, powinny być zgodne z [tymi wymaganiami wstępnymi](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Krok 1: Zaloguj się do konta platformy Azure

1. Otwórz konsolę programu PowerShell i uruchom to polecenie, aby zalogować się do konta platformy Azure. Polecenie cmdlet wyświetla stronę internetową z monitem o `Connect-AzAccount`podanie danych logowania do konta: .
   - Alternatywnie można dołączyć poświadczenia konta jako parametr `Connect-AzAccount` w połączu cmdlet, przy użyciu **polecenia poświadczeń** parametru.
   - Jeśli jesteś partnerem CSP pracującym w imieniu dzierżawcy, określ klienta jako dzierżawcę, używając ich identyfikatora dzierżawcy lub nazwy domeny podstawowej dzierżawcy. Na przykład: `Connect-AzAccount -Tenant "fabrikam.com"`
1. Skojarz subskrypcję, której chcesz użyć z kontem, ponieważ konto może mieć kilka subskrypcji:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Sprawdź, czy subskrypcja jest zarejestrowana do korzystania z dostawców platformy Azure dla usług odzyskiwania i odzyskiwania witryny, za pomocą tych poleceń:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Sprawdź, czy w danych wyjściowych polecenia, **RegistrationState** jest **ustawiona**na Zarejestrowany , można przejść do kroku 2. Jeśli nie, należy zarejestrować brakującego dostawcy w subskrypcji, uruchamiając następujące polecenia:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Sprawdź, czy dostawcy zarejestrowali się pomyślnie, używając następujących poleceń:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>Krok 2: Konfigurowanie przechowalni

1. Utwórz grupę zasobów usługi Azure Resource Manager, w której można utworzyć przechowalnię lub użyć istniejącej grupy zasobów. Utwórz nową grupę zasobów w następujący sposób. Zmienna `$ResourceGroupName` zawiera nazwę grupy zasobów, którą chcesz utworzyć, a zmienna $Geo zawiera region platformy Azure, w którym ma zostać utworzona grupa zasobów (na przykład "Brazylia Południowa").

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Aby uzyskać listę grup zasobów w ramach `Get-AzResourceGroup` subskrypcji, uruchom polecenie cmdlet.
1. Utwórz nowy magazyn usług Azure Recovery Services w następujący sposób:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

Listę istniejących przechowalni można `Get-AzRecoveryServicesVault` pobrać za pomocą polecenia cmdlet.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Krok 3: Ustawianie kontekstu magazynu usług odzyskiwania

Ustaw kontekst przechowalni w następujący sposób:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>Krok 4: Tworzenie witryny funkcji Hyper-V

1. Utwórz nową witrynę funkcji Hyper-V w następujący sposób:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. To polecenie cmdlet uruchamia zadanie odzyskiwania witryny w celu utworzenia witryny i zwraca obiekt zadania odzyskiwania witryny. Poczekaj na zakończenie zadania i sprawdź, czy zadanie zostało ukończone pomyślnie.
1. Użyj `Get-AzRecoveryServicesAsrJob` polecenia cmdlet, aby pobrać obiekt zadania i sprawdzić bieżący stan zadania.
1. Generowanie i pobieranie klucza rejestracyjnego dla witryny w następujący sposób:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Skopiuj pobrany klucz do hosta funkcji Hyper-V. Potrzebny jest klucz do zarejestrowania hosta funkcji Hyper-V w witrynie.

## <a name="step-5-install-the-provider-and-agent"></a>Krok 5: Zainstaluj dostawcę i agenta

1. Pobierz instalator najnowszej wersji dostawcy z [firmy Microsoft](https://aka.ms/downloaddra).
1. Uruchom instalator na hoście funkcji Hyper-V.
1. Po zakończeniu instalacji przejdź do kroku rejestracji.
1. Po wyświetleniu monitu podaj pobrany klucz i zakończ pełną rejestrację hosta funkcji Hyper-V.
1. Sprawdź, czy host funkcji Hyper-V jest zarejestrowany w witrynie w następujący sposób:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Jeśli korzystasz z serwera rdzenia funkcji Hyper-V, pobierz plik instalacyjny i wykonaj następujące czynności:

1. Wyodrębnij pliki z _usługi AzureSiteRecoveryProvider.exe_ do katalogu lokalnego, uruchamiając to polecenie:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Uruchom następujące polecenie:

   ```console
   .\setupdr.exe /i
   ```

   Wyniki są rejestrowane w _pliku %ProgramData%\ASRLogs\DRASetupWizard.log_.

1. Zarejestruj serwer, uruchamiając to polecenie:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>Krok 6: Tworzenie zasad replikacji

Przed rozpoczęciem określone konto magazynu powinno znajdować się w tym samym regionie platformy Azure co magazyn i powinno mieć włączoną replikację geograficzną.

1. Tworzenie zasad replikacji w następujący sposób:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. Sprawdź zwrócone zadanie, aby upewnić się, że tworzenie zasad replikacji zakończy się pomyślnie.

1. Pobierz kontener ochrony, który odpowiada witrynie, w następujący sposób:

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. Skojarz kontener ochrony z zasadami replikacji w następujący sposób:

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. Poczekaj na pomyślne wykonanie zadania skojarzenia.

1. Pobierz mapowanie kontenera ochrony.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>Krok 7: Włącz ochronę maszyn wirtualnych

1. Pobierz element chroniony, który odpowiada maszynie wirtualnej, którą chcesz chronić, w następujący sposób:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. Chroń maszynę wirtualną. Jeśli chroniona maszyna wirtualna ma do niej dołączony więcej niż jeden dysk, określ dysk systemu operacyjnego przy użyciu parametru **OSDiskName.**

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Poczekaj, aż maszyny wirtualne osiągną stan chroniony po replikacji początkowej. Może to trochę potrwać, w zależności od czynników, takich jak ilość danych do replikacji i dostępnej przepustowości nadrzędnej na platformie Azure. Gdy stan chroniony jest w miejscu, stan zadania i StateDescription są aktualizowane w następujący sposób:

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. Aktualizuj właściwości odzyskiwania (takie jak rozmiar roli maszyny Wirtualnej) i sieci platformy Azure, do której należy dołączyć kartę sieciową maszyny Wirtualnej po przełączeniu w stan failover.

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> Jeśli chcesz replikować do dysków zarządzanych z włączoną funkcją CMK na platformie Azure, wykonaj następujące kroki przy użyciu programu Az PowerShell 3.3.0:
>
> 1. Włączanie trybu failover na dyskach zarządzanych przez aktualizowanie właściwości maszyny Wirtualnej
> 1. Użyj `Get-AzRecoveryServicesAsrReplicationProtectedItem` polecenia cmdlet, aby pobrać identyfikator dysku dla każdego dysku chronionego elementu
> 1. Utwórz obiekt słownika przy użyciu `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` polecenia cmdlet, aby zawierał mapowanie identyfikatora dysku do zestawu szyfrowania dysku. Te zestawy szyfrowania dysku mają być wstępnie utworzone przez Ciebie w regionie docelowym.
> 1. Zaktualizuj właściwości `Set-AzRecoveryServicesAsrReplicationProtectedItem` maszyny Wirtualnej przy użyciu polecenia cmdlet, przekazując obiekt słownika w parametrze **DiskIdToDiskEncryptionSetMap.**

## <a name="step-8-run-a-test-failover"></a>Krok 8: Uruchamianie testu po awarii

1. Uruchom test pracy awaryjnej w następujący sposób:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Sprawdź, czy testowa maszyna wirtualna jest tworzona na platformie Azure. Zadanie pracy awaryjnej testu jest zawieszone po utworzeniu testowej maszyny Wirtualnej na platformie Azure.
1. Aby wyczyścić i zakończyć test pracy awaryjnej, uruchom:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](/powershell/module/az.recoveryservices) o usłudze Azure Site Recovery za pomocą poleceń cmdlet usługi Azure Resource Manager programu PowerShell.
