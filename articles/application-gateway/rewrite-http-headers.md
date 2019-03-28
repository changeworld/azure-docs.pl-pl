---
title: Ponownie zapisuje nagłówki HTTP w usłudze Azure Application Gateway | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie możliwości, aby ponownie zapisuje nagłówki HTTP w usłudze Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 67603e326583400e8fc250ea6120297e7a94d101
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520925"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Ponownie zapisuje nagłówki HTTP z usługą Application Gateway (publiczna wersja zapoznawcza)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nagłówki HTTP umożliwiają klientowi i serwerowi przekazywanie dodatkowych informacji w ramach żądania lub odpowiedzi. Ponowne napisanie te ułatwia nagłówki HTTP, wykonanie kilku ważnych scenariuszy, takich jak dodawanie nagłówka związanych z zabezpieczeniami pól, takich jak HSTS / X XSS ochrony lub usuwanie pola nagłówka odpowiedzi, który może spowodować ujawnienie poufnych informacji, takich jak nazwa serwera wewnętrznej bazy danych.

Usługa Application Gateway obsługuje teraz możliwość ponownego zapisywania nagłówków w przychodzących żądaniach HTTP, jak również w wychodzących odpowiedziach HTTP. Będzie można dodać, usunąć lub zaktualizować nagłówki żądania i odpowiedzi HTTP podczas pakiety żądań/odpowiedzi przenoszenia między pulami klienta i zaplecza. Można napisać ponownie obu pól nagłówka standardowe, jak również niestandardowych.

