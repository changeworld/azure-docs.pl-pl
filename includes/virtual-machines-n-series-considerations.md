---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4bec8c8ea29c10b8c0d0351a41ebc9183bb45d4f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449515"
---
## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

* Aby uzyskać dostępność maszyn wirtualnych serii N, zobacz [dostępność produktów według regionów](https://azure.microsoft.com/regions/services/).

* Maszyny wirtualne z serii N można wdrożyć tylko w modelu wdrażania usługi Resource Manager.

* Maszyny wirtualne z serii N różnią się w typie usługi Azure Storage, które obsługują ich dysków. NC i NV maszyn wirtualnych obsługują tylko dyski maszyn wirtualnych, które są wspierane przez standardowy dysk Disk Storage (HDD). NCv2, ND i NCv3 maszyn wirtualnych obsługują tylko dyski maszyn wirtualnych, które są wspierane przez usługi Premium Disk Storage (SSD).

* Jeśli chcesz wdrożyć więcej niż kilka maszyn wirtualnych serii N, należy wziąć pod uwagę subskrypcji zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

* Może być konieczne zwiększyć limit przydziału rdzeni (na region) w ramach subskrypcji platformy Azure i zwiększyć limit przydziału rdzeni NC, NCv2, NCv3, ND lub NV oddzielne. Aby zażądać zwiększenia limitu przydziału [Otwórz żądanie obsługi klienta online](../articles/azure-supportability/how-to-create-azure-support-request.md) bez dodatkowych opłat. Domyślne limity mogą się różnić w zależności od kategorii Twojej subskrypcji.




