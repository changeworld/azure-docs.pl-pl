---
title: Optymalizacja sieci maszyny Wirtualnej systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej wskazówki optymalizacji, aby upewnić się, że po skonfigurowaniu sieci maszyny Wirtualnej systemu Linux, aby uzyskać optymalną wydajność na platformie Azure
keywords: maszyny wirtualnej systemu Linux, maszyny wirtualnej systemu linux, ubuntu maszyny wirtualnej
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
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
ms.openlocfilehash: 5b0d7eb0767bb18cd74c545dc386aa16be487dae
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="optimize-your-linux-vm-on-azure"></a>Optymalizowanie maszyny wirtualnej systemu Linux na platformie Azure
Tworzenie maszyny wirtualnej systemu Linux (VM) to łatwo zrobić z wiersza polecenia lub w portalu. W tym samouczku przedstawiono sposób zapewnienia skonfigurowaniu go w celu zoptymalizowania wydajności jej na platformie Microsoft Azure. W tym temacie korzysta z maszyny Wirtualnej systemu Ubuntu Server, ale można również utworzyć maszynę wirtualną systemu Linux przy użyciu [obrazów jako szablon](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Wymagania wstępne
W tym temacie założono, masz już działający subskrypcji platformy Azure ([zakładania wersji próbnej](https://azure.microsoft.com/pricing/free-trial/)) i ma już skonfigurowanymi przez Maszynę wirtualną do Twojej subskrypcji platformy Azure. Upewnij się, że masz najnowszą [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do subskrypcji platformy Azure z [logowania az](/cli/azure/reference-index#az_login) przed [tworzenie maszyny Wirtualnej](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Azure dysku systemu operacyjnego
Po utworzeniu maszyny Wirtualnej systemu Linux na platformie Azure ma dwa dyski skojarzonych z nim. **/ dev/sda** dysku systemu operacyjnego jest **/dev/sdb** jest tymczasowy dysku.  Nie używaj głównego dysku systemu operacyjnego (**/dev/sda**) dla wszystkich elementów, z wyjątkiem systemu operacyjnego jest zoptymalizowana pod kątem szybkiego czasu rozruchu maszyny Wirtualnej i nie zapewniają dobrą wydajność dla obciążeń. Chcesz dołączyć jeden lub więcej dysków do maszyny Wirtualnej można pobrać trwałe i zoptymalizowane magazynu danych. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Dodawanie dysków dla rozmiaru i wydajności
Zależnie od rozmiaru maszyny Wirtualnej, możesz dołączyć do 16 dodatkowych dysków na A-Series, 32 dysków na D-Series i komputera 64 dyski na serię G - każdy maksymalnie 1 TB. Możesz dodać dodatkowe dyski w razie potrzeby na obszarze i wymagania IOps. Każdy dysk ma element docelowy wydajności 500 iops dla magazynu w warstwie standardowa i maksymalnie 5000 IOps dla każdego dysku dla usługi Premium Storage.  Aby uzyskać więcej informacji dotyczących dysków Premium Storage, zobacz [magazyn w warstwie Premium: magazyn o wysokiej wydajności dla maszyn wirtualnych platformy Azure](../windows/premium-storage.md)

Aby osiągnąć najwyższą IOps na dyskach magazyn w warstwie Premium, gdy ich ustawień pamięci podręcznej zostały ustawione jako **tylko do odczytu** lub **Brak**, należy wyłączyć **bariery** podczas instalowania systemu plików w systemie Linux. Nie ma potrzeby bariery, ponieważ zapisy na dyskach kopii magazyn w warstwie Premium są trwałe dla tych ustawień pamięci podręcznej.

* Jeśli używasz **reiserFS**, wyłącz bariery przy użyciu opcji instalacji `barrier=none` (Włączanie bariery, użyj `barrier=flush`)
* Jeśli używasz **ext3/ext4**, wyłącz bariery przy użyciu opcji instalacji `barrier=0` (Włączanie bariery, użyj `barrier=1`)
* Jeśli używasz **XFS**, wyłącz bariery przy użyciu opcji instalacji `nobarrier` (umożliwiających bariery, użyj opcji `barrier`)

## <a name="unmanaged-storage-account-considerations"></a>Uwagi dotyczące konta magazynu niezarządzanego
Domyślne działanie podczas tworzenia maszyny Wirtualnej Azure CLI 2.0 jest dysków zarządzanych platformy Azure.  Te dyski są obsługiwane przez platformę Azure i nie wymagają wszystkie lub lokalizację do przechowywania ich.  Niezarządzane dyski wymagają konta magazynu i mają pewne zagadnienia dotyczące wydajności dodatkowe.  Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [Omówienie usługi Azure Managed Disks](../windows/managed-disks-overview.md).  W tej części opisano zagadnienia dotyczące wydajności, tylko w przypadku dysków o niezarządzanych.  Ponownie, domyślną i zalecaną magazynu rozwiązaniem jest użycie dysków zarządzanych.

Jeśli tworzysz Maszynę wirtualną z dyskami niezarządzane, upewnij się, dołączenia dysków z konta magazynu znajdującej się w tym samym regionie co do zapewnienia sąsiedztwie i zminimalizować opóźnienie sieci maszyny Wirtualnej.  Każde konto magazynu w warstwie standardowa może zawierać maksymalnie 20 k IOps i pojemności rozmiar 500 TB.  Ten limit działa na około 40 intensywnie używane dyski tym zarówno dysk systemu operacyjnego i wszelkie dyski danych utworzone. Dla kont Premium magazynu nie ma żadnego limitu maksymalna liczba IOps, ale ma limitu rozmiaru 32 TB. 

Gdy zajmowanie dużego obciążenia IOps i wybrano Standard Storage dla dysków, konieczne może być podzielić dyski na wiele kont magazynu, aby upewnić się, że nie osiągnięto limit 20 000 IOps dla kont magazynu w warstwie standardowa. Maszyna wirtualna może zawierać kombinację dysków z różnych kont magazynu i typy kont magazynu, aby osiągnąć optymalną konfigurację.
 

## <a name="your-vm-temporary-drive"></a>Dysk tymczasowej maszyny Wirtualnej
Domyślnie podczas tworzenia maszyny Wirtualnej Azure zapewnia dysk systemu operacyjnego (**/dev/sda**) i dysku tymczasowym (**/dev/sdb**).  Wszystkie dyski dodatkowe dodać Pokaż się jako **/dev/sdc**, **/dev/sdd**, **/dev/które integrują usługi** i tak dalej. Wszystkie dane na dysku tymczasowego (**/dev/sdb**) nie są trwałe i mogą zostać utracone, jeśli określonych zdarzeń, takich jak rozmiar maszyny Wirtualnej, ponownego wdrażania, lub konserwacji wymusza ponowne uruchomienie maszyny wirtualnej.  Rozmiar i typ dysku tymczasowy jest powiązany z rozmiar maszyny Wirtualnej, który wybrano w czasie wdrażania. Wszystkie premium rozmiaru dysku tymczasowym bazują na lokalny dysk SSD dla dodatkowych wydajność 48k maszyn wirtualnych (seria DS, G i DS_V2) IOps. 

## <a name="linux-swap-file"></a>Plik wymiany systemu Linux
W przypadku maszyny Wirtualnej Azure z obrazu Ubuntu lub CoreOS, można użyć CustomData do wysyłania konfiguracji chmury do inicjowania chmury. Jeśli użytkownik [przekazać obraz niestandardowy Linux](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) używającą chmury init, można także skonfigurować partycje wymiany przy użyciu chmury init.

Na Ubuntu chmury obrazów należy użyć do skonfigurowania partycji wymiany init chmury. Aby uzyskać więcej informacji, zobacz [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

W przypadku obrazów nie obsługują init chmury obrazów maszyn wirtualnych wdrożonych w witrynie Azure Marketplace ma agenta systemu Linux maszyny Wirtualnej zintegrowane z systemem operacyjnym. Ten agent umożliwia maszyny Wirtualnej do interakcji z różnymi usługami platformy Azure. Zakładając, że wdrożono standardowy obraz z portalu Azure Marketplace, należy wykonać następujące czynności, aby poprawnie skonfigurować ustawienia plików wymiany Linux:

Zlokalizuj i zmodyfikować dwóch wpisów w **/etc/waagent.conf** pliku. Decydować, istnieje plik wymiany dedykowanych i rozmiar pliku wymiany. Chcesz zmodyfikować parametry są `ResourceDisk.EnableSwap=N` i `ResourceDisk.SwapSizeMB=0` 

Zmień parametry następujące ustawienia:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size MB do własnych potrzeb} 

Po dokonaniu zmiany, należy ponownie uruchomić agenta waagent lub ponownego uruchomienia maszyny Wirtualnej systemu Linux w celu odzwierciedlenia tych zmian.  Znasz zostały wprowadzone zmiany i utworzeniu pliku wymiany, korzystając z `free` polecenie, aby wyświetlić wolnego miejsca. W poniższym przykładzie przedstawiono plik wymiany 512MB utworzone w wyniku modyfikowanie **waagent.conf** pliku:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>We/Wy planowania algorytm magazyn w warstwie Premium
Z 2.6.18 jądra systemu Linux, domyślny we/wy planowania algorytm został zmieniony z terminu na CFQ (całkowicie odpowiedniego algorytmu kolejkowania). W przypadku wzorców we/wy dostępie swobodnym istnieje niewielka różnica w wydajności różnice między CFQ a ostatecznym terminem.  Dla dysków na dyskach SSD, gdzie wzorca operacji We/Wy dysku jest głównie sekwencyjnych przełączanie algorytm operacja lub ostatecznego terminu można osiągnąć lepszą wydajność We/Wy.

### <a name="view-the-current-io-scheduler"></a>Wyświetlanie bieżącego harmonogramu we/wy
Użyj następującego polecenia:  

```bash
cat /sys/block/sda/queue/scheduler
```

Możesz sprawdzić następujące dane wyjściowe, co oznacza bieżącego harmonogramu.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Zmień bieżącego urządzenia We/Wy planowania algorytm (/ dev/sda)
Użyj następujących poleceń:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Zastosowanie tego ustawienia dla **/dev/sda** samodzielnie nie jest użyteczne. Ustaw na wszystkich dyskach danych, w którym sekwencyjnych operacji We/Wy dominuje wzorca operacji We/Wy.  

Powinny pojawić się następujące dane wyjściowe, co oznacza, że **grub.cfg** pomyślnie odbudowany, a operacja zaktualizowano harmonogram domyślny.  

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

Rodziny dystrybucji Redhat potrzebne są tylko następujące polecenie:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Przy użyciu oprogramowania RAID w celu osiągnięcia wyższej I / Ops
Jeśli obciążeń wymagają IOps więcej niż jednego dysku, należy użyć konfiguracji RAID oprogramowania z wielu dysków. Ponieważ Azure wykonuje już odporności dysków w warstwie lokalnej sieci szkieletowej, uzyskania najwyższej wydajności z konfiguracji rozkładanie RAID-0.  Udostępnić i utworzyć dyski w środowisku platformy Azure i dołącz je do maszyny Wirtualnej systemu Linux przed partycjonowanie, formatowania i instalowanie dysków.  Więcej informacji na temat konfigurowania ustawień RAID oprogramowania na maszynie Wirtualnej systemu Linux na platformie azure można znaleźć w **[Konfigurowanie RAID oprogramowania w systemie Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** dokumentu.

## <a name="next-steps"></a>Następne kroki
Należy pamiętać, jak z wszystkich dyskusji optymalizacji, należy przeprowadzić testy przed i po każdej zmianie do mierzenia wpływ ma zmiana.  Optymalizacja jest proces krok po kroku, który ma różne wyniki na różnych komputerach w danym środowisku.  Co działa w przypadku jednej konfiguracji mogą nie działać w innym osobom.

Niektóre przydatne łącza do dodatkowych zasobów: 

* [Premium Storage: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure](premium-storage.md)
* [Podręcznik użytkownika Azure agenta systemu Linux](agent-user-guide.md)
* [Optymalizacja wydajności MySQL na maszynach wirtualnych systemu Linux platformy Azure](classic/optimize-mysql.md)
* [Należy skonfigurować oprogramowanie RAID w systemie Linux](configure-raid.md)
