---
title: Równoważenie obciążenia na wielu konfiguracji adresu IP — witryna Azure portal
titlesuffix: Azure Load Balancer
description: Równoważenie obciążenia w podstawowych i pomocniczych konfiguracji adresów IP.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0cf5aa45e1e8a28dfcdadac0ea32658e5993d06c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591715"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Równoważenie obciążenia na wielu konfiguracji adresu IP za pomocą witryny Azure portal

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [Program PowerShell](load-balancer-multiple-ip-powershell.md)
> * [Interfejs wiersza polecenia](load-balancer-multiple-ip-cli.md)


W tym artykule wdrożymy dowiesz się, jak używać usługi Azure Load Balancer z wieloma adresami IP na kontrolera interfejsu sieciowego dodatkowej (NIC). Na poniższym diagramie przedstawiono scenariuszu:

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

W naszym scenariuszu używamy następującej konfiguracji:

- Dwie maszyny wirtualne (VM) z systemem Windows.
- Każda maszyna wirtualna ma głównej i pomocniczej karty sieciowej
- Każdej pomocniczej karty Sieciowej ma dwie konfiguracje adresów IP.
- Każda maszyna wirtualna jest hostem dwie witryny sieci Web: contoso.com i fabrikam.com.
- Każda witryna sieci Web jest powiązana z konfiguracji adresu IP pomocniczej karty sieciowej
- Usługa Azure Load Balancer jest używany do udostępnienia dwóch adresów IP frontonu, jeden dla każdej witryny sieci Web. Frontonu adresy są używane do dystrybucji ruchu do odpowiedniej konfiguracji adresów IP, dla każdej witryny sieci Web.
- Ten sam numer portu jest używany zarówno adresy IP frontonu i zaplecza pulę adresów IP.

## <a name="prerequisites"></a>Wymagania wstępne

Nasz przykład w scenariuszu przyjęto założenie, iż grupę zasobów o nazwie **contosofabrikam** skonfigurowanego w następujący sposób:

- Grupa zasobów zawiera sieć wirtualną o nazwie **myVNet**.
- **MyVNet** sieć zawiera dwie maszyny wirtualne o nazwach **VM1** i **VM2**.
- Maszyna VM1 i VM2 znajdują się w tej samej zestawu dostępności o nazwie **myAvailset**. 
- Maszyny VM1, jak i maszyny VM2 mieć podstawowej karty Sieciowej o nazwie **VM1NIC1** i **VM2NIC1**, odpowiednio. 
- Maszyna VM1, jak i maszyny VM2 mają pomocniczej karty Sieciowej o nazwie **VM1NIC2** i **VM2NIC2**, odpowiednio.

