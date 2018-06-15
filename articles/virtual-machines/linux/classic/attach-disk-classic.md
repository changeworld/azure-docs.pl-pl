---
title: Dołączanie dysku do maszyny Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można dołączyć dysku danych do maszyny Wirtualnej systemu Linux przy użyciu klasycznego modelu wdrożenia i Zainicjuj dysk, dlatego jest gotowa do użycia
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 4901384d-2a6f-4f46-bba0-337a348b7f87
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 07b7f16cc019f993df90b217abd0c7ad16db75e1
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942221"
---
# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Jak można dołączyć dysku danych do maszyny wirtualnej systemu Linux
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Zobacz temat jak [dołączenie dysku danych przy użyciu modelu wdrażania usługi Resource Manager](../add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Możesz dołączyć zarówno pustych dysków i dysków, które zawierają dane na maszynach wirtualnych platformy Azure. Oba typy dysków są pliki VHD, które znajdują się na koncie magazynu platformy Azure. Jako dodawania każdego dysku do maszyny z systemem Linux po podłączyć dysk należy zainicjować i sformatować go, dlatego jest gotowa do użycia. Szczegóły tego artykułu dołączanie zarówno puste dyski i już zawierający dane do maszyn wirtualnych, a także sposób następnie zainicjować i sformatować nowy dysk.

> [!NOTE]
> Jest najlepszym rozwiązaniem jest użycie oddzielnych dyskach co najmniej jeden do przechowywania danych maszyny wirtualnej. Podczas tworzenia maszyny wirtualnej platformy Azure ma dysk systemu operacyjnego i dysku tymczasowym. **Nie używaj tymczasowych dysku do przechowywania danych.** Jak wskazuje nazwę, zawiera tylko magazyn tymczasowy. Oferuje nie nadmiarowość lub kopii zapasowej, ponieważ go nie znajdują się w magazynie Azure.
> Tymczasowe dysk jest zwykle zarządzane przez agenta systemu Linux platformy Azure i automatycznie zainstalowany **/mnt/zasobu** (lub **katalogu/mnt** na obrazach Ubuntu). Z drugiej strony, dysku danych może mieć nazwę jądra systemu Linux przypominać `/dev/sdc`, i trzeba partycji, formatowanie i zainstalowania tego zasobu. Zobacz [Podręcznik użytkownika agenta systemu Linux Azure] [ Agent] szczegółowe informacje.
> 
> 

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Zainicjuj nowy dysk danych w systemie Linux
1. SSH do maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [jak zalogować się do maszyny wirtualnej z systemem Linux][Logon].
2. Następnie musisz znaleźć identyfikator urządzenia dla dysku danych do zainicjowania. Istnieją dwa sposoby, w tym:
   
    ) Grep urządzeń SCSI w dziennikach, taki jak następujące polecenie:
   
    ```bash
    sudo grep SCSI /var/log/messages
    ```
   
    Ostatnie Ubuntu dystrybucji, należy użyć `sudo grep SCSI /var/log/syslog` ponieważ rejestrowania `/var/log/messages` mogą domyślnie wyłączone.
   
    Identyfikator ostatniego dysk danych, która została dodana w wiadomości, które są wyświetlane można znaleźć.
   
    ![Pobieranie wiadomości dysku](./media/attach-disk/scsidisklog.png)
   
    LUB
   
    b) użyj `lsscsi` polecenie, aby dowiedzieć się, identyfikator urządzenia. `lsscsi` mogą być instalowane przez albo `yum install lsscsi` (Red Hat podstawę dystrybucje) lub `apt-get install lsscsi` (Debian podstawę dystrybucji). Można znaleźć dysku, którego szukasz przez jego *lun* lub **numeru jednostki logicznej**. Na przykład *lun* dla dysków dołączonych można łatwo wynika z `azure vm disk list <virtual-machine>` jako:

    ```azurecli
    azure vm disk list myVM
    ```

    Dane wyjściowe będą podobne do następujących:

    ```azurecli
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                         OS
    data:    ---  --------  --------------------------------  -----
    data:         30        myVM-2645b8030676c8f8.vhd  Linux
    data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
    info:    vm disk list command OK
    ```
   
    Porównaj te dane przy użyciu danych wyjściowych `lsscsi` dla tych samych próbkach maszyny wirtualnej:
   
    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```
   
    Ostatni numer w spójnej kolekcji w każdym wierszu jest *lun*. Zobacz `man lsscsi` Aby uzyskać więcej informacji.
3. W wierszu polecenia wpisz następujące polecenie, aby utworzyć urządzenie:
   
    ```bash
    sudo fdisk /dev/sdc
    ```

4. Po wyświetleniu monitu wpisz **n** do utworzenia partycji.

    ![Utwórz urządzenie](./media/attach-disk/fdisknewpartition.png)

5. Po wyświetleniu monitu wpisz **p** dokonanie partycji partycji podstawowej. Typ **1** aby stał się pierwszej partycji, a następnie wpisz enter, aby zaakceptować wartości domyślne dla cylinder. W niektórych systemach go Pokaż pierwszy i ostatni sektorów, zamiast cylinder wartości domyślne. Można zaakceptować te ustawienia domyślne.

    ![Tworzenie partycji](./media/attach-disk/fdisknewpartdetails.png)


6. Typ **p** Aby wyświetlić szczegółowe informacje o dysku, który jest jest podzielona na partycje.

    ![Informacje o dyskach listy](./media/attach-disk/fdiskpartitiondetails.png)


7. Typ **w** można zapisać ustawień dla dysku.

    ![Zapisz zmiany dysku](./media/attach-disk/fdiskwritedisk.png)

8. Teraz można utworzyć systemu plików w nowej partycji. Dołącz numer partycji do identyfikator urządzenia (w tym przykładzie `/dev/sdc1`). Poniższy przykład tworzy partycji ext4 /dev/sdc1:
   
    ```bash
    sudo mkfs -t ext4 /dev/sdc1
    ```
   
    ![Utworzyć systemu plików](./media/attach-disk/mkfsext4.png)
   
   > [!NOTE]
   > SuSE Linux Enterprise 11 systemów obsługują tylko dostęp tylko do odczytu ext4 systemów plików. Dla tych systemów zalecane jest nowy system plików w formacie ext3 zamiast ext4.

9. Utwórz katalog można zainstalować nowy system plików w następujący sposób:
   
    ```bash
    sudo mkdir /datadrive
    ```

10. Na koniec dysk, można zainstalować w następujący sposób:
   
    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```
   
    Dysk danych jest teraz gotowa do użycia jako **/datadrive**.
   
    ![Utwórz katalog i zainstalować dysk](./media/attach-disk/mkdirandmount.png)

