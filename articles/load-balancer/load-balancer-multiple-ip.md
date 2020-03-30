---
title: Równoważenie obciążenia w wielu konfiguracjach IP — witryna Azure portal
titleSuffix: Azure Load Balancer
description: W tym artykule dowiesz się o równoważeniu obciążenia w konfiguracjach podstawowych i pomocniczych adresów IP przy użyciu witryny Azure portal.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 4bf74986462ecb2659505f8a1261b9b24aba3fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74077002"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Równoważenie obciążenia w wielu konfiguracjach IP przy użyciu witryny Azure portal

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [Powershell](load-balancer-multiple-ip-powershell.md)
> * [Cli](load-balancer-multiple-ip-cli.md)


W tym artykule pokażemy, jak używać modułu równoważenia obciążenia platformy Azure z wieloma adresami IP na pomocniczym kontrolerze interfejsu sieciowego (NIC). Poniższy diagram ilustruje nasz scenariusz:

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

W naszym scenariuszu używamy następującej konfiguracji:

- Dwie maszyny wirtualne (maszyny wirtualne), z których jest uruchomiony system Windows.
- Każda maszyna wirtualna ma podstawową i pomocniczą kartę sieciową.
- Każda dodatkowa karta sieciowa ma dwie konfiguracje adresów IP.
- Każda maszyna wirtualna obsługuje dwie witryny: contoso.com i fabrikam.com.
- Każda witryna sieci Web jest powiązana z konfiguracją ip na pomocniczej karcie sieciowej.
- Moduł równoważenia obciążenia platformy Azure jest używany do udostępnienia dwóch adresów IP frontonu, po jednym dla każdej witryny sieci Web. Adresy front-end są używane do dystrybucji ruchu do odpowiedniej konfiguracji IP dla każdej witryny sieci Web.
- Ten sam numer portu jest używany zarówno dla adresów IP frontu, jak i adresów IP puli zaplecza.

## <a name="prerequisites"></a>Wymagania wstępne

W naszym przykładzie scenariusza przyjęto założenie, że masz grupę zasobów o nazwie **contosofabrikam,** która jest skonfigurowana w następujący sposób:

- Grupa zasobów zawiera sieć wirtualną o nazwie **myVNet**.
- Sieć **myVNet** zawiera dwie maszyny wirtualne o nazwie **VM1** i **VM2**.
- VM1 i VM2 znajdują się w tym samym zestawie dostępności o nazwie **myAvailset**. 
- VM1 i VM2 mają podstawową kartę sieciową o nazwie **VM1NIC1** i **VM2NIC1**, odpowiednio. 
- VM1 i VM2 mają pomocniczą kartę sieciową o nazwie **VM1NIC2** i **VM2NIC2**, odpowiednio.