> [!NOTE]
> 
> Obsługa ponownego napisania nagłówka HTTP jest dostępna tylko dla [nowej jednostki SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

Oferuje obsługę ponownego napisania nagłówka bramy aplikacji:

- **Nagłówek globalnego ponowne zapisywanie adresów**: Można napisać ponownie określone nagłówki dla wszystkich żądań i odpowiedzi odnoszących się do witryny.
- **Ponowne zapisywanie adresów opartego na ścieżkach nagłówka**: ten typ ponownego napisania pozwala nagłówka ponowne zapisywanie adresów dla tych żądań i odpowiedzi, które dotyczą tylko w obszarze określonej lokacji, na przykład obszar koszyka zakupów wskazywane przez /cart/\*.

Dzięki tej zmianie należy:

1. Utwórz nowe obiekty, które są wymagane do przepisania nagłówków http: 
   - **RequestHeaderConfiguration**: ten obiekt jest używany do określenia pola nagłówka żądania, które planujesz ponownego zapisywania i nową wartość, która oryginalnego nagłówki muszą zostać przepisane, aby.
   - **ResponseHeaderConfiguration**: ten obiekt jest używany do określania, które zamierzasz zmodyfikować pola nagłówka odpowiedzi i nową wartość, która oryginalnego nagłówki muszą zostać przepisane, aby.
   - **ActionSet**: ten obiekt zawiera konfiguracje nagłówki żądania i odpowiedzi określonym powyżej. 
   - **RewriteRule**: ten obiekt zawiera wszystkie *actionSets* określonej powyżej. 
   - **RewriteRuleSet**— ten obiekt zawiera wszystkie *rewriteRules* i musi być dołączony do żądania regułę routingu — basic lub opartego na ścieżkach.
2. Następnie trzeba będzie można dołączyć zestaw reguł ponownego zapisywania, za pomocą reguły routingu. Po utworzeniu tej konfiguracji ponownego zapisywania jest dołączony do odbiornika źródła za pomocą reguły routingu. Korzystając z podstawową regułę routingu, konfiguracja ponownego napisania nagłówka jest skojarzony z odbiornika źródła i nadpisania globalnego nagłówka. W przypadku regułę routingu opartego na ścieżkach Konfiguracja ponownego napisania nagłówka został zdefiniowany w Mapa ścieżki adresu URL. Tak mają zastosowanie tylko do określonej ścieżki obszaru lokacji.

Możesz utworzyć wiele zestawów reguł ponownego zapisywania nagłówka http, a każdy zestaw reguł ponownego zapisywania, które można zastosować do wielu odbiorników. Jednakże można zastosować tylko jeden http ponownego napisania zestawu reguł do określonego odbiornika.

Można napisać ponownie wartość w nagłówki, aby:

- Wartość tekstowa. 

  *Przykład:* 

  ```azurepowershell-interactive
  $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -  HeaderValue "max-age=31536000")
  ```

- Wartość z innym nagłówkiem. 

  *Przykład 1:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration= New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-RequestHeader" -HeaderValue {http_req_oldHeader}
  ```

  > [!Note] 
  > Aby określić nagłówek żądania, należy użyć składni: {http_req_headerName}

  *Przykład 2*:

  ```azurepowershell-interactive
  $responseHeaderConfiguration= New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-ResponseHeader" -HeaderValue {http_resp_oldHeader}
  ```

  > [!Note] 
  > Aby określić nagłówek odpowiedzi, należy użyć składni: {http_resp_headerName}

- Wartość z obsługiwanych zmiennych serwera.

  *Przykład:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Ciphers-Used" -HeaderValue "{var_ciphers_used}"
  ```

  > [!Note] 
  > Aby określić zmienną serwera, należy użyć składni: {var_serverVariable}

- Kombinacja powyższych.

## <a name="server-variables"></a>Zmienne serwera

Zmienne serwera przydatne informacje są przechowywane na serwerze sieci web. Te zmienne zawierają informacje dotyczące serwera, połączenie z klientem a bieżącego żądania w połączeniu, takich jak adres IP klienta lub typ przeglądarki sieci web. One dynamicznie zmieniać, takie jak po załadowaniu nowej strony lub a opublikowania formularza.  Korzystanie z tych użytkowników zmiennych można ustawić nagłówki żądania, a także nagłówków odpowiedzi. 

Ta funkcja obsługuje przebudowywania nagłówki do następujących zmiennych serwera:

| Zmienne serwera obsługiwane | Opis                                                  |
| -------------------------- | :----------------------------------------------------------- |
| ciphers_supported          | Zwraca listę szyfrów obsługiwanych przez klienta          |
| ciphers_used               | Zwraca ciąg mechanizmów szyfrowania używany do ustanowionego połączenia SSL |
| client_ip                  | Adres IP klienta, z którego bramy application gateway odebrał żądanie. W przypadku zwrotnego serwera proxy przed bramy aplikacji i klient źródłowy, następnie *client_ip* zwróci adres IP zwrotnego serwera proxy. Zmienna tjsi jest szczególnie przydatne w scenariuszach, w których klienci mają do przepisania nagłówek X-Forwarded-dla ustawiony przez usługę Application Gateway, tak aby nagłówek zawiera tylko adres IP bez informacji o porcie. |
| client_port                | port klienta                                                  |
| client_tcp_rtt             | informacje na temat klienta połączenie TCP. dostępna w systemach, które obsługują opcję gniazda TCP_INFO |
| client_user                | Korzystając z uwierzytelniania HTTP, nazwa użytkownika podana dla uwierzytelniania |
| host                       | w następującej kolejności: Nazwa hosta z wiersz żądania lub nazwy hosta z pola nagłówka żądania "Host" lub nazwę serwera, dopasowywanie na żądanie |
| cookie_*nazwy*              | *nazwa* plików cookie |
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

## <a name="limitations"></a>Ograniczenia

- Tę możliwość ponownego zapisywania nagłówków HTTP jest obecnie dostępna tylko za pośrednictwem programu Azure PowerShell, interfejsu API platformy Azure i zestawu Azure SDK. Pomoc techniczną za pomocą interfejsu wiersza polecenia portalu i platforma Azure będzie dostępna wkrótce.

- Obsługa ponownego napisania nagłówka HTTP jest obsługiwana tylko w nowej jednostki SKU [Standard_V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). Ta funkcja nie będzie obsługiwana w starych jednostek SKU.

- Ponowne napisanie nagłówki połączenia, uaktualniania i hosta nie jest jeszcze obsługiwany.

- Możliwość warunkowo ponownie zapisuje nagłówki http będzie dostępna wkrótce.

- Nazwy nagłówków może zawierać żadnych znaków alfanumerycznych i określone symbole, zgodnie z definicją w [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Jednak obecnie nie obsługujemy "podkreślenie" (\_) znaków specjalnych w nazwie nagłówka. 

## <a name="need-help"></a>Potrzebujesz pomocy?

Skontaktuj się z nami pod adresem [ AGHeaderRewriteHelp@microsoft.com ](mailto:AGHeaderRewriteHelp@microsoft.com) w przypadku, gdy potrzebujesz pomocy przy użyciu tej funkcji.

## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się możliwość ponownego zapisywania nagłówków HTTP, przejdź do [tworzenie, skalowanie automatyczne i strefowo nadmiarowe application gateway, która ponownie zapisuje nagłówki HTTP](tutorial-http-header-rewrite-powershell.md) lub [nagłówków HTTP ponownego zapisywania w istniejących Skalowanie automatyczne i strefowo nadmiarowe usługa application gateway](add-http-header-rewrite-rule-powershell.md)
