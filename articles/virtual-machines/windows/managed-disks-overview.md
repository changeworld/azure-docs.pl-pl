---
title: Azure Disk Storage Omówienie dysku zarządzanego dla maszyn wirtualnych z systemem Windows | Microsoft Docs
description: Omówienie usługi Azure Managed disks, która obsługuje konta magazynu w przypadku korzystania z maszyn wirtualnych z systemem Windows Azure
author: roygara
ms.service: virtual-machines-windows
ms.topic: overview
ms.date: 08/15/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c71d51580c064b5b2b0bbbbf7bf95d50aa40f72f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515554"
---
# <a name="introduction-to-azure-managed-disks"></a>Wprowadzenie do usługi Azure Managed disks

Dysk zarządzany przez platformę Azure to wirtualny dysk twardy (VHD). Można traktować go jako dysk fizyczny na serwerze lokalnym, ale zwirtualizowany. Azure Managed disks są przechowywane jako stronicowe obiekty blob, które są losowym obiektem magazynu we/wy na platformie Azure. Nazywamy zarządzany dysk zarządzany, ponieważ jest to Abstrakcja dla stronicowych obiektów blob, kontenerów obiektów blob i kont usługi Azure Storage. W przypadku dysków zarządzanych wszystkie czynności, które należy wykonać, udostępniają dysk, a platforma Azure zajmie się resztą.

Gdy wybierzesz używanie usługi Azure Managed disks z obciążeniami, platforma Azure utworzy dysk i będzie nim zarządzać. Dostępne typy dysków to Ultra dysk, dysk półprzewodnikowy Premium (SSD), SSD w warstwie Standardowa i standardowy dysk twardy. Aby uzyskać więcej informacji na temat poszczególnych typów dysków, zobacz [Wybieranie typu dysku dla maszyn wirtualnych IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Wybierz typ dysku dla maszyn wirtualnych IaaS](disks-types.md)