---
title: Usługa Azure Disk Encryption przy użyciu maszyn wirtualnych IaaS z systemem Linux aplikacji usługi Azure AD (poprzedniej wersji) | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera instrukcje na temat włączania systemu Microsoft Azure Disk Encryption dla maszyn wirtualnych IaaS z systemem Linux.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 09/19/2018
ms.openlocfilehash: d544d131aed8dc6560f09b7a293e950c38380fb8
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623422"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms-previous-release"></a>Włączanie usługi Azure Disk Encryption dla maszyn wirtualnych systemu Linux IaaS (poprzedniej wersji)

**Nowa wersja usługi Azure Disk Encryption eliminuje wymaganie zapewniające parametrem aplikacji usługi Azure AD, aby włączyć szyfrowanie dysku maszyny Wirtualnej. Za pomocą nowej wersji już nie są wymagane poświadczenia usługi Azure AD podczas wykonywania kroku Włączanie szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje, aby włączyć szyfrowanie dysku maszyny Wirtualnej przy użyciu nowej wersji, zobacz [usługi Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](azure-security-disk-encryption-linux.md). Maszyny wirtualne, które już zostały zaszyfrowane za pomocą aplikacji usługi Azure AD, parametry są nadal obsługiwane i powinno być kontynuowane przy użyciu składni usługi AAD.**

