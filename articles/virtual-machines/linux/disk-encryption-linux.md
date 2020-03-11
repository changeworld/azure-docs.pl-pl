---
title: Scenariusze Azure Disk Encryption na maszynach wirtualnych z systemem Linux
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Linux w różnych scenariuszach
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 19dcfb96f29939fd92f49ba288ddb6d9264e0f9a
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970593"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Scenariusze Azure Disk Encryption na maszynach wirtualnych z systemem Linux

Azure Disk Encryption używa funkcji DM-Crypt systemu Linux w celu zapewnienia szyfrowania woluminów dla systemu operacyjnego i dysków danych maszyn wirtualnych platformy Azure, a ponadto jest zintegrowana z Azure Key Vault, aby pomóc w kontroli kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi. Aby zapoznać się z omówieniem usługi, zobacz [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](disk-encryption-overview.md).

Istnieje wiele scenariuszy szyfrowania dysku, a kroki mogą się różnić w zależności od scenariusza. W poniższych sekcjach opisano scenariusze bardziej szczegółowo dla maszyn wirtualnych z systemem Linux.

Szyfrowanie dysków można stosować tylko do maszyn wirtualnych [obsługiwanych rozmiarów maszyn wirtualnych i systemów operacyjnych](disk-encryption-overview.md#supported-vms-and-operating-systems). Musisz również spełniać następujące wymagania wstępne:

- [Dodatkowe wymagania dotyczące maszyn wirtualnych](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Wymagania dotyczące sieci](disk-encryption-overview.md#networking-requirements)
- [Wymagania dotyczące magazynu kluczy szyfrowania](disk-encryption-overview.md#encryption-key-storage-requirements)

We wszystkich przypadkach należy [wykonać migawkę](snapshot-copy-managed-disk.md) i/lub utworzyć kopię zapasową przed zaszyfrowaniem dysków. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania może wystąpić, jeśli wystąpił nieoczekiwany błąd występuje podczas szyfrowania. Maszyny wirtualne z dyskami zarządzanymi wymagają kopię zapasową przed zaszyfrowaniem. Po wykonaniu kopii zapasowej można użyć [polecenia cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) , aby zaszyfrować dyski zarządzane przez określenie parametru-skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania szyfrowanych maszyn wirtualnych, zobacz artykuł [Azure Backup](../../backup/backup-azure-vms-encryption.md) . 

>[!WARNING]
> - Jeśli usługa Azure AD została wcześniej Azure Disk Encryption użyta w celu zaszyfrowania maszyny wirtualnej, należy użyć tej opcji, aby zaszyfrować maszynę wirtualną. Aby uzyskać szczegółowe informacje, zobacz [Azure Disk Encryption w usłudze Azure AD (w poprzedniej wersji)](disk-encryption-overview-aad.md) . 
>
> - W przypadku szyfrowania woluminów systemu operacyjnego Linux maszyna wirtualna powinna być uważana za niedostępną. Zdecydowanie zalecamy uniknięcie logowania przy użyciu protokołu SSH, gdy szyfrowanie jest w toku, aby uniknąć problemów z blokowaniem otwartych plików, do których konieczne będzie uzyskanie dostępu podczas procesu szyfrowania. Aby sprawdzić postęp, użyj polecenia cmdlet programu PowerShell [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) lub [szyfrowania maszyny wirtualnej](/cli/azure/vm/encryption#az-vm-encryption-show) . Ten proces może potrwać kilka godzin w przypadku woluminu systemu operacyjnego 30 GB, a także dodatkowy czas na szyfrowanie woluminów danych. Czas szyfrowania woluminu danych będzie proporcjonalny do rozmiaru i ilości woluminów danych, chyba że zostanie użyta opcja Szyfruj cały format. 
> - Wyłączenie szyfrowania na maszynach wirtualnych z systemem Linux jest obsługiwana tylko dla woluminów danych. Go nie jest obsługiwana na woluminach systemu operacyjnego i danych, jeśli wolumin systemu operacyjnego został zaszyfrowany.  

## <a name="install-tools-and-connect-to-azure"></a>Instalowanie narzędzi i nawiązywanie połączenia z platformą Azure

Azure Disk Encryption można włączać i zarządzać nimi za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure) i [Azure PowerShell](/powershell/azure/new-azureps-module-az). Aby to zrobić, należy zainstalować narzędzia lokalnie i połączyć się z subskrypcją platformy Azure.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia [platformy azure 2,0](/cli/azure) to narzędzie z wierszem poleceń do zarządzania zasobami platformy Azure. Interfejs wiersza polecenia służy do elastyczne wykonywanie zapytań o dane, obsługi długotrwałych operacji jak nieblokujące procesy i ułatwienia tworzenia skryptów. Możesz zainstalować ją lokalnie, wykonując kroki opisane w temacie [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

 

Aby [zalogować się do konta platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli), użyj polecenia [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Jeśli chcesz wybrać dzierżawę, zaloguj się w obszarze, należy użyć:
    
```azurecli
az login --tenant <tenant>
```

Jeśli masz wiele subskrypcji i chcesz określić konkretną, Pobierz listę subskrypcji za pomocą opcji [AZ Account List](/cli/azure/account#az-account-list) i określ polecenie [AZ Account Set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z interfejsem wiersza polecenia platformy Azure 2,0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az) udostępnia zestaw poleceń cmdlet, które używają modelu [Azure Resource Manager](../../azure-resource-manager/management/overview.md) do zarządzania zasobami platformy Azure. Możesz użyć jej w przeglądarce z [Azure Cloud Shell](../../cloud-shell/overview.md)lub zainstalować ją na komputerze lokalnym, korzystając z instrukcji w temacie [Install the Azure PowerShell module](/powershell/azure/install-az-ps). 

Jeśli masz już zainstalowany lokalnie, upewnij się, że używasz najnowszej wersji programu Azure PowerShell SDK w wersji do konfigurowania usługi Azure Disk Encryption. Pobierz najnowszą wersję [Azure PowerShell wydania](https://github.com/Azure/azure-powershell/releases).

Aby [zalogować się do konta platformy Azure przy użyciu Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0), użyj polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```powershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji i chcesz określić ją, użyj polecenia cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) , aby je wyświetlić, a następnie polecenie cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Uruchomienie polecenia cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) sprawdzi, czy wybrano poprawną subskrypcję.

Aby potwierdzić, że Azure Disk Encryption polecenia cmdlet są zainstalowane, użyj polecenia cmdlet [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) :
     
```powershell
Get-command *diskencryption*
```
Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu usługi Resource Manager, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. Jeśli potrzebujesz informacji o schemacie dla rozszerzenia maszyny wirtualnej, zapoznaj się z artykułem [rozszerzenia Azure Disk Encryption dla systemu Linux](../extensions/azure-disk-enc-linux.md) .

>[!IMPORTANT]
 >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption. Migawkę dysku zarządzanego można pobrać z portalu lub za pośrednictwem [Azure Backup](../../backup/backup-azure-vms-encryption.md). Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania. Po wykonaniu kopii zapasowej można użyć polecenia cmdlet Set-AzVMDiskEncryptionExtension w celu zaszyfrowania dysków zarządzanych przez określenie parametru-skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension zakończy się niepowodzeniem z maszynami wirtualnymi opartymi na dyskach zarządzanych, dopóki nie zostanie utworzona kopia zapasowa i ten parametr został określony. 
>
>Szyfrowanie lub wyłączenie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure 

Szyfrowanie dysków można włączyć na zaszyfrowanym wirtualnym dysku twardym, instalując narzędzie wiersza polecenia [platformy Azure](/cli/azure/?view=azure-cli-latest) i korzystając z niego. Można go również używać w przeglądarce z usługą [Azure Cloud Shell](../../cloud-shell/overview.md) albo można go zainstalować na maszynie lokalnej i używać w dowolnej sesji programu PowerShell. Aby włączyć szyfrowanie na istniejących lub uruchomionych maszynach wirtualnych z systemem Linux na platformie Azure, użyj następujących poleceń interfejsu wiersza polecenia:

Użyj polecenia [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) , aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure.

- **Szyfruj uruchomioną maszynę wirtualną:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Szyfruj uruchomioną maszynę wirtualną przy użyciu KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej, użyj polecenia [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj polecenia [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . Wyłączenie szyfrowania jest dozwolona tylko na woluminach danych dla maszyn wirtualnych systemu Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) , aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure. Utwórz [migawkę](snapshot-copy-managed-disk.md) i/lub wykonaj kopię zapasową maszyny wirtualnej przy użyciu [Azure Backup](../../backup/backup-azure-vms-encryption.md) przed zaszyfrowaniem dysków. Parametr-skipVmBackup jest już określony w skryptach programu PowerShell, aby można było zaszyfrować działającą maszynę wirtualną z systemem Linux.

-  **Szyfruj uruchomioną maszynę wirtualną:** Poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyna wirtualna i Magazyn kluczy zostały utworzone jako wymagania wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami. Zmodyfikuj parametr-Volumetype, aby określić, które dyski są szyfrowane.

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
- **Szyfruj uruchomioną maszynę wirtualną przy użyciu KEK:** Może być konieczne dodanie parametru-Volumetype w przypadku szyfrowania dysków danych, a nie dysku systemu operacyjnego. 

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
    
- **Sprawdź, czy dyski są zaszyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Wyłącz szyfrowanie dysków:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . Wyłączenie szyfrowania jest dozwolona tylko na woluminach danych dla maszyn wirtualnych systemu Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu szablonu

Szyfrowanie dysków można włączyć na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux na platformie Azure przy użyciu [szablonu Menedżer zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Kliknij pozycję **Wdróż na platformie Azure** w szablonie szybkiego startu platformy Azure.

2. Wybieranie subskrypcji, grupy zasobów, lokalizację grupy zasobów, parametry, postanowienia prawne i umowy. Kliknij przycisk **Utwórz** , aby włączyć szyfrowanie na istniejącej lub URUCHOMIONEJ maszynie wirtualnej.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager dla istniejących lub działających maszyn wirtualnych:

| Parametr | Opis |
| --- | --- |
| vmName | Nazwa maszyny Wirtualnej, można uruchomić operacji szyfrowania. |
| keyVaultName | Nazwa magazynu kluczy, do którego ma zostać przekazany klucz szyfrowania. Można to zrobić za pomocą polecenia cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` lub `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`wiersza poleceń platformy Azure.|
| keyVaultResourceGroup | Nazwa grupy zasobów zawierającej Magazyn kluczy. |
|  KeyEncryptionKeyURL | Adres URL klucza szyfrowania klucza używany do szyfrowania klucza szyfrowania. Ten parametr jest opcjonalny w przypadku wybrania opcji **nokek** na liście rozwijanej UseExistingKek. Jeśli wybierzesz pozycję **KEK** na liście rozwijanej UseExistingKek, musisz wprowadzić wartość _keyEncryptionKeyURL_ . |
| VolumeType | Typ operacji szyfrowania odbywa się na wolumin. Prawidłowe wartości to _system operacyjny_, _dane_i _wszystkie_. 
| forceUpdateTag | Za każdym razem, gdy operacja musi być wymuszenie uruchomienia są przekazywane w unikatową wartość, np. identyfikator GUID. |
| resizeOSDisk | Należy partycji systemu operacyjnego można zmienić rozmiaru zajmuje pełny wirtualny dysk twardy systemu przed podziałem woluminu systemowego. |
| location | Lokalizacja dla wszystkich zasobów. |


## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Korzystanie z funkcji EncryptFormatAll w przypadku dysków z danymi na maszynach wirtualnych z systemem Linux

**EncryptFormatAll** parametr skraca czas szyfrowania dysków danych systemu Linux. Partycje spełniające określone kryteria zostaną sformatowane (z bieżącym systemem plików), a następnie ponownie zainstalowane do miejsca, w którym było przed wykonaniem polecenia. Jeśli chcesz wykluczyć dysk danych, która spełnia kryteria, należy go odinstalować przed uruchomieniem polecenia.

 Po uruchomieniu tego polecenia wszystkie zainstalowane wcześniej dyski zostaną sformatowane, a warstwa szyfrowania zostanie uruchomiona na pustym dysku. Ta opcja jest zaznaczona, dysków tymczasowych zasobów dołączonych do maszyny Wirtualnej również być szyfrowana. W przypadku zresetowania efemerycznego dysku zostanie ponownie sformatowany i ponownie szyfrowane dla maszyny Wirtualnej przez rozwiązanie Azure Disk Encryption przy okazji dalej. Po zaszyfrowaniu dysku zasobów [Agent Microsoft Azure systemu Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) nie będzie w stanie zarządzać dyskiem zasobów i włączać pliku wymiany, ale można ręcznie skonfigurować plik wymiany.

>[!WARNING]
> EncryptFormatAll nie powinny być używane, gdy ma wymaganych danych na woluminach danych maszyny Wirtualnej. Aby wykluczyć dyski z szyfrowania, mogą je odinstalowywania. Należy najpierw wypróbować EncryptFormatAll najpierw na testowej maszynie Wirtualnej, parametr funkcji i jej domniemanie, przed podjęciem próby go na maszynie Wirtualnej w środowisku produkcyjnym. Opcja EncryptFormatAll formatuje dysk z danymi i z niego wszystkie dane zostaną utracone. Przed kontynuowaniem sprawdź, czy dyski, które chcesz wykluczyć są prawidłowo odinstalowane. </br></br>
 >Jeśli ten parametr jest ustawienie podczas aktualizowania ustawień szyfrowania, może to prowadzić do ponownego uruchomienia komputera przed właściwe szyfrowanie. W tym przypadku również można usunąć dysk, na których nie chcesz sformatowany z pliku fstab. Podobnie należy dodać partycję, którą chcesz zaszyfrować sformatowanego pliku fstab przed rozpoczęciem operacji szyfrowania. 

### <a name="encryptformatall-criteria"></a>Kryteria EncryptFormatAll
Parametr jest określany przez wszystkie partycje i szyfruje je, o ile spełniają **wszystkie** poniższe kryteria: 
- Nie jest partycją rozruchową głównego/OS
- Nie jest już zaszyfrowany
- Nie jest woluminem klucz szyfrowania bloków
- Brak woluminu RAID
- Nie jest woluminem LVM
- Jest zainstalowany

Szyfrowanie dysków, które tworzą woluminu RAID lub LVM zamiast woluminu RAID lub LVM.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Używanie parametru EncryptFormatAll z interfejsem wiersza polecenia platformy Azure
Użyj polecenia [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) , aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej na platformie Azure.

-  **Szyfruj uruchomioną maszynę wirtualną przy użyciu EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Użyj parametru EncryptFormatAll z poleceniem cmdlet programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) z parametrem EncryptFormatAll. 

**Szyfruj uruchomioną maszynę wirtualną przy użyciu EncryptFormatAll:** Na przykład poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension z parametrem EncryptFormatAll. Grupa zasobów, maszyna wirtualna i Magazyn kluczy zostały utworzone jako wymagania wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Używanie parametru EncryptFormatAll z menedżerem woluminów logicznych (LVM) 
Zaleca się instalacji LVM-crypt. Dla wszystkich poniższych przykładach Zamień ścieżki urządzenia i punkty instalacji niezależnie od rodzaju pasujące do danego przypadku użycia. Ta konfiguracja może odbywać się w następujący sposób:

- Dodawanie dysków danych, tworzące maszynę Wirtualną.
- Formatowanie, zainstalować i dodać te dyski w pliku fstab.

    1. Sformatuj nowo dodany dysk. Używamy łączy symbolicznych, generowane przez platformę Azure, w tym miejscu. Za pomocą łączy symbolicznych pozwala uniknąć problemów związanych z nazwy urządzenia, zmiany. Aby uzyskać więcej informacji, zobacz artykuł [Rozwiązywanie problemów z nazwami urządzeń](troubleshoot-device-names-problems.md) .
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    1. Zainstaluj dyski.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    1. Dodaj do fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    1. Uruchom polecenie cmdlet programu PowerShell Set-AzVMDiskEncryptionExtension z parametrem-EncryptFormatAll, aby zaszyfrować te dyski.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Skonfiguruj LVM na podstawie tych nowych dysków. Należy pamiętać, że zaszyfrowanych dysków są odblokowane, po zakończeniu maszyny Wirtualnej podczas uruchamiania. Tak instalowania LVM musi także zostać później opóźnione.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nowe maszyny wirtualne utworzone z dysku VHD i kluczy szyfrowania szyfrowanych przez klienta
W tym scenariuszu można włączyć szyfrowanie przy użyciu poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. 

Aby przygotować wstępnie zaszyfrowane obrazy, które mogą być używane na platformie Azure, należy wykonać instrukcje zawarte w tym samym skrypcie usługi Azure Disk Encryption. Po utworzeniu obrazu, do tworzenia zaszyfrowanych maszyn wirtualnych platformy Azure za pomocą kroki opisane w następnej sekcji.

* [Przygotowywanie wstępnie zaszyfrowanego wirtualnego dysku twardego systemu Linux](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption. Migawkę dysku zarządzanego można pobrać z portalu lub można użyć [Azure Backup](../../backup/backup-azure-vms-encryption.md) . Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania. Po wykonaniu kopii zapasowej można użyć polecenia cmdlet Set-AzVMDiskEncryptionExtension w celu zaszyfrowania dysków zarządzanych przez określenie parametru-skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension zakończy się niepowodzeniem z maszynami wirtualnymi opartymi na dyskach zarządzanych, dopóki nie zostanie utworzona kopia zapasowa i ten parametr został określony. 
>
> Szyfrowanie lub wyłączenie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Używanie Azure PowerShell do szyfrowania maszyn wirtualnych z wstępnie szyfrowanymi dyskami VHD 
Szyfrowanie dysków można włączyć na zaszyfrowanym wirtualnym dysku twardym przy użyciu polecenia cmdlet programu PowerShell [Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). W poniższym przykładzie zapewnia niektórych wspólnych parametrów. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włącz szyfrowanie dla dysków nowo dodane dane

Nowy dysk danych można dodać przy użyciu polecenia [AZ VM Disk Attach](add-disk.md)lub [za pośrednictwem Azure Portal](attach-disk-portal.md). Przed można zaszyfrować, należy najpierw zainstalować nowo dołączony dysk danych. Należy zażądać szyfrowania dysku danych, ponieważ dysk będzie można jej używać, gdy szyfrowanie jest w toku. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włącza szyfrowanie na nowo dodany dysk przy użyciu wiersza polecenia platformy Azure

 Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu "All", parametr--type Wszystkie obejmuje zarówno systemu operacyjnego i dysków z danymi. Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu typu woluminu "OS", należy zmienić parametr---type na "All", aby uwzględnić zarówno system operacyjny, jak i nowy dysk danych. Jeśli maszyna wirtualna została zaszyfrowana przy użyciu tylko typ woluminu "Data", następnie może ona "Dane" jak pokazano poniżej. Dodawanie i dołączanie nowego dysku danych do maszyny Wirtualnej nie jest wystarczające przygotowania do szyfrowania. Nowo dołączony dysk również musi być sformatowany i prawidłowo zainstalowany na maszynie wirtualnej przed włączeniem szyfrowania. W systemie Linux dysk musi być zainstalowany w/etc/fstab z [trwałą nazwą urządzenia blokowego](troubleshoot-device-names-problems.md).  

W przeciwieństwie do składni programu Powershell interfejsu wiersza polecenia nie wymaga od użytkownika podczas włączania szyfrowania wersji unikatowy ciąg. Interfejs wiersza polecenia automatycznie generuje i używa własną wartość wersji unikatowy ciąg.

-  **Szyfruj woluminy danych uruchomionej maszyny wirtualnej:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Szyfruj woluminy danych uruchomionej maszyny wirtualnej przy użyciu KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włącza szyfrowanie na nowo dodany dysk przy użyciu programu Azure PowerShell
 Szyfrowanie nowego dysku dla systemu Linux za pomocą programu Powershell, nowa wersja sekwencji musi być określona. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID wersji sekwencji. Utwórz [migawkę](snapshot-copy-managed-disk.md) i/lub wykonaj kopię zapasową maszyny wirtualnej przy użyciu [Azure Backup](../../backup/backup-azure-vms-encryption.md) przed zaszyfrowaniem dysków. Parametr-skipVmBackup jest już określony w skryptach programu PowerShell w celu zaszyfrowania nowo dodanego dysku z danymi.
 

-  **Szyfruj woluminy danych uruchomionej maszyny wirtualnej:** Poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyny Wirtualnej i magazynu kluczy powinien zostały już utworzone jako warunki wstępne. Zastąp wartości MyVirtualMachineResourceGroup, MySecureVM i MySecureVault wartościami. Dopuszczalne wartości dla parametru - VolumeType to wszystkie, systemu operacyjnego i danych. Jeśli maszyna wirtualna została wcześniej zaszyfrowana przy użyciu typu woluminu "OS" lub "All", należy zmienić parametr-Volumetype na "All", aby uwzględnić zarówno system operacyjny, jak i nowy dysk danych.

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
- **Szyfruj woluminy danych uruchomionej maszyny wirtualnej przy użyciu KEK:** Dopuszczalne wartości dla parametru-Volumetype to wszystkie, system operacyjny i dane. Jeśli maszyna wirtualna wcześniej został zaszyfrowany za pomocą typu woluminu "System operacyjny" lub "All", następnie parametr - VolumeType należy je zmienić wszystkie tak, że zarówno system operacyjny, jak i nowy dysk danych zostaną dołączone.

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
- **Wyłącz szyfrowanie przy użyciu szablonu Menedżer zasobów:** Aby wyłączyć szyfrowanie, użyj szablonu [wyłącz szyfrowanie w uruchomionym szablonie maszyny wirtualnej systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) .
     1. Kliknij przycisk **Wdrażaj na platformie Azure**.
     2. Wybierz subskrypcję, grupy zasobów, lokalizację maszyny Wirtualnej, postanowienia prawne i umowy.

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Azure Disk Encryption nie działa w następujących scenariuszach, funkcjach i technologiach systemu Linux:

- Szyfrowanie maszyny wirtualnej warstwy podstawowej lub maszyn wirtualnych utworzonych za pomocą metody tworzenia klasycznej maszyny wirtualnej.
- Wyłączanie szyfrowania na dysku systemu operacyjnego lub dysku danych maszyny wirtualnej z systemem Linux podczas szyfrowania dysku systemu operacyjnego.
- Szyfrowanie dysku systemu operacyjnego dla zestawów skalowania maszyn wirtualnych z systemem Linux.
- Szyfrowanie obrazów niestandardowych na maszynach wirtualnych z systemem Linux.
- Integracja z lokalnym systemem zarządzania kluczami.
- Usługa pliki systemu Azure (udostępnionego systemu plików).
- Sieciowy System plików (NFS).
- Woluminy dynamiczne.
- Tymczasowe dyski systemu operacyjnego.
- Szyfrowanie współużytkowanych/rozproszonych systemów plików, takich jak (ale nie ograniczone do): DFS, GFS, DRDB i CephFS.
- Zrzut awaryjny jądra (kdump).

## <a name="next-steps"></a>Następne kroki

- [Przegląd Azure Disk Encryption](disk-encryption-overview.md)
- [Przykładowe skrypty usługi Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Rozwiązywanie problemów z usługą Azure Disk Encryption](disk-encryption-troubleshooting.md)
