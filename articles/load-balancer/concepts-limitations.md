---
title: Składniki i ograniczenia
titleSuffix: Azure Load Balancer
description: Omówienie składników Azure Load Balancer i ograniczeń.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer components and limitations and how it will affect my environment.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: allensu
ms.openlocfilehash: 93fce95ad73f5e93afdbb794af3279a35243e2e2
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046254"
---
# <a name="load-balancer-components-and-limitations"></a>Load Balancer składników i ograniczeń
Azure Load Balancer zawiera kilka kluczowych składników dla operacji.  Te składniki można skonfigurować w ramach subskrypcji za pośrednictwem Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.  

## <a name="load-balancer-components"></a>Składniki Load Balancer

* **Konfiguracje adresów IP frontonu**: adres IP modułu równoważenia obciążenia. Jest to punkt kontaktu dla klientów. Mogą to być następujące adresy: 

    - **[Publiczny adres IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)**
    - **[Prywatny adres IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)**

* **Pula zaplecza**: Grupa maszyn wirtualnych lub wystąpień w zestawie skalowania maszyn wirtualnych, które będą obsługiwać przychodzące żądanie. Aby zapewnić oszczędne skalowanie w celu spełnienia dużych ilości ruchu przychodzącego, wytyczne obliczeniowe zwykle zaleca się dodanie większej liczby wystąpień do puli zaplecza. Usługa równoważenia obciążenia natychmiast ponownie konfiguruje siebie za pomocą automatycznej zmiany konfiguracji podczas skalowania wystąpień w górę lub w dół. Dodawanie lub usuwanie maszyn wirtualnych z puli zaplecza ponownie konfiguruje moduł równoważenia obciążenia bez dodatkowych operacji w zasobie modułu równoważenia obciążenia.
* **Sondy kondycji**: **[sonda kondycji](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** służy do określania kondycji wystąpień w puli zaplecza. Można zdefiniować próg złej kondycji dla sond kondycji. Jeśli sonda nie odpowiada, moduł równoważenia obciążenia zaprzestaje inicjowania nowych połączeń z wystąpieniami o złej kondycji. Awaria sondy nie ma wpływu na istniejące połączenia. 
    
    Połączenie jest kontynuowane do momentu, gdy aplikacja: 
    - Zamyka przepływ
    - Limit czasu bezczynności
    - Maszyna wirtualna jest zamykana

    Load Balancer oferuje różne typy sond kondycji dla punktów końcowych:
    - TCP
    - HTTP
    - HTTPS
     
    Aby uzyskać więcej informacji, zobacz [typy sond](load-balancer-custom-probe-overview.md#types).

* **Reguły równoważenia obciążenia**: reguły równoważenia obciążenia są tymi, które informują Load Balancer, co należy zrobić. 
* **Reguły NAT dla ruchu przychodzącego**: Reguła ruchu przychodzącego NAT przesyła dalej ruch z określonego portu określonego adresu IP frontonu do określonego portu określonego wystąpienia zaplecza w sieci wirtualnej. **[Przekazywanie portów](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** odbywa się przy użyciu tego samego rozkładu opartego na wykorzystaniu skrótu co Równoważenie obciążenia. Typowe scenariusze tej możliwości są Remote Desktop Protocol (RDP) lub Secure Shell (SSH) do poszczególnych wystąpień maszyn wirtualnych w ramach Virtual Network platformy Azure. Można mapować wiele wewnętrznych punktów końcowych na porty w ramach tego samego adresu IP frontonu. Korzystając z adresów IP frontonu, można zdalnie administrować maszynami wirtualnymi bez dodatkowego pola skoku.
* **Reguły ruchu wychodzącego**: **[Reguła ruchu](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)** wychodzącego konfiguruje wychodzące translację adresów sieciowych (NAT) dla wszystkich maszyn wirtualnych lub wystąpień identyfikowanych przez pulę zaplecza, które mają zostać przetłumaczone na fronton.

![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)

## <a name = "load-balancer-concepts"></a>Koncepcje Load Balancer

Moduł równoważenia obciążenia zapewnia następujące podstawowe możliwości na potrzeby aplikacji protokołu TCP i UDP:

* **Algorytm równoważenia obciążenia**: w Azure Load Balancer można utworzyć regułę równoważenia obciążenia w celu dystrybucji ruchu przychodzącego do wystąpień puli zaplecza. Load Balancer używa algorytmu wyznaczania wartości skrótu do dystrybucji przepływów przychodzących i ponownie zapisuje nagłówki przepływów do wystąpień puli zaplecza. Serwer jest dostępny do odbierania nowych przepływów, gdy sonda kondycji wskazuje dobry punkt końcowy zaplecza.
Domyślnie Load Balancer używa skrótu 5-spójnej kolekcji. 

   Skrót obejmuje: 

   - **Źródłowy adres IP**
   - **Port źródłowy**
   - **Docelowy adres IP**
   - **Port docelowy**
   - **Numer protokołu IP do mapowania przepływów na dostępne serwery** 

Można utworzyć koligację ze źródłowym adresem IP przy użyciu algorytmu 2-lub 3-spójnej kolekcji dla danej reguły. Wszystkie pakiety z tego samego przepływu pakietów trafiają do tego samego wystąpienia w ramach frontonu z równoważeniem obciążenia. Gdy klient uruchamia nowy przepływ z tego samego źródłowego adresu IP, port źródłowy zostanie zmieniony. W związku z tym mieszanie 5-krotka może spowodować, że ruch przejdzie do innego punktu końcowego zaplecza.
Aby uzyskać więcej informacji, zobacz [Konfigurowanie trybu dystrybucji dla Azure Load Balancer](./load-balancer-distribution-mode.md). 

Na poniższej ilustracji przedstawiono dystrybucję opartą na skrótach:

  ![Dystrybucja oparta na skrótach](./media/load-balancer-overview/load-balancer-distribution.png)

  *Rysunek: dystrybucja oparta na skrótach*

* **Niezależność i przezroczystość aplikacji**: Load Balancer nie współdziała bezpośrednio z protokołem TCP lub UDP ani warstwą aplikacji. Można obsługiwać dowolny scenariusz aplikacji TCP lub UDP. Load Balancer nie przerywa działania lub nie pochodzą przepływy, współdziałają z ładunkiem przepływu ani nie zapewniają żadnej funkcji bramy warstwy aplikacji. Uzgadnianie protokołu zawsze następuje bezpośrednio między klientem a wystąpieniem puli zaplecza. Odpowiedź na przepływ przychodzący to zawsze odpowiedź z maszyny wirtualnej. Po dostarczeniu przepływu do maszyny wirtualnej oryginalny źródłowy adres IP również jest zachowywany.
  * Na każdy punkt końcowy odpowiada tylko maszyna wirtualna. Na przykład uzgadnianie protokołu TCP zawsze występuje między klientem a wybraną maszyną wirtualną zaplecza. Odpowiedź na żądanie do frontonu jest odpowiedzią wygenerowaną przez maszynę wirtualną zaplecza. Po pomyślnym zweryfikowaniu łączności z frontonem jest sprawdzana jest weryfikacja kompleksowej łączności z co najmniej jedną maszyną wirtualną zaplecza.
  * Ładunki aplikacji są niewidoczne dla Load Balancer. Można obsługiwać dowolną aplikację UDP lub TCP.
  * Ponieważ Load Balancer nie współdziała z ładunkiem protokołu TCP i zapewnia odciążanie protokołu TLS, można utworzyć kompleksowe scenariusze z szyfrowaniem. Korzystanie z Load Balancer jest bardzo duże dla aplikacji TLS, kończąc połączenie TLS na samej maszynie wirtualnej. Na przykład pojemność klucza sesji protokołu TLS jest ograniczona tylko przez typ i liczbę maszyn wirtualnych dodawanych do puli zaplecza.

* **Połączenia wychodzące (IP)** : wszystkie przepływy wychodzące z prywatnych adresów IP w sieci wirtualnej do publicznych adresów IP w Internecie można przetłumaczyć na adres IP frontonu Load Balancer. Gdy publiczny fronton jest powiązany z maszyną wirtualną zaplecza za pomocą reguły równoważenia obciążenia, platforma Azure tłumaczy połączenia wychodzące na publiczny adres IP frontonu. Ta konfiguracja ma następujące zalety:
  * Łatwe uaktualnienie i odzyskiwanie po awarii usług, ponieważ fronton może być dynamicznie mapowany do innego wystąpienia usługi.
  * Łatwiejsze zarządzanie listami kontroli dostępu (ACL). Listy ACL wyrażone jako adresy IP frontonu nie zmieniają się jako usługi skalowanie w górę lub w dół lub do ponownego wdrożenia. Tłumaczenie połączeń wychodzących na mniejszą liczbę adresów IP niż maszyny zmniejsza obciążenie związane z wdrażaniem bezpiecznych list adresatów.
Aby uzyskać więcej informacji, zobacz [połączenia wychodzące na platformie Azure](load-balancer-outbound-connections.md).
Usługa Load Balancer w warstwie Standardowa ma dodatkowe możliwości specyficzne dla jednostki SKU, które wykraczają poza te podstawy, zgodnie z poniższym opisem.

## <a name="load-balancer-types"></a>Typy Load Balancer

### <a name = "publicloadbalancer"></a>Publiczny moduł równoważenia obciążenia

Load Balancer publiczny mapuje publiczny adres IP i port ruchu przychodzącego na prywatny adres IP i port maszyny wirtualnej. Load Balancer mapuje ruch w inny sposób wokół ruchu odpowiedzi z maszyny wirtualnej. Można dystrybuować określone typy ruchu między wieloma maszynami wirtualnymi lub usługami, stosując reguły równoważenia obciążenia. Na przykład można rozłożyć obciążenie ruchu związanego z żądaniami internetowymi na wiele serwerów internetowych.

>[!NOTE]
>Można zaimplementować tylko jedną Load Balancer publiczną i jeden wewnętrzny Load Balancer na zestaw dostępności.

Na poniższej ilustracji przedstawiono punkt końcowy ze zrównoważonym obciążeniem dla ruchu internetowego, który jest współużytkowany przez trzy maszyny wirtualne dla portu publicznego i TCP 80. Te trzy maszyny wirtualne znajdują się w zestawie z równoważeniem obciążenia.

![Przykład publicznego modułu równoważenia obciążenia](./media/load-balancer-overview/IC727496.png)

*Ilustracja: Równoważenie ruchu internetowego przy użyciu Load Balancer publicznego*

Klienci internetowi wysyłają żądania strony sieci Web na publiczny adres IP aplikacji sieci Web na porcie TCP 80. Azure Load Balancer dystrybuuje żądania między trzema maszynami wirtualnymi w zestawie o zrównoważonym obciążeniu. Aby uzyskać więcej informacji na temat algorytmów Load Balancer, zobacz temat [Load Balancer koncepcje](concepts-limitations.md#load-balancer-concepts).

Azure Load Balancer domyślnie dystrybuuje ruch sieciowy między wieloma wystąpieniami maszyn wirtualnych. Można również skonfigurować koligację sesji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie trybu dystrybucji dla Azure Load Balancer](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Wewnętrzny moduł równoważenie obciążenia

Wewnętrzny moduł równoważenia obciążenia kieruje ruch tylko do zasobów znajdujących się w sieci wirtualnej lub korzystających z sieci VPN w celu uzyskania dostępu do infrastruktury platformy Azure, w przeciwieństwie do publicznego modułu równoważenia obciążenia. Infrastruktura platformy Azure ogranicza dostęp do adresów IP frontonu z równoważeniem obciążenia sieci wirtualnej. Adresy IP frontonu i sieci wirtualne nigdy nie są bezpośrednio ujawniane w internetowym punkcie końcowym. Wewnętrzne aplikacje biznesowe są uruchomiane na platformie Azure i dostęp do nich jest uzyskiwany z poziomu platformy Azure lub zasobów lokalnych.

Wewnętrzny moduł równoważenia obciążenia pozwala na następujące typy równoważenia obciążenia:

* **W ramach sieci wirtualnej**: Równoważenie obciążenia z maszyn wirtualnych w sieci wirtualnej do zestawu maszyn wirtualnych znajdujących się w tej samej sieci wirtualnej.
* W **przypadku sieci wirtualnej obejmującej wiele**lokalizacji: Równoważenie obciążenia z komputerów lokalnych do zestawu maszyn wirtualnych znajdujących się w tej samej sieci wirtualnej.
* **W przypadku aplikacji wielowarstwowych**: Równoważenie obciążenia dla aplikacji wielowarstwowych dostępnych z Internetu, w których warstwy zaplecza nie są dostępne w Internecie. Warstwy zaplecza wymagają równoważenia obciążenia sieciowego z warstwy połączonej z Internetem. Zobacz następną ilustrację.
* **Dla aplikacji biznesowych**: równoważenie obciążenia na potrzeby aplikacji biznesowych, które są hostowane na platformie Azure bez użycia dodatkowego sprzętu lub oprogramowania służącego do równoważenia obciążenia. Ten scenariusz obejmuje serwery lokalne, które znajdują się w zestawie komputerów, których ruch jest zrównoważony.

![Przykład wewnętrznego modułu równoważenie obciążenia](./media/load-balancer-overview/IC744147.png)

*Ilustracja: Równoważenie aplikacji wielowarstwowych przy użyciu Load Balancer publicznego i wewnętrznego*

## <a name="skus"></a> Porównanie jednostek SKU modułu równoważenia obciążenia

Moduł równoważenia obciążenia obsługuje podstawowe i standardowe jednostki SKU. Te jednostki SKU różnią się w zależności od skali, funkcji i cen. Dowolny scenariusz, który jest możliwy w przypadku podstawowego Load Balancer można utworzyć przy użyciu usługa Load Balancer w warstwie Standardowa. Interfejsy API dla obu jednostek SKU są podobne i są wywoływane przez specyfikację jednostki SKU. Interfejs API obsługujący jednostki SKU dla usługi równoważenia obciążenia i publiczny adres IP jest dostępny, począwszy od interfejsu API `2017-08-01`. Obie jednostki SKU korzystają z tego samego ogólnego interfejsu API i struktury.

Kompletna konfiguracja scenariusza może się nieco różnić w zależności od jednostki SKU. Dokumentacja usługi równoważenia obciążenia jest wywoływana, gdy artykuł ma zastosowanie tylko do określonej jednostki SKU. Aby porównać i poznać różnice, zapoznaj się z poniższą tabelą. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).

>[!NOTE]
> Firma Microsoft zaleca usługa Load Balancer w warstwie Standardowa.
Autonomiczne maszyny wirtualne, zestawy dostępności i zestawy skalowania maszyn wirtualnych można połączyć tylko jednej jednostki SKU — nigdy do obu. Load Balancer oraz jednostka SKU publicznego adresu IP muszą być zgodne, gdy są używane z publicznymi adresami IP. Jednostki SKU Load Balancer i publicznych adresów IP nie są modyfikowalne.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Aby uzyskać więcej informacji, zobacz [limity usługi równoważenia obciążenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Szczegółowe informacje dotyczące modułu równoważenia obciążenia w warstwie Standardowa zawiera [przegląd](load-balancer-standard-overview.md), [cennik](https://aka.ms/lbpricing) i [umowa dotycząca poziomu usług](https://aka.ms/lbsla).


## <a name = "limitations"></a>Limity

* Load Balancer zapewnia Równoważenie obciążenia i przekazywanie portów dla określonych protokołów TCP lub UDP. Reguły równoważenia obciążenia i reguły NAT dla ruchu przychodzącego obsługują protokoły TCP i UDP, ale nie są to adresy IP, w tym protokół ICMP.

  Load Balancer nie przerywa działania, reaguje lub nie działa w inny sposób z ładunkiem przepływu UDP lub TCP. Nie jest to serwer proxy. Pomyślne sprawdzenie poprawności łączności z frontonem musi odbywać się w paśmie przy użyciu tego samego protokołu, który jest używany w ramach równoważenia obciążenia lub przychodzącej reguły NAT. Co najmniej jedna maszyna wirtualna musi generować odpowiedź dla klienta, aby zobaczyć odpowiedź z frontonu.

  Nie odebrano odpowiedzi w paśmie z frontonu Load Balancer wskazuje, że żadne maszyny wirtualne nie mogą reagować. Nic nie może współdziałać z Load Balancer frontonu bez możliwości reagowania maszyny wirtualnej. Ta zasada ma zastosowanie również do połączeń wychodzących, w których port podszywający jest obsługiwany tylko dla protokołów TCP i UDP. Wszystkie inne protokoły IP, w tym ICMP, kończą się niepowodzeniem. Przypisz publiczny adres IP na poziomie wystąpienia, aby rozwiązać ten problem. Aby uzyskać więcej informacji, zobacz temat [Omówienie elementu reźródłowego i](load-balancer-outbound-connections.md#snat)elementu.

* Wewnętrzne moduły równoważenia obciążenia nie przekładają się na wychodzące połączenia przychodzące do frontonu wewnętrznego Load Balancer, ponieważ obie są w prywatnej przestrzeni adresów IP. Publiczne usługi równoważenia obciążenia zapewniają [połączenia wychodzące](load-balancer-outbound-connections.md) z prywatnych adresów IP w sieci wirtualnej do publicznych adresów IP. W przypadku wewnętrznych modułów równoważenia obciążenia to podejście pozwala uniknąć potencjalnego wyczerpania portów w ramach unikatowej wewnętrznej przestrzeni adresów IP, w której tłumaczenie nie jest wymagane.

  Efekt uboczny polega na tym, że jeśli przepływ wychodzący z maszyny wirtualnej w puli zaplecza próbuje przepływ do frontonu Load Balancer wewnętrznej w swojej puli _i_ jest mapowany do samego siebie, dwa etapy przepływu nie są zgodne. Ponieważ nie są one zgodne, przepływ kończy się niepowodzeniem. Przepływ powiedzie się, jeśli przepływ nie został zmapowany na tę samą maszynę wirtualną w puli zaplecza, która utworzyła przepływ do frontonu.

  Gdy przepływ zamapuje się z powrotem do samego siebie, przepływ wychodzący wydaje się pochodzić z maszyny wirtualnej do frontonu, a odpowiedni przepływ przychodzący wydaje się pochodzić z maszyny wirtualnej do samej siebie. Z punktu widzenia systemu operacyjnego gościa elementy przychodzące i wychodzące tego samego przepływu nie pasują do maszyny wirtualnej. Stos TCP nie rozpoznaje tych połówów tego samego przepływu, co jest częścią tego samego przepływu. Źródło i miejsce docelowe nie są zgodne. Gdy przepływ jest mapowany na dowolną inną maszynę wirtualną w puli zaplecza, połowy przepływu są zgodne, a maszyna wirtualna może odpowiedzieć na przepływ.

  Objawem tego scenariusza jest sporadyczne przekroczenie limitu czasu połączenia, gdy przepływ powraca do tego samego zaplecza, który pochodzi z przepływu. Typowe obejścia obejmują Wstawianie warstwy proxy za wewnętrzną Load Balancer i używanie reguł stylu bezpośredniego powrotu serwera (DSR). Aby uzyskać więcej informacji, zobacz [wiele frontonów dla Azure Load Balancer](load-balancer-multivip-overview.md).

  Wewnętrzny Load Balancer można połączyć z dowolnym serwerem proxy innej firmy lub użyć wewnętrznych [Application Gateway](../application-gateway/application-gateway-introduction.md) dla scenariuszy serwera proxy przy użyciu protokołu HTTP/HTTPS. Mimo że można użyć Load Balancer publicznego, aby zmniejszyć ten problem, wynikający z nich scenariusz jest podatny na [wyczerpanie adresów](load-balancer-outbound-connections.md#snat). Należy unikać tego drugiego podejścia, chyba że jest to starannie zarządzane.

* Ogólnie rzecz biorąc, fragmenty adresów IP przekazywania nie są obsługiwane w regułach równoważenia obciążenia. Fragmentacja IP pakietów UDP i TCP nie jest obsługiwana w regułach równoważenia obciążenia. Reguły równoważenia obciążenia portów o wysokiej dostępności mogą służyć do przesyłania dalej istniejących fragmentów adresów IP. Aby uzyskać więcej informacji, zobacz [Omówienie portów wysokiej dostępności](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Następne kroki

Zobacz [tworzenie usługa Load Balancer w warstwie Standardowa publicznej](quickstart-load-balancer-standard-public-portal.md) , aby rozpocząć korzystanie z usługi Load Balancer: Utwórz jedną, twórz maszyny wirtualne z zainstalowanym niestandardowym rozszerzeniem usług IIS i Zrównoważ obciążenie aplikacji sieci Web między maszynami wirtualnymi.
