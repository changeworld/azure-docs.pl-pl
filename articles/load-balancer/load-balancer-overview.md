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
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 080a4e670b06544d84e3d34a0b04bdb91a95aff1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="what-is-azure-load-balancer"></a>Co to jest usługa równoważenia obciążenia Azure?

Z modułem równoważenia obciążenia Azure można skalowanie aplikacji i tworzenie wysokiej dostępności dla usług. Moduł równoważenia obciążenia obsługuje scenariusze dla ruchu przychodzącego i wychodzącego, zapewnia małe opóźnienia i wysokiej wydajności i skalowanie do milionów przepływów dla wszystkich aplikacji TCP i UDP.  

Moduł równoważenia obciążenia dystrybuuje nowych przepływów przychodzących, pojawiające się na frontonu modułu równoważenia obciążenia do wystąpień puli wewnętrznej bazy danych, zgodnie z zasadami i sondy kondycji. 

Ponadto publiczny moduł równoważenia obciążenia może zapewnić połączeń wychodzących na maszynach wirtualnych (VM) w Twojej sieci wirtualnej translacji swoich prywatnych adresów IP na publiczne adresy IP.

Moduł równoważenia obciążenia Azure są dostępne w dwóch jednostki SKU: Basic i Standard. Istnieją różnice w skali, funkcje i cenach. Wszelkie scenariusz, który jest możliwe za pomocą podstawowe usługi równoważenia obciążenia można również tworzyć standardowe usługi równoważenia obciążenia, mimo że metodami może różnić się nieznacznie. Jak dowiedzieć modułu równoważenia obciążenia, należy zapoznać się z podstaw i różnice specyficzny dla jednostki SKU.

## <a name="why-use-load-balancer"></a>Dlaczego warto używać usługi równoważenia obciążenia? 

Można użyć równoważenia obciążenia Azure:

