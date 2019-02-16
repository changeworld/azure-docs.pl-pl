---
title: Usługa Azure Disk Storage managed dyskami — omówienie maszyn wirtualnych systemu Linux | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure managed disks, w której obsługuje konta magazynu dla Ciebie podczas korzystania z maszyn wirtualnych systemu Linux
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/11/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d8394121019338b53c87f7229d0ccf52fbdf8f21
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327031"
---
# <a name="introduction-to-azure-managed-disks"></a>Wprowadzenie do usługi Azure managed disks

Zarządzane przez platformę Azure dysk jest wirtualny dysk twardy (VHD). Można traktować je jak dysk fizyczny na serwerze w środowisku lokalnym, ale zwirtualizowanych. Usługi Azure managed disks są przechowywane jako stronicowe obiekty BLOB, które są losowych operacji We/Wy obiektu magazynu na platformie Azure. Nazywamy dysku zarządzanego "zarządzana", ponieważ jest klasą abstrakcyjną za pośrednictwem stronicowe obiekty BLOB i kontenery obiektów blob oraz konta usługi Azure storage. W przypadku dysków zarządzanych wszystko, co należy zrobić to aprowizowanie dysku, a platforma Azure zajmie się resztą.

Po wybraniu za pomocą platformy Azure usługi managed disks przy użyciu obciążeń, platforma Azure utworzy i zarządza dysku. Dostępne typy dysków są bardzo dysków (wersja zapoznawcza) — wersja premium dyskach półprzewodnikowych (SSD), SSD w warstwie standardowa i standardowe dyski twarde (HDD). Aby uzyskać więcej informacji o każdym typie poszczególnych dysków, zobacz [wybierz typ dysku dla maszyn wirtualnych IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Wybierz typ dysku dla maszyn wirtualnych IaaS](disks-types.md)
