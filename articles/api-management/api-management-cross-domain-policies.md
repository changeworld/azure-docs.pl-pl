---
title: Usługa Azure API Management cross zasady domeny | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zasady obejmujące różne domeny można używać w usłudze Azure API Management.
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
ms.openlocfilehash: 871294703a4be36e274df1e34b9cc9bee7d19783
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071951"
---
# <a name="api-management-cross-domain-policies"></a>API Management cross domain policies (Zasady usługi API Management obejmujące różne domeny)
Ten temat zawiera odwołania do następujących zasad usługi API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasad w usłudze API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CrossDomainPolicies"></a> Zasady międzydomenowe

- [Zezwalaj na międzydomenowe wywołania](api-management-cross-domain-policies.md#AllowCrossDomainCalls) — sprawia, że interfejs API dostępne od klientów programu Adobe Flash i Silverlight: Microsoft opartym na przeglądarce.
- [Mechanizm CORS](api-management-cross-domain-policies.md#CORS) — dodaje zasobów między źródłami sharing (CORS) obsługi operacji lub interfejsu API, aby zezwolić na międzydomenowe wywołania z klientów opartych na przeglądarce.
- [JSONP](api-management-cross-domain-policies.md#JSONP) — dodaje JSON z obsługą dopełnienie (JSONP) do operacji lub interfejsu API, aby zezwolić na międzydomenowe wywołania z klientów oparte na przeglądarce języka JavaScript.

## <a name="AllowCrossDomainCalls"></a> Zezwala na wywołania między domenami
Użyj `cross-domain` zasad, aby udostępnić interfejs API od klientów programu Adobe Flash i Silverlight: Microsoft opartym na przeglądarce.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
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

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|między domenami|Element główny. Elementy podrzędne muszą być zgodne z [specyfikacji pliku zasady międzydomenowe Adobe](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Tak|

### <a name="usage"></a>Sposób użycia
Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Sekcje zasad:** dla ruchu przychodzącego
- **Zakresy zasad:** globalne

## <a name="CORS"></a> CORS
`cors` Zasady dodaje zasobów między źródłami sharing (CORS) obsługi operacji lub interfejsu API, aby zezwolić na międzydomenowe wywołania z klientów opartych na przeglądarce.

Mechanizm CORS umożliwia przeglądarką a serwerem do interakcji i określić, czy należy zezwolić na określonych żądań cross-origin (czyli XMLHttpRequests wywołań z kodu JavaScript na stronie sieci web do innych domen). Umożliwia bardziej elastyczne niż tylko zezwolenie na żądania tego samego źródła, ale jest bezpieczniejszy niż co wszystkich żądań cross-origin.

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
W tym przykładzie pokazano, jak obsługiwać żądania krótkiej, takich jak nagłówki niestandardowe lub metod innych niż operacje GET i POST. Aby zapewnić obsługę niestandardowych nagłówków i dodatkowe zleceń HTTP, użyj `allowed-methods` i `allowed-headers` sekcje, jak pokazano w poniższym przykładzie.

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

|Name (Nazwa)|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|mechanizm cors|Element główny.|Tak|ND|
|dozwolone źródła|Zawiera `origin` elementy, które opisują dozwolonych źródeł dla żądań między domenami. `allowed-origins` może zawierać może to być pojedynczy `origin` element, który określa `*` zezwalająca na wszystkie pochodzenia, jeden lub więcej `origin` elementów, które zawierają identyfikator URI.|Tak|ND|
|źródło|Wartość może być `*` zezwalająca na wszystkie pochodzenia lub identyfikator URI, który określa pojedynczy punkt początkowy. Identyfikator URI musi zawierać schematu, hosta i portu.|Yes|W przypadku pominięcia port w identyfikatorze URI jest używany port 80 dla protokołu HTTP i port 443 jest używany do obsługi protokołu HTTPS.|
|dozwolone metody|Ten element jest wymagany, jeśli metod innych niż albo POST są dozwolone. Zawiera `method` elementy, które określają obsługiwane polecenia HTTP.|Nie|Jeśli nie ma w tej sekcji, są obsługiwane operacje GET i POST.|
|method|Określa zlecenie HTTP.|Co najmniej jeden `method` element jest wymagany, jeśli `allowed-methods` sekcja jest obecny.|ND|
|dozwolone nagłówki|Ten element zawiera `header` elementy Określanie nazw nagłówków, które mogły zostać uwzględnione w żądaniu.|Nie|ND|
|expose-headers.|Ten element zawiera `header` elementy Określanie nazw nagłówków, które będą dostępne przez klienta.|Nie|ND|
|nagłówek|Określa nazwę nagłówka.|Co najmniej jeden `header` element jest wymagany w `allowed-headers` lub `expose-headers` sekcja jest obecny.|ND|

### <a name="attributes"></a>Atrybuty

|Name (Nazwa)|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|Zezwalaj na poświadczenia|`Access-Control-Allow-Credentials` Nagłówek odpowiedzi wstępnego zostanie ustawiona na wartość tego atrybutu i wpływać na zdolność klienta, aby przesłać poświadczenia w żądaniach między domenami.|Nie|false|
|preflight-result-max-age|`Access-Control-Max-Age` Nagłówek odpowiedzi wstępnego zostanie ustawiona na wartość tego atrybutu i wpływać na zdolność agenta użytkownika do pamięci podręcznej krótkiej odpowiedzi.|Nie|0|

### <a name="usage"></a>Sposób użycia
Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Sekcje zasad:** dla ruchu przychodzącego
- **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

## <a name="JSONP"></a> JSONP
`jsonp` Zasad dodaje JSON z obsługą dopełnienie (JSONP) do operacji lub interfejsu API, aby zezwolić na międzydomenowe wywołania z klientów oparte na przeglądarce języka JavaScript. JSONP jest metodą używaną w programach języka JavaScript do dane żądania z serwera w innej domenie. JSONP pomija ograniczenia wymuszane przez większość przeglądarek internetowych, w którym dostęp do stron sieci web musi być w tej samej domenie.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Przykład

```xml
<jsonp callback-parameter-name="cb" />
```

Jeśli wywołanie metody bez parametru wywołania zwrotnego? cb = XXX zwróci zwykły JSON (bez otokę wywołania funkcji).

Jeśli dodasz parametru wywołania zwrotnego `?cb=XXX` zwróci wynik JSONP, zawijanie oryginalnego JSON wyników w okolicy funkcji wywołania zwrotnego, takich jak `XYZ('<json result goes here>');`

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|jsonp|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

|Name (Nazwa)|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|callback-parameter-name|Międzydomenowe wywołania funkcji JavaScript prefiksem nazwy FQDN, w której znajduje się funkcja.|Yes|ND|

### <a name="usage"></a>Sposób użycia
Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Sekcje zasad:** ruchu wychodzącego
- **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, w pracy z tymi zasadami zobacz:

+ [Zasady usługi API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)   
