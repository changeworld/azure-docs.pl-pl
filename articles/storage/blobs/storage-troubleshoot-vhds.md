---
title: Rozwiązywanie problemów z dysków dołączonych do maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Magazyn obiektów Blob platformy Azure służy do przechowywania dużych ilości danych obiektów niestrukturalnych, takich jak dane tekstowe lub binarne. Twoje aplikacje mają dostęp do obiektów w magazynie obiektów Blob za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure, z kodu za pomocą bibliotek klienta magazynu Azure lub za pośrednictwem REST.
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.component: disks
ms.openlocfilehash: 0dbd89c28d18d64908d92cd38d8bd1cf3138fd5c
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397971"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Rozwiązywanie problemów z dysków dołączonych do maszyn wirtualnych platformy Azure 

Azure Virtual Machines (VMs) polegają na wirtualne dyski twarde (VHD) dla dysku systemu operacyjnego i wszystkie dołączone dyski danych. Wirtualne dyski twarde są przechowywane jako stronicowe obiekty BLOB w co najmniej jedno konto usługi Azure Storage. W tym artykule wskazuje rozwiązywania typowych problemów, które mogą wystąpić przy użyciu wirtualnych dysków twardych. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Rozwiązywanie problemów z usuwaniem magazynu dla maszyny Wirtualnej

W niektórych przypadkach może wystąpić błąd podczas usuwania zasobów magazynu, gdy maszyna wirtualna, w ramach wdrożenia usługi Resource Manager zawiera dołączonych wirtualnych dysków twardych. Aby uzyskać pomoc w rozwiązaniu tego problemu zobacz jeden z następujących artykułów: 

  * Na maszynach wirtualnych systemu Linux: [błędy usuwania magazynu podczas wdrażania usługi Resource Manager](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Na Windows maszyn wirtualnych: [błędy usuwania magazynu podczas wdrażania usługi Resource Manager](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Rozwiązywanie problemów z nieoczekiwane ponowne uruchamianie maszyn wirtualnych z dołączonymi dyskami VHD

Jeśli wystąpi nieoczekiwane ponowne uruchamianie maszyny Wirtualnej o dużej liczby dołączonych wirtualnych dysków twardych, zobacz jeden z następujących artykułów:

  * Na maszynach wirtualnych systemu Linux: [nieoczekiwany wykonuje ponowny rozruch maszyn wirtualnych z dołączonymi dyskami VHD](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Na Windows maszyn wirtualnych: [nieoczekiwany wykonuje ponowny rozruch maszyn wirtualnych z dołączonymi dyskami VHD](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
