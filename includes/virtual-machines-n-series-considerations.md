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
ms.openlocfilehash: 202bffb03a73acde67f0d4a03b31aa9a6fbf9824
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901674"
---
## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

* Aby uzyskać dostęp do maszyn wirtualnych z serii N, zobacz [produkty dostępne według regionów](https://azure.microsoft.com/regions/services/).

* Maszyny wirtualne z serii N można wdrożyć tylko w modelu wdrażania Menedżer zasobów.

* Maszyny wirtualne serii N różnią się w zależności od typu magazynu platformy Azure obsługiwanego dla ich dysków. Maszyny wirtualne NC i NV obsługują tylko dyski maszyn wirtualnych, które są obsługiwane przez standardową Disk Storage (dysk twardy). Maszyny wirtualne NCv2, Seria NCV3, ND, NDv2 i NVv2 obsługują tylko dyski maszyn wirtualnych, których kopie zapasowe są obsługiwane przez Disk Storage Premium (SSD).

* Jeśli chcesz wdrożyć więcej niż kilka maszyn wirtualnych serii N, weź pod uwagę subskrypcję z opcją płatność zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

* Może być konieczne zwiększenie przydziału rdzeni (na region) w ramach subskrypcji platformy Azure i zwiększenie oddzielnego przydziału rdzeni NC, NCv2, Seria NCV3, ND, NDv2, NV lub NVv2. Aby zażądać zwiększenia limitu przydziału, [Otwórz bezpłatnie żądanie pomocy technicznej w trybie online](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) . Limity domyślne mogą się różnić w zależności od kategorii subskrypcji.




