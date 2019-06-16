---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122165"
---
W tym scenariuszu zostaną wykonane następujące zadania:

* Utworzenie modułu równoważenia obciążenia, który odbiera ruch sieciowy na porcie 80 i wysyła go do maszyn wirtualnych „web1” i „web2”, równoważąc ich obciążenie.
* Utworzenie reguł NAT dotyczących dostępu do pulpitu zdalnego lub dostępu SSH na maszynach wirtualnych za modułem równoważenia obciążenia.
* Utworzenie sond kondycji.

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)
