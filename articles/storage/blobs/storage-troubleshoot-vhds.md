---
title: Rozwiązywanie problemów z dysków dołączonych do maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Magazyn obiektów Blob platformy Azure służy do przechowywania dużych ilości danych obiektów niestrukturalnych, takich jak dane tekstowe lub binarne. Twoje aplikacje mają dostęp do obiektów w magazynie obiektów Blob za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure, z kodu za pomocą bibliotek klienta magazynu Azure lub za pośrednictwem REST.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: tamram
ms.openlocfilehash: ae309efb95e2f633effcfb5723d8377f5e94d406
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Rozwiązywanie problemów z dysków dołączonych do maszyn wirtualnych platformy Azure 

Azure maszynach wirtualnych (VM) polegają na wirtualne dyski twarde (VHD) dla dysku systemu operacyjnego oraz wszelkie dołączonych dysków z danymi. Wirtualne dyski twarde są przechowywane jako stronicowe obiekty BLOB w co najmniej jedno konto magazynu Azure. W tym artykule wskazuje na rozwiązywanie problemów z zawartością dla typowych problemów, które mogą wynikać z wirtualnych dysków twardych. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Rozwiązywanie problemów z usuwaniem magazynu dla maszyny Wirtualnej

W niektórych przypadkach może wystąpić błąd, gdy usunięcie zasobu magazynu, gdy maszyna wirtualna we wdrożeniu usługi Resource Manager zawiera dołączonych dysków VHD. Aby uzyskać pomoc w rozwiązaniu tego problemu zobacz następujące artykuły: 

  * Na maszyn wirtualnych systemu Linux: [błędów usuwania magazynu we wdrożeniu usługi Resource Manager](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Na maszyny wirtualne systemu Windows: [błędów usuwania magazynu we wdrożeniu usługi Resource Manager](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Rozwiązywanie problemów z nieoczekiwane ponowne uruchomienie maszyn wirtualnych z dołączonych dysków VHD

Jeśli wystąpiły nieoczekiwane ponowne uruchomienia maszyny Wirtualnej o dużej liczby dołączonych dysków VHD, zobacz następujące artykuły:

  * Na maszyn wirtualnych systemu Linux: [nieoczekiwane ponowne uruchomienie maszyn wirtualnych z dołączonych dysków VHD](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Na maszyny wirtualne systemu Windows: [nieoczekiwane ponowne uruchomienie maszyn wirtualnych z dołączonych dysków VHD](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
