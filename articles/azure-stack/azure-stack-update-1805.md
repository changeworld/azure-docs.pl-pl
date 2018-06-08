---
title: Aktualizacja Azure stosu 1805 | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w aktualizacji 1805 do stosu Azure zintegrowanych systemów, łącznie z znane problemy i pobierania aktualizacji.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: cb6c4d5cd1d63403c102f7d09741eba4932a79bb
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850512"
---
# <a name="azure-stack-1805-update"></a>Azure aktualizacji 1805 stosu

*Dotyczy: Azure stosu zintegrowane systemy*

W tym artykule opisano ulepszeń i poprawek w pakiecie aktualizacji 1805 znane problemy dotyczące tej wersji i pobierania aktualizacji. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i problemy z kompilacją (po instalacji).

> [!IMPORTANT]        
> Ten pakiet aktualizacji jest tylko dla stosu Azure zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji Azure stosu Development Kit.

## <a name="build-reference"></a>Tworzenie odwołania    
Numer kompilacji aktualizacji 1805 stosu Azure jest **1.1805.1.47**.  

> [!TIP]  
> Na podstawie opinii klientów, jest dostępna aktualizacja do wersji schematu na użytek systemu Microsoft Azure stosu.  Począwszy od tej aktualizacji 1805, nowego schematu lepiej reprezentuje bieżącą wersję chmury.  
> 
> Wersja schematu jest teraz *Version.YearYearMonthMonth.MinorVersion.BuildNumber* gdzie drugi i trzeci zestawy wskazać wersji i wydania. Na przykład 1805.1 reprezentuje *wersji RTM* 1805 wersji (RTM).  


### <a name="new-features"></a>Nowe funkcje
Ta aktualizacja obejmuje następujące ulepszenia stosu Azure.
<!-- 2297790 - IS, ASDK --> 
- **Stos Azure zawiera teraz *Syslog* klienta** jako *funkcja w wersji zapoznawczej*. Ten klient umożliwia przekazywanie dzienników inspekcji i zabezpieczeń dotyczące infrastruktury stosu Azure w celu Syslog serwera lub zabezpieczeń informacji i zdarzenia (SIEM) oprogramowania do zarządzania zewnętrznej stos Azure. Syslog klienta obsługuje obecnie tylko nieuwierzytelnione połączenia protokołu UDP za pośrednictwem domyślnego portu 514. Ładunek każdego komunikatu dziennika systemu jest sformatowany wspólną formatu zdarzeń (CEF). 

  Aby skonfigurować klienta usługi Syslog, użyj **SyslogServer zestaw** polecenia cmdlet w uprzywilejowanych punktu końcowego. 

  W tej wersji zapoznawczej można napotkać następujące trzy alerty. Gdy prezentowany przez stos Azure, obejmują te alerty *opisy* i *korygowania* wskazówki. 
  - Tytuł: Integralność kodu wyłączanie  
  - Nazwa: Integralności kodu w trybie inspekcji 
  - Nazwa: Konto użytkownika utworzone

  Gdy ta funkcja jest dostępna w wersji zapoznawczej, go nie powinno być stosowane na w środowiskach produkcyjnych.   




### <a name="fixed-issues"></a>Rozwiązane problemy

<!-- # - applicability -->


- **Różne poprawki** wydajności, trwałości, zabezpieczeń i systemu operacyjnego, który jest używany przez stos Azure.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Przed rozpoczęciem    

### <a name="prerequisites"></a>Wymagania wstępne
- Zainstaluj stosu Azure [zaktualizować 1804](azure-stack-update-1804.md) przed zastosowaniem aktualizacji 1805 stosu Azure.    
- Przed rozpoczęciem instalacji aktualizacji 1805 Uruchom [AzureStack testu](azure-stack-diagnostic-test.md) Aby zweryfikować stan stosu Azure i rozwiązać wszystkie problemy z działaniem znaleziono. Również przejrzeć aktywne alerty i rozwiązania, które wymagają akcji. 

### <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji   
- Podczas instalacji aktualizacji 1805, użytkownik może widzieć alerty z tytułem *błąd — szablon FaultType UserAccounts.New Brak.*  Te alerty można bezpiecznie zignorować. Te alerty zostanie zamknięte automatycznie po zakończeniu aktualizacji do 1805.   

