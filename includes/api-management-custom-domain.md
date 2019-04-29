---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: f98f750c14666400f41a249b3d8379b423e6dee2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693599"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Jak serwer Proxy usługi APIM odpowiada za pomocą certyfikatów SSL w uzgadniania TLS

### <a name="clients-calling-with-sni-header"></a>Klientom wywołującym z nagłówkiem SNI
Jeśli klient ma zawartość jednego lub wielu domen niestandardowych skonfigurowanych dla serwera Proxy, usługi APIM, oznacza mogą odpowiadać na żądania HTTPS z domeny niestandardowej (np. contoso.com), a także domyślnej domeny (na przykład apim-service-name.azure-api.net). Na podstawie informacji w nagłówku oznaczaniem nazwy serwera (SNI), usługi APIM odpowiada za pomocą certyfikatu odpowiedniego serwera.

### <a name="clients-calling-without-sni-header"></a>Klientów wywołujących bez nagłówka SNI
Jeśli klient korzysta z klienta, który nie będzie przesyłał [SNI](https://tools.ietf.org/html/rfc6066#section-3) nagłówka APIM tworzy odpowiedzi przy użyciu logiki poniższym:

* Jeśli usługa ma tylko jedną domenę niestandardowe skonfigurowane dla serwera Proxy, certyfikat domyślny jest certyfikat, który został wystawiony dla domeny niestandardowej serwera Proxy.
* Jeśli usługa została skonfigurowana wielu domen niestandardowych dla serwera Proxy (obsługiwane tylko w **Premium** warstwy), klient może określić, który certyfikat powinien być domyślnego certyfikatu. Aby ustawić domyślnego certyfikatu [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/createorupdate#hostnameconfiguration) właściwość powinna być ustawiona na wartość true ("defaultSslBinding": "true"). Jeśli klient nie ustawia się właściwości, domyślnego certyfikatu jest certyfikat wystawiony dla domyślnej domeny serwera Proxy hostowanych na *.azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Obsługa żądania PUT/POST przy użyciu dużych ładunku

Serwer Proxy usługi APIM obsługuje żądania z ładunkiem dużych, podczas korzystania z certyfikatów klienta przy użyciu protokołu HTTPS (na przykład ładunku > od 40 KB). Aby zapobiec zawiesza żądanie serwera, klientów można ustawić właściwości ["negotiateClientCertificate": "true"](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/CreateOrUpdate#hostnameconfiguration) na nazwę hosta serwera Proxy. Jeśli właściwość jest ustawiona na wartość true, klient żądania certyfikatu w momencie połączenia SSL/TLS, przed wymiana jakichkolwiek żądania protokołu HTTP. Ponieważ to ustawienie ma zastosowanie w **nazwę hosta serwera Proxy** poziomie, wszystkie żądania połączeń poprosić o certyfikat klienta. Klienci mogą skonfigurować maksymalnie 20 domen niestandardowych dla serwera Proxy (obsługiwane tylko w **Premium** warstwy) i obejście tego ograniczenia.

