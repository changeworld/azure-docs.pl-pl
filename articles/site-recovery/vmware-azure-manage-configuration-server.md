---
title: Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii programu VMware przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania istniejącego serwera konfiguracji odzyskiwania po awarii programu VMware do platformy Azure z RecoveryS witryny Azure.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: df5b2ecce2a5c9d7c263ee0acc3a49b859b93f7f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346124"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>Zarządzanie serwerem konfiguracji dla maszyn wirtualnych VMware

Ustaw się na lokalnym serwerze konfiguracji, gdy używasz [usługi Azure Site Recovery](site-recovery-overview.md) do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure. Serwer konfiguracji służy do koordynowania komunikacji między lokalną VMware i platformą Azure oraz do zarządzania replikacją danych. Ten artykuł zawiera podsumowanie typowych zadań zarządzania na serwerze konfiguracji po jej wdrożeniu.



## <a name="modify-vmware-settings"></a>Modyfikowanie ustawień programu VMware

Można uzyskać dostęp do serwera konfiguracji w następujący sposób:
    - Zaloguj się do maszyny Wirtualnej, na którym została wdrożona i Rozpocznij Azure Site Recovery Configuration Manager ze skrótu na pulpicie.
    - Alternatywnie, mają dostęp do serwera konfiguracji, zdalnie z **https://*ConfigurationServerName*/:44315 /**. Zaloguj się przy użyciu poświadczeń administratora.
   
### <a name="modify-vmware-server-settings"></a>Zmodyfikuj ustawienia serwera VMware

1. Aby skojarzyć z serwerem konfiguracji na inny serwer programu VMware, po zalogowaniu, wybierz pozycję **poświadczenia serwera vCenter Server/vSphere ESXi Dodaj**.
2. Wprowadź szczegółowe informacje, a następnie wybierz **OK**.


### <a name="modify-credentials-for-automatic-discovery"></a>Zmodyfikowania poświadczeń do automatycznego odnajdowania

