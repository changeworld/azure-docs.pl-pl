---
title: Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii programu VMware przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania istniejącego serwera konfiguracji odzyskiwania po awarii programu VMware do platformy Azure z RecoveryS witryny Azure.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 0935867e835fe88568f1cdce1ea8dfcea14a451a
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669319"
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
  ```
  
## <a name="upgrade-the-configuration-server"></a>Uaktualnij serwer konfiguracji

Możesz uruchamiać pakiety zbiorcze aktualizacji, aby zaktualizować serwer konfiguracji. Aktualizacje mogą być stosowane dla maksymalnie N-4 wersji. Na przykład:

- Jeśli zostanie uruchomione, zbierając 9,7, 9,8, 9.9 lub 9.10, można uaktualnić bezpośrednio do 9.11.
- Jeśli chcesz uaktualnić do 9.11 uruchom 9.6 lub starszym, należy najpierw uaktualnić do wersji zbierając 9,7. przed 9.11.

Łącza do pakiety zbiorcze aktualizacji dla uaktualnienie dla wszystkich wersji serwera konfiguracji są dostępne w [strona aktualizacji witryny typu wiki](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Uaktualnij serwer w następujący sposób:

1. W magazynie, przejdź do **Zarządzaj** > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**.
2. Jeśli aktualizacja jest dostępna, pojawi się łącze **wersja agenta** > kolumny.
    ![Aktualizacja](./media/vmware-azure-manage-configuration-server/update2.png)
3. Pobierz plik Instalatora aktualizacji z serwerem konfiguracji.

    ![Aktualizacja](./media/vmware-azure-manage-configuration-server/update1.png)

4. Kliknij dwukrotnie, aby uruchomić Instalatora.
5. Instalator wykrywa bieżąca wersja uruchomiona na maszynie. Kliknij przycisk **tak** uruchomiony w celu uaktualnienia.
6. Po zakończeniu uaktualniania sprawdza poprawność konfiguracji serwera.

    ![Aktualizacja](./media/vmware-azure-manage-configuration-server/update3.png)

7. Kliknij przycisk **Zakończ** zamknąć Instalatora.

## <a name="delete-or-unregister-a-configuration-server"></a>Usunąć lub wyrejestrować serwer konfiguracji

1. [Wyłącz ochronę](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) dla wszystkich maszyn wirtualnych na serwerze konfiguracji.
2. [Usuń skojarzenie](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) i [Usuń](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) wszystkie zasady replikacji z serwera konfiguracji.
3. [Usuń](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) wszystkie hosty vSphere/serwerach vCenter, które są skojarzone z serwerem konfiguracji.
4. W magazynie, otwórz **infrastruktura usługi Site Recovery** > **serwery konfiguracji**.
5. Wybierz serwer konfiguracji, który chcesz usunąć. Następnie na **szczegóły** wybierz opcję **Usuń**.

    ![Usuwanie serwera konfiguracji](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Usuń przy użyciu programu PowerShell

Możesz opcjonalnie usunąć serwer konfiguracji za pomocą programu PowerShell.

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
4. Pobieranie serwera konfiguracji.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Usuwanie serwera konfiguracji.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Możesz użyć **-Force** opcji w AzureRmSiteRecoveryFabric Usuń wymuszonego usunięcia serwera konfiguracji.

## <a name="generate-configuration-server-passphrase"></a>Generuj serwer konfiguracji hasło

1. Zaloguj się do serwera konfiguracji, a następnie otwórz okno wiersza polecenia jako administrator.
2. Aby zmienić katalog na bin folder, wykonaj polecenie **%ProgramData%\ASR\home\svsystems\bin dysku cd**
3. Aby wygenerować plik hasło, należy wykonać **genpassphrase.exe - v > MobSvc.passphrase**.
4. Twoje hasło, które będą przechowywane w pliku znajdującym się w **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="renew-ssl-certificates"></a>Odnawianie certyfikatów SSL

Serwer konfiguracji jest serwer sieci web wbudowanych, który organizuje działania usługi mobilności, serwerów przetwarzania i główne serwery docelowe połączoną z nią. Serwer sieci web używa certyfikatu SSL do uwierzytelniania klientów. Certyfikat wygasa po upływie trzech lat i można odnawiać w dowolnym momencie.

### <a name="check-expiry"></a>Sprawdzanie wygaśnięcia

Dla wdrożenia serwera konfiguracji, które maja 2016 r. Data ważności certyfikatu została ustawiona na jeden rok. Jeśli masz certyfikat wygaśnie, mają miejsce następujące zdarzenia:

- Gdy data wygaśnięcia jest dwa miesiące lub mniej, uruchamiania usługi, wysyłania powiadomień w portalu i za pośrednictwem poczty e-mail (Jeśli masz subskrypcję powiadomień Site Recovery).
- Transparent powiadomienia zostanie wyświetlona na stronie zasobów magazynu. Aby uzyskać więcej informacji wybierz baner.
- Jeśli widzisz **Uaktualnij teraz** przycisku, oznacza to, że niektóre składniki w danym środowisku nie zostały uaktualnione do 9.4.xxxx.x lub nowsze wersje. Aby odnowić certyfikat, należy uaktualnić składniki. Nie można odnowić w starszych wersjach.

### <a name="renew-the-certificate"></a>Odnów certyfikat

1. W magazynie, otwórz **infrastruktura usługi Site Recovery** > **serwera konfiguracji**. Wybierz serwer wymaganej konfiguracji.
2. Data ważności jest wyświetlany w obszarze **Kondycja serwera Configuration**.
3. Wybierz **odnawiania certyfikatów**.

## <a name="update-windows-licence"></a>Licencja Windows Update

Licencja dostarczane z szablonu pakietu OVF jest pozwolenia oceny prawidłowe przez 180 dni. Dla nieprzerwaną pracę, musisz aktywować Windows za pomocą kupowane licencji.

## <a name="failback-requirements"></a>Wymagania dotyczące powrotu po awarii

Podczas ponownego włączania ochrony i powrotu po awarii na lokalnym serwerze konfiguracji musi być uruchomiona i w stanie połączonym. Pomyślne powrotu po awarii powrót po awarii maszyny wirtualnej musi istnieć w bazie danych serwera konfiguracji.

Upewnij się, wykonanie regularnie zaplanowane kopie zapasowe serwera konfiguracji. W przypadku poważnej awarii, a serwer konfiguracji jest utracone, możesz przywrócić serwer konfiguracji z kopii zapasowej i upewnij się, że serwer konfiguracji przywróconej ma ten sam adres IP, z którym został zarejestrowany w magazynie. Powrót po awarii nie będzie działać, jeśli inny adres IP jest używany dla serwera konfiguracji przywróconych.

## <a name="next-steps"></a>Kolejne kroki

Przejrzyj samouczki dotyczące konfigurowania odzyskiwania po awarii [maszyny wirtualne VMware](vmware-azure-tutorial.md) na platformie Azure.
