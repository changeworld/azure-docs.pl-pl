---
title: Szybki Start — Tworzenie usługi linku prywatnego przy użyciu Azure Portal
titlesuffix: Azure Private Link
description: Dowiedz się, jak utworzyć usługę linku prywatnego przy użyciu Azure Portal w tym przewodniku Szybki Start
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 4dd27d3fb4321c2eecf222136376bfd4b49790bb
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027776"
---
# <a name="quickstart-create-a-private-link-service-using-the-azure-portal"></a>Szybki Start: Tworzenie usługi linku prywatnego przy użyciu Azure Portal

Usługa link prywatny platformy Azure to odwołanie do własnej usługi, która jest obsługiwana przez link prywatny platformy Azure. Usługa lub zasób działający za Azure Load Balancer można włączyć dla prywatnego dostępu do łącza. Konsumenci usługi mogą uzyskać dostęp do usługi prywatnie z własnych sieci wirtualnych. W tym przewodniku szybki start dowiesz się, jak utworzyć usługę linku prywatnego przy użyciu Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Tworzenie wewnętrznego modułu równoważenia obciążenia

Najpierw Utwórz sieć wirtualną, a następnie wewnętrzny moduł równoważenia obciążenia do użycia z usługą Azure Private link.

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz Virtual Network i podsieć służącą do hostowania modułu równoważenia obciążenia, który jest używany w celu uzyskania dostępu do prywatnej usługi link Service.


1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**.
   
1. W okienku **Tworzenie sieci wirtualnej** wpisz lub wybierz następujące wartości:
   
   - **Nazwa**: wpisz **MyVnet**.
   - **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wprowadź nazwę **MyResourceGroupLB** i wybierz przycisk **OK**. 
   - **Podsieć** > **Nazwa**: wpisz **MyBackendSubnet**.
   
