---
title: Usuń serwery i Wyłącz ochronę | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak wyrejestrować serwery z magazynu usługi Site Recovery i wyłączyć ochrony dla maszyn wirtualnych i serwerów fizycznych.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: 16a5eaac1138d328f81cfa7d50f8705da867e352
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
ms.locfileid: "29806419"
---
# <a name="remove-servers-and-disable-protection"></a>Usuwanie serwerów i wyłączanie ochrony

W tym artykule opisano, jak wyrejestrować serwery z magazynu usług odzyskiwania i wyłączanie ochrony dla maszyny chronione przez usługę Site Recovery.


## <a name="unregister-a--configuration-server"></a>Wyrejestrowanie serwera konfiguracji

Jeśli replikujesz maszyny wirtualne VMware lub serwerów fizycznych z systemem Windows lub Linux na platformie Azure można wyrejestrować serwera konfiguracji odłączony z magazynu w następujący sposób:

1. [Wyłącz ochronę maszyn wirtualnych](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Usuń skojarzenie lub usunąć](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) zasady replikacji.
3. [Usuń serwer konfiguracji](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Wyrejestrowanie serwera programu VMM

1. Zatrzymywanie replikacji maszyn wirtualnych w chmurach na serwerze programu VMM, który chcesz usunąć.
2. Usuń wszelkie mapowania sieci używane przez chmury na serwerze programu VMM, który chcesz usunąć. W **infrastruktura usługi Site Recovery** > **dla programu System Center VMM** > **mapowania sieci**, kliknij prawym przyciskiem myszy mapowania sieci > **usunąć**.
3. Należy pamiętać, identyfikator serwera VMM.
4. Usuń skojarzenia zasad replikacji z chmury na serwerze programu VMM, który chcesz usunąć.  W **infrastruktura usługi Site Recovery** > **dla programu System Center VMM** >  **zasady replikacji**, kliknij dwukrotnie skojarzonych zasad. Kliknij prawym przyciskiem myszy chmurę > **usuwanie skojarzenia**.
5. Usuń serwer programu VMM lub aktywnego węzła. W **infrastruktura usługi Site Recovery** > **dla programu System Center VMM** > **serwery VMM**, kliknij prawym przyciskiem myszy serwer > **usunąć**.
6. Jeśli serwer VMM znajduje się w stanie rozłączenia, następnie Pobierz i uruchom [skrypt czyszczący](http://aka.ms/asr-cleanup-script-vmm) na serwerze programu VMM. Otwórz program PowerShell z **Uruchom jako Administrator** opcję, aby zmienić zasady wykonywania w zakresie domyślne (LocalMachine). W skrypcie Określ identyfikator serwera VMM, który chcesz usunąć. Skrypt usuwa rejestrację i informacji z serwera parowania w chmurze.
5. Uruchom skrypt czyszczący na dowolnym serwerze pomocniczym programu VMM.
6. Uruchom skrypt czyszczący na inne pasywnych węzłach klastra VMM, które mają zainstalowanego dostawcy.
7. Odinstalowanie dostawcy usługi ręcznie na serwerze programu VMM. Jeśli masz klaster, Usuń z dowolnych węzłów.
8. Jeśli maszyny wirtualne zostały replikację do platformy Azure, należy najpierw odinstalować agenta usług odzyskiwania Microsoft z hostów funkcji Hyper-V w chmurach usunięte.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Wyrejestruj hosta funkcji Hyper-V w lokacji funkcji Hyper-V

Hosty funkcji Hyper-V, które nie są zarządzane przez program VMM są zbierane w lokacji funkcji Hyper-V. Usuń hosta w lokacji funkcji Hyper-V w następujący sposób:

1. Wyłącz replikację dla maszyn wirtualnych funkcji Hyper-V znajdujących się na hoście.
2. Usuń skojarzenie zasad dla lokacji funkcji Hyper-V. W **infrastruktura usługi Site Recovery** > **dla Lokacje funkcji Hyper-V** >  **zasady replikacji**, kliknij dwukrotnie skojarzonych zasad. Kliknij prawym przyciskiem myszy witrynę > **usuwanie skojarzenia**.
3. Usuwanie hostów funkcji Hyper-V. W **infrastruktura usługi Site Recovery** > **dla Lokacje funkcji Hyper-V** > **hosty funkcji Hyper-V**, kliknij prawym przyciskiem myszy serwer > **usunąć** .
4. Po wszystkie hosty zostały usunięte z niego, należy usunąć lokacji funkcji Hyper-V. W **infrastruktura usługi Site Recovery** > **dla Lokacje funkcji Hyper-V** > **Lokacje funkcji Hyper-V**, kliknij prawym przyciskiem myszy witrynę > **usunąć** .
5. Jeśli na hoście funkcji Hyper-V znajduje się w **Rozłączono** stanu, a następnie uruchom następujący skrypt na każdym hoście funkcji Hyper-V, które zostało usunięte. Skrypt czyści ustawienia na serwerze i wyrejestrowuje je z magazynu.



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
                "Registry keys removed."
            }

            # First retrive all the certificates to be deleted
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
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd



## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Wyłącz ochronę dla maszyny Wirtualnej VMware lub serwerów fizycznych (VMware do platformy Azure)

1. W **chronione elementy** > **elementy replikowane**, kliknij prawym przyciskiem myszy maszynę > **Wyłącz replikację**.
2. W **Wyłącz replikację** wybierz jedną z następujących opcji:
    - **Wyłącz replikację i Usuń (zalecane)** — ta opcja usunięcia elementu replikowane z usługi Azure Site Recovery i zatrzymania replikacji dla maszyny. Konfiguracja replikacji na serwerze konfiguracji jest wyczyszczone, a Site Recovery rozliczeń dla tego serwera chronionego została zatrzymana.
    - **Usuń** — ta opcja ma można używać tylko wtedy, gdy środowisko źródłowe usunięte lub jest niedostępny (nie połączono). Spowoduje to usunięcie elementu replikowane z usługi Azure Site Recovery (rozliczeń jest zatrzymany). Konfiguracja replikacji na serwerze konfiguracji **nie** można wyczyścić. 

> [!NOTE]
> W obu opcji usługa mobilności nie zostanie odinstalowane z chronionych serwerów należy najpierw odinstalować go ręcznie. Jeśli planujesz chronić serwer ponownie, używając tego samego serwera konfiguracji, można pominąć, odinstalowywanie usługi mobilności.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Wyłącz ochronę dla maszyny wirtualnej funkcji Hyper-V (Hyper-V do platformy Azure)

> [!NOTE]
> Użyj tej procedury, Jeśli replikujesz maszyny wirtualne funkcji Hyper-V do platformy Azure bez serwera VMM. Jeśli replikowania maszyn wirtualnych przy użyciu **programu System Center VMM do platformy Azure** scenariusza, postępuj zgodnie z instrukcjami [Wyłącz ochronę dla maszyny wirtualnej z funkcją Hyper-V replikacji, za pomocą programu System Center VMM do Scenariusz Azure](#disable-protection-for-a-hyper-v-virtual-machine-replicating-using-the-system-centet-vmm-to-azure-scenario)

1. W **chronione elementy** > **elementy replikowane**, kliknij prawym przyciskiem myszy maszynę > **Wyłącz replikację**.
2. W **Wyłącz replikację**, można wybrać następujące opcje:
     - **Wyłącz replikację i Usuń (zalecane)** — ta opcja usunięcia elementu replikowane z usługi Azure Site Recovery i zatrzymania replikacji dla maszyny. Konfiguracja replikacji na maszynie wirtualnej lokalnymi zostaną wyczyszczone i rozliczeń usługi Site Recovery na tym serwerze chronionym jest zatrzymana.
    - **Usuń** — ta opcja ma można używać tylko wtedy, gdy środowisko źródłowe usunięte lub jest niedostępny (nie połączono). Spowoduje to usunięcie elementu replikowane z usługi Azure Site Recovery (rozliczeń jest zatrzymany). Konfiguracja replikacji na maszynie wirtualnej lokalnymi **nie** można wyczyścić. 

    > [!NOTE]
    > Jeśli została wybrana opcja **Usuń** opcję Uruchom następujący zestaw skryptów, aby wyczyścić ustawienia replikacji funkcji Hyper-V Server lokalnymi.
1. W funkcji Hyper-V hosta serwera źródłowego, aby usunąć replikację dla maszyny wirtualnej. Zamień SQLVM1 nazwę maszyny wirtualnej, a następnie uruchom skrypt z administracyjne programu PowerShell


    
    $vmName = "SQLVM1" $vm = Get-WmiObject - Namespace "root\virtualization\v2"-zapytania "Wybierz * z Msvm_ComputerSystem gdzie ElementName ="$vmName"" $replicationService = Get-WmiObject - Namespace "root\virtualization\v2"-"Wybierz * z Msvm_ zapytania $ReplicationService.RemoveReplicationRelationship($vm.__PATH) ReplicationService"
    

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Wyłącz ochronę dla maszyny wirtualnej funkcji Hyper-V replikację do platformy Azure przy użyciu programu System Center VMM do platformy Azure scenariusza

1. W **chronione elementy** > **elementy replikowane**, kliknij prawym przyciskiem myszy maszynę > **Wyłącz replikację**.
2. W **Wyłącz replikację**, wybierz jedną z następujących opcji:

    - **Wyłącz replikację i Usuń (zalecane)** — ta opcja usunięcia elementu replikowane z usługi Azure Site Recovery i zatrzymania replikacji dla maszyny. Konfiguracja replikacji na lokalnej maszynie wirtualnej jest wyczyszczone, a Site Recovery rozliczeń dla tego serwera chronionego została zatrzymana.
    - **Usuń** — ta opcja ma można używać tylko wtedy, gdy środowisko źródłowe usunięte lub jest niedostępny (nie połączono). Spowoduje to usunięcie elementu replikowane z usługi Azure Site Recovery (rozliczeń jest zatrzymany). Konfiguracja replikacji na maszynie wirtualnej lokalnymi **nie** można wyczyścić. 

    > [!NOTE]
    > Jeśli została wybrana opcja **Usuń** opcji, a następnie tun następujące skrypty wyczyścić ustawienia replikacji lokalnego serwera programu VMM.
3. Uruchom ten skrypt na źródłowym serwerze programu VMM, przy użyciu programu PowerShell (wymagane administratora uprawnienia umożliwiające) w konsoli programu VMM. Zastąp symbol zastępczy **SQLVM1** o nazwie maszyny wirtualnej.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Powyższe kroki Wyczyść ustawienia replikacji na serwerze programu VMM. Aby zatrzymać replikację dla maszyny wirtualnej uruchomionej na serwerze hosta funkcji Hyper-V, uruchom ten skrypt. Zamień SQLVM1 nazwę maszyny wirtualnej, a host01.contoso.com z nazwą serwera hosta funkcji Hyper-V.

    
    $vmName = "SQLVM1" $hostName = "host01.contoso.com" $vm = Get-WmiObject - Namespace "root\virtualization\v2"-zapytania "Wybierz * z Msvm_ComputerSystem gdzie ElementName ="$vmName"" - computername $hostName $replicationService = Get-WmiObject - Namespace "root\virtualization\v2"-"Wybierz * z Msvm_ReplicationService" - computername $hostName $replicationService.RemoveReplicationRelationship($vm.__PATH) zapytania
    
       
 
## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Wyłącz ochronę dla maszyny wirtualnej funkcji Hyper-V replikację na pomocniczym serwerze programu VMM przy użyciu programu System Center VMM do programu VMM scenariusza

1. W **chronione elementy** > **elementy replikowane**, kliknij prawym przyciskiem myszy maszynę > **Wyłącz replikację**.
2. W **Wyłącz replikację**, wybierz jedną z następujących opcji:

    - **Wyłącz replikację i Usuń (zalecane)** — ta opcja usunięcia elementu replikowane z usługi Azure Site Recovery i zatrzymania replikacji dla maszyny. Konfiguracja replikacji na lokalnej maszynie wirtualnej jest wyczyszczone, a Site Recovery rozliczeń dla tego serwera chronionego została zatrzymana.
    - **Usuń** — ta opcja ma można używać tylko wtedy, gdy środowisko źródłowe usunięte lub jest niedostępny (nie połączono). Spowoduje to usunięcie elementu replikowane z usługi Azure Site Recovery (rozliczeń jest zatrzymany). Konfiguracja replikacji na maszynie wirtualnej lokalnymi **nie** można wyczyścić. Uruchom następujący zestaw skryptów, aby wyczyścić maszyn wirtualnych lokalnie ustawienia replikacji.
> [!NOTE]
> Jeśli została wybrana opcja **Usuń** opcji, a następnie tun następujące skrypty wyczyścić ustawienia replikacji lokalnego serwera programu VMM.

3. Uruchom ten skrypt na źródłowym serwerze programu VMM, przy użyciu programu PowerShell (wymagane administratora uprawnienia umożliwiające) w konsoli programu VMM. Zastąp symbol zastępczy **SQLVM1** o nazwie maszyny wirtualnej.

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Na pomocniczym serwerze programu VMM Uruchom ten skrypt, aby wyczyścić ustawienia dla pomocniczej maszyny wirtualnej:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. Na pomocniczym serwerze programu VMM należy odświeżyć maszyn wirtualnych na serwerze hosta funkcji Hyper-V, aby dodatkowej maszyny Wirtualnej pobiera wykryte ponownie w konsoli programu VMM.
6. Powyższe kroki Wyczyść ustawienia replikacji na serwerze programu VMM. Jeśli chcesz zatrzymać replikację dla maszyny wirtualnej, uruchom następujący skrypt Niestety głównych i dodatkowych maszyn wirtualnych. Zamień SQLVM1 nazwę maszyny wirtualnej.

        Remove-VMReplication –VMName “SQLVM1”




