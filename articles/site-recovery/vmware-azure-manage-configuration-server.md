---
title: Zarządzanie serwera konfiguracji na potrzeby odzyskiwania po awarii VMware z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania istniejącego serwera konfiguracji na potrzeby odzyskiwania po awarii VMware do platformy Azure z usługą Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: b5ba316b21e0c31e0ecc99fc2d57f81b0f24c086
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>Zarządzanie serwerem konfiguracji w maszynach wirtualnych VMware

Lokalny serwer konfiguracji należy skonfigurować korzystając z [usługi Azure Site Recovery](site-recovery-overview.md) odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure. Serwer konfiguracji koordynuje komunikacji między lokalnymi VMware i na platformie Azure i zarządza replikacji danych. Ten artykuł zawiera podsumowanie typowych zadań zarządzania serwera konfiguracji po jej wdrożeniu.


## <a name="modify-vmware-settings"></a>Zmodyfikuj ustawienia VMware

Zmodyfikuj ustawienia dla serwera VMware, do którego nawiązuje połączenie z serwerem konfiguracji.

1. Zaloguj się na komputerze, na którym działa serwer konfiguracji.
2. Uruchom Menedżera konfiguracji usługi Azure Site Recovery z skrót na pulpicie. Możesz również otworzyć [to łącze](https://configuration-server-name/IP:44315).
3. Wybierz **Zarządzaj vCenter Server/vSPhere ESXi serwera**, a następnie wykonaj następujące czynności:

    * Aby skojarzyć innego serwera VMware z serwerem konfiguracji, wybierz **Dodaj vCenter Server/vSphere ESXi serwera**. Wprowadź szczegóły serwera.

    * Aby zaktualizować poświadczenia używane do łączenia się z serwerem VMware automatycznego wykrywania maszyn wirtualnych VMware, zaznacz **Edytuj**. Wprowadź nowe poświadczenia, a następnie wybierz **OK**.

    ![Modyfikowanie VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

## <a name="modify-credentials-for-mobility-service-installation"></a>Zmodyfikowania poświadczeń do instalacji usługi mobilności

Zmodyfikuj poświadczenia używane do automatycznego zainstalowania usługi mobilności na maszynach wirtualnych VMware można włączyć replikacji.

1. Zaloguj się na komputerze, na którym działa serwer konfiguracji.
2. Uruchom Menedżera konfiguracji usługi Site Recovery z skrót na pulpicie. Możesz również otworzyć [to łącze](https://configuration-server-name/IP:44315).
3. Wybierz **Zarządzanie poświadczeniami maszyny wirtualnej**, a następnie wprowadź nowe poświadczenia. Następnie wybierz **OK** można zaktualizować ustawień.

    ![Zmodyfikowania poświadczeń usługi mobilności](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Zmodyfikuj ustawienia serwera proxy

Zmodyfikuj ustawienia serwera proxy używane przez komputer serwera konfiguracji dla dostępu do Internetu na platformie Azure. Jeśli masz maszyny serwera przetwarzania, oprócz domyślnego serwera proces uruchomiony na komputerze z serwerem konfiguracji, zmodyfikuj ustawienia na obu komputerach.

1. Zaloguj się na komputerze, na którym działa serwer konfiguracji.
2. Uruchom Menedżera konfiguracji usługi Site Recovery z skrót na pulpicie. Możesz również otworzyć [to łącze](https://configuration-server-name/IP:44315).
3. Wybierz **Zarządzanie łączności**i zaktualizuj wartości serwera proxy. Następnie wybierz **zapisać** można zaktualizować ustawień.

## <a name="add-a-network-adapter"></a>Dodaj kartę sieciową

Szablon Open Virtualization Format (OVF) wdraża serwer konfiguracji maszyny Wirtualnej z jedną kartą sieciową. Możesz [dodać dodatkowe karty na maszynie wirtualnej)](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), ale należy go dodać, aby zarejestrować serwer konfiguracji w magazynie.

Aby dodać kartę po zarejestrowaniu serwera konfiguracji w magazynie, Dodaj kartę właściwości maszyny Wirtualnej. Następnie ponownie zarejestrować serwer w magazynie.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Zarejestruj ponownie serwer konfiguracji, w tym samym magazynie

Jeśli potrzebujesz można ponownie zarejestrować serwer konfiguracji, w tym samym magazynie. Jeśli masz maszyny serwera dodatkowych procesów, oprócz domyślnego serwera proces uruchomiony na komputerze z serwerem konfiguracji, należy ponownie zarejestrować obydwie maszyny.


  1. W magazynie, otwórz **Zarządzaj** > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**.
  2. W **serwerów**, wybierz pozycję **Pobierz klucz rejestracji** można pobrać pliku poświadczeń magazynu.
  3. Zaloguj się do komputera serwera konfiguracji.
  4. W **%ProgramData%\ASR\home\svagent\bin**, otwórz **cspsconfigtool.exe**.
  5. Na **rejestracji magazynu** wybierz opcję **Przeglądaj** i Znajdź pobrany plik poświadczeń magazynu.
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

1. Pobierz plik Instalatora aktualizacji na serwerze konfiguracji.
2. Kliknij dwukrotnie, aby uruchomić Instalatora.
3. Instalator wykrywa bieżącą wersję uruchomionych na komputerze.
4. Wybierz **OK** do potwierdzenia, a następnie uruchom uaktualnianie. 


## <a name="delete-or-unregister-a-configuration-server"></a>Usuń lub Wyrejestruj serwer konfiguracji

1. Wyłącz [Wyłącz ochronę](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) dla wszystkich maszyn wirtualnych w ramach konfiguracji serwera.
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
