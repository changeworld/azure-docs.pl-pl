---
title: Zarządzanie serwerem konfiguracji w celu odzyskiwania po awarii za pomocą usługi Azure Site Recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: ramamill
ms.openlocfilehash: 93b10d56ae34ebdfe78dd20705634dea58721274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257279"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>Zarządzanie serwerem konfiguracji odzyskiwania po awarii VMware VMware VMware VM/server physical server

Konfigurowanie lokalnego serwera konfiguracji podczas korzystania z [usługi Azure Site Recovery](site-recovery-overview.md) do odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych na platformie Azure. Serwer konfiguracji koordynuje komunikację między lokalnymi urządzeniami VMware i platformą Azure i zarządza replikacją danych. W tym artykule podsumowano typowe zadania związane z zarządzaniem serwerem konfiguracji po jego wdrożeniu.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>Aktualizowanie licencji systemu Windows

Licencja dostarczona z szablonem OVF jest licencją ewaluacjską ważną przez 180 dni. Aby uzyskać nieprzerwane użytkowanie, należy aktywować system Windows za pomocą zakupionej licencji. Aktualizacja licencji może być wykonana za pomocą klucza autonomicznego lub klucza standardowego usługi zarządzania kluczem. Wskazówki są dostępne w [wierszu polecenia systemu DISM Windows do uruchamiania systemu operacyjnego](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options). Aby uzyskać klucze, zapoznaj się z konfiguracją [klienta usługi zarządzania kluczami](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys).

## <a name="access-configuration-server"></a>Serwer konfiguracji programu Access

Dostęp do serwera konfiguracji można uzyskać w następujący sposób:

* Zaloguj się do maszyny Wirtualnej, na której jest wdrożona, i uruchom **program Azure Site Recovery Configuration Manager** ze skrótu pulpitu.
* Alternatywnie można uzyskać zdalny dostęp do serwera konfiguracji z https://*ConfigurationServerName*/:44315/. Zaloguj się przy użyciu poświadczeń administratora.

## <a name="modify-vmware-server-settings"></a>Modyfikowanie ustawień serwera VMware

