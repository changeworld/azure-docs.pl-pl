---
title: Konfigurowanie odzyskiwania po awarii funkcji Hyper-V (z programem VMM) w lokacji dodatkowej za pomocą usługi Azure Site Recovery/PowerShell
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM w dodatkowej lokacji programu VMM przy użyciu usługi Azure Site Recovery i programu PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: deef7bfdbc28d744cb81da59d3ffc13a1abee54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048616"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Konfigurowanie odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V w lokacji dodatkowej przy użyciu programu PowerShell (Menedżer zasobów)

W tym artykule pokazano, jak zautomatyzować kroki replikacji maszyn wirtualnych funkcji Hyper-V w chmurach Menedżera maszyn wirtualnych w centrum systemu do chmury menedżera maszyn wirtualnych w dodatkowej lokacji lokalnej przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Zapoznaj się ze [składnikami i architekturą scenariusza](hyper-v-vmm-architecture.md).
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](site-recovery-support-matrix-to-sec-site.md) wszystkich składników.
- Upewnij się, że serwery Menedżera maszyn wirtualnych i hosty funkcji Hyper-V spełniają [wymagania dotyczące pomocy technicznej.](site-recovery-support-matrix-to-sec-site.md)
- Sprawdź, czy maszyny wirtualne, które chcesz replikować, są zgodne z [obsługą replikowanej maszyny.](site-recovery-support-matrix-to-sec-site.md)

## <a name="prepare-for-network-mapping"></a>Przygotowanie do mapowania sieci

[Mapowanie sieci](hyper-v-vmm-network-mapping.md) między lokalnymi sieciami maszyn wirtualnych Menedżera maszyn wirtualnych w chmurach źródłowych i docelowych. Mapowanie wykonuje następujące czynności:

- Łączy maszyny wirtualne z odpowiednimi docelowymi sieciami maszyn wirtualnych po przejściu w tryb failover.
- Optymalnie umieszcza repliki maszyn wirtualnych na docelowych serwerach hostów funkcji Hyper-V.
- Jeśli nie skonfigurujesz mapowania sieci, repliki maszyn wirtualnych nie będą połączone z siecią maszyn wirtualnych po przełączeniu w błąd.

Przygotuj Menedżera maszyn wirtualnych w następujący sposób:

