---
title: Szybki start:Tworzenie publicznego modułu równoważenia obciążenia — witryna Azure portal
titleSuffix: Azure Load Balancer
description: Ten przewodnik Szybki start pokazuje, jak utworzyć moduł równoważenia obciążenia przy użyciu witryny Azure portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: b424fe315737b84479283eed2d77398c8ce4f148
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78898833"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Szybki start: tworzenie modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu witryny Azure portal

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. Za pomocą witryny Azure Portal można utworzyć moduł równoważenia obciążenia na potrzeby równoważenia obciążenia maszyn wirtualnych. Ten przewodnik Szybki start pokazuje, jak równoważyć maszyny wirtualne przy użyciu publicznego modułu równoważenia obciążenia.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem. 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

W tej sekcji utworzysz moduł równoważenia obciążenia, który pomaga równoważyć obciążenie maszyn wirtualnych. Można utworzyć publiczny moduł równoważenia obciążenia lub wewnętrzny moduł równoważenia obciążenia. Podczas tworzenia publicznego modułu równoważenia obciążenia należy również utworzyć nowy publiczny adres IP, który jest skonfigurowany jako frontend (domyślnie nazwany *LoadBalancerFrontend)* dla modułu równoważenia obciążenia.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasobnik** > **Równoważenia obciążenia****sieciowego** > .
2. Na karcie **Podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przeglądanie + tworzenie**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz **pozycję Utwórz nowy** i wpisz *myResourceGroupSLB* w polu tekstowym.|
    | Nazwa                   | *myLoadBalancer*                                   |
    | Region         | Wybierz pozycję **Europa Zachodnia**.                                        |
    | Typ          | wybierz pozycję **Publiczny**.                                        |
    | SKU           | Wybierz **opcję Standardowy** lub **Podstawowy**. Firma Microsoft zaleca standard dla obciążeń produkcyjnych. |
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. Jeśli masz istniejący publiczny adres IP, którego chcesz użyć, wybierz **pozycję Użyj istniejących** |
    | Nazwa publicznego adresu IP              | Wpisz *myPublicIP* w polu tekstowym.   Służy ```-SKU Basic``` do tworzenia podstawowego publicznego adresu IP. Podstawowe publiczne wiadomości IP nie są zgodne ze **standardowym modułem** równoważenia obciążenia. Firma Microsoft zaleca stosowanie **standardu** dla obciążeń produkcyjnych.|
    | Strefa dostępności | Wpisz *strefę nadmiarową,* aby utworzyć odporny moduł równoważenia obciążenia. Aby utworzyć strefowy moduł równoważenia obciążenia, wybierz określoną strefę z 1, 2 lub 3 |

> [!IMPORTANT]
> Pozostała część tego przewodnika Szybki start zakłada, że **standardowa** jednostka SKU jest wybierana podczas procesu wyboru jednostki SKU powyżej.


