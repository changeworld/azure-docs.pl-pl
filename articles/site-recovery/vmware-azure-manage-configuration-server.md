---
title: Zarządzanie serwera konfiguracji na potrzeby odzyskiwania po awarii VMware z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania istniejącego serwera konfiguracji na potrzeby odzyskiwania po awarii VMware do platformy Azure z RecoveryS witryny Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 06/04/2018
ms.author: raynew
ms.openlocfilehash: 64f5f2105a9048d649503b0790231676182a4c4f
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737507"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>Zarządzanie serwerem konfiguracji w maszynach wirtualnych VMware

Lokalny serwer konfiguracji należy skonfigurować korzystając z [usługi Azure Site Recovery](site-recovery-overview.md) odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure. Serwer konfiguracji koordynuje komunikacji między lokalnymi VMware i na platformie Azure i zarządza replikacji danych. Ten artykuł zawiera podsumowanie typowych zadań zarządzania serwera konfiguracji po jej wdrożeniu.



## <a name="modify-vmware-settings"></a>Zmodyfikuj ustawienia VMware

Można uzyskać dostęp serwer konfiguracji w następujący sposób:
    - Zaloguj się do maszyny Wirtualnej, na którym została wdrożona i uruchom Azure Site Recovery Configuration Manager ze skrótu na pulpicie.
    - Alternatywnie można uzyskać dostęp zdalnie z serwera konfiguracji **https://*ConfigurationServerName*/:44315 /**. Zaloguj się przy użyciu poświadczeń administratora.
   
### <a name="modify-vmware-server-settings"></a>Modyfikowanie ustawień serwera VMware

1. Aby skojarzyć innego serwera VMware z serwera konfiguracji po zalogowaniu, wybierz **Dodaj vCenter Server/vSphere ESXi serwera**.
2. Wprowadź szczegóły, a następnie wybierz **OK**.


### <a name="modify-credentials-for-automatic-discovery"></a>Zmodyfikowania poświadczeń automatycznego wykrywania

