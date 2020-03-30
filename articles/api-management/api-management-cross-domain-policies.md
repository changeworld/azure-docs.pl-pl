---
title: Zasady międzydomenowe usługi Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się więcej o zasadach między domenami dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: b72abf4e208c57987375a105865046f194460058
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265989"
---
# <a name="api-management-cross-domain-policies"></a>API Management cross domain policies (Zasady usługi API Management obejmujące różne domeny)
Ten temat zawiera odwołanie do następujących zasad zarządzania interfejsami API. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [Zasady w zarządzania interfejsami API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a>Zasady między domenami

- [Zezwalaj na połączenia między domenami](api-management-cross-domain-policies.md#AllowCrossDomainCalls) — umożliwia dostęp interfejsu API od klientów korzystających z przeglądarki Adobe Flash i Microsoft Silverlight.
- [CORS](api-management-cross-domain-policies.md#CORS) — dodaje obsługę udostępniania zasobów między źródłami (CORS) do operacji lub interfejsu API, aby umożliwić wywołania między domenami od klientów opartych na przeglądarce.
- [JSONP](api-management-cross-domain-policies.md#JSONP) — dodaje JSON z obsługą dopełnienia (JSONP) do operacji lub interfejsu API, aby umożliwić wywołania między domenami z klientów opartych na przeglądarce JavaScript.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a>Zezwalaj na połączenia między domenami
Użyj `cross-domain` tych zasad, aby interfejs API był dostępny dla klientów korzystających z przeglądarki Adobe Flash i Microsoft Silverlight.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Przykład

```xml
<cross-domain>
    <cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
    </cross-domain>
</cross-domain>
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|między domenami|Element główny. Elementy podrzędne muszą być zgodne [ze specyfikacją pliku zasad adobe dotyczących różnych domen](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Tak|

### <a name="usage"></a>Sposób użycia
Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

- **Sekcje zasad:** przychodzące
- **Zakresy zasad:** wszystkie zakresy

## <a name="cors"></a><a name="CORS"></a>Cors
Zasady `cors` dodaje obsługę udostępniania zasobów między źródłami (CORS) do operacji lub interfejsu API, aby umożliwić wywołania między domenami od klientów opartych na przeglądarce.

Usługa CORS umożliwia przeglądarce i serwerowi interakcję i określenie, czy zezwolić na określone żądania między źródłami (tj. Pozwala to na większą elastyczność niż tylko zezwalanie na żądania tego samego pochodzenia, ale jest bezpieczniejsze niż zezwalanie na wszystkie żądania cross-origin.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

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
W tym przykładzie pokazano, jak obsługiwać żądania przed lotem, takie jak te z niestandardowymi nagłówkami lub metodami innymi niż GET i POST. Aby obsługiwać niestandardowe nagłówki i dodatkowe `allowed-methods` `allowed-headers` zlecenia HTTP, należy użyć i sekcje, jak pokazano w poniższym przykładzie.

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

|Nazwa|Opis|Wymagany|Domyślne|
|----------|-----------------|--------------|-------------|
|Cors|Element główny.|Tak|Nie dotyczy|
|dozwolonych początków|Zawiera `origin` elementy opisujące dozwolone źródła dla żądań między domenami. `allowed-origins`może zawierać pojedynczy `origin` element, który `*` określa, aby zezwolić `origin` na dowolne pochodzenie lub jeden lub więcej elementów, które zawierają identyfikator URI.|Tak|Nie dotyczy|
|połączenie pierwotne|Wartość może być `*` albo zezwolić na wszystkie źródła lub identyfikator URI, który określa pojedyncze pochodzenie. Identyfikator URI musi zawierać schemat, host i port.|Tak|Jeśli port zostanie pominięty w identyfikatorze URI, port 80 jest używany dla protokołu HTTP, a port 443 jest używany dla protokołu HTTPS.|
|dozwolone metody|Ten element jest wymagany, jeśli metody inne niż GET lub POST są dozwolone. Zawiera `method` elementy, które określają obsługiwane zlecenia HTTP. Wartość `*` wskazuje wszystkie metody.|Nie|Jeśli ta sekcja nie jest obecny, GET i POST są obsługiwane.|
|method|Określa zlecenie HTTP.|Co najmniej `method` jeden element jest `allowed-methods` wymagany, jeśli sekcja jest obecny.|Nie dotyczy|
|dozwolone nagłówki|Ten element `header` zawiera elementy określające nazwy nagłówków, które mogą być uwzględnione w żądaniu.|Nie|Nie dotyczy|
|expose-nagłówki|Ten element `header` zawiera elementy określające nazwy nagłówków, które będą dostępne dla klienta.|Nie|Nie dotyczy|
|nagłówek|Określa nazwę nagłówka.|Co najmniej `header` jeden element `allowed-headers` jest `expose-headers` wymagany w lub jeśli sekcja jest obecny.|Nie dotyczy|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagany|Domyślne|
|----------|-----------------|--------------|-------------|
|zezwalaj na poświadczenia|Nagłówek `Access-Control-Allow-Credentials` w odpowiedzi na dane wstępne zostanie ustawiony na wartość tego atrybutu i wpłynie na zdolność klienta do przesyłania poświadczeń w żądaniach między domenami.|Nie|false|
|preflight-result-max-age|Nagłówek `Access-Control-Max-Age` w odpowiedzi inspekcji wstępnej zostanie ustawiony na wartość tego atrybutu i wpłynie na zdolność agenta użytkownika do buforowania odpowiedzi przed lotem.|Nie|0|

### <a name="usage"></a>Sposób użycia
Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

- **Sekcje zasad:** przychodzące
- **Zakresy zasad:** wszystkie zakresy

## <a name="jsonp"></a><a name="JSONP"></a>Jsonp
Zasady `jsonp` dodaje JSON z obsługą dopełnienia (JSONP) do operacji lub interfejsu API, aby umożliwić wywołania między domenami z klientów opartych na przeglądarce JavaScript. JSONP jest metodą używaną w programach JavaScript do żądania danych z serwera w innej domenie. JSONP pomija ograniczenia wymuszane przez większość przeglądarek internetowych, w których dostęp do stron internetowych musi znajdować się w tej samej domenie.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Przykład

```xml
<jsonp callback-parameter-name="cb" />
```

Jeśli wywołasz metodę bez parametru wywołania zwrotnego ?cb=XXX, zwróci zwykły JSON (bez otoki wywołania funkcji).

Jeśli dodasz parametr `?cb=XXX` wywołania zwrotnego, zwróci wynik JSONP, zawijanie oryginalnych wyników JSON wokół funkcji wywołania zwrotnego, takich jak`XYZ('<json result goes here>');`

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|Jsonp|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagany|Domyślne|
|----------|-----------------|--------------|-------------|
|nazwa wywołania zwrotnego-parametr-name|Międzydomenowe wywołanie funkcji JavaScript poprzedzone w pełni kwalifikowaną nazwą domeny, w której znajduje się funkcja.|Tak|Nie dotyczy|

### <a name="usage"></a>Sposób użycia
Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

- **Sekcje zasad: wychodzące**
- **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w zarządzaniu interfejsami API](api-management-howto-policies.md)
+ [Przekształcanie interfejsów API](transform-api.md)
+ [Odwołanie do zasad](api-management-policy-reference.md) dla pełnej listy oświadczeń zasad i ich ustawień
+ [Przykłady zasad](policy-samples.md)
