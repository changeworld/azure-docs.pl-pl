---
title: " Zarządzanie serwera konfiguracji na potrzeby odzyskiwania po awarii serwera fizycznego z usługą Azure Site Recovery | Dokumentacja firmy Microsoft"
description: W tym artykule opisano sposób zarządzania istniejącego serwera konfiguracji na potrzeby odzyskiwania po awarii serwera fizycznego na platformie Azure, z usługą Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 04/11/2018
ms.author: anoopkv
ms.openlocfilehash: 580d32a51f6b38916ddccd46784b80b1179c29c4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Zarządzanie serwera konfiguracji na potrzeby odzyskiwania po awarii serwera fizycznego

Lokalny serwer konfiguracji należy skonfigurować korzystając z [usługi Azure Site Recovery](site-recovery-overview.md) usługi odzyskiwania po awarii serwerów fizycznych do platformy Azure. Serwer konfiguracji koordynuje komunikacji między komputerami lokalnymi i Azure i zarządza replikacji danych. Ten artykuł zawiera podsumowanie typowych zadań zarządzania serwera konfiguracji po jego wdrożeniu.

## <a name="prerequisites"></a>Wymagania wstępne

W tabeli przedstawiono wymagania wstępne dotyczące wdrażania na lokalnej maszynie serwera konfiguracji.

| **Składnik** | **Wymaganie** |
| --- |---|
| Rdzenie procesora CPU| 8 |
| Pamięć RAM | 16 GB|
| Liczba dysków | 3, w tym dysku systemu operacyjnego, dysku pamięci podręcznej serwera przetwarzania i przechowywania dysku powrotu po awarii |
| Wolne miejsce na dysku (pamięć podręczna serwera przetwarzania) | 600 GB
| Wolne miejsce na dysku (dysk przechowywania) | 600 GB|
| System operacyjny  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Ustawienia regionalne systemu operacyjnego | English (US)|
| Wersja programu VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Role systemu Windows Server | Nie włączaj tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Zasady grupy| Nie włączaj tych zasad grupy: <br> -Uniemożliwić dostęp do wiersza polecenia <br> -Uniemożliwić dostęp do narzędzia do edycji rejestru <br> — Logika zaufania dla załączników plików <br> -Włącz wykonywanie skryptu <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | -Brak istniejącego domyślnej witryny sieci Web <br> -Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Włącz [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ustawienie  <br> -Brak istniejącego witryny sieci Web/aplikacja nasłuchuje na porcie 443<br>|
| Typ karty Sieciowej | VMXNET3 (jeśli są wdrażane jako maszyny Wirtualnej VMware) |
| Typ adresu IP | Statyczny |
| Dostęp do Internetu | Serwer musi mieć dostęp do tych adresów URL: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - dc.services.visualstudio.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (nie wymaga proces skalowania w poziomie serwerów) <br> - time.nist.gov <br> - time.windows.com |
| Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych)|

## <a name="download-the-latest-installation-file"></a>Pobierz najnowszy plik instalacyjny

