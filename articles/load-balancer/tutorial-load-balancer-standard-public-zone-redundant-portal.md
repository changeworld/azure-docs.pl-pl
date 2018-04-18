---
title: Obciążenie maszyny wirtualne modułu równoważenia dostępności stref - portalu Azure | Dokumentacja firmy Microsoft
description: Utwórz standardowy moduł równoważenia obciążenia z strefowo nadmiarowy frontonu zrównoważeniu maszyn wirtualnych w różnych strefach dostępności przy użyciu portalu Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/18
ms.author: kumud
ms.openlocfilehash: ad476922342844a908961960407eb344711932f5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Równoważenie obciążenia maszyn wirtualnych w różnych strefach dostępności standardowe usługi równoważenia obciążenia przy użyciu portalu Azure

Ta procedura artykułu przez proces tworzenia publicznego obciążenia standardowego równoważenia z frontonu nadmiarowe strefy do osiągnięcia osiągnąć redundancję strefy bez zależności na wiele rekordów DNS. Pojedynczy adres IP frontonu w standardowe usługi równoważenia obciążenia jest automatycznie strefowo nadmiarowy. Dla Twojej usługi równoważenia obciążenia, z jednym adresem IP przy użyciu frontonu nadmiarowe strefy można teraz osiągnąć żadnej maszyny Wirtualnej w sieci wirtualnej w regionie, który jest we wszystkich strefach dostępności. Strefy dostępności chronią aplikacje i dane, zmniejszając prawdopodobieństwo wystąpienia awarii lub utraty całego centrum danych. Z nadmiarowością strefy co najmniej jedna strefa dostępności może zakończyć się niepowodzeniem rekompensaty dla użytkownika i ścieżki danych tak długo, jak jedną strefę w dobrej kondycji pozostaje regionu. 

