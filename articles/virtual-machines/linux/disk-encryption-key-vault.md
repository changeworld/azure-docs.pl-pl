---
title: Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption
description: Ten artykuł zawiera instrukcje dotyczące tworzenia i konfigurowania magazynu kluczy do użycia z usługą Azure Disk Encryption
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a818d9fe9707d1789fbe8e77489fc380fd2c92dd
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970621"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption

Azure Disk Encryption używa Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi.  Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz Wprowadzenie do [Azure Key Vault](../../key-vault/key-vault-get-started.md) i [Zabezpieczanie magazynu kluczy](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Jeśli usługa Azure AD została wcześniej Azure Disk Encryption użyta w celu zaszyfrowania maszyny wirtualnej, należy użyć tej opcji, aby zaszyfrować maszynę wirtualną. Aby uzyskać szczegółowe informacje [, zobacz Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption w usłudze Azure AD (w poprzedniej wersji)](disk-encryption-key-vault-aad.md) .

Tworzenie i Konfigurowanie magazynu kluczy do użycia z Azure Disk Encryption obejmuje trzy kroki:

1. Tworzenie grupy zasobów, w razie konieczności.
2. Tworzenie magazynu kluczy. 
3. Ustawianie zaawansowanych zasad dostępu magazynu kluczy.

Te kroki przedstawiono w następujących przewodnikach szybki start:

- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli-quickstart.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu programu Azure PowerShell](disk-encryption-cli-quickstart.md)

Możesz również, jeśli chcesz, wygenerować lub zaimportować klucz szyfrowania klucza (KEK).

