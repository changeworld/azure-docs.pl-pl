---
title: Omówienie konfiguracji Application Gateway platformy Azure
description: W tym artykule opisano sposób konfigurowania składników usługi Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/15/2019
ms.author: absha
ms.openlocfilehash: bb6ad1f131d1299ce1e076fee70e6640e3bdf20a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373563"
---
# <a name="application-gateway-configuration-overview"></a>Przegląd konfiguracji Application Gateway

Usługa Azure Application Gateway obejmuje kilka składników, które można skonfigurować na różne sposoby w różnych scenariuszach. W tym artykule opisano sposób konfigurowania każdego składnika.

![Wykres przepływu składników Application Gateway](./media/configuration-overview/configuration-overview1.png)

Ten obraz przedstawia aplikację, która ma trzy detektory. Pierwsze dwa są odbiornikami wielolokacjowymi dla `http://acme.com/*` i `http://fabrikam.com/*`, odpowiednio. Oba nasłuchują na porcie 80. Trzecia to podstawowy odbiornik, który ma kompleksowe SSL (SSL).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Sieć wirtualna platformy Azure i podsieć dedykowana

Brama aplikacji to dedykowane wdrożenie w sieci wirtualnej. W ramach sieci wirtualnej wymagana jest dedykowana podsieć dla bramy aplikacji. W podsieci można mieć wiele wystąpień danego wdrożenia bramy aplikacji. Możesz również wdrożyć inne bramy aplikacji w podsieci. Nie można jednak wdrożyć żadnego innego zasobu w podsieci bramy aplikacji.

> [!NOTE]
> Nie można mieszać Standard_v2 i standardowego Application Gateway platformy Azure w tej samej podsieci.

#### <a name="size-of-the-subnet"></a>Rozmiar podsieci

Application Gateway zużywa 1 prywatny adres IP na wystąpienie oraz inny prywatny adres IP w przypadku skonfigurowania prywatnego adresu IP frontonu.

Platforma Azure rezerwuje także 5 adresów IP w każdej podsieci do użytku wewnętrznego: pierwsze 4 i ostatnie adresy IP. Rozważmy na przykład 15 wystąpień usługi Application Gateway bez prywatnego adresu IP frontonu. Potrzebujesz co najmniej 20 adresów IP dla tej podsieci: 5 do użytku wewnętrznego i 15 dla wystąpień usługi Application Gateway. W związku z tym potrzebny jest rozmiar podsieci/27 lub większy.

Rozważ podsieć zawierającą 27 wystąpień usługi Application Gateway i adres IP dla prywatnego adresu IP frontonu. W takim przypadku wymagane są 33 adresów IP: 27 dla wystąpień usługi Application Gateway, 1 dla frontonu prywatnego i 5 do użytku wewnętrznego. W związku z tym potrzebny jest rozmiar podsieci/26 lub większy.

Zalecamy użycie rozmiaru podsieci co najmniej/28. Ten rozmiar daje 11 przydatnych adresów IP. Jeśli obciążenie aplikacji wymaga więcej niż 10 Application Gateway wystąpień, należy wziąć pod uwagę rozmiar podsieci/27 lub/26.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Sieciowe grupy zabezpieczeń w podsieci Application Gateway

Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) są obsługiwane w Application Gateway. Istnieją jednak pewne ograniczenia:

- Należy zezwolić na przychodzący ruch internetowy na portach TCP 65503-65534 dla jednostki SKU Application Gateway V1 i portów TCP 65200-65535 dla jednostki SKU v2 z podsiecią docelową jako **dowolny** i źródłowy jako tag usługi **gatewaymanager** . Ten zakres portów jest wymagany w przypadku komunikacji infrastruktury platformy Azure. Te porty są chronione (zablokowane) przez certyfikaty platformy Azure. Jednostki zewnętrzne, w tym klienci tych bram, nie mogą komunikować się z tymi punktami końcowymi.

- Nie można zablokować wychodzącej łączności z Internetem. Domyślne reguły ruchu wychodzącego w sieciowej grupy zabezpieczeń umożliwiają łączność z Internetem. Zalecamy wykonanie następujących czynności:

  - Nie usuwaj domyślnych reguł dla ruchu wychodzącego.
  - Nie należy tworzyć innych reguł wychodzących, które odbraniają łączności wychodzącej.

- Ruch ze znacznika **AzureLoadBalancer** musi być dozwolony.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Zezwalaj na Application Gateway dostęp do kilku źródłowych adresów IP

W tym scenariuszu należy użyć sieciowych grup zabezpieczeń w podsieci Application Gateway. W tej kolejności należy umieścić następujące ograniczenia w podsieci:

