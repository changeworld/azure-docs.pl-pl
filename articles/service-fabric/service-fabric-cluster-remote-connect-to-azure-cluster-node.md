---
title: Połączenie zdalne do węzła klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać zdalnego wystąpienia zestawu skalowania (węzeł klastra usługi Service Fabric).
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 4cc2d6355a0147c33048f1c2c27a3648b9223db4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62110927"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Połączenie zdalne wystąpienie zestawu skalowania maszyny wirtualnej lub węzła klastra
W usłudze Service Fabric cluster działających na platformie Azure, każdy typ węzła klastra, który zdefiniujesz [konfiguruje skalowania maszyn wirtualnych z oddzielnych](service-fabric-cluster-nodetypes.md).  Użytkownik może łączenie się zdalne z wystąpieniami zestawu skalowania określonych (węzły klastra).  W przeciwieństwie do maszyn wirtualnych z jednego wystąpienia wystąpień zestawu skalowania nie mają własne wirtualne adresy IP. To może stanowić wyzwanie, gdy potrzebujesz adresu IP i port, który służy do zdalnego łączenia z określonego wystąpienia.

Aby znaleźć adres IP i port, który służy do zdalnego łączenia z określonego wystąpienia, wykonaj następujące czynności.

1. Pobierz reguły dla ruchu przychodzącego translatora adresów Sieciowych dla protokołu RDP (Remote Desktop).

    Zazwyczaj każdy zdefiniowany typ węzła w klastrze ma swój własny wirtualnego adresu IP i modułu równoważenia obciążenia dedykowanych. Domyślnie moduł równoważenia obciążenia dla typu węzła nosi nazwę w następującym formacie: *LB-{nazwa klastra}-{typ węzła}* , na przykład *mycluster-LB-FrontEnd*. 
    
    Na stronie modułu równoważenia obciążenia w witrynie Azure portal wybierz **ustawienia** > **reguły NAT ruchu przychodzącego**: 

    ![Reguły NAT dla ruchu przychodzącego modułu równoważenia obciążenia](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Poniższy zrzut ekranu przedstawia reguły NAT dla ruchu przychodzącego dla typu węzła o nazwie FrontEnd: 

    ![Reguły NAT dla ruchu przychodzącego modułu równoważenia obciążenia](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Dla każdego węzła, adres IP będzie widoczny w **DOCELOWY** kolumnie **docelowej** kolumny udostępnia wystąpienia w zestawie skalowania i **usługi** kolumna zawiera numer portu. Dla połączenia zdalnego porty są przydzielone do każdego węzła w kolejności rosnącej kolejności od portu 3389.

    Możesz również znaleźć reguły NAT ruchu przychodzącego w `Microsoft.Network/loadBalancers` części szablonu usługi Resource Manager dla klastra.
    
2. Aby potwierdzić portu wejściowego do mapowania portów docelowych dla węzła, kliknij jej regułą i spójrz na **docelowy port** wartość. Poniższy zrzut ekranu przedstawia reguły NAT dla ruchu przychodzącego **frontonu (wystąpienia 1)** węzła w poprzednim kroku. Należy zauważyć, że mimo że numer portu (ruch przychodzący) 3390, port docelowy jest mapowany do portu 3389, port usługi protokołu RDP w elemencie docelowym.  

    ![Mapowanie portów docelowych](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Domyślnie w przypadku klastrów Windows port docelowy jest portu 3389, która mapuje do usług protokołu RDP w docelowym węźle. W przypadku klastrów systemu Linux port docelowy jest port 22, która mapuje do usługi Secure Shell (SSH).

3. Zdalne połączenia z określonego węzła (wystąpienie zestawu skalowania). Można użyć nazwy użytkownika i hasła, który został ustawiony podczas tworzenia klastra lub innych poświadczeń, które zostały skonfigurowane. 

    Poniższy zrzut ekranu przedstawia, aby nawiązać połączenie przy użyciu usługi Podłączanie pulpitu zdalnego **frontonu (wystąpienia 1)** węzła w klastrze Windows:
    
    ![Podłączanie pulpitu zdalnego](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    W przypadku węzłów systemu Linux można połączyć się przy użyciu protokołu SSH (poniższy przykład używa tego samego adresu IP i portu dla zwięzłości):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Dalsze czynności przeczytaj następujące artykuły:
* Zobacz [omówienie funkcji "Wdrażanie w dowolnym miejscu" oraz porównanie ich z klastrami usługi Azure managed](service-fabric-deploy-anywhere.md).
* Dowiedz się więcej o [klastra zabezpieczeń](service-fabric-cluster-security.md).
* [Aktualizowanie wartości zakresu portów protokołu RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) w klastrze maszyn wirtualnych po wdrożeniu
* [Zmień nazwę i hasło administratora](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) klastra maszyn wirtualnych

