---
title: Odłączanie dysku danych z maszyny Wirtualnej systemu Linux — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się odłączyć dysk danych z maszyny wirtualnej na platformie Azure przy użyciu wiersza polecenia platformy Azure lub w portalu Azure.
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 02cb970b5c70064abbbc71e585fe3dd1540fda90
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766341"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Jak odłączyć dysk z danymi od maszyny wirtualnej systemu Linux

Gdy już nie potrzebujesz dysku danych dołączonego do maszyny wirtualnej, możesz go łatwo odłączyć. Usuwa dysk od maszyny wirtualnej, ale nie spowoduje usunięcia go z magazynu. W tym artykule pracujemy z dystrybucją Ubuntu LTS 16.04. Jeśli używasz różnych dystrybucji instrukcje dotyczące odinstalowywania dysku mogą się różnić.

> [!WARNING]
> Jeśli możesz odłączyć dysku, który nie jest automatycznie usuwana. Jeśli subskrybujesz usługę Premium storage, możesz nadal będziesz ponosić opłaty za magazyn na dysku. Aby uzyskać więcej informacji, zobacz [cen i rozliczeń, korzystając z usługi Premium Storage](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Jeśli chcesz użyć danych znajdujących się na tym dysku, możesz dołączyć go ponownie do tej samej lub innej maszyny wirtualnej.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Łączenie z maszyną Wirtualną, aby odinstalować dysku

Przed odłączeniem dysku przy użyciu interfejsu wiersza polecenia lub w portalu, musisz odinstalować dysku i usunąć odwołania do z pliku fstab.

Nawiąż połączenie z maszyną wirtualną. W tym przykładzie jest publiczny adres IP maszyny wirtualnej *10.0.1.4* nazwy użytkownika *azureuser*: 

```bash
ssh azureuser@10.0.1.4
```

Po pierwsze Znajdź, którą chcesz odłączyć dysk danych. W poniższym przykładzie użyto dmesg filtrującą dane na dyskach SCSI:

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

W tym miejscu *sdc* to dysk, który chcesz odłączyć. Należy również Znajdź identyfikator UUID dysku.

```bash
sudo -i blkid
```

Dane wyjściowe wyglądają podobnie do poniższego przykładu:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Edytuj */etc/fstab* , aby usunąć odwołania do dysku. 

> [!NOTE]
> Nieprawidłowo edycji **/etc/fstab** plików nieraz powodowały rozruch systemu. Jeśli nie wiesz, zapoznaj się dokumentacją dystrybucji informacji na temat sposobu prawidłowo edytować ten plik. Zalecane jest również, czy kopia zapasowa pliku/etc/fstab został utworzony przed rozpoczęciem edycji.

Otwórz */etc/fstab* plik w edytorze tekstów w następujący sposób:

```bash
sudo vi /etc/fstab
```

W tym przykładzie następujący wiersz musi zostać usunięty z */etc/fstab* pliku:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Użyj `umount` odinstalował dysk. Poniższy przykład umożliwia odinstalowanie */dev/sdc1* partycji z */datadrive* punktu instalacji:

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Odłączanie dysku danych przy użyciu wiersza polecenia platformy Azure 

Ten przykład umożliwia odłączenie dysku *myDataDisk* dysk od maszyny Wirtualnej o nazwie *myVM* w *myResourceGroup*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Dysk pozostaje w magazynie, ale nie jest już dołączony do maszyny wirtualnej.


## <a name="detach-a-data-disk-using-the-portal"></a>Odłączanie dysku danych przy użyciu portalu

1. W menu po lewej stronie wybierz **maszyn wirtualnych**.
2. Wybierz maszynę wirtualną, która ma dysk danych, które chcesz odłączyć, a następnie kliknij przycisk **zatrzymać** można cofnąć alokacji maszyny Wirtualnej.
3. W okienku maszyny wirtualnej wybierz **dysków**.
4. W górnej części **dysków** okienku wybierz **Edytuj**.
5. W **dysków** okienko po prawej dysku danych, który chcesz odłączyć, kliknij przycisk ![obraz przycisku Odłącz](./media/detach-disk/detach.png) odłączyć przycisku.
5. Po usunięciu dysku kliknij przycisk Zapisz w górnej części okienka.
6. W okienku maszyny wirtualnej kliknij **Przegląd** a następnie kliknij przycisk **Start** przycisk u góry okienka, aby ponownie uruchomić maszynę Wirtualną.

Dysk pozostaje w magazynie, ale nie jest już dołączony do maszyny wirtualnej.



## <a name="next-steps"></a>Kolejne kroki
Jeśli chcesz ponownie użyć dysku danych, możesz po prostu [dołączyć go do innej maszyny Wirtualnej](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

