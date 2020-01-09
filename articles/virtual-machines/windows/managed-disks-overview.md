---
title: Azure Disk Storage przegląd dla maszyn wirtualnych z systemem Windows
description: Omówienie usługi Azure Managed disks, która obsługuje konta magazynu w przypadku korzystania z maszyn wirtualnych z systemem Windows Azure
author: roygara
ms.service: virtual-machines-windows
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7b2c2c1289a40d63b2f396ee59000c3aedb14c3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460029"
---
# <a name="introduction-to-azure-managed-disks"></a>Wprowadzenie do usługi Azure Managed disks

Azure Managed disks to woluminy magazynu na poziomie bloku, które są zarządzane przez platformę Azure i używane z usługą Azure Virtual Machines. Dyski zarządzane są podobne do dysku fizycznego na serwerze lokalnym, ale zwirtualizowane. W przypadku dysków zarządzanych należy określić rozmiar dysku, typ dysku i zainicjować obsługę administracyjną dysku. Po udostępnieniu dysku platforma Azure obsługuje pozostałe.

Dostępne typy dysków to Ultra disks, dyski półprzewodnikowe w warstwie Premium (SSD), standardowe dysków SSD i standardowe dyski twarde (dysk twardy). Aby uzyskać informacje o poszczególnych typach dysków, zobacz [Wybieranie typu dysku dla maszyn wirtualnych IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Wybierz typ dysku dla maszyn wirtualnych IaaS](disks-types.md)
