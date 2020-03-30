---
title: Zarządzanie serwerem konfiguracji serwerów fizycznych w usłudze Azure Site Recovery
description: W tym artykule opisano sposób zarządzania serwerem konfiguracji usługi Azure Site Recovery do odzyskiwania po awarii serwera fizycznego na platformie Azure.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: 25be48e9caed446be3a86a11143ce3040808065a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294308"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Zarządzanie serwerem konfiguracji odzyskiwania po awarii serwera fizycznego

Konfigurowanie lokalnego serwera konfiguracji podczas korzystania z usługi [Azure Site Recovery](site-recovery-overview.md) do odzyskiwania po awarii serwerów fizycznych na platformie Azure. Serwer konfiguracji koordynuje komunikację między komputerami lokalnymi a platformą Azure i zarządza replikacją danych. W tym artykule podsumowano typowe zadania związane z zarządzaniem serwerem konfiguracji po jego wdrożeniu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W tabeli podsumowano wymagania wstępne dotyczące wdrażania komputera serwera konfiguracji lokalnej.

| **Składnik** | **Wymaganie** |
| --- |---|
| Rdzenie procesora CPU| 8 |
| Pamięć RAM | 16 GB|
| Liczba dysków | 3, w tym dysk systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania po awarii |
| Wolne miejsce na dysku (pamięć podręczna serwera przetwarzania) | 600 GB
| Wolne miejsce na dysku (dysk przechowywania) | 600 GB|
| System operacyjny  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Ustawienia regionalne systemu operacyjnego | Angielski (Stany Zjednoczone)|
| Wersja programu VMware vSphere PowerCLI | Niewymagane|
| Role systemu Windows Server | Nie włączaj tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Zasady grupy| Nie włączaj tych zasad grupy: <br> - Uniemożliwić dostęp do wiersza polecenia <br> - Uniemożliwić dostęp do narzędzi do edycji rejestru <br> - Logika zaufania dla załączników plików <br> - Włącz wykonywanie skryptów <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | - Brak istniejącej wcześniej domyślnej strony internetowej <br> - Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Włącz ustawienie [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br> - Brak istniejącej strony internetowej / nasłuchiwania aplikacji na porcie 443<br>|
| Typ karty sieciowej | VMXNET3 (po wdrożeniu jako maszyna wirtualna VMware) |
| Typ adresu IP | Statyczny |
| Dostęp do Internetu | Serwer potrzebuje dostępu do następujących adresów URL: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi(nie jest wymagane dla serwerów procesów skalowanych w poziomie) <br> - time.nist.gov <br> - time.windows.com |
| Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych)|

## <a name="download-the-latest-installation-file"></a>Pobierz najnowszy plik instalacyjny

