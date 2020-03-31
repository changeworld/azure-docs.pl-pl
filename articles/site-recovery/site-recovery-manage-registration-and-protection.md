---
title: Usuwanie serwerów i wyłączanie ochrony | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób wyrejestrowania serwerów z magazynu odzyskiwania witryny oraz wyłączenia ochrony maszyn wirtualnych i serwerów fizycznych.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: rajanaki
ms.openlocfilehash: a411fc9a95bef595a8fc49cad77189bb88fb7661
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257630"
---
# <a name="remove-servers-and-disable-protection"></a>Usuwanie serwerów i wyłączanie ochrony

W tym artykule opisano sposób wyrejestrowania serwerów z magazynu usług odzyskiwania i wyłączania ochrony maszyn chronionych przez usługę Site Recovery.


## <a name="unregister-a--configuration-server"></a>Wyrejestrowywę serwer konfiguracji

W przypadku replikacji maszyn wirtualnych VMware lub serwerów fizycznych systemu Windows/Linux na platformę Azure można wyrejestrować niepołączony serwer konfiguracji z przechowalni w następujący sposób:

1. [Wyłącz ochronę maszyn wirtualnych](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Usuwanie łączeń lub usuwanie](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) zasad replikacji.
3. [Usuwanie serwera konfiguracji](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Wyrejestrowywania serwera programu VMM

1. Zatrzymaj replikowanie maszyn wirtualnych w chmurach na serwerze programu VMM, który chcesz usunąć.
2. Usuń wszystkie mapowania sieci używane przez chmury na serwerze programu VMM, które chcesz usunąć. W **aplikacji Site Recovery Infrastructure** > **For System Center VMM** > **Network Mapping**kliknij prawym przyciskiem myszy mapowanie sieci > **usuń**.
3. Zanotuj identyfikator serwera programu VMM.
4. Usuń zespokusij zasady replikacji z chmur na serwerze programu VMM, który chcesz usunąć.  W **witrynie Infrastruktura odzyskiwania lokacji** > dla**zasad replikacji****VMM** >  centrum systemu kliknij dwukrotnie skojarzone zasady. Kliknij prawym przyciskiem myszy > **disassociate**chmury .
5. Usuń serwer programu VMM lub aktywny węzeł. W **witrynie Infrastruktura** > odzyskiwania witryn dla**serwerów****VMM** > programu System Center kliknij prawym przyciskiem myszy serwer > **Usuń**.
6. Jeśli serwer programu VMM był w stanie rozłączony, pobierz i uruchom [skrypt oczyszczania](https://aka.ms/asr-cleanup-script-vmm) na serwerze programu VMM. Otwórz program PowerShell z opcją **Uruchom jako administrator,** aby zmienić zasady wykonywania domyślnego (LocalMachine). W skrypcie określ identyfikator serwera programu VMM, który chcesz usunąć. Skrypt usuwa informacje o rejestracji i parowaniu w chmurze z serwera.
5. Uruchom skrypt oczyszczania na dowolnym pomocniczym serwerze programu VMM.
6. Uruchom skrypt oczyszczania na innych pasywnych węzłach klastra programu VMM, które mają zainstalowanego dostawcę.
7. Odinstaluj dostawcę ręcznie na serwerze programu VMM. Jeśli masz klaster, usuń ze wszystkich węzłów.
8. Jeśli maszyny wirtualne były replikowane na platformę Azure, należy odinstalować agenta usług odzyskiwania firmy Microsoft z hostów funkcji Hyper-V w usuniętych chmurach.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Wyrejestrowywładnia hosta funkcji Hyper-V w witrynie funkcji Hyper-V

Hosty funkcji Hyper-V, które nie są zarządzane przez program VMM, są zbierane w witrynie funkcji Hyper-V. Usuń hosta w witrynie funkcji Hyper-V w następujący sposób:

1. Wyłącz replikację dla maszyn wirtualnych funkcji Hyper-V znajdujących się na hoście.
2. Odłączanie zasad dla witryny funkcji Hyper-V. W **witrynie Infrastruktury** > odzyskiwania lokacji dla**zasad replikacji**witryn >  **funkcji Hyper-V**kliknij dwukrotnie skojarzone zasady. Kliknij prawym przyciskiem myszy witrynę > **Disassociate**.
3. Usuń hosty funkcji Hyper-V. W **witrynie Infrastruktury** > odzyskiwania witryn**dla hostów Hyper-V lokacji** > **Hyper-V**kliknij prawym przyciskiem myszy serwer > **Usuń**.
4. Usuń witrynę funkcji Hyper-V po usunięciu z niej wszystkich hostów. W **witrynie Infrastruktury** > odzyskiwania witryn dla witryn > **Hyper-V****witryn Hyper-V**kliknij prawym przyciskiem myszy witrynę > **Usuń**.
5. Jeśli host funkcji Hyper-V był w stanie **Rozłączony,** uruchom następujący skrypt na każdym usuniętym hoście funkcji Hyper-V. Skrypt czyści ustawienia na serwerze i wyrejestrowyje go z repozytorium.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Wyłącz ochronę maszyny Wirtualnej VMware lub serwera fizycznego (VMware na platformie Azure)

1. W **obszarze Elementy replikowane chronione** > **kliknij**prawym przyciskiem myszy komputer > **Wyłącz replikację**.
2. W **obszarze Wyłącz replikację** wybierz jedną z następujących opcji:
    - **Wyłącz replikację i usuń (zalecane)** — ta opcja usuwa replikowany element z usługi Azure Site Recovery, a replikacja komputera zostanie zatrzymana. Konfiguracja replikacji na serwerze konfiguracji jest czyszczona, a rozliczenia usługi Site Recovery dla tego chronionego serwera są zatrzymywane. Należy zauważyć, że tej opcji można używać tylko wtedy, gdy serwer konfiguracji jest w stanie połączenia.
    - **Usuń** — ta opcja ma być używana tylko wtedy, gdy środowisko źródłowe jest usuwane lub niedostępne (nie jest podłączone). Spowoduje to usunięcie zdrekonowanego elementu z usługi Azure Site Recovery (rozliczenia są zatrzymane). Konfiguracja replikacji na serwerze konfiguracji **nie zostanie** oczyszczona. 

> [!NOTE]
> W obu opcjach usługa mobilności nie zostanie odinstalowana z chronionych serwerów, należy ją odinstalować ręcznie. Jeśli planujesz ponownie chronić serwer przy użyciu tego samego serwera konfiguracji, możesz pominąć odinstalowanie usługi mobilności.

> [!NOTE]
> Jeśli już po awarii maszyny wirtualnej i jest uruchomiona na platformie Azure, należy pamiętać, że wyłączyć ochronę nie usuwa / wpływa na maszynę wirtualną po awarii.
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Wyłącz ochronę maszyny Wirtualnej platformy Azure (platformy Azure na platformie Azure)

-  W **obszarze Elementy replikowane chronione** > **kliknij**prawym przyciskiem myszy komputer > **Wyłącz replikację**.
> [!NOTE]
> usługa mobilności nie zostanie odinstalowana z chronionych serwerów, należy ją odinstalować ręcznie. Jeśli planujesz ponownie chronić serwer, możesz pominąć odinstalowanie usługi mobilności.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Wyłącz ochronę maszyny wirtualnej funkcji Hyper-V (funkcja Hyper-V na platformie Azure)

> [!NOTE]
> Tej procedury należy użyć, jeśli replikujesz maszyny wirtualne funkcji Hyper V na platformę Azure bez serwera programu VMM. Jeśli replikujesz maszyny wirtualne przy użyciu **scenariusza Programu VMM do platformy Azure,** postępuj zgodnie z instrukcjami Wyłącz ochronę replikacji maszyny wirtualnej funkcji Hyper-V przy użyciu scenariusza programu Windows Center VMM na platformę Azure

1. W **obszarze Elementy replikowane chronione** > **kliknij**prawym przyciskiem myszy komputer > **Wyłącz replikację**.
2. W **obszarze Wyłącz replikację**można wybrać następujące opcje:
   - **Wyłącz replikację i usuń (zalecane)** — ta opcja usuwa replikowany element z usługi Azure Site Recovery, a replikacja komputera zostanie zatrzymana. Konfiguracja replikacji na lokalnej maszynie wirtualnej zostanie wyczyszczona, a rozliczenia usługi Site Recovery dla tego chronionego serwera zostaną zatrzymane.
   - **Usuń** — ta opcja ma być używana tylko wtedy, gdy środowisko źródłowe jest usuwane lub niedostępne (nie jest podłączone). Spowoduje to usunięcie zdrekonowanego elementu z usługi Azure Site Recovery (rozliczenia są zatrzymane). Konfiguracja replikacji na lokalnej maszynie wirtualnej **nie zostanie** wyczyszczona. 

 > [!NOTE]
     > Jeśli wybrano opcję **Usuń,** uruchom następujący zestaw skryptów, aby wyczyścić ustawienia replikacji lokalnego serwera Funkcji Hyper-V.

> [!NOTE]
> Jeśli już po awarii maszyny wirtualnej i jest uruchomiona na platformie Azure, należy pamiętać, że wyłączyć ochronę nie usuwa / wpływa na maszynę wirtualną po awarii.

1. Na źródłowym serwerze hosta funkcji Hyper-V, aby usunąć replikację maszyny wirtualnej. Zastępowanie programu SQLVM1 nazwą maszyny wirtualnej i uruchamianie skryptu z administracyjnego programu PowerShell

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Wyłącz ochronę replikacji maszyny wirtualnej funkcji Hyper-V na platformie Azure przy użyciu scenariusza programu Windows Center VMM na platformie Azure

1. W **obszarze Elementy replikowane chronione** > **kliknij**prawym przyciskiem myszy komputer > **Wyłącz replikację**.
2. W **obszarze Wyłącz replikację**wybierz jedną z następujących opcji:

   - **Wyłącz replikację i usuń (zalecane)** — ta opcja usuwa replikowany element z usługi Azure Site Recovery, a replikacja komputera zostanie zatrzymana. Konfiguracja replikacji na lokalnej maszynie wirtualnej jest czyszczona, a rozliczenia usługi Site Recovery dla tego chronionego serwera są zatrzymywane.
   - **Usuń** — ta opcja ma być używana tylko wtedy, gdy środowisko źródłowe jest usuwane lub niedostępne (nie jest podłączone). Spowoduje to usunięcie zdrekonowanego elementu z usługi Azure Site Recovery (rozliczenia są zatrzymane). Konfiguracja replikacji na lokalnej maszynie wirtualnej **nie zostanie** wyczyszczona. 

     > [!NOTE]
     > Jeśli wybrano opcję **Usuń,** a następnie ocieplić następujące skrypty, aby wyczyścić ustawienia replikacji lokalnego serwera programu VMM Server.
3. Uruchom ten skrypt na źródłowym serwerze programu VMM przy użyciu programu PowerShell (wymagane uprawnienia administratora) z konsoli programu VMM. Zastąp symbol zastępczy **SQLVM1** nazwą maszyny wirtualnej.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Powyższe kroki wyczyszczają ustawienia replikacji na serwerze programu VMM. Aby zatrzymać replikację maszyny wirtualnej uruchomionej na serwerze hosta funkcji Hyper-V, uruchom ten skrypt. Zastąp sqlvm1 nazwą maszyny wirtualnej i host01.contoso.com nazwą serwera hosta funkcji Hyper-V.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Wyłącz ochronę replikowania maszyny wirtualnej funkcji Hyper-V do pomocniczego serwera programu VMM przy użyciu scenariusza programu Programu VMM programu System Center do programu VMM

1. W **obszarze Elementy replikowane chronione** > **kliknij**prawym przyciskiem myszy komputer > **Wyłącz replikację**.
2. W **obszarze Wyłącz replikację**wybierz jedną z następujących opcji:

   - **Wyłącz replikację i usuń (zalecane)** — ta opcja usuwa replikowany element z usługi Azure Site Recovery, a replikacja komputera zostanie zatrzymana. Konfiguracja replikacji na lokalnej maszynie wirtualnej jest czyszczona, a rozliczenia usługi Site Recovery dla tego chronionego serwera są zatrzymywane.
   - **Usuń** — ta opcja ma być używana tylko wtedy, gdy środowisko źródłowe jest usuwane lub niedostępne (nie jest podłączone). Spowoduje to usunięcie zdrekonowanego elementu z usługi Azure Site Recovery (rozliczenia są zatrzymane). Konfiguracja replikacji na lokalnej maszynie wirtualnej **nie zostanie** wyczyszczona. Uruchom następujący zestaw skryptów, aby wyczyścić ustawienia replikacji na lokalnych maszynach wirtualnych.
     > [!NOTE]
     > Jeśli wybrano opcję **Usuń,** a następnie ocieplić następujące skrypty, aby wyczyścić ustawienia replikacji lokalnego serwera programu VMM Server.

3. Uruchom ten skrypt na źródłowym serwerze programu VMM przy użyciu programu PowerShell (wymagane uprawnienia administratora) z konsoli programu VMM. Zastąp symbol zastępczy **SQLVM1** nazwą maszyny wirtualnej.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Na pomocniczym serwerze programu VMM uruchom ten skrypt, aby wyczyścić ustawienia pomocniczej maszyny wirtualnej:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. Na pomocniczym serwerze programu VMM odśwież maszyny wirtualne na serwerze hosta funkcji Hyper-V, aby pomocnicza maszyna wirtualna została wykryta ponownie w konsoli programu VMM.
6. Powyższe kroki wyczyszczają ustawienia replikacji na serwerze programu VMM. Jeśli chcesz zatrzymać replikację dla maszyny wirtualnej, uruchom następujący skrypt o podstawowych i pomocniczych maszynach wirtualnych. Zastąp SQLVM1 nazwą maszyny wirtualnej.

        Remove-VMReplication –VMName “SQLVM1”




