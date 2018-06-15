---
title: Replikowanie maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM przy użyciu usługi Azure Site Recovery i środowiska PowerShell (Resource Manager) | Dokumentacja firmy Microsoft
description: Replikowanie maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM przy użyciu usługi Azure Site Recovery i programu PowerShell
services: site-recovery
documentationcenter: ''
author: Rajani-Janaki-Ram
manager: rochakm
editor: raynew
ms.assetid: 6ac509ad-5024-43d8-b621-d8fec019b9a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: rajanaki
ms.openlocfilehash: 67d9164fcf8fb3731c4e3a9d30ac5c38da5908c7
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677462"
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>Replikowanie maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM na platformie Azure przy użyciu programu PowerShell i usługi Azure Resource Manager
> [!div class="op_single_selector"]
> * [Azure portal](site-recovery-vmm-to-azure.md)
> * [Program PowerShell — model usługi Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell — model klasyczny](site-recovery-deploy-with-powershell.md)
>
>

## <a name="overview"></a>Przegląd
Usługa Azure Site Recovery przyczynia się do strategii biznesowej ciągłości i odzyskiwaniem po awarii odzyskiwania (BCDR) poprzez organizowanie replikacji, trybu failover i odzyskiwania maszyn wirtualnych w różnych scenariuszy wdrażania. Pełną listę wdrażania scenariuszy dla [Omówienie usługi Azure Site Recovery](site-recovery-overview.md).

W tym artykule przedstawiono sposób automatyzacji typowych zadań, które należy wykonać podczas konfigurowania usługi Azure Site Recovery do replikowania maszyn wirtualnych funkcji Hyper-V w chmurach programu System Center VMM do magazynu platformy Azure przy użyciu programu PowerShell.

Artykuł zawiera wymagania wstępne dla scenariusza i przedstawiono

* Jak skonfigurować magazyn usług odzyskiwania
* Zainstaluj dostawcę usługi Azure Site Recovery na źródłowym serwerze programu VMM
* Zarejestruj serwer w magazynie, dodawania konta magazynu platformy Azure
* Zainstaluj agenta usług odzyskiwania Azure na serwerach hostów funkcji Hyper-V
* Skonfiguruj ustawienia ochrony chmury VMM, które będą stosowane do wszystkich chronionych maszyn wirtualnych
* Włącz ochronę tych maszyn wirtualnych.
* Przetestuj awaryjnej, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.

