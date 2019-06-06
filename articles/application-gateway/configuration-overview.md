---
title: Omówienie konfiguracji usługi Azure Application Gateway
description: W tym artykule opisano sposób konfigurowania składników usługi Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/1/2019
ms.author: absha
ms.openlocfilehash: 55c7670821ee6c6f5b924bf18b5f7ad01d4b6d51
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431305"
---
# <a name="application-gateway-configuration-overview"></a>Omówienie konfiguracji bramy aplikacji

Usługa Azure Application Gateway składa się z kilku składników, które można skonfigurować na różne sposoby, w różnych scenariuszach. W tym artykule przedstawiono sposób konfigurowania poszczególnych składników.

![Schemat blokowy składniki bramy aplikacji](./media/configuration-overview/configuration-overview1.png)

Ten obraz przedstawia aplikacja, która ma trzy odbiorników. Pierwsze dwa są odbiorników obejmujących wiele lokacji dla `http://acme.com/*` i `http://fabrikam.com/*`, odpowiednio. Zarówno nasłuchiwania na porcie 80. Trzeci jest podstawowy odbiornik, który ma zakończenie protokołu Secure Sockets Layer (SSL) end-to-end.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Usługa Azure virtual network i dedykowaną podsieć

Application gateway to dedykowane wdrażania w sieci wirtualnej. W ramach sieci wirtualnej dedykowaną podsieć jest wymagana dla bramy application gateway. W podsieci, może mieć wielu wystąpień wdrożenia bramy danej aplikacji. Można także wdrożyć inne bramy aplikacji w podsieci. Ale nie można wdrożyć innych zasobów w podsieci bramy aplikacji.

> [!NOTE]
> Nie można mieszać Standard_v2 i standardowa usługi Azure Application Gateway, w tej samej podsieci.

#### <a name="size-of-the-subnet"></a>Rozmiar podsieci

Usługa Application Gateway zajmuje 1 prywatny adres IP dla każdego wystąpienia, a także innym prywatnym adresem IP, skonfigurowanie prywatnego adresu IP frontonu.

Platforma Azure rezerwuje również 5 adresów IP w każdej podsieci, do użytku wewnętrznego: 4 pierwsze i ostatnie adresy IP. Na przykład należy wziąć pod uwagę 15 wystąpienia bramy aplikacji przy użyciu nie prywatnego adresu IP frontonu. Potrzebne są co najmniej 20 adresów IP dla tej podsieci: 5 do użytku wewnętrznego i 15 w przypadku wystąpienia bramy aplikacji. Dlatego należy/27 podsieci lub większy rozmiar.

Należy wziąć pod uwagę podsieci, która ma 27 wystąpienia bramy aplikacji i adresu IP na prywatny adres IP frontonu. W takim przypadku należy 33 adresów IP: 27 dla wystąpienia bramy aplikacji: 1 dla prywatnych frontonu i 5 do użytku wewnętrznego. Dlatego należy /26 podsieci lub większy rozmiar.

Zaleca się, że używasz rozmiar podsieci, przynajmniej/28. Ten rozmiar zapewnia 11 można używać adresów IP. Jeśli Twoje obciążenia aplikacji wymaga więcej niż 10 adresów IP, należy wziąć pod uwagę/27 lub/26 rozmiar podsieci.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Sieciowe grupy zabezpieczeń w podsieci usługi Application Gateway

Sieciowe grupy zabezpieczeń (NSG) są obsługiwane w usłudze Application Gateway. Ale istnieje kilka ograniczeń:

- Musi zawierać wyjątków dla przychodzącego ruchu sieciowego na portów 65503 65534 dla bramy Application Gateway jednostkę SKU v1 i porty 65200 – 65535 dla jednostek SKU v2. Zakres tego portu jest wymagany do komunikacji infrastruktury platformy Azure. Te porty są zabezpieczone (zablokowane) z użyciem certyfikatów Azure. Podmioty zewnętrzne, w tym klienci tych bram nie można zainicjować zmiany w tych punktach końcowych bez odpowiednie certyfikaty w miejscu.

- Nie można zablokować wychodzące połączenie z Internetem. Domyślne reguły ruchu wychodzącego w sieciowej grupie zabezpieczeń umożliwiają łączność z Internetem. Zaleca się, możesz:

  - Nie usuwaj domyślne reguły ruchu wychodzącego.
  - Nie należy tworzyć inne reguły ruchu wychodzącego, które odmawiają wychodzące połączenie z Internetem.

- Ruch z **AzureLoadBalancer** tagów muszą być dozwolone.

##### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Dostęp do bramy Application Gateway kilka źródłowych adresów IP

W tym scenariuszu należy używać sieciowych grup zabezpieczeń w podsieci bramy aplikacji. W tej podsieci, w kolejności priorytetów, należy umieścić następujące ograniczenia:

1. Zezwalaj na ruch przychodzący ze źródłowego zakresu adresów IP/adresu IP.
2. Zezwalaj na przychodzące żądania pochodzące ze wszystkich źródeł porty 65503 65534 [komunikacji kondycja zaplecza](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Zakres tego portu jest wymagany do komunikacji infrastruktury platformy Azure. Te porty są zabezpieczone (zablokowane) z użyciem certyfikatów Azure. Bez odpowiednie certyfikaty w miejscu podmiotów zewnętrznych nie można zainicjować zmiany w tych punktach końcowych.
3. Zezwalaj na przychodzące Równoważenie obciążenia Azure (*AzureLoadBalancer* tag) i sieci wirtualnej ruch przychodzący (*VirtualNetwork* tag) na [sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Blokuj cały ruch przychodzący przy użyciu reguły wszystkie Odmów.
5. Zezwalaj na ruch wychodzący do Internetu dla wszystkich miejsc docelowych.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Trasy zdefiniowane przez użytkownika, obsługiwane w podsieci bramy aplikacji

Dla obiektu SKU v1 trasy zdefiniowane przez użytkownika (Udr) są obsługiwane w podsieci bramy aplikacji, tak długo, jak nie zmieniają komunikacji end-to-end żądania/odpowiedzi. Można na przykład skonfigurować trasy zdefiniowanej przez użytkownika w podsieci bramy Application Gateway wskaż urządzenie zapory dla inspekcja pakietów. Jednak należy się upewnić, pakiet dotrzeć do jego przeznaczenia po kontroli. Niewykonanie tej czynności może spowodować nieprawidłowe sondy kondycji lub zachowania routingu ruchu. Obejmuje to zapamiętane trasy lub domyślne trasy 0.0.0.0/0, które są propagowane przez usługę Azure ExpressRoute lub bram sieci VPN w sieci wirtualnej.

Dla jednostki SKU w wersji 2 i tras zdefiniowanych przez użytkownika nie są obsługiwane w podsieci bramy aplikacji. Aby uzyskać więcej informacji, zobacz [jednostki SKU w wersji 2 usługi Azure Application Gateway](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku).

> [!NOTE]
> Przy użyciu tras zdefiniowanych przez użytkownika w podsieci bramy aplikacji powoduje, że stan kondycji w [widoku kondycji zaplecza](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) aby pojawiało się jako "Nieznane". Powoduje generowanie dzienniki bramy aplikacji i metryki, aby zakończyć się niepowodzeniem. Zaleca się, że nie używasz tras zdefiniowanych przez użytkownika w podsieci bramy aplikacji tak, aby wyświetlić kondycja zaplecza, dzienniki i metryki.

## <a name="front-end-ip"></a>Adres IP frontonu

Można skonfigurować bramy aplikacji do publicznego adresu IP i/lub prywatnego adresu IP. Publiczny adres IP jest wymagany, gdy Hostuj zaplecze, że klienci muszą uzyskiwać dostęp za pośrednictwem sieci internet za pośrednictwem Internetu wirtualnego adresu IP (VIP). 

Publiczny adres IP nie jest wymagana dla wewnętrznego punktu końcowego, który nie jest połączone z Internetem. Który jest znany jako *wewnętrznego modułu równoważenia obciążenia* punktu końcowego (ILB). Application gateway wewnętrznego modułu równoważenia obciążenia jest przydatne w przypadku wewnętrznych aplikacji line-of-business, które nie są połączone z Internetem. Jest również przydatna w przypadku usług i warstw w aplikacji wielowarstwowej w obrębie granicy zabezpieczeń, które nie są połączone z Internetem, ale działanie okrężne, który wymaga załadować dystrybucji, lepkości sesji lub kończenia żądań SSL.

Tylko 1 publicznego adresu IP lub 1 prywatny adres IP jest obsługiwany. Podczas tworzenia bramy aplikacji możesz wybrać adres IP frontonu.

- Dla publicznego adresu IP można utworzyć nowego publicznego adresu IP lub użyć istniejącego publicznego adresu IP w tej samej lokalizacji co usługa application gateway. Jeśli tworzysz nowy publiczny adres IP, należy wybrać typ adresu IP (statyczne lub dynamiczne) nie można zmienić później. Aby uzyskać więcej informacji, zobacz [statycznego i dynamicznego publicznego adresu IP](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip-address).

- Aby uzyskać prywatny adres IP można określić prywatny adres IP z podsieci, w którym zostanie utworzona brama aplikacji. Jeśli nie zostanie określony, dowolnego adresu IP jest wybrany automatycznie z podsieci. Aby uzyskać więcej informacji, zobacz [Tworzenie bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Adres IP frontonu jest skojarzona z *odbiornika*, która sprawdza, czy żądania przychodzące na adres IP frontonu.

## <a name="listeners"></a>Odbiorniki

Odbiornik to jednostka logiczna, która sprawdza, czy żądania przychodzące połączenia przy użyciu portu, protokołu, hosta i adresu IP. Podczas konfigurowania odbiornika należy wprowadzić wartości dla tych, które odpowiadają odpowiednie wartości w żądaniu przychodzącym w bramie.

Podczas tworzenia bramy aplikacji przy użyciu witryny Azure portal, również utworzyć odbiornik, domyślne, wybierając protokół i port odbiornika. Możesz wybrać, czy włączyć obsługę protokołu HTTP2 na odbiornik. Po utworzeniu bramy application gateway, można edytować ustawienia tego odbiornika domyślne (*appGatewayHttpListener*/*appGatewayHttpsListener*) lub Utwórz nowe odbiorników.

### <a name="listener-type"></a>Typ odbiornika

Gdy tworzysz nowy odbiornik, możesz wybrać między [ *podstawowe* i *obejmujące wiele lokacji*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Jeśli przechowujesz jednej witryny za bramą aplikacji, wybierz opcję podstawowa. Dowiedz się, [jak utworzyć bramę aplikacji przy użyciu podstawowego odbiornika](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Jeśli konfigurujesz więcej niż jednej aplikacji sieci web lub wieloma poddomenami tej samej domeny nadrzędnej na tym samym wystąpieniu bramy aplikacji, wybierz odbiornika wielu lokacji. Dla odbiornika wielu lokacji należy również wprowadzić nazwę hosta. Jest to spowodowane Application Gateway bazuje na nagłówkach hosta HTTP 1.1 do obsługi więcej niż jednej witryny sieci Web na tym samym publicznym adresem IP i porcie.

#### <a name="order-of-processing-listeners"></a>Kolejność przetwarzania odbiorniki

W ramach jednostki SKU w wersji 1 odbiorniki są przetwarzane w kolejności, w jakiej występują na liście. Jeśli podstawowy odbiornik pasuje do przychodzącego żądania, odbiornik najpierw przetwarza żądanie. Skonfiguruj odbiorników obejmujących wiele lokacji przed podstawowe odbiorników, aby upewnić się, że ruch jest kierowany do poprawne zaplecza.

Dla jednostki SKU w wersji 2 i odbiorników obejmujących wiele lokacji są przetwarzane przed podstawowe odbiorników.

### <a name="front-end-ip"></a>Adres IP frontonu

Wybierz adres IP frontonu, który ma zostać skojarzony z tym odbiornika. Odbiornik będzie nasłuchiwać na przychodzące żądania na ten adres IP.

### <a name="front-end-port"></a>Port frontonu

Wybierz port frontonu. Wybierz istniejącego portu lub Utwórz nową. Wybierz dowolną wartość z [dozwolony zakres portów](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Można użyć nie tylko dobrze znanych portów, np. 80 i 443, ale dozwolone port niestandardowy, który jest odpowiedni. Port może służyć do odbiorników publicznego lub przeznaczonych dla prywatnych odbiorników.

### <a name="protocol"></a>Protocol

Wybierz protokołu HTTP lub HTTPS:

- Jeśli wybierzesz HTTP, ruch między klientem a bramy application gateway jest niezaszyfrowany.

- Wybierz protokół HTTPS, jeśli chcesz [kończenia żądań SSL](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) lub [end-to-end szyfrowania SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Ruch między klientem a bramy application gateway jest szyfrowany. I kończy się połączenia SSL na bramie aplikacji. Jeśli chcesz, aby end-to-end szyfrowania SSL, należy wybrać protokół HTTPS i skonfigurować **HTTP zaplecza** ustawienie. Daje to gwarancję, że ruch jest szyfrowany ponownie podczas przesyłania z bramy aplikacji z zapleczem.

Do skonfigurowania, kończenie żądań SSL i end-to-end szyfrowania SSL, należy dodać certyfikat do odbiornika do włączenia bramy aplikacji do uzyskania klucza symetrycznego. To jest zależna od specyfikacji protokołu SSL. Klucz symetryczny jest używany do szyfrowania i odszyfrowywania ruchu, który jest wysyłany do bramy. Certyfikat bramy musi mieć format wymiany informacji osobistych (PFX). Ten format umożliwia eksportowanie klucza prywatnego odpowiadającego brama używa do szyfrowania i odszyfrowywania ruchu.

#### <a name="supported-certificates"></a>Obsługiwane certyfikatów

Zobacz [certyfikatów obsługiwana w przypadku kończenia żądań SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Obsługa dodatkowych protokołów

#### <a name="http2-support"></a>Obsługa protokołu HTTP2

Obsługa protokołu HTTP/2 jest dostępna dla klientów łączących się tylko odbiorników bramy aplikacji. Komunikacja do pul serwerów zaplecza jest za pośrednictwem protokołu HTTP/1.1. Domyślnie obsługa protokołu HTTP/2 jest wyłączona. Poniższy fragment kodu programu Azure PowerShell pokazuje, jak włączyć tę opcję:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Obsługa protokołu WebSocket

Obsługa protokołu WebSocket jest domyślnie włączona. Nie ma żadnego użytkownika można skonfigurować ustawienia, aby włączyć lub wyłączyć. Za pomocą funkcji WebSockets odbiorników HTTP i HTTPS.

### <a name="custom-error-pages"></a>Niestandardowe strony błędów

Można zdefiniować niestandardowy błąd na poziomie globalnym lub odbiornika. Jednak tworzenie strony błędów niestandardowych na poziomie globalnym w witrynie Azure portal nie jest obecnie obsługiwane. Niestandardowej strony błędu dla błędu zapory aplikacji sieci web 403 lub strony 502 obsługi można skonfigurować na poziomie odbiornika. Należy także określić adres URL dostępny publicznie blob kod stanu danego błędu. Aby uzyskać więcej informacji, zobacz [Create Application Gateway custom error pages (Tworzenie niestandardowych stron błędów w usłudze Application Gateway)](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Kody błędów bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Aby skonfigurować globalne niestandardowej strony błędu, zobacz [konfiguracji programu Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>Zasady SSL

Można scentralizować zarządzanie certyfikatami SSL i zmniejszyć odszyfrowywania szyfrowania obciążenie dla farmy serwerów zaplecza. Scentralizowane Obsługa protokołu SSL pozwala także określić centralne zasady protokołu SSL, który jest odpowiedni do określonych wymagań dotyczących zabezpieczeń. Możesz wybrać *domyślne*, *wstępnie zdefiniowanych*, lub *niestandardowe* zasad protokołu SSL.

Konfigurowania zasad protokołu SSL do wersji protokołu SSL dla formantu. Można skonfigurować bramę aplikacji, aby odmówić TLS1.0 TLS1.1 i TLS1.2. Domyślnie protokół SSL 2.0 i 3.0 są wyłączone i nie są konfigurowalne. Aby uzyskać więcej informacji, zobacz [Przegląd zasad protokołu SSL bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Po utworzeniu odbiornika, należy ją skojarzyć z regułą routing żądań. Tą regułą Określa, jak żądania, które są odbierane na odbiornika są kierowane do zaplecza.

## <a name="request-routing-rules"></a>Żądanie reguły rozsyłania

Podczas tworzenia bramy aplikacji przy użyciu witryny Azure portal, należy utworzyć regułę domyślną (*rule1*). Ta reguła wiąże odbiornik z domyślnego (*appGatewayHttpListener*) za pomocą domyślnej puli zaplecza (*appGatewayBackendPool*) i domyślne ustawienia HTTP zaplecza ( *appGatewayBackendHttpSettings*). Po utworzeniu bramy można edytować ustawienia reguły domyślnej lub utworzyć nowe reguły.

### <a name="rule-type"></a>Typ reguły

Podczas tworzenia reguły, możesz wybrać między [ *podstawowe* i *opartego na ścieżkach*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule).

- Wybierz pozycję podstawowy, jeśli chcesz przekazać wszystkie żądania na skojarzony odbiornik (na przykład *blog<i></i>.contoso.com/\*)* do pojedynczej puli zaplecza.
- Wybierz, oparty na ścieżkach aby kierować żądania z określonej ścieżki adresu URL do określonej puli zaplecza. Wzorzec ścieżki jest stosowane tylko do ścieżki adresu URL, z jego parametrów zapytania.

#### <a name="order-of-processing-rules"></a>Kolejność przetwarzania reguły

Dla obiektu SKU v1 dopasowywanie do wzorców żądań przychodzących są przetwarzane w kolejności, że ścieżki są wymienione w Mapa ścieżki adresu URL opartego na ścieżkach reguły. Jeśli żądanie pasuje do wzorca w dwóch lub więcej ścieżek w mapie ścieżki, ścieżki, który znajduje się najpierw jest takie samo. I żądanie jest przekazywane do zaplecza, która jest skojarzona z tej ścieżki.

W przypadku jednostki SKU w wersji 2 i dokładne dopasowanie jest wyższy priorytet niż ścieżka kolejność w mapie ścieżki adresu URL. Jeśli żądanie jest zgodny ze wzorcem w dwóch lub więcej ścieżek, żądania są przekazywane do zaplecza, skojarzony ze ścieżką, który dokładnie pasuje do żądania. Jeśli ścieżka w żądaniu przychodzącym nie odpowiada dokładnie dowolną ścieżkę na mapie, dopasowywanie do wzorców żądania jest wykonywane na liście ścieżki mapy zamówienia dla reguły opartego na ścieżkach.

### <a name="associated-listener"></a>Skojarzony odbiornik

Skojarz odbiornika do reguły tak, aby *reguła routingu żądania* skojarzony z odbiornika jest oceniany w celu określenia puli zaplecza, aby skierować żądanie.

### <a name="associated-back-end-pool"></a>Skojarzoną pulę zaplecza

Skojarz zasady puli zaplecza, która zawiera obiektów docelowych zaplecza, które obsługiwać żądań odbieranych przez odbiornik.

 - Aby uzyskać podstawową regułę tylko jedną pulę zaplecza jest dozwolone. Wszystkie żądania na skojarzony odbiornik są przekazywane do tej puli zaplecza.

 - Reguły opartego na ścieżkach należy dodać wiele pul zaplecza, które odpowiadają każdej ścieżką URL. Żądania, które odpowiadają Ścieżka adresu URL, który jest wprowadzana są przekazywane do odpowiedniej puli zaplecza. Ponadto należy dodać domyślna pula zaplecza. Żądania, które nie są zgodne z dowolną ścieżkę adresu URL w regule są przekazywane do tej puli.

### <a name="associated-back-end-http-setting"></a>Skojarzone ustawienia HTTP zaplecza

Dodawanie ustawienia HTTP zaplecza dla każdej reguły. Żądania są kierowane z bramy aplikacji do celów zaplecza przy użyciu numeru portu, protokołu i inne informacje, który jest określony w tym ustawieniu.

W przypadku podstawową regułę dozwolone jest tylko jedno ustawienie protokołu HTTP zaplecza. Wszystkie żądania na skojarzony odbiornik są przekazywane do odpowiednich elementów docelowych zaplecza za pomocą tego ustawienia protokołu HTTP.

Dodawanie ustawienia HTTP zaplecza dla każdej reguły. Żądania są kierowane z bramy aplikacji do celów zaplecza przy użyciu numeru portu, protokołu i inne informacje, który jest określony w tym ustawieniu.

W przypadku podstawową regułę dozwolone jest tylko jedno ustawienie protokołu HTTP zaplecza. Wszystkie żądania na skojarzony odbiornik są przekazywane do odpowiednich elementów docelowych zaplecza za pomocą tego ustawienia protokołu HTTP.

Reguły na podstawie ścieżki należy dodać wiele ustawień HTTP zaplecza, które odnoszą się do każdej ścieżki adresu URL. Żądania, które odpowiadają Ścieżka adresu URL, w tym ustawieniu są przekazywane do odpowiednich elementów docelowych zaplecza za pomocą ustawienia protokołu HTTP, które odpowiadają każdej ścieżki adresu URL. Ponadto można dodać domyślnego ustawienia protokołu HTTP. Żądania, które nie są zgodne z dowolną ścieżkę adresu URL w tej regule są przekazywane do domyślnej puli zaplecza przy użyciu domyślnego ustawienia protokołu HTTP.

### <a name="redirection-setting"></a>Ustawienie przekierowywania

Jeśli skonfigurowano przekierowania podstawową regułę, wszystkie żądania na skojarzony odbiornik nastąpi przekierowanie do obiektu docelowego. Jest to *globalnego* przekierowania. Jeśli przekierowanie skonfigurowano regułę opartego na ścieżkach, nastąpi przekierowanie tylko żądania w obszarze określonej lokacji. Przykładem jest obszar koszyka zakupów jest wskazywane przez */cart/\** . Jest to *opartego na ścieżkach* przekierowania.

Aby uzyskać więcej informacji na temat przekierowania zobacz [Application Gateway redirect overview](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="redirection-type"></a>Typ przekierowania

Wybierz typ przekierowania wymagane: *Permanent(301)* , *Temporary(307)* , *Found(302)* , lub *Zobacz other(303)* .

#### <a name="redirection-target"></a>Miejsce docelowe przekierowania

Wybierz inny odbiornik lub zewnętrznej witryny jako obiektu docelowego przekierowania.

##### <a name="listener"></a>Odbiornik

Wybierz odbiornika jako obiektu docelowego przekierowania do przekierowywania ruchu z jednego odbiornika do innego w bramie. To ustawienie jest wymagane, jeśli chcesz włączyć przekierowywanie HTTP do HTTPS. Przekierowuje ruch z odbiornika źródła, która sprawdza, czy przychodzące żądania HTTP do odbiornik docelowy, który sprawdza, czy przychodzących żądań HTTPS. Możesz również zawierać ciąg zapytania i ścieżki z oryginalnego żądania w żądaniu, który jest przekazywany do obiektu docelowego przekierowania.

![Okno dialogowe składniki bramy aplikacji](./media/configuration-overview/configure-redirection.png)

Aby uzyskać więcej informacji na temat przekierowania HTTP do HTTPS zobacz:
- [Przekierowywanie HTTP do HTTPS przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [Przekierowywanie HTTP do HTTPS przy użyciu programu PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [Przekierowywanie HTTP do HTTPS przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>Witryny zewnętrznej

Wybierz witryny zewnętrznej, jeśli chcesz przekierowywanie ruchu na odbiornik, który jest skojarzony z tą regułą do zewnętrznej witryny. Istnieje możliwość Dołącz ciąg zapytania z oryginalnego żądania w żądaniu, który jest przekazywany do obiektu docelowego przekierowania. Nie można przesłać dalej ścieżkę do witryny zewnętrznej, który oryginalne żądanie.

Aby uzyskać więcej informacji o przekierowywaniu zobacz:
- [Przekierowywanie ruchu do zewnętrznej witryny przy użyciu programu PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [Przekierowywanie ruchu do zewnętrznej witryny przy użyciu interfejsu wiersza polecenia](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>Ponowne zapisywanie adresów ustawienie nagłówka HTTP

To ustawienie dodaje, usuwa lub aktualizuje nagłówki żądania i odpowiedzi HTTP podczas żądania i pakietów odpowiedzi, przenoszenie między klientem a pule zaplecza. Można skonfigurować tylko tej funkcji za pomocą programu PowerShell. Witryna Azure portal i interfejsu wiersza polecenia nie są jeszcze dostępne. Aby uzyskać więcej informacji, zobacz:

 - [Ponownie zapisuje nagłówki HTTP, omówienie](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [Konfigurowanie ponownego napisania nagłówka HTTP](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>Ustawienia HTTP

Usługa application gateway kieruje ruch do serwerów zaplecza przy użyciu konfiguracji, który określisz w tym miejscu. Po utworzeniu ustawienie protokołu HTTP, należy ją skojarzyć z co najmniej jedną regułę routingu żądań.

### <a name="cookie-based-affinity"></a>Koligacja na podstawie pliku cookie

Ta funkcja jest przydatna, gdy chcesz zachować sesję użytkownika na tym samym serwerze. Zarządzanych przez bramę pliki cookie pozwalają aplikacji bramy bezpośrednie kolejnych ruch z sesji użytkownika do tego samego serwera w celu przetworzenia. Jest to ważne w przypadku, gdy stan sesji jest zapisywany lokalnie na serwerze dla sesji użytkownika. Jeśli aplikacja nie może obsługiwać koligacji na podstawie plików cookie, nie można użyć tej funkcji. Aby go użyć, upewnij się, że klientów obsługujących pliki cookie.

### <a name="connection-draining"></a>Opróżnianie połączeń

Opróżniania połączenia pomaga bezpiecznie usunąć członków puli zaplecza podczas aktualizacji usługi planowane. To ustawienie można zastosować do wszystkich elementów członkowskich puli zaplecza podczas tworzenia reguł. Zapewnia, że wszystkie wystąpienia bez rejestrowania puli zaplecza nie otrzymasz żadnych nowych żądań. W tym samym czasie istniejącymi żądaniami mogą zakończyć w ciągu skonfigurowanego limitu czasu. Opróżniania połączenia ma zastosowanie do wystąpień zaplecza, które są jawnie usuwane z puli zaplecza przez wywołanie interfejsu API. Ma również zastosowanie do wystąpień zaplecza, które są zgłaszane jako *złej kondycji* przez kondycji sondy.

### <a name="protocol"></a>Protocol

Usługa Application Gateway obsługuje protokołów HTTP i HTTPS do kierowania żądań do serwerów zaplecza. Jeśli wybierzesz HTTP, ruch do serwerów zaplecza jest niezaszyfrowany. Nieszyfrowana komunikacja nie jest dopuszczalna, jeśli protokół HTTPS.

To ustawienie połączone za pomocą protokołu HTTPS w obsługuje odbiornika [end-to-end SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Dzięki temu można bezpiecznie przesyłać poufne dane zaszyfrowane do zaplecza. Każdy serwer zaplecza w puli zaplecza, która ma włączony protokół SSL end-to-end, należy określić przy użyciu certyfikatu, aby umożliwić bezpieczną komunikację.

### <a name="port"></a>Port

To ustawienie określa port, gdzie serwery zaplecza nasłuchiwać ruch z bramy aplikacji. Można skonfigurować porty z zakresu od 1 do 65535.

### <a name="request-timeout"></a>Limit czasu żądania

To ustawienie jest czas w sekundach, przez bramy application gateway oczekuje na odpowiedź z puli zaplecza przed zwróceniem komunikat o błędzie "Upłynął limit czasu połączenia".

### <a name="override-back-end-path"></a>Zastąp ścieżkę zaplecza

To ustawienie umożliwia skonfigurowanie ścieżki opcjonalne przekazywanie niestandardowej do użycia podczas żądania są przekazywane do zaplecza. Dowolnej części ścieżki przychodzącej, który odpowiada niestandardową ścieżkę, w **zastąpienia ścieżka zaplecza** pola jest kopiowany do ścieżki przekazywane. W poniższej tabeli przedstawiono, jak działa ta funkcja:

- Gdy ustawienie protokołu HTTP jest dołączony do podstawową regułę routingu żądań:

  | Oryginalne żądanie  | Zastąp ścieżkę zaplecza | Żądanie przekazywane do zaplecza |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override/            | / zastąpienia/home /              |
  | / home/secondhome / | /override/            | / zastępowanie/home/secondhome /   |

- Gdy ustawienie protokołu HTTP jest dołączony do reguły routingu żądań opartego na ścieżkach:

  | Oryginalne żądanie           | Reguła ścieżki       | Zastąp ścieżkę zaplecza | Żądanie przekazywane do zaplecza |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home /            | /pathrule*      | /override/            | / zastąpienia/home /              |
  | / elemencie pathrule/home/secondhome / | /pathrule*      | /override/            | / zastępowanie/home/secondhome /   |
  | /Home/                     | /pathrule*      | /override/            | / zastąpienia/home /              |
  | / home/secondhome /          | /pathrule*      | /override/            | / zastępowanie/home/secondhome /   |
  | /pathrule/home /            | / elemencie pathrule/głównej * | /override/            | /override/                   |
  | / elemencie pathrule/home/secondhome / | / elemencie pathrule/głównej * | /override/            | / zastąpienia/secondhome /        |

### <a name="use-for-app-service"></a>Użyj usługi app service

Jest to skrót interfejsu użytkownika, który wybiera dwa ustawienia wymagane dla zaplecza usługi Azure App Service. Umożliwia ona **wybierz nazwę hosta z adresów zaplecza**, i tworzy nowy niestandardowej sondy. (Aby uzyskać więcej informacji, zobacz [pobrania nazwy hosta z adresów zaplecza](#pick) ustawienie w dalszej części tego artykułu.) Utworzono nową sondę i w nagłówku sondy jest pobierane z adresów zaplecza elementu członkowskiego.

### <a name="use-custom-probe"></a>Użyj niestandardowej sondy

To ustawienie powoduje skojarzenie [niestandardowej sondy](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) z ustawieniem protokołu HTTP. Tylko jeden niestandardowej sondy można skojarzyć z ustawienie protokołu HTTP. Jeśli nie jest jawnie skojarzyć niestandardowej sondy [domyślnej funkcji badania](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) służy do monitorowania kondycji zaplecza. Zaleca się, że tworzenie niestandardowej sondy dla większą kontrolę nad monitorowania kondycji usługi zaplecza.

> [!NOTE]
> Niestandardowej sondy nie monitoruje kondycję puli zaplecza, chyba że jawnie skojarzony odbiornik odpowiednie ustawienie protokołu HTTP.

### <a id="pick"/></a>Wybierz nazwę hosta z adresów zaplecza

Ta funkcja dynamicznie ustawia *hosta* nagłówka w żądaniu na nazwę hosta w puli zaplecza. Używa adresu IP lub nazwę FQDN.

Ta funkcja pomaga nazwę domeny wewnętrznej różni się od nazwy DNS bramy aplikacji, gdy zaplecza opiera się na konkretnym nagłówku hosta lub rozszerzeniu oznaczaniem nazwy serwera (SNI) przy rozpoznawaniu właściwego punktu końcowego.

Przykład przypadek jest wielodostępnych usług zaplecza. Usługi app service jest usługą wielodostępną, używającego udostępnionych miejsca za pomocą pojedynczego adresu IP. Tak usługi app service można uzyskać tylko za pośrednictwem nazwy hostów, które są konfigurowane w ustawieniach domeny niestandardowej.

Domyślnie nazwa domeny niestandardowej jest *example.azurewebsites.<i> </i>net*. Dostęp do usługi app service przy użyciu bramy aplikacji za pomocą nazwy hosta, które nie zostały jawnie jest zarejestrowany w usłudze app service lub nazwa FQDN bramy aplikacji, możesz zastąpić nazwę hosta w żądaniu oryginalnej nazwy hosta usługi app service. Aby to zrobić, należy włączyć **wybierz nazwę hosta z adresów zaplecza** ustawienie.

Dla domeny niestandardowej, którego istniejącej niestandardowej nazwy DNS jest mapowany w usłudze app service nie trzeba to ustawienie jest włączone.

> [!NOTE]
> To ustawienie nie jest wymagane dla środowiska usługi App Service w usłudze PowerApps, która jest dedykowany wdrożenia.

### <a name="host-name-override"></a>Zastąpienie nazwy hosta

Ta funkcja zastępuje *hosta* nagłówka w żądaniu przychodzącym w usłudze application gateway z nazwą hosta, który określisz.

Na przykład jeśli *www.contoso<i></i>.com* została określona w **nazwy hosta** ustawienia oryginalne żądanie *https:/<i></i>/appgw.eastus.cloudapp.net/path1* jest zmieniana na *https:/<i></i>/www.contoso.com/path1* gdy żądanie jest przekazywane do serwera zaplecza.

## <a name="back-end-pool"></a>Pula zaplecza

Pula zaplecza może wskazywać cztery rodzaje składowych zaplecza: określonej maszyny wirtualnej, zestawu skalowania maszyn wirtualnych, adres IP/nazwę FQDN lub usługi app service. Każda pula zaplecza może wskazywać na wielu członków tego samego typu. Wskazuje członkowie różniące się typy w tej samej puli zaplecza nie jest obsługiwane.

Po utworzeniu puli zaplecza, należy ją skojarzyć z co najmniej jedną regułę routingu żądań. Należy również skonfigurować sond kondycji dla każdej puli zaplecza, na Twojej bramy application gateway. Po spełnieniu warunku reguły routingu żądań bramy application gateway przekazuje ruch do dobrej kondycji serwerów (zgodnie z ustaleniami sond kondycji) w odpowiedniej puli zaplecza.

## <a name="health-probes"></a>Sondy kondycji

Bramy aplikacji monitoruje kondycję wszystkich zasobów w swojej wewnętrznej domyślnie. Jednak zdecydowanie zaleca się tworzenie niestandardowej sondy dla każdego ustawienia HTTP zaplecza uzyskać większą kontrolę nad monitorowanie kondycji. Aby dowiedzieć się, jak skonfigurować niestandardową sondę, zobacz [niestandardowe ustawienia sondy kondycji](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]
> Po utworzeniu sondę kondycji niestandardowe, należy ją skojarzyć z ustawienia HTTP zaplecza. Niestandardowe sondy nie będzie monitorować kondycję puli zaplecza, chyba że jawnie skojarzony odbiornik odpowiednie ustawienie protokołu HTTP.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz o składniki bramy aplikacji możesz wykonywać następujące czynności:

- [Tworzenie bramy aplikacji w witrynie Azure portal](quick-create-portal.md)
- [Tworzenie bramy aplikacji przy użyciu programu PowerShell](quick-create-powershell.md)
- [Tworzenie bramy aplikacji przy użyciu wiersza polecenia platformy Azure](quick-create-cli.md)
