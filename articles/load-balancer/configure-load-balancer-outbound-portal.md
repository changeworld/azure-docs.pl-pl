---
title: Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego przy użyciu witryny Azure portal
titleSuffix: Azure Load Balancer
description: W tym artykule pokazano, jak skonfigurować równoważenie obciążenia i reguły ruchu wychodzącego w standardowym modułie równoważenia obciążenia przy użyciu witryny Azure portal.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b75f49155991bfc71f788ad88f166c0bec281841
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590015"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego w standardowym równoważeniu obciążenia przy użyciu witryny Azure portal

W tym artykule pokazano, jak skonfigurować reguły ruchu wychodzącego w standardowym równoważku obciążenia przy użyciu witryny Azure portal.  

Zasób modułu równoważenia obciążenia zawiera dwa frontowe końce i skojarzone z nimi reguły. Masz jeden frontołów dla ruchu przychodzącego i inny front-end dla ruchu wychodzącego.  

Każdy front-end odwołuje się do publicznego adresu IP. W tym scenariuszu publiczny adres IP dla ruchu przychodzącego różni się od adresu dla ruchu wychodzącego.   Reguła równoważenia obciążenia zapewnia tylko równoważenie obciążenia przychodzącego. Reguła ruchu wychodzącego steruje wychodzącym translacji adresów sieciowych (NAT) dla maszyny Wirtualnej.  

Scenariusz używa dwóch pul zaplecza: jeden dla ruchu przychodzącego i jeden dla ruchu wychodzącego. Te pule ilustrują możliwości i zapewniają elastyczność dla scenariusza.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem. 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

W tej sekcji utworzysz moduł równoważenia obciążenia, który będzie równoważenia obciążenia maszyn wirtualnych. Można utworzyć publiczny moduł równoważenia obciążenia lub wewnętrzny moduł równoważenia obciążenia. Podczas tworzenia publicznego modułu równoważenia obciążenia należy utworzyć nowy publiczny adres IP, który jest skonfigurowany jako frontend dla modułu równoważenia obciążenia. Frontend będzie domyślnie nazwany **LoadBalancerFrontEnd.**

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasobnik** > **Równoważenia obciążenia****sieciowego** > .
2. Na karcie **Podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz następujące informacje:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz **pozycję Utwórz nowy** i wpisz **myResourceGroupSLB** w polu tekstowym.|
    | Nazwa                   | **myLoadBalancer**                                   |
    | Region         | Wybierz pozycję **Europa Zachodnia**.                                        |
    | Typ          | wybierz pozycję **Publiczny**.                                        |
    | SKU           | Wybierz **opcję Standardowy** lub **Podstawowy**. Firma Microsoft zaleca standard dla obciążeń produkcyjnych. |
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. Jeśli masz istniejący publiczny adres IP, którego chcesz użyć, wybierz **pozycję Użyj istniejącego**.  Istniejący publiczny adres IP musi być **standardową jednostką** SKU.  Podstawowe publiczne wiadomości IP nie są zgodne ze **standardowym modułem** równoważenia obciążenia jednostki SKU.  |
    | Nazwa publicznego adresu IP              | Wpisz **myPublicIP** w polu tekstowym.|
    | Strefa dostępności | Wybierz **strefę nadmiarową,** aby utworzyć odporny moduł równoważenia obciążenia. Aby utworzyć strefowy moduł równoważenia obciążenia, wybierz określoną strefę z 1, 2 lub 3 |

3. Zaakceptuj ustawienia domyślne dla pozostałej części konfiguracji.
4. Wybierz **pozycję Recenzja + utwórz**

    > [!IMPORTANT]
    > Pozostała część tego przewodnika Szybki start zakłada, że **standardowa** jednostka SKU jest wybierana podczas procesu wyboru jednostki SKU powyżej.

