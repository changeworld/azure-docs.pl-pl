---
title: Azure API Management cross zasady domeny | Dokumentacja firmy Microsoft
description: Więcej informacji na temat dostępnych do użycia w usłudze Azure API Management zasady obejmujące różne domeny.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 590831454e8a18678e357b4824eb35a717d1fee0
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2017
ms.locfileid: "26129038"
---
# <a name="api-management-cross-domain-policies"></a>API Management cross domain policies (Zasady usługi API Management obejmujące różne domeny)
W tym temacie znajdują się informacje na następujące zasady usługi API Management. Aby uzyskać informacje dotyczące dodawania i konfigurowania zasad, zobacz [zasad w usłudze API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CrossDomainPolicies"></a>Krzyżowe zasady domeny  
  
-   [Zezwalaj na połączenia między domenami](api-management-cross-domain-policies.md#AllowCrossDomainCalls) — może ułatwić interfejsu API programu Adobe Flash i Microsoft Silverlight bazujące na przeglądarce klientów.  
-   [CORS](api-management-cross-domain-policies.md#CORS) -dodaje współużytkowanie zasobów między źródłami (CORS) obsługi operacji lub interfejsu API w celu zapewnienia obsługi wywołań między domenami od klientów przeglądarki do udostępniania.  
-   [JSONP](api-management-cross-domain-policies.md#JSONP) -dodaje JSON z obsługą dopełnienie (JSONP) do operacji lub interfejsu API w celu zapewnienia obsługi wywołań między domenami od klientów przeglądarki JavaScript.  
  
##  <a name="AllowCrossDomainCalls"></a>Zezwalaj na połączenia między domenami  
 Użyj `cross-domain` zasad, aby udostępnić interfejsu API programu Adobe Flash i Microsoft Silverlight bazujące na przeglądarce klientów.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<cross-domain>  
   <!-Policy configuration is in the Adobe cross-domain policy file format,   
      see http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->  
</cross-domain>  
```  
  
### <a name="example"></a>Przykład  
  
```xml  
<cross-domain>  
    <cross-domain-policy>  
        <allow-http-request-headers-from domain='*' headers='*' />  
    </cross-domain-policy>  
</cross-domain>  
```  
  
### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagane|  
|----------|-----------------|--------------|  
|między domenami|Element główny. Elementy podrzędne muszą być zgodne z [specyfikacji pliku zasad międzydomenowych Adobe](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Tak|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
-   **Zakresy zasad:** globalne  
  
##  <a name="CORS"></a>CORS  
 `cors` Zasad dodaje współużytkowanie zasobów między źródłami (CORS) obsługi operacji lub interfejsu API w celu zapewnienia obsługi wywołań między domenami od klientów przeglądarki do udostępniania.  
  
 CORS pozwala przeglądarką a serwerem interakcji i określić, czy należy zezwolić określonym żądań cross-origin (tj. XMLHttpRequests wywołań z poziomu języka JavaScript na stronie sieci web do innych domen). To pozwala na większą elastyczność niż tylko zezwalanie żądania z tego samego źródła, ale jest bezpieczniejsze niż stosowanie wszystkich żądań cross-origin.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<cors allow-credentials="false|true">  
    <allowed-origins>  
        <origin>origin uri</origin>  
    </allowed-origins>  
    <allowed-methods preflight-result-max-age="number of seconds">  
        <method>http verb</method>  
    </allowed-methods>  
    <allowed-headers>  
        <header>header name</header>  
    </allowed-headers>  
    <expose-headers>  
        <header>header name</header>  
    </expose-headers>  
</cors>  
```  
  
### <a name="example"></a>Przykład  
 W tym przykładzie pokazano, jak obsługiwać żądania wstępnego transmitowane, takich jak niestandardowe nagłówki lub metod innych niż GET i POST. Aby obsługiwać niestandardowe nagłówki i dodatkowe zleceń HTTP, należy użyć `allowed-methods` i `allowed-headers` sekcjach przedstawiono, jak pokazano w poniższym przykładzie.  
  
```xml  
<cors allow-credentials="true">  
    <allowed-origins>  
        <!-- Localhost useful for development -->  
        <origin>http://localhost:8080/</origin>  
        <origin>http://example.com/</origin>  
    </allowed-origins>  
    <allowed-methods preflight-result-max-age="300">  
        <method>GET</method>  
        <method>POST</method>  
        <method>PATCH</method>  
        <method>DELETE</method>  
    </allowed-methods>  
    <allowed-headers>  
        <!-- Examples below show Azure Mobile Services headers -->  
        <header>x-zumo-installation-id</header>  
        <header>x-zumo-application</header>  
        <header>x-zumo-version</header>  
        <header>x-zumo-auth</header>  
        <header>content-type</header>  
        <header>accept</header>  
    </allowed-headers>  
    <expose-headers>  
        <!-- Examples below show Azure Mobile Services headers -->  
        <header>x-zumo-installation-id</header>  
        <header>x-zumo-application</header>  
    </expose-headers>  
</cors>  
```  
  
### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|cors|Element główny.|Tak|Nie dotyczy|  
|dozwolone źródła|Zawiera `origin` elementy, które opisują dozwolonych źródeł dla żądań między domenami. `allowed-origins`może zawierać pojedyncze `origin` element, który określa `*` zezwalająca na wszystkie pochodzenia, jeden lub więcej `origin` elementów, które zawierają identyfikator URI.|Tak|Nie dotyczy|  
|źródło|Wartość może być albo `*` zezwalająca na wszystkie pochodzenia lub identyfikator URI, który określa pojedynczy punkt początkowy. Identyfikator URI musi zawierać schemat, hosta i portu.|Tak|W przypadku pominięcia w identyfikatorze URI port jest używany port 80 dla protokołu HTTP i jest używany port 443 dla protokołu HTTPS.|  
|dozwolone metody|Ten element jest wymagany, jeśli metod innych niż GET lub POST są dozwolone. Zawiera `method` elementy, które określają obsługiwane polecenia HTTP.|Nie|Jeśli nie ma tej sekcji, są obsługiwane GET i POST.|  
|— Metoda|Określa zlecenie HTTP.|Co najmniej jeden `method` element jest wymagany, jeśli `allowed-methods` sekcja jest obecny.|Nie dotyczy|  
|dozwolone nagłówki|Ten element zawiera `header` elementy Określanie nazw nagłówków, które mogą znajdować się w żądaniu.|Nie|Nie dotyczy|  
|Uwidacznianie nagłówki|Ten element zawiera `header` elementy Określanie nazw nagłówków, które mają być dostępne dla klienta.|Nie|Nie dotyczy|  
|nagłówek|Określa nazwę nagłówka.|Co najmniej jeden `header` element jest wymagany w `allowed-headers` lub `expose-headers` sekcja jest obecny.|Nie dotyczy|  
  
### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|Zezwalaj na poświadczenia|`Access-Control-Allow-Credentials` Nagłówek odpowiedzi dotyczące stanu wstępnego zostanie ustawiona na wartość tego atrybutu i wpływa na możliwość klienta przesyłania poświadczeń w żądaniach między domenami.|Nie|fałsz|  
|Inspekcja result-max-age.|`Access-Control-Max-Age` Nagłówek odpowiedzi dotyczące stanu wstępnego zostanie ustawiona na wartość tego atrybutu i wpływać na zdolność agenta użytkownika do pamięci podręcznej przed transmitowane odpowiedzi.|Nie|0|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
-   **Zakresy zasad:** interfejsu API, operacji  
  
##  <a name="JSONP"></a>FORMAT JSONP  
 `jsonp` Zasad dodaje JSON z obsługą dopełnienie (JSONP) do operacji lub interfejsu API w celu zapewnienia obsługi wywołań między domenami od klientów przeglądarki JavaScript. Format JSONP jest metoda używana w programach języka JavaScript do żądania danych z serwera w innej domenie. JSONP pomija ograniczenia wymuszane przez większość przeglądarek internetowych, w którym dostęp do stron sieci web musi być w tej samej domenie.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<jsonp callback-parameter-name="callback function name" />  
```  
  
### <a name="example"></a>Przykład  
  
```xml  
<jsonp callback-parameter-name="cb" />  
```  
  
 Jeśli należy wywołać metodę bez parametru wywołania zwrotnego? cb = XXX zwróci JSON zwykły (bez otoka wywołania funkcji).  
  
 Jeśli dodasz parametru wywołania zwrotnego `?cb=XXX` będzie zwracać wynik JSONP, zawijania oryginalnego JSON wyników wokół funkcja wywołania zwrotnego, takich jak`XYZ('<json result goes here>');`  
  
### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagane|  
|----------|-----------------|--------------|  
|Format jsonp|Element główny.|Tak|  
  
### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|nazwy parametru wywołania zwrotnego|Wywołanie funkcji JavaScript między domenami prefiksem nazwy FQDN, w której znajduje się funkcja.|Tak|Nie dotyczy|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** ruchu wychodzącego  
-   **Zakresy zasad:** globalnych produktu interfejsu API, operacji  
  
## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, Praca z zasad Zobacz:

+ [Zasady w usłudze API Management](api-management-howto-policies.md)
+ [Przekształć interfejsów API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę deklaracji zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)   