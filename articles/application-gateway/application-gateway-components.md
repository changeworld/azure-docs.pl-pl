---
title: Składniki bramy aplikacji platformy Azure
description: Ten artykuł zawiera informacje na temat różnych składników w usłudze Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 14f400a1b85e213496ac98996d6c2378cf559026
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675800"
---
# <a name="application-gateway-components"></a>Składniki bramy aplikacji

 Application Gateway służy jako pojedynczy punkt kontaktu dla klientów. Je dystrybuuje ruch przychodzący aplikacji dla wielu pul zaplecza, takich jak maszyny wirtualne platformy Azure, zestawu skalowania maszyn wirtualnych, App Services lub na lokalne/zewnętrznych serwerów. Aby to zrobić, dzięki narzędziom kilka składników, które opisano poniżej:

![application-gateway-components](.\media\application-gateway-components\application-gateway-components.png)

## <a name="frontend-ip"></a>Adresu IP frontonu

Fronted IP jest adres IP (Internet Protocol) skojarzony z usługą Application Gateway. Można skonfigurować bramę aplikacji, albo mieć publiczny adres IP, prywatny adres IP lub obu. Tylko jeden publiczny adres IP jest obsługiwany w bramie aplikacji. Sieć wirtualna i publiczny adres IP musi być w tej samej lokalizacji co Twoja brama Application Gateway.

### <a name="static-vs-dynamic-public-ip"></a>Statycznej dynamicznym publicznym adresem IP

Jednostka SKU v1 obsługuje statyczne wewnętrzne adresy IP, ale nie obsługuje statyczne publiczne adresy IP. Wirtualne adresy IP można zmienić bramy application gateway jest zatrzymana i uruchomiona. Nazwa DNS skojarzone z usługą application gateway nie zmienia się z cyklem bramy. Z tego powodu zaleca się użycie aliasów CNAME i wskaż adresu DNS bramy aplikacji.

Usługa Application Gateway jednostkę SKU v2 obsługuje statyczne publiczne adresy IP, a także jako wewnętrzne adresy IP. Tylko jeden publiczny adres IP jest obsługiwany w bramie aplikacji.

Po utworzeniu IP frontonu, jest on skojarzony z *odbiorników* który Wyszukaj żądania przychodzące na adresu IP frontonu.

## <a name="listeners"></a>Odbiorniki

Przed rozpoczęciem przy użyciu bramy Application Gateway, należy dodać co najmniej jednego odbiornika. Odbiornik jest procesem, za pomocą którego Application Gateway sprawdza, czy żądania przychodzące połączenia przy użyciu protokołu i portu, który można skonfigurować. Po odbiornik wykryje żądań przychodzących od klientów, usługa Application Gateway kieruje tych żądań do serwerów zaplecza w puli zaplecza przy użyciu reguł routingu żądania, które definiują dla odbiornika, który otrzymał żądanie przychodzące.

Odbiorniki obsługują następujące porty i protokoły:

### <a name="ports"></a>Porty

Jest to port, na którym nasłuchuje odbiornik na żądania klientów. Następujący zakres jest dozwolony dla konfiguracji portów: 1–65535

### <a name="protocols"></a>Protokoły

Usługa Application Gateway obsługuje następujące protokoły 4: HTTP i HTTPS, protokołu HTTP/2 Websocket

Podczas konfigurowania odbiornika, należy wybrać protokół HTTP i HTTPS. Usługa Application Gateway zapewnia macierzystą obsługę protokołu Websocket i protokołu HTTP/2. Za pomocą funkcji WebSockets odbiorników HTTP i HTTPS. 

Obsługa protokołu HTTP/2 jest dostępna dla klientów łączących się tylko odbiorników bramy aplikacji. Komunikacja do pul serwerów zaplecza jest za pośrednictwem protokołu HTTP/1.1. Domyślnie obsługa protokołu HTTP/2 jest wyłączona. Poniższy przykład fragmentu kodu programu Azure PowerShell pokazuje, jak możesz je włączyć:

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

