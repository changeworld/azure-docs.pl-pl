---
title: Kontrolowanie ruchu wychodzącego w Australii platformy Azure
description: Najważniejsze elementy sterujące ruchem wychodzącym na platformie Azure w celu spełnienia wymogów australijskiego rządu dla bezpiecznych bram internetowych
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: grgale
ms.openlocfilehash: 7922846d161b7a0cbda32101b6bbb40a1741b323
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602088"
---
# <a name="controlling-egress-traffic-in-azure-australia"></a>Kontrolowanie ruchu wychodzącego w Australii platformy Azure

Podstawowym składnikiem zabezpieczania systemów ICT jest kontrolowanie ruchu w sieci. Ograniczanie komunikacji tylko do ruchu niezbędnego do funkcjonowania systemu zmniejsza ryzyko złamania. Widoczność i kontrola nad systemami zewnętrznymi, z którymi komunikują się aplikacje i usługi, pomaga wykrywać naruszone systemy i próbować lub pomyślnie eksfiltracji dane. Ten artykuł zawiera informacje na temat sposobu działania ruchu sieciowego ruchu wychodzącego (wyjściowego) na platformie Azure i zawiera zalecenia dotyczące implementowania kontroli zabezpieczeń sieci dla systemu połączonego z Internetem, który jest wyrównany do Cybernetycznymiego Centrum zabezpieczeń (ACSC) Wskazówki dla konsumentów i zamiaru podręcznika ACSC informacji o zabezpieczeniach (ISM).

## <a name="requirements"></a>Wymagania

Ogólne wymagania dotyczące zabezpieczeń systemów Wspólnoty są zdefiniowane w ISM. Aby pomóc podmiotom Wspólnoty w wdrażaniu zabezpieczeń sieci, ACSC opublikowała _ochronę ACSC: Implementacja segmentacji i_rozdzielania sieci oraz ułatwianie zabezpieczania systemów w środowiskach w chmurze ACSC opublikowała _zabezpieczenia chmury obliczeniowej dla dzierżawców_.

Dokumenty ACSC stanowią zarys kontekstu implementowania zabezpieczeń sieci i kontrolowania ruchu oraz zapewniają praktyczne zalecenia dotyczące projektowania i konfigurowania sieci.

Następujące kluczowe wymagania dotyczące kontrolowania ruchu wychodzącego na platformie Azure zostały zidentyfikowane w dokumentach ACSC.

