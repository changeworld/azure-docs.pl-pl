---
title: Zasady buforowania usługi Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się więcej o zasadach buforowania dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 06c4ede12f939e48973d3e0b502d90b848d199bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280302"
---
# <a name="api-management-caching-policies"></a>Zasady buforowania usługi API Management
Ten temat zawiera odwołanie do następujących zasad zarządzania interfejsami API. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [Zasady w zarządzania interfejsami API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="caching-policies"></a><a name="CachingPolicies"></a>Zasady buforowania

- Zasady buforowania odpowiedzi
    - [Pobierz z pamięci podręcznej](api-management-caching-policies.md#GetFromCache) — wyszukuj pamięć podręczną i zwróć prawidłowe buforowane odpowiedzi, jeśli są dostępne.
    - [Magazyn do pamięci podręcznej](api-management-caching-policies.md#StoreToCache) — buforuje odpowiedzi zgodnie z określoną konfiguracją kontroli pamięci podręcznej.
- Zasady buforowania wartości
    - [Pobierz wartość z pamięci podręcznej](#GetFromCacheByKey) — pobieranie elementu w pamięci podręcznej według klucza.
    - [Wartość magazynu w pamięci podręcznej](#StoreToCacheByKey) — przechowuj element w pamięci podręcznej według klucza.
    - [Usuń wartość z pamięci podręcznej](#RemoveCacheByKey) — usuń element w pamięci podręcznej według klucza.

## <a name="get-from-cache"></a><a name="GetFromCache"></a>Pobierz z pamięci podręcznej
Użyj `cache-lookup` zasad, aby wyszukać pamięć podręczną i zwrócić prawidłową odpowiedź w pamięci podręcznej, gdy jest dostępna. Te zasady mogą być stosowane w przypadkach, gdy zawartość odpowiedzi pozostaje statyczna przez pewien czas. Buforowanie odpowiedzi zmniejsza wymagania dotyczące przepustowości i przetwarzania nałożone na serwer sieci web wewnętrznej bazy danych i zmniejsza opóźnienia postrzegane przez konsumentów interfejsu API.

> [!NOTE]
> Ta zasada musi mieć odpowiednie zasady [Magazynu do pamięci podręcznej.](api-management-caching-policies.md#StoreToCache)

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

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

#### <a name="example-using-policy-expressions"></a>Przykład użycia wyrażeń zasad
W tym przykładzie pokazano, jak skonfigurować czas buforowania odpowiedzi usługi API Management, który odpowiada buforowaniu odpowiedzi usługi wewnętrznej bazy danych, zgodnie z `Cache-Control` dyrektywą usługi kopii zapasowej. Aby zademonstrować konfigurowanie i używanie tych zasad, zobacz [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) and fast-forward to 25:25.

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

Aby uzyskać więcej informacji, zobacz [Wyrażenia zasad i](api-management-policy-expressions.md) [zmienna kontekstowa](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|wyszukiwanie pamięci podręcznej|Element główny.|Tak|
|zmieniaj według nagłówka|Rozpocznij buforowanie odpowiedzi na wartość określonego nagłówka, takie jak Accept, Accept-Charset, Accept-Encoding, Accept-Language, Authorization, Expect, From, Host, If-Match.|Nie|
|parametr vary-by-query|Rozpocznij buforowanie odpowiedzi na wartość określonych parametrów kwerendy. Wprowadź jeden lub wiele parametrów. Jako separator należy użyć średnika. Jeśli żaden z nich nie jest określony, używane są wszystkie parametry kwerendy.|Nie|

### <a name="attributes"></a>Atrybuty

| Nazwa                           | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagany | Domyślne           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| zezwalaj na prywatne-odpowiedzi-buforowanie | Po `true`ustawieniu , umożliwia buforowanie żądań, które zawierają nagłówek autoryzacji.                                                                                                                                                                                                                                                                        | Nie       | false             |
| typ buforowania               | Wybierz jedną z następujących wartości atrybutu:<br />- `internal`aby korzystać z wbudowanej pamięci podręcznej api Management,<br />- `external`aby użyć zewnętrznej pamięci podręcznej zgodnie z opisem w [obszarze Używanie zewnętrznej pamięci podręcznej platformy Azure dla funkcji Redis w usłudze Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external`do użycia zewnętrznej pamięci podręcznej, jeśli jest skonfigurowana lub wewnętrznej pamięci podręcznej w inny sposób. | Nie       | `prefer-external` |
| w dół-buforowanie typu        | Ten atrybut musi być ustawiony na jedną z następujących wartości.<br /><br /> - brak - buforowanie w dół jest niedozwolone.<br />- prywatne - niższego szczebla prywatne buforowanie jest dozwolone.<br />- dozwolone jest publiczne - prywatne i wspólne buforowanie niższego szczebla.                                                                                                          | Nie       | brak              |
| musi ponownie przyznać                | Gdy buforowanie podrzędne jest włączone, ten atrybut `must-revalidate` włącza lub wyłącza dyrektywę kontroli pamięci podręcznej w odpowiedziach bramy.                                                                                                                                                                                                                      | Nie       | true              |
| różne według deweloperów              | Ustaw `true` do buforowania odpowiedzi na [klucz subskrypcji](https://docs.microsoft.com/azure/api-management/api-management-subscriptions).                                                                                                                                                                                                                                                                                                         | Tak      |         False          |
| różne według grup deweloperów       | Ustaw `true` do buforowania odpowiedzi na [grupę użytkowników](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups).                                                                                                                                                                                                                                                                                                             | Tak      |       False            |

### <a name="usage"></a>Sposób użycia
Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

- **Sekcje zasad:** przychodzące
- **Zakresy zasad:** wszystkie zakresy

## <a name="store-to-cache"></a><a name="StoreToCache"></a>Przechowywanie do pamięci podręcznej
Zasady `cache-store` buforuje odpowiedzi zgodnie z określonymi ustawieniami pamięci podręcznej. Te zasady mogą być stosowane w przypadkach, gdy zawartość odpowiedzi pozostaje statyczna przez pewien czas. Buforowanie odpowiedzi zmniejsza wymagania dotyczące przepustowości i przetwarzania nałożone na serwer sieci web wewnętrznej bazy danych i zmniejsza opóźnienia postrzegane przez konsumentów interfejsu API.

> [!NOTE]
> Ta zasada musi mieć odpowiednie zasady [Pobierz z pamięci podręcznej.](api-management-caching-policies.md#GetFromCache)

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

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

#### <a name="example-using-policy-expressions"></a>Przykład użycia wyrażeń zasad
W tym przykładzie pokazano, jak skonfigurować czas buforowania odpowiedzi usługi API Management, który odpowiada buforowaniu odpowiedzi usługi wewnętrznej bazy danych, zgodnie z `Cache-Control` dyrektywą usługi kopii zapasowej. Aby zademonstrować konfigurowanie i używanie tych zasad, zobacz [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) and fast-forward to 25:25.

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

Aby uzyskać więcej informacji, zobacz [Wyrażenia zasad i](api-management-policy-expressions.md) [zmienna kontekstowa](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|magazyn pamięci podręcznej|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

| Nazwa             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagany | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| czas trwania         | Czas do żywo wpisów w pamięci podręcznej, określone w sekundach.                                                                                                                                                                                                                                                                                                   | Tak      | Nie dotyczy               |

### <a name="usage"></a>Sposób użycia
Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

- **Sekcje zasad: wychodzące**
- **Zakresy zasad:** wszystkie zakresy

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a>Uzyskaj wartość z pamięci podręcznej
Użyj `cache-lookup-value` zasad do wykonywania wyszukiwania pamięci podręcznej według klucza i zwracania wartości buforowanej. Klucz może mieć dowolną wartość ciągu i jest zazwyczaj dostarczany przy użyciu wyrażenia zasad.

> [!NOTE]
> Ta zasada musi mieć odpowiednią [wartość Magazynu w zasadach pamięci podręcznej.](#StoreToCacheByKey)

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Przykład
Aby uzyskać więcej informacji i przykładów tej zasady, zobacz [Niestandardowe buforowanie w usłudze Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|wartość wyszukiwania pamięci podręcznej|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

| Nazwa             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagany | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| typ buforowania | Wybierz jedną z następujących wartości atrybutu:<br />- `internal`aby korzystać z wbudowanej pamięci podręcznej api Management,<br />- `external`aby użyć zewnętrznej pamięci podręcznej zgodnie z opisem w [obszarze Używanie zewnętrznej pamięci podręcznej platformy Azure dla funkcji Redis w usłudze Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external`do użycia zewnętrznej pamięci podręcznej, jeśli jest skonfigurowana lub wewnętrznej pamięci podręcznej w inny sposób. | Nie       | `prefer-external` |
| wartość domyślna    | Wartość, która zostanie przypisana do zmiennej, jeśli wyszukiwanie klucza pamięci podręcznej spowodowało pominięcie. Jeśli ten atrybut nie `null` jest określony, jest przypisany.                                                                                                                                                                                                           | Nie       | `null`            |
| key              | Wartość klucza pamięci podręcznej do użycia w odnośniku.                                                                                                                                                                                                                                                                                                                       | Tak      | Nie dotyczy               |
| nazwa zmiennej    | Nazwa [zmiennej kontekstowej,](api-management-policy-expressions.md#ContextVariables) do która zostanie przypisana wyszukane wartości, jeśli wyszukiwanie zakończy się pomyślnie. Jeśli wyszukiwanie spowoduje pominięcie, zmienna zostanie przypisana `default-value` wartość atrybutu `null`lub `default-value` , jeśli atrybut zostanie pominięty.                                       | Tak      | Nie dotyczy               |

### <a name="usage"></a>Sposób użycia
Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

- **Sekcje zasad:** przychodzące, wychodzące, wewnętrznej bazy danych, on-error
- **Zakresy zasad:** wszystkie zakresy

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a>Przechowywanie wartości w pamięci podręcznej
Wykonuje `cache-store-value` magazyn pamięci podręcznej według klucza. Klucz może mieć dowolną wartość ciągu i jest zazwyczaj dostarczany przy użyciu wyrażenia zasad.

> [!NOTE]
> Ta zasada musi mieć odpowiednią [wartość Pobierz z zasad pamięci podręcznej.](#GetFromCacheByKey)

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Przykład
Aby uzyskać więcej informacji i przykładów tej zasady, zobacz [Niestandardowe buforowanie w usłudze Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|cache-store-wartość|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

| Nazwa             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagany | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| typ buforowania | Wybierz jedną z następujących wartości atrybutu:<br />- `internal`aby korzystać z wbudowanej pamięci podręcznej api Management,<br />- `external`aby użyć zewnętrznej pamięci podręcznej zgodnie z opisem w [obszarze Używanie zewnętrznej pamięci podręcznej platformy Azure dla funkcji Redis w usłudze Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external`do użycia zewnętrznej pamięci podręcznej, jeśli jest skonfigurowana lub wewnętrznej pamięci podręcznej w inny sposób. | Nie       | `prefer-external` |
| czas trwania         | Wartość zostanie buforowana dla podanej wartości czasu trwania, określonej w sekundach.                                                                                                                                                                                                                                                                                 | Tak      | Nie dotyczy               |
| key              | Klucz pamięci podręcznej, pod jego poniżej, zostanie zapisany klucz pamięci podręcznej.                                                                                                                                                                                                                                                                                                                   | Tak      | Nie dotyczy               |
| value            | Wartość, która ma być buforowana.                                                                                                                                                                                                                                                                                                                                     | Tak      | Nie dotyczy               |
### <a name="usage"></a>Sposób użycia
Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

- **Sekcje zasad:** przychodzące, wychodzące, wewnętrznej bazy danych, on-error
- **Zakresy zasad:** wszystkie zakresy

### <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a>Usuwanie wartości z pamięci podręcznej
Usuwa `cache-remove-value` element w pamięci podręcznej identyfikowany przez jego klucz. Klucz może mieć dowolną wartość ciągu i jest zazwyczaj dostarczany przy użyciu wyrażenia zasad.

#### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Przykład

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|cache-remove-value|Element główny.|Tak|

#### <a name="attributes"></a>Atrybuty

| Nazwa             | Opis                                                                                                                                                                                                                                                                                                                                                 | Wymagany | Domyślne           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| typ buforowania | Wybierz jedną z następujących wartości atrybutu:<br />- `internal`aby korzystać z wbudowanej pamięci podręcznej api Management,<br />- `external`aby użyć zewnętrznej pamięci podręcznej zgodnie z opisem w [obszarze Używanie zewnętrznej pamięci podręcznej platformy Azure dla funkcji Redis w usłudze Azure API Management](api-management-howto-cache-external.md),<br />- `prefer-external`do użycia zewnętrznej pamięci podręcznej, jeśli jest skonfigurowana lub wewnętrznej pamięci podręcznej w inny sposób. | Nie       | `prefer-external` |
| key              | Klucz wcześniej buforowanej wartości do usunięcia z pamięci podręcznej.                                                                                                                                                                                                                                                                                        | Tak      | Nie dotyczy               |

#### <a name="usage"></a>Sposób użycia
Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) zasad .

- **Sekcje zasad:** przychodzące, wychodzące, wewnętrznej bazy danych, on-error
- **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w zarządzaniu interfejsami API](api-management-howto-policies.md)
+ [Przekształcanie interfejsów API](transform-api.md)
+ [Odwołanie do zasad](api-management-policy-reference.md) dla pełnej listy oświadczeń zasad i ich ustawień
+ [Przykłady zasad](policy-samples.md)
