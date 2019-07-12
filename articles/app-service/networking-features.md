---
title: Sieć możliwości wdrożenia — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Jak używać różnych App Service, funkcji sieciowych
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: db29d0761084e32d601dc9c6d94082cd09bc5d18
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655465"
---
# <a name="app-service-networking-features"></a>Funkcje sieci usługi App Service

Aplikacje w usłudze Azure App Service można wdrożyć na wiele sposobów. Domyślnie usługi App Service w hostowanej aplikacji jest bezpośrednio dostępny przez internet i może dotyczyć wyłącznie dotrzeć do punktów końcowych hostowanej internet. Jednak wiele aplikacji klienta konieczne kontrolować ruch przychodzący i wychodzący w sieci. Kilka funkcji są dostępne w usłudze App Service do spełnienia tych wymagań. Żądania jest wiedza, jakich funkcji powinny być używane do rozwiązania danego problemu. Ten dokument ma na celu ułatwić klientom określić jakich funkcji powinien być używany w oparciu o przykładowe przypadki użycia.

Istnieją dwa typy podstawowego wdrożenia dla usługi Azure App Service. Brak usługi publicznego wielodostępną, która hostuje planów usługi App Service w bezpłatna, współdzielona, Basic, Standard, Premium i Premiumv2 wycena jednostek SKU. To ma jednej dzierżawy App Service Environment (ASE), który hostuje izolowanej usługi App Service dla jednostki SKU plany bezpośrednio w usłudze Azure Virtual Network (VNet). Funkcje, których używasz na różnią się w usłudze wielodostępnych lub w środowisku ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Funkcje sieci wielodostępnej usługi App Service 

Azure App Service to Rozproszony system. Role, które obsługują przychodzące żądania HTTP/HTTPS są nazywane frontonach. Role, obsługujące obciążenia klientów są nazywane pracowników. Wszystkie role we wdrożeniu usługi App Service istnieje w sieci wielodostępnej. Ponieważ istnieje wiele różnych klientów w tej samej jednostce skalowania usługi App Service, nie można połączyć sieć usługi App Service, bezpośrednio do sieci. Zamiast łączenia sieci, potrzebujemy funkcji do obsługi różnych aspektów komunikację z aplikacjami. Funkcje, które obsługuje żądania do aplikacji nie używać do rozwiązywania problemów podczas nawiązywania połączeń z Twojej aplikacji. Podobnie funkcje, które rozwiązywania problemów dla wywołań między aplikacją a nie używać do rozwiązywania problemów z aplikacją.  

| Funkcje dla ruchu przychodzącego | Funkcje ruchu wychodzącego |
|---------------------|-------------------|
| Aplikacja przypisany adres | Połączenia hybrydowe |
| Ograniczenia dostępu | Brama wymagane Integracja sieci wirtualnej |
| Punkty końcowe usługi | Integracja z siecią wirtualną (wersja zapoznawcza) |

Jeżeli nie określono inaczej, wszystkie funkcje można ze sobą. Możesz mieszać funkcje do rozwiązywania różnych problemów.

## <a name="use-case-and-features"></a>Przypadek użycia i funkcje

Dla dowolnego przypadku użycia danego może być kilka sposobów, aby rozwiązać problem.  Funkcję w prawo, aby używać czasami jest przyczyn poza po prostu przypadek użycia, sam. Następujące przypadki użycia dla ruchu przychodzącego sugestie dotyczące używania funkcji obsługi sieci w usłudze App Service do rozwiązywania problemów w całym kontrolowanie ruchu kierowanego do swojej aplikacji. 
 
