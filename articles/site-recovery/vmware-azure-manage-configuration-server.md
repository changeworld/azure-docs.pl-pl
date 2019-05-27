---
title: Zarządzanie serwerem konfiguracji dla programu VMware i odzyskiwanie po awarii serwerów fizycznych z usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania istniejącego serwera konfiguracji odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: ramamill
ms.openlocfilehash: 7fab3b05429e430b444c2a14213c524fbf19a01d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171678"
---
# <a name="manage-the-configuration-server-for-vmware-vm-disaster-recovery"></a>Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware

Ustaw się na lokalnym serwerze konfiguracji, gdy używasz [usługi Azure Site Recovery](site-recovery-overview.md) do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure. Serwer konfiguracji służy do koordynowania komunikacji między lokalną VMware i platformą Azure oraz do zarządzania replikacją danych. Ten artykuł zawiera podsumowanie typowych zadań zarządzania na serwerze konfiguracji po jej wdrożeniu.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-configuration-server"></a>Dostęp do serwera konfiguracji

Można uzyskać dostęp do serwera konfiguracji w następujący sposób:

* Zaloguj się do maszyny Wirtualnej, na którym została wdrożona i rozpoczęcie **Azure Site Recovery Configuration Manager** ze skrótu na pulpicie.
* Alternatywnie, są dostępne na serwerze konfiguracji zdalnie https://*ConfigurationServerName*/:44315 /. Zaloguj się przy użyciu poświadczeń administratora.

## <a name="modify-vmware-server-settings"></a>Zmodyfikuj ustawienia serwera VMware

