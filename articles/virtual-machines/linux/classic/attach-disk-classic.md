---
title: Dołączanie dysku do maszyny Wirtualnej z systemem Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dołączyć dysk danych do maszyny Wirtualnej systemu Linux przy użyciu klasycznego modelu wdrażania i zainicjować dysk, aby był gotowy do użycia
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: b5bb3a9353f83cb569988a068f3ca02da85f739c
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929155"
---
# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Jak dołączyć dysk danych do maszyny wirtualnej systemu Linux
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Zobacz jak [dołączanie dysku danych przy użyciu modelu wdrażania usługi Resource Manager](../add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Możesz dołączyć pustych dysków i dyski, które zawierają dane na maszynach wirtualnych platformy Azure. Oba typy dysków są pliki VHD, które znajdują się na koncie usługi Azure storage. Jako dodawania żadnych dysków na maszynę z systemem Linux po dołączeniu dysku musisz zainicjować i sformatować je, aby był gotowy do użycia. Ten artykuł zawiera dołączanie pustych dysków i dyski, które już zawierający dane do maszyn wirtualnych, a także jak następnie inicjowanie i formatowanie nowego dysku.

> [!NOTE]
> Jest najlepszym rozwiązaniem do przechowywania danych maszyny wirtualnej należy używać co najmniej jednego dysku oddzielne. Podczas tworzenia maszyny wirtualnej platformy Azure ma dysk systemu operacyjnego oraz dyski tymczasowe. **Nie należy używać dysku tymczasowego do przechowywania danych.** Jak wskazuje nazwa, zawiera tylko magazyn tymczasowy. Oferuje ona nie nadmiarowości lub kopii zapasowej, ponieważ on nie znajdują się w usłudze Azure storage.
> Dysk tymczasowy jest zwykle są zarządzane przez agenta systemu Linux dla platformy Azure i automatycznie instalowane na **/mnt/zasobów** (lub **katalogu/mnt** na obrazów systemu Ubuntu). Z drugiej strony, dysk z danymi mogą miały nazwy nadane przez jądra systemu Linux podobny `/dev/sdc`, i trzeba partycjonowanie, formatowania i zainstalować tego zasobu. Zobacz [przewodnik użytkownika agenta platformy Azure w systemie Linux] [ Agent] Aby uzyskać szczegółowe informacje.
> 
> 

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Zainicjuj dysk danych w systemie Linux
1. SSH z maszyną Wirtualną. Aby uzyskać więcej informacji, zobacz [jak zalogować się do maszyny wirtualnej z systemem Linux][Logon].
2. Następnie trzeba znaleźć identyfikator urządzenia dla dysku danych do zainicjowania. Istnieją dwa sposoby, aby to zrobić:
   
    ) Grep urządzeń SCSI w dziennikach, takich jak następujące polecenie:
   
    ```bash
    sudo grep SCSI /var/log/messages
    ```
   
    Ostatnie dystrybucji Ubuntu, użytkownik może być konieczne użycie `sudo grep SCSI /var/log/syslog` ponieważ rejestrowanie `/var/log/messages` może być domyślnie wyłączone.
   
    Można znaleźć identyfikatora ostatni dysk danych, który został dodany w wiadomości, które są wyświetlane.
   
    ![Pobierz komunikaty dysku](./media/attach-disk/scsidisklog.png)
   
    LUB
   
    (b) użyj `lsscsi` polecenie, aby dowiedzieć się, identyfikator urządzenia. `lsscsi` można zainstalować za pomocą polecenia `yum install lsscsi` (w systemie Red Hat dystrybucje oparte na) lub `apt-get install lsscsi` (oparte na systemie Debian dystrybucji). Można znaleźć dysku, którego szukasz, jego *jednostki lun* lub **numer jednostki logicznej**. Na przykład *jednostki lun* dla możesz dołączonych dysków można łatwo zobaczyć `azure vm disk list <virtual-machine>` jako:

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
   
    Porównaj te dane przy użyciu danych wyjściowych `lsscsi` dla taki sam przykładowy maszyny wirtualnej:
   
    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```
   
    Ostatnia liczba w krotce w każdym wierszu jest *jednostki lun*. Zobacz `man lsscsi` Aby uzyskać więcej informacji.
3. W wierszu polecenia wpisz następujące polecenie, aby utworzyć urządzenie:
   
    ```bash
    sudo fdisk /dev/sdc
    ```

4. Po wyświetleniu monitu wpisz **n** do utworzenia partycji.

    ![Tworzenie urządzenia](./media/attach-disk/fdisknewpartition.png)

5. Po wyświetleniu monitu wpisz **p** się partycja podstawowa partycja. Typ **1** się to w pierwszej partycji, a następnie wpisz enter, aby zaakceptować wartość domyślną dla tego cylindra. W niektórych systemach go wyświetlić wartości domyślnych, pierwszy i ostatni telekomunikacyjny, zamiast cylindra. Można zaakceptować te ustawienia domyślne.

    ![Tworzenie partycji](./media/attach-disk/fdisknewpartdetails.png)


6. Typ **p** Aby wyświetlić szczegółowe informacje o dysku, który jest jest podzielona na partycje.

    ![Informacje o dyskach listy](./media/attach-disk/fdiskpartitiondetails.png)


7. Typ **w** do zapisywania ustawień dla dysku.

    ![Zapisuje zmiany dysku](./media/attach-disk/fdiskwritedisk.png)

8. Teraz możesz utworzyć w nowej partycji systemu plików. Dołącz numer partycji do Identyfikatora urządzenia (w poniższym przykładzie `/dev/sdc1`). Poniższy przykład tworzy partycję ext4 na /dev/sdc1:
   
    ```bash
    sudo mkfs -t ext4 /dev/sdc1
    ```
   
    ![Tworzenie systemu plików](./media/attach-disk/mkfsext4.png)
   
   > [!NOTE]
   > SuSE Linux Enterprise 11 systemów obsługują tylko dostęp tylko do odczytu dla ext4 systemy plików. Dla tych systemów zaleca się nowy system plików w formacie ext3, a nie ext4.

9. Utwórz katalog należy zainstalować nowy system plików w następujący sposób:
   
    ```bash
    sudo mkdir /datadrive
    ```

10. Na koniec dysk, można zainstalować w następujący sposób:
   
    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```
   
    Dysk z danymi jest teraz gotowy do użycia jako **/datadrive**.
   
    ![Utwórz katalog oraz zainstaluj dysk](./media/attach-disk/mkdirandmount.png)

