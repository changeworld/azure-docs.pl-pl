---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: f0e575af51f952a80fe42102b033727713c75cf9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60398794"
---
## <a name="configuration-scenario"></a>Scenariusz konfiguracji

W tym scenariuszu tworzymy wewnętrzny moduł równoważenia obciążenia w sieci wirtualnej, co zostało pokazane na poniższej ilustracji:

![Scenariusz wewnętrznego modułu równoważenia obciążenia](./media/load-balancer-get-started-ilb-scenario-include/figure1.png)

Konfiguracja w naszym scenariuszu jest następująca:

* Dwie maszyny wirtualne o nazwach **DB1** i **DB2**
* Punkty końcowe dla wewnętrznego modułu równoważenia obciążenia
* Wewnętrzny moduł równoważenia obciążenia
