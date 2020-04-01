---
title: Funkcje sieci
description: Dowiedz się więcej o funkcjach sieciowych w usłudze Azure App Service i funkcjach potrzebnych do zaspokojenia potrzeb sieciowych w zakresie zabezpieczeń lub funkcjonalności.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 79f85261115dbddcb0b04cd2863a90912de2ab87
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474911"
---
# <a name="app-service-networking-features"></a>Funkcje sieciowe usługi App Service

Aplikacje w usłudze Azure App Service można wdrożyć na wiele sposobów. Domyślnie aplikacje hostowane usługi App Service są bezpośrednio dostępne w Internecie i mogą dotrzeć tylko do obsługiwanych punktów końcowych w Internecie. Wiele aplikacji klienta jednak trzeba kontrolować przychodzący i wychodzący ruch sieciowy. Istnieje kilka funkcji dostępnych w usłudze app service, aby zaspokoić te potrzeby. Wyzwaniem jest wiedza, jaką funkcję należy wykorzystać do rozwiązania danego problemu. Ten dokument ma na celu pomóc klientom określić, jaka funkcja powinna być używana na podstawie niektórych przykładowych przypadków użycia.

Istnieją dwa podstawowe typy wdrażania usługi Azure App Service. Istnieje wielodostępna usługa publiczna, która obsługuje plany usługi app service w jednostkach SKU cen bezpłatnych, udostępnionych, podstawowych, standardowych, premium i Premiumv2. Następnie jest środowisko usługi aplikacji pojedynczej dzierżawy(ASE), który obsługuje izolowane plany usługi app service sku bezpośrednio w sieci wirtualnej platformy Azure (VNet). Funkcje, których używasz będzie się różnić, jeśli jesteś w usłudze wielodostępnej lub w ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Funkcje sieciowe usługi app service dla wielu dzierżawców 

Usługa Azure App Service jest systemem rozproszonym. Role obsługujące przychodzące żądania HTTP/HTTPS są nazywane front-ends. Role, które hostują obciążenie klienta są nazywane pracownikami. Wszystkie role we wdrożeniu usługi App Service istnieją w sieci z wieloma dzierżawcami. Ponieważ istnieje wielu różnych klientów w tej samej jednostce skalowania usługi App Service, nie można połączyć sieci usługi App Service bezpośrednio z siecią. Zamiast łączyć sieci, potrzebujemy funkcji do obsługi różnych aspektów komunikacji aplikacji. Funkcje obsługujące żądania do aplikacji nie mogą być używane do rozwiązywania problemów podczas nawiązywać połączenia z aplikacji. Podobnie funkcje, które rozwiązują problemy z wywołaniami z aplikacji, nie mogą być używane do rozwiązywania problemów z aplikacją.  

| Funkcje przychodzące | Funkcje ruchu wychodzącego |
|---------------------|-------------------|
| Adres przypisany do aplikacji | Połączenia hybrydowe |
| Ograniczenia dostępu | Wymagana integracja sieci wirtualnej bramy |
| Punkty końcowe usługi | Integracja z siecią wirtualną |

O ile nie określono inaczej, wszystkie funkcje mogą być używane razem. Możesz mieszać funkcje, aby rozwiązać różne problemy.

## <a name="use-case-and-features"></a>Przypadek użycia i funkcje

W każdym przypadku użycia może istnieć kilka sposobów rozwiązania problemu.  Właściwa funkcja do użycia jest czasami spowodowana przyczynami wykraczania poza sam przypadek użycia. Następujące przychodzących przypadkach użycia sugerują, jak używać funkcji sieciowych usługi App Service do rozwiązywania problemów związanych z kontrolowaniem ruchu przechodzącego do aplikacji. 
 