11. Dodaj nowy dysk do/etc/fstab:
   
    Aby upewnić się, że dysk zostanie ponownie zainstalowany automatycznie po ponownym uruchomieniu należy dodać do pliku/etc/fstab. Ponadto wysoce zalecane jest, że identyfikator UUID (powszechnie Unikatowy identyfikator) służy/etc/fstab do odwoływania się do stacji, a nie tylko nazwy urządzenia (tj. /dev/sdc1). Za pomocą identyfikator UUID pozwala uniknąć nieprawidłową dysku jest zainstalowany w danej lokalizacji, jeśli system operacyjny wykryje błąd dysku podczas rozruchu i wszelkie pozostałe dyski danych, które są następnie przypisywane identyfikatory tych urządzeń. Aby znaleźć identyfikator UUID nowego dysku, można użyć **blkid** narzędzie:
   
    ```bash
    sudo -i blkid
    ```
   
    Dane wyjściowe wyglądają podobnie do poniższego przykładu:
   
    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

    > [!NOTE]
    > Nieprawidłowo edycji **/etc/fstab** plików nieraz powodowały rozruch systemu. Jeśli nie wiesz, zapoznaj się dokumentacją dystrybucji informacji na temat sposobu prawidłowo edytować ten plik. Zalecane jest również, czy kopia zapasowa pliku/etc/fstab został utworzony przed rozpoczęciem edycji.

    Następnie otwórz **/etc/fstab** plik w edytorze tekstu:

    ```bash
    sudo vi /etc/fstab
    ```

    W tym przykładzie używamy nową wartość identyfikatora UUID **/dev/sdc1** urządzenia, który został utworzony w poprzednich krokach, a punkt instalacji **/datadrive**. Dodaj następujący wiersz na końcu **/etc/fstab** pliku:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
    ```

    Lub w systemach, na podstawie w systemie SuSE Linux konieczne może być nieco inne w formacie:

    ```sh
    /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    ```

    > [!NOTE]
    > `nofail` Opcja gwarantuje, że maszyna wirtualna zacznie nawet, jeśli system plików jest uszkodzony lub dysk nie istnieje w czasie rozruchu. Bez tej opcji, możesz napotkać zachowanie zgodnie z opisem w [nie SSH do maszyny Wirtualnej systemu Linux z powodu błędów FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Teraz przetestować, że system plików jest zainstalowany prawidłowo odinstalowywania, a następnie woluminom systemu plików, np. za pomocą przykładu punktu instalacji `/datadrive` utworzone we wcześniejszych krokach:

    ```bash
    sudo umount /datadrive
    sudo mount /datadrive
    ```

    Jeśli `mount` polecenie generuje błąd, sprawdź/etc/fstab w pliku o poprawnej składni. Jeśli dodatkowe dyski z danymi lub partycje są tworzone, wprowadź je w/etc/fstab także oddzielnie.

    Dysk należy zapisywalnego za pomocą tego polecenia:

    ```bash
    sudo chmod go+w /datadrive
    ```

    > [!NOTE]
    > Następnie usunięcie dysku danych bez konieczności edytowania fstab spowodować, że maszyna wirtualna może zakończyć się niepowodzeniem, rozruch. Jeśli jest wystąpieniem typowe, większości dystrybucji zapewniają `nofail` i/lub `nobootwait` fstab opcje Zezwalaj systemowi rozruchu, nawet w przypadku awarii dysku, należy zainstalować na czas rozruchu. Zapoznaj się dokumentacją danej dystrybucji, aby uzyskać więcej informacji na temat tych parametrów.

### <a name="trimunmap-support-for-linux-in-azure"></a>TRIM/UNMAP obsługę systemu Linux na platformie Azure
Niektóre jądra systemu Linux obsługuje TRIM/UNMAP operacji można odrzucić nieużywanych bloków na dysku. Operacje te są szczególnie przydatne w magazynie standard storage do informowania platformy Azure, po usunięciu strony nie są już prawidłowe i mogą zostać odrzucone. Odrzucenie strony można zapisać kosztów, jeśli utworzysz dużych plików, a następnie je usunąć.

Istnieją dwa sposoby, aby umożliwić PRZYCINANIE obsługi w maszynie Wirtualnej systemu Linux. W zwykły sposób poszukaj dystrybucji Zalecanym podejściem:

* Użyj `discard` instalacji opcji `/etc/fstab`, na przykład:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```

* W niektórych przypadkach `discard` opcji może mieć wpływ na wydajność. Alternatywnie, możesz uruchomić `fstrim` polecenie ręcznie z poziomu wiersza polecenia lub dodać do swojej crontab regularnego uruchamiania:
  
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
Możesz dowiedzieć się więcej o korzystaniu z maszyny Wirtualnej systemu Linux w następujących artykułach:

* [Jak zalogować się do maszyny wirtualnej z systemem Linux][Logon]
* [Jak odłączyć dysk od maszyny wirtualnej systemu Linux](detach-disk-classic.md)
* [Za pomocą klasycznego modelu wdrażania przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Konfigurowanie macierzy RAID na maszynie Wirtualnej z systemem Linux na platformie Azure](../configure-raid.md)
* [Konfigurowanie LVM na Maszynę wirtualną systemu Linux na platformie Azure](../configure-lvm.md)

<!--Link references-->
[Agent]:../../extensions/agent-linux.md
[Logon]:../mac-create-ssh-keys.md
