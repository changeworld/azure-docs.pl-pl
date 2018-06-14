---
title: Zasady ograniczeń dostępu w usłudze Azure zarządzanie interfejsami API | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zasad ograniczeń dostępu dostępne do użycia w usłudze Azure API Management.
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
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 5fbb4f8a15ee7ee8b6cecbe76391e2b2a7e4be1b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31515353"
---
# <a name="api-management-access-restriction-policies"></a>Zasady ograniczeń dostępu do interfejsu API zarządzania
W tym temacie znajdują się informacje na następujące zasady usługi API Management. Aby uzyskać informacje dotyczące dodawania i konfigurowania zasad, zobacz [zasad w usłudze API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AccessRestrictionPolicies"></a> Zasady ograniczeń dostępu  
  
-   [Nagłówek HTTP wyboru](api-management-access-restriction-policies.md#CheckHTTPHeader) — wymusza istnienia i/lub wartość nagłówka HTTP.  
-   [Limit szybkości wywołanie przez subskrypcji](api-management-access-restriction-policies.md#LimitCallRate) — użycie uniemożliwia API wzrósł poprzez ograniczenie wywołań szybkości, na podstawie subskrypcji na.  
-   [Limit szybkości wywołanie przez klucz](#LimitCallRateByKey) — użycie uniemożliwia API wzrósł ograniczając szybkość połączenia, na podstawie na klucz.  
-   [Ograniczenia adresów IP wywołującego](api-management-access-restriction-policies.md#RestrictCallerIPs) -wywołania filtrów (umożliwia/nie zezwala na) z określonych adresów IP i/lub zakresów adresów.  
-   [Ustaw przydział użycia subskrypcji](api-management-access-restriction-policies.md#SetUsageQuota) — umożliwia egzekwowanie odnawialnymi lub okres istnienia wywołania woluminu i/lub przepustowości limit przydziału, na podstawie na subskrypcję.  
-   [Ustaw przydział użycia przez klucz](#SetUsageQuotaByKey) — umożliwia egzekwowanie odnawialnymi lub okres istnienia wywołania woluminu i/lub przepustowości limit przydziału, na podstawie według klucza.  
-   [Sprawdź poprawność JWT](api-management-access-restriction-policies.md#ValidateJWT) — wymusza istnienia i ważności wyodrębniony z określonego nagłówka HTTP lub parametr zapytania określony token JWT.  
  
##  <a name="CheckHTTPHeader"></a> Sprawdź nagłówka HTTP  
 Użyj `check-header` zasady do wymuszenia, że żądanie ma określonego nagłówka HTTP. Można opcjonalnie sprawdzić, czy nagłówek nie ma określonej wartości lub sprawdź, czy zakres dozwolonych wartości. W przypadku niepowodzenia sprawdzania zasad przerywa przetwarzania żądania i zwraca komunikat kodu i błąd stanu HTTP określone przez zasady.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="True">  
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
  
|Name (Nazwa)|Opis|Wymagane|  
|----------|-----------------|--------------|  
|check-header|Element główny.|Yes|  
|wartość|Dozwolone wartości nagłówka HTTP. Jeśli wiele elementów wartości są określone, sprawdzania jest uznawany sukcesu, jeśli jedna z wartości jest dopasowanie.|Nie|  
  
### <a name="attributes"></a>Atrybuty  
  
|Name (Nazwa)|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|nie powiodło się wyboru komunikatów o błędach|Komunikat o błędzie zwracany w treści odpowiedzi HTTP, jeśli nagłówek nie istnieje lub ma nieprawidłową wartość. Ta wiadomość musi mieć żadnych znaków specjalnych prawidłowo wpisywany.|Yes|ND|  
|nie powiodło się wyboru httpcode|Kod stanu HTTP do zwrócenia, jeśli nagłówek nie istnieje lub ma nieprawidłową wartość.|Yes|ND|  
|Nazwa nagłówka|Nazwa nagłówka HTTP do sprawdzenia.|Yes|ND|  
|Ignoruj case|Można ustawić na wartość True lub False. Jeśli jest ustawiona na True przypadek jest ignorowane w przypadku wartość nagłówka jest porównywana zbiór dopuszczalnych wartości.|Yes|ND|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** przychodzące, wychodzące  
  
-   **Zakresy zasad:** globalnych produktu interfejsu API, operacji  
  
##  <a name="LimitCallRate"></a> Limit szybkości wywołanie przez subskrypcję  
 `rate-limit` Zasad uniemożliwia nagłego interfejsu API na podstawie subskrypcji na ograniczając szybkość wywołania do określonej liczby na określonym przedziale czasu. Po wyzwoleniu tych zasad wywołującego odbiera `429 Too Many Requests` kod stanu odpowiedzi.  
  
> [!IMPORTANT]
>  Te zasady mogą być użyte tylko raz na dokument zasad.  
>   
>  [Wyrażenie zasad](api-management-policy-expressions.md) nie można użyć w żadnym z atrybutów zasad dla tej zasady.  
  
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
  
|Name (Nazwa)|Opis|Wymagane|  
|----------|-----------------|--------------|  
|set-limit|Element główny.|Yes|  
|api|Dodaj co najmniej jeden z tych elementów do narzuca ograniczenia szybkości wywołania interfejsów API w obrębie produktu. Produktu i interfejsu API wywołać szybkość, z jaką ograniczenia są stosowane niezależnie. Interfejs API może być przywoływany albo za pośrednictwem `name` lub `id`. Jeśli oba atrybuty są dostarczane, `id` będą używane i `name` zostaną zignorowane.|Nie|  
|Operacja|Dodaj co najmniej jeden z tych elementów do narzuca ograniczenia szybkości wywołania operacji w obrębie interfejsu API. Produkt, interfejsu API i operacji należy wywołać szybkość, z jaką ograniczenia są stosowane niezależnie. Operacja może być przywoływany albo za pośrednictwem `name` lub `id`. Jeśli oba atrybuty są dostarczane, `id` będą używane i `name` zostaną zignorowane.|Nie|  
  
### <a name="attributes"></a>Atrybuty  
  
|Name (Nazwa)|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|name|Nazwa interfejsu API, do których chcesz zastosować limit szybkości.|Yes|ND|  
|wywołania|Maksymalna liczba połączeń dozwolona określona w interwale `renewal-period`.|Yes|ND|  
|okres odnawiania|Okres czasu w sekundach, po których resetuje limit przydziału.|Yes|ND|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
  
-   **Zakresy zasad:** produktu  
  
##  <a name="LimitCallRateByKey"></a> Limit szybkości wywołanie przez klucz  
 `rate-limit-by-key` Zasad uniemożliwia nagłego interfejsu API na podstawie klucza na ograniczając szybkość wywołania do określonej liczby na określonym przedziale czasu. Klucz może mieć wartość dowolny ciąg i jest zwykle zapewniany przy użyciu wyrażenia zasad. Aby określić, które żądania powinno być liczone się do limitu można dodać warunku opcjonalnie przyrostu. Po wyzwoleniu tych zasad wywołującego odbiera `429 Too Many Requests` kod stanu odpowiedzi.  
  
 Aby uzyskać dodatkowe informacje i przykłady tych zasad, zobacz [Zaawansowane żądanie ograniczania z usługą Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).  
  
> [!IMPORTANT]
>  Te zasady mogą być użyte tylko raz na dokument zasad.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<rate-limit-by-key calls="number"  
                   renewal-period="seconds"   
                   increment-condition="condition"   
                   counter-key="key value" />  
  
```  
  
### <a name="example"></a>Przykład  
 W poniższym przykładzie limit szybkości jest wyznaczaną przez obiekt wywołujący adresu IP.  
  
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
  
|Name (Nazwa)|Opis|Wymagane|  
|----------|-----------------|--------------|  
|set-limit|Element główny.|Yes|  
  
### <a name="attributes"></a>Atrybuty  
  
|Name (Nazwa)|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|wywołania|Maksymalna liczba połączeń dozwolona określona w interwale `renewal-period`.|Yes|ND|  
|klucz liczników|Klucz do użycia zasad limitu szybkości.|Yes|ND|  
|Stan przyrostowy|Wyrażenie warunkowe określenie, czy żądanie powinno być liczone kierunku przydział (`true`).|Nie|ND|  
|okres odnawiania|Okres czasu w sekundach, po których resetuje limit przydziału.|Yes|ND|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
  
-   **Zakresy zasad:** globalnych produktu interfejsu API, operacji  
  
##  <a name="RestrictCallerIPs"></a> Ograniczenia adresów IP wywołującego  
 `ip-filter` (Umożliwia/nie zezwala na) wywołania z określonych adresów IP i/lub zakresów adresów filtry zasad.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="example"></a>Przykład  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="elements"></a>Elementy  
  
|Name (Nazwa)|Opis|Wymagane|  
|----------|-----------------|--------------|  
|ip-filter|Element główny.|Yes|  
|Adres|Określa pojedynczy adres IP, na których chcesz filtrować.|Co najmniej jeden `address` lub `address-range` element jest wymagany.|  
|zakres adresów z = "address" Aby = "address"|Określa adres zakresu adresów IP, na których chcesz filtrować.|Co najmniej jeden `address` lub `address-range` element jest wymagany.|  
  
### <a name="attributes"></a>Atrybuty  
  
|Name (Nazwa)|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|zakres adresów z = "address" Aby = "address"|Zakres adresów IP, aby zezwolić lub odmówić dostępu.|Wymagany, gdy `address-range` element jest używany.|ND|  
|Filtr IP akcji = "Zezwalaj na &#124; zabraniać"|Określa, czy powinno być dozwolone wywołania nie dla określonych adresów IP i zakresów.|Yes|ND|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
-   **Zakresy zasad:** globalnych produktu interfejsu API, operacji  
  
##  <a name="SetUsageQuota"></a> Ustaw przydział użycia przez subskrypcję  
 `quota` Zasady wymuszają zastosowanie odnawialnymi lub okres istnienia wywołania woluminu i/lub przepustowości limit przydziału, na podstawie na subskrypcję.  
  
> [!IMPORTANT]
>  Te zasady mogą być użyte tylko raz na dokument zasad.  
>   
>  [Wyrażenie zasad](api-management-policy-expressions.md) nie można użyć w żadnym z atrybutów zasad dla tej zasady.  
  
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
  
|Name (Nazwa)|Opis|Wymagane|  
|----------|-----------------|--------------|  
|quota|Element główny.|Yes|  
|api|Dodaj co najmniej jeden z tych elementów do nakładania przydział wywołania interfejsów API w obrębie produktu. Produktu i przydziały wywołania interfejsu API są stosowane niezależnie. Interfejs API może być przywoływany albo za pośrednictwem `name` lub `id`. Jeśli oba atrybuty są dostarczane, `id` będą używane i `name` zostaną zignorowane.|Nie|  
|Operacja|Dodaj co najmniej jeden z tych elementów nakładanie przydział wywołanie w operacji w obrębie interfejsu API. Przydziały wywołania produktu, interfejsu API i operacji są stosowane niezależnie. Operacja może być przywoływany albo za pośrednictwem `name` lub `id`. Jeśli oba atrybuty są dostarczane, `id` będą używane i `name` zostaną zignorowane.|Nie|  
  
### <a name="attributes"></a>Atrybuty  
  
|Name (Nazwa)|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|name|Nazwa interfejsu API lub operacji, którego dotyczy limitu przydziału.|Yes|ND|  
|bandwidth|Maksymalna liczba kilobajtów dozwolone określona w interwale `renewal-period`.|Albo `calls`, `bandwidth`, lub razem muszą być jednocześnie określone.|ND|  
|wywołania|Maksymalna liczba połączeń dozwolona określona w interwale `renewal-period`.|Albo `calls`, `bandwidth`, lub razem muszą być jednocześnie określone.|ND|  
|okres odnawiania|Okres czasu w sekundach, po których resetuje limit przydziału.|Yes|ND|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
-   **Zakresy zasad:** produktu  
  
##  <a name="SetUsageQuotaByKey"></a> Ustaw przydział użycia według klucza  
 `quota-by-key` Zasady wymuszają zastosowanie odnawialnymi lub okres istnienia wywołania woluminu i/lub przepustowości limit przydziału, na podstawie według klucza. Klucz może mieć wartość dowolny ciąg i jest zwykle zapewniany przy użyciu wyrażenia zasad. Opcjonalne przyrostu warunku można dodać do określenia żądań, które powinno być liczone kierunku limit przydziału.  
  
 Aby uzyskać dodatkowe informacje i przykłady tych zasad, zobacz [Zaawansowane żądanie ograniczania z usługą Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).  
  
> [!IMPORTANT]
>  Te zasady mogą być użyte tylko raz na dokument zasad.  
>   
>  [Wyrażenie zasad](api-management-policy-expressions.md) nie można użyć w żadnym z atrybutów zasad dla tej zasady.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<quota-by-key calls="number"   
              bandwidth="kilobytes"   
              renewal-period="seconds"  
              increment-condition="condition"   
              counter-key="key value" />  
  
```  
  
### <a name="example"></a>Przykład  
 W poniższym przykładzie przydział jest wyznaczaną przez obiekt wywołujący adresu IP.  
  
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
  
|Name (Nazwa)|Opis|Wymagane|  
|----------|-----------------|--------------|  
|quota|Element główny.|Yes|  
  
### <a name="attributes"></a>Atrybuty  
  
|Name (Nazwa)|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|bandwidth|Maksymalna liczba kilobajtów dozwolone określona w interwale `renewal-period`.|Albo `calls`, `bandwidth`, lub razem muszą być jednocześnie określone.|ND|  
|wywołania|Maksymalna liczba połączeń dozwolona określona w interwale `renewal-period`.|Albo `calls`, `bandwidth`, lub razem muszą być jednocześnie określone.|ND|  
|klucz liczników|Klucz do użycia zasad przydziału.|Yes|ND|  
|Stan przyrostowy|Wyrażenie warunkowe określenie, czy żądanie powinno być liczone kierunku przydział (`true`)|Nie|ND|  
|okres odnawiania|Okres czasu w sekundach, po których resetuje limit przydziału.|Yes|ND|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
-   **Zakresy zasad:** globalnych produktu interfejsu API, operacji  
  
##  <a name="ValidateJWT"></a> Sprawdź poprawność JWT  
 `validate-jwt` Zasady wymuszają zastosowanie istnienia i ważności token JWT wyodrębnione z albo określonego nagłówka HTTP lub parametr zapytania określony.  
  
> [!IMPORTANT]
>  `validate-jwt` Zasad wymaga, aby `exp` zarejestrowanych oświadczeń jest uwzględniona w JWT token, chyba że `require-expiration-time` atrybut jest określona i ustawić `false`.  
> `validate-jwt` Zasad obsługuje HS256 i RS256 algorytmy podpisywania. Dla HS256 klucz należy podać wbudowane w ramach zasad w postaci kodowany w standardzie base64. RS256 klucz ma zapewnienie za pośrednictwem punktu końcowego Otwórz identyfikator konfiguracji.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<validate-jwt   
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"   
    failed-validation-httpcode="http status code to return on failure"   
    failed-validation-error-message="error message to return on failure"   
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"   
    clock-skew="allowed clock skew in seconds">  
  <issuer-signing-keys>  
    <key>base64 encoded signing key</key>  
    <!-- if there are multiple keys, then add additional key elements -->  
  </issuer-signing-keys>  
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
  
#### <a name="azure-mobile-services-token-validation"></a>Azure Mobile Services tokenu weryfikacji.  
  
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
  
#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory tokenu weryfikacji.  
  
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

  
#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C tokenu weryfikacji.  
  
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
  
#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Autoryzacja dostępu do operacji na podstawie tokenu oświadczeń  
 Ten przykład przedstawia sposób użycia [JWT do zweryfikowania](api-management-access-restriction-policies.md#ValidateJWT) zasad do wstępnie autoryzacji dostępu do operacji na podstawie tokenu oświadczeń. Aby demonstracyjne konfigurowania i korzystania z tych zasad, zobacz [177 epizodu obejmują chmury: więcej funkcji zarządzania interfejsu API z Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przewijanie do przodu do 13:50. Szybko przewiń do przodu do 15:00, aby wyświetlić zasady skonfigurowane w edytorze zasad, a następnie do 18:50 dla pokaz wywołanie operacji z portalu dla deweloperów zarówno z i bez tokenu autoryzacji wymagane.  
  
```xml  
<!-- Copy the following snippet into the inbound section at the api (or higher) level to pre-authorize access to operations based on token claims -->  
<set-variable name="signingKey" value="insert signing key here" />  
<choose>  
  <when condition="@(context.Request.Method.Equals("patch",StringComparison.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="edit">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <when condition="@(new [] {"post", "put"}.Contains(context.Request.Method,StringComparer.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="create">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <otherwise>  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
    </validate-jwt>  
  </otherwise>  
</choose>  
```  
  
### <a name="elements"></a>Elementy  
  
|Element|Opis|Wymagane|  
|-------------|-----------------|--------------|  
|validate-jwt|Element główny.|Yes|  
|grupy odbiorców|Zawiera listę oświadczeń dopuszczalne odbiorców, które mogą być obecne w tokenie. Jeśli wiele wartości odbiorców są obecne, a następnie sprawdzane są poszczególne wartości do momentu wszystkie wyczerpania (w takim przypadku niepowodzenia weryfikacji) lub aż do znalezienia właściwego konta. Należy określić co najmniej jednego odbiorcy.|Nie|  
|podpisywania klucze wystawcy|Lista kluczy algorytmem Base64 zabezpieczeń używany do weryfikowania podpisanych tokenów. Jeśli wiele kluczy zabezpieczeń są obecne, a następnie sprawdzane są poszczególne klucze do momentu wszystkie wyczerpania (w takim przypadku niepowodzenia weryfikacji) lub do chwili pomyślnego jedną (przydatne w przypadku przerzucania token). Kluczowe elementy mają opcjonalny `id` atrybut używany do dopasowywania `kid` oświadczeń.|Nie|  
|wystawcy|Lista dopuszczalne podmiotów zabezpieczeń, które wystawiony token. Jeśli występują wiele wartości wystawcy, a następnie sprawdzane są poszczególne wartości do momentu wszystkie wyczerpania (w takim przypadku niepowodzenia weryfikacji) lub aż do znalezienia właściwego konta.|Nie|  
|Konfiguracja protokołu openid|Element używany do określania zgodne endpoint konfiguracji Open ID, z którego podpisywania kluczy i wystawcy można uzyskać.|Nie|  
|wymagane oświadczenia|Zawiera listę oświadczeń powinien znajdować się na token, aby były uważane za prawidłowe. Gdy `match` atrybut ma ustawioną `all` każdej wartości oświadczeń w zasadzie musi znajdować się w tokenem Weryfikacja powiodła się. Gdy `match` atrybut ma ustawioną `any` co najmniej jedno oświadczenie musi być obecny w tokenie Weryfikacja powiodła się.|Nie|  
|zumo głównego klucza|Klucz główny dla tokenów wystawionych przez usługi Azure Mobile Services|Nie|  
  
### <a name="attributes"></a>Atrybuty  
  
|Name (Nazwa)|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|niedokładność zegara|Zakres czasu. Umożliwia określenie maksymalna oczekiwana różnica czasu między zegarami systemowymi wystawcy tokenów i wystąpienia interfejsu API zarządzania.|Nie|0 sekund|  
|nie powiodło się weryfikacji komunikatów o błędach|Komunikat o błędzie do zwrócenia w treści odpowiedzi HTTP, jeśli token JWT nie przeszedł pomyślnie weryfikacji. Ta wiadomość musi mieć żadnych znaków specjalnych prawidłowo wpisywany.|Nie|Domyślnego komunikatu o błędzie jest zależny od weryfikacji problem, na przykład "token JWT nie istnieje."|  
|nie powiodło się weryfikacji httpcode|Kod stanu HTTP do zwrócenia, jeśli token JWT nie przeszedł pomyślnie weryfikacji.|Nie|401|  
|Nazwa nagłówka|Nazwa nagłówka HTTP zawierający token.|Albo `header-name` lub `query-parameter-name` musi być określony; ale nie oba.|ND|  
|id|`id` Atrybutu `key` element umożliwia określenie ciąg, który będzie dopasowywane `kid` oświadczenia w tokenie (jeśli istnieje) dowiedzieć się, odpowiedni klucz do użycia w celu weryfikacji podpisu.|Nie|ND|  
|dopasowanie|`match` Atrybutu `claim` element określa, czy każda wartość oświadczenia w zasadach musi być obecny w tokenie Weryfikacja powiodła się. Możliwe wartości:<br /><br /> -                          `all` -każdej wartości oświadczeń w zasadzie musi znajdować się w tokenem Weryfikacja powiodła się.<br /><br /> -                          `any` — wartość co najmniej jedno oświadczenie musi być obecny w tokenie Weryfikacja powiodła się.|Nie|all|  
|Nazwa zapytania — paremeter|Nazwa parametru zapytania zawierający token.|Albo `header-name` lub `query-paremeter-name` musi być określony; ale nie oba.|ND|  
|Wymagaj — wygasa|Wartość logiczna. Określa, czy oświadczenie wygaśnięcia jest wymagany w tokenie.|Nie|true|
|Wymagaj schematu|Nazwa tokenu schemat, np. "Bearer". Gdy tego atrybutu jest ustawiona, zasady zapewni określony schemat jest obecny w wartość nagłówka uwierzytelnienia.|Nie|ND|
|Wymagaj podpisany — tokeny|Wartość logiczna. Określa, czy token jest wymagany do podpisania.|Nie|true|  
|Separator|Ciąg. Określa separatora (np. ",") do zastosowania w przypadku wyodrębniania zestawu wartości z oświadczeń wielowartościowych.|Nie|ND| 
|url|Otwórz adres URL punktu końcowego konfiguracji identyfikator, z której można uzyskać metadanych konfiguracji Open ID. Odpowiedź musi być zgodny specyfikacji, zgodnie z definicją pod adresem URL:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`.  Dla usługi Azure Active Directory, użyj następującego adresu URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` podstawiając nazwa dzierżawy katalogu, np. `contoso.onmicrosoft.com`.|Yes|ND|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
-   **Zakresy zasad:** globalnych produktu interfejsu API, operacji  
  
## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, Praca z zasad Zobacz:

+ [Zasady w usłudze API Management](api-management-howto-policies.md)
+ [Przekształć interfejsów API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę deklaracji zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)   
