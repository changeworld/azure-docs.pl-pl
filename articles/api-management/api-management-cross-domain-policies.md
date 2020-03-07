---
title: Zasady międzydomenowe platformy Azure API Management | Microsoft Docs
description: Dowiedz się więcej o zasadach międzydomenowych dostępnych do użycia w usłudze Azure API Management.
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
ms.openlocfilehash: db64a2f64c592a62f621355047a7bc9844d66457
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375072"
---
# <a name="api-management-cross-domain-policies"></a>API Management cross domain policies (Zasady usługi API Management obejmujące różne domeny)
Ten temat zawiera informacje dotyczące następujących zasad API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasady w API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CrossDomainPolicies"></a>Zasady międzydomenowe

- [Zezwalaj na wywołania międzydomenowe](api-management-cross-domain-policies.md#AllowCrossDomainCalls) — umożliwia dostęp do interfejsu API z poziomu klientów opartych na przeglądarce Adobe Flash i Microsoft Silverlight.
- [CORS](api-management-cross-domain-policies.md#CORS) — dodaje obsługę funkcji udostępniania zasobów między źródłami (CORS) do operacji lub interfejsu API w celu zezwalania na wywołania międzydomenowe z klientów opartych na przeglądarce.
- [JSONP](api-management-cross-domain-policies.md#JSONP) — dodaje kod JSON z obsługą dopełnienia (JSONP) do operacji lub interfejsu API w celu zezwalania na wywołania międzydomenowe z klientów opartych na przeglądarce JavaScript.

## <a name="AllowCrossDomainCalls"></a>Zezwalaj na wywołania między domenami
Użyj zasad `cross-domain`, aby uzyskać dostęp do interfejsu API z poziomu klientów opartych na przeglądarce Adobe Flash i Microsoft Silverlight.

### <a name="policy-statement"></a>Instrukcja zasad

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

|Name (Nazwa)|Opis|Wymagany|
|----------|-----------------|--------------|
|między domenami|Element główny. Elementy podrzędne muszą być zgodne ze [specyfikacją pliku zasad między domenami Adobe](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Yes|

### <a name="usage"></a>Sposób użycia
Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

- **Sekcje zasad:** przychodzące
- **Zakresy zasad:** wszystkie zakresy

## <a name="CORS"></a>SPECYFIKACJI
Zasada `cors` dodaje obsługę funkcji udostępniania zasobów między źródłami (CORS) do operacji lub interfejsu API w celu zezwalania na wywołania międzydomenowe z klientów opartych na przeglądarce.

Mechanizm CORS pozwala przeglądarce i serwerowi na współpracujące i określać, czy zezwolić na określone żądania między źródłami (tj. wywołania XMLHttpRequest wykonane ze skryptu JavaScript na stronie sieci Web do innych domen). Zapewnia to większą elastyczność niż Zezwalanie na żądania tego samego źródła, ale jest bezpieczniejsze niż Zezwalanie na wszystkie żądania między źródłami.

### <a name="policy-statement"></a>Instrukcja zasad

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
W tym przykładzie pokazano, jak obsługiwać żądania przed inspekcją, takie jak te z niestandardowymi nagłówkami lub metodami innymi niż GET i POST. Aby obsługiwać niestandardowe nagłówki i dodatkowe czasowniki HTTP, należy użyć sekcji `allowed-methods` i `allowed-headers`, jak pokazano w poniższym przykładzie.

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

|Name (Nazwa)|Opis|Wymagany|Domyślne|
|----------|-----------------|--------------|-------------|
|specyfikacji|Element główny.|Yes|Nie dotyczy|
|dozwolone źródła|Zawiera elementy `origin`, które opisują dozwolone źródła dla żądań międzydomenowych. `allowed-origins` może zawierać pojedynczy `origin` element, który określa `*`, aby zezwolić na dowolne źródło, lub jeden lub więcej elementów `origin`, które zawierają identyfikator URI.|Yes|Nie dotyczy|
|źródło|Wartość może być `*`, aby zezwolić na wszystkie źródła, lub identyfikator URI, który określa pojedyncze źródło. Identyfikator URI musi zawierać schemat, hosta i port.|Yes|Jeśli port zostanie pominięty w identyfikatorze URI, port 80 jest używany dla protokołu HTTP, a port 443 jest używany w przypadku protokołu HTTPS.|
|dozwolone — metody|Ten element jest wymagany, jeśli dozwolone są metody inne niż GET lub POST. Zawiera elementy `method`, które określają obsługiwane zlecenia HTTP.|Nie|Jeśli ta sekcja nie jest obecna, obsługiwane są pozycje GET i POST.|
|method|Określa czasownik HTTP.|Jeśli istnieje sekcja `allowed-methods`, wymagany jest co najmniej jeden element `method`.|Nie dotyczy|
|dozwolone — nagłówki|Ten element zawiera `header` elementy określające nazwy nagłówków, które mogą zostać uwzględnione w żądaniu.|Nie|Nie dotyczy|
|Uwidacznianie — nagłówki|Ten element zawiera `header` elementy określające nazwy nagłówków, które będą dostępne dla klienta.|Nie|Nie dotyczy|
|nagłówek|Określa nazwę nagłówka.|W `allowed-headers` lub `expose-headers`, jeśli znajduje się sekcja, wymagany jest co najmniej jeden element `header`.|Nie dotyczy|

### <a name="attributes"></a>Atrybuty

|Name (Nazwa)|Opis|Wymagany|Domyślne|
|----------|-----------------|--------------|-------------|
|Zezwalaj — poświadczenia|W nagłówku `Access-Control-Allow-Credentials` w odpowiedzi na inspekcję wstępną zostanie ustawiona wartość tego atrybutu i będzie ona mieć wpływ na zdolność klienta do przesyłania poświadczeń w żądaniach międzydomenowych.|Nie|false|
|preflight-result-max-age|W nagłówku `Access-Control-Max-Age` w odpowiedzi na inspekcję wstępną zostanie ustawiona wartość tego atrybutu i będzie to miało wpływ na zdolność agenta użytkownika do buforowania odpowiedzi przed lotem.|Nie|0|

### <a name="usage"></a>Sposób użycia
Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

- **Sekcje zasad:** przychodzące
- **Zakresy zasad:** wszystkie zakresy

## <a name="JSONP"></a>JSONP
Zasada `jsonp` dodaje kod JSON z obsługą dopełnienia (JSONP) do operacji lub interfejsu API w celu zezwalania na wywołania międzydomenowe z klientów korzystających z przeglądarki JavaScript. JSONP to metoda używana w programach JavaScript do żądania danych z serwera w innej domenie. JSONP pomija ograniczenia wymuszane przez większość przeglądarek sieci Web, w których dostęp do stron sieci Web musi znajdować się w tej samej domenie.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Przykład

```xml
<jsonp callback-parameter-name="cb" />
```

Jeśli wywołasz metodę bez parametru wywołania zwrotnego? CB = XXX, zwróci on zwykły kod JSON (bez otoki wywołania funkcji).

Jeśli dodasz parametr wywołania zwrotnego, `?cb=XXX` zwróci wynik JSONP, Zawijaj oryginalne wyniki JSON wokół funkcji wywołania zwrotnego, takiej jak `XYZ('<json result goes here>');`

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagany|
|----------|-----------------|--------------|
|JSONP|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

|Name (Nazwa)|Opis|Wymagany|Domyślne|
|----------|-----------------|--------------|-------------|
|callback-parameter-name|Międzydomenowe wywołanie funkcji JavaScript poprzedzone prefiksem w pełni kwalifikowanej nazwy domeny, w której znajduje się funkcja.|Yes|Nie dotyczy|

### <a name="usage"></a>Sposób użycia
Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

- **Sekcje zasad:** wychodzące
- **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Dokumentacja zasad](api-management-policy-reference.md) pełna lista instrukcji zasad i ich ustawień
+ [Przykłady zasad](policy-samples.md)
