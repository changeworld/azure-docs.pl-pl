---
title: Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii lokalnych serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania serwerem konfiguracji usługi Azure Site Recovery na potrzeby odzyskiwania po awarii serwerów fizycznych na platformę Azure.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: b4a35cb853326aa3e54c7b261eaa72f15929a84c
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483968"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii serwerów fizycznych

Ustaw się na lokalnym serwerze konfiguracji, gdy używasz [usługi Azure Site Recovery](site-recovery-overview.md) usługi odzyskiwania po awarii serwerów fizycznych na platformę Azure. Serwer konfiguracji służy do koordynowania komunikacji między maszyn w środowisku lokalnym i platformą Azure oraz do zarządzania replikacją danych. Ten artykuł zawiera podsumowanie typowych zadań zarządzania na serwerze konfiguracji po jej wdrożeniu.

## <a name="prerequisites"></a>Wymagania wstępne

W tabeli przedstawiono wymagania wstępne dotyczące wdrażania w środowisku lokalnym komputera serwera konfiguracji.

| **Składnik** | **Wymaganie** |
| --- |---|
| Rdzenie procesora CPU| 8 |
| Pamięć RAM | 16 GB|
| Liczba dysków | 3, w tym dysku systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania na potrzeby powrotu po awarii |
| Wolne miejsce na dysku (pamięć podręczna serwera przetwarzania) | 600 GB
| Wolne miejsce na dysku (dysk przechowywania) | 600 GB|
| System operacyjny  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Ustawienia regionalne systemu operacyjnego | English (US)|
| Wersja programu VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Role systemu Windows Server | Nie włączaj tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Zasady grupy| Nie włączaj tych zasad grupy: <br> -Zapobieganie dostępowi do wiersza polecenia <br> -Uniemożliwić dostęp do narzędzi edycji rejestru <br> — Logika zaufania dla plików załączników <br> -Włącz wykonywanie skryptu <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | -Brak wcześniej istniejącej domyślnej witryny sieci Web <br> -Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Włącz [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ustawienie  <br> -Brak istniejące witryny sieci Web/aplikacja nasłuchuje na porcie 443<br>|
| Typ karty NIC | Innego VMXNET3 (jeśli jest wdrażane jako maszyny Wirtualnej VMware) |
| Typ adresu IP | Statyczny |
| Dostęp do Internetu | Serwer musi mieć dostęp do tych adresów URL: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://management.azure.com <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (niewymagane w przypadku serwerów przetwarzania skalowalnego w poziomie) <br> - time.nist.gov <br> - time.windows.com |
| Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych)|

## <a name="download-the-latest-installation-file"></a>Pobierz najnowszy plik instalacyjny

