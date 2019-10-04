---
title: Włącz Azure Disk Encryption dla maszyn wirtualnych z systemem Windows IaaS
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Windows IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 109c954ab877d0b8d348fc4b9d7de01c19e41344
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958819"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Włącz Azure Disk Encryption dla maszyn wirtualnych z systemem Windows IaaS

Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Windows IaaS. Przed rozpoczęciem korzystania z szyfrowania dysków należy najpierw spełnić [wymagania wstępne Azure Disk Encryption](../security/azure-security-disk-encryption-prerequisites.md). 

Zdecydowanie zalecamy również [utworzenie migawki](../virtual-machines/windows/snapshot-copy-managed-disk.md) i/lub utworzenie kopii zapasowej dysków przed szyfrowaniem. Kopie zapasowe zapewniają opcję odzyskiwania, jeśli podczas szyfrowania wystąpi nieoczekiwany błąd. Maszyny wirtualne z dyskami zarządzanymi wymagają utworzenia kopii zapasowej przed wystąpieniem szyfrowania. Po wykonaniu kopii zapasowej można użyć [polecenia cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) , aby zaszyfrować dyski zarządzane przez określenie parametru-skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania szyfrowanych maszyn wirtualnych, zobacz artykuł [Tworzenie kopii zapasowej i przywracanie zaszyfrowanej maszyny wirtualnej platformy Azure](../backup/backup-azure-vms-encryption.md) .

>[!WARNING]
> - Jeśli w celu zaszyfrowania tej maszyny wirtualnej wcześniej była używana [Azure Disk Encryption z aplikacją usługi Azure AD](azure-security-disk-encryption-prerequisites-aad.md) , musisz użyć tej opcji, aby ZAszyfrować maszynę wirtualną. Nie można użyć [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) dla tej zaszyfrowanej maszyny wirtualnej, ponieważ nie jest to obsługiwany scenariusz, co oznacza, że przełączanie z aplikacji usługi AAD dla tej zaszyfrowanej maszyny wirtualnej nie jest jeszcze obsługiwane. 
> - Azure Disk Encryption wymaga, aby Key Vault i maszyny wirtualne znajdowały się w tym samym regionie. Utwórz i użyj Key Vault, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningWinVM"></a>Włącz szyfrowanie na istniejących lub uruchomionych maszynach wirtualnych z systemem IaaS Windows
Szyfrowanie można włączyć przy użyciu szablonu, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. Jeśli potrzebujesz informacji o schemacie dla rozszerzenia maszyny wirtualnej, zapoznaj się z artykułem [rozszerzenia Azure Disk Encryption dla systemu Windows](../virtual-machines/extensions/azure-disk-enc-windows.md) .

>[!IMPORTANT]
 > Należy wykonać migawkę i/lub utworzyć kopię zapasową wystąpienia maszyny wirtualnej opartej na dyskach zarządzanych poza programem i przed włączeniem Azure Disk Encryption. Migawkę dysku zarządzanego można pobrać z portalu lub można użyć [Azure Backup](../backup/backup-azure-vms-encryption.md) . Kopie zapasowe zapewniają opcję odzyskiwania w przypadku wystąpienia dowolnego nieoczekiwanego błędu podczas szyfrowania. Po wykonaniu kopii zapasowej można użyć polecenia cmdlet Set-AzVMDiskEncryptionExtension w celu zaszyfrowania dysków zarządzanych przez określenie parametru-skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension zakończy się niepowodzeniem z maszynami wirtualnymi opartymi na dyskach zarządzanych, dopóki nie zostanie utworzona kopia zapasowa i ten parametr został określony. 
>
> Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej. 
>

### <a name="bkmk_RunningWinVMPSH"></a>Włącz szyfrowanie na istniejących lub uruchomionych maszynach wirtualnych za pomocą Azure PowerShell 
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) , aby włączyć szyfrowanie na działającej maszynie wirtualnej IaaS na platformie Azure. 

-  **Szyfruj uruchomioną maszynę wirtualną:** Poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyna wirtualna i Magazyn kluczy powinny być już utworzone jako wymagania wstępne. Zastąp wartości MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami.

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
- **Szyfruj uruchomioną maszynę wirtualną przy użyciu KEK:** 

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
   > Składnia wartości parametru "dysk-szyfrowanie-kluczy" jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]</br> Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Wyłącz szyfrowanie dysków:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Wyłączenie szyfrowania dysków danych na maszynie wirtualnej z systemem Windows, gdy szyfrowanie obu dysków systemu operacyjnego i danych nie działa zgodnie z oczekiwaniami. Zamiast tego należy wyłączyć szyfrowanie na wszystkich dyskach.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Włączanie szyfrowania istniejących lub uruchomionych maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure
