---
title: Omówienie usługi równoważenia obciążenia Azure | Dokumentacja firmy Microsoft
description: Omówienie funkcji równoważenia obciążenia Azure, architektura i implementacji. Dowiedz się, jak działa usługa równoważenia obciążenia i wykorzystać go w chmurze.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: 3a5d1e897d8ffe063ecf9277bef346c8b7c5092b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-load-balancer-overview"></a>Omówienie usługi Azure Load Balancer

Moduł równoważenia obciążenia Azure umożliwia skalowanie aplikacji i utworzyć wysoką dostępność usług. Usługi równoważenia obciążenia obsługuje scenariusze dla ruchu przychodzącego, jak również wychodzących i zapewnia małe opóźnienia, wysokiej przepływności i skalowanie do milionów przepływów dla wszystkich aplikacji TCP i UDP.   

Moduł równoważenia obciążenia będzie dystrybucję nowych przepływów przychodzących przychodzące do frontonu modułu równoważenia obciążenia do wystąpień puli wewnętrznej bazy danych zgodnie z zasadami i sondy kondycji.  

Ponadto publiczny moduł równoważenia obciążenia można też podać połączeń wychodzących dla maszyn wirtualnych w sieci wirtualnej przez translacji swoich prywatnych adresów IP na publiczne adresy IP.

Moduł równoważenia obciążenia Azure jest dostępna w dwóch różnych jednostki SKU: Basic i Standard.  Istnieją różnice w skali, funkcje i cenach.  Sytuacja możliwe podstawowe usługi równoważenia obciążenia można również tworzyć standardowe usługi równoważenia obciążenia, chociaż to rozwiązanie może różnić się nieznacznie.  Jak dowiedzieć modułu równoważenia obciążenia, należy zapoznać się z podstaw i różnice specyficzny dla jednostki SKU.

## <a name="why-use-load-balancer"></a>Dlaczego warto używać usługi równoważenia obciążenia? 

Moduł równoważenia obciążenia Azure można używać do:

