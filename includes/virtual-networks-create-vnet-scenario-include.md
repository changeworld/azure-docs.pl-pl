---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cff737b8c79c44494cb0151d6a6281550401b26e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
## <a name="scenario"></a>Scenariusz

Aby zilustrować tworzenie sieci wirtualnej i podsieci, w niniejszym dokumencie użyto następujący scenariusz:

![Scenariusz sieci wirtualnych](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

W tym scenariuszu należy utworzyć sieć wirtualną o nazwie **TestVNet**, z zarezerwowanym blokiem CIDR **192.168.0.0./16**. Sieć wirtualna zawiera następujące podsieci: 

* **FrontEnd**, używająca bloku CIDR **192.168.1.0/24**.
* **BackEnd**, używająca bloku CIDR **192.168.2.0/24**.

