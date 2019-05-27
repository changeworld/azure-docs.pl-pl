---
title: Usługa Azure Disk Encryption przy użyciu maszyn wirtualnych IaaS z systemem Linux aplikacji usługi Azure AD (poprzedniej wersji)
description: Ten artykuł zawiera instrukcje na temat włączania systemu Microsoft Azure Disk Encryption dla maszyn wirtualnych IaaS z systemem Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 1e535ed92305d124499fd0ce9933b7edd19df32e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66118092"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms-previous-release"></a>Włączanie usługi Azure Disk Encryption dla maszyn wirtualnych systemu Linux IaaS (poprzedniej wersji)

**Nowa wersja usługi Azure Disk Encryption eliminuje wymaganie zapewniające parametrem aplikacji usługi Azure AD, aby włączyć szyfrowanie dysku maszyny Wirtualnej. Za pomocą nowej wersji jest już wymagane podanie poświadczeń usługi Azure AD podczas wykonywania kroku Włączanie szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje, aby włączyć szyfrowanie dysku maszyny Wirtualnej przy użyciu nowej wersji, zobacz [usługi Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](azure-security-disk-encryption-linux.md). Maszyny wirtualne, które już zostały zaszyfrowane za pomocą aplikacji usługi Azure AD, parametry są nadal obsługiwane i powinno być kontynuowane przy użyciu składni usługi AAD.**