Obsługa protokołu Websocket jest domyślnie włączona. Nie ma żadnych ustawień konfigurowanych przez użytkownika umożliwiających selektywne włączenie lub wyłączenie obsługi protokołu WebSocket.

Można użyć odbiornika protokołu HTTPS dla kończenia żądań SSL, który odciążania pracy szyfrowania i odszyfrowywania do Twojej bramy Application Gateway, dzięki czemu serwerów sieci web może z nadmiaru kosztownych szyfrowania i odszyfrowywania i aplikacji może skoncentrować się na ich Logika biznesowa. Należy wdrożyć co najmniej jednego certyfikatu serwera SSL dla odbiornika protokołu HTTPS. Aby uzyskać więcej informacji, zobacz [sposobu konfigurowania kończenia żądań SSL](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

### <a name="custom-error-pages"></a>Niestandardowe strony błędów

Usługa Application Gateway umożliwia tworzenie niestandardowych stron błędów wyświetlanych zamiast domyślnych strony błędów. W przypadku niestandardowych stron błędów możesz użyć własnych oznakowań i układu. Usługa Application gateway mogą wyświetlać niestandardowej strony błędu, gdy żądanie nie może nawiązać połączenia wewnętrznej bazy danych. Aby uzyskać więcej informacji, zobacz [strony błędów niestandardowych dla bramy Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error)

### <a name="types-of-listeners"></a>Typy obiektów nasłuchujących

Istnieją dwa rodzaje odbiorniki:

- **Podstawowe**: Ten typ odbiornik nasłuchuje lokacji jednej domeny, który ma jedno mapowanie DNS do adresu IP bramy aplikacji. Ta konfiguracja odbiornika jest wymagana w przypadku, gdy obsługujesz jednej witryny za bramą aplikacji
- **Połączenia obejmujące wiele lokacji**: Ta konfiguracja odbiornika jest wymagana, gdy w przypadku konfigurowania więcej niż jednej aplikacji sieci web w tym samym wystąpieniu bramy aplikacji. Umożliwia skonfigurowanie bardziej wydajnej topologii dla wdrożeń przez dodanie maksymalnie 100 witryn sieci Web do jednej bramy aplikacji. Każdą witrynę sieci Web można skierować do jej puli zaplecza. Na przykład:  Trzy poddomen — abc.alpha.com, xyz.alpha.com i pqr.alpha.com, wskazując na adres IP bramy aplikacji. Utwórz 3 odbiorników typu "obejmujące wiele lokacji" i skonfiguruj każdy odbiornika dla odpowiednich portów i ustawienia protokołu. 

Po utworzeniu odbiornika, należy ją skojarzyć z reguły routingu żądania, który określa, jak żądanie odebrane na odbiornik ma być kierowany do wewnętrznej bazy danych.

Odbiorniki są przetwarzane w kolejności, w której są wyświetlane. Dlatego jeśli podstawowy odbiornik pasuje do przychodzącego żądania przetwarza je najpierw. Odbiorników obejmujących wiele lokacji, należy skonfigurować przed odbiornika podstawowego, aby upewnić się, że ruch jest kierowany do poprawne zaplecza.

## <a name="request-routing-rule"></a>Reguła routingu żądania

To jest najważniejszym składnikiem usługi Application Gateway i określa, jak ma być kierowany ruchu na odbiorniku skojarzone z tą regułą. Reguła wiąże odbiornik i pula serwerów zaplecza i ustawienia HTTP zaplecza i umożliwia zdefiniowanie, do której puli serwerów zaplecza, powinny być przekierowywany ruch w przypadku trafienia do określonego odbiornika. Jeden odbiornik może być dołączony do tylko jedną regułę.

Może to być dwa typy reguł rozsyłania żądań:

- **Podstawowa:** Wszystkie żądania na skojarzony odbiornik (np: blog.contoso.com/*) są przekazywane do puli zaplecza skojarzone przy użyciu skojarzone ustawienia protokołu HTTP.
- **Oparty na ścieżkach:** Ten typ reguły zapewnia możliwość kierowanie żądań na skojarzony odbiornik z pulą zaplecza określonych na podstawie adresu URL w żądaniu. Jeśli ścieżka adresu URL w żądaniu pasuje wzorzec ścieżki w regule opartego na ścieżkach, żądanie jest kierowane przy użyciu tej reguły. Wzorzec ścieżki jest stosowane tylko do ścieżki adresu URL, z jego parametrów zapytania. 

Jeśli ścieżka adresu URL żądania na odbiornik nie pasuje do żadnego reguł opartych na ścieżkach, a następnie żądanie jest kierowane do *domyślne* puli zaplecza i *domyślne* ustawienia protokołu HTTP.

Reguły są przetwarzane w kolejności, w której są one konfigurowane. Zalecane jest, czy połączenia obejmujące wiele lokacji skonfigurowanych reguł przed podstawowych reguł, aby zmniejszyć prawdopodobieństwo, że ruch jest kierowany do zaplecza nieodpowiedni jako podstawowa zasada będzie odpowiadać na ruch na podstawie portu przed reguły obejmujące wiele lokacji oceniane.

### <a name="redirection-support"></a>Obsługa przekierowania

Reguła routingu żądania umożliwia przekierowywanie ruchu w usłudze Application Gateway. Jest to ogólny mechanizm przekierowania, dzięki czemu możliwe jest przekierowanie z i do dowolnego portu zdefiniowanego przy użyciu reguł. Może to ułatwić możesz włączyć automatyczne HTTP do przekierowania protokołu HTTPS do upewnij się, że cała komunikacja między aplikacją a użytkowników odbywa się za pośrednictwem ścieżką zaszyfrowane. Można określić odbiornik docelowy lub zewnętrznej witryny, do którego pożądany jest przekierowania. Element konfiguracji obsługuje również opcje umożliwiające dodanie ciągu identyfikatora URI ścieżkę i zapytanie do adresu URL. Możesz również wybrać, czy Przekierowanie tymczasowe (kod stanu HTTP 302) lub trwałe przekierowanie (kod stanu HTTP 301). Korzystając z podstawową regułę, konfiguracji przekierowania jest skojarzony z odbiornika źródła i globalne przekierowania. Stosowania reguły na podstawie ścieżki konfiguracji przekierowania jest zdefiniowana na mapie ścieżki adresu URL. Dlatego mają zastosowanie tylko do określonej ścieżki obszaru lokacji. Aby uzyskać więcej informacji, zobacz [przekierowywanie ruchu na Twojej bramy Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="http-headers"></a>Nagłówki HTTP

Usługa Application Gateway wstawia nagłówków x-forwarded dla x-forwarded-proto i x-forwarded-port na żądanie przekazywane do zaplecza. Format dla nagłówka x-forwarded dla jest IP:Port listę rozdzielonych przecinkami. Prawidłowe wartości dla x-forwarded-proto są http lub https. X-forwarded-port Określa port, w którym żądanie osiągnięty w usłudze application gateway. Usługa Application Gateway są wstawiane nagłówek X-oryginalny-Host, zawierający oryginalnego nagłówka hosta, z którym odebrano żądanie. Tego pliku nagłówkowego jest przydatne w scenariuszach, takich jak integracja z witryny internetowej platformy Azure, gdzie przychodzącego nagłówka hosta jest modyfikowany, zanim ruch jest kierowany do wewnętrznej bazy danych.

#### <a name="rewrite-http-headers"></a>Ponowne zapisywanie nagłówków HTTP

Za pomocą reguł routingu żądania, które można dodać, usunąć lub zaktualizować nagłówki żądania i odpowiedzi HTTP (S), gdy pakiety żądań i odpowiedzi przenieść między klientem i wewnętrznej bazy danych, pule, za pośrednictwem bramy aplikacji. Nagłówki nie można ustawić tylko wartości statyczne, ale także innych nagłówków i zmiennych serwera ważne. Dzięki temu łatwiej wykonać kilka ważnych zastosowań, takich jak wyodrębnianie adres IP klientów, usunięcie poufnych informacji o wewnętrznej bazy danych, dodając dodatkowe środki bezpieczeństwa itp. Aby uzyskać więcej informacji, zobacz[nagłówków HTTP ponownego zapisywania w Twojej bramy Application Gateway](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)

## <a name="http-settings"></a>Ustawienia HTTP

Application Gateway kieruje ruch do serwerów wewnętrznej bazy danych przy użyciu numeru portu, protokołu i inne ustawienia określone w tym zasobie. 

Pule zaplecza obsługują następujące porty i protokoły:

### <a name="ports"></a>Porty

Jest to port, na którym nasłuchują serwerów wewnętrznej bazy danych, aby ruch z bramy aplikacji. Następujący zakres jest dozwolony dla konfiguracji portów: 1–65535

### <a name="protocols"></a>Protokoły

Usługa Application Gateway obsługuje protokoły HTTP i HTTPS do kierowania żądań do serwerów wewnętrznej bazy danych.

Jeśli zostanie wybrana opcja protokołu HTTP, ruch sieciowy przepływów niezaszyfrowany do serwerów wewnętrznej bazy danych. 

W przypadkach, gdzie nieszyfrowana komunikacja z serwerami wewnętrznej bazy danych nie jest dopuszczalne należy wybrać protokół HTTPS. To ustawienie, połączone z Wybieranie protokołu HTTPS w odbiornik pozwala włączyć [kompleksowej usługi SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Która zezwoli na bezpieczne przesyłanie danych poufnych na zaplecze, szyfrowane. Każdy serwer zaplecza w puli zaplecza z włączoną kompleksową usługą SSL należy skonfigurować przy użyciu certyfikatu, aby umożliwić bezpieczną komunikację.

Przy użyciu ustawień HTTP można wykorzystać kilka możliwości:

### <a name="cookie-based-session-affinity"></a>Koligacja sesji na podstawie plików cookie

Ta funkcja jest przydatna, gdy chcesz zachować sesję użytkownika na tym samym serwerze. Używając plików cookie zarządzanych przez bramę, usługa Application Gateway może kierować dalszy ruch z sesji użytkownika na ten sam serwer w celu przetwarzania. Jest to ważne w przypadkach, w których stan sesji jest zapisywany lokalnie na serwerze dla sesji użytkownika.

Jeśli aplikacja nie może obsługiwać koligacji na podstawie plików cookie, następnie nie będzie mógł używać tej funkcji

### <a name="connection-draining"></a>Opróżnianie połączeń

Opróżnianie połączeń umożliwia bezproblemowe usunięcie członków puli zaplecza podczas planowanych aktualizacji usługi. To ustawienie można zastosować do wszystkich elementów członkowskich puli zaplecza podczas tworzenia reguł. Po włączeniu tego ustawienia usługa Application Gateway zapewnia, że żadne wyrejestrowane wystąpienia puli zaplecza nie otrzymują nowych żądań, ale jednocześnie umożliwia wykonanie istniejących żądań w ramach skonfigurowanego limitu czasu. Dotyczy to zarówno wystąpień zaplecza, które są jawnie usuwane z puli zaplecza przez wywołanie interfejsu API, jak i wystąpień zaplecza, które zostały zgłoszone jako niepoprawne według oceny sond kondycji.

### <a name="override-backend-path"></a>Zastąp ścieżkę zaplecza

Ta funkcja umożliwia zastąpienie ścieżki w adresie URL, dzięki czemu można kierować żądania do określonej ścieżki do inną ścieżkę. Na przykład, jeśli zamierza się kierować żądania do contoso.com/images domyślne, a następnie wprowadź "/" w tym polu tekstowym, a następnie dołączyć tym ustawieniem protokołu HTTP do reguły skojarzone z contoso.com/images.

### <a name="pick-host-name-from-a-backend-address"></a>Wybierz nazwę hosta z adresów zaplecza

Ta funkcja ustawia *hosta* nagłówka w żądaniu na nazwę hosta w puli zaplecza (IP lub nazwa FQDN). Jest to przydatne w scenariuszach, gdzie nazwa domeny wewnętrznej bazy danych różni się od nazwy DNS aplikacji, takich jak scenariusz użycia usługi Azure App Service jako zaplecza. Jest to spowodowane ponieważ środowisku z wieloma dzierżawami przy użyciu udostępnionego miejsca za pomocą pojedynczego adresu IP odbywa się w usłudze Azure App Service, usługi App Service jest możliwy tylko z nazw hostów, w ustawieniach domeny niestandardowej. Domyślnie jest "example.azurewebsites.net", a jeśli ma dostęp do usługi App Service przy użyciu bramy aplikacji przy użyciu nazwy hosta nie jest zarejestrowany w usłudze App Service lub za pomocą nazwy FQDN bramy Application Gateway, należy zastąpić nazwę hosta w oryginalne żądanie do aplikacji Nazwa hosta usługi.

### <a name="host-override"></a>**Zastąp hosta**

Ta funkcja zastępuje *hosta* nagłówka w żądaniu przychodzącym w usłudze Application Gateway dla nazwy hosta, określone w tym miejscu. 

Po utworzeniu ustawienie protokołu HTTP, należy skojarzyć je z jedną lub więcej żądań reguł routingu.

## <a name="backend-pool"></a>Pula zaplecza

Pula zaplecza jest używany do kierowania żądań do serwerów wewnętrznej bazy danych, które będą obsługująca żądanie. Pule zaplecza może składać się z kartami sieciowymi, zestawy skalowania maszyn wirtualnych, publiczne adresy IP, wewnętrzne adresy IP, w pełni kwalifikowaną nazwę (FQDN), a wielodostępne zaplecza takich jak usługa Azure App Service. Składowych puli zaplecza bramy aplikacji nie są związane z zestawu dostępności. Elementami członkowskimi pul zaplecza może być w klastrach, centrów danych lub spoza platformy Azure, tak długo, jak długo mają łączność adresów IP. Można tworzyć pule zaplecza różne dla różnych typów żądań. Na przykład utworzyć jedną pulę zaplecza dla ogólnych żądań i innych puli zaplecza dla żądań kierowanych do mikrousług dla aplikacji.

Po utworzeniu puli zaplecza należy ją skojarzyć z co najmniej jedną regułę routingu żądania. Należy również skonfigurować sond kondycji dla każdej puli wewnętrznej bazy danych w Twojej bramy Application Gateway. Po spełnieniu warunku reguły routingu żądania Application Gateway przekazuje ruch do dobrej kondycji serwerów (zgodnie z ustaleniami sond kondycji) w odpowiedniej puli zaplecza.

## <a name="health-probes"></a>Sondy kondycji

Usługa Azure Application Gateway domyślnie monitoruje kondycję wszystkich zasobów w puli zaplecza i automatycznie usuwa dowolnego zasobu z puli uznawana za złą. Usługa Application Gateway w dalszym ciągu monitorować złej kondycji wystąpień i dodaje je do puli zaplecza w dobrej kondycji, gdy staną się dostępne i odpowiadać na sondy kondycji. Usługa Application gateway wysyła sondy kondycji za pomocą tego samego portu, który jest zdefiniowany w ustawieniach HTTP zaplecza.

Oprócz używania, sondowanie kondycji domyślnej funkcji monitorowania, można również dostosować sondę kondycji, aby odpowiadał wymaganiom Twojej aplikacji. Niestandardowe sondy umożliwiają zapewniają bardziej szczegółową kontrolę nad monitorowanie kondycji. Użycie sond niestandardowych, można skonfigurować interwał sondowania, adres URL i ścieżki do testowania i jak wiele odpowiedzi nie powiodło się, aby zaakceptować przed oznaczeniem wystąpienia pulę zaplecza o złej kondycji. Zdecydowanie zaleca się, że możesz skonfigurować niestandardowe sondy do monitorowania kondycji każdej puli wewnętrznej bazy danych. Aby uzyskać więcej informacji, zobacz [monitorowania kondycji bramy Application gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)

## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się składniki bramy aplikacji możesz [można utworzyć bramę aplikacji w witrynie Azure portal](quick-create-portal.md) lub [Tworzenie bramy aplikacji przy użyciu programu PowerShell](quick-create-powershell.md) lub [tworzenie Bramy aplikacji przy użyciu wiersza polecenia platformy Azure](quick-create-cli.md).