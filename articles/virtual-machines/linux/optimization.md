---
title: Optymalizowanie maszyny Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, wskazówki optymalizacji, aby upewnić się, że po skonfigurowaniu sieci maszyny Wirtualnej systemu Linux w celu uzyskania optymalnej wydajności na platformie Azure
keywords: maszyny wirtualnej systemu Linux, maszyny wirtualnej systemu linux, maszyny wirtualnej systemu ubuntu
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 8baa30c8-d40e-41ac-93d0-74e96fe18d4c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: bd59257c1136f52beaf217c1f983c8aeb7bd81d5
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671123"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Optymalizowanie maszyny wirtualnej systemu Linux na platformie Azure
Tworzenia maszyny wirtualnej systemu Linux (VM) to łatwo zrobić z wiersza polecenia lub w portalu. Ten samouczek pokazuje, jak upewnić się, po skonfigurowaniu go w celu zoptymalizowania wydajności na platformie Microsoft Azure. Ten temat używa maszyny Wirtualnej z systemem Ubuntu Server, ale można również utworzyć maszynę wirtualną systemu Linux przy użyciu [własnych obrazów jako szablony](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Wymagania wstępne
W tym temacie założono, masz już działającą subskrypcji platformy Azure ([bezpłatnej wersji próbnej rejestracji](https://azure.microsoft.com/pricing/free-trial/)) i już przeprowadzono aprowizację maszyny Wirtualnej w ramach subskrypcji platformy Azure. Upewnij się, że masz najnowszy [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do subskrypcji platformy Azure za pomocą [az login](/cli/azure/reference-index) przed [Utwórz Maszynę wirtualną](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Azure OS Disk
Po utworzeniu maszyny Wirtualnej z systemem Linux na platformie Azure ma dwa dyski skojarzone z nią. **/ dev/sda** jest dysk systemu operacyjnego, **/dev/sdb** jest dysku tymczasowego.  Nie używaj głównego dysku systemu operacyjnego ( **/dev/sda**) dla wszystkich elementów, z wyjątkiem systemu operacyjnego jest zoptymalizowana pod kątem szybkiego czasu rozruchu maszyny Wirtualnej i nie zapewnia dobrą wydajność dla obciążeń. Aby dołączyć co najmniej jeden dysk do maszyny Wirtualnej, aby pobrać trwałe i zoptymalizowane pod kątem magazynowania danych. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Dodawanie dysków dla rozmiaru i wydajności obiektów docelowych
Zależności od rozmiaru maszyny Wirtualnej, można dołączyć do 16 dodatkowe dyski na serii A, 32 dyski na serii D i komputera 64 dyski na serii G - każdy maksymalnie 1 TB. Możesz dodać dodatkowe dyski, zgodnie z potrzebami na obszarze i wymagania dotyczące operacji We/Wy. Każdy dysk ma element docelowy wydajności z 500 operacji We/Wy dla magazynu w warstwie standardowa i do 5000 operacji We/Wy na dysku dla usługi Premium Storage.

Aby osiągnąć najwyższą operacje We/Wy na dysków usługi Premium Storage, gdzie ich ustawienia pamięci podręcznej zostały ustawione na **tylko do odczytu** lub **Brak**, należy wyłączyć **bariery** podczas instalowania system plików w systemie Linux. Nie ma potrzeby bariery, ponieważ operacje zapisu do dysków usługi Premium Storage kopie są trwałe dla tych ustawień pamięci podręcznej.

* Jeśli używasz **reiserFS**, wyłącz barier za pomocą opcji instalacji `barrier=none` (w przypadku włączania bariery, użyj `barrier=flush`)
* Jeśli używasz **ext3/ext4**, wyłącz barier za pomocą opcji instalacji `barrier=0` (w przypadku włączania bariery, użyj `barrier=1`)
* Jeśli używasz **XFS**, wyłącz barier za pomocą opcji instalacji `nobarrier` (włączania bariery, użyj opcji `barrier`)

## <a name="unmanaged-storage-account-considerations"></a>Uwagi dotyczące konta magazynu niezarządzanego
Domyślna akcja podczas tworzenia maszyny Wirtualnej przy użyciu wiersza polecenia platformy Azure jest użycie usługi Azure Managed Disks.  Te dyski są obsługiwane przez platformę Azure, a nie są wymagane żadne przygotowanie ani lokalizacja, do ich przechowywania.  Usługa Unmanaged disks wymagają konta magazynu i mają pewne zagadnienia wyższą wydajność.  Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [Omówienie usługi Azure Managed Disks](../windows/managed-disks-overview.md).  W poniższej sekcji przedstawiono zagadnienia związane z wydajnością, tylko wtedy, gdy używasz dysków niezarządzanych.  Ponownie, domyślna i rozwiązanie do magazynowania zalecane jest używanie dysków zarządzanych.

Jeśli tworzysz Maszynę wirtualną z dyskami niezarządzanymi, upewnij się, dołączenia dysków z konta magazynu znajdujące się w tym samym regionie jako maszyny Wirtualnej, aby zapewnić bliskość Zamknij i zminimalizować opóźnienie sieci.  Każde konto magazynu w warstwie standardowa może zawierać maksymalnie 20 k operacje We/Wy i rozmiar pojemności 500 TB.  Ten limit ustali do około 40 intensywnie używanych dysków, w tym dysk systemu operacyjnego i wszelkich dysków z danymi, które można utworzyć. Dla kont usługi Premium Storage nie ma żadnego limitu maksymalnej operacje We/Wy, ale ma limitu rozmiaru 32 TB. 

Zajmowanie się wysokie obciążenia operacji We/Wy i zostały wybrane magazynu w warstwie standardowa dla dysków, należy podzielić dyski na wielu kontach magazynu, aby upewnić się, że nie osiągnięto limit 20 000 operacji We/Wy dla kont magazynu w warstwie standardowa. Maszyna wirtualna może zawierać kombinację dysków z różnych kont magazynu i typy kont magazynu w celu osiągnięcia optymalnej konfiguracji.
 

## <a name="your-vm-temporary-drive"></a>Dysk tymczasowy maszyn wirtualnych
Domyślnie podczas tworzenia maszyny Wirtualnej, platforma Azure udostępnia dysku systemu operacyjnego ( **/dev/sda**) oraz dyski tymczasowe ( **/dev/sdb**).  Wszystkie dodatkowe dyski show Dodaj się jako **/dev/sdc**, **/dev/sdd**, **/dev/sde** i tak dalej. Wszystkie dane na dysku tymczasowego ( **/dev/sdb**) nie są trwałe i mogą zostać utracone, jeśli określone zdarzenia, takie jak rozmiar maszyny Wirtualnej, ponownego wdrożenia, lub konserwacji wymusza ponowne uruchomienie maszyny wirtualnej.  Rozmiar i typ dysku tymczasowego jest powiązana z rozmiar maszyny Wirtualnej, który został wybrany w czasie wdrażania. Wszystkie warstwa Premium — rozmiar maszyn wirtualnych (serii DS, G i DS_V2) dysku tymczasowego są wspierane przez lokalny dysk SSD dla dodatkowej wydajności 48k operacje We/Wy. 

## <a name="linux-swap-file"></a>Plik wymiany w systemie Linux
W przypadku maszyny Wirtualnej platformy Azure z obrazu systemu Ubuntu lub CoreOS, można użyć funkcji CustomData wysyłać konfiguracji chmury pakietu cloud-init. Jeśli użytkownik [przekazany niestandardowych obrazów systemu Linux](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , który używa pakietu cloud-init, możesz również skonfigurować partycji wymiany, za pomocą pakietu cloud-init.

Na Ubuntu obrazy w chmurze należy użyć pakietu cloud-init w celu skonfigurowania partycji wymiany. Aby uzyskać więcej informacji, zobacz [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

W przypadku obrazów, które nie obsługują pakiet cloud-init obrazów maszyn wirtualnych wdrażanych za pomocą portalu Azure Marketplace ma agenta systemu Linux maszyny Wirtualnej, zintegrowane z systemem operacyjnym. Ten agent umożliwia maszynie Wirtualnej, aby wchodzić w interakcje z różnymi usługami platformy Azure. Przy założeniu, że standardowego obrazu z witryny Azure Marketplace została wdrożona, należy wykonać następujące czynności, aby poprawnie skonfigurować ustawienia plików wymiany systemu Linux:

Zlokalizuj i zmodyfikować dwóch wpisów w **/etc/waagent.conf** pliku. Obecność pliku wymiany dedykowanych i rozmiar pliku wymiany ich kontrolować. Parametry chcesz zmodyfikować `ResourceDisk.EnableSwap=N` i `ResourceDisk.SwapSizeMB=0` 

Zmień parametry w następujących ustawieniach:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size MB do własnych potrzeb} 

Po wprowadzeniu zmiany należy ponownie uruchomić waagent lub ponownego uruchomienia maszyny Wirtualnej systemu Linux, aby odzwierciedlać wprowadzone zmiany.  Wiesz, aby zmiany zostały wprowadzone i utworzeniu pliku wymiany, gdy używasz `free` polecenie, aby wyświetlić ilość wolnego miejsca. W poniższym przykładzie przedstawiono plik wymiany 512MB, utworzone w wyniku modyfikowanie **waagent.conf** pliku:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algorytm planowania operacji We/Wy dla usługi Premium Storage
Za pomocą 2.6.18 jądra systemu Linux, domyślne we/wy planowania algorytm został zmieniony z terminu ostatecznego na CFQ (całkowicie uczciwe kolejkowania algorytm). Pod kątem dostępu losowego wzorców operacji We/Wy są niewielkie różnice w wydajności między CFQ a ostatecznym terminem.  Dla dysków na dyskach SSD, gdzie jest głównie sekwencyjny wzorzec operacji We/Wy dysku przełączanie z powrotem do algorytmu aktualizujący nie działa lub ostatecznego terminu może osiągnąć lepszą wydajność operacji We/Wy.

### <a name="view-the-current-io-scheduler"></a>Wyświetl bieżący harmonogram we/wy
Użyj następującego polecenia:  

```bash
cat /sys/block/sda/queue/scheduler
```

Zobaczysz następujące dane wyjściowe, co oznacza bieżącego harmonogramu.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Zmień bieżące urządzenie (/ dev/sda) planowanie algorytm we/wy
Użyj następujących poleceń:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Zastosowanie tego ustawienia dla **/dev/sda** samodzielnie nie jest użyteczny. Ustaw na wszystkich dyskach danych, w którym sekwencyjnych operacji We/Wy większą wzorzec operacji We/Wy.  

Powinien zostać wyświetlony następujące dane wyjściowe, co oznacza, że **grub.cfg** pomyślnie odbudowany i domyślnego harmonogramu została zaktualizowana w celu aktualizujący nie działa.  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

W przypadku dystrybucji rodziny Red Hat potrzebne są tylko następujące polecenie:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Za pomocą macierzy RAID oprogramowania w celu osiągnięcia wyższej I / Ops
Jeśli obciążenia wymagają IOps więcej niż jednego dysku, należy użyć konfiguracji RAID oprogramowania z wielu dysków. Ponieważ platforma Azure przeprowadza już odporności dysku w warstwie lokalnej sieci szkieletowej, można osiągnąć najwyższy poziom wydajności z konfiguracji RAID-0 rozkładanie.  Aprowizowanie i tworzenia dysków w środowisku platformy Azure i dołącz je do maszyny Wirtualnej systemu Linux przed partycjonowanie, formatowanie i instalowania dysków.  Więcej informacji na temat konfigurowania ustawień macierzy RAID oprogramowania na maszynie Wirtualnej systemu Linux na platformie azure można znaleźć w **[konfigurowanie macierzy RAID oprogramowania w systemie Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** dokumentu.

## <a name="next-steps"></a>Następne kroki
Pamiętaj, że za pomocą wszystkich dyskusji optymalizacji, potrzebnych do przeprowadzenia testów przed i po każdej zmianie na mierzenie wpływu, który zawiera zmiany.  Optymalizacja jest proces krok po kroku, który ma różne wyniki na różnych komputerach w danym środowisku.  Jakie rozwiązania najlepiej jedna konfiguracja może nie działać dla innych użytkowników.

Przydatne linki do dodatkowych zasobów:

* [Przewodnik użytkownika agenta platformy Azure](../extensions/agent-linux.md)
* [Optymalizacja wydajności programu MySQL na maszynach wirtualnych z systemem Linux platformy Azure](classic/optimize-mysql.md)
* [Konfigurowanie programowej macierzy RAID w systemie Linux](configure-raid.md)