5. Na karcie **Recenzja + utwórz** wybierz pozycję **Utwórz**.   

    ![Tworzenie usługi Load Balancer w warstwie Standardowa](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Tworzenie zasobów modułu równoważenia obciążenia

W tej sekcji skonfigurujesz ustawienia modułu równoważenia obciążenia dla puli adresów zaplecza i sondy kondycji oraz określisz regułę modułu równoważenia obciążenia.

### <a name="create-a-backend-pool"></a>Tworzenie puli zaplecza

Pula adresów wewnętrznej bazy danych zawiera adresy IP wirtualnych kart sieciowych w puli wewnętrznej bazy danych. Utwórz pulę adresów wewnętrznej bazy danych **myBackendPool,** aby uwzględnić maszyny wirtualne do równoważenia obciążenia ruchu internetowego.

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie wybierz **myLoadBalancer** z listy zasobów.
2. W obszarze **Ustawienia**wybierz pozycję **Pule wewnętrznej bazy,** a następnie wybierz pozycję **Dodaj**.
3. Na stronie **Dodawanie puli wewnętrznej bazy danych** dla nazwy wpisz **myBackendPool**, jako nazwę puli wewnętrznej bazy danych, a następnie wybierz pozycję **Dodaj**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji służy do monitorowania stanu aplikacji. Sonda kondycji dodaje lub usuwa maszyny wirtualne z modułu równoważenia obciążenia na podstawie ich odpowiedzi na testy kondycji. Utwórz sondę kondycji **myHealthProbe**, aby monitorować kondycję maszyn wirtualnych.

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie wybierz **myLoadBalancer** z listy zasobów.
2. W obszarze **Ustawienia**wybierz pozycję **Sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myHealthProbe**. |
    | Protocol (Protokół) | Wybierz **HTTP**. |
    | Port | Wprowadź **80**.|
    | Interval | Wprowadź **15** dla liczby **interwałów** w sekundach między próbami sondy. |
    | Niezdrowy próg | Wybierz **2** dla liczby progu w złej kondycji lub kolejnych błędów **sondy,** które muszą wystąpić, zanim maszyna wirtualna jest uważana za złą w złej kondycji.|
    | | |
4. Kliknij przycisk **OK**.

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia
Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. 

Definiujesz:
 - Konfiguracja ip frontu dla ruchu przychodzącego.
 - Pula adresów IP wewnętrznej bazy danych do odbierania ruchu.
 - Wymagany port źródłowy i docelowy. 

W poniższej sekcji należy utworzyć:
 - Moduł równoważenia obciążenia **reguły mójHTTPRule** do nasłuchiwania portu 80.
 - Frontend **LoadBalancerFrontEnd**.
 - Pula adresów wewnętrznej bazy **danych myBackEndPool** również przy użyciu portu 80. 

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie wybierz **myLoadBalancer** z listy zasobów.
2. W obszarze **Ustawienia**wybierz pozycję **Reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.
3. Użyj tych wartości, aby skonfigurować regułę równoważenia obciążenia:
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **mójHTTPRule**. |
    | Protocol (Protokół) | wybierz pozycję **TCP**. |
    | Port | Wprowadź **80**.|
    | Port zaplecza | Wprowadź **80**. |
    | Pula zaplecza | Wybierz **myBackendPool**.|
    | Sonda kondycji | Wybierz **myHealthProbe**. |
    | Tworzenie niejawne reguły ruchu wychodzącego | Wybierz **nr**. Utworzymy reguły ruchu wychodzącego w dalszej sekcji przy użyciu dedykowanego publicznego adresu IP. |
4. Pozostaw resztę wartości domyślnych, a następnie wybierz **przycisk OK**.

## <a name="create-outbound-rule-configuration"></a>Tworzenie konfiguracji reguły ruchu wychodzącego
Reguły wychodzące modułu równoważenia obciążenia konfigurują wychodzące SNAT dla maszyn wirtualnych w puli wewnętrznej bazy danych. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Tworzenie wychodzącego publicznego adresu IP i frontendu

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie wybierz **myLoadBalancer** z listy zasobów.

2. W obszarze **Ustawienia**wybierz pozycję **Konfiguracja ip zaplecza**, a następnie wybierz pozycję **Dodaj**.

3. Użyj tych wartości, aby skonfigurować konfigurację ip wewnętrznej bazy danych dla ruchu wychodzącego:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **LoadBalancerFrontEndOutbound**. |
    | Wersja protokołu IP | Wybierz pozycję **IPv4**. |
    | Typ adresu IP | Wybierz **adres IP**.|
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. W pliku **Add a public IP address**wprowadź pozycję **mójEsPRoutbound**.  Kliknij przycisk **OK**. |

4. Wybierz pozycję **Dodaj**.

### <a name="create-an-outbound-backend-pool"></a>Tworzenie puli wychodzących wewnętrznej bazy danych

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie wybierz **myLoadBalancer** z listy zasobów.

2. W obszarze **Ustawienia**wybierz pozycję **Pule wewnętrznej bazy,** a następnie wybierz pozycję **Dodaj**.

3. Na stronie **Dodawanie puli wewnętrznej bazy danych,** aby uzyskać nazwę, wpisz **myBackendPoolOutbound**, jako nazwę puli wewnętrznej bazy danych, a następnie wybierz pozycję **Dodaj**.

### <a name="create-outbound-rule"></a>Tworzenie reguły ruchu wychodzącego

1. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby**, a następnie wybierz **myLoadBalancer** z listy zasobów.

2. W obszarze **Ustawienia**wybierz pozycję **Reguły ruchu wychodzącego**, a następnie wybierz pozycję **Dodaj**.

3. Użyj tych wartości, aby skonfigurować reguły ruchu wychodzącego:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myOutboundRule**. |
    | Adres IP frontu | Wybierz **opcję LoadBalancerFrontEndOutbound**. |
    | Limit czasu bezczynnego (minuty) | Przesuń suwak do **15 minut.|
    | Resetowanie TCP | Wybierz pozycję **Włączone**.|
    | Pula zaplecza | Wybierz **myBackendPoolOutbound** |
    | Alokacja portów - > Alokacja portu | Wybierz **pozycję Ręcznie wybierz liczbę portów wychodzących** |
    | Porty wychodzące -> Wybierz według | Wybieranie **portów na wystąpienie** |
    | Porty wychodzące -> Porty na wystąpienie | Wprowadź **10 000**. |

4. Wybierz pozycję **Dodaj**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, moduł równoważenia obciążenia i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Wybierz grupę zasobów **myResourceGroupSLB** zawierającą moduł równoważenia obciążenia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym artykule:
 - Utworzono standardowy moduł równoważenia obciążenia.
 - Skonfigurowano reguły ruchu ruchu przychodzącego i wychodzącego modułu równoważenia obciążenia.
 - Skonfigurowano sondę kondycji dla maszyn wirtualnych w puli zaplecza. 

Aby dowiedzieć się więcej, przejdź do [samouczków dotyczących modułu równoważenia obciążenia usługi Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