Jeśli napotkasz problem ze skonfigurowaniem w tym scenariuszu Opublikuj swoje pytania na [Forum usług odzyskiwania Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [!NOTE]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu modelu wdrażania Menedżera zasobów.
>
>

## <a name="before-you-start"></a>Przed rozpoczęciem
Upewnij się, że te wymagania wstępne zostały spełnione:

### <a name="azure-prerequisites"></a>Wymagania wstępne platformy Azure
* Będziesz potrzebować konta platformy [Microsoft Azure](https://azure.microsoft.com/). Jeśli nie masz, Rozpocznij od [bezpłatne konto](https://azure.microsoft.com/free). Ponadto możesz przeczytać temat [cennik usługi Azure Site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* Będziesz potrzebować subskrypcji dostawcy usług Kryptograficznych, jeśli próbujesz limit replikacji dla scenariusza subskrypcji dostawcy usług Kryptograficznych. Dowiedz się więcej na temat dostawcy usług Kryptograficznych w [rejestrowania w programie dostawcy usług Kryptograficznych](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
* Będziesz potrzebować konta magazynu (Resource Manager) platformy Azure w wersji 2 do przechowywania danych replikowanych do platformy Azure. Konto musi włączyć replikację geograficzną. Powinien znajdować się w tym samym regionie co usługa Azure Site Recovery i być skojarzone z tą samą subskrypcją lub subskrypcji dostawcy usług Kryptograficznych. Aby dowiedzieć się więcej na temat konfigurowania magazynu Azure, zobacz [wprowadzenie do usługi Microsoft Azure Storage](../storage/common/storage-introduction.md) odwołania.
* Należy się upewnić, że maszyny wirtualne, które mają być chronione są zgodne z [wymagania wstępne maszyny wirtualnej platformy Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

> [!NOTE]
> Obecnie tylko operacje poziomu maszyny Wirtualnej jest możliwe za pośrednictwem programu Powershell. Obsługa operacji poziomu planu odzyskiwania zostaną wprowadzone wkrótce.  Obecnie jest ograniczona do wykonywania przełączanie w trybie Failover tylko na poziom szczegółowości "chronione maszyny Wirtualnej", a nie na poziomie planu odzyskiwania.
>
>

### <a name="vmm-prerequisites"></a>Wymagania wstępne programu VMM
* Należy na serwerze programu VMM działa na System Center 2012 R2.
* Żadnym serwerze VMM zawierających maszyny wirtualne, które chcesz chronić, musi być uruchomiona dostawcę usługi Azure Site Recovery. To jest instalowany podczas wdrażania usługi Azure Site Recovery.
* Będziesz potrzebować co najmniej jednej chmury na serwerze programu VMM, który chcesz chronić. Chmura powinna zawierać:
  * Co najmniej jedną grupę hostów programu VMM.
  * Co najmniej jeden serwer hosta lub klaster funkcji Hyper-V w każdej grupie hostów.
  * Co najmniej jednej maszyny wirtualnej na serwerze źródłowym funkcji Hyper-V.
* Dowiedz się więcej o konfigurowaniu chmur VMM:
  * Dowiedz się więcej o prywatnych chmur programu VMM [What's New in chmury prywatnej z programu System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) i [program VMM 2012 i chmury](http://go.microsoft.com/fwlink/?LinkId=324956).
  * Dowiedz się więcej o [Konfigurowanie VMM sieci szkieletowej usług w chmurze](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
  * Po elementów sieci szkieletowej chmury w miejscu więcej informacji o tworzeniu chmur prywatnych w [tworzenia chmury prywatnej w programie VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) i [wskazówki: tworzenie chmur prywatnych z programu System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).

### <a name="hyper-v-prerequisites"></a>Wymagania wstępne funkcji Hyper-V
* Serwery hosta funkcji Hyper-V musi działać co najmniej **systemu Windows Server 2012** z rolą funkcji Hyper-V lub **Microsoft Hyper-V Server 2012** i mieć zainstalowane najnowsze aktualizacje.
* Jeśli używasz funkcji Hyper-V w klastrze, broker klastra nie jest tworzony automatycznie, jeśli masz klaster oparty na statycznym adresie IP. Konieczne będzie ręczne skonfigurowanie brokera klastra. Termin
* Instrukcje można znaleźć [jak konfigurowanie brokera repliki funkcji Hyper-V](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx).
* Dowolnego serwera hosta funkcji Hyper-V lub klastra, dla którego chcesz zarządzać ochrony musi być uwzględniona w chmurze VMM.

### <a name="network-mapping-prerequisites"></a>Wymagania wstępne związane z mapowaniem sieci
Podczas ochrony maszyn wirtualnych na platformie Azure, mapowanie sieci działa sieci maszyny wirtualnej na źródłowym serwerze programu VMM i docelowymi sieciami platformy Azure, aby umożliwić następujące:

* Wszystkie komputery, które nie przejdą pomyślnie za pośrednictwem tej sieci można połączyć ze sobą, niezależnie od tego, jakiego planu odzyskiwania znajdują się w.
* Jeśli brama sieci została skonfigurowana w docelowej sieci platformy Azure, maszyny wirtualne mogą łączyć się z innymi lokalnymi maszynami wirtualnymi.
* Jeśli nie skonfigurujesz mapowania sieci maszyn wirtualnych, które pracy awaryjnej w tym samym planie odzyskiwania będzie można połączyć ze sobą po Failover do platformy Azure.

Jeśli chcesz wdrożyć mapowanie sieci, musisz upewnić się, że spełnione są następujące warunki:

* Maszyny wirtualne, które chcesz chronić na źródłowym serwerze programu VMM, powinny być połączone z siecią maszyn wirtualnych. Ta sieć powinna być połączona z siecią logiczną skojarzoną z chmurą.
* Sieć platformy Azure, z którym replikowanych maszyn wirtualnych może się łączyć po awaryjnym. W tym czasie awaryjnej należy wybrać tej sieci. Sieć powinna znajdować się w tym samym regionie co subskrypcja usługi Azure Site Recovery.

Dowiedz się więcej o mapowanie sieci w

* [Jak skonfigurować sieci logiczne w programie VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [Konfigurowanie sieci Vmnetwork i bram w programie VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
* [Konfigurowanie i monitorowanie sieci wirtualnych na platformie Azure](https://azure.microsoft.com/documentation/services/virtual-network/)

### <a name="powershell-prerequisites"></a>Wymagania wstępne programu PowerShell
Upewnij się, że masz przejść programu Azure PowerShell. Jeśli korzystasz już z programu PowerShell, należy uaktualnić do wersji 0.8.10 lub nowszej. Aby uzyskać informacje o konfigurowaniu programu PowerShell, zobacz [Przewodnik instalowania i konfigurowania programu Azure PowerShell](/powershell/azureps-cmdlets-docs). Po należy skonfigurować i skonfigurowaniu programu PowerShell można wyświetlić wszystkie dostępne polecenia cmdlet dla usługi [tutaj](/powershell/azure/overview).

Aby dowiedzieć się więcej o wskazówki, które mogą pomóc używać poleceń cmdlet, takich jak jak wartości parametru, dane wejściowe i dane wyjściowe są zazwyczaj obsługiwane w programie Azure PowerShell, zobacz [przewodnik Wprowadzenie do poleceń cmdlet Azure](/powershell/azure/get-started-azureps).

## <a name="step-1-set-the-subscription"></a>Krok 1: Ustaw subskrypcji
1. Z programu Azure powershell, zaloguj się do konta platformy Azure: za pomocą następujących poleceń cmdlet

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Pobierz listę subskrypcji. Będzie to również listę subscriptionIDs dla każdej subskrypcji. Zanotuj identyfikator subskrypcji subskrypcji, w którym chcesz utworzyć magazyn usług odzyskiwania

        Get-AzureRmSubscription
3. Ustaw subskrypcji, w którym ma być tworzony, podając identyfikator subskrypcji magazynu usług odzyskiwania

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="step-2-create-a-recovery-services-vault"></a>Krok 2. Tworzenie magazynu Usług odzyskiwania
1. Utwórz grupę zasobów usługi Azure Resource Manager, jeśli nie masz już

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Utwórz nowy magazyn usług odzyskiwania i zapisać utworzony obiekt magazynu ASR w zmiennej (zostanie później użyty). Można również pobrać za pomocą polecenia cmdlet Get-AzureRMRecoveryServicesVault tworzenie post ASR magazynu obiektów:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Krok 3: Ustaw kontekst magazynu usług odzyskiwania

Ustaw kontekst magazynu, uruchamiając poniżej polecenia.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Krok 4: Instalowanie dostawcy usługi Azure Site Recovery
1. Na maszynie VMM należy utworzyć katalog, uruchamiając następujące polecenie:

       New-Item c:\ASR -type directory
2. Wyodrębnij pliki przy użyciu pobranego dostawcy, uruchamiając następujące polecenie

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Zainstaluj dostawcę przy użyciu następujących poleceń:

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

   Poczekaj na zakończenie instalacji.
4. Zarejestruj serwer w magazynie przy użyciu następującego polecenia:

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="step-5-create-an-azure-storage-account"></a>Krok 5: Tworzenie konta magazynu platformy Azure

Jeśli nie masz konta magazynu platformy Azure, Utwórz konto włączoną replikacją geograficzną w tej samej lokalizacji geograficznej co magazyn, uruchamiając następujące polecenie:

        $StorageAccountName = "teststorageacc1"    #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"     
        $ResourceGroupName =  “myRG”             #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Należy pamiętać, że konto magazynu musi znajdować się w tym samym regionie co usługa Azure Site Recovery i musi być skojarzony z tą samą subskrypcją.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Krok 6: Zainstaluj agenta usług odzyskiwania Azure
1. Pobierz agenta usług odzyskiwania Azure na [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) i zainstalować ją na każdym serwerze hosta funkcji Hyper-V znajdujących się w chmurach programu VMM, który chcesz chronić.
2. Uruchom następujące polecenie na wszystkich hostach VMM:

       marsagentinstaller.exe /q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>Krok 7: Konfigurowanie chmury ustawienia ochrony
1. Utwórz zasady replikacji do platformy Azure, uruchamiając następujące polecenie:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

1. Pobierz kontenera ochrony, uruchamiając następujące polecenia:

       $PrimaryCloud = "testcloud"
       $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  
2. Szczegóły zasad ze zmienną przy użyciu zadania, który został utworzony i podając nazwę przyjazną zasady pobierania:

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Uruchom skojarzenie kontenera ochrony z zasadami replikacji:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  
4. Po zakończeniu zadania, uruchom następujące polecenie:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }
5. Po zakończeniu przetwarzania zadania, uruchom następujące polecenie:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Aby sprawdzić zakończenie operacji, postępuj zgodnie z instrukcjami [monitorowanie aktywności](#monitor).

## <a name="step-8-configure-network-mapping"></a>Krok 8: Konfigurowanie mapowania sieci
Przed rozpoczęciem mapowania sieci należy się upewnić, że maszyny wirtualne na źródłowym serwerze programu VMM są połączone z siecią maszyny wirtualnej. Ponadto należy utworzyć jeden lub więcej sieci wirtualnych platformy Azure.

Dowiedz się więcej o tym, jak utworzyć sieć wirtualną przy użyciu usługi Azure Resource Manager i programu PowerShell, w [tworzenie sieci wirtualnej za pomocą połączenia sieci VPN lokacja lokacja za pomocą usługi Azure Resource Manager i programu PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Należy pamiętać, że wiele sieci maszyn wirtualnych można mapować do pojedynczej sieci platformy Azure. Jeśli sieć docelowa ma wiele podsieci i jedna z tych podsieci ma taką samą nazwę jak podsieć, na którym znajduje się źródłowa maszyna wirtualna, następnie repliki maszyny wirtualnej zostanie podłączona do tej docelowej podsieci po awaryjnym. Jeśli nie ma żadnych docelowa podsieć o pasującej nazwie, maszyny wirtualnej zostaną podłączone do pierwszej podsieci w sieci.

1. Pierwsze polecenie pobiera serwerów dla bieżącego magazynu Azure Site Recovery. Polecenie zapisuje serwerów Microsoft Azure Site Recovery w zmiennej tablicy $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Drugie polecenie pobiera sieci odzyskiwania lokacji dla pierwszego serwera w tablicy $Servers. Polecenie zapisuje sieci w zmiennej $Networks.

        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

1. Trzecie polecenie pobiera sieci wirtualnych platformy Azure, a następnie tę wartość w zmiennej $AzureVmNetworks.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork
2. Końcowe polecenie cmdlet tworzy mapowanie między sieci podstawowej i sieci maszyny wirtualnej platformy Azure. Polecenie cmdlet określa sieci podstawowej jako pierwszy element $Networks. Polecenie cmdlet określa sieć maszyny wirtualnej jako pierwszy element $AzureVmNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]

## <a name="step-9-enable-protection-for-virtual-machines"></a>Krok 9: Włączanie ochrony dla maszyn wirtualnych
Po serwerów, chmur i sieci są skonfigurowane poprawnie, można włączyć ochrony dla maszyn wirtualnych w chmurze.

 Pamiętaj o następujących kwestiach:

* Maszyny wirtualne muszą spełniać wymagania dotyczące usługi Azure. Sprawdź je w [warunki wstępne i pomocy technicznej](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) w przewodniku planowania.
* Aby włączyć ochronę, systemu operacyjnego i systemu operacyjnego należy ustawić właściwości dysku dla maszyny wirtualnej. Podczas tworzenia maszyny wirtualnej w programie VMM przy użyciu szablonu maszyny wirtualnej można ustawić właściwości. Można również ustawić te właściwości dla istniejących maszyn wirtualnych na kartach **Ogólne** i **Konfiguracja sprzętu** właściwości maszyny wirtualnej. Jeśli nie ustawisz tych właściwości w programie VMM, możesz skonfigurować je w portalu Azure Site Recovery.

1. Aby włączyć ochronę, uruchom następujące polecenie, aby pobrać kontenera ochrony:

          $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName
2. Pobierz jednostki ochrony (VM), uruchamiając następujące polecenie:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer
3. Włącz DR dla maszyny Wirtualnej, uruchamiając następujące polecenie:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1

## <a name="test-your-deployment"></a>Testowanie wdrożenia
Aby przetestować wdrożenie można uruchomić testu awaryjnej dla jednej maszyny wirtualnej, lub utworzyć plan odzyskiwania uwzględniający wiele maszyn wirtualnych i uruchom test awaryjnej planu. Awaryjnej testu symuluje z mechanizmu awaryjnej i odzyskiwania w sieci izolowanej. Należy pamiętać, że:

* Jeśli chcesz się połączyć z maszyną wirtualną na platformie Azure przy użyciu pulpitu zdalnego po awaryjnej, należy włączyć Podłączanie pulpitu zdalnego na maszynie wirtualnej przed uruchomieniem testowania trybu failover.
* Po miała użyjesz publicznego adresu IP nawiązywania połączenia z maszyną wirtualną na platformie Azure przy użyciu pulpitu zdalnego. Jeśli chcesz to zrobić, upewnij się, że nie ma żadnych zasad domeny, które uniemożliwiają połączenie z maszyną wirtualną przy użyciu adresu publicznego.

Aby sprawdzić zakończenie operacji, postępuj zgodnie z instrukcjami [monitorowanie aktywności](#monitor).

### <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover
- Uruchom test trybu failover, uruchamiając następujące polecenie:

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Realizacja planowanego trybu failover
- Planowany tryb failover należy uruchomić, uruchamiając następujące polecenie:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>Uruchom nieplanowanego trybu failover
- Uruchomić nieplanowanego trybu failover, uruchamiając następujące polecenie:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

## <a name=monitor></a> Monitorowanie aktywności
Użyj następujących poleceń, aby monitorować aktywność. Należy pamiętać, że należy poczekać Between zadania do przetwarzania zakończyć.

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
[Dowiedz się więcej](/powershell/module/azurerm.recoveryservices.backup/#recovery) dotyczące usługi Azure Site Recovery za pomocą poleceń cmdlet programu PowerShell usługi Azure Resource Manager.