1. Aby zaktualizować poświadczenia używane do łączenia z serwerem VMware do automatycznego odnajdowania maszyn wirtualnych programu VMware, po zalogowaniu wybierz **Edytuj**.
2. Wprowadź nowe poświadczenia, a następnie wybierz **OK**.

    ![Modyfikowanie programu VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>Modyfikowanie poświadczenia na potrzeby instalacji usługi mobilności

Zmodyfikuj poświadczenia używane do automatycznego instalowania usługi mobilności na maszynach wirtualnych VMware, należy włączyć dla replikacji.

1. Po zalogowaniu wybierz **Zarządzanie poświadczeniami maszyny wirtualnej**
2. Wprowadź nowe poświadczenia, a następnie wybierz **OK**.

    ![Zmodyfikowania poświadczeń usługi mobilności](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Modyfikowanie ustawień serwera proxy

Zmodyfikuj ustawienia serwera proxy komputera serwera konfiguracji, aby uzyskać dostęp do Internetu na platformie Azure. Jeśli masz maszyny server procesu, oprócz domyślnego serwera proces uruchomiony na komputerze z serwerem konfiguracji, należy zmodyfikować ustawienia na obu komputerach.

1. Po zalogowaniu do serwera konfiguracji, wybierz **Zarządzanie łącznością**.
2. Zaktualizuj wartości serwera proxy. Następnie wybierz pozycję **Zapisz** Aby zaktualizować ustawienia.

## <a name="add-a-network-adapter"></a>Dodawanie karty sieciowej

Szablon Open Virtualization Format (OVF) umożliwia wdrożenie serwera konfiguracji maszyny Wirtualnej z jedną kartą sieciową.

- Możesz [Dodawanie karty sieciowej do maszyny Wirtualnej](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), ale można go dodać, aby zarejestrować serwer konfiguracji w magazynie.
- Aby dodać kartę, po zarejestrowaniu serwera konfiguracji w magazynie, należy dodać kartę do właściwości maszyny Wirtualnej. Następnie należy ponownie zarejestrować serwer w magazynie.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Zarejestruj ponownie serwer konfiguracji, w tym samym magazynie

Jeśli trzeba, można ponownie zarejestrować serwer konfiguracji, w tym samym magazynie. Jeśli masz maszyny serwera dodatkowych procesów oprócz domyślnego serwera proces uruchomiony na komputerze z serwerem konfiguracji, Zarejestruj ponownie obu komputerach.


  1. W magazynie, otwórz **Zarządzaj** > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**.
  2. W **serwerów**, wybierz opcję **Pobierz klucz rejestracji** można pobrać pliku poświadczeń magazynu.
  3. Zaloguj się do komputera serwera konfiguracji.
  4. W **%ProgramData%\ASR\home\svsystems\bin**, otwórz **cspsconfigtool.exe**.
  5. Na **rejestracja w magazynie** zaznacz **Przeglądaj**, a następnie zlokalizuj pobranego pliku poświadczeń magazynu.
  6. Jeśli to konieczne, podaj szczegóły serwera proxy. Następnie wybierz pozycję **Zarejestruj**.
  7. Otwórz okno poleceń programu PowerShell administratora i uruchom następujące polecenie:
   ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

      >[!NOTE] 
      >W celu **ściągnięcia najnowsze certyfikaty** z serwera konfiguracji serwera przetwarzania skalowalnego w poziomie, wykonaj polecenie *"< Drive\Microsoft instalacji Azure Recovery\agent\cdpcli.exe lokacji >"--registermt*

  8. Na koniec ponownie obengine, wykonując następujące polecenie.
  ```
          net stop obengine
          net start obengine

## Upgrade the configuration server

You run update rollups to update the configuration server. Updates can be applied for up to N-4 versions. For example:

- If you run 9.7, 9.8, 9.9, or 9.10, you can upgrade directly to 9.11.
- If you run 9.6 or earlier and you want to upgrade to 9.11, you must first upgrade to version 9.7. before 9.11.

Links to update rollups for upgrading to all versions of the configuration server are available in the [wiki updates page](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade the server as follows:

1. In the vault, go to **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.
2. If an update is available, a link appears in the **Agent Version** > column.
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Download the update installer file to the configuration server.

    ![Update](./media/vmware-azure-manage-configuration-server/update1.png)

4. Double-click to run the installer.
5. The installer detects the current version running on the machine. Click **Yes** to start the upgrade.
6. When the upgrade completes the server configuration validates.

    ![Update](./media/vmware-azure-manage-configuration-server/update3.png)
    
7. Click **Finish** to close the installer.

## Delete or unregister a configuration server

1. [Disable protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) for all VMs under the configuration server.
2. [Disassociate](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) and [delete](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) all replication policies from the configuration server.
3. [Delete](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) all vCenter servers/vSphere hosts that are associated with the configuration server.
4. In the vault, open **Site Recovery Infrastructure** > **Configuration Servers**.
5. Select the configuration server that you want to remove. Then, on the **Details** page, select **Delete**.

    ![Delete configuration server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### Delete with PowerShell

You can optionally delete the configuration server by using PowerShell.

1. [Install](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) the Azure PowerShell module.
2. Sign in to your Azure account by using this command:
    
    `Connect-AzureRmAccount`
3. Select the vault subscription.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Set the vault context.
    
    ```
    $vault = get-AzureRmRecoveryServicesVault — nazwa <name of your vault> Set AzureRmSiteRecoveryVaultSettings - ARSVault $vault
    ```
4. Retrieve the configuration server.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Delete the configuration server.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> You can use the **-Force** option in Remove-AzureRmSiteRecoveryFabric for forced deletion of the configuration server.
 
## Generate configuration server Passphrase

1. Sign in to your configuration server, and then open a command prompt window as an administrator.
2. To change the directory to the bin folder, execute the command **cd %ProgramData%\ASR\home\svsystems\bin**
3. To generate the passphrase file, execute **genpassphrase.exe -v > MobSvc.passphrase**.
4. Your passphrase will be stored in the file located at **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## Renew SSL certificates

The configuration server has an inbuilt web server, which orchestrates activities of the Mobility Service, process servers, and master target servers connected to it. The web server uses an SSL certificate to authenticate clients. The certificate expires after three years and can be renewed at any time.

### Check expiry

For configuration server deployments before May 2016, certificate expiry was set to one year. If you have a certificate that is going to expire, the following occurs:

- When the expiry date is two months or less, the service starts sending notifications in the portal, and by email (if you subscribed to Site Recovery notifications).
- A notification banner appears on the vault resource page. For more information, select the banner.
- If you see an **Upgrade Now** button, it indicates that some components in your environment haven't been upgraded to 9.4.xxxx.x or higher versions. Upgrade the components before you renew the certificate. You can't renew on older versions.

### Renew the certificate

1. In the vault, open **Site Recovery Infrastructure** > **Configuration Server**. Select the required configuration server.
2. The expiry date appears under **Configuration Server health**.
3. Select **Renew Certificates**. 


## Next steps

Review the tutorials for setting up disaster recovery of [VMware VMs](vmware-azure-tutorial.md) to Azure.
