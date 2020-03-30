---
title: Najważniejsze wskazówki dotyczące zabezpieczeń sieci — Microsoft Azure
description: Ten artykuł zawiera zestaw najlepszych rozwiązań dotyczących zabezpieczeń sieci przy użyciu wbudowanych funkcji platformy Azure.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 3ded20f37a394e6adf726ad40c01aa36d41e4e8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299349"
---
# <a name="azure-best-practices-for-network-security"></a>Najważniejsze wskazówki dotyczące zabezpieczeń sieci
W tym artykule omówiono kolekcję najlepszych rozwiązań platformy Azure w celu zwiększenia zabezpieczeń sieci. Te najlepsze rozwiązania pochodzą z naszego środowiska z siecią platformy Azure i środowiskami klientów takich jak Ty.

Dla każdej najlepszej praktyki w tym artykule wyjaśniono:

* Jakie są najlepsze praktyki
* Dlaczego chcesz włączyć tę najlepszą praktykę
* Co może być wynikiem, jeśli nie można włączyć najlepsze praktyki
* Możliwe alternatywy dla najlepszych praktyk
* Jak możesz nauczyć się włączać najlepsze praktyki

Te najlepsze rozwiązania są oparte na opinii konsensusu i możliwości platformy Azure i zestawy funkcji, ponieważ istnieją one w momencie pisania tego artykułu. Opinie i technologie zmieniają się w czasie, a ten artykuł będzie regularnie aktualizowany w celu odzwierciedlenia tych zmian.