3. Na karcie **Recenzja + utwórz** wybierz pozycję **Utwórz**.   

    ![Tworzenie usługi Load Balancer w warstwie Standardowa](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Tworzenie zasobów modułu równoważenia obciążenia

W tej sekcji można skonfigurować ustawienia modułu równoważenia obciążenia dla puli adresów wewnętrznej bazy danych, sondy kondycji i określić regułę balansu.

### <a name="create-a-backend-pool"></a>Tworzenie puli wewnętrznej bazy danych

Aby rozpowszechniać ruch do maszyn wirtualnych, pula adresów wewnętrznej bazy danych zawiera adresy IP wirtualnych (KART SIECIOWYCH) połączonych z modułem równoważenia obciążenia. Utwórz pulę adresów wewnętrznej bazy danych *myBackendPool,* aby uwzględnić maszyny wirtualne do równoważenia obciążenia ruchu internetowego.

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie wybierz **myLoadBalancer** z listy zasobów.
2. W obszarze **Ustawienia**wybierz pozycję **Pule wewnętrznej bazy,** a następnie wybierz pozycję **Dodaj**.
3. Na stronie **Dodawanie puli wewnętrznej bazy danych** dla nazwy wpisz *myBackendPool*, jako nazwę puli wewnętrznej bazy danych, a następnie wybierz pozycję **Dodaj**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Aby umożliwić modułowi równoważenia obciążenia monitorowanie stanu aplikacji, należy użyć sondy kondycji. Sonda kondycji dynamicznie dodaje lub usuwa maszyny wirtualne z obrotu modułu równoważenia obciążenia na podstawie ich odpowiedzi na testy kondycji. Utwórz sondę kondycji *myHealthProbe*, aby monitorować kondycję maszyn wirtualnych.

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie wybierz **myLoadBalancer** z listy zasobów.
2. W obszarze **Ustawienia**wybierz pozycję **Sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *myHealthProbe*. |
    | Protocol (Protokół) | Wybierz **HTTP**. |
    | Port | Wprowadź *80*.|
    | Interval | Wprowadź *15* dla liczby **interwałów** w sekundach między próbami sondy. |
    | Niezdrowy próg | Wybierz **2** dla liczby progu w złej kondycji lub kolejnych błędów **sondy,** które muszą wystąpić, zanim maszyna wirtualna jest uważana za złą w złej kondycji.|
    | | |
4. Kliknij przycisk **OK**.

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia
Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę równoważenia obciążenia *myLoadBalancerRuleWeb* do nasłuchiwania portu 80 w *frontend FrontendLoadBalancer* i wysyłania ruchu sieciowego z równoważeniem obciążenia do puli adresów wewnętrznej *myBackEndPool* również przy użyciu portu 80. 

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie wybierz **myLoadBalancer** z listy zasobów.
2. W obszarze **Ustawienia**wybierz pozycję **Reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.
3. Skonfiguruj regułę równoważenia obciążenia, używając następujących wartości:
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *mójHTTPRule*. |
    | Protocol (Protokół) | wybierz pozycję **TCP**. |
    | Port | Wprowadź *80*.|
    | Port zaplecza | Wprowadź *80*. |
    | Pula zaplecza | Wybierz *myBackendPool*.|
    | Sonda kondycji | Wybierz *myHealthProbe*. |
4. Pozostaw resztę wartości domyślnych, a następnie wybierz **przycisk OK**.


## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji utworzysz sieć wirtualną, utworzysz trzy maszyny wirtualne dla puli wewnętrznej bazy danych modułu równoważenia obciążenia, a następnie zainstalujesz usługi IIS na maszynach wirtualnych, aby przetestować moduł równoważenia obciążenia.

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji należy zastąpić następujące parametry w krokach poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<>nazwa grupy zasobów**  | myResourceGroupSLB |
| **\<>nazwa sieci wirtualnej** | myVNet ( myVNet )          |
| **\<nazwa regionu>**          | Europa Zachodnia      |
| **\<>przestrzeni adresowej IPv4**   | 10.1.0.0\16          |
| **\<>nazwa podsieci**          | myBackendSubnet        |
| **\<>zakresu adresu podsieci** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych
Publicznego jednostek SKU IP i jednostek SKU równoważenia obciążenia muszą być zgodne. W przypadku standardowego modułu równoważenia obciążenia należy używać maszyn wirtualnych ze standardowymi adresami IP w puli wewnętrznej bazy danych. W tej sekcji utworzysz trzy maszyny wirtualne *(myVM1*, *myVM2* i *myVM3)* ze standardowym publicznym adresem IP w trzech różnych strefach *(Strefa 1,* *Strefa 2*i *Strefa 3),* które są później dodawane do puli zaplecza modułu równoważenia obciążenia utworzonego wcześniej. Jeśli wybrano opcję Podstawowe, użyj maszyn wirtualnych z podstawowymi adresami IP.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz źródło zasobów** > **Obliczanie** > **centrum danych systemu Windows Server 2019**. 
   
1. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:
   - **Grupa** > **zasobów**subskrypcji: Wybierz **myResourceGroupSLB**.
   - **Szczegóły** > wystąpienia**Nazwa maszyny wirtualnej:** Wpisz *myVM1*.
   - **Region Szczegółów** > **Region** wystąpienia > wybierz pozycję **Europa Zachodnia**.
   - **Opcje** > **dostępności** szczegółów wystąpienia > wybierz **strefy dostępności**. 
   - **Strefa** > **dostępności** szczegółów wystąpienia > Wybierz **1**.
   - **Konto administratora**> Wprowadź **informacje o nazwie użytkownika,** **haśle** i **potwierdzenie hasła.**
   - Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
1. Na karcie **Sieć** upewnij się, że zaznaczono następujące elementy:
   - **Sieć wirtualna**: *myVnet*
   - **Podsieć**: *myBackendSubnet*
   - **Publiczny adres IP** > wybierz pozycję **Utwórz nowy**, a następnie w oknie **Utwórz publiczny adres IP** dla **jednostki SKU**wybierz pozycję **Standardowa**i **Strefa dostępności**wybierz pozycję **Strefa nadmiarowa**, a następnie **wybierz**ok . Jeśli utworzono podstawowy moduł równoważenia obciążenia, wybierz opcję Podstawowe. Firma Microsoft zaleca używanie standardowej jednostki SKU dla obciążeń produkcyjnych.
   - Aby utworzyć nową sieciową grupę zabezpieczeń (NSG, network security group), czyli pewien typ zapory, w obszarze **Sieciowa grupa zabezpieczeń** wybierz pozycję **Zaawansowane**. 
       1. W polu **Konfigurowanie sieciowej grupy zabezpieczeń** wybierz pozycję **Utwórz nową**. 
       1. Wpisz *myNetworkSecurityGroup*i wybierz **przycisk OK**.
   - Aby maszyna wirtualna była częścią puli wewnętrznej bazy danych modułu równoważenia obciążenia, wykonaj następujące kroki:
        - W **obszarze Równoważenie obciążenia**, dla Umieść tę **maszynę wirtualną za istniejącym rozwiązaniem równoważenia obciążenia?** **Yes**
        - W **obszarze Ustawienia równoważenia obciążenia**dla opcji **równoważenia obciążenia**wybierz pozycję Azure load **balancer**.
        - Dla **Wybierz moduł równoważenia obciążenia**, *myLoadBalancer*.
        - Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej** > **Zarządzanie**.
2. Na karcie **Zarządzanie** w obszarze **Monitorowanie**ustaw **diagnostykę rozruchu** na **Wyłącz**. 
1. Wybierz pozycję **Przegląd + utwórz**.   
1. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.
1. Wykonaj kroki od 2 do 6, aby utworzyć dwie dodatkowe maszyny wirtualne z następującymi wartościami i wszystkimi innymi ustawieniami tak samo jak *myVM1:*

    | Ustawienie | MW 2| Maszyna wirtualna 3|
    | ------- | ----- |---|
    | Nazwa |  *myVM2* |*myVM3*|
    | Strefa dostępności | 2 |3|
    |Publiczny adres IP| **Standard** Numer jednostki magazynowej|**Standard** Numer jednostki magazynowej|
    | Publiczny adres IP — strefa dostępności| **Strefa nadmiarowa** |**Strefa nadmiarowa**|
    | Sieciowa grupa zabezpieczeń | Wybierz istniejącą *grupę myNetworkSecurity*| Wybierz istniejącą *grupę myNetworkSecurity*|

 ### <a name="create-nsg-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

W tej sekcji utworzysz regułę sieciowej grupy zabezpieczeń, aby zezwolić na połączenia przychodzące przy użyciu protokołu HTTP.

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie z listy zasobów wybierz **myNetworkSecurityGroup,** która znajduje się w grupie zasobów **myResourceGroupSLB.**
2. W obszarze **Ustawienia** wybierz pozycję **Reguły zabezpieczeń dla ruchu przychodzącego**, a następnie wybierz pozycję **Dodaj**.
3. Wprowadź następujące wartości dla reguły zabezpieczeń dla ruchu przychodzącego o nazwie *myHTTPRule*, aby umożliwić obsługę przychodzących połączeń HTTP przy użyciu portu 80:
    - **Źródło**: *Tag serwisu*
    -  **Tag usługi źródłowej**: *Internet*
    - **Zakresy portów docelowych:** *80*
    - **Protokół**: *TCP*
    - **Akcja**: *Zezwalaj*
    - **Priorytet**: *100*
    - **Nazwa**: *myHTTPRule* 
    - **Opis**: "*Zezwalaj na http* 
4. Wybierz pozycję **Dodaj**.
5. W razie potrzeby powtórz kroki dla przychodzącej reguły RDP, z następującymi różnymi wartościami:
   - **Zakresy portów docelowych**: wpisz *3389*.
   - **Priorytet**: wpisz *200*. 
   - **Nazwa**: wpisz *MyRDPRule*. 
   - **Opis**: wpisz *Zezwalaj na RDP*. 
 
### <a name="install-iis"></a>Instalowanie usług IIS

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie z listy zasobów wybierz **myVM1,** który znajduje się w grupie zasobów *myResourceGroupSLB.*
2. Na stronie **Przegląd** wybierz pozycję **Połącz** dla protokołu RDP z maszyną wirtualną.
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
1. Znajdź publiczny adres IP modułu równoważenia obciążenia na **ekranie Przegląd.** Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie wybierz **pozycję myPublicIP**.

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

   ![Internetowy serwer usług IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Aby wyświetlić, jak moduł równoważenia obciążenia rozprowadza ruch na wszystkich trzech maszynach wirtualnych, można dostosować domyślną stronę każdego serwera sieci Web usług IIS każdej maszyny wirtualnej, a następnie wymusić odświeżenie przeglądarki sieci Web z komputera klienckiego.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebna, usuń grupę zasobów, moduł równoważenia obciążenia i wszystkie powiązane zasoby. Aby to zrobić, wybierz grupę zasobów *(myResourceGroupSLB),* która zawiera moduł równoważenia obciążenia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono standardowy moduł równoważenia obciążenia, dołączono do niego maszyny wirtualne, skonfigurowano regułę ruchu modułu równoważenia obciążenia, sondę kondycji, a następnie przetestowano moduł równoważenia obciążenia. Aby dowiedzieć się więcej o modułie równoważenia obciążenia platformy Azure, przejdź do [samouczków dotyczący równoważenia obciążenia platformy Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Dowiedz się więcej o [strefach równoważenia obciążenia i dostępności](load-balancer-standard-availability-zones.md).