1. Aby zaktualizować poświadczenia używane do łączenia się z serwerem VMware, automatycznego wykrywania maszyn wirtualnych VMware, po zalogowaniu, zaznacz **Edytuj**.
2. Wprowadź nowe poświadczenia, a następnie wybierz **OK**.

    ![Modyfikowanie VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>Zmodyfikowania poświadczeń do instalacji usługi mobilności

Zmodyfikuj poświadczenia używane do automatycznego zainstalowania usługi mobilności na maszynach wirtualnych VMware można włączyć replikacji.

1. Po zalogowaniu, wybierz **Zarządzanie poświadczeniami maszyny wirtualnej**
2. Wprowadź nowe poświadczenia, a następnie wybierz **OK**.

    ![Zmodyfikowania poświadczeń usługi mobilności](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Zmodyfikuj ustawienia serwera proxy

Zmodyfikuj ustawienia serwera proxy używane przez komputer serwera konfiguracji dla dostępu do Internetu na platformie Azure. Jeśli masz maszyny serwera przetwarzania, oprócz domyślnego serwera proces uruchomiony na komputerze z serwerem konfiguracji, zmodyfikuj ustawienia na obu komputerach.

1. Po zalogowaniu do konfiguracji serwera, wybierz **Zarządzanie łączności**.
2. Zaktualizuj wartości serwera proxy. Następnie wybierz **zapisać** można zaktualizować ustawień.

## <a name="add-a-network-adapter"></a>Dodaj kartę sieciową

Szablon Open Virtualization Format (OVF) wdraża serwer konfiguracji maszyny Wirtualnej z jedną kartą sieciową.

- Możesz [dodać dodatkowe karty na maszynie wirtualnej)](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), ale można go dodać, aby zarejestrować serwer konfiguracji w magazynie.
- Aby dodać kartę po zarejestrowaniu serwera konfiguracji w magazynie, Dodaj kartę właściwości maszyny Wirtualnej. Następnie należy ponownie zarejestrować serwer w magazynie.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Zarejestruj ponownie serwer konfiguracji, w tym samym magazynie

Jeśli potrzebujesz można ponownie zarejestrować serwer konfiguracji, w tym samym magazynie. Jeśli masz maszyny serwera dodatkowych procesów, oprócz domyślnego serwera proces uruchomiony na komputerze z serwerem konfiguracji, należy ponownie zarejestrować obydwie maszyny.


  1. W magazynie, otwórz **Zarządzaj** > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**.
  2. W **serwerów**, wybierz pozycję **Pobierz klucz rejestracji** można pobrać pliku poświadczeń magazynu.
  3. Zaloguj się do komputera serwera konfiguracji.
  4. W **%ProgramData%\ASR\home\svsystems\bin**, otwórz **cspsconfigtool.exe**.
  5. Na **rejestracji magazynu** wybierz opcję **Przeglądaj**, a następnie zlokalizuj plik poświadczeń magazynu pobranego.
  6. Jeśli to konieczne, podaj szczegóły serwera proxy. Następnie wybierz pozycję **Zarejestruj**.
  7. Otwórz okno poleceń programu PowerShell administratora i uruchom następujące polecenie:

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

## <a name="upgrade-the-configuration-server"></a>Uaktualnij serwer konfiguracji

Możesz uruchomić pakiety zbiorcze aktualizacji, aby zaktualizować serwer konfiguracji. Aktualizacje mogą być stosowane dla maksymalnie N-4 wersji. Na przykład:

- Po uruchomieniu 9.7, 9,8, 9.9 lub 9.10 można uaktualnić bezpośrednio do 9.11.
- Jeśli chcesz uaktualnić do 9.11 uruchom 9.6 lub starszym, należy najpierw uaktualnić do wersji 9.7. przed 9.11.

Łącza do pakiety zbiorcze aktualizacji dla uaktualnienia wszystkich wersji serwera konfiguracji są dostępne w [strona typu wiki aktualizacji](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Uaktualnij serwer w następujący sposób:

1. W magazynie, przejdź do **Zarządzaj** > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**.
2. Jeśli jest dostępna aktualizacja, łącze jest wyświetlane w **wersja agenta** > kolumny.

    ![Aktualizacja](./media/vmware-azure-manage-configuration-server/update2.png)

1. Pobierz plik Instalatora aktualizacji na serwerze konfiguracji.

    ![Aktualizacja](./media/vmware-azure-manage-configuration-server/update1.png)

4. Kliknij dwukrotnie, aby uruchomić Instalatora.
2. Instalator wykrywa bieżącą wersję uruchomionych na komputerze. Kliknij przycisk **tak** uruchomiony w celu uaktualnienia. 
3. Po zakończeniu uaktualniania sprawdza poprawność konfiguracji serwera.

    ![Aktualizacja](./media/vmware-azure-manage-configuration-server/update3.png)

4. Kliknij przycisk **Zakończ** aby zamknąć Instalatora.


## <a name="delete-or-unregister-a-configuration-server"></a>Usuń lub Wyrejestruj serwer konfiguracji

1. [Wyłącz ochronę](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) dla wszystkich maszyn wirtualnych w ramach konfiguracji serwera.
2. [Usuń skojarzenie](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) i [usunąć](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) wszystkie zasady replikacji z serwera konfiguracji.
3. [Usuń](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) wszystkie hosty serwerów/vSphere vCenter, które są skojarzone z serwerem konfiguracji.
4. W magazynie, otwórz **infrastruktura usługi Site Recovery** > **serwery konfiguracji**.
5. Wybierz serwer konfiguracji, który ma zostać usunięty. Następnie na **szczegóły** wybierz pozycję **usunąć**.

    ![Usuwanie konfiguracji serwera](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Usuwanie przy użyciu programu PowerShell

Opcjonalnie można usunąć serwera konfiguracji przy użyciu programu PowerShell.

1. [Zainstaluj](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) modułu Azure PowerShell.
2. Zaloguj się do konta platformy Azure za pomocą tego polecenia:
    
    `Connect-AzureRmAccount`
3. Wybierz subskrypcję magazynu.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Ustaw kontekst magazynu.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Pobrać serwer konfiguracji.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Usuń serwer konfiguracji.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Można użyć **-Force** opcji w Usuń AzureRmSiteRecoveryFabric do wymuszenia usunięcia serwera konfiguracji.
 


## <a name="renew-ssl-certificates"></a>Odnawianie certyfikatów SSL

Serwer konfiguracji jest serwer sieci web wbudowanych, który organizuje działania usługi mobilności, proces serwerów i głównych serwerów docelowych dołączone do niego. Serwer sieci web używa certyfikatu SSL do uwierzytelniania klientów. Certyfikat wygaśnie po upływie trzech lat i mogą być odnawiane w dowolnym momencie.

### <a name="check-expiry"></a>Sprawdzanie wygaśnięcia

Dla wdrożenia serwera konfiguracji przed maj 2016 okresu ważności certyfikatu została ustawiona na jeden rok. Jeśli certyfikat wygaśnie, są następujące operacje:

- Jeśli data wygaśnięcia jest dwóch miesięcy lub mniej, wysyłanie powiadomień, w portalu, a także za pośrednictwem poczty e-mail (jeśli subskrybowania powiadomień usługi Site Recovery) uruchomienia usługi.
- Transparent powiadomienie jest wyświetlane na stronie zasobów magazynu. Aby uzyskać więcej informacji wybierz banerze.
- Jeśli widzisz **Uaktualnij teraz** przycisk oznacza, że niektóre składniki w danym środowisku nie zostały uaktualnione do 9.4.xxxx.x lub nowszej wersji. Uaktualnij składniki programu, aby odnowić certyfikat. Nie można odnowić w starszych wersjach.

### <a name="renew-the-certificate"></a>Odnów certyfikat

1. W magazynie, otwórz **infrastruktura usługi Site Recovery** > **serwera konfiguracji**. Wybierz serwer konfiguracji.
2. Data wygaśnięcia jest wyświetlany w obszarze **kondycji serwera konfiguracji**.
3. Wybierz **odnawiania certyfikatów**. 


## <a name="next-steps"></a>Kolejne kroki

Przejrzyj samouczki dotyczące konfigurowania odzyskiwania po awarii [maszyn wirtualnych VMware](vmware-azure-tutorial.md) na platformie Azure.