- <!-- 2489559 - IS --> Do not attempt to create virtual machines during the installation of this update. For more information about managing updates, see [Manage updates in Azure Stack overview](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Czynności po aktualizacji
*Nie istnieją żadne czynności po aktualizacji dla aktualizacji 1805.*


## <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)
Poniżej przedstawiono znane problemy występujące po instalacji w tej wersji kompilacji.

### <a name="portal"></a>Portal  
- <!-- 2551834 - IS, ASDK --> When you select **Overview** for a storage account in either the admin or user portals, the information from the *Essentials* pane does not display.  The Essentials pane displays information about the account like its *Resource group*, *Location*, and *Subscription ID*.  Other options for Overview  are accessible, like *Services* and *Monitoring*, as well as options to *Open in Explorer* or to *Delete storage account*. 

  Aby wyświetlić dostępne informacje, należy użyć [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) polecenia cmdlet programu PowerShell. 

- <!-- 2332636 - IS -->  When you use AD FS for your Azure Stack identity system and update to this version of Azure Stack, the default owner of the default provider subscription is reset to the built-in **CloudAdmin** user.  
  Obejście: Aby rozwiązać ten problem, po zainstalowaniu tej aktualizacji, należy użyć krok 3 z [wyzwalacza automatyzacji do skonfigurowania zaufania dostawcy oświadczeń w stosie Azure](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedury, aby zresetować właściciela subskrypcji dostawcy domyślnego.   

- <!-- TBD - IS ASDK --> Some administrative subscription types are not available.  When you upgrade Azure Stack to this version, the two subscription types that were [introduced with version 1804](azure-stack-update-1804.md#new-features) are not visible in the console. This is expected. The unavailable subscription types are *Metering subscription*, and *Consumption subscription*. These subscription types are visible in new Azure Stack environments beginning with version 1804 but are not yet ready for use. You should continue to use the *Default Provider* subscription type.  

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Nawigacją](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> It might not be possible to view compute or storage resources in the administrator portal. The cause of this issue is an error during the installation of the update that causes the update to be incorrectly reported as successful. If this issue occurs, contact Microsoft Customer Support Services for assistance.

- <!-- TBD - IS --> You might see a blank dashboard in the portal. To recover the dashboard, select the gear icon in the upper right corner of the portal, and then select **Restore default settings**.

- <!-- TBD - IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD - IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.


### <a name="health-and-monitoring"></a>Monitorowania kondycji i
- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

   Alert #1:
   - Nazwa: Rolę infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji pulsu skanera jest niedostępny. Może to dotyczyć raportów o kondycji i metryki.  

  Alert #2.
   - Nazwa: Rolę infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji skanera błędu jest niedostępny. Może to dotyczyć raportów o kondycji i metryki.

  Obydwa alerty można bezpiecznie zignorować i będzie automatycznie zamknąć wraz z upływem czasu.  

- <!-- 2368581 - IS. ASDK --> An Azure Stack operator, if you receive a low memory alert and tenant virtual machines fail to deploy with a *Fabric VM creation error*, it is possible that the Azure Stack stamp is out of available memory. Use the [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) to best understand the capacity available for your workloads. 


### <a name="compute"></a>Wystąpienia obliczeniowe
- <!-- TBD - IS, ASDK --> When selecting a virtual machine size for a virtual machine deployment, some F-Series VM sizes are not visible as part of the size selector when you create a VM. The following VM sizes do not appear in the selector: *F8s_v2*, *F16s_v2*, *F32s_v2*, and *F64s_v2*.  
  Jako obejście użyj jednej z następujących metod można wdrożyć maszyny Wirtualnej. W każdej metodzie należy określić rozmiar maszyny Wirtualnej, który ma być używany.

  - **Szablonu usługi Azure Resource Manager:** w przypadku użycia szablonu ustawić *vmSize* w szablonie równe rozmiar maszyny Wirtualnej, którego chcesz użyć. Na przykład następujący wpis służy do wdrażania maszyny Wirtualnej, która używa *F32s_v2* rozmiar:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** można użyć [tworzenia maszyny wirtualnej az](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) polecenia i określić rozmiar maszyny Wirtualnej jako parametr, podobnie jak `--size "Standard_F32s_v2"`.

  - **Środowiska PowerShell:** przy użyciu programu PowerShell, można użyć [AzureRMVMConfig nowy](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) z parametrem, który określa rozmiar maszyny Wirtualnej, podobnie jak `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD - IS --> When you create an availability set in the portal by going to **New** > **Compute** > **Availability set**, you can only create an availability set with a fault domain and update domain of 1. As a workaround, when creating a new virtual machine, create the availability set by using PowerShell, CLI, or from within the portal.

- <!-- TBD - IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD - IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  Jako obejście, Utwórz nowy obraz maszyny Wirtualnej z fikcyjny wirtualnego dysku twardego, który można utworzyć za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżka-stałej SizeBytes — 1 GB). Ten proces powinno rozwiązać problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu zastępczego, można pomyślnie usunąć go.

  Następnie spróbujesz ponownie Pobierz obraz maszyny Wirtualnej, który zakończył się niepowodzeniem.

- <!-- TBD - IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


### <a name="networking"></a>Networking
- <!-- 1766332 - IS ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 - IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  Obecnie możesz korzystać tylko nowe publiczne adresy IP do tworzenia nowych maszyn wirtualnych.

  Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia oryginalna maszyna wirtualna, a nie nowy.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased.

  Aby obejść ten problem, użyj planu dodatku, aby zwiększyć limit przydziału sieci, gdy plan jest już skojarzony z subskrypcją. Aby uzyskać więcej informacji, zobacz temat jak [udostępnić plan dodatek](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription.


- <!-- 1902460 - IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 - IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.

- <!-- TBD - IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.

- <!-- 2096388 IS --> You cannot use the admin portal to update rules for a network security group.

    Obejście usługi aplikacji: Jeśli potrzebujesz pulpitu zdalnego do wystąpień kontrolera, zmodyfikować zasady zabezpieczeń w ramach grup zabezpieczeń sieci przy użyciu programu PowerShell.  Poniżej przedstawiono przykłady *Zezwalaj*, a następnie Przywróć konfigurację, aby *odmowy*:  

    - *Zezwalaj na:*

      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      ##This doesn’t work. Need to set properties again even in case of edit

      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Odmów:*

        ```powershell

        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        ##This doesn’t work. Need to set properties again even in case of edit

        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
        ```


### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL

- <!-- TBD - IS --> Only the resource provider is supported to create items on servers that host SQL or MySQL. Items created on a host server that are not created by the resource provider might result in a mismatched state.  

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.


> [!NOTE]  
> <!-- TBD - IS --> After you update to Azure Stack 1805, you can continue to use the SQL and MySQL resource providers that you previously deployed.  We recommend you update SQL and MySQL when a new release becomes available. Like Azure Stack, apply updates to SQL and MySQL resource providers sequentially. For example, if you use version 1803, first apply version 1804, and then update to 1805.      
>   
> Instalowanie aktualizacji 1805 nie wpływa na użycie bieżącej bazy danych SQL lub MySQL dostawców zasobów przez użytkowników.
> Niezależnie od wersji dostawców zasobów, których używasz danych użytkowników w swoich baz danych nie jest dotknąć i pozostanie dostępny.    



### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- 2489178 - IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.

- <!-- TBD - IS ASDK --> App Service can only be deployed into the *Default Provider subscription* at this time. In a future update, App Service will deploy into the new *Metering subscription* that was introduced in Azure Stack 1804. When Metering is supported for use, all existing deployments will be migrated to this new subscription type.


### <a name="usage"></a>Sposób użycia  
- <!-- TBD - IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Pobierz aktualizację
Możesz pobrać pakiet aktualizacji 1805 stosu Azure z [tutaj](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Zobacz także
- Aby użyć uprzywilejowanych punktu końcowego (program ten) do monitorowania i wznowić aktualizacji, zobacz [monitorowania aktualizacji w stosie Azure przy użyciu punktu końcowego uprzywilejowanych](azure-stack-monitor-update.md).
- Omówienie zarządzania aktualizacjami w stosie Azure, zobacz [zarządzania aktualizacjami w omówieniu stosu Azure](azure-stack-updates.md).
- Aby uzyskać więcej informacji dotyczących sposobu stosowania aktualizacji za pomocą stosu Azure, zobacz [stosowanie aktualizacji w stosie Azure](azure-stack-apply-updates.md).
