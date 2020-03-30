---
title: Zasady ograniczeń dostępu usługi Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się więcej o zasadach ograniczeń dostępu dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 3ba620d66b84e6724751b2024059e8ecd66888cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266119"
---
# <a name="api-management-access-restriction-policies"></a>Zasady ograniczeń dostępu usługi API Management

Ten temat zawiera odwołanie do następujących zasad zarządzania interfejsami API. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [Zasady w zarządzania interfejsami API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a>Zasady ograniczeń dostępu

-   [Sprawdź nagłówek HTTP](api-management-access-restriction-policies.md#CheckHTTPHeader) — wymusza istnienie i/lub wartość nagłówka HTTP.
-   [Ogranicz szybkość wywołania według subskrypcji](api-management-access-restriction-policies.md#LimitCallRate) — zapobiega skokom użycia interfejsu API przez ograniczenie szybkości wywołania na podstawie subskrypcji.
-   [Ogranicz szybkość wywołania według klucza](#LimitCallRateByKey) — zapobiega skokom użycia interfejsu API, ograniczając szybkość wywołania na podstawie klucza.
-   [Ogranicz adresy IP wywołującego](api-management-access-restriction-policies.md#RestrictCallerIPs) — filtry (zezwala/odrzuca) wywołania z określonych adresów IP i/lub zakresów adresów.
-   [Ustaw przydział użycia według subskrypcji](api-management-access-restriction-policies.md#SetUsageQuota) — umożliwia wymuszanie przydziału woluminu i/lub przepustowości dla odnawialnych lub dożywotnich połączeń na podstawie subskrypcji.
-   [Ustaw przydział użycia według klucza](#SetUsageQuotaByKey) — umożliwia wymuszanie przydziału woluminu i/lub przepustowości w okresie dochodzenia w okresie dochodzenia 10 lub 10 000 000 000 000 000 000 000 000 000 000 000 000 000
-   [Sprawdź poprawność JWT](api-management-access-restriction-policies.md#ValidateJWT) — wymusza istnienie i ważność JWT wyodrębnione z określonego nagłówka HTTP lub określonego parametru kwerendy.

> [!TIP]
> Zasady ograniczeń dostępu można używać w różnych zakresach do różnych celów. Na przykład można zabezpieczyć cały interfejs API za pomocą `validate-jwt` uwierzytelniania usługi AAD, stosując zasady na poziomie `claims` interfejsu API lub można zastosować go na poziomie operacji interfejsu API i użyć do kontroli bardziej szczegółowe.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a>Sprawdź nagłówek HTTP

Użyj `check-header` zasad, aby wymusić, że żądanie ma określony nagłówek HTTP. Opcjonalnie można sprawdzić, czy nagłówek ma określoną wartość lub sprawdzić zakres dozwolonych wartości. Jeśli sprawdzanie zakończy się niepowodzeniem, zasada kończy przetwarzanie żądań i zwraca kod stanu HTTP i komunikat o błędzie określony przez zasady.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

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

| Nazwa         | Opis                                                                                                                                   | Wymagany |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| nagłówek czeku | Element główny.                                                                                                                                 | Tak      |
| value        | Dozwolona wartość nagłówka HTTP. Gdy określono wiele elementów wartości, sprawdzanie jest uważane za powodzenie, jeśli jedna z wartości jest dopasowanie. | Nie       |

### <a name="attributes"></a>Atrybuty

| Nazwa                       | Opis                                                                                                                                                            | Wymagany | Domyślne |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| nie powiodło się-check-error-message | Komunikat o błędzie do zwrócenia w treści odpowiedzi HTTP, jeśli nagłówek nie istnieje lub ma nieprawidłową wartość. Ten komunikat musi mieć wszystkie znaki specjalne poprawnie zmienione. | Tak      | Nie dotyczy     |
| nie powiodło się-check-httpcode      | Kod stanu HTTP do zwrócenia, jeśli nagłówek nie istnieje lub ma nieprawidłową wartość.                                                                                        | Tak      | Nie dotyczy     |
| nazwa nagłówka                | Nazwa nagłówka HTTP do sprawdzenia.                                                                                                                                  | Tak      | Nie dotyczy     |
| przypadek ignorowania                | Można ustawić na True lub False. Jeśli ustawiona na True case jest ignorowana, gdy wartość nagłówka jest porównywana z zestawem dopuszczalnych wartości.                                    | Tak      | Nie dotyczy     |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące

-   **Zakresy zasad:** wszystkie zakresy

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a>Ogranicz stawkę za połączenia według subskrypcji

Zasady `rate-limit` zapobiega skoki użycia interfejsu API na podstawie subskrypcji, ograniczając szybkość wywołania do określonej liczby w określonym okresie czasu. Po wyzwoleniu tej zasady wywołujący `429 Too Many Requests` odbiera kod stanu odpowiedzi.

> [!IMPORTANT]
> Tej zasady można używać tylko raz na dokument zasad.
>
> [Wyrażeń zasad](api-management-policy-expressions.md) nie można używać w żadnym z atrybutów zasad dla tej zasady.

> [!CAUTION]
> Ze względu na rozproszony charakter architektury ograniczania, ograniczanie szybkości nigdy nie jest całkowicie dokładne. Różnica między skonfigurowaną a rzeczywistą liczbą dozwolonych żądań zależy od woluminu i szybkości żądania, opóźnienia wewnętrznej bazy danych i innych czynników.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

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

| Nazwa       | Opis                                                                                                                                                                                                                                                                                              | Wymagany |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| limit kursowy | Element główny.                                                                                                                                                                                                                                                                                            | Tak      |
| api        | Dodaj jeden lub więcej z tych elementów, aby narzucić limit szybkości wywołań dla interfejsów API w produkcie. Limity szybkości wywołania produktu i interfejsu API są stosowane niezależnie. Do API można odwoływać `name` `id`się za pośrednictwem programu lub . Jeśli oba atrybuty `id` są podane, będą używane i `name` zostaną zignorowane.                    | Nie       |
| Operacji  | Dodaj jeden lub więcej z tych elementów, aby narzucić limit szybkości wywołań operacji w interfejsie API. Limity szybkości wywołania produktu, interfejsu API i operacji są stosowane niezależnie. Do operacji można się `name` odwoływać za pośrednictwem lub `id`. Jeśli oba atrybuty `id` są podane, będą używane i `name` zostaną zignorowane. | Nie       |

### <a name="attributes"></a>Atrybuty

| Nazwa           | Opis                                                                                           | Wymagany | Domyślne |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | Nazwa interfejsu API, dla którego należy zastosować limit szybkości.                                                | Tak      | Nie dotyczy     |
| Wywołania          | Maksymalna całkowita liczba połączeń dozwolonych w przedziale czasowym określonym w pliku `renewal-period`. | Tak      | Nie dotyczy     |
| okres odnowienia | Okres w sekundach, po którym przydział zostanie zresetowany.                                              | Tak      | Nie dotyczy     |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące

-   **Zakresy zasad:** produkt, api, operacja

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a>Ogranicz szybkość połączeń według klucza

> [!IMPORTANT]
> Ta funkcja jest niedostępna w warstwie **Zużycie** usługi API Management.

Zasady `rate-limit-by-key` zapobiega skoki użycia interfejsu API na podstawie klucza, ograniczając szybkość wywołania do określonej liczby w określonym okresie czasu. Klucz może mieć dowolną wartość ciągu i jest zazwyczaj dostarczany przy użyciu wyrażenia zasad. Opcjonalny warunek przyrostu można dodać, aby określić, które żądania powinny być wliczane do limitu. Po wyzwoleniu tej zasady wywołujący `429 Too Many Requests` odbiera kod stanu odpowiedzi.

Aby uzyskać więcej informacji i przykładów tej zasady, zobacz [Ograniczanie żądań zaawansowanych za pomocą usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

> [!CAUTION]
> Ze względu na rozproszony charakter architektury ograniczania, ograniczanie szybkości nigdy nie jest całkowicie dokładne. Różnica między skonfigurowaną a rzeczywistą liczbą dozwolonych żądań zależy od woluminu i szybkości żądania, opóźnienia wewnętrznej bazy danych i innych czynników.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Przykład

W poniższym przykładzie limit szybkości jest kluczem przez adres IP wywołującego.

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

| Nazwa              | Opis   | Wymagany |
| ----------------- | ------------- | -------- |
| limit szybkości po kluczu | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Nazwa                | Opis                                                                                           | Wymagany | Domyślne |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| Wywołania               | Maksymalna całkowita liczba połączeń dozwolonych w przedziale czasowym określonym w pliku `renewal-period`. | Tak      | Nie dotyczy     |
| klucz licznika         | Klucz do użycia dla zasad limitu stawek.                                                             | Tak      | Nie dotyczy     |
| warunek przyrostu | Wyrażenie logiczne określające, czy żądanie powinno być`true`zaliczone do przydziału ( ).        | Nie       | Nie dotyczy     |
| okres odnowienia      | Okres w sekundach, po którym przydział zostanie zresetowany.                                              | Tak      | Nie dotyczy     |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące

-   **Zakresy zasad:** wszystkie zakresy

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a>Ograniczanie usług IP wywołującego

Zasady `ip-filter` filtrują (zezwala/odrzuca) wywołania z określonych adresów IP i/lub zakresów adresów.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Przykład

W poniższym przykładzie zasada zezwala tylko na żądania pochodzące z pojedynczego adresu IP lub zakresu adresów IP określonych

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elementy

| Nazwa                                      | Opis                                         | Wymagany                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| filtr ip                                 | Element główny.                                       | Tak                                                            |
| adres                                   | Określa pojedynczy adres IP, na którym ma być filtrowane.   | Wymagany jest `address` `address-range` co najmniej jeden element. |
| zakres adresu od="adres" do="adres" | Określa zakres adresu IP, na którym ma być filtrowane. | Wymagany jest `address` `address-range` co najmniej jeden element. |

### <a name="attributes"></a>Atrybuty

| Nazwa                                      | Opis                                                                                 | Wymagany                                           | Domyślne |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| zakres adresu od="adres" do="adres" | Zakres adresów IP, aby zezwolić lub odmówić dostępu.                                        | Wymagane, `address-range` gdy element jest używany. | Nie dotyczy     |
| ip-filter action="zezwalaj &#124; zabraniaj"    | Określa, czy wywołania mają być dozwolone, czy nie dla określonych adresów IP i zakresów. | Tak                                                | Nie dotyczy     |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące
-   **Zakresy zasad:** wszystkie zakresy

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a>Ustawianie przydziału użycia według subskrypcji

Zasady `quota` wymuszają odnawialny lub dożywotni przydział liczby wywołań i/lub przepustowości na podstawie subskrypcji.

> [!IMPORTANT]
> Tej zasady można używać tylko raz na dokument zasad.
>
> [Wyrażeń zasad](api-management-policy-expressions.md) nie można używać w żadnym z atrybutów zasad dla tej zasady.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

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

| Nazwa      | Opis                                                                                                                                                                                                                                                                                  | Wymagany |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| limit przydziału     | Element główny.                                                                                                                                                                                                                                                                                | Tak      |
| api       | Dodaj jeden lub więcej z tych elementów, aby narzucić przydział wywołań dla interfejsów API w produkcie. Przydziały wywołania produktu i interfejsu API są stosowane niezależnie. Do API można odwoływać `name` `id`się za pośrednictwem programu lub . Jeśli oba atrybuty `id` są podane, będą używane i `name` zostaną zignorowane.                    | Nie       |
| Operacji | Dodaj jeden lub więcej z tych elementów, aby narzucić przydział wywołań na operacje w interfejsie API. Przydziały wywołania produktu, interfejsu API i operacji są stosowane niezależnie. Do operacji można się `name` odwoływać za pośrednictwem lub `id`. Jeśli oba atrybuty `id` są podane, będą używane i `name` zostaną zignorowane. | Nie       |

### <a name="attributes"></a>Atrybuty

| Nazwa           | Opis                                                                                               | Wymagany                                                         | Domyślne |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | Nazwa interfejsu API lub operacji, dla której stosuje się przydział.                                             | Tak                                                              | Nie dotyczy     |
| Przepustowości      | Maksymalna całkowita liczba kilobajtów dozwolona w `renewal-period`przedziale czasowym określonym w pliku . | Należy `calls`określić `bandwidth`razem lub oba. | Nie dotyczy     |
| Wywołania          | Maksymalna całkowita liczba połączeń dozwolonych w przedziale czasowym określonym w pliku `renewal-period`.     | Należy `calls`określić `bandwidth`razem lub oba. | Nie dotyczy     |
| okres odnowienia | Okres w sekundach, po którym przydział zostanie zresetowany.                                                  | Tak                                                              | Nie dotyczy     |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące
-   **Zakresy zasad:** produkt

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a>Ustawianie przydziału użycia według klucza

> [!IMPORTANT]
> Ta funkcja jest niedostępna w warstwie **Zużycie** usługi API Management.

Zasady `quota-by-key` wymusza odnawialne lub dożywotnie przydziału głośności i/lub przepustowości na podstawie klucza. Klucz może mieć dowolną wartość ciągu i jest zazwyczaj dostarczany przy użyciu wyrażenia zasad. Opcjonalny warunek przyrostu można dodać, aby określić, które żądania powinny być wliczane do przydziału. Jeśli wiele zasad zwiększy tę samą wartość klucza, jest zwiększana tylko raz na żądanie. Po osiągnięciu limitu połączeń wywołujący odbiera `403 Forbidden` kod stanu odpowiedzi.

Aby uzyskać więcej informacji i przykładów tej zasady, zobacz [Ograniczanie żądań zaawansowanych za pomocą usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Przykład

W poniższym przykładzie przydział jest kluczem przez adres IP wywołującego.

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

| Nazwa  | Opis   | Wymagany |
| ----- | ------------- | -------- |
| limit przydziału | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Nazwa                | Opis                                                                                               | Wymagany                                                         | Domyślne |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| Przepustowości           | Maksymalna całkowita liczba kilobajtów dozwolona w `renewal-period`przedziale czasowym określonym w pliku . | Należy `calls`określić `bandwidth`razem lub oba. | Nie dotyczy     |
| Wywołania               | Maksymalna całkowita liczba połączeń dozwolonych w przedziale czasowym określonym w pliku `renewal-period`.     | Należy `calls`określić `bandwidth`razem lub oba. | Nie dotyczy     |
| klucz licznika         | Klucz do użycia dla zasad przydziału.                                                                      | Tak                                                              | Nie dotyczy     |
| warunek przyrostu | Wyrażenie logiczne określające, czy żądanie powinno być`true`zaliczone do przydziału ( )             | Nie                                                               | Nie dotyczy     |
| okres odnowienia      | Okres w sekundach, po którym przydział zostanie zresetowany.                                                  | Tak                                                              | Nie dotyczy     |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące
-   **Zakresy zasad:** wszystkie zakresy

## <a name="validate-jwt"></a><a name="ValidateJWT"></a>Sprawdzanie poprawności JWT

Zasada `validate-jwt` wymusza istnienie i ważność JWT wyodrębnione z określonego nagłówka HTTP lub określony parametr kwerendy.

> [!IMPORTANT]
> Zasady `validate-jwt` wymagają, `exp` aby zarejestrowane oświadczenie zostało uwzględnione w `require-expiration-time` tokenie JWT, `false`chyba że atrybut jest określony i ustawiony na .
> Zasady `validate-jwt` obsługuje hs256 i RS256 algorytmy podpisywania. W przypadku HS256 klucz musi być podany w linii wbudowanej w zasadach w formularzu zakodowanym base64. W przypadku serwera RS256 klucz musi być udostępniany za pośrednictwem punktu końcowego konfiguracji open id.
> Zasady `validate-jwt` obsługują tokeny szyfrowane przy użyciu kluczy symetrycznych przy użyciu następujących algorytmów szyfrowania A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

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
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
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
</validate-jwt>

```

### <a name="examples"></a>Przykłady

#### <a name="simple-token-validation"></a>Proste sprawdzanie poprawności tokenu

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

#### <a name="azure-active-directory-token-validation"></a>Sprawdzanie poprawności tokenu usługi Azure Active Directory

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

#### <a name="azure-active-directory-b2c-token-validation"></a>Sprawdzanie poprawności tokenu usługi Azure Active Directory B2C

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

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Autoryzowanie dostępu do operacji na podstawie oświadczeń tokenu

W tym przykładzie pokazano, jak używać [zasady Sprawdzania poprawności JWT](api-management-access-restriction-policies.md#ValidateJWT) do autoryzowania dostępu do operacji na podstawie wartości oświadczeń tokenu.

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

### <a name="elements"></a>Elementy

| Element             | Opis                                                                                                                                                                                                                                                                                                                                           | Wymagany |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | Element główny.                                                                                                                                                                                                                                                                                                                                         | Tak      |
| Odbiorców           | Zawiera listę dopuszczalnych oświadczeń odbiorców, które mogą być obecne na tokenie. Jeśli istnieje wiele wartości odbiorców, a następnie każda wartość jest wypróbowywczony, dopóki nie zostaną wyczerpane (w którym to przypadku sprawdzanie poprawności nie powiedzie się) lub do jednego powiedzie się. Należy określić co najmniej jedną grupę odbiorców.                                                                     | Nie       |
| klucze podpisywania wystawcy | Lista kluczy zabezpieczeń zakodowanych w bazie Base64 używanych do sprawdzania poprawności podpisanych tokenów. Jeśli istnieje wiele kluczy zabezpieczeń, a następnie każdy klucz jest wypróbowywał, dopóki nie zostaną wyczerpane (w którym to przypadku sprawdzanie poprawności nie powiedzie się) lub dopóki nie powiedzie się (przydatne do przerzucania tokenu). Kluczowe elementy mają `id` opcjonalny atrybut `kid` używany do dopasowania do oświadczenia.               | Nie       |
| klucze deszyfrujące     | Lista kluczy zakodowanych w bazie Base64 używana do odszyfrowywania tokenów. Jeśli istnieje wiele kluczy zabezpieczeń, a następnie każdy klucz jest wypróbowywał, dopóki nie zostaną wyczerpane wszystkie klucze (w którym to przypadku sprawdzanie poprawności nie powiedzie się) lub dopóki klucz nie powiedzie się. Kluczowe elementy mają `id` opcjonalny atrybut `kid` używany do dopasowania do oświadczenia.                                                 | Nie       |
| Emitentów             | Lista dopuszczalnych podmiotów, które wydały token. Jeśli istnieje wiele wartości wystawcy, a następnie każda wartość jest wypróbowywczony, dopóki nie zostaną wyczerpane (w którym to przypadku sprawdzanie poprawności nie powiedzie się) lub dopóki nie powiedzie się.                                                                                                                                         | Nie       |
| openid-config       | Element używany do określania zgodnego punktu końcowego konfiguracji Open ID, z którego można uzyskać klucze podpisywania i wystawcę.                                                                                                                                                                                                                        | Nie       |
| wymagane roszczenia     | Zawiera listę oświadczeń oczekuje się, że obecność na tokenie, aby można go uznać za prawidłowe. Gdy `match` atrybut jest ustawiony `all` na każdą wartość oświadczenia w zasadach musi być obecny w tokenie do sprawdzania poprawności, aby zakończyć się pomyślnie. Gdy `match` atrybut jest ustawiony `any` na co najmniej jedno oświadczenie musi być obecny w tokenie do sprawdzania poprawności, aby zakończyć się pomyślnie. | Nie       |

### <a name="attributes"></a>Atrybuty

| Nazwa                            | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                            | Wymagany                                                                         | Domyślne                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| pochylenie zegara                      | Timespan. Służy do określania maksymalnej oczekiwanej różnicy czasu między zegarami systemowymi wystawcy tokenu a wystąpieniem zarządzania interfejsami API.                                                                                                                                                                                                                                                                                                               | Nie                                                                               | 0 sekund                                                                         |
| nie można potwierdzić-błąd-message | Komunikat o błędzie do zwrócenia w treści odpowiedzi HTTP, jeśli JWT nie przejdzie sprawdzania poprawności. Ten komunikat musi mieć wszystkie znaki specjalne poprawnie zmienione.                                                                                                                                                                                                                                                                                                 | Nie                                                                               | Domyślny komunikat o błędzie zależy od problemu z weryfikacją, na przykład "JWT nie występuje". |
| nie powiodło się-validation-httpcode      | Kod stanu HTTP do zwrócenia, jeśli JWT nie przejdzie sprawdzania poprawności.                                                                                                                                                                                                                                                                                                                                                                                         | Nie                                                                               | 401                                                                               |
| nazwa nagłówka                     | Nazwa nagłówka HTTP zawierającego token.                                                                                                                                                                                                                                                                                                                                                                                                         | Jeden `header-name`z `query-parameter-name` `token-value` , lub muszą być określone. | Nie dotyczy                                                                               |
| nazwa kwerendy-parametr            | Nazwa parametru kwerendy przechowującego token.                                                                                                                                                                                                                                                                                                                                                                                                     | Jeden `header-name`z `query-parameter-name` `token-value` , lub muszą być określone. | Nie dotyczy                                                                               |
| wartość tokenu                     | Wyrażenie zwracające ciąg zawierający token JWT                                                                                                                                                                                                                                                                                                                                                                                                     | Jeden `header-name`z `query-parameter-name` `token-value` , lub muszą być określone. | Nie dotyczy                                                                               |
| id                              | Atrybut `id` w elemencie `key` pozwala określić ciąg, który `kid` będzie dopasowany do oświadczenia w tokenie (jeśli jest obecny), aby dowiedzieć się odpowiedni klucz do użycia do sprawdzania poprawności podpisu.                                                                                                                                                                                                                                           | Nie                                                                               | Nie dotyczy                                                                               |
| match                           | Atrybut `match` w elemencie `claim` określa, czy każda wartość oświadczenia w zasadach musi być obecny w tokenie dla sprawdzania poprawności, aby zakończyć się pomyślnie. Możliwe wartości:<br /><br /> - `all`- każda wartość oświadczenia w zasadach musi być obecny w tokenie do sprawdzania poprawności, aby zakończyć się pomyślnie.<br /><br /> - `any`- co najmniej jedna wartość oświadczenia musi być obecny w tokenie do sprawdzania poprawności, aby zakończyć się pomyślnie.                                                       | Nie                                                                               | all                                                                               |
| wymagać czasu wygaśnięcia         | Boolean. Określa, czy w tokenie jest wymagane oświadczenie wygaśnięcia.                                                                                                                                                                                                                                                                                                                                                                               | Nie                                                                               | true                                                                              |
| schemat wymagania                  | Nazwa systemu tokenów, np. Po ustawieniu tego atrybutu zasady zapewniają, że określony schemat jest obecny w wartości nagłówka Autoryzacja.                                                                                                                                                                                                                                                                                    | Nie                                                                               | Nie dotyczy                                                                               |
| wymagaj podpisanych tokenów           | Boolean. Określa, czy token jest wymagany do podpisania.                                                                                                                                                                                                                                                                                                                                                                                           | Nie                                                                               | true                                                                              |
| Separator                       | Ciąg. Określa separator (np. ","), który ma być używany do wyodrębniania zestawu wartości z oświadczenia wielowartościowego.                                                                                                                                                                                                                                                                                                                                          | Nie                                                                               | Nie dotyczy                                                                               |
| url                             | Otwórz adres URL punktu końcowego konfiguracji identyfikatora, z którego można uzyskać metadane konfiguracji open id. Odpowiedź powinna być zgodna ze specyfikacją`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`zdefiniowaną w adresie URL: . W przypadku usługi Azure Active `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` Directory użyj następującego adresu URL: `contoso.onmicrosoft.com`zastąpienie nazwy dzierżawy katalogu, np. | Tak                                                                              | Nie dotyczy                                                                               |
| nazwa zmiennej tokenu wyjściowego      | Ciąg. Nazwa zmiennej kontekstowej, która otrzyma wartość [`Jwt`](api-management-policy-expressions.md) tokenu jako obiekt typu po pomyślnym sprawdeniu poprawności tokenu                                                                                                                                                                                                                                                                                     | Nie                                                                               | Nie dotyczy                                                                               |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące
-   **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

-   [Zasady w zarządzaniu interfejsami API](api-management-howto-policies.md)
-   [Przekształcanie interfejsów API](transform-api.md)
-   [Odwołanie do zasad](api-management-policy-reference.md) dla pełnej listy oświadczeń zasad i ich ustawień
-   [Przykłady zasad](policy-samples.md)