> [!Note]
> Kroki opisane w tym artykule są zautomatyzowane w [Azure Disk Encryption skrypt interfejsu wiersza polecenia wymagania wstępne](https://github.com/ejarvi/ade-cli-getting-started) i [Azure Disk Encryption skrypt programu PowerShell dotyczące wymagań wstępnych](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i nawiązywanie połączenia z platformą Azure

Kroki opisane w tym artykule można wykonać przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/), [Azure PowerShell Az module](/powershell/azure/overview)lub [Azure Portal](https://portal.azure.com). 

Gdy Portal jest dostępny za pomocą przeglądarki, interfejs wiersza polecenia platformy Azure i Azure PowerShell wymagają instalacji lokalnej; Zobacz [Azure Disk Encryption dla systemu Linux: Zainstaluj narzędzia](disk-encryption-linux.md#install-tools-and-connect-to-azure) , aby uzyskać szczegółowe informacje.

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Przed rozpoczęciem korzystania z interfejsu wiersza polecenia platformy Azure lub Azure PowerShell należy najpierw nawiązać połączenie z subskrypcją platformy Azure. Można to zrobić, [logując się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [logując się przy użyciu programu Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)lub dostarczając poświadczenia do Azure Portal po wyświetleniu monitu.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Utwórz grupę zasobów

*Jeśli masz już grupę zasobów, możesz pominąć, aby [utworzyć magazyn kluczy](#create-a-key-vault).*

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Utwórz grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure CLI, polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell lub z [Azure Portal](https://portal.azure.com).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

*Jeśli masz już Magazyn kluczy, możesz pominąć, aby [ustawić zaawansowane zasady dostępu magazynu kluczy](#set-key-vault-advanced-access-policies).*

Utwórz magazyn kluczy za pomocą polecenia [AZ Key magazyn Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) Azure CLI, polecenia [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) programu Azure PowerShell, [Azure Portal](https://portal.azure.com)lub [szablonu Menedżer zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Aby zapewnić, że wpisy tajne szyfrowania nie przekraczają granic regionalnych, Azure Disk Encryption wymaga, aby Key Vault i maszyny wirtualne znajdowały się w tym samym regionie. Utwórz i użyj Key Vault, który znajduje się w tym samym regionie co maszyny wirtualne do zaszyfrowania. 

Każdy Key Vault musi mieć unikatową nazwę. Zastąp < unikatowym identyfikatorem magazynu kluczy > nazwą magazynu klucza w poniższych przykładach.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Podczas tworzenia magazynu kluczy przy użyciu interfejsu wiersza polecenia platformy Azure Dodaj flagę "--Enabled-for-Disk-Encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Podczas tworzenia magazynu kluczy przy użyciu Azure PowerShell należy dodać flagę "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Możesz również utworzyć magazyn kluczy przy użyciu [szablonu Menedżer zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. W szablonie szybkiego startu platformy Azure kliknij pozycję **Wdróż na platformie Azure**.
2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, nazwę Key Vault, identyfikator obiektu, postanowienia prawne i umowę, a następnie kliknij przycisk **Kup**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy

Platforma Azure wymaga dostępu do kluczy szyfrowania i wpisy tajne w magazynie kluczy, aby udostępnić je do maszyny Wirtualnej do rozruchu i odszyfrowywania woluminów. 

Jeśli nie włączono magazynu kluczy na potrzeby wdrożenia szyfrowania dysku, wdrożenia lub szablonu w momencie tworzenia (jak pokazano w poprzednim kroku), należy zaktualizować zaawansowane zasady dostępu.  

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj [AZ Key Update](/cli/azure/keyvault#az-keyvault-update) , aby włączyć szyfrowanie dysków dla magazynu kluczy. 

 - **Włącz Key Vault szyfrowania dysku:** Szyfrowanie włączone dla dysku jest wymagane. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **W razie potrzeby włącz Key Vault wdrożenia:** Umożliwia dostawcy zasobów Microsoft. COMPUTE pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywoływany podczas tworzenia zasobu, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **W razie potrzeby włącz Key Vault wdrożenia szablonów:** Zezwól Menedżer zasobów na pobieranie wpisów tajnych z magazynu.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Aby włączyć szyfrowanie dysków dla magazynu kluczy, należy użyć polecenia cmdlet programu PowerShell magazynu kluczy [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) .

  - **Włącz Key Vault szyfrowania dysku:** Usługa EnabledForDiskEncryption jest wymagana do szyfrowania dysków Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **W razie potrzeby włącz Key Vault wdrożenia:** Umożliwia dostawcy zasobów Microsoft. COMPUTE pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywoływany podczas tworzenia zasobu, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **W razie potrzeby włącz Key Vault wdrożenia szablonów:** Umożliwia Azure Resource Manager pobierania wpisów tajnych z tego magazynu kluczy, gdy do tego magazynu kluczy odwołuje się wdrożenie szablonu.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Portalu Azure

1. Wybierz magazyn kluczy, przejdź do pozycji **zasady dostępu**, a **następnie kliknij, aby wyświetlić zaawansowane zasady dostępu**.
2. Zaznacz pole o nazwie **Włącz dostęp do Azure Disk Encryption na potrzeby szyfrowania woluminów**.
3. Wybierz pozycję **Włącz dostęp do usługi Azure Virtual Machines w celu wdrożenia** i/lub **włącz dostęp do Azure Resource Manager na potrzeby wdrożenia szablonu**, jeśli jest to konieczne. 
4. Kliknij przycisk **Save** (Zapisz).

    ![Zaawansowane zasady dostępu magazynu kluczy Azure](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Skonfiguruj klucz szyfrowania klucza (KEK)

Jeśli chcesz użyć klucz szyfrowania klucza (KEK) Aby uzyskać dodatkową warstwę zabezpieczeń dla kluczy szyfrowania, należy dodać KEK do magazynu kluczy. Jeśli klucz szyfrowania jest określony, usługi Azure Disk Encryption używa tego klucza do opakowania wpisów tajnych szyfrowania przed zapisaniem w usłudze Key Vault.

Nowy KEK można wygenerować przy użyciu interfejsu wiersza polecenia platformy Azure [AZ Key magazynu create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) Azure PowerShell, polecenie cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) lub [Azure Portal](https://portal.azure.com/). Musisz wygenerować typ klucza RSA; Azure Disk Encryption nie obsługuje jeszcze kluczy krzywej eliptycznej.

Zamiast tego możesz zaimportować KEK z lokalnego modułu HSM zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [dokumentację Key Vault](../../key-vault/key-vault-hsm-protected-keys.md). 

Adresy URL KEK magazynu kluczy muszą być w wersji. Azure wymusza to ograniczenie wersji. Nieprawidłowy klucz tajny i adresy URL KEK zobacz następujące przykłady:

* Przykładowy prawidłowy tajny adres URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Przykład prawidłowego adresu URL KEK: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Usługa Azure Disk Encryption nie obsługuje określania numerów portów jako część wpisy tajne usługi key vault i KEK adresów URL. Aby zapoznać się z przykładami adresów URL obsługiwane i nieobsługiwane key vault zobacz następujące przykłady:

  * Akceptowalny adres URL magazynu kluczy: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Nieakceptowalny adres URL magazynu kluczy: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj interfejsu wiersza polecenia platformy Azure [AZ Key magazynu Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) , aby wygenerować nowy KEK i zapisać go w magazynie kluczy.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. Zamiast tego możesz zaimportować klucz prywatny przy użyciu interfejsu wiersza polecenia platformy Azure [AZ Key magazynu klucz import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) :

W obu przypadkach należy podać nazwę KEK w interfejsie wiersza polecenia platformy Azure [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --Key-Encryption-Key. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Użyj polecenia cmdlet Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) , aby wygenerować nowy KEK i zapisać go w magazynie kluczy.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Zamiast tego możesz zaimportować klucz prywatny przy użyciu polecenia Azure PowerShell [AZ Key magazynu klucz import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) .

W obu przypadkach należy podać identyfikator magazynu kluczy KEK oraz adres URL KEK do parametrów Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId i-KeyEncryptionKeyUrl. Należy pamiętać, że w tym przykładzie założono, że używasz tego samego magazynu kluczy zarówno dla klucza szyfrowania dysku, jak i dla KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>Następne kroki

- [Skrypt interfejsu wiersza polecenia Azure Disk Encryption preinstalacji](https://github.com/ejarvi/ade-cli-getting-started)
- [Skrypt programu PowerShell dla Azure Disk Encryption wymagań wstępnych](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Uczenie się [Azure Disk Encryption scenariuszy na maszynach wirtualnych z systemem Linux](disk-encryption-linux.md)
- Dowiedz się, jak [rozwiązywać problemy Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Przeczytaj [Azure Disk Encryption przykładowe skrypty](disk-encryption-sample-scripts.md)
