---
title: Azure API Management buforowanie zasad | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zasad buforowania dostępne do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 488a4c4b7daf5c07ca5f6b6bb72464279658d372
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2017
ms.locfileid: "26344826"
---
# <a name="api-management-caching-policies"></a>Zarządzanie interfejsami API zasad buforowania
W tym temacie znajdują się informacje na następujące zasady usługi API Management. Aby uzyskać informacje dotyczące dodawania i konfigurowania zasad, zobacz [zasad w usłudze API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CachingPolicies"></a>Buforowanie zasad  
  
-   Zasad buforowania odpowiedzi  
  
    -   [Pobierz z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) — wykonaj pamięci podręcznej wyszukiwania i zwracać prawidłowy odpowiedzi pamięci podręcznej, jeśli jest dostępna.  
    -   [Magazynu pamięci podręcznej](api-management-caching-policies.md#StoreToCache) -buforuje odpowiedzi zgodnie z określonym pamięci podręcznej konfiguracji kontroli.  
  
-   Wartość zasad buforowania  

    -   [Pobiera wartość z pamięci podręcznej](#GetFromCacheByKey) -pobrania elementu pamięci podręcznej według klucza. 
    -   [Przechowywana wartość w pamięci podręcznej](#StoreToCacheByKey) -przechowywania elementu w pamięci podręcznej według klucza. 
    -   [Usuń wartość z pamięci podręcznej](#RemoveCacheByKey) -usunięcie elementu w pamięci podręcznej według klucza.  
  
##  <a name="GetFromCache"></a>Pobierz z pamięci podręcznej  
 Użyj `cache-lookup` zasady w celu wykonania pamięci podręcznej wyszukiwania i zwracać prawidłową odpowiedź buforowana, jeśli jest dostępna. Te zasady mogą być stosowane w przypadkach, gdy odpowiedzi zawartość pozostaje statyczna w danym okresie czasu. Buforowanie odpowiedzi redukuje przepustowość i wymagania dotyczące przetwarzania nałożone na opóźnienie serwera i obniża sieci web zaplecza postrzegane przez konsumentów interfejsu API.  
  
> [!NOTE]
>  Ta zasada musi mieć odpowiednią [magazynu pamięci podręcznej](api-management-caching-policies.md#StoreToCache) zasad.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">  
  <vary-by-header>Accept</vary-by-header>  
  <!-- should be present in most cases -->  
  <vary-by-header>Accept-Charset</vary-by-header>  
  <!-- should be present in most cases -->  
  <vary-by-header>Authorization</vary-by-header>  
  <!-- should be present when allow-private-response-caching is "true"-->  
  <vary-by-header>header name</vary-by-header>  
  <!-- optional, can repeated several times -->  
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>  
  <!-- optional, can repeated several times -->  
</cache-lookup>  
```  
  
### <a name="examples"></a>Przykłady  
  
#### <a name="example"></a>Przykład  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true">  
            <vary-by-query-parameter>version</vary-by-query-parameter>  
        </cache-lookup>           
    </inbound>  
    <outbound>  
        <cache-store duration="seconds" />  
        <base />          
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>Przykład za pomocą wyrażeń zasad  
 W tym przykładzie pokazano, jak do konfigurowania czas trwania odpowiadającego buforowanie odpowiedzi usługi wewnętrznej bazy danych jako buforowania odpowiedzi interfejsu API zarządzania określona przez usługę kopii `Cache-Control` dyrektywy. Aby demonstracyjne konfigurowania i korzystania z tych zasad, zobacz [177 epizodu obejmują chmury: więcej funkcji zarządzania interfejsu API z Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przewijanie do przodu do 25:25.  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management reponse cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the deafult value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 Aby uzyskać więcej informacji, zobacz [wyrażenie zasad](api-management-policy-expressions.md) i [zmiennej kontekstu](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagane|  
|----------|-----------------|--------------|  
|wyszukiwania w pamięci podręcznej|Element główny.|Tak|  
|różnią się w nagłówku|Rozpoczynają buforowanie odpowiedzi na wartość określony nagłówek Accept, Accept-Charset, Accept-Encoding, Accept-Language autoryzacji, oczekiwania, np. Z hosta i If-Match.|Nie|  
|różnią się za--parametru zapytania|Firmy rozpoczynają buforowanie odpowiedzi na wartości parametrów określonego zapytania. Wprowadź jeden lub wiele parametrów. Użyj średnika jako separatora. Jeśli nie jest określona, używane są wszystkie parametry zapytania.|Nie|  
  
### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|Zezwalaj na prywatny odpowiedzi buforowania|Jeśli wartość `true`, umożliwia buforowanie żądań zawierających nagłówek uwierzytelnienia.|Nie|fałsz|  
|typ podrzędny dla buforowania|Ten atrybut musi mieć ustawioną jedną z następujących wartości.<br /><br /> -none - podrzędne buforowanie jest niedozwolone.<br />-prywatny - podrzędne buforowanie prywatnej jest dozwolone.<br />-publiczny - prywatnych i udostępnionych podrzędne buforowanie jest dozwolone.|Nie|brak|  
|musi revalidate|Gdy włączone jest buforowanie podrzędne tego atrybutu Włącza lub wyłącza `must-revalidate` dyrektywy sterowania pamięci podręcznej w odpowiedzi bramy.|Nie|prawda|  
|różnią się przez dewelopera|Ustaw `true` do pamięci podręcznej odpowiedzi na klucz developer.|Tak||  
|różnią się przez developer grupy|Ustaw `true` do odpowiedzi z pamięci podręcznej dla każdej roli użytkownika.|Tak||  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
-   **Zakresy zasad:** interfejsu API, działania, produktu  
  
##  <a name="StoreToCache"></a>Przechowywanie w pamięci podręcznej  
 `cache-store` Zasad buforuje odpowiedzi zgodnie z ustawieniami określonego pamięci podręcznej. Te zasady mogą być stosowane w przypadkach, gdy odpowiedzi zawartość pozostaje statyczna w danym okresie czasu. Buforowanie odpowiedzi redukuje przepustowość i wymagania dotyczące przetwarzania nałożone na opóźnienie serwera i obniża sieci web zaplecza postrzegane przez konsumentów interfejsu API.  
  
> [!NOTE]
>  Ta zasada musi mieć odpowiednią [pobrać z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) zasad.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<cache-store duration="seconds" />  
```  
  
### <a name="examples"></a>Przykłady  
  
#### <a name="example"></a>Przykład  
  
```xml  
<policies>  
    <inbound>  
        <base />  
          <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">  
                <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->  
        </cache-lookup>  
    </inbound>  
    <outbound>  
        <base />  
            <cache-store duration="3600" />  
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>Przykład za pomocą wyrażeń zasad  
 W tym przykładzie pokazano, jak do konfigurowania czas trwania odpowiadającego buforowanie odpowiedzi usługi wewnętrznej bazy danych jako buforowania odpowiedzi interfejsu API zarządzania określona przez usługę kopii `Cache-Control` dyrektywy. Aby demonstracyjne konfigurowania i korzystania z tych zasad, zobacz [177 epizodu obejmują chmury: więcej funkcji zarządzania interfejsu API z Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przewijanie do przodu do 25:25.  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management reponse cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the deafult value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 Aby uzyskać więcej informacji, zobacz [wyrażenie zasad](api-management-policy-expressions.md) i [zmiennej kontekstu](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagane|  
|----------|-----------------|--------------|  
|magazynu pamięci podręcznej|Element główny.|Tak|  
  
### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|Czas trwania|Czas wygaśnięcia wpisów pamięci podręcznej wyrażony w sekundach.|Tak|Nie dotyczy|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** ruchu wychodzącego    
-   **Zakresy zasad:** interfejsu API, działania, produktu  
  
##  <a name="GetFromCacheByKey"></a>Pobiera wartość z pamięci podręcznej  
 Użyj `cache-lookup-value` zasad wykonywania wyszukiwania w pamięci podręcznej przez klucz i zwraca wartość w pamięci podręcznej. Klucz może mieć wartość dowolny ciąg i jest zwykle zapewniany przy użyciu wyrażenia zasad.  
  
> [!NOTE]
>  Ta zasada musi mieć odpowiednią [przechowywana wartość w pamięci podręcznej](#StoreToCacheByKey) zasad.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<cache-lookup-value key="cache key value"   
    default-value="value to use if cache lookup resulted in a miss"   
    variable-name="name of a variable looked up value is assigned to" />  
```  
  
### <a name="example"></a>Przykład  
 Aby uzyskać dodatkowe informacje i przykłady tych zasad, zobacz [niestandardowych buforowanie w usłudze Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).  
  
```xml  
<cache-lookup-value  
    key="@("userprofile-" + context.Variables["enduserid"])"    
    variable-name="userprofile" />  
  
```  
  
### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagane|  
|----------|-----------------|--------------|  
|wartość w pamięci podręcznej wyszukiwania|Element główny.|Tak|  
  
### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|Wartość domyślna|Chybienia w wyniku wartość, która zostanie przypisana do zmiennej Jeśli wyszukiwanie klucza pamięci podręcznej. Jeśli ten atrybut nie jest określony, `null` jest przypisany.|Nie|`null`|  
|key|Wartość klucza do użycia w polu wyszukiwania w pamięci podręcznej.|Tak|Nie dotyczy|  
|Nazwa zmiennej|Nazwa [zmiennej kontekstu](api-management-policy-expressions.md#ContextVariables) looked się wartość zostanie przypisana do użytkownika, jeśli wyszukiwanie powiedzie się. Jeśli wyszukiwanie powoduje Chybienie, zmienna zostanie przypisana wartość `default-value` atrybutu lub `null`, jeśli `default-value` atrybut nie jest określony.|Tak|Nie dotyczy|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** przychodzący, wychodzący wewnętrznej bazy danych, na błąd  
-   **Zakresy zasad:** globalnych, interfejsu API, działania, produktu  
  
##  <a name="StoreToCacheByKey"></a>Wartość magazynu w pamięci podręcznej  
 `cache-store-value` Wykonuje magazynu pamięci podręcznej według klucza. Klucz może mieć wartość dowolny ciąg i jest zwykle zapewniany przy użyciu wyrażenia zasad.  
  
> [!NOTE]
>  Ta zasada musi mieć odpowiednią [pobrać wartości z pamięci podręcznej](#GetFromCacheByKey) zasad.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<cache-store-value key="cache key value" value="value to cache" duration="seconds" />  
```  
  
### <a name="example"></a>Przykład  
 Aby uzyskać dodatkowe informacje i przykłady tych zasad, zobacz [niestandardowych buforowanie w usłudze Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).  
  
```xml  
<cache-store-value  
    key="@("userprofile-" + context.Variables["enduserid"])"  
    value="@((string)context.Variables["userprofile"])" duration="100000" />  
  
```  
  
### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagane|  
|----------|-----------------|--------------|  
|wartość w przypadku magazynu pamięci podręcznej|Element główny.|Tak|  
  
### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|Czas trwania|Wartość będą buforowane przez podany czas trwania wybrana, w sekundach.|Tak|Nie dotyczy|  
|key|W obszarze będzie przechowywany klucz pamięci podręcznej wartość.|Tak|Nie dotyczy|  
|wartość|Wartość w pamięci podręcznej.|Tak|Nie dotyczy|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** przychodzący, wychodzący wewnętrznej bazy danych, na błąd  
-   **Zakresy zasad:** globalnych, interfejsu API, działania, produktu  
  
###  <a name="RemoveCacheByKey"></a>Usuń wartość z pamięci podręcznej  
`cache-remove-value` Usuwa element pamięci podręcznej identyfikowane za pomocą klucza. Klucz może mieć wartość dowolny ciąg i jest zwykle zapewniany przy użyciu wyrażenia zasad.  
  
#### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
  
<cache-remove-value key="cache key value"/>  
  
```  
  
#### <a name="example"></a>Przykład  
  
```xml  
  
<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>  
  
```  
  
#### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagane|  
|----------|-----------------|--------------|  
|remove wartość w pamięci podręcznej|Element główny.|Tak|  
  
#### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|key|Klucz buforowanych wartość ma zostać usunięty z pamięci podręcznej.|Tak|Nie dotyczy|  
  
#### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .  
  
-   **Sekcje zasad:** przychodzący, wychodzący wewnętrznej bazy danych, na błąd  
-   **Zakresy zasad:** globalnych, interfejsu API, działania, produktu  

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, Praca z zasad Zobacz:

+ [Zasady w usłudze API Management](api-management-howto-policies.md)
+ [Przekształć interfejsów API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę deklaracji zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)   
