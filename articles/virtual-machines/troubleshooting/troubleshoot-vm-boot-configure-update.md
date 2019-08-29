---
title: Uruchamianie maszyny wirtualnej jest zablokowane na "Trwa przygotowywanie systemu Windows. Nie wyłączaj komputera "na platformie Azure | Microsoft Docs
description: Wprowadź kroki rozwiązywania problemu z zawieszeniem uruchomienia maszyny wirtualnej "Trwa przygotowywanie systemu Windows. Nie wyłączaj komputera”.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: 2f3c18ea1887ea5b05bb89f85371139ac83dfe49
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080159"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Uruchamianie maszyny wirtualnej jest zablokowane na "Trwa przygotowywanie systemu Windows. Nie wyłączaj komputera "na platformie Azure

Ten artykuł pomaga w rozwiązaniu problemu, gdy maszyna wirtualna (VM) jest zablokowana na "Przygotowywanie systemu Windows. Nie wyłączaj etapu komputera podczas uruchamiania.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>Objawy

W przypadku korzystania z **diagnostyki rozruchu** w celu pobrania zrzutu ekranu maszyny wirtualnej system operacyjny nie jest w pełni uruchamiany. Na maszynie wirtualnej zostanie wyświetlony komunikat "Trwa przygotowywanie systemu Windows. Nie wyłączaj komputera”.

![Przykład komunikatu dla systemu Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Przykład komunikatu](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Przyczyna

Zazwyczaj ten problem występuje, gdy serwer przeprowadza ostateczną ponowny rozruch po zmianie konfiguracji. Zmiana konfiguracji może zostać zainicjowana przez aktualizacje systemu Windows lub zmiany na rolach/funkcji serwera. W przypadku Windows Update, jeśli rozmiar aktualizacji był duży, system operacyjny potrzebuje więcej czasu, aby ponownie skonfigurować zmiany.

## <a name="back-up-the-os-disk"></a>Tworzenie kopii zapasowej dysku systemu operacyjnego

Przed podjęciem próby rozwiązania problemu wykonaj kopię zapasową dysku systemu operacyjnego.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>W przypadku maszyn wirtualnych z szyfrowanym dyskiem należy najpierw odblokować dyski

Wykonaj następujące kroki, aby określić, czy maszyna wirtualna jest zaszyfrowaną maszyną wirtualną.

1. Na Azure Portal Otwórz maszynę wirtualną, a następnie przejdź do dysków.

2. Sprawdź kolumnę **szyfrowanie** , aby sprawdzić, czy szyfrowanie jest włączone.

Jeśli dysk systemu operacyjnego jest zaszyfrowany, Odblokuj zaszyfrowany dysk. Aby odblokować dysk, wykonaj następujące kroki.

1. Utwórz maszynę wirtualną odzyskiwania, która znajduje się w tej samej grupie zasobów, koncie magazynu i lokalizacji co uwzględniona maszyna wirtualna.

2. W Azure Portal Usuń zaatakowaną maszynę wirtualną i Zachowaj ten dysk.

3. Uruchom program PowerShell jako administrator.

4. Uruchom następujące polecenie cmdlet, aby pobrać nazwę klucza tajnego.

    ```Powershell
    Login-AzAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. Po określeniu nazwy wpisu tajnego Uruchom następujące polecenia w programie PowerShell.

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Przekonwertuj wartość zakodowaną algorytmem Base64 na bajty i Zapisz dane wyjściowe do pliku. 

    > [!Note]
    > Jeśli używasz opcji odblokowywania USB, nazwa pliku klucz szyfrowania bloków musi być zgodna z oryginalnym identyfikatorem GUID klucz szyfrowania bloków. Utwórz folder na dysku C o nazwie "klucz szyfrowania bloków" przed wykonaniem tych kroków.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. Po utworzeniu pliku klucz szyfrowania bloków na komputerze Skopiuj plik do maszyny wirtualnej odzyskiwania, do której jest dołączony zablokowany dysk systemu operacyjnego. Uruchom następujące polecenia przy użyciu lokalizacji pliku klucz szyfrowania bloków.

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Opcjonalnie**: W niektórych scenariuszach może być konieczne odszyfrowanie dysku za pomocą tego polecenia.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > W poprzednim poleceniu przyjęto założenie, że dysk jest szyfrowany na literę F.

8. Jeśli konieczne jest zebranie dzienników, przejdź do **litery dysku Path: \ Windows\System32\winevt\Logs**.

9. Odłącz dysk od maszyny odzyskiwania.

### <a name="create-a-snapshot"></a>Tworzenie migawki

Aby utworzyć migawkę, wykonaj kroki opisane w sekcji [migawka dysku](../windows/snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Zbieranie zrzutu pamięci systemu operacyjnego

Wykonaj kroki opisane w sekcji [Zbierz zrzut systemu operacyjnego](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) , aby zebrać zrzut systemu operacyjnego w przypadku zablokowania maszyny wirtualnej w konfiguracji.

## <a name="contact-microsoft-support"></a>Skontaktuj się z pomocą techniczną firmy Microsoft

Po zebraniu pliku zrzutu skontaktuj się z [pomocą techniczną firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby przeanalizować główną przyczynę.


## <a name="rebuild-the-vm-by-using-powershell"></a>Ponowne kompilowanie maszyny wirtualnej przy użyciu programu PowerShell

Po zebraniu pliku zrzutu pamięci wykonaj następujące kroki, aby skompilować ponownie maszynę wirtualną.

**Dla dysków niezarządzanych**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID “SubscriptionID” | Select-AzSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**W przypadku dysków zarządzanych**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID "SubscriptionID" | Select-AzSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
