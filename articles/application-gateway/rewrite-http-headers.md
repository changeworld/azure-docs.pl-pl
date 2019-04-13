---
title: Ponownie zapisuje nagłówki HTTP w usłudze Azure Application Gateway | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie możliwości, aby ponownie zapisuje nagłówki HTTP w usłudze Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/11/2019
ms.author: absha
ms.openlocfilehash: efb7b46919066beb1382d70b676a2115ea0fb8ac
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544156"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Ponownie zapisuje nagłówki HTTP z usługą Application Gateway (publiczna wersja zapoznawcza)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nagłówki HTTP umożliwiają klientowi i serwerowi przekazywanie dodatkowych informacji w ramach żądania lub odpowiedzi. Ponowne zapisywanie tych nagłówków HTTP ułatwia wykonywanie kilku ważnych scenariuszy, takich jak dodawanie pól nagłówka związanych z zabezpieczeniami, takich jak HSTS / X-XSS-ochrony, usunięcie nagłówka odpowiedzi pola, która może spowodować ujawnienie poufnych informacji, informacje o porcie oddzielającego z X-Forwarded-dla nagłówków itp. Usługa Application gateway obsługuje możliwość dodać, usunąć lub zaktualizować nagłówki żądania i odpowiedzi HTTP podczas żądania oraz pakiety odpowiedzi poruszanie się między klienta i wewnętrznej bazy danych w pulach. On również zapewnia możliwość dodawania warunków w celu zapewnienia, że określone nagłówki są przepisany tylko wtedy, gdy zostaną spełnione określone warunki.
> [!NOTE]
>
> Obsługa ponownego napisania nagłówka HTTP jest dostępna tylko dla [nowej jednostki SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

## <a name="headers-supported-for-rewrite"></a>Nagłówki obsługiwane w przypadku ponownego zapisywania

Ta funkcja umożliwia ponownie zapisuje wszystkie nagłówki żądania i odpowiedzi, wyłączając nagłówki hosta, połączenia i uaktualnianie. Brama aplikacji umożliwia również tworzenie niestandardowych nagłówków i dodać je do żądania i odpowiedzi jest kierowany przez nią. 

## <a name="rewrite-conditions"></a>Warunki ponownego napisania

Za pomocą ponownego zapisywania, którego warunki oceny treści żądania HTTP (S) i odpowiedzi oraz wykonywać nagłówek Napisz ponownie tylko wtedy, gdy spełnione są co najmniej jeden warunek. 3 następujące rodzaje zmiennych są używane przez tę bramę aplikacji do oceny, zawartość żądania HTTP (S) i odpowiedzi:

- Nagłówki HTTP w żądaniu
- Nagłówki HTTP w odpowiedzi
- Zmienne serwera bramy aplikacji

Warunek może służyć do oceny, czy dana zmienna jest obecny, czy określona zmienna dokładnie odpowiada określonej wartości, czy określona zmienna dokładnie odpowiada określonym wzorcem. [Biblioteka języka Perl zgodne regularne wyrażenia (PCRE)](https://www.pcre.org/) jest używany do implementowania wzorca wyrażenia regularnego dopasowania w warunkach. Aby dowiedzieć się więcej na temat składni wyrażeń regularnych, zobacz [wyrażeń regularnych języka Perl man strony](http://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Akcje ponownego napisania

Ponowne zapisywanie adresów akcji są używane do określania nagłówków żądania i odpowiedzi, przeznaczonych do ponownego zapisywania i nową wartość, która oryginalnego nagłówki muszą zostać przepisane, aby. Można utworzyć nowy nagłówek, zmodyfikuj wartość istniejący nagłówek lub usuń istniejący nagłówek. Wartość nagłówka nowy lub istniejący nagłówek można ustawić następujących wartości:

- Tekst 
- Nagłówek żądania: Aby określić nagłówek żądania, należy użyć składni {http_req_*headerName*}
- Nagłówek odpowiedzi: Aby określić nagłówek odpowiedzi, należy użyć składni {http_resp_*headerName*}
- Zmienna serwera: Aby określić zmienną serwera, należy użyć składni {var_*serverVariable*}
- Kombinacja tekstu, nagłówek żądania, nagłówek odpowiedzi i Zmienna serwera.

## <a name="server-variables"></a>Zmienne serwera

Usługa Application gateway używa zmiennych serwera do przechowywania przydatne informacje dotyczące serwera, połączenie z klientem a bieżącego żądania w połączeniu, takich jak adres IP klienta lub typ przeglądarki sieci web. Te zmienne zmian dynamicznie, na przykład po załadowaniu nowej strony lub a opublikowania formularza. Te zmienne serwera można użyć do oceny warunków ponownego zapisywania i ponownego zapisywania nagłówków. 

Usługa Application gateway obsługuje następujące zmienne serwera:

| Zmienne serwera obsługiwane | Opis                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Zawiera "X-Forwarded-dla" klienta polem nagłówka żądania z `client_ip` (opisana w poniższej tabeli) zmiennej dołączoną w formacie (IP1, IP2, IP3,...). Jeśli pole "X-Forwarded-dla" nie jest obecna w nagłówku żądania klienta `add_x_forwarded_for_proxy` zmienna jest równa `$client_ip` zmiennej. Ta zmienna jest szczególnie przydatne w scenariuszach, gdzie klienci mają do przepisania nagłówek X-Forwarded-dla ustawiony przez usługę Application Gateway w taki sposób, że nagłówek zawiera tylko adres IP bez informacji o porcie. |
| ciphers_supported          | Zwraca listę szyfrów obsługiwanych przez klienta          |
| ciphers_used               | Zwraca ciąg mechanizmów szyfrowania używany do ustanowionego połączenia SSL |
| client_ip                  | Adres IP klienta, z którego bramy application gateway odebrał żądanie. W przypadku zwrotnego serwera proxy przed bramy aplikacji i klient źródłowy, następnie *client_ip* zwróci adres IP zwrotnego serwera proxy. |
| client_port                | port klienta                                                  |
| client_tcp_rtt             | informacje na temat klienta połączenie TCP. dostępna w systemach, które obsługują opcję gniazda TCP_INFO |
| client_user                | Korzystając z uwierzytelniania HTTP, nazwa użytkownika podana dla uwierzytelniania |
| host                       | w następującej kolejności: Nazwa hosta z wiersz żądania lub nazwy hosta z pola nagłówka żądania "Host" lub nazwę serwera, dopasowywanie na żądanie |
| cookie_*nazwy*              | *nazwa* plików cookie                                            |
| http_method                | Metoda użyta do utworzenia adresu URL żądania. Na przykład pobrać POST itp. |
| http_status                | Stan sesji, na przykład: 200, 400, 403 itp.                       |
| http_version               | żądanie protokołu, zazwyczaj "HTTP/1.0", "HTTP/1.1" lub "HTTP/2.0" |
| QUERY_STRING               | na liście wartość zmiennej pary poniżej "?" w żądanego adresu URL. |
| received_bytes             | długość żądania (w tym wierszu żądania, nagłówek i treść żądania) |
| request_query              | argumenty w wierszu żądania                                |
| request_scheme             | Schemat żądania, "http" lub "https"                            |
| request_uri                | pełne oryginalnego identyfikatora URI żądania (z argumentami)                   |
| sent_bytes                 | Liczba bajtów wysłanych do klienta                             |
| server_port                | Port serwera, które zaakceptowane żądanie                 |
| ssl_connection_protocol    | Zwraca protokół ustanowionego połączenia SSL        |
| ssl_enabled                | "włączone" if połączenie działa w trybie SSL lub ciąg pusty, w przeciwnym razie |

## <a name="rewrite-configuration"></a>Konfiguracja ponownego napisania

Aby skonfigurować ponownego napisania nagłówka HTTP, konieczne będzie:

1. Utwórz nowe obiekty, które są wymagane do przepisania nagłówków http:

   - **Ponowne zapisywanie adresów akcji**: używany do określenia żądania i pola nagłówka żądania, które planujesz ponownego zapisywania i nową wartość, która oryginalnego nagłówki muszą zostać przepisane, aby. Można skojarzyć więcej warunków ponownego napisania co najmniej jeden element za pomocą akcji ponownego zapisywania.

   - **Ponowne zapisywanie adresów warunek**: Jest opcjonalna konfiguracja. warunek ponownego zapisywania jest dodawany, oceni treści żądania HTTP (S) i odpowiedzi. Decyzja do wykonania akcji ponownego napisania skojarzone z warunkiem ponownego napisania będzie zależeć czy dopasowywane żądania HTTP (S) lub odpowiedzi z warunkiem ponownego zapisywania. 

     Jeśli więcej niż jeden warunki są skojarzone z akcji, a następnie akcję będą wykonywane tylko wtedy, gdy wszystkie warunki są spełnione, czyli, logiczne i będzie można wykonać operacji.

   - **Napisz ponownie reguły**: reguły ponownego pisania zawiera wiele akcji ponownego napisania — Nadpisz kombinacje warunku.

   - **Reguła sekwencji**: ułatwia określenie kolejności, w której poszczególne reguły ponownego zapisywania są wykonywane. Jest to przydatne, gdy wiele reguł ponownego zapisywania znajduje się w zestawie ponownego zapisywania. Reguły ponownego pisania o niższej wartości kolejności reguł pobiera wykonywany jako pierwszy. Możesz podać tę samą sekwencję regułę na dwie reguły ponownego zapisywania kolejność wykonywania zostaną deterministyczna.

   - **Napisz ponownie zestaw**: zawiera wiele reguł ponownego zapisywania, które będą skojarzone reguły routingu żądania.

2. Będą potrzebne do dołączenia zestawu ponownego napisania (*rewriteRuleSet*) przy użyciu reguły routingu. Jest to spowodowane konfiguracji ponownego zapisywania jest dołączony do odbiornika źródła za pomocą reguły routingu. Korzystając z podstawową regułę routingu, konfiguracja ponownego napisania nagłówka jest skojarzony z odbiornika źródła i nadpisania globalnego nagłówka. W przypadku regułę routingu opartego na ścieżkach Konfiguracja ponownego napisania nagłówka został zdefiniowany w Mapa ścieżki adresu URL. Tak mają zastosowanie tylko do określonej ścieżki obszaru lokacji.

Można utworzyć wiele zestawów ponownego napisania nagłówka http, a każdy zestaw ponownego napisania można zastosować do wielu odbiorników. Jednak można zastosować tylko do jednego ponownego zapisywania zestawu do określonego odbiornika.

## <a name="common-scenarios"></a>Typowe scenariusze

Niektóre typowe scenariusze, które wymagają ponownego napisania nagłówka są wymienione poniżej.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Usuń informacje o porcie z nagłówka X-Forwarded-dla

Usługa Application gateway Wstawia nagłówek X-Forwarded-dla wszystkich żądań, zanim przekazuje żądania do zaplecza. Format dla tego pliku nagłówkowego jest IP:port listę rozdzielonych przecinkami. Jednak może być scenariuszy, w którym serwerów wewnętrznej bazy danych wymaga nagłówka, aby zawierała tylko adresy IP. Na wykonywanie takich scenariuszy, napisz ponownie nagłówek może służyć do usuwania informacji o porcie nagłówek X-Forwarded-dla. Jednym ze sposobów, w tym celu jest równa nagłówek add_x_forwarded_for_proxy zmiennej serwera. 

![Usuń port](media/rewrite-http-headers/remove-port.png)

### <a name="modify-the-redirection-url"></a>Modyfikowanie adresu URL przekierowania

Aplikacja wewnętrznej bazy danych wysyła odpowiedź przekierowania, możesz przekierowuje klienta do innego adresu URL niż określona przez aplikację w wewnętrznej bazie danych. Taki scenariusz jest, gdy znajduje się za bramą aplikacji usługi app service i wymaga od klienta w celu przekierowania do jego ścieżka względna (przekierowanie z contoso.azurewebsites.net/path1 do contoso.azurewebsites.net/path2). 

Ponieważ usługi app service jest usługą wielodostępną, używa nagłówka hosta w żądaniu przekierowywać do właściwego punktu końcowego. Aplikacja usług ma domyślną nazwę domeny *. azurewebsites.net (np. contoso.azurewebsites.net), który różni się od nazwy domeny bramy aplikacji (np. contoso.com). Ponieważ oryginalne żądanie od klienta ma usługa application gateway wartość Nazwa domeny contoso.com jako nazwę hosta, bramy application gateway zmiany nazwy hosta contoso.azurewebsites.net, tak aby usługi app service można go przesłać do właściwego punktu końcowego. Gdy usługa aplikacji wysyła odpowiedź przekierowania, używa tej samej nazwy hosta w nagłówku location odpowiedzi przez punkt końcowy, w żądaniu otrzymywanych z bramy aplikacji. W związku z tym spowoduje, że klient żądania bezpośrednio do contoso.azurewebsites.net/path2 zamiast przechodzenia przez bramy application gateway (contoso.com/path2). Pomijanie bramy application gateway nie jest pożądane. 

Ten problem można rozwiązać przez ustawienie dla nazwy hosta w nagłówku location na nazwę domeny bramy aplikacji. Aby to zrobić, można utworzyć regułę ponownego zapisywania warunek, który ocenia, czy nagłówek lokalizacji, w odpowiedzi zawiera azurewebsites.net, wprowadzając `(https?):\/\/.*azurewebsites\.net(.*)$` jako wzorzec i wykonują akcję do przepisania nagłówek lokalizacji, aby usługa application gateway Nazwa hosta, wprowadzając `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako wartość nagłówka.

![Modyfikowanie nagłówek lokalizacji](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementowanie nagłówki zabezpieczeń protokołu HTTP, aby zapobiec luk w zabezpieczeniach

Kilka luk w zabezpieczeniach można naprawić poprzez implementację niezbędne nagłówki odpowiedzi aplikacji. Niektóre z tych nagłówków zabezpieczeń są X XSS ochrony, zabezpieczenia w przypadku transportu Strict, zawartość-Security-Policy itp. Usługa application gateway służy do ustawiania tych nagłówków na wszystkie odpowiedzi.

![Nagłówka zabezpieczeń](media/rewrite-http-headers/security-header.png)

## <a name="limitations"></a>Ograniczenia

- Ponowne napisanie nagłówki połączenia, uaktualniania i hosta nie jest jeszcze obsługiwany.

- Nazwy nagłówków może zawierać żadnych znaków alfanumerycznych i określone symbole, zgodnie z definicją w [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Jednak obecnie nie obsługujemy "podkreślenie" (\_) znaków specjalnych w nazwie nagłówka. 

## <a name="need-help"></a>Potrzebujesz pomocy?

Skontaktuj się z nami pod adresem [ AGHeaderRewriteHelp@microsoft.com ](mailto:AGHeaderRewriteHelp@microsoft.com) w przypadku, gdy potrzebujesz pomocy przy użyciu tej funkcji.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak ponownie zapisuje nagłówki HTTP, zobacz:

-  [Ponownie zapisuje nagłówki HTTP przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
-  [Ponownie zapisuje nagłówki HTTP przy użyciu programu Azure PowerShell](add-http-header-rewrite-rule-powershell.md)