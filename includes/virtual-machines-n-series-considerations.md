---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3267f649e360c512a5523ce1d5948719a1969934
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

* Dostępność N serii maszyn wirtualnych, zobacz [produkty, które są dostępne w regionie](https://azure.microsoft.com/regions/services/).

* N-series maszyny wirtualne mogą być wdrażane tylko w modelu wdrażania usługi Resource Manager.

* N-series maszyn wirtualnych różnią się w typie usługi Azure Storage obsługuje na dyskach. NC i maszyn wirtualnych z wirtualizacją sieci obsługują tylko dyski maszyn wirtualnych, które bazują przez standardowe dysku magazynu (HDD). NCv2 ND i maszyn wirtualnych NCv3 obsługują tylko dyski maszyn wirtualnych, które bazują przez magazynu dysków Premium (SSD).

* Jeśli chcesz wdrożyć więcej niż kilka N serii maszyn wirtualnych, należy wziąć pod uwagę z subskrypcji lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

* Konieczne może być Zwiększ limit przydziału rdzeni (na region) w Twojej subskrypcji platformy Azure i zwiększyć oddzielne limit przydziału rdzeni NC, NCv2, NCv3, ND lub wirtualizacją sieci. Aby zażądać zwiększenia limitu przydziału [otwarcia żądania pomocy technicznej online klienta](../articles/azure-supportability/how-to-create-azure-support-request.md) bez dodatkowych opłat. Domyślne limity może się różnić w zależności od kategorii subskrypcji.




