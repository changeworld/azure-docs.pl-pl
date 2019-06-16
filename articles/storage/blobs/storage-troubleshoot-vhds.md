---
title: Rozwiązywanie problemów z dysków dołączonych do maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Zawiera łącza do rozwiązywania problemów z zasobami dla wirtualnych dysków twardych (VHD) maszyny wirtualnej platformy Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: c00fdf3ad02edc1faf0d3257d3836a1c2f44d682
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65150768"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Rozwiązywanie problemów z dysków dołączonych do maszyn wirtualnych platformy Azure 

Azure Virtual Machines (VMs) polegają na wirtualne dyski twarde (VHD) dla dysku systemu operacyjnego i wszystkie dołączone dyski danych. Wirtualne dyski twarde są przechowywane jako stronicowe obiekty BLOB w co najmniej jedno konto usługi Azure Storage. W tym artykule wskazuje rozwiązywania typowych problemów, które mogą wystąpić przy użyciu wirtualnych dysków twardych. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Rozwiązywanie problemów z usuwaniem magazynu dla maszyny Wirtualnej

W niektórych przypadkach może wystąpić błąd podczas usuwania zasobów magazynu, gdy maszyna wirtualna, w ramach wdrożenia usługi Resource Manager zawiera dołączonych wirtualnych dysków twardych. Aby uzyskać pomoc w rozwiązaniu tego problemu zobacz jeden z następujących artykułów: 

  * Na maszynach wirtualnych systemu Linux: [Błędy usuwania magazynu podczas wdrażania usługi Resource Manager](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Na maszynach wirtualnych Windows: [Błędy usuwania magazynu podczas wdrażania usługi Resource Manager](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Rozwiązywanie problemów z nieoczekiwane ponowne uruchamianie maszyn wirtualnych z dołączonymi dyskami VHD

Jeśli wystąpi nieoczekiwane ponowne uruchamianie maszyny Wirtualnej o dużej liczby dołączonych wirtualnych dysków twardych, zobacz jeden z następujących artykułów:

  * Na maszynach wirtualnych systemu Linux: [Nieoczekiwane ponowne uruchamianie maszyn wirtualnych z dołączonymi dyskami VHD](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Na maszynach wirtualnych Windows: [Nieoczekiwane ponowne uruchamianie maszyn wirtualnych z dołączonymi dyskami VHD](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
