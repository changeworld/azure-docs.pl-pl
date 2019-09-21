---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 14f5998ee1c562b649257f7dce9ffc2f52a66226
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174979"
---
## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

* Aby uzyskać dostęp do maszyn wirtualnych z serii N, zobacz [produkty dostępne według regionów](https://azure.microsoft.com/regions/services/).

* Maszyny wirtualne z serii N można wdrożyć tylko w modelu wdrażania Menedżer zasobów.

* Maszyny wirtualne serii N różnią się w zależności od typu magazynu platformy Azure obsługiwanego dla ich dysków. Maszyny wirtualne NC i NV obsługują tylko dyski maszyn wirtualnych, które są obsługiwane przez standardową Disk Storage (dysk twardy). Maszyny wirtualne NCv2, Seria NCV3, ND, NDv2 i NVv2 obsługują tylko dyski maszyn wirtualnych, których kopie zapasowe są obsługiwane przez Disk Storage Premium (SSD).

* Jeśli chcesz wdrożyć więcej niż kilka maszyn wirtualnych serii N, weź pod uwagę subskrypcję z opcją płatność zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

* Może być konieczne zwiększenie przydziału rdzeni (na region) w ramach subskrypcji platformy Azure i zwiększenie oddzielnego przydziału rdzeni NC, NCv2, Seria NCV3, ND, NDv2, NV lub NVv2. Aby zażądać zwiększenia limitu przydziału, [Otwórz bezpłatnie żądanie pomocy technicznej w trybie online](../articles/azure-supportability/how-to-create-azure-support-request.md) . Limity domyślne mogą się różnić w zależności od kategorii subskrypcji.




