---
title: Azure Disk Storage Omówienie dysku zarządzanego dla maszyn wirtualnych z systemem Linux | Microsoft Docs
description: Omówienie usługi Azure Managed disks, która obsługuje konta magazynu w przypadku korzystania z maszyn wirtualnych z systemem Linux
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 18dc1bd2eea232d0c2eb73d496dd4bd9d2d5016e
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "64707808"
---
# <a name="introduction-to-azure-managed-disks"></a>Wprowadzenie do usługi Azure Managed disks

Dysk zarządzany przez platformę Azure to wirtualny dysk twardy (VHD). Można traktować go jako dysk fizyczny na serwerze lokalnym, ale zwirtualizowany. Azure Managed disks są przechowywane jako stronicowe obiekty blob, które są losowym obiektem magazynu we/wy na platformie Azure. Nazywamy zarządzany dysk zarządzany, ponieważ jest to Abstrakcja dla stronicowych obiektów blob, kontenerów obiektów blob i kont usługi Azure Storage. W przypadku dysków zarządzanych wszystkie czynności, które należy wykonać, udostępniają dysk, a platforma Azure zajmie się resztą.

Gdy wybierzesz używanie usługi Azure Managed disks z obciążeniami, platforma Azure utworzy dysk i będzie nim zarządzać. Dostępne typy dysków to Ultra disks (wersja zapoznawcza), dyski półprzewodnikowe (SSD), standardowy dysk SSD i standardowe dyski twarde (dysk twardy). Aby uzyskać więcej informacji na temat poszczególnych typów dysków, zobacz [Wybieranie typu dysku dla maszyn wirtualnych IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Wybierz typ dysku dla maszyn wirtualnych IaaS](disks-types.md)
