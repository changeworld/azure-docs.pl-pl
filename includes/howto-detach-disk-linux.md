---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/25/2018
ms.author: cynthn
ms.openlocfilehash: 25e67a5a6c3abebbac2e0489ff636ca1457b62ea
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807734"
---
Gdy już nie potrzebujesz dysku danych dołączonego do maszyny wirtualnej, możesz go łatwo odłączyć. Po odłączeniu dysku od maszyny wirtualnej nie zostanie on usunięty z magazynu. Jeśli chcesz ponownie użyć danych znajdujących się na dysku, możesz dołączyć go ponownie do tej samej lub innej maszyny wirtualnej.  

> [!NOTE]
> Maszyna wirtualna na platformie Azure używa różnych typów dysku — dysku systemu operacyjnego, lokalnego dysku tymczasowego i opcjonalnych dysków danych. Szczegółowe informacje zawiera artykuł [About Disks and VHDs for Virtual Machines](../articles/virtual-machines/linux/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Informacje o dyskach i dyskach VHD maszyn wirtualnych). Nie możesz odłączyć dysku systemu operacyjnego, chyba że usuwasz także maszynę wirtualną.

## <a name="find-the-disk"></a>Wyszukiwanie dysku
Przed odłączeniem dysku od maszyny wirtualnej należy określić numer LUN, który jest identyfikatorem dysku do odłączenia. W tym celu wykonaj następujące kroki:

1. Otwórz interfejs wiersza polecenia platformy Azure i [nawiązać połączenie z subskrypcją platformy Azure](/cli/azure/authenticate-azure-cli). Upewnij się, że jesteś w trybie usługi Azure Service Management (`azure config mode asm`).
2. Dowiedz się, które dyski są dołączone do maszyny wirtualnej. Następujący przykład umożliwia wyświetlenie dysków maszyny wirtualnej o nazwie `myVM`:

    ```azurecli
    azure vm disk list myVM
    ```

    Dane wyjściowe są podobne do poniższego przykładu:

    ```azurecli
    * Fetching disk images
    * Getting virtual machines
    * Getting VM disks
      data:    Lun  Size(GB)  Blob-Name                         OS
      data:    ---  --------  --------------------------------  -----
      data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
      data:    0    30        myDataDisk.vhd
      info:    vm disk list command OK
    ```

3. Zapisz numer LUN, czyli **numer jednostki logicznej**, dysku, który chcesz odłączyć.

## <a name="remove-operating-system-references-to-the-disk"></a>Usuwanie odwołań systemu operacyjnego do dysku
Przed odłączeniem dysku od systemu Linux gościa upewnij się, że żadna partycja na dysku nie jest używana. Sprawdź, czy system operacyjny nie spróbuje ponownie ich zainstalować po ponownym uruchomieniu. Następujące kroki umożliwiają cofnięcie konfiguracji, którą prawdopodobnie utworzono podczas [dołączania](../articles/virtual-machines/linux/classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) dysku.

1. Użyj polecenia `lsscsi`, aby określić identyfikator dysku. Program `lsscsi` można zainstalować za pomocą polecenia `yum install lsscsi` (dystrybucje oparte na systemie Red Hat) lub `apt-get install lsscsi` (dystrybucje oparte na systemie Debian). Możesz określić identyfikator dysku, którego szukasz, przy użyciu numeru LUN. Ostatnia liczba w krotce w każdym wierszu to numer LUN. W następującym przykładzie polecenia `lsscsi` jednostka LUN 0 jest zamapowana na urządzenie */dev/sdc*

    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```

2. Użyj polecenia `fdisk -l <disk>`, aby określić partycje skojarzone z dyskiem, który ma zostać odłączony. Następujący przykład przedstawia dane wyjściowe dla urządzenia `/dev/sdc`:

    ```bash
    Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x5a1d2a1a
    
        Device Boot      Start         End      Blocks   Id  System
    /dev/sdc1            2048  2145386495  1072692224   83  Linux
    ```

3. Odinstaluj każdą z partycji wyświetlonych dla dysku. Następujący przykład umożliwia odinstalowanie urządzenia `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

4. Użyj polecenia `blkid`, aby określić identyfikatory UUID wszystkich partycji. Dane wyjściowe są podobne do poniższego przykładu:

    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

5. Usuń wpisy w pliku **/etc/fstab** skojarzone ze ścieżkami urządzeń lub identyfikatorami UUID wszystkich partycji dysku, który ma zostać odłączony.  Wpisy dla tego przykładu mogą być następujące:

    ```sh  
   UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
   ```

    lub
   
   ```sh   
   /dev/sdc1   /datadrive   ext4   defaults   1   2
   ```

## <a name="detach-the-disk"></a>Odłączanie dysku
Po określeniu numeru LUN dysku i usunięciu odwołań systemu operacyjnego można go odłączyć:

1. Odłącz wybrany dysk od maszyny wirtualnej za pomocą polecenia `azure vm disk detach
   <virtual-machine-name> <LUN>`. Następujący przykład umożliwia odłączenie dysku o numerze LUN `0` od maszyny wirtualnej o nazwie `myVM`:
   
    ```azurecli
    azure vm disk detach myVM 0
    ```

2. Możesz sprawdzić, czy dysk został odłączony, uruchamiając ponownie polecenie `azure vm disk list`. Następujący przykład umożliwia sprawdzenie maszyny wirtualnej o nazwie `myVM`:
   
    ```azurecli
    azure vm disk list myVM
    ```

    Dane wyjściowe są podobne do następującego przykładu przedstawiającego dysk danych, który nie jest już dołączony:

    ```azurecli
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     info:    vm disk list command OK
    ```

Odłączony dysk pozostaje w magazynie, lecz nie jest już dołączony do maszyny wirtualnej.

