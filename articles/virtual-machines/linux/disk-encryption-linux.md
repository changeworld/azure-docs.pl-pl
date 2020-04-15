---
title: Scenariusze usługi Azure Disk Encryption na maszynach wirtualnych z systemem Linux
description: Ten artykuł zawiera instrukcje dotyczące włączania szyfrowania dysków platformy Microsoft Azure dla maszyn wirtualnych z systemem Linux dla różnych scenariuszy
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 6b60ccc7a635e4b6071b43d7ff75e182aa96cd08
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313623"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Scenariusze usługi Azure Disk Encryption na maszynach wirtualnych z systemem Linux


Usługa Azure Disk Encryption for Linux (VMs) korzysta z funkcji DM-Crypt systemu Linux, aby zapewnić pełne szyfrowanie dysku systemu operacyjnego i dysków z danymi. Ponadto zapewnia szyfrowanie dysku zasobów efemeryczny podczas korzystania z funkcji EncryptFormatAll.

Usługa Azure Disk Encryption jest [zintegrowana z usługą Azure Key Vault,](disk-encryption-key-vault.md) która ułatwia kontrolowanie kluczy szyfrowania dysku i zarządzanie nimi. Aby zapoznać się z omówieniem usługi, zobacz [Szyfrowanie dysków platformy Azure dla maszyn wirtualnych z systemem Linux](disk-encryption-overview.md).