Opis|Source
--------------- |------------------
**Implementowanie segmentacji i**rozdzielania sieci, na przykład używanie architektury n-warstwowej przy użyciu zapór opartych na hoście i kontroli dostępu do sieci w celu ograniczenia łączności sieciowej przychodzącej i wychodzącej tylko do wymaganych portów i protokołów.| [Przetwarzanie w chmurze dla dzierżawców](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
**Implementowanie odpowiednio dużej przepustowości, małych opóźnień i niezawodnej łączności sieciowej** między dzierżawcą (łącznie z użytkownikami zdalnymi dzierżawy) i usługą w chmurze w celu spełnienia wymagań dotyczących dostępności dzierżawy  | [ACSC ochronę: Implementowanie segmentacji sieci i segregowania](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Zastosuj technologie tylko w warstwie sieciowej**. Każdy host i sieć powinny być poddzielone i segregowane, tam gdzie to możliwe, na najniższym poziomie, który może być praktycznie zarządzany. W większości przypadków jest to stosowane z warstwy linku danych do warstwy aplikacji, a także do niej. Jednak w środowiskach wrażliwych może być odpowiednia izolacja fizyczna. Miary oparte na hoście i całej sieci należy wdrażać w sposób uzupełniający i monitorowany centralnie. Wystarczy zaimplementowanie zapory lub urządzenia zabezpieczeń, ponieważ jedyny środek zabezpieczeń nie jest wystarczający. |[ACSC ochronę: Implementowanie segmentacji sieci i segregowania](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Skorzystaj z zasad o najniższych uprawnieniach i potrzebujesz do lokalnych do do lokalnych**. Jeśli host, usługa lub sieć nie musi komunikować się z innym hostem, usługą lub siecią, nie powinno być dozwolone. Jeśli host, usługa lub Sieć musi komunikować się tylko z innym hostem, usługą lub siecią przy użyciu określonego portu lub protokołu, powinien być ograniczony tylko do portów i protokołów. Zastosowanie tych zasad w sieci spowoduje uzupełnienie minimalnych uprawnień użytkowników i znacząco zwiększy ogólną stan bezpieczeństwa środowiska. |[ACSC ochronę: Implementowanie segmentacji sieci i segregowania](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Oddzielne hosty i sieci na podstawie ich czułości lub znaczenia dla operacji działania biznesowego**. Może to obejmować użycie innego sprzętu lub platform w zależności od różnych klasyfikacji zabezpieczeń, domen zabezpieczeń lub wymagań dotyczących dostępności/integralności dla określonych hostów lub sieci. W szczególności oddziel sieci zarządzania i rozważ fizyczne izolowanie sieci zarządzania poza pasmem w środowiskach poufnych. |[ACSC ochronę: Implementowanie segmentacji sieci i segregowania](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Identyfikuj, uwierzytelniaj i Autoryzuj dostęp przez wszystkie jednostki do wszystkich innych jednostek**. Wszyscy użytkownicy, hosty i usługi powinni mieć dostęp do wszystkich innych użytkowników, hostów i usług ograniczonych tylko do tych, które są wymagane do wykonania wyznaczonych obowiązków lub funkcji. Wszystkie starsze lub lokalne usługi, które pomijają lub obniżają siłę tożsamości, uwierzytelniania i usług autoryzacji, powinny być wyłączone wszędzie tam, gdzie jest to możliwe i są ściśle monitorowane. |[ACSC ochronę: Implementowanie segmentacji sieci i segregowania](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Zaimplementuj opcję Zezwól na listę ruchu sieciowego zamiast listy Odmów**. Zezwalanie na dostęp tylko dla znanego dobrego ruchu sieciowego (identyfikowanego, uwierzytelnianego i autoryzowanego) zamiast odmawiania dostępu do znanego nieprawidłowego ruchu sieciowego (na przykład zablokowanie określonego adresu lub usługi). Lista dozwolonych skutkuje wyższymi zasadami zabezpieczeń w celu odmowy list i znacząco zwiększa możliwości wykrywania i oceniania potencjalnych intruzów w sieci. |[ACSC ochronę: Implementowanie segmentacji sieci i segregowania](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Zdefiniowanie listy dozwolonych witryn sieci Web i blokowanie wszystkich nieznajdujących się na liście witryn sieci Web** skutecznie usuwa jedną z najpopularniejszych technik dostarczania i eksfiltracji danych używanych przez atakującej. Jeśli użytkownicy mają uzasadnione wymagania dotyczące dostępu do licznych witryn sieci Web lub szybkiej zmiany listy witryn sieci Web; należy wziąć pod uwagę koszty takiej implementacji. Nawet względnie niezależna lista dozwolonych zapewnia lepsze zabezpieczenia niż nie polega na listach Odmów lub nie ma żadnych ograniczeń, przy jednoczesnym zmniejszeniu kosztów wdrożenia. Przykładem dozwolonej listy dozwolonych może być cała poddomena Australii, czyli "*. au" lub umożliwienie górnych witryn 1 000 w klasyfikacji witryny Alexa (po przefiltrowaniu domen dynamicznego systemu nazw domen (DDNS) i innych niewłaściwych domenach).| [Podręcznik bezpieczeństwa informacji rządowych Australii (ISM)](https://www.cyber.gov.au/ism)
|

Ten artykuł zawiera informacje i zalecenia dotyczące sposobu, w jaki ruch sieciowy opuszcza środowisko platformy Azure. Obejmuje ona systemy wdrożone na platformie Azure, korzystając z infrastruktury jako usługi (IaaS) i platformy jako usługi (PaaS).

Artykuł dotyczący ruchu przychodzącego [bramy](gateway-ingress-traffic.md) rozwiązuje ruch sieciowy wprowadzany do środowiska platformy Azure i jest pomocnikiem tego artykułu w celu zapewnienia pełnego zakresu kontroli sieci.

## <a name="architecture"></a>Architektura

Aby odpowiednio kontrolować ruch wychodzący, podczas projektowania i implementowania zabezpieczeń sieci należy najpierw zrozumieć, jak ruch sieciowy ruchu sieciowego działa na platformie Azure zarówno w IaaS, jak i w PaaS. Ta sekcja zawiera omówienie sposobu przetwarzania ruchu wychodzącego wygenerowanego przez zasób hostowany na platformie Azure oraz kontrolki zabezpieczeń dostępne do ograniczania i kontrolowania tego ruchu.

### <a name="architecture-components"></a>Składniki architektury

Przedstawiony tutaj diagram architektury przedstawia możliwe ścieżki, które mogą być wykonywane przez ruch sieciowy podczas zamykania systemu wdrożonego w podsieci w sieci wirtualnej. Ruch w sieci wirtualnej jest zarządzany i podlega zasadom routingu i zabezpieczeń, które mają zastosowanie do zasobów zawartych w ramach programu. Składniki związane z ruchem wychodzącym są podzielone na systemy, obowiązujące trasy, typy następnych przeskoków, mechanizmy kontroli zabezpieczeń i PaaS ruch wychodzący.

![Architektura](media/egress-traffic.png)

### <a name="systems"></a>Systemach

Systemy to zasoby platformy Azure i powiązane składniki generujące ruch wychodzący w podsieci IP, która jest częścią sieci wirtualnej.

| Składnik | Opis |
| --- | ---|
|Sieć wirtualna (VNet) | Sieć wirtualna to podstawowe zasoby platformy Azure, które udostępniają platformę i granicę na potrzeby wdrażania zasobów i włączania komunikacji. Sieć wirtualna istnieje w regionie świadczenia usługi Azure i definiuje przestrzeń adresów IP oraz granice routingu dla zintegrowanych zasobów sieci wirtualnej, takich jak Virtual Machines.|
|Subnet | Podsieć jest zakresem adresów IP, który jest tworzony w sieci wirtualnej. Można utworzyć wiele podsieci w sieci wirtualnej dla segmentacji sieci.|
|Interfejs sieciowy| Interfejs sieciowy jest zasobem, który istnieje na platformie Azure. Jest on dołączony do maszyny wirtualnej i ma przypisany prywatny, nieinternetowy adres IP z podsieci, z którą jest skojarzona. Ten adres IP jest dynamicznie lub statycznie przypisywany przez Azure Resource Manager.|
|Publiczne adresy IP| Publiczny adres IP jest zasobem, który rezerwuje jeden z publicznych adresów IP należących do firmy Microsoft w określonym regionie do użycia w sieci wirtualnej. Może być skojarzona z określonym interfejsem sieciowym lub zasobem PaaS, co umożliwia zasobom komunikowanie się z Internetem, ExpressRoute i z innymi systemami PaaS.|
|

### <a name="routes"></a>Trasy

Ścieżka, która obejmuje ruch wychodzący, zależy od obowiązujących tras dla tego zasobu, który jest wynikowym zestawem tras określonym przez kombinację tras uzyskanych ze wszystkich możliwych źródeł i aplikacji logiki routingu platformy Azure.

| Składnik | Opis |
|--- | ---|
|Trasy systemowe| Platforma Azure automatycznie tworzy trasy systemowe i przypisuje trasy do każdej podsieci w sieci wirtualnej. Nie można utworzyć lub usunąć tras systemowych, ale niektóre mogą zostać zastąpione trasami niestandardowymi. Platforma Azure tworzy domyślne trasy systemowe dla każdej podsieci i dodaje dodatkowe opcjonalne trasy domyślne do określonych podsieci lub każdej podsieci, gdy są wykorzystywane określone możliwości platformy Azure.|
|Punkty końcowe usługi| Punkty końcowe usługi zapewniają bezpośrednie, prywatne połączenie wychodzące z podsieci z określoną możliwością PaaS. Punkty końcowe usługi, które są dostępne tylko dla podzbioru możliwości PaaS, zapewniają zwiększoną wydajność i bezpieczeństwo zasobów w sieci wirtualnej uzyskujących dostęp do PaaS.|
|Tabele tras| Tabela tras to zasób na platformie Azure, który można utworzyć, aby określić trasy zdefiniowane przez użytkownika (UDR), które mogą uzupełniać lub przesłonić trasy lub trasy systemu, w Border Gateway Protocol. Każdy UDR określa sieć, maskę sieci i następnym przeskokiem. Tabela tras może być skojarzona z podsiecią, a ta sama tabela tras może być skojarzona z wieloma podsieciami, ale podsieć może mieć tylko zero lub jedną tabelę tras.|
|Border Gateway Protocol (BGP)| BGP to międzyautonomiczny protokół routingu systemu. System autonomiczny jest siecią lub grupą sieci w ramach wspólnego administrowania i ze wspólnymi zasadami routingu. Protokół BGP służy do wymiany informacji o routingu między systemami autonomicznymi. Protokół BGP można zintegrować z sieciami wirtualnymi za poorednictwem bram sieci wirtualnej.|
|

### <a name="next-hop-types-defined"></a>Zdefiniowane typy następnego przeskoku

Każda trasa na platformie Azure obejmuje zakres sieci i skojarzoną maskę podsieci oraz następny przeskok, który określa sposób przetwarzania ruchu.

Typ następnego przeskoku | Opis
---- | ----
**Virtual Network** | Kieruje ruchem między zakresami adresów w przestrzeni adresowej sieci wirtualnej. Platforma Azure tworzy trasę z prefiksem adresu odpowiadającym każdemu zakresowi adresów zdefiniowanemu w przestrzeni adresowej sieci wirtualnej. Jeśli przestrzeń adresowa sieci wirtualnej ma zdefiniowane wiele zakresów adresów, platforma Azure tworzy indywidualną trasę dla każdego zakresu adresów. Platforma Azure automatycznie kieruje ruchem między podsieciami w sieci wirtualnej przy użyciu tras utworzonych dla każdego zakresu adresów.
**Komunikacja równorzędna sieci wirtualnych** | Po utworzeniu komunikacji równorzędnej sieci wirtualnej między dwiema sieciami wirtualnymi do sieci wirtualnej jest dodawana trasa dla każdego zakresu adresów każdej sieci wirtualnej. Ruch jest kierowany między równorzędnymi sieciami wirtualnymi w taki sam sposób jak podsieci w sieci wirtualnej.
**Brama sieci wirtualnej** | Co najmniej jedna trasa z bramą sieci wirtualnej wymienioną jako typ następnego przeskoku jest dodawana po dodaniu bramy sieci wirtualnej do sieci wirtualnej. Zawarte trasy są tymi, które są skonfigurowane w ramach zasobu bramy sieci lokalnej i dowolnych tras uzyskiwanych za pośrednictwem protokołu BGP.
**Urządzenie wirtualne** | Urządzenie wirtualne zazwyczaj uruchamia aplikację sieciową, taką jak Zapora. Urządzenie wirtualne umożliwia dodatkowe przetwarzanie ruchu, na przykład filtrowanie, inspekcję lub translację adresów. Każda trasa z typem przeskoku urządzenia wirtualnego musi również określać adres IP następnego przeskoku.
**VirtualNetworkServiceEndpoint** | Publiczne adresy IP dla określonej usługi są dodawane jako trasy do podsieci przy następnym przeskoku VirtualNetworkServiceEndpoint, gdy punkt końcowy usługi jest włączony. Punkty końcowe usługi są włączone dla poszczególnych usług w poszczególnych podsieciach w obrębie sieci wirtualnej. Publiczne adresy IP usług platformy Azure są okresowo zmieniane. Platforma Azure zarządza adresami w tabeli tras automatycznie po zmianie adresów.
**Internet** | Ruch z następnym przeskokiem Internetu spowoduje zamknięcie sieci wirtualnej i automatyczne przetłumaczenie na publiczny adres IP z puli dynamicznej dostępnej w skojarzonym regionie platformy Azure lub przy użyciu publicznego adresu IP skonfigurowanego dla tego zasobu. Jeśli adres docelowy dotyczy jednej z usług platformy Azure, ruch jest kierowany bezpośrednio do usługi za pośrednictwem sieci szkieletowej platformy Azure, a nie przez kierowanie ruchu do Internetu. Ruch między usługami Azure nie przechodzi przez Internet niezależnie od tego, w którym regionie platformy Azure istnieje sieć wirtualna lub w którym regionie platformy Azure zostało wdrożone wystąpienie usługi platformy Azure.
**Brak** | Ruch z następnym przeskokiem nie jest usuwany. Platforma Azure tworzy domyślne trasy systemowe dla zarezerwowanych prefiksów adresów bez typu następnego przeskoku. Trasy z następnym przeskokiem nie mogą być również dodawane przy użyciu tabel tras, aby uniemożliwić kierowanie ruchu do określonych sieci.
|

### <a name="security-controls"></a>Kontrolki zabezpieczeń

Formant | Opis
----- | -----
**Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)** | Sieciowych grup zabezpieczeń kontrolować ruch do i z zasobów sieci wirtualnej na platformie Azure. Sieciowych grup zabezpieczeń stosują reguły dla przepływów ruchu, które są dozwolone lub odrzucane, które obejmują ruch na platformie Azure oraz między systemem Azure i sieciami zewnętrznymi, takimi jak lokalne lub internetowe. Sieciowych grup zabezpieczeń są stosowane do podsieci w obrębie sieci wirtualnej lub do poszczególnych interfejsów sieciowych.
**Zapora platformy Azure** | Zapora systemu Azure to zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby sieci wirtualnej platformy Azure. Jest to w pełni stanowa zapora oferowana jako usługa, z wbudowaną wysoką dostępnością i możliwością nieograniczonego skalowania w chmurze. Zaporę platformy Azure można skonfigurować przy użyciu tradycyjnych reguł filtrowania sieci na podstawie adresów IP, protokołów i portów, ale również obsługuje filtrowanie na podstawie w pełni kwalifikowanych nazw domen (FQDN), tagów usługi i niezbudowanej analizy zagrożeń.
**Wirtualne urządzenie sieciowe (urządzenie WUS)** | Wirtualne urządzenia sieciowe to nośniki maszyn wirtualnych, które mogą zapewnić obsługę sieci, zabezpieczeń i innych funkcji na platformie Azure. Urządzeń WUS obsługiwać funkcje i usługi sieciowe w formie maszyn wirtualnych w sieciach wirtualnych i wdrożeniach. Urządzeń WUS może służyć do rozwiązywania określonych wymagań, integrowania z narzędziami do zarządzania i działaniami lub zapewnienia spójności z istniejącymi produktami. Platforma Azure obsługuje szeroką gamę sieciowych urządzeń wirtualnych innych firm, w tym zapory aplikacji sieci Web (WAF), zapory, bramy/routery, kontrolery dostarczania aplikacji (ADC) i optymalizatory WAN.
**Zasady punktu końcowego usługi (wersja zapoznawcza)** | Zasady punktu końcowego usługi sieci wirtualnej umożliwiają filtrowanie ruchu w sieci wirtualnej do usług platformy Azure, co pozwala na dostęp tylko do określonych zasobów usługi platformy Azure za pośrednictwem punktów końcowych usługi. Zasady punktu końcowego zapewniają szczegółową kontrolę dostępu w przypadku ruchu w sieci wirtualnej do usług platformy Azure.
**Azure Policy** | Azure Policy to usługa platformy Azure do tworzenia i przypisywania zasad oraz zarządzania nimi. Zasady te służą do kontrolowania typów zasobów, które można wdrożyć, oraz konfiguracji tych zasobów. Zasady mogą służyć do wymuszania zgodności, uniemożliwiając wdrożenie zasobów, jeśli nie spełniają wymagań lub mogą być używane do monitorowania w celu raportowania stanu zgodności.
|

### <a name="paas-egress"></a>PaaS ruch wychodzący

Większość zasobów PaaS nie generuje ruchu wychodzącego, ale odpowiada na przychodzące żądania (takie jak Application Gateway, Storage, SQL Database itp.) lub dane przekazywane z innych zasobów (takich jak Service Bus i Azure Relay). Przepływy komunikacji sieciowej między zasobami PaaS, takimi jak App Services do magazynu lub bazami danych SQL, są kontrolowane i zawarte przez platformę Azure i zabezpieczone za pomocą tożsamości i innych kontrolek konfiguracji zasobów, a nie segmentacji sieci ani segregowania.

Zasoby PaaS wdrożone w sieci wirtualnej otrzymują dedykowane adresy IP i podlegają dowolnym kontrolkom routingu i sieciowych grup zabezpieczeń w taki sam sposób jak inne zasoby w sieci wirtualnej. Zasoby PaaS, które nie istnieją w ramach sieci wirtualnej, będą wykorzystywać pulę adresów IP, które są współużytkowane przez wszystkie wystąpienia zasobu, które są publikowane za pośrednictwem dokumentacji firmy Microsoft lub mogą być określane za pośrednictwem Azure Resource Manager.

## <a name="general-guidance"></a>Ogólne wytyczne

Aby zaprojektować i utworzyć bezpieczne rozwiązania na platformie Azure, należy zrozumieć i kontrolować ruch sieciowy w taki sposób, że może wystąpić tylko zidentyfikowana i autoryzowana komunikacja. Celem niniejszych wskazówek i wskazówek dotyczących składników w kolejnych sekcjach jest opisanie narzędzi i usług, których można użyć do zastosowania zasad opisanych w [artykule ACSC Protect: Implementowanie segmentacji sieci i podziału](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) między obciążeniami platformy Azure. Obejmuje to szczegółowo, jak utworzyć architekturę wirtualną w celu zabezpieczania zasobów, gdy nie jest możliwe stosowanie tych samych tradycyjnych kontroli fizycznych i sieci, które są możliwe w środowisku lokalnym.

### <a name="guidance"></a>Wskazówki

* Ogranicz liczbę punktów ruchu wychodzącego dla sieci wirtualnych
* Zastąp domyślną trasę systemową dla wszystkich podsieci, które nie potrzebują bezpośredniej komunikacji wychodzącej z Internetem
* Projektuj i Implementuj kompletną architekturę sieci, aby identyfikować i kontrolować wszystkie punkty ruchu przychodzącego i wychodzącego do zasobów platformy Azure
* Rozważ użycie sieci typu Hub i szprych dla sieci wirtualnych, zgodnie z opisem w dokumentacji Microsoft Virtual Data Centre (VDC).
* Wykorzystywanie produktów z wbudowanymi funkcjami zabezpieczeń dla połączeń wychodzących z Internetem (na przykład Zapora platformy Azure, wirtualne urządzenia sieciowe lub serwery proxy sieci Web)
* Użyj kontrolek tożsamości, takich jak dostęp oparty na rolach, dostęp warunkowy i uwierzytelnianie wieloskładnikowe (MFA), aby ograniczyć uprawnienia do konfiguracji sieci
* Zaimplementuj blokady, aby zapobiec modyfikacji lub usunięciu kluczowych elementów konfiguracji sieci
* Wdrażanie PaaS w konfiguracji zintegrowanej sieci wirtualnej na potrzeby szerszego segregowania i kontroli
* Implementowanie ExpressRoute na potrzeby łączności z sieciami lokalnymi
* Implementowanie sieci VPN do integracji z sieciami zewnętrznymi
* Wykorzystanie Azure Policy, aby ograniczyć regiony i zasoby tylko do tych, które są niezbędne do korzystania z funkcji systemu
* Wykorzystanie Azure Policy do wymuszania konfiguracji zabezpieczeń linii bazowej dla zasobów
* Wykorzystanie Network Watcher i Azure Monitor do rejestrowania, inspekcji i wglądu w ruch sieciowy w ramach platformy Azure

### <a name="resources"></a>Zasoby

Element | Łącze
-----------| ---------
_Udokumentowane dokumenty dotyczące zgodności z przepisami i zasad, w tym wskazówki dla użytkowników_ | [https://aka.ms/au-irap](https://aka.ms/au-irap)
_Wirtualne centrum danych Azure_ | [https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)
_Segmentacja sieci ACSC_ | [https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
_ACSC zabezpieczenia chmury dla dzierżawców_ | [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
_Podręcznik zabezpieczeń informacji ACSC_ | [https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)
|

## <a name="component-guidance"></a>Wskazówki dotyczące składników

Ta sekcja zawiera dalsze wskazówki dotyczące poszczególnych składników związanych z ruchem wychodzącym dla systemów wdrożonych na platformie Azure. W każdej sekcji opisano intencję określonego składnika z łączami do dokumentacji i przewodników konfiguracji, których można użyć do ułatwienia projektowania i tworzenia działań.

### <a name="systems-security"></a>Zabezpieczenia systemów

Cała komunikacja z zasobami w ramach platformy Azure odbywa się za pośrednictwem infrastruktury sieci utrzymywanej przez firmę Microsoft, która zapewnia łączność i funkcje zabezpieczeń. Zakres ochrony jest wdrażany automatycznie przez firmę Microsoft w celu ochrony platformy Azure i infrastruktury sieciowej oraz dodatkowych funkcji dostępnych jako usługi na platformie Azure w celu kontrolowania ruchu sieciowego i ustanawiania segmentacji sieci. podział.

### <a name="virtual-network-vnet"></a>Sieć wirtualna (VNet)

Sieci wirtualne są jednym z podstawowych bloków konstrukcyjnych dla sieci na platformie Azure. Sieci wirtualne definiują przestrzeń adresów IP i granicę routingu, które mają być używane w różnych systemach. Sieci wirtualne są podzielone na podsieci, a wszystkie podsieci w ramach Virtual Network mają bezpośrednią trasę sieciową. Korzystając z bram sieci wirtualnej (ExpressRoute lub VPN), systemy w sieci wirtualnej można zintegrować ze środowiskami lokalnymi i zewnętrznymi, a za pośrednictwem platformy Azure udostępnionej translacji adresów sieciowych (NAT) i publicznego adresu IP, systemy mogą Nawiązywanie połączenia z Internetem lub innymi regionami i usługami platformy Azure. Zrozumienie sieci wirtualnych i skojarzonych z nimi parametrów konfiguracji i routingu jest kluczowe w zrozumieniu i kontrolowaniu ruchu sieciowego w sieci.

W miarę jak sieci wirtualne tworzą podstawową przestrzeń adresową i granicę routingu w ramach platformy Azure, można jej użyć jako głównego bloku segmentacji sieci i rozdzielenia.

| Resource | Łącze |
| --- | --- |
| *Omówienie sieci wirtualnych* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) |
| *Planowanie sieci wirtualnych — Przewodnik*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) |
| *Tworzenie Virtual Network szybkiego startu* | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
|

### <a name="subnet"></a>Subnet

Podsieci to kluczowy składnik służący do segmentacji sieci i podziału na platformę Azure. Podsieci mogą służyć do zapewnienia rozdzielania między systemami. Podsieć jest zasobem w sieci wirtualnej, w której są stosowane sieciowych grup zabezpieczeń, tabele tras i punkty końcowe usługi. Podsieci mogą być używane jako adresy źródłowe i docelowe dla reguł zapory i list kontroli dostępu.

Podsieci w sieci wirtualnej powinny być planowane w celu spełnienia wymagań obciążeń i systemów. Poszczególne osoby związane z projektowaniem lub implementacją podsieci powinny odnosić się do wytycznych ACSC dotyczących segmentacji sieci w celu określenia sposobu grupowania systemów w podsieci.

|Resource|Łącze|
|---|---|
|*Dodawanie, zmienianie i usuwanie podsieci sieci wirtualnej* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
|

### <a name="network-interface"></a>Interfejs sieciowy

Interfejsy sieciowe są źródłem całego ruchu wychodzącego z maszyny wirtualnej. Interfejsy sieciowe umożliwiają konfigurację adresowania IP i mogą być używane do stosowania sieciowych grup zabezpieczeń lub do kierowania ruchu przez urządzenie WUS. Interfejsy sieciowe dla maszyn wirtualnych powinny być planowane i odpowiednio skonfigurowane do dopasowania z ogólną segmentacją sieci i celami podziału.

|Resource|Łącze|
|---|---|
|*Tworzenie, zmienianie lub usuwanie interfejsu sieciowego* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface) |
|*Adresowanie IP interfejsu sieciowego*               | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
|

### <a name="vnet-integrated-paas"></a>Zintegrowana PaaS sieci wirtualnej

PaaS może zapewnić rozszerzoną funkcjonalność i dostępność oraz zmniejszyć koszty związane z zarządzaniem, ale muszą być odpowiednio zabezpieczone. Aby zwiększyć kontrolę, wymusić segmentację sieci lub zapewnić bezpieczny punkt wychodzący dla aplikacji i usług, wiele możliwości PaaS można zintegrować z siecią wirtualną.

Korzystając z PaaS jako zintegrowanej części architektury systemu lub aplikacji, firma Microsoft udostępnia wiele mechanizmów wdrażania PaaS w sieci wirtualnej. Metodologia wdrażania może pomóc ograniczyć dostęp, zapewniając łączność i integrację z wewnętrznymi systemami i aplikacjami. Przykłady obejmują środowiska App Service, wystąpienia zarządzane SQL i wiele innych.

W przypadku wdrażania PaaS w sieci wirtualnej, w której zostały zaimplementowane kontrolki routingu i sieciowej grupy zabezpieczeń, ważne jest, aby zrozumieć określone wymagania dotyczące komunikacji, w tym dostęp do zarządzania z usług firmy Microsoft i ścieżkę, która ruch komunikacyjny będzie podejmowany podczas odpowiadania na żądania przychodzące z tych usług.

| Resource  | Łącze  |
| --- | --- |
| *Integracja z siecią wirtualną dla usług platformy Azure* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services) |
| *Integrowanie aplikacji z usługą Azure Virtual Network Przewodnik po poradniku* | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
|

### <a name="public-ip"></a>Publiczny adres IP

Publiczne adresy IP są używane podczas komunikacji poza siecią wirtualną. Obejmuje to zasoby PaaS i wszystkie trasy z następnym przeskokiem Internetu. Jednostki Wspólnoty powinny dokładnie zaplanować przydział publicznych adresów IP i przypisać je tylko do zasobów, w których istnieje oryginalne wymaganie. Ogólnie rzecz biorąc, publiczne adresy IP powinny być przydzieloną do kontrolowanych punktów wychodzących dla sieci wirtualnej, takich jak Zapora platformy Azure, VPN Gateway lub wirtualne urządzenia sieciowe.

|Resource|Łącze|
|---|---|
|*Przegląd Publiczne adresy IP*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) |
|*Tworzenie, zmienianie lub usuwanie publicznego adresu IP* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
|

## <a name="effective-routes"></a>Efektywne trasy

Efektywne trasy to wynikowy zestaw tras określony przez kombinację tras systemowych, punktów końcowych usług, tabel tras i protokołu BGP oraz aplikacji logiki routingu platformy Azure. Po wysłaniu ruchu wychodzącego z podsieci platforma Azure wybiera trasę na podstawie docelowego adresu IP przy użyciu algorytmu dopasowania najdłuższego prefiksu. Jeśli wiele tras zawiera ten sam prefiks adresu, platforma Azure wybiera typ trasy na podstawie następującego priorytetu:

1. Trasa zdefiniowana przez użytkownika
2. Trasa protokołu BGP
3. Trasa systemowa

Należy pamiętać, że trasy systemowe dla ruchu związanego z siecią wirtualną, Komunikacja równorzędna sieci wirtualnych lub punkty końcowe usługi sieci wirtualnej są preferowanymi trasami, nawet jeśli trasy protokołu BGP są bardziej szczegółowe.

Poszczególne osoby związane z projektowaniem lub implementacją topologii routingu na platformie Azure powinny zrozumieć, jak platforma Azure kieruje ruchem i opracowuje architekturę, która równoważy konieczną funkcjonalność systemów z wymaganymi zabezpieczeniami i widocznością. Należy zachować ostrożność w celu zaplanowania środowiska, aby uniknąć nadmiernych interwencji i wyjątków dla zachowań routingu, ponieważ spowoduje to zwiększenie złożoności i może ułatwić rozwiązywanie problemów i znajdowanie błędów.

| Resource | Łącze  |
| --- | --- |
| *Wyświetl efektywne trasy* | <https://docs.microsoft.com/azure/virtual-network/manage-route-table#view-effective-routes> |
|

### <a name="system-routes"></a>Trasy systemowe

W przypadku [tras systemowych](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#system-routes)poszczególne osoby związane z projektowaniem lub implementacją sieci wirtualnych powinny zrozumieć domyślne trasy systemowe i opcje dostępne do uzupełnienia lub zastąpienia tych tras.

### <a name="service-endpoints"></a>Punkty końcowe usługi

Włączenie [punktów końcowych usługi](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) w podsieci zapewnia bezpośrednią ścieżkę komunikacji do skojarzonego zasobu PaaS. Może to zapewnić zwiększoną wydajność i bezpieczeństwo, ograniczając dostępną ścieżkę komunikacji tylko do tej usługi. Korzystanie z punktów końcowych usługi wprowadza potencjalną ścieżkę eksfiltracjii danych, ponieważ konfiguracja domyślna umożliwia dostęp do wszystkich wystąpień usługi PaaS, a nie konkretnych wystąpień wymaganych dla aplikacji lub systemu.

Jednostki Wspólnoty powinny oszacować ryzyko związane z zapewnianiem bezpośredniego dostępu do zasobu PaaS, w tym prawdopodobieństwo i konsekwencję nieużywanej ścieżki.

Aby ograniczyć potencjalne zagrożenia związane z punktami końcowymi usługi, należy zaimplementować zasady punktu końcowego usługi, jeśli jest to możliwe, lub rozważyć włączenie punktów końcowych usługi w zaporze platformy Azure lub urządzenie WUS podsieci i kierowanie ruchu z określonych podsieci za pośrednictwem tego miejsca, gdzie można stosować filtrowanie, monitorowanie lub inspekcję.

|Resource|Łącze|
|---|---|
|*Samouczek: Ogranicz dostęp sieciowy do zasobów PaaS za pomocą punktów końcowych usługi sieci wirtualnej przy użyciu Azure Portal* |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

### <a name="route-tables"></a>Tabele tras

Tabele tras zapewniają mechanizm skonfigurowany przez administratora do kontrolowania ruchu sieciowego w ramach platformy Azure. Tabele tras mogą być wykorzystywane do przekazywania ruchu sieciowego do zapory platformy Azure lub urządzenie WUS, łączyć się bezpośrednio z zasobami zewnętrznymi lub przesłaniać trasy systemu Azure. Tabel tras można także użyć, aby uniemożliwić dostęp do sieci za pośrednictwem bramy sieci wirtualnej w celu udostępnienia zasobów w podsieci przez wyłączenie propagacji trasy bramy sieci wirtualnej.

|Resource|Łącze|
|---|---|
|*Koncepcje routingu — trasy niestandardowe* |[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)|
|*Samouczek: Kierowanie ruchu sieciowego* |[https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)|
|

### <a name="border-gateway-protocol-bgp"></a>Border Gateway Protocol (BGP)

Protokół BGP może być wykorzystywany przez bramy sieci wirtualnej do dynamicznej wymiany informacji routingu z lokalnymi lub innymi sieciami zewnętrznymi. Protokół BGP ma zastosowanie do sieci wirtualnej, gdy jest konfigurowana za pośrednictwem bramy sieci wirtualnej ExpressRoute przez prywatną komunikację równorzędną ExpressRoute i w przypadku włączenia jej na VPN Gateway platformy Azure.

Poszczególne osoby biorące pod uwagę projekt lub implementację sieci wirtualnych i bram sieci wirtualnej na platformie Azure powinny mieć czas, aby zrozumieć, jakie są opcje dotyczące zachowań i konfiguracji protokołu BGP na platformie Azure.

|Resource|Łącze|
|---|---|
|*Pojęcia dotyczące routingu: PROTOKÓŁ* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools)|
|*Wymagania dotyczące routingu ExpressRoute* | [https://docs.microsoft.com/azure/expressroute/expressroute-routing](https://docs.microsoft.com/azure/expressroute/expressroute-routing)|
|*Informacje o protokole BGP z platformą Azure VPN Gateway* |[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)|
|*Samouczek: Konfigurowanie sieci VPN typu lokacja-lokacja za pośrednictwem komunikacji równorzędnej firmy Microsoft ExpressRoute* |[https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering](https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering)|
|

## <a name="next-hop-types"></a>Typy następnego przeskoku

### <a name="virtual-network"></a>Sieć wirtualna

Trasy z następnym przeskokiem Virtual Network są dodawane automatycznie jako trasy systemowe, ale można je również dodać do tras zdefiniowanych przez użytkownika, aby skierować ruch z powrotem do sieci wirtualnej w wystąpieniach, w których trasa systemowa została przesłonięta.

### <a name="vnet-peering"></a>Komunikacja równorzędna sieci wirtualnych

Komunikacja równorzędna sieci wirtualnych umożliwia komunikację między dwiema różnymi sieciami wirtualnymi. Skonfigurowanie komunikacji równorzędnej sieci wirtualnej musi być włączone w poszczególnych sieciach wirtualnych, ale sieci wirtualne nie muszą znajdować się w tym samym regionie, subskrypcji ani skojarzone z tą samą dzierżawą usługi Azure Active Directory (Azure AD).

Podczas konfigurowania komunikacji równorzędnej sieci wirtualnych ważne jest, aby osoby korzystające z projektowania lub implementacji komunikacji równorzędnej sieci wirtualnej zrozumieć cztery skojarzone parametry konfiguracji i jak mają zastosowanie do każdej strony elementu równorzędnego:

1. **Zezwalaj na dostęp do sieci wirtualnej:** Wybierz opcję **włączone** (domyślnie), aby umożliwić komunikację między dwiema sieciami wirtualnymi. Włączenie komunikacji między sieciami wirtualnymi pozwala zasobom połączonym z siecią wirtualną komunikować się ze sobą za pomocą tej samej przepustowości i opóźnienia, tak jakby były połączone z tą samą siecią wirtualną.
2. **Zezwalaj na ruch przesłany dalej:** Zaznacz to pole wyboru, aby zezwalać na ruch przesyłany przez ruch sieciowy, który nie pochodzi z sieci wirtualnej — do przepływu tej sieci wirtualnej za pośrednictwem komunikacji równorzędnej. To ustawienie ma podstawowe znaczenie dla implementacji topologii sieci gwiazdy i gwiazdy.
3. **Zezwalaj na tranzyt bramy:** Zaznacz to pole wyboru, aby umożliwić sieci wirtualnej równorzędnej wykorzystanie bramy sieci wirtualnej podłączonej do tej sieci wirtualnej. *Zezwalaj na tranzyt bramy* w sieci wirtualnej z zasobem bramy sieci wirtualnej, ale stosuje się tylko wtedy, gdy w innej sieci wirtualnej jest włączona *Funkcja Użyj bram zdalnych* .
4. **Korzystanie z bram zdalnych:** Zaznacz to pole wyboru, aby zezwolić na przepływ ruchu z tej sieci wirtualnej przez bramę sieci wirtualnej dołączoną do sieci wirtualnej, z którą nastąpi Komunikacja równorzędna. *Używanie bram zdalnych* jest włączone w sieci wirtualnej bez bramy sieci wirtualnej i ma zastosowanie tylko wtedy, gdy opcja *Zezwalaj na tranzyt bramy* jest włączona w innej sieci wirtualnej.

|Resource|Łącze|
|---|---|
| Pojęcia: Wirtualne sieci równorzędne                   | [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) |
| Tworzenie, zmienianie lub usuwanie komunikacji równorzędnej sieci wirtualnej | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="virtual-network-gateway"></a>Brama sieci wirtualnej

Bramy sieci wirtualnej zapewniają mechanizm integrowania sieci wirtualnych z sieciami zewnętrznymi, takimi jak środowiska lokalne, środowiska partnerskie i inne wdrożenia w chmurze. Dwa typy bram sieci wirtualnej to ExpressRoute i sieci VPN.

#### <a name="expressroute-gateway"></a>Brama ExpressRoute

Bramy ExpressRoute zapewniają punkt wychodzący z sieci wirtualnej do środowiska lokalnego i powinny być wdrażane w celu spełnienia wymagań dotyczących zabezpieczeń, dostępności, finansów i wydajności. Bramy ExpressRoute zapewniają określoną przepustowość sieci i obciążają koszty użycia po wdrożeniu. Sieci wirtualne mogą mieć tylko jedną bramę ExpressRoute, ale może to być połączone z wieloma obwodami usługi ExpressRoute i mogą być używane przez wiele sieci wirtualnych za pośrednictwem komunikacji równorzędnej wirtualnej, co pozwala na współużytkowanie przepustowości i łączności. Należy zachować ostrożność konfigurowania routingu między środowiskami lokalnymi i sieciami wirtualnymi przy użyciu bram ExpressRoute, aby zapewnić kompleksową łączność przy użyciu znanych, kontrolowanych punktów ruchu wychodzącego w sieci. Jednostki Wspólnoty korzystające z bramy ExpressRoute za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute muszą również wdrożyć wirtualne urządzenia sieciowe (urządzenie WUS) w celu ustanowienia łączności sieci VPN z środowiskiem lokalnym w celu zapewnienia zgodności ze wskazówkami dotyczącymi użytkowników ACSC.

Należy pamiętać, że bramy ExpressRoute mają ograniczenia dotyczące zakresów adresów, społeczności i innych określonych elementów konfiguracji wymienianych za pomocą protokołu BGP.

| Resource  | Łącze  |
|---|---|
| ExpressRoute Gateway — Omówienie | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) |
| Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)
|

#### <a name="vpn-gateway"></a>VPN Gateway

Usługa Azure VPN Gateway udostępnia punkt sieci ruchu wychodzącego z sieci wirtualnej do sieci zewnętrznej w celu zapewnienia bezpiecznej łączności między lokacjami. Bramy sieci VPN zapewniają zdefiniowaną przepustowość sieci i obciążają koszty użycia po wdrożeniu. Jednostki Wspólnoty wykorzystujące VPN Gateway powinny mieć pewność, że zostały skonfigurowane zgodnie z zaleceniami konsumentów ACSC. Sieci wirtualne mogą mieć tylko jeden VPN Gateway, ale można je skonfigurować z wieloma tunelami i mogą być używane przez wiele sieci wirtualnych za pośrednictwem komunikacji równorzędnej sieci wirtualnych, co pozwala wielu wirtualnym współużytkować przepustowości i łączności. Bramy sieci VPN można ustanawiać za pośrednictwem Internetu lub przez ExpressRoute za pośrednictwem komunikacji równorzędnej firmy Microsoft.

| Resource  | Łącze |
| --- | --- |
| Przegląd VPN Gateway| [https://docs.microsoft.com/azure/vpn-gateway](https://docs.microsoft.com/azure/vpn-gateway)|
| Planowanie i projektowanie dla usługi VPN Gateway | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
| VPN Gateway platformy Azure w Australii platformy Azure | [VPN Gateway platformy Azure w Australii platformy Azure](vpn-gateway.md)
|

### <a name="next-hop-of-virtual-appliance"></a>Następny przeskok urządzenia wirtualnego

Następnym przeskokiem urządzenia wirtualnego jest możliwość przetwarzania ruchu sieciowego spoza topologii routingu i sieci platformy Azure zastosowanej do sieci wirtualnych. Urządzenia wirtualne mogą stosować reguły zabezpieczeń, przetłumaczać adresy, ustanawiać sieci VPN, ruch serwera proxy lub wiele innych funkcji. Następny przeskok urządzenia wirtualnego jest stosowany za pośrednictwem usługi UDR w tabeli tras i może służyć do kierowania ruchem do zapory platformy Azure, poszczególnych urządzenie WUS lub Azure Load Balancer zapewniania dostępności w wielu urządzeń WUS. Aby można było używać urządzenia wirtualnego do routingu, do przesyłania dalej IP muszą być włączone skojarzone interfejsy sieciowe.

| Resource  | Łącze |
| --- | ---|
| Pojęcia dotyczące routingu: Trasy niestandardowe | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) |
| Włączanie lub wyłączanie przekazywania adresów IP | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding)
|

### <a name="next-hop-of-virtualnetworkserviceendpoint"></a>Następny przeskok elementu VirtualNetworkServiceEndpoint

Trasy z typem następnego przeskoku VirtualNetworkServiceEndpoint są dodawane tylko wtedy, gdy punkt końcowy usługi jest skonfigurowany w podsieci i nie można go ręcznie skonfigurować przy użyciu tabel tras.

### <a name="next-hop-of-internet"></a>Następny przeskok z Internetu

Internet w następnym przeskoku jest używany do uzyskiwania dostępu do wszystkich zasobów, które korzystają z publicznego adresu IP, w tym Internetu, a także usług PaaS i Azure w regionach platformy Azure. Internetowe kolejne przeskoki nie wymagają trasy domyślnej (0.0.0.0/0) obejmującej wszystkie sieci, ale mogą być używane do włączania ścieżek routingu do określonych usług publicznych. Następny przeskok Internet powinien służyć do dodawania tras do autoryzowanych usług i możliwości wymaganych przez funkcje systemu, takie jak adresy Microsoft Management.

Przykłady usług, które można dodać przy użyciu następnego przeskoku z Internetu, to:

1. Usługi zarządzania kluczami dla aktywacji systemu Windows
2. Zarządzanie App Service Environment

|Resource|Łącze|
|---|---|
| Połączenia wychodzące na platformie Azure | [https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) |
| Używanie tras niestandardowych platformy Azure do włączania aktywacji usługi KMS | [https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation](https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) |
| Blokowanie App Service Environment  | [https://docs.microsoft.com/azure/app-service/environment/firewall-integration](https://docs.microsoft.com/azure/app-service/environment/firewall-integration) |
|

### <a name="next-hop-of-none"></a>Następny przeskok o brak

Nie można użyć następnego przeskoku elementu None, aby uniemożliwić komunikację z konkretną siecią. W przeciwieństwie do sieciowej grupy zabezpieczeń, która kontroluje, czy ruch jest dozwolony czy odrzucany od przechodzenia przez dostępną ścieżkę sieciową, przy użyciu następnego przeskoku nie usuwa ścieżki sieciowej całkowicie. Należy zachować ostrożność podczas tworzenia tras z następnym przeskokiem brak, szczególnie w przypadku zastosowania jej do domyślnej trasy równej 0.0.0.0/0, ponieważ może to mieć niezamierzone konsekwencje i może rozwiązywać problemy związane z systemem i czasochłonne.

## <a name="security"></a>Bezpieczeństwo

Implementowanie segmentacji sieci i kontroli podziału na możliwościach IaaS i PaaS jest realizowane za pośrednictwem zabezpieczania możliwości i implementowania kontrolowane ścieżki komunikacyjne z systemów, które będą komunikować się z pozwala.

Projektowanie i kompilowanie rozwiązań na platformie Azure to proces tworzenia architektury logicznej do zrozumienia, kontrolowania i monitorowania zasobów sieciowych w całej obecności platformy Azure. Ta architektura logiczna jest oprogramowaniem zdefiniowanym na platformie Azure i ma miejsce topologii sieci fizycznej wdrożonej w tradycyjnych środowiskach sieciowych.

Utworzona architektura logiczna musi zapewniać funkcjonalność potrzebną dla użyteczności, ale musi także zapewnić widoczność i kontrolę potrzebną w zakresie zabezpieczeń i integralności.

Osiągnięcie tego wyniku jest uzależnione od implementacji niezbędnego segmentacji sieci i narzędzi do segregowania, ale również w ochronie i wymuszanie topologii sieci i implementacji tych narzędzi.

### <a name="network-security-groups-nsgs"></a>Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)

Sieciowych grup zabezpieczeń są używane do określania ruchu przychodzącego i wychodzącego, który jest dozwolony dla podsieci lub określonego interfejsu sieciowego. Podczas konfigurowania sieciowych grup zabezpieczeń jednostki Wspólnoty powinny używać podejścia listy dozwolonych, w którym reguły są skonfigurowane tak, aby zezwalały na ruch wymagany z regułą domyślną skonfigurowaną tak, aby odrzucać cały ruch, który nie jest zgodny z konkretną instrukcją zezwolenia. Podczas planowania i konfigurowania sieciowych grup zabezpieczeń należy zachować ostrożność, aby upewnić się, że cały ruch przychodzący i wychodzący jest odpowiednio przechwytywany. Obejmuje to identyfikowanie i zrozumienie wszystkich zakresów prywatnych adresów IP wykorzystywanych w ramach sieci wirtualnych i środowiska lokalnego oraz określonych usług firmy Microsoft, takich jak Azure Load Balancer i wymagania dotyczące zarządzania PaaS. Osoby mające na celu projektowanie i implementację sieciowych grup zabezpieczeń powinni również zrozumieć użycie tagów usługi i grup zabezpieczeń aplikacji do tworzenia szczegółowych reguł zabezpieczeń, usług i aplikacji.

Należy pamiętać, że domyślna konfiguracja sieciowej grupy zabezpieczeń zezwala na ruch wychodzący do wszystkich adresów w sieci wirtualnej i wszystkich publicznych adresów IP.

|Resource|Łącze|
|---|---|
|Omówienie zabezpieczeń sieci | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

### <a name="azure-firewall"></a>Azure Firewall

Zapora platformy Azure może być wykorzystywana do tworzenia topologii sieci gwiazdy i satelity oraz wymuszania scentralizowanych kontroli zabezpieczeń sieci. Za pomocą zapory platformy Azure można spełnić wymagania wymagane przez usługę ISM dla ruchu wychodzącego przez implementację podejścia do listy dozwolonych, w przypadku których dozwolone są tylko adresy IP, protokoły, porty i nazwy FQDN wymagane dla funkcji systemu. Jednostki Wspólnoty powinny podejmować podejście oparte na ryzyku w celu ustalenia, czy funkcje zabezpieczeń zapewniane przez zaporę systemu Azure są wystarczające do ich wymagań. W scenariuszach, w których wymagane są dodatkowe funkcje zabezpieczeń poza tymi dostarczonymi przez zaporę platformy Azure, jednostki Wspólnoty powinny rozważyć wdrożenie urządzeń WUS.

|Resource|Łącze|
|---|---|
|*Dokumentacja zapory platformy Azure* | [https://docs.microsoft.com/azure/firewall](https://docs.microsoft.com/azure/firewall)|
|*Samouczek: Wdrażanie i Konfigurowanie zapory platformy Azure w sieci hybrydowej przy użyciu Azure PowerShell* | [https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps](https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps)|
|

### <a name="network-virtual-appliances-nvas"></a>Wirtualne urządzenia sieciowe (urządzeń WUS)

Urządzeń WUS może służyć do budowania topologii sieci gwiazdy, zapewniać ulepszone lub uzupełniające możliwości sieci lub może służyć jako alternatywa dla mechanizmów sieciowych platformy Azure w celu uzyskania znajomości i spójnej obsługi oraz zarządzania z lokalnymi usługami sieciowymi. Urządzeń WUS można wdrożyć w celu spełnienia określonych wymagań w zakresie bezpieczeństwa, takich jak: scenariusze, w których istnieje wymóg świadomości tożsamości związanej z ruchem sieciowym, odszyfrowywaniem protokołu HTTPS, inspekcją zawartości, filtrowaniem lub innymi funkcjami zabezpieczeń. Urządzeń WUS należy wdrożyć w konfiguracji o wysokiej dostępności i osobach zaangażowanych w projektowanie lub implementację urządzeń WUS należy zapoznać się z dokumentacją odpowiedniego dostawcy w celu uzyskania wskazówek dotyczących wdrażania na platformie Azure.

|Resource|Łącze|
|---|---|
|*Wdrażanie wirtualnych urządzeń sieciowych o wysokiej dostępności* | [https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)|
|

### <a name="service-endpoint-policies-preview"></a>Zasady punktu końcowego usługi (wersja zapoznawcza)

Skonfiguruj zasady punktu końcowego usługi na podstawie dostępności usługi i oceny ryzyka związanego z bezpieczeństwem prawdopodobieństwa i wpływu eksfiltracji danych. Zasady punktu końcowego usługi należy uwzględnić w usłudze Azure Storage i zarządzane w przypadku wystąpienia w przypadku innych usług w oparciu o profil powiązanego ryzyka.

| Resource | Łącze  |
| --- | --- |
| *Omówienie zasad punktu końcowego usługi* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview) |
| *Tworzenie, zmienianie lub usuwanie zasad punktu końcowego usługi przy użyciu Azure Portal* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal)
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy to kluczowy składnik służący do wymuszania i utrzymywania integralności logicznej architektury środowiska platformy Azure. Istnieją różne usługi i ścieżki ruchu wychodzącego w sieci dostępne za pomocą usług platformy Azure. Ważne jest, aby jednostki Wspólnoty znały zasoby istniejące w ich środowisku i dostępne punkty wychodzące z sieci. Aby zapewnić, że nieautoryzowane punkty wychodzące w sieci nie są tworzone w środowisku platformy Azure, jednostki Wspólnoty powinny używać Azure Policy do kontrolowania typów zasobów, które można wdrożyć, oraz konfiguracji tych zasobów. Praktyczne przykłady obejmują ograniczanie zasobów tylko do tych zatwierdzonych i zatwierdzonych do użycia i wymaganie, aby sieciowych grup zabezpieczeń dodać do podsieci.

|Resource | Łącze|
|---|---|
|*Przegląd Azure Policy* | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|*Przykładowe zasady dla dozwolonych typów zasobów* | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)|
|*Wymuś sieciowej grupy zabezpieczeń na zasadzie przykładowej podsieci*| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="paas-egress-capabilities"></a>Możliwości PaaS ruch wychodzący

Możliwości PaaS zapewniają możliwości w celu zwiększenia funkcjonalności i uproszczonego zarządzania, ale wprowadzają złożoność w zakresie wymagań dotyczących segmentacji sieci i rozdzielenia. Możliwości PaaS są zwykle konfigurowane przy użyciu publicznych adresów IP i są dostępne z Internetu.  Jeśli używasz możliwości PaaS w systemach i rozwiązaniach, należy zwrócić uwagę, aby zidentyfikować przepływy komunikacji między składnikami i utworzyć reguły zabezpieczeń sieciowych w celu umożliwienia tylko tej komunikacji. W ramach kompleksowego podejścia do obrony należy skonfigurować funkcje PaaS z szyfrowaniem, uwierzytelnianiem i odpowiednimi kontrolami dostępu i uprawnieniami.  

### <a name="public-ip-for-paas"></a>Publiczny adres IP dla PaaS

Publiczne adresy IP dla możliwości PaaS są przypisywane w oparciu o region, w którym usługa jest hostowana lub wdrożona. W przypadku tworzenia odpowiednich reguł zabezpieczeń sieci i topologii routingu na potrzeby segmentacji sieci i segregowania obejmujących sieci wirtualne platformy Azure, PaaS i ExpressRoute oraz Łączność z Internetem. Platforma Azure przydziela adresy IP z puli przydzielonej do każdego regionu platformy Azure. Firma Microsoft udostępnia adresy używane w poszczególnych regionach do pobrania, które są aktualizowane w regularnych i kontrolowany sposób. Usługi, które są dostępne w poszczególnych regionach, również często zmieniają się po udostępnieniu nowych usług lub wdrożenia usług. Jednostki Wspólnoty powinny regularnie sprawdzać te materiały i mogą korzystać z automatyzacji w razie potrzeby. Poszczególne adresy IP dla niektórych usług hostowanych w każdym regionie można uzyskać, kontaktując się z pomocą techniczną firmy Microsoft.

| Resource | Łącze |
| --- | --- |
| *Zakresy adresów IP centrum danych platformy Microsoft Azure*                   | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653) |
| *Usługi platformy Azure na region*                              | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional Australia-środkowe, Australia-środkowe-2, Australia-Wschodnia, Australia-Południowo-Wschodnia & produkty = wszystkie](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all) |
| *Przychodzące i wychodzące adresy IP w Azure App Service* | [https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips](https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips)
|

## <a name="next-steps"></a>Następne kroki

Porównaj swoją ogólną architekturę i Zaprojektuj opublikowane [projekty chronione dla aplikacji sieci Web IaaS i PaaS](https://aka.ms/au-protected).
