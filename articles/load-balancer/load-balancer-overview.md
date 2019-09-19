---
title: Co to jest usługa Azure Load Balancer?
titlesuffix: Azure Load Balancer
description: Omówienie funkcji, architektury i implementacji usługi Azure Load Balancer. Dowiedz się, jak działa moduł równoważenia obciążenia i wykorzystaj go w chmurze.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/11/2019
ms.author: allensu
ms.openlocfilehash: 349d8afd46a06455edcd25e2a7ea48f407d285ef
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130429"
---
# <a name="what-is-azure-load-balancer"></a>Co to jest usługa Azure Load Balancer?

Za pomocą usługi Azure Load Balancer możesz skalować aplikacje i zapewniać wysoką dostępność swoich usług. Moduł równoważenia obciążenia obsługuje scenariusze ruchu przychodzącego i wychodzącego, udostępnia małe opóźnienia i wysoką przepływność oraz skaluje nawet miliony przepływów dla wszystkich aplikacji protokołu TCP i UDP.  

Moduł ten dystrybuuje nowe przepływy przychodzące do jego frontonu do wystąpień pól zaplecza zgodnie z zasadami i sondami kondycji. 

Ponadto publiczny moduł równoważenia obciążenia zapewnia połączenia wychodzące dla maszyn wirtualnych wewnątrz sieci wirtualnej, tłumacząc ich prywatne adresy IP na publiczne adresy IP.

Usługa Azure Load Balancer jest dostępna w formie dwóch jednostek SKU: Podstawowa i Standardowa. Istnieją różnice w ich skalowalności, funkcjach i cenach. Wszystkie scenariusze możliwe do wykonania za pomocą modułu równoważenia obciążenia w warstwie Podstawowa mogą zostać również wykonane przy użyciu modułu równoważenia obciążenia w warstwie Standardowa, ale podejścia mogą się nieznacznie różnić. Podczas zdobywania doświadczenia w korzystaniu z modułu równoważenia obciążenia ważne jest, aby zapoznać się z podstawowymi informacjami i różnicami specyficznymi dla danej jednostki SKU.

## <a name="why-use-load-balancer"></a>Dlaczego warto używać modułu równoważenia obciążenia? 

Usługa Azure Load Balancer umożliwia:

* Równoważenie obciążenia przychodzącego ruchu internetowego do maszyn wirtualnych. Taka konfiguracja jest określana jako [publiczny moduł równoważenia obciążenia](#publicloadbalancer).
* Równoważenie obciążenia związanego z ruchem w obrębie maszyn wirtualnych w sieci wirtualnej. Możesz również uzyskać dostęp do frontonu modułu równoważenia obciążenia z sieci lokalnej w scenariuszu hybrydowym. Oba scenariusze korzystają z konfiguracji, która jest znana jako [wewnętrzny moduł równoważenia obciążenia](#internalloadbalancer).
* Przekazywanie ruchu do określonego portu w ramach określonych maszyn wirtualnych za pomocą reguł translatora adresów sieciowych.
* Zapewnianie [łączności wychodzącej](load-balancer-outbound-connections.md) dla maszyn wirtualnych w ramach Twojej sieci wirtualnej przy użyciu publicznego modułu równoważenia obciążenia.


>[!NOTE]
> Platforma Azure udostępnia zestaw w pełni zarządzanych rozwiązań do równoważenia obciążenia dla Twoich scenariuszy. Jeśli chcesz zakończyć protokół zabezpieczeń TLS (Transport Layer Security) („odciążanie protokołu SSL”) lub przetwarzanie poszczególnych żądań dotyczących protokołu HTTP/HTTPS na poziomie warstwy aplikacji, zapoznaj się z tematem dotyczącym usługi [Application Gateway](../application-gateway/application-gateway-introduction.md). Jeśli szukasz globalnego modułu równoważenia obciążenia systemu DNS, zapoznaj się z tematem dotyczącym usługi [Traffic Manager](../traffic-manager/traffic-manager-overview.md). Scenariusze kompleksowe mogą w razie potrzeby korzystać z zalet łączenia tych rozwiązań.

## <a name="what-are-load-balancer-resources"></a>Czym są zasoby modułu równoważenia obciążenia?

Zasób modułu równoważenia obciążenia może istnieć jako publiczny moduł równoważenia obciążenia lub wewnętrzny moduł równoważenia obciążenia. Funkcje zasobów modułu równoważenia są wyrażane jako definicje frontonu, reguły, sondy kondycji i puli zaplecza. Możesz umieścić maszyny wirtualnych w puli zaplecza, określając pulę zaplecza z poziomu maszyny wirtualnej.

Zasoby modułu równoważenia obciążenia to obiekty, w ramach których możesz określić sposób, w jaki platforma Azure ma zaprogramować swoją infrastrukturę z wieloma dzierżawami w celu osiągnięcia scenariusza, który zamierz utworzyć. Nie ma bezpośredniej relacji między zasobami modułu równoważenia obciążenia i rzeczywistą infrastrukturą. Tworzenie modułu równoważenia obciążenia nie powoduje utworzenia wystąpienia, a pojemność jest zawsze dostępna. 

## <a name="fundamental-load-balancer-features"></a>Podstawowe funkcje modułu równoważenia obciążenia

Moduł równoważenia obciążenia zapewnia następujące podstawowe możliwości na potrzeby aplikacji protokołu TCP i UDP:

* **Równoważenie obciążenia**

    Za pomocą usługi Azure Load Balancer możesz utworzyć regułę równoważenia obciążenia, aby dystrybuować ruch przychodzący do frontonu do wystąpień pól zaplecza. Moduł równoważenia obciążenia używa algorytmu bazującego na skrótach do dystrybucji przepływów przychodzących i odpowiednio przepisuje nagłówki przepływów do wystąpień puli zaplecza. Serwer może odbierać nowe przepływy, gdy sonda kondycji wskazuje punkt końcowy zaplecza w dobrej kondycji.
    
    Domyślnie moduł równoważenia obciążenia używa skrótu składającego się z pięciu krotek (źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy i numer protokołu IP) służących do mapowania przepływów na dostępne serwery. Istnieje możliwość utworzenia koligacji z określonym źródłowym adresem IP, decydując się na użycie skrótu składającego się z dwóch lub trzech krotek dla danej reguły. Wszystkie pakiety z tego samego przepływu pakietów trafiają do tego samego wystąpienia w ramach frontonu z równoważeniem obciążenia. Gdy klient inicjuje nowy przepływ z tego samego źródłowego adresu IP port źródłowy ulega zmianie. W rezultacie skrót składający się z pięciu krotek może spowodować skierowanie ruchu do innego punktu końcowego zaplecza.

    Aby uzyskać więcej informacji, zobacz [Load Balancer distribution mode (Tryb dystrybucji modułu równoważenia obciążenia)](load-balancer-distribution-mode.md). Na poniższej ilustracji przedstawiono dystrybucję opartą na skrótach:

    ![Dystrybucja oparta na skrótach](./media/load-balancer-overview/load-balancer-distribution.png)

    *Rysunek: dystrybucja oparta na skrótach*

* **Przekierowywanie portów**

    Za pomocą modułu równoważenia obciążenia można utworzyć regułę NAT dla ruchu przychodzącego w celu przekazywania ruchu z określonego portu konkretnego adresu IP frontonu do określonego portu wystąpienia zaplecza w ramach sieci wirtualnej. Odbywa się również przy użyciu takiej samej dystrybucji opartej na skrótach, co w przypadku równoważenia obciążenia. Typowe scenariusze dotyczące tej funkcji to sesje protokołu Remote Desktop Protocol (RDP) lub protokołu Secure Shell (SSH) względem poszczególnych wystąpień maszyn wirtualnych w ramach usługi Azure Virtual Network. Istnieje możliwość zamapowania wielu wewnętrznych punktów końcowych na różne porty w ramach tego samego adresu IP frontonu. Adresów IP frontonu można użyć do zdalnego administrowania maszynami wirtualnymi przez Internet bez konieczności stosowania dodatkowego serwera przesiadkowego.

* **Niezależny od aplikacji i przezroczysty**

    Moduł równoważenia obciążenia nie współdziała bezpośrednio z protokołami TCP i UDP ani warstwą aplikacji, ale wszelkie scenariusze aplikacji dotyczące protokołów TCP lub UDP mogą być obsługiwane.  Moduł równoważenia obciążenia nie powoduje kończenia lub tworzenia przepływów, nie wchodzi w interakcje z ładunkiem przepływu, nie zapewnia żadnej funkcji bramy warstwy aplikacji, natomiast uzgodnienia protokołu zawsze mają miejsce bezpośrednio między klientem a wystąpieniem puli zaplecza.  Odpowiedź na przepływ przychodzący to zawsze odpowiedź z maszyny wirtualnej.  Po dostarczeniu przepływu do maszyny wirtualnej oryginalny źródłowy adres IP również jest zachowywany.  Kilka przykładów w celu dalszego zilustrowania przezroczystości:
    - Na każdy punkt końcowy odpowiada tylko maszyna wirtualna.  Na przykład uzgadnianie protokołu TCP zawsze ma miejsce między klientem a wybranym zapleczem maszyny wirtualnej.  Odpowiedź na żądanie względem frontonu to odpowiedź generowana przez maszynę wirtualną zaplecza. Po pomyślnym zweryfikowaniu łączności z frontonem ma miejsce weryfikacja kompleksowej łączności z co najmniej jedną maszyną wirtualną zaplecza.
    - Ładunki aplikacji są niewidoczne dla modułu równoważenia obciążenia i obsługiwana może być dowolna aplikacja protokołu UDP lub TCP. W przypadku obciążeń wymagających przetwarzania żądań HTTP lub manipulowania ładunkami warstwy aplikacji (na przykład analizowania adresów URL protokołu HTTP) należy użyć modułu równoważenia obciążenia warstwy 7, takiego jak usługa [Application Gateway](https://azure.microsoft.com/services/application-gateway).
    - Ponieważ moduł równoważenia obciążenia jest niezależny od ładunku protokołu TCP, a odciążanie protokołu TLS („SSL”) nie zostało udostępnione, możesz tworzyć kompleksowe zaszyfrowane scenariusze za pomocą modułu równoważenia obciążenia i uzyskać znaczące skalowanie w poziomie na potrzeby aplikacji protokołu TLS, przerywając połączenie protokołu TLS na maszynie wirtualnej.  Na przykład pojemność obsługi kluczy sesji TLS jest ograniczona tylko przez typ i liczbę maszyn wirtualnych dodawanych do puli zaplecza.  Jeśli potrzebujesz „odciążania protokołu SSL”, obsługę warstwy aplikacji lub chcesz oddelegować zarządzanie certyfikatami na platformę Azure, skorzystaj zamiast tego z modułu równoważenia obciążenia platformy Azure warstwy 7, czyli usługi [Application Gateway](https://azure.microsoft.com/services/application-gateway).
        

* **Rekonfiguracja automatyczna**

    Moduł równoważenia obciążenia samodzielnie się rekonfiguruje po przeskalowaniu wystąpienia w górę lub w dół. Dodawanie lub usuwanie maszyn wirtualnych z puli zaplecza powoduje rekonfigurację modułu równoważenia obciążenia bez przeprowadzania dodatkowych operacji w ramach zasobu modułu równoważenia obciążenia.

* **Sondy kondycji**

    Aby określić kondycję wystąpień w puli zaplecza, moduł równoważenia obciążenia używa sond kondycji zdefiniowanych przez użytkownika. Jeśli sonda nie odpowiada, moduł równoważenia obciążenia zaprzestaje inicjowania nowych połączeń z wystąpieniami o złej kondycji. Nie wpływa to na istniejące połączenia, które działają do momentu zakończenia przepływu przez aplikację, przekroczenia limitu czasu lub zamknięcia maszyny wirtualnej.
     
    Moduł równoważenia obciążenia udostępnia [różne typy sond kondycji](load-balancer-custom-probe-overview.md#types) dla punktów końcowych protokołu TCP, HTTP i HTTPS.

    Ponadto podczas korzystania z usług w chmurze klasycznej dozwolony jest dodatkowy typ:  [agent gościa](load-balancer-custom-probe-overview.md#guestagent).  Ta sonda kondycji powinna być używana w ostateczności i nie jest zalecana, gdy możliwe jest użycie innych opcji.
    
* **Połączenia wychodzące (SNAT)**

    Wszystkie przepływy wychodzące z prywatnych adresów IP w ramach Twojej sieci wirtualnej do publicznych adresów IP w Internecie mogą zostać przetłumaczone na adresy IP frontonu modułu równoważenia obciążenia. Gdy fronton publiczny jest powiązany z maszyną wirtualną zaplecza za pomocą reguły równoważenia obciążenia, platforma Azure programuje połączenia wychodzące w taki sposób, aby były automatycznie tłumaczone na publiczny adres IP frontonu.

  * Włącz łatwe uaktualnianie i odzyskiwania po awarii usług ze względu na możliwość mapowania frontonu na inne wystąpienie usługi.
  * Łatwiejsze zarządzanie listami kontroli dostępu. Listy kontroli dostępu określane jako adresy IP frontonu nie ulegają zmianie w przypadku skalowania usług w górę lub w dół bądź ich ponownego wdrażania.  Tłumaczenie połączeń wychodzących na mniejszą liczbę adresów IP niż liczba maszyn może zmniejszyć obciążenie związane z umieszczaniem na białej liście.

    Aby uzyskać więcej informacji, zapoznaj się z tematem dotyczącym [połączeń wychodzących](load-balancer-outbound-connections.md).

Moduł równoważenia obciążenia w warstwie Standardowa poza podstawowymi ma dodatkowe funkcje specyficzne dla jednostki SKU. Przejrzyj pozostałą część tego artykułu, aby uzyskać szczegółowe informacje.

## <a name="skus"></a> Porównanie jednostek SKU modułu równoważenia obciążenia

Moduł równoważenia obciążenia obsługuje jednostkę SKU Podstawowa i Standardowa, które różnią się od siebie pod względem scenariuszy skalowania, funkcji i ceny. Wszystkie scenariusze możliwe do wykonania za pomocą modułu równoważenia obciążenia w warstwie Podstawowa mogą zostać również wykonane przy użyciu modułu równoważenia obciążenia w warstwie Standardowa. W rzeczywistości interfejsy API dla obu tych jednostek SKU są podobne i wywoływane za pośrednictwem specyfikacji tych jednostek. Interfejs API służący do obsługi jednostek SKU dla modułu równoważenia obciążenia i publicznego adresu IP jest dostępny od wersji 2017-08-01 interfejsu API. Obie jednostki SKU mają ten sam ogólny interfejs API i tę samą strukturę.

Jednak w zależności od wybranej jednostki SKU cały scenariusz konfiguracji może się nieco różnić. W dokumentacji modułu równoważenia obciążenia zaznaczone jest, gdy dany artykuł ma zastosowanie tylko do określonej jednostki SKU. Aby porównać i poznać różnice, zapoznaj się z poniższą tabelą. Aby uzyskać więcej informacji, zobacz [Omówienie modułu równoważenia obciążenia w warstwie Standardowa](load-balancer-standard-overview.md).

>[!NOTE]
> Nowe projekty powinny zostać dostosowane do modułu równoważenia obciążenia w warstwie Standardowa. 

Autonomiczne maszyny wirtualne, zestawy dostępności i zestawy skalowania maszyn wirtualnych można połączyć tylko jednej jednostki SKU — nigdy do obu. Gdy używasz autonomicznych maszyn wirtualnych z publicznymi adresami IP, jednostki SKU modułu równoważenia obciążenia i publicznego adresu IP muszą być zgodne. Jednostek SKU modułu równoważenia obciążenia i publicznego adresu IP nie można modyfikować.

_Najlepszym rozwiązaniem jest jawne określenie jednostek SKU nawet wtedy, gdy nie jest to obowiązkowe._  W tej chwili wymagane zmiany są ograniczane do minimum. Jeśli nie określono jednostki SKU, jest to interpretowane jako zamiar użycia interfejsu API w wersji 2017-08-01 jednostki SKU Podstawowa.

>[!IMPORTANT]
>Moduł równoważenia obciążenia w warstwie Standardowa to nowy produkt służący do równoważenia obciążenia, który jest w dużym stopniu nadzbiorem modułu równoważenia obciążenia w warstwie Podstawowa. Istnieją ważne i zamierzone różnice między tymi dwoma produktami. Każdy kompleksowy scenariusz, który można utworzyć za pomocą modułu równoważenia obciążenia w warstwie Podstawowa można również utworzyć za pomocą modułu równoważenia obciążenia w warstwie Standardowa. Jeśli masz już doświadczenie w korzystaniu z modułu równoważenia obciążenia w warstwie Podstawowa, zalecane jest zapoznanie się z modułem równoważenia obciążenia w warstwie Standardowa, aby poznać najnowsze zmiany zachowania w warstwach Standardowa i Podstawowa oraz ich konsekwencje. Uważnie przejrzyj tę sekcję.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Aby uzyskać więcej informacji, zapoznaj się z tematem dotyczącym [limitów usługi dla modułu równoważenia obciążenia](https://aka.ms/lblimits). Szczegółowe informacje dotyczące modułu równoważenia obciążenia w warstwie Standardowa zawiera [przegląd](load-balancer-standard-overview.md), [cennik](https://aka.ms/lbpricing) i [umowa dotycząca poziomu usług](https://aka.ms/lbsla).

## <a name="concepts"></a>Pojęcia

### <a name = "publicloadbalancer"></a>Publiczny moduł równoważenia obciążenia

Publiczny moduł równoważenia obciążenia mapuje publiczny adres IP i numer portu ruchu przychodzącego na prywatny adres IP oraz numer portu maszyny wirtualnej i na odwrót w przypadku ruchu odpowiedzi z maszyny wirtualnej. Stosując reguły równoważenia obciążenia, można rozpowszechniać określone typy ruchu między wiele maszyn wirtualnych lub usług. Na przykład można rozłożyć obciążenie ruchu związanego z żądaniami internetowymi na wiele serwerów internetowych.

Na poniższej ilustracji przedstawiono punkt końcowy z równoważeniem obciążenia dla ruchu internetowego, który jest udostępniany publicznie między trzy maszyny wirtualne na potrzeby komunikacji publicznej i port 80 protokołu TCP. Te trzy maszyny wirtualne znajdują się w zestawie z równoważeniem obciążenia.

![Przykład publicznego modułu równoważenia obciążenia](./media/load-balancer-overview/IC727496.png)

*Rysunek: równoważenie obciążenia ruchu internetowego za pomocą publicznego modułu równoważenia obciążenia*

Gdy klienci internetowi wysyłają żądania dotyczące strony internetowej do publicznego adresu IP na porcie 80 protokołu TCP, usługa Azure Load Balancer dystrybuuje żądania na trzy maszyny wirtualne w zestawie z równoważeniem obciążenia. Aby uzyskać więcej informacji na temat algorytmów równoważenia obciążenia, zobacz sekcję [Funkcje modułu równoważenia obciążenia](load-balancer-overview.md##fundamental-load-balancer-features) w tym artykule.

Domyślnie usługa Azure Load Balancer dystrybuuje ruch sieciowy równomiernie między wiele wystąpień maszyn wirtualnych. Można również skonfigurować koligację sesji. Aby uzyskać więcej informacji, zobacz [Load Balancer distribution mode (Tryb dystrybucji modułu równoważenia obciążenia)](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Wewnętrzny moduł równoważenie obciążenia

Wewnętrzny moduł równoważenia obciążenia kieruje ruch tylko do zasobów, które znajdują się w ramach sieci wirtualnej lub które korzystają z sieci VPN w celu uzyskania dostępu do infrastruktury platformy Azure. W związku z tym wewnętrzny moduł równoważenia obciążenia różni się od publicznego modułu równoważenia obciążenia. Infrastruktura platformy Azure ogranicza dostęp do adresów IP frontonu z równoważeniem obciążenia w sieci wirtualnej. Adresy IP frontonu i sieci wirtualne nigdy nie są bezpośrednio widoczne dla internetowego punktu końcowego. Wewnętrzne aplikacje biznesowe są uruchomiane na platformie Azure i dostęp do nich jest uzyskiwany z poziomu platformy Azure lub zasobów lokalnych.

Wewnętrzny moduł równoważenia obciążenia pozwala na następujące typy równoważenia obciążenia:

* **Z poziomu sieci wirtualnej**: równoważenie obciążenia z poziomu maszyn wirtualnych w sieci wirtualnej do zestawu maszyn wirtualnych, które znajdują się w tej samej sieci wirtualnej.
* **Dla sieci wirtualnej obejmującej wiele lokalizacji**: równoważenie obciążenia z poziomu komputerów lokalnych do zestawu maszyn wirtualnych, które znajdują się w tej samej sieci wirtualnej. 
* **Dla aplikacji wielowarstwowych**: równoważenia obciążenia dla aplikacji wielowarstwowych dostępnych z Internetu, gdy warstwy zaplecza są niedostępne z Internetu. Warstwy zaplecza wymagają równoważenia obciążenia ruchu z poziomu warstwy z dostępem do Internetu (patrz następny rysunek).
* **Dla aplikacji biznesowych**: równoważenie obciążenia na potrzeby aplikacji biznesowych, które są hostowane na platformie Azure bez użycia dodatkowego sprzętu lub oprogramowania służącego do równoważenia obciążenia. Ten scenariusz obejmuje serwery lokalne znajdujące się w ramach zestawu komputerów, w przypadku których ma miejsce równoważenie obciążenia ruchu.

![Przykład wewnętrznego modułu równoważenie obciążenia](./media/load-balancer-overview/IC744147.png)

*Rysunek: równoważenie obciążenia aplikacji wielowarstwowych za pomocą publicznego i wewnętrznego modułu równoważenia obciążenia*

## <a name="pricing"></a>Cennik

Usługa Load Balancer w warstwie Standardowa jest płatna.

- Liczba skonfigurowanych reguł równoważenia obciążenia i reguł dla ruchu wychodzącego (reguły NAT dla ruchu przychodzącego nie są wliczane do łącznej liczby reguł).
- Ilość przetworzonych danych dla ruchu przychodzącego i wychodzącego niezależnie od reguły. 

Aby uzyskać informacje na temat cen modułu równoważenia obciążenia w warstwie Standardowa, przejdź na stronę [cennika modułu równoważenia obciążenia](https://azure.microsoft.com/pricing/details/load-balancer/).

Za użycie modułu równoważenia obciążenia w warstwie Podstawowa nie są naliczane opłaty.

## <a name="sla"></a>Umowa SLA

Aby uzyskać informacje o umowie dotyczącej poziomu usług dla modułu równoważenia obciążenia w warstwie Standardowa, przejdź na stronę [umowy dotyczącej poziomu usług dla modułu równoważenia obciążenia](https://aka.ms/lbsla). 

## <a name="limitations"></a>Ograniczenia

- Moduł równoważenia obciążenia to produkt protokołu TCP lub UDP służący do równoważenia obciążenia i przekierowania portów na potrzeby tych konkretnych protokołów IP.  Reguły równoważenia obciążenia i reguły NAT dla ruchu przychodzącego są obsługiwane dla protokołów TCP i UDP, ale nie są obsługiwane dla innych protokołów IP, w tym protokołu ICMP. Moduł równoważenia obciążenia nie kończy, nie odpowiada ani w żaden inny sposób nie wchodzi w interakcję z ładunkiem przepływu protokołu UDP ani TCP. Nie jest to serwer proxy. Pomyślna weryfikacja łączności z frontonem musi mieć miejsce w sposób wewnątrzpasmowy za pomocą tego samego protokołu, który został użyty na potrzeby równoważenia obciążenia lub reguły NAT dla ruchu przychodzącego (protokół TCP lub UDP), _i_ co najmniej jedna z maszyn wirtualnych musi wygenerować odpowiedzi, aby dla klienta widoczna była odpowiedź frontonu.  Brak otrzymania odpowiedzi wewnątrzpasmowej z frontonu modułu równoważenia obciążenia oznacza, że żadne maszyny wirtualne nie mogły udzielić odpowiedzi.  Nie jest możliwa interakcja z frontonem modułu równoważenia obciążenia bez maszyny wirtualnej, która może udzielić odpowiedzi.  Dotyczy to również połączeń wychodzących, dla których [translator adresów sieciowych opartych na źródle na potrzeby maskowania portów](load-balancer-outbound-connections.md#snat) jest obsługiwany tylko w przypadku protokołów TCP i UDP; dla wszystkich innych protokołów IP, w tym ICMP, również zakończy się to niepowodzeniem.  Przypisz publiczny adres IP na poziomie wystąpienia, aby rozwiązać ten problem.
- Inaczej niż w przypadku publicznych modułów równoważenia obciążenia, które udostępniają [połączenia wychodzące](load-balancer-outbound-connections.md) podczas przechodzenia z prywatnych adresów IP w ramach sieci wirtualnej do publicznych adresów IP, wewnętrzne moduły równoważenia obciążenia nie tłumaczą połączeń pochodzących z ruchu wychodzącego do frontonu wewnętrznego modułu równoważenia obciążenia, ponieważ znajdują się w prywatnej przestrzeni adresów IP.  Umożliwia to uniknięcia potencjalnego wyczerpania portów translatora adresów sieciowych opartych na źródle w obrębie wewnętrznej przestrzeni unikatowych adresów IP, gdzie tłumaczenie nie jest wymagane.  Efekt uboczny jest następujący: gdy przepływ wychodzący z maszyny wirtualnej w puli zaplecza podejmie próbę utworzenia przepływu do frontonu wewnętrznego modułu równoważenia obciążenia, w którego puli się znajduje, _oraz_ zostanie mapowany na siebie, obie części przepływu nie będą zgodne i przepływ zakończy się niepowodzeniem.  Jeśli przepływ nie zostanie zamapowany na tę samą maszynę wirtualną w puli zaplecza, która utworzyła przepływ do frontonu, działanie przepływu zostanie zakończone pomyślnie.   Gdy przepływ jest mapowany na siebie, przepływ wychodzący jest wyświetlany jako pochodzący z maszyny wirtualnej i kierowany do frontonu, a odpowiedni przepływ ruchu przychodzącego jest wyświetlany jako pochodzący z maszyny wirtualnej i kierowany do samego siebie. Z punktu widzenia systemu operacyjnego gościa części dotyczące ruchu przychodzącego i wychodzącego tego samego przepływu nie są zgodne w ramach maszyny wirtualnej. Stos TCP nie rozpozna tych części jednego przepływu jako należących do tego samego przepływu, ponieważ elementy źródłowe i docelowe nie będą zgodne.  Gdy przepływ jest mapowany na dowolną inną maszynę wirtualną w ramach puli zaplecza, części przepływu będą zgodne, a maszyna wirtualna może pomyślnie odpowiedzieć na przepływ.  Ten scenariusz charakteryzuje się występowaniem sporadycznych przekroczeń limitu czasu po powrocie przepływu do tego samego zaplecza, z którego pochodzi. Istnieje kilka typowych sposobów obejścia tej sytuacji, aby w sposób wiarygodny osiągnąć ten scenariusz (przepływy pochodzące z puli zaplecza kierowane do pól zaplecza zgodnie z frontonem modułu równoważenia), co obejmuje wstawienie warstwy proxy za wewnętrznym modułem równoważenia obciążenia lub [użycie reguł stylu DSR](load-balancer-multivip-overview.md).  Klienci mogą połączyć wewnętrzny moduł równoważenia obciążenia z serwerem proxy innej firmy lub zastąpić wewnętrzną usługę [Application Gateway](../application-gateway/application-gateway-introduction.md) w przypadku scenariuszy serwera proxy ograniczonych do protokołu HTTP/HTTPS. Użycie publicznego modułu równoważenia obciążenia jest możliwe w celu rozwiązania tego problemu, ale wynikowy scenariusz jest podatny na [wyczerpanie translatora adresów sieciowych opartych na źródle](load-balancer-outbound-connections.md#snat) i należy tego unikać lub starannie kontrolować.
- Ogólnie rzecz biorąc przekazywanie fragmentów adresów IP lub wykonywanie fragmentacji adresów IP pakietów UDP i TCP nie jest obsługiwane w regułach równoważenia obciążenia.  [Reguły równoważenia obciążenia portów ha](load-balancer-ha-ports-overview.md) są wyjątkiem od tej ogólnej instrukcji i mogą być używane do przesyłania dalej istniejących fragmentów adresów IP.

## <a name="next-steps"></a>Następne kroki

Masz teraz ogólne pojęcie o usłudze Azure Load Balancer. Aby rozpocząć korzystanie z modułu równoważenia obciążenia, utwórz go, utwórz maszyny wirtualne z zainstalowanym niestandardowym rozszerzeniem usług IIS i przeprowadź równoważenie obciążenia aplikacji internetowej między maszynami wirtualnymi. Aby dowiedzieć się, jak to zrobić, zobacz przewodnik Szybki start [Tworzenie modułu równoważenia obciążenia w warstwie Podstawowa](quickstart-create-basic-load-balancer-portal.md).