* Równoważenie obciążenia przychodzącego ruchu internetowego do maszyn wirtualnych. Ta konfiguracja jest określana jako [publiczny moduł równoważenia obciążenia](#publicloadbalancer).
* Równoważenie obciążenia ruch na maszynach wirtualnych w ramach sieci wirtualnej. Zapewnia także łączność obciążenia równoważenia frontonu z siecią lokalną w scenariuszu hybrydowym. Oba scenariusze korzysta z konfiguracji znany jako [wewnętrznego modułu równoważenia obciążenia](#internalloadbalancer).
* Ruch do przodu portów do określonego portu na określonych maszynach wirtualnych przy użyciu reguł translacji adresów adresów sieciowych dla ruchu przychodzącego.
* Podaj [łączność wychodząca](load-balancer-outbound-connections.md) dla maszyn wirtualnych w sieci wirtualnej przy użyciu publiczny moduł równoważenia obciążenia.


>[!NOTE]
> Platforma Azure udostępnia mechanizm w pełni zarządzana rozwiązań do równoważenia obciążenia dla scenariuszy. Jeśli szukasz zakończenia protokołu zabezpieczeń warstwy transportu (TLS) ("odciążanie protokołu SSL") lub żądania dla protokołu HTTP/HTTPS, przetwarzania warstwy aplikacji, przejrzyj [brama aplikacji w](../application-gateway/application-gateway-introduction.md). Jeśli szukasz DNS globalnego równoważenia obciążenia, przejrzyj [Traffic Manager](../traffic-manager/traffic-manager-overview.md). Scenariuszy end-to-end mogą korzystać z połączenia tych rozwiązań, zgodnie z potrzebami.

## <a name="what-are-load-balancer-resources"></a>Co to są zasoby usługi równoważenia obciążenia?

Zasób równoważenia obciążenia może istnieć jako publiczny moduł równoważenia obciążenia lub wewnętrzny moduł równoważenia obciążenia. Funkcje zasobów usługi równoważenia obciążenia są wyrażane jako fronton, regułę, sondy kondycji i definicji puli wewnętrznej bazy danych. Maszyn wirtualnych można umieścić w puli zaplecza, określając puli wewnętrznej bazy danych z maszyny Wirtualnej.

Zasoby usługi równoważenia obciążenia to obiekty w ramach których mogą express, jak program jego wielodostępnej infrastrukturze osiągnięcie tego scenariusza, który chcesz utworzyć w usłudze Azure. Nie ma żadnej bezpośredniej zależności między zasobami usługi równoważenia obciążenia i rzeczywistego infrastruktury. Tworzenie modułu równoważenia obciążenia nie tworzy wystąpienie i pojemność są zawsze dostępne. 

## <a name="fundamental-load-balancer-features"></a>Podstawowe funkcje równoważenia obciążenia

Moduł równoważenia obciążenia zapewnia następujące funkcje podstawowe dla aplikacji TCP i UDP:

* **Równoważenie obciążenia**

    Z usługą równoważenia obciążenia Azure można utworzyć regułę równoważenia obciążenia do dystrybucji ruchu, który dociera do serwera sieci Web do wystąpień puli wewnętrznej bazy danych. Moduł równoważenia obciążenia używa algorytmu wyznaczania wartości skrótu na podstawie dystrybucji przepływów ruchu przychodzącego i odpowiednio ponownie zapisuje nagłówki są przesyłane wystąpień puli wewnętrznej bazy danych. Serwer jest dostępny na odbieranie nowych przepływów podczas badania kondycji wskazuje punkt końcowy w dobrej kondycji wewnętrznej bazy danych.
    
    Domyślnie moduł równoważenia obciążenia używa skrót 5-elementowej składa się z źródłowy adres IP, portu źródłowego, docelowego adresu IP, port docelowy i numer protokołu IP do mapowania przepływów dostępne serwery. Istnieje możliwość utworzenia koligacji do określonego źródłowego adresu IP przez Rezygnacja do skrótu podwójnego 2 lub 3 dla danej reguły. Wszystkie pakiety o takim samym przepływie pakietów przychodzących na tym samym wystąpieniu za frontonu równoważeniem obciążenia. Gdy klient inicjuje nowy przepływ z tym samym źródłowy adres IP, zmiany portu źródłowego. W związku z tym krotka 5 może spowodować ruchu przejść do punktu końcowego innej wewnętrznej bazy danych.

    Aby uzyskać więcej informacji, zobacz [tryb dystrybucji modułu równoważenia obciążenia](load-balancer-distribution-mode.md). Poniższa ilustracja przedstawia dystrybucji wyznaczania wartości skrótu:

    ![Dystrybucji wyznaczania wartości skrótu](./media/load-balancer-overview/load-balancer-distribution.png)

    *Rysunek: Hash-based dystrybucji*

* **Przekierowanie portów**

    Z modułem równoważenia obciążenia można utworzyć regułę ruchu przychodzącego translatora adresów Sieciowych do przesyłania dalej ruch na porcie z określonego portu frontonu określonego adresu IP do określonego portu wystąpienia określonego wewnętrznej bazy danych w sieci wirtualnej. Ponadto jest to osiągane przez tego samego dystrybucji bazującego na skrót jako równoważenia obciążenia. Typowe scenariusze dla tej funkcji są sesji protokołu RDP (Remote Desktop) lub protokołu Secure Shell (SSH) do pojedynczych wystąpień maszyn wirtualnych w sieci wirtualnej platformy Azure. Wiele wewnętrznych punktów końcowych można mapować na różne porty na ten sam adres IP frontonu. Można używać ich do zdalnego administrowania w Internecie bez konieczności okno przeskoku dodatkowych maszyn wirtualnych.

* **Aplikacja o niesprecyzowanym i przezroczyste**

    Moduł równoważenia obciążenia nie bezpośrednio współdziała z TCP lub UDP lub warstwy aplikacji i wszelkich TCP lub UDP scenariusza aplikacji mogą być obsługiwane.  Moduł równoważenia obciążenia nie zakończyć lub pochodzą przepływów, interakcji z ładunku przepływu, zapewnia żadnej funkcji bramy warstwy aplikacji, a protokół uzgodnienia zawsze wykonywane bezpośrednio między klientem a wystąpienia puli wewnętrznej bazy danych.  Odpowiedzi na przepływ ruchu przychodzącego jest zawsze odpowiedzi z maszyny wirtualnej.  Po odebraniu przepływ na maszynie wirtualnej, oryginalnego adresu IP źródłowego również są zachowywane.  Kilka przykładów dalsze ilustrujący przezroczystość:
    - Każdy punkt końcowy jest tylko odbierane przez Maszynę wirtualną.  Na przykład uzgadniania TCP zawsze występuje między klientem a wybranego zaplecza maszyny Wirtualnej.  Odpowiedzi na żądanie fronton jest odpowiedzi generowane przez zaplecze maszyny Wirtualnej. Podczas sprawdzania poprawności pomyślnie połączenie frontonu, są sprawdzania poprawności łączności kompleksowe do co najmniej jednej maszyny wirtualnej wewnętrznej bazy danych.
    - Ładunki aplikacji są niewidoczne dla usługi równoważenia obciążenia i wszelkie UDP lub TCP aplikacji mogą być obsługiwane. W przypadku obciążeń, które wymagają na przetwarzanie żądań HTTP lub manipulowania ładunków warstwy aplikacji (na przykład analizowania adresów URL protokołu HTTP), należy użyć modułu równoważenia obciążenia warstwy 7 jak [brama aplikacji w](https://azure.microsoft.com/services/application-gateway).
    - Ponieważ usługi równoważenia obciążenia jest niezależny od ładunku TCP oraz odciążanie protokołu TLS ("SSL") nie zostanie podana, możesz skompilować kompleksowe scenariuszy zaszyfrowanych przy użyciu usługi równoważenia obciążenia i uzyskania dużych skalowania dla aplikacji protokołu TLS przerywa połączenie protokołu TLS w samej maszyny Wirtualnej.  Na przykład czy pojemność klucza sesji TLS jest ograniczona tylko według typu i liczbę maszyn wirtualnych, należy dodać do puli wewnętrznej bazy danych.  Jeśli potrzebujesz "Odciążanie protokołu SSL", sposób postępowania z warstwy aplikacji lub chcesz delegować zarządzanie certyfikatami na platformie Azure, należy użyć modułu równoważenia obciążenia warstwy 7 platformy Azure [brama aplikacji w](https://azure.microsoft.com/services/application-gateway) zamiast tego.
        

* **Automatyczna ponowna konfiguracja**

    Moduł równoważenia obciążenia natychmiast ponownie konfiguruje się wystąpień skalowanie w górę lub w dół. Dodawanie lub usuwanie maszyn wirtualnych z puli zaplecza spowoduje zmianę konfiguracji usługi równoważenia obciążenia bez dodatkowych operacji dla zasobu usługi równoważenia obciążenia.

* **Sondy kondycji**

     Ustalenie kondycji wystąpień w puli zaplecza modułu równoważenia obciążenia używa sondy kondycji, które zostały zdefiniowane. Podczas badania nie odpowiada, usługi równoważenia obciążenia zatrzymuje wysyłanie nowych połączeń do wystąpień złej kondycji. Nie ma wpływu na istniejące połączenia, a użytkownik nadal dopóki aplikacja kończy przepływu, limit czasu bezczynności wystąpi, lub maszyna wirtualna jest zamknięta.

    Obsługiwane są trzy typy sond:

    - **Niestandardowe badanie HTTP**: to sondowanie umożliwia utworzenie niestandardowej logiki do ustalenia stanu wystąpienia puli wewnętrznej bazy danych. Moduł równoważenia obciążenia sondy regularnie punktu końcowego (co 15 sekund, domyślnie). Wystąpienie jest uważana za dobrej kondycji, jeśli odpowie przed upływem limitu czasu (domyślnie 31 sekund) o 200 protokołu HTTP. Sonda niepowodzenie powoduje, że stan dowolnego innego niż 200 protokołu HTTP. To sondowanie jest również przydatna do implementacji logiki można usunąć wystąpienia z obrotu modułu równoważenia obciążenia. Na przykład można skonfigurować wystąpienia próbę zwrócenia stanu z systemem innym niż 200, jeśli wystąpienie jest większa niż 90 procent procesora CPU.  To sondowanie zastępuje domyślną sondę agenta gościa.

    - **Niestandardowe sondowaniem TCP**: sonda zależy od ustanawiania sesji TCP pomyślnie do portu zdefiniowanych sondowania. Tak długo, jak określony odbiornik na maszynie Wirtualnej istnieje, to sondowanie zakończy się pomyślnie. Połączenie zostało odrzucone, sondy kończy się niepowodzeniem. To sondowanie zastępuje domyślną sondę agenta gościa.

    - **Sondowanie agenta gościa (na platforma jako usługa [PaaS] VMs tylko)**: usługi równoważenia obciążenia można również korzystać z agenta gościa w ramach maszyny Wirtualnej. Agent gościa wykrywa i wysyła odpowiedź HTTP 200 OK tylko wtedy, gdy wystąpienie jest w stanie gotowe. Jeśli agent nie odpowiada OK 200 protokołu HTTP, usługi równoważenia obciążenia oznacza wystąpienia jako odpowiadać i zatrzymuje wysyłania ruchu do tego wystąpienia. Moduł równoważenia obciążenia w dalszym ciągu próbują nawiązać połączenie z wystąpieniem. Jeśli agent gościa odpowie 200 protokołu HTTP, usługi równoważenia obciążenia wysyła ruch do tego wystąpienia ponownie. Sondy agenta gościa są ostateczność i nie jest zalecane podczas konfiguracje niestandardowe badania HTTP lub TCP są możliwe. 
    
* **Połączenia wychodzące (SNAT)**

    Wszystkie przepływy ruchu wychodzącego z prywatnych adresów IP w sieci wirtualnej na publiczne adresy IP w Internecie można przekonwertować na adres IP frontonu modułu równoważenia obciążenia. Jeżeli publicznego fronton jest związany z zaplecza maszyny Wirtualnej i reguły równoważenia obciążenia, Azure programów połączeń wychodzących, które mają być automatycznie tłumaczone na publicznego adresu IP frontonu.

    * Włącz łatwe uaktualnianie i odzyskiwania po awarii usługi, ponieważ fronton można dynamicznie mapować do innego wystąpienia usługi.
    * Łatwiejsze zarządzanie kontrolą dostępu listę kontroli dostępu (ACL) do. Listy kontroli dostępu w przeliczeniu na frontonie adresów IP nie należy zmieniać jako usługi skalowania w górę lub w dół, lub pobrać ponownej instalacji.  Translacja połączeń wychodzących na mniejszą liczbę adresów IP, niż maszyny można zmniejszyć obciążenie listę dozwolonych podobnej.

    Aby uzyskać więcej informacji, zobacz [połączeń wychodzących](load-balancer-outbound-connections.md).

Standardowe usługi równoważenia obciążenia ma dodatkowe możliwości specyficzny dla jednostki SKU poza te podstawy. Przejrzyj pozostałej części tego artykułu, aby uzyskać szczegółowe informacje.

## <a name="skus"></a> Porównanie SKU usługi równoważenia obciążenia

Moduł równoważenia obciążenia obsługuje Basic i Standard jednostki SKU, różniących się każdego scenariusza skali, funkcje i cenach. Wszelkie scenariusz, który jest możliwe za pomocą podstawowe usługi równoważenia obciążenia można również utworzyć standardowe usługi równoważenia obciążenia. W rzeczywistości interfejsów API dla obu SKU są podobne i wywoływane przez specyfikację wersji. Interfejs API do obsługi jednostki SKU dla usługi równoważenia obciążenia i publiczny adres IP jest dostępna, począwszy od interfejsu API 2017-08-01. Obu SKU mają tego samego ogólnego interfejsu API i struktury.

Jednak w zależności od wersji produktu można wybrać, konfiguracji całego scenariusza może różnić się nieznacznie. Dokumentacja usługi równoważenia obciążenia wywołuje, gdy artykułu ma zastosowanie tylko do określonej jednostki SKU. Aby porównać i poznać różnice, zobacz poniższą tabelę. Aby uzyskać więcej informacji, zobacz [Omówienie usługi równoważenia obciążenia standardowego](load-balancer-standard-overview.md).

>[!NOTE]
> Jeśli korzystasz z nowszej scenariusz projektu, należy rozważyć użycie standardowego modułu równoważenia obciążenia. 

Autonomiczne maszyny wirtualne, zestawów dostępności i zestawy skalowania maszyny wirtualnej można podłączyć do tylko jednej jednostki SKU, nigdy nie oba. Gdy używasz je z publicznych adresów IP, zarówno usługi równoważenia obciążenia i publiczny adres IP SKU muszą być zgodne. Usługi równoważenia obciążenia i publicznego adresu IP jednostki SKU nie jest modyfikowalna.

_Mimo że nie jest jeszcze obowiązkowe jest najlepszym rozwiązaniem jest jawnie, określ jednostki SKU._  W tym czasie wymagane zmiany są są ograniczone do minimum. Jeśli nie określono jednostki SKU, jest interpretowany jako zamiar przy użyciu wersji interfejsu API 2017-08-01 podstawowy SKU.

>[!IMPORTANT]
>Standardowe usługi równoważenia obciążenia jest nowym produktem modułu równoważenia obciążenia i przede wszystkim jest nadzbiorem podstawowe usługi równoważenia obciążenia. Ma różnic ważne i zamierzonego te dwa produkty. Sytuacja end-to-end jest możliwe podstawowe usługi równoważenia obciążenia można również tworzyć standardowe usługi równoważenia obciążenia. Jeśli jest już używana do podstawowych modułu równoważenia obciążenia, należy zapoznać się z modułem równoważenia obciążenia standardowego zrozumienie najnowsze zmiany w zachowaniu między standardu Basic i ich wpływu. Uważnie przejrzyj tę sekcję.

| | [Standardowy SKU](load-balancer-standard-overview.md) | Podstawowy SKU |
| --- | --- | --- |
| Rozmiar puli wewnętrznej bazy danych | Wystąpienia do 1000. | Wystąpienia do 100. |
| Punkty końcowe puli wewnętrznej bazy danych | Wszystkie maszyny Wirtualnej w ramach jednej sieci wirtualnych, w tym blend maszyn wirtualnych, zestawów dostępności i zestawy skalowania maszyny wirtualnej. | Maszyny wirtualne w jednej dostępności ustawić lub zestawu skalowania maszyn wirtualnych. |
| Strefy dostępności platformy Azure | Strefowo nadmiarowy i zonal końce frontonu dla ruchu przychodzącego i mapowanie przepływu wychodzącego, wychodzący awarie strefy, cross strefy równoważenia obciążenia. | / |
| Diagnostyka | Azure Monitor wielowymiarowych metryk, takich jak bajt i liczniki pakietów, kondycji sondowania stanu, próby połączenia (TCP SYN), kondycja połączenia wychodzącego (SNAT udane i nieudane przepływy), pomiarów płaszczyzny aktywnych danych. | Azure Log Analytics dla publicznego załadować tylko równoważenia, alert wyczerpania SNAT, liczba kondycji puli wewnętrznej bazy danych. |
| HA portów | Wewnętrzny moduł równoważenia obciążenia. | / |
| Bezpieczeństwo domyślne | Domyślnie zamknięcie dla publicznego adresu IP i obciążenia równoważenia punktów końcowych. Na przepływ ruchu sieciową grupę zabezpieczeń należy jawnie dozwolonych podmiotów. | Otwórz domyślne grupy zabezpieczeń sieci jest opcjonalna. |
| Połączenia wychodzące | Wiele przodu kończy Wypisz rule. Scenariusz wychodzących _musi_ jawnie można utworzyć maszyny wirtualnej można było użyć łączność wychodząca. [Punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) bez połączenia wychodzącego jest osiągalna, a nie są wliczane do przetwarzania danych. Wszystkie publiczne adresy IP, takich jak usługi Azure PaaS, które nie są dostępne jako punktów końcowych usługi sieci wirtualnej, musi zostać osiągnięta poprzez łączność wychodząca i liczba kierunku dane przetworzone. Gdy tylko do wewnętrznego modułu równoważenia obciążenia działa jako maszyna wirtualna, połączeń wychodzących przy użyciu domyślnego SNAT są niedostępne. Wychodzące programowania SNAT jest protokół transportu określonej na podstawie protokołu przychodzącą regułę równoważenia obciążenia. | Pojedynczy frontonu, losowo wybrany, gdy istnieją wielu front kończy się. Gdy tylko do wewnętrznego modułu równoważenia obciążenia działa jako maszyna wirtualna, używana jest domyślna SNAT. |
| Wiele front kończy się | Przychodzący i wychodzący. | Tylko przychodzące. |
| Operacje zarządzania | Większość operacji < 30 sekund. | 60-90 sekund typowych. |
| Umowa SLA | 99,99% dla ścieżki danych z dwóch maszyn wirtualnych w dobrej kondycji. | Niejawne w umowie SLA maszyny Wirtualnej. | 
| Cennik | Opłaty są oparte na liczbę reguł i dane przetworzone ruchu przychodzącego lub wychodzącego skojarzonych z zasobem.  | Bez dodatkowych opłat. |

Aby uzyskać więcej informacji, zobacz [usługi limity dla usługi równoważenia obciążenia](https://aka.ms/lblimits). Szczegółowe informacje standardowe usługi równoważenia obciążenia, zobacz [omówienie](load-balancer-standard-overview.md), [cennik](https://aka.ms/lbpricing), i [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Pojęcia

### <a name = "publicloadbalancer"></a>Publiczny moduł równoważenia obciążenia

Publiczny moduł równoważenia obciążenia mapuje publiczny adres IP i port numer ruchu przychodzącego prywatnego adresu IP adres i numer portu maszynę Wirtualną i odwrotnie dla ruchu odpowiedzi z maszyny Wirtualnej. Stosując reguły równoważenia obciążenia wielu maszyn wirtualnych lub usług można rozpowszechniać określonych rodzajów ruchu. Na przykład mogą rozprzestrzeniać się obciążenie ruchu w sieci web żądania na wielu serwerach sieci web.

Na poniższej ilustracji przedstawiono punktu końcowego równoważeniem obciążenia dla ruchu w sieci web współużytkowany trzech maszyn wirtualnych dla publicznych i prywatnych port TCP 80. Te trzy maszyny wirtualne są w zestawie o zrównoważonym obciążeniu.

![przykład modułu równoważenia obciążenia publiczny](./media/load-balancer-overview/IC727496.png)

*Rysunek: Ładowanie równoważenia ruchu w sieci web za pomocą publiczny moduł równoważenia obciążenia*

Gdy klienci internetowi wysyłać żądania dotyczące stron internetowych do publicznego adresu IP aplikacji sieci web na porcie TCP 80, moduł równoważenia obciążenia Azure rozdzielaj żądania między trzech maszyn wirtualnych w zestawie równoważeniem obciążenia. Aby uzyskać więcej informacji dotyczących algorytmów równoważenia obciążenia, zobacz [funkcji równoważenia obciążenia](load-balancer-overview.md##fundamental-load-balancer-features) sekcji tego artykułu.

Domyślnie usługa równoważenia obciążenia Azure dystrybuuje ruch sieciowy równomiernie wielu wystąpień maszyny Wirtualnej. Można również skonfigurować koligację sesji. Aby uzyskać więcej informacji, zobacz [tryb dystrybucji modułu równoważenia obciążenia](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Wewnętrzny moduł równoważenia obciążenia

Wewnętrzny moduł równoważenia obciążenia kieruje ruch tylko do zasobów, które znajdują się wewnątrz sieci wirtualnej lub wykorzystujących sieci VPN uzyskiwać dostęp do infrastruktury platformy Azure. W związku z tym wewnętrznego modułu równoważenia obciążenia różni się od publiczny moduł równoważenia obciążenia. Infrastruktury platformy Azure ogranicza dostęp do serwera sieci Web z równoważeniem obciążenia adresów IP sieci wirtualnej. Adresy IP frontonu i sieci wirtualne są nigdy nie bezpośrednio widoczne dla punktu końcowego internet. Wewnętrznych aplikacji biznesowych — Uruchom na platformie Azure i są dostępne z platformy Azure lub z lokalnymi zasobami.

Wewnętrzny moduł równoważenia obciążenia zapewnia następujące typy równoważenia obciążenia:

* **W ramach sieci wirtualnej**: równoważenia obciążenia z maszyn wirtualnych w sieci wirtualnej do zestawu maszyn wirtualnych znajdujących się w tej samej sieci wirtualnej.
* **Dla sieci wirtualnej między lokalizacjami**: załadować równoważenia z komputerów lokalnych do wielu maszyn wirtualnych znajdujących się w tej samej sieci wirtualnej. 
* **Dla aplikacji wielowarstwowych**: równoważenia obciążenia dla aplikacji wielowarstwowych internetowy gdy warstw wewnętrznej bazy danych nie są skierowane do Internetu. Poziomy wewnętrznej bazy danych wymagają, równoważenie obciążenia ruchu z Internetem warstwy (zobacz następny rysunek).
* **W przypadku aplikacji biznesowych z**: równoważenia obciążenia dla aplikacji — biznesowych, które są obsługiwane na platformie Azure, bez dodatkowego obciążenia równoważenia sprzętu lub oprogramowania. Taki scenariusz obejmuje serwery lokalne, które znajdują się w zestawie komputerów, których ruch jest równoważeniem obciążenia.

![przykład modułu równoważenia obciążenia wewnętrznego](./media/load-balancer-overview/IC744147.png)

*Rysunek: Funkcji równoważenia obciążenia wielowarstwowe aplikacje przy użyciu obu usług równoważenia obciążenia publicznych oraz wewnętrznych*

## <a name="pricing"></a>Cennik
Standardowego użycia usługi równoważenia obciążenia jest pobierana w zależności od liczby skonfigurowanych reguł równoważenia obciążenia i ilości przetworzonych danych przychodzących i wychodzących. Dla usługi równoważenia obciążenia standardowego cennik informacji, przejdź do [cennik usługi równoważenia obciążenia](https://azure.microsoft.com/pricing/details/load-balancer/) strony.

Podstawowe usługi równoważenia obciążenia jest oferowane bezpłatnie.

## <a name="sla"></a>Umowa SLA

Aby uzyskać informacje o standardowych umowy SLA dla usługi równoważenia obciążenia, przejdź do [umowy SLA dla usługi równoważenia obciążenia](https://aka.ms/lbsla) strony. 

## <a name="limitations"></a>Ograniczenia

- Moduł równoważenia obciążenia jest produktem TCP lub UDP do równoważenia obciążenia i portu przekazywania tych określonych protokołów IP.  Reguły równoważenia obciążenia i reguły NAT ruchu przychodzącego są obsługiwane w przypadku protokołu TCP i UDP i nie są obsługiwane przez inne protokoły IP, w tym protokołu ICMP. Moduł równoważenia obciążenia nie zakończy, odpowiadać lub inny sposób interakcji z ładunku przepływu UDP lub TCP. Nie jest serwer proxy. Sprawdzeniu poprawności łączności frontonu musi mieć miejsce w paśmie z tego samego protokołu, które są używane w równoważenia lub przychodzącej reguły NAT obciążenia (TCP lub UDP) _i_ co najmniej jeden z maszyn wirtualnych należy wygenerować odpowiedzi dla klientów Zobacz na odpowiedź z serwera sieci Web.  Nie odbiera odpowiedź wewnątrzpasmowe z frontonu modułu równoważenia obciążenia oznacza, że żadne maszyny wirtualne zostały mogą odpowiadać.  Nie jest możliwe do interakcji z frontonu modułu równoważenia obciążenia bez mogą odpowiadać maszyny wirtualnej.  Dotyczy to również połączenia wychodzące gdzie [maskowaniu portu SNAT](load-balancer-outbound-connections.md#snat) jest obsługiwana tylko w przypadku protokołu TCP i UDP; żadnych innych protokołów IP ICMP w tym również zakończy się niepowodzeniem.  Przypisz poziomie wystąpienia publicznego adresu IP do ograniczenia.
- W przeciwieństwie do publicznej usługi równoważenia obciążenia, która zapewnia [połączeń wychodzących](load-balancer-outbound-connections.md) podczas przejścia z prywatnych adresów IP w sieci wirtualnej na publiczne adresy IP, wewnętrzne moduły równoważenia obciążenia nie tłumaczenia wychodzącego pochodzi połączenia frontonu wewnętrznego modułu równoważenia obciążenia jako zarówno są w prywatnej przestrzeni adresów IP.  Dzięki temu można uniknąć potencjalnych wyczerpania SNAT wewnątrz Unikatowy wewnętrzny przestrzeń adresową gdzie tłumaczenia nie jest wymagana.  Efektem ubocznym jest to, że jeśli przepływu wychodzącego z maszyn wirtualnych w puli zaplecza prób przepływu na frontonie wewnętrzny moduł równoważenia obciążenia w puli, która znajduje się _i_ jest zamapowana do samej siebie, nie są zgodne oba etapy przepływu i przepływ zakończy się niepowodzeniem.  Jeśli przepływ mapują wróć do tej samej maszyny Wirtualnej w puli zaplecza, który utworzył przepływu frontonu, przepływ zostanie wykonana pomyślnie.   Przepływ mapy do samej siebie przepływu wychodzącego prawdopodobnie wynikają z maszyny Wirtualnej z serwera sieci Web, a odpowiedni przepływu ruchu przychodzącego wydaje się pochodzą z maszyny Wirtualnej do samej siebie. Z punktu widzenia systemu operacyjnego gościa na maszynie wirtualnej nie zgadzają się części ruchu przychodzącego i wychodzącego z takim samym przepływie. Stosu TCP nie rozpozna tych połowy takim samym przepływie jako część tego samego przepływu zgodnie z serwera źródłowego i docelowego nie są zgodne.  Przepływ, mapy do innych maszyn wirtualnych w puli zaplecza, połowy przepływ będą zgodne, a maszyna wirtualna może pomyślnie odpowiedzieć przepływu.  Objaw dla tego scenariusza jest sporadyczne połączenia przekroczeń limitu czasu. Istnieją kilka typowych obejścia niezawodny sposób realizacji tego scenariusza (pochodzące z są przesyłane do puli wewnętrznej bazy danych zaplecza pule odpowiednich wewnętrznego modułu równoważenia obciążenia frontonu) zawierające albo wstawiania za obciążenia wewnętrznego serwera proxy innych firm Moduł równoważenia lub [przy użyciu reguł stylu DSR](load-balancer-multivip-overview.md).  Publiczny moduł równoważenia obciążenia można użyć do ograniczenia, wynikowy scenariusz jest podatna na [wyczerpania SNAT](load-balancer-outbound-connections.md#snat) i należy unikać, chyba że dokładnie zarządzane.

## <a name="next-steps"></a>Kolejne kroki

Omówienie usługi równoważenia obciążenia Azure jest teraz dostępna. Aby rozpocząć używanie modułu równoważenia obciążenia, utwórz je, Tworzenie maszyn wirtualnych z zainstalowane niestandardowe rozszerzenie usług IIS i równoważenie obciążenia aplikacji sieci web między maszynami wirtualnymi. Aby dowiedzieć się więcej, zobacz temat [utworzyć podstawowe usługi równoważenia obciążenia](quickstart-create-basic-load-balancer-portal.md) Szybki Start.
