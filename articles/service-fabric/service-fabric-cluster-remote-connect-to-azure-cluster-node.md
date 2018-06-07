---
title: Zdalne połączenia do węzła klastra usługi sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zdalne połączenia z wystąpieniem zestaw skalowania (węzłem klastra usługi sieć szkieletowa).
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 28424f9a7a0f77882ee3360c5599549303075c18
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642577"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Połączenie zdalne wystąpienie zestawu skali maszyny wirtualnej lub węzła klastra
W sieci szkieletowej usług klastra z systemem Azure, każdy typ węzła klastra, który należy zdefiniować [konfiguruje oddzielne skali maszyny wirtualnej](service-fabric-cluster-nodetypes.md).  Użytkownik może połączenie zdalne wystąpienia zestawu skali określonych (węzły klastra).  W przeciwieństwie do maszyn wirtualnych z jednego wystąpienia wystąpienia zestawu skali nie mają własne wirtualne adresy IP. To może być wyzwaniem, jeśli szukasz adres IP i port, który umożliwia zdalne połączenia z określonego wystąpienia.

Aby znaleźć adres IP i port, który umożliwia zdalne połączenia z konkretnym wystąpieniem, wykonaj następujące kroki.

1. Pobierz reguły NAT ruchu przychodzącego dla protokołu RDP (Remote Desktop).

    Zazwyczaj każdy typ węzła zdefiniowane w klastrze ma własną wirtualnego adresu IP i modułu równoważenia obciążenia dedykowanych. Domyślnie usługi równoważenia obciążenia dla typu węzła nosi nazwę o następującym formacie: *LB-{nazwa klastra}-{typu węzła}*, na przykład *LB-mójklaster frontonu*. 
    
    Na stronie przez moduł równoważenia obciążenia w portalu Azure wybierz **ustawienia** > **reguł ruchu przychodzącego translatora adresów Sieciowych**: 

    ![Reguły NAT ruchu przychodzącego modułu równoważenia obciążenia](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Poniższy zrzut ekranu przedstawia reguły NAT ruchu przychodzącego dla typu węzła o nazwie serwera sieci Web: 

    ![Reguły NAT ruchu przychodzącego modułu równoważenia obciążenia](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Dla każdego węzła adres IP znajduje się w **docelowego** kolumny, **docelowej** kolumna podaje wystąpienia zestawu skali i **usługi** kolumna zawiera numer portu. Dla połączenia zdalnego porty są przydzielone do każdego węzła w rosnącej kolejności od portu 3389.

    Możesz również znaleźć reguł ruchu przychodzącego translatora adresów Sieciowych w `Microsoft.Network/loadBalancers` części szablonu usługi Resource Manager dla klastra.
    
2. Aby potwierdzić portu wejściowego mapowanie portu docelowego węzła, kliknij jego reguły i przyjrzyj się **docelowy port** wartość. Poniższy zrzut ekranu przedstawia przychodzącą regułę NAT dla **frontonu (wystąpienie 1)** węzła w poprzednim kroku. Należy zauważyć, że chociaż numer portu (ruch przychodzący) jest 3390, port docelowy jest zamapowana do portu 3389, port dla usługi protokołu RDP w systemie docelowym.  

    ![Mapowanie portów docelowych](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Domyślnie w przypadku klastrów systemu Windows port docelowy jest portu 3389, która mapuje do usługi protokołu RDP w docelowym węźle. W przypadku klastrów systemu Linux port docelowy jest port 22, która mapuje do usługi Secure Shell (SSH).

3. Zdalne połączenia z określonym węźle (wystąpienia zestawu skalowania). Można użyć nazwy użytkownika i hasło ustawione podczas tworzenia klastra lub innych poświadczeń, które zostały skonfigurowane. 

    Poniższy zrzut ekranu przedstawia przy użyciu usługi Podłączanie pulpitu zdalnego w celu nawiązywania **frontonu (wystąpienie 1)** węzła w klastrze systemu Windows:
    
    ![Podłączanie pulpitu zdalnego](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    W węzłach Linux można połączyć się przy użyciu protokołu SSH (poniższy przykład ponownie używa tego samego adresu IP i portu dla skrócenia):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Dalsze czynności przeczytaj następujące artykuły:
* Zobacz [omówienie funkcji "Wdrażanie z dowolnego miejsca" i porównanie z klastrami zarządzane Azure](service-fabric-deploy-anywhere.md).
* Dowiedz się więcej o [klastra zabezpieczeń](service-fabric-cluster-security.md).
* [Zaktualizuj wartości zakresu portów protokołu RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) w klastrze maszyn wirtualnych po wdrożeniu
* [Zmień nazwa użytkownika i hasło](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) klastra maszyny wirtualne