Najnowszą wersję pliku instalacyjnego serwera konfiguracji jest dostępna w portalu usługi Site Recovery. Ponadto można go pobrać bezpośrednio z [Microsoft Download Center](http://aka.ms/unifiedsetup).

1. Zaloguj się do portalu Azure i przejdź do magazynu usług odzyskiwania.
2. Przejdź do **infrastrukturę odzyskiwania lokacji** > **serwery konfiguracji** (w obszarze VMware i maszyn fizycznych).
3. Kliknij przycisk **+ serwery** przycisku.
4. Na **Dodaj serwer** kliknij przycisk pobierania, aby pobrać klucz rejestracji. Ten klucz jest wymagane podczas instalacji serwera konfiguracji, aby zarejestrować go z usługą Azure Site Recovery.
5. Kliknij przycisk **Pobierz Instalatora Microsoft Azure Site Recovery Unified** łącze, aby pobrać najnowszą wersję serwera konfiguracji.

  ![Strona pobierania](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Zainstaluj i Zarejestruj serwer

1. Uruchom plik instalacyjny ujednoliconego instalatora.
2. W **przed rozpoczęciem**, wybierz pozycję **zainstalować serwer konfiguracji i serwera przetwarzania**.

    ![Przed rozpoczęciem](./media/physical-manage-configuration-server/combined-wiz1.png)

3. W obszarze **Licencja na oprogramowanie innej firmy** kliknij pozycję **Akceptuję**, aby pobrać i zainstalować program MySQL.
4. W obszarze **Ustawienia internetowe** określ, jak dostawca działający na serwerze konfiguracji ma się łączyć z usługą Azure Site Recovery przez Internet. Upewnij się, że zezwolono odpowiednie adresy URL.

    - Jeśli chcesz się połączyć z serwerem proxy aktualnie skonfigurowany na komputerze, wybierz opcję **nawiązywanie połączenia z usługi Azure Site Recovery przy użyciu serwera proxy**.
    - Jeśli chcesz, aby dostawca łączył się bezpośrednio, wybierz opcję **Połącz bezpośrednio z usługi Azure Site Recovery bez serwera proxy**.
    - Jeśli istniejący serwer proxy wymaga uwierzytelnienia, lub jeśli chcesz użyć niestandardowego serwera proxy dla połączenia dostawcy, wybierz opcję **Połącz przy użyciu niestandardowych ustawień serwera proxy**i określ adres, port oraz poświadczenia.
     ![Zapora](./media/physical-manage-configuration-server/combined-wiz4.png)
6. W kroku **Sprawdzanie wymagań wstępnych** Instalator przeprowadza kontrolę w celu upewnienia się, że można uruchomić instalację. Jeśli zostanie wyświetlone ostrzeżenie dotyczące **kontroli synchronizacji czasu globalnego**, sprawdź, czy czas zegara systemowego (ustawienia **Data i godzina**) jest taki sam jak dla strefy czasowej.

    ![Wymagania wstępne](./media/physical-manage-configuration-server/combined-wiz5.png)
7. W obszarze **Konfiguracja programu MySQL** utwórz poświadczenia do logowania się do zainstalowanego wystąpienia serwera programu MySQL.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. W obszarze **Szczegóły środowiska** wybierz, czy zamierzasz replikować maszyny wirtualne programu VMware. Jeśli jesteś, Instalator sprawdza, czy zainstalowana jest PowerCLI 6.0.
9. W obszarze **Lokalizacja instalacji** wybierz, gdzie mają zostać zainstalowane pliki binarne i gdzie ma być przechowywana pamięć podręczną. Na wybranym dysku musi być co najmniej 5 GB dostępnego miejsca, ale zalecamy dysk pamięci podręcznej z co najmniej 600 GB wolnego miejsca.

    ![Lokalizacja instalacji](./media/physical-manage-configuration-server/combined-wiz8.png)
10. W obszarze **Wybór sieci** określ odbiornik (kartę sieciową i port SSL), za pomocą którego serwer konfiguracji będzie wysyłać i odbierać dane replikacji. Port 9443 jest domyślnym portem używanym do wysyłania i odbierania ruchu związanego z replikacją, ale możesz zmienić ten numer portu tak, aby odpowiadał wymaganiom Twojego środowiska. Oprócz portu 9443 otwieramy też port 443, który jest używany przez serwer sieci Web do organizowania operacji replikacji. Nie należy używać portu 443 do wysyłania i odbierania ruchu związanego z replikacją.

    ![Wybór sieci](./media/physical-manage-configuration-server/combined-wiz9.png)


11. W obszarze **Podsumowanie** przejrzyj informacje i kliknij przycisk **Zainstaluj**. Po zakończeniu instalacji generowane jest hasło. Będzie ono potrzebne po włączeniu replikacji, dlatego skopiuj je i przechowuj w bezpiecznym miejscu.


Po zakończeniu rejestracji serwer jest wyświetlany w bloku **Ustawienia** > **Serwery** w magazynie.


## <a name="install-from-the-command-line"></a>Zainstaluj z poziomu wiersza polecenia

Uruchom plik instalacyjny w następujący sposób:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Przykładowe zastosowanie
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parametry

|Nazwa parametru| Typ | Opis| Wartości|
|-|-|-|-|
| /ServerMode|Wymagane|Określa, czy mają zostać zainstalowane oba serwery, konfiguracji i przetwarzania, czy tylko serwer przetwarzania|CS<br>PS|
|/InstallLocation|Wymagane|Folder, w którym są instalowane składniki| Dowolny folder na komputerze|
|/MySQLCredsFilePath|Wymagane|Ścieżka pliku, w której są przechowywane poświadczenia serwera MySQL|Plik powinien mieć format określony poniżej|
|/VaultCredsFilePath|Wymagane|Ścieżka pliku poświadczeń magazynu|Prawidłowa ścieżka pliku|
|/EnvType|Wymagane|Typ środowiska, które ma być chronione |VMware<br>NonVMware|
|/PSIP|Wymagane|Adres IP karty sieciowej do użytku podczas przesyłania danych replikacji| Dowolny prawidłowy adres IP|
|/CSIP|Wymagane|Adres IP karty sieciowej, na której nasłuchuje serwer konfiguracji| Dowolny prawidłowy adres IP|
|/PassphraseFilePath|Wymagane|Pełna ścieżka do lokalizacji pliku hasła|Prawidłowa ścieżka pliku|
|/BypassProxy|Optional (Opcjonalność)|Określa, że serwer konfiguracji łączy się z platformą Azure bez serwera proxy|Należy uzyskać tę wartość z Venu|
|/ProxySettingsFilePath|Optional (Opcjonalność)|Ustawienia serwera proxy (domyślny serwer proxy wymaga uwierzytelniania lub niestandardowy serwer proxy)|Plik powinien mieć format określony poniżej|
|DataTransferSecurePort|Optional (Opcjonalność)|Numer portu dla protokołu PSIP do użytku z danymi replikacji| Prawidłowy numer portu (wartość domyślna to 9433)|
|/SkipSpaceCheck|Optional (Opcjonalność)|Pomiń sprawdzanie miejsca dla dysku pamięci podręcznej| |
|/AcceptThirdpartyEULA|Wymagane|Flaga implikuje akceptację umowy licencyjnej innego producenta| |
|/ShowThirdpartyEULA|Optional (Opcjonalność)|Wyświetla umowę licencyjną innej firmy. Jeśli zostanie podany w danych wejściowych, wszystkie inne parametry są ignorowane| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Utwórz plik danych wejściowych MYSQLCredsFilePath

Parametr MySQLCredsFilePath przyjmuje pliku jako dane wejściowe. Utwórz plik w następującym formacie, a następnie przekaż go jako parametr wejściowy MySQLCredsFilePath.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Utwórz plik danych wejściowych ProxySettingsFilePath
Parametr ProxySettingsFilePath przyjmuje pliku jako dane wejściowe. Utwórz plik w następującym formacie, a następnie przekaż go jako parametr wejściowy ProxySettingsFilePath.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Zmodyfikuj ustawienia serwera proxy

Ustawienia serwera proxy dla komputera serwera konfiguracji można zmodyfikować w następujący sposób:

1. Zaloguj się na serwerze konfiguracji.
2. Uruchamianie cspsconfigtool.exe za pomocą skrótu na użytkownika.
3. Kliknij przycisk **rejestracji magazynu** kartę.
4. Pobranie nowego pliku rejestracji magazynu z portalu, a następnie przekazać go jako dane wejściowe do narzędzia.

  ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Podaj nowe szczegóły serwera proxy i kliknij przycisk **zarejestrować** przycisku.
6. Otwórz okno poleceń programu PowerShell administratora.
7. Uruchom następujące polecenie:
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Jeśli masz serwery dodatkowych procesów podłączone do serwera konfiguracji należy [Popraw ustawienia serwera proxy na wszystkich serwerach proces skalowania w poziomie](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) w danym wdrożeniu.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Zarejestruj ponownie serwer konfiguracji, w tym samym magazynie
  1. Zaloguj się do serwera konfiguracji.
  2. Uruchom cspsconfigtool.exe za pomocą skrótu na pulpicie.
  3. Kliknij przycisk **rejestracji magazynu** kartę.
  4. Pobranie nowego pliku rejestracji z portalu i udostępnij go jako dane wejściowe do narzędzia.
        ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
  5. Podaj szczegóły serwera Proxy, a następnie kliknij przycisk **zarejestrować** przycisku.  
  6. Otwórz okno poleceń programu PowerShell administratora.
  7. Uruchom następujące polecenie

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  Jeśli masz wiele serwerów procesu należy [ponownie je zarejestrować](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Zarejestruj serwer konfiguracji w innym magazynie

> [!WARNING]
> Następny krok powoduje usunięcie serwera konfiguracji z bieżącego magazynu, a następnie replikację chronionych maszyn wirtualnych w ramach serwera konfiguracji jest zatrzymana.

1. Zaloguj się do konfiguracji serwera
2. z wiersza polecenia z uprawnieniami administratora uruchom polecenie:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Uruchom cspsconfigtool.exe za pomocą skrótu na pulpicie.
4. Kliknij przycisk **rejestracji magazynu** kartę.
5. Pobranie nowego pliku rejestracji z portalu i udostępnij go jako dane wejściowe do narzędzia.
6. Podaj szczegóły serwera Proxy, a następnie kliknij przycisk **zarejestrować** przycisku.  
7. Otwórz okno poleceń programu PowerShell administratora.
8. Uruchom następujące polecenie
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Uaktualnianie serwera konfiguracji

Możesz uruchomić pakiety zbiorcze aktualizacji, aby zaktualizować serwer konfiguracji. Aktualizacje mogą być stosowane dla maksymalnie N-4 wersji. Na przykład:

- Jeśli używasz 9.7, 9,8, 9.9 lub 9.10 — można uaktualnić bezpośrednio do 9.11.
- Jeśli używasz 9.6 lub starszym, i chcesz uaktualnić do 9.11, należy najpierw uaktualnić do wersji 9.7. przed 9.11.

Łącza do pakiety zbiorcze aktualizacji dla uaktualnienia wszystkich wersji serwera konfiguracji są dostępne w [strona typu wiki aktualizacji](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Uaktualnij serwer w następujący sposób:

1. Pobierz plik Instalatora aktualizacji na serwerze konfiguracji.
2. Kliknij dwukrotnie, aby uruchomić Instalatora.
3. Instalator wykrywa bieżącą wersję uruchomionych na komputerze.
4. Kliknij przycisk **OK** do potwierdzenia, a następnie uruchom uaktualnianie. 


## <a name="delete-or-unregister-a-configuration-server"></a>Usuń lub Wyrejestruj serwer konfiguracji

> [!WARNING]
> Upewnij się przed rozpoczęciem likwidowania serwera konfiguracji.
> 1. [Wyłącz ochronę](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) dla wszystkich maszyn wirtualnych na tym serwerze konfiguracji.
> 2. [Usuń skojarzenie](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) i [usunąć](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) wszystkie zasady replikacji z serwera konfiguracji.
> 3. [Usuń](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) wszystkie hosty serwerów/vSphere Vcenter, które są skojarzone z serwerem konfiguracji.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Usuń serwer konfiguracji z portalu Azure
1. W portalu Azure, przejdź do **infrastruktura usługi Site Recovery** > **serwery konfiguracji** w menu magazynu.
2. Kliknij serwer konfiguracji, który ma zostać zlikwidowany.
3. Na stronie Szczegóły serwera konfiguracji, kliknij przycisk **usunąć** przycisku.
4. Kliknij przycisk **tak** o potwierdzenie usunięcia serwera.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Odinstaluj serwer konfiguracji i jego zależności
  > [!TIP]
  Jeśli planujesz ponowne serwerze konfiguracji z usługą Azure Site Recovery, następnie można przejść do kroku 4 bezpośrednio

1. Zaloguj się jako Administrator serwera konfiguracji.
2. Otwórz Panel sterowania > Program > Odinstaluj programy
3. Należy odinstalować te programy w następującej kolejności:
  * Agent usług Microsoft Azure Recovery Services
  * Microsoft Azure Site odzyskiwania mobilności usługi/główny serwer docelowy
  * Dostawcy usługi Microsoft Azure Site Recovery
  * Serwer procesu/serwera konfiguracji odzyskiwania witryny Microsoft Azure
  * Microsoft Azure Site odzyskiwania konfiguracji serwera zależności
  * MySQL Server 5.5
4. Uruchom następujące polecenie z i wiersza polecenia administratora.
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Usuń lub Wyrejestruj serwer konfiguracji (PowerShell)

1. [Zainstaluj](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) modułu Azure PowerShell
2. Logowanie do konta platformy Azure przy użyciu polecenia
    
    `Connect-AzureRmAccount`
3. Wybierz subskrypcję, w którym znajduje się magazyn

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Teraz skonfigurować kontekst magazynu
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. GET, wybierz serwer konfiguracji

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Usuń serwer konfiguracji

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> **-Force** opcji w AzureRmSiteRecoveryFabric Usuń może służyć do wymusić usunięcie/usunięcie serwera konfiguracji.

## <a name="renew-ssl-certificates"></a>Odnawianie certyfikatów SSL
Serwer konfiguracji jest serwer sieci web wbudowanych, który organizuje działania usługi mobilności, proces serwerów i głównych serwerów docelowych dołączone do niego. Serwer sieci web używa certyfikatu SSL do uwierzytelniania klientów. Certyfikat wygaśnie po upływie trzech lat i mogą być odnawiane w dowolnym momencie.

### <a name="check-expiry"></a>Sprawdzanie wygaśnięcia

Dla wdrożenia serwera konfiguracji przed maj 2016 okresu ważności certyfikatu została ustawiona na jeden rok. Jeśli certyfikat jest wkrótce wygaśnie, są następujące:

- Jeśli data wygaśnięcia jest dwóch miesięcy lub mniej, Usługa rozpoczyna wysyłanie powiadomień, w portalu, a także za pośrednictwem poczty e-mail (Jeśli masz subskrypcję usługi Azure Site Recovery powiadomień).
- Transparent powiadomienie jest wyświetlane na stronie zasobów magazynu. Kliknij przycisk transparentu, aby uzyskać więcej informacji.
- Jeśli widzisz **Uaktualnij teraz** przycisku, oznacza to, czy niektóre składniki w danym środowisku, które nie zostały uaktualnione do 9.4.xxxx.x lub nowszej wersji. Należy uaktualnić składniki, aby odnowić certyfikat. Nie można odnowić w starszych wersjach.

### <a name="renew-the-certificate"></a>Odnów certyfikat

1. W magazynie, otwórz **infrastruktura usługi Site Recovery** > **serwera konfiguracji**i kliknij serwer konfiguracji.
2. Data wygaśnięcia jest wyświetlany w obszarze **kondycję konfiguracji serwera**
3. Kliknij przycisk **odnawiania certyfikatów**. 




## <a name="common-issues"></a>Typowe problemy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Kolejne kroki

Przejrzyj samouczki dotyczące konfigurowania odzyskiwania po awarii [serwerów fizycznych](tutorial-physical-to-azure.md) na platformie Azure.

