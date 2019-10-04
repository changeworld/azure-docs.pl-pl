---
title: Azure Disk Encryption z aplikacja usługi Azure AD wymaganiami wstępnymi (poprzednia wersja)
description: Ten artykuł zawiera wymagania wstępne dotyczące używania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 09/30/2019
ms.custom: seodec18
ms.openlocfilehash: 07db6c5cecf2b8dec803e73d5775edef3f8906fa
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828633"
---
# <a name="azure-disk-encryption-prerequisites-previous-release"></a>Wymagania wstępne Azure Disk Encryption (poprzednia wersja)

**Nowa wersja Azure Disk Encryption eliminuje wymóg udostępniania parametru aplikacji usługi Azure AD w celu włączenia szyfrowania dysków maszyny wirtualnej. W nowej wersji nie jest już wymagane podanie poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje włączania szyfrowania dysków maszyn wirtualnych przy użyciu nowej wersji, zobacz [Azure Disk Encryption wymagania wstępne](azure-security-disk-encryption-prerequisites.md). Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal utrzymywane przy użyciu składni usługi AAD.**

 W tym artykule Azure Disk Encryption wymagania wstępne, wyjaśniono elementy, które muszą być stosowane przed użyciem Azure Disk Encryption. Wraz z ogólnymi wymaganiami wstępnymi Azure Disk Encryption jest zintegrowany z [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) i używa aplikacji usługi Azure AD w celu zapewnienia uwierzytelniania w celu zarządzania kluczami szyfrowania w magazynie kluczy. Możesz również użyć [Azure PowerShell](/powershell/azure/overview) lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/) , aby skonfigurować lub skonfigurować Key Vault i aplikację usługi Azure AD.

Przed włączeniem Azure Disk Encryption na maszynach wirtualnych IaaS platformy Azure dla obsługiwanych scenariuszy, które zostały omówione w artykule [omówienie Azure Disk Encryption](azure-security-disk-encryption-overview.md) , należy upewnić się, że wymagania wstępne zostały spełnione. 

> [!WARNING]
> - Niektóre zalecenia mogą zwiększyć użycie zasobów, sieci lub obliczeń, co skutkuje dodatkowymi kosztami licencji lub subskrypcji. Aby tworzyć zasoby na platformie Azure w obsługiwanych regionach, musisz mieć prawidłową aktywną subskrypcję platformy Azure.
> - Jeśli w celu zaszyfrowania tej maszyny wirtualnej wcześniej była używana [Azure Disk Encryption z aplikacją usługi Azure AD](azure-security-disk-encryption-prerequisites-aad.md) , musisz użyć tej opcji, aby ZAszyfrować maszynę wirtualną. Nie można użyć [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) dla tej zaszyfrowanej maszyny wirtualnej, ponieważ nie jest to obsługiwany scenariusz, co oznacza, że przełączanie z aplikacji usługi AAD dla tej zaszyfrowanej maszyny wirtualnej nie jest jeszcze obsługiwane. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

### <a name="windows"></a>Windows

- Klient systemu Windows: system Windows 8 lub nowszy.
- Windows Server: system Windows Server 2008 R2 lub nowszy.  
 
> [!NOTE] 
> System Windows Server 2008 R2 wymaga zainstalowania .NET Framework 4,5 do szyfrowania; Zainstaluj go z Windows Update z opcjonalną aktualizacją Microsoft .NET Framework 4.5.2 dla systemów Windows Server 2008 R2 x64 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Systemy Windows Server 2012 R2 Core i Windows Server 2016 Core wymagają zainstalowania składnika BdeHdCfg na maszynie wirtualnej w celu szyfrowania.

### <a name="linux"></a>Linux 

Azure Disk Encryption jest obsługiwane w podzestawie [dystrybucji systemu Linux z zatwierdzona przez platformę Azure](../virtual-machines/linux/endorsed-distros.md), które jest samym podzbiorem wszystkich możliwych dystrybucji serwerów z systemem Linux.

