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
ms.date: 05/05/2019
ms.author: TomSh
ms.openlocfilehash: 78402d3e388f08eae6652859a71c93ff408a5b0d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152989"
---
# <a name="azure-best-practices-for-network-security"></a>Najlepszymi rozwiązaniami dotyczącymi bezpieczeństwa sieci Azure
W tym artykule omówiono zbiór najlepsze rozwiązania platformy Azure, aby zwiększyć bezpieczeństwo sieci. Następujące najlepsze rozwiązania są uzyskiwane z naszych doświadczeniach z sieci platformy Azure i procesy, przez klientów, takie jak samodzielnie.

Dla każdego najlepszym rozwiązaniem w tym artykule opisano:

* Co to jest najlepsze rozwiązanie
* Dlaczego chcesz włączyć na tym najlepszym rozwiązaniem jest
* W przypadku awarii umożliwiające najlepszym rozwiązaniem, co może być skutkiem
* Możliwe alternatywy najlepsze rozwiązanie polegające na
* Jak można dowiesz się umożliwić najlepszym rozwiązaniem jest

Następujące najlepsze rozwiązania zależą od opinii consensus i funkcji platformy Azure i zestawy funkcji występujących w czasie, który został zapisany w tym artykule. Opinie i technologii zmieniają się wraz z upływem czasu, a w tym artykule zostanie zaktualizowana w regularnych odstępach czasu, aby odzwierciedlać wprowadzone zmiany.

