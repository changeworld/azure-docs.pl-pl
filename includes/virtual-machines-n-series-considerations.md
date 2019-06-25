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
ms.openlocfilehash: 1de7221f100077e07a2211bdb94e0198b35cb77c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183192"
---
## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

* Aby uzyskać dostępność maszyn wirtualnych serii N, zobacz [dostępność produktów według regionów](https://azure.microsoft.com/regions/services/).

* Maszyny wirtualne z serii N można wdrożyć tylko w modelu wdrażania usługi Resource Manager.

* Maszyny wirtualne z serii N różnią się w typie usługi Azure Storage, które obsługują ich dysków. NC i NV maszyn wirtualnych obsługują tylko dyski maszyn wirtualnych, które są wspierane przez standardowy dysk Disk Storage (HDD). NCv2, NCv3, ND, NDv2 i NVv2 maszyn wirtualnych obsługują tylko dyski maszyn wirtualnych, które są wspierane przez usługi Premium Disk Storage (SSD).

* Jeśli chcesz wdrożyć więcej niż kilka maszyn wirtualnych serii N, należy wziąć pod uwagę subskrypcji zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

* Może być konieczne zwiększyć limit przydziału rdzeni (na region) w ramach subskrypcji platformy Azure i zwiększyć limit przydziału rdzeni NC, NCv2, NCv3, ND, NDv2, NV lub NVv2 oddzielne. Aby zażądać zwiększenia limitu przydziału [Otwórz żądanie obsługi klienta online](../articles/azure-supportability/how-to-create-azure-support-request.md) bez dodatkowych opłat. Domyślne limity mogą się różnić w zależności od kategorii Twojej subskrypcji.




