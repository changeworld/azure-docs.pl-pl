---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 6f7f319d2ebb4cd39933addf04f249df02d7819f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314113"
---
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

*Jeśli masz już grupę zasobów, możesz przejść do [programu Utwórz magazyn kluczy](#create-a-key-vault).*

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Utwórz grupę zasobów przy użyciu [grupy az utwórz](/cli/azure/group?view=azure-cli-latest#az-group-create) polecenie interfejsu wiersza polecenia Platformy Azure, polecenie [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell lub z [witryny Azure portal](https://portal.azure.com).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

*Jeśli masz już magazyn kluczy, możesz przejść do [ustawienia zasad zaawansowanego dostępu magazynu kluczy](#set-key-vault-advanced-access-policies).*

Utwórz magazyn kluczy przy użyciu [az keyvault utworzyć](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) polecenie interfejsu wiersza polecenia platformy Azure, [new-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell polecenia, [witryny Azure portal](https://portal.azure.com), lub [szablonu Menedżera zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Magazyn kluczy i maszyny wirtualne muszą być w tej samej subskrypcji. Ponadto, aby upewnić się, że wpisy tajne szyfrowania nie przekraczają granic regionalnych, szyfrowanie dysków platformy Azure wymaga magazynu kluczy i maszyn wirtualnych, które mają być zlokalizowane w tym samym regionie. Tworzenie i używanie magazynu kluczy, który znajduje się w tej samej subskrypcji i regionie co maszyny wirtualne, które mają być szyfrowane. 

Każda przechowalnia kluczy musi mieć unikatową nazwę. W poniższych przykładach <> nazwa <nazwa magazynu kluczy.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Podczas tworzenia magazynu kluczy przy użyciu interfejsu wiersza polecenia platformy Azure dodaj flagę "--enabled-for-disk-encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Podczas tworzenia magazynu kluczy przy użyciu programu Azure PowerShell dodaj flagę "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Przechowalnia kluczy można również utworzyć za pomocą [szablonu Menedżera zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. W szablonie szybki start platformy Azure kliknij pozycję **Wdrażanie na platformie Azure**.
2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, nazwę magazynu kluczy, identyfikator obiektu, warunki prawne i umowę, a następnie kliknij pozycję **Zakup**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy

Platforma Azure potrzebuje dostępu do kluczy szyfrowania lub wpisów tajnych w magazynie kluczy, aby udostępnić je maszynie Wirtualnej do uruchamiania i odszyfrowywania woluminów. 

Jeśli magazyn kluczy nie został włączona do szyfrowania dysków, wdrażania lub wdrażania szablonów w momencie tworzenia (jak pokazano w poprzednim kroku), należy zaktualizować jego zaawansowane zasady dostępu.  

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj [aktualizacji az keyvault,](/cli/azure/keyvault#az-keyvault-update) aby włączyć szyfrowanie dysku dla magazynu kluczy. 

 - **Włącz magazyn kluczy do szyfrowania dysku:** Wymagane jest włączone szyfrowanie dysku. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Włącz magazyn kluczy do wdrożenia, jeśli to konieczne:** Umożliwia dostawcy zasobów Microsoft.Compute pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy odwołuje się w tworzeniu zasobów, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Włącz magazyn kluczy do wdrożenia szablonów, jeśli to konieczne:** Zezwalaj Menedżerowi zasobów na pobieranie wpisów tajnych z magazynu.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Użyj polecenia cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) magazynu kluczy, aby włączyć szyfrowanie dysku dla magazynu kluczy.

  - **Włącz magazyn kluczy do szyfrowania dysku:** EnabledForDiskEncryption jest wymagany do szyfrowania dysku Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Włącz magazyn kluczy do wdrożenia, jeśli to konieczne:** Umożliwia dostawcy zasobów Microsoft.Compute pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy odwołuje się w tworzeniu zasobów, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Włącz magazyn kluczy do wdrożenia szablonów, jeśli to konieczne:** Umożliwia usługi Azure Resource Manager, aby uzyskać wpisy tajne z tego magazynu kluczy, gdy ten magazyn kluczy odwołuje się do wdrożenia szablonu.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. Wybierz magazyn kluczy, przejdź do **opcji Zasady dostępu**i **Kliknij, aby wyświetlić zaawansowane zasady dostępu.**
2. Zaznacz pole oznaczone jako **Włącz dostęp do szyfrowania dysków platformy Azure w celu szyfrowania woluminów**.
3. Wybierz **włącz dostęp do maszyn wirtualnych platformy Azure do wdrożenia** i/lub Włącz dostęp do usługi Azure Resource Manager do wdrożenia **szablonu,** jeśli to konieczne. 
4. Kliknij pozycję **Zapisz**.

    ![Zaawansowane zasady dostępu magazynu kluczy platformy Azure](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Konfigurowanie klucza szyfrowania klucza (KEK)

Jeśli chcesz użyć klucza szyfrowania klucza (KEK) dla dodatkowej warstwy zabezpieczeń kluczy szyfrowania, dodaj KEK do magazynu kluczy. Po określeniu klucza szyfrowania klucza szyfrowanie platformy Azure Disk Encryption używa tego klucza do zawijania klucza tajne szyfrowania przed zapisaniem do usługi Key Vault.

Można wygenerować nowy KEK przy użyciu [polecenia tworzenia klucza klucza az języka azure az,](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) polecenia cmdlet dodatku Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) lub [portalu Azure.](https://portal.azure.com/) Należy wygenerować typ klucza RSA; Szyfrowanie dysków platformy Azure nie obsługuje jeszcze przy użyciu kluczy krzywej eliptycznej.

Zamiast tego można zaimportować KEK z lokalnego modułu HSM zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [Dokumentacja magazynu kluczy](/azure/key-vault/key-vault-hsm-protected-keys).

Adresy URL KEK magazynu kluczy muszą być wersjonetowane. Platforma Azure wymusza to ograniczenie przechowywania wersji. W przypadku prawidłowych adresów URL kluczy tajnych i KEK zobacz następujące przykłady:

* Przykład prawidłowego tajnego adresu URL:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Przykład prawidłowego adresu URL KEK:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Szyfrowanie dysków platformy Azure nie obsługuje określania numerów portów jako części wpisów tajnych magazynu kluczy i adresów URL KEK. Przykłady nieobjętych i obsługiwanych adresów URL magazynu kluczy można znaleźć w następujących przykładach:

  * Akceptowalny adres URL magazynu kluczy:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Niedopuszczalny adres URL magazynu kluczy:*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj polecenia [tworzenia klucza azure az keyvault,](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) aby wygenerować nowy kek i przechowywać go w magazynie kluczy.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

Zamiast tego można zaimportować klucz prywatny przy użyciu polecenia [importowania klucza klucza az języka Azure az:](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)

W obu przypadkach zostanie podaj nazwę pliku KEK do szyfrowania platformy Azure [CLI az vm enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --key-encryption-key parametr. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Użyj polecenia cmdlet [dodatku AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) platformy Azure, aby wygenerować nowy kek i przechowywać go w magazynie kluczy.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Zamiast tego można zaimportować klucz prywatny przy użyciu polecenia [importu klucza programu Azure PowerShell az keyvault.](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)

W obu przypadkach zostanie podana identyfikator magazynu kluczy KEK i adres URL kek do usługi Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId i -KeyEncryptionKeyUrl parametrów. Należy zauważyć, że w tym przykładzie przyjęto założenie, że używasz tego samego magazynu kluczy zarówno dla klucza szyfrowania dysku, jak i klucza KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
