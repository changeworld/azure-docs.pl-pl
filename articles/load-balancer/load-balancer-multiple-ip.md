---
title: Obciążenia równoważenia na wielu konfiguracji adresów IP na platformie Azure | Dokumentacja firmy Microsoft
description: Równoważenie obciążenia w konfiguracji adresu IP podstawowego i pomocniczego.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3bf2f793baec4a0fdacadd7fb6d3d6784aa17f29
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Równoważenia obciążenia na wielu konfiguracji adresów IP za pomocą portalu Azure

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [Program PowerShell](load-balancer-multiple-ip-powershell.md)
> * [Interfejs wiersza polecenia](load-balancer-multiple-ip-cli.md)


W tym artykule zamierzamy opisano, jak używać usługi równoważenia obciążenia Azure przy użyciu wielu adresów IP w kontrolera interfejsu dodatkowej sieciowego (NIC). Na poniższym diagramie przedstawiono naszego scenariusza:

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

W naszym scenariuszu firma Microsoft korzysta z następujących konfiguracji:

- Dwóch maszyn wirtualnych (VM) z systemem Windows.
- Każda maszyna wirtualna ma podstawowego i pomocniczego karty sieciowej.
- Każdej dodatkowej karta NIC ma dwie konfiguracje adresów IP.
- Każda maszyna wirtualna hostuje dwie witryny sieci Web: contoso.com i fabrikam.com.
- Każda witryna sieci Web jest powiązana z konfiguracji protokołu IP dodatkowej karty sieciowej.
- Moduł równoważenia obciążenia Azure jest używany do udostępnienia dwa frontonu adresy IP, po jednej dla każdej witryny sieci Web. Frontonu adresy są używane do rozpowszechniania ruch do odpowiednich konfigurację adresu IP dla każdej witryny sieci Web.
- Ten sam numer portu jest używany zarówno adresy IP frontonu i zaplecza pulę adresów IP.

## <a name="prerequisites"></a>Wymagania wstępne

Naszym przykładzie scenariuszu założono, że grupy zasobów o nazwie **contosofabrikam** skonfigurowanego w następujący sposób:

- Grupa zasobów zawiera sieć wirtualną o nazwie **myVNet**.
- **MyVNet** sieć zawiera dwie maszyny wirtualne o nazwach **VM1** i **maszyny VM2**.
- VM1 i maszyny VM2 znajdują się w tym samym zestawie nazwanego dostępności **myAvailset**. 
- VM1, jak i maszyny VM2 mają podstawowej karty Sieciowej o nazwie **VM1NIC1** i **VM2NIC1**odpowiednio. 
- VM1, jak i maszyny VM2 mają dodatkowej karty Sieciowej o nazwie **VM1NIC2** i **VM2NIC2**odpowiednio.