| Przypadki użycia dla ruchu przychodzącego | Cecha |
|---------------------|-------------------|
| Potrzeb SSL oparte na adresie IP dla swojej aplikacji | Aplikacja przypisany adres |
| Nieudostępnione, dedykowany adres dla ruchu przychodzącego dla aplikacji | Aplikacja przypisany adres |
| Ograniczanie dostępu do aplikacji z zestawu dobrze zdefiniowanych adresów | Ograniczenia dostępu |
| Udostępnić aplikację na prywatne adresy IP w mojej sieci wirtualnej | ŚRODOWISKO ASE Z WEWNĘTRZNYM MODUŁEM RÓWNOWAŻENIA OBCIĄŻENIA </br> Application Gateway przy użyciu punktów końcowych usługi |
| Ograniczanie dostępu do mojej aplikacji z zasobów w sieci wirtualnej | Punkty końcowe usługi </br> ŚRODOWISKO ASE Z WEWNĘTRZNYM MODUŁEM RÓWNOWAŻENIA OBCIĄŻENIA |
| Udostępnianie aplikacji w prywatnym adresem IP w mojej sieci wirtualnej | ŚRODOWISKO ASE Z WEWNĘTRZNYM MODUŁEM RÓWNOWAŻENIA OBCIĄŻENIA </br> prywatny adres IP dla ruchu przychodzącego na bramę aplikacji z punktami końcowymi usługi |
| Ochrona aplikacji za pomocą zapory aplikacji sieci Web | Usługa Application Gateway i środowisko ASE z wewnętrznym modułem równoważenia obciążenia </br> Application Gateway przy użyciu punktów końcowych usługi </br> Usługa Azure drzwiami frontowymi z ograniczeniami dostępu |
| Równoważenie obciążenia ruchem do moich aplikacji w różnych regionach | Usługa Azure drzwiami frontowymi z ograniczeniami dostępu | 
| Równoważenie obciążenia ruchu w tym samym regionie | Application Gateway przy użyciu punktów końcowych usługi | 

Następujące przypadki użycia wychodzących Sugeruj jak rozwiązać wychodzącego dostępu do informacji dotyczących aplikacji za pomocą funkcji obsługi sieci w usłudze App Service. 

| Zastosowań ruchu wychodzącego | Cecha |
|---------------------|-------------------|
| Uzyskaj dostęp do zasobów w sieci wirtualnej platformy Azure, w tym samym regionie | Integracja z siecią wirtualną </br> ASE |
| Uzyskaj dostęp do zasobów w sieci wirtualnej platformy Azure w innym regionie | Brama wymagane Integracja sieci wirtualnej </br> Środowiska ASE i komunikacja równorzędna sieci wirtualnych |
| Uzyskaj dostęp do zasobów zabezpieczonych za pomocą punktów końcowych usługi | Integracja z siecią wirtualną </br> ASE |
| Uzyskaj dostęp do zasobów w sieci prywatnej, nie jest połączony z platformą Azure | Połączenia hybrydowe |
| Dostęp do zasobów w wielu obwodów usługi ExpressRoute | Integracja z siecią wirtualną (obecnie ograniczeń adresów RFC 1918) </br> ASE | 


### <a name="default-networking-behavior"></a>Domyślne zachowanie sieci

Jednostki skalowania usługi Azure App Service obsługuje wielu klientów w każdym wdrożeniu. Plany bezpłatne i udostępniane jednostki SKU obsługi obciążeń klientów dla wielu dzierżawców procesów roboczych. Basic lub nowszym obciążeń klientów na hoście plany, które są przeznaczone do tylko jeden plan usługi App Service (ASP). Jeśli masz plan usługi aplikacji — warstwa standardowa, wszystkie aplikacje, w tym planie zostanie uruchomione w ramach tego samego procesu roboczego. Jeśli proces roboczy jest skalowanie, następnie wszystkie aplikacje w ASP będą replikowane w ramach nowego procesu roboczego dla każdego wystąpienia w strona ASP. Procesy robocze, które są używane do Premiumv2 różnią się od pracowników, używane do innych planów. Każde wdrożenie usługi App Service ma jeden adres IP, który jest używany w przypadku ruchu przychodzącego do aplikacji, w tym wdrożeniu usługi App Service. Istnieją jednak w dowolnym miejscu od 4 do 11 adresy, używane do nawiązywania połączeń wychodzących. Te adresy są współużytkowane przez wszystkie aplikacje, w tym wdrożeniu usługi App Service. Wychodzące adresy różnią się na podstawie typów innego procesu roboczego. Oznacza to, że adresy, używane przez bezpłatna, współdzielona, podstawowa, standardowa i Premium ASP są inne niż adresy, używane do połączenia wychodzące z ASP Premiumv2. Jeśli możesz wyszukiwać we właściwościach aplikacji, zostanie wyświetlony adresy ruchu przychodzącego i wychodzącego, które są używane przez aplikację. Jeśli konieczne jest zablokowanie zależności z listy ACL adresów IP, należy użyć possibleOutboundAddresses. 

