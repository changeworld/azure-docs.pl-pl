---
title: Najlepsze rozwiązania sieci platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zestaw najlepsze rozwiązania dotyczące sieci zabezpieczeń przy użyciu wbudowanych funkcji platformy Azure.
services: security
documentationcenter: na
author: TomShinder
manager: swadhwa
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 3dee3411dadbca5e88951dec2ed1836d440423c4
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2017
---
# <a name="azure-network-security-best-practices"></a>Najlepsze rozwiązania sieci platformy Azure
Microsoft Azure umożliwia podłączenie maszyn wirtualnych i urządzenia do innych urządzeń sieciowych, umieszczając je w sieciach wirtualnych platformy Azure. Sieci wirtualnej platformy Azure jest konstrukcję umożliwia łączenie z karty interfejsu sieci wirtualnej do sieci wirtualnej, aby umożliwić opartych na protokole TCP/IP komunikację między urządzeniami sieciowymi włączone. Azure maszyny wirtualne podłączone do sieci wirtualnej platformy Azure będą mogli nawiązać połączenia z urządzeń w tej samej sieci wirtualnej Azure, różnych sieciach wirtualnych platformy Azure, w Internecie lub nawet w sieci lokalnej.

W tym artykule omówiono kolekcja najlepszych rozwiązań dotyczących zabezpieczeń sieci platformy Azure. Następujące najlepsze rozwiązania są uzyskiwane z wiemy z doświadczenia z platformy Azure w sieci i jej klientów, takich jak samodzielnie.

Dla każdego ze względów wyjaśniamy:

* Co to jest najlepszym rozwiązaniem
* Dlaczego chcesz włączyć tej najlepsze praktyki
* Jeśli nie zostanie włączone najlepszym rozwiązaniem, co może być skutkiem
* Możliwe alternatywy najlepsze praktyki
* Jak można znaleźć umożliwiające najlepsze praktyki

W tym artykule Azure sieci najlepsze rozwiązania w zakresie zabezpieczeń jest na podstawie opinii konsensu i funkcji platformy Azure i zestawy funkcji istniejących w chwili ten artykuł dotyczy. Opinie i technologie ulegną zmianom, a ten artykuł będzie aktualizowany na bieżąco w celu odzwierciedlenia tych zmian.

Azure sieci najlepsze rozwiązania omówione w tym artykule obejmują:

* Logicznie segmentu podsieci
* Kontrolowania zachowania routingu
* Włączanie tunelowania wymuszonego
* Użyj wirtualnych urządzeń sieciowych
* Wdrażanie sieci obwodowej podziału na strefy zabezpieczeń
* Unikaj narażenia z Internetem za pomocą dedykowanego łącza sieci WAN
* Optymalizuj czas działania i wydajności
* Użyj równoważenia obciążenia globalne
* Wyłączanie dostępu RDP do maszyn wirtualnych platformy Azure
* Centrum zabezpieczeń Azure Włącz
* Rozszerzanie centrum danych na platformie Azure

