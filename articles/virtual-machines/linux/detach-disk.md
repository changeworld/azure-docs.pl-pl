---
title: Odłączaj dysk danych z maszyny Wirtualnej systemu Linux — Azure
description: Dowiedz się, jak odłączyć dysk danych z maszyny wirtualnej na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure lub witryny Azure portal.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f8a0790169b17ad7755386f9bdd4f9372efc83e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74036368"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>How to detach a data disk from a Linux virtual machine (Jak odłączyć dysk od maszyny wirtualnej systemu Linux)

Gdy już nie potrzebujesz dysku danych dołączonego do maszyny wirtualnej, możesz go łatwo odłączyć. Spowoduje to usunięcie dysku z maszyny wirtualnej, ale nie powoduje usunięcia go z magazynu. W tym artykule pracujemy z dystrybucją Ubuntu LTS 16.04. Jeśli używasz innej dystrybucji, instrukcje dotyczące odinstalowywania dysku mogą być różne.

> [!WARNING]
> W przypadku odłączenia dysku nie jest on automatycznie usuwany. Jeśli zasubskrybujesz magazyn w wersji Premium, nadal będziesz ponosić opłaty za magazyn dysku. Aby uzyskać więcej informacji, zobacz [Ceny i rozliczenia podczas korzystania z magazynu w wersji Premium](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Jeśli chcesz użyć danych znajdujących się na tym dysku, możesz dołączyć go ponownie do tej samej lub innej maszyny wirtualnej.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Połącz się z maszyną wirtualną, aby odinstalować dysk

Przed odłączeniem dysku za pomocą interfejsu wiersza polecenia lub portalu należy odinstalować dysk i usunąć odwołania do if z pliku fstab.

Nawiąż połączenie z maszyną wirtualną. W tym przykładzie publiczny adres IP maszyny Wirtualnej jest *10.0.1.4* z nazwą użytkownika *azureuser:* 

```bash
ssh azureuser@10.0.1.4
```

Najpierw znajdź dysk danych, który chcesz odłączyć. W poniższym przykładzie do filtrowania dysków SCSI użyto dmesg:

```bash
dmesg | grep SCSI
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

Tutaj *sdc* to dysk, który chcemy odłączyć. Należy również pobrać UUID dysku.

```bash
sudo -i blkid
```

Dane wyjściowe wyglądają podobnie do następującego przykładu:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Edytuj plik */etc/fstab,* aby usunąć odwołania do dysku. 

> [!NOTE]
> Nieprawidłowa edycja pliku **/etc/fstab** może spowodować nierozprzestłowienie systemu. Jeśli nie masz pewności, jak to zrobić, sprawdź informacje na temat prawidłowego edytowania tego pliku w dokumentacji dystrybucji. Zaleca się również utworzenie kopii zapasowej pliku /etc/fstab przed edycją.

Otwórz plik */etc/fstab* w edytorze tekstu w następujący sposób:

```bash
sudo vi /etc/fstab
```

W tym przykładzie należy usunąć następujący wiersz z pliku */etc/fstab:*

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Użyj, `umount` aby odinstalować dysk. Poniższy przykład odinstalowuje partycję */dev/sdc1* z punktu instalacji */datadrive:*

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Odłączaj dysk danych przy użyciu interfejsu wiersza polecenia platformy Azure 

W tym przykładzie odłącza dysk *myDataDisk* od maszyny Wirtualnej o nazwie *myVM* w *myResourceGroup*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Dysk pozostaje w magazynie, ale nie jest już dołączony do maszyny wirtualnej.


## <a name="detach-a-data-disk-using-the-portal"></a>Odłączanie dysku danych przy użyciu portalu

1. W menu po lewej stronie wybierz polecenie **Maszyny wirtualne**.
2. Wybierz maszynę wirtualną, która ma dysk danych, który chcesz odłączyć, i kliknij przycisk **Zatrzymaj,** aby zdelocytować maszynę wirtualną.
3. W okienku maszyny wirtualnej wybierz pozycję **Dyski**.
4. U góry okienka **Dyski** wybierz pozycję **Edytuj**.
5. W okienku **Dyski** po prawej stronie dysku danych, który chcesz odłączyć, ![](./media/detach-disk/detach.png) kliknij przycisk Odłącz obraz przycisku Odłącz.
5. Po usunięciu dysku kliknij przycisk Zapisz u góry okienka.
6. W okienku maszyny wirtualnej kliknij pozycję **Przegląd,** a następnie kliknij przycisk **Start** u góry okienka, aby ponownie uruchomić maszynę wirtualną.

Dysk pozostaje w magazynie, ale nie jest już dołączony do maszyny wirtualnej.



## <a name="next-steps"></a>Następne kroki
Jeśli chcesz ponownie użyć dysku danych, możesz po prostu [dołączyć go do innej maszyny Wirtualnej](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

