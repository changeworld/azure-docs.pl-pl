---
title: 'Samouczek: maszyny wirtualne usługi Load Balancer w różnych strefach dostępności — Azure Portal | Microsoft Docs'
description: Ten samouczek przedstawia sposób tworzenia usługi Load Balancer w warstwie Standardowa przy użyciu frontonu strefowo nadmiarowego w celu zrównoważenia obciążenia maszyn wirtualnych w różnych strefach dostępności przy użyciu witryny Azure Portal
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines across availability zones in a region, so that the customers can still access the web service if a datacenter is unavailable.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 9ff0b53f6c6f10a2e97bd3158f874fa5cfe33bb6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Samouczek: równoważenie obciążenia maszyn wirtualnych w różnych strefach dostępności przy użyciu usługi Load Balancer w strefie Standardowa w witrynie Azure Portal

Równoważenie obciążenia zwiększa dostępność dzięki rozdzieleniu żądań przychodzących pomiędzy wiele maszyn wirtualnych. W tym samouczku opisano kroki tworzenia publicznego modułu równoważenia obciążenia w warstwie Standardowa, który równoważy obciążenia maszyn wirtualnych w różnych strefach dostępności. Pomaga on chronić aplikacje i dane, zmniejszając prawdopodobieństwo wystąpienia awarii lub utraty całego centrum danych. Dzięki nadmiarowości stref w co najmniej jednej strefie dostępności może wystąpić błąd, a ścieżka do danych będzie działać tak długo, jak jedna strefa w regionie pozostanie w dobrej kondycji. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie usługi Load Balancer w warstwie Standardowa
> * Tworzenie sieciowych grup zabezpieczeń w celu zdefiniowania reguł ruchu przychodzącego
> * Tworzenie strefowo nadmiarowych maszyn wirtualnych w wielu strefach i dołączanie do modułu równoważenia obciążenia
> * Tworzenie sondy kondycji modułu równoważenia obciążenia
> * Tworzenie reguł ruchu modułu równoważenia obciążenia
> * Tworzenie podstawowej witryny usług IIS
> * Wyświetlanie działającego modułu równoważenia obciążenia

