---
title: 'Samouczek: Równoważenie obciążenia ruchem internetowym przez skierowanie go do maszyn wirtualnych — Azure Portal'
titlesuffix: Azure Load Balancer
description: W tym samouczku pokazano, jak utworzyć wystąpienie usługi Load Balancer w warstwie Standardowa przy użyciu witryny Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 22d0e56a77036c551b6006f43997c92fcce07499
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273353"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Samouczek: Równoważenie obciążenia ruchem internetowym przez skierowanie go do maszyn wirtualnych przy użyciu witryny Azure Portal

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. W tym samouczku poznasz poszczególne składniki usługi Load Balancer w warstwie Standardowa platformy Azure, które dystrybuują ruch internetowy do maszyn wirtualnych i zapewniają wysoką dostępność. Omawiane kwestie:


> [!div class="checklist"]
> * Tworzenie modułu równoważenia obciążenia platformy Azure
> * Tworzenie zasobów modułu równoważenia obciążenia
> * Tworzenie maszyn wirtualnych i instalowanie serwera usług IIS
> * Wyświetl równoważenia obciążenia w akcji
> * Dodawanie i usuwanie maszyn wirtualnych z modułu równoważenia obciążenia

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Tworzenie usługi Load Balancer w warstwie Standardowa

W tej sekcji opisano tworzenie standardowego modułu równoważenia obciążenia, który pomaga równoważyć obciążenie maszyn wirtualnych. Usługa Load Balancer w warstwie Standardowa obsługuje tylko publiczny adres IP w warstwie Standardowa. Podczas tworzenia usługi Load Balancer w warstwie Standardowa musisz także utworzyć nowy publiczny adres IP w warstwie Standardowa, który jest skonfigurowany jako fronton (domyślnie o nazwie *LoadBalancerFrontend*) dla usługi Load Balancer w warstwie Standardowa. 

1. W lewym górnym rogu ekranu kliknij pozycję **Utwórz zasób** > **Sieć** > **Moduł równoważenia obciążenia**.
2. Na karcie **Podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przeglądanie + tworzenie**:

    | Ustawienie                 | Value                                              |
    | ---                     | ---                                                |
    | Subscription               | Wybierz subskrypcję.    |    
    | Resource group         | Wybierz **Utwórz nową** i typ *myResourceGroupSLB* w polu tekstowym.|
    | Name (Nazwa)                   | *myLoadBalancer*                                   |
    | Region         | Wybierz pozycję **Europa Zachodnia**.                                        |
    | Type          | Wybierz pozycję **Publiczna**.                                        |
    | SKU           | Wybierz opcję **Standardowa**.                          |
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. |
    | Nazwa publicznego adresu IP              | Wpisz *myPublicIP* w polu tekstowym.   |
    |Strefa dostępności| Wybierz pozycję **Strefowo nadmiarowy**.    |