## <a name="use-strong-network-controls"></a>Użycie kontroli silne sieci
Możesz połączyć [maszyn wirtualnych (VM)](https://azure.microsoft.com/services/virtual-machines/) i do innych urządzeń sieciowych, umieszczając je na [sieciami wirtualnymi platformy Azure](https://docs.microsoft.com/azure/virtual-network/). Oznacza to, że możesz połączyć karty interfejsu sieci wirtualnej z siecią wirtualną, aby umożliwić komunikację opartego na protokole IP między urządzeniami z włączoną obsługą sieci. Maszyny wirtualne połączone z siecią wirtualną platformy Azure mogą łączyć się urządzenia w tej samej sieci wirtualnej, w różnych sieciach wirtualnych, w Internecie lub w sieci lokalnej.

Podczas planowania sieci i bezpieczeństwo sieci, zaleca się, można scentralizować:

- Zarządzanie podstawowych funkcji sieci, takich jak usługi ExpressRoute, sieć wirtualną i podsieć inicjowania obsługi administracyjnej i adresów IP.
- Zarządzanie elementom zabezpieczeń sieci, takich jak sieciowe urządzenie wirtualne funkcje, takie jak usługi ExpressRoute, sieć wirtualną i podsieć inicjowania obsługi administracyjnej i adresów IP.

Korzystając ze wspólnego zestawu narzędzi do zarządzania do monitorowania sieci i zabezpieczeń sieci, uzyskujesz dobry wgląd w obu. Strategia zabezpieczeń prostym, ujednoliconym zmniejsza błędy, ponieważ zwiększa zrozumienia ludzi i niezawodność usługi automation.

## <a name="logically-segment-subnets"></a>Logicznie segmentu podsieci
Sieci wirtualne platformy Azure są podobne do sieci LAN w sieci lokalnej. Idei siecią wirtualną platformy Azure jest utworzenie sieci, w oparciu o jednym przestrzeń prywatnych adresów IP, na którym można umieścić wszystkich maszynach wirtualnych platformy Azure. Prywatnych przestrzeni adresów IP dostępnych znajdują się w klasie (10.0.0.0/8), klasy B (172.16.0.0/12) i zakresy adresów klasy C (192.168.0.0/16).

Najlepsze rozwiązania dotyczące logicznie segmentacji podsieci obejmują:

**Najlepsze rozwiązanie**: Nie przypisuj reguły z szerokim-zakresami zezwalania (na przykład zezwolić 0.0.0.0 za pośrednictwem 255.255.255.255).  
**Szczegóły**: Upewnij się, procedur rozwiązywania problemów zniechęcić lub zablokować konfigurowania tych typów reguł. Te reguły doprowadzić do fałszywe poczucie bezpieczeństwa i są często znaleziono i wykorzystane przez zespoły czerwony.

**Najlepsze rozwiązanie**: Segmenty większą przestrzeń adresów w podsieci.   
**Szczegóły**: Użyj [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)— na podstawie zasad podsieci, aby utworzyć podsieci.

**Najlepsze rozwiązanie**: Utwórz kontrolę dostępu do sieci między podsieciami. Routing między podsieciami odbywa się automatycznie i nie trzeba ręcznie skonfigurować tabele routingu. Domyślnie istnieją kontrolę dostępu do nie sieci między podsieciami, utworzone w sieci wirtualnej platformy Azure.   
**Szczegóły**: Użyj [sieciowej grupy zabezpieczeń](../virtual-network/virtual-networks-nsg.md) do ochrony przed niepożądanym ruchem sieciowym w podsieci platformy Azure. Sieciowe grupy zabezpieczeń to prosty i stanowej pakietów inspekcji urządzenia, używanych do tworzenia reguł zezwalania/niezezwalania dla ruchu sieciowego za pomocą podejścia 5-krotka (źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy i protokół warstwy 4). Zezwalaj lub Odmów ruch do i z pojedynczego adresu IP do wielu adresów IP, lub do i z całej podsieci.

Korzystając z grup zabezpieczeń sieci dla kontroli dostępu do sieci między podsieciami, wystarczy umieścić zasoby, które należą do tej samej strefie zabezpieczeń lub roli w ich własnych podsieciach.

**Najlepsze rozwiązanie**: Należy unikać małych sieci wirtualne i podsieci, aby upewnić się, prostota i elastyczność.   
**Szczegóły**: Większość organizacji, Dodaj więcej zasobów niż początkowo planowano i ponowne przydzielanie adresów jest intensywnie korzystających z pracy. Przy użyciu małych podsieci dodaje wartość ograniczonych zabezpieczeń i mapowanie sieciowej grupy zabezpieczeń w każdej podsieci dodaje obciążenie. Definiowania szeroko podsieci, aby zapewnić elastyczność na rozwój.

**Najlepsze rozwiązanie**: Uprość Zarządzanie reguły grupy zabezpieczeń sieci, definiując [grupy zabezpieczeń aplikacji](https://docs.microsoft.com/rest/api/virtualnetwork/applicationsecuritygroups).  
**Szczegóły**: Zdefiniuj grupę zabezpieczeń aplikacji, dla listy adresów IP, które Twoim zdaniem mogą ulec zmianie w przyszłości, lub można stosować w przypadku wielu grup zabezpieczeń sieci. Upewnij się, do nazwy aplikacji zabezpieczeń grupy wyraźnie więc osobom może zrozumieć ich zawartość i przeznaczenie.

## <a name="adopt-a-zero-trust-approach"></a>Przyjęcie podejścia Zero zaufania
Sieci obwodowej działają na założeniu, że wszystkich systemów w sieci można zaufać. Obecnie pracownicy uzyskiwać dostęp do zasobów w organizacji z dowolnego miejsca, ale na wielu różnych urządzeń i aplikacji, co sprawia, że obwodowej kontroli zabezpieczeń nie ma znaczenia. Zasady kontroli dostępu koncentrujących się tylko w systemie, którzy mogą uzyskać dostęp do zasobu nie są wystarczające. Skutecznie stawisz równowagę między bezpieczeństwa i produktywności, Administratorzy zabezpieczeń również należy wziąć pod uwagę *jak* dostęp do zasobu.

Sieci muszą podlegać ewolucji z mechanizmów obronnych w tradycyjnych, ponieważ sieci może być narażony na naruszenia: atakujący może naruszyć jednym punkcie końcowym w granicach zaufanej i następnie szybko rozbudować przyczółka w całej sieci. [Zero zaufania](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) sieci wyeliminować koncepcji zaufania, na podstawie lokalizacji sieciowej, w ramach obwodu. Zero zaufania architektury można użyć urządzenia i użytkownika oświadczeń zaufania dla bramy dostępu do danych organizacji i zasobów. Dla nowych inicjatyw przyjęcie podejścia Zero zaufania, sprawdzające zaufania w czasie dostępu.

Najlepsze rozwiązania są:

**Najlepsze rozwiązanie**: Zapewnić warunkowy dostęp do zasobów opartych na urządzeniu, tożsamość, assurance i lokalizacji sieciowej.  
**Szczegóły**: [Dostęp warunkowy usługi Azure AD](../active-directory/conditional-access/overview.md) umożliwia stosowanie kontroli dostępu poprzez implementację decyzji dotyczących kontroli dostępu automatycznego na podstawie warunków wymaganych. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem do zarządzania platformy Azure przy użyciu dostępu warunkowego](../role-based-access-control/conditional-access-azure-management.md).

**Najlepsze rozwiązanie**: Włączanie dostępu do portu tylko po zatwierdzeniu przepływu pracy.  
**Szczegóły**: Możesz użyć [dostęp do maszyny Wirtualnej just-in-time w usłudze Azure Security Center](../security-center/security-center-just-in-time.md) do blokowania ruchu przychodzącego do maszyn wirtualnych platformy Azure, zmniejszenia narażenia na ataki przy zapewnianiu łatwego dostępu, aby nawiązać połączenie z maszynami wirtualnymi w razie.

**Najlepsze rozwiązanie**: Przyznaj tymczasowe uprawnienia do wykonywania zadań uprzywilejowanego, co uniemożliwia uzyskanie dostępu, po upływie limitu uprawnień użytkowników rodzajach nieautoryzowanego lub złośliwego. Dostęp jest udzielany tylko wtedy, gdy użytkownicy go potrzebują.  
**Szczegóły**: Użyj dostępu just in time w usłudze Azure AD Privileged Identity Management lub rozwiązaniu innej firmy o nadanie uprawnień do wykonywania zadań uprzywilejowanego.

Zero zaufania to kolejny etap ewolucji zabezpieczeń sieci. Stan zadecydowały o dyski organizacje sposób myślenia "założenie złamania zabezpieczeń", ale nie powinien stanowić ograniczenie tej metody. Zero zaufania sieci chronić dane i zasoby firmowe, przy jednoczesnym zapewnieniu, że organizacje mogą tworzyć nowoczesnych miejscach pracy przy użyciu technologii, pozwalające pracownikom produktywnego działania dowolnym czasie i z dowolnego miejsca, w żaden sposób.

## <a name="control-routing-behavior"></a>Kontrolowania zachowania routingu
Po umieszczeniu maszyny wirtualnej w sieci wirtualnej platformy Azure, maszyny Wirtualnej można nawiązać dowolnej innej maszyny Wirtualnej w tej samej sieci wirtualnej, nawet jeśli inne maszyny wirtualne są w różnych podsieciach. Jest to możliwe, ponieważ ten typ komunikacji pozwala na zbiór tras systemowych, domyślnie włączone. Te trasy domyślne umożliwić maszynom wirtualnym na tej samej sieci wirtualnej do inicjowania połączeń ze sobą oraz z Internetem (dla komunikacji wychodzącej tylko z Internetem).

Mimo że domyślne trasy systemowe są przydatne w przypadku wiele scenariuszy wdrażania, istnieją czasy, kiedy chcesz dostosować konfigurację routingu dla wdrożeń. Można skonfigurować adres następnego przeskoku do osiągnięcia określonych miejsc docelowych.

Firma Microsoft zaleca, aby skonfigurować [trasy zdefiniowane przez użytkownika](../virtual-network/virtual-networks-udr-overview.md) podczas wdrażania urządzenia zabezpieczeń w sieci wirtualnej. Będziemy wyjaśniać, to w dalszej części tego tematu zatytułowanej [zabezpieczanie zasobów usługi platformy Azure krytyczne tylko sieci wirtualne](azure-security-network-security-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Trasy zdefiniowane przez użytkownika nie są wymagane i zazwyczaj działają domyślnych tras systemowych.
>
>

## <a name="use-virtual-network-appliances"></a>Użyj urządzenia sieci wirtualnej
Grupy zabezpieczeń sieci i routingu zdefiniowanego przez użytkownika może zapewnić miary zabezpieczenia sieci w sieci i mechanizm transportu warstw [OSI model](https://en.wikipedia.org/wiki/OSI_model). Ale w niektórych sytuacjach ma lub należy włączyć zabezpieczenia na wysoki poziom stosu. W takich sytuacjach zalecamy wdrażania wirtualnego sieciowe urządzenia zabezpieczające udostępniane przez partnerów platformy Azure.

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

Sieci obwodowej są przydatne, ponieważ możesz skupić się Twoja sieć zarządzania kontrolą dostępu, monitorowanie, rejestrowanie i raportowanie na urządzeniach, na brzegu sieci wirtualnej platformy Azure. Sieci obwodowej jest to, gdzie zwykle umożliwiają rozproszona odmowa zapobiegania service (DDoS), włamań wykrywania/nieautoryzowanego dostępu zapobiegania systemów (IDS/IPS), reguły zapory i zasady, filtrowanie sieci web, sieci ochrony przed złośliwym oprogramowaniem i więcej. Urządzenia zabezpieczeń sieciowych znajdują się między Internetem a siecią wirtualną platformy Azure i interfejs w obu sieciach.

Mimo że jest to podstawowy projekt sieci obwodowej, istnieje wiele różnych projektów, takich jak symetryczna, tri, sieci i wieloadresowych.

Oparte na Zero zaufania pojęcia wspomniano wcześniej, firma Microsoft zaleca, aby podwyższyć poziom sieci zabezpieczenia i kontrola dostępu do zasobów platformy Azure przy użyciu sieci obwodowej dla wszystkich wdrożeń o wysokim poziomie zabezpieczeń rozważ. Platformy Azure lub rozwiązanie innej firmy umożliwia zapewniają dodatkową warstwę zabezpieczeń zasobów od Internetu:

- Natywne kontroli systemu Azure. [Zapora usługi Azure](../firewall/overview.md) i [zapory aplikacji sieci web w usłudze Application Gateway](../application-gateway/overview.md#web-application-firewall) oferują podstawowe zabezpieczenia za pomocą zapory stanowe pełni jako usługa, wbudowanej wysokiej dostępności, skalowalności chmury bez ograniczeń, filtrowanie w pełni kwalifikowaną nazwę domeny , obsługa podstawowych zestawów reguł OWASP i prostą instalację i konfigurację.
- Oferty innych firm. Wyszukiwanie [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/) dla następnej generacji zapory (nowej NGFW) i ofert innych firm, które zapewniają zabezpieczenia dobrze znanych narzędzi i znacznie udoskonalonych poziomów zabezpieczeń sieci. Konfiguracja może być bardziej skomplikowane, ale oferty innych firm mogą umożliwić użycie istniejących możliwości i dokładniejsze.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Unikaj zagrożeń w Internecie za pomocą dedykowanego łącza sieci WAN
W wielu organizacjach wybrana opcja hybrydowa trasy IT. Za pomocą hybrydowej IT niektórych zasobów informacji firmy są na platformie Azure i inne pozostają w środowisku lokalnym. W wielu przypadkach niektóre składniki usługi działają na platformie Azure, podczas gdy inne składniki pozostaje w środowisku lokalnym.

W hybrydowych scenariuszy IT jest zazwyczaj pewien rodzaj łączności między środowiskami lokalnymi. Łączność między wieloma lokalizacjami umożliwia firmy do łączenia z jego sieciami lokalnymi sieciami wirtualnymi platformy Azure. Dostępne są dwa rozwiązania w zakresie połączeń obejmujących wiele lokalizacji:

* [Site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Jest to technologia zaufanej, niezawodnej i ustanowionych, ale połączenie odbywa się za pośrednictwem Internetu. Przepustowość jest ograniczone do maksymalnie około 200 MB/s. Sieci VPN typu lokacja lokacja to opcja pożądana w niektórych scenariuszach.
* **Azure ExpressRoute**. Firma Microsoft zaleca użycie [ExpressRoute](../expressroute/expressroute-introduction.md) dla łączności między środowiskami lokalnymi. Usługa ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami chmurowymi firmy Microsoft, takich jak Azure, usługi Office 365 i Dynamics 365. Usługa ExpressRoute jest dedykowany sieci WAN łącze między Twojej lokalizacji lokalnej lub dostawcy usług hostingowych programu Microsoft Exchange. Ponieważ jest to połączenie telco, dane nie są przesyłane przez internet, tak nie jest widoczne potencjalnych zagrożeń komunikację z Internetem.

Lokalizacja połączenie usługi ExpressRoute może mieć wpływ na zapory wydajności, skalowalności, niezawodności i widoczność ruchu sieciowego. Należy wskazać, gdzie można zakończyć usługi ExpressRoute w istniejących sieci (lokalnej). Możesz:

- W razie potrzeby widoczność ruchu sieciowego, jeśli należy kontynuować, istniejące rozwiązanie polegające na izolowanie centrów danych lub jeśli chcesz tylko umieścić zasoby ekstranetowe na platformie Azure i zakończyć znajdujących się za zaporą (paradygmat sieci obwodowej).
- Zakończ wewnątrz zapory (paradygmat rozszerzenia sieci). Jest to określenia domyślnego zalecenia. We wszystkich innych przypadkach zaleca się traktowanie platformy Azure jako n-ty centrum danych.

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

## <a name="next-steps"></a>Kolejne kroki
Zobacz [zabezpieczeń platformy Azure najlepsze rozwiązania i wzorce](security-best-practices-and-patterns.md) dla więcej najważniejsze wskazówki dotyczące zabezpieczeń do użycia podczas one projektowanie, wdrażanie i zarządzanie rozwiązań w chmurze, korzystając z platformy Azure.
