---
title: Rozwiązywanie problemów z dysków dołączonych do maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Magazyn obiektów Blob platformy Azure służy do przechowywania dużych ilości danych obiektów niestrukturalnych, takich jak dane tekstowe lub binarne. Twoje aplikacje mają dostęp do obiektów w magazynie obiektów Blob za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure, z kodu za pomocą bibliotek klienta magazynu Azure lub za pośrednictwem REST.
services: storage
author: genlin
manager: cshepard
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 766062b085c359499046151f337921a51d948715
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362710"
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