1. Aby skojarzyć inny serwer VMware z serwerem konfiguracyjnym, po [zalogowaniu](#access-configuration-server)wybierz pozycję **Dodaj serwer vCenter Server/vSphere ESXi**.
2. Wprowadź szczegóły, a następnie wybierz **przycisk OK**.

## <a name="modify-credentials-for-automatic-discovery"></a>Modyfikowanie poświadczeń w celu automatycznego odnajdowania

1. Aby zaktualizować poświadczenia używane do łączenia się z serwerem VMware w celu automatycznego odnajdywania maszyn wirtualnych VMware, po [zalogowaniu](#access-configuration-server)wybierz konto i kliknij przycisk **Edytuj**.
2. Wprowadź nowe poświadczenia, a następnie wybierz przycisk **OK**.

    ![Modyfikowanie oprogramowania VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

Poświadczenia można również modyfikować za pośrednictwem pliku CSPSConfigtool.exe.

1. Zaloguj się na serwer konfiguracji i uruchom CSPSConfigtool.exe
2. Wybierz konto, które chcesz zmodyfikować, i kliknij przycisk **Edytuj**.
3. Wprowadź zmodyfikowane poświadczenia i kliknij przycisk **Ok**

## <a name="modify-credentials-for-mobility-service-installation"></a>Modyfikowanie poświadczeń dla instalacji usługi mobilności

Zmodyfikuj poświadczenia używane do automatycznego instalowania usługi mobilności na maszynach wirtualnych VMware, które są włączane do replikacji.

1. Po [zalogowaniu](#access-configuration-server)wybierz pozycję **Zarządzaj poświadczeniami maszyny wirtualnej**
2. Wybierz konto, które chcesz zmodyfikować, i kliknij pozycję **Edytuj**
3. Wprowadź nowe poświadczenia, a następnie wybierz przycisk **OK**.

    ![Modyfikowanie poświadczeń usługi mobilności](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

Poświadczenia można również modyfikować za pośrednictwem pliku CSPSConfigtool.exe.

1. Zaloguj się do serwera konfiguracji i uruchom cspsconfigtool.exe
2. Wybierz konto, które chcesz zmodyfikować, i kliknij pozycję **Edytuj**
3. Wprowadź nowe poświadczenia i kliknij przycisk **Ok**.

## <a name="add-credentials-for-mobility-service-installation"></a>Dodawanie poświadczeń dla instalacji usługi mobilności

W przypadku pominięcia dodawania poświadczeń podczas wdrażania serwera konfiguracyjnego OVF,

1. Po [zalogowaniu](#access-configuration-server)wybierz pozycję **Zarządzaj poświadczeniami maszyny wirtualnej**.
2. Kliknij **pozycję Dodaj poświadczenia maszyny wirtualnej**.
    ![poświadczenia dodatkowej mobilności](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Wprowadź nowe poświadczenia i kliknij **dodaj**.

Poświadczenia można również dodawać za pośrednictwem pliku CSPSConfigtool.exe.

1. Zaloguj się do serwera konfiguracji i uruchom cspsconfigtool.exe
2. Kliknij **przycisk Dodaj**, wprowadź nowe poświadczenia i kliknij przycisk **Ok**.

## <a name="modify-proxy-settings"></a>Modyfikowanie ustawień serwera proxy

Zmodyfikuj ustawienia serwera proxy używane przez komputer serwera konfiguracji w celu uzyskania dostępu do Internetu na platformie Azure. Jeśli masz komputer serwera przetwarzania oprócz domyślnego serwera przetwarzania uruchomionego na komputerze serwera konfiguracji, zmodyfikuj ustawienia na obu komputerach.

1. Po [zalogowaniu](#access-configuration-server) się na serwerze konfiguracji wybierz pozycję **Zarządzaj łącznością**.
2. Zaktualizuj wartości serwera proxy. Następnie wybierz pozycję **Zapisz,** aby zaktualizować ustawienia.

## <a name="add-a-network-adapter"></a>Dodawanie karty sieciowej

Szablon Format otwartej wirtualizacji (OVF) wdraża maszynę wirtualną serwera konfiguracji z jedną kartą sieciową.

- Można [dodać dodatkową kartę do maszyny Wirtualnej](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), ale należy ją dodać przed zarejestrowaniem serwera konfiguracji w przechowalni.
- Aby dodać kartę po zarejestrowaniu serwera konfiguracji w przechowalni, dodaj kartę we właściwościach maszyny Wirtualnej. Następnie należy [ponownie zarejestrować](#reregister-a-configuration-server-in-the-same-vault) serwer w przechowalni.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Ponowne rejestrowanie serwera konfiguracji w tym samym magazynie

W razie potrzeby można ponownie zarejestrować serwer konfiguracji w tym samym magazynie. Jeśli masz dodatkowy komputer serwera przetwarzania, oprócz domyślnego serwera przetwarzania uruchomionego na komputerze serwera konfiguracji, należy ponownie zarejestrować oba komputery.


1. W przechowalni otwórz otwórz stronę **Zarządzaj serwerami** > konfiguracji**infrastruktury** > odzyskiwania**lokacji**.
2. W **obszarze Serwery**wybierz pozycję **Pobierz klucz rejestracji,** aby pobrać plik poświadczeń przechowalni.
3. Zaloguj się do komputera serwera konfiguracji.
4. W **obszarze %ProgramData%\ASR\home\svsystems\bin**otwórz **plik cspsconfigtool.exe**.
5. Na karcie **Rejestracja przechowalni** wybierz pozycję **Przeglądaj**i znajdź pobrany plik poświadczeń przechowalni.
6. W razie potrzeby podaj szczegóły serwera proxy. Następnie wybierz pozycję **Zarejestruj**.
7. Otwórz okno polecenia programu PowerShell administratora i uruchom następujące polecenie:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >Aby **pobierać najnowsze certyfikaty** z serwera konfiguracji do serwera procesów skalowanych w poziomie, wykonaj polecenie *"\<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe>" --registermt*

8. Na koniec uruchom ponownie oszurzaj, wykonując następujące polecenie.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Rejestrowanie serwera konfiguracji w innym magazynie

> [!WARNING]
> Poniższy krok odłącza serwer konfiguracji z bieżącego magazynu, a replikacja wszystkich chronionych maszyn wirtualnych w serwerze konfiguracji jest zatrzymana.

1. Zaloguj się do serwera konfiguracji.
2. Otwórz okno polecenia programu PowerShell administratora i uruchom następujące polecenie:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Uruchom portal przeglądarki urządzenia serwera konfiguracji za pomocą skrótu na pulpicie.
4. Wykonaj kroki rejestracji podobne do rejestracji [nowego](vmware-azure-tutorial.md#register-the-configuration-server)serwera konfiguracji .

## <a name="upgrade-the-configuration-server"></a>Uaktualnianie serwera konfiguracji

Uruchom pakiety zbiorcze aktualizacji, aby zaktualizować serwer konfiguracji. Aktualizacje mogą być stosowane do wersji N-4. Przykład:

- Jeśli uruchomisz 9.7, 9.8, 9.9 lub 9.10, możesz uaktualnić bezpośrednio do 9.11.
- Jeśli uruchomisz wersję 9.6 lub wcześniejszą i chcesz uaktualnić do wersji 9.11, musisz najpierw uaktualnić do wersji 9.7. przed 9.11.

Szczegółowe wskazówki dotyczące obsługi składników usługi Azure Site Recovery można znaleźć [w tym miejscu](https://aka.ms/asr_support_statement).
Łącza do aktualizacji pakietów zbiorczych w celu uaktualnienia do wszystkich wersji serwera konfiguracji są dostępne [tutaj](https://aka.ms/asr_update_rollups).

> [!IMPORTANT]
> Z każdej nowej wersji 'N' składnika usługi Azure Site Recovery, który jest zwolniony, wszystkie wersje poniżej "N-4" jest uważany za z pomocy technicznej. Zawsze zaleca się uaktualnienie do najnowszych dostępnych wersji.</br>
> Szczegółowe wskazówki dotyczące obsługi składników usługi Azure Site Recovery można znaleźć [w tym miejscu](https://aka.ms/asr_support_statement).

Uaktualnij serwer w następujący sposób:

1. W przechowalni przejdź do **witryny Zarządzanie serwerami** > konfiguracji**infrastruktury** > odzyskiwania**lokacji**.
2. Jeśli aktualizacja jest dostępna, w kolumnie **Wersja agenta** > zostanie wyświetlene łącze.
    ![Aktualizowanie](./media/vmware-azure-manage-configuration-server/update2.png)
3. Pobierz plik instalatora aktualizacji na serwer konfiguracji.

    ![Aktualizacja](./media/vmware-azure-manage-configuration-server/update1.png)

4. Kliknij dwukrotnie, aby uruchomić instalator.
5. Instalator wykrywa bieżącą wersję działającą na komputerze. Kliknij **przycisk Tak,** aby rozpocząć uaktualnienie.
6. Po zakończeniu uaktualniania konfiguracja serwera sprawdza poprawność.

    ![Aktualizacja](./media/vmware-azure-manage-configuration-server/update3.png)

7. Kliknij **przycisk Zakończ,** aby zamknąć instalator.
8. Aby uaktualnić pozostałe składniki usługi Site Recovery, zapoznaj się z naszymi [wskazówkami dotyczącymi uaktualniania.](https://aka.ms/asr_vmware_upgrades)

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Uaktualnianie serwera konfiguracji/serwera przetwarzania z wiersza polecenia

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

|Nazwa parametru| Typ | Opis| Wartości|
|-|-|-|-|
| /ServerMode|Wymagany|Określa, czy mają zostać zainstalowane oba serwery, konfiguracji i przetwarzania, czy tylko serwer przetwarzania|CS<br>PS|
|/InstallLocation|Wymagany|Folder, w którym są instalowane składniki| Dowolny folder na komputerze|
|/MySQLCredsFilePath|Wymagany|Ścieżka pliku, w której są przechowywane poświadczenia serwera MySQL|Plik powinien mieć format określony poniżej|
|/VaultCredsFilePath|Wymagany|Ścieżka pliku poświadczeń magazynu|Prawidłowa ścieżka pliku|
|/EnvType|Wymagany|Typ środowiska, które chcesz chronić |VMware<br>NonVMware|
|/PSIP|Wymagany|Adres IP karty sieciowej do użytku podczas przesyłania danych replikacji| Dowolny prawidłowy adres IP|
|/CSIP|Wymagany|Adres IP karty sieciowej, na której nasłuchuje serwer konfiguracji| Dowolny prawidłowy adres IP|
|/PassphraseFilePath|Wymagany|Pełna ścieżka do lokalizacji pliku hasła|Prawidłowa ścieżka pliku|
|/BypassProxy|Optional (Opcjonalność)|Określa, że serwer konfiguracji łączy się z platformą Azure bez serwera proxy|Należy uzyskać tę wartość z Venu|
|/ProxySettingsFilePath|Optional (Opcjonalność)|Ustawienia serwera proxy (domyślny serwer proxy wymaga uwierzytelniania lub niestandardowy serwer proxy)|Plik powinien mieć format określony poniżej|
|DataTransferSecurePort|Optional (Opcjonalność)|Numer portu dla protokołu PSIP do użytku z danymi replikacji| Prawidłowy numer portu (wartość domyślna to 9433)|
|/SkipSpaceCheck|Optional (Opcjonalność)|Pomiń sprawdzanie miejsca dla dysku pamięci podręcznej| |
|/AcceptThirdpartyEULA|Wymagany|Flaga implikuje akceptację umowy licencyjnej innego producenta| |
|/ShowThirdpartyEULA|Optional (Opcjonalność)|Wyświetla umowę licencyjną innej firmy. Jeśli zostanie podany w danych wejściowych, wszystkie inne parametry są ignorowane| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Tworzenie danych wejściowych dla programu MYSQLCredsFilePath

Parametr MySQLCredsFilePath przyjmuje plik jako dane wejściowe. Utwórz plik przy użyciu następującego formatu i przekaż go jako parametr MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Tworzenie danych wejściowych pliku dla serwera proxysettingsFilePath
Parametr ProxySettingsFilePath przyjmuje plik jako dane wejściowe. Utwórz plik przy użyciu następującego formatu i przekaż go jako wejściowy parametr ProxySettingsFilePath.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```

## <a name="delete-or-unregister-a-configuration-server"></a>Usuwanie lub wyrejestrowywać serwer konfiguracji

1. [Wyłącz ochronę](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) wszystkich maszyn wirtualnych w ramach serwera konfiguracji.
2. [Usuń](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) ze strony serwera konfiguracji wszystkie zasady replikacji i [usuń](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) je.
3. [Usuń](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) wszystkie hosty vCenter/vSphere skojarzone z serwerem konfiguracji.
4. W przechowalni otwórz **stronę Serwery**konfiguracji infrastruktury > odzyskiwania**lokacji**.
5. Wybierz serwer konfiguracji, który chcesz usunąć. Następnie na stronie **Szczegóły** wybierz pozycję **Usuń**.

    ![Usuwanie serwera konfiguracji](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Usuwanie za pomocą programu PowerShell

Opcjonalnie można usunąć serwer konfiguracji przy użyciu programu PowerShell.

1. [Zainstaluj](https://docs.microsoft.com/powershell/azure/install-Az-ps) moduł programu Azure PowerShell.
2. Zaloguj się do konta platformy Azure za pomocą tego polecenia:

    `Connect-AzAccount`
3. Wybierz subskrypcję przechowalni.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Ustawianie kontekstu przechowalni.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Pobierz serwer konfiguracji.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Usuń serwer konfiguracji.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> W remove-AzSiteRecoveryFabric można użyć opcji **-Force** w celu wymuszonego usunięcia serwera konfiguracyjnego.

## <a name="generate-configuration-server-passphrase"></a>Generowanie hasła serwera konfiguracji

1. Zaloguj się do serwera konfiguracji, a następnie otwórz okno wiersza polecenia jako administrator.
2. Aby zmienić katalog na folder bin, wykonaj polecenie **cd %ProgramData%\ASR\home\svsystems\bin**
3. Aby wygenerować plik hasła, wykonaj **plik genpassphrase.exe -v > MobSvc.passphrase**.
4. Hasło będzie przechowywane w pliku znajdującym się pod adresem **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="renew-ssl-certificates"></a>Odnawianie certyfikatów SSL

Serwer konfiguracji ma wbudowany serwer sieci web, który organizuje działania usługi mobilności, serwery przetwarzania i główne serwery docelowe podłączone do niego. Serwer sieci web używa certyfikatu SSL do uwierzytelniania klientów. Certyfikat wygasa po trzech latach i może zostać odnowiony w dowolnym momencie.

### <a name="check-expiry"></a>Sprawdź wygaśnięcie

W przypadku wdrożeń serwerów konfiguracji przed majem 2016 r. wygaśnięcie certyfikatu zostało ustawione na jeden rok. Jeśli masz certyfikat, który wygaśnie, wystąpią następujące zdarzenia:

- Gdy data wygaśnięcia wynosi co najmniej dwa miesiące, usługa rozpoczyna wysyłanie powiadomień w portalu i pocztą e-mail (jeśli zasubskrybujesz powiadomienia o odzyskiwaniu witryn).
- Baner powiadomień pojawi się na stronie zasobu przechowalni. Aby uzyskać więcej informacji, wybierz baner.
- Jeśli zostanie wyświetlony przycisk **Uaktualnij teraz,** oznacza to, że niektóre składniki w twoim środowisku nie zostały uaktualnione do wersji 9.4.xxxx.x lub nowszych. Uaktualnij składniki przed odnowieniem certyfikatu. Nie można odnowić w starszych wersjach.

### <a name="renew-the-certificate"></a>Odnawianie certyfikatu

1. W przechowalni otwórz serwer konfiguracji **infrastruktury odzyskiwania lokacji** > **Configuration Server**. Wybierz wymagany serwer konfiguracji.
2. Data wygaśnięcia jest wyświetlana w obszarze **Kondycja serwera konfiguracji**.
3. Wybierz **pozycję Odnów certyfikaty**.

## <a name="refresh-configuration-server"></a>Serwer konfiguracji odświeżania

1. W witrynie Azure portal przejdź do usługi odzyskiwania za pomocą**Manage** >  > **infrastruktury** > odzyskiwania **witryny**usługi Odzyskiwania witryny dla**serwerów konfiguracyjnych** **maszyn VMware & komputerów** > fizycznych
2. Kliknij serwer konfiguracji, który chcesz odświeżyć.
3. Na bloku ze szczegółami wybranego serwera konfiguracji kliknij pozycję **Więcej** > **odświeżania serwera**.
4. Monitorowanie postępu zadania w obszarze**Monitoring** > **Zadania odzyskiwania**usługi monitorowania usługi odzyskiwania usługi **odzyskiwania** > .

## <a name="failback-requirements"></a>Wymagania dotyczące powrotu po awarii

Podczas ponownego odtwarzania i powrotu po awarii lokalny serwer konfiguracji musi być uruchomiony i jest połączony. W przypadku pomyślnego powrotu po awarii maszyna wirtualna, która nie powiodła się, musi istnieć w bazie danych serwera konfiguracji.

Upewnij się, że regularnie można wykonać zaplanowane kopie zapasowe serwera konfiguracji. W przypadku wystąpienia awarii i utraty serwera konfiguracji należy najpierw przywrócić serwer konfiguracji z kopii zapasowej i upewnić się, że serwer konfiguracji przywróconej ma ten sam adres IP, z którym został zarejestrowany w przechowalni. Powrót po awarii nie będzie działać, jeśli dla przywróconego serwera konfiguracji używany jest inny adres IP.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkami konfigurowania odzyskiwania po awarii [maszyn wirtualnych VMware](vmware-azure-tutorial.md) na platformie Azure.