Można włączyć wiele scenariuszy szyfrowania dysku i kroki zależą od scenariusza. W poniższych częściach omówiono scenariuszach szczegółowo dla maszyn wirtualnych IaaS z systemem Linux. Zanim będzie można użyć szyfrowania dysku [wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) należy wykonać i [dodatkowe wymagania wstępne dotyczące maszyn wirtualnych IaaS z systemem Linux](azure-security-disk-encryption-prerequisites-aad.md#bkmk_LinuxPrereq) powinna zostać przejrzana pod sekcji.

Wykonaj [migawki](../virtual-machines/windows/snapshot-copy-managed-disk.md) i/lub wykonać kopię zapasową przed dyski są szyfrowane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania może wystąpić, jeśli wystąpił nieoczekiwany błąd występuje podczas szyfrowania. Maszyny wirtualne z dyskami zarządzanymi wymagają kopię zapasową przed zaszyfrowaniem. Po nawiązaniu kopia zapasowa służy polecenie cmdlet Set-AzVMDiskEncryptionExtension do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych, zobacz [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) artykułu. 

>[!WARNING]
 > - Jeśli wcześniej używano [usługi Azure Disk Encryption przy użyciu aplikacji Azure AD](azure-security-disk-encryption-prerequisites-aad.md) do zaszyfrowania tej maszyny Wirtualnej, konieczne będzie kontynuować ta opcja służy do szyfrowania maszyny Wirtualnej. Nie można użyć [usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) na tej zaszyfrowanej maszyny Wirtualnej, ponieważ nie jest to obsługiwany scenariusz znaczenie przełączania się aplikacja usługi AAD dla to zaszyfrowanych maszyn wirtualnych nie jest jeszcze obsługiwane.
 > - Upewnij się, że klucze tajne szyfrowania nie przekracza granic regionalnych, usługa Azure Disk Encryption musi znajdować się w tym samym regionie usługi Key Vault i maszyn wirtualnych. Tworzenie i używanie usługi Key Vault, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania.
 > - W przypadku szyfrowania woluminów systemu operacyjnego Linux, proces może potrwać kilka godzin. Jest to normalny trwać dłużej niż woluminów danych na potrzeby szyfrowania woluminów systemu operacyjnego Linux.
> - W przypadku szyfrowania woluminów systemu operacyjnego Linux, maszyny Wirtualnej powinna być uznawana za niedostępną. Zdecydowanie zaleca się unikać logowania do protokołu SSH, gdy szyfrowanie jest w toku, aby uniknąć problemów z blokowaniem wszystkie otwarte pliki, które muszą uzyskać dostęp podczas procesu szyfrowania. Aby sprawdzić postęp, [Get AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) lub [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) polecenia mogą być używane. Ten proces można spodziewać się zająć kilka godzin, 30GB woluminu systemu operacyjnego, a także dodatkowego czasu na szyfrowanie woluminów danych. Czas szyfrowania woluminu danych będzie proporcjonalny do rozmiaru, a ilość ilości danych, chyba że Szyfruj formatowania all-opcja jest używana. 
 > - Wyłączenie szyfrowania na maszynach wirtualnych z systemem Linux jest obsługiwana tylko dla woluminów danych. Go nie jest obsługiwana na woluminach systemu operacyjnego i danych, jeśli wolumin systemu operacyjnego został zaszyfrowany.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningLinux"> </a> Włącz szyfrowanie dla istniejących lub uruchamianie systemu Linux Maszynie wirtualnej IaaS

W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu usługi Resource Manager, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. 

>[!IMPORTANT]
 >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption. Migawkę dysku zarządzanego, może zostać pobrany z portalu lub [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) mogą być używane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania. Po nawiązaniu kopii zapasowej polecenia cmdlet Set-AzVMDiskEncryptionExtension może służyć do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension zakończy się niepowodzeniem dla maszyn wirtualnych dysku zarządzanego na podstawie, dopóki nie wykonano kopii zapasowej, a ten parametr został określony. 
>
>Szyfrowanie lub wyłączenie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Włącz szyfrowanie dla istniejących lub uruchomionej maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure 
Można włączyć szyfrowanie dysków na zaszyfrowane wirtualnego dysku twardego przy instalowaniu i używaniu [interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure) narzędzie wiersza polecenia. Można go również używać w przeglądarce z usługą [Azure Cloud Shell](../cloud-shell/overview.md) albo można go zainstalować na maszynie lokalnej i używać w dowolnej sesji programu PowerShell. Aby włączyć szyfrowanie na istniejące lub działających maszyn wirtualnych systemu Linux IaaS na platformie Azure, użyj następujących poleceń interfejsu wiersza polecenia:

Użyj [Włącz az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-enable) polecenie, aby włączyć szyfrowanie na uruchomionej maszyny wirtualnej IaaS na platformie Azure.

-  **Szyfruj uruchomionej maszyny Wirtualnej za pomocą klucza tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Szyfruj uruchomionej maszyny Wirtualnej przy użyciu klucza KEK zawijania wpisu tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, należy użyć [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) polecenia. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Wyłącz szyfrowanie:** Aby wyłączyć szyfrowanie, użyj [az vm encryption, wyłącz](/cli/azure/vm/encryption#az-vm-encryption-disable) polecenia. Wyłączenie szyfrowania jest dozwolona tylko na woluminach danych dla maszyn wirtualnych systemu Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Włącz szyfrowanie dla istniejących lub uruchomionej maszyny Wirtualnej systemu Linux przy użyciu programu PowerShell
Użyj [AzVMDiskEncryptionExtension zestaw](/powershell/module/az.compute/set-azvmdiskencryptionextension) polecenia cmdlet, aby włączyć szyfrowanie na uruchomionej maszyny wirtualnej IaaS na platformie Azure. Wykonaj [migawki](../virtual-machines/windows/snapshot-copy-managed-disk.md) i/lub utworzyć kopię zapasową maszyny Wirtualnej przy użyciu [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) przed dyski są szyfrowane. Parametr - skipVmBackup jest już określony w skryptach programu PowerShell do zaszyfrowania uruchomionej maszyny Wirtualnej systemu Linux.

- **Szyfruj uruchomionej maszyny Wirtualnej za pomocą klucza tajnego klienta:** Poniższy skrypt inicjuje zmiennych i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupy zasobów, maszyna wirtualna, magazyn kluczy, aplikacji usługi AAD i klucz tajny klienta powinny zostały już utworzone jako warunki wstępne. Zamień MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM MySecureVault, My-AAD-client-ID, a My-AAD-client-secret własnymi wartościami. Zmodyfikuj parametr - VolumeType, aby określić, które dyski, na którą Szyfrujesz.

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
- **Szyfruj uruchomionej maszyny Wirtualnej przy użyciu klucza KEK zawijania wpisu tajnego klienta:** Usługa Azure Disk Encryption umożliwia określenie istniejącego klucza w magazynie kluczy na zawijanie kluczy tajnych szyfrowania dysków, które zostały wygenerowane podczas włączania szyfrowania. Jeśli klucz szyfrowania jest określony, usługi Azure Disk Encryption używa tego klucza do opakowania wpisów tajnych szyfrowania przed zapisaniem w usłudze Key Vault. Zmodyfikuj parametr - VolumeType, aby określić, które dyski, na którą Szyfrujesz. 

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
  > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
  Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Sprawdź, czy dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, należy użyć [Get AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) polecenia cmdlet. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Wyłącz szyfrowanie dysków:** Aby wyłączyć szyfrowanie, użyj [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) polecenia cmdlet. Wyłączenie szyfrowania jest dozwolona tylko na woluminach danych dla maszyn wirtualnych systemu Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Włącz szyfrowanie dla istniejących lub uruchamianie systemu Linux maszyny Wirtualnej IaaS przy użyciu szablonu

Można włączyć szyfrowania dysków na istniejące lub uruchamianie systemu Linux maszyny Wirtualnej IaaS na platformie Azure przy użyciu [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Kliknij przycisk **Wdróż na platformie Azure** na szablonie Szybki Start platformy Azure.

2. Wybieranie subskrypcji, grupy zasobów, lokalizację grupy zasobów, parametry, postanowienia prawne i umowy. Kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na istniejące lub uruchomionej maszyny Wirtualnej IaaS.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager dla istniejących lub uruchomione maszyny wirtualne korzystające z usługi Azure AD identyfikator klienta:

| Parametr | Opis |
| --- | --- |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, które ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| keyVaultName | Nazwa klucza powinny zostać przekazane do magazyn kluczy. Możesz pobrać go przy użyciu polecenia interfejsu wiersza polecenia Azure `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  KeyEncryptionKeyURL | Adres URL klucza szyfrowania klucza, który jest używany do szyfrowania wygenerowany klucz. Ten parametr jest opcjonalny w przypadku wybrania **nokek** na liście rozwijanej UseExistingKek. Jeśli wybierzesz **kek** na liście rozwijanej UseExistingKek należy wprowadzić _keyEncryptionKeyURL_ wartości. |
| VolumeType | Typ operacji szyfrowania odbywa się na wolumin. Prawidłowe wartości obsługiwanych _OS_ lub _wszystkich_ (zobacz obsługiwane dystrybucje systemu Linux i ich wersje dla systemu operacyjnego i dysków z danymi w sekcji wymagań wstępnych, wcześniej). |
| SequenceVersion | Wersja sekwencji operacji funkcji BitLocker. Za każdym razem, gdy operacja szyfrowania dysku jest wykonywana na tej samej maszyny Wirtualnej, należy zwiększyć numer wersji. |
| vmName | Nazwa maszyny Wirtualnej, można wykonać w operacji szyfrowania. |
| hasło | Wpisz silne hasło klucza szyfrowania danych. |



## <a name="bkmk_EFA"> </a>Użyj funkcji EncryptFormatAll dla dysków z danymi na maszynach wirtualnych IaaS z systemem Linux
**EncryptFormatAll** parametru skraca czas dla dysków z danymi systemu Linux do zaszyfrowania. Partycje spełniającego określone kryteria zostaną sformatowane (z bieżącym system plików). Następnie będzie ponownego zainstalowania powrót, do których była przed wykonaniem polecenia. Jeśli chcesz wykluczyć dysk danych, która spełnia kryteria, należy go odinstalować przed uruchomieniem polecenia.

 Po uruchomieniu tego polecenia, wszystkie dyski, które zostały zainstalowane wcześniej zostaną sformatowane. Następnie Warstwa szyfrowania zostanie uruchomiony na podstawie teraz pusty dysk. Ta opcja jest zaznaczona, dysków tymczasowych zasobów dołączonych do maszyny Wirtualnej również być szyfrowana. W przypadku zresetowania efemerycznego dysku zostanie ponownie sformatowany i ponownie szyfrowane dla maszyny Wirtualnej przez rozwiązanie Azure Disk Encryption przy okazji dalej.

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

### <a name="bkmk_EFATemplate"> </a> Użyj parametru EncryptFormatAll przy użyciu szablonu
Aby użyć opcji EncryptFormatAll, należy użyć dowolnego istniejącego szablonu usługi Azure Resource Manager, która szyfruje maszyny Wirtualnej z systemem Linux i zmienić **EncryptionOperation** dla zasobu AzureDiskEncryption pole.

1. Na przykład użyć [szablonu usługi Resource Manager, aby zaszyfrować uruchomionej maszyny Wirtualnej systemu Linux IaaS](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Kliknij przycisk **Wdróż na platformie Azure** na szablonie Szybki Start platformy Azure.
3. Zmiana **EncryptionOperation** z **EnableEncryption** do **EnableEncryptionFormatAl**
4. Wybierz subskrypcję, grupy zasobów, lokalizację grupy zasobów, inne parametry, postanowienia prawne i umowy. Kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na istniejące lub uruchomionej maszyny Wirtualnej IaaS.


### <a name="bkmk_EFAPSH"> </a> Parametr EncryptFormatAll za pomocą polecenia cmdlet programu PowerShell
Użyj [AzVMDiskEncryptionExtension zestaw](/powershell/module/az.compute/set-azvmdiskencryptionextension) polecenia cmdlet z `EncryptFormatAll` parametru.

**Szyfruj przy użyciu klucza tajnego klienta i jego EncryptFormatAll uruchomionej maszyny Wirtualnej:** Na przykład poniższy skrypt inicjuje zmiennych i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension z parametrem EncryptFormatAll. Grupy zasobów, maszyna wirtualna, magazyn kluczy, aplikacji usługi AAD i klucz tajny klienta powinny zostały już utworzone jako warunki wstępne. Zamień MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM MySecureVault, My-AAD-client-ID, a My-AAD-client-secret własnymi wartościami.
  
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
    
    4. Uruchom polecenie cmdlet programu PowerShell Set-AzVMDiskEncryptionExtension z - EncryptFormatAll można zaszyfrować te dyski.
         ```azurepowershell-interactive
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Skonfiguruj LVM na podstawie tych nowych dysków. Należy pamiętać, że zaszyfrowanych dysków są odblokowane, po zakończeniu maszyny Wirtualnej podczas uruchamiania. Tak instalowania LVM musi także zostać później opóźnione.




## <a name="bkmk_VHDpre"> </a> Nowe maszyny wirtualne IaaS utworzone na podstawie szyfrowane klienta wirtualnego dysku twardego i kluczy szyfrowania
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu usługi Resource Manager, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. W poniższych sekcjach opisano szczegółowo szablonu usługi Resource Manager i interfejsu wiersza polecenia. 

Postępuj zgodnie z instrukcjami w dodatku do przygotowania obrazów zaszyfrowane wstępnie, których można użyć na platformie Azure. Po utworzeniu obrazu, do tworzenia zaszyfrowanych maszyn wirtualnych platformy Azure za pomocą kroki opisane w następnej sekcji.

* [Przygotowywanie wirtualnego dysku twardego Windows zaszyfrowane wstępnie](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Przygotowywanie wirtualnego dysku twardego systemu Linux zaszyfrowane wstępnie](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption. Migawkę dysku zarządzanego, może zostać pobrany z portalu lub [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) mogą być używane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania. Po nawiązaniu kopii zapasowej polecenia cmdlet Set-AzVMDiskEncryptionExtension może służyć do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup. Polecenie Set-AzVMDiskEncryptionExtension zakończy się niepowodzeniem dla maszyn wirtualnych dysku zarządzanego na podstawie, dopóki nie wykonano kopii zapasowej, a ten parametr został określony. 
>
>Szyfrowanie lub wyłączenie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 



### <a name="bkmk_VHDprePSH"> </a> Szyfrowanie maszyn wirtualnych IaaS z zaszyfrowane wstępnie dyskami VHD za pomocą programu Azure PowerShell 
Można włączyć szyfrowanie dysków na zaszyfrowane wirtualnego dysku twardego za pomocą polecenia cmdlet programu PowerShell [AzVMOSDisk zestaw](/powershell/module/az.compute/set-azvmosdisk#examples). W poniższym przykładzie zapewnia niektórych wspólnych parametrów. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włącz szyfrowanie dla dysków nowo dodane dane
Możesz dodać nowy dysk danych, za pomocą [dołączanie dysku maszyny wirtualnej az](../virtual-machines/linux/add-disk.md), lub [za pośrednictwem witryny Azure portal](../virtual-machines/linux/attach-disk-portal.md). Przed można zaszyfrować, należy najpierw zainstalować nowo dołączony dysk danych. Należy zażądać szyfrowania dysku danych, ponieważ dysk będzie można jej używać, gdy szyfrowanie jest w toku. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włącza szyfrowanie na nowo dodany dysk przy użyciu wiersza polecenia platformy Azure
 Jeśli maszyna wirtualna została wcześniej zaszyfrowane za pomocą "All" następnie typ woluminu parametr powinien pozostać wszystkie. Wszystkie obejmuje zarówno systemu operacyjnego i dysków z danymi. Jeśli maszyna wirtualna wcześniej został zaszyfrowany za pomocą typu woluminu "System operacyjny", a następnie typ woluminu parametru powinny być zmieniane dla wszystkich, więc, że zarówno system operacyjny, jak i nowy dysk danych zostaną dołączone. Jeśli maszyna wirtualna została zaszyfrowana przy użyciu tylko typ woluminu "Data", następnie może ona "Dane" jak pokazano poniżej. Dodawanie i dołączanie nowego dysku danych do maszyny Wirtualnej nie jest wystarczające przygotowania do szyfrowania. Nowo dołączony dysk również musi być sformatowany i prawidłowo zainstalowany na maszynie wirtualnej przed włączeniem szyfrowania. W systemie Linux dysk musi być zainstalowana w/etc/fstab za pomocą [nazwy urządzenia trwałego bloku](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

W przeciwieństwie do składni programu Powershell interfejsu wiersza polecenia nie wymaga od użytkownika podczas włączania szyfrowania wersji unikatowy ciąg. Interfejs wiersza polecenia automatycznie generuje i używa własną wartość wersji unikatowy ciąg.

-  **Szyfruj uruchomionej maszyny Wirtualnej za pomocą klucza tajnego klienta:** 

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Szyfruj uruchomionej maszyny Wirtualnej przy użyciu klucza KEK zawijania wpisu tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włącza szyfrowanie na nowo dodany dysk przy użyciu programu Azure PowerShell
 Szyfrowanie nowego dysku dla systemu Linux za pomocą programu Powershell, nowa wersja sekwencji musi być określona. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID wersji sekwencji. 
 

-  **Szyfruj uruchomionej maszyny Wirtualnej za pomocą klucza tajnego klienta:** Poniższy skrypt inicjuje zmiennych i uruchamia polecenie cmdlet Set-AzVMDiskEncryptionExtension. Grupy zasobów, maszyna wirtualna, magazyn kluczy, aplikacji usługi AAD i klucz tajny klienta powinny zostały już utworzone jako warunki wstępne. Zamień MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM MySecureVault, My-AAD-client-ID, a My-AAD-client-secret własnymi wartościami. Parametr - VolumeType ustawiono dysków z danymi i nie dysk systemu operacyjnego. Jeśli maszyna wirtualna wcześniej został zaszyfrowany za pomocą typu woluminu "System operacyjny" lub "All", następnie parametr - VolumeType należy je zmienić wszystkie tak, że zarówno system operacyjny, jak i nowy dysk danych zostaną dołączone.

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
- **Szyfruj uruchomionej maszyny Wirtualnej przy użyciu klucza KEK zawijania wpisu tajnego klienta:** Usługa Azure Disk Encryption umożliwia określenie istniejącego klucza w magazynie kluczy na zawijanie kluczy tajnych szyfrowania dysków, które zostały wygenerowane podczas włączania szyfrowania. Jeśli klucz szyfrowania jest określony, usługi Azure Disk Encryption używa tego klucza do opakowania wpisów tajnych szyfrowania przed zapisaniem w usłudze Key Vault. Parametr - VolumeType ustawiono dysków z danymi i nie dysk systemu operacyjnego. Jeśli maszyna wirtualna wcześniej został zaszyfrowany za pomocą typu woluminu "System operacyjny" lub "All", następnie parametr - VolumeType należy je zmienić wszystkie tak, że zarówno system operacyjny, jak i nowy dysk danych zostaną dołączone.

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
> Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> </br>
Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

## <a name="disable-encryption-for-linux-vms"></a>Wyłącz szyfrowanie dla maszyn wirtualnych z systemem Linux
Można wyłączyć szyfrowanie przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub przy użyciu szablonu usługi Resource Manager. 

>[!IMPORTANT]
>Wyłączenie szyfrowania za pomocą usługi Azure Disk Encryption na maszynach wirtualnych z systemem Linux jest obsługiwana tylko dla woluminów danych. Go nie jest obsługiwana na woluminach systemu operacyjnego i danych, jeśli wolumin systemu operacyjnego został zaszyfrowany.  

- **Wyłącz szyfrowanie dysków za pomocą programu Azure PowerShell:** Aby wyłączyć szyfrowanie, użyj [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) polecenia cmdlet. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Wyłącz szyfrowanie za pomocą wiersza polecenia platformy Azure:** Aby wyłączyć szyfrowanie, użyj [az vm encryption, wyłącz](/cli/azure/vm/encryption#az-vm-encryption-disable) polecenia. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Wyłącz szyfrowanie za pomocą szablonu usługi Resource Manager:** Użyj [Wyłącz szyfrowanie dla uruchomionej maszyny Wirtualnej systemu Linux](https://aka.ms/decrypt-linuxvm) szablon, aby wyłączyć szyfrowanie.
     1. Kliknij przycisk **Wdrażaj na platformie Azure**.
     2. Wybierz subskrypcję, grupy zasobów, lokalizację maszyny Wirtualnej, postanowienia prawne i umowy.
     3.  Kliknij przycisk **zakupu** umożliwia wyłączenie szyfrowania dysku na uruchomionej maszynie Wirtualnej Windows. 


## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Włączanie usługi Azure Disk Encryption for Windows](azure-security-disk-encryption-windows-aad.md)
