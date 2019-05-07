---
title: 'Szybki start: tworzenie usługi Load Balancer w warstwie Standardowa — Azure Portal'
titlesuffix: Azure Load Balancer
description: Ten przewodnik Szybki Start przedstawiono tworzenie standardowego modułu równoważenia obciążenia przy użyciu witryny Azure portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 79ba86fd32248da240706fda2d8b5fcf8323263d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143200"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Szybki start: Tworzenie usługi Load Balancer w warstwie Standardowa przy użyciu witryny Azure Portal w celu równoważenia obciążenia maszyn wirtualnych

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. Za pomocą witryny Azure Portal można utworzyć moduł równoważenia obciążenia na potrzeby równoważenia obciążenia maszyn wirtualnych. W tym przewodniku Szybki start pokazano, jak zrównoważyć obciążenie maszyn wirtualnych przy użyciu usługi Load Balancer w warstwie Standardowa.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Tworzenie usługi Load Balancer w warstwie Standardowa

W tej sekcji opisano tworzenie standardowego modułu równoważenia obciążenia, który pomaga równoważyć obciążenie maszyn wirtualnych. Usługa Load Balancer w warstwie Standardowa obsługuje tylko publiczny adres IP w warstwie Standardowa. Podczas tworzenia usługi Load Balancer w warstwie Standardowa musisz także utworzyć nowy publiczny adres IP w warstwie Standardowa, który jest skonfigurowany jako fronton (domyślnie o nazwie *LoadBalancerFrontend*) dla usługi Load Balancer w warstwie Standardowa. 

1. W lewym górnym rogu ekranu wybierz **Utwórz zasób** > **sieć** > **modułu równoważenia obciążenia**.
2. Na karcie **Podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przeglądanie + tworzenie**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz **Utwórz nową** i typ *myResourceGroupSLB* w polu tekstowym.|
    | Name (Nazwa)                   | *myLoadBalancer*                                   |
    | Region         | Wybierz pozycję **Europa Zachodnia**.                                        |
    | Type          | Wybierz pozycję **Publiczna**.                                        |
    | SKU           | Wybierz opcję **Standardowa**.                          |
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. |
    | Nazwa publicznego adresu IP              | Wpisz *myPublicIP* w polu tekstowym.   |
    |Strefa dostępności| Wybierz pozycję **Strefowo nadmiarowy**.    |
