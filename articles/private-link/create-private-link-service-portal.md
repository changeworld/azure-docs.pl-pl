---
title: Szybki start — tworzenie usługi łącze prywatne przy użyciu portalu Azure
titlesuffix: Azure Private Link
description: Dowiedz się, jak utworzyć usługę łącze prywatne przy użyciu portalu Azure w tym przewodniku Szybki start
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: f21b440ee0e2c53d9824300e85b683629c1575da
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78252548"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Szybki start: tworzenie usługi łącze prywatne przy użyciu portalu Azure

Usługa Azure Private Link odnosi się do własnej usługi, która jest zarządzana przez private link. Można udzielić łączu prywatnemu dostępu do usługi lub zasobu, który działa za Azure Standard Load Balancer. Konsumenci usługi mogą uzyskać do niej dostęp prywatnie z ich własnych sieci wirtualnych. W tym przewodniku Szybki start dowiesz się, jak utworzyć usługę łącze prywatne przy użyciu witryny Azure portal.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Utwórz wewnętrzny moduł równoważenia obciążenia.

Najpierw utwórz sieć wirtualną. Następnie utwórz wewnętrzny moduł równoważenia obciążenia do użycia z usługą Private Link.

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji utworzysz sieć wirtualną. Należy również utworzyć podsieć do obsługi modułu równoważenia obciążenia, który uzyskuje dostęp do usługi Private Link.

W tej sekcji należy zastąpić następujące parametry w krokach poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<>nazwa grupy zasobów**  | myResourceGroupLB |
| **\<>nazwa sieci wirtualnej** | myVNet ( myVNet )          |
| **\<nazwa regionu>**          | Wschodnie stany USA 2      |
| **\<>przestrzeni adresowej IPv4**   | 10.3.0.0\16          |
| **\<>nazwa podsieci**          | myBackendSubnet        |
| **\<>zakresu adresu podsieci** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Tworzenie modułu równoważenia obciążenia w warstwie Standardowa

Użyj portalu, aby utworzyć standardowy wewnętrzny moduł równoważenia obciążenia. Określona nazwa i adres IP są automatycznie konfigurowane jako frontonianiejszego modułu równoważenia obciążenia.

1. W lewym górnym rogu portalu wybierz pozycję Utwórz moduł > **równoważenia obciążenia****sieciowego** **zasobu** > .

1. Na karcie **Podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz następujące informacje:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | **Subskrypcja**               | Wybierz subskrypcję.    |
    | **Grupa zasobów**         | Z pola **wybierz myResourceGroupLB.**|
    | **Nazwa**                   | Wpisz **myLoadBalancer**.                                   |
    | **Region**         | Wybierz pozycję **East US 2** (Wschodnie stany USA 2).                                        |
    | **Typ**          | wybierz pozycję **Wewnętrzny**.                                        |
    | **Numer jednostki magazynowej**           | Wybierz **opcję Standardowy**.                          |
    | **Sieć wirtualna**           | Wybierz **myVNet**.                          |
    | **Przypisanie adresu IP**              | Wybierz wartość **Statyczny**.   |
    | **Prywatny adres IP**|Wprowadź adres, który znajduje się w przestrzeni adresowej sieci wirtualnej i podsieci. Przykładem jest 10.3.0.7.  |

1. Zaakceptuj ustawienia domyślne dla pozostałych ustawień, a następnie wybierz **pozycję Przejrzyj + utwórz**

1. Na karcie **Recenzja + utwórz** wybierz pozycję **Utwórz**.

### <a name="create-standard-load-balancer-resources"></a>Tworzenie standardowych zasobów modułu równoważenia obciążenia

W tej sekcji skonfigurujesz ustawienia modułu równoważenia obciążenia dla puli adresów zaplecza i sondy kondycji. Można również określić reguły modułu równoważenia obciążenia.

#### <a name="create-a-back-end-pool"></a>Tworzenie puli zaplecza

Pula adresów zaplecza zawiera adresy IP wirtualnych kart sieciowych połączonych z modułem równoważenia obciążenia. Ta pula umożliwia dystrybucję ruchu do zasobów. Utwórz pulę adresów zaplecza o nazwie **myBackendPool,** aby uwzględnić zasoby, które równoważą ruch równoważenia obciążenia.

1. Wybierz **wszystkie usługi** z menu po lewej stronie.
1. Wybierz **pozycję Wszystkie zasoby**, a następnie wybierz **myLoadBalancer** z listy zasobów.
1. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.
1. Na stronie **Dodawanie puli wewnętrznej bazy danych** wprowadź **myBackendPool** jako nazwę puli zaplecza, a następnie wybierz pozycję **Dodaj**.

#### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Użyj sondy kondycji, aby umożliwić modułowi równoważenia obciążenia monitorowanie stanu zasobu. Na podstawie odpowiedzi zasobów na kontrole kondycji sonda kondycji dynamicznie dodaje lub usuwa zasoby z rotacji modułu równoważenia obciążenia.

Aby utworzyć sondę kondycji w celu monitorowania kondycji zasobów:

1. Wybierz **wszystkie zasoby** w menu po lewej stronie, a następnie wybierz **myLoadBalancer** z listy zasobów.

1. W obszarze **Ustawienia** wybierz pozycję **Sondy kondycji**, a następnie wybierz pozycję **Dodaj**.

