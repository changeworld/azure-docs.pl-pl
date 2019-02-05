---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/25/2018
ms.author: cynthn
ms.openlocfilehash: de7d5ab73eb95e79cfca150f0692d5e793b16b76
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735543"
---
Aby uzyskać więcej informacji o dyskach, zobacz [About Disks and VHDs for Virtual Machines](../articles/virtual-machines/linux/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Informacje o dyskach i wirtualnych dyskach twardych dla maszyn wirtualnych).

<a id="attachempty"></a>

## <a name="attach-an-empty-disk"></a>Dołączanie pustego dysku
1. Otwórz klasyczny wiersza polecenia platformy Azure i [nawiązać połączenie z subskrypcją platformy Azure](/cli/azure/authenticate-azure-cli). Upewnij się, że jesteś w trybie usługi Azure Service Management (`azure config mode asm`).
2. Wprowadź polecenie `azure vm disk attach-new`, aby utworzyć i dołączyć nowy dysk, jak pokazano to w następującym przykładzie. Zastąp ciąg *myVM* nazwą swojej maszyny wirtualnej z systemem Linux i określ rozmiar dysku w GB, który w tym przykładzie wynosi *100 GB*:

    ```azurecli
    azure vm disk attach-new myVM 100
    ```

3. Po utworzeniu i dołączeniu dysku danych jest on widoczny w danych wyjściowych polecenia `azure vm disk list <virtual-machine-name>`, jak pokazano to w następującym przykładzie:
   
    ```azurecli
    azure vm disk list TestVM
    ```

    Dane wyjściowe są podobne do poniższego przykładu:

    ```bash
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        myVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

<a id="attachexisting"></a>

## <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku
Dołączanie istniejącego dysku wymaga pliku vhd dostępnego na koncie magazynu.

1. Otwórz klasyczny wiersza polecenia platformy Azure i [nawiązać połączenie z subskrypcją platformy Azure](/cli/azure/authenticate-azure-cli). Upewnij się, że jesteś w trybie usługi Azure Service Management (`azure config mode asm`).
2. Sprawdź, czy wirtualny dysk twardy, który chcesz dołączyć, został już przekazany do Twojej subskrypcji platformy Azure:
   
    ```azurecli
    azure vm disk list
    ```

    Dane wyjściowe są podobne do poniższego przykładu:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
    ```

3. Jeśli nie możesz znaleźć dysku, którego chcesz użyć, możesz przekazać do swojej subskrypcji lokalny wirtualny dysk twardy, używając polecenia `azure vm disk create` lub `azure vm disk upload`. Polecenie `disk create` mogłoby wyglądać tak jak w następującym przykładzie:
   
    ```azurecli
    azure vm disk create myVhd .\TempDisk\test.VHD -l "East US" -o Linux
    ```

    Dane wyjściowe są podobne do poniższego przykładu:

    ```azurecli
    info:    Executing command vm disk create
    + Retrieving storage accounts
    info:    VHD size : 10 GB
    info:    Uploading 10485760.5 KB
    Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
    info:    Finishing computing MD5 hash, 16% is complete.
    info:    https://mystorageaccount.blob.core.windows.net/disks/myVHD.vhd was
    uploaded successfully
    info:    vm disk create command OK
    ```
   
   Można także użyć polecenia `azure vm disk upload` do przekazania wirtualnego dysku twardego na konkretne konto magazynu. [Tutaj](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) możesz przeczytać więcej na temat poleceń do zarządzania dyskami danych maszyny wirtualnej platformy Azure.

4. Teraz dołącz żądany wirtualny dysk twardy do maszyny wirtualnej:
   
    ```azurecli
    azure vm disk attach myVM myVhd
    ```
   
   Pamiętaj, aby zastąpić ciąg *myVM* nazwą maszyny wirtualnej, a ciąg *myVHD* żądanym wirtualnym dyskiem twardym.

5. Możesz sprawdzić, czy dysk jest podłączony do maszyny wirtualnej, używając polecenia `azure vm disk list <virtual-machine-name>`:
   
    ```azurecli
    azure vm disk list myVM
    ```

    Dane wyjściowe są podobne do poniższego przykładu:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

> [!NOTE]
> Po dodaniu dysku danych musisz zalogować się na maszynę wirtualną i zainicjować dysk, aby maszyna wirtualna mogła używać tego dysku do przechowywania danych (informacje o inicjowaniu dysku znajdziesz w kolejnych krokach).
> 
> 

