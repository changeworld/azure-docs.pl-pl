---
title: Usuwanie serwerów i wyłączanie ochrony | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wyrejestrować serwerów z magazynu usługi Site Recovery i Wyłącz ochronę dla maszyn wirtualnych i serwerów fizycznych.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: c22acb1ae82e5c1e781598e8545c7f1625cc1c09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61277165"
---
# <a name="remove-servers-and-disable-protection"></a>Usuwanie serwerów i wyłączanie ochrony

W tym artykule opisano sposób Wyrejestrowywanie serwerów z magazynu usługi Recovery Services oraz wyłączyć ochronę maszyny chronione przez usługę Site Recovery.


## <a name="unregister-a--configuration-server"></a>Wyrejestrowywanie serwera konfiguracji

Jeśli replikujesz maszyny wirtualne VMware lub serwerach fizycznych systemu Windows/Linux na platformie Azure, można wyrejestrować serwer konfiguracji niepołączonych z magazynu w następujący sposób:

1. [Wyłącz ochronę maszyn wirtualnych](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Usuwanie skojarzenia lub usuń](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) zasady replikacji.
3. [Usuwanie serwera konfiguracji](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Wyrejestruj serwer programu VMM

1. Zatrzymaj replikację maszyn wirtualnych w chmurach na serwerze programu VMM, który chcesz usunąć.
2. Usuń wszystkie mapowania sieci używane przez chmur na serwerze VMM, który chcesz usunąć. W **infrastruktura usługi Site Recovery** > **dla programu System Center VMM** > **mapowania sieci**, kliknij prawym przyciskiem myszy mapowania sieci > **Usuń**.
3. Należy pamiętać, identyfikator serwera programu VMM.
4. Usuń skojarzenie zasad replikacji z chmur na serwerze programu VMM, który chcesz usunąć.  W **infrastruktura usługi Site Recovery** > **dla programu System Center VMM** >  **zasady replikacji**, kliknij dwukrotnie skojarzonych zasad. Kliknij prawym przyciskiem myszy chmurę > **usuwanie skojarzenia**.
5. Usuń serwer programu VMM lub aktywnego węzła. W **infrastruktura usługi Site Recovery** > **dla programu System Center VMM** > **serwery VMM**, kliknij prawym przyciskiem myszy serwer > **Usuń** .
6. Jeśli serwer programu VMM był w stanie rozłączenia, następnie Pobierz i uruchom [skrypt czyszczący](https://aka.ms/asr-cleanup-script-vmm) na serwerze programu VMM. Otwórz program PowerShell z **Uruchom jako Administrator** opcję, aby zmienić zasady wykonywania dla zakresu domyślnej (LocalMachine). W skrypcie należy określić identyfikator serwera programu VMM, który chcesz usunąć. Skrypt usuwa rejestrację i informacji z serwera parowanie chmury.
5. Uruchom skrypt czyszczenia w dowolnym pomocniczy serwer programu VMM.
6. Uruchom skrypt czyszczenia w innych pasywne węzły klastra VMM, które ma zainstalowanego dostawcy.
7. Odinstaluj dostawcę ręcznie na serwerze programu VMM. Jeśli masz klaster, należy usunąć ze wszystkich węzłów.
8. Jeśli maszyny wirtualne zostały replikację do platformy Azure, należy najpierw odinstalować agenta usług odzyskiwania Microsoft z hostów funkcji Hyper-V w chmurach usunięte.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Wyrejestrować hosta funkcji Hyper-V w lokacji funkcji Hyper-V

Hosty funkcji Hyper-V, które nie są zarządzane przez program VMM są zbierane do lokacji funkcji Hyper-V. Usuwanie hosta w lokacji funkcji Hyper-V w następujący sposób:

1. Wyłącz replikację dla maszyn wirtualnych funkcji Hyper-V na hoście.
2. Usuń skojarzenia zasad dla lokacji funkcji Hyper-V. W **infrastruktura usługi Site Recovery** > **dla lokacji funkcji Hyper-V** >  **zasady replikacji**, kliknij dwukrotnie skojarzonych zasad. Kliknij prawym przyciskiem myszy lokacji > **usuwanie skojarzenia**.
3. Usuwanie hostów funkcji Hyper-V. W **infrastruktura usługi Site Recovery** > **dla lokacji funkcji Hyper-V** > **hosty funkcji Hyper-V**, kliknij prawym przyciskiem myszy serwer > **Usuń** .
4. Usuń lokację funkcji Hyper-V, po wszystkie hosty zostały usunięte z niej. W **infrastruktura usługi Site Recovery** > **dla lokacji funkcji Hyper-V** > **lokacji funkcji Hyper-V**, kliknij prawym przyciskiem myszy lokacji > **Usuń** .
5. Jeśli dostęp do hosta funkcji Hyper-V w **Disconnected** stanu, a następnie uruchom następujący skrypt na każdym hoście funkcji Hyper-V, która została usunięta. Skrypt czyści ustawienia na serwerze i wyrejestrowuje go z magazynu.


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


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Wyłącz ochronę dla maszyny Wirtualnej VMware lub serwera fizycznego (program VMware do platformy Azure)

1. W **chronione elementy** > **zreplikowane elementy**, kliknij prawym przyciskiem myszy maszyny > **Wyłącz replikację**.
2. W **Wyłącz replikację** wybierz jedną z następujących opcji:
    - **Wyłącz replikację i Usuń (zalecane)** — ta opcja to usunięcie replikowanego elementu z usługi Azure Site Recovery i replikacji dla maszyny została zatrzymana. Konfiguracja replikacji na serwerze konfiguracji jest wyczyszczone, a Site Recovery rozliczenia dla tego serwera chronionego została zatrzymana. Należy pamiętać, że ta opcja tylko mogą być używane, gdy serwer konfiguracji jest w stanie połączonym.
    - **Usuń** — tę opcję, powinien być używane tylko wtedy, gdy środowisko źródłowe został usunięty lub jest niedostępny (nie połączono). Spowoduje to usunięcie replikowanego elementu z usługi Azure Site Recovery (rozliczanie kończy się). Konfiguracja replikacji na serwerze konfiguracji **nie będzie** wyczyszczone. 

> [!NOTE]
> W obie opcje, które usługi mobilności nie zostanie odinstalowane z chronionych serwerów należy najpierw odinstalować go ręcznie. Jeśli planujesz chronić serwer ponownie, używając tego samego serwera konfiguracji, można pominąć, odinstalowanie usługi mobilności.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Wyłącz ochronę dla maszyny wirtualnej funkcji Hyper-V (funkcji Hyper-V do platformy Azure)

> [!NOTE]
> Użyj tej procedury, Jeśli replikujesz maszyny wirtualne funkcji Hyper-V na platformę Azure bez serwera programu VMM. Jeśli replikujesz maszyny wirtualne przy użyciu **programu System Center VMM do platformy Azure** scenariusza, a następnie postępuj zgodnie z instrukcjami wyłączenia ochrony dla funkcji Hyper-V wirtualnych maszyny replikowania, za pomocą programu System Center VMM na platformę Azure

1. W **chronione elementy** > **zreplikowane elementy**, kliknij prawym przyciskiem myszy maszyny > **Wyłącz replikację**.
2. W **Wyłącz replikację**, można wybrać następujące opcje:
   - **Wyłącz replikację i Usuń (zalecane)** — ta opcja to usunięcie replikowanego elementu z usługi Azure Site Recovery i replikacji dla maszyny została zatrzymana. Konfiguracja replikacji na maszynie wirtualnej w środowisku lokalnym zostaną wyczyszczone i rozliczeń Site Recovery dla tego serwera chronionego została zatrzymana.
   - **Usuń** — tę opcję, powinien być używane tylko wtedy, gdy środowisko źródłowe został usunięty lub jest niedostępny (nie połączono). Spowoduje to usunięcie replikowanego elementu z usługi Azure Site Recovery (rozliczanie kończy się). Konfiguracja replikacji na maszynie wirtualnej w środowisku lokalnym **nie będzie** wyczyszczone. 

     > [!NOTE]
     > Jeśli została wybrana opcja **Usuń** opcję, następnie uruchom następujący zestaw skryptów, aby wyczyścić ustawienia replikacji w środowisku lokalnym serwerze funkcji Hyper-V.
1. W funkcji Hyper-V hosta serwera źródłowego, aby usunąć replikację dla maszyny wirtualnej. Zamień SQLVM1 nazwę maszyny wirtualnej, a następnie uruchom skrypt z administracyjne programu PowerShell

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Wyłącz ochronę dla maszyny wirtualnej funkcji Hyper-V, replikowanych do platformy Azure przy użyciu programu System Center VMM na platformę Azure

1. W **chronione elementy** > **zreplikowane elementy**, kliknij prawym przyciskiem myszy maszyny > **Wyłącz replikację**.
2. W **Wyłącz replikację**, wybierz jedną z następujących opcji:

   - **Wyłącz replikację i Usuń (zalecane)** — ta opcja to usunięcie replikowanego elementu z usługi Azure Site Recovery i replikacji dla maszyny została zatrzymana. Konfiguracja replikacji na maszynie wirtualnej w środowisku lokalnym jest wyczyszczone, a Site Recovery rozliczenia dla tego serwera chronionego została zatrzymana.
   - **Usuń** — tę opcję, powinien być używane tylko wtedy, gdy środowisko źródłowe został usunięty lub jest niedostępny (nie połączono). Spowoduje to usunięcie replikowanego elementu z usługi Azure Site Recovery (rozliczanie kończy się). Konfiguracja replikacji na maszynie wirtualnej w środowisku lokalnym **nie będzie** wyczyszczone. 

     > [!NOTE]
     > Jeśli została wybrana opcja **Usuń** opcji, a następnie tun następujących skryptów, aby wyczyścić ustawień replikacji w środowisku lokalnym serwerze programu VMM.
3. Uruchom ten skrypt na źródłowym serwerze programu VMM przy użyciu programu PowerShell (wymagane są uprawnienia administratora) z konsoli programu VMM. Zastąp symbol zastępczy **SQLVM1** o nazwie maszyny wirtualnej.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Powyższe kroki Wyczyść ustawienia replikacji na serwerze programu VMM. Aby zatrzymać replikację dla maszyny wirtualnej z systemem na serwerze hosta funkcji Hyper-V, uruchom ten skrypt. Zamień SQLVM1 nazwę maszyny wirtualnej i host01.contoso.com o nazwie serwer hosta funkcji Hyper-V.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Wyłącz ochronę dla maszyny wirtualnej funkcji Hyper-V replikowanie na serwer pomocniczy programu VMM przy użyciu programu System Center VMM do programu VMM scenariusza

1. W **chronione elementy** > **zreplikowane elementy**, kliknij prawym przyciskiem myszy maszyny > **Wyłącz replikację**.
2. W **Wyłącz replikację**, wybierz jedną z następujących opcji:

   - **Wyłącz replikację i Usuń (zalecane)** — ta opcja to usunięcie replikowanego elementu z usługi Azure Site Recovery i replikacji dla maszyny została zatrzymana. Konfiguracja replikacji na maszynie wirtualnej w środowisku lokalnym jest wyczyszczone, a Site Recovery rozliczenia dla tego serwera chronionego została zatrzymana.
   - **Usuń** — tę opcję, powinien być używane tylko wtedy, gdy środowisko źródłowe został usunięty lub jest niedostępny (nie połączono). Spowoduje to usunięcie replikowanego elementu z usługi Azure Site Recovery (rozliczanie kończy się). Konfiguracja replikacji na maszynie wirtualnej w środowisku lokalnym **nie będzie** wyczyszczone. Uruchom następujący zestaw skryptów, aby wyczyścić maszyn wirtualnych w środowisku lokalnym ustawienia replikacji.
     > [!NOTE]
     > Jeśli została wybrana opcja **Usuń** opcji, a następnie tun następujących skryptów, aby wyczyścić ustawień replikacji w środowisku lokalnym serwerze programu VMM.

3. Uruchom ten skrypt na źródłowym serwerze programu VMM przy użyciu programu PowerShell (wymagane są uprawnienia administratora) z konsoli programu VMM. Zastąp symbol zastępczy **SQLVM1** o nazwie maszyny wirtualnej.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Na pomocniczym serwerze programu VMM Uruchom ten skrypt, aby wyczyścić ustawienia dla pomocniczej maszyny wirtualnej:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. Na pomocniczym serwerze programu VMM należy odświeżyć maszyn wirtualnych na serwerze hosta funkcji Hyper-V, aby pomocniczej maszyny Wirtualnej pobiera wykryte ponownie w konsoli programu VMM.
6. Powyższe kroki Wyczyść ustawienia replikacji na serwerze programu VMM. Jeśli chcesz zatrzymać replikację dla maszyny wirtualnej, uruchom następujący skrypt o głównej i dodatkowej maszynie wirtualnej. Zamień SQLVM1 nazwę maszyny wirtualnej.

        Remove-VMReplication –VMName “SQLVM1”




