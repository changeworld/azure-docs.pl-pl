---
title: Zasady ograniczeń dostępu w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zasady ograniczeń dostępu można używać w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: apimpm
ms.openlocfilehash: 8947637a42adfca12268c3f84e208079768870e0
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921224"
---
# <a name="api-management-access-restriction-policies"></a>Zasady ograniczeń dostępu do usługi API Management

Ten temat zawiera odwołania do następujących zasad usługi API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasad w usłudze API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AccessRestrictionPolicies"></a> Zasady ograniczeń dostępu

-   [Nagłówek HTTP wyboru](api-management-access-restriction-policies.md#CheckHTTPHeader) -wymusza obecność i/lub wartość nagłówka HTTP.
-   [Ograniczanie liczby wywołań według subskrypcji](api-management-access-restriction-policies.md#LimitCallRate) — użycie zapobiega interfejsu API gwałtowne wzrosty przez ograniczanie liczby wywołań, na podstawie każdej subskrypcji.
-   [Ograniczanie liczby wywołań według klucza](#LimitCallRateByKey) — użycie zapobiega interfejsu API gwałtowne wzrosty przez ograniczanie liczby wywołań, na podstawie na klucz.
-   [Ograniczenia adresów IP obiektu wywołującego](api-management-access-restriction-policies.md#RestrictCallerIPs) -wywołania filtrów (umożliwia/nie zezwala na) z określonych adresów IP i/lub zakresów adresów.
-   [Ustawianie przydziału użycia według subskrypcji](api-management-access-restriction-policies.md#SetUsageQuota) — umożliwia egzekwowanie odnawialnych lub okres istnienia woluminu i/lub przepustowości limit przydziału wywołania, na podstawie każdej subskrypcji.
-   [Ustawianie przydziału użycia według klucza](#SetUsageQuotaByKey) — umożliwia egzekwowanie odnawialnych lub okres istnienia woluminu i/lub przepustowości limit przydziału wywołania, na podstawie na klucz.
-   [Sprawdzanie poprawności tokenu JWT](api-management-access-restriction-policies.md#ValidateJWT) -wymusza istnienia i ważności wyodrębnione z określonego nagłówka HTTP lub parametr zapytania określony token JWT.

## <a name="CheckHTTPHeader"></a> Sprawdź nagłówka HTTP

Użyj `check-header` zasad w celu wymuszania, czy żądanie ma określonego nagłówka HTTP. Można opcjonalnie zaznacz, aby sprawdzić, czy nagłówek określonej wartości lub Wyszukaj dozwolonym zakresem wartości. W przypadku niepowodzenia sprawdzania zasad kończy przetwarzania żądania i zwraca komunikat stanu HTTP kodu i błąd określony przez zasady.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>Przykład

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>Elementy

| Name (Nazwa)         | Opis                                                                                                                                   | Wymagane |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-header | Element główny.                                                                                                                                 | Tak      |
| value        | Dozwolone wartości nagłówka HTTP. Jeśli określono wiele wartości elementów wyboru jest uznawany za sukces Jeśli którykolwiek z wartości jest zgodny. | Nie       |

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)                       | Opis                                                                                                                                                            | Wymagane | Domyślne |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | Komunikat o błędzie do zwrócenia w treści odpowiedzi HTTP, jeśli nagłówek nie istnieje lub ma nieprawidłową wartość. Ta wiadomość musi mieć żadnych znaków specjalnych, które zostały prawidłowo poprzedzone znakiem zmiany znaczenia. | Tak      | ND     |
| failed-check-httpcode      | Kod stanu HTTP do zwrócenia, jeśli nagłówek nie istnieje lub ma nieprawidłową wartość.                                                                                        | Tak      | ND     |
| header-name                | Nazwa nagłówka HTTP do sprawdzenia.                                                                                                                                  | Tak      | ND     |
| Ignoruj case                | Można ustawić na wartość True lub False. Jeśli ustawiona na wartość True, przypadek jest ignorowana, gdy wartość nagłówka jest porównywana zestaw wartości dozwolonych.                                    | Tak      | ND     |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego, ruchu wychodzącego

-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

## <a name="LimitCallRate"></a> Ograniczanie liczby wywołań według subskrypcji

`rate-limit` Zasady uniemożliwiają okresów zwiększonego użycia interfejsu API na podstawie każdej subskrypcji przez ograniczanie liczby wywołań do określonej liczby na określonym przedziale czasu. Gdy te zasady są wyzwalane obiekt wywołujący otrzymuje `429 Too Many Requests` kod stanu odpowiedzi.

> [!IMPORTANT]
> Ta zasada może służyć tylko raz dla dokumentu zasad.
>
> [Wyrażenia zasad](api-management-policy-expressions.md) nie można użyć w żadnym z atrybutów zasad dla tych zasad.

> [!CAUTION]
> Ograniczanie szybkości nigdy nie jest z powodu Rozproszony charakter ograniczania architektury dokładne. Różnica między skonfigurowany, a rzeczywistą liczbę dozwolone żądania różnią się zależnie od wolumin żądań i szybkości, opóźnienie zaplecza oraz innych czynników.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Name (Nazwa)      | Opis                                                                                                                                                                                                                                                                                              | Wymagane |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| set-limit | Element główny.                                                                                                                                                                                                                                                                                            | Tak      |
| interfejs API       | Dodaj jeden lub więcej z tych elementów, aby nałożyć limit szybkości wywołania interfejsów API w ramach produktu. Produkt i interfejsu API należy wywołać szybkość, z jaką ograniczenia są stosowane niezależnie. Interfejs API mogą być przywoływane za pośrednictwem `name` lub `id`. Jeśli oba atrybuty są dostarczane, `id` będą używane i `name` zostaną zignorowane.                    | Nie       |
| operacja | Dodaj jeden lub więcej z tych elementów na narzuca ograniczenia szybkości wywołanie operacji w obrębie interfejsu API. Liczby ograniczenia są stosowane niezależnie, z których można wywołań produktu, interfejsu API i operacji. Operacja mogą być przywoływane za pośrednictwem `name` lub `id`. Jeśli oba atrybuty są dostarczane, `id` będą używane i `name` zostaną zignorowane. | Nie       |

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)           | Opis                                                                                           | Wymagane | Domyślne |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | Nazwa interfejsu API, dla którego mają zostać zastosowane ograniczania liczby wywołań.                                                | Tak      | ND     |
| wywołania          | Maksymalna całkowita liczba wywołań określona w interwale `renewal-period`. | Tak      | ND     |
| okres odnowienia | Okres czasu w sekundach, po upływie których resetuje limitu przydziału.                                              | Tak      | ND     |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego

-   **Zakresy zasad:** produktu

## <a name="LimitCallRateByKey"></a> Ograniczanie liczby wywołań według klucza

> [!IMPORTANT]
> Ta funkcja jest niedostępna w **zużycie** warstwy usługi API Management.

`rate-limit-by-key` Zasady uniemożliwiają okresów zwiększonego użycia interfejsu API na podstawie klucza na przez ograniczanie liczby wywołań do określonej liczby na określonym przedziale czasu. Klucz może mieć wartość dowolny ciąg i zazwyczaj znajduje się za pomocą wyrażenia zasad. Aby określić, które żądania powinno być liczone na limit można dodać warunku opcjonalnie przyrostu. Gdy te zasady są wyzwalane obiekt wywołujący otrzymuje `429 Too Many Requests` kod stanu odpowiedzi.

Aby uzyskać więcej informacji i przykładów niniejszych zasad, zobacz [Zaawansowane żądanie ograniczania usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

> [!CAUTION]
> Ograniczanie szybkości nigdy nie jest z powodu Rozproszony charakter ograniczania architektury dokładne. Różnica między skonfigurowany, a rzeczywistą liczbę dozwolone żądania różnią się zależnie od wolumin żądań i szybkości, opóźnienie zaplecza oraz innych czynników.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Przykład

W poniższym przykładzie ograniczania liczby wywołań Zróżnicuj według adresu IP obiektu wywołującego.

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Name (Nazwa)      | Opis   | Wymagane |
| --------- | ------------- | -------- |
| set-limit | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)                | Opis                                                                                           | Wymagane | Domyślne |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| wywołania               | Maksymalna całkowita liczba wywołań określona w interwale `renewal-period`. | Tak      | ND     |
| klucz liczników         | Klucz do użycia dla zasad limitu szybkości.                                                             | Tak      | ND     |
| increment-condition | Wyrażenie logiczne, określając, jeśli żądanie powinno być liczone na limit przydziału (`true`).        | Nie       | ND     |
| okres odnowienia      | Okres czasu w sekundach, po upływie których resetuje limitu przydziału.                                              | Tak      | ND     |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego

-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

## <a name="RestrictCallerIPs"></a> Ograniczenia adresów IP obiektu wywołującego

`ip-filter` Zasad filtry (umożliwia/nie zezwala na) wywołań z określonych adresów IP i/lub zakresów adresów.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Przykład

W poniższym przykładzie zasady umożliwiają tylko żądań pochodzących z jednego adresu IP lub zakres adresów IP określony

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elementy

| Name (Nazwa)                                      | Opis                                         | Wymagane                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | Element główny.                                       | Tak                                                            |
| adres                                   | Określa pojedynczy adres IP, według której chcesz filtrować.   | Co najmniej jeden `address` lub `address-range` element jest wymagany. |
| zakres adresów z = "address", aby = "address" | Określa adres zakresu adresów IP, według której chcesz filtrować. | Co najmniej jeden `address` lub `address-range` element jest wymagany. |

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)                                      | Opis                                                                                 | Wymagane                                           | Domyślne |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| zakres adresów z = "address", aby = "address" | Zakres adresów IP, aby udzielić lub odmówić dostępu.                                        | Wymagany, gdy `address-range` element jest używany. | ND     |
| akcji filtrowania adresów IP = "Zezwalaj na &#124; zabraniają"    | Określa, czy powinno być dozwolone wywołania nie dla określonych adresów IP i zakresów. | Tak                                                | ND     |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego
-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

## <a name="SetUsageQuota"></a> Ustawianie przydziału użycia według subskrypcji

`quota` Zasada wymusza odnawialnych lub okres istnienia woluminu i/lub przepustowości limit przydziału wywołania, na podstawie każdej subskrypcji.

> [!IMPORTANT]
> Ta zasada może służyć tylko raz dla dokumentu zasad.
>
> [Wyrażenia zasad](api-management-policy-expressions.md) nie można użyć w żadnym z atrybutów zasad dla tych zasad.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Name (Nazwa)      | Opis                                                                                                                                                                                                                                                                                  | Wymagane |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | Element główny.                                                                                                                                                                                                                                                                                | Tak      |
| interfejs API       | Dodaj jeden lub więcej z tych elementów, aby nałożyć limit przydziału wywołania interfejsów API w ramach produktu. Produkt i limity przydziału wywołań interfejsu API są stosowane niezależnie. Interfejs API mogą być przywoływane za pośrednictwem `name` lub `id`. Jeśli oba atrybuty są dostarczane, `id` będą używane i `name` zostaną zignorowane.                    | Nie       |
| operacja | Dodaj jeden lub więcej z tych elementów, aby nałożyć limit przydziału wywołania operacji w ramach interfejsu API. Limity przydziału wywołań produktu, interfejsu API i operacji są stosowane niezależnie. Operacja mogą być przywoływane za pośrednictwem `name` lub `id`. Jeśli oba atrybuty są dostarczane, `id` będą używane i `name` zostaną zignorowane. | Nie       |

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)           | Opis                                                                                               | Wymagane                                                         | Domyślne |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | Nazwa interfejsu API lub operacji, dla którego ma zastosowanie limitu przydziału.                                             | Tak                                                              | ND     |
| przepustowość      | Maksymalna całkowita liczba kilobajtów dozwolone określona w interwale `renewal-period`. | Albo `calls`, `bandwidth`, lub ze sobą musi być jednocześnie określone. | ND     |
| wywołania          | Maksymalna całkowita liczba wywołań określona w interwale `renewal-period`.     | Albo `calls`, `bandwidth`, lub ze sobą musi być jednocześnie określone. | ND     |
| okres odnowienia | Okres czasu w sekundach, po upływie których resetuje limitu przydziału.                                                  | Tak                                                              | ND     |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego
-   **Zakresy zasad:** produktu

## <a name="SetUsageQuotaByKey"></a> Ustawianie przydziału użycia klucza

> [!IMPORTANT]
> Ta funkcja jest niedostępna w **zużycie** warstwy usługi API Management.

`quota-by-key` Zasada wymusza odnawialnych lub okres istnienia woluminu i/lub przepustowości limit przydziału wywołania, na podstawie na klucz. Klucz może mieć wartość dowolny ciąg i zazwyczaj znajduje się za pomocą wyrażenia zasad. Aby określić, które żądania powinno być liczone na limit przydziału można dodać warunku opcjonalnie przyrostu. Jeśli wiele zasad będzie zwiększa się taką samą wartość klucza, ten numer jest zwiększany tylko raz na każde żądanie. Po osiągnięciu limitu wywołania, otrzymuje obiekt wywołujący `403 Forbidden` kod stanu odpowiedzi.

Aby uzyskać więcej informacji i przykładów niniejszych zasad, zobacz [Zaawansowane żądanie ograniczania usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Przykład

W poniższym przykładzie limit przydziału Zróżnicuj według adresu IP obiektu wywołującego.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Name (Nazwa)  | Opis   | Wymagane |
| ----- | ------------- | -------- |
| quota | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)                | Opis                                                                                               | Wymagane                                                         | Domyślne |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| przepustowość           | Maksymalna całkowita liczba kilobajtów dozwolone określona w interwale `renewal-period`. | Albo `calls`, `bandwidth`, lub ze sobą musi być jednocześnie określone. | ND     |
| wywołania               | Maksymalna całkowita liczba wywołań określona w interwale `renewal-period`.     | Albo `calls`, `bandwidth`, lub ze sobą musi być jednocześnie określone. | ND     |
| klucz liczników         | Klucz do użycia zasad limitu przydziału.                                                                      | Tak                                                              | ND     |
| increment-condition | Wyrażenie logiczne, określając, jeśli żądanie powinno być liczone na limit przydziału (`true`)             | Nie                                                               | ND     |
| okres odnowienia      | Okres czasu w sekundach, po upływie których resetuje limitu przydziału.                                                  | Tak                                                              | ND     |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego
-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

## <a name="ValidateJWT"></a> Sprawdzanie poprawności tokenu JWT

`validate-jwt` Zasada wymusza obecność i ważności token JWT wyodrębnione z dowolnego określonego nagłówka HTTP lub parametr zapytania określony.

> [!IMPORTANT]
> `validate-jwt` Zasad wymaga, aby `exp` zarejestrowanych oświadczenia znajduje się w JWT token, chyba że `require-expiration-time` atrybut jest określona i ustawiona na `false`.
> `validate-jwt` Zasady obsługuje algorytmy podpisywania HS256 i RS256. HS256 klucza musi być podana wbudowane w zasady w formie zakodowane w formacie base64. Ma klucz RS256 zapewnienie za pośrednictwem punktu końcowego Otwórz identyfikator konfiguracji.
> `validate-jwt` Zasady obsługują szyfrowane przy użyciu kluczy symetrycznych przy użyciu następujących algorytmów szyfrowania A128CBC-HS256 A192CBC-HS384 A256CBC HS512 tokenów.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <zumo-master-key id="key identifier">key value</zumo-master-key>
</validate-jwt>

```

### <a name="examples"></a>Przykłady

#### <a name="simple-token-validation"></a>Prostej weryfikacji tokenu

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Weryfikacja tokenu usługi Azure Active Directory

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Weryfikacja tokenu usługi Azure Active Directory B2C

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Autoryzowanie dostępu do operacji na podstawie tokenu oświadczeń

W tym przykładzie pokazano, jak używać [weryfikacji tokenu JWT](api-management-access-restriction-policies.md#ValidateJWT) zasad do autoryzowania dostępu do operacji na podstawie oświadczeń tokenu wartości.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

#### <a name="azure-mobile-services-token-validation"></a>Usługa Azure Mobile Services weryfikacji tokenu

```xml
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">
    <issuers>
        <issuer>urn:microsoft:windows-azure:zumo</issuer>
    </issuers>
    <audiences>
        <audience>Facebook</audience>
    </audiences>
    <issuer-signing-keys>
        <zumo-master-key id="0">insert key here</zumo-master-key>
    </issuer-signing-keys>
</validate-jwt>
```

### <a name="elements"></a>Elementy

| Element             | Opis                                                                                                                                                                                                                                                                                                                                           | Wymagane |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | Element główny.                                                                                                                                                                                                                                                                                                                                         | Tak      |
| grupy odbiorców           | Zawiera listę oświadczeń dopuszczalne odbiorców, które mogą być obecne w tokenie. Jeśli istnieją wartości wielu odbiorców, a następnie wypróbowuje każdej wartości do momentu wszystkie wyczerpania (w którym to przypadku niepowodzenia weryfikacji) lub aż któraś się powiedzie. Należy określić co najmniej jednego odbiorcy.                                                                     | Nie       |
| issuer-signing-keys | Lista kluczy algorytmem Base64 zabezpieczeń służący do weryfikowania podpisanych tokenów. Jeśli wiele kluczy zabezpieczeń są obecne, a następnie każdy klucz zostanie podjęta próba do momentu wszystkie wyczerpania (w którym to przypadku niepowodzenia weryfikacji) lub aż któraś się powiedzie (przydatne w przypadku przerzucenia tokenu). Kluczowe elementy mieć opcjonalną `id` atrybut używany do dopasowywania `kid` oświadczenia.               | Nie       |
| decryption-keys     | Lista kluczy algorytmem Base64, używany do odszyfrowywania tokenów. Jeśli wiele kluczy zabezpieczeń są obecne, każdy klucz zostanie wybrany do momentu albo wszystkie klucze są wyczerpane (w którym to przypadku niepowodzenia weryfikacji) lub klucza zakończy się pomyślnie. Kluczowe elementy mieć opcjonalną `id` atrybut używany do dopasowywania `kid` oświadczenia.                                                 | Nie       |
| wystawcy             | Lista dopuszczalne podmiotów zabezpieczeń, które wystawiony token. Jeśli wiele wartości wystawcy są obecne, a następnie wypróbowuje każdej wartości do momentu wszystkie wyczerpania (w którym to przypadku niepowodzenia weryfikacji) lub aż któraś się powiedzie.                                                                                                                                         | Nie       |
| Konfiguracja protokołu openid       | Element używany do określania zgodności Otwórz identyfikator konfiguracji punktu końcowego z którego podpisywania kluczy i Wystawca można uzyskać.                                                                                                                                                                                                                        | Nie       |
| required-claims     | Zawiera listę oświadczeń powinny być obecne w tokenie, aby były uważane za prawidłowe. Gdy `match` ma ustawioną wartość atrybutu `all` każda wartość oświadczenia w zasadach musi być obecne w tokenie do weryfikacji została wykonana pomyślnie. Gdy `match` ma ustawioną wartość atrybutu `any` co najmniej jedno oświadczenie musi być obecne w tokenie do weryfikacji została wykonana pomyślnie. | Nie       |
| zumo-master-key     | Klucz główny dla tokeny wystawione przez usług Azure Mobile Services                                                                                                                                                                                                                                                                                                 | Nie       |

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)                            | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                            | Wymagane                                                                         | Domyślne                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| przesunięcia czasowego zegara                      | Zakres czasu. Użyj, aby określić maksymalną oczekiwanego różnicę czasu między zegarami systemowymi wystawcy tokenów i wystąpienia usługi API Management.                                                                                                                                                                                                                                                                                                               | Nie                                                                               | 0 sekund                                                                         |
| failed-validation-error-message | Komunikat o błędzie do zwrócenia w treści odpowiedzi HTTP, jeśli tokenu JWT nie przeszły pomyślnie sprawdzania poprawności. Ta wiadomość musi mieć żadnych znaków specjalnych, które zostały prawidłowo poprzedzone znakiem zmiany znaczenia.                                                                                                                                                                                                                                                                                                 | Nie                                                                               | Domyślny komunikat o błędzie, który jest zależny od problem ze sprawdzaniem poprawności, na przykład "JWT nie istnieje." |
| failed-validation-httpcode      | Kod stanu HTTP do zwrócenia, jeśli tokenu JWT nie przeszło weryfikacji.                                                                                                                                                                                                                                                                                                                                                                                         | Nie                                                                               | 401                                                                               |
| header-name                     | Nazwa nagłówka HTTP zawierający tokenu.                                                                                                                                                                                                                                                                                                                                                                                                         | Jedną z `header-name`, `query-parameter-name` lub `token-value` musi być określona. | ND                                                                               |
| query-parameter-name            | Nazwa parametru zapytania, zawierający tokenu.                                                                                                                                                                                                                                                                                                                                                                                                     | Jedną z `header-name`, `query-parameter-name` lub `token-value` musi być określona. | ND                                                                               |
| wartość tokenu                     | Wyrażenie zwracające ciąg zawierający JWT token                                                                                                                                                                                                                                                                                                                                                                                                     | Jedną z `header-name`, `query-parameter-name` lub `token-value` musi być określona. | ND                                                                               |
| identyfikator                              | `id` Atrybutu na `key` element można określić ciąg, który dopasowywane `kid` oświadczenia w tokenie (jeśli istnieje) dowiedzieć się, odpowiedni klucz do użycia w celu weryfikacji podpisu.                                                                                                                                                                                                                                           | Nie                                                                               | ND                                                                               |
| dopasowanie                           | `match` Atrybutu na `claim` element określa, czy każda wartość oświadczenia w ramach zasad musi być obecne w tokenie do weryfikacji została wykonana pomyślnie. Możliwe wartości to:<br /><br /> - `all` — Każda wartość oświadczenia w zasadach musi być obecne w tokenie do weryfikacji została wykonana pomyślnie.<br /><br /> - `any` -wartość co najmniej jedno oświadczenie musi być obecne w tokenie do weryfikacji została wykonana pomyślnie.                                                       | Nie                                                                               | Wszystko                                                                               |
| require-expiration-time         | Wartość logiczna. Określa, czy oświadczeniu wygaśnięcia jest wymagany w tokenie.                                                                                                                                                                                                                                                                                                                                                                               | Nie                                                                               | true                                                                              |
| require-scheme                  | Nazwa tokenu schemat, np. "Bearer". Gdy ten atrybut jest ustawiony, zasady zapewni, że określony schemat jest obecny w wartości nagłówka autoryzacji.                                                                                                                                                                                                                                                                                    | Nie                                                                               | ND                                                                               |
| Wymagaj podpisane — tokeny zabezpieczające           | Wartość logiczna. Określa, czy token jest wymagany do podpisania.                                                                                                                                                                                                                                                                                                                                                                                           | Nie                                                                               | true                                                                              |
| Separator                       | ciąg. Określa separator (np. ",") ma być używany do wyodrębniania zestawu wartości z oświadczeń wielowartościowe.                                                                                                                                                                                                                                                                                                                                          | Nie                                                                               | ND                                                                               |
| url                             | Otwórz adres URL punktu końcowego konfiguracji identyfikator, z którym można uzyskać metadanych konfiguracji Open ID. Odpowiedź musi być zgodny specyfikacje, zgodnie z definicją pod adresem URL:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`. Usługi Azure Active Directory, użyj następującego adresu URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` podstawiając nazwy dzierżawy katalogu, np. `contoso.onmicrosoft.com`. | Tak                                                                              | ND                                                                               |
dane wyjściowe — token-— nazwa zmiennej|ciąg. Nazwa zmiennej kontekstu, który otrzyma wartość tokenu jako obiekt typu [ `Jwt` ](api-management-policy-expressions.md) po pomyślnej weryfikacji tokenu|Nie|ND

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego
-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, w pracy z tymi zasadami zobacz:

-   [Zasady usługi API Management](api-management-howto-policies.md)
-   [Przekształć interfejsy API](transform-api.md)
-   [Informacje o zasadach](api-management-policy-reference.md) pełną listę zasad i ich ustawienia
-   [Przykłady zasad](policy-samples.md)