## <a name="logically-segment-subnets"></a>Logicznie segmentu podsieci
[Sieci wirtualnych platformy Azure](https://azure.microsoft.com/documentation/services/virtual-network/) są podobne do sieci LAN w sieci lokalnej. W założeniu sieci wirtualnej platformy Azure jest utworzenie pojedynczej prywatny adres przestrzennych sieci IP na którym można umieścić wszystkie Twoje [maszyny wirtualne Azure](https://azure.microsoft.com/services/virtual-machines/). Prywatnych przestrzeni adresów IP dostępne w klasy A (10.0.0.0/8) klasy B (172.16.0.0/12) i zakresy klasy C (192.168.0.0/16).

Podobnie jak co zrobić lokalnie, należy podzielić większą przestrzeń adresową na podsieci. Można użyć [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) na podstawie zasad podsieci można utworzyć podsieci.

Routing między podsieciami nastąpi automatycznie i nie trzeba ręcznie skonfigurować tabele routingu. Jednak domyślne ustawienie zakłada, że bez kontroli dostępu do sieci między podsieciami, utworzone w sieci wirtualnej platformy Azure. Aby można było utworzyć kontroli dostępu do sieci między podsieciami, należy umieścić coś między podsieciami.

Jednym z elementów, można użyć do wykonania tego zadania jest [sieciowej grupy zabezpieczeń](../virtual-network/virtual-networks-nsg.md) (NSG). Grupy NSG są proste pakietów kontroli urządzeń, które używają 5 parametrów (źródłowy adres IP, port źródłowy, docelowy adres IP, docelowy port i protokół warstwy 4) podejście do tworzenia zezwalania/niezezwalania reguły dla ruchu sieciowego. Można akceptować lub odrzucać ruch do i z pojedynczego adresu IP do i z wielu adresów IP lub nawet do i z całej podsieci.

Za pomocą grup NSG kontroli dostępu do sieci między podsieciami pozwala umieścić zasoby, które należą do tej samej strefie zabezpieczeń lub roli w swoich własnych podsieciach. Na przykład traktować prostą aplikację 3-warstwowej warstwa sieci web, warstwy logiki aplikacji i warstwy bazy danych. Możesz zaznaczyć maszyn wirtualnych, które należą do każdego z tych warstw w swoich własnych podsieciach. Następnie można użyć grupy NSG, aby kontrolować ruch między podsieciami:

* Maszyny wirtualne warstwy sieci Web tylko mogą inicjować połączenia z maszynami logiki aplikacji i może akceptować tylko połączenia z Internetu
* Maszyny wirtualne logiki aplikacji tylko mogą inicjować połączenia z warstwą bazy danych i może akceptować tylko połączenia z warstwą sieci web
* Maszyny wirtualne warstwy bazy danych nie można zainicjować połączenia z niczego poza ich własnych podsieci i może akceptować tylko połączenia z warstwy logiki aplikacji

Aby dowiedzieć się więcej o grup zabezpieczeń sieci i sposobie ich użycia logicznie segmentów sieci wirtualne platformy Azure, przeczytaj artykuł [co to jest grupa zabezpieczeń sieci](../virtual-network/virtual-networks-nsg.md) (NSG).

## <a name="control-routing-behavior"></a>Kontrolowania zachowania routingu
Po umieszczeniu maszyny wirtualnej w sieci wirtualnej platformy Azure można zauważyć, że maszyna wirtualna może łączyć się żadnej innej maszyny wirtualnej w tej samej sieci wirtualnej Azure, nawet jeśli inne maszyny wirtualne są w różnych podsieciach. Dlaczego jest to możliwe dzieje się tak że jest to zbiór tras systemowych, które są domyślnie włączone zezwalające na ten typ komunikacji. Te trasy domyślnej umożliwić maszynom wirtualnym na tej samej sieci wirtualnej platformy Azure do nawiązania połączenia ze sobą oraz z Internetem (dla komunikacji wychodzącej tylko z Internetem).

Trasy systemowe domyślne są przydatne w przypadku wielu wdrożeń, istnieją razy, jeśli chcesz dostosować konfigurację routingu wdrożeń. Te modyfikacje zostaną umożliwiają konfigurowanie adres następnego przeskoku do osiągnięcia określonych miejsc docelowych.

Zaleca się skonfigurowanie trasy zdefiniowane przez użytkownika podczas wdrażania urządzenia zabezpieczeń sieci wirtualnej, które będzie są omawiane w późniejszym najlepszym rozwiązaniem.

> [!NOTE]
> Trasy zdefiniowane przez użytkownika nie są wymagane i tras systemowych domyślne działa w większości przypadków.
>
>

Dowiedz się więcej o trasy zdefiniowane przez użytkownika i sposobach ich konfigurowania, przeczytaj artykuł [co to są trasy zdefiniowane przez użytkownika i przesyłania dalej protokołu IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>Włączanie tunelowania wymuszonego
Aby lepiej zrozumieć tunelowania wymuszonego, warto dowiedzieć się, jakie "podział tunelowania".
Najbardziej typowym przykładem tunelowanie podzielone jest widoczna przy użyciu połączeń VPN. Wyobraź sobie ustanowić połączenie sieci VPN z pokoju hoteli do sieci firmowej. To połączenie zezwala na dostęp do firmowych zasobów i cała komunikacja z sieci firmowej go za pośrednictwem tunelu VPN.

Co się stanie, jeśli chcesz nawiązać połączenia z zasobami przez Internet? Po włączeniu tunelowania podzielonego tych połączeń, przejdź bezpośrednio do Internetu, a nie za pośrednictwem tunelu VPN. Niektóre ekspertów zabezpieczeń należy wziąć pod uwagę to potencjalne ryzyko i dlatego zaleca się, że tunelowanie podzielone wyłączone i wszystkich połączeń, te kierowanego do Internetu i te przeznaczonych dla zasobów firmowych, go za pośrednictwem tunelu VPN. Zaletą tej czynności jest następnie wymuszenie połączenia z Internetem za pośrednictwem sieci firmowej urządzeń zabezpieczeń, które nie będą wielkość liter, jeśli klient sieci VPN połączony z Internetem poza tunel VPN.

Teraz załóżmy przywrócić tym maszynom wirtualnym na sieć wirtualną platformy Azure. Trasy domyślnej dla sieci wirtualnej platformy Azure umożliwiają maszyn wirtualnych zainicjować ruch do Internetu. To zbyt może reprezentować zagrożenie bezpieczeństwa tych połączeń wychodzących można zwiększyć podatność na maszynie wirtualnej i być wykorzystywane przez osoby atakujące.
Z tego powodu zaleca się włączenie wymuszanie tunelowania na maszynach wirtualnych, jeśli masz łączności między lokalizacjami między sieci wirtualnej platformy Azure i siecią lokalną. Omawianiu będzie między lokalnym łączności dalej w tej sieci najlepsze rozwiązania w zakresie dokumentów w usłudze Azure.

Jeśli nie masz połączenia między różnymi lokalizacjami, upewnij się, możesz korzystać z grup zabezpieczeń sieci (opisanych wcześniej) lub Azure wirtualnych sieci urządzenia zabezpieczeń (opisanych dalej), aby uniemożliwić połączenia wychodzące z Internetem z maszyn wirtualnych platformy Azure.

Aby dowiedzieć się więcej o wymuszonego tunelowania i jak włączyć, przeczytaj artykuł [skonfigurować wymuszonego tunelowania przy użyciu programu PowerShell i usługi Azure Resource Manager](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Użyj urządzeń sieci wirtualnej
Gdy grup zabezpieczeń sieci i routingu zdefiniowane użytkownika zapewniają miary zabezpieczeń sieciowych w sieci i transportu warstw [OSI model](https://en.wikipedia.org/wiki/OSI_model), mają być wystąpić sytuacje, w którym będzie mają lub konieczne włączenie zabezpieczeń na wysoki poziom stosu. W takich sytuacjach zalecamy wdrożenie zapewniana przez partnerów Azure urządzenia zabezpieczeń sieci wirtualnej.

Urządzenia zabezpieczeń sieci platformy Azure może zapewnić znaczne zwiększenie poziomu zabezpieczeń przez dostarczanych przez kontrolę poziomu sieci. Oto niektóre z funkcji zabezpieczeń sieci udostępniane przez urządzenia zabezpieczeń sieci wirtualnej:

* Zapory
* Wykrywania nieautoryzowanego dostępu/włamań zapobiegania
* Zarządzanie luki w zabezpieczeniach
* Formant aplikacji
* Wykrywanie anomalii opartych na sieci
* Filtrowanie sieci Web
* Oprogramowanie antywirusowe
* Botnet ochrony

Jeśli potrzebujesz wyższy poziom zabezpieczeń sieci, nie można uzyskać z kontroli dostępu na poziomie sieci, następnie zalecamy zbadać, a następnie wdrożyć urządzenia zabezpieczeń sieci wirtualnej platformy Azure.

Aby dowiedzieć się więcej o jakie sieci wirtualnej platformy Azure są dostępne urządzenia zabezpieczeń i dotyczące ich możliwości, odwiedź stronę [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/) i wyszukaj "zabezpieczenia" i "zabezpieczenia sieciowe".

## <a name="deploy-dmzs-for-security-zoning"></a>Wdrażanie sieci obwodowej podziału na strefy zabezpieczeń
Strefa DMZ lub "w sieci obwodowej" jest segment sieci fizycznej lub logicznej, która zapewnia dodatkową warstwę zabezpieczeń zasobów od Internetu. Celem sieci obwodowej jest umieszczenie urządzenia kontroli dostępu do sieci specjalne na granicy sieci obwodowej, tak aby tylko odpowiednie ruch jest dozwolony, urządzenie sieciowe zabezpieczeń i w sieci wirtualnej platformy Azure.

Sieci obwodowe są przydatne, ponieważ monitorowania, rejestrowania i raportowania na urządzeniach na brzegu sieci wirtualnej platformy Azure można skupić się zarządzanie kontrolą dostępu z sieci. W tym miejscu zwykle czy włączyć zapobiegania DDoS, systemów zapobiegania włamań/wykrywania nieautoryzowanego dostępu (Identyfikatory/adresów IP), reguły zapory i zasady, filtrowanie sieci web, sieci ochrony przed złośliwym oprogramowaniem i więcej. Urządzenia zabezpieczeń sieci znajdują się między Internetu i sieci wirtualnej platformy Azure i interfejs w obu sieciach.

Jest to podstawowy projekt sieci obwodowej, istnieje wiele różnych projektów sieci obwodowej, takich jak symetryczna, adresem IP tri, wieloadresowego i inne.

Firma Microsoft zaleca wdrożeń wysokiego poziomu zabezpieczeń rozważ wdrożenie sieci obwodowej, aby zwiększyć poziom zabezpieczeń sieci dla zasobów platformy Azure.

Aby dowiedzieć się więcej o sieci obwodowej i ich wdrażanie na platformie Azure, przeczytaj artykuł [usług chmurowych firmy Microsoft i zabezpieczeń sieciowych](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Unikaj narażenia z Internetem za pomocą dedykowanego łącza sieci WAN
W wielu organizacjach wybrane trasy IT hybrydowego. Hybrydowy IT niektóre zasobów informacji firmy są na platformie Azure, podczas gdy inne pozostają lokalnymi. W wielu przypadkach niektóre składniki usługi będzie uruchomiony w Azure, podczas gdy inne składniki pozostają lokalnymi.

Hybrydowy scenariusz IT jest zwykle pewien typ łączności między lokalizacjami. To między różnymi lokalizacjami firmy do łączenia ich sieci lokalnej sieci wirtualnych Azure umożliwia łączności. Dostępne są dwa rozwiązania w zakresie łączności między lokalizacjami:

* Sieci VPN typu lokacja lokacja
* ExpressRoute

[Sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-site-to-site-create.md) reprezentuje połączenie prywatnej wirtualnej między siecią lokalną a sieci wirtualnej platformy Azure. To połączenie odbywa się za pośrednictwem Internetu oraz pozwala na informacji "tunnel" wewnątrz zaszyfrowanych łącza między siecią a Azure. Sieci VPN typu lokacja lokacja jest bezpieczne, dojrzała technologia, która jest wdrażany w przedsiębiorstwach wszystkich rozmiarów dekad. Tunel szyfrowanie odbywa się przy użyciu [trybu tunelowania IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Podczas sieci VPN typu lokacja lokacja jest technologią zaufanych, niezawodny i ustalonych ruchu w tunelu przechodzenie przez Internet. Ponadto przepustowość stosunkowo jest ograniczona do maksymalnie o 200 MB/s.

Jeśli potrzebujesz wyjątkowych poziom bezpieczeństwa i wydajności dla połączeń między różnymi lokalizacjami, zalecane jest użycie Azure ExpressRoute dla łączność między lokalizacjami. ExpressRoute jest dedykowanych sieci WAN łącza między Twojej lokalizacji lokalnej lub dostawcy usług hosta programu Exchange. Ponieważ jest to połączenie telco, dane nie są przesyłane w Internecie i w związku z tym nie jest narażony na potencjalne ryzyko związane z komunikacją internetową.

Aby dowiedzieć się więcej o sposobie działania usługi Azure ExpressRoute i sposobu wdrażania, przeczytaj artykuł [opis techniczny ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Optymalizuj czas działania i wydajności
Poufności, integralności i dostępności (CIA) obejmują Triada współczesnych najbardziej znaczenie modelu zabezpieczeń. Poufność jest o szyfrowaniu i zachowania poufności, integralności jest upewnienie się, że dane nie ulega zmianie przez nieautoryzowane osoby i dostępności jest upewnienie się, że uprawnionych osób będą mogli uzyskiwać dostęp do informacji, które użytkownik jest uprawniony do dostępu. Błąd w jednym z tych obszarów reprezentuje potencjalne naruszenie zabezpieczeń.

Dostępność można traktować jako o dostępności i wydajności. Jeśli usługa nie działa, informacje są niedostępne. Jeśli więc niska, aby uniemożliwić korzystanie z tej danych wydajności, możemy należy rozważyć danych jest niedostępna. W związku z tym z punktu widzenia zabezpieczeń, musimy wykonać niezależnie od możemy upewnij się, że naszych usług mają optymalnej dostępności i wydajności.
Metoda popularnych i skuteczne używana w celu zwiększenia dostępności i wydajności jest użycie, równoważenia obciążenia. Równoważenie obciążenia sieciowego jest metoda dystrybucji ruchu sieciowego na serwerach, które są częścią usługi. Na przykład jeśli masz serwerów frontonu sieci web jako część usługi umożliwia równoważenia obciążenia dystrybuowanie ruchu między wiele serwerów frontonu sieci web.

Tej dystrybucji ruchu sieciowego zwiększa dostępność, ponieważ jeśli jeden z serwerów sieci web jest niedostępna, usługi równoważenia obciążenia przestanie wysyłać ruch do tego serwera, a następnie go przekierowuje do serwerów, które są nadal w trybie online. Równoważenie obciążenia sieciowego pomaga również wydajności, ponieważ procesor, sieci i pamięci, że obciążenie obsługująca żądania jest dystrybuowana do wszystkich obciążenia zrównoważonym serwerów.

Zaleca się, że zostanie zastosowana równoważenia obciążenia zawsze można się odpowiednie dla Twojej usługi. Będzie można rozwiązać właściwości w następujących sekcjach: na poziomie sieci wirtualnej platformy Azure, platforma Azure udostępnia z trzech podstawowych ładowania równoważenia opcje:

* Równoważenie obciążenia oparte na protokole HTTP
* Zewnętrzne Równoważenie obciążenia
* Wewnętrzne równoważenie obciążenia

## <a name="http-based-load-balancing"></a>Równoważenie obciążenia oparte na protokole HTTP
Równoważenie obciążenia oparte na protokole HTTP podstawowych decyzji o jakie serwerowi na wysyłanie połączeń za pomocą właściwości protokołu HTTP. Platforma Azure ma usługi równoważenia obciążenia HTTP, który jest przesyłany przez nazwę bramy aplikacji.

Zalecamy, aby użytkownik nam brama aplikacji w usłudze Azure po:

* Aplikacje, które wymagają żądań z tej samej sesji klienta/użytkownika, aby dotrzeć do tej samej maszyny wirtualnej zaplecza. Przykłady to będzie zakupów, koszyka aplikacji i serwerów poczty w sieci web.
* Aplikacje, które mają być narzut korzystając z bramy aplikacji w warstwie bezpłatna farmach serwerów sieci web z kończenia żądań SSL [odciążania SSL](https://f5.com/glossary/ssl-offloading) funkcji.
* Aplikacje, takie jak sieci dostarczania zawartości, które wymagają się, że wiele żądań HTTP na tego samego połączenia protokołu TCP długotrwałe być kierowane lub załadować zrównoważonym do różnych serwerów zaplecza.

Aby dowiedzieć się więcej o sposobie działania usługi Azure Application Gateway i używania go w ramach wdrożeń, przeczytaj artykuł na temat [omówienie bramy aplikacji](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Zewnętrzne Równoważenie obciążenia
Równoważenie obciążenia zewnętrznych ma miejsce, gdy połączenia przychodzące z Internetu jest równoważone między serwerów znajdujących się w sieci wirtualnej platformy Azure. Azure zewnętrznej usługi równoważenia obciążenia zapewnia tej możliwości i zalecane jest użycie jej nie wymagają trwałe sesje lub odciążanie protokołu SSL.

W przeciwieństwie do równoważenia obciążenia opartą na protokole HTTP, zewnętrznej usługi równoważenia obciążenia używa informacji w sieci i transportu warstwy modelu sieci OSI do podejmowania decyzji na serwerze, w jakie połączenie równoważenia obciążenia.

Firma Microsoft zaleca użycie zewnętrznego równoważenia obciążenia zawsze [aplikacji bezstanowych](http://whatis.techtarget.com/definition/stateless-app) akceptować żądania przychodzące z Internetu.

Aby dowiedzieć się więcej na temat sposobu działania Azure zewnętrzną usługą równoważenia obciążenia oraz sposób jego wdrażania przeczytaj artykuł [rozpocząć tworzenie Internet ukierunkowane modułu równoważenia obciążenia w Menedżerze zasobów przy użyciu programu PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Wewnętrzny moduł równoważenia obciążenia
Równoważenie obciążenia wewnętrznego jest podobny do równoważenia obciążenia zewnętrznych i używa ten sam mechanizm załadować saldo połączeń z serwerami za ich. Jedyna różnica polega na tym, że moduł równoważenia obciążenia w tym przypadku akceptowania połączeń z maszyn wirtualnych, które nie są dostępne w Internecie. W większości przypadków połączeń, które są akceptowane Równoważenie obciążenia sieciowego są inicjowane przez urządzenia w sieci wirtualnej platformy Azure.

Firma Microsoft zaleca użycie wewnętrzne Równoważenie obciążenia dla scenariuszy, które korzystają z tej możliwości, takie jak konieczność załadowania saldo połączeń z serwerami SQL lub wewnętrznej sieci web.

Aby dowiedzieć się więcej na temat sposobu działania wewnętrznego równoważenia obciążenia Azure i jak można ją wdrożyć, przeczytaj artykuł [rozpocząć tworzenie przy użyciu programu PowerShell do wewnętrznego modułu równoważenia obciążenia](../load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer).

## <a name="use-global-load-balancing"></a>Użyj równoważenia obciążenia globalne
Obliczeniowych sprawia, że chmura publiczna jego można wdrożyć globalnie dystrybucji aplikacji, które mają składniki znajdujące się w centrach danych na całym świecie. Jest to możliwe w systemie Microsoft Azure z powodu obecności globalne centrum danych platformy Azure. W przeciwieństwie do wymienionych poniżej technologii równoważenia obciążenia globalnego równoważenia obciążenia umożliwia udostępniają usługi, nawet wtedy, gdy cały centrów danych mogą stać się niedostępne.

Możesz uzyskać tego typu globalnego równoważenia obciążenia w Azure dzięki wykorzystaniu [usługi Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/). Sprawia, że Menedżer ruchu jest możliwe do załadowania saldo połączenia usługi na podstawie lokalizacji użytkownika.

Na przykład jeśli użytkownik jest żądanie do usługi z UE, połączenie jest kierowany do usługi znajduje się w centrum danych Unii Europejskiej. Ta część ruchu Menedżera globalne załadować równoważenia pozwala zwiększyć wydajność, ponieważ połączenie z najbliższym centrum danych jest większa niż nawiązywania połączenia z centrów danych, które są daleko.

Po stronie dostępności równoważenia obciążenia globalne upewnia się, czy usługa jest dostępna nawet wtedy, gdy całe centrum danych powinny stać się niedostępne.

Na przykład, jeśli Centrum danych Azure, powinny stać się niedostępne, przyczyn środowiska lub z powodu awarii (na przykład błędy sieciowe), połączenia z usługą może być przekierowany do najbliższego centrum danych w trybie online. Dzięki wykorzystaniu zasad DNS, które można utworzyć w usłudze Traffic Manager odbywa się to równoważenia obciążenia globalnego.

Firma Microsoft zaleca, użyj Menedżera ruchu dla dowolnego chmury rozwiązania wprowadzane ma zakres dystrybucji w różnych regionach i wymaga najwyższego poziomu czas pracy jest możliwe.

Aby dowiedzieć się więcej na temat usługi Azure Traffic Manager oraz sposób jego wdrażania, przeczytaj artykuł [co to jest Menedżer ruchu](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Wyłączanie protokołu RDP/SSH dostępu do maszyn wirtualnych platformy Azure
Istnieje możliwość osiągnięcia maszynach wirtualnych platformy Azure przy użyciu [protokołu Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) i [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) protokołów (SSH). Protokoły te umożliwiają zarządzanie maszynami wirtualnymi z lokalizacji zdalnych i standardowe w centrum danych obliczeniowych.

Potencjalny problem zabezpieczeń przy użyciu tych protokołów za pośrednictwem Internetu jest osoby atakujące mogą wykorzystać różnych [siłowych](https://en.wikipedia.org/wiki/Brute-force_attack) technik w celu uzyskania dostępu do maszyn wirtualnych platformy Azure. Po dostęp osoby atakujące ich użyć maszyny wirtualnej jako punkt wyjścia do naruszenia innych komputerów w sieci wirtualnej platformy Azure lub nawet ataki urządzeń sieciowych poza platformą Azure.

W związku z tym zaleca się wyłączenie bezpośredniego dostępu RDP i SSH do maszyn wirtualnych platformy Azure z Internetu. Po wyłączeniu bezpośredniego dostępu RDP i SSH z Internetu, masz inne opcje, które umożliwiają dostęp do tych maszyn wirtualnych do zdalnego zarządzania:

* Sieć VPN punkt lokacja
* Sieci VPN typu lokacja lokacja
* ExpressRoute

[Sieć VPN punkt lokacja](../vpn-gateway/vpn-gateway-point-to-site-create.md) jest inna nazwa połączenia VPN klienta/serwera dostępu zdalnego. Sieć VPN punkt lokacja umożliwia pojedynczego użytkownika nawiązać połączenie sieci wirtualnej platformy Azure za pośrednictwem Internetu. Po nawiązaniu połączenia punkt lokacja, użytkownik będzie mógł używać protokołu RDP lub SSH do nawiązania połączenia wszelkich maszyn wirtualnych znajdujących się w sieci wirtualnej Azure, który użytkownik połączył się za pośrednictwem sieci VPN typu punkt lokacja. Przy założeniu, że użytkownik jest autoryzowany do osiągnięcia tych maszyn wirtualnych.

Sieć VPN punkt lokacja jest bezpieczniejsza niż bezpośrednich połączeń protokołu RDP lub SSH, ponieważ użytkownik ma do uwierzytelniania, zanim połączenie z maszyną wirtualną. Najpierw, użytkownik musi uwierzytelnić (i autoryzowanie) do nawiązywania połączenia z siecią VPN punkt lokacja; drugie, użytkownik musi uwierzytelnić (i autoryzowanie) ustanowienie sesji protokołu RDP lub SSH.

A [sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-site-to-site-create.md) cała sieć łączy się z inną siecią za pośrednictwem Internetu. Sieć VPN lokacja lokacja można użyć, aby połączyć sieć lokalną sieć wirtualną platformy Azure. W przypadku wdrożenia sieci VPN lokacja lokacja, użytkownicy w sieci lokalnej będzie mogła nawiązać połączenia z maszynami wirtualnymi w sieci wirtualnej platformy Azure za pomocą protokołu RDP lub SSH za pośrednictwem połączenia sieci VPN typu lokacja lokacja i nie wymaga umożliwienia bezpośredniego dostępu RDP lub SSH za pośrednictwem Internetu.

Dedykowane łącza sieci WAN umożliwia również zapewniać funkcje podobne do sieci VPN typu lokacja lokacja. Główne różnice są 1. dedykowane łącza sieci WAN nie przechodzenie przez Internet i 2. dedykowane łącza sieci WAN są zazwyczaj więcej stabilności i wydajności. Platforma Azure udostępnia rozwiązanie dedykowane łącza sieci WAN w formie [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Centrum zabezpieczeń Azure Włącz
Centrum zabezpieczeń Azure ułatwia zapobieganie, wykrywania i reagowania na zagrożenia i zapewnia zwiększyć widoczność i kontrolę nad, zabezpieczeń zasobów platformy Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

Centrum zabezpieczeń Azure ułatwia optymalizacji i monitorować przez zabezpieczenia sieci:

* Udostępnia zalecenia dotyczące zabezpieczeń sieci
* Monitorowanie stanu konfiguracji zabezpieczeń sieci
* Wysyłać alerty o podstawie zagrożenia sieciowe zarówno na poziomie punktu końcowego i sieci

Zdecydowanie zaleca się włączenie Centrum zabezpieczeń Azure dla wszystkich wdrożeń platformy Azure.

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure oraz jak je włączyć wdrożeń, przeczytaj artykuł [wprowadzenie do Centrum zabezpieczeń Azure](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Bezpieczne rozszerzenie centrum danych na platformie Azure
Wiele organizacji IT organizacji chce się dowiedzieć, aby rozwinąć w chmurze zamiast rośnie ich lokalnych centrów danych. To rozwinięcie reprezentuje rozszerzeniem istniejącej infrastruktury informatycznej do chmury publicznej. Dzięki wykorzystaniu między lokalizacjami opcji łączności jest można traktować jako po prostu inną podsieć infrastrukturę sieci lokalnej sieci wirtualne Azure.

Istnieje jednak wiele problemów planowania i projektowania, które muszą być przetwarzane jako pierwsze. Jest to szczególnie ważne w zakresie zabezpieczeń sieci. Jednym z najlepszym sposobem, aby zrozumieć, jak podejście do tego projektu jest przykład.

Firma Microsoft opracowała [Diagram architektury odwołanie rozszerzenia Datacenter](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) i obsługi zabezpieczenia, aby lepiej zrozumieć, jak będzie wyglądać rozszerzenia centrum danych. Zapewnia to przykład implementację odwołania, która służy do planowania i projektowania rozszerzenie centrum danych przedsiębiorstwa bezpieczny do chmury. Firma Microsoft zaleca zapoznanie się tego dokumentu, aby poznać najważniejsze składniki bezpieczne rozwiązanie.

Aby dowiedzieć się więcej na temat bezpiecznego rozszerzona centrum danych Azure, Obejrzyj klip wideo [rozszerzanie Your Datacenter do systemu Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA).
