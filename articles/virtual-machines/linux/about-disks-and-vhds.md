---
title: Niezarządzane (stronicowych obiektów blob) i zarządzany magazyn dysków dla maszyn wirtualnych systemu Linux platformy firmy Microsoft — informacje | Dokumentacja firmy Microsoft
description: Poznaj podstawy niezarządzane (stronicowych obiektów blob), a zarządzane magazyn dysków dla maszyn wirtualnych systemu Linux na platformie Azure.
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 11/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 07d26590799f169e8e252557287b5c7e0003ea87
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469417"
---
# <a name="about-disks-storage-for-azure-linux-vms"></a>Magazyn dysków dla maszyn wirtualnych systemu Linux platformy Azure — informacje
Podobnie jak dowolny inny komputer maszyn wirtualnych na platformie Azure używać dysków jako miejsce do przechowywania systemu operacyjnego, aplikacji i danych. Wszystkie maszyny wirtualne platformy Azure ma co najmniej dwa dyski — dysk systemu operacyjnego Linux oraz dyski tymczasowe. Dysk systemu operacyjnego jest tworzone na podstawie obrazu, a dysk systemu operacyjnego i obrazu są wirtualne dyski twarde (VHD) przechowywane na koncie usługi Azure storage. Maszyny wirtualne mogą także mieć co najmniej jeden dysk danych, które również są przechowywane jako wirtualne dyski twarde.

W tym artykule firma Microsoft będzie omówiono różne sposoby zastosowania dysków, a następnie omówiono różnych typów dysków można tworzyć i używać. W tym artykule jest także dostępna dla [maszyn wirtualnych Windows](../windows/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Dysków używanych przez maszyny wirtualne

Przyjrzyjmy, jak dyski są używane przez maszyny wirtualne.

## <a name="operating-system-disk"></a>Dysk systemu operacyjnego

Do każdej maszyny wirtualnej ma jeden dysk dołączony systemu operacyjnego. Został zarejestrowany jako dyski SATA i jest oznaczona etykietą /dev/sda domyślnie. Ten dysk ma maksymalną pojemność wynoszącą 2048 gigabajtów (GB).

## <a name="temporary-disk"></a>Dysk tymczasowy

Każda maszyna wirtualna zawiera dyski tymczasowe. Dysk tymczasowy udostępnia krótkoterminowy magazyn dla aplikacji i procesów i jest przeznaczone do przechowywania tylko dane, takie jak stronicowania lub plik wymiany. Dane na dysku tymczasowym mogą zostać utracone podczas [zdarzeń związanych z konserwacją](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) lub po użytkownik [ponowne wdrożenie maszyny Wirtualnej](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Podczas standardowych ponowny rozruch maszyny wirtualnej ma utrwalić dane na dysku tymczasowego. Jednakże istnieją przypadki, w którym dane mogą nie będzie się powtarzać, takie jak przeniesienie do nowego hosta. W związku z tym wszystkie dane na dysku tymczasowego nie powinien być dane, które mają kluczowe znaczenie dla systemu.

Na maszynach wirtualnych z systemem Linux dysk jest zwykle **/dev/sdb** jest sformatowany i zamontowany **katalogu/mnt** przez agenta systemu Linux platformy Azure. Rozmiar dysku tymczasowego zależy od rozmiaru maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych systemu Linux](../windows/sizes.md).

## <a name="data-disk"></a>Dysk z danymi

Dysk danych to wirtualny dysk twardy, który jest dołączony do maszyny wirtualnej do przechowywania danych aplikacji lub innymi danymi, które należy zachować. Dyski danych są rejestrowane jako dyski SCSI i są oznaczone literą, który wybierzesz. Każdy dysk z danymi ma maksymalną pojemność wynoszącą 4095 GB. Rozmiar maszyny wirtualnej Określa, jak wiele dysków z danymi można dołączać i typ magazynu służy do obsługi dysków.

> [!NOTE]
> Aby uzyskać więcej informacji na temat pojemności maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych systemu Linux](./sizes.md).

Platforma Azure utworzy dysk systemu operacyjnego, podczas tworzenia maszyny wirtualnej z obrazu. Jeśli używasz obrazu, który zawiera dyski danych, platforma Azure utworzy również dyski z danymi podczas tworzenia maszyny wirtualnej. W przeciwnym razie możesz dodać dyski z danymi, po utworzeniu maszyny wirtualnej.

Można dodać dysków z danymi do maszyny wirtualnej w dowolnym momencie przez **dołączanie** dysku do maszyny wirtualnej. Można użyć wirtualnego dysku twardego, które zostały przekazane lub kopiowane na koncie magazynu lub taki, który platforma Azure utworzy dla Ciebie. Dołączanie dysku danych skojarzenie pliku wirtualnego dysku twardego z maszyną Wirtualną, umieszczając dzierżawy do wirtualnego dysku twardego, gdy nadal jest dołączony nie może być usunięty z magazynu.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

Rozmiary (wersja zapoznawcza), zobacz nasze [— często zadawane pytania](faq-for-disks.md#new-disk-sizes-managed-and-unmanaged) Aby dowiedzieć się, jakie regiony są one dostępne w.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Dołączanie dysku](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) można dodać dodatkowy magazyn dla maszyny Wirtualnej.
* [Utwórz migawkę](snapshot-copy-managed-disk.md).
* [Konwertowanie do usługi managed disks](convert-unmanaged-to-managed-disks.md).
