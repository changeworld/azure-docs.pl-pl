---
title: Dla maszyn wirtualnych Windows Azure Disk Storage zarządzany dyskami — omówienie | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure usługi managed disks, która obsługuje konta magazynu dla Ciebie podczas przy użyciu maszyn wirtualnych Windows Azure
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.workload: storage
ms.tgt_pltfrm: vm-windows
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: fb1ee8556935b141dfee6a18c96ecafb476aa584
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725820"
---
# <a name="introduction-to-azure-managed-disks"></a>Wprowadzenie do usługi Azure managed disks

Zarządzane przez platformę Azure dysk jest wirtualny dysk twardy (VHD). Można traktować je jak dysk fizyczny na serwerze w środowisku lokalnym, ale zwirtualizowanych. Usługi Azure managed disks są przechowywane jako stronicowe obiekty BLOB, które są losowych operacji We/Wy obiektu magazynu na platformie Azure. Nazywamy dysku zarządzanego "zarządzana", ponieważ jest klasą abstrakcyjną za pośrednictwem stronicowe obiekty BLOB i kontenery obiektów blob oraz konta usługi Azure storage. W przypadku dysków zarządzanych wszystko, co należy zrobić to aprowizowanie dysku, a platforma Azure zajmie się resztą.

Po wybraniu za pomocą platformy Azure usługi managed disks przy użyciu obciążeń, platforma Azure utworzy i zarządza dysku. Dostępne typy dysków są Ultra pełny stan dyski (SSD) (wersja zapoznawcza), w warstwie Premium SSD, SSD w warstwie standardowa i standardowych dysków twardych (HDD). Aby uzyskać więcej informacji o każdym typie poszczególnych dysków, zobacz [wybierz typ dysku dla maszyn wirtualnych IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Wybierz typ dysku dla maszyn wirtualnych IaaS](disks-types.md)