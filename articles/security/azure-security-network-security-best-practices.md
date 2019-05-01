---
title: Najlepsze rozwiązania dotyczące zabezpieczeń sieci — Microsoft Azure
description: Ten artykuł zawiera zestaw najlepszych rozwiązań dla sieci zabezpieczeń przy użyciu wbudowanych funkcji platformy Azure.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: TomSh
ms.openlocfilehash: 5bec7db1c4409165242416df16e437b121381b49
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872558"
---
# <a name="azure-network-security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń sieci platformy Azure
W tym artykule omówiono kolekcja najlepszych rozwiązań dotyczących zabezpieczeń sieci platformy Azure. Następujące najlepsze rozwiązania są uzyskiwane z naszych doświadczeniach z sieci platformy Azure i procesy, przez klientów, takie jak samodzielnie.

Dla każdego najlepszym rozwiązaniem w tym artykule opisano:

* Co to jest najlepsze rozwiązanie
* Dlaczego chcesz włączyć na tym najlepszym rozwiązaniem jest
* W przypadku awarii umożliwiające najlepszym rozwiązaniem, co może być skutkiem
* Możliwe alternatywy najlepsze rozwiązanie polegające na
* Jak można dowiesz się umożliwić najlepszym rozwiązaniem jest

W tym artykule najlepszych rozwiązań dotyczących platformy Azure sieci zabezpieczeń opiera się na opinii consensus i funkcji platformy Azure i zestawy funkcji występujących w czasie, który został zapisany w tym artykule. Opinie i technologii zmieniają się wraz z upływem czasu, a w tym artykule zostanie zaktualizowana w regularnych odstępach czasu, aby odzwierciedlać wprowadzone zmiany.

## <a name="logically-segment-subnets"></a>Logicznie segmentu podsieci
Sieci wirtualne platformy Azure są podobne do sieci lokalnej w sieci lokalnej. Idei siecią wirtualną platformy Azure jest utworzenie pojedynczego adresu IP adres oparte na przestrzeni sieci prywatnej w którym można umieścić wszystkich maszynach wirtualnych platformy Azure. Prywatnych przestrzeni adresów IP dostępnych znajdują się w klasie (10.0.0.0/8), klasy B (172.16.0.0/12) i zakresy adresów klasy C (192.168.0.0/16).

Najlepsze rozwiązania dotyczące logicznie segmentacji podsieci obejmują:

**Najlepsze rozwiązanie**: Segmenty większą przestrzeń adresów w podsieci.   
**Szczegóły**: Użyj [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)— na podstawie zasad podsieci, aby utworzyć podsieci.

**Najlepsze rozwiązanie**: Utwórz kontrolę dostępu do sieci między podsieciami. Routing między podsieciami odbywa się automatycznie i nie trzeba ręcznie skonfigurować tabele routingu. Domyślnie istnieją kontrolę dostępu do nie sieci między podsieciami, utworzone w sieci wirtualnej platformy Azure.   
**Szczegóły**: Użyj [sieciowej grupy zabezpieczeń](../virtual-network/virtual-networks-nsg.md) (NSG). Sieciowe grupy zabezpieczeń są proste, urządzenia inspekcja pakietów, które korzystają z 5-krotka (źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy i protokół warstwy 4) podejście do tworzenia reguł zezwalania/niezezwalania ruchu sieciowego. Zezwalaj lub Odmów ruch do i z pojedynczego adresu IP do wielu adresów IP, lub do i z całej podsieci.

Korzystając z sieciowymi grupami zabezpieczeń kontroli dostępu do sieci między podsieciami, wystarczy umieścić zasoby, które należą do tej samej strefie zabezpieczeń lub roli w ich własnych podsieciach.

## <a name="control-routing-behavior"></a>Kontrolowania zachowania routingu
Po umieszczeniu maszyny wirtualnej w sieci wirtualnej platformy Azure, maszyny Wirtualnej można nawiązać dowolnej innej maszyny Wirtualnej w tej samej sieci wirtualnej, nawet jeśli inne maszyny wirtualne są w różnych podsieciach. Jest to możliwe, ponieważ ten typ komunikacji pozwala na zbiór tras systemowych, domyślnie włączone. Te trasy domyślne umożliwić maszynom wirtualnym na tej samej sieci wirtualnej do inicjowania połączeń ze sobą oraz z Internetem (dla komunikacji wychodzącej tylko z Internetem).