Aby uzyskać więcej informacji o używaniu stref dostępności z modułem równoważenia obciążenia standardowego, zobacz [standardowego modułu równoważenia obciążenia i dostępności stref](load-balancer-standard-availability-zones.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal na stronie [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer"></a>Utwórz publiczny standardowego modułu równoważenia obciążenia

Standardowa usługa równoważenia obciążenia obsługuje tylko do standardowych publicznego adresu IP. Po utworzeniu nowego publicznego adresu IP podczas tworzenia modułu równoważenia obciążenia jest automatycznie konfigurowany jako wersja wersji Standard, a jest również automatycznie strefowo nadmiarowy.

1. W lewej górnej części ekranu, kliknij polecenie **Utwórz zasób** > **sieci** > **modułu równoważenia obciążenia**.
2. W **tworzenia modułu równoważenia obciążenia** stronie należy podać te wartości dla usługi równoważenia obciążenia:
    - *myLoadBalancer* — dla nazwy usługi równoważenia obciążenia.
    - **Publiczny** — dla typu usługi równoważenia obciążenia.
     - *myPublicIP* — nowy adres publiczny adres IP, który można utworzyć. Aby to zrobić, kliknij przycisk **wybierz publiczny adres IP**, a następnie kliknij przycisk **Utwórz nowy**. Nazwa typu *myPublicIP*, jednostka SKU jest standardem domyślnie i wybierz **Strefowo nadmiarowy** dla **strefy dostępności**.
    - *myResourceGroupLBAZ* — nazwę nowej grupy zasobów, które można utworzyć.
    - **westeurope** — lokalizacji.
3. Kliknij przycisk **Utwórz** można utworzyć modułu równoważenia obciążenia.
   
    ![Tworzenie modułu równoważenia obciążenia](./media/load-balancer-standard-public-availability-zones-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>Utwórz serwerów wewnętrznej bazy danych

W tej sekcji możesz utworzyć sieć wirtualną, Tworzenie maszyn wirtualnych w różnych strefach (strefy 1, 2 strefy i 3) dla tego obszaru można dodać do puli zaplecza modułu równoważenia obciążenia, a następnie zainstaluj usługę IIS na maszynach wirtualnych, aby ułatwić testowanie bilans obciążenia strefowo nadmiarowy ncer. W związku z tym jeśli strefa nie powiedzie się, sondy kondycji dla maszyny Wirtualnej w tej samej strefie zakończy się niepowodzeniem, a ruch nadal ma być obsługiwana przez maszyny wirtualne w innych stref.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
1. W lewej górnej części ekranu kliknij polecenie **Utwórz zasób** > **sieci** > **sieci wirtualnej** i wprowadzić te wartości sieć wirtualna:
    - *myVnet* — dla nazwy sieci wirtualnej.
    - *myResourceGroupLBAZ* — nazwę istniejącej grupy zasobów
    - *myBackendSubnet* — dla nazwy podsieci.
2. Kliknij przycisk **Utwórz** można utworzyć sieci wirtualnej.

    ![Tworzenie sieci wirtualnej](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

1. W lewej górnej części ekranu, kliknij polecenie **Utwórz zasób**, w polu wyszukiwania wpisz *sieciowej grupy zabezpieczeń*, a na stronie grupy zabezpieczeń sieci, kliknij przycisk **Utwórz**.
2. Na stronie grupy zabezpieczeń sieci utwórz wprowadź następujące wartości:
    - *myNetworkSecurityGroup* — Nazwa grupy zabezpieczeń sieci.
    - *myResourceGroupLBAZ* — nazwę istniejącej grupy zasobów.
   
![Tworzenie sieci wirtualnej](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-nsg-rules"></a>Tworzenie reguły NSG

W tej sekcji utworzysz reguły NSG, aby zezwolić na połączenia przychodzące przy użyciu protokołu HTTP i protokołu RDP przy użyciu portalu Azure.

1. W portalu Azure kliknij **wszystkie zasoby** w menu po lewej stronie, a następnie wyszukaj i kliknij przycisk **myNetworkSecurityGroup** znajdującej się w **myResourceGroupLBAZ** grupy zasobów.
2. W obszarze **ustawienia**, kliknij przycisk **reguły zabezpieczeń dla ruchu przychodzącego**, a następnie kliknij przycisk **Dodaj**.
3. Należy podać te wartości dla reguł zabezpieczeń dla ruchu przychodzącego o nazwie *myHTTPRule* do obsługi przychodzących połączeń HTTP przy użyciu portu 80:
    - *Usługa Tag* — **źródła**.
    - *Internet* — **źródła numer seryjny**
    - *80* — **zakresy port docelowy**
    - *TCP* — **protokołu**
    - *Zezwalaj na* — **akcji**
    - *100* dla **priorytet**
    - *myHTTPRule* dla nazwy
    - *Zezwalaj na HTTP* — opis
4. Kliknij przycisk **OK**.
 
 ![Tworzenie sieci wirtualnej](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Powtórz kroki od 2 do 4, aby utworzyć inną regułę o nazwie *myRDPRule* aby umożliwić połączenia przychodzącego RDP przy użyciu portu 3389 z następującymi wartościami:
    - *Usługa Tag* — **źródła**.
    - *Internet* — **źródła numer seryjny**
    - *3389* — **zakresy port docelowy**
    - *TCP* — **protokołu**
    - *Zezwalaj na* — **akcji**
    - *200* dla **priorytet**
    - *myRDPRule* dla nazwy
    - *Zezwalaj na RDP* — opis


### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

1. W lewej górnej części ekranu, kliknij polecenie **Utwórz zasób** > **obliczeniowe** > **systemu Windows Server Datacenter 2016** , a następnie wprowadź je wartości dla maszyny wirtualnej:
    - *myVM1* — Nazwa maszyny wirtualnej.        
    - *azureuser* — nazwa użytkownika administratora.    
    - *myResourceGroupLBAZ* — **grupy zasobów**, wybierz pozycję **Użyj istniejącego**, a następnie wybierz *myResourceGroupLBAZ*.
2. Kliknij przycisk **OK**.
3. Wybierz **DS1_V2** dla rozmiaru maszyny wirtualnej, a następnie kliknij przycisk **wybierz**.
4. Wprowadź wartości tych ustawień maszyny Wirtualnej:
    - *Strefa 1* — dla strefy, gdzie umieścić maszynę Wirtualną.
    -  *myVNet* — upewnij się, co sieć wirtualna jest zaznaczone.
    - *myBackendSubnet* — upewnij się, jest on wybrany jako podsieci.
    - *myNetworkSecurityGroup* — Nazwa grupy zabezpieczeń sieci (zapory).
5. Kliknij przycisk **wyłączone** wyłączyć diagnostyki rozruchu.
6. Kliknij przycisk **OK**Przejrzyj ustawienia na stronie Podsumowanie, a następnie kliknij przycisk **Utwórz**.
  
 ![Tworzenie maszyny wirtualnej](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. Tworzenie drugiej maszyny Wirtualnej o nazwie *maszyny VM2* w strefie 2, a trzeci maszyny Wirtualnej w strefie 3 oraz *myVnet* co sieć wirtualna *myBackendSubnet* jak podsieć, i * *myNetworkSecurityGroup* jak użycie grupy zabezpieczeń sieci kroki od 1 do 6.

### <a name="install-iis-on-vms"></a>Zainstaluj usługi IIS na maszynach wirtualnych

1. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij **myVM1** znajdującej się w *myResourceGroupLBAZ* grupy zasobów.
2. Na **omówienie** kliknij przycisk **Connect** dla protokołu RDP w Maszynie wirtualnej.
3. Zaloguj się do maszyny Wirtualnej z nazwą użytkownika *azureuser*.
4. Na pulpicie serwera, przejdź do **narzędzi administracyjnych systemu Windows**>**Menedżera serwera**.
5. Na stronie szybkiego startu w Menedżerze serwera kliknij **Dodaj role i funkcje**.

   ![Dodawanie do puli adresów zaplecza ](./media/load-balancer-standard-public-availability-zones-portal/servermanager.png)    

1. W **Kreatora dodawania ról i funkcji**, użyj następujących wartości:
    - W **Wybieranie typu instalacji** kliknij przycisk **Instalacja roli lub funkcji**.
    - W **serwera docelowego wybierz** kliknij przycisk **myVM1**.
    - W **roli serwera wybierz** kliknij przycisk **serwer sieci Web (IIS)**.
    - Postępuj zgodnie z instrukcjami w dalszej części kreatora.
2. Zamknij sesję RDP dla maszyny wirtualnej — *myVM1*.
3. Powtórz kroki od 1 do 7, aby zainstalować usługi IIS na maszynach wirtualnych *myVM2* i *myVM3*.

## <a name="create-load-balancer-resources"></a>Utwórz zasoby usługi równoważenia obciążenia

W tej sekcji należy skonfigurować ustawienia usługi równoważenia obciążenia dla puli adresów zaplecza i sondy kondycji i określić modułu równoważenia obciążenia i reguł NAT.


### <a name="create-a-backend-address-pool"></a>Utwórz pulę adresów zaplecza

Na potrzeby rozdzielania ruchu między maszyny wirtualne używana jest pula adresów zaplecza, zawierająca adresy IP wirtualnych kart sieciowych połączonych z modułem równoważenia obciążenia. Tworzenie puli adresów zaplecza *myBackendPool* uwzględnienie *VM1* i *maszyny VM2*.

1. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie, a następnie kliknij przycisk **myLoadBalancer** na liście zasobów.
2. W obszarze **ustawienia**, kliknij przycisk **pul zaplecza**, następnie kliknij przycisk **Dodaj**.
3. Na **Dodaj pulę zaplecza** strony, wykonaj następujące czynności:
    - Na stronie Nazwa wpisz * myBackEndPool, jak nazwa użytkownika puli wewnętrznej bazy danych.
    - Aby uzyskać **sieci wirtualnej**, w menu rozwijanym kliknij **myVNet**
    - Dla **maszyny wirtualnej**, w menu rozwijanym pozycję **myVM1**.
    - Aby uzyskać **adres IP**, w menu rozwijanym kliknij adres IP myVM1.
4. Kliknij przycisk **Dodaj nowy zasób zaplecza** można dodać każdej maszyny wirtualnej (*myVM2* i *myVM3*) do dodania do puli zaplecza modułu równoważenia obciążenia.
5. Kliknij pozycję **Add** (Dodaj).

    ![Dodawanie do puli adresów zaplecza ](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Upewnij się, że wszystkie trzy maszyny wirtualne - Wyświetla ustawienia puli zaplecza modułu równoważenia obciążenia **myVM1**, **myVM2** i **myVM3**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji umożliwia modułowi równoważenia obciążenia monitorowanie stanu aplikacji. Dynamicznie dodaje lub usuwa maszyny wirtualne w rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. Utworzyć sondy kondycji *myHealthProbe* do monitorowania kondycji maszyn wirtualnych.

1. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie, a następnie kliknij przycisk **myLoadBalancer** na liście zasobów.
2. W obszarze **ustawienia**, kliknij przycisk **sondy kondycji**, następnie kliknij przycisk **Dodaj**.
3. Użyj tych wartości można utworzyć sondy kondycji:
    - *myHealthProbe* — nazwa sondy kondycji.
    - **HTTP** — typ protokołu.
    - *80* — numer portu.
    - *15* — liczba **interwał** w sekundach między próbami sondy.
    - *2* — liczba **próg złej kondycji** lub kolejnych niepowodzeń sondy musi wystąpić przed maszyny Wirtualnej jest określana jako zła.
4. Kliknij przycisk **OK**.

   ![Dodawanie sondy](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Tworzenie reguły modułu równoważenia obciążenia *myLoadBalancerRuleWeb* do nasłuchiwania na porcie 80 w nakładce *FrontendLoadBalancer* i wysyłania ruchu równoważeniem obciążenia sieciowego do puli adresów zaplecza *myBackEndPool* również przy użyciu portu 80. 

1. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie, a następnie kliknij przycisk **myLoadBalancer** na liście zasobów.
2. W obszarze **ustawienia**, kliknij przycisk **reguły równoważenia obciążenia**, następnie kliknij przycisk **Dodaj**.
3. Użyj tych wartości, aby skonfigurować regułę równoważenia obciążenia:
    - *myHTTPRule* - nazwy reguły równoważenia obciążenia.
    - **TCP** — typ protokołu.
    - *80* — numer portu.
    - *80* — portu zaplecza.
    - *myBackendPool* — Nazwa puli wewnętrznej bazy danych.
    - *myHealthProbe* — nazwa sondy kondycji.
4. Kliknij przycisk **OK**.
    
    ![Dodawanie reguły równoważenia obciążenia](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testowanie usługi równoważenia obciążenia
1. Znajdź publicznego adresu IP usługi równoważenia obciążenia na **omówienie** ekranu. Kliknij przycisk **wszystkie zasoby** , a następnie kliknij przycisk **myPublicIP**.

2. Skopiuj publicznego adresu IP, a następnie wklej go w pasku adresu przeglądarki. Domyślna strona serwera sieci Web usług IIS jest wyświetlany w przeglądarce.

      ![Serwer sieci Web usług IIS](./media/load-balancer-standard-public-availability-zones-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, Usuń grupę zasobów, obciążenia równoważenia i wszystkie powiązane zasoby. Aby to zrobić, wybierz grupę zasobów, która zawiera modułu równoważenia obciążenia i kliknij przycisk **usunąć**.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [standardowego modułu równoważenia obciążenia](load-balancer-standard-overview.md).