3. W **przeglądu + Utwórz** zaznacz **Utwórz**.   

    ![Tworzenie usługi Load Balancer w warstwie Standardowa](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Tworzenie zasobów modułu równoważenia obciążenia

W tej sekcji należy skonfigurować ustawienia usługi równoważenia obciążenia dla puli adresów zaplecza, sondę kondycji i określić reguły modułu równoważenia.

### <a name="create-a-backend-address-pool"></a>Tworzenie puli adresów zaplecza

Kierowanie ruchu do maszyn wirtualnych, adres IP zawiera pulę adresów zaplecza adresów wirtualnych kart sieciowych połączonych z modułem równoważenia obciążenia. Tworzenie puli adresów zaplecza *myBackendPool* obejmujący maszyny wirtualne na potrzeby równoważenia obciążenia ruchu internetowego.

1. Wybierz **wszystkich usług** w menu po lewej stronie wybierz **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** na liście zasobów.
2. W obszarze **ustawienia**, wybierz opcję **pule zaplecza**, a następnie wybierz **Dodaj**.
3. Na **Dodawanie puli zaplecza** strony dla nazwy, typu *myBackendPool*, jako nazwę puli zaplecza, a następnie wybierz **Dodaj**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Aby zezwolić na moduł równoważenia obciążenia monitorować stan aplikacji, należy użyć sondy kondycji. Sonda kondycji dynamicznie dodaje lub usuwa maszyny wirtualne w rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. Utwórz sondę kondycji *myHealthProbe*, aby monitorować kondycję maszyn wirtualnych.

1. Wybierz **wszystkich usług** w menu po lewej stronie wybierz **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** na liście zasobów.
2. W obszarze **ustawienia**, wybierz opcję **sondy kondycji**, a następnie wybierz **Dodaj**.
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Name (Nazwa) | Wprowadź *myHealthProbe*. |
    | Protokół | Wybierz **HTTP**. |
    | Port | Wprowadź *80*.|
    | Interval | Wprowadź *15* liczbę **interwał** w ciągu kilku sekund między próbami sondy. |
    | Próg złej kondycji | Wybierz **2** liczbę **próg złej kondycji** lub kolejnych niepowodzeń sondy musi wystąpić, zanim maszyny Wirtualnej jest uznawana za złą.|
    | | |
4. Kliknij przycisk **OK**.

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia
Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę modułu równoważenia obciążenia *myLoadBalancerRuleWeb* w celu nasłuchiwania na porcie 80 w frontonie *Myfrontendpool* i wysyłania ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza *myBackEndPool* również przy użyciu portu 80. 

1. Wybierz **wszystkich usług** w menu po lewej stronie wybierz **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** na liście zasobów.
2. W obszarze **ustawienia**, wybierz opcję **reguły równoważenia obciążenia**, a następnie wybierz **Dodaj**.
3. Skonfiguruj regułę równoważenia obciążenia, używając następujących wartości:
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Name (Nazwa) | Wprowadź *myHTTPRule*. |
    | Protokół | wybierz pozycję **TCP**. |
    | Port | Wprowadź *80*.|
    | Port zaplecza | Wprowadź *80*. |
    | Pula zaplecza | Select *myBackendPool*.|
    | Sonda kondycji | Wybierz *myHealthProbe*. |
4. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **OK**.


## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji Utwórz sieć wirtualną, utworzyć trzy maszyny wirtualne dla puli zaplecza modułu równoważenia obciążenia i następnie zainstalujesz usługi IIS na maszynach wirtualnych, aby ułatwić testowanie modułu równoważenia obciążenia.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**.

1. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Name (Nazwa) | Wprowadź nazwę *myVNet*. |
    | Przestrzeń adresowa | Wprowadź adres *10.1.0.0/16*. |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz istniejący zasób - *myResourceGroupSLB*. |
    | Lokalizacja | Wybierz pozycję **Europa Zachodnia**.|
    | Podsieć — nazwa | Wprowadź nazwę podsieci *myBackendSubnet*. |
    | Zakres adresów podsieci: 10.41.0.0/24 | Wprowadź *10.1.0.0/24*. |
1. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **Utwórz**.

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych
Load Balancer w warstwie standardowa obsługuje tylko maszyny wirtualne z adresami IP w warstwie standardowa w puli zaplecza. W tej sekcji utworzysz trzy maszyny wirtualne (*myVM1*, *myVM2* i *myVM3*) przy użyciu standardowego publicznego adresu IP w trzech różnych stref (*strefa 1*, *Strefa 2*, i *strefa 3*) nowszym dodawane do puli zaplecza standardowego modułu równoważenia obciążenia, który został utworzony wcześniej.

1. W lewym górnym rogu portalu wybierz **Utwórz zasób** > **obliczenia** > **systemie Windows Datacenter 2019**. 
   
1. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:
   - **Subskrypcja** > **Grupa zasobów**: Select **myResourceGroupSLB**.
   - **Szczegóły wystąpienia** > **Nazwa maszyny wirtualnej**: Typ *myVM1*.
   - **Szczegóły wystąpienia** > **Region** > Wybierz **Europa Zachodnia**.
   - **Szczegóły wystąpienia** > **opcji dostępności** > Wybierz **strefy dostępności**. 
   - **Szczegóły wystąpienia** > **strefy dostępności** > Wybierz **1**.
   - **Konto administratora**> wprowadź **Username**, **hasło** i **Potwierdź hasło** informacji.
   - Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
1. W **sieć** karcie upewnij się, że wybrane są następujące:
   - **Sieć wirtualna**: *myVnet*
   - **Podsieci**: *myBackendSubnet*
   - **Publiczny adres IP** > Wybierz **Utwórz nową**i w **tworzenie publicznego adresu IP** oknie dla **jednostki SKU**, wybierz opcję **standardowa**, i **strefy dostępności**, wybierz opcję **strefowo nadmiarowe**, a następnie wybierz pozycję **OK**.
   - Aby utworzyć nową sieciową grupę zabezpieczeń (NSG, network security group), czyli pewien typ zapory, w obszarze **Sieciowa grupa zabezpieczeń** wybierz pozycję **Zaawansowane**. 
       1. W polu **Konfigurowanie sieciowej grupy zabezpieczeń** wybierz pozycję **Utwórz nową**. 
       1. Typ *myNetworkSecurityGroup*i wybierz **OK**.
   - Aby utworzyć maszynę Wirtualną częścią puli zaplecza modułu równoważenia obciążenia, wykonaj następujące czynności:
        - W **Równoważenie obciążenia**, aby uzyskać **umieścić tej maszyny wirtualnej za modułem istniejące rozwiązanie do równoważenia obciążenia?**, wybierz opcję **tak**.
        - W **ustawienia równoważenia obciążenia**, aby uzyskać **opcje równoważenia obciążenia**, wybierz opcję **usługa Azure load balancer**.
        - Aby uzyskać **wybrać moduł równoważenia obciążenia**, *myLoadBalancer*.
        - Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej** > **Zarządzanie**.
2. W **zarządzania** , w obszarze **monitorowanie**ustaw **diagnostykę rozruchu** do **poza**. 
1. Wybierz pozycję **Przegląd + utwórz**.   
1. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.
1. Wykonaj kroki 2 do 6, aby utworzyć dwie dodatkowe maszyny wirtualne z następujących wartości i inne ustawienia, takie same jak *myVM1*:

    | Ustawienie | VM 2| VM 3|
    | ------- | ----- |---|
    | Name (Nazwa) |  *myVM2* |*myVM3*|
    | Strefa dostępności | 2 |3|
    |Publiczny adres IP| **Standardowa** jednostki SKU|**Standardowa** jednostki SKU|
    | Publiczny adres IP — strefa dostępności| **Strefowo nadmiarowe** |**Strefowo nadmiarowe**|
    | Sieciowa grupa zabezpieczeń | Wybierz istniejący *myNetworkSecurity grupy*| Wybierz istniejący *myNetworkSecurity grupy*|

 ### <a name="create-nsg-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

W tej sekcji utworzysz reguły sieciowej grupy zabezpieczeń, aby zezwolić na połączenia przychodzące za pośrednictwem protokołu HTTP.

1. Wybierz **wszystkich usług** w menu po lewej stronie wybierz **wszystkie zasoby**, a następnie z zasobów z listy wybierz **myNetworkSecurityGroup** znajdującą się w **myResourceGroupSLB** grupy zasobów.
2. W obszarze **Ustawienia** wybierz pozycję **Reguły zabezpieczeń dla ruchu przychodzącego**, a następnie wybierz pozycję **Dodaj**.
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
 
### <a name="install-iis"></a>Instalowanie usług IIS

1. Wybierz **wszystkich usług** w menu po lewej stronie wybierz **wszystkie zasoby**, a następnie na liście zasobów wybierz **myVM1** znajdującą się w  *myResourceGroupSLB* grupy zasobów.
2. Na stronie **Przegląd** wybierz pozycję **Połącz** dla protokołu RDP z maszyną wirtualną.
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
1. Znajdź publiczny adres IP dla usługi Load Balancer na ekranie **Przegląd**. Wybierz **wszystkich usług** w menu po lewej stronie wybierz **wszystkie zasoby**, a następnie wybierz pozycję **myPublicIP**.

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

   ![Internetowy serwer usług IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Aby zobaczyć moduł równoważenia obciążenia rozdziela ruch między wszystkie trzy maszyny wirtualne, można dostosować domyślną stronę serwera sieci Web usług IIS każdej maszyny Wirtualnej i następnie wymusić odświeżenie w przeglądarce sieci web z komputera klienckiego.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów, moduł równoważenia obciążenia i wszystkich powiązanych zasobów. Aby to zrobić, wybierz grupę zasobów (*myResourceGroupSLB*), zawierający modułu równoważenia obciążenia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start utworzono standardowego modułu równoważenia obciążenia, dołączono maszyny wirtualne do niego, skonfigurowane reguły modułu równoważenia obciążenia ruchu, sondy kondycji i następnie przetestowano moduł równoważenia obciążenia. Aby dowiedzieć się więcej na temat usługi Azure Load Balancer, przejdź do samouczków dotyczących usługi Azure Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
