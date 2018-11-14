---
title: Uruchamianie maszyny wirtualnej zatrzymuje się na komunikacie „Przygotowywanie systemu Windows. Nie wyłączaj komputera”. na platformie Azure | Dokumentacja firmy Microsoft
description: Wprowadzenie kroki, aby rozwiązać ten problem, w jakiej maszynie Wirtualnej uruchamiania jest zablokowany na "Windows przygotowanie. Nie wyłączaj komputera".
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
ms.openlocfilehash: 2bcdb2b458327a5e87dc36e4a5f50f0ac46bf96a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621041"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Uruchamianie maszyny wirtualnej zatrzymuje się na komunikacie „Przygotowywanie systemu Windows. Nie wyłączaj komputera”. na platformie Azure

Ten artykuł pomoże Ci rozwiązać ten problem, gdy utkwiła Twojej maszyny wirtualnej (VM) na "Windows przygotowanie. Nie wyłączaj komputera”. etap podczas uruchamiania.

## <a name="symptoms"></a>Objawy

Kiedy używasz **diagnostykę rozruchu** można pobrać zrzut ekranu maszyny wirtualnej, możesz znaleźć, system operacyjny nie pełni uruchomiona. Ponadto maszyna wirtualna wyświetlenie **"Przygotowanie Windows. Nie wyłączaj komputera."** Komunikat.

![Przykładowy komunikat](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Przykładowy komunikat](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Przyczyna

Zazwyczaj ten problem występuje, gdy serwer wykonuje końcowe ponowny rozruch po konfiguracji został zmieniony. Zmiana konfiguracji można zainicjować przez aktualizacje Windows lub zmiany na temat ról/funkcji serwera. For Windows Update Jeśli długą rozmiaru aktualizacji systemu operacyjnego należy więcej czasu, aby zmienić konfigurację zmiany.

## <a name="back-up-the-os-disk"></a>Tworzenie kopii zapasowej dysku systemu operacyjnego

Przed podjęciem próby rozwiązania problemu, należy wykonać kopię zapasową dysku systemu operacyjnego:

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>W przypadku maszyn wirtualnych z zaszyfrowanego dysku musisz odblokować dyski najpierw

Sprawdź, czy maszyna wirtualna jest zaszyfrowanej maszyny Wirtualnej. W tym celu wykonaj następujące kroki:

1. W portalu Otwórz maszyny Wirtualnej, a następnie przejdź do dysków.

2. Zobaczysz wywołanie "Szyfrowanie", który informuje o włączenie szyfrowania kolumny.

Jeśli dysk systemu operacyjnego jest szyfrowany, odblokuj zaszyfrowanego dysku. W tym celu wykonaj następujące kroki:

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

5. Po nazwie klucza tajnego, uruchom następujące polecenia w programie PowerShell:

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Konwertuj wartość zakodowane w formacie Base64 bajtów i zapisać dane wyjściowe do pliku. 

    > [!Note]
    > Nazwa pliku klucza szyfrowania bloków musi odpowiadać oryginalny klucz szyfrowania bloków identyfikator GUID, jeśli używasz USB odblokować opcji. Ponadto należy utworzyć folder na dysku C, o nazwie "Klucz szyfrowania bloków", zanim będą działać przez następujące kroki.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. Po plik klucza szyfrowania bloków jest tworzony na komputerze, skopiuj plik do zablokowanej dysku systemu operacyjnego, dołączony do maszyny Wirtualnej odzyskiwania. Uruchom następujące polecenie, przy użyciu klucza szyfrowania bloków lokalizacja pliku:

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Opcjonalnie** w niektórych scenariuszach może być konieczne, również odszyfrowywania dysku za pomocą tego polecenia.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > To polega na uwzględnieniu czy dysk do zaszyfrowania znajduje się na literę F:.

8. Jeśli potrzebujesz dzienniki były zbierane, możesz przejść do ścieżki **litera dysku: \Windows\System32\winevt\Logs**.

9. Odłączanie dysku od maszyny odzyskiwania.

### <a name="create-a-snapshot"></a>Utwórz migawkę

Aby utworzyć migawkę, wykonaj kroki opisane w [Tworzenie migawki dysku](..\windows\snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Zbieraj zrzut pamięci systemu operacyjnego

Użyj kroków w [os Zbierz zrzut](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) sekcji do zbierania zrzutu systemu operacyjnego, gdy maszyna wirtualna jest zablokowana podczas konfiguracji.

## <a name="contact-microsoft-support"></a>Skontaktuj się z pomocą techniczną firmy Microsoft

Po zebraniu pliku zrzutu, skontaktuj się z pomocą [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) do analizy głównej przyczyny problemu.


## <a name="rebuild-the-vm-using-powershell"></a>Ponownie skompiluj maszyny Wirtualnej przy użyciu programu PowerShell

Po zebraniu w pliku zrzutu pamięci Użyj następujących kroków, aby odbudować maszyny Wirtualnej.

**W przypadku dysków niezarządzanych**

```PowerShell
# To login to Azure Resource Manager
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
# To login to Azure Resource Manager
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

#This can be found by selecting the Managed Disks you wish you use in the Azure Portal if the format below doesn't match
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
