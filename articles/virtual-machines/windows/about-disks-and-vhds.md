---
title: Niezarządzane (stronicowych obiektów blob) i zarządzany magazyn dysków dla maszyn wirtualnych Windows Azure Microsoft — informacje | Dokumentacja firmy Microsoft
description: Poznaj podstawy niezarządzane (stronicowych obiektów blob), a zarządzane magazyn dysków dla maszyn wirtualnych Windows na platformie Azure.
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.tgt_pltfrm: windows
ms.topic: article
ms.date: 11/15/2017
ms.author: rogarana
ms.component: disks
ms.openlocfilehash: de5322709b5d7f7bcfe6c512bab94b025d9a56a7
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023561"
---
# <a name="about-disks-storage-for-azure-windows-vms"></a>Magazyn dysków dla maszyn wirtualnych Windows Azure — informacje

Podobnie jak dowolny inny komputer maszyn wirtualnych na platformie Azure używać dysków jako miejsce do przechowywania systemu operacyjnego, aplikacji i danych. Wszystkie maszyny wirtualne platformy Azure ma co najmniej dwa dyski — dysk systemu operacyjnego Windows oraz dyski tymczasowe. Dysk systemu operacyjnego jest tworzone na podstawie obrazu, a dysk systemu operacyjnego i obrazu są wirtualne dyski twarde (VHD) przechowywane na koncie usługi Azure storage. Maszyny wirtualne mogą także mieć co najmniej jeden dysk danych, które również są przechowywane jako wirtualne dyski twarde. 

W tym artykule firma Microsoft będzie omówiono różne sposoby zastosowania dysków, a następnie omówiono różnych typów dysków można tworzyć i używać. W tym artykule jest także dostępna dla [maszyn wirtualnych systemu Linux](../linux/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Dysków używanych przez maszyny wirtualne

Przyjrzyjmy, jak dyski są używane przez maszyny wirtualne.

### <a name="operating-system-disk"></a>Dysk systemu operacyjnego

Do każdej maszyny wirtualnej ma jeden dysk dołączony systemu operacyjnego. Ma zarejestrowany jako dysk SATA i oznaczone jako dysk C: domyślnie. Ten dysk ma maksymalną pojemność wynoszącą 2048 gigabajtów (GB).

### <a name="temporary-disk"></a>Dysk tymczasowy

Każda maszyna wirtualna zawiera dyski tymczasowe. Dysk tymczasowy udostępnia krótkoterminowy magazyn dla aplikacji i procesów i jest przeznaczone do przechowywania tylko dane, takie jak stronicowania lub plik wymiany. Dane na dysku tymczasowym mogą zostać utracone podczas [zdarzeń związanych z konserwacją](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) lub po użytkownik [ponowne wdrożenie maszyny Wirtualnej](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Podczas pomyślnego standardowa ponowny rozruch maszyny wirtualnej utrwalać danych na dysku tymczasowego. Jednakże istnieją przypadki, w którym dane mogą nie będzie się powtarzać, takie jak przeniesienie do nowego hosta. W związku z tym wszystkie dane na dysku tymczasowego nie powinien być dane, które mają kluczowe znaczenie dla systemu.

Dysk tymczasowy jest oznaczony jako dysku D: domyślnie i używany do przechowywania pagefile.sys. Aby ponownie zamapować dysk inną literę dysku, zobacz [zmienić literę dysku tymczasowego Windows](change-drive-letter.md). Rozmiar dysku tymczasowego zależy od rozmiaru maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [maszyn wirtualnych rozmiary dla Windows](sizes.md).

Aby uzyskać więcej informacji o używaniu platformy Azure na dysk tymczasowy, zobacz [opis dysku tymczasowego na Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

### <a name="data-disk"></a>Dysk z danymi

Dysk danych to wirtualny dysk twardy, który jest dołączony do maszyny wirtualnej do przechowywania danych aplikacji lub innymi danymi, które należy zachować. Dyski danych są rejestrowane jako dyski SCSI i są oznaczone literą, który wybierzesz. Każdy dysk z danymi ma maksymalną pojemność wynoszącą 4095 GB, dyski zarządzane ma maksymalną pojemność wynoszącą 32 767 TiB. Rozmiar maszyny wirtualnej Określa, jak wiele dysków z danymi można dołączać i typ magazynu służy do obsługi dysków.

> [!NOTE]
> Aby uzyskać więcej informacji na temat pojemności maszyn wirtualnych, zobacz [maszyn wirtualnych rozmiary dla Windows](sizes.md).

Platforma Azure utworzy dysk systemu operacyjnego, podczas tworzenia maszyny wirtualnej z obrazu. Jeśli używasz obrazu, który zawiera dyski danych, platforma Azure utworzy również dyski z danymi podczas tworzenia maszyny wirtualnej. W przeciwnym razie możesz dodać dyski z danymi, po utworzeniu maszyny wirtualnej.

Można dodać dysków z danymi do maszyny wirtualnej w dowolnym momencie przez **dołączanie** dysku do maszyny wirtualnej. Możesz użyć dysku VHD, które zostały przekazane lub skopiowany do swojego konta magazynu lub użyj pustego wirtualnego dysku twardego, platforma Azure utworzy dla Ciebie. Dołączanie dysku danych skojarzenie pliku wirtualnego dysku twardego z maszyną Wirtualną, umieszczając dzierżawy do wirtualnego dysku twardego, gdy nadal jest dołączony nie może być usunięty z magazynu.


[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>Jedno zalecenie ostatniego: Użyj TRIM z niezarządzanych dysków w warstwie standardowa

Jeśli używasz niezarządzanych dysków w warstwie standardowa (HDD), należy włączyć PRZYCINANIE. TRIM odrzuca nieużywanych bloków na dysku, dzięki czemu możesz naliczana tylko za rzeczywiście używasz magazynu. To zmniejszyć koszty Jeśli utworzysz dużych plików, a następnie je usunąć.

Można uruchomić to polecenie, aby sprawdzić ustawienie PRZYCINANIA. Otwórz wiersz polecenia na maszynie Wirtualnej Windows i wpisz:

```
fsutil behavior query DisableDeleteNotify
```

Jeśli polecenie zwróci wartość 0, TRIM jest prawidłowo włączone. Jeśli zwraca wartość 1, uruchom następujące polecenie, aby umożliwić PRZYCINANIE:

```
fsutil behavior set DisableDeleteNotify 0
```

> [!NOTE]
> Uwaga: Obsługa funkcji usuwania zaczyna się od systemu Windows Server 2012 / systemu Windows 8 i nowszym zobacz [nowy interfejs API umożliwia aplikacji wysyłanie wskazówki "TRIM i mapowania" nośniku magazynującym,](https://msdn.microsoft.com/windows/compatibility/new-api-allows-apps-to-send-trim-and-unmap-hints).
> 

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>Kolejne kroki
* [Dołączanie dysku](attach-disk-portal.md) można dodać dodatkowy magazyn dla maszyny Wirtualnej.
* [Utwórz migawkę](snapshot-copy-managed-disk.md).
* [Konwertowanie do usługi managed disks](convert-unmanaged-to-managed-disks.md).