| Przypadki użycia przychodzącego | Funkcja |
|---------------------|-------------------|
| Obsługa potrzeb SSL opartych na protokoi dla twojej aplikacji | adres przypisany do aplikacji |
| Nieudzielony, dedykowany adres przychodzący aplikacji | adres przypisany do aplikacji |
| Ograniczanie dostępu do aplikacji z zestawu dobrze zdefiniowanych adresów | Ograniczenia dostępu |
| Ograniczanie dostępu do aplikacji z zasobów w sieci wirtualnej | Punkty końcowe usługi </br> ILB ASE </br> Prywatny punkt końcowy (wersja zapoznawcza) |
| Uwidacznianie mojej aplikacji na prywatnym adresie IP w mojej sieci wirtualnej | ILB ASE </br> prywatny adres IP dla ruchu przychodzącego w bramie aplikacji z punktami końcowymi usługi </br> Punkt końcowy usługi (wersja zapoznawcza) |
| Ochrona mojej aplikacji za pomocą waf | Brama aplikacji + ILB ASE </br> Usługa Application Gateway z punktami końcowymi usługi </br> Drzwi frontowe platformy Azure z ograniczeniami dostępu |
| Obciążenie ruchu z równoważenia obciążenia do moich aplikacji w różnych regionach | Drzwi frontowe platformy Azure z ograniczeniami dostępu | 
| Ruch równoważenia obciążenia w tym samym regionie | [Usługa Application Gateway z punktami końcowymi usługi][appgwserviceendpoints] | 

Następujące wychodzące przypadki użycia sugerują, jak używać funkcji sieciowych usługi App Service do rozwiązywania potrzeb dostępu wychodzącego dla aplikacji. 

| Przypadki użycia wychodzącego | Funkcja |
|---------------------|-------------------|
| Uzyskiwanie dostępu do zasobów w sieci wirtualnej platformy Azure w tym samym regionie | Integracja z siecią wirtualną </br> Ase |
| Uzyskiwanie dostępu do zasobów w sieci wirtualnej platformy Azure w innym regionie | Wymagana integracja sieci wirtualnej bramy </br> Komunikacja równorzędna ASE i sieci wirtualnej |
| Dostęp do zasobów zabezpieczonych za pomocą punktów końcowych usługi | Integracja z siecią wirtualną </br> Ase |
| Uzyskiwanie dostępu do zasobów w sieci prywatnej niepołączonej z platformą Azure | Połączenia hybrydowe |
| Dostęp do zasobów w obwodach usługi ExpressRoute | Integracja z siecią wirtualną </br> Ase | 
| Zabezpieczanie ruchu wychodzącego z aplikacji sieci Web | Integracja sieci wirtualnych i sieciowe grupy zabezpieczeń </br> Ase | 
| Kierowanie ruchu wychodzącego z aplikacji sieci Web | Integracja sieci wirtualnej i tabele tras </br> Ase | 


### <a name="default-networking-behavior"></a>Domyślne zachowanie sieci

Jednostki skalowania usługi Azure App Service obsługują wielu klientów w każdym wdrożeniu. Bezpłatna i udostępniona jednostka SKU planuje hostować obciążenia klientów na pracownikach wielodostępnych. Plany podstawowe i powyższe hostują obciążenia klientów, które są dedykowane tylko jednemu planowi usługi app service (ASP). Jeśli masz standardowy plan usługi aplikacji, wszystkie aplikacje w tym planie będą uruchamiane na tym samym usłudze. Jeśli skalujesz w poziomie pracownika, wszystkie aplikacje w tym asp będą replikowane na nowy proces roboczy dla każdego wystąpienia w asp. Pracownicy, którzy są używane do Premiumv2 różnią się od pracowników używanych dla innych planów. Każde wdrożenie usługi App Service ma jeden adres IP, który jest używany dla całego ruchu przychodzącego do aplikacji w tym wdrożeniu usługi App Service. Istnieją jednak wszędzie od 4 do 11 adresów używanych do wykonywania połączeń wychodzących. Te adresy są współużytkowane przez wszystkie aplikacje w tym wdrożeniu usługi App Service. Adresy wychodzące są różne na podstawie różnych typów procesów roboczych. Oznacza to, że adresy używane przez bezpłatne, udostępnione, podstawowe, standardowe i premium asps różnią się od adresów używanych do połączeń wychodzących z asps Premiumv2. Jeśli spojrzysz we właściwości aplikacji, możesz zobaczyć adresy przychodzące i wychodzące używane przez aplikację. Jeśli chcesz zablokować zależność z listy ACL IP, należy użyć possibleOutboundAddresses. 