Można włączyć wiele scenariuszy szyfrowania dysku i kroki zależą od scenariusza. W poniższych częściach omówiono scenariuszach szczegółowo dla maszyn wirtualnych IaaS z systemem Linux. Zanim będzie można użyć szyfrowania dysku [wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) należy wykonać i [dodatkowe wymagania wstępne dotyczące maszyn wirtualnych IaaS z systemem Linux](azure-security-disk-encryption-prerequisites-aad.md#bkmk_LinuxPrereq) powinna zostać przejrzana pod sekcji.

Wykonaj [migawki](../virtual-machines/windows/snapshot-copy-managed-disk.md) i/lub wykonać kopię zapasową przed dyski są szyfrowane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania może wystąpić, jeśli wystąpił nieoczekiwany błąd występuje podczas szyfrowania. Maszyny wirtualne z dyskami zarządzanymi wymagają kopię zapasową przed zaszyfrowaniem. Po nawiązaniu kopia zapasowa służy polecenie cmdlet polecenia Set-AzureRmVMDiskEncryptionExtension do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych, zobacz [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) artykułu. 

>[!WARNING]
 >Upewnij się, że klucze tajne szyfrowania nie przekracza granic regionalnych, usługa Azure Disk Encryption musi znajdować się w tym samym regionie usługi Key Vault i maszyn wirtualnych. Tworzenie i używanie usługi Key Vault, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania.</br></br>

> W przypadku szyfrowania woluminów systemu operacyjnego Linux, proces może potrwać kilka godzin. Jest to normalny trwać dłużej niż woluminów danych na potrzeby szyfrowania woluminów systemu operacyjnego Linux. 

>Wyłączenie szyfrowania na maszynach wirtualnych z systemem Linux jest obsługiwana tylko dla woluminów danych. Go nie jest obsługiwana na woluminach systemu operacyjnego i danych, jeśli wolumin systemu operacyjnego został zaszyfrowany.  


## <a name="bkmk_NewLinux"></a> Wdrażanie nowych maszyn wirtualnych IaaS systemu Linux przy użyciu włączone szyfrowanie dysków 

1. Użyj [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel) do tworzenia nowego zaszyfrowanych maszyn wirtualnych IaaS z systemem Linux. Szablon spowoduje utworzenie nowej maszyny Wirtualnej programu firmę Red Hat Linux 7.2 z macierzy RAID-0 200 GB i pełne szyfrowanie dysków za pomocą dysków zarządzanych. Na [— często zadawane pytania](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) artykułu, możesz zauważyć, że niektórych dystrybucjach systemu Linux obsługuje tylko szyfrowanie dla dysków z danymi. Jednak ten szablon umożliwia zapoznanie się z wdrażania szablonów i zweryfikowanie stanu szyfrowania na wiele sposobów. 
 
1. Kliknij przycisk **Wdróż na platformie Azure** szablonu usługi Azure Resource Manager.

2. Wybieranie subskrypcji, grupy zasobów, lokalizację grupy zasobów, parametry, postanowienia prawne i umowy. Kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na istniejące lub uruchomionej maszyny Wirtualnej IaaS.

3. Po wdrożeniu szablonu, sprawdź stan szyfrowania maszyny Wirtualnej przy użyciu preferowanej metody:
     - Weryfikuj za pomocą wiersza polecenia platformy Azure przy użyciu [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) polecenia. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - Weryfikuj za pomocą programu Azure PowerShell przy użyciu [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) polecenia cmdlet. 

         ```azurepowershell-interactive
         Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     - Wybierz maszynę Wirtualną, a następnie kliknij pozycję **dysków** w obszarze **ustawienia** nagłówek, aby zweryfikować stan szyfrowania w portalu. Wykres w obszarze **szyfrowania**, zobaczysz, czy jest włączony. 

| Parametr | Opis |
| --- | --- |
| Identyfikator klienta usługi AAD | Identyfikator klienta aplikacji usługi Azure AD, które ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| Klucz tajny klienta usługi AAD | Klucz tajny klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| Nazwa usługi Key Vault | Nazwa magazynu kluczy, należy umieścić klucz. |
| Grupa zasobów magazynu kluczy | Grupa zasobów magazynu kluczy. |


## <a name="bkmk_RunningLinux"> </a> Włącz szyfrowanie dla istniejących lub uruchamianie systemu Linux Maszynie wirtualnej IaaS
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu usługi Resource Manager, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. 

>[!IMPORTANT]
 >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption. Migawkę dysku zarządzanego, może zostać pobrany z portalu lub [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) mogą być używane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania. Po nawiązaniu kopii zapasowej polecenia cmdlet polecenia Set-AzureRmVMDiskEncryptionExtension może służyć do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup. Polecenie polecenia Set-AzureRmVMDiskEncryptionExtension zakończy się niepowodzeniem dla maszyn wirtualnych dysku zarządzanego na podstawie, dopóki nie wykonano kopii zapasowej, a ten parametr został określony. 
>
>Szyfrowanie lub wyłączenie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Włącz szyfrowanie dla istniejących lub uruchomionej maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure 
Można włączyć szyfrowanie dysków na zaszyfrowane wirtualnego dysku twardego przy instalowaniu i używaniu [interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure) narzędzie wiersza polecenia. Można go również używać w przeglądarce z usługą [Azure Cloud Shell](../cloud-shell/overview.md) albo można go zainstalować na maszynie lokalnej i używać w dowolnej sesji programu PowerShell. Aby włączyć szyfrowanie na istniejące lub działających maszyn wirtualnych systemu Linux IaaS na platformie Azure, użyj następujących poleceń interfejsu wiersza polecenia:

Użyj [Włącz az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-enable) polecenie, aby włączyć szyfrowanie na uruchomionej maszyny wirtualnej IaaS na platformie Azure.

-  **Szyfruj uruchomionej maszyny Wirtualnej za pomocą klucza tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Szyfruj uruchomionej maszyny Wirtualnej przy użyciu klucza KEK zawijania wpisu tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Sprawdź dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, należy użyć [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) polecenia. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Wyłącz szyfrowanie:** umożliwia wyłączenie szyfrowania, należy użyć [az vm encryption, wyłącz](/cli/azure/vm/encryption#az-vm-encryption-disable) polecenia. Wyłączenie szyfrowania jest dozwolona tylko na woluminach danych dla maszyn wirtualnych systemu Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Włącz szyfrowanie dla istniejących lub uruchomionej maszyny Wirtualnej systemu Linux przy użyciu programu PowerShell
Użyj [polecenia Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) polecenia cmdlet, aby włączyć szyfrowanie na uruchomionej maszyny wirtualnej IaaS na platformie Azure. 

-  **Szyfruj uruchomionej maszyny Wirtualnej za pomocą klucza tajnego klienta:** poniższy skrypt inicjuje zmiennych i uruchamia polecenie cmdlet polecenia Set-AzureRmVMDiskEncryptionExtension. Grupy zasobów, maszyna wirtualna, magazyn kluczy, aplikacji usługi AAD i klucz tajny klienta powinny zostały już utworzone jako warunki wstępne. Zastąp MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID, a My-AAD-client-secret z własnymi wartościami. Może być konieczne dodanie parametru - VolumeType, jeśli Szyfrujesz dysk danych i nie dysk systemu operacyjnego. 

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Szyfruj uruchomionej maszyny Wirtualnej przy użyciu klucza KEK zawijania wpisu tajnego klienta:** usługi Azure Disk Encryption umożliwia określenie istniejącego klucza w magazynie kluczy na zawijanie kluczy tajnych szyfrowania dysków, które zostały wygenerowane podczas włączania szyfrowania. Jeśli klucz szyfrowania jest określony, usługi Azure Disk Encryption używa tego klucza do opakowania wpisów tajnych szyfrowania przed zapisaniem w usłudze Key Vault. Może być konieczne dodanie parametru - VolumeType, jeśli Szyfrujesz dysk danych i nie dysk systemu operacyjnego. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

 >[!NOTE]
 > Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Sprawdź dyski są szyfrowane:** Aby sprawdzić stan szyfrowania maszyny wirtualnej IaaS, należy użyć [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) polecenia cmdlet. 
    
     ```azurepowershell-interactive 
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName MySecureRg -VMName MySecureVM
     ```
    
- **Wyłącz szyfrowanie dysków:** umożliwia wyłączenie szyfrowania, należy użyć [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) polecenia cmdlet. Wyłączenie szyfrowania jest dozwolona tylko na woluminach danych dla maszyn wirtualnych systemu Linux.
     
     ```azurepowershell-interactive 
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
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
| keyVaultName | Nazwa klucza powinny zostać przekazane do magazyn kluczy. Możesz pobrać go przy użyciu polecenia interfejsu wiersza polecenia Azure `az keyvault show --name "MySecureVault" --query resourceGroup`. |
|  KeyEncryptionKeyURL | Adres URL klucza szyfrowania klucza, który jest używany do szyfrowania wygenerowany klucz. Ten parametr jest opcjonalny w przypadku wybrania **nokek** na liście rozwijanej UseExistingKek. Jeśli wybierzesz **kek** na liście rozwijanej UseExistingKek należy wprowadzić _keyEncryptionKeyURL_ wartości. |
| VolumeType | Typ operacji szyfrowania odbywa się na wolumin. Prawidłowe wartości obsługiwanych _OS_ lub _wszystkich_ (zobacz obsługiwane dystrybucje systemu Linux i ich wersje dla systemu operacyjnego i dysków z danymi w sekcji wymagań wstępnych, wcześniej). |
| SequenceVersion | Wersja sekwencji operacji funkcji BitLocker. Za każdym razem, gdy operacja szyfrowania dysku jest wykonywana na tej samej maszyny Wirtualnej, należy zwiększyć numer wersji. |
| vmName | Nazwa maszyny Wirtualnej, można wykonać w operacji szyfrowania. |
| hasło | Wpisz silne hasło klucza szyfrowania danych. |



## <a name="bkmk_EFA"> </a>Użyj funkcji EncryptFormatAll dla dysków z danymi na maszynach wirtualnych IaaS z systemem Linux
**EncryptFormatAll** parametru skraca czas dla dysków z danymi systemu Linux do zaszyfrowania. Partycje spełniającego określone kryteria zostaną sformatowane (z bieżącym system plików). Następnie będzie ponownego zainstalowania powrót, do których była przed wykonaniem polecenia. Jeśli chcesz wykluczyć dysk danych, która spełnia kryteria, należy go odinstalować przed uruchomieniem polecenia.

 Po uruchomieniu tego polecenia, wszystkie dyski, które zostały zainstalowane wcześniej zostaną sformatowane. Następnie Warstwa szyfrowania zostanie uruchomiony na podstawie teraz pusty dysk. Ta opcja jest zaznaczona, dysków tymczasowych zasobów dołączonych do maszyny Wirtualnej również być szyfrowana. W przypadku zresetowania efemerycznego dysku zostanie ponownie sformatowany i ponownie szyfrowane dla maszyny Wirtualnej przez rozwiązanie Azure Disk Encryption przy okazji dalej.

>[!WARNING]
> EncryptFormatAll nie powinny być używane, gdy ma wymaganych danych na woluminach danych maszyny Wirtualnej. Aby wykluczyć dyski z szyfrowania, mogą je odinstalowywania. Należy najpierw wypróbować EncryptFormatAll najpierw na testowej maszynie Wirtualnej, parametr funkcji i jej domniemanie, przed podjęciem próby go na maszynie Wirtualnej w środowisku produkcyjnym. Opcja EncryptFormatAll formatuje dysk z danymi i z niego wszystkie dane zostaną utracone. Przed kontynuowaniem sprawdź, czy dyski, które chcesz wykluczyć są prawidłowo unmouted. </br></br>
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
Użyj [polecenia Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) polecenia cmdlet z [parametru EncryptFormatAll](https://www.powershellgallery.com/packages/AzureRM/5.0.0). 

**Szyfruj uruchomionej maszyny Wirtualnej przy użyciu klucza tajnego klienta i jego EncryptFormatAll:** jako przykład, poniższy skrypt inicjuje zmiennych i uruchamia polecenia Set-AzureRmVMDiskEncryptionExtension polecenie cmdlet z parametrem EncryptFormatAll. Grupy zasobów, maszyna wirtualna, magazyn kluczy, aplikacji usługi AAD i klucz tajny klienta powinny zostały już utworzone jako warunki wstępne. Zastąp MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID, a My-AAD-client-secret z własnymi wartościami.
  
   ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
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
    
    4. Uruchom polecenie cmdlet programu PowerShell polecenia Set-AzureRmVMDiskEncryptionExtension z - EncryptFormatAll można zaszyfrować te dyski.
         ```azurepowershell-interactive
         Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Skonfiguruj LVM na podstawie tych nowych dysków. Należy pamiętać, że zaszyfrowanych dysków są odblokowane, po zakończeniu maszyny Wirtualnej podczas uruchamiania. Tak instalowania LVM musi także zostać później opóźnione.




## <a name="bkmk_VHDpre"> </a> Nowe maszyny wirtualne IaaS utworzone na podstawie szyfrowane klienta wirtualnego dysku twardego i kluczy szyfrowania
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu usługi Resource Manager, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. W poniższych sekcjach opisano szczegółowo szablonu usługi Resource Manager i interfejsu wiersza polecenia. 

Postępuj zgodnie z instrukcjami w dodatku do przygotowania obrazów zaszyfrowane wstępnie, których można użyć na platformie Azure. Po utworzeniu obrazu, do tworzenia zaszyfrowanych maszyn wirtualnych platformy Azure za pomocą kroki opisane w następnej sekcji.

* [Przygotowywanie wirtualnego dysku twardego Windows zaszyfrowane wstępnie](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Przygotowywanie wirtualnego dysku twardego systemu Linux zaszyfrowane wstępnie](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption. Migawkę dysku zarządzanego, może zostać pobrany z portalu lub [kopia zapasowa Azure](../backup/backup-azure-vms-encryption.md) mogą być używane. Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania. Po nawiązaniu kopii zapasowej polecenia cmdlet polecenia Set-AzureRmVMDiskEncryptionExtension może służyć do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup. Polecenie polecenia Set-AzureRmVMDiskEncryptionExtension zakończy się niepowodzeniem dla maszyn wirtualnych dysku zarządzanego na podstawie, dopóki nie wykonano kopii zapasowej, a ten parametr został określony. 
>
>Szyfrowanie lub wyłączenie szyfrowania może spowodować ponowne uruchomienie maszyny Wirtualnej. 



### <a name="bkmk_VHDprePSH"> </a> Szyfrowanie maszyn wirtualnych IaaS z zaszyfrowane wstępnie dyskami VHD za pomocą programu Azure PowerShell 
Można włączyć szyfrowanie dysków na zaszyfrowane wirtualnego dysku twardego za pomocą polecenia cmdlet programu PowerShell [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). W poniższym przykładzie zapewnia niektórych wspólnych parametrów. 

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName "MySecureRG"
```

### <a name="bkmk_VHDpreRM"> </a> Szablon usługi Resource Manager służy do szyfrowania maszyn wirtualnych IaaS z zaszyfrowane wstępnie wirtualne dyski twarde 
Można włączyć szyfrowanie dysków na zaszyfrowane wirtualnego dysku twardego za pomocą [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. Szablon szybkiego startu platformy Azure, wybierz polecenie **Wdróż na platformie Azure**.

2. Wybieranie subskrypcji, grupy zasobów, lokalizację grupy zasobów, parametry, postanowienia prawne i umowy. Kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na nowej maszynie Wirtualnej IaaS.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager dla zaszyfrowanych wirtualnego dysku twardego:

| Parametr | Opis |
| --- | --- |
| newStorageAccountName | Nazwa konta magazynu do przechowywania wirtualnego dysku twardego systemu operacyjnego zaszyfrowane. To konto magazynu powinny już został utworzony w tej samej grupie zasobów i tej samej lokalizacji co maszyna wirtualna. |
| osVhdUri | Identyfikator URI wirtualnego dysku twardego systemu operacyjnego z konta magazynu. |
| osType | Typ produktu systemu operacyjnego (Windows/Linux). |
| virtualNetworkName | Nazwa sieci wirtualnej, która karty Sieciowej maszyny Wirtualnej powinny należeć do. Nazwa powinna już została utworzona w tej samej grupie zasobów i tej samej lokalizacji co maszyna wirtualna. |
| subnetName | Nazwa podsieci w sieci wirtualnej, której karty Sieciowej maszyny Wirtualnej powinna należeć. |
| vmSize | Rozmiar maszyny Wirtualnej. Obecnie są obsługiwane tylko standardowe A, D i G serii. |
| keyVaultResourceID | Identyfikator zasobu, która identyfikuje zasób magazynu kluczy w usłudze Azure Resource Manager. Możesz pobrać go przy użyciu polecenia cmdlet programu PowerShell `(Get-AzureRmKeyVault -VaultName &lt;MyKeyVaultName&gt; -ResourceGroupName &lt;MyResourceGroupName&gt;).ResourceId` lub za pomocą polecenia wiersza polecenia platformy Azure `az keyvault show --name "MySecureVault" --query id`|
| keyVaultSecretUrl | Adres URL klucza szyfrowania dysku, który jest skonfigurowany w usłudze key vault. |
| keyVaultKekUrl | Adres URL klucza szyfrowania do szyfrowania klucza wygenerowanego szyfrowania dysków. |
| vmName | Nazwa maszyny Wirtualnej IaaS. |

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Włącz szyfrowanie dla dysków nowo dodane dane
Możesz dodać nowy dysk danych, za pomocą [dołączanie dysku maszyny wirtualnej az](../virtual-machines/linux/add-disk.md), lub [za pośrednictwem witryny Azure portal](../virtual-machines/linux/attach-disk-portal.md). Przed można zaszyfrować, należy najpierw zainstalować nowo dołączony dysk danych. Należy zażądać szyfrowania dysku danych, ponieważ dysk będzie można jej używać, gdy szyfrowanie jest w toku. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Włącza szyfrowanie na nowo dodany dysk przy użyciu wiersza polecenia platformy Azure
 Jeśli maszyna wirtualna została wcześniej zaszyfrowane za pomocą "All" następnie typ woluminu parametr powinien pozostać wszystkie. Wszystkie obejmuje zarówno systemu operacyjnego i dysków z danymi. Jeśli maszyna wirtualna wcześniej został zaszyfrowany za pomocą typu woluminu "System operacyjny", a następnie typ woluminu parametru powinny być zmieniane dla wszystkich, więc, że zarówno system operacyjny, jak i nowy dysk danych zostaną dołączone. Jeśli maszyna wirtualna została zaszyfrowana przy użyciu tylko typ woluminu "Data", następnie może ona "Dane" jak pokazano poniżej. Dodawanie i dołączanie nowego dysku danych do maszyny Wirtualnej nie jest wystarczające przygotowania do szyfrowania. Nowo dołączony dysk również musi być sformatowany i prawidłowo zainstalowany na maszynie wirtualnej przed włączeniem szyfrowania. W systemie Linux dysk musi być zainstalowana w/etc/fstab za pomocą [nazwy urządzenia trwałego bloku](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

W przeciwieństwie do składni programu Powershell interfejsu wiersza polecenia nie wymaga od użytkownika podczas włączania szyfrowania wersji unikatowy ciąg. Interfejs wiersza polecenia automatycznie generuje i używa własną wartość wersji unikatowy ciąg.

-  **Szyfruj uruchomionej maszyny Wirtualnej za pomocą klucza tajnego klienta:** 

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Szyfruj uruchomionej maszyny Wirtualnej przy użyciu klucza KEK zawijania wpisu tajnego klienta:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Włącza szyfrowanie na nowo dodany dysk przy użyciu programu Azure PowerShell
 Szyfrowanie nowego dysku dla systemu Linux za pomocą programu Powershell, nowa wersja sekwencji musi być określona. Wersja sekwencji musi być unikatowa. Poniższy skrypt generuje identyfikator GUID wersji sekwencji. 
 

-  **Szyfruj uruchomionej maszyny Wirtualnej za pomocą klucza tajnego klienta:** poniższy skrypt inicjuje zmiennych i uruchamia polecenie cmdlet polecenia Set-AzureRmVMDiskEncryptionExtension. Grupy zasobów, maszyna wirtualna, magazyn kluczy, aplikacji usługi AAD i klucz tajny klienta powinny zostały już utworzone jako warunki wstępne. Zastąp MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID, a My-AAD-client-secret z własnymi wartościami. Parametr - VolumeType ustawiono dysków z danymi i nie dysk systemu operacyjnego. Jeśli maszyna wirtualna wcześniej został zaszyfrowany za pomocą typu woluminu "System operacyjny" lub "All", następnie parametr - VolumeType należy je zmienić wszystkie tak, że zarówno system operacyjny, jak i nowy dysk danych zostaną dołączone.

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
    ```
- **Szyfruj uruchomionej maszyny Wirtualnej przy użyciu klucza KEK zawijania wpisu tajnego klienta:** usługi Azure Disk Encryption umożliwia określenie istniejącego klucza w magazynie kluczy na zawijanie kluczy tajnych szyfrowania dysków, które zostały wygenerowane podczas włączania szyfrowania. Jeśli klucz szyfrowania jest określony, usługi Azure Disk Encryption używa tego klucza do opakowania wpisów tajnych szyfrowania przed zapisaniem w usłudze Key Vault. Parametr - VolumeType ustawiono dysków z danymi i nie dysk systemu operacyjnego. Jeśli maszyna wirtualna wcześniej został zaszyfrowany za pomocą typu woluminu "System operacyjny" lub "All", następnie parametr - VolumeType należy je zmienić wszystkie tak, że zarówno system operacyjny, jak i nowy dysk danych zostaną dołączone.

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data';
     ```


>[!NOTE]
> Składnia służąca do wartości parametru dysku — szyfrowanie — magazyn kluczy jest ciągiem pełny identyfikator: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> </br>
Składnia wartości parametru klucza szyfrowania jest pełny identyfikator URI do KEK, podobnie jak w: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

## <a name="disable-encryption-for-linux-vms"></a>Wyłącz szyfrowanie dla maszyn wirtualnych z systemem Linux
Można wyłączyć szyfrowanie przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub przy użyciu szablonu usługi Resource Manager. 

>[!IMPORTANT]
>Wyłączenie szyfrowania za pomocą usługi Azure Disk Encryption na maszynach wirtualnych z systemem Linux jest obsługiwana tylko dla woluminów danych. Go nie jest obsługiwana na woluminach systemu operacyjnego i danych, jeśli wolumin systemu operacyjnego został zaszyfrowany.  

- **Wyłącz szyfrowanie dysków za pomocą programu Azure PowerShell:** umożliwia wyłączenie szyfrowania, należy użyć [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) polecenia cmdlet. 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Wyłącz szyfrowanie za pomocą wiersza polecenia platformy Azure:** umożliwia wyłączenie szyfrowania, należy użyć [az vm encryption, wyłącz](/cli/azure/vm/encryption#az-vm-encryption-disable) polecenia. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Wyłączenie szyfrowania za pomocą szablonu usługi Resource Manager:** użyj [Wyłącz szyfrowanie dla uruchomionej maszyny Wirtualnej systemu Linux](https://aka.ms/decrypt-linuxvm) szablon, aby wyłączyć szyfrowanie.
     1. Kliknij przycisk **Wdrażaj na platformie Azure**.
     2. Wybierz subskrypcję, grupy zasobów, lokalizację maszyny Wirtualnej, postanowienia prawne i umowy.
     3.  Kliknij przycisk **zakupu** umożliwia wyłączenie szyfrowania dysku na uruchomionej maszynie Wirtualnej Windows. 


## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Włączanie usługi Azure Disk Encryption for Windows](azure-security-disk-encryption-windows-aad.md)