Najnowszą wersję pliku instalacyjnego serwera konfiguracji jest dostępny w portalu usługi Site Recovery. Ponadto można go pobrać bezpośrednio z [Microsoft Download Center](https://aka.ms/unifiedsetup).

1. Zaloguj się do witryny Azure portal i przejdź do magazynu usługi Recovery Services.
2. Przejdź do **infrastruktura usługi Site Recovery** > **serwery konfiguracji** (w ramach programu VMware i maszyn fizycznych).
3. Kliknij przycisk **+ serwery** przycisku.
4. Na **Dodaj serwer** kliknij przycisk pobierania, aby pobrać klucz rejestracji. Będzie on potrzebny podczas instalacji serwera konfiguracji, aby zarejestrować go za pomocą usługi Azure Site Recovery.
5. Kliknij przycisk **Pobierz Microsoft Azure Site Recovery Unified Setup** link, aby pobrać najnowszą wersję serwera konfiguracji.

   ![Strona pobierania](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Instalowanie i rejestrowanie serwera

1. Uruchom plik instalacyjny ujednoliconego instalatora.
2. W **przed rozpoczęciem**, wybierz opcję **Zainstaluj serwer konfiguracji i serwer przetwarzania**.

    ![Przed rozpoczęciem](./media/physical-manage-configuration-server/combined-wiz1.png)

3. W obszarze **Licencja na oprogramowanie innej firmy** kliknij pozycję **Akceptuję**, aby pobrać i zainstalować program MySQL.
4. W obszarze **Ustawienia internetowe** określ, jak dostawca działający na serwerze konfiguracji ma się łączyć z usługą Azure Site Recovery przez Internet. Upewnij się, że zezwolono wymaganego adresów URL.

    - Jeśli chcesz się połączyć z serwerem proxy, który jest obecnie skonfigurowane na maszynie, wybierz opcję **nawiązywanie połączenia z usługi Azure Site Recovery przy użyciu serwera proxy**.
    - Jeśli chcesz, aby dostawca łączył się bezpośrednio, wybierz opcję **Połącz bezpośrednio z usługi Azure Site Recovery bez serwera proxy**.
    - Jeśli istniejący serwer proxy wymaga uwierzytelniania lub chcesz użyć niestandardowego serwera proxy dla połączenia dostawcy, wybierz opcję **Połącz przy użyciu niestandardowych ustawień serwera proxy**, a następnie określ adres, port i poświadczenia.
     ![Zapora](./media/physical-manage-configuration-server/combined-wiz4.png)
6. W kroku **Sprawdzanie wymagań wstępnych** Instalator przeprowadza kontrolę w celu upewnienia się, że można uruchomić instalację. Jeśli zostanie wyświetlone ostrzeżenie dotyczące **kontroli synchronizacji czasu globalnego**, sprawdź, czy czas zegara systemowego (ustawienia **Data i godzina**) jest taki sam jak dla strefy czasowej.

    ![Wymagania wstępne](./media/physical-manage-configuration-server/combined-wiz5.png)
7. W obszarze **Konfiguracja programu MySQL** utwórz poświadczenia do logowania się do zainstalowanego wystąpienia serwera programu MySQL.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. W obszarze **Szczegóły środowiska** wybierz, czy zamierzasz replikować maszyny wirtualne programu VMware. Jeśli jesteś, Instalator sprawdza, czy interfejs PowerCLI 6.0 jest zainstalowany.
9. W obszarze **Lokalizacja instalacji** wybierz, gdzie mają zostać zainstalowane pliki binarne i gdzie ma być przechowywana pamięć podręczną. Na wybranym dysku musi być co najmniej 5 GB dostępnego miejsca, ale zalecamy dysk pamięci podręcznej z co najmniej 600 GB wolnego miejsca.

    ![Lokalizacja instalacji](./media/physical-manage-configuration-server/combined-wiz8.png)
10. W **wybór sieci**, najpierw wybierz kartę Sieciową, używanego przez serwer w tworzonym przez proces odnajdywania i wypychanie instalacji usługi mobilności na maszynach źródła, a następnie wybierz kartę Sieciową, która korzysta z serwera konfiguracji dla połączenia za pomocą platformy Azure. Port 9443 jest domyślnym portem używanym do wysyłania i odbierania ruchu związanego z replikacją, ale możesz zmienić ten numer portu tak, aby odpowiadał wymaganiom Twojego środowiska. Oprócz portu 9443 otwieramy też port 443, który jest używany przez serwer sieci Web do organizowania operacji replikacji. Nie należy używać portu 443 do wysyłania i odbierania ruchu związanego z replikacją.

    ![Wybór sieci](./media/physical-manage-configuration-server/combined-wiz9.png)


11. W obszarze **Podsumowanie** przejrzyj informacje i kliknij przycisk **Zainstaluj**. Po zakończeniu instalacji generowane jest hasło. Będzie ono potrzebne po włączeniu replikacji, dlatego skopiuj je i przechowuj w bezpiecznym miejscu.


Po zakończeniu rejestracji serwer jest wyświetlany w bloku **Ustawienia** > **Serwery** w magazynie.


## <a name="install-from-the-command-line"></a>Instalowanie przy użyciu wiersza polecenia

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
|/BypassProxy|Optional (Opcjonalność)|Określa, że serwer konfiguracji łączy się z platformą Azure bez serwera proxy|Należy uzyskać tę wartość z Venu|
|/ProxySettingsFilePath|Optional (Opcjonalność)|Ustawienia serwera proxy (domyślny serwer proxy wymaga uwierzytelniania lub niestandardowy serwer proxy)|Plik powinien mieć format określony poniżej|
|DataTransferSecurePort|Optional (Opcjonalność)|Numer portu dla protokołu PSIP do użytku z danymi replikacji| Prawidłowy numer portu (wartość domyślna to 9433)|
|/SkipSpaceCheck|Optional (Opcjonalność)|Pomiń sprawdzanie miejsca dla dysku pamięci podręcznej| |
|/AcceptThirdpartyEULA|Wymagane|Flaga implikuje akceptację umowy licencyjnej innego producenta| |
|/ShowThirdpartyEULA|Optional (Opcjonalność)|Wyświetla umowę licencyjną innej firmy. Jeśli zostanie podany w danych wejściowych, wszystkie inne parametry są ignorowane| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Tworzenie pliku wejściowego dla MYSQLCredsFilePath

Parametr MySQLCredsFilePath przyjmuje plik jako dane wejściowe. Utwórz plik w następującym formacie, a następnie przekaż go jako parametr wejściowy MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
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
## <a name="modify-proxy-settings"></a>Modyfikowanie ustawień serwera proxy

Można zmodyfikować ustawienia serwera proxy dla komputera serwera konfiguracji w następujący sposób:

1. Zaloguj się do serwera konfiguracji.
2. Uruchom cspsconfigtool.exe za pomocą skrótu na pulpicie.
3. Kliknij przycisk **rejestracja w magazynie** kartę.
4. Pobierz nowy plik rejestracji magazynu z portalu i podaj je jako dane wejściowe do narzędzia.

   ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Podaj nowe szczegóły serwera proxy, a następnie kliknij przycisk **zarejestrować** przycisku.
6. Otwórz okno poleceń programu PowerShell.
7. Uruchom następujące polecenie:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > W przypadku dodatkowych serwerów przetwarzania dołączonych do serwera konfiguracji należy [poprawienie ustawień serwera proxy na wszystkich serwerach proces skalowania w poziomie](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) w danym wdrożeniu.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Zarejestruj ponownie serwer konfiguracji, w tym samym magazynie
1. Zaloguj się do serwera konfiguracji.
2. Uruchom cspsconfigtool.exe za pomocą skrótu na pulpicie.
3. Kliknij przycisk **rejestracja w magazynie** kartę.
4. Pobierz nowy plik rejestracji z portalu, a następnie podać go jako dane wejściowe do narzędzia.
      ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Podaj szczegóły serwera Proxy, a następnie kliknij przycisk **zarejestrować** przycisku.  
6. Otwórz okno poleceń programu PowerShell.
7. Uruchom następujące polecenie

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Jeśli masz wiele serwerów procesu, musisz [ponownie je zarejestrować](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Rejestrowanie serwera konfiguracji za pomocą innego magazynu

> [!WARNING]
> Następny krok powoduje usunięcie serwera konfiguracji z bieżącego magazynu, a następnie replikację chronionych maszyn wirtualnych na serwerze konfiguracji jest zatrzymana.

1. Zaloguj się do serwera konfiguracji
2. z wiersza polecenia z uprawnieniami administratora uruchom polecenie:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Uruchom cspsconfigtool.exe za pomocą skrótu na pulpicie.
4. Kliknij przycisk **rejestracja w magazynie** kartę.
5. Pobierz nowy plik rejestracji z portalu, a następnie podać go jako dane wejściowe do narzędzia.
6. Podaj szczegóły serwera Proxy, a następnie kliknij przycisk **zarejestrować** przycisku.  
7. Otwórz okno poleceń programu PowerShell.
8. Uruchom następujące polecenie
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Uaktualnij serwer konfiguracji

Możesz uruchamiać pakiety zbiorcze aktualizacji, aby zaktualizować serwer konfiguracji. Aktualizacje mogą być stosowane dla maksymalnie N-4 wersji. Na przykład:

- Jeśli korzystasz z zbierając 9,7, 9,8, 9.9 lub 9.10 — można uaktualnić bezpośrednio do 9.11.
- Jeśli korzystasz z 9.6 lub starszym, a chcesz uaktualnić do 9.11, należy najpierw uaktualnić do wersji zbierając 9,7. przed 9.11.

Łącza do pakiety zbiorcze aktualizacji dla uaktualnienie dla wszystkich wersji serwera konfiguracji są dostępne w [strona aktualizacji witryny typu wiki](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Uaktualnij serwer w następujący sposób:

1. Pobierz plik Instalatora aktualizacji z serwerem konfiguracji.
2. Kliknij dwukrotnie, aby uruchomić Instalatora.
3. Instalator wykrywa bieżąca wersja uruchomiona na maszynie.
4. Kliknij przycisk **OK** upewnij się, w celu przeprowadzenia uaktualnienia. 


## <a name="delete-or-unregister-a-configuration-server"></a>Usunąć lub wyrejestrować serwer konfiguracji

> [!WARNING]
> Upewnij się przed rozpoczęciem Likwidowanie serwera konfiguracji.
> 1. [Wyłącz ochronę](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) dla wszystkich maszyn wirtualnych na tym serwerze konfiguracji.
> 2. [Usuń skojarzenie](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) i [Usuń](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) wszystkie zasady replikacji z serwera konfiguracji.
> 3. [Usuń](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) wszystkie hosty vSphere/serwerach vCenters, które są skojarzone z serwerem konfiguracji.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Usuwanie serwera konfiguracji z witryny Azure portal
1. W witrynie Azure portal, przejdź do **infrastruktura usługi Site Recovery** > **serwery konfiguracji** w menu magazynu.
2. Kliknij serwer konfiguracji, który ma zostać zlikwidowany.
3. Na stronie szczegółów serwera konfiguracji, kliknij przycisk **Usuń** przycisku.
4. Kliknij przycisk **tak** o potwierdzenie usunięcia serwera.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Odinstaluj serwer konfiguracji i jego zależności
> [!TIP]
>   Jeśli planujesz ponowne serwera konfiguracji za pomocą usługi Azure Site Recovery, następnie możesz przejść do kroku 4 bezpośrednio

1. Zaloguj się do serwera konfiguracji jako Administrator.
2. Otwórz Panel sterowania > Program > Odinstaluj programy
3. Należy odinstalować te programy w następującej kolejności:
   * Agent usług Microsoft Azure Recovery Services
   * Microsoft Azure lokacji odzyskiwania mobilności usługi/główny serwer docelowy
   * Dostawca programu Microsoft Azure Site Recovery
   * Serwer procesu/serwera konfiguracji odzyskiwania lokacji platformy Microsoft Azure
   * Zależności serwera konfiguracji odzyskiwania lokacji platformy Microsoft Azure
   * MySQL Server 5.5
4. Uruchom następujące polecenie i wierszu polecenia administratora.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Usunąć lub wyrejestrować serwer konfiguracji (PowerShell)

1. [Zainstaluj](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) modułu Azure PowerShell
2. Logowanie do konta platformy Azure za pomocą polecenia
    
    `Connect-AzureRmAccount`
3. Wybierz subskrypcję, w którym znajduje się magazyn

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Teraz konfigurować kontekst magazynu
    
    ```powershell
    $Vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Pobierz, wybierz serwer konfiguracji

    `$Fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Usuwanie serwera konfiguracji

    `Remove-AzureRmSiteRecoveryFabric -Fabric $Fabric [-Force] `

> [!NOTE]
> **-Force** opcji AzureRmSiteRecoveryFabric Usuń można wymusić usunięcie/usuwanie serwera konfiguracji.

## <a name="renew-ssl-certificates"></a>Odnawianie certyfikatów SSL
Serwer konfiguracji jest serwer sieci web wbudowanych, który organizuje działania usługi mobilności, serwerów przetwarzania i główne serwery docelowe połączoną z nią. Serwer sieci web używa certyfikatu SSL do uwierzytelniania klientów. Certyfikat wygasa po upływie trzech lat i można odnawiać w dowolnym momencie.

### <a name="check-expiry"></a>Sprawdzanie wygaśnięcia

Dla wdrożenia serwera konfiguracji, które maja 2016 r. Data ważności certyfikatu została ustawiona na jeden rok. Jeśli masz certyfikat ma wygaśnie, mają miejsce następujące zdarzenia:

- Gdy data wygaśnięcia jest dwa miesiące lub mniej, uruchamiania usługi, wysyłania powiadomień w portalu i za pośrednictwem poczty e-mail (Jeśli masz subskrypcję powiadomień usługi Azure Site Recovery).
- Transparent powiadomienia zostanie wyświetlona na stronie zasobów magazynu. Kliknij transparent, aby uzyskać więcej informacji.
- Jeśli widzisz **Uaktualnij teraz** przycisku, oznacza to, że istnieją niektóre składniki w danym środowisku, które nie zostały uaktualnione do 9.4.xxxx.x lub nowsze wersje. Uaktualnij składniki, aby odnowić certyfikat. Nie można odnowić w starszych wersjach.

### <a name="renew-the-certificate"></a>Odnów certyfikat

1. W magazynie, otwórz **infrastruktura usługi Site Recovery** > **serwera konfiguracji**i kliknij serwer, wymaganej konfiguracji.
2. Data ważności jest wyświetlany w obszarze **kondycji serwera konfiguracji**
3. Kliknij przycisk **odnawiania certyfikatów**. 




## <a name="common-issues"></a>Typowe problemy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Kolejne kroki

Przejrzyj samouczki dotyczące konfigurowania odzyskiwania po awarii [serwerów fizycznych](tutorial-physical-to-azure.md) na platformie Azure.