Aby uzyskać więcej informacji na temat tworzenia maszyn wirtualnych z wieloma kartami sieciowymi, zobacz [utworzyć Maszynę wirtualną z wieloma kartami sieciowymi przy użyciu programu PowerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Wykonywanie obciążenia równoważenia na wielu konfiguracji adresów IP

Wykonaj poniższe kroki, aby osiągnąć scenariusz opisany w tym artykule.

### <a name="step-1-configure-the-secondary-nics"></a>Krok 1: Konfigurowanie dodatkowej kart sieciowych

Dla każdej maszyny Wirtualnej w sieci wirtualnej Dodaj konfigurację adresu IP dla dodatkowej karty Sieciowej:  

1. Przejdź do portalu Azure: http://portal.azure.com. Zaloguj się przy użyciu konta platformy Azure.

2. W lewym górnym rogu ekranu, wybierz **grupy zasobów** ikony. Następnie wybierz grupę zasobów, w którym znajdują się maszyny wirtualne (na przykład **contosofabrikam**). **Grup zasobów** okienko wyświetla wszystkie zasoby i kart interfejsu sieciowego dla maszyn wirtualnych.

3. Dodatkowej karty sieciowej każdej maszyny Wirtualnej Dodaj konfigurację adresu IP:

    1. Wybierz dodatkowej karty interfejsu Sieciowego, który chcesz skonfigurować.
    
    2. Wybierz **konfiguracje adresów IP**. W okienku dalej u góry, wybierz **Dodaj**.

    3. W obszarze **konfiguracje dodawanie adresów IP**, dodać drugi konfiguracji IP do karty Sieciowej: 

        1. Wprowadź nazwę dodatkowej konfiguracji adresu IP. (Na przykład VM1 i maszyny VM2 nazwę konfiguracji IP **VM1NIC2 ipconfig2** i **VM2NIC2 ipconfig2**odpowiednio.)

        2. Aby uzyskać **prywatny adres IP**, **alokacji** wybierz pozycję **statycznych**.

        3. Kliknij przycisk **OK**.

Po drugie konfiguracji IP dodatkowej karty interfejsu Sieciowego jest zakończone, pojawi się w obszarze **konfiguracje adresów IP** ustawień dla danej karty sieciowej.

### <a name="step-2-create-the-load-balancer"></a>Krok 2. Tworzenie modułu równoważenia obciążenia

Utwórz moduł równoważenia obciążenia dla konfiguracji:

1. Przejdź do portalu Azure: http://portal.azure.com. Zaloguj się przy użyciu konta platformy Azure.

2. W lewym górnym rogu ekranu, wybierz **Utwórz zasób** > **sieci** > **modułu równoważenia obciążenia**. Następnie wybierz pozycję **Utwórz**.

3. W obszarze **modułu równoważenia obciążenia Utwórz**, wpisz nazwę użytkownika usługi równoważenia obciążenia. W tym scenariuszu firma Microsoft korzysta z nazwą **mylb**.

4. W obszarze **publicznego adresu IP**, Utwórz nowy publiczny adres IP o nazwie **PublicIP1**.

5. W obszarze **grupy zasobów**, wybierz istniejącą grupę zasobów dla maszyn wirtualnych (na przykład **contosofabrikam**). Wybierz lokalizację, aby wdrożyć przez moduł równoważenia obciążenia do, a następnie wybierz **OK**.

Uruchamia usługi równoważenia obciążenia do wdrożenia. Wdrażanie może potrwać kilka minut do pomyślnego ukończenia. Po zakończeniu wdrażania usługi równoważenia obciążenia jest wyświetlana jako zasób w grupie zasobów.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Krok 3: Konfigurowanie puli adresów IP frontonu

Każda witryna sieci Web (contoso.com i fabrikam.com) można skonfigurować w puli adresów IP frontonu przez moduł równoważenia obciążenia:

1. W portalu, wybierz **więcej usług**. W polu filtru wpisz **publicznego adresu IP** , a następnie wybierz **publicznego adresu IP, adresy**. W okienku dalej u góry, wybierz **Dodaj**.

2. Skonfiguruj dwa publiczne adresy IP (**PublicIP1** i **PublicIP2**) dla obu witryn sieci Web (contoso.com i fabrikam.com):

    1. Wpisz nazwę dla adresu IP frontonu.

    2. Aby uzyskać **grupy zasobów**, wybierz istniejącą grupę zasobów dla maszyn wirtualnych (na przykład **contosofabrikam**).

    3. Aby uzyskać **lokalizacji**, wybierz lokalizację, w tym samym jako maszyn wirtualnych.

    4. Kliknij przycisk **OK**.

    Po utworzeniu publiczne adresy IP są wyświetlane w obszarze **publicznego adresu IP** adresów.

3. <a name="step3-3"></a>W portalu, wybierz **więcej usług**. W polu filtru wpisz **modułu równoważenia obciążenia** , a następnie wybierz **moduł równoważenia obciążenia**. 

4. Wybierz usługę równoważenia obciążenia (**mylb**) chcesz dodać puli adresów IP frontonu, aby.

5. W obszarze **ustawienia**, wybierz pozycję **konfiguracji IP frontonu**. W okienku dalej u góry, wybierz **Dodaj**.

6. Wpisz nazwę dla adresu IP frontonu (na przykład **contosofe** lub **fabrikamfe**).

7. <a name="step3-7"></a>Wybierz **adres IP**. W obszarze **wybierz publiczny adres IP**, wybierz adresy IP dla sieci frontonu (**PublicIP1** lub **PublicIP2**).

8. Utworzyć drugi adres IP frontonu, powtarzając <a href="#step3-3">krok 3</a> za pośrednictwem <a href="#step3-7">krok 7</a> w tej sekcji.

Po skonfigurowaniu frontonu puli adresów IP są wyświetlane w obszarze przez moduł równoważenia obciążenia **konfiguracji IP frontonu** ustawienia. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Krok 4: Konfigurowanie puli zaplecza

Dla każdej witryny sieci Web (contoso.com i fabrikam.com) należy skonfigurować pulę adresów zaplecza na moduł równoważenia obciążenia:
        
1. W portalu, wybierz **więcej usług**. W polu filtru wpisz **modułu równoważenia obciążenia** , a następnie wybierz **moduł równoważenia obciążenia**.

2. Wybierz usługę równoważenia obciążenia (**mylb**) chcesz dodać do puli zaplecza.

3. W obszarze **ustawienia**, wybierz pozycję **pul zaplecza**. Wpisz nazwę użytkownika puli zaplecza (na przykład **contosopool** lub **fabrikampool**). W okienku dalej u góry, wybierz **Dodaj**. 

4. Aby uzyskać **skojarzonego z**, wybierz pozycję **zestawu dostępności**.

5. Aby uzyskać **zestawu dostępności**, wybierz pozycję **myAvailset**.

6. Dodaj obie maszyny wirtualne w konfiguracji adresów IP sieci docelowej: 

    ![Skonfiguruj pule zaplecza dla usługi równoważenia obciążenia](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. Aby uzyskać **docelowej maszyny wirtualnej**, wybierz maszynę Wirtualną, która ma zostać dodany do puli zaplecza (na przykład **VM1** lub **maszyny VM2**).

    2. Dla **konfiguracji sieci IP**, wybierz konfigurację adresu IP dodatkowej karty sieciowej dla maszyny Wirtualnej wybrany w poprzednim kroku (na przykład **VM1NIC2 ipconfig2** lub **VM2NIC2 ipconfig2** ).

7. Kliknij przycisk **OK**.

Po skonfigurowaniu puli zaplecza adresy są wyświetlane w obszarze przez moduł równoważenia obciążenia **puli zaplecza** ustawienia.

### <a name="step-5-configure-the-health-probe"></a>Krok 5: Konfigurowanie badania kondycji

Skonfiguruj badanie kondycji dla Twojej usługi równoważenia obciążenia:

1. W portalu, wybierz **więcej usług**. W polu filtru wpisz **modułu równoważenia obciążenia** , a następnie wybierz **moduł równoważenia obciążenia**.

2. Wybierz usługę równoważenia obciążenia (**mylb**) czy chcesz dodać do sondy kondycji.

3. W obszarze **ustawienia**, wybierz pozycję **sondy kondycji**. W okienku dalej u góry, wybierz **Dodaj**. 

4. Wpisz nazwę dla sondy kondycji (na przykład **HTTP**). Kliknij przycisk **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Krok 6: Skonfigurowanie reguł równoważenia obciążenia

Dla każdej witryny sieci Web (contoso.com i fabrikam.com) należy skonfigurować reguły równoważenia obciążenia:
    
1. <a name="step6-1"></a>W obszarze **ustawienia**, wybierz pozycję **sondy kondycji**. W okienku dalej u góry, wybierz **Dodaj**. 

2. Aby uzyskać **nazwa**, wpisz nazwę reguły równoważenia obciążenia (na przykład **HTTPc** dla domeny contoso.com, lub **HTTPf** dla fabrikam.com).

3. Aby uzyskać **adresu IP frontonu**, wybierz adres IP frontonu, która została wcześniej utworzona (na przykład **contosofe** lub **fabrikamfe**).

4. Aby uzyskać **portu** i **portu zaplecza**, należy zachować wartość domyślną **80**.

5. Aby uzyskać **pływającego adresu IP (bezpośredni zwrot serwera)**, wybierz pozycję **włączone**.

6. <a name="step6-6"></a>Wybierz **OK**.

7. Utwórz drugą regułę modułu równoważenia obciążenia, powtarzając <a href="#step6-1">krok 1</a> za pośrednictwem <a href="#step6-6">krok 6</a> w tej sekcji.

Po skonfigurowaniu reguły są wyświetlane w obszarze przez moduł równoważenia obciążenia **reguły równoważenia obciążenia** ustawienia.

### <a name="step-7-configure-dns-records"></a>Krok 7: Konfigurowanie rekordów DNS

Jako ostatni krok należy skonfigurować Twoje rekordów zasobów DNS wskazać odpowiednie adresy IP frontonu dla Twojej usługi równoważenia obciążenia. Można hostować domen w usłudze Azure DNS. Aby uzyskać więcej informacji o korzystaniu z usługi Azure DNS z usługi równoważenia obciążenia, zobacz [przy użyciu usługi Azure DNS z innymi usługami Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o sposobie łączenia usługi na platformie Azure w równoważenia obciążenia [przy użyciu usługi równoważenia obciążenia w Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Dowiedz się, jak różne typy dzienników można użyć do zarządzania i rozwiązywania problemów z usługi równoważenia obciążenia w [dziennika analizy dla usługi równoważenia obciążenia Azure](../load-balancer/load-balancer-monitor-log.md).