3. Na karcie **Przeglądanie + tworzenie** kliknij pozycję **Utwórz**.

   ![Tworzenie usługi Load Balancer w warstwie Standardowa](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Tworzenie zasobów modułu równoważenia obciążenia

W tej sekcji należy skonfigurować ustawienia usługi równoważenia obciążenia dla puli adresów zaplecza, sondę kondycji i określić reguły modułu równoważenia.

### <a name="create-a-backend-address-pool"></a>Tworzenie puli adresów zaplecza

Kierowanie ruchu do maszyn wirtualnych, adres IP zawiera pulę adresów zaplecza adresów wirtualnych kart sieciowych połączonych z modułem równoważenia obciążenia. Tworzenie puli adresów zaplecza *myBackendPool* obejmujący maszyny wirtualne na potrzeby równoważenia obciążenia ruchu internetowego.

1. Wybierz **wszystkich usług** w menu po lewej stronie wybierz **wszystkie zasoby**, a następnie kliknij przycisk **myLoadBalancer** na liście zasobów.
2. W obszarze **Ustawienia** kliknij pozycję **Pule zaplecza**, a następnie kliknij pozycję **Dodaj**.
3. Na **Dodawanie puli zaplecza** strony dla nazwy, typu *myBackendPool*, jako nazwę puli zaplecza, a następnie wybierz **Dodaj**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Aby zezwolić na moduł równoważenia obciążenia monitorować stan aplikacji, należy użyć sondy kondycji. Sonda kondycji dynamicznie dodaje lub usuwa maszyny wirtualne w rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. Utwórz sondę kondycji *myHealthProbe*, aby monitorować kondycję maszyn wirtualnych.

1. Wybierz **wszystkich usług** w menu po lewej stronie wybierz **wszystkie zasoby**, a następnie kliknij przycisk **myLoadBalancer** na liście zasobów.
2. W obszarze **Ustawienia** kliknij pozycję **Sondy kondycji**, a następnie kliknij pozycję **Dodaj**.
3. Utwórz sondę kondycji, używając następujących wartości:
     
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Name (Nazwa) | Wprowadź *myHealthProbe*. |
    | Protocol | Wybierz **HTTP**. |
    | Port | Wprowadź *80*.|
    | Interval | Wprowadź *15* liczbę **interwał** w ciągu kilku sekund między próbami sondy. |
    | Próg złej kondycji | Wybierz *2* liczbę **próg złej kondycji** lub kolejnych niepowodzeń sondy musi wystąpić, zanim maszyny Wirtualnej jest uznawana za złą.|
    | Sonda kondycji | Wybierz *myHealthProbe*. |
    
4. Kliknij przycisk **OK**.

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę modułu równoważenia obciążenia *myLoadBalancerRuleWeb* w celu nasłuchiwania na porcie 80 w frontonie *Myfrontendpool* i wysyłania ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza *myBackEndPool* również przy użyciu portu 80.

1. Wybierz **wszystkich usług** w menu po lewej stronie wybierz **wszystkie zasoby**, a następnie kliknij przycisk **myLoadBalancer** na liście zasobów.
2. W obszarze **Ustawienia** kliknij pozycję **Reguły równoważenia obciążenia**, a następnie kliknij pozycję **Dodaj**.
3. Aby skonfigurować regułę równoważenia obciążenia, używając następujących wartości:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Name (Nazwa) | Wprowadź *myHTTPRule*. |
    | Protocol | wybierz pozycję **TCP**. |
    | Port | Wprowadź *80*.|
    | Port zaplecza | Wprowadź *80*. |
    | Pula zaplecza | Wybierz *myBackendPool*.|
    | Sonda kondycji | Wybierz *myHealthProbe*. |
    
4. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **OK**.

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji Utwórz sieć wirtualną, utworzyć trzy maszyny wirtualne dla puli zaplecza modułu równoważenia obciążenia i następnie zainstalujesz usługi IIS na maszynach wirtualnych, aby ułatwić testowanie modułu równoważenia obciążenia.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**.
2. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Name (Nazwa) | Wprowadź nazwę *myVNet*. |
    | Przestrzeń adresowa | Wprowadź adres *10.1.0.0/16*. |
    | Subscription | Wybierz subskrypcję.|
    | Resource group | Wybierz istniejący zasób - *myResourceGroupSLB*. |
    | Location | Wybierz pozycję **Europa Zachodnia**.|
    | Podsieć — nazwa | Wprowadź nazwę podsieci *myBackendSubnet*. |
    | Zakres adresów podsieci: 10.41.0.0/24 | Wprowadź *10.1.0.0/24*. |
    
3. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **Utwórz**.

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Load Balancer w warstwie standardowa obsługuje tylko maszyny wirtualne z adresami IP w warstwie standardowa w puli zaplecza. W tej sekcji utworzysz trzy maszyny wirtualne (*myVM1*, *myVM2*, i *myVM3*) przy użyciu standardowego publicznego adresu IP w trzech różnych stref (*strefa 1*, *Strefa 2*, i *strefa 3*), są dodawane do puli zaplecza standardowego modułu równoważenia obciążenia, który został utworzony wcześniej.

1. W lewej górnej części portalu wybierz pozycję **Utwórz zasób** > **Compute** > **Windows Server 2016 Datacenter**. 
   
1. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:
   - **Subskrypcja** > **Grupa zasobów**: Select **myResourceGroupSLB**.
   - **Szczegóły wystąpienia** > **Nazwa maszyny wirtualnej**: Typ *myVM1*.
   - **Szczegóły wystąpienia** > **Region** > Wybierz **Europa Zachodnia**.
   - **Szczegóły wystąpienia** > **opcji dostępności** > Wybierz **strefy dostępności**. 
   - **Szczegóły wystąpienia** > **strefy dostępności** > Wybierz **1**.
  
1. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**. 
   
   - Upewnij się, że zostały wybrane następujące opcje:
       - **Sieć wirtualna**: **myVnet**
       - **Podsieci**: **myBackendSubnet**
       - **Publiczny adres IP** > Wybierz **Utwórz nową**i w **tworzenie publicznego adresu IP** oknie dla **jednostki SKU**, wybierz opcję **standardowa**, i **strefy dostępności**, wybierz opcję **strefowo nadmiarowy**
      
   - Aby utworzyć nową sieciową grupę zabezpieczeń (NSG, network security group), czyli pewien typ zapory, w obszarze **Sieciowa grupa zabezpieczeń** wybierz pozycję **Zaawansowane**. 
       1. W polu **Konfigurowanie sieciowej grupy zabezpieczeń** wybierz pozycję **Utwórz nową**. 
       1. Typ *myNetworkSecurityGroup*i wybierz **OK**.

   - Aby utworzyć maszynę Wirtualną częścią puli zaplecza modułu równoważenia obciążenia, wykonaj następujące czynności:
        - W **Równoważenie obciążenia**, aby uzyskać **umieścić tej maszyny wirtualnej za modułem istniejące rozwiązanie do równoważenia obciążenia?** , wybierz opcję **tak**.
        - W **ustawienia równoważenia obciążenia**, aby uzyskać **opcje równoważenia obciążenia**, wybierz opcję **usługa Azure load balancer**.
        - Aby uzyskać **wybrać moduł równoważenia obciążenia**, *myLoadBalancer*. 
1. Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej** > **Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**. 
1. Wybierz pozycję **Przegląd + utwórz**.   
1. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.
1. Postępuj zgodnie z instrukcjami, aby utworzyć dwie dodatkowe maszyny wirtualne - *myVM2* i *myVM3*, za pomocą publicznego adresu IP standardowej jednostki SKU w **strefy dostępności** **2** i **3** odpowiednio i inne ustawienia takie same jak *myVM1*.  

### <a name="create-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

W tej sekcji utworzysz reguły sieciowej grupy zabezpieczeń, aby zezwolić na połączenia przychodzące za pośrednictwem protokołu HTTP.

1. Wybierz **wszystkich usług** w menu po lewej stronie wybierz **wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myNetworkSecurityGroup** znajdującą się w **myResourceGroupSLB** grupy zasobów.
2. W obszarze **Ustawienia** kliknij pozycję **Reguły zabezpieczeń dla ruchu przychodzącego**, a następnie kliknij przycisk **Dodaj**.
3. Wprowadź następujące wartości dla reguły zabezpieczeń dla ruchu przychodzącego o nazwie *myHTTPRule*, aby umożliwić obsługę przychodzących połączeń HTTP przy użyciu portu 80:
    - *Tag usługi* — w polu **Źródło**.
    - *Internet* — w polu **Tag usługi źródłowej**
    - *80* — w polu **Docelowe zakresy portów**
    - *TCP* — w polu **Protokół**
    - *Zezwalaj* — w polu **Akcja**
    - *100* — w polu **Priorytet**
    - *myHTTPRule* — jako nazwę
    - *Zezwalaj na HTTP* —jako opis
4. Wybierz pozycję **Dodaj**.

### <a name="install-iis-on-vms"></a>Instalowanie usług IIS na maszynach wirtualnych

1. Wybierz **wszystkich usług** w menu po lewej stronie wybierz **wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myVM1** znajdującą się w  *myResourceGroupSLB* grupy zasobów.
2. Na stronie **Przegląd** kliknij pozycję **Połącz** dla protokołu RDP z maszyną wirtualną.
3. W wyskakującym okienku **Podłączanie do maszyny wirtualnej** wybierz pozycję **Pobierz plik RDP**, a następnie otwórz pobrany plik RDP.
4. W oknie **Podłączanie pulpitu zdalnego** kliknij pozycję **Połącz**.
5. Zaloguj się do maszyny wirtualnej przy użyciu poświadczeń podanych podczas jej tworzenia. Spowoduje to uruchomienie sesji pulpitu zdalnego z maszyną wirtualną — *myVM1*.
6. Na pulpicie serwera przejdź do pozycji **Narzędzia administracyjne systemu Windows**>**Windows PowerShell**.
7. W oknie programu PowerShell uruchom poniższe polecenia, aby zainstalować serwer usług IIS, usunąć domyślny plik iisstart.htm i dodać nowy plik iisstart.htm, który wyświetla nazwę maszyny wirtualnej:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Zamknij sesję protokołu RDP na maszynie wirtualnej *myVM1*.
7. Powtórz kroki od 1 do 6, aby zainstalować usługi IIS i zaktualizowany plik iisstart.htm na maszynach *myVM2* i *myVM3*.

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia
1. Znajdź publiczny adres IP dla usługi Load Balancer na ekranie **Przegląd**. Wybierz **wszystkich usług** w menu po lewej stronie wybierz **wszystkie zasoby**, a następnie kliknij przycisk **myPublicIP**.

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

      ![Internetowy serwer usług IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Aby zobaczyć moduł równoważenia obciążenia rozdziela ruch między trzy maszyny wirtualne, z tą aplikacją, możesz wymusić odświeżenie przeglądarki sieci web.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Usuwanie lub dodawanie maszyn wirtualnych w puli zaplecza
Maszyny wirtualne, na których działa aplikacja, mogą wymagać przeprowadzenia konserwacji, na przykład zainstalowania aktualizacji systemu operacyjnego. Zwiększony ruch do Twojej aplikacji może wiązać się z koniecznością dodania większej liczby maszyn wirtualnych. W tej sekcji dowiesz się, jak usunąć lub dodać maszyny Wirtualnej (*myVM1*) z modułu równoważenia obciążenia.

### <a name="remove-vm-from-a-backend-pool"></a>Usuwanie maszyny Wirtualnej z puli zaplecza
Aby usunąć *myVM1* z puli zaplecza, wykonaj następujące czynności:

1. Wybierz **wszystkich usług** w menu po lewej stronie wybierz **wszystkie zasoby**, a następnie kliknij przycisk **myLoadBalancer** na liście zasobów.
2. W obszarze **Ustawienia** kliknij pozycję **Pule zaplecza**, a następnie na liście pul zaplecza kliknij pozycję **myBackendPool**.
3. Na **myBackendPool** strony, aby usunąć *VM1* wybierz ikonę Usuń na końcu wiersza, który wyświetla *myVM1*, a następnie kliknij przycisk **Zapisz**.

Po usunięciu maszyny *myVM1* z puli adresów zaplecza możesz wykonywać wszelkie zadania konserwacyjne na maszynie *myVM1*, takie jak instalowanie aktualizacji oprogramowania. W przypadku braku *VM1*, obciążenie jest równoważone *myVM2* i *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Dodaj maszynę Wirtualną do puli zaplecza
Aby dodać *myVM1* do puli zaplecza, wykonaj następujące czynności:

1. Wybierz **wszystkich usług** w menu po lewej stronie wybierz **wszystkie zasoby**, a następnie wybierz pozycję **myVM1** na liście zasobów.
2. W **VM1** w obszarze **ustawienia**, wybierz opcję **sieć**.
3. W **sieć** wybierz opcję **Równoważenie obciążenia** , a następnie wybierz pozycję **Dodaj równoważenia obciążenia**.
4. W **Dodaj równoważenia obciążenia** wykonaj następujące czynności:
   1. Aby uzyskać **opcje równoważenia obciążenia**, wybierz opcję **usługa Azure load balancer**.
   2. Aby uzyskać **wybrać moduł równoważenia obciążenia**, wybierz opcję *myLoadBalancer*.
   3. Aby uzyskać **wybierz pulę zaplecza**, wybierz opcję *myBackendPool*. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów, moduł równoważenia obciążenia i wszystkich powiązanych zasobów. Aby to zrobić, wybierz *myResouceGroupSLB* grupę zasobów, która zawiera moduł równoważenia obciążenia, a następnie wybierz **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono standardowego modułu równoważenia obciążenia, dołączono maszyny wirtualne do niego, skonfigurowane reguły modułu równoważenia obciążenia ruchu, sondy kondycji i następnie przetestowano moduł równoważenia obciążenia. Ponadto usunięto maszynę wirtualną z zestawu o zrównoważonym obciążeniu i dodano ją z powrotem do puli adresów zaplecza. Aby dowiedzieć się więcej na temat usługi Azure Load Balancer, przejdź do samouczków dotyczących usługi Azure Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