Szyfrowanie dysku można stosować tylko do maszyn wirtualnych [obsługiwanych rozmiarów maszyn wirtualnych i systemów operacyjnych](disk-encryption-overview.md#supported-vms-and-operating-systems). Należy również spełnić następujące wymagania wstępne:

- [Dodatkowe wymagania dotyczące maszyn wirtualnych](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)
- [Wymagania dotyczące przechowywania kluczy szyfrowania](disk-encryption-overview.md#encryption-key-storage-requirements)

We wszystkich przypadkach należy [wykonać migawkę](snapshot-copy-managed-disk.md) i/lub utworzyć kopię zapasową, zanim dyski zostaną zaszyfrowane. Kopie zapasowe zapewniają, że opcja odzyskiwania jest możliwa, jeśli wystąpi nieoczekiwany błąd podczas szyfrowania. Maszyny wirtualne z dyskami zarządzanymi wymagają kopii zapasowej przed wystąpieniem szyfrowania. Po wykonaniu kopii zapasowej można użyć [polecenia cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) do szyfrowania dysków zarządzanych, określając parametr -skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania zaszyfrowanych maszyn wirtualnych, zobacz artykuł [usługi Azure Backup.](../../backup/backup-azure-vms-encryption.md) 

>[!WARNING]
> - Jeśli wcześniej używano szyfrowania dysków platformy Azure z usługą Azure AD do szyfrowania maszyny wirtualnej, należy nadal używać tej opcji do szyfrowania maszyny wirtualnej. Szczegółowe informacje można znaleźć [w witrynie Azure Disk Encryption with Azure AD (previous release).](disk-encryption-overview-aad.md) 
>
> - Podczas szyfrowania woluminów systemu operacyjnego Linux maszyna wirtualna powinna być uważana za niedostępną. Zdecydowanie zalecamy unikanie logowania SSH podczas szyfrowania w toku, aby uniknąć problemów z blokowaniem otwartych plików, które będą musiały być dostępne podczas procesu szyfrowania. Aby sprawdzić postęp, użyj polecenia [cmdlet Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell lub [szyfrowania maszyny wirtualnej pokaż](/cli/azure/vm/encryption#az-vm-encryption-show) polecenia CLI. Można oczekiwać, że ten proces zajmie kilka godzin dla woluminu systemu operacyjnego o pojemności 30 GB, a także dodatkowy czas na szyfrowanie woluminów danych. Czas szyfrowania woluminu danych będzie proporcjonalny do rozmiaru i ilości woluminów danych, chyba że używana jest cała opcja formatu szyfrowania. 
> - Wyłączenie szyfrowania na maszynach wirtualnych z systemem Linux jest obsługiwane tylko dla woluminów danych. Nie jest obsługiwany na woluminach danych lub systemu operacyjnego, jeśli wolumin systemu operacyjnego został zaszyfrowany.  

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i łączenie się z platformą Azure

Szyfrowanie dysków platformy Azure można włączyć i zarządzać za pośrednictwem [interfejsu wiersza polecenia platformy Azure](/cli/azure) i programu Azure [PowerShell.](/powershell/azure/new-azureps-module-az) Aby to zrobić, należy zainstalować narzędzia lokalnie i połączyć się z subskrypcją platformy Azure.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Narzędzie [cli platformy Azure 2.0](/cli/azure) to narzędzie wiersza polecenia do zarządzania zasobami platformy Azure. Cli jest przeznaczony do elastycznego wyszukiwania danych, obsługi długotrwałych operacji jako procesów nieblokujących i ułatwić wykonywanie skryptów. Można go zainstalować lokalnie, wykonując kroki opisane w [install the Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

 

Aby [zalogować się do konta platformy Azure za pomocą interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure,](/cli/azure/authenticate-azure-cli)użyj polecenia [logowania az.](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli
az login
```

Jeśli chcesz wybrać dzierżawcę, aby zalogować się w obszarze, użyj:
    
```azurecli
az login --tenant <tenant>
```

Jeśli masz wiele subskrypcji i chcesz określić konkretną, pobierz listę subskrypcji z [listą kont AZ](/cli/azure/account#az-account-list) i określ z [zestawem kont AZ](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Moduł Azure PowerShell az](/powershell/azure/new-azureps-module-az) zawiera zestaw poleceń cmdlet, które używają modelu [usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md) do zarządzania zasobami platformy Azure. Można go używać w przeglądarce za pomocą [usługi Azure Cloud Shell](../../cloud-shell/overview.md)lub można go zainstalować na komputerze lokalnym, korzystając z instrukcji w module Azure [PowerShell](/powershell/azure/install-az-ps). 

Jeśli masz już zainstalowany lokalnie, upewnij się, że używasz najnowszej wersji sdk azure powershell do konfigurowania szyfrowania dysku Azure. Pobierz najnowszą wersję [programu Azure PowerShell.](https://github.com/Azure/azure-powershell/releases)

Aby [zalogować się do konta platformy Azure za pomocą programu Azure PowerShell,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)użyj polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```powershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji i chcesz je określić, użyj polecenia cmdlet [Get-AzSubscription,](/powershell/module/Az.Accounts/Get-AzSubscription) aby je wyświetlić, a następnie polecenie cmdlet [Set-AzContext:](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Uruchomienie polecenia cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) spowoduje sprawdzenie, czy wybrano poprawną subskrypcję.

Aby potwierdzić, że są zainstalowane polecenia cmdlet szyfrowania dysku Azure, użyj polecenia cmdlet get:To confirm the Azure Disk Encryption cmdlets are installed, use the [Get-command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) commanddlet:
     
```powershell
Get-command *diskencryption*
```
Aby uzyskać więcej informacji, zobacz [Wprowadzenie do programu Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu Menedżera zasobów, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. Jeśli potrzebujesz informacji o schemacie dla rozszerzenia maszyny wirtualnej, zobacz artykuł [rozszerzenia szyfrowania dysków azure dla systemu Linux.](../extensions/azure-disk-enc-linux.md)

>[!IMPORTANT]
 >Jest to obowiązkowe do migawki i/lub tworzenia kopii zapasowych wystąpienia maszyny wirtualnej na dysku zarządzanym poza i przed włączeniem szyfrowania dysku Azure. Migawkę dysku zarządzanego można wykonać z portalu lub za pośrednictwem [usługi Azure Backup.](../../backup/backup-azure-vms-encryption.md) Kopie zapasowe zapewniają, że opcja odzyskiwania jest możliwa w przypadku nieoczekiwanego błędu podczas szyfrowania. Po wykonaniu kopii zapasowej polecenie cmdlet Set-AzVMDiskEncryptionExtension może służyć do szyfrowania dysków zarządzanych przez określenie parametru -skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension zakończy się niepowodzeniem w stosunku do maszyn wirtualnych opartych na dysku zarządzanym, dopóki nie zostanie utworzona kopia zapasowa i ten parametr zostanie określony. 
>
>Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure 

Szyfrowanie dysku można włączyć na zaszyfrowanym dysku VHD, instalując i używając narzędzia wiersza polecenia interfejsu wiersza interfejsu [wiersza interfejsu wiersza interfejsu wiersza interfejsu wiersza interfejsu wiersza platformy Azure.](/cli/azure/?view=azure-cli-latest) Można go również używać w przeglądarce z usługą [Azure Cloud Shell](../../cloud-shell/overview.md) albo można go zainstalować na maszynie lokalnej i używać w dowolnej sesji programu PowerShell. Aby włączyć szyfrowanie na istniejących lub uruchomionych maszynach wirtualnych z systemem Linux na platformie Azure, użyj następujących poleceń interfejsu wiersza polecenia:

Użyj polecenia [włączania szyfrowania az vm,](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure.

- **Szyfrowanie uruchomionej maszyny Wirtualnej:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu funkcji KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Składnią wartości parametru disk-encryption-keyvault jest ciąg pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
Składnią wartości parametru key-encryption-key jest pełny identyfikator URI do narzędzia KEK, jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny Wirtualnej, użyj polecenia [az vm encryption show.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj polecenia [az vm encryption disable.](/cli/azure/vm/encryption#az-vm-encryption-disable) Wyłączenie szyfrowania jest dozwolone tylko na woluminach danych dla maszyn wirtualnych z systemem Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension,](/powershell/module/az.compute/set-azvmdiskencryptionextension) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure. Wykonaj [migawkę](snapshot-copy-managed-disk.md) i/lub wykonaj kopię zapasową maszyny Wirtualnej za pomocą [usługi Azure Backup,](../../backup/backup-azure-vms-encryption.md) zanim dyski zostaną zaszyfrowane. Parametr -skipVmBackup jest już określony w skryptach programu PowerShell w celu zaszyfrowania uruchomionej maszyny wirtualnej z systemem Linux.

-  **Szyfrowanie uruchomionej maszyny Wirtualnej:** Poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyna wirtualna i magazyn kluczy zostały utworzone jako wymagania wstępne. Zastąp MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami. Zmodyfikuj parametr -VolumeType, aby określić, które dyski są szyfrowane.

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
- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu funkcji KEK:** Może być konieczne dodanie parametru -VolumeType, jeśli szyfrujesz dyski danych, a nie dysk systemu operacyjnego. 

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
    > Składnią wartości parametru disk-encryption-keyvault jest ciąg pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnią wartości parametru key-encryption-key jest pełny identyfikator URI do narzędzia KEK, jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny Wirtualnej, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Wyłącz szyfrowanie dysku:** Aby wyłączyć szyfrowanie, użyj polecenia cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Wyłączenie szyfrowania jest dozwolone tylko na woluminach danych dla maszyn wirtualnych z systemem Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux za pomocą szablonu

Szyfrowanie dysku można włączyć na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux na platformie Azure przy użyciu [szablonu Menedżera zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Kliknij pozycję Wdrażanie na **platformie Azure** w szablonie szybki start platformy Azure.

2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, parametry, warunki prawne i umowę. Kliknij **przycisk Utwórz,** aby włączyć szyfrowanie na istniejącej lub uruchomionej maszynie wirtualnej.

W poniższej tabeli wymieniono parametry szablonu Menedżera zasobów dla istniejących lub uruchomionych maszyn wirtualnych:

| Parametr | Opis |
| --- | --- |
| vmName | Nazwa maszyny Wirtualnej do uruchomienia operacji szyfrowania. |
| keyVaultName (nazwa klucza) | Nazwa magazynu kluczy, do których należy przekazać klucz szyfrowania. Można go uzyskać za pomocą `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` polecenia cmdlet `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`lub interfejsu wiersza polecenia platformy Azure.|
| keyVaultResourceGroup | Nazwa grupy zasobów zawierającej magazyn kluczy. |
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza używanego do szyfrowania klucza szyfrowania. Ten parametr jest opcjonalny, jeśli **wybierzesz nokek** na liście rozwijanej UseExistingKek. Jeśli wybierzesz **kek** na liście rozwijanej UseExistingKek, musisz wprowadzić wartość _keyEncryptionKeyURL._ |
| volumeType | Typ woluminu, na który wykonywana jest operacja szyfrowania. Prawidłowe wartości _to OS_, _Dane_i _Wszystkie_. 
| forceUpdateTag (ForceUpdateTag) | Przekaż w unikatowej wartości, takich jak identyfikator GUID za każdym razem, gdy operacja musi być wymuszać. |
| location | Lokalizacja dla wszystkich zasobów. |

Aby uzyskać więcej informacji na temat konfigurowania szablonu szyfrowania dysku maszyny Wirtualnej systemu Linux, zobacz [Szyfrowanie dysków platformy Azure dla systemu Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/azure-disk-enc-linux).

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Korzystanie z funkcji EncryptFormatAll dla dysków danych na maszynach wirtualnych z systemem Linux

**Parametr EncryptFormatAll** skraca czas szyfrowania dysków z danymi systemu Linux. Partycje spełniające określone kryteria zostaną sformatowane (z bieżącym systemem plików), a następnie ponownie zamontowane z powrotem do miejsca, w którym znajdowały się przed wykonaniem polecenia. Jeśli chcesz wykluczyć dysk danych, który spełnia kryteria, można go odinstalować przed uruchomieniem polecenia.

 Po uruchomieniu tego polecenia wszystkie dyski, które zostały wcześniej zamontowane, zostaną sformatowane, a warstwa szyfrowania zostanie uruchomiona na pustym dysku. Po wybraniu tej opcji dysk zasobu efemerycznego dołączony do maszyny Wirtualnej również zostanie zaszyfrowany. Jeśli dysk efemeryczny zostanie zresetowany, zostanie sformatowany i ponownie zaszyfrowany dla maszyny Wirtualnej przez rozwiązanie szyfrowania dysków Azure przy następnej okazji. Gdy dysk zasobów zostanie zaszyfrowany, [agent microsoft azure linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) nie będzie mógł zarządzać dyskiem zasobów i włączyć plik wymiany, ale można ręcznie skonfigurować plik wymiany.

>[!WARNING]
> EncryptFormatAll nie powinny być używane, gdy są potrzebne dane na woluminach danych maszyny Wirtualnej. Dyski można wykluczyć z szyfrowania, odinstalując je. Należy najpierw wypróbować EncryptFormatAll najpierw na testowej maszynie wirtualnej, zrozumieć parametr funkcji i jego implikacje przed wypróbowaniem go na produkcyjnej maszynie wirtualnej. Opcja EncryptFormatAll formatuje dysk danych, a wszystkie dane na nim zostaną utracone. Przed kontynuowaniem sprawdź, czy dyski, które chcesz wykluczyć, są poprawnie odinstalowane. </br></br>
 >Jeśli ustawiasz ten parametr podczas aktualizowania ustawień szyfrowania, może to doprowadzić do ponownego uruchomienia komputera przed faktycznym szyfrowaniem. W takim przypadku należy również usunąć dysk, który nie ma być sformatowany z pliku fstab. Podobnie należy dodać partycję, która ma być sformatowana szyfrowaniem do pliku fstab przed zainicjowaniem operacji szyfrowania. 

### <a name="encryptformatall-criteria"></a>SzyfrowanieFormatWszystkie kryteria
Parametr przechodzi jednak wszystkie partycje i szyfruje je tak długo, jak spełniają **wszystkie** poniższe kryteria: 
- Nie jest to partycja główna/OS/rozruchowa
- Nie jest jeszcze zaszyfrowany
- Nie jest woluminem BEK
- Nie jest woluminem RAID
- Nie jest woluminem LVM
- Jest zamontowany

Zaszyfruj dyski, które tworzą wolumin RAID lub LVM, a nie wolumin RAID lub LVM.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Użyj parametru EncryptFormatAll z platformą Azure CLI
Użyj polecenia [włączania szyfrowania az vm,](/cli/azure/vm/encryption#az-vm-encryption-enable) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure.

-  **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu encryptformatall:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Użyj parametru EncryptFormatAll z poleceniem cmdlet programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) z parametrem EncryptFormatAll. 

**Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu encryptformatall:** Na przykład poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension z parametrem EncryptFormatAll. Grupa zasobów, maszyna wirtualna i magazyn kluczy zostały utworzone jako wymagania wstępne. Zastąp MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Użyj parametru EncryptFormatAll z menedżerem woluminów logicznych (LVM) 
Zalecamy konfigurację LVM-on-crypt. Dla wszystkich poniższych przykładów, zastąp ścieżkę urządzenia i punkty montażowe na to, co pasuje do przypadku użycia. Tę konfigurację można wykonać w następujący sposób:

- Dodaj dyski z danymi, które będą redagować maszynę wirtualną.
- Formatuj, montuj i dodaj te dyski do pliku fstab.

    1. Wybierz standard partycji, utwórz partycję obejmującą cały dysk, a następnie sformatować partycję. Używamy linków symbolicznych generowanych przez platformę Azure w tym miejscu. Używanie dowiązań symbolicznych pozwala uniknąć problemów związanych ze zmianą nazw urządzeń. Aby uzyskać więcej informacji, zobacz [rozwiązywanie problemów z nazwami urządzeń.](troubleshoot-device-names-problems.md)
    
         ```azurepowershell-interactive
         parted /dev/disk/azure/scsi1/lun0 mklabel gpt
         parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
         
         mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
         ```
    
    1. Zamontuj dyski.
         
         `mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint`
    
    1. Dodaj do fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab`
    
    1. Uruchom polecenie cmdlet Programu PowerShell Set-AzVMDiskEncryptionExtension za pomocą polecenia -EncryptFormatAll w celu zaszyfrowania tych dysków.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Skonfiguruj lvm na tych nowych dyskach. Należy pamiętać, że zaszyfrowane dyski są odblokowywały po zakończeniu rozruchu maszyny Wirtualnej. Tak więc montaż LVM będzie musiał być następnie opóźniony.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nowe maszyny wirtualne utworzone na podstawie zaszyfrowanych przez klienta dysków VHD i kluczy szyfrowania
W tym scenariuszu można włączyć szyfrowanie przy użyciu poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. 

Użyj instrukcji w szyfrowaniu usługi Azure Disk te same skrypty do przygotowywania wstępnie zaszyfrowanych obrazów, które mogą być używane na platformie Azure. Po utworzeniu obrazu można użyć kroków w następnej sekcji, aby utworzyć zaszyfrowaną maszynę wirtualną platformy Azure.

* [Przygotowanie wstępnie zaszyfrowanego dysku VHD systemu Linux](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Jest to obowiązkowe do migawki i/lub tworzenia kopii zapasowych wystąpienia maszyny wirtualnej na dysku zarządzanym poza i przed włączeniem szyfrowania dysku Azure. Migawka dysku zarządzanego można wykonać z portalu lub można użyć [usługi Azure Backup.](../../backup/backup-azure-vms-encryption.md) Kopie zapasowe zapewniają, że opcja odzyskiwania jest możliwa w przypadku nieoczekiwanego błędu podczas szyfrowania. Po wykonaniu kopii zapasowej polecenie cmdlet Set-AzVMDiskEncryptionExtension może służyć do szyfrowania dysków zarządzanych przez określenie parametru -skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension zakończy się niepowodzeniem w stosunku do maszyn wirtualnych opartych na dysku zarządzanym, dopóki nie zostanie utworzona kopia zapasowa i ten parametr zostanie określony. 
>
> Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Szyfrowanie maszyn wirtualnych za pomocą wstępnie zaszyfrowanych dysków wirtualnych za pomocą programu Azure PowerShell 
Szyfrowanie dysku można włączyć na zaszyfrowanej dysku VHD za pomocą polecenia cmdlet [Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples)programu PowerShell . Poniższy przykład zawiera kilka typowych parametrów. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włączanie szyfrowania na nowo dodanym dysku danych

Nowy dysk danych można dodać za pomocą [dysku az vm attach](add-disk.md)lub [za pośrednictwem witryny Azure Portal](attach-disk-portal.md). Aby można było szyfrować, należy najpierw zainstalować nowo dołączony dysk danych. Należy zażądać szyfrowania dysku danych, ponieważ dysk będzie bezużyteczny podczas szyfrowania w toku. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włączanie szyfrowania na nowo dodanym dysku za pomocą interfejsu wiersza polecenia platformy Azure

 Jeśli maszyna wirtualna była wcześniej szyfrowana za pomocą "Wszystkie", parametr typu --volume powinien pozostać "Wszystkie". Wszystkie zawierają zarówno dyski systemu operacyjnego, jak i dysków z danymi. Jeśli maszyna wirtualna była wcześniej szyfrowana za pomocą typu woluminu "OS", parametr --volume-type powinien zostać zmieniony na "Wszystkie", tak aby uwzględniono zarówno system operacyjny, jak i nowy dysk danych. Jeśli maszyna wirtualna została zaszyfrowana tylko z typem woluminu "Dane", a następnie może pozostać "Dane", jak pokazano poniżej. Dodawanie i dołączanie nowego dysku danych do maszyny Wirtualnej nie jest wystarczające przygotowanie do szyfrowania. Nowo podłączony dysk musi być również sformatowany i prawidłowo zainstalowany w maszynie wirtualnej przed włączeniem szyfrowania. W systemie Linux dysk musi być zamontowany w /etc/fstab z [trwałą nazwą urządzenia bloku](troubleshoot-device-names-problems.md).  

W przeciwieństwie do składni programu Powershell interfejs wiersza polecenia nie wymaga od użytkownika podania unikatowej wersji sekwencji podczas włączania szyfrowania. Wierszka polecenia automatycznie generuje i używa własnej unikatowej wartości wersji sekwencji.

-  **Szyfruj woluminy danych uruchomionej maszyny Wirtualnej:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Szyfruj woluminy danych uruchomionej maszyny Wirtualnej przy użyciu funkcji KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włączanie szyfrowania na nowo dodanym dysku za pomocą programu Azure PowerShell
 Podczas używania programu Powershell do szyfrowania nowego dysku dla systemu Linux należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID dla wersji sekwencji. Wykonaj [migawkę](snapshot-copy-managed-disk.md) i/lub wykonaj kopię zapasową maszyny Wirtualnej za pomocą [usługi Azure Backup,](../../backup/backup-azure-vms-encryption.md) zanim dyski zostaną zaszyfrowane. Parametr -skipVmBackup jest już określony w skryptach programu PowerShell w celu zaszyfrowania nowo dodanego dysku danych.
 

-  **Szyfruj woluminy danych uruchomionej maszyny Wirtualnej:** Poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyna wirtualna i magazyn kluczy powinny być już utworzone jako wymagania wstępne. Zastąp MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami. Dopuszczalne wartości dla parametru -VolumeType to Wszystkie, SYSTEM OPERACYJNY i Dane. Jeśli maszyna wirtualna była wcześniej szyfrowana za pomocą typu woluminu "OS" lub "Wszystkie", parametr -VolumeType powinien zostać zmieniony na "Wszystkie", tak aby uwzględniono zarówno system operacyjny, jak i nowy dysk danych.

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
- **Szyfruj woluminy danych uruchomionej maszyny Wirtualnej przy użyciu funkcji KEK:** Dopuszczalne wartości dla parametru -VolumeType to Wszystkie, SYSTEM OPERACYJNY i Dane. Jeśli maszyna wirtualna była wcześniej szyfrowana za pomocą typu woluminu "OS" lub "Wszystkie", parametr -VolumeType powinien zostać zmieniony na Wszystkie, tak aby uwzględniono zarówno system operacyjny, jak i nowy dysk danych.

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
    > Składnią wartości parametru disk-encryption-keyvault jest ciąg pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Składnią wartości parametru key-encryption-key jest pełny identyfikator URI do narzędzia KEK, jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Wyłącz szyfrowanie maszyn wirtualnych z systemem Linux
Szyfrowanie można wyłączyć przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu Usługi Resource Manager. 

>[!IMPORTANT]
>Wyłączenie szyfrowania za pomocą szyfrowania dysków azure na maszynach wirtualnych z systemem Linux jest obsługiwane tylko dla woluminów danych. Nie jest obsługiwany na woluminach danych lub systemu operacyjnego, jeśli wolumin systemu operacyjnego został zaszyfrowany.  

- **Wyłącz szyfrowanie dysku za pomocą programu Azure PowerShell:** Aby wyłączyć szyfrowanie, użyj polecenia cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Wyłącz szyfrowanie za pomocą interfejsu wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj polecenia [az vm encryption disable.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Wyłącz szyfrowanie za pomocą szablonu Menedżera zasobów:** Użyj [wyłącz szyfrowania na uruchomionym szablonie maszyny Wirtualnej systemu Linux,](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) aby wyłączyć szyfrowanie.
     1. Kliknij pozycję **Wdrażanie na platformie Azure**.
     2. Wybierz subskrypcję, grupę zasobów, lokalizację, maszynę wirtualną, warunki prawne i umowę.

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Szyfrowanie dysków platformy Azure nie działa w następujących scenariuszach, funkcjach i technologii systemu Linux:

- Szyfrowanie maszyn wirtualnych warstwy podstawowej lub maszyn wirtualnych utworzonych za pomocą klasycznej metody tworzenia maszyn wirtualnych.
- Wyłączenie szyfrowania na dysku systemu operacyjnego lub dysku danych maszyny wirtualnej z systemem Linux, gdy dysk systemu operacyjnego jest szyfrowany.
- Szyfrowanie dysku systemu operacyjnego dla zestawów skalowania maszyn wirtualnych systemu Linux.
- Szyfrowanie obrazów niestandardowych na maszynach wirtualnych z systemem Linux.
- Integracja z lokalnym systemem zarządzania kluczami.
- Usługi Azure Files (udostępniony system plików).
- sieciowego systemu plików (NFS).
- Woluminy dynamiczne.
- Efemeryczne dyski systemu operacyjnego.
- Szyfrowanie systemów plików udostępnionych/rozproszonych, takich jak (ale nie ograniczając się do): DFS, GFS, DRDB i CephFS.
- Przenoszenie zaszyfrowanej maszyny Wirtualnej do innej subskrypcji.
- Zrzut awaryjny jądra (kdump).
- Oracle ACFS (ASM Cluster File System)
- Maszyny wirtualne gen2 (zobacz: [Obsługa maszyn wirtualnych generacji 2 na platformie Azure)](generation-2.md#generation-1-vs-generation-2-capabilities)
- Maszyny wirtualne serii Lsv2 (patrz: [seria Lsv2)](../lsv2-series.md)

## <a name="next-steps"></a>Następne kroki

- [Omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md)
- [Przykładowe skrypty usługi Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Rozwiązywanie problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)
