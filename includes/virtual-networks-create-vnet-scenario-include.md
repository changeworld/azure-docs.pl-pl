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
ms.openlocfilehash: 29b2ac1a5dc128028dbd40e683c1b45e6208d124
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>Scenariusz

Aby zilustrować tworzenie sieci wirtualnej i podsieci, w niniejszym dokumencie użyto następujący scenariusz:

![Scenariusz sieci wirtualnych](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

W tym scenariuszu należy utworzyć sieć wirtualną o nazwie **TestVNet**, z zarezerwowanym blokiem CIDR **192.168.0.0./16**. Sieć wirtualna zawiera następujące podsieci: 

* **FrontEnd**, używająca bloku CIDR **192.168.1.0/24**.
* **BackEnd**, używająca bloku CIDR **192.168.2.0/24**.

