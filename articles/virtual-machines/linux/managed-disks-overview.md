---
title: Przegląd Azure Disk Storage dla maszyn wirtualnych z systemem Linux
description: Omówienie usługi Azure Managed disks, która obsługuje konta magazynu w przypadku korzystania z maszyn wirtualnych z systemem Linux
author: roygara
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2b25da174399b092fe821a46b235d7a2bd14572b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355913"
---
# <a name="introduction-to-azure-managed-disks"></a>Wprowadzenie do usługi Azure Managed disks

Azure Managed disks to woluminy magazynu na poziomie bloku, które są zarządzane przez platformę Azure i używane z usługą Azure Virtual Machines. Dyski zarządzane są podobne do dysku fizycznego na serwerze lokalnym, ale zwirtualizowane. W przypadku dysków zarządzanych należy określić rozmiar dysku, typ dysku i zainicjować obsługę administracyjną dysku. Po udostępnieniu dysku platforma Azure obsługuje pozostałe.

Dostępne typy dysków to Ultra disks, dyski półprzewodnikowe w warstwie Premium (SSD), standardowe dysków SSD i standardowe dyski twarde (dysk twardy). Aby uzyskać informacje o poszczególnych typach dysków, zobacz [Wybieranie typu dysku dla maszyn wirtualnych IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Wybierz typ dysku dla maszyn wirtualnych IaaS](disks-types.md)