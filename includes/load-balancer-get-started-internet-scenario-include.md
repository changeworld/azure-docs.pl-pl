---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516996"
---
W tym scenariuszu zostaną wykonane następujące zadania:

* Utworzenie modułu równoważenia obciążenia, który odbiera ruch sieciowy na porcie 80 i wysyła go do maszyn wirtualnych „web1” i „web2”, równoważąc ich obciążenie.
* Utworzenie reguł NAT dotyczących dostępu do pulpitu zdalnego lub dostępu SSH na maszynach wirtualnych za modułem równoważenia obciążenia.
* Utworzenie sond kondycji.

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)