1. Aby skojarzyć inny serwer programu VMware z serwerem konfiguracji po [logowania](#access-configuration-server), wybierz opcję **poświadczenia serwera vCenter Server/vSphere ESXi Dodaj**.
2. Wprowadź szczegółowe informacje, a następnie wybierz **OK**.

## <a name="modify-credentials-for-automatic-discovery"></a>Zmodyfikowania poświadczeń do automatycznego odnajdowania

1. Aby zaktualizować poświadczenia używane do łączenia z serwerem VMware do automatycznego odnajdowania maszyn wirtualnych programu VMware, po [logowania](#access-configuration-server), wybierz konto i kliknij przycisk **Edytuj**.
2. Wprowadź nowe poświadczenia, a następnie wybierz **OK**.

    ![Modyfikowanie programu VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

Można również zmodyfikować poświadczenia za pośrednictwem CSPSConfigtool.exe.

1. Zaloguj się do serwera konfiguracji, a następnie uruchom CSPSConfigtool.exe
2. Wybierz konto chcesz zmodyfikować, a następnie kliknij przycisk **Edytuj**.
3. Wprowadź poświadczenia zmodyfikowane, a następnie kliknij przycisk **Ok**

## <a name="modify-credentials-for-mobility-service-installation"></a>Modyfikowanie poświadczenia na potrzeby instalacji usługi mobilności

Zmodyfikuj poświadczenia używane do automatycznego instalowania usługi mobilności na maszynach wirtualnych VMware, należy włączyć dla replikacji.

1. Po [logowania](#access-configuration-server), wybierz opcję **Zarządzanie poświadczeniami maszyny wirtualnej**
2. Wybierz konto chcesz zmodyfikować, a następnie kliknij przycisk **edycji**
3. Wprowadź nowe poświadczenia, a następnie wybierz **OK**.

    ![Zmodyfikowania poświadczeń usługi mobilności](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

Można również zmodyfikować poświadczeń za pośrednictwem CSPSConfigtool.exe.

1. Zaloguj się do serwera konfiguracji, a następnie uruchom CSPSConfigtool.exe
2. Wybierz konto chcesz zmodyfikować, a następnie kliknij przycisk **edycji**
3. Wprowadź nowe poświadczenia, a następnie kliknij przycisk **Ok**.

## <a name="add-credentials-for-mobility-service-installation"></a>Dodaj poświadczenia na potrzeby instalacji usługi mobilności

Jeśli zdarzyło Ci się przeoczyć dodawania poświadczeń podczas wdrażania pakietu OVF serwera konfiguracji

1. Po [logowania](#access-configuration-server), wybierz opcję **Zarządzanie poświadczeniami maszyny wirtualnej**.
2. Kliknij pozycję **Dodawanie poświadczeń maszyny wirtualnej**.
    ![add-mobility-credentials](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Wprowadź nowe poświadczenia, a następnie kliknij pozycję **Dodaj**.

Można również dodać poświadczeń za pośrednictwem CSPSConfigtool.exe.

1. Zaloguj się do serwera konfiguracji, a następnie uruchom CSPSConfigtool.exe
2. Kliknij przycisk **Dodaj**, wprowadź nowe poświadczenia i kliknij przycisk **Ok**.

## <a name="modify-proxy-settings"></a>Modyfikowanie ustawień serwera proxy

Zmodyfikuj ustawienia serwera proxy komputera serwera konfiguracji, aby uzyskać dostęp do Internetu na platformie Azure. Jeśli masz maszyny server procesu, oprócz domyślnego serwera proces uruchomiony na komputerze z serwerem konfiguracji, należy zmodyfikować ustawienia na obu komputerach.

1. Po [logowania](#access-configuration-server) z serwerem konfiguracji wybierz **Zarządzanie łącznością**.
2. Zaktualizuj wartości serwera proxy. Następnie wybierz pozycję **Zapisz** Aby zaktualizować ustawienia.

## <a name="add-a-network-adapter"></a>Dodawanie karty sieciowej

Szablon Open Virtualization Format (OVF) umożliwia wdrożenie serwera konfiguracji maszyny Wirtualnej z jedną kartą sieciową.

- Możesz [Dodawanie karty sieciowej do maszyny Wirtualnej](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), ale można go dodać, aby zarejestrować serwer konfiguracji w magazynie.
- Aby dodać kartę, po zarejestrowaniu serwera konfiguracji w magazynie, należy dodać kartę do właściwości maszyny Wirtualnej. Następnie należy [ponownie zarejestrować](#reregister-a-configuration-server-in-the-same-vault) serwera w magazynie.


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


## <a name="register-a-configuration-server-with-a-different-vault"></a>Rejestrowanie serwera konfiguracji za pomocą innego magazynu

> [!WARNING]
> Następny krok powoduje usunięcie serwera konfiguracji z bieżącego magazynu, a następnie replikację chronionych maszyn wirtualnych na serwerze konfiguracji jest zatrzymana.

1. Zaloguj się do serwera konfiguracji.
2. Otwórz okno poleceń programu PowerShell administratora i uruchom następujące polecenie:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Uruchom portal przeglądarki urządzenia serwera konfiguracji za pomocą skrótu na pulpicie.
4. Wykonaj kroki rejestracji, podobnie jak na nowy serwer konfiguracji [rejestracji](vmware-azure-tutorial.md#register-the-configuration-server).

## <a name="upgrade-the-configuration-server"></a>Uaktualnij serwer konfiguracji

Możesz uruchamiać pakiety zbiorcze aktualizacji, aby zaktualizować serwer konfiguracji. Aktualizacje mogą być stosowane dla maksymalnie N-4 wersji. Na przykład:

- Jeśli zostanie uruchomione, zbierając 9,7, 9,8, 9.9 lub 9.10, można uaktualnić bezpośrednio do 9.11.
- Jeśli chcesz uaktualnić do 9.11 uruchom 9.6 lub starszym, należy najpierw uaktualnić do wersji zbierając 9,7. przed 9.11.

Aby uzyskać szczegółowe wskazówki na temat składników usługi Azure Site Recovery można znaleźć instrukcji obsługi [tutaj](https://aka.ms/asr_support_statement).
Dostępne są linki do pakiety zbiorcze aktualizacji dla uaktualnienie dla wszystkich wersji serwera konfiguracji [tutaj](https://aka.ms/asr_update_rollups).

> [!IMPORTANT]
> Przy każdej nowej wersji "n" z usługi Azure Site Recovery składnik, który jest zwalniana, wszystkie wersje poniżej n-4 "jest uważany za świadczenie pomocy technicznej. Zawsze zaleca się uaktualnienie do najnowszej wersji dostępne.</br>
> Aby uzyskać szczegółowe wskazówki na temat składników usługi Azure Site Recovery można znaleźć instrukcji obsługi [tutaj](https://aka.ms/asr_support_statement).

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
8. Aby Uaktualnianie pozostałych składników usługi Site Recovery, zobacz nasze [wskazówki dotyczące uaktualniania](https://aka.ms/asr_vmware_upgrades).

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Uaktualnij konfigurację serwera lub serwer przetwarzania w wierszu polecenia

Uruchom plik instalacyjny w następujący sposób:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Przykładowe zastosowanie
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parametry

|Nazwa parametru| Type | Opis| Wartości|
|-|-|-|-|
| /ServerMode|Wymagane|Określa, czy mają zostać zainstalowane oba serwery, konfiguracji i przetwarzania, czy tylko serwer przetwarzania|CS<br>PS|
|/InstallLocation|Wymagane|Folder, w którym są instalowane składniki| Dowolny folder na komputerze|
|/MySQLCredsFilePath|Wymagane|Ścieżka pliku, w której są przechowywane poświadczenia serwera MySQL|Plik powinien mieć format określony poniżej|
|/VaultCredsFilePath|Wymagane|Ścieżka pliku poświadczeń magazynu|Prawidłowa ścieżka pliku|
|/EnvType|Wymagane|Typ środowiska, które mają być chronione |VMware<br>NonVMware|
|/PSIP|Wymagane|Adres IP karty sieciowej do użytku podczas przesyłania danych replikacji| Dowolny prawidłowy adres IP|
|/CSIP|Wymagane|Adres IP karty sieciowej, na której nasłuchuje serwer konfiguracji| Dowolny prawidłowy adres IP|
|/PassphraseFilePath|Wymagane|Pełna ścieżka do lokalizacji pliku hasła|Prawidłowa ścieżka pliku|
|/BypassProxy|Opcjonalne|Określa, że serwer konfiguracji łączy się z platformą Azure bez serwera proxy|Należy uzyskać tę wartość z Venu|
|/ProxySettingsFilePath|Opcjonalne|Ustawienia serwera proxy (domyślny serwer proxy wymaga uwierzytelniania lub niestandardowy serwer proxy)|Plik powinien mieć format określony poniżej|
|DataTransferSecurePort|Opcjonalne|Numer portu dla protokołu PSIP do użytku z danymi replikacji| Prawidłowy numer portu (wartość domyślna to 9433)|
|/SkipSpaceCheck|Opcjonalne|Pomiń sprawdzanie miejsca dla dysku pamięci podręcznej| |
|/AcceptThirdpartyEULA|Wymagane|Flaga implikuje akceptację umowy licencyjnej innego producenta| |
|/ShowThirdpartyEULA|Optional (Opcjonalność)|Wyświetla umowę licencyjną innej firmy. Jeśli zostanie podany w danych wejściowych, wszystkie inne parametry są ignorowane| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Tworzenie pliku wejściowego dla MYSQLCredsFilePath

Parametr MySQLCredsFilePath przyjmuje plik jako dane wejściowe. Utwórz plik w następującym formacie, a następnie przekaż go jako parametr wejściowy MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Tworzenie pliku wejściowego dla ProxySettingsFilePath
Parametr ProxySettingsFilePath przyjmuje plik jako dane wejściowe. Utwórz plik w następującym formacie, a następnie przekaż go jako parametr wejściowy ProxySettingsFilePath.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```

## <a name="delete-or-unregister-a-configuration-server"></a>Usunąć lub wyrejestrować serwer konfiguracji

1. [Wyłącz ochronę](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) dla wszystkich maszyn wirtualnych na serwerze konfiguracji.
2. [Usuń skojarzenie](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) i [Usuń](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) wszystkie zasady replikacji z serwera konfiguracji.
3. [Usuń](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) wszystkie hosty vSphere/serwerach vCenter, które są skojarzone z serwerem konfiguracji.
4. W magazynie, otwórz **infrastruktura usługi Site Recovery** > **serwery konfiguracji**.
5. Wybierz serwer konfiguracji, który chcesz usunąć. Następnie na **szczegóły** wybierz opcję **Usuń**.

    ![Usuwanie serwera konfiguracji](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Usuń przy użyciu programu PowerShell

Możesz opcjonalnie usunąć serwer konfiguracji za pomocą programu PowerShell.

1. [Zainstaluj](https://docs.microsoft.com/powershell/azure/install-Az-ps) modułu Azure PowerShell.
2. Zaloguj się do konta platformy Azure za pomocą tego polecenia:

    `Connect-AzAccount`
3. Wybierz subskrypcję magazynu.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Ustaw kontekst magazynu.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Pobieranie serwera konfiguracji.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Usuwanie serwera konfiguracji.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> Możesz użyć **-Force** opcji w AzSiteRecoveryFabric Usuń wymuszonego usunięcia serwera konfiguracji.

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

## <a name="refresh-configuration-server"></a>Odśwież serwer konfiguracji

1. W witrynie Azure portal przejdź do **magazyn usługi Recovery Services** > **Zarządzaj** > **infrastruktura usługi Site Recovery**  >   **Dla maszyn VMware i fizycznych** > **serwery konfiguracji**
2. Kliknij na serwerze konfiguracji, którą chcesz odświeżyć.
3. W bloku ze szczegółowymi informacjami wybrana konfiguracja serwera, kliknij **więcej** > **Odśwież serwer**.
4. Monitorowanie postępu zadania w obszarze **magazyn usługi Recovery Services** > **monitorowanie** > **zadania usługi Site Recovery**.

## <a name="update-windows-license"></a>Aktualizuj licencję Windows

Licencja dostarczane z szablonu pakietu OVF jest ocena prawidłowe przez 180 dni. Dla nieprzerwaną pracę, musisz aktywować Windows uzyskiwanych licencją.

## <a name="failback-requirements"></a>Wymagania dotyczące powrotu po awarii

Podczas ponownego włączania ochrony i powrotu po awarii na lokalnym serwerze konfiguracji musi być uruchomiona i w stanie połączonym. Pomyślne powrotu po awarii powrót po awarii maszyny wirtualnej musi istnieć w bazie danych serwera konfiguracji.

Upewnij się, wykonanie regularnie zaplanowane kopie zapasowe serwera konfiguracji. W przypadku poważnej awarii, a serwer konfiguracji jest utracone, możesz przywrócić serwer konfiguracji z kopii zapasowej i upewnij się, że serwer konfiguracji przywróconej ma ten sam adres IP, z którym został zarejestrowany w magazynie. Powrót po awarii nie będzie działać, jeśli inny adres IP jest używany dla serwera konfiguracji przywróconych.

## <a name="next-steps"></a>Kolejne kroki

Przejrzyj samouczki dotyczące konfigurowania odzyskiwania po awarii [maszyny wirtualne VMware](vmware-azure-tutorial.md) na platformie Azure.
