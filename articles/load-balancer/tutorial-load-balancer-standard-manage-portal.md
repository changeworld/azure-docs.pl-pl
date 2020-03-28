---
title: 'Samouczek: Równoważenie obciążenia ruchu internetowego do maszyn wirtualnych — witryna Azure portal'
titleSuffix: Azure Load Balancer
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
ms.openlocfilehash: 8961a50490bdbf8b456e87e1c00577c2c8afd050
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80240362"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Samouczek: równoważenie obciążenia ruchem internetowym przez skierowanie go do maszyn wirtualnych przy użyciu witryny Azure Portal

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. W tym samouczku poznasz poszczególne składniki usługi Load Balancer w warstwie Standardowa platformy Azure, które dystrybuują ruch internetowy do maszyn wirtualnych i zapewniają wysoką dostępność. Omawiane kwestie:


> [!div class="checklist"]
> * Tworzenie równoważenia obciążenia platformy Azure
> * Tworzenie zasobów modułu równoważenia obciążenia
> * Tworzenie maszyn wirtualnych i instalowanie serwera usług IIS
> * Wyświetlanie modułu równoważenia obciążenia w akcji
> * Dodawanie i usuwanie maszyn wirtualnych z modułu równoważenia obciążenia

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem. 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Tworzenie usługi Load Balancer w warstwie Standardowa

W tej sekcji utworzysz standardowy moduł równoważenia obciążenia, który pomaga równoważyć obciążenie maszyn wirtualnych. Usługa Load Balancer w warstwie Standardowa obsługuje tylko publiczny adres IP w warstwie Standardowa. Podczas tworzenia usługi Load Balancer w warstwie Standardowa musisz także utworzyć nowy publiczny adres IP w warstwie Standardowa, który jest skonfigurowany jako fronton (domyślnie o nazwie *LoadBalancerFrontend*) dla usługi Load Balancer w warstwie Standardowa. 

1. W lewym górnym rogu ekranu kliknij pozycję **Utwórz zasobowy** > **Moduł równoważenia obciążenia****sieciowego** > .
2. Na karcie **Podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przeglądanie + tworzenie**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz **pozycję Utwórz nowy** i wpisz *myResourceGroupSLB* w polu tekstowym.|
    | Nazwa                   | *myLoadBalancer*                                   |
    | Region         | Wybierz pozycję **Europa Zachodnia**.                                        |
    | Typ          | wybierz pozycję **Publiczny**.                                        |
    | SKU           | Wybierz **opcję Standardowy**.                          |
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. |
    | Nazwa publicznego adresu IP              | Wpisz *myPublicIP* w polu tekstowym.   |
    |Strefa dostępności| Wybierz pozycję **Strefowo nadmiarowy**.    |