Najnowsza wersja pliku instalacyjnego serwera konfiguracji jest dostępna w portalu Odzyskiwania witryny. Dodatkowo można go pobrać bezpośrednio z [Centrum pobierania Microsoft](https://aka.ms/unifiedsetup).

1. Zaloguj się do portalu Azure i przejdź do magazynu usług odzyskiwania.
2. Przejdź do **serwerów**konfiguracji infrastruktury > odzyskiwania**lokacji** (w obszarze Dla maszyn wirtualnych & fizycznych).
3. Kliknij przycisk **+Serwery.**
4. Na stronie **Dodaj serwer** kliknij przycisk Pobierz, aby pobrać klucz rejestracji. Ten klucz jest potrzebny podczas instalacji serwera konfiguracji, aby zarejestrować go w usłudze Azure Site Recovery.
5. Kliknij **łącze Pobierz ujednoliconą instalację usługi Microsoft Azure Site Recovery,** aby pobrać najnowszą wersję serwera konfiguracji.

   ![Strona pobierania](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Instalowanie i rejestrowanie serwera

1. Uruchom plik instalacyjny ujednoliconego instalatora.
2. W **przed rozpoczęciem**wybierz pozycję Zainstaluj serwer konfiguracji i serwer **przetwarzania**.

    ![Przed rozpoczęciem](./media/physical-manage-configuration-server/combined-wiz1.png)

3. W obszarze **Licencja na oprogramowanie innej firmy** kliknij pozycję **Akceptuję**, aby pobrać i zainstalować program MySQL.
4. W **ustawieniach internetowych**określ, jak dostawca działający na serwerze konfiguracji łączy się z usługą Azure Site Recovery przez Internet. Upewnij się, że masz dozwolone wymagane adresy URL.

    - Jeśli chcesz połączyć się z serwerem proxy, który jest obecnie skonfigurowany na komputerze, wybierz pozycję **Połącz z usługą Azure Site Recovery przy użyciu serwera proxy**.
    - Jeśli chcesz, aby dostawca łączył się bezpośrednio, wybierz pozycję **Połącz bezpośrednio z usługą Azure Site Recovery bez serwera proxy**.
    - Jeśli istniejący serwer proxy wymaga uwierzytelniania lub jeśli chcesz użyć niestandardowego serwera proxy dla połączenia z dostawcą, wybierz pozycję **Połącz z niestandardowymi ustawieniami serwera proxy**i określ adres, port i poświadczenia.
     ![Zapora](./media/physical-manage-configuration-server/combined-wiz4.png)
6. W kroku **Sprawdzanie wymagań wstępnych** Instalator przeprowadza kontrolę w celu upewnienia się, że można uruchomić instalację. Jeśli zostanie wyświetlone ostrzeżenie dotyczące **kontroli synchronizacji czasu globalnego**, sprawdź, czy czas zegara systemowego (ustawienia **Data i godzina**) jest taki sam jak dla strefy czasowej.

    ![Wymagania wstępne](./media/physical-manage-configuration-server/combined-wiz5.png)
7. W obszarze **Konfiguracja programu MySQL** utwórz poświadczenia do logowania się do zainstalowanego wystąpienia serwera programu MySQL.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. W obszarze **Szczegóły środowiska** wybierz, czy zamierzasz replikować maszyny wirtualne programu VMware. Jeśli tak, instalator sprawdza, czy jest zainstalowany program PowerCLI 6.0.
9. W obszarze **Lokalizacja instalacji** wybierz, gdzie mają zostać zainstalowane pliki binarne i gdzie ma być przechowywana pamięć podręczną. Na wybranym dysku musi być co najmniej 5 GB dostępnego miejsca, ale zalecamy dysk pamięci podręcznej z co najmniej 600 GB wolnego miejsca.

    ![Lokalizacja instalacji](./media/physical-manage-configuration-server/combined-wiz8.png)
10. W **obszarze Wybór sieci**najpierw wybierz kartę sieciową używaną przez wbudowany serwer procesowy do odnajdowania i wypychania instalacji usługi mobilności na komputerach źródłowych, a następnie wybierz kartę sieciową używaną przez serwer konfiguracji do łączności z platformą Azure. Port 9443 jest domyślnym portem używanym do wysyłania i odbierania ruchu związanego z replikacją, ale możesz zmienić ten numer portu tak, aby odpowiadał wymaganiom Twojego środowiska. Oprócz portu 9443 otwieramy też port 443, który jest używany przez serwer sieci Web do organizowania operacji replikacji. Nie należy używać portu 443 do wysyłania lub odbierania ruchu replikacji.

    ![Wybór sieci](./media/physical-manage-configuration-server/combined-wiz9.png)


11. W obszarze **Podsumowanie** przejrzyj informacje i kliknij przycisk **Zainstaluj**. Po zakończeniu instalacji generowane jest hasło. Będzie ono potrzebne po włączeniu replikacji, dlatego skopiuj je i przechowuj w bezpiecznym miejscu.


Po zakończeniu rejestracji serwer jest wyświetlany w bloku**Serwery** **ustawień** > w przechowalni.


## <a name="install-from-the-command-line"></a>Instalacja z wiersza polecenia

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
MySQLRootPassword = "Password"
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
## <a name="modify-proxy-settings"></a>Modyfikowanie ustawień serwera proxy

Ustawienia serwera proxy dla komputera serwera konfiguracji można zmodyfikować w następujący sposób:

1. Zaloguj się do serwera konfiguracji.
2. Uruchom cspsconfigtool.exe za pomocą skrótu na pulpicie.
3. Kliknij kartę **Rejestracja przechowalni.**
4. Pobierz nowy plik rejestracyjny przechowalni z portalu i podaj go jako dane wejściowe do narzędzia.

   ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Podaj nowe szczegóły serwera proxy i kliknij przycisk **Zarejestruj.**
6. Otwórz okno polecenia programu Admin PowerShell.
7. Uruchom następujące polecenie:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Jeśli do serwera konfiguracji są podłączone dodatkowe serwery procesów, należy [naprawić ustawienia serwera proxy na wszystkich serwerach procesów skalowanych](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) w poziomie we wdrożeniu.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Ponowne rejestrowanie serwera konfiguracji z tym samym przechowalnią
1. Zaloguj się do serwera konfiguracji.
2. Uruchom cspsconfigtool.exe za pomocą skrótu na pulpicie.
3. Kliknij kartę **Rejestracja przechowalni.**
4. Pobierz nowy plik rejestracyjny z portalu i podaj go jako dane wejściowe do narzędzia.
      ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Podaj szczegóły serwera proxy i kliknij przycisk **Zarejestruj.**  
6. Otwórz okno polecenia programu Admin PowerShell.
7. Uruchom następujące polecenie

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Jeśli masz wiele serwerów procesów, musisz [je ponownie zarejestrować.](vmware-azure-manage-process-server.md#reregister-a-process-server)

## <a name="register-a-configuration-server-with-a-different-vault"></a>Rejestrowanie serwera konfiguracji w innym magazynie

> [!WARNING]
> Poniższy krok odłącza serwer konfiguracji z bieżącego magazynu, a replikacja wszystkich chronionych maszyn wirtualnych w serwerze konfiguracji jest zatrzymana.

1. Zaloguj się na serwer konfiguracji
2. z wiersza polecenia administratora uruchom polecenie:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Uruchom cspsconfigtool.exe za pomocą skrótu na pulpicie.
4. Kliknij kartę **Rejestracja przechowalni.**
5. Pobierz nowy plik rejestracyjny z portalu i podaj go jako dane wejściowe do narzędzia.
6. Podaj szczegóły serwera proxy i kliknij przycisk **Zarejestruj.**  
7. Otwórz okno polecenia programu Admin PowerShell.
8. Uruchom następujące polecenie
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Uaktualnianie serwera konfiguracji

Uruchom pakiety zbiorcze aktualizacji, aby zaktualizować serwer konfiguracji. Aktualizacje mogą być stosowane do wersji N-4. Przykład:

- Jeśli korzystasz z 9.7, 9.8, 9.9 lub 9.10 - możesz uaktualnić bezpośrednio do 9.11.
- Jeśli korzystasz z wersji 9.6 lub wcześniejszej i chcesz uaktualnić do wersji 9.11, musisz najpierw uaktualnić do wersji 9.7. przed 9.11.

Łącza do aktualizacji pakietów zbiorczych w celu uaktualnienia do wszystkich wersji serwera konfiguracji są dostępne na [stronie aktualizacje typu wiki](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Uaktualnij serwer w następujący sposób:

1. Pobierz plik instalatora aktualizacji na serwer konfiguracji.
2. Kliknij dwukrotnie, aby uruchomić instalator.
3. Instalator wykrywa bieżącą wersję działającą na komputerze.
4. Kliknij **przycisk OK,** aby potwierdzić i uruchomić uaktualnienie. 


## <a name="delete-or-unregister-a-configuration-server"></a>Usuwanie lub wyrejestrowywać serwer konfiguracji

> [!WARNING]
> Przed rozpoczęciem likwidacji serwera konfiguracji należy upewnić się, że są następujące.
> 1. [Wyłącz ochronę](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) wszystkich maszyn wirtualnych w ramach tego serwera konfiguracji.
> 2. [Usuń](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) ze strony serwera konfiguracji wszystkie zasady replikacji i [usuń.](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)
> 3. [Usuń](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) wszystkie hosty vCenters/vSphere skojarzone z serwerem konfiguracji.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Usuwanie serwera konfiguracji z witryny Azure portal
1. W witrynie Azure portal przejdź do**serwerów konfiguracji** **infrastruktury** > odzyskiwania witryn z menu Przechowalnia.
2. Kliknij serwer konfiguracji, który chcesz zlikwidować.
3. Na stronie szczegóły serwera konfiguracji kliknij przycisk **Usuń.**
4. Kliknij **przycisk Tak,** aby potwierdzić usunięcie serwera.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Odinstalowywanie serwera konfiguracji i jego zależności
> [!TIP]
>   Jeśli planujesz ponownie użyć serwera konfiguracji z usługą Azure Site Recovery, możesz przejść bezpośrednio do kroku 4

1. Zaloguj się do serwera konfiguracji jako administrator.
2. Otwórz program > > Programu > odinstalowywania
3. Odinstaluj programy w następującej kolejności:
   * Agent usług odzyskiwania platformy Microsoft Azure
   * Usługa mobilności odzyskiwania witryny platformy Microsoft Azure/serwer docelowy wzorca
   * Dostawca odzyskiwania witryny platformy Microsoft Azure
   * Serwer konfiguracji/procesy odzyskiwania witryny microsoft Azure
   * Zależności serwera konfiguracji odzyskiwania witryny platformy Microsoft Azure
   * Serwer MySQL 5.5
4. Uruchom następujące polecenie z wiersza polecenia administratora i administratora.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Usuwanie lub wyrejestrowanie serwera konfiguracji (PowerShell)

1. [Instalacja](https://docs.microsoft.com/powershell/azure/install-Az-ps) Moduł programu Azure PowerShell
2. Zaloguj się do konta platformy Azure za pomocą polecenia
    
    `Connect-AzAccount`
3. Wybierz subskrypcję, w której znajduje się przechowalnia

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Teraz skonfiguruj kontekst przechowalni
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Pobierz pozycję wybierz serwer konfiguracji

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Usuwanie serwera konfiguracji

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> Opcja **-Force** w remove-AzSiteRecoveryFabric może być użyta do wymuszenia usunięcia/usunięcia serwera konfiguracji.

## <a name="renew-ssl-certificates"></a>Odnawianie certyfikatów SSL
Serwer konfiguracji ma wbudowany serwer sieci web, który organizuje działania usługi mobilności, serwerów przetwarzania i głównych serwerów docelowych podłączonych do niego. Serwer sieci web używa certyfikatu SSL do uwierzytelniania klientów. Certyfikat wygasa po trzech latach i może zostać odnowiony w dowolnym momencie.

### <a name="check-expiry"></a>Sprawdź wygaśnięcie

W przypadku wdrożeń serwerów konfiguracji przed majem 2016 r. wygaśnięcie certyfikatu zostało ustawione na jeden rok. Jeśli masz certyfikat ma wygasnąć, wystąpią następujące czynności:

- Gdy data wygaśnięcia wynosi dwa miesiące lub mniej, usługa rozpoczyna wysyłanie powiadomień w portalu i pocztą e-mail (jeśli zasubskrybujesz powiadomienia usługi Azure Site Recovery).
- Baner powiadomień pojawi się na stronie zasobu przechowalni. Kliknij baner, aby uzyskać więcej informacji.
- Jeśli zostanie wyświetlony przycisk **Uaktualnij teraz,** oznacza to, że istnieją pewne składniki w środowisku, które nie zostały uaktualnione do wersji 9.4.xxxx.x lub nowszej. Uaktualnianie składników przed odnowieniem certyfikatu. Nie można odnowić w starszych wersjach.

### <a name="renew-the-certificate"></a>Odnawianie certyfikatu

1. W przechowalni otwórz serwer konfiguracji **infrastruktury odzyskiwania lokacji** > **Configuration Server**i kliknij wymagany serwer konfiguracji.
2. Data wygaśnięcia jest wyświetlana w obszarze **Kondycja serwera konfiguracji**
3. Kliknij przycisk **Odnów certyfikaty**. 




## <a name="common-issues"></a>Typowe problemy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkami konfigurowania odzyskiwania po awarii [serwerów fizycznych](tutorial-physical-to-azure.md) na platformie Azure.

