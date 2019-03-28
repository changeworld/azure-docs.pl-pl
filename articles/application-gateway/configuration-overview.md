---
title: Omówienie konfiguracji usługi Azure Application Gateway
description: Ten artykuł zawiera informacje dotyczące sposobu konfigurowania różnych składników w usłudze Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: absha
ms.openlocfilehash: ca4f9bf00d70f327ff756558e25315762a9a77a8
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519752"
---
# <a name="application-gateway-configuration-overview"></a>Omówienie konfiguracji bramy aplikacji

Usługa Application gateway składa się z kilku składników, które można skonfigurować na różne sposoby wykonywania różnych scenariuszy. W tym artykule przedstawiono sposób każdy składnik ma zostać skonfigurowany.

![application-gateway-components](./media/configuration-overview/configuration-overview1.png)

Na powyższej ilustracji przykład przedstawia konfiguracji aplikacji z 3 odbiorników. Pierwsze dwa są odbiorników obejmujących wiele lokacji dla `http://acme.com/*` i `http://fabrikam.com/*`, odpowiednio. Oba są nasłuchuje na porcie 80. Trzeci odbiornik jest podstawowy odbiornik mający kończenia żądań SSL typu end to end. 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Usługa Azure virtual network i dedykowaną podsieć

Usługa Application gateway to dedykowane wdrożenie w sieci wirtualnej. W ramach sieci wirtualnej dedykowaną podsieć jest wymagana dla bramy application gateway. W tej podsieci, może mieć wielu wystąpień wdrożenia bramy danej aplikacji. Można także wdrożyć inne bramy aplikacji należących do podsieci, ale nie można wdrożyć innych zasobów w podsieci bramy aplikacji.  

> [!NOTE]   
> Mieszanie Standard_v2 i standardowa usługa Application Gateway w tej samej podsieci nie jest obsługiwane.

#### <a name="size-of-the-subnet"></a>Rozmiar podsieci

Bramy Application Gateway zużywa jeden prywatny adres IP dla każdego wystąpienia, a także innym prywatnym adresem IP, jeśli konfiguracji IP frontonu prywatnego jest skonfigurowany. Ponadto platforma Azure rezerwuje 5 adresów IP — pierwsze cztery ostatnie IP adresów i — w każdej podsieci, do wewnętrznego użycia. Na przykład jeśli bramy aplikacji jest równa 15 wystąpień i nie adresu IP frontonu prywatnych, następnie co najmniej 20 adresów IP będzie wymagane w podsieci - pięciu adresów IP do wewnętrznego użycia i 15 adresów IP dla 15 wystąpień bramy application gateway. W związku z tym, w tym przypadku/27 podsieć, rozmiar lub nowszej jest wymagana. Jeśli masz 27 wystąpień i adresów IP dla prywatnych frontonu Konfiguracja protokołu IP, a następnie 33 adresów IP będzie wymagana - 27 adresów IP dla 27 wystąpień bramy application Gateway jeden adres IP dla adresu IP frontonu prywatne i pięć IP adresów dla wewnętrznego użycia. W związku z tym, w tym przypadku /26 podsieć, rozmiar lub nowszej jest wymagana.

Zalecane jest użycie co najmniej o rozmiarze/28 rozmiar podsieci. Dzięki temu 11 brakować adresów. Jeśli Twoje obciążenia aplikacji wymaga więcej niż 10 wystąpień, należy rozważyć/27 lub/26 rozmiar podsieci.

#### <a name="network-security-groups-supported-on-the-application-gateway-subnet"></a>Sieciowe grupy zabezpieczeń obsługiwane w podsieci bramy aplikacji

Sieciowe grupy zabezpieczeń (NSG) są obsługiwane w podsieci bramy aplikacji z następującymi zastrzeżeniami: 

- Wyjątki musi być włączony dla ruchu przychodzącego na portach 65503 65534 w usłudze Application Gateway v1 jednostki SKU i porty 65200 – 65535 dla jednostki SKU w wersji 2. Zakres portów jest wymagany do komunikacji infrastruktury platformy Azure. Są one zabezpieczone (zablokowane) z użyciem certyfikatów Azure. Bez prawidłowych certyfikatów podmioty zewnętrzne, w tym klienci tych bram, nie będą mogli zainicjować wszelkie zmiany w tych punktach końcowych.

- Nie można zablokować wychodzące połączenie z Internetem. Domyślne reguły ruchu wychodzącego w sieciowej grupie zabezpieczeń umożliwiają już połączenie z Internetem. Firma Microsoft zaleca, nie usuwaj domyślne reguły ruchu wychodzącego i że nie utworzono inne reguły ruchu wychodzącego, które odmawiają wychodzące połączenie z Internetem.

