---
title: Omówienie usługi Azure Load Balancer | Dokumentacja firmy Microsoft
description: Omówienie funkcji usługi Azure Load Balancer, architektury i wdrażania. Dowiedz się, jak działa moduł równoważenia obciążenia i jej wykorzystania w chmurze.
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
ms.date: 08/20/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 47509cd0a9208f41a52bf1a07c460bcdda2cb479
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "42055916"
---
# <a name="what-is-azure-load-balancer"></a>Co to jest usługa Azure Load Balancer?

Za pomocą usługi Azure Load Balancer można skalować aplikacje i zapewniać wysoką dostępność usług. Równoważenia obciążenia obsługuje scenariusze ruchu przychodzącego i wychodzącego, zapewnia małe opóźnienia i wysoką przepływność oraz skaluje nawet miliony przepływów dla wszystkich aplikacji TCP i UDP.  

Moduł równoważenia obciążenia dystrybuuje nowych przepływów ruchu przychodzącego, przychodzące do frontonu modułu równoważenia obciążenia do wystąpień puli zaplecza, zgodnie z zasadami i sondy kondycji. 

Ponadto publiczny moduł równoważenia obciążenia zapewniają połączeń wychodzących dla maszyn wirtualnych (VM) w Twojej sieci wirtualnej według ich prywatnych adresów IP do publicznych adresów IP tłumaczenia.

Równoważenie obciążenia Azure jest dostępna w dwóch jednostkach SKU: podstawowa i standardowa. Istnieją różnice w skali, funkcje i ceny. Wszystkich scenariuszy, które można zrobić za pomocą podstawowego modułu równoważenia obciążenia mogą być tworzone przy użyciu standardowego modułu równoważenia obciążenia, mimo że podejścia mogą się nieznacznie różnić. Dowiesz się o usłudze Load Balancer, ważne jest zapoznanie się z podstaw i różnice właściwe dla jednostki SKU.

## <a name="why-use-load-balancer"></a>Dlaczego warto używać usługi równoważenia obciążenia? 

Możesz użyć usługi Azure Load Balancer do:

* Równoważenie obciążenia ruchu internetowego przychodzącego do maszyn wirtualnych. Ta konfiguracja jest określana jako [publiczny moduł równoważenia obciążenia](#publicloadbalancer).
* Równoważenie obciążenia ruchem maszyn wirtualnych w sieci wirtualnej. Możesz również połączyć frontonu modułu równoważenia obciążenia z sieci lokalnej w scenariuszu hybrydowym. Oba scenariusze korzysta z konfiguracji, który jest znany jako [wewnętrznego modułu równoważenia obciążenia](#internalloadbalancer).
* Port ruch wychodzący do konkretnego portu na określonych maszyn wirtualnych przy użyciu reguły translatora (NAT) adresów sieciowych dla ruchu przychodzącego.
* Podaj [łączności wychodzącej](load-balancer-outbound-connections.md) dla maszyn wirtualnych w Twojej sieci wirtualnej przy użyciu publicznego modułu równoważenia obciążenia.


>[!NOTE]
> Platforma Azure udostępnia zestaw w pełni zarządzanych rozwiązań do równoważenia obciążenia dla Twoich scenariuszy. Jeśli chcesz zakończyć protokół zabezpieczeń TLS (Transport Layer Security) („odciążanie protokołu SSL”) lub przetwarzanie poszczególnych żądań dotyczących protokołu HTTP/HTTPS na poziomie warstwy aplikacji, zapoznaj się z tematem dotyczącym usługi [Application Gateway](../application-gateway/application-gateway-introduction.md). Jeśli szukasz globalnego DNS równoważenia obciążenia, przejrzyj [usługi Traffic Manager](../traffic-manager/traffic-manager-overview.md). Scenariusze kompleksowe mogą w razie potrzeby korzystać z zalet łączenia tych rozwiązań.

## <a name="what-are-load-balancer-resources"></a>Co to są zasobów modułu równoważenia obciążenia?

Zasób modułu równoważenia obciążenia może istnieć jako publiczny moduł równoważenia obciążenia lub wewnętrznego modułu równoważenia obciążenia. Funkcje zasobów modułu równoważenia obciążenia są wyrażane jako fronton, reguły, sondy kondycji i definicji puli zaplecza. Możesz umieścić maszyn wirtualnych w puli zaplecza, określając puli wewnętrznej bazy danych z maszyny Wirtualnej.

Zasobów modułu równoważenia obciążenia są obiekty, w których można wyrazić jak Azure powinien program swoją infrastrukturę wielodostępnych do osiągnięcia tego scenariusza, który chcesz utworzyć. Nie ma bezpośredniej relacji pomiędzy zasobów modułu równoważenia obciążenia i rzeczywistej infrastruktury. Tworzenie modułu równoważenia obciążenia nie tworzy wystąpienia i pojemność są zawsze dostępne. 

## <a name="fundamental-load-balancer-features"></a>Podstawowe funkcje równoważenia obciążenia

Moduł równoważenia obciążenia zapewnia następujące funkcje podstawowe dla aplikacji TCP i UDP:

* **Równoważenie obciążenia**

    Za pomocą usługi Azure Load Balancer można utworzyć regułę równoważenia obciążenia, aby dystrybuować ruch dociera do serwera sieci Web do wystąpień puli zaplecza. Moduł równoważenia obciążenia używa algorytmu wyznaczania wartości skrótu na podstawie do dystrybucji ruchu przychodzącego przepływów i w związku z tym ponownie zapisuje nagłówki przepływy do wystąpień puli zaplecza. Serwer jest dostępne do pobrania nowych przepływów, gdy sondę kondycji wskazuje punktu końcowego zaplecza w dobrej kondycji.
    
    Domyślnie moduł równoważenia obciążenia używa skrót 5-elementowe spójne kolekcje składa się z źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy i numer protokołu IP do mapowania przepływów dostępnych serwerów. Istnieje możliwość utworzenia koligacji do określonego źródłowego adresu IP, rezygnując na 2 lub 3-krotka skrót dla danej reguły. Wszystkie pakiety o takim samym przepływie pakietów pojawić się na tym samym wystąpieniu za zaporą frontonu ze zrównoważonym obciążeniem. Gdy klient inicjuje nowy przepływ w tym samym źródłowy adres IP, zmiany portu źródłowego. W rezultacie krotki 5 może powodować ruchu przejść do endpoint różnych wewnętrznych baz danych.

    Aby uzyskać więcej informacji, zobacz [trybu dystrybucji modułu równoważenia obciążenia](load-balancer-distribution-mode.md). Poniższa ilustracja przedstawia bazujących na skrótach dystrybucji:

    ![Dystrybucji skrótów](./media/load-balancer-overview/load-balancer-distribution.png)

    *Rysunek: Bazujących na skrótach dystrybucji*

* **Przekierowania portów**

    Z modułem równoważenia obciążenia można utworzyć regułę NAT dla ruchu przychodzącego do portu powodują przesyłanie ruchu z określonego portu adresu IP frontonu określonych do określonego portu wystąpienia określonego wewnętrznej bazy danych w sieci wirtualnej. To również odbywa się przez ten sam dystrybucji bazujących na skrótach równoważeniem obciążenia. Typowe scenariusze dotyczące tej funkcji są sesji protokołu RDP (Remote Desktop) lub protokołu Secure Shell (SSH) do poszczególnych wystąpień maszyn wirtualnych w sieci wirtualnej platformy Azure. Wiele wewnętrznych punktów końcowych można zamapować na różne porty na ten sam adres IP frontonu. Ich umożliwia zdalne administrowanie maszynami wirtualnymi za pośrednictwem sieci internet, bez konieczności stosowania dodatkowych przesiadkowym.

* **Aplikacja niezależny i przejrzystości**

    Moduł równoważenia obciążenia nie bezpośrednio współdziała z protokołu TCP lub UDP lub warstwy aplikacji i wszelkie TCP lub UDP scenariusza aplikacji mogą być obsługiwane.  Moduł równoważenia obciążenia nie zakończyć lub pochodzą przepływów, wchodzić w interakcje z ładunku przepływu, zapewnia żadnej funkcji bramy warstwy aplikacji i uzgodnienia protokołu zawsze wykonywane bezpośrednio między klientem a wystąpienie puli serwerów zaplecza.  Odpowiedzi na przepływie ruchu przychodzącego jest zawsze odpowiedzi z maszyny wirtualnej.  Po nadejściu przepływ na maszynie wirtualnej, oryginalnym źródłowy adres IP również są zachowywane.  Kilka przykładów dalsze zilustrowanie przejrzystości:
    - Każdy punkt końcowy jest tylko odbierane przez Maszynę wirtualną.  Na przykład uzgadniania protokołu TCP zawsze występuje między klientem a wybranych maszyn wirtualnych wewnętrznej bazy danych.  Odpowiedzi na żądanie fronton jest odpowiedzi generowane przez maszynę Wirtualną w wewnętrznej bazie danych. Pomyślnie sprawdzania poprawności łączności frontonu, zatwierdzasz łączność typu end to end z co najmniej jedna maszyna wirtualna zaplecza.
    - Ładunki aplikacji są niewidoczne dla modułu równoważenia obciążenia i wszelkie UDP lub TCP aplikacji mogą być obsługiwane. W przypadku obciążeń wymagających na przetwarzanie żądań HTTP lub manipulowania ładunków warstwy aplikacji (na przykład podczas analizowania adresów URL HTTP), należy użyć równoważenia obciążenia warstwy 7, takie jak [Application Gateway](https://azure.microsoft.com/services/application-gateway).
    - Moduł równoważenia obciążenia jest niezależny od ładunek TCP oraz odciążanie protokołu TLS ("SSL") nie zostanie podany, możesz tworzyć kompleksowe scenariusze zaszyfrowanych za pomocą modułu równoważenia obciążenia i osiągnąć duże skalowania dla aplikacji TLS połączenia TLS na samej maszyny Wirtualnej.  Na przykład pojemności klucza sesji protokołu TLS jest ograniczona tylko według typu i liczby maszyn wirtualnych, możesz dodać do puli zaplecza.  Jeśli potrzebujesz "Odciążanie protokołu SSL" traktowania warstwy aplikacji i chcesz delegować zarządzanie certyfikatami na platformie Azure, skorzystaj z platformy Azure load balancer dla warstwy 7 [Application Gateway](https://azure.microsoft.com/services/application-gateway) zamiast tego.
        

* **Automatycznej ponownej konfiguracji**

    Moduł równoważenia obciążenia natychmiast zmienia konfigurację wystąpień skalowanie w górę lub w dół. Dodawanie lub usuwanie maszyn wirtualnych z puli zaplecza ponownie konfiguruje moduł równoważenia obciążenia, bez dodatkowych operacji dla zasobu modułu równoważenia obciążenia.

* **Sondy kondycji**

    Aby ustalić kondycję wystąpień w puli zaplecza, moduł równoważenia obciążenia używa sondy kondycji, zdefiniowanych przez użytkownika. Jeśli sonda nie odpowiada, moduł równoważenia obciążenia zaprzestaje inicjowania nowych połączeń do wystąpień w złej kondycji. Nie wpływają na istniejące połączenia, a użytkownik nadal aż do zakończenia aplikacji flow, limit czasu bezczynności wystąpi, lub maszyna wirtualna jest zamknięta.
     
    Moduł równoważenia obciążenia zapewnia [typów sondy kondycji różnych](load-balancer-custom-probe-overview.md#types) dla punktów końcowych TCP, HTTP i HTTPS.

    Ponadto, korzystając z usług w chmurze klasyczne, dodatkowy typ jest dozwolony: [agenta gościa](load-balancer-custom-probe-overview.md#guestagent).  To powinno być uznane za sondę kondycji w ostateczności i nie jest zalecane w przypadku innych opcji możliwego do użycia.
    
* **Połączenia wychodzące (SNAT)**

    Wszystkie przepływy wychodzące z prywatnych adresów IP w Twojej sieci wirtualnej do publicznych adresów IP w Internecie mogą być tłumaczone na adres IP frontonu modułu równoważenia obciążenia. Gdy publicznych frontonu jest powiązany z maszyny Wirtualnej zaplecza za pomocą reguły równoważenia obciążenia, Azure programów połączeń wychodzących, które mają być automatycznie tłumaczone na publicznego adresu IP frontonu.

    * Włącz łatwe uaktualnianie i odzyskiwania po awarii usług, ponieważ frontonu, które mogą być dynamicznie mapowane do innego wystąpienia usługi.
    * Łatwiejsze zarządzanie kontrolą dostępu listę kontroli dostępu (ACL) do. List ACL w przeliczeniu na frontonie adresów IP nie należy zmieniać jako usługi są skalowane w górę lub w dół lub uzyskać ponownego wdrażania.  Translacja połączenia wychodzące do mniejszej liczby adresów IP, niż maszyny można zmniejszyć obciążenie umieszczania na białej liście.

    Aby uzyskać więcej informacji, zobacz [połączeń wychodzących](load-balancer-outbound-connections.md).

Standardowy moduł równoważenia obciążenia ma dodatkowe funkcje specyficzne dla jednostki SKU poza te podstawy. Przejrzyj pozostałej części tego artykułu, aby uzyskać szczegółowe informacje.

## <a name="skus"></a> Porównanie jednostki SKU modułu równoważenia obciążenia

Load Balancer obsługuje zarówno podstawowy, jak i standardowej jednostki SKU, różniących się każdy scenariusz skali, funkcje i ceny. Wszystkich scenariuszy, które można zrobić za pomocą podstawowego modułu równoważenia obciążenia można również utworzyć przy użyciu standardowego modułu równoważenia obciążenia. W rzeczywistości interfejsów API dla obu jednostek SKU są podobne i wywoływane przez specyfikację jednostki SKU. Interfejs API do obsługi jednostki SKU dla usługi równoważenia obciążenia i publiczny adres IP jest dostępna, począwszy od 2017-08-01 interfejsu API. Zarówno jednostki SKU mają tego samego ogólnego interfejsu API i strukturę.

Jednak w zależności od wybranej wersji produktu, konfiguracji całego scenariusza może się nieco różnić. Dokumentacja usługi Równoważenie obciążenia wywołuje się, gdy artykuł ma zastosowanie tylko do określonej jednostki SKU. Aby porównać i poznać różnice, zobacz poniższej tabeli. Aby uzyskać więcej informacji, zobacz [omówienie Standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Nowe projekty powinna przyjąć standardowego modułu równoważenia obciążenia. 

Autonomiczne maszyny wirtualne, zestawy dostępności i zestawy skalowania maszyn wirtualnych można połączyć tylko jednej jednostki SKU, nigdy nie oba. Gdy używasz ich z publicznych adresów IP modułu równoważenia obciążenia i publicznego adresu IP jednostki SKU muszą być zgodne. Moduł równoważenia obciążenia i publicznego adresu IP jednostki SKU nie jest modyfikowalna.

_Najlepszym rozwiązaniem, aby jawnie określić jednostki SKU jest nawet, jeśli jeszcze nie jest obowiązkowe._  W tej chwili zmiany wymagane są są ograniczone do minimum. Jeśli nie określono jednostki SKU, jest interpretowany jako zamiar użyć podstawowa jednostka SKU w wersji interfejsu API 2017-08-01.

>[!IMPORTANT]
>Load Balancer w warstwie standardowa jest nowy produkt modułu równoważenia obciążenia i dużym stopniu nadzbiorem podstawowego modułu równoważenia obciążenia. Istnieją ważne i zamierzonego różnice między tymi dwoma produktami. Można również tworzyć wszystkich scenariuszy end-to-end, które można zrobić za pomocą podstawowego modułu równoważenia obciążenia przy użyciu standardowego modułu równoważenia obciążenia. Jeśli już do podstawowego modułu równoważenia obciążenia, należy zapoznać się ze standardowego modułu równoważenia obciążenia, aby poznać najnowsze zmiany w zachowaniu warstw standardowa i podstawowa i ich wpływ. Uważnie przejrzyj tę sekcję.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Aby uzyskać więcej informacji, zobacz [limitów usług dla usługi Load Balancer](https://aka.ms/lblimits). Load Balancer w warstwie standardowa szczegółowe informacje, zobacz [Przegląd](load-balancer-standard-overview.md), [ceny](https://aka.ms/lbpricing), i [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Pojęcia

### <a name = "publicloadbalancer"></a>Publiczny moduł równoważenia obciążenia

Publiczny moduł równoważenia obciążenia mapuje publiczny adres IP i port numer przychodzącego ruchu sieciowego do prywatnego adresu IP adres i numer portu maszyny wirtualnej i odwrotnie dla ruchu odpowiedzi z maszyny Wirtualnej. Stosując reguły równoważenia obciążenia, można rozpowszechniać określonych rodzajów ruchu sieciowego między wiele maszyn wirtualnych ani usług. Na przykład można rozłożyć obciążenie ruchu związanego z żądaniami sieci web na wielu serwerach sieci web.

Na poniższej ilustracji przedstawiono punkt końcowy z równoważeniem obciążenia dla ruchu w sieci web, który jest udostępniany między trzy maszyny wirtualne dla publicznej komunikacji równorzędnej i TCP port 80. Te trzy maszyny wirtualne znajdują się w zestawie z równoważeniem obciążenia.

![Przykład modułu równoważenia obciążenia publiczny](./media/load-balancer-overview/IC727496.png)

*Rysunek: Obciążenia równoważenia ruchu w sieci web przy użyciu publicznego modułu równoważenia obciążenia*

Gdy klienci internetowi wysyłać żądania strony sieci Web do publicznego adresu IP aplikacji sieci web na porcie 80 protokołu TCP, usługi Azure Load Balancer dystrybuuje żądania trzech maszyn wirtualnych w zestawie z równoważeniem obciążenia. Aby uzyskać więcej informacji na temat algorytmów równoważenia obciążenia, zobacz [funkcje modułu równoważenia obciążenia](load-balancer-overview.md##fundamental-load-balancer-features) dalszej części tego artykułu.

Domyślnie usługa Azure Load Balancer dystrybuuje ruch sieciowy, równo między wieloma wystąpieniami maszyn wirtualnych. Można również skonfigurować koligację sesji. Aby uzyskać więcej informacji, zobacz [trybu dystrybucji modułu równoważenia obciążenia](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Wewnętrzny moduł równoważenia obciążenia

Wewnętrzny moduł równoważenia obciążenia kieruje ruch tylko do zasobów, które znajdują się wewnątrz sieci wirtualnej lub który umożliwia dostęp do infrastruktury platformy Azure przez sieci VPN. W związku z tym wewnętrznego modułu równoważenia obciążenia różni się od publicznego modułu równoważenia obciążenia. Infrastruktura platformy Azure ogranicza dostęp do adresów IP ze zrównoważonym obciążeniem frontonu w sieci wirtualnej. adresy IP frontonu i sieciami wirtualnymi nigdy bezpośrednio są widoczne dla internetowego punktu końcowego. Wewnętrzne aplikacje line-of-business, działają na platformie Azure i są dostępne z w obrębie platformy Azure lub z zasobami lokalnymi.

Wewnętrzny moduł równoważenia obciążenia umożliwia następujące rodzaje równoważenia obciążenia:

* **W ramach sieci wirtualnej**: równoważenie obciążenia maszyn wirtualnych w sieci wirtualnej do zestawu maszyn wirtualnych, które znajdują się w tej samej sieci wirtualnej.
* **Dla sieci wirtualnej między lokalizacjami**: obciążenia równoważenia z komputerów w środowisku lokalnym, do zestawu maszyn wirtualnych, które znajdują się w tej samej sieci wirtualnej. 
* **Dla aplikacji wielowarstwowych**: równoważenia obciążenia dla aplikacji wielowarstwowych dostępnego z Internetu, gdy warstw zaplecza nie są połączone z Internetem. Równoważenie obciążenia ruchu wymagane w przypadku warstw zaplecza z Internetu — warstwa (patrz następny rysunek).
* **W przypadku aplikacji line-of-business**: równoważenia obciążenia dla aplikacji line-of-business, które są hostowane na platformie Azure bez dodatkowego obciążenia równoważenia sprzętu lub oprogramowania. Ten scenariusz obejmuje serwery lokalne, które znajdują się w zestawie komputerów, których ruch jest ze zrównoważonym obciążeniem.

![Przykład modułu równoważenia obciążenia wewnętrznego](./media/load-balancer-overview/IC744147.png)

*Rysunek: Obciążenia równoważenia aplikacje obejmujące wiele warstw przy użyciu zarówno usługi równoważenia obciążenia publicznych i wewnętrznych*

## <a name="pricing"></a>Cennik
Standardowego użycia Load Balancer, która jest rozliczana według liczby skonfigurowanych reguł równoważenia obciążenia i ilość przetworzonych danych przychodzących i wychodzących. Aby uzyskać Balancer w warstwie standardowa ceny informacji, przejdź do [cennik modułu równoważenia obciążenia](https://azure.microsoft.com/pricing/details/load-balancer/) strony.

Podstawowy moduł równoważenia obciążenia jest oferowane bez dodatkowych opłat.

## <a name="sla"></a>Umowa SLA

Aby uzyskać informacje o standardowych SLA usługi równoważenia obciążenia, przejdź do [umowa SLA usługi równoważenia obciążenia](https://aka.ms/lbsla) strony. 

## <a name="limitations"></a>Ograniczenia

- Moduł równoważenia obciążenia jest produkt TCP lub UDP do przekierowania portów dla tych określonych protokołów IP i równoważenia obciążenia.  Reguły równoważenia obciążenia i reguł translatora adresów Sieciowych dla ruchu przychodzącego są obsługiwane w przypadku protokołów TCP i UDP i nie jest obsługiwane dla innych protokołów IP, w tym protokołu ICMP. Moduł równoważenia obciążenia nie kończy, odpowiadać lub inny sposób interakcji z ładunku przepływ protokołu UDP lub TCP. Nie jest serwer proxy. Pomyślnej weryfikacji łączności z frontonu musi mieć miejsce wewnątrzpasmowe przy użyciu tego samego protokołu, które są używane w obciążenia równoważenia ruchu przychodzącego translatora adresów Sieciowych regułę lub (TCP lub UDP) _i_ co najmniej jedną z maszyn wirtualnych należy wygenerować odpowiedzi dla klientów zobacz odpowiedź z serwera sieci Web.  Nie odbiera odpowiedź wewnątrzpasmowe z frontonu modułu równoważenia obciążenia oznacza, że żadne maszyny wirtualne zostały przygotowane.  Nie jest możliwe do interakcji z frontonu modułu równoważenia obciążenia nie może odpowiadać maszyny wirtualnej.  Dotyczy to również połączenia wychodzące gdzie [maskująca portu SNAT](load-balancer-outbound-connections.md#snat) jest obsługiwana tylko w przypadku protokołów TCP i UDP; żadnych innych protokołów IP, ICMP w tym również zakończy się niepowodzeniem.  Przypisz adres publiczny adres IP na poziomie wystąpienia, aby uniknąć.
- Inaczej niż w przypadku publicznych modułów równoważenia obciążenia, które zawierają [połączeń wychodzących](load-balancer-outbound-connections.md) podczas przenoszenia z prywatnych adresów IP w sieci wirtualnej do publicznych adresów IP, wewnętrznego równoważenia obciążenia nie tłumaczenia wychodzącego pochodzi połączenia z frontonu dla wewnętrznego modułu równoważenia obciążenia jako znajdują się w prywatnej przestrzeni adresowej IP.  Umożliwia to uniknięcie potencjalne wyczerpanie portów SNAT wewnątrz unikatowy wewnętrznej przestrzeni adresów IP gdzie tłumaczenia nie jest wymagana.  Efektem ubocznym jest to, że jeśli przepływu wychodzącego z maszyny Wirtualnej w puli zaplecza prób przepływ frontonu wewnętrznego modułu równoważenia obciążenia w puli, która znajduje się _i_ jest mapowany do siebie, oba etapy przepływu nie są zgodne i przepływ zakończy się niepowodzeniem.  Jeśli przepływ nie zamapować powrót do tej samej maszyny Wirtualnej w puli zaplecza, która utworzyła przepływ do frontonu modułu, przepływ zostanie wykonane pomyślnie.   Gdy przepływ mapuje do samego przepływu wychodzącego jest wyświetlana tak, jakby pochodził z maszyny Wirtualnej do frontonu modułu i odpowiednie przepływu ruchu przychodzącego, który pojawia się tak, jakby pochodził z maszyny Wirtualnej do samego siebie. Z punktu widzenia systemu operacyjnego gościa dla ruchu przychodzącego i wychodzącego części takim samym przepływie nie są zgodne na maszynie wirtualnej. Dotyczącą stosu TCP nie rozpozna tych części tego samego przepływu jako część tego samego przepływu jako źródłowe i docelowe nie są zgodne.  Gdy przepływ jest mapowany do innej maszyny Wirtualnej w puli zaplecza, będzie zgodna części przepływu i maszyny Wirtualnej pomyślnie mogą odpowiadać na przepływ.  Objaw w tym scenariuszu jest połączenie przekroczeń limitu czasu, gdy przepływ powróci do tego samego zaplecza, które pochodzą z przepływu. Istnieją kilka typowych obejścia wiarygodny sposób osiągnięcia tego scenariusza (pochodzące z przepływów z puli zaplecza do wewnętrznej bazy danych pule odpowiednich wewnętrznego modułu równoważenia obciążenia frontonu) obejmujące albo wstawiania warstwy proxy za wewnętrznego modułu równoważenia obciążenia lub [przy użyciu reguł stylu DSR](load-balancer-multivip-overview.md).  Klientów można połączyć wewnętrznego modułu równoważenia obciążenia z 3 serwera proxy innych firm lub Zastąp wewnętrznego [Application Gateway](../application-gateway/application-gateway-introduction.md) dla scenariuszy serwera proxy ograniczone do protokołu HTTP/HTTPS. Podczas korzystania z publicznej usługi Load Balancer, można rozwiązać, wynikowy scenariusz jest podatna na [wyczerpania SNAT](load-balancer-outbound-connections.md#snat) i należy ich unikać, chyba że uważnie zarządzane.

## <a name="next-steps"></a>Kolejne kroki

Masz teraz z omówieniem usługi Azure Load Balancer. Aby rozpocząć pracę przy użyciu modułu równoważenia obciążenia, utwórz ją, Tworzenie maszyn wirtualnych przy użyciu niestandardowych zainstalowane rozszerzenie usług IIS i równoważenie obciążenia aplikacji internetowej między maszynami wirtualnymi. Aby dowiedzieć się więcej, zobacz temat [Tworzenie podstawowego modułu równoważenia obciążenia](quickstart-create-basic-load-balancer-portal.md) Szybki Start.
