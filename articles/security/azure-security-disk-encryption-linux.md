---
title: Włączanie usługi Azure Disk Encryption dla maszyn wirtualnych IaaS z systemem Linux
description: Ten artykuł zawiera instrukcje na temat włączania systemu Microsoft Azure Disk Encryption dla maszyn wirtualnych IaaS z systemem Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 09/16/2019
ms.custom: seodec18
ms.openlocfilehash: 03d50fbd9c3138f4d34dd748da50faefc3d8b24d
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067821"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms"></a>Włączanie usługi Azure Disk Encryption dla maszyn wirtualnych IaaS z systemem Linux 

Można włączyć wiele scenariuszy szyfrowania dysku i kroki zależą od scenariusza. W poniższych częściach omówiono scenariuszach szczegółowo dla maszyn wirtualnych IaaS z systemem Linux. Przed rozpoczęciem korzystania z szyfrowania dysków należy zapoznać się z [wymaganiami wstępnymi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) , a sekcja [dodatkowych wymagań wstępnych dotyczących maszyn wirtualnych z systemem Linux IaaS](azure-security-disk-encryption-prerequisites.md#additional-prerequisites-for-linux-iaas-vms) powinna zostać sprawdzona.

Wykonaj [migawki](../virtual-machines/windows/snapshot-copy-managed-disk.md) i/lub wykonać kopię zapasową przed dyski są szyfrowane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania może wystąpić, jeśli wystąpił nieoczekiwany błąd występuje podczas szyfrowania. Maszyny wirtualne z dyskami zarządzanymi wymagają kopię zapasową przed zaszyfrowaniem. Po wykonaniu kopii zapasowej można użyć polecenia cmdlet Set-AzVMDiskEncryptionExtension, aby zaszyfrować dyski zarządzane przez określenie parametru-skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych, zobacz [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) artykułu. 

>[!WARNING]
> - Jeśli w celu zaszyfrowania tej maszyny wirtualnej wcześniej była używana [Azure Disk Encryption z aplikacją usługi Azure AD](azure-security-disk-encryption-prerequisites-aad.md) , musisz użyć tej opcji, aby ZAszyfrować maszynę wirtualną. Nie można użyć [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) dla tej zaszyfrowanej maszyny wirtualnej, ponieważ nie jest to obsługiwany scenariusz, co oznacza, że przełączanie z aplikacji usługi AAD dla tej zaszyfrowanej maszyny wirtualnej nie jest jeszcze obsługiwane.
 > - Usługa Azure Disk Encryption musi znajdować się w tym samym regionie usługi Key Vault i maszyn wirtualnych. Tworzenie i używanie usługi Key Vault, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania.
> - W przypadku szyfrowania woluminów systemu operacyjnego Linux maszyna wirtualna powinna być uważana za niedostępną. Zdecydowanie zalecamy uniknięcie logowania przy użyciu protokołu SSH, gdy szyfrowanie jest w toku, aby uniknąć problemów z blokowaniem otwartych plików, do których konieczne będzie uzyskanie dostępu podczas procesu szyfrowania. Aby sprawdzić postęp, można użyć poleceń [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) lub [szyfrowania maszyny wirtualnej](/cli/azure/vm/encryption#az-vm-encryption-show) . Ten proces może potrwać kilka godzin w przypadku woluminu systemu operacyjnego 30 GB, a także dodatkowy czas na szyfrowanie woluminów danych. Czas szyfrowania woluminu danych będzie proporcjonalny do rozmiaru i ilości woluminów danych, chyba że zostanie użyta opcja Szyfruj cały format. 
> - Wyłączenie szyfrowania na maszynach wirtualnych z systemem Linux jest obsługiwana tylko dla woluminów danych. Go nie jest obsługiwana na woluminach systemu operacyjnego i danych, jeśli wolumin systemu operacyjnego został zaszyfrowany.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningLinux"> </a> Włącz szyfrowanie dla istniejących lub uruchamianie systemu Linux Maszynie wirtualnej IaaS
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu usługi Resource Manager, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. Jeśli potrzebujesz informacji o schemacie dla rozszerzenia maszyny wirtualnej, zobacz [usługi Azure Disk Encryption dla systemu Linux rozszerzenia](../virtual-machines/extensions/azure-disk-enc-linux.md) artykułu.

>[!IMPORTANT]
 >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption. Migawkę dysku zarządzanego, może zostać pobrany z portalu lub [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) mogą być używane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania. Po wykonaniu kopii zapasowej można użyć polecenia cmdlet Set-AzVMDiskEncryptionExtension w celu zaszyfrowania dysków zarządzanych przez określenie parametru-skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension zakończy się niepowodzeniem z maszynami wirtualnymi opartymi na dyskach zarządzanych, dopóki nie zostanie utworzona kopia zapasowa i ten parametr został określony. 
>
>Szyfrowanie lub wyłączenie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Włącz szyfrowanie dla istniejących lub uruchomionej maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure 
Można włączyć szyfrowanie dysków na zaszyfrowane wirtualnego dysku twardego przy instalowaniu i używaniu [interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure) narzędzie wiersza polecenia. Można go również używać w przeglądarce z usługą [Azure Cloud Shell](../cloud-shell/overview.md) albo można go zainstalować na maszynie lokalnej i używać w dowolnej sesji programu PowerShell. Aby włączyć szyfrowanie na istniejące lub działających maszyn wirtualnych systemu Linux IaaS na platformie Azure, użyj następujących poleceń interfejsu wiersza polecenia:

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
    > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj polecenia [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj polecenia [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Wyłączenie szyfrowania jest dozwolona tylko na woluminach danych dla maszyn wirtualnych systemu Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Włącz szyfrowanie dla istniejących lub uruchomionej maszyny Wirtualnej systemu Linux przy użyciu programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) , aby włączyć szyfrowanie na działającej maszynie wirtualnej IaaS na platformie Azure. Utwórz [migawkę](../virtual-machines/windows/snapshot-copy-managed-disk.md) i/lub wykonaj kopię zapasową maszyny wirtualnej przy użyciu [Azure Backup](../backup/backup-azure-vms-encryption.md) przed zaszyfrowaniem dysków. Parametr-skipVmBackup jest już określony w skryptach programu PowerShell, aby można było zaszyfrować działającą maszynę wirtualną z systemem Linux.

-  **Szyfruj uruchomionej maszyny Wirtualnej:** Poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyny Wirtualnej i magazynu kluczy powinien zostały już utworzone jako warunki wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami. Zmodyfikuj parametr-Volumetype, aby określić, które dyski są szyfrowane.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Szyfruj przy użyciu klucza KEK uruchomionej maszyny Wirtualnej:** Może być konieczne dodanie parametru - VolumeType, jeśli Szyfrujesz dysk danych i nie dysk systemu operacyjnego. 

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Wyłącz szyfrowanie dysków:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Wyłączenie szyfrowania jest dozwolona tylko na woluminach danych dla maszyn wirtualnych systemu Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Włącz szyfrowanie dla istniejących lub uruchamianie systemu Linux maszyny Wirtualnej IaaS przy użyciu szablonu

Można włączyć szyfrowania dysków na istniejące lub uruchamianie systemu Linux maszyny Wirtualnej IaaS na platformie Azure przy użyciu [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Kliknij przycisk **Wdróż na platformie Azure** na szablonie Szybki Start platformy Azure.

2. Wybieranie subskrypcji, grupy zasobów, lokalizację grupy zasobów, parametry, postanowienia prawne i umowy. Kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na istniejące lub uruchomionej maszyny Wirtualnej IaaS.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager dla istniejących lub działających maszyn wirtualnych:

| Parametr | Opis |
| --- | --- |
| vmName | Nazwa maszyny Wirtualnej, można uruchomić operacji szyfrowania. |
| keyVaultName | Nazwa klucza funkcji BitLocker, należy przekazać do magazyn kluczy. Można to zrobić za pomocą polecenia cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` lub interfejsu CLI platformy Azure`az keyvault list --resource-group "MyKeyVaultResourceGroupName"`|
| keyVaultResourceGroup | Nazwa grupy zasobów, który zawiera usługi key vault|
|  KeyEncryptionKeyURL | Adres URL klucza szyfrowania klucza, który jest używany do szyfrowania wygenerowanego klucza funkcji BitLocker. Ten parametr jest opcjonalny w przypadku wybrania **nokek** na liście rozwijanej UseExistingKek. Jeśli wybierzesz **kek** na liście rozwijanej UseExistingKek należy wprowadzić _keyEncryptionKeyURL_ wartości. |
| VolumeType | Typ operacji szyfrowania odbywa się na wolumin. Prawidłowe wartości to _OS_, _danych_, i _wszystkich_. 
| forceUpdateTag | Za każdym razem, gdy operacja musi być wymuszenie uruchomienia są przekazywane w unikatową wartość, np. identyfikator GUID. |
| resizeOSDisk | Należy partycji systemu operacyjnego można zmienić rozmiaru zajmuje pełny wirtualny dysk twardy systemu przed podziałem woluminu systemowego. |
| location | Lokalizacja dla wszystkich zasobów. |



## <a name="encrypt-virtual-machine-scale-sets"></a>Szyfruj zestawów skalowania maszyn wirtualnych
[Zestawy skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/overview.md) pozwalają na tworzenie i zarządzanie grupę identycznych, a obciążenie maszyn wirtualnych o zrównoważonym obciążeniu. Liczba wystąpień maszyn wirtualnych może automatycznie zwiększać lub zmniejszać się w reakcji na zapotrzebowanie lub zdefiniowany harmonogram. Użyj interfejsu wiersza polecenia lub programu Azure PowerShell do zaszyfrowania zestawów skalowania maszyn wirtualnych. Tylko szyfrowanie dysków danych jest obsługiwane na maszynach wirtualnych zestawu skalowania systemu Linux.

Przykład pliku wsadowego, do szyfrowania dysku danych zestaw skalowania systemu Linux można znaleźć [tutaj](https://github.com/Azure-Samples/azure-cli-samples/tree/master/disk-encryption/vmss). W tym przykładzie tworzy grupę zasobów, zestaw skalowania systemu Linux, instaluje dysk 5 GB danych oraz szyfruje zestawu skalowania maszyn wirtualnych.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Szyfruj zestawów skalowania maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure

Użyj [Włącz az vmss szyfrowania](/cli/azure/vmss/encryption#az-vmss-encryption-enable) Aby włączyć szyfrowanie na zestaw skalowania maszyn wirtualnych Windows. Jeśli ustawisz zasad uaktualniania na zestawie skalowania, aby ręcznie uruchomić szyfrowanie za pomocą [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). Grupa zasobów, maszyny Wirtualnej i magazynu kluczy powinien zostały już utworzone jako warunki wstępne. 

-  **Szyfruj uruchamianie zestawu skalowania maszyn wirtualnych**
    ```azurecli-interactive
    az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --volume-type DATA --disk-encryption-keyvault "MySecureVault"
    ```

-  **Uruchomionej maszyny wirtualnej zestawu skalowania przy użyciu klucza KEK opakowywać klucz szyfrowania**
     ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --volume-type DATA --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault"
     ```

    >[!NOTE]
    > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Pobierz stan szyfrowania dla zestawu skalowania maszyn wirtualnych:** Użyj [AZ VMSS Encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
    az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Wyłącz szyfrowanie na zestawie skalowania maszyn wirtualnych**: Użyj [AZ VMSS Encryption Disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
    az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Szyfruj zestawów skalowania maszyn wirtualnych przy użyciu programu Azure PowerShell

Użyj polecenia cmdlet [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension) , aby włączyć szyfrowanie w zestawie skalowania maszyn wirtualnych z systemem Windows. Grupa zasobów, maszyny Wirtualnej i magazynu kluczy powinien zostały już utworzone jako warunki wstępne.

-  **Szyfruj uruchamianie zestawu skalowania maszyn wirtualnych**:
      ```powershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
      $VmssName = "MySecureVmss";
      $KeyVaultName= "MySecureVault";
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -VolumeType Data -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
      ```

-  **Szyfruj uruchomiony zestaw skalowania maszyn wirtualnych za pomocą KEK, aby otoczyć klucz**:
      ```powershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
      $VmssName = "MySecureVmss";
      $KeyVaultName= "MySecureVault";
      $keyEncryptionKeyName = "MyKeyEncryptionKey";
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -VolumeType Data -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl  -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
      ```

    >[!NOTE]
    > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Pobierz stan szyfrowania dla zestawu maszyn wirtualnych**: Użyj polecenia cmdlet [Get-AzVmssVMDiskEncryption](/powershell/module/az.compute/get-azvmssvmdiskencryption) .
    
    ```powershell
    Get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Wyłącz szyfrowanie na zestawie skalowania maszyn wirtualnych**: Użyj polecenia cmdlet [disable-AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) . 

    ```powershell
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-linux-virtual-machine-scale-sets"></a>Szablony Azure Resource Manager dla zestawów skalowania maszyn wirtualnych z systemem Linux

Aby zaszyfrować lub odszyfrować zestawy skalowania maszyn wirtualnych z systemem Linux, użyj Azure Resource Manager szablonów i instrukcji poniżej:

- [Włącz szyfrowanie na zestawie skalowania maszyn wirtualnych z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)
- [Wyłączanie szyfrowania na zestawie skalowania maszyn wirtualnych z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

     1. Kliknij przycisk **Wdrażaj na platformie Azure**.
     2. Wypełnij pola wymagane, a następnie zaakceptuj warunki i postanowienia.
     3. Kliknij przycisk **Kup** , aby wdrożyć szablon.

## <a name="bkmk_EFA"> </a>Użyj funkcji EncryptFormatAll dla dysków z danymi na maszynach wirtualnych IaaS z systemem Linux

**EncryptFormatAll** parametru skraca czas dla dysków z danymi systemu Linux do zaszyfrowania. Partycje spełniającego określone kryteria zostaną sformatowane (z bieżącym system plików). Następnie będzie ponownego zainstalowania powrót, do których była przed wykonaniem polecenia. Jeśli chcesz wykluczyć dysk danych, która spełnia kryteria, należy go odinstalować przed uruchomieniem polecenia.

 Po uruchomieniu tego polecenia, wszystkie dyski, które zostały zainstalowane wcześniej zostaną sformatowane. Następnie Warstwa szyfrowania zostanie uruchomiony na podstawie teraz pusty dysk. Ta opcja jest zaznaczona, dysków tymczasowych zasobów dołączonych do maszyny Wirtualnej również być szyfrowana. W przypadku zresetowania efemerycznego dysku zostanie ponownie sformatowany i ponownie szyfrowane dla maszyny Wirtualnej przez rozwiązanie Azure Disk Encryption przy okazji dalej. Po zaszyfrowaniu dysku zasobów [Agent Microsoft Azure systemu Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) nie będzie w stanie zarządzać dyskiem zasobów i włączać pliku wymiany, ale można ręcznie skonfigurować plik wymiany.

>[!WARNING]
> EncryptFormatAll nie powinny być używane, gdy ma wymaganych danych na woluminach danych maszyny Wirtualnej. Aby wykluczyć dyski z szyfrowania, mogą je odinstalowywania. Należy najpierw wypróbować EncryptFormatAll najpierw na testowej maszynie Wirtualnej, parametr funkcji i jej domniemanie, przed podjęciem próby go na maszynie Wirtualnej w środowisku produkcyjnym. Opcja EncryptFormatAll formatuje dysk z danymi i z niego wszystkie dane zostaną utracone. Przed kontynuowaniem sprawdź, czy dyski, które chcesz wykluczyć są prawidłowo odinstalowane. </br></br>
 >Jeśli ten parametr jest ustawienie podczas aktualizowania ustawień szyfrowania, może to prowadzić do ponownego uruchomienia komputera przed właściwe szyfrowanie. W tym przypadku również można usunąć dysk, na których nie chcesz sformatowany z pliku fstab. Podobnie należy dodać partycję, którą chcesz zaszyfrować sformatowanego pliku fstab przed rozpoczęciem operacji szyfrowania. 

### <a name="bkmk_EFACriteria"> </a> Kryteria EncryptFormatAll
Parametr wykracza jednak wszystkie partycje i szyfruje je, tak długo, jak spełniają **wszystkich** poniższe kryteria: 
- Nie jest partycją rozruchową głównego/OS
- Nie jest już zaszyfrowany
- Nie jest woluminem klucz szyfrowania bloków
- Brak woluminu RAID
- Nie jest woluminem LVM
- Jest zainstalowany

Szyfrowanie dysków, które tworzą woluminu RAID lub LVM zamiast woluminu RAID lub LVM.

### <a name="bkmk_EFAPSH"> </a> Parametr EncryptFormatAll za pomocą wiersza polecenia platformy Azure
Użyj [Włącz az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-enable) polecenie, aby włączyć szyfrowanie na uruchomionej maszyny wirtualnej IaaS na platformie Azure.

-  **Szyfruj przy użyciu EncryptFormatAll uruchomionej maszyny Wirtualnej:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="bkmk_EFAPSH"> </a> Parametr EncryptFormatAll za pomocą polecenia cmdlet programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) z parametrem EncryptFormatAll. 

**Szyfruj przy użyciu EncryptFormatAll uruchomionej maszyny Wirtualnej:** Na przykład poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension z parametrem EncryptFormatAll. Grupa zasobów, maszyny Wirtualnej i magazynu kluczy powinien zostały już utworzone jako warunki wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="bkmk_EFALVM"> </a> Użyj parametru EncryptFormatAll za pomocą Menedżera woluminów logicznych (LVM) 
Zaleca się instalacji LVM-crypt. Dla wszystkich poniższych przykładach Zamień ścieżki urządzenia i punkty instalacji niezależnie od rodzaju pasujące do danego przypadku użycia. Ta konfiguracja może odbywać się w następujący sposób:

- Dodawanie dysków danych, tworzące maszynę Wirtualną.
- Formatowanie, zainstalować i dodać te dyski w pliku fstab.

    1. Sformatuj nowo dodany dysk. Używamy łączy symbolicznych, generowane przez platformę Azure, w tym miejscu. Za pomocą łączy symbolicznych pozwala uniknąć problemów związanych z nazwy urządzenia, zmiany. Aby uzyskać więcej informacji, zobacz [problemów rozwiązać nazwy urządzenia](../virtual-machines/linux/troubleshoot-device-names-problems.md) artykułu.
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Zainstaluj dyski.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Dodaj do fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Uruchom polecenie cmdlet programu PowerShell Set-AzVMDiskEncryptionExtension z parametrem-EncryptFormatAll, aby zaszyfrować te dyski.

         ```azurepowershell-interactive
         $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
         
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
         ```

    5. Skonfiguruj LVM na podstawie tych nowych dysków. Należy pamiętać, że zaszyfrowanych dysków są odblokowane, po zakończeniu maszyny Wirtualnej podczas uruchamiania. Tak instalowania LVM musi także zostać później opóźnione.


## <a name="bkmk_VHDpre"> </a> Nowe maszyny wirtualne IaaS utworzone na podstawie szyfrowane klienta wirtualnego dysku twardego i kluczy szyfrowania
W tym scenariuszu można włączyć szyfrowanie przy użyciu poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. 

Postępuj zgodnie z instrukcjami w dodatku do przygotowania obrazów zaszyfrowane wstępnie, których można użyć na platformie Azure. Po utworzeniu obrazu, do tworzenia zaszyfrowanych maszyn wirtualnych platformy Azure za pomocą kroki opisane w następnej sekcji.

* [Przygotowywanie wirtualnego dysku twardego Windows zaszyfrowane wstępnie](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Przygotowywanie wirtualnego dysku twardego systemu Linux zaszyfrowane wstępnie](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption. Migawkę dysku zarządzanego, może zostać pobrany z portalu lub [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) mogą być używane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania. Po wykonaniu kopii zapasowej można użyć polecenia cmdlet Set-AzVMDiskEncryptionExtension w celu zaszyfrowania dysków zarządzanych przez określenie parametru-skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension zakończy się niepowodzeniem z maszynami wirtualnymi opartymi na dyskach zarządzanych, dopóki nie zostanie utworzona kopia zapasowa i ten parametr został określony. 
>
>Szyfrowanie lub wyłączenie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 



### <a name="bkmk_VHDprePSH"> </a> Szyfrowanie maszyn wirtualnych IaaS z zaszyfrowane wstępnie dyskami VHD za pomocą programu Azure PowerShell 
Szyfrowanie dysków można włączyć na zaszyfrowanym wirtualnym dysku twardym przy użyciu polecenia cmdlet programu PowerShell [Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). W poniższym przykładzie zapewnia niektórych wspólnych parametrów. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włącz szyfrowanie dla dysków nowo dodane dane

Możesz dodać nowy dysk danych, za pomocą [dołączanie dysku maszyny wirtualnej az](../virtual-machines/linux/add-disk.md), lub [za pośrednictwem witryny Azure portal](../virtual-machines/linux/attach-disk-portal.md). Przed można zaszyfrować, należy najpierw zainstalować nowo dołączony dysk danych. Należy zażądać szyfrowania dysku danych, ponieważ dysk będzie można jej używać, gdy szyfrowanie jest w toku. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włącza szyfrowanie na nowo dodany dysk przy użyciu wiersza polecenia platformy Azure

 Jeśli maszyna wirtualna została wcześniej zaszyfrowane za pomocą "All" następnie typ woluminu parametr powinien pozostać wszystkie. Wszystkie obejmuje zarówno systemu operacyjnego i dysków z danymi. Jeśli maszyna wirtualna wcześniej został zaszyfrowany za pomocą typu woluminu "System operacyjny", a następnie typ woluminu parametru powinny być zmieniane dla wszystkich, więc, że zarówno system operacyjny, jak i nowy dysk danych zostaną dołączone. Jeśli maszyna wirtualna została zaszyfrowana przy użyciu tylko typ woluminu "Data", następnie może ona "Dane" jak pokazano poniżej. Dodawanie i dołączanie nowego dysku danych do maszyny Wirtualnej nie jest wystarczające przygotowania do szyfrowania. Nowo dołączony dysk również musi być sformatowany i prawidłowo zainstalowany na maszynie wirtualnej przed włączeniem szyfrowania. W systemie Linux dysk musi być zainstalowana w/etc/fstab za pomocą [nazwy urządzenia trwałego bloku](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

W przeciwieństwie do składni programu Powershell interfejsu wiersza polecenia nie wymaga od użytkownika podczas włączania szyfrowania wersji unikatowy ciąg. Interfejs wiersza polecenia automatycznie generuje i używa własną wartość wersji unikatowy ciąg.

-  **Szyfrowanie woluminów danych uruchomionej maszyny wirtualnej:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Szyfruj ilości danych przy użyciu klucza KEK uruchomionej maszyny wirtualnej:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włącza szyfrowanie na nowo dodany dysk przy użyciu programu Azure PowerShell
 Szyfrowanie nowego dysku dla systemu Linux za pomocą programu Powershell, nowa wersja sekwencji musi być określona. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID wersji sekwencji. Utwórz [migawkę](../virtual-machines/windows/snapshot-copy-managed-disk.md) i/lub wykonaj kopię zapasową maszyny wirtualnej przy użyciu [Azure Backup](../backup/backup-azure-vms-encryption.md) przed zaszyfrowaniem dysków. Parametr-skipVmBackup jest już określony w skryptach programu PowerShell w celu zaszyfrowania nowo dodanego dysku z danymi.
 

-  **Szyfrowanie woluminów danych uruchomionej maszyny wirtualnej:** Poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyny Wirtualnej i magazynu kluczy powinien zostały już utworzone jako warunki wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami. Dopuszczalne wartości dla parametru - VolumeType to wszystkie, systemu operacyjnego i danych. Jeśli maszyna wirtualna wcześniej został zaszyfrowany za pomocą typu woluminu "System operacyjny" lub "All", następnie parametr - VolumeType należy je zmienić wszystkie tak, że zarówno system operacyjny, jak i nowy dysk danych zostaną dołączone.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Szyfruj ilości danych przy użyciu klucza KEK uruchomionej maszyny wirtualnej:** Dopuszczalne wartości dla parametru - VolumeType to wszystkie, systemu operacyjnego i danych. Jeśli maszyna wirtualna wcześniej został zaszyfrowany za pomocą typu woluminu "System operacyjny" lub "All", następnie parametr - VolumeType należy je zmienić wszystkie tak, że zarówno system operacyjny, jak i nowy dysk danych zostaną dołączone.

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Składnia wartości parametru dysk-szyfrowanie-kluczy jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[KVresource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]</br> Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Wyłącz szyfrowanie dla maszyn wirtualnych z systemem Linux
Można wyłączyć szyfrowanie przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub przy użyciu szablonu usługi Resource Manager. 

>[!IMPORTANT]
>Wyłączenie szyfrowania za pomocą usługi Azure Disk Encryption na maszynach wirtualnych z systemem Linux jest obsługiwana tylko dla woluminów danych. Go nie jest obsługiwana na woluminach systemu operacyjnego i danych, jeśli wolumin systemu operacyjnego został zaszyfrowany.  

- **Wyłącz szyfrowanie dysków przy użyciu Azure PowerShell:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Wyłącz szyfrowanie za pomocą interfejsu wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj polecenia [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Wyłącz szyfrowanie za pomocą szablonu usługi Resource Manager:** Aby wyłączyć szyfrowanie, użyj szablonu [wyłącz szyfrowanie w uruchomionym szablonie maszyny wirtualnej systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) .
     1. Kliknij przycisk **Wdrażaj na platformie Azure**.
     2. Wybierz subskrypcję, grupy zasobów, lokalizację maszyny Wirtualnej, postanowienia prawne i umowy.
     3.  Kliknij przycisk **zakupu** umożliwia wyłączenie szyfrowania dysku na uruchomionej maszynie Wirtualnej Windows. 


## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Włączanie usługi Azure Disk Encryption for Windows](azure-security-disk-encryption-windows.md)