1. Wybierz pozycję **Utwórz**.

   ![Tworzenie sieci wirtualnej](../load-balancer/media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-a-standard-load-balancer"></a>Tworzenie modułu równoważenia obciążenia w warstwie Standardowa

Tworzenie standardowego wewnętrznego modułu równoważenia obciążenia przy użyciu portalu. Utworzona przez Ciebie nazwa i adres IP są automatycznie konfigurowane jako fronton modułu równoważenia obciążenia.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób** > **Sieć** > **Moduł równoważenia obciążenia**.
   
2. Na karcie **Podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przeglądanie + tworzenie**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | W polu ściąganie wybierz pozycję *MyResourceGroupLB* .|
    | Nazwa                   | *myLoadBalancer*                                   |
    | Region         | Wybierz pozycję **East US 2** (Wschodnie stany USA 2).                                        |
    | Typ          | Wybierz pozycję **wewnętrzne**.                                        |
    | JSZ           | Wybierz pozycję **Standardowy**.                          |
    | Sieć wirtualna           | Wybierz wartość *MojaSiećWirtualna*.                          |    
    | Przypisanie adresu IP              | Wybierz wartość **Statyczny**.   |
    | Prywatny adres IP|Wpisz adres znajdujący się w przestrzeni adresowej sieci wirtualnej i podsieci, na przykład *10.3.0.7*.  |

3. Na karcie **Przeglądanie + tworzenie** kliknij pozycję **Utwórz**. 
   

### <a name="create-standard-load-balancer-resources"></a>Tworzenie zasobów usługi równoważenia obciążenia w warstwie Standardowa

W tej sekcji skonfigurujesz ustawienia modułu równoważenia obciążenia dla puli adresów zaplecza i sondy kondycji oraz określisz reguły modułu równoważenia obciążenia.

#### <a name="create-a-backend-pool"></a>Tworzenie puli zaplecza

Aby dystrybuować ruch do zasobów, Pula adresów zaplecza zawiera adresy IP wirtualnych (kart sieciowych) podłączonych do Load Balancer. Utwórz pulę adresów zaplecza *myBackendPool* , aby uwzględnić zasoby dla ruchu związanego z równoważeniem obciążenia.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.
2. W obszarze **Ustawienia**wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.
3. Na stronie **Dodawanie puli zaplecza** wpisz nazwę *myBackendPool*, jako nazwę puli zaplecza, a następnie wybierz pozycję **Dodaj**.



#### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Aby umożliwić usłudze równoważenia obciążenia monitorowanie stanu zasobów, należy użyć sondy kondycji. Sonda kondycji dynamicznie dodaje lub usuwa zasoby z rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. 

**Aby utworzyć sondę kondycji do monitorowania kondycji zasobów:**

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **MyLoadBalancer** na liście zasobów.
   
1. W obszarze **Ustawienia** wybierz pozycję **Sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
   
1. Na stronie **Dodawanie sondy kondycji** wpisz lub wybierz następujące wartości:
   
   - **Nazwa**: wpisz **MyHealthProbe**.
   - **Protokół**: Lista rozwijana i wybór **protokołu TCP**. 
   - **Port**: wpisz **80**. 
   - **Interwał**: wpisz **15**. Interwał to liczba sekund między próbami sondy.
   - **Próg złej kondycji**: wpisz **2**. Ta wartość to liczba kolejnych niepowodzeń sondy, które występują, zanim kondycja maszyny wirtualnej zostanie uznana za złą.
   
1. Kliknij przycisk **OK**.

#### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje sposób dystrybucji ruchu do zasobów. Reguła definiuje konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego, pulę adresów IP zaplecza do odbierania ruchu oraz wymagany port źródłowy i docelowy. 

Reguła modułu równoważenia obciążenia o nazwie **MyLoadBalancerRule** nasłuchuje na porcie 80 frontonu **LoadBalancerFrontEnd**. Ta reguła wysyła ruch sieciowy do puli adresów zaplecza **MyBackEndPool**, również przy użyciu portu 80. 

**Aby utworzyć regułę modułu równoważenia obciążenia:**

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **MyLoadBalancer** na liście zasobów.
   
1. W obszarze **Ustawienia**wybierz pozycję **reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.
   
1. Na stronie **Dodaj regułę równoważenia obciążenia** wpisz lub wybierz następujące wartości, jeśli jeszcze nie istnieją:
   
   - **Nazwa**: wpisz **MyLoadBalancerRule**.
   - **Adres IP frontonu:** wpisz **LoadBalancerFrontend**, jeśli ta wartość nie istnieje.
   - **Protokół**: wybierz pozycję **TCP**.
   - **Port**: wpisz **80**.
   - **Port zaplecza**: wpisz **80**.
   - **Pula zaplecza**: wybierz pozycję **MyBackendPool**.
   - **Sonda kondycji**: wybierz pozycję **MyHealthProbe**. 
   
1. Kliknij przycisk **OK**.
   
## <a name="create-a-private-link-service"></a>Tworzenie usługi linku prywatnego

W tej sekcji utworzysz usługę linku prywatnego za pomocą usługi równoważenia obciążenia w warstwie Standardowa.

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób** > **Networking** > **prywatnym centrum linków (wersja zapoznawcza)** . Możesz również przeszukiwać prywatne łącze za poorednictwem wyszukiwania w portalu.

2. W **centrum linków prywatnych — Omówienie**opcji **"Uwidacznianie własnej usługi", aby umożliwić innym użytkownikom nawiązywanie połączeń "** SELECT Start.

3. W obszarze **Tworzenie usługi link prywatny — podstawowe**wprowadź lub wybierz następujące informacje:

    | Ustawienie           | Wartość                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | **Szczegóły projektu:**  |                                                                              |
    | Subskrypcja      | Wybierz swoją subskrypcję                                                     |
    | Grupa zasobów    | Wybierz *MyResourceGroupLB*                                                     |
    | **SZCZEGÓŁY WYSTĄPIENIA:** |                                                                              |
    | Nazwa              | Wprowadź *myPrivateLinkService* |
    | Region            | Wybierz *Wschodnie stany USA 2*                                                         |

4. Wybierz pozycję **Dalej: ustawienia wychodzące**.

5. W obszarze **Tworzenie usługi link prywatny — ustawienia wychodzące**wprowadź lub wybierz następujące informacje:


    | Ustawienie                           | Wartość                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | Równoważenie obciążenia                     | Wybierz *MyLoadBalancer*                                                           |
    | Load Balancer adres IP frontonu | Wybierz adres IP frontonu MyLoadBalancer                                |
    | Źródłowa sieć wirtualna translatora adresów sieciowych        | Wybierz *myVNET*                                                                   |
    | Źródłowa podsieć NAT                 | Wybierz *myBackendSubnet*                                                          |
    | Włącz serwer proxy TCP v2               | Wybierz opcję tak/nie w zależności od tego, czy aplikacja oczekuje nagłówka serwera proxy TCP v2 |
    | Ustawienia prywatnego adresu IP       | Skonfiguruj metodę alokacji i adres IP dla każdego adresu IP translatora adresów sieciowych                  |

6. Wybierz pozycję **Dalej: zabezpieczenia dostępu**.

7. W obszarze **Tworzenie usługi link prywatny — zabezpieczenia dostępu**wprowadź lub wybierz następujące informacje:

    | Ustawienie                                     | Wartość                                                             |
    |---------------------------------------------|-------------------------------------------------------------------|
    | Widoczność                                  | Wybierz *tylko kontrolę dostępu opartą na rolach*         |
  
8. Wybierz kolejno pozycje **Dalej: Tagi**, a następnie **Przejrzyj + Utwórz** lub wybierz **kartę Recenzja + tworzenie** w górnej części strony.

9. Przejrzyj informacje i wybierz pozycję **Utwórz**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
Gdy skończysz korzystać z usługi link prywatny, Usuń grupę zasobów, aby wyczyścić zasoby używane w tym przewodniku szybki start:

1. Wprowadź *myResourceGroupLB* w polu **wyszukiwania** w górnej części portalu i wybierz pozycję *myResourceGroupLB* z wyników wyszukiwania. 
2. Wybierz pozycję **Usuń grupę zasobów**. 
3. Wprowadź wartość webresourcename **, aby wpisać nazwę grupy zasobów** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono wewnętrzny moduł równoważenia obciążenia platformy Azure i usługę łącza prywatnego. Aby dowiedzieć się więcej na temat tworzenia prywatnych punktów końcowych, zobacz [tworzenie prywatnych punktów końcowych przy użyciu Azure Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
