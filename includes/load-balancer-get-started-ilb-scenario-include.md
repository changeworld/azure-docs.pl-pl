---
author: kumudD
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: f0e575af51f952a80fe42102b033727713c75cf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122268"
---
## <a name="configuration-scenario"></a>Scenariusz konfiguracji

W tym scenariuszu tworzymy wewnętrzny moduł równoważenia obciążenia w sieci wirtualnej, co zostało pokazane na poniższej ilustracji:

![Scenariusz wewnętrznego modułu równoważenia obciążenia](./media/load-balancer-get-started-ilb-scenario-include/figure1.png)

Konfiguracja w naszym scenariuszu jest następująca:

* Dwie maszyny wirtualne o nazwach **DB1** i **DB2**
* Punkty końcowe dla wewnętrznego modułu równoważenia obciążenia
* Wewnętrzny moduł równoważenia obciążenia