* Równoważenia obciążenia przychodzącego Internet ruchu do maszyn wirtualnych. Ta konfiguracja jest określana jako [publiczny moduł równoważenia obciążenia](#publicloadbalancer).
* Równoważenie obciążenia ruchu między maszynami wirtualnymi w sieci wirtualnej. Zapewnia także łączność z siecią lokalną w scenariuszu hybrydowym frontonu modułu równoważenia obciążenia. Oba te scenariusze korzysta z konfiguracji znany jako [wewnętrzny moduł równoważenia obciążenia](#internalloadbalancer).
* Ruch do przodu portów do określonego portu określonych maszyn wirtualnych z reguły NAT ruchu przychodzącego.
* Podaj [łączność wychodząca](load-balancer-outbound-connections.md) dla maszyn wirtualnych w sieci wirtualnej przy użyciu publiczny moduł równoważenia obciążenia.


>[!NOTE]
> Platforma Azure udostępnia mechanizm równoważenia rozwiązań dla scenariuszy pełni zarządzana obciążenia.  Jeśli szukasz dla zakończenia połączenia TLS ("odciążanie protokołu SSL") lub na przetwarzanie warstwy aplikacji żądań HTTP i HTTPS, przejrzyj [brama aplikacji w](../application-gateway/application-gateway-introduction.md).  Jeśli szukasz DNS globalnego równoważenia obciążenia, przejrzyj [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Scenariuszy end-to-end mogą korzystać z łączenia tych rozwiązań, zgodnie z potrzebami.

## <a name="what-is-load-balancer"></a>Co to jest moduł równoważenia obciążenia?

Zasób usługi równoważenia obciążenia może istnieć jako publiczny moduł równoważenia obciążenia lub wewnętrzny moduł równoważenia obciążenia. Funkcje zasobów usługi równoważenia obciążenia są wyrażane jako frontonu, regułę, sondy kondycji i definicji puli wewnętrznej bazy danych.  Maszyny wirtualne są umieszczane w puli zaplecza, określając puli wewnętrznej bazy danych z maszyny wirtualnej.

Zasoby usługi równoważenia obciążenia to obiekty w ramach których mogą express, jak program jego wielodostępnej infrastrukturze do osiągnięcia scenariusza, który chcesz utworzyć w usłudze Azure.  Nie ma żadnej bezpośredniej zależności między zasobami usługi równoważenia obciążenia i rzeczywistego infrastruktury. Tworzenie modułu równoważenia obciążenia nie tworzy wystąpienie i pojemność są zawsze dostępne. 

## <a name="fundamental-load-balancer-features"></a>Podstawowe funkcje równoważenia obciążenia

Moduł równoważenia obciążenia zapewnia następujące funkcje podstawowe dla aplikacji TCP i UDP:

* **Równoważenie obciążenia**

    Moduł równoważenia obciążenia Azure umożliwia tworzenie reguł do dystrybucji ruchu otrzymywanych frontonu do wystąpień puli wewnętrznej bazy danych z równoważeniem obciążenia.  Używa algorytmu wyznaczania wartości skrótu na podstawie dystrybucji przepływów ruchu przychodzącego, a w związku z tym ponownie zapisuje nagłówki są przesyłane wystąpień puli wewnętrznej bazy danych. Serwer jest dostępny na odbieranie nowych przepływów podczas badania kondycji wskazuje punkt końcowy w dobrej kondycji wewnętrznej bazy danych.
    
    Domyślnie używa do mapowania przepływów dostępnych serwerów skrót 5-elementowej składa się z źródłowy adres IP, portu źródłowego, docelowego adresu IP, port docelowy i numer protokołu IP.  Istnieje możliwość utworzenia koligacji do określonego źródłowego adresu IP przez Rezygnacja do skrótu podwójnego 2 lub 3 dla danej reguły.  Wszystkie pakiety o takim samym przepływie pakietów przychodzących na tym samym wystąpieniu za frontonu równoważeniem obciążenia.  Gdy klient inicjuje nowy przepływ z tym samym źródłowy adres IP, zmiany portu źródłowego. Wynikowa 5-elementowej może spowodować ruchu przejść do punktu końcowego innej wewnętrznej bazy danych, w związku z tym.

    Aby uzyskać więcej informacji, zobacz [tryb dystrybucji modułu równoważenia obciążenia](load-balancer-distribution-mode.md). Na poniższym rysunku przedstawiono dystrybucji wyznaczania wartości skrótu:

    ![Dystrybucji wyznaczania wartości skrótu](./media/load-balancer-overview/load-balancer-distribution.png)

    *Rysunek - Hash-based dystrybucji*

* **Przekierowanie portów**

    Moduł równoważenia obciążenia Azure umożliwia tworzenie przychodzącą regułę NAT do przesyłania dalej ruch na porcie z określonego portu frontonu określonego adresu IP do określonego portu wystąpienia określonego wewnętrznej bazy danych w sieci wirtualnej. Ponadto jest to osiągane przez tego samego dystrybucji bazującego na skrót jako równoważenia obciążenia.  Typowe scenariusze dotyczące tej możliwości są sesji protokołu RDP (Remote Desktop) lub protokołu Secure Shell (SSH) do wystąpień poszczególnych maszyn wirtualnych w sieci wirtualnej.  Wiele wewnętrznych punktów końcowych można mapować na różnych portów na ten sam adres IP frontonu. Te umożliwia zdalne administrowanie maszyn wirtualnych w Internecie bez konieczności okno przeskoku dodatkowe.

* **Aplikacja o niesprecyzowanym i przezroczyste**

    Moduł równoważenia obciążenia nie bezpośrednio współdziała z TCP lub UDP lub warstwy aplikacji i wszelkich TCP lub może być obsługiwany scenariusz aplikacji opartych na protokole UDP.  Na przykład gdy moduł równoważenia obciążenia nie zakończy TLS samego, możesz skompilować i skalowanie aplikacji TLS przy użyciu usługi równoważenia obciążenia i kończ połączenia TLS w samej maszyny wirtualnej. Moduł równoważenia obciążenia nie zakończy przepływu i uzgodnienia protokołu są zawsze bezpośrednio między klientem a wystąpienia puli wewnętrznej bazy danych wybrane wyznaczania wartości skrótu. Na przykład uzgadniania protokołu TCP jest zawsze między klientem i maszyny wirtualnej wybrany wewnętrznej bazy danych.  I odpowiedzi na żądanie frontonu odpowiedzi generowane z wewnętrznej bazy danych maszyny wirtualnej.  Wydajność sieci wychodzącego modułu równoważenia obciążenia jest tylko ograniczony przez maszynę wirtualną, możesz wybrać jednostki SKU i przepływów pozostanie aktywności przez dłuższy czas, gdy nigdy nie zostanie osiągnięty limit czasu bezczynności.

* **Automatyczna ponowna konfiguracja**

    Moduł równoważenia obciążenia Azure natychmiast ponownie konfiguruje się wystąpień skalowanie w górę lub w dół. Dodawanie lub usuwanie maszyn wirtualnych z puli zaplecza spowoduje zmianę konfiguracji usługi równoważenia obciążenia bez dodatkowych operacji dla zasobu usługi równoważenia obciążenia.

* **Sondy kondycji**

    Moduł równoważenia obciążenia Azure używa sondy kondycji należy zdefiniować w celu ustalenia kondycji wystąpień w puli zaplecza. Podczas badania nie odpowiada, usługi równoważenia obciążenia zatrzymuje wysyłanie nowych połączeń do wystąpień złej kondycji. Istniejące połączenia nie ma wpływ i będzie kontynuowane, dopóki aplikacja kończy przepływu, limit czasu bezczynności wystąpi, lub maszyna wirtualna jest zamknięta.

    Obsługiwane są trzy typy sond:

    - **Niestandardowe badanie HTTP:** służy do tworzenia niestandardowej logiki do ustalenia stanu wystąpienia puli wewnętrznej bazy danych. Moduł równoważenia obciążenia będzie regularnie sondowania punktu końcowego (co 15 sekund, domyślnie). Wystąpienie jest uważana za dobrej kondycji, jeśli odpowie przed upływem limitu czasu (domyślnie 31 sekund) o 200 protokołu HTTP. Sonda niepowodzenie powoduje, że stan dowolnego innego niż 200 protokołu HTTP.  To jest również przydatna do implementacji logiki można usunąć wystąpienia z obrotu modułu równoważenia obciążenia. Na przykład można skonfigurować wystąpienia próbę zwrócenia stanu z systemem innym niż 200, jeśli wystąpienie jest ponad 90% zasobów Procesora.   To sondowanie zastępuje domyślną sondę agenta gościa.

    - **Niestandardowe sondowaniem TCP:** sonda opiera się na pomyślne ustanawiania sesji TCP port sondy zdefiniowane.  Tak długo, jak określony odbiornik na maszynie wirtualnej istnieje, to sondowanie zostanie wykonana pomyślnie. Połączenie zostało odrzucone, sondy zakończy się niepowodzeniem. To sondowanie zastępuje domyślną sondę agenta gościa.

    - **Sondowanie agenta gościa (na platformie jako usługa maszyn wirtualnych tylko):** usługi równoważenia obciążenia można również korzystać z agenta gościa na maszynie wirtualnej. Agent gościa wykrywa i wysyła odpowiedź HTTP 200 OK tylko wtedy, gdy wystąpienie jest w stanie gotowe. Jeśli agent nie odpowiada OK 200 protokołu HTTP, usługi równoważenia obciążenia oznacza wystąpienia jako odpowiadać i zatrzymuje wysyłania ruchu do tego wystąpienia. Moduł równoważenia obciążenia w dalszym ciągu próbują nawiązać połączenie z wystąpieniem. Agent gościa odpowie 200 protokołu HTTP, usługi równoważenia obciążenia będzie wysyłać ruch do tego wystąpienia ponownie.  Sondy agenta gościa są ostateczność i nie powinna być używana, gdy są możliwe konfiguracje niestandardowe badania HTTP lub TCP. 
    
* **Połączenia wychodzące (NAT źródła)**

    Wszystkie przepływy ruchu wychodzącego z prywatnych adresów IP w sieci wirtualnej na publiczne adresy IP w Internecie można przekonwertować na adres IP frontonu modułu równoważenia obciążenia. Gdy publicznego serwera sieci Web jest powiązany z maszyną wirtualną wewnętrznej bazy danych i reguły równoważenia obciążenia, Azure programów połączeń wychodzących, które mają być automatycznie tłumaczone na frontonie publicznego adresu IP. Jest to również źródła translatora adresów Sieciowych (SNAT). SNAT oferuje istotne korzyści:

    + Umożliwia on łatwe uaktualnianie i odzyskiwanie po awarii usługi, ponieważ frontonu, które mogą być dynamicznie mapowane do innego wystąpienia usługi.
    + Ułatwia ona zarządzanie listę kontroli dostępu (ACL) kontrolą dostępu. Listy kontroli dostępu w przeliczeniu na frontonie adresów IP nie należy zmieniać, usługi skalowania w górę, dół, lub pobrać ponownej instalacji.

    Zapoznaj się [połączeń wychodzących](load-balancer-outbound-connections.md) artykułu szczegółowe omówienie tę możliwość.

Standardowe usługi równoważenia obciążenia ma dodatkowe specyficzny dla jednostki SKU możliwości poza tymi podstawy.  Przejrzyj pozostałej części tego artykułu, aby uzyskać szczegółowe informacje.

## <a name="skus"></a> Porównanie SKU usługi równoważenia obciążenia

Moduł równoważenia obciążenia Azure obsługuje dwa różne jednostki magazynowe: Basic i Standard.  Istnieją różnice w scenariuszu skali, funkcje i cenach.  Sytuacja możliwe podstawowe usługi równoważenia obciążenia można również utworzyć standardowe usługi równoważenia obciążenia.  W rzeczywistości interfejsów API dla obu SKU są podobne i wywoływane przez specyfikację wersji.  Interfejs API do obsługi jednostki SKU dla usługi równoważenia obciążenia i publiczny adres IP jest dostępna, począwszy od interfejsu API 2017-08-01.  Obu SKU mają tego samego ogólnego interfejsu API i struktury.

Jednak w zależności od tego, którego jednostka SKU jest wybrana, szczegóły konfiguracji całego scenariusza mogą być nieco inne. Dokumentacja usługi równoważenia obciążenia wywołuje, gdy artykułu ma zastosowanie do określonej jednostki SKU tylko. Należy przejrzeć poniższą tabelę poniżej, aby porównać i poznać różnice.  Przegląd [standardowe Omówienie usługi równoważenia obciążenia](load-balancer-standard-overview.md) uzyskać więcej szczegółowych informacji.

>[!NOTE]
> Nowe projekty należy rozważyć użycie standardowego modułu równoważenia obciążenia. 

Autonomicznych maszyn wirtualnych, zestawów dostępności i zestawy skalowania maszyny wirtualnej można połączenie tylko jednego identyfikatora jednostki Magazynowej, nigdy nie oba. W przypadku użycia z publicznych adresów IP, zarówno usługi równoważenia obciążenia i publiczny adres IP SKU muszą być zgodne. Usługi równoważenia obciążenia i publicznego adresu IP jednostki SKU nie jest modyfikowalna.

_Mimo że nie jest jeszcze obowiązkowe jest najlepszym rozwiązaniem jest jawnie, określ jednostki SKU._  W tym czasie wymagane zmiany są są ograniczone do minimum. Jeśli nie określono jednostki SKU, jest interpretowany jako zamiar użyć podstawowy SKU w wersji interfejsu API 2017-08-01.

>[!IMPORTANT]
>Standardowe usługi równoważenia obciążenia jest nowym produktem modułu równoważenia obciążenia i przede wszystkim jest nadzbiorem podstawowe usługi równoważenia obciążenia.  Ma różnic ważne i zamierzonego oba produkty.  Wszystkie możliwe podstawowe usługi równoważenia obciążenia scenariusz end-to-end można tworzyć standardowe usługi równoważenia obciążenia.  Jeśli zostały już użyte podstawowa usługa równoważenia obciążenia, należy zapoznać się z modułem równoważenia obciążenia standardowego zrozumienie istotne zmiany w zachowaniu między standardu Basic i ich wpływu. Uważnie przejrzyj tę sekcję.

| | [Standardowy SKU](load-balancer-standard-overview.md) | Podstawowy SKU |
| --- | --- | --- |
| Rozmiar puli wewnętrznej bazy danych | maksymalnie 1000 wystąpień | do 100 wystąpień |
| Punkty końcowe puli wewnętrznej bazy danych | Żadnej maszyny wirtualnej w jednej sieci wirtualnej, tym blend maszyn wirtualnych, zestawów dostępności zestawach skali maszyn wirtualnych. | Ustaw maszyn wirtualnych w skali dostępności pojedynczy zestaw lub maszyny wirtualnej |
| Strefy dostępności | Nadmiarowe strefy i zonal frontends dla ruchu przychodzącego i awarie strefy, równoważenie obciążenia sieciowego stref między mapowania przepływów wychodzących, wychodzący | / |
| Diagnostyka | Azure Monitor wielowymiarowych metryk, takich jak bajt i liczniki pakietów, kondycji sondowania stanu, próby połączenia (TCP SYN), kondycja połączenia wychodzącego (SNAT udane i nieudane przepływy), pomiarów płaszczyzny aktywnych danych | Azure Log Analytics publicznego równoważenia obciążenia, alert wyczerpania SNAT, liczba kondycji puli wewnętrznej bazy danych |
| HA portów | Wewnętrzny moduł równoważenia obciążenia | / |
| Bezpieczeństwo domyślne | domyślne zamknięty dla publicznych adresów IP i usługi równoważenia obciążenia punktów końcowych i grupy zabezpieczeń sieci musi być używany do jawnie dozwolone w przypadku ruchu przepływu | domyślny otwarty, grupy zabezpieczeń sieci opcjonalne |
| Połączenia wychodzące | Wiele frontends z każdej reguły zrezygnować. Scenariusz wychodzących _musi_ jawnie można utworzyć maszyny wirtualnej można było użyć łączność wychodząca.  [Punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) bez połączenia wychodzącego jest osiągalna, a nie wchodzą w skład dane przetworzone.  Publicznych adresów IP, takich jak usługi Azure PaaS nie jest dostępna jako punktów końcowych usługi sieci wirtualnej, musi być osiągnięta poprzez łączność wychodząca i count do przetwarzania danych. Połączenia wychodzące przez domyślny SNAT wyłącznie do wewnętrznego modułu równoważenia obciążenia działa jako maszyna wirtualna, nie są dostępne. Wychodzące programowania SNAT jest protokołu transportowego określonej na podstawie protokołu reguły równoważenia obciążenia dla ruchu przychodzącego. | Fronton jeden losowo wybrany, gdy istnieją frontends wiele.  Gdy tylko wewnętrzny moduł równoważenia obciążenia działa jako maszynę wirtualną, używana jest domyślna SNAT. |
| Wiele frontends | Przychodzące i wychodzące | Tylko transfer przychodzący |
| Operacje zarządzania | Większość operacji < 30 sekund | 60-90 sekund typowe |
| Umowa SLA | 99,99% dla ścieżki danych z maszynami wirtualnymi na dwóch dobrej kondycji | Niejawne w umowie SLA maszyny Wirtualnej | 
| Cennik | Na podstawie liczby reguł, dane przetworzone ruchu przychodzącego lub wychodzącego skojarzony z zasobem  | Bez dodatkowych opłat |

Przegląd [usługi limity dla usługi równoważenia obciążenia](https://aka.ms/lblimits).  Dla usługi równoważenia obciążenia standardowego także przejrzeć bardziej szczegółowe [omówienie](load-balancer-standard-overview.md), [cennik](https://aka.ms/lbpricing), i [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Pojęcia

### <a name = "publicloadbalancer"></a>Publiczny moduł równoważenia obciążenia

Publiczny moduł równoważenia obciążenia mapuje publiczny adres IP i numer portu dla ruchu przychodzącego prywatny adres IP i numer portu z maszyny wirtualnej i odwrotnie dla ruchu odpowiedzi z maszyny wirtualnej. Reguły równoważenia obciążenia umożliwiają dystrybucji określonych rodzajów ruchu sieciowego między wiele maszyn wirtualnych lub usług. Na przykład mogą rozprzestrzeniać się obciążenie ruchu w sieci web żądania na wielu serwerach sieci web.

Na poniższej ilustracji przedstawiono punktu końcowego równoważeniem obciążenia dla ruchu w sieci web współużytkowany trzech maszyn wirtualnych dla publicznych i prywatnych port TCP 80. Te trzy maszyny wirtualne są w zestawie o zrównoważonym obciążeniu.

![przykład modułu równoważenia obciążenia publiczny](./media/load-balancer-overview/IC727496.png)

**Rysunek 1: Ruchu w sieci web przy użyciu publiczny moduł równoważenia obciążenia równoważenia obciążenia**

Gdy klienci internetowi wysyłać żądania strony sieci web do publicznego adresu IP aplikacji sieci web na porcie TCP 80, moduł równoważenia obciążenia Azure rozdziela żądania między trzy maszyny wirtualne w zestawie równoważeniem obciążenia. Aby uzyskać więcej informacji dotyczących algorytmów równoważenia obciążenia, zobacz [strony Przegląd usługi równoważenia obciążenia](load-balancer-overview.md#load-balancer-features).

Domyślnie usługa równoważenia obciążenia Azure dystrybuuje ruch sieciowy równomiernie wielu wystąpień maszyny wirtualnej. Można również skonfigurować koligację sesji. Aby uzyskać więcej informacji, zobacz [tryb dystrybucji modułu równoważenia obciążenia](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Wewnętrzny moduł równoważenia obciążenia

Wewnętrzny moduł równoważenia obciążenia kieruje tylko ruch do zasobów, które znajdują się wewnątrz sieci wirtualnej lub wykorzystujących sieci VPN uzyskiwać dostęp do infrastruktury platformy Azure. W związku z tym wewnętrzny moduł równoważenia obciążenia różni się od publiczny moduł równoważenia obciążenia. Infrastruktury platformy Azure ogranicza dostęp do serwera sieci Web z równoważeniem obciążenia adresów IP sieci wirtualnej. Adresy IP frontonu i sieci wirtualne są nigdy nie bezpośrednio widoczne dla punktu końcowego internet. Wewnętrznych aplikacji biznesowych — Uruchom na platformie Azure i są dostępne z platformy Azure lub z lokalnymi zasobami.

Wewnętrzny moduł równoważenia obciążenia zapewnia następujące typy równoważenia obciążenia:

* W ramach sieci wirtualnej: równoważenia obciążenia z maszyn wirtualnych w sieci wirtualnej do zestawu maszyn wirtualnych znajdujących się w tej samej sieci wirtualnej.
* Dla sieci wirtualnej między lokalizacjami: załadować równoważenia z komputerów lokalnych do wielu maszyn wirtualnych znajdujących się w tej samej sieci wirtualnej. 
* Dla aplikacji wielowarstwowych: równoważenia obciążenia dla aplikacji wielowarstwowych internetowy gdy warstw zaplecza nie są skierowane do Internetu. Poziomy zaplecza wymagają obciążenia ruchu równoważenia z Internetem warstwy (patrz rysunek 2).
* W przypadku aplikacji biznesowych z: równoważenia obciążenia dla aplikacji — biznesowych, które są obsługiwane na platformie Azure, bez dodatkowego obciążenia równoważenia sprzętu lub oprogramowania. Taki scenariusz obejmuje serwery lokalne, które znajdują się w zestawie komputerów, których ruch jest równoważeniem obciążenia.

![przykład modułu równoważenia obciążenia wewnętrznego](./media/load-balancer-overview/IC744147.png)

**Rysunek 2 — aplikacje wielowarstwowe przy użyciu obu usług równoważenia obciążenia publicznych oraz wewnętrznych równoważenia obciążenia**

## <a name="pricing"></a>Cennik
Standardowy moduł równoważenia obciążenia jest produktem obciążona, na podstawie liczby reguł równoważenia obciążenia skonfigurowane i wszystkie dane przychodzące i wychodzące przetworzone. Standardowe informacje o cenach równoważenia obciążenia, można znaleźć [cennik usługi równoważenia obciążenia](https://azure.microsoft.com/pricing/details/load-balancer/) strony.

Podstawowe usługi równoważenia obciążenia jest oferowane bezpłatnie.

## <a name="sla"></a>Umowa SLA

Informacje standardowe umowy SLA dla usługi równoważenia obciążenia, odwiedź stronę [umowy SLA dla usługi równoważenia obciążenia](https://aka.ms/lbsla) strony. 

## <a name="next-steps"></a>Kolejne kroki

- Przegląd [standardowego modułu równoważenia obciążenia bardziej szczegółowo](load-balancer-standard-overview.md)
- Informacje o używaniu [standardowego modułu równoważenia obciążenia i dostępności stref](load-balancer-standard-availability-zones.md)
- Informacje o używaniu [modułu równoważenia obciążenia dla połączeń wychodzących](load-balancer-outbound-connections.md)
- Dowiedz się więcej o [porty wysokiej dostępności usługi równoważenia obciążenia](load-balancer-ha-ports-overview.md)
- Informacje o używaniu [modułu równoważenia obciążenia z wieloma Frontends](load-balancer-multivip-overview.md)
- Dowiedz się więcej o [punktów końcowych usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md)
- Dowiedz się, jak utworzyć [podstawowe publiczny moduł równoważenia obciążenia](load-balancer-get-started-internet-portal.md)

