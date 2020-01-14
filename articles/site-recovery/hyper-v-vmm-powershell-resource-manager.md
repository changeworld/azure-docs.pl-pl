---
title: Konfigurowanie funkcji Hyper-V (z programem VMM) odzyskiwanie awaryjne w lokacji dodatkowej przy użyciu programu Azure Site Recovery/PowerShell
description: Opisuje sposób konfigurowania odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM do pomocniczej lokacji programu VMM przy użyciu Azure Site Recovery i programu PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: d2f25774f89182004e23605bf4c37d1e1d739df7
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867031"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Konfigurowanie odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V w lokacji dodatkowej przy użyciu programu PowerShell (Menedżer zasobów)

W tym artykule przedstawiono sposób automatyzacji czynności związanych z replikacją maszyn wirtualnych funkcji Hyper-V w chmurach System Center Virtual Machine Manager do Virtual Machine Managerj chmury w dodatkowej lokacji lokalnej przy użyciu [Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Zapoznaj się ze [składnikami i architekturą scenariusza](hyper-v-vmm-architecture.md).
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](site-recovery-support-matrix-to-sec-site.md) wszystkich składników.
- Upewnij się, że serwery Virtual Machine Manager i hosty funkcji Hyper-V są zgodne z wymaganiami dotyczącymi [obsługi](site-recovery-support-matrix-to-sec-site.md).
- Sprawdź, czy maszyny wirtualne, które chcesz replikować, są zgodne z [obsługą zreplikowanej maszyny](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Przygotowanie do mapowania sieci

[Mapowanie sieci](hyper-v-vmm-network-mapping.md) mapuje między lokalnymi Virtual Machine Manager sieciami maszyn wirtualnych w chmurze źródłowej i docelowej. Mapowanie wykonuje następujące czynności:

- Łączy maszyny wirtualne z odpowiednimi docelowymi sieciami maszyn wirtualnych po przejściu w tryb failover. 
- Optymalnie umieszcza repliki maszyn wirtualnych na docelowych serwerach hostów funkcji Hyper-V. 
- Jeśli nie skonfigurujesz mapowania sieci, maszyny wirtualne repliki nie będą połączone z siecią maszyny wirtualnej po przejściu w tryb failover.

Przygotuj Virtual Machine Manager w następujący sposób:

* Upewnij się, że w źródłowym i docelowym serwerze Virtual Machine Manager znajdują się [Virtual Machine Manager sieci logicznych](https://docs.microsoft.com/system-center/vmm/network-logical) :

    - Sieć logiczna na serwerze źródłowym powinna być skojarzona z chmurą źródłową, w której znajdują się hosty funkcji Hyper-V.
    - Sieć logiczna na serwerze docelowym powinna być skojarzona z chmurą docelową.
* Upewnij się, że masz [sieci VMNetwork](https://docs.microsoft.com/system-center/vmm/network-virtual) na źródłowym i docelowym serwerze Virtual Machine Manager. Sieci maszyn wirtualnych powinny być połączone z siecią logiczną w każdej lokalizacji.
* Połącz maszyny wirtualne na źródłowych hostach funkcji Hyper-V ze źródłową siecią maszyny wirtualnej. 

## <a name="prepare-for-powershell"></a>Przygotowanie do programu PowerShell

Upewnij się, że masz Azure PowerShell gotowy do użycia:

- Jeśli używasz już programu PowerShell, Uaktualnij go do wersji 0.8.10 lub nowszej. [Dowiedz się więcej](/powershell/azureps-cmdlets-docs) na temat sposobu konfigurowania programu PowerShell.
- Po skonfigurowaniu i skonfigurowaniu programu PowerShell Przejrzyj [polecenia cmdlet usługi](/powershell/azure/overview).
- Aby dowiedzieć się więcej na temat używania wartości parametrów, danych wejściowych i wyjść w programie PowerShell, Przeczytaj przewodnik [wprowadzenie](/powershell/azure/get-started-azureps) .

## <a name="set-up-a-subscription"></a>Konfigurowanie subskrypcji
1. W programie PowerShell Zaloguj się do konta platformy Azure.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzAccount #-Credential $Cred
2. Pobierz listę subskrypcji z identyfikatorami subskrypcji. Zanotuj identyfikator subskrypcji, w której chcesz utworzyć magazyn Recovery Services. 

        Get-AzSubscription
3. Ustaw subskrypcję magazynu.

        Set-AzContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services
1. Utwórz grupę zasobów Azure Resource Manager, jeśli jej nie masz.

        New-AzResourceGroup -Name #ResourceGroupName -Location #location
2. Utwórz nowy magazyn Recovery Services. Zapisz obiekt magazynu w zmiennej, która ma zostać użyta później. 

        $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   
    Obiekt magazynu można pobrać po utworzeniu go za pomocą polecenia cmdlet Get-AzRecoveryServicesVault.

## <a name="set-the-vault-context"></a>Ustaw kontekst magazynu
1. Pobierz istniejący magazyn.

       $vault = Get-AzRecoveryServicesVault -Name #vaultname
2. Ustaw kontekst magazynu.

       Set-AzSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Zainstaluj dostawcę Site Recovery
1. Na maszynie Virtual Machine Manager Utwórz katalog, uruchamiając następujące polecenie:

       New-Item c:\ASR -type directory
2. Wyodrębnij pliki przy użyciu pobranego pliku instalacyjnego dostawcy.

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

## <a name="create-and-associate-a-replication-policy"></a>Tworzenie i kojarzenie zasad replikacji
1. Utwórz zasady replikacji w tym przypadku dla funkcji Hyper-V 2012 R2 w następujący sposób:

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
    > Chmura Virtual Machine Manager może zawierać hosty funkcji Hyper-V z różnymi wersjami systemu Windows Server, ale zasady replikacji dotyczą określonej wersji systemu operacyjnego. Jeśli masz różne hosty działające w różnych systemach operacyjnych, Utwórz oddzielne zasady replikacji dla każdego systemu. Jeśli na przykład masz pięć hostów uruchomionych w systemie Windows Server 2012 i trzech hostach z systemem Windows Server 2012 R2, Utwórz dwie zasady replikacji. Tworzysz jeden dla każdego typu systemu operacyjnego.

2. Pobierz kontener ochrony podstawowej (podstawowa Virtual Machine Manager w chmurze) i kontener ochrony odzyskiwania (Odzyskiwanie Virtual Machine Manager w chmurze).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Pobierz utworzone zasady replikacji przy użyciu przyjaznej nazwy.

       $policy = Get-AzSiteRecoveryPolicy -FriendlyName $policyname
4. Uruchom skojarzenie kontenera ochrony (Virtual Machine Manager Cloud) z zasadami replikacji.

       $associationJob  = Start-AzSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Poczekaj na zakończenie zadania skojarzenia zasad. Aby sprawdzić, czy zadanie zostało zakończone, użyj następującego fragmentu kodu programu PowerShell:

       $job = Get-AzSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. Po zakończeniu przetwarzania zadania uruchom następujące polecenie:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Aby sprawdzić zakończenie operacji, wykonaj kroki opisane w sekcji [monitorowanie aktywności](#monitor-activity).

##  <a name="configure-network-mapping"></a>Konfiguracja mapowania sieci
1. Użyj tego polecenia, aby pobrać serwery dla bieżącego magazynu. Polecenie przechowuje Site Recovery serwerów w zmiennej tablicowej $Servers.

        $Servers = Get-AzSiteRecoveryServer
2. Uruchom to polecenie, aby pobrać sieci dla źródłowego serwera Virtual Machine Manager i docelowego serwera Virtual Machine Manager.

        $PrimaryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Źródłowy serwer Virtual Machine Manager może być pierwszy lub drugi z nich w macierzy serwerowej. Sprawdź nazwy serwerów Virtual Machine Manager i odpowiednio Pobierz sieci.


3. To polecenie cmdlet tworzy mapowanie między siecią podstawową i siecią odzyskiwania. Określa sieć podstawową jako pierwszy element $PrimaryNetworks. Określa sieć odzyskiwania jako pierwszy element $RecoveryNetworks.

        New-AzSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Włącz ochronę maszyn wirtualnych
Po poprawnym skonfigurowaniu serwerów, chmur i sieci należy włączyć ochronę maszyn wirtualnych w chmurze.

1. Aby włączyć ochronę, uruchom następujące polecenie w celu pobrania kontenera ochrony:

          $PrimaryProtectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Pobierz jednostkę ochrony (VM) w następujący sposób:

           $protectionEntity = Get-AzSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Włącz replikację dla maszyny wirtualnej.

          $jobResult = Set-AzSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

> [!NOTE]
> Jeśli chcesz przeprowadzić replikację do CMK dysków zarządzanych na platformie Azure, wykonaj następujące czynności za pomocą polecenia AZ PowerShell 3.3.0 i nowszego:
>
> 1. Włącz tryb failover na dyskach zarządzanych przez zaktualizowanie właściwości maszyny wirtualnej
> 2. Użyj polecenia cmdlet Get-AsrReplicationProtectedItem, aby pobrać identyfikator dysku dla każdego dysku chronionego elementu
> 3. Utwórz obiekt słownika przy użyciu polecenia cmdlet New-Object "System. Collections. Generic. dictionary" "2 [System. String, system. String]", aby zawierać mapowanie identyfikatora dysku na zestaw szyfrowania dysków. Te zestawy szyfrowania dysków są wstępnie utworzone przez użytkownika w regionie docelowym.
> 4. Zaktualizuj właściwości maszyny wirtualnej za pomocą polecenia cmdlet Set-AsrReplicationProtectedItem, przekazując obiekt dictionary w parametrze-DiskIdToDiskEncryptionSetMap.

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Aby przetestować wdrożenie, Uruchom test pracy w trybie failover dla pojedynczej maszyny wirtualnej. Można również utworzyć plan odzyskiwania, który zawiera wiele maszyn wirtualnych, i uruchomić test pracy w trybie failover dla planu. Próba przejścia w tryb failover symuluje mechanizm pracy awaryjnej i odzyskiwania w sieci izolowanej.

1. Pobierz maszynę wirtualną, do której maszyny wirtualne będą działać w trybie failover.

       $Servers = Get-AzSiteRecoveryServer
       $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

2. Wykonaj test pracy w trybie failover.

   Dla jednej maszyny wirtualnej:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   W przypadku planu odzyskiwania:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Aby sprawdzić zakończenie operacji, wykonaj kroki opisane w sekcji [monitorowanie aktywności](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Uruchamianie planowanych i nieplanowanych przełączeń w tryb failover

1. Wykonaj planowaną pracę w trybie failover.

   Dla jednej maszyny wirtualnej:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   W przypadku planu odzyskiwania:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Wykonaj niezaplanowaną pracę w trybie failover.

   Dla jednej maszyny wirtualnej:
        
        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   W przypadku planu odzyskiwania:

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



## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](/powershell/module/az.recoveryservices) na temat Site Recovery za pomocą Menedżer zasobów poleceń cmdlet programu PowerShell.
