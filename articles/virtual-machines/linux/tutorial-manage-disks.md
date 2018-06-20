---
title: Samouczek — zarządzanie dyskami platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak tworzyć dyski platformy Azure dla maszyn wirtualnych i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure 2.0
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/30/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 470e938aa4ef70f9682528003ccdaba2ef6cc013
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839694"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli-20"></a>Samouczek — zarządzanie dyskami platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure 2.0

Maszyny wirtualne platformy Azure przechowują swoje systemy operacyjne, aplikacje i dane na dyskach. Ważne jest, aby podczas tworzenia maszyny wirtualnej wybrać rozmiar dysku i konfigurację odpowiadające oczekiwanemu obciążeniu. Ten samouczek przedstawia sposób wdrażania dysków maszyny wirtualnej i zarządzania nimi. Zapoznasz się z informacjami na temat następujących zagadnień:

> [!div class="checklist"]
> * Dyski systemu operacyjnego i dyski tymczasowe
> * Dyski z danymi
> * Dyski w warstwie Standardowa i Premium
> * Wydajność dysku
> * Dołączanie i przygotowywanie dysków z danymi
> * Zmienianie rozmiaru dysków
> * Migawki dysków

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli).

## <a name="default-azure-disks"></a>Domyślne dyski platformy Azure

Do tworzonej maszyny wirtualnej platformy Azure automatycznie dołączane są dwa dyski.

**Dysk systemu operacyjnego** — dyski systemu operacyjnego mogą mieć rozmiar do 2 TB i hostują system operacyjny maszyny wirtualnej. Domyślnie dysk sytemu operacyjnego ma etykietę */dev/sda*. Konfiguracja buforowania dysku systemu operacyjnego jest zoptymalizowana pod kątem wydajności systemu operacyjnego. Ze względu na tę konfigurację dysk sytemu operacyjnego **nie powinien** być używany przez aplikacje ani dane. Na potrzeby aplikacji i danych należy użyć dysków z danymi, które zostaną szczegółowo opisane w dalszej części tego samouczka.

**Dysk tymczasowy** — dyski tymczasowe używają dysku SSD, który znajduje się na tym samym hoście platformy Azure co maszyna wirtualna. Dyski tymczasowe są wysoce wydajne i można przy ich użyciu wykonywać operacje takie jak przetwarzanie danych tymczasowych. Jednak jeśli maszyna wirtualna zostanie przeniesiona do nowego hosta, wszystkie dane przechowywane na dysku tymczasowym zostaną usunięte. Rozmiar dysku tymczasowego zależy od rozmiaru maszyny wirtualnej. Dyski tymczasowe mają etykietę */dev/sdb* oraz punkt instalacji */mnt*.

### <a name="temporary-disk-sizes"></a>Rozmiary dysków tymczasowych

| Typ | Typowe rozmiary | Maksymalny rozmiar dysku tymczasowego (GiB) |
|----|----|----|
| [Zastosowania ogólne](sizes-general.md) | Seria A, B i D | 1600 |
| [Optymalizacja pod kątem obliczeń](sizes-compute.md) | Seria F | 576 |
| [Optymalizacja pod kątem pamięci](sizes-memory.md) | Seria D, E, G i M | 6144 |
| [Optymalizacja pod kątem magazynu](sizes-storage.md) | Seria L | 5630 |
| [Procesor GPU](sizes-gpu.md) | Seria N | 1440 |
| [Wysoka wydajność](sizes-hpc.md) | Seria A i H | 2000 |

## <a name="azure-data-disks"></a>Dyski z danymi platformy Azure

Aby zainstalować aplikacje i przechowywać dane, można dodać kolejne dyski z danymi. Dyski z danymi powinny być używane w sytuacji, gdy potrzebny jest trwały i dynamiczny magazyn danych. Każdy dysk z danymi ma maksymalną pojemność wynoszącą 4 TB. Liczba dysków z danymi, które można dołączyć do maszyny wirtualnej, zależy od jej rozmiaru. Na każdy procesor wirtualny maszyny wirtualnej można dołączyć dwa dyski z danymi.

