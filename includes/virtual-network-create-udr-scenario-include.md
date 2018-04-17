---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: aa513d63a2af0fe994b8ab1ed7335a30998ff8ce
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>Scenariusz
Aby lepiej zilustrować tworzenie Udr, w niniejszym dokumencie użyto następujący scenariusz:

![OPIS ILUSTRACJI](./media/virtual-network-create-udr-scenario-include/figure1.png)

W tym scenariuszu utworzysz przez jeden dla *podsieci frontonu* i przez inny dla *podsieci wewnętrznej*w następujący sposób: 

* **Frontonu przez**. Frontonu przez jest stosowany do *frontonu* podsieci i może zawierać jedną trasę:    
  * **RouteToBackend**. Ta trasa wysyła cały ruch do podsieci zaplecza, aby **FW1** maszyny wirtualnej.
* **Wewnętrznej bazy danych przez**. PRZEZ zaplecza jest stosowany do *zaplecza* podsieci i może zawierać jedną trasę:    
  * **RouteToFrontend**. Ta trasa wysyła cały ruch do podsieci frontonu, aby **FW1** maszyny wirtualnej.

Kombinacja te trasy gwarantuje, że cały ruch kierowany z jednej podsieci do drugiej jest kierowany do **FW1** maszyny wirtualnej, która jest używana jako urządzenie wirtualne. Należy również włączyć funkcję przesyłania dalej dla adresu IP **FW1** maszynę Wirtualną, aby upewnić się, może odbierać ruch kierowany do innych maszyn wirtualnych.