- Ruch z AzureLoadBalancer tag musi być zablokowany.

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>Lista dozwolonych Application Gateway dostęp do kilku źródłowych adresów IP

W tym scenariuszu można zrobić przy użyciu sieciowych grup zabezpieczeń w podsieci bramy aplikacji. Następujące ograniczenia powinny znajdować się w podsieci w określonej kolejności priorytetu:

1. Zezwalaj na ruch przychodzący ze źródłowego zakresu adresów IP/adresu IP.
2. Zezwalaj na przychodzące żądania pochodzące ze wszystkich źródeł porty 65503 65534 [komunikacji kondycji zaplecza](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Zakres tego portu jest wymagany do komunikacji infrastruktury platformy Azure. Są one zabezpieczone (zablokowane) z użyciem certyfikatów Azure. Bez prawidłowych certyfikatów podmioty zewnętrzne, w tym klienci tych bram, nie będzie można zainicjować wszelkie zmiany w tych punktach końcowych.
3. Zezwalaj na przychodzące sondy usługi Azure Load Balancer (AzureLoadBalancer tag) oraz dla ruchu przychodzącego ruchu w sieci wirtualnej (znacznik VirtualNetwork) na [sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Zablokuj wszystkie inne ruch przychodzący z odmowy wszystkie reguły.
5. Zezwalaj na ruch wychodzący do Internetu dla wszystkich miejsc docelowych.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Trasy zdefiniowane przez użytkownika, obsługiwane w podsieci bramy aplikacji

W przypadku jednostki SKU w wersji 1 i trasy zdefiniowane przez użytkownika (Udr) są obsługiwane w podsieci bramy aplikacji, tak długo, jak nie wpływają one komunikacji end-to-end żądania/odpowiedzi. Na przykład można skonfigurować trasy zdefiniowanej przez użytkownika w podsieci bramy aplikacji, aby wskazywał urządzenie zapory dla inspekcję pakietów, ale należy upewnić się, że pakiet może osiągnąć jego przeznaczenia wpis inspekcji. Niewykonanie tej czynności może spowodować nieprawidłowe kondycji sondy lub ruch zachowania routingu. Obejmuje to zapamiętane trasy lub domyślne trasy 0.0.0.0/0 propagowane przez usługi ExpressRoute lub bram sieci VPN w sieci wirtualnej.

W przypadku v2 jednostki SKU, tras zdefiniowanych przez użytkownika w podsieci bramy aplikacji nie są obsługiwane. Aby uzyskać więcej informacji, zobacz [Skalowanie automatyczne i strefowo nadmiarowe Application Gateway (publiczna wersja zapoznawcza)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations).

> [!NOTE]
> Przy użyciu tras zdefiniowanych przez użytkownika w podsieci bramy aplikacji spowoduje, że stan kondycji w [widoku kondycji zaplecza](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) mają być wyświetlane jako **nieznany** i spowoduje również failue generowania dzienników usługi application gateway i metryki. Zalecane jest, że nie używasz tras zdefiniowanych przez użytkownika w podsieci bramy aplikacji można wyświetlić kondycję wewnętrznej bazy danych, dzienników i metryk.

## <a name="frontend-ip"></a>Adresu IP frontonu

Można skonfigurować bramę aplikacji, albo mieć publiczny adres IP lub prywatnego adresu IP lub obu. Publiczny adres IP jest wymagany, gdy obsługujesz wewnętrznej bazy danych, która jest wymagana, aby były dostępne dla klientów za pośrednictwem sieci internet za pośrednictwem adresu VIP na dostępnym z Internetu. Publiczny adres IP nie jest wymagana dla wewnętrznego punktu końcowego, który nie jest połączone z Internetem, znany także jako punkt końcowy wewnętrznego obciążenia balancer (ILB). Konfigurowanie bramy przy użyciu wewnętrznego modułu równoważenia obciążenia jest pomocne w przypadku wewnętrznych aplikacji LOB niepołączonych z Internetem. Ta opcja jest również przydatna w przypadku usług i warstw w aplikacji wielowarstwowej, która znajduje się w granicach zabezpieczeń bez połączenia z Internetem, ale nadal wymaga dystrybucji obciążenia z działaniem okrężnym, lepkości sesji lub zakończenia protokołu SSL (Secure Sockets Layer).

Obsługiwane jest tylko jeden publiczny adres IP lub jeden prywatny adres IP. Podczas tworzenia bramy aplikacji możesz wybrać adresu IP frontonu. 

- W przypadku publicznego adresu IP można utworzyć nowy publiczny adres IP lub użyć istniejącego publicznego adresu IP w tej samej lokalizacji co usługa Application Gateway. Jeśli tworzysz nowy publiczny adres IP, nie można później zmienić wybrany typ adresu IP (statyczne lub dynamiczne). Aby uzyskać więcej informacji, zobacz [statycznej dynamicznym publicznym adresem IP](https://docs.microsoft.com/azure/application-gateway/application-gateway-components) 

- W przypadku prywatny adres IP można określić prywatny adres IP z podsieci, w którym zostanie utworzona brama aplikacji. Jeśli nie zostanie określony jawnie, dowolnego adresu IP zostanie automatycznie wybrana z podsieci. Aby uzyskać więcej informacji, zobacz [utworzyć bramę aplikacji z punktem końcowym usługi wewnętrznego load balancer (ILB).](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)

IP frontonu jest skojarzona z *odbiornika* która sprawdza, czy żądania przychodzące na adresu IP frontonu.

## <a name="listeners"></a>Odbiorniki

Odbiornik jest jednostką logiczną, która sprawdza, czy żądania przychodzące połączenia przy użyciu portu, protokołu, hosta i adresu IP. W związku z tym, podczas konfigurowania odbiornika, musisz podać te wartości portu, protokołu, hosta i adresów IP który są takie same jak odpowiednie wartości w żądaniu przychodzącym w bramie. Podczas tworzenia bramy aplikacji przy użyciu witryny Azure portal, również utworzyć odbiornik, domyślne, wybierając protokół i port odbiornika. Ponadto można wybrać, czy chcesz włączyć obsługę protokołu HTTP2 na odbiornik. Po utworzeniu bramy aplikacji można edytować ustawienia tego odbiornika domyślne (*appGatewayHttpListener*/*appGatewayHttpsListener*) i/lub utworzyć nowy odbiorników.

### <a name="listener-type"></a>Typ odbiornika

Możesz wybrać między [odbiornika podstawowego lub połączenia obejmujące wiele lokacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners) podczas tworzenia nowego odbiornika. 

- Hostujesz jednej witryny za bramą aplikacji, jeśli podstawowy odbiornik. Dowiedz się, [jak utworzyć bramę aplikacji przy użyciu podstawowego odbiornika](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Jeśli konfigurujesz więcej niż jednej aplikacji sieci web lub wieloma poddomenami tej samej domeny nadrzędnej na tym samym wystąpieniu bramy aplikacji, wybierz odbiornika wielu lokacji. Dla odbiornika wielu lokacji należy dodatkowo wprowadź nazwę hosta. Jest to spowodowane Application Gateway bazuje na nagłówkach hosta HTTP 1.1 do obsługi więcej niż jednej witryny sieci Web na tym samym publicznym adresem IP i porcie.

#### <a name="order-of-processing-listeners"></a>Kolejność przetwarzania odbiorniki

W przypadku jednostek SKU v1 odbiorniki są przetwarzane w kolejności, w której są wyświetlane. Dlatego jeśli podstawowy odbiornik pasuje do przychodzącego żądania przetwarza je najpierw. W związku z tym należy skonfigurować odbiorników obejmujących wiele lokacji przed odbiornika podstawowego, aby upewnić się, że ruch jest kierowany do poprawne zaplecza.

W przypadku jednostek SKU v2 odbiorników obejmujących wiele lokacji są przetwarzane przed podstawowe odbiorników.

### <a name="frontend-ip"></a>Adresu IP frontonu

Wybierz adres IP frontonu, który chcesz skojarzyć z tego odbiornika. Odbiornik będzie nasłuchiwać na żądanie przychodzące na ten adres IP.

### <a name="frontend-port"></a>Port frontonu

Wybierz port frontonu. Można wybrać istniejący portów lub Utwórz nową. Możesz wybrać dowolną wartość z [dozwolony zakres portów](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Dzięki temu można nie tylko korzystać z dobrze znanych portów, np. 80 i 443, ale dowolne dozwolone numery portów nadaje się do Twojego użytku. Jeden port albo może służyć do publicznego odbiorników umożliwiający dostęp do Internetu lub prywatnej odbiorników umożliwiający dostęp do Internetu.

### <a name="protocol"></a>Protokół

Należy wybrać protokół HTTP i HTTPS. 

- Jeśli wybierzesz HTTP, ruch między bramy klienta i aplikacji będą przepływać niezaszyfrowane.

- Wybierz protokół HTTPS, jeśli interesują Cię [kończenie żądań protokołu Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/overview) lub [kompleksowe szyfrowanie SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Jeśli wybierzesz HTTPS, ruch między bramy klienta i aplikacji zostaną zaszyfrowane i połączenia SSL zostanie zakończony w usłudze application gateway.  Chcąc kompleksowe szyfrowanie SSL, ponadto należy wybrać protokół HTTPS podczas konfigurowania *ustawienia zaplecza HTTP*. Pozwoli to zagwarantować, że ruch ponownie szyfrowane podczas przesyłania z bramy aplikacji z zapleczem usługi.

  Aby skonfigurować kończenie żądań protokołu Secure Sockets Layer (SSL) i kompleksowe szyfrowanie SSL, certyfikat jest wymagany do dodania do odbiornika tak, aby umożliwić Application Gateway do wyprowadzenia klucza symetrycznego zgodnie z specyfikacją protokołu SSL. Klucz symetryczny jest następnie używany do szyfrowania i odszyfrowywania ruch wysyłany do bramy. Certyfikat bramy musi mieć format wymiany informacji osobistych (PFX). Ten format pliku umożliwia eksportowanie klucza prywatnego, wymagane przez tę bramę aplikacji z realizacją szyfrowania i odszyfrowywania ruchu. 

#### <a name="supported-certificates"></a>Obsługiwane certyfikatów

Zobacz [certyfikatów obsługiwana w przypadku kończenia żądań SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Obsługa dodatkowych protokołów

#### <a name="http2-support"></a>Obsługa protokołu HTTP2

Obsługa protokołu HTTP/2 jest dostępna dla klientów łączących się tylko odbiorników bramy aplikacji. Komunikacja do pul serwerów zaplecza jest za pośrednictwem protokołu HTTP/1.1. Domyślnie obsługa protokołu HTTP/2 jest wyłączona. Poniższy przykład fragmentu kodu programu Azure PowerShell pokazuje, jak możesz je włączyć:

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Obsługa protokołu Websocket

Obsługa protokołu Websocket jest domyślnie włączona. Nie ma żadnych ustawień konfigurowanych przez użytkownika umożliwiających selektywne włączenie lub wyłączenie obsługi protokołu WebSocket. Za pomocą funkcji WebSockets odbiorników HTTP i HTTPS. 

### <a name="custom-error-page"></a>Strona błędu niestandardowego

Strony błędów niestandardowych można definiować na poziomie globalnym, a także poziomu odbiornika, jednak tworzenie globalnych poziomu błędów niestandardowych stron w witrynie Azure portal nie jest obecnie obsługiwane. Niestandardowej strony błędu 403 błędu zapory aplikacji sieci Web lub 502 strony konserwacji można skonfigurować na poziomie odbiornika. Należy również określić adres URL dostępny publicznie obiektu blob dla kodu stanu danego błędu. Aby uzyskać więcej informacji, zobacz [Tworzenie niestandardowej strony błędu](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Kody błędów bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Aby skonfigurować globalne niestandardowej strony błędu, należy użyć [programu Azure PowerShell dla konfiguracji](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration) 

### <a name="ssl-policy"></a>Zasady SSL

Można scentralizować zarządzanie certyfikatami SSL i zmniejszyć koszty szyfrowania i odszyfrowywania z farmy serwerów zaplecza. Scentralizowane Obsługa również SSL pozwala określić centralne zasady protokołu SSL, który jest odpowiedni do określonych wymagań dotyczących zabezpieczeń w organizacji.  Można wybrać ustawienie domyślne, które są wstępnie zdefiniowane i niestandardowe zasady protokołu SSL. 

Można skonfigurować zasady protokołu SSL w celu kontrolowania wersji protokołu SSL. Można skonfigurować bramę aplikacji do odmowy TLS1.0 TLS1.1 i TLS1.2. Protokół SSL 2.0 i 3.0 już są domyślnie wyłączone i nie można konfigurować. Aby uzyskać więcej informacji, zobacz [Przegląd zasad protokołu SSL bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Po utworzeniu odbiornika, należy ją skojarzyć z reguły routingu żądania, który określa, jak żądanie odebrane na odbiornik ma być kierowany do wewnętrznej bazy danych.

## <a name="request-routing-rule"></a>Reguła routingu żądania

Podczas tworzenia bramy aplikacji przy użyciu witryny Azure portal, należy utworzyć regułę domyślną (*rule1*), który wiąże odbiornik z domyślnego (*appGatewayHttpListener*) za pomocą domyślnej puli zaplecza (*appGatewayBackendPool*) i domyślne ustawienia HTTP zaplecza (*appGatewayBackendHttpSettings*). Po utworzeniu bramy application gateway można edytować ustawienia tej reguły domyślne lub utworzyć nowe reguły.

### <a name="rule-type"></a>Typ reguły

Możesz wybrać między [zasada podstawowa lub opartego na ścieżkach](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule) podczas tworzenia nowej reguły. 

- Jeśli chcesz przesłać dalej wszystkie żądania na skojarzony odbiornik (np: blog.contoso.com/*) do puli zaplecza pojedynczej, wybierz podstawowy odbiornik. 
- Wybierz opartego na ścieżkach odbiornik, aby kierować żądania przy użyciu określonej ścieżki adresu URL do pul zaplecza określone. Wzorzec ścieżki jest stosowane tylko do ścieżki adresu URL, z jego parametrów zapytania.


#### <a name="order-of-processing-rules"></a>Kolejność przetwarzania reguły

W przypadku jednostek SKU v1 dopasowania wzorca żądania przychodzące są przetwarzane w kolejności, w którym ścieżki są wymienione w Mapa ścieżki adresu URL opartego na ścieżkach reguły. Z tego powodu jeśli żądanie jest zgodny ze wzorcem w dwóch lub więcej ścieżek w Mapa ścieżki adresu URL, następnie ścieżkę, która znajduje się najpierw zostaną dopasowane, a zostanie ona przesłana do wewnętrznej bazy danych skojarzonych z tej ścieżki.

W przypadku jednostek SKU v2 dokładne dopasowanie przechowuje wyższy priorytet nad kolejności, w którym ścieżki są wymienione w Mapa ścieżki adresu URL. W tym przyczynę, jeśli żądanie pasuje do wzorca w co najmniej dwóch ścieżek, a następnie zostanie ona przesłana do wewnętrznej bazy danych skojarzone z tej ścieżki, która jest zgodna dokładnie z żądaniem. Jeśli ścieżka w żądaniu przychodzącym nie jest dokładnie dopasowana dowolną ścieżkę na mapie ścieżki adresu URL, następnie dopasowania wzorca przychodzące żądania są przetwarzane w kolejności, w którym ścieżki są wymienione w Mapa ścieżki adresu URL opartego na ścieżkach reguły.

### <a name="associated-listener"></a>Skojarzony odbiornik

Należy skojarzyć odbiornika do reguły, aby *reguła routingu żądania* skojarzone z *odbiornika* jest oceniany w celu określenia *puli zaplecza* do której żądanie jest kierowane.

### <a name="associated-backend-pool"></a>Skojarzone puli wewnętrznej bazy danych

Kojarzenie puli zaplecza, zawierająca elementy docelowe wewnętrznej bazy danych, które będą obsługiwać żądań odebranych przez odbiornik. W przypadku podstawową regułę tylko jedną pulę zaplecza jest dozwolone, ponieważ wszystkie żądania na skojarzony odbiornik, które zostaną przekazane do tej puli zaplecza. W przypadku reguły opartego na ścieżkach należy dodać wiele pul zaplecza odpowiadający każdej ścieżki adresu URL. Żądania, które zgodna ze ścieżką URL wprowadzone w tym miejscu, będzie przekazywany do odpowiedniej puli zaplecza. Ponadto należy dodać domyślna pula zaplecza, ponieważ żądania, które nie są zgodne z dowolną ścieżkę adresu URL wprowadzony w tej regule zostaną przekazane do niego.

### <a name="associated-backend-http-setting"></a>Ustawienia HTTP zaplecza skojarzone

Dodaj ustawienia HTTP zaplecza, dla każdej reguły. Żądania będą kierowane z bramy aplikacji do celów wewnętrznej bazy danych przy użyciu numeru portu, protokołu i inne ustawienia określone w tym ustawieniu. W przypadku podstawową regułę tylko jedno ustawienie protokołu HTTP zaplecza jest dozwolone, ponieważ wszystkie żądania na skojarzony odbiornik, które zostaną przekazane do odpowiednich elementów docelowych wewnętrznej bazy danych przy użyciu tego ustawienia protokołu HTTP. W przypadku reguły opartego na ścieżkach należy dodać wiele ustawień HTTP zaplecza odpowiadający każdej ścieżki adresu URL. Żądania, które zgodna ze ścieżką URL wprowadzone w tym miejscu, zostanie przesłana do odpowiedniego celów wewnętrznej bazy danych przy użyciu ustawień HTTP odpowiadający każdej ścieżki adresu URL. Ponadto należy dodać domyślne ustawienie protokołu HTTP, ponieważ żądania, które nie są zgodne z dowolną ścieżkę adresu URL wprowadzony w tej regule zostaną przekazane do domyślna pula zaplecza przy użyciu domyślnego ustawienia protokołu HTTP.

### <a name="redirection-setting"></a>Ustawienie przekierowywania

Jeśli skonfigurowano przekierowania podstawową regułę, wszystkie żądania na skojarzony odbiornik nastąpi przekierowanie do obiektu docelowego przekierowania, umożliwiając w ten sposób przekierowywania globalnego. Skonfigurowanie przekierowania w przypadku opartego na ścieżkach reguły żądania tylko w obszarze określonej lokacji, na przykład koszyka obszaru wskazywane przez/koszyka / *, nastąpi przekierowanie do obiektu docelowego przekierowania, umożliwiając w ten sposób przekierowywania opartego na ścieżkach. 

Aby uzyskać informacji na temat możliwości przekierowania, zobacz [omówienie przekierowania](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

- #### <a name="redirection-type"></a>Typ przekierowania

  Wybierz typ przekierowania wymaganej od: Other(303) Permanent(301), Temporary(307), Found(302) lub zobacz.

- #### <a name="redirection-target"></a>Miejsce docelowe przekierowania

  Możesz wybrać inny odbiornik lub zewnętrznej witryny jako miejsce docelowe przekierowania. 

  - ##### <a name="listener"></a>Odbiornik

    Wybranie pozycji odbiornika jako obiektu docelowego przekierowania ułatwia przekierowywanie z jednego odbiornika do innego odbiornika na bramie. To ustawienie jest wymagane, jeśli chcesz włączyć protokół HTTP do przekierowania protokołu HTTPS, czyli przekierowywanie ruchu z odbiornika źródła sprawdzania pod kątem przychodzących żądań HTTP do odbiornika docelowego sprawdzania pod kątem przychodzących żądań HTTPS. Możesz również ciąg zapytania i ścieżki w oryginalne żądanie, które mają zostać uwzględnione w żądaniu przekazywane do obiektu docelowego przekierowania.![application-gateway-components](./media/configuration-overview/configure-redirection.png)

    Aby uzyskać więcej informacji na temat protokołu HTTP do przekierowania protokołu HTTPS, zobacz [przekierowania HTTP lub HTTP przy użyciu portalu](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal), [przekierowania HTTP lub HTTP przy użyciu programu PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell), [przekierowania HTTP lub HTTP przy użyciu interfejsu wiersza polecenia](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

  - ##### <a name="external-site"></a>Lokacja zewnętrzna

    Wybierz witryny zewnętrznej, jeśli chcesz przekierowywanie ruchu na odbiorniku skojarzony z regułą w związku z tym nastąpi przekierowanie do zewnętrznej witryny. Możesz wybrać ciągu zapytania w oryginalne żądanie, które mają zostać uwzględnione w żądaniu przekazywane do obiektu docelowego przekierowania. Nie można przesłać dalej ścieżki w oryginalne żądanie do witryny zewnętrznej.

    Aby uzyskać więcej informacji na temat przekierowania do witryny zewnętrznej, zobacz [przekierowywanie ruchu do zewnętrznej witryny przy użyciu programu PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell) i [https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-http-header-setting"></a>Ponowne zapisywanie adresów ustawienie nagłówka HTTP

Ta funkcja pozwala dodać, usunąć lub zaktualizować nagłówki żądania i odpowiedzi HTTP podczas żądania i pakietów odpowiedzi przenoszenia między pulami klienta i zaplecza.    Możesz skonfigurować tę możliwość wyłącznie za pośrednictwem programu PowerShell. Portal i interfejs wiersza polecenia obsługi nie jest jeszcze dostępna. Aby uzyskać więcej informacji, zobacz [nagłówków HTTP Nadpisz](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) Przegląd i [ponownego napisania nagłówka HTTP, skonfiguruj](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration).

## <a name="http-settings"></a>Ustawienia protokołu HTTP

Usługa application gateway kieruje ruch do serwerów wewnętrznej bazy danych przy użyciu konfiguracji określone w tej części. Po utworzeniu ustawienie protokołu HTTP, należy skojarzyć je z jedną lub więcej żądań reguł routingu.

### <a name="cookie-based-affinity"></a>Koligacja na podstawie pliku cookie

Ta funkcja jest przydatna, gdy chcesz zachować sesję użytkownika na tym samym serwerze. Używając plików cookie zarządzanych przez bramę, brama aplikacji może kierować dalszy ruch z sesji użytkownika na ten sam serwer w celu przetwarzania. Jest to ważne w przypadkach, w których stan sesji jest zapisywany lokalnie na serwerze dla sesji użytkownika. Jeśli aplikacja nie może obsługiwać koligacji na podstawie plików cookie, następnie nie będzie mógł używać tej funkcji. Aby użyć koligacji sesji na podstawie pliku cookie, należy upewnić się, że klienci muszą obsługiwać pliki cookie. 

### <a name="connection-draining"></a>Opróżnianie połączeń

Opróżnianie połączeń umożliwia bezproblemowe usunięcie członków puli zaplecza podczas planowanych aktualizacji usługi. To ustawienie można zastosować do wszystkich elementów członkowskich puli zaplecza podczas tworzenia reguł. Po włączeniu usługa application gateway zapewnia wszystkie wystąpienia bez rejestrowania puli zaplecza nie wszystkie nowe żądania zezwalając istniejącymi żądaniami zakończyć w ciągu skonfigurowanego limitu czasu. Dotyczy to zarówno wystąpień zaplecza, które są jawnie usuwane z puli zaplecza przez wywołanie interfejsu API, jak i wystąpień zaplecza, które zostały zgłoszone jako niepoprawne według oceny sond kondycji.

### <a name="protocol"></a>Protokół

Usługa Application gateway obsługuje protokoły HTTP i HTTPS do kierowania żądań do serwerów wewnętrznej bazy danych. Jeśli zostanie wybrana opcja protokołu HTTP, ruch sieciowy przepływów niezaszyfrowany do serwerów wewnętrznej bazy danych. W przypadkach, gdzie nieszyfrowana komunikacja z serwerami wewnętrznej bazy danych nie jest dopuszczalne należy wybrać protokół HTTPS. To ustawienie, połączone z Wybieranie protokołu HTTPS w odbiornik umożliwia włączenie [kompleksowej usługi SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Pozwala na bezpieczne przesyłanie danych poufnych na zaplecze, szyfrowane. Każdy serwer zaplecza w puli zaplecza z włączoną kompleksową usługą SSL należy skonfigurować przy użyciu certyfikatu, aby umożliwić bezpieczną komunikację.

### <a name="port"></a>Port

Jest to portu nasłuchiwania serwerów wewnętrznej bazy danych na ruch z bramy aplikacji. Można skonfigurować porty z zakresu od 1 do 65535.

### <a name="request-timeout"></a>Limit czasu żądania

Liczba sekund oczekiwania przez bramę aplikacji do odbierania odpowiedzi z puli wewnętrznej bazy danych, zanim zwróci komunikat "Upłynął limit czasu połączenia".

### <a name="override-backend-path"></a>Zastąp ścieżkę zaplecza

To ustawienie pozwala skonfigurować ścieżkę opcjonalne przekazywanie niestandardowej do użycia podczas żądania są przekazywane do zaplecza. Spowoduje to skopiowanie dowolnej części ścieżki przychodzącej, który pasuje do niestandardowa ścieżka określona w **zastąpienia ścieżka zaplecza** pole przekazywane ścieżki. Zobacz tabelę poniżej, aby zrozumieć, jak działa ta funkcja.

- Gdy ustawienie protokołu HTTP jest dołączony do reguły routingu podstawowego żądania:

  | Oryginalne żądanie  | Zastąp ścieżkę zaplecza | Żądanie przekazywane do zaplecza |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override/            | / zastąpienia/home /              |
  | / home/secondhome / | /override/            | / zastępowanie/home/secondhome /   |

- Gdy ustawienie protokołu HTTP jest dołączony do reguły routingu opartego na ścieżkach żądania:

  | Oryginalne żądanie           | Reguła ścieżki       | Zastąp ścieżkę zaplecza | Żądanie przekazywane do zaplecza |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home /            | /pathrule*      | /override/            | / zastąpienia/home /              |
  | / elemencie pathrule/home/secondhome / | /pathrule*      | /override/            | / zastępowanie/home/secondhome /   |
  | /Home/                     | /pathrule*      | /override/            | / zastąpienia/home /              |
  | / home/secondhome /          | /pathrule*      | /override/            | / zastępowanie/home/secondhome /   |
  | /pathrule/home /            | / elemencie pathrule/głównej * | /override/            | /override/                   |
  | / elemencie pathrule/home/secondhome / | / elemencie pathrule/głównej * | /override/            | / zastąpienia/secondhome /        |

### <a name="use-for-app-service"></a>Użyj dla usługi App Service

To skrót interfejsu użytkownika, który wybiera dwa ustawienia wymagane dla aplikacji usługi zaplecza — umożliwia pobranie nazwy hosta z adresu wewnętrznej bazy danych i tworzy nowy niestandardowej sondy. Powód, dlaczego odbywa się dawne zostało wyjaśnione w sekcji, aby **wybierz nazwę hosta z adresów zaplecza** ustawienie. Nową sondę jest tworzona, gdy w nagłówku sondy także jest pobierany z wewnętrznej bazy danych elementu członkowskiego adresu.

### <a name="use-custom-probe"></a>Użyj niestandardowej sondy

To ustawienie jest używane do kojarzenia [niestandardowej sondy](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) z tym ustawieniem protokołu HTTP. Tylko jeden niestandardowej sondy można skojarzyć z ustawienie protokołu HTTP. Jeśli nie jawnie skojarzysz niestandardowej sondy następnie [domyślnej funkcji badania](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) będzie służyć do monitorowania prawidłowości wewnętrznej bazy danych. Zaleca się, że tworzenie niestandardowej sondy do zapewniają bardziej szczegółową kontrolę nad monitorowanie kondycji zaplecza.

> [!NOTE]   
> Niestandardowe sondy nie rozpocznie się monitorowanie kondycji puli zaplecza, chyba że jawnie skojarzony odbiornik odpowiednie ustawienie protokołu HTTP.

### <a name="pick-host-name-from-backend-address"></a>Wybierz nazwę hosta z adresu zaplecza

Ta funkcja dynamicznie ustawia *hosta* nagłówka w żądaniu na nazwę hosta w puli zaplecza przy użyciu adresu IP lub w pełni kwalifikowaną nazwę domeny (FQDN). Jest to przydatne w scenariuszach, w którym nazwa domeny wewnętrznej bazy danych różni się od nazwy DNS bramy aplikacji i wewnętrznej bazy danych opiera się na konkretnym nagłówku hosta lub rozszerzeniu SNI przy rozwiązać właściwego punktu końcowego, takie jak w przypadku usługi wielodostępnej jako zaplecze. Ponieważ usługi App service to usługa dla wielu dzierżawców przy użyciu udostępnionego miejsca za pomocą pojedynczego adresu IP, dostęp do usługi App service są dostępne tylko w przypadku nazw hostów, w ustawieniach domeny niestandardowej. Domyślnie nazwa domeny niestandardowej jest *example.azurewebsites.net*. W związku z tym jeśli chcesz uzyskać dostęp w usłudze App service przy użyciu bramy aplikacji z nazwą hosta nie zostało zarejestrowane w usłudze App service lub nazwa FQDN bramy Application gateway, należy zastąpić nazwę hosta w żądaniu oryginalnej nazwy hosta usługi App service, za Włączanie **wybierz nazwę hosta z adresów zaplecza** ustawienie.

Jeśli masz domenę niestandardową i zmapowane istniejącej niestandardowej nazwy DNS w usłudze App service, nie trzeba to ustawienie jest włączone.

> [!NOTE]   
> To ustawienie nie jest wymagane dla usługi App Service Environment (ASE), ponieważ środowisko ASE jest dedykowany wdrożenia. 

### <a name="host-name-override"></a>Zastąpienie nazwy hosta

Ta funkcja zastępuje *hosta* nagłówka w żądaniu przychodzącym w usłudze application gateway dla nazwy hosta, określone w tym miejscu. Na przykład jeśli www\.contoso.com jest określony jako **nazwy hosta** ustawienia oryginalne żądanie https://appgw.eastus.cloudapp.net/path1 zostanie zmieniony na https://www.contoso.com/path1 gdy żądanie jest przekazywane do serwera wewnętrznej bazy danych. 

## <a name="backend-pool"></a>Pula zaplecza

Pula zaplecza może będzie wskazywał na cztery typy składowych zaplecza: określonej maszyny wirtualnej, zestawu skalowania maszyn wirtualnych, adres IP/nazwę FQDN lub usługi app service. Każda pula zaplecza może wskazywać na wielu członków tego samego typu. Wskazuje członkowie różniące się typy w tej samej puli zaplecza nie jest obsługiwane. 

Po utworzeniu puli zaplecza, należy ją skojarzyć z co najmniej jedną regułę routingu żądania. Należy również skonfigurować sond kondycji dla każdej puli wewnętrznej bazy danych w Twojej bramy application gateway. Po spełnieniu warunku reguły routingu żądania bramy application gateway przekazuje ruch do dobrej kondycji serwerów (zgodnie z ustaleniami sond kondycji) w odpowiedniej puli zaplecza.

## <a name="health-probes"></a>Sondy kondycji

Mimo że usługa application gateway monitoruje kondycję wszystkich zasobów w swojej wewnętrznej bazy danych, domyślnie, zdecydowanie zaleca się tworzenie niestandardowej sondy dla każdego ustawienia HTTP zaplecza w taki sposób, aby mieć większą kontrolę nad monitorowanie kondycji. Aby dowiedzieć się, jak skonfigurować sondy kondycji niestandardowych, zobacz [niestandardowe ustawienia sondy kondycji](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]   
> Po utworzeniu sondę kondycji niestandardowe, należy ją skojarzyć z ustawienia HTTP zaplecza. Niestandardowe sondy nie rozpocznie się monitorowanie kondycji puli zaplecza, chyba że jawnie skojarzony odbiornik odpowiednie ustawienie protokołu HTTP.

## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się składniki bramy aplikacji, możesz wykonywać następujące czynności:

- [Tworzenie bramy aplikacji w witrynie Azure portal](quick-create-portal.md)
- [Tworzenie bramy aplikacji przy użyciu programu PowerShell](quick-create-powershell.md)
- [Tworzenie bramy aplikacji przy użyciu wiersza polecenia platformy Azure](quick-create-cli.md)