3. Na karcie **Przeglądanie + tworzenie** kliknij pozycję **Utwórz**.

   ![Tworzenie usługi Load Balancer w warstwie Standardowa](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Tworzenie zasobów modułu równoważenia obciążenia

W tej sekcji można skonfigurować ustawienia modułu równoważenia obciążenia dla puli adresów wewnętrznej bazy danych, sondy kondycji i określić regułę balansu.

### <a name="create-a-backend-address-pool"></a>Tworzenie puli adresów zaplecza

Aby rozpowszechniać ruch do maszyn wirtualnych, pula adresów wewnętrznej bazy danych zawiera adresy IP wirtualnych (KART SIECIOWYCH) połączonych z modułem równoważenia obciążenia. Utwórz pulę adresów wewnętrznej bazy danych *myBackendPool,* aby uwzględnić maszyny wirtualne do równoważenia obciążenia ruchu internetowego.

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie kliknij **pozycję myLoadBalancer** z listy zasobów.
2. W obszarze **Ustawienia** kliknij pozycję **Pule zaplecza**, a następnie kliknij pozycję **Dodaj**.
3. Na stronie **Dodawanie puli wewnętrznej bazy danych** dla nazwy wpisz *myBackendPool*, jako nazwę puli wewnętrznej bazy danych, a następnie wybierz pozycję **Dodaj**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Aby umożliwić modułowi równoważenia obciążenia monitorowanie stanu aplikacji, należy użyć sondy kondycji. Sonda kondycji dynamicznie dodaje lub usuwa maszyny wirtualne z obrotu modułu równoważenia obciążenia na podstawie ich odpowiedzi na testy kondycji. Utwórz sondę kondycji *myHealthProbe*, aby monitorować kondycję maszyn wirtualnych.

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie kliknij **pozycję myLoadBalancer** z listy zasobów.
2. W obszarze **Ustawienia** kliknij pozycję **Sondy kondycji**, a następnie kliknij pozycję **Dodaj**.
3. Utwórz sondę kondycji, używając następujących wartości:
     
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *myHealthProbe*. |
    | Protocol (Protokół) | Wybierz **HTTP**. |
    | Port | Wprowadź *80*.|
    | Interval | Wprowadź *15* dla liczby **interwałów** w sekundach między próbami sondy. |
    | Niezdrowy próg | Wybierz *2* dla liczby progu w złej kondycji lub kolejnych błędów **sondy,** które muszą wystąpić, zanim maszyna wirtualna jest uważana za złą w złej kondycji.|
    
4. Kliknij przycisk **OK**.

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę równoważenia obciążenia *myLoadBalancerRuleWeb* do nasłuchiwania portu 80 w *frontend FrontendLoadBalancer* i wysyłania ruchu sieciowego z równoważeniem obciążenia do puli adresów wewnętrznej *myBackEndPool* również przy użyciu portu 80.

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie kliknij **pozycję myLoadBalancer** z listy zasobów.
2. W obszarze **Ustawienia** kliknij pozycję **Reguły równoważenia obciążenia**, a następnie kliknij pozycję **Dodaj**.
3. Użyj tych wartości, aby skonfigurować regułę równoważenia obciążenia:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *mójHTTPRule*. |
    | Protocol (Protokół) | wybierz pozycję **TCP**. |
    | Port | Wprowadź *80*.|
    | Port zaplecza | Wprowadź *80*. |
    | Pula zaplecza | Wybierz *myBackendPool*.|
    | Sonda kondycji | Wybierz *myHealthProbe*. |
    
4. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **OK**.

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji utworzysz sieć wirtualną, utworzysz trzy maszyny wirtualne dla puli wewnętrznej bazy danych modułu równoważenia obciążenia, a następnie zainstalujesz usługi IIS na maszynach wirtualnych, aby przetestować moduł równoważenia obciążenia.

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji należy zastąpić następujące parametry w krokach poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<>nazwa grupy zasobów**  | myResourceGroupSLB (Wybierz istniejącą grupę zasobów) |
| **\<>nazwa sieci wirtualnej** | myVNet ( myVNet )          |
| **\<nazwa regionu>**          | Europa Zachodnia      |
| **\<>przestrzeni adresowej IPv4**   | 10.1.0.0/16          |
| **\<>nazwa podsieci**          | mySubnet        |
| **\<>zakresu adresu podsieci** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Standardowy moduł równoważenia obciążenia obsługuje tylko maszyny wirtualne ze standardowymi adresami IP w puli wewnętrznej bazy danych. W tej sekcji utworzysz trzy maszyny wirtualne *(myVM1*, *myVM2*i *myVM3)* ze standardowym publicznym adresem IP w trzech różnych strefach (*Strefa 1,* *Strefa 2*i *Strefa 3),* które są dodawane do puli zaplecza standardowego modułu równoważenia obciążenia utworzonego wcześniej.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz źródło zasobów** > **Obliczanie** > **centrum danych systemu Windows Server 2016**. 
   
1. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:
   - **Grupa** > **zasobów**subskrypcji: Wybierz **myResourceGroupSLB**.
   - **Szczegóły** > wystąpienia**Nazwa maszyny wirtualnej:** Wpisz *myVM1*.
   - **Region Szczegółów** > **Region** wystąpienia > wybierz pozycję **Europa Zachodnia**.
   - **Opcje** > **dostępności** szczegółów wystąpienia > wybierz **strefy dostępności**. 
   - **Strefa** > **dostępności** szczegółów wystąpienia > Wybierz **1**.
  
1. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**. 
   
   - Upewnij się, że zostały wybrane następujące opcje:
       - **Sieć wirtualna**: **myVnet**
       - **Podsieć**: **myBackendSubnet**
       - **Publiczne** > IP wybierz pozycję **Utwórz nowy**, a następnie w oknie **Tworzenie publicznego adresu IP** dla **jednostki SKU**, wybierz pozycję **Standardowa**i **Strefa dostępności**wybierz pozycję **Strefa nadmiarowa**
      
   - Aby utworzyć nową sieciową grupę zabezpieczeń (NSG, network security group), czyli pewien typ zapory, w obszarze **Sieciowa grupa zabezpieczeń** wybierz pozycję **Zaawansowane**. 
       1. W polu **Konfigurowanie sieciowej grupy zabezpieczeń** wybierz pozycję **Utwórz nową**. 
       1. Wpisz *myNetworkSecurityGroup*i wybierz **przycisk OK**.

   - Aby maszyna wirtualna była częścią puli wewnętrznej bazy danych modułu równoważenia obciążenia, wykonaj następujące kroki:
        - W **obszarze Równoważenie obciążenia**, dla Umieść tę **maszynę wirtualną za istniejącym rozwiązaniem równoważenia obciążenia?** **Yes**
        - W **obszarze Ustawienia równoważenia obciążenia**dla opcji **równoważenia obciążenia**wybierz pozycję Azure load **balancer**.
        - Dla **Wybierz moduł równoważenia obciążenia**, *myLoadBalancer*. 
1. Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej** > **Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**. 
1. Wybierz pozycję **Przegląd + utwórz**.   
1. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.
1. Wykonaj kroki, aby utworzyć dwie dodatkowe maszyny wirtualne - *myVM2* i *myVM3*, ze standardowym publicznym adresem IP jednostki SKU w **strefie dostępności** **2** i **3,** a wszystkie inne ustawienia są takie same jak *myVM1*.  

### <a name="create-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

W tej sekcji utworzysz regułę sieciowej grupy zabezpieczeń, aby zezwolić na połączenia przychodzące przy użyciu protokołu HTTP.

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie z listy zasobów kliknij **pozycję myNetworkSecurityGroup** znajdującą się w grupie zasobów **myResourceGroupSLB.**
2. W obszarze **Ustawienia** kliknij pozycję **Reguły zabezpieczeń dla ruchu przychodzącego**, a następnie kliknij przycisk **Dodaj**.
3. Wprowadź następujące wartości dla reguły zabezpieczeń dla ruchu przychodzącego o nazwie *myHTTPRule*, aby umożliwić obsługę przychodzących połączeń HTTP przy użyciu portu 80:
    - *Tag usługi* — w polu **Źródło**.
    - *Internet* — w polu **Tag usługi źródłowej**
    - *80* — w polu **Docelowe zakresy portów**
    - *TCP* — w polu **Protokół**
    - *Zezwalaj* — w polu **Akcja**
    - *100* — w polu **Priorytet**
    - *mójHTTPRule dla* nazwy
    - *Zezwalaj na HTTP* —jako opis
4. Wybierz pozycję **Dodaj**.

### <a name="install-iis-on-vms"></a>Instalowanie usług IIS na maszynach wirtualnych

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie z listy zasobów kliknij **pozycję myVM1** znajdującą się w grupie zasobów *myResourceGroupSLB.*
2. Na stronie **Przegląd** kliknij pozycję **Połącz** dla protokołu RDP z maszyną wirtualną.
3. W wyskakującym okienku **Podłączanie do maszyny wirtualnej** wybierz pozycję **Pobierz plik RDP**, a następnie otwórz pobrany plik RDP.
4. W oknie **Podłączanie pulpitu zdalnego** kliknij pozycję **Połącz**.
5. Zaloguj się do maszyny wirtualnej przy użyciu poświadczeń podanych podczas jej tworzenia. Spowoduje to uruchomienie sesji pulpitu zdalnego z maszyną wirtualną — *myVM1*.
6. Na pulpicie serwera przejdź do pozycji **Narzędzia administracyjne**>systemu**Windows Windows PowerShell**.
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
1. Znajdź publiczny adres IP modułu równoważenia obciążenia na **ekranie Przegląd.** Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie kliknij **pozycję mójEipIP**.

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

      ![Internetowy serwer usług IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Aby wyświetlić moduł równoważenia obciążenia rozprowadzać ruch na trzech maszynach wirtualnych z uruchomieniem aplikacji, można wymusić odświeżenie przeglądarki sieci Web.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Usuwanie lub dodawanie maszyn wirtualnych w puli zaplecza
Maszyny wirtualne, na których działa aplikacja, mogą wymagać przeprowadzenia konserwacji, na przykład zainstalowania aktualizacji systemu operacyjnego. Zwiększony ruch do Twojej aplikacji może wiązać się z koniecznością dodania większej liczby maszyn wirtualnych. W tej sekcji pokazano, jak usunąć lub dodać maszynę wirtualną *(myVM1)* z modułu równoważenia obciążenia.

### <a name="remove-vm-from-a-backend-pool"></a>Usuwanie maszyny wirtualnej z puli wewnętrznej bazy danych
Aby usunąć *myVM1* z puli wewnętrznej bazy danych, wykonaj następujące czynności:

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie kliknij **pozycję myLoadBalancer** z listy zasobów.
2. W obszarze **Ustawienia** kliknij pozycję **Pule zaplecza**, a następnie na liście pul zaplecza kliknij pozycję **myBackendPool**.
3. Na stronie **myBackendPool,** aby usunąć *VM1,* wybierz ikonę usuwania na końcu wiersza, która wyświetla *myVM1*, a następnie kliknij przycisk **Zapisz**.

Po usunięciu maszyny *myVM1* z puli adresów zaplecza możesz wykonywać wszelkie zadania konserwacyjne na maszynie *myVM1*, takie jak instalowanie aktualizacji oprogramowania. W przypadku braku *VM1*obciążenie jest teraz zrównoważone w *myVM2* i *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Dodawanie maszyny wirtualnej do puli wewnętrznej bazy danych
Aby dodać *myVM1* z powrotem do puli wewnętrznej bazy danych, wykonaj następujące kroki:

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie wybierz **myVM1** z listy zasobów.
2. Na stronie **VM1** w obszarze **Ustawienia**wybierz pozycję **Sieć**.
3. Na stronie **Sieć** wybierz kartę **Równoważenie obciążenia,** a następnie wybierz pozycję **Dodaj równoważenie obciążenia**.
4. Na stronie **Dodawanie równoważenia obciążenia** wykonaj następujące czynności:
   1. W przypadku **opcji równoważenia obciążenia**wybierz pozycję **Azure load balancer**.
   2. W obszarze **Wybierz moduł równoważenia obciążenia**wybierz *myLoadBalancer*.
   3. W obszarze **Wybierz pulę zaplecza**wybierz *myBackendPool*. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, usuń grupę zasobów, moduł równoważenia obciążenia i wszystkie powiązane zasoby. Aby to zrobić, wybierz grupę zasobów *myResouceGroupSLB* zawierającą moduł równoważenia obciążenia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono standardowy moduł równoważenia obciążenia, dołączone maszyny wirtualne do niego, skonfigurowano regułę ruchu modułu równoważenia obciążenia, sondę kondycji, a następnie przetestowano moduł równoważenia obciążenia. Ponadto usunięto maszynę wirtualną z zestawu o zrównoważonym obciążeniu i dodano ją z powrotem do puli adresów zaplecza. Aby dowiedzieć się więcej na temat usługi Azure Load Balancer, przejdź do samouczków dotyczących usługi Azure Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