Aby uzyskać więcej informacji na temat tworzenia maszyn wirtualnych z wieloma kartami sieciowymi, zobacz [Tworzenie maszyny wirtualnej z wieloma kartami sieciowymi przy użyciu programu PowerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Równoważenie obciążenia w wielu konfiguracjach IP

Wykonaj następujące kroki, aby osiągnąć scenariusz opisany w tym artykule.

### <a name="step-1-configure-the-secondary-nics"></a>Krok 1: Konfigurowanie dodatkowych kart sieciowych

Dla każdej maszyny Wirtualnej w sieci wirtualnej dodaj konfigurację IP pomocniczej karty sieciowej:  

1. Przejdź do witryny https://portal.azure.comAzure portal: . Zaloguj się za pomocą konta platformy Azure.

2. W lewym górnym rogu ekranu wybierz ikonę **Grupy zasobów.** Następnie wybierz grupę zasobów, w której znajdują się maszyny wirtualne (na przykład **contosofabrikam**). W okienku **Grupy zasobów** są wyświetlane wszystkie zasoby i karty sieciowe dla maszyn wirtualnych.

3. Dla dodatkowej karty sieciowej każdej maszyny Wirtualnej dodaj konfigurację IP:

    1. Wybierz pomocniczą kartę sieciową, którą chcesz skonfigurować.
    
    2. Wybierz **konfiguracje IP**. W następnym okienku u góry wybierz pozycję **Dodaj**.

    3. W obszarze **Dodawanie konfiguracji IP**dodaj drugą konfigurację IP do karty sieciowej: 

        1. Wprowadź nazwę pomocniczej konfiguracji IP. (Na przykład dla VM1 i VM2, nazwa konfiguracji IP **VM1NIC2-ipconfig2** i **VM2NIC2-ipconfig2**, odpowiednio.)

        2. W przypadku ustawienia **Prywatny adres IP**, **Alokacja** wybierz pozycję **Statyczny**.

        3. Kliknij przycisk **OK**.

Po zakończeniu drugiej konfiguracji IP dla dodatkowej karty sieciowej jest wyświetlana w **ustawieniach konfiguracji IP** dla danej karty sieciowej.

### <a name="step-2-create-the-load-balancer"></a>Krok 2. Tworzenie modułu równoważenia obciążenia

Utwórz moduł równoważenia obciążenia dla konfiguracji:

1. Przejdź do witryny https://portal.azure.comAzure portal: . Zaloguj się za pomocą konta platformy Azure.

2. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasobnik** > **Równoważenia obciążenia****sieciowego** > . Następnie wybierz pozycję **Utwórz**.

3. W obszarze **Tworzenie modułu równoważenia obciążenia**wpisz nazwę modułu równoważenia obciążenia. W tym scenariuszu używamy nazwy **mylb**.

4. W obszarze **Publiczny adres IP**utwórz nowy publiczny adres IP o nazwie **PublicIP1**.

5. W obszarze **Grupa zasobów**wybierz istniejącą grupę zasobów dla maszyn wirtualnych (na przykład **contosofabrikam**). Wybierz lokalizację, w którym ma być wdrażany moduł równoważenia obciążenia, a następnie wybierz przycisk **OK**.

Moduł równoważenia obciążenia rozpoczyna wdrażanie. Pomyślne wdrożenie może potrwać kilka minut. Po zakończeniu wdrażania moduł równoważenia obciążenia jest wyświetlany jako zasób w grupie zasobów.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Krok 3: Konfigurowanie puli adresów IP front-end

Dla każdej witryny sieci Web (contoso.com i fabrikam.com) skonfiguruj pulę adresów IP front-endu na modułze równoważenia obciążenia:

1. W portalu wybierz pozycję **Więcej usług**. W polu filtru wpisz **publiczny adres IP,** a następnie wybierz pozycję **Publiczne adresy IP**. W następnym okienku u góry wybierz pozycję **Dodaj**.

2. Skonfiguruj dwa publiczne adresy IP **(PublicIP1** i **PublicIP2)** dla obu stron internetowych (contoso.com i fabrikam.com):

   1. Wpisz nazwę adresu IP front-end.

   2. W przypadku **grupy zasobów**wybierz istniejącą grupę zasobów dla maszyn wirtualnych (na przykład **contosofabrikam**).

   3. W obszarze **Lokalizacja**wybierz tę samą lokalizację co maszyny wirtualne.

   4. Kliknij przycisk **OK**.

      Po utworzeniu publicznych adresów IP są one wyświetlane pod publicznymi adresami **IP.**

3. <a name="step3-3"></a>W portalu wybierz pozycję **Więcej usług**. W polu filtru wpisz **moduł równoważenia obciążenia,** a następnie wybierz pozycję **Load Balancer**. 

4. Wybierz moduł równoważenia obciążenia (**mylb**), do którego chcesz dodać pulę adresów IP frontu.

5. W obszarze **Ustawienia**wybierz **pozycję Konfiguracja ip zaplecza**. W następnym okienku u góry wybierz pozycję **Dodaj**.

6. Wpisz nazwę adresu IP frontonu (na przykład **contosofe** lub **fabrikamfe).**

7. <a name="step3-7"></a>Wybierz **adres IP**. W obszarze **Wybierz publiczny adres IP**wybierz adresy IP dla front-endu **(PublicIP1** lub **PublicIP2).**

8. Utwórz drugi adres IP front-end, powtarzając <a href="#step3-3">krok 3</a> do <a href="#step3-7">kroku 7</a> w tej sekcji.

Po skonfigurowaniu puli front-end adresy IP są wyświetlane w ustawieniach konfiguracji IP modułu równoważenia obciążenia **Frontend.** 
    
### <a name="step-4-configure-the-back-end-pool"></a>Krok 4: Konfigurowanie puli zaplecza

Dla każdej witryny sieci Web (contoso.com i fabrikam.com) skonfiguruj pulę adresów zaplecza na modułze równoważenia obciążenia:
        
1. W portalu wybierz pozycję **Więcej usług**. W polu filtru wpisz **moduł równoważenia obciążenia,** a następnie wybierz pozycję **Load Balancer**.

2. Wybierz moduł równoważenia obciążenia (**mylb**), do którego chcesz dodać pulę zaplecza.

3. W obszarze **Ustawienia**wybierz pozycję **Pule zaplecza**. Wpisz nazwę puli zaplecza (na przykład **contosopool** lub **fabrikampool).** W następnym okienku u góry wybierz pozycję **Dodaj**. 

4. Dla **skojarzonego**z , wybierz **zestaw dostępności**.

5. W przypadku **zestawu dostępności**wybierz **myAvailset**.

6. Dodaj docelowe konfiguracje adresów IP sieci dla obu maszyn wirtualnych: 

    ![Konfigurowanie pul zaplecza dla modułu równoważenia obciążenia](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. W przypadku **maszyny wirtualnej docelowej**wybierz maszynę wirtualną, którą chcesz dodać do puli zaplecza (na przykład **VM1** lub **VM2).**

    2. W przypadku **konfiguracji sieciowej karty IP**wybierz konfigurację IP pomocniczej karty sieciowej dla maszyny Wirtualnej wybranej w poprzednim kroku (na przykład **VM1NIC2-ipconfig2** lub **VM2NIC2-ipconfig2**).

7. Kliknij przycisk **OK**.

Po skonfigurowaniu puli zaplecza adresy są wyświetlane w ustawieniach **puli** modułu równoważenia obciążenia.

### <a name="step-5-configure-the-health-probe"></a>Krok 5: Konfigurowanie sondy kondycji

Skonfiguruj sondę kondycji dla modułu równoważenia obciążenia:

1. W portalu wybierz pozycję **Więcej usług**. W polu filtru wpisz **moduł równoważenia obciążenia,** a następnie wybierz pozycję **Load Balancer**.

2. Wybierz moduł równoważenia obciążenia (**mylb**), do którego chcesz dodać sondę kondycji.

3. W obszarze **Ustawienia**wybierz **pozycję Sonda kondycji**. W następnym okienku u góry wybierz pozycję **Dodaj**. 

4. Wpisz nazwę sondy kondycji (na przykład **HTTP**). Kliknij przycisk **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Krok 6: Konfigurowanie reguł równoważenia obciążenia

Dla każdej witryny sieci Web (contoso.com i fabrikam.com) skonfiguruj reguły równoważenia obciążenia:
    
1. <a name="step6-1"></a>W obszarze **Ustawienia**wybierz pozycję **Reguły równoważenia obciążenia**. W następnym okienku u góry wybierz pozycję **Dodaj**. 

2. W pozycji **Nazwa**wpisz nazwę reguły równoważenia obciążenia (na przykład **HTTPc** dla contoso.com lub **HTTPf** dla fabrikam.com).

3. W przypadku **adresu IP frontonu**wybierz poprzednio utworzony adres IP frontonu (na przykład **contosofe** lub **fabrikamfe).**

4. W przypadku portu **portowego** i **zaplecza**zachowaj wartość domyślną **80**.

5. W przypadku **przestawnego adresu IP (bezpośredni powrót serwera)** wybierz pozycję **Wyłączone**.

6. <a name="step6-6"></a>Wybierz **przycisk OK**.

7. Utwórz drugą regułę równoważenia obciążenia, powtarzając <a href="#step6-1">krok od 1</a> do <a href="#step6-6">6</a> w tej sekcji.

Po skonfigurowaniu reguł są one wyświetlane w ustawieniach **reguł równoważenia obciążenia modułu równoważenia obciążenia.**

### <a name="step-7-configure-dns-records"></a>Krok 7: Konfigurowanie rekordów DNS

W ostatnim kroku skonfiguruj rekordy zasobów DNS tak, aby wskazywały odpowiednie adresy IP frontu dla modułu równoważenia obciążenia. Domeny można hostować w usłudze Azure DNS. Aby uzyskać więcej informacji na temat korzystania z usługi Azure DNS z modułem równoważenia obciążenia, zobacz [Korzystanie z usługi Azure DNS z innymi usługami platformy Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o łączeniu usług równoważenia obciążenia na platformie Azure w [obszarze Korzystanie z usług równoważenia obciążenia na platformie Azure.](../traffic-manager/traffic-manager-load-balancing-azure.md)
- Dowiedz się, jak używać różnych typów dzienników do zarządzania i rozwiązywania problemów z modułem równoważenia obciążenia w [dziennikach usługi Azure Monitor dla modułu Azure Load Balancer.](../load-balancer/load-balancer-monitor-log.md)
