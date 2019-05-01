---
title: Ponownie zapisuje nagłówki HTTP za pomocą usługi Azure Application Gateway | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie ponownego zapisywania adresów nagłówków HTTP w usłudze Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/29/2019
ms.author: absha
ms.openlocfilehash: 89df3a981ba3710e848f834c303772e94e10b139
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947177"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Ponownie zapisuje nagłówki HTTP z usługą Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zezwalaj na nagłówki HTTP, klienta i serwera przekazać dodatkowe informacje z żądania lub odpowiedzi. Zapisując je ponownie tych nagłówków, można wykonywać ważne zadania, takie jak dodawanie pola nagłówka związanych z zabezpieczeniami, takich jak HSTS / X XSS ochrony, usunięcie pola nagłówka odpowiedzi, które może ujawnić informacje poufne i usuwanie informacji o porcie X-Forwarded-dla nagłówków.

Usługa Application Gateway można dodać, usunąć lub zaktualizować nagłówki żądania i odpowiedzi HTTP podczas żądania i pakietów odpowiedzi, przenoszenie między klientem a pule zaplecza. I pozwala na dodawanie warunków w celu zapewnienia, że określone nagłówki są przepisany tylko wtedy, gdy zostaną spełnione określone warunki.

Usługa Application Gateway obsługuje również kilka [zmiennych serwera](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) ułatwiające przechowywać dodatkowe informacje na temat żądań i odpowiedzi. Ułatwia tworzenie reguły ponownego zapisywania zaawansowane.

> [!NOTE]
>
> Obsługa ponownego napisania nagłówka HTTP jest dostępna tylko dla [Standard_V2 i jednostki SKU WAF_v2](application-gateway-autoscaling-zone-redundant.md).