### <a name="max-data-disks-per-vm"></a>Maksymalna liczba dysków z danymi na maszynę wirtualną

| Typ | Rozmiar maszyny wirtualnej | Maksymalna liczba dysków z danymi na maszynę wirtualną |
|----|----|----|
| [Zastosowania ogólne](sizes-general.md) | Seria A, B i D | 64 |
| [Optymalizacja pod kątem obliczeń](sizes-compute.md) | Seria F | 64 |
| [Optymalizacja pod kątem pamięci](../virtual-machines-windows-sizes-memory.md) | Seria D, E i G | 64 |
| [Optymalizacja pod kątem magazynu](../virtual-machines-windows-sizes-storage.md) | Seria L | 64 |
| [Procesor GPU](sizes-gpu.md) | Seria N | 64 |
| [Wysoka wydajność](sizes-hpc.md) | Seria A i H | 64 |

## <a name="vm-disk-types"></a>Typy dysków maszyny wirtualnej

Na platformie Azure dostępne są dwa typy dysków.

### <a name="standard-disk"></a>Dysk w warstwie Standardowa

Magazyn Standard Storage bazuje na dyskach twardych (HDD) i stanowi ekonomiczne, chociaż wciąż wydajne rozwiązanie. Dyski w warstwie Standardowa idealnie nadają się do ekonomicznej obsługi obciążeń tworzenia i testowania.

### <a name="premium-disk"></a>Dysk w warstwie Premium

Dyski w warstwie Premium są wspierane przez oparty na technologii SSD dysk o wysokiej wydajności i niskim opóźnieniu. Idealnie nadają się one dla maszyn wirtualnych z uruchomionym obciążeniem produkcyjnym. Usługa Premium Storage obsługuje maszyny wirtualne z serii DS, DSv2, GS i FS. Podczas wybierania rozmiaru dysku wartość jest zaokrąglana do następnego typu. Jeśli na przykład rozmiar dysku wynosi mniej niż 128 GB, jest to dysk typu P10. Jeśli rozmiar dysku należy do zakresu od 129 GB do 512 GB, jest to dysk typu P20. Dyski o rozmiarze powyżej 512 GB są dyskami typu P30.

### <a name="premium-disk-performance"></a>Wydajność dysku w warstwie Premium

|Typ dysku magazynu Premium Storage | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Rozmiar dysku (zaokrąglony w górę) | 32 GB | 64 GB | 128 GB | 512 GB | 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) |
| Maksymalna liczba operacji wejścia/wyjścia na sekundę na dysk | 120 | 240 | 500 | 2300 | 5000 | 7500 | 7500 |
Przepływność na dysk | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

W powyższej tabeli podano maksymalną liczbę operacji wejścia/wyjścia na sekundę na dysk, ale wyższą wydajność można osiągnąć przez stosowanie wielu dysków z danymi. Na przykład maszyna wirtualna Standard_GS5 może osiągnąć maksymalnie 80 000 operacji we/wy na sekundę Aby uzyskać szczegółowe informacje na temat maksymalnej liczby operacji we/wy na sekundę na maszynę wirtualną, zobacz [Rozmiary maszyn wirtualnych z systemem Linux](sizes.md).

## <a name="create-and-attach-disks"></a>Tworzenie i dołączanie dysków

Dyski z danymi można tworzyć i dołączać podczas tworzenia maszyny wirtualnej lub dołączać je do istniejącej maszyny wirtualnej.

### <a name="attach-disk-at-vm-creation"></a>Dołączanie dysku podczas tworzenia maszyny wirtualnej

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create).

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). W poniższym przykładzie pokazano tworzenie maszyny wirtualnej o nazwie *myVM*, dodawanie konta użytkownika o nazwie *azureuser* i generowanie kluczy SSH, jeśli nie istnieją. Za pomocą argumentu `--datadisk-sizes-gb` określa się, że powinien zostać utworzony dodatkowy dysk i dołączony do maszyny wirtualnej. Aby utworzyć i dołączyć więcej niż jeden dysk, użyj rozdzielanej spacjami listy wartości rozmiarów dysków. W poniższym przykładzie maszyna wirtualna jest tworzona z dwoma dyskami z danymi o rozmiarze 128 GB każdy. Ponieważ rozmiary dysków wynoszą 128 GB, oba dyski są konfigurowane jako dyski P10, co zapewnia 500 operacji we/wy na sekundę na dysk.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Dołączanie dysku do istniejącej maszyny wirtualnej

