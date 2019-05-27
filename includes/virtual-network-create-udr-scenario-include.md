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
ms.openlocfilehash: 40b81904daabfdad7e45571d8ab86cf32cac8964
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170864"
---
## <a name="scenario"></a>Scenariusz
Aby lepiej zilustrować tworzenie tras zdefiniowanych przez użytkownika, w tym dokumencie używa następujący scenariusz:

![OPIS ILUSTRACJI](./media/virtual-network-create-udr-scenario-include/figure1.png)

W tym scenariuszu utworzysz jeden trasy zdefiniowanej przez użytkownika dla *podsieci frontonu* i innej trasy zdefiniowanej przez użytkownika dla *podsieci zaplecza*, wykonując następujące czynności: 

* **Trasa zdefiniowana przez użytkownika frontonu**. Frontonu zdefiniowanej przez użytkownika jest stosowana do *frontonu* podsieci i zawierać jedną trasę:    
  * **RouteToBackend**. Ta trasa wysyła cały ruch do podsieci zaplecza w celu **FW1** maszyny wirtualnej.
* **UDR-BackEnd**. Serwer zaplecza zdefiniowanej przez użytkownika jest stosowana do *zaplecza* podsieci i zawierać jedną trasę:    
  * **RouteToFrontend**. Ta trasa wysyła cały ruch do podsieci frontonu do **FW1** maszyny wirtualnej.

Kombinacja te trasy gwarantuje, że cały ruch kierowany z jednej podsieci do innej jest kierowany do **FW1** maszynę wirtualną, która jest używana jako urządzenie wirtualne. Należy również włączyć funkcję przekazywania dla adresu IP **FW1** maszynę Wirtualną, aby upewnić się, może ona odbierać ruch kierowany do innych maszyn wirtualnych.