- Upewnij się, że masz [sieci logiczne Menedżera maszyn wirtualnych](https://docs.microsoft.com/system-center/vmm/network-logical) na serwerach Menedżera maszyn źródłowych i docelowych:
  - Sieć logiczna na serwerze źródłowym powinna być skojarzona z chmurą źródłową, w której znajdują się hosty funkcji Hyper-V.
  - Sieć logiczna na serwerze docelowym powinna być skojarzona z chmurą docelową.
- Upewnij się, że masz [sieci maszyn wirtualnych](https://docs.microsoft.com/system-center/vmm/network-virtual) na serwerach menedżera maszyn źródłowych i docelowych. Sieci maszyn wirtualnych powinny być połączone z siecią logiczną w każdej lokalizacji.
- Połącz maszyny wirtualne na źródłowych hostach funkcji Hyper-V ze źródłową siecią maszyny wirtualnej.

## <a name="prepare-for-powershell"></a>Przygotowanie do programu PowerShell

Upewnij się, że masz platformę Azure PowerShell gotową do pracy:

- Jeśli program PowerShell jest już używany, należy uaktualnić do wersji 0.8.10 lub nowszej. [Dowiedz się więcej](/powershell/azureps-cmdlets-docs) o konfigurowaniu programu PowerShell.
- Po skonfigurowaniu i skonfigurowaniu programu PowerShell przejrzyj [polecenia cmdlet usługi](/powershell/azure/overview).
- Aby dowiedzieć się więcej na temat używania wartości parametrów, danych wejściowych i wyjściowych w programie PowerShell, przeczytaj przewodnik [Wprowadzenie.](/powershell/azure/get-started-azureps)

## <a name="set-up-a-subscription"></a>Konfigurowanie subskrypcji

1. W programie PowerShell zaloguj się do konta platformy Azure.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. Pobierz listę subskrypcji z identyfikatorami subskrypcji. Zanotuj identyfikator subskrypcji, w której chcesz utworzyć magazyn usług odzyskiwania.

   ```azurepowershell
   Get-AzSubscription
   ```

1. Ustaw subskrypcję dla magazynu.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

1. Utwórz grupę zasobów usługi Azure Resource Manager, jeśli jej nie masz.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. Utwórz nowy magazyn usług odzyskiwania. Zapisz obiekt przechowalni w zmiennej, która ma być użyta później.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   Obiekt przechowalni można pobrać po `Get-AzRecoveryServicesVault` jego utworzeniu przy użyciu polecenia cmdlet.

## <a name="set-the-vault-context"></a>Ustawianie kontekstu przechowalni

1. Pobieranie istniejącego przechowalni.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. Ustawianie kontekstu przechowalni.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>Instalowanie dostawcy odzyskiwania witryny

1. Na komputerze Menedżera maszyn wirtualnych utwórz katalog, uruchamiając następujące polecenie:

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. Wyodrębnij pliki przy użyciu pobranego pliku instalacyjnego dostawcy.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Zainstaluj dostawcę i poczekaj na zakończenie instalacji.

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. Zarejestruj serwer w przechowalni.

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>Tworzenie i kojarzenie zasad replikacji

1. Utwórz zasady replikacji, w tym przypadku dla funkcji Hyper-V 2012 R2, w następujący sposób:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > Usługa Menedżer maszyn wirtualnych może zawierać hosty funkcji Hyper-V z różnymi wersjami systemu Windows Server, ale zasady replikacji są przeznaczone dla określonej wersji systemu operacyjnego. Jeśli w różnych systemach operacyjnych działają różne hosty, należy utworzyć oddzielne zasady replikacji dla każdego systemu. Jeśli na przykład w systemie Windows Server 2012 jest uruchomionych pięć hostów i trzy hosty uruchomione w systemie Windows Server 2012 R2, należy utworzyć dwie zasady replikacji. Można utworzyć jeden dla każdego typu systemu operacyjnego.

1. Pobierz podstawowy kontener ochrony (podstawowa chmura menedżera maszyn wirtualnych) i kontener ochrony odzyskiwania (odzyskiwanie chmury Menedżera maszyn wirtualnych).

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. Pobierz zasady replikacji utworzone przy użyciu przyjaznej nazwy.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. Uruchom skojarzenie kontenera ochrony (chmura menedżera maszyn wirtualnych) z zasadami replikacji.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. Poczekaj na zakończenie zadania skojarzenia zasad. Aby sprawdzić, czy zadanie jest zakończone, użyj następującego fragmentu kodu programu PowerShell:

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. Po zakończeniu przetwarzania zadania uruchom następujące polecenie:

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

Aby sprawdzić zakończenie operacji, wykonaj kroki opisane w [części Monitorowanie aktywności](#monitor-activity).

##  <a name="configure-network-mapping"></a>Konfiguracja mapowania sieci

1. To polecenie służy do pobierania serwerów dla bieżącego magazynu. Polecenie przechowuje serwery odzyskiwania `$Servers` lokacji w zmiennej macierzy.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. Uruchom to polecenie, aby pobrać sieci dla źródłowego serwera Menedżera maszyn wirtualnych i docelowego serwera Menedżera maszyn wirtualnych.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > Źródłowy serwer Menedżera maszyn wirtualnych może być pierwszym lub drugim w macierzy serwera. Sprawdź nazwy serwerów Menedżera maszyn wirtualnych i odpowiednio pobrać sieci.

1. To polecenie cmdlet tworzy mapowanie między siecią podstawową a siecią odzyskiwania. Określa sieć podstawową jako pierwszy `$PrimaryNetworks`element . Określa sieć odzyskiwania jako pierwszy element `$RecoveryNetworks`.

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>Włącz ochronę maszyn wirtualnych

Po prawidłowym skonfigurowaniu serwerów, chmur i sieci włącz ochronę maszyn wirtualnych w chmurze.

1. Aby włączyć ochronę, uruchom następujące polecenie, aby pobrać kontener ochrony:

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. Pobierz jednostkę ochrony (VM), w następujący sposób:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. Włącz replikację maszyny Wirtualnej.

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Jeśli chcesz replikować do dysków zarządzanych z włączoną funkcją CMK na platformie Azure, wykonaj następujące kroki przy użyciu programu Az PowerShell 3.3.0:
>
> 1. Włączanie trybu failover na dyskach zarządzanych przez aktualizowanie właściwości maszyny Wirtualnej
> 1. Użyj `Get-AzRecoveryServicesAsrReplicationProtectedItem` polecenia cmdlet, aby pobrać identyfikator dysku dla każdego dysku chronionego elementu
> 1. Utwórz obiekt słownika przy użyciu `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` polecenia cmdlet, aby zawierał mapowanie identyfikatora dysku do zestawu szyfrowania dysku. Te zestawy szyfrowania dysku mają być wstępnie utworzone przez Ciebie w regionie docelowym.
> 1. Zaktualizuj właściwości `Set-AzRecoveryServicesAsrReplicationProtectedItem` maszyny Wirtualnej przy użyciu polecenia cmdlet, przekazując obiekt słownika w parametrze **DiskIdToDiskEncryptionSetMap.**

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Aby przetestować wdrożenie, uruchom test pracy awaryjnej dla pojedynczej maszyny wirtualnej. Można również utworzyć plan odzyskiwania, który zawiera wiele maszyn wirtualnych i uruchomić test pracy awaryjnej dla planu. Próba przejścia w tryb failover symuluje mechanizm pracy awaryjnej i odzyskiwania w sieci izolowanej.

1. Pobierz maszynę wirtualną, do której maszyny wirtualne zostaną przejęte awaryjnie.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. Wykonaj test pracy awaryjnej.

   Dla pojedynczej maszyny Wirtualnej:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   W przypadku planu naprawczego:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

Aby sprawdzić zakończenie operacji, wykonaj kroki opisane w [części Monitorowanie aktywności](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Uruchamianie planowanych i nieplanowanych przesiewów awaryjnych

1. Wykonaj planowaną przemijkę awaryjną.

   Dla pojedynczej maszyny Wirtualnej:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   W przypadku planu naprawczego:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. Wykonaj nieplanowaną funkcję failover.

   Dla pojedynczej maszyny Wirtualnej:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   W przypadku planu naprawczego:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>Monitorowanie aktywności

Poniższe polecenia obejmują monitorowanie aktywności trybu failover. Poczekaj na zakończenie przetwarzania między zadaniami.

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
    Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
    if($job -eq $null -or $job.StateDescription -ne "Completed")
    {
        $isJobLeftForProcessing = $true;
    }

if($isJobLeftForProcessing)
    {
        Start-Sleep -Seconds 60
    }
}While($isJobLeftForProcessing)
```

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](/powershell/module/az.recoveryservices) o poleceniach cmdlet programu Power Manager programu PowerShell.
