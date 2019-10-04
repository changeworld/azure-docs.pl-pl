---
title: Wymagania wstępne — Azure Disk Encryption dla maszyn wirtualnych IaaS | Microsoft Docs
description: Ten artykuł zawiera wymagania wstępne dotyczące używania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 09/30/2019
ms.custom: seodec18
ms.openlocfilehash: 551918373f8292d798980600d6e0d43add55bd18
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828283"
---
# <a name="azure-disk-encryption-prerequisites"></a>Wymagania wstępne Azure Disk Encryption

W tym artykule Azure Disk Encryption wymagania wstępne, wyjaśniono elementy, które muszą być stosowane przed użyciem Azure Disk Encryption. Azure Disk Encryption jest zintegrowana z [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) w celu ułatwienia zarządzania kluczami szyfrowania. Aby skonfigurować Azure Disk Encryption, można użyć [Azure PowerShell](/powershell/azure/overview), [interfejsu wiersza polecenia platformy Azure](/cli/azure/)lub [Azure Portal](https://portal.azure.com) .

Przed włączeniem Azure Disk Encryption na maszynach wirtualnych IaaS platformy Azure dla obsługiwanych scenariuszy, które zostały omówione w artykule [omówienie Azure Disk Encryption](azure-security-disk-encryption-overview.md) , należy upewnić się, że wymagania wstępne zostały spełnione. 

> [!WARNING]
> - Jeśli w celu zaszyfrowania tej maszyny wirtualnej wcześniej była używana [Azure Disk Encryption z aplikacją usługi Azure AD](azure-security-disk-encryption-prerequisites-aad.md) , musisz użyć tej opcji, aby ZAszyfrować maszynę wirtualną. Nie można użyć [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) dla tej zaszyfrowanej maszyny wirtualnej, ponieważ nie jest to obsługiwany scenariusz, co oznacza, że przełączanie z aplikacji usługi AAD dla tej zaszyfrowanej maszyny wirtualnej nie jest jeszcze obsługiwane.
> - Niektóre zalecenia mogą zwiększyć użycie zasobów, sieci lub obliczeń, co skutkuje dodatkowymi kosztami licencji lub subskrypcji. Aby tworzyć zasoby na platformie Azure w obsługiwanych regionach, musisz mieć prawidłową aktywną subskrypcję platformy Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-vm-sizes"></a>Obsługiwane rozmiary maszyn wirtualnych

Azure Disk Encryption nie jest dostępna na [maszynach wirtualnych serii A](https://azure.microsoft.com/pricing/details/virtual-machines/series/). Azure Disk Encryption jest dostępna na innych maszynach wirtualnych, które spełniają te minimalne wymagania dotyczące pamięci:

| Maszyna wirtualna | Minimalne wymagania dotyczące pamięci |
|--|--|
| Maszyny wirtualne z systemem Windows | 2 GB |
| Maszyny wirtualne z systemem Linux, w których są szyfrowane tylko woluminy danych| 2 GB |
| Maszyny wirtualne z systemem Linux podczas szyfrowania woluminów danych i systemu operacyjnego oraz miejsce użycia systemu plików głównego (/) jest 4 GB lub mniej | 8 GB |
| Maszyny wirtualne z systemem Linux podczas szyfrowania woluminów danych i systemu operacyjnego oraz miejsce użycia systemu plików głównego (/) o wartości większej niż 4 GB | Użycie głównego systemu plików * 2. Na przykład użycie 16 GB z głównego systemu plików wymaga co najmniej 32 GB pamięci RAM |

Po zakończeniu procesu szyfrowania dysku systemu operacyjnego na maszynach wirtualnych z systemem Linux można skonfigurować maszynę wirtualną do uruchamiania z mniejszą ilością pamięci. 

> [!NOTE]
> Szyfrowanie dysków systemu operacyjnego Linux nie jest dostępne dla [Virtual Machine Scale Sets](../virtual-machine-scale-sets/index.yml).

Azure Disk Encryption jest również dostępna dla maszyn wirtualnych z magazynem w warstwie Premium. 

## <a name="supported-operating-systems"></a>Supported operating systems

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
| RHEL | 7,3 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 7,2 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 6,8 | Dysk danych (patrz Uwaga poniżej) |
| RHEL | 6,7 | Dysk danych (patrz Uwaga poniżej) |
| CentOS | 7,7 | System operacyjny i dysk z danymi |
| CentOS | 7,6 | System operacyjny i dysk z danymi |
| CentOS | 7,5 | System operacyjny i dysk z danymi |
| CentOS | 7,4 | System operacyjny i dysk z danymi |
| CentOS | 7,3 | System operacyjny i dysk z danymi |
| CentOS | 7.2 n | System operacyjny i dysk z danymi |
| CentOS | 6,8 | Dysk danych |
| openSUSE | 42,3 | Dysk danych |
| SLES | 12 — SP4 | Dysk danych |
| SLES | 12 — SP3 | Dysk danych |

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

**Aby włączyć funkcję Azure Disk Encryption, maszyny wirtualne IaaS muszą spełniać następujące wymagania dotyczące konfiguracji punktu końcowego sieci:**
  - Aby uzyskać token, aby połączyć się z magazynem kluczy, maszyna wirtualna IaaS musi mieć możliwość nawiązania połączenia z punktem końcowym Azure Active Directory @no__t -0login. microsoftonline. com @ no__t-1.
  - Aby zapisać klucze szyfrowania do magazynu kluczy, maszyna wirtualna IaaS musi mieć możliwość nawiązania połączenia z punktem końcowym magazynu kluczy.
  - Maszyna wirtualna IaaS musi mieć możliwość nawiązania połączenia z punktem końcowym usługi Azure Storage, który obsługuje repozytorium rozszerzeń platformy Azure i konto usługi Azure Storage, które obsługuje pliki VHD.
  -  Jeśli zasady zabezpieczeń ograniczają dostęp z maszyn wirtualnych platformy Azure do Internetu, można rozwiązać poprzedni identyfikator URI i skonfigurować określoną regułę, aby zezwolić na połączenia wychodzące z adresami IP. Aby uzyskać więcej informacji, zobacz [Azure Key Vault za zaporą](../key-vault/key-vault-access-behind-firewall.md).    


**Zasady grupy:**
 - Rozwiązanie Azure Disk Encryption używa funkcji ochrony klucza zewnętrznego funkcji BitLocker dla maszyn wirtualnych z systemem Windows IaaS. W przypadku maszyn wirtualnych przyłączonych do domeny nie wypychanie żadnych zasad grupy, które wymuszają funkcje ochrony modułu TPM. Aby uzyskać informacje o zasadach grupy dla "Zezwalaj na funkcję BitLocker bez zgodnego modułu TPM", zobacz [BitLocker zasady grupy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Zasady funkcji BitLocker na maszynach wirtualnych przyłączonych do domeny z niestandardowymi zasadami grupy muszą zawierać następujące ustawienie: [Konfigurowanie magazynu użytkownika informacje odzyskiwania funkcji BitLocker — > Zezwalaj na 256-bitowy klucz odzyskiwania](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption zakończy się niepowodzeniem w przypadku niezgodności niestandardowych ustawień zasad grupy dla funkcji BitLocker. Na maszynach, które nie mają poprawnego ustawienia zasad, Zastosuj nowe zasady, Wymuś aktualizację nowych zasad (gpupdate. exe/Force), a następnie ponowne uruchomienie może być wymagane.

- Azure Disk Encryption zakończy się niepowodzeniem, jeśli zasady grupy na poziomie domeny blokują algorytm AES-CBC, który jest używany przez funkcję BitLocker.


## <a name="bkmk_PSH"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) zawiera zestaw poleceń cmdlet, które używają modelu [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) do zarządzania zasobami platformy Azure. Możesz użyć jej w przeglądarce z [Azure Cloud Shell](../cloud-shell/overview.md)lub zainstalować ją na maszynie lokalnej, korzystając z poniższych instrukcji, aby użyć jej w dowolnej sesji programu PowerShell. Jeśli masz już zainstalowany lokalnie, upewnij się, że korzystasz z najnowszej wersji zestawu SDK Azure PowerShell, aby skonfigurować Azure Disk Encryption. Pobierz najnowszą wersję [Azure PowerShell wydania](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Zainstaluj Azure PowerShell do użycia na komputerze lokalnym (opcjonalnie): 
1. Postępuj zgodnie z instrukcjami wyświetlanymi w obszarze linki dla danego systemu operacyjnego, a następnie kontynuuj pozostałe kroki opisane poniżej.      
   - [Zainstaluj i skonfiguruj Azure PowerShell](/powershell/azure/install-az-ps). 
     - Zainstaluj PowerShellGet, Azure PowerShell i Załaduj polecenie AZ module. 

2. Sprawdź zainstalowane wersje modułu AZ module. W razie konieczności [zaktualizuj moduł Azure PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module).
    Zalecane jest użycie najnowszej wersji modułu AZ.

     ```powershell
     Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. Zaloguj się do platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
     
     ```azurepowershell-interactive
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

4.  W razie konieczności zapoznaj [się z tematem wprowadzenie do Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="bkmk_CLI"></a>Instalowanie interfejsu wiersza polecenia platformy Azure do użycia na komputerze lokalnym (opcjonalnie)

Interfejs wiersza polecenia [platformy azure 2,0](/cli/azure) to narzędzie z wierszem poleceń do zarządzania zasobami platformy Azure. Interfejs wiersza polecenia jest przeznaczony do elastycznego wykonywania zapytań dotyczących danych, obsługuje długotrwałe operacje jako procesy nieblokujące i ułatwiają wykonywanie skryptów. Możesz użyć jej w przeglądarce z [Azure Cloud shellem](../cloud-shell/overview.md)lub zainstalować ją na maszynie lokalnej i używać jej w dowolnej sesji programu PowerShell.

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


## <a name="prerequisite-workflow-for-key-vault"></a>Przepływ pracy wymagań wstępnych dla Key Vault
Jeśli znasz już Key Vault i wymagania wstępne usługi Azure AD dla Azure Disk Encryption, możesz użyć [skryptu programu PowerShell dotyczącego wymagań wstępnych Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Aby uzyskać więcej informacji na temat korzystania ze skryptu wymagań wstępnych, zobacz artykuł [szyfrowanie maszyny wirtualnej — szybki start](../virtual-machines/linux/disk-encryption-powershell-quickstart.md) i [Azure Disk Encryption dodatek](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. W razie konieczności Utwórz grupę zasobów.
2. Utwórz magazyn kluczy. 
3. Ustaw zaawansowane zasady dostępu magazynu kluczy.

>[!WARNING]
>Przed usunięciem magazynu kluczy upewnij się, że nie zaszyfrujesz żadnych istniejących maszyn wirtualnych. Aby chronić magazyn przed przypadkowym usunięciem, [Włącz usuwanie nietrwałe](../key-vault/key-vault-soft-delete-powershell.md#enabling-soft-delete) i [blokadę zasobów](../azure-resource-manager/resource-group-lock-resources.md) w magazynie. 
 
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

 - **W razie potrzeby włącz Key Vault wdrożenia:** Umożliwia dostawcy zasobów Microsoft. COMPUTE pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywoływany podczas tworzenia zasobu, na przykład podczas tworzenia maszyny wirtualnej.

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
4. Kliknij przycisk **Zapisz**.

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
     New-AzResourceGroup –Name $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).VaultUri;
     
 #Step 2: Enable the vault for disk encryption.
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
      
 #Step 3: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'HSM';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 4: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```


 
## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Włącz Azure Disk Encryption dla systemu Windows](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Włączanie Azure Disk Encryption dla systemu Linux](azure-security-disk-encryption-linux.md)

