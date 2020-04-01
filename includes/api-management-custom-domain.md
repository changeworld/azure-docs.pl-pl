---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: b9e601c72395b4910850714460321a83a3113e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649549"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Jak serwer proxy usługi API Management odpowiada przy użyciu certyfikatów SSL podczas uzgadniania szyfrowania TLS

### <a name="clients-calling-with-sni-header"></a>Klienci dzwoniący z nagłówkiem SNI
Jeśli klient ma jedną lub wiele domen niestandardowych skonfigurowanych dla serwera proxy, interfejs APIM może odpowiadać na żądania HTTPS z domen niestandardowych (na przykład contoso.com) oraz domeny domyślnej (na przykład apim-service-name.azure-api.net). Na podstawie informacji w nagłówku wskazania nazwy serwera (SNI) interfejs APIM odpowiada odpowiednim certyfikatem serwera.

### <a name="clients-calling-without-sni-header"></a>Klienci dzwoniący bez nagłówka SNI
Jeśli klient korzysta z klienta, który nie wysyła nagłówka [SNI,](https://tools.ietf.org/html/rfc6066#section-3) apim tworzy odpowiedzi na podstawie następującej logiki:

* Jeśli usługa ma tylko jedną domenę niestandardową skonfigurowaną dla serwera proxy, certyfikat domyślny jest certyfikatem wystawionym domenie niestandardowej serwera proxy.
* Jeśli usługa skonfigurowała wiele domen niestandardowych dla serwera proxy (obsługiwanych w warstwie **Deweloper** i **Premium),** klient może wyznaczyć, który certyfikat powinien być certyfikatem domyślnym. Aby ustawić domyślny certyfikat, [właściwość defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/createorupdate#hostnameconfiguration) powinna być ustawiona na true ("defaultSslBinding":"true"). Jeśli klient nie ustawi właściwości, domyślnym certyfikatem jest certyfikat wystawiony domyślnej domenie serwera proxy hostowanej na *.azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Obsługa żądania PUT/POST z dużą ładownością

Serwer proxy apim obsługuje żądanie z dużym ładunkiem podczas korzystania z certyfikatów po stronie klienta w https (na przykład ładunek > 40 KB). Aby zapobiec zamrożeniu żądania serwera, klienci mogą ustawić właściwość ["negotiateClientCertificate": "true"](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) na serwerze proxy hostname. Jeśli właściwość jest ustawiona na true, certyfikat klienta jest wymagane w czasie połączenia SSL/TLS, przed wymiany żądań HTTP. Ponieważ ustawienie ma zastosowanie na poziomie **nazwy hosta serwera proxy,** wszystkie żądania połączenia proszą o certyfikat klienta. Klienci mogą skonfigurować do 20 domen niestandardowych dla serwera proxy (obsługiwane tylko w warstwie **Premium)** i obejść to ograniczenie.