![Właściwości aplikacji](media/networking-features/app-properties.png)

Usługa App Service ma wiele punktów końcowych, które są używane do zarządzania usługą.  Te adresy są publikowane w dokumencie oddzielne i znajdują się również w tagu usługi AppServiceManagement adresów IP. AppServiceManagement tag jest używana tylko z App Service Environment (ASE) gdzie należy zezwolić na taki ruch. App Service, które adresy ruchu przychodzącego są śledzone w tagu usługi App Service IP. Tag usługi IP zawierający adresy ruchu wychodzącego używane przez usługę App Service nie istnieje. 

![Diagram usługi App Service dla ruchu przychodzącego i wychodzącego](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Aplikacja przypisany adres 

Funkcja przypisany adres aplikacji jest offshoot możliwości opartych na protokole SSL i jest dostępny, konfigurując SSL z aplikacją. Ta funkcja może być używany dla wywołań SSL opartego na protokole IP, ale może również służyć do nadaj adres który ma on tylko aplikacji. 

![Przypisany adres diagram aplikacji](media/networking-features/app-assigned-address.png)

Korzystając z aplikacji przypisany adres, ruch sieciowy nadal przechodzi przez te same role frontonu, które obsługuje cały ruch przychodzący do jednostki skalowania usługi App Service. Adres, który jest przypisany do aplikacji, jednak jest używana tylko przez aplikację. Przypadki użycia dla tej funkcji są:

* Potrzeb SSL oparte na adresie IP dla swojej aplikacji
* Ustawianie adresu dedykowanej dla twojej aplikacji, który nie jest udostępniany się inaczej

Możesz dowiedzieć się, jak ustawić adres w Twojej aplikacji za pomocą tego samouczka w [Konfigurowanie adresów IP SSL opartego na][appassignedaddress]. 

### <a name="access-restrictions"></a>Ograniczenia dostępu 

Umożliwia możliwość ograniczenia dostępu filtrowanie **dla ruchu przychodzącego** żądania oparte na adresie IP pochodzenia. Akcji filtrowania odbywa się na rolach frontonu, które są nadrzędne z ustala procesu roboczego gdy Twoje aplikacje działają. Ponieważ frontonu role są nadrzędne z pracowników, możliwość ograniczenia dostępu mogą być uważane ochrony na poziomie sieci dla aplikacji. Ta funkcja umożliwia tworzenie listy zezwalania i odmowy bloki adresów, które są obliczane w kolejności priorytetów. Jest on podobny do funkcji grupy zabezpieczeń sieci (NSG), który znajduje się w sieci platformy Azure.  W środowisku ASE lub usługą wielodostępną, można użyć tej funkcji. W przypadku użycia za pomocą środowisko ASE z wewnętrznym modułem równoważenia obciążenia, możesz ograniczyć dostęp z prywatnym adresem bloków.

![Ograniczenia dostępu](media/networking-features/access-restrictions.png)

Funkcja ograniczenia dostępu ułatwia scenariuszy, w której chcesz ograniczyć adresy IP, które może służyć do osiągnięcia swojej aplikacji. Używanie przypadków dla tej funkcji należą:

* Ograniczanie dostępu do aplikacji z zestawu dobrze zdefiniowanych adresów 
* Ograniczanie dostępu do mieszczących się za pośrednictwem usługi równoważenia obciążenia, takich jak Azure drzwi wejściowe. Jeśli chce się blokowania dla ruchu przychodzącego ruchu sieciowego do platformy Azure drzwiami frontowymi, utwórz reguły zezwalające na ruch z 147.243.0.0/16 i 2a01:111:2050:: / 44. 

![Ograniczenia dostępu przy wejściu](media/networking-features/access-restrictions-afd.png)

Jeśli chcesz zablokować dostęp do Twojej aplikacji tak, aby tylko osiągalna z zasobów w usłudze Azure Virtual Network (VNet), należy statyczny publiczny adres niezależnie od źródła jest w sieci wirtualnej. Jeśli zasoby nie mają adresów publicznych, należy użyć funkcji punktów końcowych usługi. Dowiedz się, jak włączyć tę funkcję za pomocą tego samouczka [Konfigurowanie ograniczeń dostępu][iprestrictions].

### <a name="service-endpoints"></a>Punkty końcowe usługi

Punkty końcowe usługi umożliwia zablokowanie **dla ruchu przychodzącego** dostęp do aplikacji w taki sposób, że adres źródłowy musi pochodzić z zestaw podsieci, które można wybierać. Ta funkcja działa w połączeniu z ograniczenia dostępu do adresów IP. Punkty końcowe usługi są ustawiane w tym samym środowisku użytkownika jako ograniczenia dostępu do adresów IP. Można tworzyć dozwolonych/niezezwalania listy reguł dostępu zawiera adresy publiczne, a także podsieci w Twoich sieciach wirtualnych. Ta funkcja obsługuje scenariusze, takie jak:

![punkty końcowe usługi](media/networking-features/service-endpoints.png)

* Konfigurowanie bramy aplikacji za pomocą aplikacji do blokowania ruchu przychodzącego do aplikacji
* Ograniczanie dostępu do aplikacji do zasobów w sieci wirtualnej. Może to obejmować maszyny wirtualne, za pomocą środowisk ASE lub nawet inne aplikacje używające Integracja sieci wirtualnej 

![punkty końcowe usługi z usługą application gateway](media/networking-features/service-endpoints-appgw.png)

Dowiedz się więcej na temat konfigurowania punktów końcowych usługi za pomocą aplikacji w tym samouczku na [Konfigurowanie ograniczenia dostępu do punktu końcowego usługi][serviceendpoints]
 
### <a name="hybrid-connections"></a>Połączenia hybrydowe

Połączenia hybrydowe usługi App Service umożliwia aplikacjom upewnij **ruchu wychodzącego** wywołania określonych punktach końcowych protokołu TCP. Punkt końcowy może być lokalnie w sieci wirtualnej lub dowolnego miejsca, zezwala na ruch wychodzący na platformie Azure na porcie 443. Ta funkcja wymaga zainstalowania agenta przekazywania, o nazwie Menedżer połączeń hybrydowych (HCM) w systemie Windows Server 2012 lub nowszej hosta. HCM, musi mieć dostęp do usługi Azure Relay w porcie 443. HCM, można go pobrać z usługi hybrydowe połączeń Interfejsie użytkownika aplikacji w portalu. 

![Przepływ sieciowy połączeń hybrydowych](media/networking-features/hybrid-connections.png)

Funkcja połączeń hybrydowych usługi App Service jest oparta na możliwości połączeń hybrydowych usługi Azure Relay. Usługa App Service używa formą specjalistyczne funkcja, która obsługuje tylko wprowadzania połączenia wychodzące z aplikacją hosta TCP i port. Tego hosta i portu wystarczy rozwiązać na hoście, na którym zainstalowano HCM. W przypadku aplikacji w usłudze App Service ma wyszukiwania DNS na hoście i porcie zdefiniowane w połączenie hybrydowe, ruch jest automatycznie przekierowywane do go za pośrednictwem połączenia hybrydowego i poziomie Menedżera połączeń hybrydowych. Aby dowiedzieć się więcej na temat połączeń hybrydowych, zapoznaj się dokumentacją dotyczącą [połączeń hybrydowych usługi App Service][hybridconn]

Ta funkcja jest często używana do:

* Uzyskaj dostęp do zasobów w sieciach prywatnych, które nie są podłączone do platformy Azure z sieci VPN lub usługi ExpressRoute
* Obsługuje lift- and -shift lokalnych aplikacji w usłudze App Service bez konieczności będzie również przeniesienie pomocniczych baz danych  
* Bezpiecznie zapewniają dostęp do jednego hosta i portu dla połączenia hybrydowego. Większość funkcji sieci Otwórz dostęp do sieci, a dzięki połączeniom hybrydowym masz tylko jednego hosta i port, którego możesz uzyskiwać dostęp.
* Przeznaczone dla scenariuszy, które nie pasuje do żadnego innymi metodami łączności wychodzącej
* Wykonanie w usłudze App Service, gdzie można łatwo używać w aplikacjach lokalnych zasobów 

Ponieważ ta funkcja umożliwia dostęp do zasobów lokalnych bez przerw zapory dla ruchu przychodzącego, to popularne z deweloperami. Inne wychodzące usługi App Service sieci funkcje są bardzo wirtualnych sieci na platformie Azure związane z. Połączenia hybrydowe nie ma zależności przesyłane za pośrednictwem sieci wirtualnej i może służyć do szerszego zakresu wymagań sieci. Należy zauważyć, że funkcji połączeń hybrydowych usługi App Service care lub nie wiedzieć, co robisz na jego podstawie. To znaczy, że służy do dostępu do bazy danych, usługi sieci web lub dowolnego gniazda TCP na mainframe. Funkcja tuneli zasadniczo pakiety TCP. 

Podczas połączenia hybrydowe to popularne w rozwoju, również jest używana w wielu aplikacjach produkcyjnych także. To idealne narzędzie do uzyskiwania dostępu do usługi sieci web lub bazy danych, ale nie jest odpowiednie w sytuacjach, obejmujących tworzenie wielu połączeń. 

### <a name="gateway-required-vnet-integration"></a>Brama wymagane Integracja sieci wirtualnej 

Brama wymaganych funkcji integracji sieci wirtualnej z usługą App umożliwia aplikacji upewnij **wychodzącego** żądań do usługi Azure Virtual Network. Ta funkcja działa, nawiązując połączenie hostów, w których Twoja aplikacja działa do bramy sieci wirtualnej w sieci wirtualnej za pomocą połączenia VPN punkt lokacja. Po skonfigurowaniu tej funkcji, aplikacja pobiera jeden z adresów point-to-site, przypisany do każdego wystąpienia. Ta funkcja umożliwia dostęp do zasobów w klasycznym lub sieci wirtualnych Menedżera zasobów w dowolnym regionie. 

![Brama wymagane Integracja sieci wirtualnej](media/networking-features/gw-vnet-integration.png)

Ta funkcja rozwiązuje problem podczas uzyskiwania dostępu do zasobów w innych sieci wirtualnych i nawet może służyć do łączenia za pośrednictwem sieci wirtualnej do innych sieci wirtualnych lub nawet lokalnie. Nie działa z usługą ExpressRoute połączone sieci połączone sieci wirtualne, ale ma z sieci VPN typu lokacja lokacja. Nie należy zwykle użyć tej funkcji, z aplikacji w App Service Environment (ASE), ponieważ środowisko ASE jest już w sieci wirtualnej. Przypadki użycia, które rozwiązuje tej funkcji są następujące:

* Uzyskiwanie dostępu do zasobów na prywatne adresy IP w swoich sieciach wirtualnych platformy Azure 
* Uzyskiwanie dostępu do zasobów w środowisku lokalnym czy sieć VPN lokacja lokacja 
* Uzyskiwanie dostępu do zasobów w wirtualnych sieciach równorzędnych 

Gdy ta funkcja jest włączona, Twoja aplikacja będzie używać serwera DNS, który skonfigurowano docelowej sieci wirtualnej. Możesz dowiedzieć się więcej na temat tej funkcji w dokumentacji dotyczącej [integracji sieci wirtualnej usługi aplikacji][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integracja z siecią wirtualną

Brama wymaganych funkcji integracji sieci wirtualnej jest bardzo przydatna, ale nadal nie rozwiązuje dostęp do zasobów dla usługi ExpressRoute. Na podstawie wymagające dotrzeć do połączeń usługi ExpressRoute, konieczne jest dla aplikacji móc wykonywać wywołania usług zabezpieczeń punktu końcowego usługi. Aby rozwiązać, zarówno tych dodatkowych wymagań, innej funkcji integracji sieci wirtualnej została dodana. Nowej funkcji integracji sieci wirtualnej można umieścić zaplecze Twojej aplikacji w podsieci w sieci wirtualnej usługi Resource Manager w tym samym regionie. Ta funkcja nie jest dostępny w środowisku usługi App Service, który już znajduje się w sieci wirtualnej. Ta funkcja umożliwia:

* Uzyskiwanie dostępu do zasobów w sieci wirtualnych Menedżera zasobów, w tym samym regionie
* Uzyskiwanie dostępu do zasobów zabezpieczonych za pomocą punktów końcowych usługi 
* Uzyskiwanie dostępu do zasobów, które są dostępne w ramach połączeń usługi ExpressRoute lub sieci VPN

![Integracja z siecią wirtualną](media/networking-features/vnet-integration.png)

Ta funkcja jest dostępna w wersji zapoznawczej i nie powinna być używana w przypadku obciążeń produkcyjnych. Aby dowiedzieć się więcej na temat tej funkcji, zapoznaj się z dokumentacją na [integracji sieci wirtualnej usługi aplikacji][vnetintegration].

## <a name="app-service-environment"></a>Środowisko usługi App Service 

App Service Environment (ASE) to wdrożenie pojedynczej dzierżawy usługi Azure App Service działa w sieci wirtualnej. Środowisko ASE umożliwia przypadki użycia, takie jak:

* Uzyskaj dostęp do zasobów w sieci wirtualnej
* Uzyskaj dostęp do zasobów dla usługi ExpressRoute
* Udostępnianie aplikacji za pomocą prywatnego adresu w sieci wirtualnej 
* Uzyskaj dostęp do zasobów w obrębie punktów końcowych usługi 

Za pomocą środowisko ASE nie trzeba używać funkcji, takich jak integracja z siecią wirtualną lub punktów końcowych usługi, ponieważ środowisko ASE znajduje się już w sieci wirtualnej. Jeśli chcesz uzyskać dostęp do zasobów takich jak SQL lub magazynu za pośrednictwem punktów końcowych usługi, należy włączyć punkty końcowe usługi w podsieci środowiska ASE. Jeśli chcesz uzyskać dostęp do zasobów w sieci wirtualnej, nie ma konieczności dodatkowej konfiguracji.  Jeśli chcesz uzyskać dostęp do zasobów dla usługi ExpressRoute znajdują się już w sieci wirtualnej i nie trzeba konfigurować niczego w środowisku ASE lub aplikacji wewnątrz niego. 

Ponieważ aplikacje w środowisku ASE z wewnętrznym modułem równoważenia obciążenia może być odsłonięta na prywatny adres IP, możesz łatwo dodać urządzenia zapory aplikacji sieci Web do udostępnienia tylko aplikacji, aby przez internet i zabezpieczyć pozostałe. Jej pozwala na łatwe projektowanie aplikacji wielowarstwowych. 

Istnieje kilka kwestii, które nie są jeszcze możliwe z wieloma dzierżawami usługi, które pochodzą z ASE. Elementów, takich jak te obejmują:

* Udostępnianie aplikacji na prywatny adres IP
* Bezpieczny ruch wychodzący z kontrolkami sieci, które nie są częścią Twojej aplikacji 
* Hostowanie aplikacji w usłudze pojedynczej dzierżawy 
* Skalowanie w górę do wiele więcej wystąpień niż jest możliwe w usługę wielodostępu 
* Ładowanie certyfikatów klienta prywatnych urzędu certyfikacji do użytku przez aplikacje za pomocą prywatnej urzędu certyfikacji, zabezpieczone punkty końcowe 
* Wymuszanie protokołu TLS 1.1 na wszystkie aplikacje hostowane w systemie, bez żadnych możliwość wyłączenia na poziomie aplikacji 
* Zapewniają dedykowany adres ruchu wychodzącego dla wszystkich aplikacji w środowisku ASE, która nie jest udostępniony za pomocą klientów 

![Środowisko ASE w sieci wirtualnej](media/networking-features/app-service-environment.png)

Środowisko ASE zapewnia najlepsze wątku wokół hosting aplikacji izolowane, dedykowane, ale dołączone niektóre wyzwania, zarządzania. Niektóre kwestie, należy wziąć pod uwagę przed rozpoczęciem korzystania z ASE operacyjne są następujące:
 
 * Środowisko ASE działają w obrębie sieci wirtualnej, ale mają zależności poza siecią wirtualną. Te zależności, muszą być dozwolone. Dowiedz się więcej w [informacje dotyczące sieci środowiska App Service Environment][networkinfo]
 * Środowisko ASE nie działa natychmiast takich jak usługa wielu dzierżawców. Należy przewidzieć skalowanie potrzeb, a nie sposób reaktywny skalowania. 
 * Środowisko ASE ma wyższe na początku kosztów skojarzonych z nim. Aby uzyskać w pełni wykorzystać środowiska ASE, można należy zaplanować na umieszczenie wielu obciążeń w jednym środowisku ASE zamiast jest używany dla małych wysiłków
 * Aplikacje w środowisku ASE nie można ograniczyć dostęp do niektórych aplikacji w środowisku ASE i innych nie.
 * Środowisko ASE znajduje się w podsieci i wszelkie reguły sieci mają zastosowanie do całego ruchu do i z tym środowisku ASE. Jeśli chcesz przypisać reguły ruchu przychodzącego dla tylko jednej aplikacji, użyj ograniczenia dostępu. 

## <a name="combining-features"></a>Łączenie funkcji 

Funkcje podane dla wielu dzierżaw usługi można ze sobą rozwiązania bardziej rozbudowany przypadki użycia. Dwie typowe przypadki użycia są opisane poniżej, ale są tylko przykładowe. Zrozumienie, co zrobić, różne funkcje, można rozwiązać, prawie wszystkie wymagania w zakresie architektury systemu.

### <a name="inject-app-into-a-vnet"></a>Dodanie aplikacji do sieci wirtualnej

Typowe żądanie znajduje się na sposób Udostępnij swoją aplikację w sieci wirtualnej. Umieszczanie aplikacji w sieci wirtualnej oznacza, że dla ruchu przychodzącego i wychodzącego punktów końcowych dla aplikacji w sieci wirtualnej. Środowisko ASE zawiera najlepsze rozwiązania, aby rozwiązać ten problem, ale możesz uzyskać większość potrzebne przy użyciu usługi wielodostępne, łącząc funkcji. Na przykład może obsługiwać tylko aplikacje intranetowe z prywatnymi adresami ruchu przychodzącego i wychodzącego przez:

* Tworzenie bramy aplikacji za pomocą prywatnego adresu ruchu przychodzącego i wychodzącego
* Zabezpieczanie ruchu przychodzącego do aplikacji za pomocą punktów końcowych usługi 
* Użyj nowej integracji sieci wirtualnej, zaplecza aplikacji znajduje się w sieci wirtualnej 

Ten styl wdrożenia będzie zapewniają dedykowany adres dla ruchu wychodzącego z Internetem lub nie zapewniają możliwość blokowania całego ruchu wychodzącego ze swojej aplikacji.  Ten styl wdrożenia pozwoli uzyskać bardzo o tym, co spowoduje tylko w przeciwnym razie oferuje środowisko ASE. 

### <a name="create-multi-tier-applications"></a>Tworzenie aplikacji wielowarstwowych

Wielowarstwowej aplikacji to aplikacja, której aplikacje zaplecza interfejsu API może zostać oceniony jedynie z warstwy frontonu. Aby utworzyć aplikację wielowarstwową, możesz wykonywać następujące czynności:

* Łączenie z zapleczem aplikacji frontonu sieci web za pomocą podsieci w sieci wirtualnej przy użyciu Integracja z siecią wirtualną
* Punkty końcowe usługi umożliwiają zabezpieczanie ruchu przychodzącego do aplikacji interfejsu API do tylko pochodzące z podsieci używane przez Twoją aplikację frontonu sieci web

![aplikacji wielowarstwowej](media/networking-features/multi-tier-app.png)

Może mieć wiele aplikacji frontonu, użyj tej samej aplikacji interfejsu API przy użyciu Integracja z siecią wirtualną z innych aplikacji frontonu i punkty końcowe usługi z aplikacji interfejsu API z ich podsieciami.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