## <a name="use-strong-network-controls"></a>Korzystanie z silnych elementów sterujących siecią
[Maszyny wirtualne platformy Azure (VM)](https://azure.microsoft.com/services/virtual-machines/) i urządzenia można połączyć z innymi urządzeniami sieciowymi, umieszczając je w [sieciach wirtualnych platformy Azure.](../../virtual-network/index.yml) Oznacza to, że można połączyć karty interfejsu sieci wirtualnej z siecią wirtualną, aby umożliwić komunikację opartą na TCP/IP między urządzeniami obsługującymi sieć. Maszyny wirtualne połączone z siecią wirtualną platformy Azure mogą łączyć się z urządzeniami w tej samej sieci wirtualnej, różnych sieciach wirtualnych, Internecie lub własnych sieciach lokalnych.

Podczas planowania sieci i bezpieczeństwa sieci zalecamy scentralizowanie:

- Zarządzanie funkcjami sieci podstawowej, takimi jak usługa ExpressRoute, inicjowanie obsługi administracyjnej sieci wirtualnej i podsieci oraz adresowanie IP.
- Zarządzanie elementami zabezpieczeń sieci, takimi jak funkcje wirtualnego urządzenia sieciowego, takie jak usługa ExpressRoute, inicjowanie obsługi administracyjnej sieci wirtualnej i podsieci oraz adresowanie IP.

Jeśli używasz wspólnego zestawu narzędzi do zarządzania do monitorowania sieci i bezpieczeństwa sieci, uzyskasz wyraźny wgląd w obie te kwestie. Prosta, ujednolicona strategia zabezpieczeń zmniejsza liczbę błędów, ponieważ zwiększa ludzkie zrozumienie i niezawodność automatyzacji.

## <a name="logically-segment-subnets"></a>Logicznie segmentu podsieci
Sieci wirtualne platformy Azure są podobne do sieci LAN w sieci lokalnej. Ideą sieci wirtualnej platformy Azure jest utworzenie sieci opartej na jednej prywatnej przestrzeni adresowej IP, na której można umieścić wszystkie maszyny wirtualne platformy Azure. Prywatne przestrzenie adresowe IP znajdują się w zakresach klasy A (10.0.0.0/8), klasy B (172.16.0.0/12) i klasy C (192.168.0.0/16).

Najważniejsze wskazówki dotyczące logicznego segmentowania podsieci obejmują:

**Najlepsze rozwiązanie:** Nie przypisuj reguł zezwalaj z szerokimi zakresami (na przykład zezwalaj na od 0,0,0 do 255 255 255 255 255).  
**Szczegóły**: Upewnij się, że procedury rozwiązywania problemów zniechęcają lub zakazują konfigurowania tego typu reguł. Pozwalają one reguły prowadzą do fałszywego poczucia bezpieczeństwa i są często spotykane i wykorzystywane przez czerwone zespoły.

**Najlepsze rozwiązanie:** Segmentowanie większej przestrzeni adresowej w podsieci.   
**Szczegóły:** Do tworzenia podsieci użyj zasad podsieci opartych na [cidr.](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

**Najlepsze rozwiązanie:** Tworzenie kontroli dostępu do sieci między podsieciami. Routing między podsieciami odbywa się automatycznie i nie trzeba ręcznie konfigurować tabel routingu. Domyślnie nie ma żadnych formantów dostępu do sieci między podsieciami, które można utworzyć w sieci wirtualnej platformy Azure.   
**Szczegóły:** Użyj [sieciowej grupy zabezpieczeń,](/azure/virtual-network/virtual-networks-nsg) aby chronić przed niezamawianym ruchem w podsieciach platformy Azure. Sieciowe grupy zabezpieczeń to proste, stanowe urządzenia do sprawdzania pakietów, które używają podejścia 5-krotki (źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy i protokół warstwy 4) w celu utworzenia reguł zezwalania/odrzucania ruchu sieciowego. Zezwalasz lub odmówisz ruchu do i z jednego adresu IP, do i z wielu adresów IP lub do i z całych podsieci.

Korzystając z sieciowych grup zabezpieczeń do kontroli dostępu do sieci między podsieciami, można umieścić zasoby należące do tej samej strefy zabezpieczeń lub roli w ich własnych podsieciach.

**Najlepsze rozwiązanie:** Unikaj małych sieci wirtualnych i podsieci, aby zapewnić prostotę i elastyczność.   
**Szczegóły:** Większość organizacji dodaje więcej zasobów niż początkowo planowano, a ponowne przydzielanie adresów jest pracochłonne. Za pomocą małych podsieci dodaje ograniczoną wartość zabezpieczeń i mapowanie sieciowej grupy zabezpieczeń do każdej podsieci dodaje obciążenie. Zdefiniuj podsieci szeroko, aby zapewnić elastyczność wzrostu.

**Najlepsze rozwiązanie**: Uprość zarządzanie regułami sieciowej grupy zabezpieczeń, definiując [grupy zabezpieczeń aplikacji](https://azure.microsoft.com/blog/applicationsecuritygroups/).  
**Szczegóły:** Zdefiniuj grupę zabezpieczeń aplikacji dla list adresów IP, które ich zdaniem mogą ulec zmianie w przyszłości lub być używane w wielu grupach zabezpieczeń sieciowych. Pamiętaj, aby wyraźnie nazwać grupy zabezpieczeń aplikacji, aby inni mogli zrozumieć ich zawartość i cel.

## <a name="adopt-a-zero-trust-approach"></a>Przyjęcie podejścia zero zaufania
Sieci obwodowe działają przy założeniu, że można ufać wszystkim systemom w sieci. Ale dzisi pracownicy uzyskują dostęp do zasobów swojej organizacji z dowolnego miejsca na różnych urządzeniach i aplikacjach, co sprawia, że kontrole zabezpieczeń obwodowych nie mają znaczenia. Zasady kontroli dostępu, które koncentrują się tylko na tym, kto może uzyskać dostęp do zasobu, nie są wystarczające. Aby opanować równowagę między zabezpieczeniami a *wydajnością,* administratorzy zabezpieczeń muszą również uwzględnić sposób uzyskiwania dostępu do zasobu.

Sieci muszą ewoluować od tradycyjnych zabezpieczeń, ponieważ sieci mogą być narażone na naruszenia: osoba atakująca może naruszyć pojedynczy punkt końcowy w zaufanej granicy, a następnie szybko rozwinąć przyczółek w całej sieci. [Sieci Zero Trust](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) eliminują koncepcję zaufania na podstawie lokalizacji sieciowej w obwodzie. Zamiast tego architektury Zero Trust używają oświadczeń o zaufaniu urządzeń i użytkowników, aby uzyskać dostęp do danych i zasobów organizacji. W przypadku nowych inicjatyw należy przyjąć podejścia Zero Trust, które weryfikują zaufanie w momencie uzyskiwania dostępu.

Najważniejsze wskazówki to:

**Najlepsze rozwiązanie:** Zapewnij dostęp warunkowy do zasobów na podstawie urządzenia, tożsamości, pewności, lokalizacji sieciowej i innych.  
**Szczegóły:** [Dostęp warunkowy usługi Azure AD](/azure/active-directory/conditional-access/overview) umożliwia zastosowanie odpowiednich kontroli dostępu przez implementowanie decyzji o automatycznej kontroli dostępu na podstawie wymaganych warunków. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do zarządzania platformą Azure za pomocą programu Dostęp warunkowy](../../role-based-access-control/conditional-access-azure-management.md).

**Najlepsze rozwiązanie:** Włącz dostęp do portu tylko po zatwierdzeniu przepływu pracy.  
**Szczegóły:** Możesz użyć [dostępu do maszyn wirtualnych just-in-time w usłudze Azure Security Center,](../../security-center/security-center-just-in-time.md) aby zablokować ruch przychodzący do maszyn wirtualnych platformy Azure, zmniejszając narażenie na ataki, zapewniając jednocześnie łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby.

**Najlepsze rozwiązanie:** Udzielanie tymczasowych uprawnień do wykonywania uprzywilejowanych zadań, co uniemożliwia złośliwym lub nieautoryzowanym użytkownikom uzyskanie dostępu po wygaśnięciu uprawnień. Dostęp jest udzielany tylko wtedy, gdy użytkownicy go potrzebują.  
**Szczegóły:** Użyj dostępu just-in-time w usłudze Azure AD Privileged Identity Management lub w rozwiązaniu innej firmy, aby udzielić uprawnień do wykonywania zadań uprzywilejowanych.

Zero Trust to kolejna ewolucja w zakresie bezpieczeństwa sieci. Stan cyberataków skłania organizacje do przyjęcia mentalności "zakładaj naruszenie", ale takie podejście nie powinno ograniczać. Sieci Zero Trust chronią dane i zasoby firmowe, zapewniając jednocześnie organizacjom możliwość tworzenia nowoczesnego miejsca pracy przy użyciu technologii, które umożliwiają pracownikom produktywność w dowolnym miejscu i czasie.

## <a name="control-routing-behavior"></a>Sterowanie zachowaniem routingu
Po umieszczeniu maszyny wirtualnej w sieci wirtualnej platformy Azure maszyna wirtualna może łączyć się z dowolną inną maszyną wirtualną w tej samej sieci wirtualnej, nawet jeśli inne maszyny wirtualne znajdują się w różnych podsieciach. Jest to możliwe, ponieważ kolekcja tras systemowych włączonych domyślnie umożliwia tego typu komunikacji. Te trasy domyślne umożliwiają maszynom wirtualnym w tej samej sieci wirtualnej inicjowanie połączeń między sobą i z Internetem (tylko w przypadku komunikacji wychodzącej z Internetem).

Chociaż domyślne trasy systemowe są przydatne w wielu scenariuszach wdrażania, istnieją chwile, w których chcesz dostosować konfigurację routingu dla wdrożeń. Adres następnego przeskoku można skonfigurować tak, aby docierał do określonych miejsc docelowych.

Zaleca się skonfigurowanie [tras zdefiniowanych przez użytkownika](../../virtual-network/virtual-networks-udr-overview.md) podczas wdrażania urządzenia zabezpieczającego dla sieci wirtualnej. Mówimy o tym w dalszej sekcji zatytułowanej [zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych.](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks)

> [!NOTE]
> Trasy zdefiniowane przez użytkownika nie są wymagane, a domyślne trasy systemowe zwykle działają.
>
>

## <a name="use-virtual-network-appliances"></a>Korzystanie z urządzeń sieci wirtualnej
Sieciowe grupy zabezpieczeń i routing zdefiniowany przez użytkownika mogą zapewnić pewną miarę bezpieczeństwa sieci w warstwach sieci i transportu [modelu OSI](https://en.wikipedia.org/wiki/OSI_model). Ale w niektórych sytuacjach chcesz lub trzeba włączyć zabezpieczenia na wysokim poziomie stosu. W takich sytuacjach zaleca się wdrożenie urządzeń zabezpieczeń sieci wirtualnej dostarczonych przez partnerów platformy Azure.

Urządzenia zabezpieczeń sieci platformy Azure mogą zapewniać lepsze zabezpieczenia niż formanty na poziomie sieci. Funkcje zabezpieczeń sieciowych urządzeń zabezpieczających sieci wirtualnej obejmują:


* Zapory sieciowe
* Wykrywanie włamań/zapobieganie włamaniom
* Zarządzanie lukami w zabezpieczeniach
* Sterowanie aplikacjami
* Wykrywanie anomalii sieciowe
* Filtrowanie sieci web
* Oprogramowanie antywirusowe
* Ochrona botnetu

Aby znaleźć dostępne urządzenia zabezpieczeń sieci wirtualnej platformy Azure, przejdź do [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/) i wyszukaj hasło "zabezpieczenia" i "zabezpieczenia sieci".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Wdrażanie sieci obwodowych dla stref zabezpieczeń
[Sieć obwodowa](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) (znana również jako strefa DMZ) to fizyczny lub logiczny segment sieci, który zapewnia dodatkową warstwę zabezpieczeń między zasobami a Internetem. Wyspecjalizowane urządzenia kontroli dostępu do sieci na krawędzi sieci obwodowej umożliwiają tylko żądany ruch do sieci wirtualnej.

Sieci obwodowe są przydatne, ponieważ można skupić zarządzanie kontrolą dostępu do sieci, monitorowanie, rejestrowanie i raportowanie na urządzeniach na obrzeżach sieci wirtualnej platformy Azure. Sieć obwodowa to miejsce, w którym zazwyczaj można włączyć zapobieganie rozproszonym atakom typu "odmowa usługi" (DDoS), systemy wykrywania włamań/zapobiegania włamaniom (IDS/IPS), reguły i zasady zapory, filtrowanie sieci Web, ochrona przed złośliwym oprogramowaniem sieciowym i inne. Sieciowe urządzenia zabezpieczające znajdują się między Internetem a siecią wirtualną platformy Azure i mają interfejs w obu sieciach.

Chociaż jest to podstawowa konstrukcja sieci obwodowej, istnieje wiele różnych wzorów, takich jak back-to-back, tri-homed i multi-homed.

Na podstawie koncepcji Zero Trust wspomniano wcześniej, zaleca się, aby rozważyć użycie sieci obwodowej dla wszystkich wdrożeń wysokiego poziomu zabezpieczeń w celu zwiększenia poziomu zabezpieczeń sieci i kontroli dostępu dla zasobów platformy Azure. Można użyć platformy Azure lub rozwiązania innej firmy, aby zapewnić dodatkową warstwę zabezpieczeń między zasobami a Internetem:

- Formanty natywne platformy Azure. [Zapora azure](/azure/firewall/overview) i [zapora aplikacji sieci web w bramie aplikacji](../../application-gateway/features.md#web-application-firewall) oferują podstawowe zabezpieczenia z w pełni stanową zaporą jako usługą, wbudowaną wysoką dostępnością, nieograniczoną skalowalnością chmury, filtrowaniem FQDN, obsługą podstawowych zestawów reguł OWASP oraz prostą konfiguracją i konfiguracją.
- Oferty innych firm. Przeszukaj [witrynę Azure Marketplace](https://azuremarketplace.microsoft.com/) w poszukiwaniu zapory nowej generacji (NGFW) i innych ofert innych firm, które zapewniają znane narzędzia zabezpieczeń i znacznie ulepszone poziomy zabezpieczeń sieci. Konfiguracja może być bardziej złożona, ale oferta innej firmy może umożliwiać korzystanie z istniejących funkcji i zestawów umiejętności.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Unikaj kontaktu z Internetem dzięki dedykowanym linkom WAN
Wiele organizacji wybrało hybrydową trasę IT. W przypadku hybrydowego rozwiązania IT niektóre zasoby informacyjne firmy znajdują się na platformie Azure, a inne pozostają w środowisku lokalnym. W wielu przypadkach niektóre składniki usługi są uruchomione na platformie Azure, podczas gdy inne składniki pozostają w środowisku lokalnym.

W hybrydowym scenariuszu IT zwykle istnieje pewien rodzaj łączności międzylokacjowej. Łączność między lokalizacjami umożliwia firmie łączenie sieci lokalnych z sieciami wirtualnymi platformy Azure. Dostępne są dwa rozwiązania łączności międzylokacjowej:

* [Sieć VPN typu lokacja-lokacja](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Jest to zaufana, niezawodna i ugruntowana technologia, ale połączenie odbywa się przez Internet. Przepustowość jest ograniczona do maksymalnie około 1,25 Gb/s. Sieć VPN typu lokacja lokacja jest pożądaną opcją w niektórych scenariuszach.
* **Usługa Azure ExpressRoute**. Zaleca się używanie usługi [ExpressRoute](../../expressroute/expressroute-introduction.md) do łączności międzylokacjowej. Usługa ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Za pomocą usługi ExpressRoute można nawiązywać połączenia z usługami w chmurze firmy Microsoft, takimi jak Azure, Office 365 i Dynamics 365. Usługa ExpressRoute to dedykowane łącze sieci WAN między lokalizacją lokalną lub dostawcą hostingu programu Microsoft Exchange. Ponieważ jest to połączenie telekomunikacyjne, Twoje dane nie są przesyłane przez Internet, więc nie są narażone na potencjalne ryzyko związane z komunikacją internetową.

Lokalizacja połączenia usługi ExpressRoute może mieć wpływ na pojemność zapory, skalowalność, niezawodność i widoczność ruchu sieciowego. Musisz określić, gdzie zakończyć program ExpressRoute w istniejących sieciach (lokalnych). Możesz:

- Zakończ poza zaporą (paradygmat sieci obwodowej), jeśli potrzebujesz wglądu w ruch, jeśli musisz kontynuować istniejącą praktykę izolowania centrów danych lub jeśli umieszczasz wyłącznie zasoby ekstranetu na platformie Azure.
- Zakończ wewnątrz zapory (paradygmat rozszerzenia sieci). Jest to domyślne zalecenie. We wszystkich innych przypadkach zaleca się traktowanie platformy Azure jako n-tego centrum danych.

## <a name="optimize-uptime-and-performance"></a>Optymalizacja czasu pracy i wydajności bez przestojów
Jeśli usługa jest wyłączona, nie można uzyskać dostępu do informacji. Jeśli wydajność jest tak niska, że dane są bezużyteczne, można uznać, że dane są niedostępne. Z punktu widzenia zabezpieczeń musisz zrobić wszystko, co w twojej mocy, aby upewnić się, że twoje usługi mają optymalny czas pracy bez przestojów i wydajność.

Popularną i skuteczną metodą zwiększania dostępności i wydajności jest równoważenie obciążenia. Równoważenie obciążenia to metoda dystrybucji ruchu sieciowego między serwerami, które są częścią usługi. Na przykład jeśli masz serwery sieci web frontu jako część usługi, można użyć równoważenia obciążenia do dystrybucji ruchu na wielu serwerach sieci web frontu.

Ta dystrybucja ruchu zwiększa dostępność, ponieważ jeśli jeden z serwerów sieci web stanie się niedostępny, moduł równoważenia obciążenia przestaje wysyłać ruch do tego serwera i przekierowuje go do serwerów, które są nadal w trybie online. Równoważenie obciążenia pomaga również wydajność, ponieważ procesor, sieć i pamięć obciążenie dla obsługi żądań jest rozłożone na wszystkich serwerach z równoważeniem obciążenia.

Zaleca się stosowanie równoważenia obciążenia, kiedy tylko jest to możliwe i odpowiednio do usług. Poniżej przedstawiono scenariusze na poziomie sieci wirtualnej platformy Azure i na poziomie globalnym, a także opcje równoważenia obciążenia dla każdego z nich.

**Scenariusz:** Masz aplikację, która:

- Wymaga żądań z tej samej sesji użytkownika/klienta, aby dotrzeć do tej samej maszyny wirtualnej zaplecza. Przykładem tego są aplikacje koszyka zakupów i serwery poczty internetowej.
- Akceptuje tylko bezpieczne połączenie, więc niezaszyfrowana komunikacja z serwerem nie jest akceptowalną opcją.
- Wymaga wielu żądań HTTP na tym samym długotrwałym połączeniu TCP, które mają być kierowane lub równoważenia obciążenia do różnych serwerów zaplecza.

**Opcja równoważenia obciążenia:** Użyj [bramy aplikacji azure](/azure/application-gateway/application-gateway-introduction), moduł równoważenia obciążenia ruchu internetowego HTTP. Brama aplikacji obsługuje szyfrowanie SSL typu end-to-end i [zakończenie SSL](/azure/application-gateway/application-gateway-introduction) na bramie. Serwery sieci Web mogą być następnie odciążone od szyfrowania i odszyfrowywania narzutów oraz ruchu przepływającego w stanie niezaszyfrowanym do serwerów zaplecza.

**Scenariusz:** Należy załadować równoważenie połączeń przychodzących z Internetu między serwerami znajdującymi się w sieci wirtualnej platformy Azure. Scenariusze są wtedy, gdy:

- Mają aplikacje bezstanowe, które akceptują przychodzące żądania z Internetu.
- Nie wymagaj lepkich sesji ani odciążania SSL. Sesje przyklejone to metoda używana z równoważeniem obciążenia aplikacji w celu osiągnięcia koligacji serwera.

**Opcja równoważenia obciążenia:** Użyj witryny Azure portal, aby [utworzyć zewnętrzny moduł równoważenia obciążenia,](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) który rozkłada przychodzące żądania na wiele maszyn wirtualnych, aby zapewnić wyższy poziom dostępności.

**Scenariusz:** Należy załadować połączenia równoważenia z maszyn wirtualnych, które nie są w Internecie. W większości przypadków połączenia, które są akceptowane dla równoważenia obciążenia są inicjowane przez urządzenia w sieci wirtualnej platformy Azure, takich jak wystąpienia programu SQL Server lub wewnętrzne serwery sieci web.   
**Opcja równoważenia obciążenia:** Użyj witryny Azure portal, aby [utworzyć wewnętrzny moduł równoważenia obciążenia,](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) który rozkłada przychodzące żądania na wiele maszyn wirtualnych, aby zapewnić wyższy poziom dostępności.

**Scenariusz:** Potrzebujesz globalnego równoważenia obciążenia, ponieważ:

- Mieć rozwiązanie w chmurze, które jest szeroko rozpowszechnione w wielu regionach i wymaga najwyższego poziomu dostępności (dostępności) możliwe.
- Potrzebujesz najwyższego możliwego poziomu czasu pracy, aby upewnić się, że usługa jest dostępna, nawet jeśli całe centrum danych stanie się niedostępne.

**Opcja równoważenia obciążenia:** użyj usługi Azure Traffic Manager. Usługa Traffic Manager umożliwia równoważenie połączeń z usługami na podstawie lokalizacji użytkownika.

Jeśli na przykład użytkownik złoży żądanie do usługi z UE, połączenie zostanie przekierowane do usług znajdujących się w centrum danych UE. Ta część globalnego równoważenia obciążenia usługi Traffic Manager pomaga zwiększyć wydajność, ponieważ łączenie się z najbliższym centrum danych jest szybsze niż łączenie się z centrami danych, które są daleko.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Wyłączanie dostępu RDP/SSH do maszyn wirtualnych
Istnieje możliwość osiągnięcia maszyn wirtualnych platformy Azure przy użyciu protokołu RDP [(Remote Desktop Protocol)](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) i protokołu [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Protokoły te umożliwiają zarządzanie maszynami wirtualnymi z lokalizacji zdalnych i są używane jako standard w centrach danych.

Potencjalny problem z zabezpieczeniami przy użyciu tych protokołów przez Internet jest, że osoby atakujące można użyć [technik brutalnej siły,](https://en.wikipedia.org/wiki/Brute-force_attack) aby uzyskać dostęp do maszyn wirtualnych platformy Azure. Po uzyskaniu dostępu atakujący mogą korzystać z maszyny wirtualnej jako punktu uruchamiania w celu naruszenia zabezpieczeń innych maszyn w sieci wirtualnej, a nawet zaatakowania urządzeń sieciowych poza platformą Azure.

Zaleca się wyłączenie bezpośredniego dostępu RDP i SSH do maszyn wirtualnych platformy Azure z Internetu. Po wyłączenia bezpośredniego dostępu RDP i SSH z Internetu dostępne są inne opcje, za pomocą których można uzyskać dostęp do tych maszyn wirtualnych do zdalnego zarządzania.

**Scenariusz:** Włącz pojedynczego użytkownika, aby połączyć się z siecią wirtualną platformy Azure przez Internet.   
**Opcja:** [Sieć VPN typu punkt-lokacja](/azure/vpn-gateway/vpn-gateway-point-to-site-create) to inny termin połączenia klient/serwer sieci VPN z dostępem zdalnym. Po nawiązaniu połączenia typu punkt-lokacja użytkownik może używać protokołu RDP lub SSH do łączenia się z dowolnymi maszynami wirtualnymi znajdującymi się w sieci wirtualnej platformy Azure, z którą użytkownik łączy się za pośrednictwem sieci VPN typu lokacja. Zakłada się, że użytkownik jest autoryzowany do dotarcia do tych maszyn wirtualnych.

Sieć VPN typu punkt-lokacja jest bezpieczniejsza niż bezpośrednie połączenia RDP lub SSH, ponieważ użytkownik musi uwierzytelnić się dwukrotnie przed nawiązaniem połączenia z maszyną wirtualną. Po pierwsze, użytkownik musi uwierzytelnić (i być autoryzowany) w celu ustanowienia połączenia sieci VPN typu punkt-lokacja. Po drugie użytkownik musi uwierzytelnić (i być autoryzowany) w celu ustanowienia sesji RDP lub SSH.

**Scenariusz:** Włącz użytkownikom w sieci lokalnej łączenie się z maszynami wirtualnymi w sieci wirtualnej platformy Azure.   
**Opcja:** [Sieć VPN typu lokacja-lokacja](/azure/vpn-gateway/vpn-gateway-site-to-site-create) łączy całą sieć z inną siecią przez Internet. Sieci VPN lokacji lokacji można połączyć sieć lokalną z siecią wirtualną platformy Azure. Użytkownicy w sieci lokalnej łączą się przy użyciu protokołu RDP lub SSH za pośrednictwem połączenia sieci VPN lokacja-lokacja. Nie musisz zezwalać na bezpośredni dostęp do PROW lub SSH przez Internet.

**Scenariusz:** Użyj dedykowanego łącza sieci WAN, aby zapewnić funkcje podobne do sieci VPN lokacja-lokacja.   
**Opcja**: Użyj [usługi ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Zapewnia funkcje podobne do sieci VPN lokacja-lokacja. Główne różnice to:

- Dedykowane łącze WAN nie przechodzi przez Internet.
- Dedykowane łącza WAN są zazwyczaj bardziej stabilne i działają lepiej.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Zabezpiecz krytyczne zasoby usługi platformy Azure tylko w sieciach wirtualnych
Punkty końcowe usługi sieci wirtualnej umożliwia rozszerzenie prywatnej przestrzeni adresowej sieci wirtualnej oraz tożsamości sieci wirtualnej na usługi platformy Azure za pośrednictwem połączenia bezpośredniego. Punkty końcowe umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych. Ruch z sieci wirtualnej do usługi Platformy Azure zawsze pozostaje w sieci szkieletowej platformy Microsoft Azure.

Punkty końcowe usługi oferują następujące korzyści:

- **Lepsze zabezpieczenia zasobów usługi platformy Azure**: dzięki punktom końcowym zasoby usługi platformy Azure mogą być chronione w sieci wirtualnej. Zabezpieczenie zasobów usługi w sieci wirtualnej zapewnia większe bezpieczeństwo dzięki całkowitemu uniemożliwieniu dostępu do tych zasobów z publicznego Internetu i zezwolenie na ruch tylko z Twojej sieci wirtualnej.
- **Optymalne routing dla ruchu usługi platformy Azure z sieci wirtualnej:** Wszystkie trasy w sieci wirtualnej, które wymuszają ruch internetowy do lokalnego i/lub urządzeń wirtualnych, znany jako wymuszone tunelowanie, również wymusić ruch usługi platformy Azure do podjęcia tej samej trasy co ruch internetowy. Punkty końcowe usługi zapewniają optymalny routing ruchu platformy Azure.

  Punkty końcowe zawsze przyjmują ruch usługi bezpośrednio z sieci wirtualnej do usługi w sieci szkieletowej platformy Azure. Utrzymywanie ruchu w sieci szkieletowej platformy Azure umożliwia kontynuowanie inspekcji i monitorowania wychodzącego ruchu internetowego z sieci wirtualnych za pośrednictwem wymuszonego tunelowania bez wpływu na ruch usługi. Dowiedz się więcej o [trasach zdefiniowanych przez użytkownika i wymuszonym tunelowaniu.](../../virtual-network/virtual-networks-udr-overview.md)

- **Proste konfigurowanie z mniejszym obciążeniem dla zarządzania:** nie potrzebujesz już zarezerwowanych, publicznych adresów IP w sieciach wirtualnych, aby zabezpieczyć zasoby platformy Azure za pośrednictwem zapory IP. Do skonfigurowania punktów końcowych usługi nie jest wymagany translator adresów sieciowych ani urządzenie bramy. Punkty końcowe usługi można skonfigurować za pomocą prostego kliknięcia w podsieci. Nie ma żadnych dodatkowych narzutów do utrzymania punktów końcowych.

Aby dowiedzieć się więcej o punktach końcowych usługi oraz usługach i regionach platformy Azure, dla których są dostępne punkty końcowe usługi, zobacz [Punkty końcowe usługi sieci wirtualnej.](../../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="next-steps"></a>Następne kroki
Zobacz [najlepsze rozwiązania i wzorce zabezpieczeń platformy Azure,](best-practices-and-patterns.md) aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, których należy używać podczas projektowania, wdrażania i zarządzania rozwiązaniami w chmurze przy użyciu platformy Azure.