Aby uzyskać więcej informacji na temat obsługi stref dostępności przy użyciu usługi Load Balancer w warstwie Standardowa, zobacz [Standard Load Balancer and Availability Zones (Usługa Load Balancer w warstwie Standardowa i strefy dostępności)](load-balancer-standard-availability-zones.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Tworzenie usługi Load Balancer w warstwie Standardowa

Usługa Load Balancer w warstwie Standardowa obsługuje tylko publiczny adres IP w warstwie Standardowa. W przypadku tworzenia nowego publicznego adresu IP podczas tworzenia modułu równoważenia obciążenia jest on automatycznie konfigurowany jako standardowa jednostka SKU. Jest on również automatycznie strefowo nadmiarowy.

1. W lewym górnym rogu ekranu kliknij pozycję **Utwórz zasób** > **Sieć** > **Moduł równoważenia obciążenia**.
2. Na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź następujące wartości dla modułu równoważenia obciążenia:
    - *myLoadBalancer* — jako nazwę modułu równoważenia obciążenia.
    - **Public** — jako typ modułu równoważenia obciążenia.
     - *myPublicIP* — jako nowy publiczny adres IP, który tworzysz. W tym celu kliknij pozycję **Wybierz publiczny adres IP**, a następnie kliknij pozycję **Utwórz nowy**. Wpisz nazwę *myPublicIP*. Jednostka SKU ma domyślnie typ Standardowa. Wybierz pozycję **Strefowo nadmiarowa** w obszarze **Strefa dostępności**.
    - *myResourceGroupLBAZ* — jako nazwę nowo tworzonej grupy zasobów.
    - **westeurope** — jako lokalizację.
3. Kliknij pozycję **Utwórz**, aby utworzyć moduł równoważenia obciążenia.
   
    ![Tworzenie modułu równoważenia obciążenia](./media/load-balancer-standard-public-availability-zones-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji utworzysz sieć wirtualną i maszyny wirtualne w różnych strefach w regionie, a następnie zainstalujesz usługi IIS na maszynach wirtualnych, aby ułatwić testowanie strefowo nadmiarowego modułu równoważenia obciążenia. W rezultacie błąd strefy spowoduje niepowodzenie sondy kondycji maszyny wirtualnej w tej samej strefie, a ruch będzie nadal obsługiwany przez maszyny wirtualne w innych strefach.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną na potrzeby wdrażania serwerów zaplecza.

1. W lewej górnej części ekranu kliknij pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**, a następnie wprowadź następujące wartości dla sieci wirtualnej:
    - *myVnet* — jako nazwę sieci wirtualnej.
    - *myResourceGroupLBAZ* — jako nazwę istniejącej grupy zasobów.
    - *myBackendSubnet* — jako nazwę podsieci.
2. Kliknij pozycję **Utwórz**, aby utworzyć sieć wirtualną.

    ![Tworzenie sieci wirtualnej](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń w celu zdefiniowania połączeń przychodzących do sieci wirtualnej.

1. W lewej górnej części ekranu kliknij pozycję **Utwórz zasób**, w polu wyszukiwania wpisz *Sieciowa grupa zabezpieczeń*, a następnie na stronie sieciowej grupy zabezpieczeń kliknij pozycję **Utwórz**.
2. Na stronie tworzenia sieciowej grupy zabezpieczeń wprowadź następujące wartości:
    - *myNetworkSecurityGroup* — jako nazwę sieciowej grupy zabezpieczeń.
    - *myResourceGroupLBAZ* — jako nazwę istniejącej grupy zasobów.
   
![Tworzenie sieci wirtualnej](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-network-security-group-rules"></a>Tworzenie reguł sieciowej grupy zabezpieczeń

W tej sekcji utworzysz reguły sieciowej grupy zabezpieczeń, aby zezwolić na połączenia przychodzące przy użyciu protokołów HTTP i RDP w witrynie Azure Portal.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie zasoby** w menu po lewej stronie, a następnie wyszukaj i kliknij pozycję **myNetworkSecurityGroup** znajdującą się w grupie zasobów **myResourceGroupLBAZ**.
2. W obszarze **Ustawienia** kliknij pozycję **Reguły zabezpieczeń dla ruchu przychodzącego**, a następnie kliknij przycisk **Dodaj**.
3. Wprowadź następujące wartości dla reguły zabezpieczeń dla ruchu przychodzącego o nazwie *myHTTPRule*, aby umożliwić obsługę przychodzących połączeń HTTP przy użyciu portu 80:
    - *Tag usługi* — w polu **Źródło**.
    - *Internet* — w polu **Tag usługi źródłowej**
    - *80* — w polu **Docelowe zakresy portów**
    - *TCP* — w polu **Protokół**
    - *Zezwalaj* — w polu **Akcja**
    - *100* — w polu **Priorytet**
    - *myHTTPRule* — jako nazwę reguły modułu równoważenia obciążenia.
    - *Zezwalaj na HTTP* — jako opis reguły modułu równoważenia obciążenia.
4. Kliknij przycisk **OK**.
 
 ![Tworzenie sieci wirtualnej](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Powtórz kroki od 2 do 4, używając następujących wartości, aby utworzyć inną regułę o nazwie *myRDPRule* zezwalającą na przychodzące połączenia RDP przy użyciu portu 3389:
    - *Tag usługi* — w polu **Źródło**.
    - *Internet* — w polu **Tag usługi źródłowej**
    - *3389* — w polu **Docelowe zakresy portów**
    - *TCP* — w polu **Protokół**
    - *Zezwalaj* — w polu **Akcja**
    - *200* — w polu **Priorytet**
    - *myRDPRule* — w polu nazwy
    - *Zezwalaj na RDP* — w polu opisu

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszyny wirtualne w różnych strefach regionu (strefie 1, strefie 2 i strefie 3). Mogą one pełnić rolę serwerów zaplecza dla modułu równoważenia obciążenia.

1. W lewej górnej części ekranu kliknij pozycję **Utwórz zasób** > **Compute** > **Windows Server 2016 Datacenter** i wprowadź następujące wartości dla maszyny wirtualnej:
    - *myVM1* — jako nazwę maszyny wirtualnej.        
    - *azureuser* — jako nazwę użytkownika administratora.    
    - *myResourceGroupLBAZ* — w obszarze **Grupa zasobów** wybierz opcję **Użyj istniejącej**, a następnie wybierz wartość *myResourceGroupLBAZ*.
2. Kliknij przycisk **OK**.
3. Wybierz **DS1_V2** jako rozmiar maszyny wirtualnej, a następnie kliknij pozycję **Wybierz**.
4. Wprowadź następujące wartości ustawień maszyny wirtualnej:
    - *zone 1* — jako strefę dostępności, w której umieszczono maszynę wirtualną.
    -  *myVNet* — jako sieć wirtualną.
    - *myBackendSubnet* — jako podsieć.
    - *myNetworkSecurityGroup* — jako nazwę sieciowej grupy zabezpieczeń (zapory).
5. Kliknij pozycję **Wyłączone**, aby wyłączyć diagnostykę rozruchu.
6. Kliknij przycisk **OK**, przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **Utwórz**.
  
 ![Tworzenie maszyny wirtualnej](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. Utwórz drugą maszynę wirtualną o nazwie *VM2* w strefie 2 i trzecią maszynę wirtualną w strefie 3 z następującymi ustawieniami: siecią wirtualną *myVnet*, podsiecią *myBackendSubnet* i sieciową grupą zabezpieczeń **myNetworkSecurityGroup*, wykonując kroki 1–6.

### <a name="install-iis-on-vms"></a>Instalowanie usług IIS na maszynach wirtualnych

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myVM1** znajdującą się w grupie zasobów *myResourceGroupLBAZ*.
2. Na stronie **Przegląd** kliknij pozycję **Połącz** dla protokołu RDP z maszyną wirtualną.
3. Zaloguj się do maszyny wirtualnej przy użyciu nazwy użytkownika *azureuser*.
4. Na pulpicie serwera przejdź do pozycji **Narzędzia administracyjne systemu Windows**>**Menedżer serwera**.
5. Na stronie Szybki start Menedżera serwera kliknij pozycję **Dodaj role i funkcje**.

   ![Dodawanie do puli adresów zaplecza ](./media/load-balancer-standard-public-availability-zones-portal/servermanager.png)    

1. W **kreatorze dodawania ról i funkcji** użyj następujących wartości:
    - Na stronie **Wybieranie typu instalacji** kliknij pozycję **Instalacja oparta na rolach lub funkcjach**.
    - Na stronie **Wybieranie serwera docelowego** kliknij pozycję **myVM1**.
    - Na stronie **Wybieranie roli serwera** kliknij pozycję **Serwer internetowy (IIS)**.
    - Postępuj zgodnie z instrukcjami, aby ukończyć pozostałą część kreatora.
2. Zamknij sesję protokołu RDP dla maszyny wirtualnej — *myVM1*.
3. Powtórz kroki od 1 do 7, aby zainstalować usługi IIS na maszynach wirtualnych *myVM2* i *myVM3*.

## <a name="create-load-balancer-resources"></a>Tworzenie zasobów modułu równoważenia obciążenia

W tej sekcji skonfigurujesz ustawienia modułu równoważenia obciążenia dla puli adresów zaplecza i sondy kondycji oraz określisz reguły modułu równoważenia obciążenia i reguły translatora adresów sieciowych.


### <a name="create-a-backend-address-pool"></a>Tworzenie puli adresów zaplecza

Na potrzeby rozdzielania ruchu między maszyny wirtualne używana jest pula adresów zaplecza, zawierająca adresy IP wirtualnych kart sieciowych połączonych z modułem równoważenia obciążenia. Utwórz pulę adresów zaplecza *myBackendPool* obejmującą maszyny wirtualne *VM1*, *VM2* i *VM3*.

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myLoadBalancer**.
2. W obszarze **Ustawienia** kliknij pozycję **Pule zaplecza**, a następnie kliknij pozycję **Dodaj**.
3. Na stronie **Dodawanie puli zaplecza** wykonaj następujące czynności:
    - W polu nazwy wpisz *myBackEndPool* jako nazwę puli zaplecza.
    - W menu rozwijanym w obszarze **Sieć wirtualna** kliknij pozycję **myVNet**.
    - W menu rozwijanym w obszarze **Maszyna wirtualna** kliknij pozycję **myVM1**.
    - W menu rozwijanym w obszarze **Adres IP** kliknij pozycję myVM1.
4. Kliknij pozycję **Dodaj nowy zasób zaplecza**, aby dodać wszystkie maszyny wirtualne (*myVM2* i *myVM3*) do dodania do puli zaplecza modułu równoważenia obciążenia.
5. Kliknij pozycję **Add** (Dodaj).

    ![Dodawanie do puli adresów zaplecza ](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Upewnij się, że ustawienie puli zaplecza modułu równoważenia obciążenia wyświetla wszystkie trzy maszyny wirtualne — **myVM1**, **myVM2** i **myVM3**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji umożliwia modułowi równoważenia obciążenia monitorowanie stanu aplikacji. Dynamicznie dodaje lub usuwa maszyny wirtualne w rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. Utwórz sondę kondycji *myHealthProbe*, aby monitorować kondycję maszyn wirtualnych.

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myLoadBalancer**.
2. W obszarze **Ustawienia** kliknij pozycję **Sondy kondycji**, a następnie kliknij pozycję **Dodaj**.
3. Utwórz sondę kondycji, używając następujących wartości:
    - *myHealthProbe* — jako nazwy sondy kondycji.
    - **HTTP** — jako typu protokołu.
    - *80* — jako numeru portu.
    - *15* — w polu **Interwał** jako liczbę sekund między próbami sondy.
    - *2* — w polu **Próg złej kondycji** jako liczbę kolejnych niepowodzeń sondy, które muszą wystąpić, aby maszyna wirtualna została uznana za będącą w złej kondycji.
4. Kliknij przycisk **OK**.

   ![Dodawanie sondy](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę modułu równoważenia obciążenia *myLoadBalancerRuleWeb* w celu nasłuchiwania na porcie 80 w puli frontonu *myFrontEndPool* i wysyłania ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza *myBackEndPool*, również przy użyciu portu 80. 

1. W menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myLoadBalancer**.
2. W obszarze **Ustawienia** kliknij pozycję **Reguły równoważenia obciążenia**, a następnie kliknij pozycję **Dodaj**.
3. Skonfiguruj regułę równoważenia obciążenia, używając następujących wartości:
    - *myHTTPRule* — jako nazwy reguły równoważenia obciążenia.
    - **TCP** — jako typu protokołu.
    - *80* — jako numeru portu.
    - *80* — jako portu zaplecza.
    - *myBackendPool* — jako nazwy puli zaplecza.
    - *myHealthProbe* — jako nazwy sondy kondycji.
4. Kliknij przycisk **OK**.
    
    ![Dodawanie reguły równoważenia obciążenia](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia
1. Znajdź publiczny adres IP dla usługi Load Balancer na ekranie **Przegląd**. Kliknij pozycję **Wszystkie zasoby**, a następnie kliknij pozycję **myPublicIP**.

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

      ![Internetowy serwer usług IIS](./media/load-balancer-standard-public-availability-zones-portal/9-load-balancer-test.png)

Aby zobaczyć, jak moduł równoważenia obciążenia rozdziela ruch między maszynami wirtualnymi rozproszonymi w strefie, możesz wymusić odświeżenie w przeglądarce internetowej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, moduł równoważenia obciążenia i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Aby to zrobić, wybierz grupę zasobów zawierającą moduł równoważenia obciążenia, a następnie kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