1. Zezwalaj na ruch przychodzący ze źródłowego adresu IP lub zakresu adresów IP z miejscem docelowym jako cały zakres adresów podsieci Application Gateway i port docelowy jako port dostępu przychodzącego, na przykład port 80 dla dostępu za pośrednictwem protokołu HTTP.
2. Zezwalaj na przychodzące żądania ze źródła jako tag usługi **gatewaymanager** i miejsce docelowe jako **dowolnych** i docelowych portów as 65503-65534 dla jednostki SKU Application Gateway V1 oraz portów 65200-65535 dla wersji 2 jednostki SKU do [komunikacji z stanem kondycji zaplecza](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Ten zakres portów jest wymagany w przypadku komunikacji infrastruktury platformy Azure. Te porty są chronione (zablokowane) przez certyfikaty platformy Azure. Bez odpowiednich certyfikatów jednostki zewnętrzne nie mogą inicjować zmian w tych punktach końcowych.
3. Zezwalaj na wychodzące sondy Azure Load Balancer (tag*AzureLoadBalancer* ) i ruchu przychodzącego sieci wirtualnej (tag*VirtualNetwork* ) w [sieciowej grupie zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Blokuj cały ruch przychodzący za pomocą reguły Odmów.
5. Zezwalaj na ruch wychodzący do Internetu dla wszystkich miejsc docelowych.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Trasy zdefiniowane przez użytkownika są obsługiwane w podsieci Application Gateway

W przypadku jednostki SKU w wersji 1 trasy zdefiniowane przez użytkownika (UDR) są obsługiwane w podsieci Application Gateway, o ile nie zmieniają one kompleksowej komunikacji żądania/odpowiedzi. Na przykład można skonfigurować UDR w podsieci Application Gateway, aby wskazać urządzenie zapory na potrzeby inspekcji pakietów. Należy jednak upewnić się, że pakiet może dotrzeć do zamierzonego miejsca docelowego po inspekcji. Niewykonanie tej czynności może spowodować nieprawidłowe działanie sondowania kondycji lub routingu ruchu. Obejmuje to informacje o trasach lub domyślnych trasach 0.0.0.0/0, które są propagowane przez usługi Azure ExpressRoute lub bramy sieci VPN w sieci wirtualnej.

W przypadku jednostki SKU v2 UDR nie są obsługiwane w podsieci Application Gateway. Aby uzyskać więcej informacji, zobacz [jednostka SKU platformy Azure Application Gateway v2](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku).

> [!NOTE]
> UDR nie są obecnie obsługiwane dla jednostki SKU w wersji 2.

> [!NOTE]
> Użycie UDR w podsieci Application Gateway może spowodować, że stan kondycji w [widoku kondycji zaplecza](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) będzie wyświetlany jako "nieznany". Może to spowodować, że generowanie dzienników Application Gateway i metryk kończy się niepowodzeniem. Zalecamy, aby nie używać UDR w podsieci Application Gateway, aby można było wyświetlić kondycję, dzienniki i metryki zaplecza.

## <a name="front-end-ip"></a>Adres IP frontonu

Bramę aplikacji można skonfigurować tak, aby zawierała publiczny adres IP, prywatny adres IP lub oba te elementy. Publiczny adres IP jest wymagany w przypadku hostowania zaplecza, do którego klienci muszą uzyskać dostęp za pośrednictwem Internetu za pośrednictwem wirtualnego adresu IP (VIP) dostępnego z Internetu. 

Publiczny adres IP nie jest wymagany dla wewnętrznego punktu końcowego, który nie jest narażony na Internet. Jest on znany jako punkt końcowy *wewnętrznego modułu równoważenia obciążenia* (ILB) lub prywatny adres IP frontonu. ILB bramy aplikacji jest przydatne w przypadku wewnętrznych aplikacji biznesowych, które nie są dostępne w Internecie. Jest on również przydatny w przypadku usług i warstw w aplikacji wielowarstwowej w ramach granicy zabezpieczeń, która nie jest dostępna w Internecie, ale wymaga dystrybucji obciążenia z działaniem okrężnym, lepkość sesji lub zakończenia protokołu SSL.

Obsługiwany jest tylko 1 publiczny adres IP lub 1 prywatny adres IP. Adres IP frontonu jest wybierany podczas tworzenia bramy aplikacji.

- W przypadku publicznego adresu IP można utworzyć nowy publiczny adres IP lub użyć istniejącego publicznego adresu IP w tej samej lokalizacji, w której znajduje się Brama aplikacji. Aby uzyskać więcej informacji, zobacz [statyczny i dynamiczny publiczny adres IP](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Dla prywatnego adresu IP można określić prywatny adres IP z podsieci, w której jest tworzona Brama aplikacji. Jeśli nie określisz go, zostanie automatycznie wybrany dowolny adres IP z podsieci. Wybrany typ adresu IP (statyczny lub dynamiczny) nie może zostać później zmieniony. Aby uzyskać więcej informacji, zobacz [Tworzenie bramy aplikacji przy użyciu wewnętrznego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Adres IP frontonu jest skojarzony z *odbiornikiem*, który sprawdza przychodzące żądania w adresie IP frontonu.

## <a name="listeners"></a>Obiektów

Odbiornik jest jednostką logiczną, która sprawdza przychodzące żądania połączeń przy użyciu portu, protokołu, hosta i adresu IP. Podczas konfigurowania odbiornika należy wprowadzić wartości pasujące do odpowiednich wartości w żądaniu przychodzącym na bramie.

Podczas tworzenia bramy aplikacji przy użyciu Azure Portal należy również utworzyć odbiornik domyślny, wybierając protokół i port odbiornika. Możesz wybrać, czy włączyć obsługę HTTP2 na odbiorniku. Po utworzeniu bramy aplikacji można edytować ustawienia tego odbiornika domyślnego (*appGatewayHttpListener*) lub utworzyć nowe odbiorniki.

### <a name="listener-type"></a>Typ odbiornika

Podczas tworzenia nowego odbiornika należy wybrać jedną z [ *podstawowych* i *wiele lokacji*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Jeśli chcesz, aby wszystkie żądania (dla dowolnej domeny) zostały zaakceptowane i przesłane dalej do pul zaplecza, wybierz pozycję podstawowa. Dowiedz się, [jak utworzyć bramę aplikacji z odbiornikiem podstawowym](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Jeśli chcesz przekazywać żądania do różnych pul zaplecza na podstawie nagłówka *hosta* lub nazwy hosta, wybierz odbiornik wielu witryn, gdzie należy określić nazwę hosta zgodną z żądaniem przychodzącym. Jest to spowodowane tym, że Application Gateway opiera się na nagłówkach hosta HTTP 1,1 do hostowania więcej niż jednej witryny sieci Web na tym samym publicznym adresie IP i porcie.

#### <a name="order-of-processing-listeners"></a>Kolejność przetwarzania odbiorników

W przypadku jednostki SKU V1 żądania są dopasowywane zgodnie z kolejnością reguł i typem odbiornika. Jeśli zasada z odbiornikiem podstawowym jest najpierw w kolejności, zostanie ona przetworzona jako pierwsza i zaakceptuje wszystkie żądania dla tego portu i kombinacji adresów IP. Aby tego uniknąć, należy najpierw skonfigurować reguły z odbiornikami z obsługą kilku lokacji i wypchnąć regułę z odbiornikiem podstawowym do ostatniego z listy.

W przypadku jednostki SKU w wersji 2 odbiorniki z obsługą wiele lokacji są przetwarzane przed podstawowymi odbiornikami.

### <a name="front-end-ip"></a>Adres IP frontonu

Wybierz adres IP frontonu, który ma zostać skojarzony z tym odbiornikiem. Odbiornik nasłuchuje przychodzących żądań w tym adresie IP.

### <a name="front-end-port"></a>Port frontonu

Wybierz port frontonu. Wybierz istniejący port lub Utwórz nowy. Wybierz dowolną wartość z [dozwolonego zakresu portów](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Można użyć nie tylko dobrze znanych portów, na przykład 80 i 443, ale dowolnego dozwolonego niestandardowego portu, który jest odpowiedni. Port może być używany na potrzeby odbiorników publicznych lub odbiorników prywatnych.

### <a name="protocol"></a>Protokół

Wybierz pozycję HTTP lub HTTPS:

- W przypadku wybrania protokołu HTTP ruch między klientem i bramą aplikacji jest niezaszyfrowany.

- Wybierz opcję HTTPS, jeśli chcesz, aby [SSL](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssltls-termination) lub [kompleksowe szyfrowanie SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Ruch między klientem i bramą aplikacji jest szyfrowany. Połączenie SSL kończy się na bramie aplikacji. Aby kompleksowe szyfrowanie SSL było wymagane, należy wybrać opcję HTTPS i skonfigurować ustawienia **protokołu HTTP zaplecza** . Gwarantuje to, że ruch jest ponownie szyfrowany podczas podróży z bramy aplikacji do zaplecza.

Aby skonfigurować zakończenie SSL i kompleksowe szyfrowanie SSL, należy dodać certyfikat do odbiornika, aby umożliwić bramie aplikacji uzyskanie klucza symetrycznego. Jest to podyktowane specyfikacją protokołu SSL. Klucz symetryczny służy do szyfrowania i odszyfrowywania ruchu wysyłanego do bramy. Certyfikat bramy musi być w formacie wymiany informacji osobistych (PFX). Ten format umożliwia wyeksportowanie klucza prywatnego, który jest wykorzystywany przez bramę do szyfrowania i odszyfrowywania ruchu.

#### <a name="supported-certificates"></a>Obsługiwane certyfikaty

Zobacz [Certyfikaty obsługiwane przez zakończenie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Obsługa dodatkowych protokołów

#### <a name="http2-support"></a>Obsługa HTTP2

Obsługa protokołu HTTP/2 jest dostępna dla klientów, którzy łączą się tylko z odbiornikami bramy aplikacji. Komunikacja z pulami serwerów zaplecza odbywa się za pośrednictwem protokołu HTTP/1.1. Domyślnie obsługa protokołu HTTP/2 jest wyłączona. Poniższy fragment kodu Azure PowerShell ilustruje, jak to włączyć:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Obsługa protokołu WebSocket

Obsługa protokołu WebSocket jest domyślnie włączona. Nie istnieje ustawienie konfigurowalne przez użytkownika, które można włączyć lub wyłączyć. Można używać obiektów WebSockets z odbiornikami HTTP i HTTPS.

### <a name="custom-error-pages"></a>Niestandardowe strony błędów

Błąd niestandardowy można zdefiniować na poziomie globalnym lub na poziomie odbiornika. Jednak tworzenie niestandardowych stron błędów niestandardowych na poziomie globalnym na podstawie Azure Portal nie jest obecnie obsługiwane. Można skonfigurować niestandardową stronę błędów dla błędu zapory aplikacji sieci Web 403 lub strony obsługi 502 na poziomie odbiornika. Należy również określić publicznie dostępny adres URL obiektu BLOB dla danego kodu stanu błędu. Aby uzyskać więcej informacji, zobacz [Create Application Gateway custom error pages (Tworzenie niestandardowych stron błędów w usłudze Application Gateway)](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Application Gateway kody błędów](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Aby skonfigurować globalną stronę błędu niestandardowego, zobacz [konfiguracja Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>Zasady SSL

Można scentralizować zarządzanie certyfikatami SSL i zmniejszyć obciążenie odszyfrowywania szyfrowania dla farmy serwerów zaplecza. Scentralizowana obsługa protokołu SSL umożliwia również określenie centralnych zasad protokołu SSL, które są odpowiednie do wymagań dotyczących zabezpieczeń. Można wybrać *domyślne*, *wstępnie zdefiniowane*lub *niestandardowe* zasady protokołu SSL.

Należy skonfigurować zasady protokołu SSL w celu kontrolowania wersji protokołu SSL. Bramę aplikacji można skonfigurować tak, aby korzystała z minimalnej wersji protokołu dla uzgadniania TLS z protokołów TLS 1.0, TLS 1.1 i TLS 1.2. Domyślnie protokoły SSL 2,0 i 3,0 są wyłączone i nie można ich konfigurować. Aby uzyskać więcej informacji, zobacz [Application Gateway Omówienie zasad protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Po utworzeniu odbiornika należy skojarzyć go z regułą routingu żądania. Ta reguła określa, jak żądania odbierane w odbiorniku są kierowane do zaplecza.

## <a name="request-routing-rules"></a>Reguły routingu żądań

Podczas tworzenia bramy aplikacji przy użyciu Azure Portal należy utworzyć regułę domyślną (*RULE1*). Ta reguła wiąże domyślny odbiornik (*appGatewayHttpListener*) z domyślną pulą zaplecza (*appGatewayBackendPool*) i domyślnymi ustawieniami http zaplecza (*appGatewayBackendHttpSettings*). Po utworzeniu bramy można edytować ustawienia reguły domyślnej lub utworzyć nowe reguły.

### <a name="rule-type"></a>Typ reguły

Podczas tworzenia reguły wybiera się między [ *podstawową* a *opartą na ścieżce*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Wybierz pozycję podstawowa, jeśli chcesz przesyłać dalej wszystkie żądania na skojarzonym odbiorniku (na *przykład<i></i>blog. contoso.com/\*)* do jednej puli zaplecza.
- Wybierz pozycję oparta na ścieżce, jeśli chcesz kierować żądania z określonych ścieżek URL do określonych pul zaplecza. Wzorzec ścieżki jest stosowany tylko do ścieżki adresu URL, a nie do parametrów zapytania.

#### <a name="order-of-processing-rules"></a>Kolejność reguł przetwarzania

W przypadku jednostki SKU V1 dopasowanie wzorców żądań przychodzących jest przetwarzane w kolejności, w jakiej ścieżki są wyświetlane na mapie ścieżki URL reguły opartej na ścieżce. Jeśli żądanie pasuje do wzorca w co najmniej dwóch ścieżkach w mapie ścieżki, ścieżka, która jest wymieniona jako pierwsza jest dopasowana. A żądanie jest przekazywane do zaplecza, który jest skojarzony z tą ścieżką.

W przypadku jednostki SKU v2 dokładne dopasowanie jest wyższym priorytetem niż kolejność ścieżki w mapie ścieżki URL. Jeśli żądanie pasuje do wzorca w co najmniej dwóch ścieżkach, żądanie jest przekazywane do zaplecza, który jest skojarzony ze ścieżką dokładnie zgodną z żądaniem. Jeśli ścieżka w żądaniu przychodzącym nie dokładnie pasuje do żadnej ścieżki na mapie, dopasowanie wzorca żądania jest przetwarzane na liście kolejności mapowania ścieżki dla reguły opartej na ścieżce.

### <a name="associated-listener"></a>Skojarzony odbiornik

Skojarz odbiornik z regułą, tak aby *reguła routingu żądań* skojarzona z odbiornikiem była oceniana w celu określenia puli zaplecza, do której ma zostać rozesłane żądanie.

### <a name="associated-back-end-pool"></a>Skojarzona Pula zaplecza

Skojarz z regułą Pula zaplecza zawierającą elementy docelowe zaplecza, które obsługują żądania odbierane przez odbiornik.

 - W przypadku podstawowej reguły dozwolony jest tylko jedna pula zaplecza. Wszystkie żądania na skojarzonym odbiorniku są przekazywane do tej puli zaplecza.

 - Dla reguły opartej na ścieżce Dodaj wiele pul zaplecza odpowiadających każdej ścieżce URL. Żądania zgodne z podaną ścieżką URL są przekazywane do odpowiedniej puli zaplecza. Ponadto Dodaj domyślną pulę zaplecza. Żądania, które nie pasują do żadnej ścieżki URL w regule, są przekazywane do tej puli.

### <a name="associated-back-end-http-setting"></a>Skojarzone ustawienie protokołu HTTP zaplecza

Dodaj ustawienia protokołu HTTP zaplecza dla każdej reguły. Żądania są kierowane z bramy aplikacji do obiektów docelowych zaplecza przy użyciu numeru portu, protokołu i innych informacji określonych w tym ustawieniu.

W przypadku podstawowej reguły dozwolone jest tylko jedno ustawienie HTTP zaplecza. Wszystkie żądania na skojarzonym odbiorniku są przekazywane do odpowiednich obiektów docelowych zaplecza przy użyciu tego ustawienia protokołu HTTP.

Dla reguły opartej na ścieżce Dodaj wiele ustawień protokołu HTTP zaplecza, które odpowiadają każdej ścieżce URL. Żądania zgodne ze ścieżką URL w tym ustawieniu są przekazywane do odpowiednich obiektów docelowych zaplecza przy użyciu ustawień HTTP, które odpowiadają każdej ścieżce URL. Ponadto Dodaj domyślne ustawienie protokołu HTTP. Żądania, które nie pasują do żadnej ścieżki URL w tej regule, są przekazywane do domyślnej puli zaplecza przy użyciu domyślnego ustawienia protokołu HTTP.

### <a name="redirection-setting"></a>Ustawienie przekierowania

W przypadku skonfigurowania przekierowania dla podstawowej reguły wszystkie żądania na skojarzonym odbiorniku są przekierowywane do obiektu docelowego. To jest przekierowanie *globalne* . Jeśli przekierowanie jest skonfigurowane dla reguły opartej na ścieżce, przekierowywane są tylko żądania w określonym obszarze witryny. Przykładem jest obszar koszyka zakupów, który jest określany przez */cart/\** . Jest to przekierowanie *oparte na ścieżce* .

Aby uzyskać więcej informacji na temat przekierowań, zobacz [Omówienie przekierowania Application Gateway](redirect-overview.md).

#### <a name="redirection-type"></a>Typ przekierowania

Wybierz wymagany typ przekierowania: *trwały (301)* , *tymczasowy (307)* , *znaleziono (302)* lub *Zobacz inne (303)* .

#### <a name="redirection-target"></a>Cel przekierowania

Wybierz inny odbiornik lub lokację zewnętrzną jako cel przekierowania.

##### <a name="listener"></a>Odbiornika

Wybierz odbiornik jako miejsce docelowe przekierowania, aby przekierować ruch z jednego odbiornika do innego na bramie. To ustawienie jest wymagane, jeśli chcesz włączyć przekierowywanie HTTP-to-HTTPS. Przekierowuje ruch z odbiornika źródłowego, który sprawdza przychodzące żądania HTTP do odbiornika docelowego, który sprawdza przychodzące żądania HTTPS. Można również dołączyć ciąg zapytania i ścieżkę z oryginalnego żądania w żądaniu, które jest przekazywane do docelowego przekierowania.

![Składnik Application Gateway — okno dialogowe](./media/configuration-overview/configure-redirection.png)

Aby uzyskać więcej informacji na temat przekierowania protokołu HTTP-HTTPS, zobacz:
- [Przekierowanie z protokołu HTTP do HTTPS przy użyciu Azure Portal](redirect-http-to-https-portal.md)
- [Przekierowanie z protokołu HTTP do HTTPS przy użyciu programu PowerShell](redirect-http-to-https-powershell.md)
- [Przekierowanie z protokołu HTTP do HTTPS przy użyciu interfejsu wiersza polecenia platformy Azure](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Lokacja zewnętrzna

Wybierz opcję lokacja zewnętrzna, jeśli chcesz przekierować ruch do odbiornika skojarzonego z tą regułą do zewnętrznej lokacji. Możesz dołączyć ciąg zapytania z oryginalnego żądania w żądaniu, które jest przekazywane do docelowego przekierowania. Nie można przesłać dalej ścieżki do zewnętrznej witryny, która była w oryginalnym żądaniu.

Aby uzyskać więcej informacji na temat przekierowania, zobacz:
- [Przekierowywanie ruchu do lokacji zewnętrznej przy użyciu programu PowerShell](redirect-external-site-powershell.md)
- [Przekierowywanie ruchu do lokacji zewnętrznej przy użyciu interfejsu wiersza polecenia](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>Ponowne zapisywanie ustawienia nagłówka HTTP

To ustawienie powoduje dodanie, usunięcie lub aktualizację nagłówków żądań i odpowiedzi HTTP, podczas gdy pakiety żądań i odpowiedzi przechodzą między klientami a pulami zaplecza. Aby uzyskać więcej informacji, zobacz:

 - [Przegląd ponownego zapisywania nagłówków HTTP](rewrite-http-headers.md)
 - [Konfigurowanie ponownego zapisywania nagłówka HTTP](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>Ustawienia protokołu HTTP

Brama aplikacji kieruje ruch do serwerów zaplecza przy użyciu konfiguracji określonej w tym miejscu. Po utworzeniu ustawienia protokołu HTTP należy je skojarzyć z co najmniej jedną regułą routingu żądania.

### <a name="cookie-based-affinity"></a>Koligacja na podstawie pliku cookie

Usługa Azure Application Gateway używa plików cookie zarządzanych przez bramę do obsługi sesji użytkowników. Gdy użytkownik wysyła pierwsze żądanie do Application Gateway, ustawia plik cookie koligacji w odpowiedzi z wartością skrótu, która zawiera szczegóły sesji, dzięki czemu kolejne żądania przenoszące plik cookie koligacji będą kierowane do tego samego serwera wewnętrznej bazy danych dla Obsługa lepkość. 

Ta funkcja jest przydatna, gdy chcesz zachować sesję użytkownika na tym samym serwerze, a stan sesji jest zapisywany lokalnie na serwerze na potrzeby sesji użytkownika. Jeśli aplikacja nie może obsłużyć koligacji opartej na plikach cookie, nie można użyć tej funkcji. Aby go użyć, należy się upewnić, że klienci obsługują pliki cookie.

[Aktualizacja V80](https://chromiumdash.appspot.com/schedule) w [przeglądarce chrom](https://www.chromium.org/Home) została przypisana, gdzie pliki cookie HTTP bez atrybutu [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) musi być traktowany jako SameSite = swobodny. W przypadku żądań CORS (współużytkowanie zasobów między źródłami), jeśli plik cookie musi zostać wysłany w kontekście innej firmy, musi używać *SameSite = none; Bezpieczne* atrybuty i powinny być wysyłane tylko za pośrednictwem protokołu HTTPS. W przeciwnym razie w scenariuszu tylko HTTP przeglądarka nie wysyła plików cookie w kontekście innej firmy. Celem tej aktualizacji z programu Chrome jest podwyższenie poziomu zabezpieczeń oraz uniknięcie ataków na wiele witryn (CSRF). 

Aby obsłużyć tę zmianę, od 17 lutego 2020 Application Gateway (wszystkie typy jednostek SKU) będą wstrzyknąć inny plik cookie o nazwie *ApplicationGatewayAffinityCORS* , a także istniejący plik cookie *ApplicationGatewayAffinity* . Plik cookie *ApplicationGatewayAffinityCORS* ma dwa więcej atrybutów ( *"SameSite = none; Secure "* ), aby sesja programu Sticky Notes była utrzymywana nawet w przypadku żądań między źródłami.

Należy pamiętać, że domyślna nazwa pliku cookie koligacji to *ApplicationGatewayAffinity* i można ją zmienić. Jeśli używasz niestandardowej nazwy pliku cookie koligacji, dodatkowy plik cookie jest dodawany przy użyciu mechanizmu CORS jako sufiks. Na przykład *CustomCookieNameCORS*.

> [!NOTE]
> Jeśli atrybut *SameSite = none* jest ustawiony, wymagane jest, aby plik cookie zawierał także flagę *Secure* , i musi być wysyłany za pośrednictwem protokołu HTTPS.  Jeśli koligacja sesji jest wymagana za pośrednictwem mechanizmu CORS, należy przeprowadzić migrację obciążenia do protokołu HTTPS. Zapoznaj się z dokumentacją dotyczącą odciążania protokołu SSL i kompleksowej dokumentacji protokołu SSL dla Application Gateway tym miejscu — [Omówienie](ssl-overview.md), [jak skonfigurować odciążanie protokołu SSL](create-ssl-portal.md), [jak skonfigurować kompleksowy protokół SSL](end-to-end-ssl-portal.md).

### <a name="connection-draining"></a>Opróżnianie połączeń

Opróżnianie połączeń pomaga bezpiecznie usunąć członków puli zaplecza podczas aktualizacji planowanych usług. To ustawienie można zastosować do wszystkich elementów członkowskich puli zaplecza podczas tworzenia reguły. Gwarantuje to, że wszystkie wyrejestrujące się wystąpienia puli zaplecza nadal utrzymują istniejące połączenia i obsługują żądania w celu skonfigurowania limitu czasu i nie otrzymają żadnych nowych żądań ani połączeń. Jedynym wyjątkiem są żądania związane z wyrejestrowywaniem wystąpień z powodu koligacji sesji zarządzanej przez bramę i będzie on nadal przekazywany do wyrejestrowania wystąpień. Opróżnianie połączeń dotyczy wystąpień zaplecza, które są jawnie usuwane z puli zaplecza.

### <a name="protocol"></a>Protokół

Application Gateway obsługuje zarówno protokół HTTP, jak i HTTPS w przypadku żądań routingu do serwerów zaplecza. W przypadku wybrania protokołu HTTP ruch do serwerów zaplecza jest niezaszyfrowany. Jeśli nieszyfrowana komunikacja nie jest akceptowalna, wybierz pozycję HTTPS.

To ustawienie połączone z protokołem HTTPS w odbiorniku obsługuje [kompleksowy protokół SSL](ssl-overview.md). Dzięki temu można bezpiecznie przesyłać poufne dane zaszyfrowane do zaplecza. Każdy serwer zaplecza w puli zaplecza z włączonym kompleksowym protokołem SSL musi być skonfigurowany przy użyciu certyfikatu w celu umożliwienia bezpiecznej komunikacji.

### <a name="port"></a>Port

To ustawienie określa port, w którym serwery zaplecza nasłuchują ruchu z bramy aplikacji. Można skonfigurować porty z zakresu od 1 do 65535.

### <a name="request-timeout"></a>Limit czasu żądania

To ustawienie określa liczbę sekund oczekiwania przez bramę aplikacji na odebranie odpowiedzi z serwera zaplecza.

### <a name="override-back-end-path"></a>Zastąp ścieżkę zaplecza

To ustawienie umożliwia skonfigurowanie opcjonalnej niestandardowej ścieżki przesyłania dalej do użycia, gdy żądanie jest przekazywane do zaplecza. Dowolna część ścieżki przychodzącej zgodna ze ścieżką niestandardową w polu **Przesłaniaj ścieżkę zaplecza** jest kopiowana do przekazanej ścieżki. W poniższej tabeli przedstawiono sposób działania tej funkcji:

- Gdy ustawienie protokołu HTTP jest dołączone do podstawowej reguły routingu żądania:

  | Oryginalne żądanie  | Zastąp ścieżkę zaplecza | Żądanie przekazane do zaplecza |
  | ----------------- | --------------------- | ---------------------------- |
  | mowa            | opcją/override.            | /override/home/              |
  | /home/secondhome/ | opcją/override.            | /override/home/secondhome/   |

- Po dołączeniu ustawienia protokołu HTTP do reguły routingu żądania opartej na ścieżce:

  | Oryginalne żądanie           | Reguła ścieżki       | Zastąp ścieżkę zaplecza | Żądanie przekazane do zaplecza |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | elemencie pathrule      | opcją/override.            | /override/home/              |
  | /pathrule/home/secondhome/ | elemencie pathrule      | opcją/override.            | /override/home/secondhome/   |
  | mowa                     | elemencie pathrule      | opcją/override.            | /override/home/              |
  | /home/secondhome/          | elemencie pathrule      | opcją/override.            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | opcją/override.            | opcją/override.                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | opcją/override.            | /override/secondhome/        |
  | elemencie pathrule                 | elemencie pathrule      | opcją/override.            | opcją/override.                   |

### <a name="use-for-app-service"></a>Korzystanie z usługi App Service

Jest to skrót tylko do interfejsu użytkownika, który wybiera dwa wymagane ustawienia dla Azure App Service zaplecza. Umożliwia **wybranie nazwy hosta z adresu zaplecza**i utworzenie nowej sondy niestandardowej, jeśli nie została ona jeszcze zainstalowana. (Aby uzyskać więcej informacji, zobacz sekcję [Wybieranie nazwy hosta z ustawienia adres zaplecza w](#pick) tym artykule). Zostanie utworzona nowa sonda, a nagłówek sondy jest wybierany z adresu członka zaplecza.

### <a name="use-custom-probe"></a>Użyj sondy niestandardowej

To ustawienie kojarzy [niestandardową sondę](application-gateway-probe-overview.md#custom-health-probe) z USTAWIENIEM protokołu HTTP. Można skojarzyć tylko jedną sondę niestandardową z ustawieniem protokołu HTTP. Jeśli nie utworzysz jawnie niestandardowej sondy, do monitorowania kondycji zaplecza służy [sonda domyślna](application-gateway-probe-overview.md#default-health-probe-settings) . Zalecamy utworzenie niestandardowej sondy do większej kontroli nad monitorowaniem kondycji zaplecza.

> [!NOTE]
> Niestandardowa Sonda nie monitoruje kondycji puli zaplecza, chyba że odpowiednie ustawienie protokołu HTTP jest jawnie skojarzone z odbiornikiem.

### <a id="pick"/></a>wybierz nazwę hosta z adresu zaplecza

Ta funkcja dynamicznie ustawia nagłówek *hosta* w żądaniu na nazwę hosta puli zaplecza. Używa adresu IP lub nazwy FQDN.

Ta funkcja pomaga, gdy nazwa domeny zaplecza różni się od nazwy DNS bramy aplikacji, a zaplecze opiera się na określonym nagłówku hosta, aby rozwiązać prawidłowy punkt końcowy.

Przykładem przypadku są usługi wielodostępne jako zaplecze. Usługa App Service to usługa z wieloma dzierżawami, która używa udostępnionego miejsca z pojedynczym adresem IP. Z tego względu dostęp do usługi App Service można uzyskać tylko za pomocą nazw hostów skonfigurowanych w niestandardowych ustawieniach domeny.

Domyślnie niestandardowa nazwa domeny to *example.azurewebsites.NET*. Aby uzyskać dostęp do usługi App Service przy użyciu bramy aplikacji za pomocą nazwy hosta, która nie jest jawnie zarejestrowana w usłudze App Service lub za pośrednictwem nazwy FQDN bramy aplikacji, należy zastąpić nazwę hosta w oryginalnym żądaniu do nazwy hosta usługi App Service. W tym celu należy włączyć ustawienie **Wybierz nazwę hosta z adresu zaplecza** .

W przypadku domeny niestandardowej, której istniejąca niestandardowa nazwa DNS jest zamapowana na usługę App Service, nie trzeba włączać tego ustawienia.

> [!NOTE]
> To ustawienie nie jest wymagane w przypadku App Service Environment, który jest wdrożeniem dedykowanym.

### <a name="host-name-override"></a>Zastąpienie nazwy hosta

Ta funkcja zastępuje nagłówek *hosta* w żądaniu przychodzącym w bramie aplikacji z określoną nazwą hosta.

Na przykład jeśli *www.contoso.com* jest określony w ustawieniu **Nazwa hosta** , oryginalne żądanie * https://appgw.eastus.cloudapp.azure.com/path1 zostanie zmienione na * https://www.contoso.com/path1, gdy żądanie jest przekazywane do serwera zaplecza.

## <a name="back-end-pool"></a>Pula zaplecza

Pulę zaplecza można wskazać na cztery typy elementów członkowskich zaplecza: konkretną maszynę wirtualną, zestaw skalowania maszyn wirtualnych, adres IP/nazwa FQDN lub Usługa aplikacji. Każda pula zaplecza może wskazywać na wiele elementów członkowskich tego samego typu. Wskazanie składowych różnych typów w tej samej puli zaplecza nie jest obsługiwane.

Po utworzeniu puli zaplecza należy ją skojarzyć z co najmniej jedną regułą routingu żądania. Należy również skonfigurować sondy kondycji dla każdej puli zaplecza na bramie aplikacji. Gdy spełniony jest warunek reguły routingu żądania, Brama aplikacji przekazuje ruch do serwerów w dobrej kondycji (zgodnie z sondami kondycji) w odpowiedniej puli zaplecza.

## <a name="health-probes"></a>Sondy kondycji

Brama aplikacji domyślnie monitoruje kondycję wszystkich zasobów w zapleczu. Jednak zdecydowanie zalecamy utworzenie niestandardowej sondy dla każdego ustawienia HTTP zaplecza, aby uzyskać większą kontrolę nad monitorowaniem kondycji. Aby dowiedzieć się, jak skonfigurować sondę niestandardową, zobacz [niestandardowe ustawienia sondy kondycji](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Po utworzeniu niestandardowej sondy kondycji należy ją skojarzyć z ustawieniem HTTP zaplecza. Niestandardowa Sonda nie będzie monitorować kondycji puli zaplecza, chyba że odpowiednie ustawienie protokołu HTTP jest jawnie skojarzone z odbiornikiem przy użyciu reguły.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już o składnikach Application Gateway, możesz:

- [Tworzenie bramy aplikacji w Azure Portal](quick-create-portal.md)
- [Tworzenie bramy aplikacji przy użyciu programu PowerShell](quick-create-powershell.md)
- [Tworzenie bramy aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)