11. Dodaj nowy dysk do /etc/fstab:
   
    Aby upewnić się, że dysk jest instalacja przeprowadzona automatycznie po ponownym uruchomieniu ona dodana do pliku/etc/fstab. Zaleca się ponadto, czy identyfikator UUID (powszechnie Unikatowy identyfikator) jest używany w /etc/fstab do odwoływania się do stacji, a nie tylko nazwę urządzenia (tj. /dev/sdc1). Korzystanie z identyfikatorem UUID pozwala uniknąć nieprawidłową dysku jest zainstalowany w danej lokalizacji, jeśli system operacyjny wykryje błąd dysku podczas rozruchu i wszelkie pozostałe dyski danych, które są następnie przypisywane identyfikatory tych urządzeń. Aby znaleźć identyfikator UUID nowego dysku, można użyć **blkid** narzędzie:
   
    ```bash
    sudo -i blkid
    ```
   
    Dane wyjściowe wygląda podobnie do poniższego przykładu:
   
    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

    > [!NOTE]
    > Nieprawidłowo edycji **/etc/fstab** pliku może spowodować rozruch systemu. Jeśli nie wiesz, można znaleźć dystrybucji dokumentacji, aby uzyskać informacje dotyczące prawidłowo edytować ten plik. Zalecane jest również, że kopia zapasowa pliku /etc/fstab został utworzony przed przystąpieniem do edytowania.

    Następnie otwórz folder **/etc/fstab** plik w edytorze tekstu:

    ```bash
    sudo vi /etc/fstab
    ```

    W tym przykładzie używamy dla nowej wartości identyfikatora UUID **/dev/sdc1** urządzenia, który został utworzony w poprzednich krokach i punktu instalacji **/datadrive**. Dodaj następujący wiersz na końcu **/etc/fstab** pliku:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
    ```

    Lub na systemy oparte na systemie SuSE Linux może być konieczne do korzystania z formatu nieco inne:

    ```sh
    /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    ```

    > [!NOTE]
    > `nofail` Opcja zapewnia, że maszyna wirtualna zacznie nawet wtedy, gdy system plików jest uszkodzony lub dysk nie istnieje w czasie rozruchu. Bez tej opcji, możesz napotkać zachowanie zgodnie z opisem w [nie SSH do maszyny Wirtualnej systemu Linux z powodu błędów FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Teraz można sprawdzić, czy jest zainstalowany system plików prawidłowo odinstalowywania, a następnie woluminom systemu plików, np. w przykładzie punkt instalacji `/datadrive` utworzone we wcześniejszych krokach:

    ```bash
    sudo umount /datadrive
    sudo mount /datadrive
    ```

    Jeśli `mount` polecenia powoduje błąd, sprawdź plik fstab/etc/poprawną składnię. Jeśli dodatkowe dane dysków lub partycji są tworzone, wprowadź je w/etc/fstab także oddzielnie.

    Należy zapisu dysku za pomocą tego polecenia:

    ```bash
    sudo chmod go+w /datadrive
    ```

    > [!NOTE]
    > Następnie usunięcie dysku danych bez konieczności edytowania fstab może spowodować niepowodzenie do rozruchu maszyny Wirtualnej. Jeśli jest to często dochodzi, większości dystrybucji zapewniają `nofail` i/lub `nobootwait` fstab opcje, które system może rozruch nawet w przypadku awarii dysku do zainstalowania na czas rozruchu. Dokumentacja programu dystrybucji, aby uzyskać więcej informacji na temat tych parametrów.

### <a name="trimunmap-support-for-linux-in-azure"></a>PRZYCINANIE/UNMAP obsługę systemu Linux na platformie Azure
Niektóre jądra systemu Linux obsługują PRZYCINANIE/UNMAP operacji Odrzuć nieużywanych bloków na dysku. Operacje te są szczególnie przydatna w standardowe magazynu Azure, po usunięciu strony nie są już prawidłowe i mogą zostać odrzucone. Odrzucanie strony można zapisać kosztów, jeśli Tworzenie dużych plików, a następnie usuń je.

Istnieją dwa sposoby, aby umożliwić PRZYCINANIE obsługi w maszynie Wirtualnej systemu Linux. W zwykły sposób poszukaj dystrybucji zalecane podejście:

* Użyj `discard` zainstalować opcję `/etc/fstab`, na przykład:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```

* W niektórych przypadkach `discard` opcji może mieć wpływ na wydajność. Alternatywnie można uruchomić `fstrim` ręcznie polecenie w wierszu polecenia lub dodać go do Twojego crontab regularnego uruchamiania:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**
  
    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Rozwiązywanie problemów
[!INCLUDE [virtual-machines-linux-lunzero](../../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Następne kroki
Więcej o korzystaniu z maszyny Wirtualnej systemu Linux w następujących artykułach:

* [Jak zalogować się do maszyny wirtualnej z systemem Linux][Logon]
* [Jak można odłączyć dysku od maszyny wirtualnej systemu Linux](detach-disk-classic.md)
* [Z klasycznego modelu wdrażania przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Skonfiguruj RAID na maszynie Wirtualnej systemu Linux na platformie Azure](../configure-raid.md)
* [Skonfiguruj LVM na Maszynę wirtualną systemu Linux na platformie Azure](../configure-lvm.md)

<!--Link references-->
[Agent]:../../extensions/agent-linux.md
[Logon]:../mac-create-ssh-keys.md
