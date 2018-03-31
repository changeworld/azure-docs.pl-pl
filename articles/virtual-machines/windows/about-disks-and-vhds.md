---
title: O niezarządzanych (stronicowych obiektów blob) i zarządzanych dyski magazynu dla maszyn wirtualnych systemu Windows, Microsoft Azure | Dokumentacja firmy Microsoft
description: Poznaj podstawy niezarządzane (stronicowych obiektów blob), a zarządzane dyski magazynu dla maszyn wirtualnych systemu Windows Azure.
services: virtual-machines
author: roygara
manager: jeconnoc
ms.service: virtual-machines
ms.workload: storage
ms.tgt_pltfrm: windows
ms.topic: article
ms.date: 11/15/2017
ms.author: rogarana
ms.openlocfilehash: 3d82416b7382b8e09706fd7fb8af9e1d3fe79f01
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="about-disks-storage-for-azure-windows-vms"></a>Temat dyski magazynu dla maszyn wirtualnych systemu Windows Azure
Podobnie jak dowolnego innego komputera maszynach wirtualnych platformy Azure używać dysków jako miejsce do przechowywania systemu operacyjnego, aplikacji i danych. Wszystkie maszyny wirtualne platformy Azure są co najmniej dwa dyski — dysk systemu operacyjnego Windows i dysku tymczasowym. Dysk systemu operacyjnego jest tworzony z obrazu, a jednocześnie dysku systemu operacyjnego i obrazu są wirtualnych dysków twardych (VHD) przechowywane w koncie magazynu platformy Azure. Maszyny wirtualne mogą także mieć co najmniej jeden dysk danych, które są także przechowywane jako wirtualne dyski twarde. 

W tym artykule firma Microsoft będzie porozmawiać na temat różnych zastosowań dysków, a następnie omówiono różne typy dysków, można tworzyć i używać. W tym artykule jest również dostępny do [maszyn wirtualnych systemu Linux](../linux/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Dysków używanych przez maszyny wirtualne

Spójrzmy na jak dyski są używane przez maszyny wirtualne.

### <a name="operating-system-disk"></a>Dysk systemu operacyjnego
Co maszyna wirtualna ma jeden dysk systemu operacyjnego podłączonego. Ma ona zarejestrowana jako dyski SATA i oznaczone jako dysk C: domyślnie. Ten dysk ma maksymalną pojemność 2048 gigabajtów (GB). 

### <a name="temporary-disk"></a>Tymczasowe dysku
Każda maszyna wirtualna zawiera dysku tymczasowym. Dysku tymczasowym zapewnia krótkoterminowy magazyn dla aplikacji i procesów i jest przeznaczona tylko przechowywania danych, takich jak pliki strony lub wymiany. Dane na dysku tymczasowym mogą zostać utracone podczas [zdarzenia konserwacji](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) lub gdy użytkownik [ponownego wdrażania maszyny Wirtualnej](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Podczas standardowego ponownego uruchomienia maszyny wirtualnej ma utrwalić danych na dysku tymczasowym.

Dysku tymczasowym jest oznaczony jako dysk D: domyślnie i używany do przechowywania pagefile.sys. Aby mapować dysk inną literę dysku, zobacz [zmienić literę dysku tymczasowym systemu Windows](change-drive-letter.md). Rozmiar dysku tymczasowym różni się zależnie od rozmiaru maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [maszyn wirtualnych rozmiary dla systemu Windows](sizes.md).

Aby uzyskać więcej informacji o używaniu dysku tymczasowym Azure, zobacz [opis dysku tymczasowym na maszynach wirtualnych Azure firmy Microsoft](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)


### <a name="data-disk"></a>Dysk z danymi
Dysk z danymi jest wirtualnego dysku twardego, który jest dołączony do maszyny wirtualnej do przechowywania danych aplikacji lub innych danych, które należy zachować. Dyski danych są rejestrowane jako dyski SCSI i są oznaczone literą, którą można wybrać. Każdy dysk danych ma maksymalną pojemność 4095 GB. Rozmiar maszyny wirtualnej Określa, jak wiele dysków z danymi można dołączyć do jego i typ magazynu służy do obsługi dysków.

> [!NOTE]
> Aby uzyskać więcej informacji na temat pojemności maszyn wirtualnych, zobacz [maszyn wirtualnych rozmiary dla systemu Windows](sizes.md).
> 

Azure tworzy dysk systemu operacyjnego, podczas tworzenia maszyny wirtualnej z obrazu. Jeśli używasz obrazu, który zawiera dyski danych Azure tworzy także dysków danych podczas tworzenia maszyny wirtualnej. W przeciwnym razie możesz dodać dysk danych, po utworzeniu maszyny wirtualnej.

Można dodać dysków z danymi do maszyny wirtualnej w dowolnym momencie przez **dołączanie** dysku do maszyny wirtualnej. Możesz użyć wirtualnego dysku twardego, który został przekazany lub kopiowane do konta magazynu, lub pusty wirtualny dysk twardy tworzy Azure. Dołączenie dysku danych skojarzenie pliku wirtualnego dysku twardego z maszyną Wirtualną przez umieszczenie dzierżawy na dysku VHD nie może być usunięty z magazynu nadal będzie dołączony.


[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>Zalecamy ostatniego: Użyj PRZYCINANIE z niezarządzanego dyski standardowe 

Jeśli używasz niezarządzane dyski standardowe (HDD), należy włączyć PRZYCINANIE. PRZYCINANIE odrzuca nieużywanych bloków na dysku dzięki rozliczenie jest przeprowadzane tylko dla magazynu, który faktycznie używasz. Można to zapisanie kosztów, jeśli Tworzenie dużych plików, a następnie usuń je. 

Można uruchomić tego polecenia, aby sprawdzić ustawienie PRZYCINANIA. Otwórz wiersz polecenia na Twojej maszyny Wirtualnej systemu Windows i wpisz:


```
fsutil behavior query DisableDeleteNotify
```

Jeśli polecenie zwróci wartość 0, PRZYCINANIE jest prawidłowo włączona. Jeśli zmienna zwraca 1, uruchom następujące polecenie, aby umożliwić PRZYCINANIE:

```
fsutil behavior set DisableDeleteNotify 0
```

> [!NOTE]
> Uwaga: Obsługa funkcji usuwania rozpoczyna się od systemu Windows Server 2012 / Windows 8 lub nowszym, można znaleźć w temacie [nowy interfejs API umożliwia aplikacjom wysyłania "PRZYCINANIE i Usuń mapowanie" wskazówki na nośnikach magazynowania](https://msdn.microsoft.com/windows/compatibility/new-api-allows-apps-to-send-trim-and-unmap-hints).
> 

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>Kolejne kroki
* [Dołączenie dysku](attach-disk-portal.md) dodać dodatkowy magazyn dla maszyny Wirtualnej.
* [Utwórz migawkę](snapshot-copy-managed-disk.md).
* [Konwertuj na dyskach zarządzanych](convert-unmanaged-to-managed-disks.md).