![Właściwości aplikacji](media/networking-features/app-properties.png)

Usługa App Service ma wiele punktów końcowych, które są używane do zarządzania usługą.  Te adresy są publikowane w oddzielnym dokumencie i są również w AppServiceManagement ip tag usługi. AppServiceManagement tag jest używany tylko ze środowiskiem usługi aplikacji (ASE), gdzie należy zezwolić na taki ruch. Adresy przychodzące usługi App Service są śledzone w tagu usługi IP usługi AppService. Nie ma tagu usługi IP, który zawiera adresy wychodzące używane przez usługę App Service. 

![Diagram przychodzący i wychodzący usługi app service](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Adres przypisany do aplikacji 

Funkcja adresu przypisana przez aplikację jest pochodną funkcji SSL opartej na protokona z protokołu IP i jest dostępna przez skonfigurowanie protokołu SSL za pomocą aplikacji. Ta funkcja może służyć do wywołań SSL opartych na protoke, ale może być również używana do nadadawania aplikacji adresu, który ma tylko. 

![Diagram adresów przypisanych aplikacji](media/networking-features/app-assigned-address.png)

Gdy używasz adresu przypisanego aplikacji, ruch nadal przechodzi przez te same role front-end, które obsługują cały ruch przychodzący do jednostki skalowania usługi App Service. Adres przypisany do aplikacji jest jednak używany tylko przez aplikację. Przypadki użycia dla tej funkcji to:

* Obsługa potrzeb SSL opartych na protokoi dla twojej aplikacji
* Ustawianie dedykowanego adresu aplikacji, który nie jest udostępniany innym osobom

Możesz dowiedzieć się, jak ustawić adres w aplikacji, za pomocą [samouczka Dodawanie certyfikatu TLS/SSL w usłudze Azure App Service][appassignedaddress]. 

### <a name="access-restrictions"></a>Ograniczenia dostępu 

Funkcja Ograniczenia dostępu umożliwia filtrowanie żądań **przychodzących** na podstawie początkowego adresu IP. Akcja filtrowania odbywa się na role front-end, które są nadrzędne od ról procesu roboczego, gdzie aplikacje są uruchomione. Ponieważ role front-end są nadrzędne od pracowników, możliwości ograniczeń dostępu można uznać za ochronę poziomu sieci dla aplikacji. Funkcja umożliwia tworzenie listy bloków adresów zezwalaj i odrzucania, które są oceniane w kolejności priorytetu. Jest podobny do funkcji network security group (NSG), która istnieje w usłudze Azure Networking.  Tej funkcji można użyć w ase lub w usłudze wielodostępnej. W przypadku użycia z ase ILB, można ograniczyć dostęp z prywatnych bloków adresów.

![Ograniczenia dostępu](media/networking-features/access-restrictions.png)

Funkcja Ograniczenia dostępu pomaga w scenariuszach, w których chcesz ograniczyć adresy IP, które mogą służyć do dotarcia do aplikacji. Wśród przypadków użycia tej funkcji są:

* Ograniczanie dostępu do aplikacji z zestawu dobrze zdefiniowanych adresów 
* Ogranicz dostęp do przechodzenia za pośrednictwem usługi równoważenia obciążenia, takiej jak usługi Azure Front Door. Jeśli chcesz zablokować ruch przychodzący do drzwi frontowych platformy Azure, utwórz reguły zezwalaj na ruch z 147.243.0.0/16 i 2a01:111:2050::/44. 

![Ograniczenia dostępu za pomocą drzwi wejściowych](media/networking-features/access-restrictions-afd.png)

Jeśli chcesz zablokować dostęp do aplikacji, dzięki czemu można uzyskać dostęp tylko z zasobów w sieci wirtualnej platformy Azure (VNet), potrzebujesz statycznego adresu publicznego na niezależnie od źródła znajduje się w sieci wirtualnej. Jeśli zasoby nie mają adresu publicznego, należy użyć funkcji Punkty końcowe usługi zamiast tego. Dowiedz się, jak włączyć tę funkcję, za pomocą [samouczka Konfigurowanie ograniczeń dostępu][iprestrictions].

### <a name="service-endpoints"></a>Punkty końcowe usługi

Punkty końcowe usługi umożliwia zablokowanie dostępu **przychodzącego** do aplikacji w taki sposób, że adres źródłowy musi pochodzić z zestawu podsieci, które można wybrać. Ta funkcja działa w połączeniu z ograniczeniami dostępu IP. Punkty końcowe usługi są ustawione w tym samym środowiska użytkownika co ograniczenia dostępu IP. Można utworzyć listę reguł dostępu zezwalaj/odrzucać, która zawiera adresy publiczne, a także podsieci w sieciach wirtualnych. Ta funkcja obsługuje scenariusze, takie jak:

![punkty końcowe usługi](media/networking-features/service-endpoints.png)

* Konfigurowanie bramy aplikacji za pomocą aplikacji w celu blokowania ruchu przychodzącego do aplikacji
* Ograniczanie dostępu do aplikacji do zasobów w sieci wirtualnej. Może to obejmować maszyny wirtualne, asy lub nawet inne aplikacje korzystające z integracji sieci wirtualnej 

![punkty końcowe usługi z bramą aplikacji](media/networking-features/service-endpoints-appgw.png)

Więcej informacji na temat konfigurowania punktów końcowych usługi za pomocą aplikacji można dowiedzieć się więcej na temat konfigurowania punktów końcowych usługi w samouczku dotyczącym [konfigurowania ograniczeń dostępu do punktu końcowego usługi][serviceendpoints]

### <a name="private-endpoint-preview"></a>Prywatny punkt końcowy (wersja zapoznawcza)

Prywatny punkt końcowy to interfejs sieciowy, który łączy Cię prywatnie i bezpiecznie z aplikacją sieci Web przez usługę Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, skutecznie wprowadzając aplikację sieci Web do sieci wirtualnej. Ta funkcja jest dostępna tylko dla przepływów **przychodzących** do aplikacji sieci Web.
[Korzystanie z prywatnych punktów końcowych dla aplikacji Azure Web App (Wersja zapoznawcza)][privateendpoints]
 
### <a name="hybrid-connections"></a>Połączenia hybrydowe

Połączenia hybrydowe usługi App Service umożliwiają aplikacjom wykonywanie wywołań **wychodzących** do określonych punktów końcowych TCP. Punkt końcowy może być lokalny, w sieci wirtualnej lub w dowolnym miejscu, który umożliwia ruch wychodzący do platformy Azure na porcie 443. Ta funkcja wymaga instalacji agenta przekazywania o nazwie Menedżer połączeń hybrydowych (HCM) na serwerze Windows Server 2012 lub nowszym hoście. Moduł HCM musi mieć możliwość osiągnięcia usługi Azure Relay w porcie 443. HCM można pobrać z interfejsu użytkownika połączeń hybrydowych usługi app service w portalu. 

![Przepływ sieci połączeń hybrydowych](media/networking-features/hybrid-connections.png)

Funkcja połączenia hybrydowe usługi App Service jest oparta na funkcji połączeń hybrydowych usługi Azure Relay. Usługa App Service używa wyspecjalizowanej formy funkcji, która obsługuje tylko wykonywanie połączeń wychodzących z aplikacji do hosta i portu TCP. Ten host i port trzeba rozpoznać tylko na hoście, na którym jest zainstalowany moduł HCM. Gdy aplikacja w usłudze App Service wykonuje wyszukiwanie DNS na hoście i porcie zdefiniowanym w połączeniu hybrydowym, ruch jest automatycznie przekierowywane, aby przejść przez menedżera połączeń hybrydowych i obecnie Menedżera połączeń hybrydowych. Aby dowiedzieć się więcej o połączeniach hybrydowych, przeczytaj dokumentację dotyczącą [połączeń hybrydowych usługi App Service][hybridconn]

Ta funkcja jest powszechnie używana do:

* Uzyskiwanie dostępu do zasobów w sieciach prywatnych, które nie są połączone z platformą Azure za pomocą sieci VPN lub usługi ExpressRoute
* Obsługa przenoszenia i przenoszenia aplikacji lokalnych do usługi App Service bez konieczności przenoszenia obsługiwanych baz danych  
* Bezpiecznie zapewnij dostęp do jednego hosta i portu na połączenie hybrydowe. Większość funkcji sieciowych otwiera dostęp do sieci, a dzięki połączeniom hybrydowym masz tylko jeden host i port, do którego możesz dotrzeć.
* Opis scenariuszy nieobjętych innymi metodami połączeń wychodzących
* Programowanie w usłudze App Service, w którym aplikacje mogą łatwo korzystać z zasobów lokalnych 

Ponieważ funkcja umożliwia dostęp do zasobów lokalnych bez otworu zapory przychodzącej, jest popularna wśród deweloperów. Inne funkcje sieciowe usługi aplikacji wychodzących są bardzo związane z siecią wirtualną platformy Azure. Połączenia hybrydowe nie mają zależności od przechodzenia przez sieć wirtualną i mogą być używane dla szerszej gamy potrzeb sieciowych. Należy pamiętać, że funkcja połączenia hybrydowe usługi App Service nie dba lub wie, co robisz na jej szczycie. Oznacza to, że można go używać do uzyskiwania dostępu do bazy danych, usługi sieci web lub dowolnego gniazda TCP na mainframe. Funkcja zasadniczo tunele pakietów TCP. 

Podczas gdy połączenia hybrydowe jest popularny dla rozwoju, jest również używany w wielu zastosowaniach produkcyjnych, jak również. Doskonale nadaje się do uzyskiwania dostępu do usługi sieci web lub bazy danych, ale nie jest odpowiedni dla sytuacji związanych z tworzeniem wielu połączeń. 

### <a name="gateway-required-vnet-integration"></a>Wymagana integracja sieci wirtualnej bramy 

Funkcja integracji sieci wirtualnej usługi app service wymagane umożliwia aplikacji do żądania **wychodzącego** w sieci wirtualnej platformy Azure. Funkcja działa po połączeniu hosta, na który aplikacja jest uruchomiona z bramą sieci wirtualnej w sieci wirtualnej za pomocą sieci VPN typu punkt-lokacja. Podczas konfigurowania funkcji aplikacja pobiera jeden z adresów typu punkt-lokacja przypisanych do każdego wystąpienia. Ta funkcja umożliwia dostęp do zasobów w sieciach wirtualnych Klasycznego lub Menedżera zasobów w dowolnym regionie. 

![Wymagana integracja sieci wirtualnej bramy](media/networking-features/gw-vnet-integration.png)

Ta funkcja rozwiązuje problem uzyskiwania dostępu do zasobów w innych sieciach wirtualnych, a nawet może służyć do łączenia się za pośrednictwem sieci wirtualnej z innymi sieciami wirtualnymi lub nawet lokalnymi. Nie działa z połączonymi sieciami wirtualnymi usługi ExpressRoute, ale ma miejsce w sieciach połączonych z sieciami sieci VPN typu lokacja. Zwykle jest niewłaściwe użycie tej funkcji z aplikacji w środowisku usługi app service (ASE), ponieważ środowisko ASE jest już w sieci wirtualnej. Przypadki użycia, które rozwiązuje ta funkcja, to:

* Uzyskiwanie dostępu do zasobów na prywatnych adresach IP w sieciach wirtualnych platformy Azure 
* Uzyskiwanie dostępu do zasobów w środowisku lokalnym, jeśli istnieje sieć VPN lokacja lokacja 
* Uzyskiwanie dostępu do zasobów w sieciach wirtualnych w sieciach wirtualnych w sieciach wirtualnych w sieciach wirtualnych 

Gdy ta funkcja jest włączona, aplikacja będzie używać serwera DNS, z którym jest skonfigurowana docelowa sieci wirtualna. Więcej informacji na temat tej funkcji można znaleźć w dokumentacji dotyczącej [integracji sieci wirtualnej usługi App Service][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integracja z siecią wirtualną

Funkcja integracji sieci wirtualnej wymaganej bramy jest bardzo przydatna, ale nadal nie rozwiązuje dostępu do zasobów w udziale usługi ExpressRoute. Oprócz konieczności dotarcia do połączeń usługi ExpressRoute, istnieje potrzeba, aby aplikacje mogły nawiązywać połączenia z usługami zabezpieczonymi punktami końcowymi usługi. Aby rozwiązać oba te dodatkowe potrzeby, dodano inną funkcję integracji sieci wirtualnej. Nowa funkcja integracji sieci wirtualnej umożliwia umieszczenie wewnętrznej bazy danych aplikacji w podsieci w sieci wirtualnej Menedżera zasobów w tym samym regionie. Ta funkcja nie jest dostępna w środowisku usługi aplikacji, która jest już w sieci wirtualnej. Ta funkcja umożliwia:

* Uzyskiwanie dostępu do zasobów w sieciach wirtualnych Menedżera zasobów w tym samym regionie
* Uzyskiwanie dostępu do zasobów zabezpieczonych za pomocą punktów końcowych usługi 
* Uzyskiwanie dostępu do zasobów dostępnych w u źródła połączeniach usługi ExpressRoute lub VPN
* Zabezpieczanie całego ruchu wychodzącego 
* Wymusz tunelowanie całego ruchu wychodzącego. 

![Integracja z siecią wirtualną](media/networking-features/vnet-integration.png)

Aby dowiedzieć się więcej o tej funkcji, przeczytaj dokumenty dotyczące [integracji sieci wirtualnej usługi App Service][vnetintegration].

## <a name="app-service-environment"></a>Środowisko usługi App Service 

Środowisko usługi aplikacji (ASE) to wdrożenie pojedynczej dzierżawy usługi Azure App Service, która jest uruchamiana w sieci wirtualnej. ASE umożliwia przypadki użycia, takie jak:

* Dostęp do zasobów w sieci wirtualnej
* Uzyskiwanie dostępu do zasobów w udziale usługi ExpressRoute
* Udostępnianie aplikacji za pomocą adresu prywatnego w sieci wirtualnej 
* Dostęp do zasobów między punktami końcowymi usługi 

W przypadku ase nie trzeba używać funkcji, takich jak integracja sieci wirtualnej lub punktów końcowych usługi, ponieważ ASE jest już w sieci wirtualnej. Jeśli chcesz uzyskać dostęp do zasobów, takich jak SQL lub Magazyn za pośrednictwem punktów końcowych usługi, włącz punkty końcowe usługi w podsieci ASE. Jeśli chcesz uzyskać dostęp do zasobów w sieci wirtualnej, nie jest wymagana żadna dodatkowa konfiguracja.  Jeśli chcesz uzyskać dostęp do zasobów w udziale usługi ExpressRoute, jesteś już w sieci wirtualnej i nie trzeba konfigurować niczego na ASE lub aplikacji wewnątrz niego. 

Ponieważ aplikacje w ASE ILB mogą być udostępniane na prywatny adres IP, można łatwo dodać urządzenia WAF, aby uwidaczniać tylko aplikacje, które chcesz do Internetu i zachować resztę bezpieczne. Nadaje się do łatwego rozwoju aplikacji wielowarstwowych. 

Istnieje kilka rzeczy, które nie są jeszcze możliwe z usługi wielu dzierżawców, które są z ASE. Należą do nich takie rzeczy jak:

* Udostępnianie aplikacji na prywatny adres IP
* Zabezpiecz cały ruch wychodzący za pomocą kontrolek sieciowych, które nie są częścią aplikacji 
* Hostowanie aplikacji w jednej usłudze dzierżawy 
* Skalowanie do wielu innych wystąpień niż jest to możliwe w usłudze wielodostępowej 
* Ładowanie prywatnych certyfikatów klienta urzędu certyfikacji do użytku przez aplikacje z prywatnymi punktami końcowymi zabezpieczonymi urzędem certyfikacji 
* Wymuszaj TLS 1.1 we wszystkich aplikacjach hostowanych w systemie bez możliwości wyłączania na poziomie aplikacji 
* Podaj dedykowany adres wychodzący dla wszystkich aplikacji w ase, który nie jest udostępniany żadnym klientom 

![ASE w sieci wirtualnej](media/networking-features/app-service-environment.png)

ASE zapewnia najlepszą historię wokół izolowanego i dedykowanego hostingu aplikacji, ale wiąże się z pewnymi wyzwaniami związanymi z zarządzaniem. Oto kilka rzeczy, które należy wziąć pod uwagę przed użyciem operacyjnego ase:
 
 * Ase działa wewnątrz sieci wirtualnej, ale ma zależności poza sieci wirtualnej. Te zależności muszą być dozwolone. Dowiedz się więcej [w zagadnieniach dotyczących sieci dla środowiska usługi aplikacji][networkinfo]
 * Ase nie skaluje się natychmiast jak usługi wielu dzierżawców. Należy przewidzieć potrzeby skalowania, a nie reaktywnie skalowania. 
 * ASE ma wyższy koszt z góry związane z nim. Aby jak najlepiej wykorzystać swój ASE, należy zaplanować umieszczenie wielu obciążeń w jednym ASE, a nie wykorzystać go do małych wysiłków
 * Aplikacje w ase nie może ograniczyć dostępu do niektórych aplikacji w ASE, a nie innych.
 * Ase znajduje się w podsieci i wszelkie reguły sieciowe mają zastosowanie do całego ruchu do i z tego ASE. Jeśli chcesz przypisać reguły ruchu przychodzącego tylko dla jednej aplikacji, użyj ograniczeń dostępu. 

## <a name="combining-features"></a>Łączenie funkcji 

Funkcje znane dla usługi wielodostępnej mogą być używane razem w celu rozwiązania bardziej skomplikowanych przypadków użycia. Dwa z bardziej typowych przypadków użycia są opisane tutaj, ale są to tylko przykłady. Dzięki zrozumieniu, co robią różne funkcje, można rozwiązać prawie wszystkie potrzeby architektury systemu.

### <a name="inject-app-into-a-vnet"></a>Wstrzyknąć aplikację do sieci wirtualnej

Typowe żądanie dotyczy sposobu umieszczenia aplikacji w sieci wirtualnej. Umieszczenie aplikacji w sieci wirtualnej oznacza, że przychodzące i wychodzące punkty końcowe dla aplikacji znajdują się w sieci wirtualnej. ASE zapewnia najlepsze rozwiązanie, aby rozwiązać ten problem, ale można uzyskać większość tego, co jest potrzebne w usłudze wielodostępnej, łącząc funkcje. Na przykład można obsługiwać aplikacje tylko w intranecie z prywatnymi adresami przychodzącymi i wychodzącymi przez:

* Tworzenie bramy aplikacji z prywatnym adresem przychodzącym i wychodzącym
* Zabezpieczanie ruchu przychodzącego do aplikacji za pomocą punktów końcowych usługi 
* Użyj nowej integracji sieci wirtualnej, aby zaplecze aplikacji było w sieci wirtualnej 

Ten styl wdrażania nie daje dedykowany adres dla ruchu wychodzącego do Internetu lub daje możliwość blokowania całego ruchu wychodzącego z aplikacji.  Ten styl wdrażania daje wiele z tego, co tylko w przeciwnym razie uzyskać z ASE. 

### <a name="create-multi-tier-applications"></a>Tworzenie aplikacji wielowarstwowych

Aplikacja wielowarstwowa to aplikacja, do której można uzyskać dostęp do aplikacji zaplecza interfejsu API tylko z warstwy frontonalnej. Aby utworzyć aplikację wielowarstwową, można:

* Łączenie wewnętrznej bazy aplikacji sieci web za pomocą sieci wirtualnej za pomocą sieci wirtualnej w sieci wirtualnej
* Zabezpieczanie ruchu przychodzącego do aplikacji interfejsu API za pomocą punktów końcowych usługi do zabezpieczania ruchu przychodzącego do aplikacji interfejsu API tylko z podsieci używanej przez aplikację sieci Web frontu

![aplikacja wielowarstwowa](media/networking-features/multi-tier-app.png)

Możesz mieć wiele aplikacji front-end używać tej samej aplikacji interfejsu API przy użyciu integracji sieci wirtualnej z innych aplikacji front-end-end i punktów końcowych usługi z aplikacji interfejsu API z ich podsieci.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