Aby utworzyć i dołączyć nowy dysk do istniejącej maszyny wirtualnej, użyj polecenia [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach). W poniższym przykładzie tworzony jest dysk w warstwie Premium o rozmiarze 128 GB i dołączany do maszyny wirtualnej utworzonej w poprzednim kroku.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --disk myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Przygotowywanie dysków z danymi

Po dołączeniu dysku do maszyny wirtualnej należy skonfigurować system operacyjny do korzystania z tego dysku. Poniższy przykład pokazuje, jak ręcznie skonfigurować dysk. Ten proces można także zautomatyzować za pomocą pakietu cloud-init, co omówiono w jednym z [dalszych samouczków](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Konfiguracja ręczna

Utwórz połączenie SSH z maszyną wirtualną. Zastąp przykładowy adres IP publicznym adresem IP maszyny wirtualnej.

```azurecli-interactive
ssh azureuser@52.174.34.95
```

Podziel dysk na partycje za pomocą polecenia `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Utwórz na partycji system plików za pomocą polecenia `mkfs`.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Zainstaluj nowy dysk, aby był dostępny w systemie operacyjnym.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

Do tego dysku można teraz uzyskać dostęp za pośrednictwem punktu instalacji *datadrive*, co można zweryfikować, uruchamiając polecenie `df -h`.

```bash
df -h
```

W danych wyjściowych widoczny jest nowy dysk zainstalowany w punkcie instalacji */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Aby zapewnić, że dysk zostanie ponownie zainstalowany po ponownym uruchomieniu, należy dodać go do pliku */etc/fstab*. Aby to zrobić, uzyskaj identyfikator UUID dysku za pomocą narzędzia `blkid`.

```bash
sudo -i blkid
```

Identyfikator UUID dysku jest wyświetlany w danych wyjściowych, w tym przypadku jest to `/dev/sdc1`.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Dodaj do pliku */etc/fstab* wiersz podobny do następującego.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail   1  2
```

Teraz, gdy dysk został skonfigurowany, zamknij sesję SSH.

```bash
exit
```

## <a name="resize-vm-disk"></a>Zmienianie rozmiaru dysku maszyny wirtualnej

Po wdrożeniu maszyny wirtualnej można zwiększyć rozmiar dysku systemu operacyjnego lub dowolnych dołączonych dysków z danymi. Zwiększenie rozmiaru dysku jest korzystne, gdy potrzebujesz więcej miejsca do magazynowania lub wyższego poziomu wydajności (P10, P20 lub P30). Rozmiaru dysku nie można zmniejszyć.

Aby zwiększyć rozmiar dysku, należy najpierw uzyskać jego identyfikator lub nazwę. Za pomocą polecenia [az disk list](/cli/azure/disk#az-disk-list) wyświetl listę wszystkich dysków w grupie zasobów. Zanotuj nazwę dysku, którego rozmiar chcesz zwiększyć.

```azurecli-interactive
az disk list \
    --resource-group myResourceGroupDisk \
    --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
    --output table
```

Należy cofnąć przydział maszyny wirtualnej. Za pomocą polecenia [az vm deallocate](/cli/azure/vm#az-vm-deallocate) zatrzymaj maszynę wirtualną i cofnij jej przydział.

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupDisk --name myVM
```

Za pomocą polecenia [az disk update](/cli/azure/vm/disk#az-vm-disk-update) zmień rozmiar dysku. Ten przykład zmienia rozmiar dysku o nazwie *myDataDisk* na 1 terabajt.

```azurecli-interactive
az disk update --name myDataDisk --resource-group myResourceGroupDisk --size-gb 1023
```

Po zakończeniu operacji zmiany rozmiaru włącz maszynę wirtualną.

```azurecli-interactive
az vm start --resource-group myResourceGroupDisk --name myVM
```

Jeśli zmieniono rozmiar dysku systemu operacyjnego, partycja zostanie automatycznie rozszerzona. Jeśli zmieniono rozmiar dysku z danymi, bieżące partycje należy rozszerzyć z poziomu systemu operacyjnego maszyny wirtualnej.

## <a name="snapshot-azure-disks"></a>Tworzenie migawek dysków platformy Azure

Utworzenie migawki dysku powoduje utworzenie przez platformę Azure jego kopii tylko do odczytu ze stałym punktem odniesienia. Migawki maszyn wirtualnych platformy Azure są przydatne do szybkiego zapisywania stanu maszyny wirtualnej przed wprowadzeniem zmian w konfiguracji. Jeśli zmiany konfiguracji okażą się niepożądane, za pomocą migawki można przywrócić stan maszyny wirtualnej. Jeśli maszyna wirtualna ma więcej niż jeden dysk, migawki każdego dysku są tworzone niezależnie od innych. W przypadku wykonywania kopii zapasowych spójnych z aplikacjami rozważ zatrzymanie maszyny wirtualnej przed utworzeniem migawek dysków. Możesz także użyć [usługi Azure Backup](/azure/backup/), która umożliwia wykonywanie automatycznych kopii zapasowych, gdy maszyna wirtualna jest uruchomiona.

### <a name="create-snapshot"></a>Tworzenie migawki

Aby utworzyć migawkę dysku maszyny wirtualnej, należy najpierw uzyskać identyfikator i nazwę dysku. Za pomocą polecenia [az vm show](/cli/azure/vm#az-vm-show) wyświetl identyfikator dysku. W tym przykładzie identyfikator dysku jest przechowywany w zmiennej, aby można go było użyć w późniejszym kroku.

```azurecli-interactive
osdiskid=$(az vm show -g myResourceGroupDisk -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Gdy masz już identyfikator dysku maszyny wirtualnej, za pomocą poniższego polecenia możesz utworzyć migawkę tego dysku.

```azurcli
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Tworzenie dysku z migawki

Tę migawkę można następnie przekonwertować na dysk, przy użyciu którego można odtworzyć maszynę wirtualną.

```azurecli-interactive
az disk create --resource-group myResourceGroupDisk --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Przywracanie maszyny wirtualnej z migawki

Aby zademonstrować odzyskiwanie maszyny wirtualnej, usuń istniejącą maszynę wirtualną.

```azurecli-interactive
az vm delete --resource-group myResourceGroupDisk --name myVM
```

Utwórz nową maszynę wirtualną z migawki dysku.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Ponowne dołączanie dysku z danymi

Wszystkie dyski z danymi należy ponownie dołączyć do maszyny wirtualnej.

Najpierw odnajdź nazwę dysku z danymi za pomocą polecenia [az disk list](/cli/azure/disk#az-disk-list). W tym przykładzie nazwę dysku umieszczono w zmiennej o nazwie *datadisk*, która zostanie użyta w następnym kroku.

```azurecli-interactive
datadisk=$(az disk list -g myResourceGroupDisk --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Dołącz dysk za pomocą polecenia [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach).

```azurecli-interactive
az vm disk attach –g myResourceGroupDisk –-vm-name myVM –-disk $datadisk
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące dysków maszyn wirtualnych, takie jak:

> [!div class="checklist"]
> * Dyski systemu operacyjnego i dyski tymczasowe
> * Dyski z danymi
> * Dyski w warstwie Standardowa i Premium
> * Wydajność dysku
> * Dołączanie i przygotowywanie dysków z danymi
> * Zmienianie rozmiaru dysków
> * Migawki dysków

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat automatyzowania konfigurowania maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Automatyzowanie konfiguracji maszyny wirtualnej](./tutorial-automate-vm-deployment.md)
