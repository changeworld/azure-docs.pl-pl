---
title: Składniki bramy aplikacji platformy Azure
description: Ten artykuł zawiera informacje na temat różnych składników w usłudze Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 44c8b331ebb258c39a003c91e0711e6dfb87cb12
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076301"
---
# <a name="application-gateway-components"></a>Składniki bramy aplikacji

 Bramy aplikacji służy jako pojedynczy punkt kontaktu dla klientów. Je dystrybuuje ruch przychodzący aplikacji dla wielu pul zaplecza, takich jak maszyny wirtualne platformy Azure, zestawy skalowania maszyn wirtualnych, App Services lub na lokalne/zewnętrznych serwerów. Aby to zrobić, używa kilka składników, które opisano w tym artykule.

![application-gateway-components](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-address"></a>Adres IP frontonu

Adres IP frontonu jest adres IP skojarzony z usługą application gateway. Można skonfigurować bramę aplikacji, albo mieć publiczny adres IP lub prywatnego adresu IP lub obu. Obsługiwane jest tylko jeden publiczny adres IP lub jeden prywatny adres IP. Sieć wirtualna i publiczny adres IP musi być w tej samej lokalizacji co Twoja brama application gateway.

Adres IP frontonu jest skojarzona z *odbiornika* po jego utworzeniu. 

### <a name="static-vs-dynamic-public-ip-address"></a>Dynamicznego publicznego adresu IP statycznej

Jednostka SKU v1 obsługuje statyczne wewnętrzne adresy IP, ale nie obsługuje statyczne publiczne adresy IP. Wirtualne adresy IP można zmienić bramy application gateway jest zatrzymana i uruchomiona. Nazwa DNS skojarzone z usługą application gateway nie zmienia się z cyklem bramy. Z tego powodu zaleca się użycie aliasów CNAME i wskaż adresu DNS bramy aplikacji.

Usługa Application Gateway jednostkę SKU v2 obsługuje statyczne publiczne adresy IP, a także statyczne adresy IP wewnętrznej. 

## <a name="listeners"></a>Odbiorniki

Przed rozpoczęciem przy użyciu bramy application gateway, należy dodać co najmniej jednego odbiornika. Odbiornik jest logicznej jednostki, która sprawdza, czy przychodzące żądania połączeń i akceptuje żądania, jeśli protokół, port, hosta i adresu IP skojarzone z dopasowaniem żądania przy użyciu protokołu, portu, hosta i adres IP skojarzony z konfiguracji odbiornika. Może istnieć wiele odbiorników dołączonego do bramy aplikacji i mogą być używane dla tego samego protokołu. Po odbiornik wykryje żądań przychodzących od klientów, usługa Application Gateway kieruje żądania do elementów członkowskich w puli zaplecza przy użyciu reguł routingu żądania, które definiują dla odbiornika, który otrzymał żądanie przychodzące.

Odbiorniki obsługują następujące porty i protokoły:

### <a name="ports"></a>Porty

Jest to port, na którym nasłuchuje odbiornik dla żądania klienta. Można skonfigurować porty z zakresu od 1 do 65502 dla jednostki SKU w wersji 1 i 1 do 65199 dla jednostek SKU V2.

### <a name="protocols"></a>Protokoły

Usługa Application gateway obsługuje następujące cztery Protokoły: HTTP, HTTPS i HTTP/2 oraz protokołu WebSocket

Możesz jawnie określić wybór między protokołów HTTP i HTTPS, w konfiguracji odbiornika. [Obsługi protokołu Websocket i protokołu HTTP/2](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) udostępnianych w sposób natywny. [Obsługa protokołu Websocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) jest domyślnie włączona. Nie ma żadnych ustawień konfigurowanych przez użytkownika umożliwiających selektywne włączenie lub wyłączenie obsługi protokołu WebSocket. Za pomocą funkcji WebSockets odbiorników HTTP i HTTPS. Obsługa protokołu HTTP/2 jest dostępna dla klientów łączących się tylko odbiorników bramy aplikacji. Komunikacja do pul serwerów zaplecza jest za pośrednictwem protokołu HTTP/1.1. Domyślnie obsługa protokołu HTTP/2 jest wyłączona. Można ją włączyć.

Kończenie żądań SSL umożliwiają odbiornika protokołu HTTPS. Odbiornik HTTPS rozwiązania odciążania, szyfrowania i odszyfrowywania współpracują, aby Twoja brama application gateway, aby nie obciążać serwerów sieci web, przy tym koszty. Aplikacje są następnie skupić się na logice biznesowej.

### <a name="custom-error-pages"></a>Niestandardowe strony błędów

Usługa Application gateway umożliwia tworzenie strony błędów niestandardowych zamiast domyślnej strony błędów. W przypadku niestandardowych stron błędów możesz użyć własnych oznakowań i układu. Usługa Application gateway mogą wyświetlać niestandardowej strony błędu, gdy żądanie nie może nawiązać połączenia wewnętrznej bazy danych. Aby uzyskać więcej informacji, zobacz [strony błędów niestandardowych dla bramy Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Typy obiektów nasłuchujących

Istnieją dwa rodzaje odbiorniki:

- **Podstawowe**: Ten typ odbiornik nasłuchuje lokacji jednej domeny, który ma jedno mapowanie DNS do adresu IP bramy aplikacji. Ta konfiguracja odbiornika jest wymagana, gdy obsługujesz jednej witryny za bramą aplikacji.
- **Połączenia obejmujące wiele lokacji**: Ta konfiguracja odbiornika jest wymagana, gdy konfigurujesz więcej niż jednej aplikacji sieci web na tym samym wystąpieniu bramy aplikacji. Umożliwia skonfigurowanie bardziej wydajnej topologii dla wdrożeń przez dodanie maksymalnie 100 witryn sieci Web do jednej bramy aplikacji. Każdą witrynę sieci Web można skierować do jej puli zaplecza. Na przykład:  Trzy poddomen — abc.contoso.com, xyz.contoso.com i pqr.contoso.com, wskazując na adres IP bramy aplikacji. Utwórz trzy odbiorników typu *obejmujące wiele lokacji* i skonfigurować każdego odbiornika dla odpowiednich portu i protokołu ustawienie. Aby uzyskać więcej informacji, zobacz [hostowanie wielu witryn](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Po utworzeniu odbiornika, należy ją skojarzyć z reguły routingu żądania, która określa, jak żądanie odebrane na odbiornik ma być kierowany do wewnętrznej bazy danych.

Odbiorniki są przetwarzane w kolejności, w której są wyświetlane. Z tego powodu jeśli podstawowy odbiornik pasuje do przychodzącego żądania przetwarza je najpierw. Odbiorników obejmujących wiele lokacji, należy skonfigurować przed odbiornika podstawowego, aby upewnić się, że ruch jest kierowany do poprawne zaplecza.

## <a name="request-routing-rule"></a>Reguła routingu żądania

To jest najważniejszym składnikiem usługi application gateway i określa, jak odbywa się ruchu na odbiorniku skojarzone z tą regułą. Reguła wiąże odbiornik, puli serwerów zaplecza i ustawienia HTTP zaplecza. Po zaakceptowaniu żądania przez odbiornik reguły routingu żądań określa, czy żądanie jest przekazywane do wewnętrznej bazy danych lub przekierowanie w innym miejscu. Jeśli żądanie zostanie uznane za przekazywane do zaplecza, reguły routingu żądań definiuje której puli serwerów zaplecza powinien on być przekazywany do. Ponadto reguły routingu żądań określa również, czy mają zostać przepisany z nagłówków w żądaniu. Jeden odbiornik może być dołączony do tylko jedną regułę.

Może to być dwa typy reguł rozsyłania żądań:

- **Podstawowa:** Wszystkie żądania na skojarzony odbiornik (na przykład: blog.contoso.com/*) są przekazywane do puli zaplecza skojarzone przy użyciu skojarzone ustawienia protokołu HTTP.
- **Oparty na ścieżkach:** Ten typ reguły umożliwia kierowanie żądań na skojarzony odbiornik z pulą zaplecza określonych na podstawie adresu URL w żądaniu. Jeśli ścieżka adresu URL w żądaniu pasuje wzorzec ścieżki w regule opartego na ścieżkach, żądanie jest kierowane przy użyciu tej reguły. Wzorzec ścieżki jest stosowane tylko do ścieżki adresu URL, z jego parametrów zapytania. Jeśli ścieżka adresu URL żądania na odbiornik nie pasuje do żadnego z zasady na podstawie ścieżki, a następnie żądanie jest kierowane do *domyślne* puli zaplecza i *domyślne* ustawienia protokołu HTTP. Aby uzyskać więcej informacji, zobacz [routing oparty na adresach URL](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Obsługa przekierowania

Reguła routingu żądania umożliwia przekierowywanie ruchu w usłudze application gateway. Jest to ogólny mechanizm przekierowania, dzięki czemu możliwe jest przekierowanie z i do dowolnego portu zdefiniowanego przy użyciu reguł. Możesz wybierz miejsce docelowe przekierowania inny odbiornik, (które mogą pomóc włączyć automatyczne HTTP do przekierowania protokołu HTTPS) lub zewnętrznej witryny, wybierz przekierowania jako tymczasowy lub stały lub wybierz dołączanie ciągu identyfikatora URI ścieżkę i zapytanie do adresu URL. Aby uzyskać więcej informacji, zobacz [przekierowywanie ruchu na Twojej bramy Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="rewrite-http-headers"></a>Ponowne zapisywanie nagłówków HTTP

Za pomocą żądania reguł routingu żądania można dodać, usunąć lub zaktualizować HTTP (S) i nagłówkami odpowiedzi podczas żądania, oraz pakiety odpowiedzi, przenoszenie między pulami klienta i zaplecza za pośrednictwem bramy aplikacji. Nagłówki nie można ustawić tylko wartości statyczne, ale także innych nagłówków i zmiennych serwera ważne. Dzięki temu łatwiej wykonać kilka ważnych zastosowań, takich jak wyodrębnianie adres IP klientów, usunięcie poufnych informacji o wewnętrznej bazy danych, dodając dodatkowe środki bezpieczeństwa itp. Aby uzyskać więcej informacji, zobacz [nagłówków HTTP ponownego zapisywania w Twojej bramy application gateway](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>Ustawienia protokołu HTTP

Ruch trasy bramy aplikacji do wewnętrznej bazy danych, które serwery (określone w regule routingu żądania, do którego ustawienia HTTP są dołączone) przy użyciu numeru portu, protokołu i inne ustawienia określone w tej części. Port i protokół używany w ustawieniach protokołu HTTP należy określić, czy ruchu między serwerami bramy i wewnętrznej bazy danych aplikacji są szyfrowane, więc wykonywanie kompleksowej usługi SSL lub bez szyfrowania. Ten składnik jest również używane do: ustalić, czy sesja użytkownika jest mają być przechowywane na tym samym serwerze przy użyciu [koligacji sesji na podstawie pliku cookie](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity), wykonać łagodne usunięcie składowych puli zaplecza przy użyciu [połączenia opróżnianie](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining), skojarz niestandardowej sondy, aby monitorować kondycję wewnętrznej bazy danych, ustaw interwał limitu czasu żądania, zastąp nazwę hosta i ścieżkę w żądaniu i zapewnić jednym kliknięciem łatwo można określać ustawienia usługi zaplecza dla aplikacji usługi wewnętrznej bazy danych. 

## <a name="backend-pool"></a>Pula zaplecza

Pula zaplecza jest używany do kierowania żądań do serwerów wewnętrznej bazy danych, które obsłużyć żądania. Pule zaplecza może składać się z kartami sieciowymi, skalowania maszyny wirtualnej, zestawy, publicznych adresów IP adresy, wewnętrzny IP adresów, nazwy FQDN i wielodostępnych zapleczy, takich jak usługa Azure App Service. Składowych puli zaplecza bramy aplikacji nie są związane z zestawu dostępności. Usługa Application Gateway mogą komunikować się z wystąpieniami poza siecią wirtualną czy znajduje się w, dlatego może być członkami pule zaplecza, różnych klastrów i centrów danych lub poza platformą Azure, tak długo, jak istnieje łączność IP. Jeśli planujesz używać wewnętrznych adresów IP jako składowych puli zaplecza, a następnie wymaga [komunikacja równorzędna sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) lub [bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Komunikacja równorzędna sieci wirtualnych jest obsługiwana i jest korzystne dla równoważenia obciążenia ruchu w innych sieciach wirtualnych. Bramy application gateway można również przekazać za pomocą lokalnych serwerów łączenia tunele ExpressRoute lub sieci VPN, tak długo, jak ruch jest dozwolony.

Można tworzyć pule zaplecza różne dla różnych typów żądań. Na przykład utworzyć jedną pulę zaplecza dla ogólnych żądań i innych puli zaplecza dla żądań kierowanych do mikrousług dla aplikacji.

## <a name="health-probes"></a>Sondy kondycji

Usługa Application gateway domyślnie monitoruje kondycję wszystkich zasobów w puli zaplecza i automatycznie usuwa dowolnego zasobu z puli uznawana za złą. On w dalszym ciągu monitorować złej kondycji wystąpień i dodaje je do puli zaplecza w dobrej kondycji, gdy staną się dostępne i odpowiadać na sondy kondycji. Oprócz używania, sondowanie kondycji domyślnej funkcji monitorowania, można również dostosować sondę kondycji, aby odpowiadał wymaganiom Twojej aplikacji. Niestandardowe sondy umożliwiają zapewniają bardziej szczegółową kontrolę nad monitorowanie kondycji. Użycie sond niestandardowych, można skonfigurować interwał sondowania, adres URL i ścieżki do testowania i jak wiele odpowiedzi nie powiodło się, aby zaakceptować wystąpienie puli zaplecza jest oznaczony jako w złej kondycji. Zdecydowanie zaleca się, że możesz skonfigurować niestandardowe sondy do monitorowania kondycji każdej puli wewnętrznej bazy danych. Aby uzyskać więcej informacji, zobacz [monitorowania kondycji bramy Application gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się składniki bramy aplikacji, możesz wykonywać następujące czynności:
* [Tworzenie bramy aplikacji w witrynie Azure portal](quick-create-portal.md)
* [Tworzenie bramy aplikacji przy użyciu programu Azure PowerShell](quick-create-powershell.md)
* [Tworzenie bramy aplikacji przy użyciu wiersza polecenia platformy Azure](quick-create-cli.md)
