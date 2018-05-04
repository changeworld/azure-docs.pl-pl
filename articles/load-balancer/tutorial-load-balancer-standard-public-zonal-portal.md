---
title: Obciążenia maszyn wirtualnych równoważenia w strefie - portalu Azure | Dokumentacja firmy Microsoft
description: Utwórz standardowy moduł równoważenia obciążenia z zonal frontonu zrównoważeniu maszyn wirtualnych w strefie dostępności przy użyciu portalu Azure
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
ms.date: 04/18/2018
ms.author: kumud
ms.openlocfilehash: 41a33436cb0d2c4c2bbfef4888bb704c62e2b91e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="load-balance-vms-within-an-availability-zone-with-a-standard-load-balancer-using-the-azure-portal"></a>Równoważenie obciążenia maszyn wirtualnych w strefie dostępności standardowe usługi równoważenia obciążenia przy użyciu portalu Azure

W tym artykule opisano przez proces tworzenia publiczny [standardowe usługi równoważenia obciążenia](https://aka.ms/azureloadbalancerstandard) z zonal frontonu przy użyciu publicznego standardowe IP adresów przy użyciu portalu Azure. W tym scenariuszu należy określić strefę określonego dla swoich wystąpień frontonu i zaplecza, aby były wyrównane z ścieżki danych i zasobów z określonej strefy.
Aby uzyskać więcej informacji o używaniu stref dostępności z modułem równoważenia obciążenia standardowego, zobacz [standardowego modułu równoważenia obciążenia i dostępności stref](load-balancer-standard-availability-zones.md).

Jeśli chcesz, możesz wykonać ten samouczek przy użyciu [interfejsu wiersza polecenia platformy Azure](load-balancer-standard-public-zonal-cli.md).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal na stronie [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer"></a>Utwórz publiczny standardowego modułu równoważenia obciążenia

Usługa Load Balancer w warstwie Standardowa obsługuje tylko publiczny adres IP w warstwie Standardowa. Po utworzeniu nowego publicznego adresu IP podczas tworzenia modułu równoważenia obciążenia jest automatycznie konfigurowany jako wersja wersji Standard, a jest również automatycznie strefowo nadmiarowy.

1. W lewym górnym rogu ekranu kliknij pozycję **Utwórz zasób** > **Sieć** > **Moduł równoważenia obciążenia**.
2. Na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź następujące wartości dla modułu równoważenia obciążenia:
    - *myLoadBalancer* — jako nazwę modułu równoważenia obciążenia.
    - **Public** — jako typ modułu równoważenia obciążenia.
     - *myPublicIPZonal* — nowy adres publiczny adres IP, który można utworzyć. Aby to zrobić, kliknij przycisk **wybierz publiczny adres IP**, a następnie kliknij przycisk **Utwórz nowy**. Nazwa typu *myPublicIP*, jednostka SKU jest standardem domyślnie i wybiera **1 strefy** dla **strefy dostępności**.
    - *myResourceGroupZLB* — nazwę nowej grupy zasobów, które można utworzyć.
    - **westeurope** — jako lokalizację.
3. Kliknij pozycję **Utwórz**, aby utworzyć moduł równoważenia obciążenia.
   
    ![Utwórz zonal standardowe usługi równoważenia obciążenia z portalu Azure](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji Tworzenie sieci wirtualnej, Utwórz dwie maszyny wirtualne w tej samej strefie (to znaczy, strefa 1) dla tego obszaru do dodania do puli wewnętrznej bazy danych z usługi równoważenia obciążenia i następnie zainstalować usługi IIS na maszynach wirtualnych, aby ułatwić testowanie usługi równoważenia obciążenia strefowo nadmiarowy. W związku z tym jeśli jedna maszyna wirtualna nie powiedzie się, sondy kondycji dla maszyny Wirtualnej w tej samej strefie zakończy się niepowodzeniem, a ruch nadal ma być obsługiwana przez inne maszyny Wirtualnej w ramach tej samej strefie.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
1. W lewej górnej części ekranu kliknij polecenie **Utwórz zasób** > **sieci** > **sieci wirtualnej** i wprowadzić te wartości sieć wirtualna:
    - *myVnet* — jako nazwę sieci wirtualnej.
    - *myResourceGroupZLB* — nazwę istniejącej grupy zasobów
    - *myBackendSubnet* — jako nazwę podsieci.
2. Kliknij pozycję **Utwórz**, aby utworzyć sieć wirtualną.

    ![Tworzenie sieci wirtualnej](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

1. W lewej górnej części ekranu, kliknij polecenie **Utwórz zasób**, w polu wyszukiwania wpisz *sieciowej grupy zabezpieczeń*, a na stronie grupy zabezpieczeń sieci, kliknij przycisk **Utwórz**.
2. Na stronie grupy zabezpieczeń sieci utwórz wprowadź następujące wartości:
    - *myNetworkSecurityGroup* — Nazwa grupy zabezpieczeń sieci.
    - *myResourceGroupLBAZ* — nazwę istniejącej grupy zasobów.
   
    ![Tworzenie sieci wirtualnej](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Tworzenie reguł sieciowej grupy zabezpieczeń

W tej sekcji utworzysz reguły NSG, aby zezwolić na połączenia przychodzące przy użyciu protokołu HTTP i protokołu RDP przy użyciu portalu Azure.

1. W portalu Azure kliknij **wszystkie zasoby** w menu po lewej stronie, a następnie wyszukaj i kliknij przycisk **myNetworkSecurityGroup** znajdującej się w **myResourceGroupZLB** grupy zasobów.
2. W obszarze **Ustawienia** kliknij pozycję **Reguły zabezpieczeń dla ruchu przychodzącego**, a następnie kliknij przycisk **Dodaj**.
3. Wprowadź następujące wartości dla reguły zabezpieczeń dla ruchu przychodzącego o nazwie *myHTTPRule*, aby umożliwić obsługę przychodzących połączeń HTTP przy użyciu portu 80:
    - *Tag usługi* — w polu **Źródło**.
    - *Internet* — w polu **Tag usługi źródłowej**
    - *80* — w polu **Docelowe zakresy portów**
    - *TCP* — w polu **Protokół**
    - *Zezwalaj* — w polu **Akcja**
    - *100* — w polu **Priorytet**
    - *myHTTPRule* dla **nazwy**
    - *Zezwalaj na HTTP* — **opis**
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

    ![Tworzenie reguły protokołu RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

1. W lewej górnej części ekranu, kliknij polecenie **Utwórz zasób** > **obliczeniowe** > **systemu Windows Server Datacenter 2016** , a następnie wprowadź je wartości dla maszyny wirtualnej:
    - *myVM1* — jako nazwę maszyny wirtualnej.        
    - *azureuser* — jako nazwę użytkownika administratora.    
    - *myResourceGroupZLB* — **grupy zasobów**, wybierz pozycję **Użyj istniejącego**, a następnie wybierz *myResourceGroupZLB*.
2. Kliknij przycisk **OK**.
3. Wybierz **DS1_V2** jako rozmiar maszyny wirtualnej, a następnie kliknij pozycję **Wybierz**.
4. Wprowadź następujące wartości ustawień maszyny wirtualnej:
    - *Strefa 1* — strefy dostępności, gdzie umieścić maszynę Wirtualną.
    -  *myVNet* — jako sieć wirtualną.
    - *myVM1PIP* — standardowe publicznego adresu IP, który utworzono. Kliknij przycisk *Utwórz nowy* , a następnie nazwę typu, *myVM1PIP*dla strefy wybierz **1**. Adres IP jednostka SKU jest standardem domyślnie.
    - *myBackendSubnet* — jako podsieć.
    - *myNetworkSecurityGroup* — Nazwa grupy zabezpieczeń sieci (zapora), która już istnieje.
5. Kliknij pozycję **Wyłączone**, aby wyłączyć diagnostykę rozruchu.
6. Kliknij przycisk **OK**, przejrzyj ustawienia na stronie podsumowania, a następnie kliknij przycisk **Utwórz**.
7. Tworzenie drugiej maszyny Wirtualnej o nazwie *myVM2* w strefie 1 z *myVnet* co sieć wirtualna *myVM2PIP* jako standardowa publicznego adresu IP, *myBackendSubnet* jak podsieć, i **myNetworkSecurityGroup* jako użycie grupy zabezpieczeń sieci kroki od 1 do 6.

    ![Tworzenie reguły protokołu RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Zainstaluj usługi IIS na maszynach wirtualnych

1. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij **myVM1** znajdującej się w *myResourceGroupZLB* grupy zasobów.
2. Na stronie **Przegląd** kliknij pozycję **Połącz** dla protokołu RDP z maszyną wirtualną.
3. Zaloguj się do maszyny Wirtualnej o nazwę użytkownika i hasło, które zostało określone podczas tworzenia maszyny Wirtualnej (musisz wybrać **więcej opcji**, następnie **korzystała z innego konta**, aby określić podczas obliczania wprowadzone poświadczenia możesz tworzenia maszyny Wirtualnej), następnie wybierz **OK**. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz pozycję **Tak**, aby nawiązać połączenie.
4. Na pulpicie serwera, przejdź do **narzędzi administracyjnych systemu Windows**>**programu Windows PowerShell**.
6. W oknie programu PowerShell, uruchom następujące polecenia, aby zainstalować serwer usług IIS, Usuń pliku default.htm Dodaj nowy plik default.htm, który wyświetla nazwę maszyny wirtualnej:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
   ```
8. Zamknij sesję RDP z *myVM1*
9. Powtórz kroki od 1 do 8, aby zainstalować usługi IIS w *myVM2*.

## <a name="create-load-balancer-resources"></a>Tworzenie zasobów modułu równoważenia obciążenia

W tej sekcji skonfigurujesz ustawienia modułu równoważenia obciążenia dla puli adresów zaplecza i sondy kondycji oraz określisz reguły modułu równoważenia obciążenia i reguły translatora adresów sieciowych.


### <a name="create-a-backend-address-pool"></a>Tworzenie puli adresów zaplecza

Na potrzeby rozdzielania ruchu między maszyny wirtualne używana jest pula adresów zaplecza, zawierająca adresy IP wirtualnych kart sieciowych połączonych z modułem równoważenia obciążenia. Utwórz pulę adresów zaplecza *myBackendPool* obejmującą maszyny wirtualne *VM1* i *VM2*.

1. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie, a następnie kliknij przycisk **myLoadBalancer* na liście zasobów.
2. W obszarze **Ustawienia** kliknij pozycję **Pule zaplecza**, a następnie kliknij pozycję **Dodaj**.
3. Na stronie **Dodawanie puli zaplecza** wykonaj następujące czynności:
    - Na stronie Nazwa wpisz *myBackEndPool*, jak nazwa użytkownika puli wewnętrznej bazy danych.
    - Aby uzyskać **sieci wirtualnej**, w menu rozwijanym kliknij *myVNet*
    - Dla **maszyny wirtualnej** i **adres IP** dodać *myVM1* i *myVM2* oraz ich odpowiednich publicznych adresów IP.
4. Kliknij pozycję **Add** (Dodaj).
5. Upewnij się, że obie maszyny wirtualne - Wyświetla ustawienia puli zaplecza modułu równoważenia obciążenia **myVM1** i **myVM2**.
 
    ![Tworzenie puli wewnętrznej bazy danych](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

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
    
    ![Dodawanie reguły równoważenia obciążenia](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia
1. Znajdź publiczny adres IP dla usługi Load Balancer na ekranie **Przegląd**. Kliknij pozycję **Wszystkie zasoby**, a następnie kliknij pozycję **myPublicIP**. 

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Domyślna strona, która zawiera nazwę strony sieci Web, serwer jest wyświetlany w przeglądarce.

      ![Internetowy serwer usług IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Aby zapoznać się z usługą równoważenia obciążenia w akcji, możesz force Zatrzymaj maszynę Wirtualną, która jest wyświetlana i odświeżyć przeglądarkę, aby wyświetlić inne nazwy serwera wyświetlany w przeglądarce.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, moduł równoważenia obciążenia i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Aby to zrobić, wybierz grupę zasobów zawierającą moduł równoważenia obciążenia, a następnie kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- [Równoważenie obciążenia maszyn wirtualnych w różnych strefach dostępności](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
