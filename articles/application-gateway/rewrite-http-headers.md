---
title: Ponownie zapisuje nagłówki HTTP w usłudze Azure Application Gateway | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie możliwości, aby ponownie zapisuje nagłówki HTTP w usłudze Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 6750276cf31d0c804b38cdf3ea6e41a4505c93f1
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971822"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Ponownie zapisuje nagłówki HTTP z usługą Application Gateway (publiczna wersja zapoznawcza)

Zezwalaj na nagłówki HTTP, klient i serwer do przekazania dodatkowych informacji z żądania lub odpowiedzi. Ponowne napisanie te ułatwia nagłówki HTTP, wykonanie kilku ważnych scenariuszy, takich jak dodawanie nagłówka związanych z zabezpieczeniami pól, takich jak HSTS / X XSS ochrony lub usuwanie pola nagłówka odpowiedzi, który może spowodować ujawnienie poufnych informacji, takich jak nazwa serwera wewnętrznej bazy danych.

Usługa Application Gateway obsługuje teraz możliwość nadpisywania nagłówki przychodzących żądań HTTP, a także wychodzące odpowiedzi HTTP. Będzie można dodać, usunąć lub zaktualizować nagłówki żądania i odpowiedzi HTTP podczas pakiety żądań/odpowiedzi przenoszenia między pulami klienta i zaplecza. Można napisać ponownie standardowe (zdefiniowane w [dokumencie RFC 2616](https://www.ietf.org/rfc/rfc2616.txt)) oraz pola niestandardowego nagłówka.

> [!NOTE] 
>
> Obsługa ponownego napisania nagłówka HTTP jest dostępna tylko dla [nowej jednostki SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

Oferuje obsługę ponownego napisania nagłówka bramy aplikacji:

- **Nagłówek globalnego ponowne zapisywanie adresów**: Można napisać ponownie określone nagłówki dla wszystkich żądań i odpowiedzi odnoszących się do witryny.
- **Ponowne zapisywanie adresów opartego na ścieżkach nagłówka**: ten typ ponownego napisania pozwala nagłówka ponowne zapisywanie adresów dla tych żądań i odpowiedzi, które dotyczą tylko w obszarze określonej lokacji, na przykład obszar koszyka zakupów wskazywane przez/koszyka / *.

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
  $responseHeaderConfiguration = New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -  HeaderValue "max-age=31536000")
  ```

- Wartość z innym nagłówkiem. 

  *Przykład 1:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration= New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-RequestHeader" -HeaderValue {http_req_oldHeader}
  ```

  > [!Note] 
  > Aby określić nagłówek żądania, należy użyć składni: {http_req_headerName}

  *Przykład 2*:

  ```azurepowershell-interactive
  $responseHeaderConfiguration= New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-ResponseHeader" -HeaderValue {http_resp_oldHeader}
  ```

  > [!Note] 
  > Aby określić nagłówek odpowiedzi, należy użyć składni: {http_resp_headerName}

- Wartość z obsługiwanych zmiennych serwera.

  *Przykład:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration = New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Ciphers-Used" -HeaderValue "{var_ciphers_used}"
  ```

  > [!Note] 
  > Aby określić zmienną serwera, należy użyć składni: {var_serverVariable}

- Kombinacja powyższych.

Zmienne serwera wymienione powyżej są zmienne, które dostarczają informacji o serwerze, połączenie klienta i bieżącego żądania połączenia. Ta funkcja obsługuje przebudowywania nagłówki do następujących zmiennych serwera:

| Zmienne serwera obsługiwane | Opis                                                  |
| -------------------------- | :----------------------------------------------------------- |
| ciphers_supported          | Zwraca listę szyfrów obsługiwanych przez klienta          |
| ciphers_used               | Zwraca ciąg mechanizmów szyfrowania używany do ustanowionego połączenia SSL |
| client_latitude            | Aby określić kraju, regionu i miasta, w zależności od adresu IP klienta |
| client_longitude           | Aby określić kraju, regionu i miasta, w zależności od adresu IP klienta |
| client_port                | port klienta                                                  |
| client_tcp_rtt             | informacje na temat klienta połączenie TCP. dostępna w systemach, które obsługują opcję gniazda TCP_INFO |
| client_user                | Korzystając z uwierzytelniania HTTP, nazwa użytkownika podana dla uwierzytelniania |
| host                       | w następującej kolejności: Nazwa hosta z wiersz żądania lub nazwy hosta z pola nagłówka żądania "Host" lub nazwę serwera, dopasowywanie na żądanie |
| http_method                | Metoda użyta do utworzenia adresu URL żądania. Na przykład pobrać POST itp. |
| HTTP_STATUS                | Stan sesji, na przykład: 200, 400, 403 itp.                       |
| http_version               | żądanie protokołu, zazwyczaj "HTTP/1.0", "HTTP/1.1" lub "HTTP/2.0" |
| QUERY_STRING               | na liście wartość zmiennej pary poniżej "?" w żądanego adresu URL. |
| received_byte              | długość żądania (w tym wierszu żądania, nagłówek i treść żądania) |
| request_query              | argumenty w wierszu żądania                                |
| request_scheme             | Schemat żądania, "http" lub "https"                            |
| request_uri                | pełne oryginalnego identyfikatora URI żądania (z argumentami)                   |
| sent_bytes                 | Liczba bajtów wysłanych do klienta                             |
| SERVER_PORT                | Port serwera, które zaakceptowane żądanie                 |
| ssl_connection_protocol    | Zwraca protokół ustanowionego połączenia SSL        |
| ssl_enabled                | "włączone" if połączenie działa w trybie SSL lub ciąg pusty, w przeciwnym razie |

## <a name="limitations"></a>Ograniczenia

- Tę możliwość ponownego zapisywania nagłówków HTTP jest obecnie dostępna tylko za pośrednictwem programu Azure PowerShell, interfejsu API platformy Azure i zestawu Azure SDK. Pomoc techniczną za pomocą interfejsu wiersza polecenia portalu i platforma Azure będzie dostępna wkrótce.

- Po zastosowaniu nadpisania nagłówek na Twojej bramy Application Gateway, nie należy używać portalu wprowadzania żadnych kolejnych zmian do tej bramy aplikacji, dopóki ta funkcja jest obsługiwana w portalu. Jeśli używasz portalu, aby wprowadzić zmiany do usługi Application Gateway, po zastosowaniu reguły ponownego pisania, nagłówek ponownie zapisać zasady. Możesz kontynuować wprowadzanie zmian przy użyciu programu Azure PowerShell, interfejsów API platformy Azure lub zestawu SDK usługi Azure.

- Obsługa ponownego napisania nagłówka HTTP jest obsługiwana tylko w nowej jednostki SKU [Standard_V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). Ta funkcja nie będzie obsługiwana w starych jednostek SKU.

- Ponowne napisanie nagłówki Connect, uaktualniania i hosta nie jest jeszcze obsługiwany.

- Dwie zmienne serwera ważne, client_ip (adres IP klienta wysyłającego żądanie) i cookie_*nazwa* ( *nazwa* plików cookie), nie są jeszcze obsługiwane. Zmienna serwera client_ip jest szczególnie przydatne w scenariuszach, w których klienci mają do przepisania nagłówek x-forwarded dla ustawiony przez usługę Application Gateway, tak aby nagłówek zawiera tylko adres IP klienta i nie informacje o porcie.

  Te zmienne serwera będzie wkrótce obsługiwany.

- Możliwość warunkowo ponownie zapisuje nagłówki http będzie dostępna wkrótce.

- Nazwy nagłówków może zawierać żadnych znaków alfanumerycznych i określone symbole, zgodnie z definicją w [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Jednak obecnie nie obsługujemy "podkreślenie" (\_) znaków specjalnych w nazwie nagłówka. 

## <a name="need-help"></a>Potrzebujesz pomocy?

Skontaktuj się z nami pod adresem [ AGHeaderRewriteHelp@microsoft.com ](mailto:AGHeaderRewriteHelp@microsoft.com) w przypadku, gdy potrzebujesz pomocy przy użyciu tej funkcji.

## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się możliwość ponownego zapisywania nagłówków HTTP, przejdź do [tworzenie, skalowanie automatyczne i strefowo nadmiarowe application gateway, która ponownie zapisuje nagłówki HTTP](tutorial-http-header-rewrite-powershell.md) lub [nagłówków HTTP ponownego zapisywania w istniejących Skalowanie automatyczne i strefowo nadmiarowe usługa application gateway](add-http-header-rewrite-rule-powershell.md)
