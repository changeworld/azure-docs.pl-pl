---
title: Szyfrowanie dysków platformy Azure za pomocą maszyn wirtualnych IaaS usługi Azure AD App Linux (poprzednia wersja)
description: Ten artykuł zawiera instrukcje dotyczące włączania szyfrowania dysków platformy Microsoft Azure dla maszyn wirtualnych IaaS systemu Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: ee365d37a957350fa8a68da0f34149d3210d6238
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970604"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Włącz szyfrowanie dysków platformy Azure za pomocą usługi Azure AD na maszynach wirtualnych z systemem Linux (poprzednia wersja)

Nowa wersja szyfrowania dysków platformy Azure eliminuje konieczność dostarczania parametru aplikacji usługi Azure Active Directory (Azure AD) w celu włączenia szyfrowania dysku maszyny Wirtualnej. W nowej wersji nie musisz już podawać poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby uzyskać instrukcje dotyczące włączania szyfrowania dysku maszyn wirtualnych przy użyciu nowej wersji, zobacz [Szyfrowanie dysków platformy Azure dla maszyn wirtualnych systemu Linux](disk-encryption-linux.md). Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal obsługiwane ze składnią usługi AAD.

Można włączyć wiele scenariuszy szyfrowania dysku, a kroki mogą się różnić w zależności od scenariusza. W poniższych sekcjach opisano scenariusze bardziej szczegółowo dla infrastruktury systemu Linux jako usługi (IaaS) maszyny wirtualne. Szyfrowanie dysku można stosować tylko do maszyn wirtualnych [obsługiwanych rozmiarów maszyn wirtualnych i systemów operacyjnych](disk-encryption-overview.md#supported-vms-and-operating-systems). Należy również spełnić następujące wymagania wstępne:

- [Dodatkowe wymagania dotyczące maszyn wirtualnych](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Zasady sieci i grupy](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Wymagania dotyczące przechowywania kluczy szyfrowania](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Zrób [migawkę,](snapshot-copy-managed-disk.md)utwórz kopię zapasową lub obie te możliwości przed zaszyfrowaniem dysków. Kopie zapasowe zapewniają, że opcja odzyskiwania jest możliwa, jeśli wystąpi nieoczekiwany błąd podczas szyfrowania. Maszyny wirtualne z dyskami zarządzanymi wymagają kopii zapasowej przed wystąpieniem szyfrowania. Po wykonaniu kopii zapasowej można użyć polecenia cmdlet Set-AzVMDiskEncryptionExtension do szyfrowania dysków zarządzanych, określając parametr -skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania zaszyfrowanych maszyn wirtualnych, zobacz [Kopia zapasowa usługi Azure](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Jeśli wcześniej używano [szyfrowania dysków platformy Azure za pomocą aplikacji usługi Azure AD](disk-encryption-overview-aad.md) do szyfrowania tej maszyny wirtualnej, należy nadal używać tej opcji do szyfrowania maszyny wirtualnej. Nie można użyć [szyfrowania dysków platformy Azure](disk-encryption-overview.md) na tej zaszyfrowanej maszynie wirtualnej, ponieważ nie jest to obsługiwany scenariusz, co oznacza, że wyłączenie z aplikacji usługi Azure AD dla tej zaszyfrowanej maszyny Wirtualnej nie jest jeszcze obsługiwane.
 > - Aby upewnić się, że wpisy tajne szyfrowania nie przekraczają granic regionalnych, szyfrowanie dysków platformy Azure wymaga magazynu kluczy i maszyn wirtualnych, które mają współuczesażyć w tym samym regionie. Tworzenie i używanie magazynu kluczy, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania.
 > - Podczas szyfrowania woluminów systemu operacyjnego Linux proces może potrwać kilka godzin. To normalne, że woluminy z systemem operacyjnym Linux mogą trwać dłużej niż woluminy danych do szyfrowania.
> - Podczas szyfrowania woluminów systemu operacyjnego Linux maszyny wirtualnej należy uznać za niedostępne. Zdecydowanie zaleca się unikanie logowania SSH podczas szyfrowania w toku, aby uniknąć blokowania otwartych plików, które muszą być dostępne podczas procesu szyfrowania. Aby sprawdzić postęp, użyj polecenia [get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) lub [vm encryption show.](/cli/azure/vm/encryption#az-vm-encryption-show) Można oczekiwać, że ten proces potrwa kilka godzin dla woluminu systemu operacyjnego 30 GB, a także dodatkowy czas szyfrowania woluminów danych. Czas szyfrowania woluminu danych jest proporcjonalny do rozmiaru i ilości woluminów danych, chyba że używana jest cała opcja **formatu szyfrowania.** 
 > - Wyłączenie szyfrowania na maszynach wirtualnych z systemem Linux jest obsługiwane tylko dla woluminów danych. Nie jest obsługiwany na woluminach danych lub systemu operacyjnego, jeśli wolumin systemu operacyjnego został zaszyfrowany. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej IaaS z systemem Linux

W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu usługi Azure Resource Manager, polecenia cmdlet programu PowerShell lub interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure. 

>[!IMPORTANT]
 >Jest to obowiązkowe, aby zrobić migawkę lub kopii zapasowej wystąpienia maszyny wirtualnej opartej na dysku zarządzane poza i przed włączeniem szyfrowania dysku Azure. Możesz wykonać migawkę dysku zarządzanego z witryny Azure portal lub użyć [usługi Azure Backup](../../backup/backup-azure-vms-encryption.md). Kopie zapasowe zapewniają, że opcja odzyskiwania jest możliwa w przypadku nieoczekiwanego błędu podczas szyfrowania. Po wykonaniu kopii zapasowej użyj polecenia cmdlet Set-AzVMDiskEncryptionExtension do szyfrowania dysków zarządzanych, określając parametr -skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension nie powiedzie się w stosunku do zarządzanych maszyn wirtualnych opartych na dysku, dopóki nie zostanie utworzona kopia zapasowa i ten parametr zostanie określony. 
>
>Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure 
Szyfrowanie dysku można włączyć na zaszyfrowanym dysku VHD, instalując i używając narzędzia wiersza polecenia interfejsu wiersza interfejsu [wiersza interfejsu wiersza interfejsu wiersza interfejsu wiersza interfejsu wiersza interfejsu wiersza interfejsu wiersza interfejsu wiersza platformy Azure 2.0.](/cli/azure) Można go również używać w przeglądarce z usługą [Azure Cloud Shell](../../cloud-shell/overview.md) albo można go zainstalować na maszynie lokalnej i używać w dowolnej sesji programu PowerShell. Aby włączyć szyfrowanie na istniejących lub uruchomionych maszynach wirtualnych z systemem IaaS z systemem Linux na platformie Azure, należy użyć następujących poleceń interfejsu wiersza polecenia:

Użyj polecenia [włączania szyfrowania az vm,](/cli/azure/vm/encryption#az-vm-encryption-enable) aby włączyć szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure.

-  **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu klucza tajnego klienta:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu KEK do zawijania klucza tajnego klienta:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > Składnią wartości parametru disk-encryption-keyvault jest ciąg pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br> Składnią wartości parametru key-encryption-key jest pełny identyfikator URI do narzędzia KEK, jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny Wirtualnej IaaS, użyj polecenia [az vm encryption show.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj polecenia [az vm encryption disable.](/cli/azure/vm/encryption#az-vm-encryption-disable) Wyłączenie szyfrowania jest dozwolone tylko na woluminach danych dla maszyn wirtualnych z systemem Linux.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a> Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej z systemem Linux przy użyciu programu PowerShell
Polecenie cmdlet [Set-AzVMMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) umożliwia szyfrowanie na uruchomionej maszynie wirtualnej IaaS na platformie Azure. Wykonaj [migawkę](snapshot-copy-managed-disk.md) lub wykonaj kopię zapasową maszyny Wirtualnej za pomocą [usługi Azure Backup,](../../backup/backup-azure-vms-encryption.md) zanim dyski zostaną zaszyfrowane. Parametr -skipVmBackup jest już określony w skryptach programu PowerShell w celu zaszyfrowania uruchomionej maszyny wirtualnej z systemem Linux.

- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu klucza tajnego klienta:** Poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyna wirtualna, magazyn kluczy, aplikacja usługi Azure AD i klucz tajny klienta powinny już zostać utworzone jako wymagania wstępne. Zastąp MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID i My-AAD-client-secret swoimi wartościami. Zmodyfikuj parametr -VolumeType, aby określić, które dyski są szyfrowane.

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu KEK do zawijania klucza tajnego klienta:** Usługa Azure Disk Encryption umożliwia określenie istniejącego klucza w magazynie kluczy w celu zawijania kluczy tajnych szyfrowania dysku, które zostały wygenerowane podczas włączania szyfrowania. Po określeniu klucza szyfrowania klucza szyfrowanie platformy Azure Disk Encryption używa tego klucza do zawijania wpisów tajnych szyfrowania przed zapisaniem do magazynu kluczy. Zmodyfikuj parametr -VolumeType, aby określić, które dyski są szyfrowane. 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > Składnią wartości parametru disk-encryption-keyvault jest ciąg pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  Składnią wartości parametru key-encryption-key jest pełny identyfikator URI do narzędzia KEK, jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny Wirtualnej IaaS, użyj polecenia cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Wyłącz szyfrowanie dysku:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [Disable-AzureRmVMIskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) Wyłączenie szyfrowania jest dozwolone tylko na woluminach danych dla maszyn wirtualnych z systemem Linux.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a> Włączanie szyfrowania na istniejącej lub uruchomionej maszynie wirtualnej IaaS z systemem Linux za pomocą szablonu

Szyfrowanie dysku można włączyć na istniejącej lub uruchomionej maszynie wirtualnej systemu IaaS z systemem Linux na platformie Azure przy użyciu [szablonu Menedżera zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Wybierz pozycję Wdrażanie na **platformie Azure** w szablonie szybki start platformy Azure.

2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, parametry, warunki prawne i umowę. Wybierz **pozycję Utwórz,** aby włączyć szyfrowanie na istniejącej lub uruchomionej maszynie wirtualnej IaaS.

W poniższej tabeli wymieniono parametry szablonu Menedżera zasobów dla istniejących lub uruchomionych maszyn wirtualnych, które używają identyfikatora klienta usługi Azure AD:

| Parametr | Opis |
| --- | --- |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, który ma uprawnienia do zapisywania wpisów tajnych do magazynu kluczy. |
| Protokół AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisywania wpisów tajnych do magazynu kluczy. |
| keyVaultName (nazwa klucza) | Nazwa magazynu kluczy, do których należy przekazać klucz. Można go uzyskać za pomocą `az keyvault show --name "MySecureVault" --query KVresourceGroup`polecenia interfejsu wiersza polecenia platformy Azure . |
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza używanego do szyfrowania wygenerowanego klucza. Ten parametr jest opcjonalny, jeśli **wybierzesz nokek** na liście rozwijanej **UseExistingKek.** Jeśli wybierzesz **kek** na liście rozwijanej **UseExistingKek,** musisz wprowadzić wartość _keyEncryptionKeyURL._ |
| volumeType | Typ woluminu, na który wykonywana jest operacja szyfrowania. Prawidłowe obsługiwane wartości _to system operacyjny_ lub _wszystkie_. (Zobacz obsługiwane dystrybucje systemu Linux i ich wersje dla systemów operacyjnych i dysków z danymi w sekcji wymagania wstępne wcześniej.) |
| sequenceVersion (wersja sekwencji) | Sekwencja wersji operacji funkcji BitLocker. Przyrost ten numer wersji za każdym razem, gdy operacja szyfrowania dysku jest wykonywana na tej samej maszynie wirtualnej. |
| vmName | Nazwa maszyny Wirtualnej, na których ma być wykonywana operacja szyfrowania. |
| hasło | Wpisz silne hasło jako klucz szyfrowania danych. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Korzystanie z funkcji EncryptFormatAll dla dysków danych na maszynach wirtualnych IaaS systemu Linux
Parametr EncryptFormatAll skraca czas szyfrowania dysków z danymi systemu Linux. Partycje spełniające określone kryteria są sformatowane (z ich bieżącym systemem plików). Następnie są one ponownie montowane z powrotem do miejsca, w którym były przed wykonaniem polecenia. Jeśli chcesz wykluczyć dysk danych, który spełnia kryteria, można go odinstalować przed uruchomieniem polecenia.

 Po uruchomieniu tego polecenia wszystkie dyski, które zostały wcześniej zamontowane, są sformatowane. Następnie warstwa szyfrowania rozpoczyna się na pustym dysku. Gdy ta opcja jest zaznaczona, tymczasowy dysk zasobów dołączony do maszyny Wirtualnej jest również szyfrowany. Jeśli dysk efemeryczny zostanie zresetowany, jest sformatowany i ponownie zaszyfrowany dla maszyny Wirtualnej przez rozwiązanie szyfrowania dysków azure przy następnej okazji.

>[!WARNING]
> EncryptFormatAll nie powinny być używane, gdy są potrzebne dane na woluminach danych maszyny Wirtualnej. Dyski można wykluczyć z szyfrowania, odinstalując je. Wypróbuj EncryptFormatAll parametr na maszynie wirtualnej testu pierwszy zrozumieć parametr funkcji i jego implikacje przed wypróbowaniem go na maszynie wirtualnej produkcji. Opcja EncryptFormatAll formatuje dysk danych, dzięki czemu wszystkie dane na nim zostaną utracone. Przed kontynuowaniem sprawdź, czy wszystkie dyski, które chcesz wykluczyć, są poprawnie odinstalowane. </br></br>
 >Jeśli ten parametr zostanie ustawiony podczas aktualizowania ustawień szyfrowania, może to doprowadzić do ponownego uruchomienia komputera przed faktycznym szyfrowaniem. W takim przypadku chcesz również usunąć dysk, który nie ma być sformatowany z pliku fstab. Podobnie należy dodać partycję, która ma być sformatowana szyfrowaniem do pliku fstab przed zainicjowaniem operacji szyfrowania. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> SzyfrowanieFormatWszystkie kryteria
Parametr przechodzi przez wszystkie partycje i szyfruje je tak długo, jak spełniają one *wszystkie* następujące kryteria: 
- Nie jest to partycja główna/OS/rozruchowa
- Nie jest jeszcze zaszyfrowany
- Nie jest woluminem BEK
- Nie jest woluminem RAID
- Nie jest woluminem LVM
- Jest zamontowany

Zaszyfruj dyski, które tworzą wolumin RAID lub LVM, a nie wolumin RAID lub LVM.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> Użyj parametru EncryptFormatAll z szablonem
Aby użyć opcji EncryptFormatAll, użyj dowolnego istniejącego wcześniej szablonu usługi Azure Resource Manager, który szyfruje maszynę wirtualną systemu Linux i zmień pole **EncryptionOperation** dla zasobu AzureDiskEncryption.

1. Na przykład użyj [szablonu Menedżera zasobów, aby zaszyfrować działającą maszynę wirtualną IaaS systemu Linux](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Wybierz pozycję Wdrażanie na **platformie Azure** w szablonie szybki start platformy Azure.
3. Zmień pole **EncryptionOperation** z **EnableEncryption** na **EnableEncryptionFormatAl**.
4. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, inne parametry, warunki prawne i umowę. Wybierz **pozycję Utwórz,** aby włączyć szyfrowanie na istniejącej lub uruchomionej maszynie wirtualnej IaaS.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> Użyj parametru EncryptFormatAll z poleceniem cmdlet programu PowerShell
Użyj polecenia cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) z parametrem EncryptFormatAll.

**Szyfruj uruchomionej maszyny Wirtualnej przy użyciu klucza tajnego klienta i EncryptFormatAll:** Na przykład poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension z parametrem EncryptFormatAll. Grupa zasobów, maszyna wirtualna, magazyn kluczy, aplikacja usługi Azure AD i klucz tajny klienta powinny już zostać utworzone jako wymagania wstępne. Zastąp MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID i My-AAD-client-secret swoimi wartościami.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a> Użyj parametru EncryptFormatAll z menedżerem woluminów logicznych (LVM) 
Zalecamy konfigurację LVM-on-crypt. Dla wszystkich poniższych przykładów, zastąp ścieżkę urządzenia i punkty montażowe na to, co pasuje do przypadku użycia. Tę konfigurację można wykonać w następujący sposób:

- Dodaj dyski z danymi, które będą redagować maszynę wirtualną.
- Formatuj, montuj i dodaj te dyski do pliku fstab.

    1. Sformatować nowo dodany dysk. Używamy linków symbolicznych generowanych przez platformę Azure w tym miejscu. Używanie dowiązań symbolicznych pozwala uniknąć problemów związanych ze zmianą nazw urządzeń. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z nazwami urządzeń](troubleshoot-device-names-problems.md).
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. Zamontuj dyski.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. Dodaj do fstab.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. Uruchom polecenie cmdlet Programu PowerShell Set-AzVMDiskEncryptionExtension za pomocą polecenia -EncryptFormatAll w celu zaszyfrowania tych dysków.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. Skonfiguruj lvm na tych nowych dyskach. Należy pamiętać, że zaszyfrowane dyski są odblokowywały po zakończeniu rozruchu maszyny Wirtualnej. Tak więc montaż LVM będzie musiał być następnie opóźniony.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a> Nowe maszyny wirtualne IaaS utworzone na podstawie zaszyfrowanych przez klienta kluczy VHD i szyfrowania
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu Menedżera zasobów, poleceń cmdlet programu PowerShell lub poleceń interfejsu wiersza polecenia. W poniższych sekcjach opisano bardziej szczegółowo szablon Menedżera zasobów i polecenia interfejsu wiersza polecenia. 

Użyj instrukcji zawartych w dodatku do przygotowywania wstępnie zaszyfrowanych obrazów, które mogą być używane na platformie Azure. Po utworzeniu obrazu można użyć kroków w następnej sekcji, aby utworzyć zaszyfrowaną maszynę wirtualną platformy Azure.

* [Przygotowanie wstępnie zaszyfrowanego dysku VHD systemu Linux](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Jest to obowiązkowe, aby zrobić migawkę lub kopii zapasowej wystąpienia maszyny wirtualnej opartej na dysku zarządzane poza i przed włączeniem szyfrowania dysku Azure. Możesz wykonać migawkę dysku zarządzanego z portalu lub użyć [usługi Azure Backup](../../backup/backup-azure-vms-encryption.md). Kopie zapasowe zapewniają, że opcja odzyskiwania jest możliwa w przypadku nieoczekiwanego błędu podczas szyfrowania. Po wykonaniu kopii zapasowej użyj polecenia cmdlet Set-AzVMDiskEncryptionExtension do szyfrowania dysków zarządzanych, określając parametr -skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension nie powiedzie się w stosunku do zarządzanych maszyn wirtualnych opartych na dysku, dopóki nie zostanie utworzona kopia zapasowa i ten parametr zostanie określony. 
>
>Szyfrowanie lub wyłączanie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> Szyfrowanie maszyn wirtualnych IaaS za pomocą wstępnie zaszyfrowanych dysków wirtualnych za pomocą programu Azure PowerShell 
Szyfrowanie dysku można włączyć na zaszyfrowanej dysku VHD za pomocą polecenia cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples)programu PowerShell . Poniższy przykład zawiera kilka typowych parametrów. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włączanie szyfrowania na nowo dodanym dysku danych
Nowy dysk danych można dodać za pomocą [dysku az vm attach](add-disk.md) lub [za pośrednictwem witryny Azure Portal](attach-disk-portal.md). Aby można było szyfrować, należy najpierw zainstalować nowo dołączony dysk danych. Należy zażądać szyfrowania dysku danych, ponieważ dysk będzie bezużyteczny podczas szyfrowania w toku. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Włączanie szyfrowania na nowo dodanym dysku za pomocą interfejsu wiersza polecenia platformy Azure
 Jeśli maszyna wirtualna została wcześniej zaszyfrowana za pomocą "Wszystkie", parametr typu --volume powinien pozostać all. Wszystkie zawierają zarówno dyski systemu operacyjnego, jak i dysków z danymi. Jeśli maszyna wirtualna była wcześniej szyfrowana za pomocą typu woluminu "OS", parametr --volume-type powinien zostać zmieniony na Wszystkie, tak aby uwzględniono zarówno system operacyjny, jak i nowy dysk danych. Jeśli maszyna wirtualna została zaszyfrowana tylko z typem woluminu "Dane", a następnie może pozostać dane, jak pokazano w tym miejscu. Dodawanie i dołączanie nowego dysku danych do maszyny Wirtualnej nie jest wystarczające przygotowanie do szyfrowania. Nowo podłączony dysk musi być również sformatowany i prawidłowo zainstalowany w maszynie wirtualnej przed włączeniem szyfrowania. W systemie Linux dysk musi być zamontowany w /etc/fstab z [trwałą nazwą urządzenia bloku](troubleshoot-device-names-problems.md). 

W przeciwieństwie do składni programu Powershell, wiersz polecenia nie wymaga podania unikatowej wersji sekwencji po włączeniu szyfrowania. Wierszka polecenia automatycznie generuje i używa własnej unikatowej wartości wersji sekwencji.

-  **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu klucza tajnego klienta:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu KEK do zawijania klucza tajnego klienta:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włączanie szyfrowania na nowo dodanym dysku za pomocą programu Azure PowerShell
 Podczas używania programu Powershell do szyfrowania nowego dysku dla systemu Linux należy określić nową wersję sekwencji. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID dla wersji sekwencji. 
 

-  **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu klucza tajnego klienta:** Poniższy skrypt inicjuje zmienne i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupa zasobów, maszyna wirtualna, magazyn kluczy, aplikacja usługi Azure AD i klucz tajny klienta powinny już zostać utworzone jako wymagania wstępne. Zastąp MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID i My-AAD-client-secret swoimi wartościami. Parametr -VolumeType jest ustawiony na dyski danych, a nie na dysk systemu operacyjnego. Jeśli maszyna wirtualna była wcześniej szyfrowana za pomocą typu woluminu "OS" lub "Wszystkie", parametr -VolumeType powinien zostać zmieniony na Wszystkie, tak aby uwzględniono zarówno system operacyjny, jak i nowy dysk danych.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup'; 
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **Szyfrowanie uruchomionej maszyny Wirtualnej przy użyciu KEK do zawijania klucza tajnego klienta:** Usługa Azure Disk Encryption umożliwia określenie istniejącego klucza w magazynie kluczy w celu zawijania kluczy tajnych szyfrowania dysku, które zostały wygenerowane podczas włączania szyfrowania. Po określeniu klucza szyfrowania klucza szyfrowanie platformy Azure Disk Encryption używa tego klucza do zawijania wpisów tajnych szyfrowania przed zapisaniem do magazynu kluczy. Parametr -VolumeType jest ustawiony na dyski danych, a nie na dysk systemu operacyjnego. Jeśli maszyna wirtualna była wcześniej szyfrowana za pomocą typu woluminu "OS" lub "Wszystkie", parametr -VolumeType powinien zostać zmieniony na Wszystkie, tak aby uwzględniono zarówno system operacyjny, jak i nowy dysk danych.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $vmName = 'MyExtraSecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> Składnią wartości parametru disk-encryption-keyvault jest ciąg pełnego identyfikatora: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]. </br> </br>
Składnią wartości parametru key-encryption-key jest pełny identyfikator URI do narzędzia KEK, jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Wyłącz szyfrowanie maszyn wirtualnych z systemem Linux
Szyfrowanie można wyłączyć przy użyciu usługi Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu Menedżera zasobów. 

>[!IMPORTANT]
>Wyłączenie szyfrowania za pomocą szyfrowania dysków azure na maszynach wirtualnych z systemem Linux jest obsługiwane tylko dla woluminów danych. Nie jest obsługiwany na woluminach danych lub systemu operacyjnego, jeśli wolumin systemu operacyjnego został zaszyfrowany. 

- **Wyłącz szyfrowanie dysku za pomocą programu Azure PowerShell:** Aby wyłączyć szyfrowanie, należy użyć polecenia cmdlet [Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Wyłącz szyfrowanie za pomocą interfejsu wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj polecenia [az vm encryption disable.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Wyłącz szyfrowanie za pomocą szablonu Menedżera zasobów:** Aby wyłączyć szyfrowanie, użyj [wyłącz szyfrowania na uruchomionym szablonie maszyny Wirtualnej systemu Linux.](https://aka.ms/decrypt-linuxvm)
     1. Wybierz **pozycję Wdrażanie na platformie Azure**.
     2. Wybierz subskrypcję, grupę zasobów, lokalizację, maszynę wirtualną, warunki prawne i umowę.
     3. Wybierz **opcję Zakup,** aby wyłączyć szyfrowanie dysku na uruchomionej maszynie Wirtualnej systemu Windows. 


## <a name="next-steps"></a>Następne kroki

- [Omówienie szyfrowania dysków platformy Azure dla systemu Linux](disk-encryption-overview-aad.md)
- [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja)](disk-encryption-key-vault-aad.md)
