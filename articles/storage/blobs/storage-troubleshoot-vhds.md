---
title: Rozwiązywanie problemów z dyskami podłączonymi do maszyn wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: Zawiera łącza do zasobów rozwiązywania problemów z wirtualnymi dyskami twardymi maszyny wirtualnej platformy Azure(VHD).
services: storage
author: roygara
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: b81c0947327dc8e84c5d3fbbecde3aa31313cc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061181"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Rozwiązywanie problemów z dyskami podłączonymi do maszyn wirtualnych platformy Azure 

Maszyny wirtualne platformy Azure (VM) polegają na wirtualnych dyskach twardych (VHD) dla dysku systemu operacyjnego i wszystkich dołączonych dysków z danymi. Dyski VHD są przechowywane jako obiekty blob strony w co najmniej jednym kontie usługi Azure Storage. W tym artykule wskazano do rozwiązywania problemów z zawartością dla typowych problemów, które mogą pojawić się z VHDs. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Rozwiązywanie problemów z błędami usuwania magazynu dla maszyny Wirtualnej

W niektórych przypadkach może wystąpić błąd podczas usuwania zasobu magazynu, gdy maszyna wirtualna we wdrożeniu Menedżera zasobów zawiera dołączone dyski VHD. Aby uzyskać pomoc dotyczącą rozwiązania tego problemu, zobacz jeden z następujących artykułów: 

  * Na maszynach wirtualnych z systemem Linux: [błędy usuwania magazynu we wdrażaniu Menedżera zasobów](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Na maszynach wirtualnych systemu Windows: [błędy usuwania magazynu we wdrażaniu Menedżera zasobów](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Rozwiązywanie problemów z nieoczekiwanym ponownym uruchomieniem maszyn wirtualnych za pomocą dołączonych wirtualnych

Jeśli wystąpią nieoczekiwane ponowne uruchomienie maszyny Wirtualnej z dużą liczbą dołączonych wirtualnych elementów, zobacz jeden z następujących artykułów:

  * Na maszynach wirtualnych z systemem Linux: [nieoczekiwane ponowne uruchomienie maszyn wirtualnych z dołączonymi vhdami](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Na maszynach wirtualnych systemu Windows: [nieoczekiwane ponowne uruchomienie maszyn wirtualnych z dołączonymi wirtualnymi](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