![Diagram Venna dystrybucji serwerów z systemem Linux, który obsługuje Azure Disk Encryption](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Dystrybucje serwera z systemem Linux, które nie są zatwierdzone przez platformę Azure, nie obsługują Azure Disk Encryption i, które są zatwierdzone, obsługują tylko następujące dystrybucje i wersje Azure Disk Encryption:

| Dystrybucja systemu Linux | Wersja | Typ woluminu obsługiwany na potrzeby szyfrowania|
| --- | --- |--- |
| Ubuntu | 18,04| System operacyjny i dysk z danymi |
| Ubuntu | 16,04| System operacyjny i dysk z danymi |
| Ubuntu | 14.04.5</br>[Dzięki dostrojeniu jądra platformy Azure do wersji 4,15 lub nowszej](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | System operacyjny i dysk z danymi |
| RHEL | 7,7 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 7,6 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 7,5 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 7,4 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 7.3 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 7.2 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 6.8 | Dysk danych (patrz Uwaga poniżej) |
| RHEL | 6.7 | Dysk danych (patrz Uwaga poniżej) |
| CentOS | 7,7 | System operacyjny i dysk z danymi |
| CentOS | 7,6 | System operacyjny i dysk z danymi |
| CentOS | 7,5 | System operacyjny i dysk z danymi |
| CentOS | 7,4 | System operacyjny i dysk z danymi |
| CentOS | 7.3 | System operacyjny i dysk z danymi |
| CentOS | 7.2 n | System operacyjny i dysk z danymi |
| CentOS | 6.8 | Dysk z danymi |
| openSUSE | 42,3 | Dysk z danymi |
| SLES | 12 — SP4 | Dysk z danymi |
| SLES | 12 — SP3 | Dysk z danymi |

> [!NOTE]
> Nowa implementacja elementu ADE jest obsługiwana w przypadku systemu operacyjnego RHEL i dysku danych dla obrazów z opcją płatność zgodnie z rzeczywistym użyciem. ADE nie jest obecnie obsługiwane w przypadku obrazów RHEL z własnymi subskrypcjami (BYOS). Aby uzyskać więcej informacji, zobacz [Azure Disk Encryption dla systemu Linux](azure-security-disk-encryption-linux.md) .

- Azure Disk Encryption wymaga, aby Twój Magazyn kluczy i maszyny wirtualne znajdowały się w tym samym regionie i subskrypcji platformy Azure. Skonfigurowanie zasobów w osobnych regionach powoduje awarię włączania funkcji Azure Disk Encryption.

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>Dodatkowe wymagania wstępne dotyczące maszyn wirtualnych z systemem Linux IaaS 

- Azure Disk Encryption wymaga obecności modułów dm-crypt i vfat w systemie. Usunięcie lub wyłączenie vfat z obrazu domyślnego uniemożliwi systemowi odczytywanie woluminu klucza i Uzyskiwanie klucza wymaganego do odblokowania dysków podczas kolejnych ponownych uruchomień. Kroki ograniczania systemu, które usuwają moduł vfat z systemu, nie są zgodne z Azure Disk Encryption. 
- Przed włączeniem szyfrowania, dyski danych, które mają być szyfrowane, muszą być poprawnie wymienione w katalogu/etc/fstab. Użyj trwałej nazwy urządzenia blokowego dla tego wpisu, ponieważ nazwy urządzeń w formacie "/dev/sdX" nie mogą być używane do skojarzenia z tym samym dyskiem w ramach ponownych uruchomień, szczególnie po zastosowaniu szyfrowania. Aby uzyskać więcej szczegółów dotyczących tego zachowania, zobacz: [Rozwiązywanie problemów z nazwami urządzeń maszyny wirtualnej z systemem Linux zmiany nazwy](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Upewnij się, że ustawienia/etc/fstab są prawidłowo skonfigurowane do zainstalowania. Aby skonfigurować te ustawienia, uruchom polecenie instalacji-a lub Uruchom ponownie maszynę wirtualną i Wyzwól w ten sposób ponowne zainstalowanie. Po zakończeniu Sprawdź dane wyjściowe polecenia lsblk, aby sprawdzić, czy dysk jest nadal zainstalowany. 
  - Jeśli plik/etc/fstab nie instaluje dysku prawidłowo przed włączeniem szyfrowania, Azure Disk Encryption nie będzie w stanie zainstalować go prawidłowo.
  - Proces Azure Disk Encryption spowoduje przeniesienie informacji o instalacji z/etc/fstab i do własnego pliku konfiguracji w ramach procesu szyfrowania. Nie można sprawdzić, czy nie ma wpisu w/etc/fstab po zakończeniu szyfrowania dysku danych.
  - Przed rozpoczęciem szyfrowania należy zatrzymać wszystkie usługi i procesy, które mogą być zapisywane na zainstalowanych dyskach danych i je wyłączyć, aby nie uruchamiały się automatycznie po ponownym uruchomieniu komputera. Może to spowodować, że pliki będą otwierane na tych partycjach, co uniemożliwia procedurę szyfrowania w celu ich ponownego zainstalowania, powodując niepowodzenie szyfrowania. 
  - Po ponownym uruchomieniu proces Azure Disk Encryption będzie miał czas na zainstalowanie nowo zaszyfrowanych dysków. Po ponownym uruchomieniu nie będą one natychmiast dostępne. Proces wymaga czasu uruchomienia, odblokowania, a następnie zainstalowania szyfrowanych dysków przed udostępnieniem ich innym procesom. Ten proces może potrwać kilka minut po ponownym uruchomieniu w zależności od charakterystyki systemu.

Przykładem poleceń, których można użyć do zainstalowania dysków danych i utworzenia wymaganych wpisów/etc/fstab, można znaleźć w [wierszach 244-248 tego pliku skryptu](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 

## <a name="bkmk_GPO"></a>Sieci i zasady grupy

**Aby włączyć funkcję Azure Disk Encryption przy użyciu starszej składni parametrów usługi AAD, maszyny wirtualne IaaS muszą spełniać następujące wymagania dotyczące konfiguracji punktu końcowego sieci:** 
  - Aby uzyskać token, aby połączyć się z magazynem kluczy, maszyna wirtualna IaaS musi mieć możliwość nawiązania połączenia z punktem końcowym Azure Active Directory @no__t -0login. microsoftonline. com @ no__t-1.
  - Aby zapisać klucze szyfrowania do magazynu kluczy, maszyna wirtualna IaaS musi mieć możliwość nawiązania połączenia z punktem końcowym magazynu kluczy.
  - Maszyna wirtualna IaaS musi mieć możliwość nawiązania połączenia z punktem końcowym usługi Azure Storage, który obsługuje repozytorium rozszerzeń platformy Azure i konto usługi Azure Storage, które obsługuje pliki VHD.
  -  Jeśli zasady zabezpieczeń ograniczają dostęp z maszyn wirtualnych platformy Azure do Internetu, można rozwiązać poprzedni identyfikator URI i skonfigurować określoną regułę, aby zezwolić na połączenia wychodzące z adresami IP. Aby uzyskać więcej informacji, zobacz [Azure Key Vault za zaporą](../key-vault/key-vault-access-behind-firewall.md).
  - W systemie Windows, jeśli protokół TLS 1,0 został jawnie wyłączony, a wersja platformy .NET nie została zaktualizowana do wersji 4,6 lub nowszej, następująca zmiana w rejestrze umożliwi wybranie nowszej wersji protokołu TLS:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

 


**Zasady grupy:**
 - Rozwiązanie Azure Disk Encryption używa funkcji ochrony klucza zewnętrznego funkcji BitLocker dla maszyn wirtualnych z systemem Windows IaaS. W przypadku maszyn wirtualnych przyłączonych do domeny nie wypychanie żadnych zasad grupy, które wymuszają funkcje ochrony modułu TPM. Aby uzyskać informacje o zasadach grupy dla "Zezwalaj na funkcję BitLocker bez zgodnego modułu TPM", zobacz [BitLocker zasady grupy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Zasady funkcji BitLocker na maszynach wirtualnych przyłączonych do domeny z niestandardowymi zasadami grupy muszą zawierać następujące ustawienie: [Konfigurowanie magazynu użytkownika informacje odzyskiwania funkcji BitLocker — > Zezwalaj na 256-bitowy klucz odzyskiwania](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption zakończy się niepowodzeniem w przypadku niezgodności niestandardowych ustawień zasad grupy dla funkcji BitLocker. Na maszynach, które nie mają poprawnego ustawienia zasad, Zastosuj nowe zasady, Wymuś aktualizację nowych zasad (gpupdate. exe/Force), a następnie ponowne uruchomienie może być wymagane.  


## <a name="bkmk_PSH"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) zawiera zestaw poleceń cmdlet, które używają modelu [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) do zarządzania zasobami platformy Azure. Możesz użyć jej w przeglądarce z [Azure Cloud Shell](../cloud-shell/overview.md)lub zainstalować ją na maszynie lokalnej, korzystając z poniższych instrukcji, aby użyć jej w dowolnej sesji programu PowerShell. Jeśli masz już zainstalowany lokalnie, upewnij się, że korzystasz z najnowszej wersji Azure PowerShell, aby skonfigurować Azure Disk Encryption.

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Zainstaluj Azure PowerShell do użycia na komputerze lokalnym (opcjonalnie):  
1. [Zainstaluj i skonfiguruj Azure PowerShell](/powershell/azure/install-az-ps). 

2. Zainstaluj [moduł Azure Active Directory PowerShell](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module). 

     ```powershell
     Install-Module AzureAD
     ```

3. Sprawdź zainstalowane wersje modułów.
      ```powershell
      Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. Zaloguj się do platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
     
     ```powershell
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  Nawiązywanie połączenia z usługą Azure AD [Connect — AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```

6. W razie konieczności zapoznaj [się z tematem wprowadzenie do Azure PowerShell](/powershell/azure/get-started-azureps) i [AzureAD](/powershell/module/azuread).

## <a name="bkmk_CLI"></a>Interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia [platformy azure 2,0](/cli/azure) to narzędzie z wierszem poleceń do zarządzania zasobami platformy Azure. Interfejs wiersza polecenia jest przeznaczony do elastycznego wykonywania zapytań dotyczących danych, obsługuje długotrwałe operacje jako procesy nieblokujące i ułatwiają wykonywanie skryptów. Można go również używać w przeglądarce z usługą [Azure Cloud Shell](../cloud-shell/overview.md) albo można go zainstalować na maszynie lokalnej i używać w dowolnej sesji programu PowerShell.

1. [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) do użycia na komputerze lokalnym (opcjonalnie):

2. Sprawdź zainstalowaną wersję.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Zaloguj się do platformy Azure za pomocą polecenia [AZ login](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. W razie konieczności Przejrzyj [wprowadzenie do interfejsu wiersza polecenia platformy Azure 2,0](/cli/azure/get-started-with-azure-cli) . 


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>Przepływ pracy wymagań wstępnych dla Key Vault i aplikacji usługi Azure AD

Jeśli znasz już Key Vault i wymagania wstępne usługi Azure AD dla Azure Disk Encryption, możesz użyć [skryptu programu PowerShell dotyczącego wymagań wstępnych Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Aby uzyskać więcej informacji o używaniu skryptu wymagań wstępnych, zobacz [dodatek Azure Disk Encryption](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Tworzenie magazynu kluczy. 
2. Skonfiguruj aplikację usługi Azure AD i nazwę główną usługi.
3. Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD.
4. Ustaw zaawansowane zasady dostępu magazynu kluczy.
 
## <a name="bkmk_KeyVault"></a>Tworzenie magazynu kluczy 
Azure Disk Encryption jest zintegrowana z [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , aby pomóc w kontroli i zarządzaniu kluczami szyfrowania dysków oraz wpisami tajnymi w ramach subskrypcji magazynu kluczy. Możesz utworzyć magazyn kluczy lub użyć istniejącego do Azure Disk Encryption. Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz Wprowadzenie do [Azure Key Vault](../key-vault/key-vault-get-started.md) i [Zabezpieczanie magazynu kluczy](../key-vault/key-vault-secure-your-key-vault.md). Do utworzenia magazynu kluczy można użyć szablonu Menedżer zasobów, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. 


>[!WARNING]
>Aby upewnić się, że wpisy tajne szyfrowania nie przekraczają granic regionalnych, Azure Disk Encryption potrzebują Key Vault i maszyn wirtualnych, które mają być umieszczone w tym samym regionie. Utwórz i użyj Key Vault, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania. 


### <a name="bkmk_KVPSH"></a>Tworzenie magazynu kluczy przy użyciu programu PowerShell

Można utworzyć magazyn kluczy z Azure PowerShell przy użyciu polecenia cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Aby uzyskać dodatkowe polecenia cmdlet dla Key Vault, zobacz [AZ. datamagazyn](/powershell/module/az.keyvault/). 

1. W razie potrzeby [Połącz się z subskrypcją platformy Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Utwórz nową grupę zasobów, w razie konieczności z poleceniem [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Aby wyświetlić listę lokalizacji centrów danych, użyj polecenie [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Tworzenie nowego magazynu kluczy przy użyciu polecenia [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Zanotuj **nazwę magazynu**, **nazwę grupy zasobów**, **Identyfikator zasobu**, identyfikator **URI magazynu**i **Identyfikator obiektu** , który jest zwracany do późniejszego użycia podczas szyfrowania dysków. 


### <a name="bkmk_KVCLI"></a>Tworzenie magazynu kluczy za pomocą interfejsu wiersza polecenia platformy Azure
Możesz zarządzać swoim magazynem kluczy za pomocą interfejsu wiersza polecenia platformy Azure, korzystając z poleceń [AZ Key magazyn](/cli/azure/keyvault#commands) . Aby utworzyć magazyn kluczy, użyj [AZ Key magazyn Create](/cli/azure/keyvault#az-keyvault-create).

1. W razie potrzeby [Połącz się z subskrypcją platformy Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Utwórz nową grupę zasobów, w razie konieczności, za pomocą [AZ Group Create](/cli/azure/group#az-group-create). Aby wyświetlić listę lokalizacji, użyj [AZ Account List-Locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Utwórz nowy magazyn kluczy za pomocą polecenia [AZ Key webcreate](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Zanotuj **nazwę magazynu** (nazwę), **nazwę grupy zasobów**, **Identyfikator zasobu** (identyfikator), **Identyfikator URI magazynu**i **Identyfikator obiektu** , który jest zwracany do użycia później. 

### <a name="bkmk_KVRM"></a>Tworzenie magazynu kluczy przy użyciu szablonu Menedżer zasobów

Magazyn kluczy można utworzyć przy użyciu [szablonu Menedżer zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. W szablonie szybkiego startu platformy Azure kliknij pozycję **Wdróż na platformie Azure**.
2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, nazwę Key Vault, identyfikator obiektu, postanowienia prawne i umowę, a następnie kliknij przycisk **Kup**. 


## <a name="bkmk_ADapp"></a>Konfigurowanie aplikacji usługi Azure AD i nazwy głównej usługi 
Aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure, Azure Disk Encryption generuje i zapisuje klucze szyfrowania do magazynu kluczy. Zarządzanie kluczami szyfrowania w magazynie kluczy wymaga uwierzytelniania usługi Azure AD. Utwórz w tym celu aplikację usługi Azure AD. Do celów uwierzytelniania można użyć uwierzytelniania opartego na kluczu tajnym klienta lub [uwierzytelniania usługi Azure AD opartego na certyfikatach klienta](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="bkmk_ADappPSH"></a>Konfigurowanie aplikacji usługi Azure AD i nazwy głównej usługi przy użyciu Azure PowerShell 
Aby wykonać następujące polecenia, Pobierz [moduł Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)i użyj go. 

1. W razie potrzeby [Połącz się z subskrypcją platformy Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Użyj polecenia cmdlet [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) programu PowerShell, aby utworzyć aplikację usługi Azure AD. MyApplicationHomePage i MyApplicationUri mogą być dowolną wartością.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $AzureAdApplication. Identyfikator aplikacji to ClientID usługi Azure AD, a $aadClientSecret jest kluczem tajnym klienta, który będzie używany później do włączania Azure Disk Encryption. Odpowiednio Zabezpiecz klucz tajny klienta usługi Azure AD. Uruchomienie `$azureAdApplication.ApplicationId` spowoduje wyświetlenie Twojej aplikacji.


### <a name="bkmk_ADappCLI"></a>Konfigurowanie aplikacji usługi Azure AD i jednostki usługi przy użyciu interfejsu wiersza polecenia platformy Azure

Jednostkami usługi można zarządzać za pomocą interfejsu wiersza polecenia platformy Azure, korzystając z poleceń [AZ AD Sp](/cli/azure/ad/sp) . Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. W razie potrzeby [Połącz się z subskrypcją platformy Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Utwórz nową nazwę główną usługi.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Zwrócony identyfikator appId to usługa Azure AD ClientID użyta w innych poleceniach. Jest to również nazwa SPN, która będzie używana przez AZ "Set-Policy". Hasło jest kluczem tajnym klienta, którego należy użyć później do włączenia Azure Disk Encryption. Odpowiednio Zabezpiecz klucz tajny klienta usługi Azure AD.
 
### <a name="bkmk_ADappRM"></a>Skonfiguruj aplikację usługi Azure AD i nazwę główną usługi, chociaż Azure Portal
Wykonaj kroki z [portalu use, aby utworzyć aplikację Azure Active Directory i nazwę główną usługi, która będzie mogła uzyskać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md) artykułu w celu utworzenia aplikacji usługi Azure AD. Każdy krok wymieniony poniżej przeprowadzi Cię bezpośrednio do sekcji artykułu, aby zakończyć. 

1. [Weryfikuj wymagane uprawnienia](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Tworzenie aplikacji Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - W przypadku tworzenia aplikacji możesz użyć dowolnej nazwy i adresu URL logowania.
3. [Pobierz identyfikator aplikacji i klucz uwierzytelniania](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - Klucz uwierzytelniania jest kluczem tajnym klienta i jest używany jako AadClientSecret dla elementu Set-AzVMDiskEncryptionExtension. 
        - Klucz uwierzytelniania jest używany przez aplikację jako poświadczenia do logowania się do usługi Azure AD. W Azure Portal ten klucz tajny nosi nazwę klucze, ale nie ma relacji do magazynów kluczy. Odpowiednio Zabezpiecz ten klucz tajny. 
     - Identyfikator aplikacji będzie później używany jako AadClientId dla właściwości Set-AzVMDiskEncryptionExtension i jako ServicePrincipalName dla elementu Set-AzKeyVaultAccessPolicy. 

## <a name="bkmk_KVAP"></a>Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD
Aby zapisać wpisy tajne szyfrowania do określonego Key Vault, Azure Disk Encryption potrzebuje identyfikatora klienta i klucza tajnego klienta aplikacji Azure Active Directory, która ma uprawnienia do zapisu wpisów tajnych do Key Vault. 

> [!NOTE]
> Azure Disk Encryption wymaga skonfigurowania następujących zasad dostępu do aplikacji klienckiej usługi Azure AD: _WrapKey_ i _Ustaw_ uprawnienia.

### <a name="bkmk_KVAPPSH"></a>Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD za pomocą Azure PowerShell
Aplikacja usługi Azure AD wymaga uprawnień dostępu do kluczy lub wpisów tajnych w magazynie. Użyj polecenia cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) , aby udzielić uprawnień do aplikacji przy użyciu identyfikatora klienta (który został wygenerowany podczas rejestrowania aplikacji) jako wartość parametru _– ServicePrincipalName_ . Aby dowiedzieć się więcej, zobacz wpis w blogu [Azure Key Vault — krok](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)po kroku. 

1. W razie potrzeby [Połącz się z subskrypcją platformy Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi AD przy użyciu programu PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a>Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD przy użyciu interfejsu wiersza polecenia platformy Azure
Użyj [azexception Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) , aby ustawić zasady dostępu. Aby uzyskać więcej informacji, zobacz [zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia 2,0](../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

1. W razie potrzeby [Połącz się z subskrypcją platformy Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Podaj nazwę główną usługi utworzoną za pośrednictwem interfejsu wiersza polecenia platformy Azure, aby pobrać klucze tajne i otoczyć je kluczem przy użyciu następującego polecenia:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a>Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD przy użyciu portalu

1. Otwórz grupę zasobów z magazynem kluczy.
2. Wybierz magazyn kluczy, przejdź do pozycji **zasady dostępu**, a następnie kliknij przycisk **Dodaj nowe**.
3. W obszarze **Wybierz podmiot zabezpieczeń**Wyszukaj utworzoną aplikację usługi Azure AD i wybierz ją. 
4. W przypadku **uprawnień kluczowych**zaznacz pole **Zawijanie klucza** w obszarze **operacje kryptograficzne**.
5. W przypadku uprawnień do wpisów **tajnych**zaznacz pozycję **Ustaw** w obszarze **operacje zarządzania kluczami tajnymi**.
6. Kliknij przycisk **OK** , aby zapisać zasady dostępu. 

![Azure Key Vault operacji kryptograficznych — klawisz zawijania](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault uprawnień tajnych — Ustawianie](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy
Platforma Azure wymaga dostępu do kluczy szyfrowania lub wpisów tajnych w magazynie kluczy, aby udostępnić je na maszynie wirtualnej w celu rozruchu i odszyfrowywania woluminów. Włączenie szyfrowania dysków w magazynie kluczy lub wdrożenie zakończy się niepowodzeniem.  

### <a name="bkmk_KVperPSH"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy za pomocą Azure PowerShell
 Aby włączyć szyfrowanie dysków dla magazynu kluczy, należy użyć polecenia cmdlet programu PowerShell magazynu kluczy [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) .

  - **Włącz Key Vault szyfrowania dysku:** Usługa EnabledForDiskEncryption jest wymagana do szyfrowania dysków Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **W razie potrzeby włącz Key Vault wdrożenia:** Umożliwia dostawcy zasobów Microsoft. COMPUTE pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywoływany podczas tworzenia zasobu, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **W razie potrzeby włącz Key Vault wdrożenia szablonów:** Umożliwia Azure Resource Manager pobierania wpisów tajnych z tego magazynu kluczy, gdy do tego magazynu kluczy odwołuje się wdrożenie szablonu.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy za pomocą interfejsu wiersza polecenia platformy Azure
Użyj [AZ Key Update](/cli/azure/keyvault#az-keyvault-update) , aby włączyć szyfrowanie dysków dla magazynu kluczy. 

 - **Włącz Key Vault szyfrowania dysku:** Szyfrowanie włączone dla dysku jest wymagane. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **W razie potrzeby włącz Key Vault wdrożenia:** Zezwól Virtual Machines na pobieranie certyfikatów przechowywanych jako wpisy tajne z magazynu.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **W razie potrzeby włącz Key Vault wdrożenia szablonów:** Zezwól Menedżer zasobów na pobieranie wpisów tajnych z magazynu.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy za pomocą Azure Portal

1. Wybierz swój magazyn kluczy, przejdź do pozycji **zasady dostępu**, a **następnie kliknij, aby wyświetlić zaawansowane zasady dostępu**.
2. Zaznacz pole o nazwie **Włącz dostęp do Azure Disk Encryption na potrzeby szyfrowania woluminów**.
3. Wybierz pozycję **Włącz dostęp do usługi Azure Virtual Machines w celu wdrożenia** i/lub **włącz dostęp do Azure Resource Manager na potrzeby wdrożenia szablonu**, jeśli jest to konieczne. 
4. Kliknij przycisk **Save** (Zapisz).

![Zaawansowane zasady dostępu magazynu kluczy platformy Azure](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a>Skonfiguruj klucz szyfrowania klucza (opcjonalnie)
Jeśli chcesz użyć klucza szyfrowania klucza (KEK) w celu uzyskania dodatkowej warstwy zabezpieczeń dla kluczy szyfrowania, Dodaj KEK do magazynu kluczy. Użyj polecenia cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) , aby utworzyć klucz szyfrowania klucza w magazynie kluczy. Możesz również zaimportować KEK z lokalnego modułu HSM zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [dokumentację Key Vault](../key-vault/key-vault-hsm-protected-keys.md). W przypadku określenia klucza szyfrowania klucza Azure Disk Encryption używa tego klucza do zawijania wpisów tajnych szyfrowania przed zapisem w Key Vault. 

* Podczas generowania kluczy należy użyć typu klucza RSA. Azure Disk Encryption nie obsługuje jeszcze kluczy krzywej eliptycznej.

* Klucze tajne magazynu kluczy i adresy URL KEK muszą mieć wersję. System Azure wymusza to ograniczenie wersji. Aby uzyskać prawidłowe tajne i KEK adresy URL, zobacz następujące przykłady:

  * Przykładowy prawidłowy tajny adres URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Przykład prawidłowego adresu URL KEK: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption nie obsługuje określania numerów portów jako części wpisów tajnych magazynu kluczy i adresów URL KEK. Przykłady nieobsługiwanych i obsługiwanych adresów URL magazynu kluczy można znaleźć w następujących przykładach:

  * Nieakceptowalny adres URL magazynu kluczy *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Akceptowalny adres URL magazynu kluczy: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="bkmk_KEKPSH"></a>Skonfiguruj klucz szyfrowania klucza z Azure PowerShell 
Przed użyciem skryptu programu PowerShell należy zapoznać się z wymaganiami wstępnymi Azure Disk Encryption, aby poznać kroki skryptu. Przykładowy skrypt może wymagać zmian w danym środowisku. Ten skrypt tworzy wszystkie Azure Disk Encryption wymagania wstępne i szyfruje istniejącą maszynę wirtualną IaaS, zawijając klucz szyfrowania dysku przy użyciu klucza szyfrowania klucza. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a>Uwierzytelnianie oparte na certyfikatach (opcjonalnie)
Jeśli chcesz użyć uwierzytelniania przy użyciu certyfikatu, możesz przekazać jeden do magazynu kluczy i wdrożyć go na kliencie. Przed użyciem skryptu programu PowerShell należy zapoznać się z wymaganiami wstępnymi Azure Disk Encryption, aby poznać kroki skryptu. Przykładowy skrypt może wymagać zmian w danym środowisku.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a>Uwierzytelnianie oparte na certyfikacie i KEK (opcjonalnie)

Jeśli chcesz użyć uwierzytelniania certyfikatu i otoczyć klucz szyfrowania KEK, możesz użyć poniższego skryptu jako przykładu. Przed użyciem skryptu programu PowerShell należy zapoznać się ze wszystkimi poprzednimi Azure Disk Encryption wstępnie wymaganymi elementami, aby poznać kroki skryptu. Przykładowy skrypt może wymagać zmian w danym środowisku.

> [!IMPORTANT]
> Uwierzytelnianie oparte na certyfikatach usługi Azure AD nie jest obecnie obsługiwane na maszynach wirtualnych z systemem Linux.



     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Włącz Azure Disk Encryption dla systemu Windows](azure-security-disk-encryption-windows-aad.md)

> [!div class="nextstepaction"]
> [Włączanie Azure Disk Encryption dla systemu Linux](azure-security-disk-encryption-linux-aad.md)
