---
title: Tworzenie maszyny Wirtualnej z dysku specjalne na platformie Azure | Dokumentacja firmy Microsoft
description: Dołączanie specjalne dysku niezarządzane, w modelu wdrażania usługi Resource Manager, aby utworzyć nową maszynę Wirtualną.
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
ms.openlocfilehash: ffa36967eb987f5e1b66f007ae60a63e640a609a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Utwórz maszynę Wirtualną z wirtualnego dysku twardego specjalne konta magazynu

Tworzenie nowej maszyny Wirtualnej przez dołączenie dysku niezarządzane specjalne jako dysk systemu operacyjnego przy użyciu programu Powershell. Specjalne dysku jest kopią wirtualnego dysku twardego z istniejącej maszyny Wirtualnej, który przechowuje konta użytkowników, aplikacji i innych danych o stanie z oryginalnego maszyny Wirtualnej. 

Dostępne są dwie opcje:
* [Przekazywanie wirtualnego dysku twardego](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Skopiuj wirtualny dysk twardy z istniejącej maszyny Wirtualnej Azure](sa-create-vm-specialized.md#option-2-copy-an-existing-azure-vm)

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli używasz programu PowerShell, upewnij się, że masz najnowszą wersję modułu programu AzureRM.Compute PowerShell. Uruchom następujące polecenie, aby go zainstalować.

```powershell
Install-Module AzureRM.Compute 
```
Aby uzyskać więcej informacji, zobacz [przechowywanie wersji programu Azure PowerShell](/powershell/azure/overview).


## <a name="option-1-upload-a-specialized-vhd"></a>Opcja 1: Przekazanie specjalne dysku VHD

Możesz przekazać wirtualnego dysku twardego z specjalne utworzone za pomocą wirtualizacji narzędzia lokalnych, takich jak funkcja Hyper-V lub maszyny Wirtualnej wyeksportowane z innej maszyny Wirtualnej.

### <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej
Możesz przekazać specjalne wirtualnego dysku twardego, który został utworzony przy użyciu lokalnej maszyny Wirtualnej lub wirtualnego dysku twardego wyeksportowane z innej chmury. Specjalne wirtualnego dysku twardego przechowuje konta użytkowników, aplikacji i innych danych o stanie z oryginalnego maszyny Wirtualnej. Jeśli zamierzasz używać wirtualnego dysku twardego jako — jest, aby utworzyć nową maszynę Wirtualną, upewnij się, wykonywane są następujące czynności. 
  
  * [Przygotowywanie dysku VHD systemu Windows, aby przekazać do usługi Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Nie** generalize maszyny Wirtualnej za pomocą programu Sysprep.
  * Usuń wszystkie narzędzia wirtualizacji gościa i agentów, które są zainstalowane na maszynie Wirtualnej (np. narzędzi VMware).
  * Upewnij się, że maszyna wirtualna jest skonfigurowana do pobierania jego adres IP i ustawienia DNS za pośrednictwem protokołu DHCP. Daje to pewność, że serwer uzyskuje adres IP w sieci wirtualnej podczas uruchamiania. 


### <a name="get-the-storage-account"></a>Uzyskaj konto magazynu
Potrzebujesz konta magazynu na platformie Azure do przechowywania załadowanego obrazu maszyny Wirtualnej. Możesz użyć istniejącego konta magazynu lub Utwórz nową. 

Aby wyświetlić konta dostępny magazyn, wpisz:

```powershell
Get-AzureRmStorageAccount
```

Jeśli chcesz użyć istniejącego konta magazynu, przejdź do [przekazać obraz maszyny Wirtualnej](#upload-the-vm-vhd-to-your-storage-account) sekcji.

Jeśli musisz utworzyć konto magazynu, wykonaj następujące kroki:

1. Potrzebna jest nazwa grupy zasobów, w którym ma zostać utworzony na koncie magazynu. Aby dowiedzieć się, wszystkie grupy zasobów, które są w ramach subskrypcji, wpisz:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Aby utworzyć grupę zasobów o nazwie **myResourceGroup** w **zachodnie stany USA** regionu, wpisz:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Utwórz konto magazynu o nazwie **mojekontomagazynu** w tej grupie zasobów za pomocą [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) polecenia cmdlet:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Przekazanie dysku VHD do konta magazynu
Użyj [AzureRmVhd Dodaj](/powershell/module/azurerm.compute/add-azurermvhd) polecenia cmdlet, aby przekazać obraz do kontenera na koncie magazynu. W tym przykładzie powoduje przekazanie pliku **myVHD.vhd** z `"C:\Users\Public\Documents\Virtual hard disks\"` na konto magazynu o nazwie **mojekontomagazynu** w **myResourceGroup** grupy zasobów. Plik zostaną umieszczone w kontenerze o nazwie **mojkontener** i Nowa nazwa pliku będzie **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


W przypadku powodzenia można uzyskać odpowiedzi, która wygląda podobnie do poniższego:

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

W zależności od połączenia sieciowego i rozmiar pliku VHD to polecenie może zająć trochę czasu, aby zakończyć.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Opcja 2: Skopiuj wirtualny dysk twardy z istniejącej maszyny Wirtualnej Azure

Wirtualny dysk twardy można skopiować do innego konta magazynu do użycia podczas tworzenia nowego, zduplikowany maszyny Wirtualnej.

### <a name="before-you-begin"></a>Przed rozpoczęciem
Upewnij się, że możesz:

* Ma informacje o **konta magazynu źródłowego i docelowego**. Dla źródłowej maszyny Wirtualnej musisz mieć nazwy konta i kontener magazynu. Zazwyczaj nazwa kontenera będzie **wirtualne dyski twarde**. Należy również mieć konto magazynu docelowego. Jeśli nie masz jeszcze jeden, możesz utworzyć jedną przy użyciu albo portalu (**wszystkie usługi** > kont magazynu > Dodaj) lub przy użyciu [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) polecenia cmdlet. 
* Zostały pobrane i zainstalowane [narzędzia AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Cofnięcie przydziału maszyny Wirtualnej
Cofnięcie przydziału maszyny Wirtualnej, co zwalnia dysk VHD do skopiowania. 

* **Portal**: kliknij **maszyn wirtualnych** > **myVM** > Zatrzymaj
* **PowerShell**: Użyj [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) zatrzymania (deallocate) maszyny Wirtualnej o nazwie **myVM** w grupie zasobów **myResourceGroup**.

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

**Stan** dla maszyny Wirtualnej na platformie Azure portal zmieni się z **zatrzymane** do **zatrzymane (cofnięciu przydziału)**.

### <a name="get-the-storage-account-urls"></a>Uzyskiwanie adresów URL konta magazynu
Należy adresy URL konta magazynu źródłowego i docelowego. Adresy URL wygląda jak: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Jeśli znasz już nazwę konta i kontener magazynu, trzeba po prostu zastąpić informacji między nawiasy, aby utworzyć adres URL. 

Aby uzyskać adres URL, można użyć portalu Azure lub programu Azure Powershell:

* **Portal**: kliknij **>** dla **wszystkie usługi** > **kont magazynu** > *magazynu konto* > **obiekty BLOB** i pliku wirtualnego dysku twardego źródłowego jest prawdopodobnie w **wirtualne dyski twarde** kontenera. Kliknij przycisk **właściwości** dla kontenera, a następnie skopiuj tekst, którego etykietą jest **adres URL**. Będziesz potrzebować adresy URL kontenery źródłowym i docelowym. 
* **PowerShell**: Użyj [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) Aby uzyskać informacje dotyczące maszyny Wirtualnej o nazwie **myVM** w grupie zasobów **myResourceGroup**. W wynikach wyszukiwania **profilu magazynu** sekcji **identyfikator Uri dysku Vhd**. Pierwsza część identyfikatora Uri jest adres URL do kontenera i ostatniej części jest nazwa wirtualnego dysku twardego systemu operacyjnego dla maszyny Wirtualnej.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Uzyskaj klucze dostępu do magazynu
Znajdź klucze dostępu dla serwera źródłowego i docelowego konta magazynu. Aby uzyskać więcej informacji na temat kluczy dostępu, zobacz [kont magazynu Azure o](../../storage/common/storage-create-storage-account.md).

* **Portal**: kliknij **wszystkie usługi** > **kont magazynu** > *konta magazynu*  >   **Klucze dostępu**. Skopiuj klucz oznaczony jako **klucz1**.
* **PowerShell**: Użyj [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) można uzyskać klucza magazynu dla konta magazynu **mojekontomagazynu** w grupie zasobów **myResourceGroup**. Skopiuj klucz z etykietą **klucz1**.

```powershell
Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Skopiuj wirtualny dysk twardy
Możesz skopiować pliki między kontami magazynu przy użyciu narzędzia AzCopy. Docelowy kontener Jeśli określony kontener nie istnieje, jej zostanie utworzona automatycznie. 

Aby użyć narzędzia AzCopy, otwórz wiersz polecenia na komputerze lokalnym, a następnie przejdź do folderu, w którym zainstalowano narzędzia AzCopy. Powinien być podobny do *\Microsoft SDKs\Azure\AzCopy C:\Program Files (x86)*. 

Aby skopiować wszystkie pliki znajdujące się w kontenerze, należy użyć **/S** przełącznika. Może to służyć do Skopiuj wirtualny dysk twardy systemu operacyjnego i wszystkich dysków danych, jeśli są w tym samym kontenerze. W tym przykładzie pokazano, jak skopiować wszystkie pliki w kontenerze **mysourcecontainer** na koncie magazynu **mysourcestorageaccount** do kontenera **mydestinationcontainer**w **mydestinationstorageaccount** konta magazynu. Zamień nazwy konta magazynu i kontenery własne. Zastąp `<sourceStorageAccountKey1>` i `<destinationStorageAccountKey1>` z własnych kluczy.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Jeśli chcesz skopiować konkretnego dysku VHD w kontenerze z wieloma plikami, można również określić nazwę pliku za pomocą przełącznika /Pattern. W tym przykładzie tylko plik o nazwie **myFileName.vhd** zostaną skopiowane.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Po zakończeniu otrzymasz wiadomość, która wygląda:

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
* Korzystając z narzędzia AZCopy, jeśli zostanie wyświetlony błąd "Serwer nie można uwierzytelnić żądania", upewnij się, że wartość nagłówka autoryzacji jest sformatowany poprawnie tym podpisu. Jeśli używasz 2 klucza lub klucza pomocniczego magazynu, spróbuj użyć klucza podstawowego lub 1 magazynu.

## <a name="create-the-new-vm"></a>Tworzenie nowej maszyny Wirtualnej 

Należy utworzyć sieci i innych zasobów maszyny Wirtualnej do użycia przez nowej maszyny Wirtualnej.

### <a name="create-the-subnet-and-vnet"></a>Utwórz podsieć i sieć wirtualna

Utwórz sieć wirtualną i podsieć [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

1. Utwórz podsieć. W tym przykładzie tworzy podsieć o nazwie **mySubNet**, w grupie zasobów **myResourceGroup**i ustawia prefiks adresu podsieci **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Tworzenie sieci wirtualnej. W tym przykładzie nazwa sieci wirtualnej, który ma zostać **myVnetName**, lokalizacja **zachodnie stany USA**i prefiksu adresu dla sieci wirtualnej do **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Tworzenie grupy zabezpieczeń sieci i reguły protokołu RDP
Aby móc zalogować się do maszyny Wirtualnej za pomocą protokołu RDP, musisz mieć regułę zabezpieczeń, która pozwala dostępu RDP do portu 3389. Ponieważ wirtualny dysk twardy dla nowej maszyny Wirtualnej został utworzony z istniejącego specjalistyczne maszyny Wirtualnej, po utworzeniu maszyny Wirtualnej, możesz można Użyj istniejącego konta ze źródłowej maszyny wirtualnej, który ma uprawnienia do logowania się za pomocą protokołu RDP.
Musi to zostać wykonane przed utworzeniem interfejsu sieciowego, z którą zostanie skojarzona z.  
W tym przykładzie nazwa grupy NSG **myNsg** i nazwa reguły protokołu RDP do **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Aby uzyskać więcej informacji na temat punktów końcowych i reguły NSG, zobacz [Otwieranie portów dla maszyny Wirtualnej na platformie Azure przy użyciu programu PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Tworzenie publicznego adresu IP i karty Sieciowej
Aby umożliwić komunikację z maszyną wirtualną w sieci wirtualnej, potrzebujesz [publicznego adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) i interfejsu sieciowego.

1. Tworzenie publicznego adresu IP. W tym przykładzie ustawiono nazwa publicznego adresu IP **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Utwórz kartę sieciową. W tym przykładzie nazwa karty Sieciowej jest ustawiona **myNicName**. Ten krok powoduje również skojarzenie grupy zabezpieczeń sieci utworzony wcześniej z tej karty sieciowej.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Ustaw nazwę maszyny Wirtualnej i rozmiar

W tym przykładzie nazwa maszyny Wirtualnej na "myVM" i rozmiaru maszyny Wirtualnej "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Dodaj kartę Sieciową
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Skonfiguruj dysk systemu operacyjnego

1. Ustaw identyfikator URI dysku VHD, który został przekazany lub skopiowany. W tym przykładzie pliku wirtualnego dysku twardego o nazwie **myOsDisk.vhd** jest przechowywana na koncie magazynu o nazwie **Mojekontomagazynu** w kontenerze o nazwie **Mojkontener**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Dodaj dysk systemu operacyjnego. Gdy tworzony jest dysk systemu operacyjnego, w tym przykładzie termin "osDisk" jest appened na nazwę maszyny Wirtualnej, aby utworzyć nazwa dysku systemu operacyjnego. W tym przykładzie określa również, że ten dysku VHD systemu Windows musi być podłączona do maszyny Wirtualnej jako dysk systemu operacyjnego.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Opcjonalnie: Jeśli masz dysków z danymi, które muszą być podłączone do maszyny Wirtualnej, dodać dyski danych przy użyciu adresów URL danych wirtualne dyski twarde i odpowiedni numer jednostki logicznej (Lun).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Korzystając z konta magazynu, danych i adresy URL dysku systemu operacyjnego wyglądać mniej więcej tak: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. To w portalu można znaleźć przeglądanie do kontenera magazynu docelowego, klikając systemu operacyjnego lub dane wirtualnego dysku twardego, która została skopiowana, a następnie skopiować zawartość adresu URL.


### <a name="complete-the-vm"></a>Zakończenie maszyny Wirtualnej 

Utwórz maszynę Wirtualną przy użyciu konfiguracji, które właśnie utworzyliśmy.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Jeśli to polecenie zakończyło się pomyślnie, zostanie wyświetlone dane wyjściowe w następujący sposób:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Sprawdź, czy maszyna wirtualna została utworzona
Nowo utworzona maszyna wirtualna powinna być widoczna albo w [portalu Azure](https://portal.azure.com)w obszarze **wszystkie usługi** > **maszyn wirtualnych**, lub za pomocą następującego środowiska PowerShell polecenia:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Kolejne kroki
Aby zalogować się do nowej maszyny wirtualnej, przejdź do maszyny Wirtualnej w [portal](https://portal.azure.com), kliknij przycisk **Connect**i Otwórz plik RDP pulpitu zdalnego. Korzystać z poświadczeń konta oryginalnego maszyny wirtualnej, aby zalogować się do nowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [jak połączenia i zaloguj się do maszyny wirtualnej platformy Azure systemem Windows](connect-logon.md).

