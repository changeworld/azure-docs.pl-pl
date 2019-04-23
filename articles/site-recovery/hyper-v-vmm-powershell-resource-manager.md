---
title: Konfigurowanie odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM do lokacji dodatkowej przy użyciu programu PowerShell i usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM do lokacji dodatkowej programu VMM przy użyciu programu PowerShell i usługi Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 78bd077b5491b093510b9c55bf7b5a42ee9cb578
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787602"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Konfigurowanie odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej przy użyciu programu PowerShell (Resource Manager)

W tym artykule pokazano, jak do automatyzowania czynności w przypadku replikacji maszyn wirtualnych z funkcją Hyper-V w chmurach programu System Center Virtual Machine Manager do programu Virtual Machine Manager chmury w dodatkowej lokacji lokalnej przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Zapoznaj się ze [składnikami i architekturą scenariusza](hyper-v-vmm-architecture.md).
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](site-recovery-support-matrix-to-sec-site.md) wszystkich składników.
- Upewnij się, że program Virtual Machine Manager, serwery i hosty funkcji Hyper-V są zgodne z [obsługi wymagań związanych z](site-recovery-support-matrix-to-sec-site.md).
- Upewnij się, że maszyny wirtualne mają być replikowane są zgodne z [replikowane maszyny obsługi](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Przygotowanie do mapowania sieci

[Mapowanie sieci](hyper-v-vmm-network-mapping.md) mapowania między w środowisku lokalnym sieci maszyn wirtualnych programu Virtual Machine Manager w źródłową i docelową. Mapowanie wykonuje następujące czynności:

- Łączy maszyny wirtualne z odpowiednimi docelowymi sieciami maszyn wirtualnych po przejściu w tryb failover. 
- Optymalnie umieszcza repliki maszyn wirtualnych na docelowych serwerach hostów funkcji Hyper-V. 
- Jeśli nie skonfigurujesz mapowania sieci, maszyn wirtualnych repliki nie będzie połączony z siecią maszyny Wirtualnej po włączeniu trybu failover.

Program Virtual Machine Manager, należy przygotować w następujący sposób:

* Upewnij się, że masz [sieci logiczne programu Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/network-logical) na serwerach źródłowym i docelowym, program Virtual Machine Manager:

    - Sieć logiczna na serwerze źródłowym powinna być skojarzona z chmurą źródłową, w której znajdują się hosty funkcji Hyper-V.
    - Sieć logiczna na serwerze docelowym powinna być skojarzona z chmurą docelową.
* Upewnij się, że masz [sieci maszyn wirtualnych](https://docs.microsoft.com/system-center/vmm/network-virtual) na serwerach źródłowym i docelowym, program Virtual Machine Manager. Sieci maszyn wirtualnych powinny być połączone z siecią logiczną w każdej lokalizacji.
* Połącz maszyny wirtualne na źródłowych hostach funkcji Hyper-V ze źródłową siecią maszyny wirtualnej. 

## <a name="prepare-for-powershell"></a>Przygotowanie środowiska PowerShell

Upewnij się, że jest gotowa do użytku programu Azure PowerShell:

- Jeśli już używasz programu PowerShell, przeprowadź uaktualnienie do wersji 0.8.10 lub nowszej. [Dowiedz się więcej](/powershell/azureps-cmdlets-docs) o sposobie konfigurowania programu PowerShell.
- Po skonfigurowaniu i konfigurowanie programu PowerShell, przejrzyj [poleceń cmdlet usługi](/powershell/azure/overview).
- Aby dowiedzieć się więcej o sposobie używania wartości parametrów, wejść i wyjść w programie PowerShell, przeczytaj [wprowadzenie](/powershell/azure/get-started-azureps) przewodnik.

## <a name="set-up-a-subscription"></a>Konfigurowanie subskrypcji
1. Za pomocą programu PowerShell Zaloguj się do konta platformy Azure.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzAccount #-Credential $Cred
2. Pobierz listę subskrypcji z subskrypcją identyfikatorów. Należy pamiętać, identyfikator subskrypcji, w którym chcesz utworzyć magazyn usługi Recovery Services. 

        Get-AzSubscription
3. Ustaw subskrypcję magazynu.

        Set-AzContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services
1. Jeśli nie masz, Utwórz grupę zasobów usługi Azure Resource Manager.

        New-AzResourceGroup -Name #ResourceGroupName -Location #location
2. Utwórz nowy magazyn usługi Recovery Services. Zapisz obiekt magazynu w zmiennej na później. 

        $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   
    Po utworzeniu za pomocą polecenia cmdlet Get-AzRecoveryServicesVault można pobrać obiektu magazynu.

## <a name="set-the-vault-context"></a>Ustaw kontekst magazynu
1. Pobieranie istniejącego magazynu.

       $vault = Get-AzRecoveryServicesVault -Name #vaultname
2. Ustaw kontekst magazynu.

       Set-AzSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Zainstaluj dostawcę usługi Site Recovery
1. Na komputerze program Virtual Machine Manager należy utworzyć katalog, uruchamiając następujące polecenie:

       New-Item c:\ASR -type directory
2. Wyodrębnij pliki przy użyciu pliku instalacji pobranego dostawcy.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Zainstaluj dostawcę i poczekaj na zakończenie instalacji.

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

4. Zarejestruj serwer w magazynie.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Utwórz i Skojarz zasady replikacji
1. Tworzenie zasad replikacji, w tym przypadku dla funkcji Hyper-V 2012 R2, w następujący sposób:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > Chmura programu Virtual Machine Manager może zawierać hosty funkcji Hyper-V uruchomione różne wersje systemu Windows Server, ale zasady replikacji jest dla określonej wersji systemu operacyjnego. W przypadku różnych hostach, działających w różnych systemach operacyjnych, należy utworzyć zasady replikacji osobne dla każdego systemu. Na przykład jeśli masz pięć hostów z uruchomionym w systemie Windows Server 2012 i trzy hosty z systemem Windows Server 2012 R2, należy utworzyć dwie zasady replikacji. Można utworzyć dla każdego typu systemu operacyjnego.

2. Pobierz kontener ochrony podstawowej (głównej chmura programu Virtual Machine Manager) i kontener ochrony odzyskiwania (recovery chmur programu Virtual Machine Manager).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Pobieranie zasad replikacji utworzonych przy użyciu przyjaznej nazwy.

       $policy = Get-AzSiteRecoveryPolicy -FriendlyName $policyname
4. Uruchom skojarzenia kontenera ochrony (w chmurze programu Virtual Machine Manager) z zasadami replikacji.

       $associationJob  = Start-AzSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Poczekaj na zakończenie zadania skojarzenia zasad. Aby sprawdzić, jeśli zadanie jest zakończone, użyj następującego fragmentu kodu programu PowerShell:

       $job = Get-AzSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. Po zakończeniu zadania przetwarzania, uruchom następujące polecenie:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Aby sprawdzić ukończeniem operacji, wykonaj kroki opisane w [monitorować aktywność](#monitor-activity).

##  <a name="configure-network-mapping"></a>Konfiguracja mapowania sieci
1. Użyj tego polecenia, aby pobrać serwerów dla bieżącego magazynu. Polecenie przechowuje serwerów Site Recovery w $Servers zmiennej tablicowej.

        $Servers = Get-AzSiteRecoveryServer
2. Uruchom następujące polecenie, aby pobrać sieci do serwera programu Virtual Machine Manager źródłowego i docelowego serwera programu Virtual Machine Manager.

        $PrimaryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Źródłowy serwer programu Virtual Machine Manager może być pierwszym lub drugim w tablicy serwera. Sprawdź nazwy serwera programu Virtual Machine Manager, a następnie odpowiednio pobierania sieci.


3. To polecenie cmdlet tworzy mapowanie między podstawowym siecią i siecią odzyskiwania. Jako pierwszy element $PrimaryNetworks określa sieci podstawowej. Określa sieć odzyskiwania jako pierwszy element $RecoveryNetworks.

        New-AzSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Włączyć ochronę maszyn wirtualnych
Po serwerów, chmur i sieci zostały skonfigurowane poprawnie, należy włączyć ochronę maszyn wirtualnych w chmurze.

1. Aby włączyć ochronę, uruchom następujące polecenie, aby pobrać kontenera ochrony:

          $PrimaryProtectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Pobierz jednostki ochrony (VM) w następujący sposób:

           $protectionEntity = Get-AzSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Włącz replikację dla maszyny Wirtualnej.

          $jobResult = Set-AzSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Aby przetestować wdrożenie, należy uruchomić test trybu failover dla pojedynczej maszyny wirtualnej. Możesz również utworzyć plan odzyskiwania, który zawiera wiele maszyn wirtualnych i uruchomić test trybu failover dla planu. Próba przejścia w tryb failover symuluje mechanizm pracy awaryjnej i odzyskiwania w sieci izolowanej.

1. Pobieranie maszyny Wirtualnej, do którego maszyn wirtualnych przejdzie w tryb failover.

       $Servers = Get-AzSiteRecoveryServer
       $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

2. Wykonaj test trybu failover.

   Dla jednej maszyny Wirtualnej:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   Plan odzyskiwania:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Aby sprawdzić ukończeniem operacji, wykonaj kroki opisane w [monitorować aktywność](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Uruchomić planowany i nieplanowany tryb failover

1. Realizacja planowanego trybu failover.

   Dla jednej maszyny Wirtualnej:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Plan odzyskiwania:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Wykonaj nieplanowany tryb failover.

   Dla jednej maszyny Wirtualnej:
        
        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Plan odzyskiwania:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Monitorowanie aktywności
Użyj następujących poleceń, aby monitorować aktywność trybu failover. Poczekaj na zakończenie przetwarzania między zadaniami.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
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



## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](/powershell/module/az.recoveryservices) o usłudze Site Recovery przy użyciu poleceń cmdlet programu PowerShell usługi Resource Manager.
