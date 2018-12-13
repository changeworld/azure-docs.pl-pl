---
title: Uruchamianie maszyny Wirtualnej znajduje się na "gotowy Windows wprowadzenie. Nie wyłączaj komputera"na platformie Azure | Dokumentacja firmy Microsoft
description: Wprowadź kroki, aby rozwiązać ten problem, w którym uruchamiania maszyny Wirtualnej znajduje się na "gotowy Windows wprowadzenie. Nie wyłączaj komputera”.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: eb27b4e6c60f23a55a58cd2aae3cff927ffeaf03
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316101"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Uruchamianie maszyny Wirtualnej znajduje się na "gotowy Windows wprowadzenie. Nie wyłączaj komputera"na platformie Azure

Ten artykuł pomoże Ci rozwiązać ten problem, gdy utkwiła maszyny wirtualnej (VM) na "Windows przygotowanie. Nie wyłączaj komputera"etap podczas uruchamiania.

## <a name="symptoms"></a>Objawy

Kiedy używasz **diagnostykę rozruchu** można pobrać zrzut ekranu maszyny wirtualnej, system operacyjny nie pełni uruchomiona. Maszyna wirtualna zostanie wyświetlony komunikat "wprowadzenie Windows gotowe. Nie wyłączaj komputera”.

![Przykładowy komunikat dla systemu Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Przykładowy komunikat](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Przyczyna

Zazwyczaj ten problem występuje, gdy serwer wykonuje końcowe ponowny rozruch po konfiguracji został zmieniony. Zmiana konfiguracji może być zainicjowana przez aktualizacje Windows lub zmiany na temat ról/funkcji serwera. For Windows Update Jeśli długą rozmiaru aktualizacji systemu operacyjnego musi więcej czasu, aby zmienić konfigurację zmiany.

## <a name="back-up-the-os-disk"></a>Tworzenie kopii zapasowej dysku systemu operacyjnego

Przed podjęciem próby rozwiązania problemu, Utwórz kopię zapasową dysku systemu operacyjnego.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>W przypadku maszyn wirtualnych z zaszyfrowanego dysku musisz odblokować dyski najpierw

Wykonaj następujące kroki, aby ustalić, czy maszyna wirtualna jest zaszyfrowanej maszyny Wirtualnej.

1. W witrynie Azure portal Otwórz maszyny Wirtualnej, a następnie przejdź do dysków.

2. Przyjrzyj się **szyfrowania** kolumny, aby zobaczyć, czy włączono szyfrowanie.

Jeśli dysk systemu operacyjnego jest szyfrowany, odblokuj zaszyfrowanego dysku. Aby odblokować dysk, wykonaj następujące kroki.

1. Tworzenie maszyny Wirtualnej odzyskiwania, który znajduje się w tej samej grupie zasobów, konto magazynu oraz lokalizacji, co dotyczy maszyny Wirtualnej.

2. W witrynie Azure portal Usuń dotyczy maszyny Wirtualnej i Zachowaj dysku.

3. Uruchom program PowerShell jako administrator.

4. Uruchom następujące polecenie cmdlet, aby uzyskać nazwę wpisu tajnego.

    ```Powershell
    Login-AzureRmAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. Po utworzeniu nazwa wpisu tajnego, uruchom następujące polecenia w programie PowerShell.

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Konwertuj wartość algorytmem Base64 bajtów i zapisać dane wyjściowe do pliku. 

    > [!Note]
    > Jeśli używasz USB odblokować opcji, nazwa pliku klucza szyfrowania bloków musi odpowiadać oryginalny identyfikator GUID klucz szyfrowania bloków. Utwórz folder na dysku C, o nazwie "Klucz szyfrowania bloków", przed wykonaniem tych kroków.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. Po plik klucza szyfrowania bloków jest tworzony na komputerze, skopiuj plik do zablokowanej dysku systemu operacyjnego, dołączony do maszyny Wirtualnej odzyskiwania. Uruchom następujące polecenia, korzystając z lokalizacji pliku klucza szyfrowania bloków.

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Opcjonalnie**: W niektórych scenariuszach może być wymagany do odszyfrowania dysku za pomocą tego polecenia.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > Poprzednie polecenie przyjęto założenie, że dysk do zaszyfrowania znajduje się w litery F.

8. Jeśli potrzebujesz dzienniki były zbierane, przejdź do ścieżki **litera dysku: \Windows\System32\winevt\Logs**.

9. Odłączanie dysku od maszyny odzyskiwania.

### <a name="create-a-snapshot"></a>Utwórz migawkę

Aby utworzyć migawkę, wykonaj kroki opisane w [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Zbieraj zrzut pamięci systemu operacyjnego

Użyj kroków w [os Zbierz zrzut](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) sekcji do zbierania zrzutu systemu operacyjnego, gdy maszyna wirtualna jest zablokowana podczas konfiguracji.

## <a name="contact-microsoft-support"></a>Skontaktuj się z pomocą techniczną firmy Microsoft

Po zebraniu pliku zrzutu, skontaktuj się z pomocą [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) do analizy głównej przyczyny problemu.


## <a name="rebuild-the-vm-by-using-powershell"></a>Ponownie utworzyć maszynę Wirtualną przy użyciu programu PowerShell

Po zebraniu w pliku zrzutu pamięci, wykonaj następujące kroki, aby ponownie utworzyć maszynę Wirtualną.

**W przypadku dysków niezarządzanych**

```PowerShell
# To log in to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID “SubscriptionID” | Select-AzureRmSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzureRmVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzureRmNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzureRmVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzureRmVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**Za dyski zarządzane**

```PowerShell
# To log in to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID "SubscriptionID" | Select-AzureRmSubscription

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

$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzureRmVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzureRmVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
