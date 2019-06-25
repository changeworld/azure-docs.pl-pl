---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183654"
---
W tym scenariuszu zostaną wykonane następujące zadania:

* Utworzenie modułu równoważenia obciążenia, który odbiera ruch sieciowy na porcie 80 i wysyła go do maszyn wirtualnych „web1” i „web2”, równoważąc ich obciążenie.
* Utworzenie reguł NAT dotyczących dostępu do pulpitu zdalnego lub dostępu SSH na maszynach wirtualnych za modułem równoważenia obciążenia.
* Utworzenie sond kondycji.

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)
