---
title: Zdalne łączenie się z węzłem klastra sieci szkieletowej usługi Azure
description: Dowiedz się, jak zdalnie łączyć się z wystąpieniem zestawu skalowania (węzłem klastra sieci szkieletowej usług).
ms.topic: conceptual
ms.date: 03/23/2018
ms.openlocfilehash: c7ca4f0d5dce1b19837a44d5c9749f3e1293c6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458321"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Zdalne łączenie się z wystąpieniem zestawu skalowania maszyny wirtualnej lub węzłem klastra
W klastrze sieci szkieletowej usług uruchomionej na platformie Azure każdy zdefiniowany typ węzła klastra [konfiguruje osobną skalę maszyny wirtualnej.](service-fabric-cluster-nodetypes.md)  Można zdalnie łączyć się z określonymi wystąpieniami zestawu skalowania (węzłami klastra).  W przeciwieństwie do maszyn wirtualnych z pojedynczym wystąpieniem, wystąpienia zestawu skalowania nie mają własnych wirtualnych adresów IP. Może to być trudne, gdy szukasz adresu IP i portu, którego można użyć do zdalnego łączenia się z określonym wystąpieniem.

Aby znaleźć adres IP i port, za pomocą którego można zdalnie połączyć się z określonym wystąpieniem, wykonaj następujące kroki.

1. Pobierz przychodzące reguły NAT dla protokołu RDP (Remote Desktop Protocol).

    Zazwyczaj każdy typ węzła zdefiniowany w klastrze ma swój własny wirtualny adres IP i dedykowany moduł równoważenia obciążenia. Domyślnie moduł równoważenia obciążenia typu węzła ma na imię *lb-{cluster-name}-{node-type};* na przykład *LB-mycluster-FrontEnd*. 
    
    Na stronie modułu równoważenia obciążenia w witrynie Azure portal wybierz pozycję **Ustawienia** > **przychodzących reguł NAT:** 

    ![Reguły przychodzącego translatora ładunków przychodzące modułu równoważenia obciążenia](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Poniższy zrzut ekranu przedstawia przychodzące reguły TRANSLATORa dla typu węzła o nazwie FrontEnd: 

    ![Reguły przychodzącego translatora ładunków przychodzące modułu równoważenia obciążenia](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Dla każdego węzła adres IP pojawia się w kolumnie **DESTINATION,** kolumna **TARGET** podaje wystąpienie zestawu skalowania, a kolumna **SERVICE** podaje numer portu. W przypadku połączenia zdalnego porty są przydzielane do każdego węzła w kolejności rosnącej, począwszy od portu 3389.

    Reguły przychodzącej usługi NAT `Microsoft.Network/loadBalancers` można również znaleźć w sekcji szablonu Menedżera zasobów dla klastra.
    
2. Aby potwierdzić mapowanie portu przychodzącego do docelowego portu dla węzła, można kliknąć jego regułę i spojrzeć na wartość **portu docelowego.** Poniższy zrzut ekranu przedstawia przychodzącą regułę NAT dla **węzła FrontEnd (wystąpienie 1)** w poprzednim kroku. Należy zauważyć, że chociaż (przychodzący) numer portu wynosi 3390, port docelowy jest mapowany do portu 3389, portu dla usługi RDP na docelowej.  

    ![Mapowanie portu docelowego](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Domyślnie w przypadku klastrów systemu Windows portem docelowym jest port 3389, który jest mapowane do usługi RDP w węźle docelowym. W przypadku klastrów systemu Linux portem docelowym jest port 22, który jest mapowane do usługi Secure Shell (SSH).

3. Zdalnie połącz się z określonym węzłem (wystąpienie zestawu skalowania). Można użyć nazwy użytkownika i hasła ustawionego podczas tworzenia klastra lub innych skonfigurowanych poświadczeń. 

    Poniższy zrzut ekranu przedstawia połączenie z węzłem **FrontEnd (Wystąpienie 1)** w klastrze systemu Windows przy użyciu usługi Podłączanie pulpitu zdalnego:
    
    ![Podłączanie pulpitu zdalnego](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    W węzłach systemu Linux można połączyć się z SSH (poniższy przykład ponownie używa tego samego adresu IP i portu dla zwięzłości):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Aby uzyskać następujące kroki, przeczytaj następujące artykuły:
* Zobacz [omówienie funkcji "Wdrażanie w dowolnym miejscu" i porównanie z klastrami zarządzanymi przez platformę Azure.](service-fabric-deploy-anywhere.md)
* Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
* [Aktualizowanie wartości zakresu portów RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) na maszynach wirtualnych klastra po wdrożeniu
* [Zmienianie nazwy użytkownika i hasła administratora](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) dla maszyn wirtualnych klastra

