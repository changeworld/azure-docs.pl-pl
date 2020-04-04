---
title: Omówienie konfiguracji usługi Azure Application Gateway
description: W tym artykule opisano sposób konfigurowania składników bramy aplikacji platformy Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: absha
ms.openlocfilehash: 89d894a5125a16f95e6ef8a15c2503d48f3a8e55
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632186"
---
# <a name="application-gateway-configuration-overview"></a>Omówienie konfiguracji bramy aplikacji

Usługa Azure Application Gateway składa się z kilku składników, które można skonfigurować na różne sposoby dla różnych scenariuszy. W tym artykule pokazano, jak skonfigurować każdy składnik.

![Schemat blokowy składników bramy aplikacji](./media/configuration-overview/configuration-overview1.png)

Ten obraz ilustruje aplikację, która ma trzy odbiorniki. Pierwsze dwa są detektory wielu `http://acme.com/*` `http://fabrikam.com/*`witryn dla i , odpowiednio. Oba nasłuchiwają na porcie 80. Trzeci jest podstawowym odbiornikiem, który ma zakończenie end-to-end Transport Layer Security (TLS), wcześniej znany jako secure sockets layer (SSL) zakończenia.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Sieć wirtualna platformy Azure i dedykowana podsieć

Brama aplikacji jest dedykowanym wdrożeniem w sieci wirtualnej. W sieci wirtualnej dla bramy aplikacji jest wymagana dedykowana podsieć. W podsieci może być dostępnych wiele wystąpień wdrożenia bramy danej aplikacji. Można również wdrożyć inne bramy aplikacji w podsieci. Ale nie można wdrożyć żadnego innego zasobu w podsieci bramy aplikacji.

> [!NOTE]
> Nie można mieszać Standard_v2 i standardowej bramy aplikacji platformy Azure w tej samej podsieci.

#### <a name="size-of-the-subnet"></a>Rozmiar podsieci

Brama aplikacji używa jednego prywatnego adresu IP na wystąpienie oraz innego prywatnego adresu IP, jeśli skonfigurowano prywatny adres IP front-endu.

Platforma Azure rezerwuje również pięć adresów IP w każdej podsieci do użytku wewnętrznego: pierwsze cztery i ostatnie adresy IP. Rozważmy na przykład 15 wystąpień bramy aplikacji bez prywatnego adresu IP front-end. Potrzebujesz co najmniej 20 adresów IP dla tej podsieci: pięć do użytku wewnętrznego i 15 dla wystąpień bramy aplikacji. Tak, trzeba /27 rozmiar podsieci lub większy.

Należy wziąć podsieć, która ma 27 wystąpień bramy aplikacji i adres IP dla prywatnego adresu IP front-end. W takim przypadku potrzebujesz 33 adresów IP: 27 dla wystąpień bramy aplikacji, jeden dla prywatnego frontonia i pięć do użytku wewnętrznego. Tak, trzeba /26 rozmiar podsieci lub większy.

Zaleca się użycie rozmiaru podsieci co najmniej /28. Ten rozmiar zapewnia 11 użytecznych adresów IP. Jeśli obciążenie aplikacji wymaga więcej niż 10 wystąpień bramy aplikacji, należy wziąć pod uwagę /27 lub /26 rozmiar podsieci.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Sieciowe grupy zabezpieczeń w podsieci Bramy aplikacji

Sieciowe grupy zabezpieczeń są obsługiwane w bramie aplikacji. Ale istnieją pewne ograniczenia:

- Należy zezwolić na przychodzący ruch internetowy na portach TCP 65503-65534 dla jednostki SKU bramy aplikacji w wersji 1 i portów TCP 65200-65535 dla jednostki SKU w wersji 2 z podsiecią docelową jako **dowolny** i źródłowy jako tag usługi **GatewayManager.** Ten zakres portów jest wymagany do komunikacji z infrastrukturą platformy Azure. Te porty są chronione (zablokowane) przez certyfikaty platformy Azure. Jednostki zewnętrzne, w tym klienci tych bram, nie mogą komunikować się w tych punktach końcowych.

- Nie można zablokować wychodzącej łączności internetowej. Domyślne reguły ruchu wychodzącego w sieciowej sieciowej zezwalają na łączność z Internetem. Zalecamy wykonanie następujących czynności:

  - Nie usuwaj domyślnych reguł ruchu wychodzącego.
  - Nie należy tworzyć innych reguł ruchu wychodzącego, które odmawiają żadnej łączności wychodzącej.

- Ruch z **tagu AzureLoadBalancer** musi być dozwolony.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Zezwalaj na dostęp bramy aplikacji do kilku źródłowych serwerów IP

W tym scenariuszu należy użyć grup zabezpieczeń w podsieci bramy aplikacji. Umieść następujące ograniczenia w podsieci w następującej kolejności priorytetu:

