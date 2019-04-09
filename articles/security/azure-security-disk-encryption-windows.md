---
title: Włączanie usługi Azure Disk Encryption dla maszyn wirtualnych IaaS Windows
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft dysku szyfrowanie dla Windows IaaS maszyn wirtualnych platformy Azure.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: f17dc61f47dadf4c808467b2158cd9ef034e1ce9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277123"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Włączanie usługi Azure Disk Encryption dla maszyn wirtualnych IaaS Windows

Można włączyć wiele scenariuszy szyfrowania dysku i kroki zależą od scenariusza. W poniższych częściach omówiono scenariuszach szczegółowo dla maszyn wirtualnych IaaS Windows. Zanim będzie można użyć szyfrowania dysku [wymagania wstępne dotyczące usługi Azure Disk Encryption](../security/azure-security-disk-encryption-prerequisites.md) należy wykonać. 

Wykonaj [migawki](../virtual-machines/windows/snapshot-copy-managed-disk.md) i/lub wykonać kopię zapasową przed dyski są szyfrowane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania może wystąpić, jeśli wystąpił nieoczekiwany błąd występuje podczas szyfrowania. Maszyny wirtualne z dyskami zarządzanymi wymagają kopię zapasową przed zaszyfrowaniem. Po nawiązaniu kopia zapasowa służy polecenie cmdlet Set-AzVMDiskEncryptionExtension do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych, zobacz [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) artykułu. 

>[!WARNING]
> - Jeśli wcześniej używano [usługi Azure Disk Encryption przy użyciu aplikacji Azure AD](azure-security-disk-encryption-prerequisites-aad.md) do zaszyfrowania tej maszyny Wirtualnej, konieczne będzie kontynuować ta opcja służy do szyfrowania maszyny Wirtualnej. Nie można użyć [usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) na tej zaszyfrowanej maszyny Wirtualnej, ponieważ nie jest to obsługiwany scenariusz znaczenie przełączania się aplikacja usługi AAD dla to zaszyfrowanych maszyn wirtualnych nie jest jeszcze obsługiwane. 
> - Usługa Azure Disk Encryption musi znajdować się w tym samym regionie usługi Key Vault i maszyn wirtualnych. Tworzenie i używanie usługi Key Vault, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningWinVM"></a> Włącz szyfrowanie dla istniejących lub działających maszyn wirtualnych Windows IaaS
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. W następujących sekcjach opisano szczegółowo sposób włączania usługi Azure Disk Encryption. Jeśli potrzebujesz informacji o schemacie dla rozszerzenia maszyny wirtualnej, zobacz [rozszerzenia Azure szyfrowania dysku dla Windows](../virtual-machines/extensions/azure-disk-enc-windows.md) artykułu.

>[!IMPORTANT]
 >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption. Migawkę dysku zarządzanego, może zostać pobrany z portalu lub [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) mogą być używane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania. Po nawiązaniu kopii zapasowej polecenia cmdlet Set-AzVMDiskEncryptionExtension może służyć do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension zakończy się niepowodzeniem dla maszyn wirtualnych dysku zarządzanego na podstawie, dopóki nie wykonano kopii zapasowej, a ten parametr został określony. 
>
>Szyfrowanie lub wyłączenie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Włącz szyfrowanie dla istniejących lub działających maszyn wirtualnych przy użyciu programu Azure PowerShell 
Użyj [AzVMDiskEncryptionExtension zestaw](/powershell/module/az.compute/set-azvmdiskencryptionextension) polecenia cmdlet, aby włączyć szyfrowanie na uruchomionej maszyny wirtualnej IaaS na platformie Azure. 

-  **Szyfruj uruchomionej maszyny Wirtualnej:** Poniższy skrypt inicjuje zmiennych i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyny Wirtualnej i magazynu kluczy powinien zostały już utworzone jako warunki wstępne. Zamień MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM i MySecureVault własnymi wartościami.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Szyfruj przy użyciu klucza KEK uruchomionej maszyny Wirtualnej:** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, należy użyć [Get AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) polecenia cmdlet. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Wyłącz szyfrowanie dysków:** Aby wyłączyć szyfrowanie, użyj [AzVMDiskEncryption Wyłącz](/powershell/module/az.compute/disable-azvmdiskencryption) polecenia cmdlet. Wyłączenie szyfrowania dysku danych na maszynie Wirtualnej Windows, gdy zostały zaszyfrowane zarówno systemu operacyjnego i dysków z danymi nie działa zgodnie z oczekiwaniami. Zamiast tego Wyłącz szyfrowanie dla wszystkich dysków.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Włącz szyfrowanie dla istniejących lub działających maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure
Użyj [Włącz az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-enable) polecenie, aby włączyć szyfrowanie na uruchomionej maszyny wirtualnej IaaS na platformie Azure.

