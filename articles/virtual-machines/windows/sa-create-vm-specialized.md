---
title: Tworzenie maszyny Wirtualnej na podstawie wyspecjalizowanego dysku na platformie Azure | Dokumentacja firmy Microsoft
description: Tworzenie nowej maszyny Wirtualnej przez dołączenie wyspecjalizowanego dysku niezarządzanego, w modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: f2110a749c41f59b11a6d400faa2e42e751305fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251075"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Tworzenie maszyny Wirtualnej na podstawie wyspecjalizowanego wirtualnego dysku twardego w ramach konta magazynu

Tworzenie nowej maszyny Wirtualnej przez dołączenie wyspecjalizowanego dysku niezarządzanego jako dysk systemu operacyjnego przy użyciu programu Powershell. Wyspecjalizowanego dysku jest kopii wirtualnego dysku twardego z istniejącej maszyny Wirtualnej, który przechowuje konta użytkowników, aplikacji i inne dane o stanie z oryginalną maszynę Wirtualną. 

Dostępne są dwie opcje:
* [Przekazywanie wirtualnego dysku twardego](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Skopiuj wirtualny dysk twardy z istniejącej maszyny Wirtualnej platformy Azure](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="option-1-upload-a-specialized-vhd"></a>Opcja 1: Przekazywanie wyspecjalizowanego wirtualnego dysku twardego

Możesz przekazać wirtualny dysk twardy z wyspecjalizowanej maszyny Wirtualnej utworzone za pomocą wirtualizacji narzędzia lokalnych, takich jak funkcji Hyper-V lub maszyny Wirtualnej wyeksportowane z innej chmury.

### <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej
Możesz przekazać wyspecjalizowanego wirtualnego dysku twardego, który został utworzony przy użyciu lokalnej maszyny Wirtualnej lub wirtualnego dysku twardego wyeksportowane z innej chmury. Wyspecjalizowanego wirtualnego dysku twardego przechowuje konta użytkowników, aplikacji i inne dane o stanie z oryginalną maszynę Wirtualną. Jeśli zamierzasz używać wirtualnego dysku twardego jako — jest, aby utworzyć nową maszynę Wirtualną, upewnij się, są wykonywane następujące czynności. 
  
  * [Przygotowywanie wirtualnego dysku twardego Windows do przekazania na platformę Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Nie** uogólnianie maszyny Wirtualnej za pomocą programu Sysprep.
  * Usuń wszelkie gościa wirtualizacji narzędzi i agentów, które są zainstalowane na maszynie Wirtualnej (np. narzędzi VMware).
  * Upewnij się, że maszyna wirtualna jest skonfigurowana do pobierania adresu IP i ustawienia DNS za pośrednictwem protokołu DHCP. Daje to gwarancję, że serwer uzyskuje adres IP w sieci wirtualnej podczas uruchamiania. 


### <a name="get-the-storage-account"></a>Pobierz konto magazynu
Wymagane jest konto magazynu na platformie Azure do przechowywania przekazanego obrazu maszyny Wirtualnej. Możesz użyć istniejącego konta magazynu lub Utwórz nową. 

Aby wyświetlić konta dostępnego magazynu, wpisz:

```powershell
Get-AzStorageAccount
```

Jeśli chcesz użyć istniejącego konta magazynu, przejdź do przekazywania sekcję obrazu maszyny Wirtualnej.

Jeśli musisz utworzyć konto magazynu, wykonaj następujące czynności:

1. Potrzebna jest nazwa grupy zasobów, tworzona na koncie magazynu. Aby dowiedzieć się, wszystkie grupy zasobów, które znajdują się w Twojej subskrypcji, wpisz:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Aby utworzyć grupę zasobów o nazwie **myResourceGroup** w **zachodnie stany USA** regionu, wpisz:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Utwórz konto magazynu o nazwie **mystorageaccount** w tej grupie zasobów przy użyciu [New AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) polecenia cmdlet:
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Przekazanie dysku VHD do konta magazynu
Użyj [AzVhd Dodaj](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) polecenia cmdlet w celu przekazania obrazu do kontenera na koncie magazynu. Ten przykładowy przekazuje plik **myVHD.vhd** z `"C:\Users\Public\Documents\Virtual hard disks\"` na konto magazynu o nazwie **mystorageaccount** w **myResourceGroup** grupy zasobów. Plik zostanie umieszczony w kontenerze o nazwie **mycontainer** nową nazwę pliku. zostanie ona **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Jeśli to się powiedzie, otrzymasz odpowiedź, która wygląda podobnie do następującej:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

W zależności od połączenia sieciowego i rozmiar pliku wirtualnego dysku twardego tego polecenia może potrwać trochę czasu.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Opcja 2: Skopiuj wirtualny dysk twardy z istniejącej maszyny Wirtualnej platformy Azure

Wirtualny dysk twardy można skopiować do innego konta magazynu do użycia podczas tworzenia nowego, zduplikowane maszyny Wirtualnej.

### <a name="before-you-begin"></a>Przed rozpoczęciem
Upewnij się, że możesz:

* Udostępniają informacje o **źródłowych i docelowych kont magazynu**. Dla źródłowej maszyny Wirtualnej musisz mieć nazwy konta i kontenera magazynu. Zwykle będzie nazwa kontenera **wirtualne dyski twarde**. Musisz również mieć docelowe konto magazynu. Jeśli nie masz jeszcze jeden, możesz utworzyć ją przy użyciu zarówno w portalu (**wszystkich usług** > konta magazynu > Dodaj) lub za pomocą [New AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) polecenia cmdlet. 
* Pobrano i zainstalowano [narzędzie AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Cofnij Przydział maszyny Wirtualnej
Cofnij Przydział maszyny Wirtualnej, co zwalnia wirtualnego dysku twardego do skopiowania. 

* **Portal**: Kliknij przycisk **maszyn wirtualnych** > **myVM** > Zatrzymaj
* **Program PowerShell**: Użyj [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) zatrzymania (Cofnij ich przydział) maszyny Wirtualnej o nazwie **myVM** w grupie zasobów **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

**Stan** dla maszyny Wirtualnej w witrynie Azure portal zmieni się z **zatrzymane** do **zatrzymane (cofnięty przydział)**.

### <a name="get-the-storage-account-urls"></a>Uzyskaj adresy URL konta magazynu
Należy adresy URL konta magazynu źródłowego i docelowego. Jak wygląda adresów URL: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Jeśli znasz już nazwę konta i kontenera magazynu, można po prostu zastąpić informacji między nawiasami, aby utworzyć adres URL. 

Aby uzyskać adres URL, można użyć witryny Azure portal lub programu Azure Powershell:

* **Portal**: Kliknij przycisk **>** dla **wszystkich usług** > **kont magazynu** > *konta magazynu*  >  **Obiektów blob** i pliku wirtualnego dysku twardego źródłowego jest prawdopodobnie w **wirtualne dyski twarde** kontenera. Kliknij przycisk **właściwości** dla kontenerów i skopiuj tekst etykietą **adresu URL**. Potrzebna będzie adresy URL kontenery źródłowym i docelowym. 
* **Program PowerShell**: Użyj [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) Aby uzyskać informacje dotyczące maszyny Wirtualnej o nazwie **myVM** w grupie zasobów **myResourceGroup**. W wynikach wyszukiwania **profil magazynu** sekcji **identyfikator Uri dysku Vhd**. Pierwsza część identyfikatora Uri jest adres URL do kontenera, a drugi fragment to nazwa wirtualnego dysku twardego systemu operacyjnego dla maszyny Wirtualnej.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Pobieranie kluczy dostępu do magazynu
Znajdź klucze dostępu dla źródłowego i docelowego konta magazynu. Aby uzyskać więcej informacji na temat kluczy dostępu, zobacz [kontach magazynu Azure o](../../storage/common/storage-create-storage-account.md).

* **Portal**: Kliknij przycisk **wszystkich usług** > **kont magazynu** > *konta magazynu* > **kluczedostępu**. Skopiuj klucz oznaczony etykietą **klucz1**.
* **Program PowerShell**: Użyj [Get AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) można pobrać klucza magazynu dla konta magazynu **mystorageaccount** w grupie zasobów **myResourceGroup**. Skopiuj klucz etykietą **klucz1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Skopiuj wirtualny dysk twardy
Można kopiować pliki między kontami magazynu przy użyciu narzędzia AzCopy. Kontenera docelowego jeśli określony kontener nie istnieje, jego zostanie utworzony dla Ciebie. 

Aby użyć narzędzia AzCopy, otwórz wiersz polecenia na komputerze lokalnym, a następnie przejdź do folderu, w którym zainstalowano narzędzia AzCopy. Będą wyglądać mniej więcej *\Microsoft SDKs\Azure\AzCopy C:\Program Files (x86)*. 

Aby skopiować wszystkie pliki znajdujące się w kontenerze, należy użyć **/S** przełącznika. To może służyć do skopiowania wirtualnego dysku twardego systemu operacyjnego i dyski danych, jeśli są w tym samym kontenerze. W tym przykładzie pokazano, jak skopiować wszystkie pliki w kontenerze **mysourcecontainer** na koncie magazynu **mysourcestorageaccount** do kontenera **mydestinationcontainer**w **mydestinationstorageaccount** konta magazynu. Nazwy kont magazynu i kontenerów Zastąp własnymi. Zastąp `<sourceStorageAccountKey1>` i `<destinationStorageAccountKey1>` za pomocą własnych kluczy.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Jeśli chcesz skopiować konkretnego dysku VHD w kontenerze za pomocą wielu plików, można również określić nazwę pliku, za pomocą przełącznika /Pattern. W tym przykładzie plik o nazwie **myFileName.vhd** zostaną skopiowane.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Po jej zakończeniu zostanie wyświetlony komunikat, który wygląda podobnie:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Rozwiązywanie problemów
* Korzystając z narzędzia AZCopy, jeśli zostanie wyświetlony błąd "Serwer nie powiodło się uwierzytelnienie żądania", upewnij się, że wartość nagłówka autoryzacji został utworzony prawidłowo tym podpis. Jeśli używane są 2 klucza lub klucza pomocniczego magazynu, spróbuj użyć klucza podstawowego lub 1. magazynu.

## <a name="create-the-new-vm"></a>Utwórz nową maszynę Wirtualną 

Musisz utworzyć sieci i innych zasobów maszyny Wirtualnej, który będzie używany przez nową maszynę Wirtualną.

### <a name="create-the-subnet-and-vnet"></a>Tworzenie podsieci i sieci wirtualnej

Tworzenie sieci wirtualnej i podsieci [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

1. Utwórz podsieć. W tym przykładzie tworzy podsieć o nazwie **mySubNet**, w grupie zasobów **myResourceGroup**i ustawia prefiks adresu podsieci **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Tworzenie sieci wirtualnej. W tym przykładzie nazwa sieci wirtualnej, który ma zostać **myVnetName**, lokalizację **zachodnie stany USA**i prefiksu adresu dla sieci wirtualnej do **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Tworzenie sieciowej grupy zabezpieczeń i regułę protokołu RDP
   Aby można było zalogować się do maszyny Wirtualnej przy użyciu protokołu RDP, musisz mieć reguły zabezpieczeń, która umożliwia dostęp do portu 3389 protokołu RDP. Ponieważ wirtualnego dysku twardego dla nowej maszyny Wirtualnej został utworzony na podstawie istniejącego wyspecjalizowanej maszyny Wirtualnej, po utworzeniu maszyny Wirtualnej, możesz użyć istniejącego konta ze źródłowej maszyny wirtualnej, który ma uprawnienia do logowania się przy użyciu protokołu RDP.
   To musi zostać ukończona przed utworzeniem interfejsu sieciowego, które zostaną skojarzone z.  
   W tym przykładzie nazwa sieciowej grupy zabezpieczeń w **myNsg** i nazwa reguły protokołu RDP do **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Aby uzyskać więcej informacji na temat punktów końcowych i reguł sieciowej grupy zabezpieczeń, zobacz [Otwieranie portów dla maszyny Wirtualnej na platformie Azure przy użyciu programu PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Tworzenie publicznego adresu IP i karty Sieciowej
Aby umożliwić komunikację z maszyną wirtualną w sieci wirtualnej, potrzebujesz [publicznego adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) i interfejsu sieciowego.

1. Tworzenie publicznego adresu IP. W tym przykładzie nazwa publicznego adresu IP jest ustawiona na **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Utwórz kartę sieciową. W tym przykładzie nazwa karty interfejsu Sieciowego jest ustawiona na **myNicName**. Ten krok powoduje również skojarzenie sieciowej grupy zabezpieczeń utworzonej wcześniej przy użyciu tej karty sieciowej.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Ustaw nazwę maszyny Wirtualnej i rozmiar

W tym przykładzie nazwa maszyny Wirtualnej na "myVM" i rozmiaru maszyny Wirtualnej "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Dodawanie karty Sieciowej
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Konfigurowanie dysku systemu operacyjnego

1. Ustaw identyfikator URI dysku VHD, który został przekazany lub skopiowany. W tym przykładzie plik wirtualnego dysku twardego o nazwie **myOsDisk.vhd** są przechowywane na koncie magazynu o nazwie **myStorageAccount** w kontenerze o nazwie **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Dodaj dysk systemu operacyjnego. W tym przykładzie tworzony jest dysk systemu operacyjnego, termin "osDisk" jest dołączany do nazwy maszyny Wirtualnej można utworzyć nazwy dysku systemu operacyjnego. Ten przykład pokazuje również, że ten dysk VHD z systemem Windows musi być podłączona do maszyny Wirtualnej jako dysk systemu operacyjnego.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Opcjonalnie: Jeśli masz dyski z danymi, które musi zostać dołączony do maszyny Wirtualnej, należy dodać dyski z danymi przy użyciu adresów URL danych wirtualne dyski twarde i odpowiedni numer jednostki logicznej (Lun).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Podczas korzystania z konta magazynu danych i adresy URL dysku systemu operacyjnego wyglądać mniej więcej tak: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. To w portalu można znaleźć, przechodząc do kontenera magazynu docelowego, klikając systemu operacyjnego lub dane wirtualnego dysku twardego, który został skopiowany, a następnie skopiować zawartość adresu URL.


### <a name="complete-the-vm"></a>Wykonaj maszyny Wirtualnej 

Tworzenie maszyny Wirtualnej przy użyciu konfiguracji, które właśnie utworzyliśmy.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Jeśli to polecenie zakończyło się pomyślnie, zostanie wyświetlone następujące dane wyjściowe:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Sprawdź, czy maszyna wirtualna została utworzona
Powinien zostać wyświetlony nowo utworzonej maszyny Wirtualnej albo w [witryny Azure portal](https://portal.azure.com)w obszarze **wszystkich usług** > **maszyn wirtualnych**, lub używając następujące polecenie programu PowerShell polecenia:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Kolejne kroki
Zaloguj się do swojej nowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [jak połączyć i zaloguj się na maszynie wirtualnej platformy Azure, systemem Windows](connect-logon.md).

