---
title: Usługa Azure API Management caching zasad | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zasad buforowania, która jest dostępna do użycia w usłudze Azure API Management.
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
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 08b6f803d6994015432bf68c7b3edae14af8f976
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61095455"
---
# <a name="api-management-caching-policies"></a>Zasady buforowania usługi API Management
Ten temat zawiera odwołania do następujących zasad usługi API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasad w usłudze API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CachingPolicies"></a> Zasady buforowania

- Zasady buforowania odpowiedzi
    - [Pobieranie z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) — wykonaj pamięci podręcznej wyszukać i zwrócić prawidłowe odpowiedzi pamięci podręcznej, jeśli jest dostępna.
    - [Store do pamięci podręcznej](api-management-caching-policies.md#StoreToCache) -buforuje odpowiedzi zgodnie z określonym pamięci podręcznej konfiguracji kontroli.
- Wartość zasad buforowania
    - [Pobiera wartość z pamięci podręcznej](#GetFromCacheByKey) -pobrać element pamięci podręcznej według klucza.
    - [Store wartość w pamięci podręcznej](#StoreToCacheByKey) -Store elementu w pamięci podręcznej według klucza.
    - [Usuń wartość z pamięci podręcznej](#RemoveCacheByKey) — usunięcie elementu w pamięci podręcznej według klucza.

## <a name="GetFromCache"></a> Pobieranie z pamięci podręcznej
Użyj `cache-lookup` zasad do wykonania pamięci podręcznej wyszukać i zwrócić prawidłowej odpowiedzi pamięci podręcznej, jeśli jest dostępna. Te zasady można stosować w przypadkach, gdzie zawartość odpowiedzi pozostaje statycznych w okresie czasu. Buforowanie odpowiedzi redukuje przepustowość i wymagań dotyczących przetwarzania nałożone na opóźnienie serwera i obniża sieci web zaplecza postrzegane przez konsumentów interfejsu API.

> [!NOTE]
> Te zasady muszą mieć odpowiedni [Store do pamięci podręcznej](api-management-caching-policies.md#StoreToCache) zasad.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
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
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Przykład przy użyciu wyrażeń zasad
Ten przykład przedstawia sposób konfigurowania usługi API Management odpowiedzi czas trwania buforowania zgodną buforowanie odpowiedzi usługi wewnętrznej bazy danych określony przez usługę kopii `Cache-Control` dyrektywy. Demonstracyjne, konfigurowania i korzystania z tych zasad, zobacz [Cloud Cover odcinek 177: Więcej funkcji zarządzania interfejsu API za pomocą Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przewijanie do przodu do 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Aby uzyskać więcej informacji, zobacz [wyrażenia zasad](api-management-policy-expressions.md) i [zmiennej kontekstowej](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|przeszukiwania pamięci podręcznej|Element główny.|Yes|
|różnią się w nagłówku|Firmy rozpoczynają buforowanie odpowiedzi na wartość określonego nagłówka, takie jak Accept, Accept-Charset, Accept-Encoding, Accept-Language autoryzacji Expect, z hosta If-Match.|Nie|
|vary-by-query-parameter|Firmy rozpoczynają buforowanie odpowiedzi na wartości określonych parametrów zapytania. Wprowadź jeden lub wiele parametrów. Użyj średnika jako separatora. Jeśli nie jest określona, używane są wszystkie parametry zapytania.|Nie|

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)                           | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| allow-private-response-caching | Po ustawieniu `true`, pozwala na buforowanie żądań, które zawierają nagłówka autoryzacji.                                                                                                                                                                                                                                                                        | Nie       | false             |
| Typ buforowania               | Wybrać jeden z następujących wartości atrybutu:<br />- `internal` Aby użyć wbudowaną pamięć podręczną usługi API Management<br />- `external` Aby użyć zewnętrzna pamięć podręczna, zgodnie z opisem w [użytek zewnętrzna pamięć podręczna Azure redis Cache w usłudze Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` Aby użyć w przeciwnym razie zewnętrzna pamięć podręczna, jeśli skonfigurowane lub wewnętrznej pamięci podręcznej. | Nie       | `prefer-external` |
| downstream-caching-type        | Ten atrybut musi być równa jeden z następujących wartości.<br /><br /> -Brak — buforowanie podrzędnych nie jest dozwolone.<br />— prywatna — podrzędny prywatnej pamięci podręcznej jest dozwolone.<br />-publiczny — prywatny i udostępnianie podrzędnego buforowanie jest dozwolone.                                                                                                          | Nie       | brak              |
| must-revalidate                | Gdy włączone jest buforowanie podrzędnych tego atrybutu Włącza lub wyłącza `must-revalidate` dyrektywa kontroli pamięci podręcznej w odpowiedziach bramy.                                                                                                                                                                                                                      | Nie       | true              |
| różnią się przez deweloperów              | Ustaw `true` do pamięci podręcznej odpowiedzi na [klucz subskrypcji](https://docs.microsoft.com/azure/api-management/api-management-subscriptions).                                                                                                                                                                                                                                                                                                         | Yes      |         False          |
| różnią się przez developer-groups       | Ustaw `true` do pamięci podręcznej odpowiedzi na [grupy użytkowników](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups).                                                                                                                                                                                                                                                                                                             | Yes      |       False            |

### <a name="usage"></a>Sposób użycia
Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Sekcje zasad:** dla ruchu przychodzącego
- **Zakresy zasad:** Interfejs API operacji, produkt

## <a name="StoreToCache"></a> Store do pamięci podręcznej
`cache-store` Zasad buforuje odpowiedzi zgodnie z ustawieniami określonego pamięci podręcznej. Te zasady można stosować w przypadkach, gdzie zawartość odpowiedzi pozostaje statycznych w okresie czasu. Buforowanie odpowiedzi redukuje przepustowość i wymagań dotyczących przetwarzania nałożone na opóźnienie serwera i obniża sieci web zaplecza postrzegane przez konsumentów interfejsu API.

> [!NOTE]
> Te zasady muszą mieć odpowiedni [uzyskać z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) zasad.

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

#### <a name="example-using-policy-expressions"></a>Przykład przy użyciu wyrażeń zasad
Ten przykład przedstawia sposób konfigurowania usługi API Management odpowiedzi czas trwania buforowania zgodną buforowanie odpowiedzi usługi wewnętrznej bazy danych określony przez usługę kopii `Cache-Control` dyrektywy. Demonstracyjne, konfigurowania i korzystania z tych zasad, zobacz [Cloud Cover odcinek 177: Więcej funkcji zarządzania interfejsu API za pomocą Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przewijanie do przodu do 25:25.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

Aby uzyskać więcej informacji, zobacz [wyrażenia zasad](api-management-policy-expressions.md) i [zmiennej kontekstowej](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|magazynu pamięci podręcznej|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| czas trwania         | Time-to-live wpisów pamięci podręcznej wyrażony w sekundach.                                                                                                                                                                                                                                                                                                   | Yes      | ND               |

### <a name="usage"></a>Sposób użycia
Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Sekcje zasad:** ruchu wychodzącego
- **Zakresy zasad:** Interfejs API operacji, produkt

## <a name="GetFromCacheByKey"></a> Pobiera wartość z pamięci podręcznej
Użyj `cache-lookup-value` zasady wykonują wyszukiwanie pamięci podręcznej według klucza i zwracają wartość w pamięci podręcznej. Klucz może mieć wartość dowolny ciąg i zazwyczaj znajduje się za pomocą wyrażenia zasad.

> [!NOTE]
> Te zasady muszą mieć odpowiedni [Store wartość w pamięci podręcznej](#StoreToCacheByKey) zasad.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Przykład
Aby uzyskać więcej informacji i przykładów niniejszych zasad, zobacz [niestandardowe buforowanie w usłudze Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|wartość w pamięci podręcznej — wyszukiwanie|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| Typ buforowania | Wybrać jeden z następujących wartości atrybutu:<br />- `internal` Aby użyć wbudowaną pamięć podręczną usługi API Management<br />- `external` Aby użyć zewnętrzna pamięć podręczna, zgodnie z opisem w [użytek zewnętrzna pamięć podręczna Azure redis Cache w usłudze Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` Aby użyć w przeciwnym razie zewnętrzna pamięć podręczna, jeśli skonfigurowane lub wewnętrznej pamięci podręcznej. | Nie       | `prefer-external` |
| Wartość domyślna    | Wartość, która zostanie przypisana do zmiennej czy wyszukiwanie klucza pamięci podręcznej w wyniku trafienia. Jeśli ten atrybut nie jest określony, `null` jest przypisany.                                                                                                                                                                                                           | Nie       | `null`            |
| key              | Wartość klucza do użycia w polu wyszukiwania w pamięci podręcznej.                                                                                                                                                                                                                                                                                                                       | Yes      | ND               |
| Nazwa zmiennej    | Nazwa [zmiennej kontekstowej](api-management-policy-expressions.md#ContextVariables) looked zapasową zostanie przypisana wartość, jeśli wyszukiwanie zakończy się pomyślnie. Jeśli wyszukiwanie w wyniku trafienia, zmienna zostanie przypisana wartość `default-value` atrybutu lub `null`, jeśli `default-value` atrybut jest określony.                                       | Yes      | ND               |

### <a name="usage"></a>Sposób użycia
Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Sekcje zasad:** zaplecza dla ruchu przychodzącego, wychodzący — błąd
- **Zakresy zasad:** globalnej, operacji interfejsu API, produktów

## <a name="StoreToCacheByKey"></a> Store wartość w pamięci podręcznej
`cache-store-value` Wykonuje magazyn pamięci podręcznej według klucza. Klucz może mieć wartość dowolny ciąg i zazwyczaj znajduje się za pomocą wyrażenia zasad.

> [!NOTE]
> Te zasady muszą mieć odpowiedni [korzyści z pamięci podręcznej](#GetFromCacheByKey) zasad.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Przykład
Aby uzyskać więcej informacji i przykładów niniejszych zasad, zobacz [niestandardowe buforowanie w usłudze Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|cache-store-value|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

| Name (Nazwa)             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| Typ buforowania | Wybrać jeden z następujących wartości atrybutu:<br />- `internal` Aby użyć wbudowaną pamięć podręczną usługi API Management<br />- `external` Aby użyć zewnętrzna pamięć podręczna, zgodnie z opisem w [użytek zewnętrzna pamięć podręczna Azure redis Cache w usłudze Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` Aby użyć w przeciwnym razie zewnętrzna pamięć podręczna, jeśli skonfigurowane lub wewnętrznej pamięci podręcznej. | Nie       | `prefer-external` |
| czas trwania         | Wartość będzie zapisywane jako wartość podany czas trwania w sekundach.                                                                                                                                                                                                                                                                                 | Yes      | ND               |
| key              | Klucz pamięci podręcznej wartości będą przechowywane w obszarze.                                                                                                                                                                                                                                                                                                                   | Yes      | ND               |
| value            | Wartość w pamięci podręcznej.                                                                                                                                                                                                                                                                                                                                     | Yes      | ND               |
### <a name="usage"></a>Sposób użycia
Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Sekcje zasad:** zaplecza dla ruchu przychodzącego, wychodzący — błąd
- **Zakresy zasad:** globalnej, operacji interfejsu API, produktów

### <a name="RemoveCacheByKey"></a> Usuń wartość z pamięci podręcznej
`cache-remove-value` Usuwa element pamięci podręcznej identyfikowane za pomocą klucza. Klucz może mieć wartość dowolny ciąg i zazwyczaj znajduje się za pomocą wyrażenia zasad.

#### <a name="policy-statement"></a>Deklaracja zasad

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Przykład

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|cache-remove-value|Element główny.|Yes|

#### <a name="attributes"></a>Atrybuty

| Name (Nazwa)             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| Typ buforowania | Wybrać jeden z następujących wartości atrybutu:<br />- `internal` Aby użyć wbudowaną pamięć podręczną usługi API Management<br />- `external` Aby użyć zewnętrzna pamięć podręczna, zgodnie z opisem w [użytek zewnętrzna pamięć podręczna Azure redis Cache w usłudze Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external` Aby użyć w przeciwnym razie zewnętrzna pamięć podręczna, jeśli skonfigurowane lub wewnętrznej pamięci podręcznej. | Nie       | `prefer-external` |
| key              | Klucz wartości wcześniej w pamięci podręcznej, aby były usuwane z pamięci podręcznej.                                                                                                                                                                                                                                                                                        | Yes      | ND               |

#### <a name="usage"></a>Sposób użycia
Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

- **Sekcje zasad:** zaplecza dla ruchu przychodzącego, wychodzący — błąd
- **Zakresy zasad:** globalnej, operacji interfejsu API, produktów

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, w pracy z tymi zasadami zobacz:

+ [Zasady usługi API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)
