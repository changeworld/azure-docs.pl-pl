---
title: Wymagania wstępne — usługa Azure Disk Encryption dla maszyn wirtualnych IaaS | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wymagania wstępne dotyczące korzystania z systemu Microsoft Azure Disk Encryption dla maszyn wirtualnych IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/25/2019
ms.custom: seodec18
ms.openlocfilehash: 423fad943190232d9e5e674b98b62f4f0dffb8ae
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728747"
---
# <a name="azure-disk-encryption-prerequisites"></a>Wymagania wstępne usługi Azure Disk Encryption

W tym artykule, wymagań wstępnych szyfrowania dysków Azure, opisano elementy, które muszą być spełnione, zanim użyjesz usługi Azure Disk Encryption. Usługa Azure Disk Encryption jest zintegrowana z usługą [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) pomagające w zarządzaniu kluczami szyfrowania. Możesz użyć [programu Azure PowerShell](/powershell/azure/overview), [wiersza polecenia platformy Azure](/cli/azure/), lub [witryny Azure portal](https://portal.azure.com) do konfigurowania usługi Azure Disk Encryption.

Przed włączeniem usługi Azure Disk Encryption na maszynach wirtualnych IaaS platformy Azure w przypadku obsługiwanych scenariuszy, które zostały omówione w [Omówienie szyfrowania dysków Azure](azure-security-disk-encryption-overview.md) artykuł, pamiętaj zostały spełnione wymagania wstępne w miejscu. 

> [!WARNING]
> - Jeśli w celu zaszyfrowania tej maszyny wirtualnej wcześniej była używana [Azure Disk Encryption z aplikacją usługi Azure AD](azure-security-disk-encryption-prerequisites-aad.md) , musisz użyć tej opcji, aby ZAszyfrować maszynę wirtualną. Nie można użyć [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) dla tej zaszyfrowanej maszyny wirtualnej, ponieważ nie jest to obsługiwany scenariusz, co oznacza, że przełączanie z aplikacji usługi AAD dla tej zaszyfrowanej maszyny wirtualnej nie jest jeszcze obsługiwane.
> - Zastosowanie niektórych zaleceń zamieszczonych może zwiększyć danych, sieci lub użycia zasobów obliczeniowych, wynikiem dodatkowych kosztów licencji lub subskrypcji. Musi mieć prawidłową aktywną subskrypcją platformy Azure do tworzenia zasobów na platformie Azure w obsługiwanych regionach.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-vm-sizes"></a>Obsługiwane rozmiary maszyn wirtualnych

Azure Disk Encryption jest dostępna na maszynach wirtualnych spełniających minimalne wymagania dotyczące pamięci:

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

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

### <a name="windows"></a>Windows

- Klient systemu Windows: System Windows 8 lub nowszy.
- System Windows Server: System Windows Server 2008 R2 lub nowszy.  
 
> [!NOTE]
> System Windows Server 2008 R2 wymaga zainstalowania .NET Framework 4,5 do szyfrowania; Zainstaluj go z Windows Update z opcjonalną aktualizacją Microsoft .NET Framework 4.5.2 dla systemów Windows Server 2008 R2 x64 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Systemy Windows Server 2012 R2 Core i Windows Server 2016 Core wymagają zainstalowania składnika BdeHdCfg na maszynie wirtualnej w celu szyfrowania.


### <a name="linux"></a>Linux 

Azure Disk Encryption jest obsługiwane w podzestawie [dystrybucji systemu Linux](../virtual-machines/linux/endorsed-distros.md)z zatwierdzona przez platformę Azure, które jest samym podzbiorem wszystkich możliwych dystrybucji serwerów z systemem Linux.

![Diagram Venna dystrybucji serwerów z systemem Linux, który obsługuje Azure Disk Encryption](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Dystrybucje serwera z systemem Linux, które nie są zatwierdzone przez platformę Azure, nie obsługują Azure Disk Encryption i, które są zatwierdzone, obsługują tylko następujące dystrybucje i wersje Azure Disk Encryption:

| Dystrybucja systemu Linux | Wersja | Typ woluminu obsługiwany w przypadku szyfrowania|
| --- | --- |--- |
| Ubuntu | 18,04| Dysk systemu operacyjnego i danych |
| Ubuntu | 16.04| Dysk systemu operacyjnego i danych |
| Ubuntu | 14.04.5</br>[Dzięki dostrojeniu jądra platformy Azure do wersji 4,15 lub nowszej](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | Dysk systemu operacyjnego i danych |
| RHEL | 7,6 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 7.5 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 7.4 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 7.3 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 7.2 | Dysk systemu operacyjnego i danych (patrz Uwaga poniżej) |
| RHEL | 6.8 | Dysk danych (patrz Uwaga poniżej) |
| RHEL | 6.7 | Dysk danych (patrz Uwaga poniżej) |
| CentOS | 7,6 | Dysk systemu operacyjnego i danych |
| CentOS | 7.5 | Dysk systemu operacyjnego i danych |
| CentOS | 7.4 | Dysk systemu operacyjnego i danych |
| CentOS | 7.3 | Dysk systemu operacyjnego i danych |
| CentOS | 7.2n | Dysk systemu operacyjnego i danych |
| CentOS | 6.8 | Dysk z danymi |
| openSUSE | 42,3 | Dysk z danymi |
| SLES | 12-SP4 | Dysk z danymi |
| SLES | 12-SP3 | Dysk z danymi |

> [!NOTE]
> Nowa implementacja elementu ADE jest obsługiwana w przypadku systemu operacyjnego RHEL i dysku danych dla obrazów z opcją płatność zgodnie z rzeczywistym użyciem. ADE nie jest obecnie obsługiwane w przypadku obrazów RHEL z własnymi subskrypcjami (BYOS). Aby uzyskać więcej informacji, zobacz [Azure Disk Encryption dla systemu Linux](azure-security-disk-encryption-linux.md) .

- Usługa Azure Disk Encryption wymaga, że maszyny wirtualne i usługi key vault, na których znajdują się w tym samym regionie platformy Azure i subskrypcji. Konfigurowanie zasobów w oddzielnych regionach powoduje awarię w włączenie funkcji usługi Azure Disk Encryption.

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>Dodatkowe wymagania wstępne dotyczące maszyn wirtualnych z systemem Linux IaaS 

- Azure Disk Encryption wymaga obecności modułów dm-crypt i vfat w systemie. Usunięcie lub wyłączenie vfat z obrazu domyślnego uniemożliwi systemowi odczytywanie woluminu klucza i Uzyskiwanie klucza wymaganego do odblokowania dysków podczas kolejnych ponownych uruchomień. Kroki ograniczania systemu, które usuwają moduł vfat z systemu, nie są zgodne z Azure Disk Encryption. 
- Przed włączeniem szyfrowania, dysków danych do zaszyfrowania muszą właściwie się na liście/etc/fstab. Użyj nazwy urządzenia trwałego blok dla tego wpisu jako nazwy w formacie "/ dev/sdX" nie można polegać ma zostać skojarzony z tym samym dysku między ponownymi uruchomieniami, szczególnie w przypadku, po zastosowaniu szyfrowania urządzenia. Aby uzyskać szczegółowe informacje na temat tego zachowania, zobacz: [Rozwiązywanie problemów ze zmianami nazw urządzeń maszyny wirtualnej z systemem Linux](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Upewnij się, że poprawnie skonfigurowano ustawienia/etc/fstab potrzeby instalowania. Aby skonfigurować te ustawienia, polecenie instalacji lub ponowne uruchomienie maszyny Wirtualnej i wyzwolić ponowne zainstalowanie w ten sposób. Sprawdź dane wyjściowe polecenia lsblk, aby zweryfikować, że dysk jest nadal zainstalowany, po zakończeniu tej operacji. 
  - Jeśli plik/etc/fstab nie poprawnie zainstalować dysku przed włączeniem szyfrowania, usługa Azure Disk Encryption nie będzie mogła poprawnie go zainstalować.
  - Proces szyfrowania dysków Azure zostanie przesunięty informacji dotyczących instalacji poza/etc/fstab i w jej własnym pliku konfiguracji w ramach procesu szyfrowania. Zostać zignorowany, aby zobaczyć, że kończy zapis brakuje/etc/fstab po szyfrowaniem dysków danych.
  - Przed rozpoczęciem szyfrowania należy zatrzymać wszystkie usługi i procesy, które mogą być zapisywane na zainstalowanych dyskach danych i je wyłączyć, aby nie uruchamiały się automatycznie po ponownym uruchomieniu komputera. Może to spowodować, że pliki będą otwierane na tych partycjach, co uniemożliwia procedurę szyfrowania w celu ich ponownego zainstalowania, powodując niepowodzenie szyfrowania. 
  - Po ponownym uruchomieniu potrwa czas procesu szyfrowania dysków Azure na instalowanie nowo zaszyfrowanych dysków. Nie będzie natychmiast dostępny po ponownym uruchomieniu. Proces wymaga czasu do uruchomienia, odblokowywania i następnie zainstalować zaszyfrowanych dysków przed jest dostępna na potrzeby dostępu do innych procesów. Ten proces może potrwać ponad minutę po ponownym uruchomieniu, w zależności od charakterystyki systemu.

Przykład polecenia, które mogą służyć do zamontowania dysków z danymi oraz tworzenie niezbędne/etc/fstab wpisów można znaleźć w [linii 244-248 tego pliku skryptu](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 

## <a name="bkmk_GPO"></a> Sieci i zasad grupy

**Aby włączyć usługi Azure Disk Encryption funkcji maszyn wirtualnych IaaS musi spełniać następujące wymagania dotyczące konfiguracji punktu końcowego sieci:**
  - Aby uzyskać tokenu, połączyć się z magazynem kluczy, maszyn wirtualnych IaaS musi być w stanie połączyć się z punktem końcowym usługi Azure Active Directory, \[login.microsoftonline.com\].
  - Można zapisać klucze szyfrowania do magazynu kluczy, maszyn wirtualnych IaaS musi być możliwe nawiązanie połączenia z punktu końcowego magazynu kluczy.
  - Maszyn wirtualnych IaaS musi być możliwe nawiązanie połączenia z punktu końcowego usługi Azure storage, który hostuje repozytorium rozszerzenie platformy Azure i konto magazynu platformy Azure, w którym przechowywane są pliki wirtualnego dysku twardego.
  -  Zasady zabezpieczeń ogranicza dostęp do Internetu z maszyn wirtualnych platformy Azure, przypadku rozwiązać poprzedni identyfikator URI i skonfigurowania określonych Reguła zezwalająca na łączności wychodzącej do adresów IP. Aby uzyskać więcej informacji, zobacz [usługi Azure Key Vault za zaporą](../key-vault/key-vault-access-behind-firewall.md).    


**Zasady grupy:**
 - Rozwiązanie Azure Disk Encryption dla maszyn wirtualnych IaaS Windows korzysta z zewnętrznego ochrony klucza funkcji BitLocker. Dla maszyn wirtualnych przyłączonych do domeny, nie Wypchnij żadnych zasad grupy, które wymuszają modułu TPM funkcje ochrony kluczy. Aby uzyskać informacje o zasadach grupy "Zezwalaj na funkcję BitLocker bez zgodnego modułu TPM", zobacz [dokumentacja zasad grupy funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Zasady funkcji BitLocker na maszynach wirtualnych przyłączonych do domeny z niestandardowymi zasadami grupy muszą zawierać następujące ustawienie: [Konfigurowanie magazynu użytkownika informacje odzyskiwania funkcji BitLocker — > zezwalać na 256-bitowy klucz odzyskiwania](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption zakończy się niepowodzeniem w przypadku niezgodności niestandardowych ustawień zasad grupy dla funkcji BitLocker. Na komputerach, które nie mają poprawne ustawienie, należy zastosować nowe zasady, wymusić nowe zasady w celu aktualizacji (/ Force gpupdate.exe) i ponowne uruchomienie, może być wymagane.

- Azure Disk Encryption zakończy się niepowodzeniem, jeśli zasady grupy na poziomie domeny blokują algorytm AES-CBC, który jest używany przez funkcję BitLocker.


## <a name="bkmk_PSH"></a> Program Azure PowerShell
[Program Azure PowerShell](/powershell/azure/overview) udostępnia zestaw poleceń cmdlet, który używa [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) modelu do zarządzania zasobami platformy Azure. Używasz go w przeglądarce za pośrednictwem [usługi Azure Cloud Shell](../cloud-shell/overview.md), lub można go zainstalować na komputerze lokalnym wykonując poniższe instrukcje z niej korzystać w dowolnej sesji programu PowerShell. Jeśli masz już zainstalowany lokalnie, upewnij się, że używasz najnowszej wersji programu Azure PowerShell SDK w wersji do konfigurowania usługi Azure Disk Encryption. Pobierz najnowszą wersję [wersji programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Instalowanie programu Azure PowerShell do użytku na komputerze lokalnym (opcjonalnie): 
1. Postępuj zgodnie z instrukcjami w linkach w systemie operacyjnym, następnie Kontynuuj mimo że pozostałe kroki.      
   - [Zainstaluj i skonfiguruj Azure PowerShell](/powershell/azure/install-az-ps). 
     - Zainstaluj PowerShellGet, Azure PowerShell i Załaduj polecenie AZ module. 

2. Sprawdź zainstalowane wersje modułu AZ module. Jeśli to konieczne, [zaktualizuj moduł programu Azure PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module).
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

4.  Jeśli to konieczne, przejrzyj [wprowadzenie do programu Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="bkmk_CLI"></a> Instalowanie interfejsu wiersza polecenia platformy Azure do użytku na komputerze lokalnym (opcjonalnie)

[Interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure) jest narzędziem wiersza polecenia do zarządzania zasobami platformy Azure. Interfejs wiersza polecenia służy do elastyczne wykonywanie zapytań o dane, obsługi długotrwałych operacji jak nieblokujące procesy i ułatwienia tworzenia skryptów. Można go również używać w przeglądarce z usługą [Azure Cloud Shell](../cloud-shell/overview.md) albo można go zainstalować na maszynie lokalnej i używać w dowolnej sesji programu PowerShell.

1. [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) do użytku na komputerze lokalnym (opcjonalnie):

2. Sprawdź zainstalowaną wersję.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Zaloguj się do platformy Azure za pomocą [az login](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Przegląd [Rozpoczynanie pracy z usługą Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) w razie potrzeby. 


## <a name="prerequisite-workflow-for-key-vault"></a>Wymagań wstępnych przepływu pracy dla usługi Key Vault
Jeśli już znasz z usługi Key Vault i Azure AD wymagania wstępne dotyczące usługi Azure Disk Encryption, możesz użyć [skrypt programu PowerShell wymagania wstępne dotyczące usługi Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Aby uzyskać więcej informacji na temat używania skryptu wymagań wstępnych, zobacz [szyfrowania VM Quickstart](azure-disk-encryption-linux-powershell-quickstart.md) i [dodatku szyfrowania dysków Azure](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Jeśli to konieczne, Utwórz grupę zasobów.
2. Tworzenie magazynu kluczy. 
3. Zaawansowane zasady dostępu magazynu kluczy zestawu.

>[!WARNING]
>Przed usunięciem magazynu kluczy, upewnij się, szyfruje wszystkie istniejące maszyny wirtualne z nim. Aby chronić w magazynie przed przypadkowym usunięciem [Włącz usuwanie nietrwałe](../key-vault/key-vault-soft-delete-powershell.md#enabling-soft-delete) i [blokady zasobu](../azure-resource-manager/resource-group-lock-resources.md) w magazynie. 
 
## <a name="bkmk_KeyVault"></a> Tworzenie magazynu kluczy 
Usługa Azure Disk Encryption jest zintegrowana z usługą [usługi Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ułatwiają kontrolowanie i zarządzanie kluczami szyfrowania dysku i wpisami tajnymi w ramach subskrypcji usługi key vault. Można utworzyć magazyn kluczy lub użyć istniejącego dla usługi Azure Disk Encryption. Aby uzyskać więcej informacji dotyczących magazynów kluczy, zobacz [Rozpoczynanie pracy z usługą Azure Key Vault](../key-vault/key-vault-get-started.md) i [zabezpieczanie własnego magazynu kluczy](../key-vault/key-vault-secure-your-key-vault.md). Szablon usługi Resource Manager, programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure umożliwia tworzenie magazynu kluczy. 


>[!WARNING]
>Upewnij się, że klucze tajne szyfrowania nie przekracza granic regionalnych, usługa Azure Disk Encryption musi znajdować się w tym samym regionie usługi Key Vault i maszyn wirtualnych. Tworzenie i używanie usługi Key Vault, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania. 


### <a name="bkmk_KVPSH"></a> Tworzenie magazynu kluczy przy użyciu programu PowerShell

Można utworzyć magazyn kluczy z Azure PowerShell przy użyciu polecenia cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Aby uzyskać dodatkowe polecenia cmdlet dla Key Vault, zobacz [AZ. datamagazyn](/powershell/module/az.keyvault/). 

1. Jeśli to konieczne, [nawiązać połączenie z subskrypcją platformy Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Utwórz nową grupę zasobów, w razie konieczności z poleceniem [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Aby wyświetlić listę lokalizacji centrów danych, użyj polecenie [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Tworzenie nowego magazynu kluczy przy użyciu polecenia [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Uwaga **nazwa magazynu**, **Nazwa grupy zasobów**, **identyfikator zasobu**, **identyfikator URI magazynu**i **obiekt o identyfikatorze** które są zwracane do późniejszego użycia podczas szyfrowania dysków. 


### <a name="bkmk_KVCLI"></a> Tworzenie magazynu kluczy przy użyciu wiersza polecenia platformy Azure
Można zarządzać magazynem kluczy przy użyciu wiersza polecenia platformy Azure [az keyvault](/cli/azure/keyvault#commands) poleceń. Aby utworzyć magazyn kluczy, użyj [tworzenie az keyvault](/cli/azure/keyvault#az-keyvault-create).

1. Jeśli to konieczne, [nawiązać połączenie z subskrypcją platformy Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Utwórz nową grupę zasobów, jeśli to konieczne, za pomocą [Tworzenie grupy az](/cli/azure/group#az-group-create). Aby wyświetlić lokalizacje, użyj [konta az list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Tworzenie nowego magazynu kluczy przy użyciu [tworzenie az keyvault](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Uwaga **nazwa magazynu** (nazwa), **nazwy grupy zasobów**, **identyfikator zasobu** (ID), **identyfikator URI magazynu**i **obiektu o identyfikatorze** później, które są zwracane do użycia. 

### <a name="bkmk_KVRM"></a> Tworzenie magazynu kluczy przy użyciu szablonu usługi Resource Manager

Można utworzyć magazynu kluczy przy użyciu [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Szablon szybkiego startu platformy Azure, wybierz polecenie **Wdróż na platformie Azure**.
2. Wybierz subskrypcję, grupy zasobów, lokalizację grupy zasobów, usługi Key Vault nazwa, identyfikator obiektu, postanowienia prawne i umowy, a następnie kliknij przycisk **zakupu**. 


## <a name="bkmk_KVper"></a> Zestaw magazynu kluczy, zaawansowane zasady dostępu
Platforma Azure wymaga dostępu do kluczy szyfrowania i wpisy tajne w magazynie kluczy, aby udostępnić je do maszyny Wirtualnej do rozruchu i odszyfrowywania woluminów. Włącz szyfrowanie dysków w magazynie kluczy lub wdrożenia zakończy się niepowodzeniem.  

### <a name="bkmk_KVperPSH"></a> Zestaw magazynu kluczy, zaawansowane zasady dostępu przy użyciu programu Azure PowerShell
 Aby włączyć szyfrowanie dysków dla magazynu kluczy, należy użyć polecenia cmdlet programu PowerShell magazynu kluczy [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) .

  - **Włącz Key Vault szyfrowania dysku:** Usługa EnabledForDiskEncryption jest wymagana do szyfrowania dysków Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **W razie potrzeby Włącz Key Vault wdrożenia:** Umożliwia dostawcy zasobów Microsoft. COMPUTE pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywoływany podczas tworzenia zasobu, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **W razie potrzeby Włącz Key Vault wdrożenia szablonów:** Umożliwia Azure Resource Manager pobierania wpisów tajnych z tego magazynu kluczy, gdy do tego magazynu kluczy odwołuje się wdrożenie szablonu.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Zestaw magazynu kluczy, zaawansowane zasady dostępu przy użyciu wiersza polecenia platformy Azure
Użyj [az keyvault update](/cli/azure/keyvault#az-keyvault-update) umożliwia szyfrowanie dysków dla magazynu kluczy. 

 - **Włącz Key Vault szyfrowania dysku:** Szyfrowanie włączone dla dysku jest wymagane. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **W razie potrzeby Włącz Key Vault wdrożenia:** Umożliwia dostawcy zasobów Microsoft. COMPUTE pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywoływany podczas tworzenia zasobu, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **W razie potrzeby Włącz Key Vault wdrożenia szablonów:** Zezwól Menedżer zasobów na pobieranie wpisów tajnych z magazynu.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Zestaw magazynu kluczy, zaawansowane zasady dostępu w witrynie Azure portal

1. Wybierz z magazynu kluczy, przejdź do **zasady dostępu**, i **kliknij, aby wyświetlić zaawansowane zasady dostępu**.
2. Zaznacz pole opcji **zapewnianie dostępu do usługi Azure Disk Encryption dla szyfrowania woluminu**.
3. Wybierz **włączyć dostęp do usługi Azure Virtual Machines na potrzeby wdrożenia** i/lub **Włącz dostęp do usługi Azure Resource Manager dla wdrożenia szablonu**, jeśli to konieczne. 
4. Kliknij pozycję **Zapisz**.

    ![Zaawansowane zasady dostępu magazynu kluczy Azure](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Konfigurowanie klucza szyfrowania (opcjonalnie)
Jeśli chcesz użyć klucz szyfrowania klucza (KEK) Aby uzyskać dodatkową warstwę zabezpieczeń dla kluczy szyfrowania, należy dodać KEK do magazynu kluczy. Użyj polecenia cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) , aby utworzyć klucz szyfrowania klucza w magazynie kluczy. Można także zaimportować KEK, z usługi zarządzania kluczami w środowisku lokalnym przez moduł HSM. Aby uzyskać więcej informacji, zobacz [Key Vault dokumentacji](../key-vault/key-vault-hsm-protected-keys.md). Jeśli klucz szyfrowania jest określony, usługi Azure Disk Encryption używa tego klucza do opakowania wpisów tajnych szyfrowania przed zapisaniem w usłudze Key Vault.

* Podczas generowania kluczy należy użyć typu klucza RSA. Azure Disk Encryption nie obsługuje jeszcze kluczy krzywej eliptycznej.

* KEK adresy URL i wpisu tajnego usługi key vault, na których musi być poddany kontroli wersji. Azure wymusza to ograniczenie wersji. Nieprawidłowy klucz tajny i adresy URL KEK zobacz następujące przykłady:

  * Przykład prawidłowy adres URL wpisu tajnego:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Przykład prawidłowego adresu URL klucza KEK:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Usługa Azure Disk Encryption nie obsługuje określania numerów portów jako część wpisy tajne usługi key vault i KEK adresów URL. Aby zapoznać się z przykładami adresów URL obsługiwane i nieobsługiwane key vault zobacz następujące przykłady:

  * Adres URL magazynu kluczy można zaakceptować  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Adres URL dopuszczalne magazynu kluczy:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*


### <a name="bkmk_KEKPSH"></a> Skonfiguruj klucz szyfrowania klucza przy użyciu programu Azure PowerShell 
Przed użyciem skryptu programu PowerShell, należy zapoznać się z wstępnie wymagane składniki usługi Azure Disk Encryption, aby zrozumieć kroki w skrypcie. Przykładowy skrypt może wymagać zmiany w danym środowisku. Ten skrypt tworzy wszystkie wymagania wstępne usługi Azure Disk Encryption i szyfruje istniejącej maszyny Wirtualnej IaaS, zawijanie klucz szyfrowania dysku przy użyciu klucza szyfrowania. 

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


 
## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Włączanie usługi Azure Disk Encryption for Windows](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Disk Encryption dla systemu Linux](azure-security-disk-encryption-linux.md)

