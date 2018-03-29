---
title: Zdalne połączenia do węzła klastra usługi sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zdalne połączenia z wystąpieniem zestaw skalowania (węzłem klastra usługi sieć szkieletowa).
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: 8c7d5446429089a0fc931175b55e81e1ad0c97a0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Połączenie zdalne wystąpienie zestawu skali maszyny wirtualnej lub węzła klastra
W sieci szkieletowej usług klastra z systemem Azure, każdy typ węzła klastra, który należy zdefiniować [konfiguruje oddzielne skali maszyny wirtualnej](service-fabric-cluster-nodetypes.md).  Użytkownik może zdalnego połączyć się z określoną zestaw wystąpień (lub węzły klastra).  W przeciwieństwie do maszyn wirtualnych z jednego wystąpienia wystąpienia zestawu skali nie mają własne wirtualne adresy IP. To może być wyzwaniem, jeśli szukasz adres IP i port, który umożliwia zdalne połączenia z określonego wystąpienia.

Aby znaleźć adres IP i port, który umożliwia zdalne połączenia z konkretnym wystąpieniem, wykonaj następujące kroki.

1. Znaleźć wirtualnego adresu IP dla typu węzła, pobierając reguły NAT ruchu przychodzącego dla protokołu RDP (Remote Desktop).

    Najpierw pobierz zdefiniowane jako część definicji zasobu dla wartości reguły ruchu przychodzącego translatora adresów Sieciowych `Microsoft.Network/loadBalancers`.
    
    W portalu Azure, na stronie usługi równoważenia obciążenia, wybierz **ustawienia** > **reguł ruchu przychodzącego translatora adresów Sieciowych**. Zapewnia to adres IP i port, który umożliwia zdalne połączenia z pierwszej skali Ustaw wystąpienia. 
    
    ![Moduł równoważenia obciążenia][LBBlade]
    
    Na poniższej ilustracji, adres IP i port są **104.42.106.156** i **3389**.
    
    ![Reguły translatora adresów sieciowych][NATRules]

2. Znajdź port, który umożliwia zdalne połączenia z wystąpienia zestawu skali określonych lub węzeł.

    Skala ustawioną mapy wystąpień węzłów. Umożliwia określenie dokładnej port używany informacji zestaw skali.
    
    Porty są przydzielane w kolejności rosnącej, odpowiadający wystąpienia zestawu skali. Na przykład wcześniej typu węzła serwera sieci Web w poniższej tabeli wymieniono portów dla wszystkich wystąpień pięcioma węzłami. Zastosowanie tego samego mapowania do Twojego wystąpienia zestawu skali.
    
    | **Wystąpienia zestawu skalowania maszyn wirtualnych** | **Port** |
    | --- | --- |
    | FrontEnd_0 |3389 |
    | FrontEnd_1 |3390 |
    | FrontEnd_2 |3391 |
    | FrontEnd_3 |3392 |
    | FrontEnd_4 |3393 |
    | FrontEnd_5 |3394 |

3. Zdalne połączenia z wystąpieniem zestawu określoną skalę.

    Na poniższym rysunku pokazano, przy użyciu usługi Podłączanie pulpitu zdalnego w celu nawiązywania wystąpienia zestawu skali FrontEnd_1:
    
    ![Podłączanie pulpitu zdalnego][RDP]


Dalsze czynności przeczytaj następujące artykuły:
* Zobacz [omówienie funkcji "Wdrażanie z dowolnego miejsca" i porównanie z klastrami zarządzane Azure](service-fabric-deploy-anywhere.md).
* Dowiedz się więcej o [klastra zabezpieczeń](service-fabric-cluster-security.md).
* [Zaktualizuj wartości zakresu portów protokołu RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) w klastrze maszyn wirtualnych po wdrożeniu
* [Zmień nazwa użytkownika i hasło](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) klastra maszyny wirtualne

<!--Image references-->
[LBBlade]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/NATRules.png
[RDP]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/RDP.png