Użyj polecenia [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) , aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure.

- **Szyfruj uruchomioną maszynę wirtualną:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Szyfruj uruchomioną maszynę wirtualną przy użyciu KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Składnia wartości parametru "dysk-szyfrowanie-kluczy" jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy] </br> Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, użyj polecenia [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj polecenia [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Wyłączenie szyfrowania dysków danych na maszynie wirtualnej z systemem Windows, gdy szyfrowanie obu dysków systemu operacyjnego i danych nie działa zgodnie z oczekiwaniami. Zamiast tego należy wyłączyć szyfrowanie na wszystkich dyskach.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
 
  > [!NOTE]
  >Należy wykonać migawkę i/lub utworzyć kopię zapasową wystąpienia maszyny wirtualnej opartej na dyskach zarządzanych poza programem i przed włączeniem Azure Disk Encryption. Migawkę dysku zarządzanego można pobrać z portalu lub można użyć [Azure Backup](../backup/backup-azure-vms-encryption.md) . Kopie zapasowe zapewniają opcję odzyskiwania w przypadku wystąpienia dowolnego nieoczekiwanego błędu podczas szyfrowania. Po wykonaniu kopii zapasowej można użyć polecenia cmdlet Set-AzVMDiskEncryptionExtension w celu zaszyfrowania dysków zarządzanych przez określenie parametru-skipVmBackup. To polecenie zakończy się niepowodzeniem na maszynach wirtualnych opartych na dyskach zarządzanych, dopóki nie zostanie utworzona kopia zapasowa i ten parametr nie został określony. 
  >
  >Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej. 

### <a name="bkmk_RunningWinVMwRM"> </a>Korzystanie z szablonu Menedżer zasobów

Szyfrowanie dysków można włączyć na istniejących lub uruchomionych maszynach wirtualnych z systemem IaaS na platformie Azure przy użyciu [szablonu Menedżer zasobów, aby zaszyfrować działającą maszynę wirtualną](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)z systemem Windows.


1. W szablonie szybkiego startu platformy Azure kliknij pozycję **Wdróż na platformie Azure**.

2. Wybierz subskrypcję, grupę zasobów, lokalizację, ustawienia, warunki prawne i umowę. Kliknij przycisk **Kup** , aby włączyć szyfrowanie na istniejącej lub URUCHOMIONEJ maszynie wirtualnej IaaS.

W poniższej tabeli wymieniono Menedżer zasobów parametry szablonu dla istniejących lub uruchomionych maszyn wirtualnych:

| Parametr | Opis |
| --- | --- |
| vmName | Nazwa maszyny wirtualnej do uruchomienia operacji szyfrowania. |
| Nazwakluczamagazynu | Nazwa magazynu kluczy, do którego należy przekazać klucz funkcji BitLocker. Można to zrobić za pomocą polecenia cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` lub interfejsu CLI platformy Azure `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Nazwa grupy zasobów zawierającej Magazyn kluczy|
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza w formacie https://@no__t -0keyvault-name&gt;.vault.azure.net/Key/&lt;key-Name @ no__t-3. Jeśli nie chcesz używać elementu KEK, pozostaw to pole puste. |
| liczba woluminów | Typ woluminu, na którym jest wykonywana operacja szyfrowania. Prawidłowe wartości to _system operacyjny_, _dane_i _wszystkie_. 
| forceUpdateTag | Przekazuj unikatową wartość, taką jak identyfikator GUID za każdym razem, gdy operacja musi zostać wymuszona. |
| resizeOSDisk | Należy zmienić rozmiar partycji systemu operacyjnego w celu zajmowania całego wirtualnego dysku twardego systemu operacyjnego przed podziałem woluminu systemowego. |
| location | Lokalizacja dla wszystkich zasobów. |

## <a name="encrypt-virtual-machine-scale-sets"></a>Szyfrowanie zestawów skalowania maszyn wirtualnych

[Zestawy skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/overview.md) umożliwiają tworzenie i Zarządzanie grupą takich samych maszyn wirtualnych o zrównoważonym obciążeniu. Liczba wystąpień maszyn wirtualnych może automatycznie zwiększać lub zmniejszać się w reakcji na zapotrzebowanie lub zdefiniowany harmonogram. Użyj interfejsu wiersza polecenia lub Azure PowerShell, aby zaszyfrować zestawy skalowania maszyn wirtualnych.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Szyfruj zestawy skalowania maszyn wirtualnych za pomocą Azure PowerShell

Użyj polecenia cmdlet [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension) , aby włączyć szyfrowanie w zestawie skalowania maszyn wirtualnych z systemem Windows. Grupy zasobów, zestaw skalowania maszyn wirtualnych i Magazyn kluczy powinny być już utworzone jako wymagania wstępne.

-  **Szyfrowanie uruchomionego zestawu skalowania maszyn wirtualnych**:
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
   > Składnia wartości parametru "dysk-szyfrowanie-kluczy" jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]</br> Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Pobierz stan szyfrowania dla zestawu skalowania maszyn wirtualnych:** Użyj polecenia cmdlet [Get-AzVmssDiskEncryption](/powershell/module/az.compute/get-azvmssdiskencryption) .
    
    ```azurepowershell-interactive
    get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Wyłącz szyfrowanie na zestawie skalowania maszyn wirtualnych**: Użyj polecenia cmdlet [disable-AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) . 

    ```azurepowershell-interactive
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Szyfrowanie zestawów skalowania maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure

Aby włączyć szyfrowanie w zestawie skalowania maszyn wirtualnych z systemem Windows, należy użyć [AZ VMSS Encryption Enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable) . W przypadku ustawienia zasad uaktualniania zestawu skalowania na ręczny należy uruchomić szyfrowanie za pomocą [AZ VMSS Update-Instances](/cli/azure/vmss#az-vmss-update-instances). Grupy zasobów, zestaw skalowania maszyn wirtualnych i Magazyn kluczy powinny być już utworzone jako wymagania wstępne.

-  **Szyfrowanie uruchomionego zestawu skalowania maszyn wirtualnych**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Szyfrowanie uruchomionego zestawu skalowania maszyn wirtualnych za pomocą KEK do zawijania klucza**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
     
   >[!NOTE]
   > Składnia wartości parametru "dysk-szyfrowanie-kluczy" jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]</br> Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

- **Pobierz stan szyfrowania dla zestawu skalowania maszyn wirtualnych:** Użyj [AZ VMSS Encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Wyłącz szyfrowanie na zestawie skalowania maszyn wirtualnych**: Użyj [AZ VMSS Encryption Disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-windows-virtual-machine-scale-sets"></a>Szablony Azure Resource Manager dla zestawów skalowania maszyn wirtualnych z systemem Windows

Aby zaszyfrować lub odszyfrować zestawy skalowania maszyn wirtualnych z systemem Windows, należy użyć Azure Resource Manager szablonów i instrukcji poniżej:

- [Włącz szyfrowanie na zestawie skalowania maszyn wirtualnych z systemem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)
- [Wyłączanie szyfrowania na zestawie skalowania maszyn wirtualnych z systemem Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

     1. Kliknij pozycję **Wdrażanie na platformie Azure**.
     2. Wypełnij pola wymagane, a następnie zaakceptuj warunki i postanowienia.
     3. Kliknij przycisk **Kup** , aby wdrożyć szablon.

## <a name="bkmk_VHDpre"></a> Nowe maszyny wirtualne IaaS utworzone z poziomu dysków VHD szyfrowanych przez klienta i kluczy szyfrowania

W tym scenariuszu można włączyć szyfrowanie przy użyciu poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. 

Aby przygotować wstępnie zaszyfrowane obrazy, które mogą być używane na platformie Azure, należy wykonać instrukcje zawarte w dodatku. Po utworzeniu obrazu możesz wykonać czynności opisane w następnej sekcji, aby utworzyć zaszyfrowaną maszynę wirtualną platformy Azure.

* [Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Windows](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Linux](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Należy wykonać migawkę i/lub utworzyć kopię zapasową wystąpienia maszyny wirtualnej opartej na dyskach zarządzanych poza programem i przed włączeniem Azure Disk Encryption. Migawkę dysku zarządzanego można pobrać z portalu lub można użyć [Azure Backup](../backup/backup-azure-vms-encryption.md) . Kopie zapasowe zapewniają opcję odzyskiwania w przypadku wystąpienia dowolnego nieoczekiwanego błędu podczas szyfrowania. Po wykonaniu kopii zapasowej można użyć polecenia cmdlet Set-AzVMDiskEncryptionExtension w celu zaszyfrowania dysków zarządzanych przez określenie parametru-skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension zakończy się niepowodzeniem z maszynami wirtualnymi opartymi na dyskach zarządzanych, dopóki nie zostanie utworzona kopia zapasowa i ten parametr został określony. 
>
>Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny wirtualnej. 


### <a name="bkmk_VHDprePSH"></a> Szyfruj maszyny wirtualne z wstępnie zaszyfrowanymi dyskami VHD za pomocą Azure PowerShell
Szyfrowanie dysków można włączyć na zaszyfrowanym wirtualnym dysku twardym przy użyciu polecenia cmdlet programu PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). W poniższym przykładzie przedstawiono kilka typowych parametrów. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włącz szyfrowanie na nowo dodanym dysku z danymi
[Nowy dysk można dodać do maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md)lub [Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włącz szyfrowanie na nowo dodanym dysku przy użyciu Azure PowerShell
 W przypadku szyfrowania nowego dysku dla maszyn wirtualnych z systemem Windows przy użyciu programu PowerShell należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID dla wersji sekwencji. W niektórych przypadkach nowo dodany dysk danych może być zaszyfrowany automatycznie przez rozszerzenie Azure Disk Encryption. Autoszyfrowanie jest zwykle wykonywane, gdy maszyna wirtualna zostanie ponownie uruchomiona po przejściu nowego dysku w tryb online. Zwykle jest to spowodowane tym, że dla typu woluminu została określona wartość "All", gdy wcześniej uruchomiono szyfrowanie dysku na maszynie wirtualnej. Jeśli funkcja autoszyfrowania występuje na nowo dodanym dysku z danymi, zalecamy ponowne uruchomienie polecenia cmdlet Set-AzVmDiskEncryptionExtension z nową wersją sekwencji. Jeśli nowy dysk danych jest szyfrowany jako szyfrowany i nie chcesz go zaszyfrować, najpierw Odszyfruj wszystkie dyski, a następnie Zaszyfruj ponownie przy użyciu nowej wersji sekwencji określającej system operacyjny dla typu woluminu. 
  
 

-  **Szyfruj uruchomioną maszynę wirtualną:** Poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyna wirtualna i Magazyn kluczy powinny być już utworzone jako wymagania wstępne. Zastąp wartości MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami. Ten przykład używa "All" dla parametru-Volumetype, który obejmuje woluminy systemu operacyjnego i danych. Jeśli chcesz tylko zaszyfrować wolumin systemu operacyjnego, użyj "OS" dla parametru-Volumetype. 

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
- **Szyfruj uruchomioną maszynę wirtualną przy użyciu KEK:** Ten przykład używa "All" dla parametru-Volumetype, który obejmuje woluminy systemu operacyjnego i danych. Jeśli chcesz tylko zaszyfrować wolumin systemu operacyjnego, użyj "OS" dla parametru-Volumetype.

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
    > Składnia wartości parametru "dysk-szyfrowanie-kluczy" jest pełnym ciągiem identyfikatora:/subscriptions/[Identyfikator subskrypcji-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]</br> Składnia wartości parametru klucz-Encryption-Key to pełny identyfikator URI KEK as in: https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włącz szyfrowanie na nowo dodanym dysku przy użyciu interfejsu wiersza polecenia platformy Azure
 Polecenie interfejsu wiersza polecenia platformy Azure automatycznie udostępni nową wersję sekwencji po uruchomieniu polecenia, aby włączyć szyfrowanie. W przykładzie dla parametru typu woluminu jest stosowana wartość "All". W przypadku szyfrowania dysku systemu operacyjnego może być konieczna zmiana parametru typu woluminu na system operacyjny. W przeciwieństwie do składni programu PowerShell interfejs wiersza polecenia nie wymaga od użytkownika podania unikatowej wersji sekwencji podczas włączania szyfrowania. Interfejs wiersza polecenia automatycznie generuje i używa własną unikatową wartość wersji sekwencji.   

-  **Szyfruj uruchomioną maszynę wirtualną:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Szyfruj uruchomioną maszynę wirtualną przy użyciu KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Wyłącz szyfrowanie
Szyfrowanie można wyłączyć przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu Menedżer zasobów. Wyłączenie szyfrowania dysków danych na maszynie wirtualnej z systemem Windows, gdy szyfrowanie obu dysków systemu operacyjnego i danych nie działa zgodnie z oczekiwaniami. Zamiast tego należy wyłączyć szyfrowanie na wszystkich dyskach.

- **Wyłącz szyfrowanie dysków przy użyciu Azure PowerShell:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Wyłącz szyfrowanie za pomocą interfejsu wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj polecenia [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Wyłącz szyfrowanie przy użyciu szablonu Menedżer zasobów:** 

    1. Kliknij pozycję **Wdróż na platformie Azure** , korzystając z szablonu [wyłącz szyfrowanie dysków na URUCHOMIONYM szablonie maszyny wirtualnej systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) .
    2. Wybierz subskrypcję, grupę zasobów, lokalizację, maszynę wirtualną, typ woluminu, warunki prawne i umowę.
    3.  Kliknij przycisk **Kup** , aby wyłączyć szyfrowanie dysków na działającej maszynie wirtualnej z systemem Windows. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Włączanie Azure Disk Encryption dla systemu Linux](azure-security-disk-encryption-linux.md)
