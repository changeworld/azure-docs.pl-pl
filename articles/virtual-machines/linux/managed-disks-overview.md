---
title: Usługa Azure Disk Storage managed dyskami — omówienie maszyn wirtualnych systemu Linux | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure managed disks, w której obsługuje konta magazynu dla Ciebie podczas korzystania z maszyn wirtualnych systemu Linux
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 18dc1bd2eea232d0c2eb73d496dd4bd9d2d5016e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613709"
---
# <a name="introduction-to-azure-managed-disks"></a>Wprowadzenie do usługi Azure managed disks

Zarządzane przez platformę Azure dysk jest wirtualny dysk twardy (VHD). Można traktować je jak dysk fizyczny na serwerze w środowisku lokalnym, ale zwirtualizowanych. Usługi Azure managed disks są przechowywane jako stronicowe obiekty BLOB, które są losowych operacji We/Wy obiektu magazynu na platformie Azure. Nazywamy dysku zarządzanego "zarządzana", ponieważ jest klasą abstrakcyjną za pośrednictwem stronicowe obiekty BLOB i kontenery obiektów blob oraz konta usługi Azure storage. W przypadku dysków zarządzanych wszystko, co należy zrobić to aprowizowanie dysku, a platforma Azure zajmie się resztą.

Po wybraniu za pomocą platformy Azure usługi managed disks przy użyciu obciążeń, platforma Azure utworzy i zarządza dysku. Dostępne typy dysków są bardzo dysków (wersja zapoznawcza) — wersja premium dyskach półprzewodnikowych (SSD), SSD w warstwie standardowa i standardowe dyski twarde (HDD). Aby uzyskać więcej informacji o każdym typie poszczególnych dysków, zobacz [wybierz typ dysku dla maszyn wirtualnych IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Wybierz typ dysku dla maszyn wirtualnych IaaS](disks-types.md)