1. Zezwalaj na ruch przychodzący ze źródłowego zakresu IP lub IP z miejscem docelowym jako całym zakresem adresu podsieci bramy aplikacji i portem docelowym jako portem dostępu przychodzącego, na przykład portem 80 dla dostępu HTTP.
2. Zezwalaj na przychodzące żądania ze źródła jako tag usługi **GatewayManager** i miejsce docelowe jako **porty dowolny** i docelowy jako 65503-65534 dla jednostki SKU bramy aplikacji w wersji 1 oraz porty 65200-65535 dla jednostki SKU w wersji 2 dla [komunikacji ze stanem kondycji zaplecza](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Ten zakres portów jest wymagany do komunikacji z infrastrukturą platformy Azure. Te porty są chronione (zablokowane) przez certyfikaty platformy Azure. Bez odpowiednich certyfikatów w miejscu, jednostki zewnętrzne nie można zainicjować zmiany w tych punktach końcowych.
3. Zezwalaj na przychodzące sondy równoważenia obciążenia platformy Azure (tag*AzureLoadBalancer)* i przychodzący ruch sieci wirtualnej (tag*VirtualNetwork)* w [sieciowej grupie zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Blokuj cały inny ruch przychodzący przy użyciu reguły odmów wszystkich.
5. Zezwalaj na ruch wychodzący do Internetu dla wszystkich miejsc docelowych.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Trasy zdefiniowane przez użytkownika obsługiwane w podsieci usługi Application Gateway

> [!IMPORTANT]
> Użycie UDR w podsieci bramy aplikacji może spowodować, że stan kondycji w [widoku kondycji zaplecza](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) będzie wyświetlany jako **Nieznany.** Może to również spowodować generowanie dzienników bramy aplikacji i metryki zakończyć się niepowodzeniem. Zaleca się, aby nie używać UDR w podsieci bramy aplikacji, dzięki czemu można wyświetlić kondycję zaplecza, dzienniki i metryki.

- **w wersji 1**

   Dla jednostki SKU w wersji 1 trasy zdefiniowane przez użytkownika (UDR) są obsługiwane w podsieci bramy aplikacji, o ile nie zmieniają one komunikacji żądania/odpowiedzi typu end-to-end. Na przykład można skonfigurować UDR w podsieci Bramy aplikacji, aby wskazać urządzenie zapory do inspekcji pakietów. Należy jednak upewnić się, że pakiet może dotrzeć do zamierzonego miejsca docelowego po inspekcji. Nie można tego zrobić może spowodować niepoprawne kondycji sondy lub ruchu routingu zachowanie. Obejmuje to trasy wyuczone lub domyślne trasy 0.0.0.0/0, które są propagowane przez usługi Azure ExpressRoute lub bramy sieci VPN w sieci wirtualnej.

- **v2**

   Dla jednostki SKU w wersji 2 istnieją obsługiwane i nieobsługiwały scenariuszy:

   **Obsługiwane scenariusze w wersji 2**
   > [!WARNING]
   > Nieprawidłowa konfiguracja tabeli trasy może spowodować routing asymetryczny w bramie aplikacji w wersji 2. Upewnij się, że cały ruch płaszczyzny zarządzania/kontroli jest wysyłany bezpośrednio do Internetu, a nie za pośrednictwem urządzenia wirtualnego. Może to również mieć wpływ na rejestrowanie i metryki.


  **Scenariusz 1:** UDR, aby wyłączyć propagację trasy protokołu bramy granicznej (BGP) do podsieci bramy aplikacji

   Czasami domyślna trasa bramy (0.0.0.0/0) jest anonsowana za pośrednictwem bram usługi ExpressRoute lub sieci VPN skojarzonych z siecią wirtualną bramy aplikacji. Spowoduje to przerwanie ruchu płaszczyzny zarządzania, co wymaga bezpośredniej ścieżki do Internetu. W takich scenariuszach UDR może służyć do wyłączania propagacji trasy BGP. 

   Aby wyłączyć propagację marszruty BGP, należy wykonać następujące czynności:

   1. Tworzenie zasobu tabeli marszruty na platformie Azure.
   2. Wyłącz parametr **propagacji bramy wirtualnej.** 
   3. Skojarz tabelę marszrut z odpowiednią podsiecią. 

   Włączenie UDR dla tego scenariusza nie należy przerywać żadnych istniejących ustawień.

  **Scenariusz 2**: UDR do bezpośredniego 0.0.0.0/0 do Internetu

   Można utworzyć UDR, aby wysłać ruch 0.0.0.0/0 bezpośrednio do Internetu. 

  **Scenariusz 3:** UDR dla usługi Azure Kubernetes

  Jeśli używasz kubenet z usługi Azure Kubernetes Service (AKS) i kontrolera transferu danych przychodzących bramy aplikacji (AGIC), należy skonfigurować tabelę tras, aby umożliwić ruch wysyłany do zasobników, które mają być kierowane do właściwego węzła. Nie będzie to konieczne, jeśli używasz usługi Azure CNI. 

   Aby skonfigurować tabelę tras, aby umożliwić pracę kubenetu, należy wykonać następujące czynności:

  1. Tworzenie zasobu tabeli marszruty na platformie Azure. 
  2. Po utworzeniu przejdź do strony **Trasy.** 
  3. Dodaj nową trasę:
     - Prefiks adresu powinien być zakresem ip zasobników, do których chcesz dotrzeć w uzywce AKS. 
     - Następnym typem przeskoku powinno być **urządzenie wirtualne**. 
     - Następny adres przeskoku powinien być adresem IP węzła obsługującego zasobników w zakresie IP zdefiniowanym w polu prefiksu adresu. 
    
  **Nieobsługiwały scenariuszy w wersji 2**

  **Scenariusz 1:** UDR dla urządzeń wirtualnych

  Każdy scenariusz, w którym 0.0.0.0/0 musi zostać przekierowany za pośrednictwem dowolnego urządzenia wirtualnego, sieci wirtualnej koncentratora/szprychy lub lokalnego (tunelowanie wymuszone) nie jest obsługiwany dla wersji 2.

## <a name="front-end-ip"></a>Adres IP front-endu

Bramę aplikacji można skonfigurować tak, aby miała publiczny adres IP, prywatny adres IP lub oba te elementy. Publiczny adres IP jest wymagany podczas hosta zaplecza, do którego klienci muszą uzyskać dostęp przez Internet za pośrednictwem wirtualnego adresu IP (VIP). 

Publiczny adres IP nie jest wymagany dla wewnętrznego punktu końcowego, który nie jest narażony na działanie Internetu. Jest to znane jako wewnętrzny punkt końcowy *równoważenia obciążenia* (ILB) lub prywatny adres IP frontendu. Równoważenie obciążenia sieciowego bramy aplikacji jest przydatne w przypadku wewnętrznych aplikacji biznesowych, które nie są udostępniane w Internecie. Jest to również przydatne dla usług i warstw w aplikacji wielowarstwowej w granicach zabezpieczeń, które nie są narażone na internet, ale które wymagają dystrybucji obciążenia okrężnego, lepkości sesji lub zakończenia TLS.

Obsługiwany jest tylko jeden publiczny adres IP lub jeden prywatny adres IP. Podczas tworzenia bramy aplikacji można wybrać adres IP frontu.

- W przypadku publicznego adresu IP można utworzyć nowy publiczny adres IP lub użyć istniejącego publicznego adresu IP w tej samej lokalizacji co brama aplikacji. Aby uzyskać więcej informacji, zobacz [statyczny a dynamiczny publiczny adres IP](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- W przypadku prywatnego adresu IP można określić prywatny adres IP z podsieci, w której tworzona jest brama aplikacji. Jeśli go nie określisz, dowolny adres IP jest automatycznie wybierany z podsieci. Nie można później zmienić wybranego typu adresu IP (statycznego lub dynamicznego). Aby uzyskać więcej informacji, zobacz [Tworzenie bramy aplikacji za pomocą wewnętrznego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Adres IP front-end jest skojarzony z *odbiornikiem,* który sprawdza, czy przychodzące żądania są adresami IP front-end.

## <a name="listeners"></a>Odbiorniki

Odbiornik jest jednostką logiczną, która sprawdza żądania połączenia przychodzącego przy użyciu portu, protokołu, hosta i adresu IP. Podczas konfigurowania odbiornika, należy wprowadzić wartości dla tych, które pasują do odpowiednich wartości w żądaniu przychodzącym na bramie.

Podczas tworzenia bramy aplikacji przy użyciu witryny Azure portal, można również utworzyć odbiornika domyślnego, wybierając protokół i port dla odbiornika. Można wybrać, czy włączyć obsługę PROTOKOŁU HTTP2 w odbiorniku. Po utworzeniu bramy aplikacji można edytować ustawienia tego domyślnego odbiornika *(appGatewayHttpListener)* lub utworzyć nowe detektory.

### <a name="listener-type"></a>Typ odbiornika

Podczas tworzenia nowego odbiornika należy wybrać między [ *programem basic* i *multi-site*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Jeśli chcesz, aby wszystkie żądania (dla dowolnej domeny) były akceptowane i przekazywane do pul zaplecza, wybierz podstawowe. Dowiedz [się, jak utworzyć bramę aplikacji za pomocą podstawowego odbiornika](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Jeśli chcesz przesyłać dalej żądania do różnych pul zaplecza na podstawie *nagłówka hosta* lub nazwy hosta, wybierz odbiornik wielu lokacji, gdzie należy również określić nazwę hosta, która jest zgodna z żądaniem przychodzącym. Dzieje się tak, ponieważ brama aplikacji opiera się na nagłówkach hosta HTTP 1.1, aby hostować więcej niż jedną witrynę sieci Web na tym samym publicznym adresie IP i porcie.

#### <a name="order-of-processing-listeners"></a>Kolejność przetwarzania słuchaczy

Dla jednostki SKU w wersji 1 żądania są dopasowywać zgodnie z kolejnością reguł i typem odbiornika. Jeśli reguła z podstawowym odbiornikiem jest na pierwszym miejscu w kolejności, jest przetwarzana jako pierwsza i zaakceptuje każde żądanie dla tej kombinacji portu i adresu IP. Aby tego uniknąć, należy najpierw skonfigurować reguły za pomocą detektorów wielu lokacji i wypchnąć regułę z odbiornikiem podstawowym do ostatniego na liście.

Dla jednostki SKU w wersji 2 detektory wielu lokacji są przetwarzane przed podstawowych odbiorników.

### <a name="front-end-ip"></a>Adres IP front-endu

Wybierz adres IP front-end, który ma być skojarzony z tym odbiornikiem. Odbiornik będzie słuchać przychodzących żądań na tym adresie IP.

### <a name="front-end-port"></a>Port front-end

Wybierz port front-end. Wybierz istniejący port lub utwórz nowy. Wybierz dowolną wartość z [dozwolonego zakresu portów](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Można używać nie tylko dobrze znanych portów, takich jak 80 i 443, ale każdego dozwolonego portu niestandardowego, który jest odpowiedni. Port może być używany dla słuchaczy publicznych lub odbiorników prywatnych.

### <a name="protocol"></a>Protocol (Protokół)

Wybierz http lub HTTPS:

- Jeśli wybierzesz http, ruch między klientem a bramą aplikacji jest niezaszyfrowany.

- Wybierz protokół HTTPS, jeśli chcesz, aby [szyfrowanie TLS było przerywane](features.md#secure-sockets-layer-ssltls-termination) lub [kompleksowe.](https://docs.microsoft.com/azure/application-gateway/ssl-overview) Ruch między klientem a bramą aplikacji jest szyfrowany. Połączenie TLS kończy się na bramie aplikacji. Jeśli chcesz szyfrowanie TLS end-to-end, należy wybrać protokół HTTPS i skonfigurować ustawienie **HTTP zaplecza.** Gwarantuje to, że ruch jest ponownie szyfrowany podczas przesuń z bramy aplikacji do zaplecza.


Aby skonfigurować szyfrowanie TLS i end-to-end TLS, należy dodać certyfikat do odbiornika, aby umożliwić bramie aplikacji uzyskanie klucza symetrycznego. Jest to podyktowane specyfikacją protokołu TLS. Klucz symetryczny służy do szyfrowania i odszyfrowywania ruchu wysyłanego do bramy. Certyfikat bramy musi być w formacie wymiany informacji osobistych (PFX). Ten format umożliwia eksportowanie klucza prywatnego używanego przez bramę do szyfrowania i odszyfrowywania ruchu.

#### <a name="supported-certificates"></a>Obsługiwane certyfikaty

Zobacz [certyfikaty obsługiwane dla zakończenia protokołu TLS](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Obsługa protokołu dodatkowego

#### <a name="http2-support"></a>Obsługa protokołu HTTP2

Obsługa protokołu HTTP/2 jest dostępna tylko dla klientów, którzy łączą się tylko z detektorami bramy aplikacji. Komunikacja z pulami serwerów zaplecza jest za pośrednictwem protokołu HTTP/1.1. Domyślnie obsługa protokołu HTTP/2 jest wyłączona. Poniższy fragment kodu programu Azure PowerShell pokazuje, jak włączyć tę funkcję:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Obsługa protokołu WebSocket

Obsługa WebSocket jest domyślnie włączona. Nie ma skonfigurowanego przez użytkownika ustawienia, aby je włączyć lub wyłączyć. Zestawy websockets można używać zarówno ze słuchaczami HTTP, jak i HTTPS.

### <a name="custom-error-pages"></a>Niestandardowe strony błędów

Można zdefiniować błąd niestandardowy na poziomie globalnym lub na poziomie odbiornika. Ale tworzenie stron błędów niestandardowych na poziomie globalnym z witryny Azure portal nie jest obecnie obsługiwane. Można skonfigurować niestandardową stronę błędu dla błędu zapory aplikacji sieci web 403 lub strony konserwacji 502 na poziomie odbiornika. Należy również określić publicznie dostępny adres URL obiektu blob dla danego kodu stanu błędu. Aby uzyskać więcej informacji, zobacz [Create Application Gateway custom error pages (Tworzenie niestandardowych stron błędów w usłudze Application Gateway)](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Kody błędów bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Aby skonfigurować globalną stronę niestandardowego błędu, zobacz [Konfiguracja programu Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="tls-policy"></a>Zasady protokołu TLS

Można scentralizować zarządzanie certyfikatami TLS/SSL i zmniejszyć obciążenie szyfrowania odszyfrowywania dla farmy serwerów zaplecza. Scentralna obsługa protokołu TLS umożliwia również określenie centralnej zasady TLS dostosowanej do wymagań zabezpieczeń. Można wybrać *domyślną,* *wstępnie zdefiniowaną*lub *niestandardową* zasadę TLS.

Zasady TLS można skonfigurować do sterowania wersjami protokołów TLS. Bramę aplikacji można skonfigurować tak, aby używała minimalnej wersji protokołu dla uzgadniania TLS z protokołów TLS1.0, TLS1.1 i TLS1.2. Domyślnie ssl 2.0 i 3.0 są wyłączone i nie można ich konfigurować. Aby uzyskać więcej informacji, zobacz [Omówienie zasad TLS bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Po utworzeniu odbiornika należy skojarzyć go z regułą routingu żądań. Ta reguła określa sposób, w jaki żądania, które są odbierane w odbiorniku są kierowane do zaplecza.

## <a name="request-routing-rules"></a>Żądania reguł routingu

Podczas tworzenia bramy aplikacji przy użyciu portalu Azure, należy utworzyć regułę domyślną (*reguła1*). Ta reguła wiąże domyślny odbiornik *(appGatewayHttpListener)* z domyślną pulą zaplecza *(appGatewayBackendPool)* i domyślnymi ustawieniami HTTP zaplecza *(appGatewayBackendHttpSettings*). Po utworzeniu bramy można edytować ustawienia reguły domyślnej lub utworzyć nowe reguły.

### <a name="rule-type"></a>Typ reguły

Podczas tworzenia reguły należy wybrać opcję [ *"podstawowa"* i *oparta na ścieżce*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Wybierz opcję podstawowe, jeśli chcesz przesłać dalej wszystkie żądania w skojarzonym odbiorniku (na przykład *blog<i></i>.contoso.com/\*)* do jednej puli zaplecza.
- Wybierz oparte na ścieżce, jeśli chcesz kierować żądania z określonych ścieżek adresów URL do określonych pul zaplecza. Wzorzec ścieżki jest stosowany tylko do ścieżki adresu URL, a nie do jego parametrów zapytania.

#### <a name="order-of-processing-rules"></a>Kolejność reguł przetwarzania

Dla jednostki SKU w wersji 1 dopasowanie wzorców przychodzących żądań jest przetwarzane w kolejności, w zależności od tego, w zależności od ścieżki ścieżki na mapie adresu URL reguły opartej na ścieżce. Jeśli żądanie pasuje do wzorca w dwóch lub więcej ścieżek na mapie ścieżki, ścieżka, która jest wymieniona jako pierwsza, jest dopasowywała. Żądanie jest przekazywane do zaplecza, który jest skojarzony z tą ścieżką.

Dla jednostki SKU w wersji 2 dokładne dopasowanie jest wyższy priorytet niż kolejność ścieżki na mapie ścieżki adresu URL. Jeśli żądanie pasuje do wzorca w dwóch lub więcej ścieżek, żądanie jest przekazywane do zaplecza, który jest skojarzony ze ścieżką, która dokładnie pasuje do żądania. Jeśli ścieżka w żądaniu przychodzącym nie jest dokładnie zgodna z dowolną ścieżką na mapie, dopasowanie wzorca żądania jest przetwarzane na liście kolejności mapy ścieżki dla reguły opartej na ścieżce.

### <a name="associated-listener"></a>Skojarzony odbiornik

Skojarz odbiornika z regułą, tak aby *reguła routingu żądania* skojarzona z odbiornikiem jest oceniana w celu określenia puli zaplecza, do których ma być kierowane żądanie.

### <a name="associated-back-end-pool"></a>Skojarzona pula zaplecza

Skojarz z regułą pulę zaplecza, która zawiera obiekty docelowe zaplecza, które obsługują żądania odbierane przez odbiornik.

 - W przypadku reguły podstawowej dozwolona jest tylko jedna pula zaplecza. Wszystkie żądania w skojarzonym odbiorniku są przekazywane do tej puli zaplecza.

 - W przypadku reguły opartej na ścieżce dodaj wiele pul zaplecza, które odpowiadają każdej ścieżce adresu URL. Żądania zgodne ze ścieżką adresu URL, która została wprowadzona, są przekazywane do odpowiedniej puli zaplecza. Ponadto dodaj domyślną pulę zaplecza. Żądania, które nie pasują do żadnej ścieżki adresu URL w regule, są przekazywane do tej puli.

### <a name="associated-back-end-http-setting"></a>Skojarzone ustawienie HTTP zaplecza

Dodaj ustawienie HTTP zaplecza dla każdej reguły. Żądania są kierowane z bramy aplikacji do obiektów docelowych zaplecza przy użyciu numeru portu, protokołu i innych informacji, które są określone w tym ustawieniu.

W przypadku reguły podstawowej dozwolone jest tylko jedno ustawienie HTTP zaplecza. Wszystkie żądania skojarzonego odbiornika są przekazywane do odpowiednich obiektów docelowych zaplecza przy użyciu tego ustawienia HTTP.

W przypadku reguły opartej na ścieżce dodaj wiele ustawień HTTP zaplecza, które odpowiadają każdej ścieżce adresu URL. Żądania zgodne ze ścieżką adresu URL w tym ustawieniu są przekazywane do odpowiednich obiektów docelowych zaplecza przy użyciu ustawień HTTP, które odpowiadają każdej ścieżce adresu URL. Ponadto dodaj domyślne ustawienie HTTP. Żądania, które nie pasują do żadnej ścieżki adresu URL w tej regule, są przekazywane do domyślnej puli zaplecza przy użyciu domyślnego ustawienia HTTP.

### <a name="redirection-setting"></a>Ustawienie przekierowania

Jeśli przekierowanie jest skonfigurowane dla podstawowej reguły, wszystkie żądania skojarzonego odbiornika są przekierowywane do obiektu docelowego. Jest to *globalne* przekierowanie. Jeśli przekierowanie jest skonfigurowane dla reguły opartej na ścieżce, tylko żądania w określonym obszarze witryny są przekierowywane. Przykładem jest obszar koszyka, który jest oznaczony przez */cart/\**. Jest to przekierowanie *oparte na ścieżce.*

Aby uzyskać więcej informacji na temat przekierowań, zobacz [Omówienie przekierowania bramy aplikacji](redirect-overview.md).

#### <a name="redirection-type"></a>Typ przekierowania

Wybierz typ wymaganego przekierowania: *Stałe(301),* *Tymczasowe(307)*, *Found(302)* lub *Zobacz inne(303)*.

#### <a name="redirection-target"></a>Cel przekierowania

Wybierz innego odbiornika lub witryny zewnętrznej jako miejsce docelowe przekierowania.

##### <a name="listener"></a>Odbiornik

Wybierz odbiornik jako miejsce docelowe przekierowania, aby przekierować ruch z jednego odbiornika do drugiego na bramie. To ustawienie jest wymagane, gdy chcesz włączyć przekierowanie http-to-HTTPS. Przekierowuje ruch z odbiornika źródłowego, który sprawdza, czy przychodzące żądania HTTP do odbiornika docelowego, który sprawdza przychodzące żądania HTTPS. Można również dołączyć ciąg zapytania i ścieżkę z oryginalnego żądania w żądaniu, który jest przekazywane do obiektu docelowego przekierowania.

![Okno dialogowe Składniki bramy aplikacji](./media/configuration-overview/configure-redirection.png)

Aby uzyskać więcej informacji na temat przekierowania http-to-HTTPS, zobacz:
- [Przekierowanie http-to-HTTPS przy użyciu portalu Azure](redirect-http-to-https-portal.md)
- [Przekierowanie http-to-HTTPS przy użyciu programu PowerShell](redirect-http-to-https-powershell.md)
- [Przekierowanie http-to-HTTPS przy użyciu interfejsu wiersza polecenia platformy Azure](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Strona zewnętrzna

Wybierz witrynę zewnętrzną, jeśli chcesz przekierować ruch w odbiorniku skojarzonym z tą regułą do witryny zewnętrznej. Można dołączyć ciąg zapytania z oryginalnego żądania w żądaniu, który jest przekazycony do obiektu docelowego przekierowania. Nie można przesłać dalej ścieżki do lokacji zewnętrznej, która znajdowała się w oryginalnym żądaniu.

Aby uzyskać więcej informacji na temat przekierowania, zobacz:
- [Przekierowywanie ruchu do witryny zewnętrznej przy użyciu programu PowerShell](redirect-external-site-powershell.md)
- [Przekierowywanie ruchu do witryny zewnętrznej przy użyciu interfejsu wiersza polecenia](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>Przepisywanie ustawienia nagłówka HTTP

To ustawienie dodaje, usuwa lub aktualizuje nagłówki żądań i odpowiedzi HTTP, podczas gdy pakiety żądań i odpowiedzi są przesunięcia między pulami klienta i zaplecza. Aby uzyskać więcej informacji, zobacz:

 - [Omówienie przepisywania nagłówków HTTP](rewrite-http-headers.md)
 - [Konfigurowanie ponownego przepisywania nagłówka HTTP](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>Ustawienia protokołu HTTP

Brama aplikacji kieruje ruch do serwerów zaplecza przy użyciu konfiguracji określonej w tym miejscu. Po utworzeniu ustawienia HTTP należy skojarzyć je z co najmniej jedną regułą routingu żądań.

### <a name="cookie-based-affinity"></a>Koligacja na podstawie pliku cookie

Usługa Azure Application Gateway używa plików cookie zarządzanych przez bramę do obsługi sesji użytkownika. Gdy użytkownik wysyła pierwsze żądanie do bramy aplikacji, ustawia plik cookie koligacji w odpowiedzi z wartością mieszania, która zawiera szczegóły sesji, dzięki czemu kolejne żądania zawierające plik cookie koligacji będą kierowane do tego samego serwera wewnętrznej bazy danych w celu zachowania lepkości. 

Ta funkcja jest przydatna, gdy chcesz zachować sesję użytkownika na tym samym serwerze i gdy stan sesji jest zapisywany lokalnie na serwerze dla sesji użytkownika. Jeśli aplikacja nie może obsłużyć koligacji opartej na plikach cookie, nie można użyć tej funkcji. Aby go użyć, upewnij się, że klienci obsługują pliki cookie.

Aktualizacja [chromei browser](https://www.chromium.org/Home) [v80](https://chromiumdash.appspot.com/schedule) przyniosła mandat, w którym pliki cookie HTTP bez atrybutu [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) muszą być traktowane jako SameSite=Lax. W przypadku żądań CORS (Cross-Origin Resource Sharing), jeśli plik cookie musi być wysyłany w kontekście strony trzeciej, musi używać *SameSite=None; Bezpieczne* atrybuty i powinny być wysyłane tylko za pośrednictwem protokołu HTTPS. W przeciwnym razie w scenariuszu tylko HTTP przeglądarka nie wysyła plików cookie w kontekście innej firmy. Celem tej aktualizacji z Chrome jest zwiększenie bezpieczeństwa i uniknięcie ataków csrf (Cross-Site Request Fałszerstwo). 

Aby wesprzeć tę zmianę, począwszy od 17 lutego 2020 r. Brama aplikacji (wszystkie typy jednostek SKU) wstrzyknie inny plik cookie o nazwie *ApplicationGatewayAffinityCORS* oprócz istniejącego pliku cookie *ApplicationGatewayAffinity.* Plik cookie *ApplicationGatewayAffinityCORS* ma dwa dodatkowe atrybuty dodane do niego (*"SameSite=None; Bezpieczne"*), dzięki czemu sesja lepka jest utrzymywana nawet w przypadku żądań cross-origin.

Należy zauważyć, że domyślna nazwa pliku cookie koligacji jest *ApplicationGatewayAffinity* i można go zmienić. W przypadku, gdy używasz niestandardowej nazwy pliku cookie koligacji, dodatkowy plik cookie jest dodawany z CORS jako sufiksem. Na przykład *CustomCookieNameCORS*.

> [!NOTE]
> Jeśli atrybut *SameSite=None* jest ustawiony, jest obowiązkowe, że plik cookie zawiera również *flagę Secure* i musi być wysyłany za pośrednictwem protokołu HTTPS.  Jeśli koligacja sesji jest wymagana za pośrednictwem mechanizmu CORS, należy przeprowadzić migrację obciążenia do protokołu HTTPS. W tym miejscu można zapoznać się z dokumentacją odciążania protokołu TLS i kompleksowej sieci TLS dla bramy aplikacji — [przegląd](ssl-overview.md), [Konfigurowanie bramy aplikacji z zakończeniem protokołu TLS przy użyciu portalu Azure](create-ssl-portal.md)portal , [Konfigurowanie kompleksowego protokołu TLS przy użyciu bramy aplikacji z portalem](end-to-end-ssl-portal.md).

### <a name="connection-draining"></a>Opróżnianie połączeń

Opróżnianie połączeń pomaga bezpiecznie usunąć członków puli zaplecza podczas planowanych aktualizacji usługi. To ustawienie można zastosować do wszystkich członków puli zaplecza podczas tworzenia reguł. Zapewnia, że wszystkie wystąpienia wyrejestrowania puli zaplecza w dalszym ciągu obsługiwać istniejące połączenia i obsługiwać bieżące żądania dla konfigurowalny limit czasu i nie otrzymują żadnych nowych żądań lub połączeń. Jedynym wyjątkiem są żądania związane z wyrejestrowywanie wystąpień z powodu koligacji sesji zarządzanej przez bramę i będą nadal przekazywane do wystąpień wyrejestrowania. Opróżnianie połączeń ma zastosowanie do wystąpień zaplecza, które są jawnie usuwane z puli zaplecza.

### <a name="protocol"></a>Protocol (Protokół)

Brama aplikacji obsługuje zarówno protokół HTTP, jak i protokół HTTPS dla wysyłania żądań do serwerów zaplecza. Jeśli wybierzesz protokół HTTP, ruch na serwerach zaplecza będzie niezaszyfrowany. Jeśli niezaszyfrowana komunikacja jest nie dopuszczalna, wybierz https.

To ustawienie w połączeniu z protokołem HTTPS w odbiorniku obsługuje [end-to-end TLS](ssl-overview.md). Dzięki temu można bezpiecznie przesyłać poufne dane zaszyfrowane do zaplecza. Każdy serwer zaplecza w puli zaplecza z włączoną funkcją TLS typu end-to-end musi być skonfigurowany z certyfikatem umożliwiającym bezpieczną komunikację.

### <a name="port"></a>Port

To ustawienie określa port, na którym serwery zaplecza nasłuchuje ruchu z bramy aplikacji. Można skonfigurować porty od 1 do 65535.

### <a name="request-timeout"></a>Limit czasu żądania

To ustawienie jest liczbą sekund oczekiwania bramy aplikacji na otrzymanie odpowiedzi z serwera zaplecza.

### <a name="override-back-end-path"></a>Zastępowanie ścieżki zaplecza

To ustawienie umożliwia skonfigurowanie opcjonalnej niestandardowej ścieżki przekazywania do użycia, gdy żądanie jest przekazywane do zaplecza. Każda część ścieżki przychodzącej, która pasuje do ścieżki niestandardowej w polu **ścieżka wewnętrznej bazy danych zastąpienia,** jest kopiowana do ścieżki przekazywanej dalej. W poniższej tabeli przedstawiono, jak działa ta funkcja:

- Gdy ustawienie HTTP jest dołączone do podstawowej reguły routingu żądań:

  | Oryginalne żądanie  | Zastępowanie ścieżki zaplecza | Żądanie przekazane do zaplecza |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- Gdy ustawienie HTTP jest dołączone do reguły routingu żądań opartych na ścieżce:

  | Oryginalne żądanie           | Reguła ścieżki       | Zastępowanie ścieżki zaplecza | Żądanie przekazane do zaplecza |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /zasady ścieżki*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /zasady ścieżki*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /zasady ścieżki*      | /override/            | /override/home/              |
  | /home/secondhome/          | /zasady ścieżki*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | /override/            | /override/                   |

### <a name="use-for-app-service"></a>Użyj do usługi aplikacji

Jest to skrót tylko interfejsu użytkownika, który wybiera dwa wymagane ustawienia dla zaplecza usługi Azure App Service. Umożliwia **wybranie nazwy hosta z adresu zaplecza**i tworzy nową niestandardową sondę, jeśli jeszcze jej nie masz. (Aby uzyskać więcej informacji, zobacz [sekcję Wybierania nazwy hosta z](#pick) sekcji ustawień adresu zaplecza w tym artykule). Zostanie utworzona nowa sonda, a nagłówek sondy jest pobierany z adresu elementu członkowskiego zaplecza.

### <a name="use-custom-probe"></a>Użyj sondy niestandardowej

To ustawienie kojarzy [sondę niestandardową](application-gateway-probe-overview.md#custom-health-probe) z ustawieniem HTTP. Można skojarzyć tylko jedną niestandardową sondę z ustawieniem HTTP. Jeśli nie jawnie skojarzyć sondy niestandardowej, [domyślna sonda](application-gateway-probe-overview.md#default-health-probe-settings) jest używana do monitorowania kondycji zaplecza. Zaleca się utworzenie niestandardowej sondy w celu zwiększenia kontroli nad monitorowaniem kondycji zaplecza.

> [!NOTE]
> Sonda niestandardowa nie monitoruje kondycji puli zaplecza, chyba że odpowiednie ustawienie HTTP jest jawnie skojarzone z odbiornikiem.

### <a name="pick-host-name-from-back-end-address"></a><a id="pick"/></a>Wybieranie nazwy hosta z adresu zaplecza

Ta funkcja dynamicznie ustawia nagłówek *hosta* w żądaniu na nazwę hosta puli zaplecza. Używa adresu IP lub FQDN.

Ta funkcja pomaga, gdy nazwa domeny zaplecza różni się od nazwy DNS bramy aplikacji, a zaplecze opiera się na określonym nagłówku hosta, aby rozpoznać do prawidłowego punktu końcowego.

Przykładowy przypadek jest usługi wielodostępne jako zaplecza. Usługa aplikacji to usługa wielodostępna, która używa udostępnionego miejsca o jednym adresie IP. Dzięki temu usługa aplikacji jest dostępna tylko za pośrednictwem nazw hostów skonfigurowanych w niestandardowych ustawieniach domeny.

Domyślnie niestandardowa nazwa domeny jest *example.azurewebsites.net*. Aby uzyskać dostęp do usługi aplikacji przy użyciu bramy aplikacji za pośrednictwem nazwy hosta, która nie jest jawnie zarejestrowana w usłudze aplikacji lub za pośrednictwem nazwy FQDN bramy aplikacji, należy zastąpić nazwa hosta w oryginalnym żądaniu do nazwy hosta usługi aplikacji. Aby to zrobić, należy włączyć nazwę hosta pobrania z ustawienia **adresu wewnętrznej bazy danych.**

W przypadku domeny niestandardowej, której istniejąca niestandardowa nazwa DNS jest mapowana na usługę aplikacji, nie trzeba włączać tego ustawienia.

> [!NOTE]
> To ustawienie nie jest wymagane dla środowiska usługi app service, które jest wdrożenie dedykowane.

### <a name="host-name-override"></a>Zastępowanie nazwy hosta

Ta funkcja zastępuje nagłówek *hosta* w żądaniu przychodzącym w bramie aplikacji o określonej nazwie hosta.

Na przykład jeśli *www.contoso.com* jest określony w ustawieniu **nazwa hosta,** oryginalne żądanie *`https://appgw.eastus.cloudapp.azure.com/path1` zostanie zmienione na *`https://www.contoso.com/path1` gdy żądanie jest przekazywane do serwera zaplecza.

## <a name="back-end-pool"></a>Pula zaplecza

Pulę zaplecza można skierować na cztery typy elementów członkowskich zaplecza: określoną maszynę wirtualną, zestaw skalowania maszyny wirtualnej, adres IP/FQDN lub usługę aplikacji. 

Po utworzeniu puli zaplecza należy skojarzyć ją z co najmniej jedną regułą routingu żądań. Należy również skonfigurować sondy kondycji dla każdej puli zaplecza w bramie aplikacji. Po spełnieniu warunku reguły routingu żądania brama aplikacji przekazuje ruch do serwerów w dobrej kondycji (zgodnie z ustaleniami sond kondycji) w odpowiedniej puli zaplecza.

## <a name="health-probes"></a>Sondy kondycji

Brama aplikacji domyślnie monitoruje kondycję wszystkich zasobów w zapleczu. Zdecydowanie zaleca się jednak utworzenie niestandardowej sondy dla każdego ustawienia HTTP zaplecza, aby uzyskać większą kontrolę nad monitorowaniem kondycji. Aby dowiedzieć się, jak skonfigurować sondę niestandardową, zobacz [Niestandardowe ustawienia sondy kondycji](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Po utworzeniu niestandardowej sondy kondycji należy skojarzyć ją z ustawieniem HTTP zaplecza. Sonda niestandardowa nie będzie monitorować kondycji puli zaplecza, chyba że odpowiednie ustawienie HTTP jest jawnie skojarzone z odbiornikiem przy użyciu reguły.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz o składnikach bramy aplikacji, możesz:

- [Tworzenie bramy aplikacji w witrynie Azure portal](quick-create-portal.md)
- [Tworzenie bramy aplikacji przy użyciu programu PowerShell](quick-create-powershell.md)
- [Tworzenie bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)