![Ponowne napisanie nagłówki](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Nagłówki obsługiwane

Można napisać ponownie wszystkie nagłówki żądania i odpowiedzi, z wyjątkiem nagłówki hosta, połączenia i uaktualnianie. Brama aplikacji umożliwia również tworzenie niestandardowych nagłówków i dodać je do żądania i odpowiedzi jest kierowany przez nią.

## <a name="rewrite-conditions"></a>Warunki ponownego napisania

Warunki ponownego napisania można użyć do oceny treści żądania HTTP (S) i odpowiedzi i wykonywania nagłówka Napisz ponownie tylko wtedy, gdy jest to jeden lub więcej warunki są spełnione. Bramy aplikacji używa tego rodzaju zmienne do oceny, zawartość i odpowiedzi na żądania HTTP (S):

- Nagłówki HTTP w żądaniu.
- Nagłówki HTTP w odpowiedzi.
- Zmienne serwera bramy aplikacji.

Warunku można użyć do oceny, czy określona zmienna jest obecny, czy określona zmienna odpowiada określonej wartości, czy określona zmienna jest zgodny ze wzorcem określonym. Możesz użyć [biblioteki Perl zgodne regularne wyrażenia (PCRE)](https://www.pcre.org/) skonfigurować wzorzec wyrażenia regularnego dopasowania w warunkach. Aby dowiedzieć się więcej na temat składni wyrażeń regularnych, zobacz [strona główna wyrażeń regularnych języka Perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Akcje ponownego napisania

Akcje ponownego napisania umożliwia określenie nagłówki żądania i odpowiedzi, które chcesz ponownie zapisać i nową wartość dla nagłówków. Można albo utworzyć nowy nagłówek, zmodyfikuj wartość istniejący nagłówek lub usuń istniejący nagłówek. Wartość nagłówka nowy lub istniejący nagłówek można ustawić do tych typów wartości:

- Tekst.
- Nagłówek żądania. Aby określić nagłówek żądania, należy użyć składni {http_req_*headerName*}.
- Nagłówek odpowiedzi. Aby określić nagłówek odpowiedzi, należy użyć składni {http_resp_*headerName*}.
- Zmienna serwera. Aby określić zmienną serwera, należy użyć składni {var_*serverVariable*}.
- Kombinacja tekstu, nagłówek żądania, nagłówek odpowiedzi i Zmienna serwera.

## <a name="server-variables"></a>Zmienne serwera

Usługa Application Gateway używa zmiennych serwera do przechowywania przydatnych informacji o serwerze, połączenie z klientem a bieżącego żądania połączenia. Przykładami informacji przechowywanych adres IP klienta i typ przeglądarki sieci web. Zmienne serwera zmieniać dynamicznie, na przykład po załadowaniu nowej strony lub po opublikowaniu formularza. Tych zmiennych można użyć do oceny warunków ponownego zapisywania i ponownego zapisywania nagłówków.

Usługa Application gateway obsługuje te zmienne serwera:

| Nazwa zmiennej | Opis                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Pola nagłówka żądania X-Forwarded-dla klientów z `client_ip` zmiennej (patrz wyjaśnienia później w tej tabeli) dołączone do niego w formacie IP1, IP2, IP3 i tak dalej. Jeśli X przekazany do pole nie znajduje się w nagłówku żądania klienta `add_x_forwarded_for_proxy` zmienna jest równa `$client_ip` zmiennej. Ta zmienna jest szczególnie przydatne, jeśli chcesz ponownie zapisać nagłówek X-Forwarded-dla ustawione przez usługę Application Gateway, tak więc nagłówek zawiera tylko adres IP bez informacji o porcie. |
| ciphers_supported          | Listę szyfrów obsługiwanych przez klienta.          |
| ciphers_used               | Ciąg mechanizmów szyfrowania używany do ustanowionego połączenia SSL. |
| client_ip                  | Adres IP klienta, z którego bramy application gateway odebrał żądanie. Jeśli zwrotny serwer proxy przed bramy aplikacji i klient źródłowy *client_ip* zwróci adres IP zwrotnego serwera proxy. |
| client_port                | Port klienta.                                                  |
| client_tcp_rtt             | Informacje o kliencie połączenia TCP. Dostępne w systemach, które obsługują TCP_INFO opcję gniazda. |
| client_user                | Gdy jest używane uwierzytelnianie HTTP, nazwa użytkownika dostarczony do uwierzytelniania. |
| host                       | W następującej kolejności: nazwę hosta z wiersz, nazwy hosta z pola nagłówka żądania hosta lub nazwę serwera, dopasowywanie na żądanie. |
| cookie_*nazwy*              | *Nazwa* pliku cookie.                                            |
| http_method                | Metoda użyta do utworzenia adresu URL żądania. Na przykład GET lub POST. |
| http_status                | Stan sesji. Na przykład 200, 400 lub 403.                       |
| http_version               | Protokół żądania. Zazwyczaj HTTP/1.0, HTTP/1.1 lub protokołu HTTP/2.0. |
| QUERY_STRING               | Następująca lista zmiennej/pary wartości, "?" w żądanego adresu URL. |
| received_bytes             | Długość żądania (w tym wierszu żądania, nagłówek i treść żądania). |
| request_query              | Argumenty w wierszu żądania.                                |
| request_scheme             | Schemat żądania: http lub https.                            |
| request_uri                | Pełne oryginalne żądanie identyfikatora URI (z argumentów).                   |
| sent_bytes                 | Liczba bajtów wysłanych do klienta.                             |
| server_port                | Port serwera, na którym zaakceptowane żądanie.                 |
| ssl_connection_protocol    | Protokół ustanowionego połączenia SSL.        |
| ssl_enabled                | "Włączone", jeśli połączenie działa w trybie SSL. W przeciwnym razie pusty ciąg. |

## <a name="rewrite-configuration"></a>Konfiguracja ponownego napisania

Aby skonfigurować ponownego napisania nagłówka HTTP, należy wykonać następujące czynności.

1. Tworzenie obiektów, które są wymagane do ponownego napisania nagłówka HTTP:

   - **Ponowne zapisywanie adresów akcji**: Służy do określania żądania i pola nagłówka żądania, które chcesz ponownie zapisać i nową wartość dla nagłówków. Można skojarzyć jedną lub więcej warunków za pomocą akcji ponownego napisania ponowne zapisywanie adresów.

   - **Ponowne zapisywanie adresów warunek**: Konfiguracja opcjonalna. Ponowne zapisywanie adresów warunki zostaną obliczone treści żądania HTTP (S) i odpowiedzi. Akcja ponownego napisania wystąpi, jeśli żądania HTTP (S) lub odpowiedzi dopasowuje warunek ponownego zapisywania.

     Jeśli więcej niż jeden warunek jest skojarzona z akcją, akcja jest wykonywana tylko wtedy, gdy są spełnione wszystkie warunki. Innymi słowy operacja jest operacją i logicznych.

   - **Napisz ponownie reguły**: Zawiera wiele akcji ponownego napisania / ponownego zapisywania kombinacje warunku.

   - **Reguła sekwencji**: Ułatwia określenie kolejności, w którym wykonywanie reguły ponownego zapisywania. Ta konfiguracja jest przydatne, jeśli masz wiele reguł ponownego zapisywania w zestawie ponownego zapisywania. Reguły ponownego pisania, który ma mniejszą wartość kolejności reguły jest uruchamiany w pierwszy. Jeśli przypiszesz tę samą sekwencję regułę na dwie reguły ponownego zapisywania kolejność wykonywania jest niedeterministyczny.

   - **Napisz ponownie zestaw**: Zawiera wiele reguł ponownego zapisywania, które mają zostać skojarzone z regułą routingu żądania.

2. Dołącz zestaw ponownego napisania (*rewriteRuleSet*) do reguły routingu. Konfiguracja ponownego zapisywania jest dołączony do odbiornika źródła za pomocą reguły routingu. Korzystając z podstawową regułę routingu, konfiguracja ponownego napisania nagłówka jest skojarzony z odbiornika źródła i nadpisania globalnego nagłówka. Gdy używasz regułę routingu opartego na ścieżkach, konfiguracja ponownego napisania nagłówka został zdefiniowany w Mapa ścieżki adresu URL. W takim przypadku zastosowanie tylko do określonej ścieżki obszaru lokacji.

Można utworzyć wiele zestawów ponownego napisania nagłówka HTTP i stosować każdego Napisz ponownie ustawić do wielu odbiorników. Jednak można zastosować tylko do jednego ponownego zapisywania zestawu do określonego odbiornika.

## <a name="common-scenarios"></a>Typowe scenariusze

Poniżej przedstawiono niektóre typowe scenariusze dotyczące przy użyciu nagłówka ponowne zapisywanie adresów.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Usuń informacje o porcie z nagłówka X-Forwarded-dla

Usługa Application Gateway Wstawia nagłówek X-Forwarded-dla do wszystkich żądań przed przekazuje żądania do zaplecza. Tego pliku nagłówkowego jest rozdzielana przecinkami lista portów IP. Mogą istnieć scenariusze, w których serwery zaplecza wystarczy te nagłówki, aby zawierać adresy IP. Napisz ponownie nagłówek służy do usuwania informacji o porcie nagłówek X-Forwarded-dla. Jednym ze sposobów, w tym celu jest równa nagłówek add_x_forwarded_for_proxy zmienna serwera:

![Usuń port](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Modyfikowanie adresem URL przekierowania

W przypadku aplikacji zaplecza są wysyłane odpowiedzi przekierowania, możesz chcieć przekierowuje klienta do innego adresu URL niż określona w aplikacji zaplecza. Na przykład można to zrobić, gdy usługi app service znajduje się za bramą aplikacji wymaga od klienta w celu przekierowania do swojej ścieżki względnej. (Na przykład przekierowanie z contoso.azurewebsites.net/path1 contoso.azurewebsites.net/path2.)

Ponieważ usługi App Service jest usługą z wieloma dzierżawcami, używa nagłówka hosta w żądaniu przekierowywać żądania do właściwego punktu końcowego. Aplikacja usług ma domyślną nazwę domeny *. azurewebsites.net (np. contoso.azurewebsites.net), która różni się od nazwy domeny bramy aplikacji (np. contoso.com). Ponieważ oryginalne żądanie od klienta ma application gateway nazwę domeny (contoso.com) jako nazwę hosta, bramy application gateway contoso.azurewebsites.net zmiany nazwy hosta. Wprowadza zmianę, tak aby usługi app service może kierować żądania do właściwego punktu końcowego.

Gdy usługa aplikacji wysyła odpowiedź przekierowania, używa tej samej nazwy hosta w nagłówku location odpowiedzi przez punkt końcowy, w żądaniu otrzymywanych z bramy aplikacji. Dlatego klient spowoduje, że żądania bezpośrednio do contoso.azurewebsites.net/path2 zamiast przechodzenia przez bramy application gateway (contoso.com/path2). Pomijanie bramy application gateway nie jest pożądane.

Aby rozwiązać ten problem, należy ustawienie nazwy hosta w nagłówku location na nazwę domeny bramy aplikacji.

Poniżej przedstawiono kroki, aby wymienić nazwy hosta:

1. Utwórz reguły ponownego pisania z warunek, który ocenia, czy nagłówek lokalizacji, w odpowiedzi zawiera azurewebsites.net. Wprowadź wzorzec `(https?):\/\/.*azurewebsites\.net(.*)$`.
1. Wykonaj akcję do przepisania nagłówek lokalizacji tak, aby miało nazwę hosta bramy aplikacji. To zrobić, wprowadzając `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako wartość nagłówka.

![Modyfikowanie nagłówek lokalizacji](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementowanie nagłówki zabezpieczeń protokołu HTTP, aby zapobiec luk w zabezpieczeniach

Można naprawić kilka luk w zabezpieczeniach, implementując niezbędne nagłówki odpowiedzi aplikacji. Te nagłówki zabezpieczeń obejmują X XSS ochrony, ścisłe transportu zabezpieczeniami i zawartości-Security-Policy. Usługa Application Gateway służy do ustawiania tych nagłówków na wszystkie odpowiedzi.

![Nagłówka zabezpieczeń](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Usuwanie niepożądanych nagłówków

Możesz chcieć usunąć nagłówki, które ujawniają poufne informacje z odpowiedzi HTTP. Na przykład możesz usunąć informacje, takie jak nazwa serwera zaplecza, system operacyjny lub szczegóły biblioteki. Aby usunąć te nagłówki, można użyć bramy application gateway:

![Usunięcie nagłówka](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Sprawdzanie obecności nagłówka

Możesz ocenić nagłówka żądania lub odpowiedzi HTTP na obecność zmiennej nagłówka lub serwera. Ocena jest przydatne, gdy chcesz wykonać nadpisania nagłówka, tylko wtedy, gdy występuje niektórych nagłówka.

![Sprawdzanie obecności nagłówka](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Ograniczenia

- Ponowne napisanie nagłówki połączenia, uaktualniania i hosta nie jest obecnie obsługiwane.

- Nazwy nagłówków może zawierać żadnych znaków alfanumerycznych i określone symbole, zgodnie z definicją w [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Obecnie nie obsługujemy znaku podkreślenia (\_) znaków specjalnych w nazwy nagłówków.

## <a name="need-help"></a>Potrzebujesz pomocy?

Skontaktuj się z nami pod adresem [ AGHeaderRewriteHelp@microsoft.com ](mailto:AGHeaderRewriteHelp@microsoft.com) Jeśli potrzebujesz pomocy przy użyciu tej funkcji.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak ponownie zapisuje nagłówki HTTP, zobacz:

- [Ponownie zapisuje nagłówki HTTP przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Ponownie zapisuje nagłówki HTTP przy użyciu programu Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