- **Szyfruj uruchomionej maszyny Wirtualnej:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Szyfruj przy użyciu klucza KEK uruchomionej maszyny Wirtualnej:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, należy użyć [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) polecenia. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj [az vm encryption, wyłącz](/cli/azure/vm/encryption#az-vm-encryption-disable) polecenia. Wyłączenie szyfrowania dysku danych na maszynie Wirtualnej Windows, gdy zostały zaszyfrowane zarówno systemu operacyjnego i dysków z danymi nie działa zgodnie z oczekiwaniami. Zamiast tego Wyłącz szyfrowanie dla wszystkich dysków.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
 
  > [!NOTE]
  >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption. Migawkę dysku zarządzanego, może zostać pobrany z portalu lub [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) mogą być używane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania. Po nawiązaniu kopii zapasowej polecenia cmdlet Set-AzVMDiskEncryptionExtension może służyć do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup. To polecenie zakończy się niepowodzeniem dla maszyn wirtualnych dysku zarządzanego na podstawie, dopóki nie wykonano kopii zapasowej, a ten parametr został określony. 
  >
  >Szyfrowanie lub wyłączenie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 

### <a name="bkmk_RunningWinVMwRM"> </a>Przy użyciu szablonu usługi Resource Manager

Można włączyć szyfrowanie dysków dla istniejących lub uruchomionych maszyn wirtualnych Windows IaaS na platformie Azure przy użyciu [szablonu usługi Resource Manager w celu zaszyfrowania uruchomionej maszyny Wirtualnej Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. Szablon szybkiego startu platformy Azure, wybierz polecenie **Wdróż na platformie Azure**.

2. Wybierz subskrypcję, grupy zasobów, lokalizacji, ustawienia, postanowienia prawne i umowy. Kliknij przycisk **zakupu** Aby włączyć szyfrowanie na istniejące lub uruchomionej maszyny Wirtualnej IaaS.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager dla istniejących lub działających maszyn wirtualnych:

| Parametr | Opis |
| --- | --- |
| vmName | Nazwa maszyny Wirtualnej, można uruchomić operacji szyfrowania. |
| keyVaultName | Nazwa klucza funkcji BitLocker, należy przekazać do magazyn kluczy. Możesz pobrać go za pomocą polecenia cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` lub polecenia wiersza polecenia platformy Azure `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Nazwa grupy zasobów, który zawiera usługi key vault|
|  KeyEncryptionKeyURL | Adres URL klucza szyfrowania klucza, który jest używany do szyfrowania wygenerowanego klucza funkcji BitLocker. Ten parametr jest opcjonalny w przypadku wybrania **nokek** na liście rozwijanej UseExistingKek. Jeśli wybierzesz **kek** na liście rozwijanej UseExistingKek należy wprowadzić _keyEncryptionKeyURL_ wartości. |
| VolumeType | Typ operacji szyfrowania odbywa się na wolumin. Prawidłowe wartości to _OS_, _danych_, i _wszystkich_. 
| forceUpdateTag | Za każdym razem, gdy operacja musi być wymuszenie uruchomienia są przekazywane w unikatową wartość, np. identyfikator GUID. |
| resizeOSDisk | Należy partycji systemu operacyjnego można zmienić rozmiaru zajmuje pełny wirtualny dysk twardy systemu przed podziałem woluminu systemowego. |
| location | Lokalizacja dla wszystkich zasobów. |

## <a name="encrypt-virtual-machine-scale-sets"></a>Szyfruj zestawów skalowania maszyn wirtualnych

[Zestawy skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/overview.md) pozwalają na tworzenie i zarządzanie grupę identycznych, a obciążenie maszyn wirtualnych o zrównoważonym obciążeniu. Liczba wystąpień maszyn wirtualnych może automatycznie zwiększać lub zmniejszać się w reakcji na zapotrzebowanie lub zdefiniowany harmonogram. Użyj interfejsu wiersza polecenia lub programu Azure PowerShell do zaszyfrowania zestawów skalowania maszyn wirtualnych.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Szyfruj zestawów skalowania maszyn wirtualnych przy użyciu programu Azure PowerShell

Użyj [AzVmssDiskEncryptionExtension zestaw](/powershell/module/az.compute/set-azvmssdiskencryptionextension) polecenia cmdlet, aby włączyć szyfrowanie na zestaw skalowania maszyn wirtualnych Windows. Grupa zasobów, zestaw skalowania maszyn wirtualnych i magazynu kluczy powinien zostały już utworzone jako warunki wstępne.

-  **Szyfruj uruchamianie zestawu skalowania maszyn wirtualnych**:
    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     Set-AzRmVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

   >[!NOTE]
   > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Pobierz stan szyfrowania dla zestawu skalowania maszyn wirtualnych:** Użyj [Get AzVmssDiskEncryption](/powershell/module/az.compute/get-azvmssdiskencryption) polecenia cmdlet.
    
    ```azurepowershell-interactive
    get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Wyłącz szyfrowanie dla zestawu skalowania maszyn wirtualnych**: Użyj [AzVmssDiskEncryption Wyłącz](/powershell/module/az.compute/disable-azvmssdiskencryption) polecenia cmdlet. 

    ```azurepowershell-interactive
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Szyfruj zestawów skalowania maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure

Użyj [Włącz az vmss szyfrowania](/cli/azure/vmss/encryption#az-vmss-encryption-enable) Aby włączyć szyfrowanie na zestaw skalowania maszyn wirtualnych Windows. Jeśli ustawisz zasad uaktualniania na zestawie skalowania, aby ręcznie uruchomić szyfrowanie za pomocą [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). Grupa zasobów, zestaw skalowania maszyn wirtualnych i magazynu kluczy powinien zostały już utworzone jako warunki wstępne.

-  **Szyfruj uruchamianie zestawu skalowania maszyn wirtualnych**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Uruchomionej maszyny wirtualnej zestawu skalowania przy użyciu klucza KEK opakowywać klucz szyfrowania**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
     
   >[!NOTE]
   > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Pobierz stan szyfrowania dla zestawu skalowania maszyn wirtualnych:** Użyj [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Wyłącz szyfrowanie dla zestawu skalowania maszyn wirtualnych**: Użyj [az vmss szyfrowanie wyłączone](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-windows-virtual-machine-scale-sets"></a>Ustawia szablony usługi Azure Resource Manager dla Windows skalowania maszyn wirtualnych

Do szyfrowania lub odszyfrowywania skalowania maszyn wirtualnych Windows ustawia, należy użyć szablonów usługi Azure Resource Manager i poniższymi instrukcjami:

- [Włącz szyfrowanie dla zestawu skalowania maszyn wirtualnych Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)
- [Wyłącz szyfrowanie dla zestawu skalowania maszyn wirtualnych Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

     1. Kliknij przycisk **Wdrażaj na platformie Azure**.
     2. Wypełnij wymagane pola, a następnie zaakceptuj warunki i postanowienia.
     3. Kliknij przycisk **zakupu** do wdrożenia szablonu.

## <a name="bkmk_VHDpre"> </a> Nowe maszyny wirtualne IaaS utworzone na podstawie szyfrowane klienta wirtualnego dysku twardego i kluczy szyfrowania

W tym scenariuszu można włączyć szyfrowanie przy użyciu poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. 

Postępuj zgodnie z instrukcjami w dodatku do przygotowania obrazów zaszyfrowane wstępnie, których można użyć na platformie Azure. Po utworzeniu obrazu, do tworzenia zaszyfrowanych maszyn wirtualnych platformy Azure za pomocą kroki opisane w następnej sekcji.

* [Przygotowywanie wirtualnego dysku twardego Windows zaszyfrowane wstępnie](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Przygotowywanie wirtualnego dysku twardego systemu Linux zaszyfrowane wstępnie](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption. Migawkę dysku zarządzanego, może zostać pobrany z portalu lub [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) mogą być używane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania. Po nawiązaniu kopii zapasowej polecenia cmdlet Set-AzVMDiskEncryptionExtension może służyć do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension zakończy się niepowodzeniem dla maszyn wirtualnych dysku zarządzanego na podstawie, dopóki nie wykonano kopii zapasowej, a ten parametr został określony. 
>
>Szyfrowanie lub wyłączenie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 


### <a name="bkmk_VHDprePSH"> </a> Szyfrowanie maszyn wirtualnych przy użyciu wstępnie zaszyfrowanych dysków VHD za pomocą programu Azure PowerShell
Można włączyć szyfrowanie dysków na zaszyfrowane wirtualnego dysku twardego za pomocą polecenia cmdlet programu PowerShell [AzVMOSDisk zestaw](/powershell/module/az.compute/set-azvmosdisk#examples). W poniższym przykładzie zapewnia niektórych wspólnych parametrów. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włącz szyfrowanie dla dysków nowo dodane dane
Możesz [Dodaj nowy dysk do maszyny Wirtualnej Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md), lub [za pośrednictwem witryny Azure portal](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włącza szyfrowanie na nowo dodany dysk przy użyciu programu Azure PowerShell
 Szyfrowanie nowego dysku dla maszyn wirtualnych Windows za pomocą programu Powershell, należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID wersji sekwencji. W niektórych przypadkach dysk nowo dodane dane mogą być szyfrowane automatycznie przez rozszerzenie usługi Azure Disk Encryption. Automatyczne szyfrowanie występuje przeważnie, po ponownym uruchomieniu maszyny Wirtualnej, po nowy dysk przejściu do trybu online. Jest to zazwyczaj spowodowane tym, ponieważ określono "All" dla typu woluminu podczas szyfrowania dysku wcześniej został uruchomiony na maszynie Wirtualnej. W przypadku automatycznego szyfrowania na dysku nowo dodane dane, firma Microsoft zaleca, ponownie uruchom polecenie cmdlet Set-AzVmDiskEncryptionExtension nowej wersji sekwencji. Jeśli nowy dysk danych jest automatycznie zaszyfrowane i nie powinna być szyfrowane, najpierw odszyfrować wszystkie dyski, a następnie ponownie zaszyfrować przy użyciu nowej wersji sekwencji, określając system operacyjny dla typu woluminu. 
  
 

-  **Szyfruj uruchomionej maszyny Wirtualnej:** Poniższy skrypt inicjuje zmiennych i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyny Wirtualnej i magazynu kluczy powinien zostały już utworzone jako warunki wstępne. Zamień MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM i MySecureVault własnymi wartościami. W tym przykładzie użyto "All" dla parametru - VolumeType zawiera woluminy zarówno systemu operacyjnego i danych. Jeśli chcesz szyfrowania woluminu systemu operacyjnego, należy użyć "System operacyjny" dla parametru - VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Szyfruj przy użyciu klucza KEK uruchomionej maszyny Wirtualnej:** W tym przykładzie użyto "All" dla parametru - VolumeType zawiera woluminy zarówno systemu operacyjnego i danych. Jeśli chcesz szyfrowania woluminu systemu operacyjnego, należy użyć "System operacyjny" dla parametru - VolumeType.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włącza szyfrowanie na nowo dodany dysk przy użyciu wiersza polecenia platformy Azure
 Po uruchomieniu polecenia, aby włączyć szyfrowanie polecenia wiersza polecenia platformy Azure automatycznie zapewni nowej wersji sekwencji dla Ciebie. W przykładzie użyto "All" dla parametru typu woluminu. Może być konieczna zmiana parametru typu woluminu do systemu operacyjnego, jeśli tylko Szyfrujesz dysk systemu operacyjnego. W przeciwieństwie do składni programu Powershell interfejsu wiersza polecenia nie wymaga od użytkownika podczas włączania szyfrowania wersji unikatowy ciąg. Interfejs wiersza polecenia automatycznie generuje i używa własną wartość wersji unikatowy ciąg.   

-  **Szyfruj uruchomionej maszyny Wirtualnej:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Szyfruj przy użyciu klucza KEK uruchomionej maszyny Wirtualnej:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Wyłącz szyfrowanie
Można wyłączyć szyfrowanie przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub przy użyciu szablonu usługi Resource Manager. Wyłączenie szyfrowania dysku danych na maszynie Wirtualnej Windows, gdy zostały zaszyfrowane zarówno systemu operacyjnego i dysków z danymi nie działa zgodnie z oczekiwaniami. Zamiast tego Wyłącz szyfrowanie dla wszystkich dysków.

- **Wyłącz szyfrowanie dysków za pomocą programu Azure PowerShell:** Aby wyłączyć szyfrowanie, użyj [AzVMDiskEncryption Wyłącz](/powershell/module/az.compute/disable-azvmdiskencryption) polecenia cmdlet. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Wyłącz szyfrowanie za pomocą wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj [az vm encryption, wyłącz](/cli/azure/vm/encryption#az-vm-encryption-disable) polecenia. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Wyłącz szyfrowanie za pomocą szablonu usługi Resource Manager:** 

    1. Kliknij przycisk **Wdróż na platformie Azure** z [wyłączenie szyfrowania dysków na uruchomioną maszynę Wirtualną Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) szablonu.
    2. Wybierz subskrypcję, grupy zasobów, lokalizację, maszyny Wirtualnej, typ woluminu, postanowienia prawne i umowy.
    3.  Kliknij przycisk **zakupu** umożliwia wyłączenie szyfrowania dysku na uruchomionej maszynie Wirtualnej Windows. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Disk Encryption dla systemu Linux](azure-security-disk-encryption-linux.md)