Mimo że domyślne trasy systemowe są przydatne w przypadku wiele scenariuszy wdrażania, istnieją czasy, kiedy chcesz dostosować konfigurację routingu dla wdrożeń. Można skonfigurować adres następnego przeskoku do osiągnięcia określonych miejsc docelowych.

Firma Microsoft zaleca, aby skonfigurować [trasy zdefiniowane przez użytkownika](../virtual-network/virtual-networks-udr-overview.md) podczas wdrażania urządzenia zabezpieczeń w sieci wirtualnej. Będziemy wyjaśniać, to w dalszej części tego tematu zatytułowanej [zabezpieczanie zasobów usługi platformy Azure krytyczne tylko sieci wirtualne](azure-security-network-security-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Trasy zdefiniowane przez użytkownika nie są wymagane i zazwyczaj działają domyślnych tras systemowych.
>
>

## <a name="enable-forced-tunneling"></a>Włączanie tunelowania wymuszonego
Aby lepiej zrozumieć, wymuszonego tunelowania, warto zrozumieć, jakie "Podziel tunelowanie" jest. Najbardziej typowym przykładem tunelowania podzielonego pojawia się przy użyciu połączenia wirtualnej sieci prywatnej (VPN). Wyobraź sobie ustanowić połączenie sieci VPN z pokoju w hotelu do sieci firmowej. To połączenie umożliwia dostęp do zasobów firmy. Przejdź cała komunikacja z siecią firmową za pośrednictwem tunelu sieci VPN.

Co się stanie, jeśli chcesz nawiązać połączenie z zasobami przez internet? Po włączeniu tunelowania podzielonego tych połączeń, przejdź bezpośrednio do Internetu, a nie za pośrednictwem tunelu sieci VPN. Niektóre ekspertów ds. bezpieczeństwa należy rozważyć, to potencjalne ryzyko. One zaleca się wyłączenie tunelowania podzielonego i upewniając się, że wszystkie połączenia, osobami skierowany do Internetu i te informacje przeznaczone dla zasobów firmowych, przejdź za pośrednictwem tunelu sieci VPN. Zaletą wyłączenie tunelowania podzielonego jest, że połączenie z Internetem jest następnie wymuszone za pomocą urządzenia zabezpieczeń w sieci firmowej. Który nie byłoby możliwe gdyby klienta sieci VPN połączony z Internetem wyjeżdża z tunelu sieci VPN.

Teraz możemy przywrócić tym do maszyn wirtualnych w sieci wirtualnej platformy Azure. Trasy domyślne dla sieci wirtualnej platformy Azure umożliwiają maszyn wirtualnych zainicjować ruch do Internetu. To zbyt może reprezentować zagrożenie bezpieczeństwa, ponieważ te połączenia wychodzące może zwiększyć podatność Maszynę wirtualną i używana przez osoby atakujące. Z tego powodu zaleca się, że możesz [Włącz wymuszonego tunelowania](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) na maszynach wirtualnych w przypadku łączności między środowiskami lokalnymi między siecią wirtualną platformy Azure i siecią lokalną. Omówione łączności między środowiskami lokalnymi w dalszej części sieci najlepszych rozwiązań.

Jeśli nie masz połączenia między środowiskami lokalnymi, pamiętaj móc korzystać z sieciowymi grupami zabezpieczeń (opisany wcześniej) lub sieć platformy Azure wirtualnego urządzenia zabezpieczeń (omówionej poniżej) aby zapobiec połączenia wychodzące z Internetem w maszynach wirtualnych platformy Azure.

## <a name="use-virtual-network-appliances"></a>Użyj urządzenia sieci wirtualnej
Sieciowe grupy zabezpieczeń i routingu zdefiniowanego przez użytkownika może zapewnić miary zabezpieczenia sieci w sieci i mechanizm transportu warstw [OSI model](https://en.wikipedia.org/wiki/OSI_model). Ale w niektórych sytuacjach ma lub należy włączyć zabezpieczenia na wysoki poziom stosu. W takich sytuacjach zalecamy wdrażania wirtualnego sieciowe urządzenia zabezpieczające udostępniane przez partnerów platformy Azure.

Urządzenia zabezpieczeń sieci platformy Azure może dostarczać lepsze zabezpieczenia niż poziom sieci elementy sterujące udostępniają. Możliwości zabezpieczeń sieci wirtualnych sieciowe urządzenia zabezpieczające obejmują:


* Zapora
* Włamań wykrywania/nieautoryzowanego dostępu zapobiegania
* Zarządzanie lukami w zabezpieczeniach
* Sterowanie aplikacjami
* Wykrywanie anomalii oparte na sieci
* Filtrowanie sieci Web
* Oprogramowanie antywirusowe
* Botnet ochrony

Aby znaleźć dostępne sieci wirtualnej platformy Azure, urządzenia zabezpieczeń, przejdź do [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/) i poszukaj pozycji "zabezpieczenia" i "" zabezpieczenia sieciowe.

## <a name="deploy-perimeter-networks-for-security-zones"></a>Wdrażanie sieci obwodowej do strefy zabezpieczeń
A [sieci obwodowej](https://docs.microsoft.com/azure/best-practices-network-security) (DMZ) jest segment sieci fizycznej lub logicznej, który zapewnia dodatkową warstwę zabezpieczeń między zasobów i z Internetu. Urządzenia kontroli dostępu do wyspecjalizowanego sieci na krawędzi sieci obwodowej zezwolić tylko na żądany ruch w sieci wirtualnej.

Sieci obwodowej są przydatne, ponieważ możesz skupić się Twoja sieć zarządzania kontrolą dostępu, monitorowanie, rejestrowanie i raportowanie na urządzeniach, na brzegu sieci wirtualnej platformy Azure. W tym miejscu możesz włączyć zazwyczaj rozproszona odmowa zapobiegania service (DDoS), włamań wykrywania/nieautoryzowanego dostępu zapobiegania systemów (IDS/IPS), reguły zapory i zasady, filtrowanie sieci web, sieci ochrony przed złośliwym oprogramowaniem i więcej. Urządzenia zabezpieczeń sieciowych znajdują się między Internetem a siecią wirtualną platformy Azure i interfejs w obu sieciach.

Chociaż jest to podstawowy projekt sieci obwodowej, istnieje wiele różnych projektów, takich jak symetryczna, tri, sieci i wieloadresowych.

Firma Microsoft zaleca w przypadku wszystkich wdrożeń o wysokim poziomie zabezpieczeń rozważ podwyższyć poziom zabezpieczeń sieci dla zasobów platformy Azure przy użyciu sieci obwodowej.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Unikaj zagrożeń w Internecie za pomocą dedykowanego łącza sieci WAN
W wielu organizacjach wybrana opcja hybrydowa trasy IT. Hybrydowy IT niektórych zasobów informacji firmy znajdują się na platformie Azure, inne pozostają w środowisku lokalnym. W wielu przypadkach niektóre składniki usługi działają na platformie Azure, podczas gdy inne składniki pozostaje w środowisku lokalnym.

W hybrydowych scenariuszy IT jest zazwyczaj pewien rodzaj łączności między środowiskami lokalnymi. Łączność między wieloma lokalizacjami umożliwia firmy do łączenia z jego sieciami lokalnymi sieciami wirtualnymi platformy Azure. Dostępne są dwa rozwiązania w zakresie połączeń obejmujących wiele lokalizacji:

* **Site-to-site VPN**: Jest to technologia zaufanej, niezawodnej i ustanowionych, ale połączenie odbywa się za pośrednictwem Internetu. Przepustowość jest ograniczone do maksymalnie około 200 MB/s. Sieci VPN typu lokacja lokacja jest pożądane opcji w niektórych scenariuszach i jest omówiona w dalszej sekcji [RDP/SSH wyłączyć dostęp do maszyn wirtualnych](#disable-rdpssh-access-to-virtual-machines).
* **Azure ExpressRoute**: Firma Microsoft zaleca użycie [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) dla łączności między środowiskami lokalnymi. Usługa ExpressRoute jest dedykowany sieci WAN łącze między Twojej lokalizacji lokalnej lub dostawcy usług hosta programu Exchange. Ponieważ jest to połączenie telco, dane nie przesyłane za pośrednictwem Internetu i w związku z tym nie jest narażony na potencjalnych zagrożeń komunikację z Internetem.

## <a name="optimize-uptime-and-performance"></a>Optymalizuj czas pracy i wydajność
Jeśli usługa nie działa, informacje nie są dostępne. Jeśli wydajność jest niska tak, że dane są bezużyteczne, można rozważyć dane staną się niedostępne. Z punktu widzenia zabezpieczeń należy zrobić, niezależnie od rodzaju możesz się upewnić, że usługi mają optymalny czas pracy i wydajność.

Metoda popularnych i efektywne dla zwiększenia dostępności i wydajności jest równoważenia obciążenia. Równoważenie obciążenia jest metody dystrybucji ruchu sieciowego na serwerach, które są częścią usługi. Na przykład w przypadku serwerów frontonu sieci web jako część usługi, umożliwia równoważenia obciążenia rozdziela ruch między wiele serwerów frontonu sieci web.

Tej dystrybucji ruchu sieciowego zwiększa dostępność, ponieważ jeśli jeden z serwerów sieci web jest niedostępna, moduł równoważenia obciążenia przestaje wysyłać ruch do tego serwera, a następnie go przekierowuje do serwerów, które są nadal w trybie online. Równoważenie obciążenia sieciowego pomaga również wydajności, ponieważ obciążenie procesora, sieci i pamięci do obsługi żądań jest dystrybuowane na wszystkich serwerach ze zrównoważonym obciążeniem.

Zaleca się, że zostanie zastosowana Równoważenie obciążenia sieciowego Jeśli to tylko możliwe, a odpowiednie dla usługi. Poniżej przedstawiono scenariusze, zarówno w poziomie sieci wirtualnej platformy Azure, jak i w poziomie globalnym zamieszczono opcje równoważenia obciążenia dla każdego.

**Scenariusz**: Masz aplikację, która:

- Wymaga żądań z tej samej sesji klienta/użytkownika, aby dotrzeć do tej samej maszyny wirtualnej zaplecza. Przykładem tego są koszyka zakupów i serwery poczty sieci web.
- Akceptuje tylko bezpiecznego połączenia, więc nieszyfrowana komunikacja z serwerem nie jest dopuszczalna.
- Wymaga, aby wiele żądań HTTP w ramach tego samego połączenia TCP długotrwałe kierowane lub załadować równoważenia do różnych serwerów zaplecza.

**Opcja równoważeniem obciążenia**: Użyj [usługi Azure Application Gateway](../application-gateway/application-gateway-introduction.md), moduł równoważenia obciążenia ruchu w sieci web HTTP. Usługa Application Gateway obsługuje end-to-end szyfrowania SSL i [kończenia żądań SSL](../application-gateway/application-gateway-introduction.md) na bramie. Serwery sieci Web mogą następnie z nadmiaru szyfrowania i odszyfrowywania i ruch przepływający niezaszyfrowany do serwerów zaplecza.

**Scenariusz**: Musisz załadować saldo połączeń przychodzących z Internetu wśród serwerów znajdujących się w sieci wirtualnej platformy Azure. Scenariusze są, gdy użytkownik:

- Mieć aplikacji bezstanowych, które akceptują żądania przychodzące z Internetu.
- Nie wymaga trwałych sesji lub odciążanie protokołu SSL. Trwałych sesji to metoda stosowana przy użyciu równoważenia obciążenia aplikacji koligację serwera.

**Opcja równoważeniem obciążenia**: Użyj portalu Azure w celu [Tworzenie modułu równoważenia obciążenia zewnętrznych](../load-balancer/quickstart-create-basic-load-balancer-portal.md) które rozprzestrzeniają się żądania przychodzące na wielu maszynach wirtualnych, aby zapewnić wyższy poziom dostępności.

**Scenariusz**: Musisz załadować saldo połączeń z maszynami wirtualnymi, które nie są dostępne w Internecie. W większości przypadków połączeń, które są akceptowane dla równoważenia obciążenia są inicjowane przez urządzenia w sieci wirtualnej platformy Azure, takich jak wystąpienia programu SQL Server lub serwerów sieci web wewnętrznej.   
**Opcja równoważeniem obciążenia**: Użyj portalu Azure w celu [tworzenia wewnętrznego modułu równoważenia obciążenia](../load-balancer/quickstart-create-basic-load-balancer-powershell.md) które rozprzestrzeniają się żądania przychodzące na wielu maszynach wirtualnych, aby zapewnić wyższy poziom dostępności.

**Scenariusz**: Potrzebujesz równoważenia obciążenia globalne ponieważ możesz:

- Masz rozwiązanie w chmurze jest powszechnie rozpowszechniane w wielu regionach i wymaga najwyższego poziomu czas działania (dostępność) to możliwe.
- Największy czas działania muszą być możliwe upewnić się, czy aplikacja jest dostępna, nawet wtedy, gdy całe centrum danych stanie się niedostępny.

**Opcja równoważeniem obciążenia**: Użyj usługi Azure Traffic Manager. Usługa Traffic Manager sprawia, że można załadować saldo połączeń usługi na podstawie lokalizacji użytkownika.

Na przykład jeśli użytkownik wysyła żądanie do usługi z UE, połączenie zostanie skierowany do usług znajdujących się w centrum danych Unii Europejskiej. Ta część usługi Traffic Manager globalnego obciążenia równoważenia pozwala zwiększyć wydajność, ponieważ nawiązywanie połączenia z najbliższego centrum danych jest szybsza niż nawiązywania połączenia z centrami danych, które są daleko.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Wyłącz dostęp RDP/SSH dla maszyn wirtualnych
Można nawiązać połączenia przy użyciu usługi Azure virtual machines [protokołu Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) i [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) protokołu (SSH). Te protokoły włączyć zarządzanie maszynami wirtualnymi z lokalizacji zdalnych i są standardowe w przypadku komputerów w centrum danych.

Potencjalny problem zabezpieczeń przy użyciu tych protokołów za pośrednictwem Internetu jest, że osoby atakujące mogą wykorzystać [atak siłowy](https://en.wikipedia.org/wiki/Brute-force_attack) technik w celu uzyskania dostępu do maszyn wirtualnych platformy Azure. Po osobom atakującym uzyskanie dostępu, można użyć maszyny Wirtualnej jako punkt wyjścia dla uszczerbku dla innych maszyn w sieci wirtualnej, lub nawet ataki urządzeń sieciowych poza systemem Azure.

Firma Microsoft zaleca wyłączenie bezpośredni dostęp protokołu RDP i SSH na maszynach wirtualnych platformy Azure z Internetu. Po wyłączeniu bezpośredni dostęp protokołu RDP i SSH z Internetu, masz inne opcje, które umożliwiają dostęp do tych maszyn wirtualnych do zdalnego zarządzania.

**Scenariusz**: Włącz pojedynczego użytkownika nawiązać połączenie z siecią wirtualną platformy Azure za pośrednictwem Internetu.   
**Opcja**: [Sieci VPN typu punkt lokacja](../vpn-gateway/vpn-gateway-point-to-site-create.md) jest inna nazwa połączenia klient/serwer sieci VPN dostępu zdalnego. Po nawiązaniu połączenia punkt lokacja, użytkownik może użyć protokołu RDP lub SSH połączyć się z maszyn wirtualnych znajdujących się w sieci wirtualnej platformy Azure, który połączył się za pośrednictwem sieci VPN typu punkt lokacja. Przy założeniu, że użytkownik jest autoryzowany do osiągnięcia tych maszyn wirtualnych.

Sieci VPN typu punkt lokacja jest bezpieczniejszy niż bezpośrednich połączeń protokołu RDP lub SSH, ponieważ użytkownik będzie musiał uwierzytelnić dwa razy, przed połączeniem z maszyną wirtualną. Najpierw, użytkownik musi uwierzytelnić (i autoryzować) do nawiązania połączenia sieci VPN typu punkt lokacja. Po drugie, użytkownik musi uwierzytelnić (i autoryzować) do ustanowienia sesji protokołu RDP lub SSH.

**Scenariusz**: Użytkownicy w Twojej sieci lokalnej do połączonych z maszynami wirtualnymi w sieci wirtualnej platformy Azure.   
**Opcja**: A [sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-site-to-site-create.md) cała sieć łączy się z inną siecią za pośrednictwem Internetu. Sieć VPN lokacja lokacja można użyć, aby połączyć sieć lokalną z siecią wirtualną platformy Azure. Użytkownicy w Twojej sieci lokalnej łączyć się przy użyciu protokołu RDP lub SSH za pośrednictwem połączenia sieci VPN typu lokacja lokacja. Nie masz zezwolić na bezpośredni dostęp protokołu RDP lub SSH za pośrednictwem Internetu.

**Scenariusz**: Użyj dedykowanych łącza sieci WAN, aby dostarczyć funkcjonalność podobną do sieci VPN typu lokacja lokacja.   
**Opcja**: Użyj [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Zapewnia funkcje podobne do sieci VPN typu lokacja lokacja. Główne różnice to:

- Dedykowane łącza sieci WAN nie przechodzi przez internet.
- Dedykowane łącza sieci WAN są zwykle bardziej stabilny i poprawienia.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Zabezpieczanie zasobów usługi platformy Azure krytyczne tylko sieci wirtualne
Punkty końcowe usługi sieci wirtualnej umożliwia rozszerzanie sieci wirtualnej sieci prywatnej przestrzeni adresowej i tożsamość sieci wirtualnej do usług platformy Azure za pośrednictwem bezpośredniego połączenia. Punkty końcowe umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych. Ruch z sieci wirtualnej do usługi platformy Azure zawsze pozostaje w sieci szkieletowej platformy Microsoft Azure.

Punkty końcowe usługi oferują następujące korzyści:

- **Lepsze zabezpieczenia zasobów usługi platformy Azure**: Dzięki punktom końcowym zasoby usługi platformy Azure mogą być chronione w sieci wirtualnej. Zabezpieczanie zasobów usługi sieci wirtualnej zapewnia lepsze zabezpieczenia dzięki w pełni usunięcie publicznego dostępu do Internetu do zasobów i zezwolenie na ruch tylko z Twojej sieci wirtualnej.
- **Optymalny routing ruchu usług sieci platformy Azure z sieci wirtualnej**: Wszystkie trasy w sieci wirtualnej, które wymuszają ruch internetowy w środowisku lokalnym i/lub urządzenie wirtualne, znane jako wymuszonego tunelowania, wymuszają również ruch usługi platformy Azure po tej samej trasie co ruch internetowy. Punkty końcowe usługi zapewniają optymalny routing ruchu platformy Azure.

  Punkty końcowe zawsze pobierają ruch bezpośrednio z sieci wirtualnej do usługi w sieci szkieletowej platformy Azure. Zachowywanie ruchu w sieci szkieletowej platformy Azure umożliwia kontynuowanie inspekcji i monitorowania wychodzącego ruchu internetowego z sieci wirtualnych przy użyciu wymuszonego tunelowania, bez wywierania wpływu na ruch usługi. Dowiedz się więcej o [trasy zdefiniowane przez użytkownika i tunelowania](../virtual-network/virtual-networks-udr-overview.md).

- **Prosta konfiguracja i mniejsze ogólne koszty zarządzania**: Nie potrzebujesz już zastrzeżonych publicznych adresów IP w swoich sieciach wirtualnych w celu zabezpieczenia zasobów platformy Azure za pośrednictwem zapory adresów IP. Do skonfigurowania punktów końcowych usługi nie jest wymagany translator adresów sieciowych ani urządzenie bramy. Punkty końcowe usługi można skonfigurować za pomocą prostego kliknięcia w podsieci. Brak bez dodatkowych nakładów, aby zachować punktów końcowych.

Aby dowiedzieć się więcej na temat punktów końcowych usługi i usług platformy Azure i regionów, które punkty końcowe usługi są dostępne dla, zobacz [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="next-step"></a>Następny krok
Zobacz [zabezpieczeń platformy Azure najlepsze rozwiązania i wzorce](security-best-practices-and-patterns.md) dla więcej najważniejsze wskazówki dotyczące zabezpieczeń do użycia podczas one projektowanie, wdrażanie i zarządzanie rozwiązań w chmurze, korzystając z platformy Azure.