Aby uzyskać więcej informacji na temat tworzenia maszyn wirtualnych z wieloma kartami sieciowymi, zobacz [Utwórz Maszynę wirtualną z wieloma kartami sieciowymi przy użyciu programu PowerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Wykonaj równoważenia obciążenia na wielu konfiguracji adresu IP

Wykonaj poniższe kroki, aby osiągnąć scenariusz opisany w tym artykule.

### <a name="step-1-configure-the-secondary-nics"></a>Krok 1: Skonfiguruj dodatkową kartą sieciową

Dla każdej maszyny Wirtualnej w sieci wirtualnej należy dodać konfiguracji adresu IP dla pomocniczej karty Sieciowej:  

1. Przejdź do witryny Azure portal: https://portal.azure.com. Zaloguj się przy użyciu konta platformy Azure.

2. W lewym górnym rogu ekranu, wybierz **grupy zasobów** ikony. Następnie wybierz grupę zasobów, w którym znajdują się maszyny wirtualne (na przykład **contosofabrikam**). **Grup zasobów** okienko Wyświetla listę wszystkich zasobów i kart sieciowych dla maszyn wirtualnych.

3. Dodaj konfigurację adresu IP dla pomocniczej karty Sieciowej poszczególnych maszyn wirtualnych:

    1. Wybierz pomocniczej karty Sieciowej, którą chcesz skonfigurować.
    
    2. Wybierz **konfiguracje adresów IP**. W okienku dalej, u góry, zaznacz **Dodaj**.

    3. W obszarze **konfiguracje Dodaj adres IP**, Dodaj druga Konfiguracja protokołu IP do karty Sieciowej: 

        1. Wprowadź nazwę dla konfiguracji pomocniczego adresu IP. (Na przykład w przypadku maszyny VM1 i VM2 nazwać Konfiguracja protokołu IP **VM1NIC2 ipconfig2** i **VM2NIC2 ipconfig2**odpowiednio.)

        2. Aby uzyskać **prywatny adres IP**, **alokacji** ustawienie, wybierz **statyczne**.

        3. Kliknij przycisk **OK**.

Po druga Konfiguracja protokołu IP dla pomocniczej karty Sieciowej jest zakończone, pojawi się w obszarze **konfiguracje adresów IP** ustawień dla danej karty sieciowej.

### <a name="step-2-create-the-load-balancer"></a>Krok 2: Tworzenie modułu równoważenia obciążenia

Tworzenie modułu równoważenia obciążenia dla konfiguracji:

1. Przejdź do witryny Azure portal: https://portal.azure.com. Zaloguj się przy użyciu konta platformy Azure.

2. W lewym górnym rogu ekranu, wybierz **Utwórz zasób** > **sieć** > **modułu równoważenia obciążenia**. Następnie wybierz pozycję **Utwórz**.

3. W obszarze **Tworzenie modułu równoważenia obciążenia**, wpisz nazwę dla modułu równoważenia obciążenia. W tym scenariuszu używamy nazwy **mylb**.

4. W obszarze **publiczny adres IP**, Utwórz nowy publiczny adres IP o nazwie **PublicIP1**.

5. W obszarze **grupy zasobów**, wybierz istniejącą grupę zasobów dla maszyn wirtualnych (na przykład **contosofabrikam**). Wybierz lokalizację, aby wdrożyć moduł równoważenia obciążenia do, a następnie wybierz **OK**.

Moduł równoważenia obciążenia rozpocznie się do wdrożenia. Wdrożenie może potrwać kilka minut. Po zakończeniu wdrażania modułu równoważenia obciążenia jest wyświetlany jako zasób w grupie zasobów.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Krok 3: Konfigurowanie puli adresów IP frontonu

Dla każdej witryny sieci Web (contoso.com i fabrikam.com) należy skonfigurować puli adresów IP frontonu na moduł równoważenia obciążenia:

1. W portalu, wybierz **więcej usług**. W polu filtru wpisz **publiczny adres IP** , a następnie wybierz **publiczne adresy IP**. W okienku dalej, u góry, zaznacz **Dodaj**.

2. Skonfiguruj dwa publiczne adresy IP (**PublicIP1** i **PublicIP2**) dla obu witryn sieci Web (contoso.com i fabrikam.com):

   1. Wpisz nazwę dla podanego adresu IP frontonu.

   2. Aby uzyskać **grupy zasobów**, wybierz istniejącą grupę zasobów dla maszyn wirtualnych (na przykład **contosofabrikam**).

   3. Aby uzyskać **lokalizacji**, wybierz lokalizację, ponieważ maszyny wirtualne.

   4. Kliknij przycisk **OK**.

      Po utworzeniu publicznego adresu IP, są wyświetlane w obszarze **publiczny adres IP** adresów.

3. <a name="step3-3"></a>W portalu, wybierz **więcej usług**. W polu filtru wpisz **moduł równoważenia obciążenia** , a następnie wybierz **usługi Load Balancer**. 

4. Wybierz moduł równoważenia obciążenia (**mylb**) czy chcesz dodać puli adresów IP frontonu, aby.

5. W obszarze **ustawienia**, wybierz opcję **konfiguracja adresu IP frontonu**. W okienku dalej, u góry, zaznacz **Dodaj**.

6. Wpisz nazwę dla podanego adresu IP frontonu (na przykład **contosofe** lub **fabrikamfe**).

7. <a name="step3-7"></a>Wybierz **adresu IP**. W obszarze **wybierz publiczny adres IP**, wybierz adresy IP dla usługi frontonu (**PublicIP1** lub **PublicIP2**).

8. Utwórz drugi adres IP frontonu, powtarzając <a href="#step3-3">kroku 3</a> za pośrednictwem <a href="#step3-7">kroku 7</a> w tej sekcji.

Po skonfigurowaniu puli frontonu adresy IP są wyświetlane w obszarze modułu równoważenia obciążenia **konfiguracja adresu IP frontonu** ustawienia. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Krok 4: Konfigurowanie puli zaplecza

Każda witryna sieci Web (contoso.com i fabrikam.com) można skonfigurować w puli adresów zaplecza modułu równoważenia obciążenia:
        
1. W portalu, wybierz **więcej usług**. W polu filtru wpisz **moduł równoważenia obciążenia** , a następnie wybierz **usługi Load Balancer**.

2. Wybierz moduł równoważenia obciążenia (**mylb**) przewidziane do dodania do puli zaplecza.

3. W obszarze **ustawienia**, wybierz opcję **pule zaplecza**. Wpisz nazwę puli zaplecza (na przykład **contosopool** lub **fabrikampool**). W okienku dalej, u góry, zaznacz **Dodaj**. 

4. Aby uzyskać **powiązanych z**, wybierz opcję **zestawu dostępności**.

5. Aby uzyskać **zestawu dostępności**, wybierz opcję **myAvailset**.

6. Dodaj konfiguracje protokołu IP sieci docelowej dla obu maszyn wirtualnych: 

    ![Skonfiguruj pule zaplecza dla modułu równoważenia obciążenia](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. Aby uzyskać **docelowej maszyny wirtualnej**, wybierz maszynę Wirtualną, którą chcesz dodać do puli zaplecza (na przykład **VM1** lub **VM2**).

    2. Dla **Konfiguracja protokołu IP sieci**, wybierz konfigurację adresu IP dla pomocniczej karty Sieciowej dla maszyny Wirtualnej, który został wybrany w poprzednim kroku (na przykład **VM1NIC2 ipconfig2** lub **VM2NIC2 ipconfig2** ).

7. Kliknij przycisk **OK**.

Po skonfigurowaniu puli zaplecza adresy są wyświetlane w obszarze modułu równoważenia obciążenia **puli zaplecza** ustawienia.

### <a name="step-5-configure-the-health-probe"></a>Krok 5. Konfigurowanie sondy kondycji

Konfigurowanie sondy kondycji modułu równoważenia obciążenia:

1. W portalu, wybierz **więcej usług**. W polu filtru wpisz **moduł równoważenia obciążenia** , a następnie wybierz **usługi Load Balancer**.

2. Wybierz moduł równoważenia obciążenia (**mylb**) czy chcesz dodać sondy kondycji.

3. W obszarze **ustawienia**, wybierz opcję **sondy kondycji**. W okienku dalej, u góry, zaznacz **Dodaj**. 

4. Wpisz nazwę dla sondy kondycji (na przykład **HTTP**). Kliknij przycisk **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Krok 6: Konfigurowanie reguł równoważenia obciążenia

Dla każdej witryny sieci Web (contoso.com i fabrikam.com) należy skonfigurować reguły równoważenia obciążenia:
    
1. <a name="step6-1"></a>W obszarze **ustawienia**, wybierz opcję **reguły równoważenia obciążenia**. W okienku dalej, u góry, zaznacz **Dodaj**. 

2. Aby uzyskać **nazwa**, wpisz nazwę reguły równoważenia obciążenia (na przykład **HTTPc** dla domeny contoso.com, lub **HTTPf** dla fabrikam.com).

3. Aby uzyskać **adres IP frontonu**, wybierz adres IP frontonu, który został wcześniej utworzony (na przykład **contosofe** lub **fabrikamfe**).

4. Aby uzyskać **portu** i **port zaplecza**, Zachowaj wartość domyślną **80**.

5. Aby uzyskać **pływającego adresu IP (bezpośredni zwrot serwera)**, wybierz opcję **wyłączone**.

6. <a name="step6-6"></a>Wybierz **OK**.

7. Utwórz drugi reguły modułu równoważenia obciążenia, powtarzając <a href="#step6-1">kroku 1</a> za pośrednictwem <a href="#step6-6">kroku 6</a> w tej sekcji.

Po skonfigurowaniu zasad, zostaną one wyświetlone w obszarze modułu równoważenia obciążenia **reguły równoważenia obciążenia** ustawienia.

### <a name="step-7-configure-dns-records"></a>Krok 7: Skonfiguruj rekordy DNS

Ostatnim krokiem skonfigurować swoje rekordy zasobów DNS wskaż odpowiednie adresy IP frontonu modułu równoważenia obciążenia. Możesz hostować swoje domeny w usłudze Azure DNS. Aby uzyskać więcej informacji o korzystaniu z usługi Azure DNS przy użyciu modułu równoważenia obciążenia, zobacz [przy użyciu usługi Azure DNS z innymi usługami Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o sposobie łączenia usług na platformie Azure w równoważenia obciążenia [przy użyciu usługi równoważenia obciążenia na platformie Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Dowiedz się, jak można użyć różnych typów dzienników zarządzanie i rozwiązywanie problemów z modułu równoważenia obciążenia w [usługi Azure Monitor dzienniki dla usługi Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