1. Na stronie **Dodaj sondę kondycji** wprowadź lub wybierz następujące wartości:

   - **Nazwa**: Wpisz **myHealthProbe**.
   - **Protokół**: wybierz pozycję **TCP**.
   - **Port**: Wprowadź **80**.
   - **Interwał**: Wprowadź **15**. Ta wartość jest liczbą sekund między próbami sondy.
   - **Próg niezdrowej**: Wprowadź **2**. Ta wartość jest liczba kolejnych błędów sondy, które występują przed maszyny wirtualnej jest uważany w złej kondycji.

1. Kliknij przycisk **OK**.

#### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje sposób dystrybucji ruchu do zasobów. Reguła definiuje:

- Konfiguracja front-end IP dla ruchu przychodzącego.
- Back-end puli adresów IP do odbierania ruchu.
- Wymagane porty źródłowe i docelowe.

Reguła równoważenia obciążenia o nazwie **myLoadBalancerRule** nasłuchuje portu 80 w **frontonie LoadBalancerFrontEnd.** Reguła wysyła ruch sieciowy do puli adresów zaplecza **myBackendPool** na tym samym porcie 80.

Aby utworzyć regułę równoważenia obciążenia:

1. Wybierz **wszystkie zasoby** w menu po lewej stronie, a następnie wybierz **myLoadBalancer** z listy zasobów.

1. W obszarze **Ustawienia**wybierz pozycję **Reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.

1. Na stronie **Dodaj regułę równoważenia obciążenia** wprowadź lub wybierz następujące wartości, jeśli jeszcze ich nie ma:

   - **Nazwa**: Wpisz **myLoadBalancerRule**.
   - **Adres IP frontu:** Wprowadź **LoadBalancerFrontEnd**.
   - **Protokół**: wybierz pozycję **TCP**.
   - **Port**: Wprowadź **80**.
   - **Port zaplecza**: Wprowadź **80**.
   - **Pula zaplecza**: Wybierz **myBackendPool**.
   - **Sonda kondycji**: Wybierz **myHealthProbe**. 

1. Kliknij przycisk **OK**.

## <a name="create-a-private-link-service"></a>Tworzenie usługi łącza prywatnego

W tej sekcji utworzysz usługę Private Link za standardowym modułem równoważenia obciążenia.

1. W lewej górnej części strony w portalu Azure wybierz pozycję **Utwórz prywatny** > program**Networking Networking** > **Private Link Center (Preview)** w górnej części strony. Możesz również użyć pola wyszukiwania portalu, aby wyszukać link prywatny.

1. W **Private Link Center — przegląd Uwidacznij** > własną**usługę, aby inni mogli się połączyć**, wybierz **Start**.

1. W obszarze **Tworzenie usługi łączy prywatnych — podstawy**wprowadź lub wybierz te informacje:

    | Ustawienie           | Wartość                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Szczegóły projektu:  |                                                                              |
    | **Subskrypcja**      | Wybierz subskrypcję.                                                     |
    | **Grupa zasobów**    | Wybierz **myResourceGroupLB**.                                                    |
    | Szczegóły wystąpienia: |                                                                              |
    | **Nazwa**              | Wprowadź **myPrivateLinkService**. |
    | **Region**            | Wybierz pozycję **East US 2** (Wschodnie stany USA 2).                                                        |

1. Wybierz **dalej: Ustawienia ruchu wychodzącego**.

1. W obszarze **Tworzenie usługi łączy prywatnych — ustawienia wychodzące**wprowadź lub wybierz te informacje:

    | Ustawienie                           | Wartość                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Moduł równoważenia obciążenia**                     | Wybierz **myLoadBalancer**.                                                           |
    | **Adres IP frontendu modułu równoważenia obciążenia** | Wybierz adres IP front-end **myLoadBalancer**.                                |
    | **Źródłona sieć wirtualna NAT**        | Wybierz **myVNet**.                                                                   |
    | **Źródłona podsieć NAT**                 | Wybierz **myBackendSubnet**.                                                          |
    | **Włączanie serwera proxy TCP w wersji 2**               | Wybierz **TAK** lub **NIE** w zależności od tego, czy aplikacja oczekuje nagłówka serwera proxy TCP w wersji 2. |
    | **Ustawienia prywatnego adresu IP**       | Skonfiguruj metodę alokacji i adres IP dla każdego adresu IP translatora adresów sieciowych.                  |

1. **Wybierz dalej: Zabezpieczenia dostępu**.

1. W obszarze **Tworzenie usługi łączy prywatnych — zabezpieczenia dostępu**wybierz pozycję **Widoczność**, a następnie wybierz pozycję **Tylko kontrola dostępu oparta na rolach**.
  
1. Wybierz przycisk **Dalej:** > Recenzja tagów **+ utwórz** lub wybierz kartę Recenzja + **utwórz** u góry strony.

1. Przejrzyj informacje i wybierz pozycję **Utwórz**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu korzystania z usługi Łącze prywatne usuń grupę zasobów, aby wyczyścić zasoby użyte w tym przewodniku Szybki start.

1. Wprowadź **myResourceGroupLB** w polu wyszukiwania u góry portalu i wybierz **myResourceGroupLB** z wyników wyszukiwania.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. W **WPISZ NAZWĘ GRUPY ZASOBÓW**wprowadź **myResourceGroup**.
1. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono wewnętrzny moduł równoważenia obciążenia platformy Azure i usługę łącze prywatne. Możesz również dowiedzieć się, jak [utworzyć prywatny punkt końcowy przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
