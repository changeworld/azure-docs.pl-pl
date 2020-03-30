---
title: Tworzenie maszyny wirtualnej na specjalistycznym dysku na platformie Azure
description: Utwórz nową maszynę wirtualną, dołączając wyspecjalizowany dysk niezarządzany w modelu wdrażania Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: d887ef2ef74bb433d6e8ae7f53cd0b77f5948303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073348"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Tworzenie maszyny wirtualnej na podstawie wyspecjalizowanego dysku wirtualnego na koncie magazynu

Utwórz nową maszynę wirtualną, dołączając wyspecjalizowany dysk niezarządzany jako dysk systemu operacyjnego przy użyciu programu Powershell. Dysk specjalistyczny jest kopią dysku VHD z istniejącej maszyny Wirtualnej, która przechowuje konta użytkowników, aplikacje i inne dane o stanie z oryginalnej maszyny Wirtualnej. 

Dostępne są dwie opcje:
* [Przekazywanie wirtualnego dysku twardego](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Kopiowanie dysku twardego istniejącej maszyny Wirtualnej platformy Azure](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

 


## <a name="option-1-upload-a-specialized-vhd"></a>Opcja 1: Prześlij wyspecjalizowany VHD

Dysk VHD można przekazać ze specjalistycznej maszyny Wirtualnej utworzonej za pomocą lokalnego narzędzia do wirtualizacji, takiego jak funkcja Hyper-V, lub maszyny wirtualnej eksportowanej z innej chmury.

### <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej
Można przekazać wyspecjalizowany dysk wirtualny, który został utworzony przy użyciu lokalnej maszyny Wirtualnej lub dysku VHD eksportowanego z innej chmury. Wyspecjalizowany dysk wirtualny przechowuje konta użytkowników, aplikacje i inne dane o stanie z oryginalnej maszyny Wirtualnej. Jeśli zamierzasz użyć dysku wirtualnego w stanie as-is, aby utworzyć nową maszynę wirtualną, upewnij się, że następujące kroki zostały wykonane. 
  
  * [Przygotuj dysk VHD systemu Windows do przekazania na platformę Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Nie** uogólniaj maszyny Wirtualnej przy użyciu programu Sysprep.
  * Usuń wszystkie narzędzia wirtualizacji gości i agentów zainstalowanych na maszynie wirtualnej (tj. narzędzia VMware).
  * Upewnij się, że maszyna wirtualna jest skonfigurowana do ściągania jej adresu IP i ustawień DNS za pośrednictwem usługi DHCP. Gwarantuje to, że serwer uzyskuje adres IP w sieci wirtualnej podczas uruchamiania. 


### <a name="get-the-storage-account"></a>Pobierz konto magazynu
Do przechowywania przekazanego obrazu maszyny Wirtualnej potrzebne jest konto magazynu na platformie Azure. Można użyć istniejącego konta magazynu lub utworzyć nowe. 

Aby wyświetlić dostępne konta magazynu, wpisz:

```powershell
Get-AzStorageAccount
```

Jeśli chcesz użyć istniejącego konta magazynu, przejdź do sekcji Przekaż obraz maszyny Wirtualnej.

Jeśli chcesz utworzyć konto magazynu, wykonaj następujące czynności:

1. Potrzebna jest nazwa grupy zasobów, w której należy utworzyć konto magazynu. Aby dowiedzieć się, że wszystkie grupy zasobów znajdują się w subskrypcji, wpisz:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Aby utworzyć grupę zasobów o nazwie **myResourceGroup** w regionie **Zachodnie stany USA,** należy wpisać:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Utwórz konto magazynu o nazwie **mystorageaccount** w tej grupie zasobów przy użyciu polecenia cmdlet [New-AzStorageAccount:](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Przekazywanie wirtualnego dysku twardego do konta magazynu
Użyj polecenia cmdlet [Add-AzVhd,](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) aby przesłać obraz do kontenera na koncie magazynu. W tym przykładzie plik **myVHD.vhd** z `"C:\Users\Public\Documents\Virtual hard disks\"` konta magazynu o nazwie **mystorageaccount** w grupie zasobów **myResourceGroup.** Plik zostanie umieszczony w kontenerze o nazwie **mycontainer,** a nowa nazwa pliku będzie **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Jeśli się powiedzie, otrzymasz odpowiedź, która wygląda podobnie do tego:

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

W zależności od połączenia sieciowego i rozmiaru pliku VHD to polecenie może trochę potrwać.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Opcja 2: Kopiowanie dysku twardego vhd z istniejącej maszyny Wirtualnej platformy Azure

Dysk VHD można skopiować na inne konto magazynu, aby użyć go podczas tworzenia nowej, zduplikowanej maszyny Wirtualnej.

### <a name="before-you-begin"></a>Przed rozpoczęciem
Upewnij się, że:

* Posiadaj informacje o **kontach magazynu źródłowego i docelowego**. Dla źródłowej maszyny Wirtualnej, należy mieć konta magazynu i nazwy kontenerów. Zazwyczaj nazwa kontenera będzie **vhds**. Musisz również mieć konto magazynu docelowego. Jeśli jeszcze go nie masz, możesz go utworzyć za pomocą portalu **(Wszystkie konta magazynu** > magazynowania > Dodaj) lub przy użyciu polecenia cmdlet [New-AzStorageAccount.](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) 
* Pobrałem i zainstalowałem [narzędzie AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Cofnięto alokację maszyny Wirtualnej
Przydziel przydział maszyny Wirtualnej, co zwalnia dysk VHD do skopiowania. 

* **Portal**: Kliknij pozycję **Maszyny wirtualne** > **myVM** > Stop
* **Powershell:** Użyj [Stop-AzVM,](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) aby zatrzymać (cofnięto przydział) maszyny wirtualnej o nazwie **myVM** w grupie zasobów **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

**Stan** maszyny Wirtualnej w witrynie Azure portal zmienia się z **Zatrzymane** na **zatrzymane (cofnięte)**.

### <a name="get-the-storage-account-urls"></a>Pobierz adresy URL kont magazynu
Potrzebne są adresy URL kont magazynu źródłowego i docelowego. Adresy URL wyglądają `https://<storageaccount>.blob.core.windows.net/<containerName>/`następująco: . Jeśli znasz już konto magazynu i nazwę kontenera, możesz po prostu zastąpić informacje między nawiasami, aby utworzyć adres URL. 

Możesz użyć witryny Azure portal lub usługi Azure Powershell, aby uzyskać adres URL:

* **Portal**: **>** Kliknij dla **wszystkich usług** > **Konta** > *magazynu* > konta**obiektów Blobs** i źródłowy plik VHD jest prawdopodobnie w kontenerze **vhds.** Kliknij **pozycję Właściwości** kontenera i skopiuj adres **URL**oznaczony tekstem . Potrzebne będą adresy URL kontenerów źródłowych i docelowych. 
* **Powershell:** Użyj [get-AzVM,](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) aby uzyskać informacje o maszynie Wirtualnej o nazwie **myVM** w grupie zasobów **myResourceGroup**. W wynikach poszukaj w sekcji **Profil magazynu** **dla Vhd Uri**. Pierwsza część Uri jest adres URL do kontenera, a ostatnia część jest nazwa VHD systemu operacyjnego dla maszyny Wirtualnej.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Uzyskiwanie kluczy dostępu do magazynu
Znajdź klucze dostępu dla kont magazynu źródłowego i docelowego. Aby uzyskać więcej informacji na temat kluczy dostępu, zobacz [Informacje o kontach magazynu platformy Azure](../../storage/common/storage-create-storage-account.md).

* **Portal**: Kliknij pozycję **Wszystkie usługi** > **Klucze** > konta > *magazynu***magazynu usługi .** Skopiuj klucz oznaczony jako **key1**.
* **Powershell:** Użyj [Get-AzStorageAccountKey,](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) aby uzyskać klucz magazynu dla konta magazynu **mystorageaccount** w grupie zasobów **myResourceGroup**. Skopiuj klucz oznaczony **jako key1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Kopiowanie dysku VHD
Pliki można kopiować między kontami magazynu przy użyciu programu AzCopy. Dla kontenera docelowego, jeśli określony kontener nie istnieje, zostanie utworzony dla Ciebie. 

Aby użyć polecenia AzCopy, otwórz wiersz polecenia na komputerze lokalnym i przejdź do folderu, w którym jest zainstalowany program AzCopy. Będzie podobny do *C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy*. 

Aby skopiować wszystkie pliki w kontenerze, należy użyć przełącznika **/S.** Może to służyć do kopiowania dysku VHD systemu operacyjnego i wszystkich dysków z danymi, jeśli znajdują się one w tym samym kontenerze. W tym przykładzie pokazano, jak skopiować wszystkie pliki w kontenerze **mysourcecontainer** w koncie magazynu **mysourcestorageaccount** do **kontenera mydestinationcontainer** na koncie magazynu **mydestinationstoragecount.** Zastąp nazwy kont magazynu i kontenerów własnymi. Wymień `<sourceStorageAccountKey1>` i `<destinationStorageAccountKey1>` za pomocą własnych kluczy.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Jeśli chcesz skopiować tylko określony dysk VHD w kontenerze z wieloma plikami, można również określić nazwę pliku za pomocą przełącznika /Pattern. W tym przykładzie tylko plik o nazwie **myFileName.vhd** zostanie skopiowany.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Po zakończeniu otrzymasz komunikat, który wygląda mniej więcej tak:

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
* W przypadku korzystania z AZCopy, jeśli zostanie wyświetlony błąd "Serwer nie może uwierzytelnić żądania", upewnij się, że wartość nagłówka Autoryzacja jest poprawnie sformułowana, w tym podpis. Jeśli używasz klucza 2 lub pomocniczego klucza magazynu, spróbuj użyć podstawowego lub pierwszego klucza magazynu.

## <a name="create-the-new-vm"></a>Tworzenie nowej maszyny Wirtualnej 

Należy utworzyć sieci i innych zasobów maszyny Wirtualnej, które mają być używane przez nową maszynę wirtualną.

### <a name="create-the-subnet-and-vnet"></a>Tworzenie podsieci i sieci wirtualnej

Utwórz sieć wirtualną i podsieć [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

1. Utwórz podsieć. W tym przykładzie utworzysz podsieć o nazwie **mySubNet**w grupie zasobów **myResourceGroup**i ustawi prefiks adresu podsieci na **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Utwórz sieć vNet. W tym przykładzie nazwa sieci wirtualnej jest **myVnetName**, lokalizacja do **zachodnie stany USA**, a prefiks adresu dla sieci wirtualnej do **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Tworzenie sieciowej grupy zabezpieczeń i reguły RDP
   Aby móc zalogować się do maszyny Wirtualnej przy użyciu protokołu RDP, musisz mieć regułę zabezpieczeń, która umożliwia dostęp do protokołu RDP na porcie 3389. Ponieważ dysk VHD dla nowej maszyny Wirtualnej został utworzony na podstawie istniejącej wyspecjalizowanej maszyny Wirtualnej, po utworzeniu maszyny wirtualnej można użyć istniejącego konta ze źródłowej maszyny wirtualnej, która miała uprawnienia do logowania się przy użyciu protokołu RDP.
   Należy to zakończyć przed utworzeniem interfejsu sieciowego, z który będzie skojarzony.  
   W tym przykładzie ustawia nazwę NSG na **myNsg** i nazwę reguły RDP na **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Aby uzyskać więcej informacji na temat punktów końcowych i reguł sieciowej sieciowej, zobacz [Otwieranie portów na maszynie wirtualnej na platformie Azure przy użyciu programu PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Tworzenie publicznego adresu IP i karty sieciowej
Aby umożliwić komunikację z maszyną wirtualną w sieci wirtualnej, potrzebujesz [publicznego adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) i interfejsu sieciowego.

1. Utwórz publiczny adres IP. W tym przykładzie nazwa publicznego adresu IP jest ustawiona na **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Utwórz kartę sieciową. W tym przykładzie nazwa karty sieciowej jest ustawiona na **myNicName**. Ten krok kojarzy również grupę zabezpieczeń sieci utworzoną wcześniej z tą kartą sieciową.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Ustawianie nazwy i rozmiaru maszyny Wirtualnej

W tym przykładzie nazwa maszyny Wirtualnej ma na "myVM", a rozmiar maszyny Wirtualnej na "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Dodawanie karty sieciowej
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Konfigurowanie dysku systemu operacyjnego

1. Ustaw identyfikator URI dla dysku VHD, który został przekazany lub skopiowany. W tym przykładzie plik VHD o nazwie **myOsDisk.vhd** jest przechowywany na koncie magazynu o nazwie **myStorageAccount** w kontenerze o nazwie **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Dodaj dysk systemu operacyjnego. W tym przykładzie podczas tworzenia dysku systemu operacyjnego termin "osDisk" jest dołączany do nazwy maszyny Wirtualnej w celu utworzenia nazwy dysku systemu operacyjnego. W tym przykładzie określono również, że ten dysk VHD oparty na systemie Windows powinien być dołączony do maszyny Wirtualnej jako dysk systemu operacyjnego.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Opcjonalnie: Jeśli masz dyski danych, które muszą być dołączone do maszyny Wirtualnej, dodaj dyski danych przy użyciu adresów URL danych VHD i odpowiedniego numeru jednostki logicznej (Lun).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Podczas korzystania z konta magazynu adresy URL danych i `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`dysków systemu operacyjnego wyglądają mniej więcej tak: . Można to znaleźć w portalu, przeglądając docelowy kontener magazynu, klikając system operacyjny lub dane VHD, który został skopiowany, a następnie kopiując zawartość adresu URL.


### <a name="complete-the-vm"></a>Ukończ maszynę wirtualną 

Utwórz maszynę wirtualną przy użyciu konfiguracji, które właśnie utworzono.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Jeśli to polecenie zakończyło się pomyślnie, zobaczysz dane wyjściowe w ten sposób:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Sprawdź, czy maszyna wirtualna została utworzona
Nowo utworzona maszyna wirtualna powinna zostać wyświetlona w [witrynie Azure portal](https://portal.azure.com), w obszarze **Wszystkie usługi** > **maszyny wirtualne**lub przy użyciu następujących poleceń programu PowerShell:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Następne kroki
Zaloguj się do nowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Jak połączyć się i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](connect-logon.md).

