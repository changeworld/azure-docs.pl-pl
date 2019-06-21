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
ms.openlocfilehash: 2b1f9990985951a4e6ef260954968c0e1466c298
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183147"
---
## <a name="scenario"></a>Scenariusz

Aby zilustrować tworzenie sieci wirtualnej i podsieci, w tym dokumencie używa następujący scenariusz:

![Scenariusz sieci wirtualnych](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

W tym scenariuszu utworzysz sieć wirtualną o nazwie **TestVNet**, przy użyciu zarezerwowanego bloku CIDR **192.168.0.0./16**. Sieć wirtualna zawiera następujących podsieci: 

* **FrontEnd**, używająca bloku CIDR **192.168.1.0/24**.
* **BackEnd**, używająca bloku CIDR **192.168.2.0/24**.

