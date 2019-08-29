---
title: Zasady zaawansowane platformy Azure API Management | Microsoft Docs
description: Dowiedz się więcej na temat zaawansowanych zasad dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: efc439d56ee864d940942369b3d226ed2a94a383
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072635"
---
# <a name="api-management-advanced-policies"></a>Zasady zaawansowane API Management

Ten temat zawiera informacje dotyczące następujących zasad API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasady w API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AdvancedPolicies"></a>Zasady zaawansowane

-   [Przepływ sterowania](api-management-advanced-policies.md#choose) — warunkowo stosuje instrukcje zasad na podstawie wyników oceny [wyrażeń](api-management-policy-expressions.md)logicznych.
-   [Żądanie przesyłania dalej](#ForwardRequest) przesyła żądanie do usługi zaplecza.
-   [Ograniczenie współbieżności](#LimitConcurrency) uniemożliwia wykonywanie załączonych zasad przez więcej niż określoną liczbę żądań w danym momencie.
-   [Logowanie do centrum zdarzeń](#log-to-eventhub) — wysyła komunikaty w określonym formacie do centrum zdarzeń zdefiniowanego przez jednostkę rejestratora.
-   [Makieta odpowiedzi](#mock-response) — przerywa wykonywanie potoku i zwraca zamakietę odpowiedzi bezpośrednio do obiektu wywołującego.
-   Ponawianie próbuje [wykonać](#Retry) załączone instrukcje zasad, jeśli i do momentu spełnienia warunku. Wykonanie będzie powtarzane w określonych odstępach czasu i do określonej liczby ponownych prób.
-   [Odpowiedź zwrotna](#ReturnResponse) — przerywa wykonywanie potoku i zwraca określoną odpowiedź bezpośrednio do obiektu wywołującego.
-   [Wyślij jednokierunkowe żądanie](#SendOneWayRequest) — wysyła żądanie do określonego adresu URL bez oczekiwania na odpowiedź.
-   [Wyślij żądanie](#SendRequest) — wysyła żądanie do określonego adresu URL.
-   [Ustaw serwer proxy HTTP](#SetHttpProxy) — umożliwia kierowanie żądań przesyłanych dalej za pośrednictwem serwera proxy HTTP.
-   [Ustaw metodę żądania](#SetRequestMethod) — pozwala zmienić metodę http dla żądania.
-   [Ustawianie kodu stanu](#SetStatus) — zmienia kod stanu HTTP na określoną wartość.
-   [Set Variable](api-management-advanced-policies.md#set-variable) — utrwala wartość w nazwanej zmiennej [kontekstowej](api-management-policy-expressions.md#ContextVariables) w celu późniejszego dostępu.
-   [Trace](#Trace) — dodaje ciąg do danych wyjściowych [inspektora interfejsu API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) .
-   [](#Wait) Zaczekaj — oczekiwanie na załączone [żądanie wysłania](api-management-advanced-policies.md#SendRequest), [Pobierz wartość z pamięci](api-management-caching-policies.md#GetFromCacheByKey)podręcznej lub zasady [przepływu sterowania](api-management-advanced-policies.md#choose) do ukończenia przed kontynuowaniem.

## <a name="choose"></a>Przepływ sterowania

`choose` Zasady stosują się do załączonych instrukcji zasad na podstawie wyniku oceny wyrażeń logicznych, podobnie jak konstrukcja if-then-else lub Switch w języku programowania.

### <a name="ChoosePolicyStatement"></a>Instrukcja zasad

```xml
<choose>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <otherwise>
        <!— one or more policy statements to be applied if none of the above conditions are true  -->
</otherwise>
</choose>
```

Zasady przepływu sterowania muszą zawierać co najmniej jeden `<when/>` element. `<otherwise/>` Element jest opcjonalny. Warunki w `<when/>` elementach są oceniane w kolejności ich wyglądu w ramach zasad. Deklaracje zasad ujęte w pierwszym `<when/>` elemencie z atrybutem Condition Equals `true` zostaną zastosowane. Zasady zawarte w `<otherwise/>` elemencie (jeśli istnieją) będą stosowane, jeśli wszystkie `<when/>` atrybuty warunku elementu są `false`.

### <a name="examples"></a>Przykłady

#### <a name="ChooseExample"></a>Przyklad

Poniższy przykład ilustruje zasady [zestawu zmiennych](api-management-advanced-policies.md#set-variable) i dwie zasady przepływu sterowania.

Zasada Ustawianie zmiennej znajduje się w sekcji przychodzące i `isMobile` tworzy zmienną [kontekstu](api-management-policy-expressions.md#ContextVariables) logicznego, która ma wartość true, jeśli `User-Agent` nagłówek żądania zawiera tekst `iPad` lub `iPhone`.

Zasady pierwszego przepływu sterowania są również w sekcji przychodzące i warunkowo stosuje jedną z dwóch [ustawionych zasad parametrów ciągu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) w zależności od wartości `isMobile` zmiennej kontekstowej.

Zasada drugiego przepływu sterowania znajduje się w sekcji wychodzące i warunkowo stosuje zasady [Konwertuj XML do JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) , `isMobile` gdy jest ustawiony `true`na.

```xml
<policies>
    <inbound>
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <set-query-parameter name="mobile" exists-action="override">
                    <value>true</value>
                </set-query-parameter>
            </when>
            <otherwise>
                <set-query-parameter name="mobile" exists-action="override">
                    <value>false</value>
                </set-query-parameter>
            </otherwise>
        </choose>
    </inbound>
    <outbound>
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>
            </when>
        </choose>
    </outbound>
</policies>
```

#### <a name="example"></a>Przykład

Ten przykład pokazuje, jak wykonywać filtrowanie zawartości przez usunięcie elementów danych z odpowiedzi otrzymanej z usługi wewnętrznej bazy wiedzy podczas korzystania `Starter` z produktu. Aby zapoznać się z prezentacją konfigurowania i używania tych zasad [, zobacz temat Cloud okładki epizod 177: Więcej API Management funkcji Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkich — do 34:30. Zacznij od 31:50, aby zapoznać się z omówieniem [interfejsu API prognozowania ciemnej przestrzeniki](https://developer.forecast.io/) używanej w tej wersji demonstracyjnej.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="elements"></a>Elementy

| Element   | Opis                                                                                                                                                                                                                                                               | Wymagane |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| następnie    | Element główny.                                                                                                                                                                                                                                                             | Tak      |
| kiedy      | Warunek, który ma być używany `if` dla `ifelse` lub części `choose` zasad. Jeśli zasady zawierają wiele `when` sekcji, są oceniane sekwencyjnie. `choose` Gdy element elementu when ma `true`wartość, nie są oceniane żadne dalsze `when` warunki. `condition` | Tak      |
| przypadku | Zawiera fragment kodu zasad, który ma być używany, jeśli `when` nie ma żadnych `true`warunków do obliczenia.                                                                                                                                                                               | Nie       |

### <a name="attributes"></a>Atrybuty

| Atrybut                                              | Opis                                                                                               | Wymagane |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| warunek = "wyrażenie &#124; logiczne stała Boolean" | Wyrażenie logiczne lub stała do oceny, gdy zostanie oceniona zawierająca `when` ją instrukcja Policy. | Tak      |

### <a name="ChooseUsage"></a>Wykorzystywani

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="ForwardRequest"></a>Prześlij żądanie dalej

Zasady przesyłają żądanie przychodzące do usługi wewnętrznej bazy danych określonej w kontekście żądania. [](api-management-policy-expressions.md#ContextVariables) `forward-request` Adres URL usługi wewnętrznej bazy danych jest określony w [ustawieniach](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) interfejsu API i można go zmienić przy użyciu zasad dla [Ustawienia usługi zaplecza](api-management-transformation-policies.md) .

> [!NOTE]
> Usunięcie tych zasad spowoduje, że żądanie nie zostanie przekazane do usługi wewnętrznej bazy danych, a zasady w sekcji wychodzącej są oceniane natychmiast po pomyślnym zakończeniu zasad w sekcji przychodzące.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<forward-request timeout="time in seconds" follow-redirects="true | false" buffer-request-body="true | false" />
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

Poniższe zasady poziomu interfejsu API przesyłają dalej wszystkie żądania interfejsu API do usługi wewnętrznej bazy danych z interwałem limitu czasu wynoszącym 60 sekund.

```xml
<!-- api level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="60"/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Przykład

Te zasady poziomu operacji używają `base` elementu do dziedziczenia zasad zaplecza z nadrzędnego zakresu poziomu interfejsu API.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <base/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Przykład

Te zasady na poziomie operacji jawnie przekazują wszystkie żądania do usługi wewnętrznej bazy danych o limicie czasu 120 i nie dziedziczą zasad zaplecza nadrzędnego interfejsu API.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120"/>
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Przykład

Te zasady poziomu operacji nie przesyłają dalej żądań do usługi wewnętrznej bazy danych.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <!-- no forwarding to backend -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

### <a name="elements"></a>Elementy

| Element         | Opis   | Wymagane |
| --------------- | ------------- | -------- |
| Prześlij dalej żądania | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut                               | Opis                                                                                                      | Wymagane | Domyślny     |
| --------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | -------- | ----------- |
| timeout="integer"                       | Czas (w sekundach) oczekiwania na zwrócenie nagłówków odpowiedzi HTTP przez usługę zaplecza przed podjęciem błędu limitu czasu. Wartość minimalna to 0 s. Wartości większe niż 240 sekund mogą nie być honorowane, ponieważ źródłowa infrastruktura sieciowa może porzucić bezczynne połączenia po tym czasie. | Nie       | Brak |
| follow-redirects="true &#124; false"    | Określa, czy przekierowania z usługi wewnętrznej bazy danych następuje przez bramę, czy zwracane do obiektu wywołującego.      | Nie       | false       |
| buffer-request-body="true &#124; false" | Kiedy wartość "true" żądania jest buforowana i zostanie ponownie użyta podczas [ponawiania](api-management-advanced-policies.md#Retry). | Nie       | false       |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** zaplecze
-   **Zakresy zasad:** wszystkie zakresy

## <a name="LimitConcurrency"></a>Ogranicz współbieżność

Zasady `limit-concurrency` te uniemożliwiają wykonywanie zawartych zasad przez więcej niż określoną liczbę żądań w dowolnym momencie. Po przekroczeniu tej liczby nowe żądania będą natychmiast kończyć się niepowodzeniem z 429 zbyt dużą liczbą żądań w kodzie stanu.

### <a name="LimitConcurrencyStatement"></a>Instrukcja zasad

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

W poniższym przykładzie pokazano, jak ograniczyć liczbę żądań przesyłanych do zaplecza na podstawie wartości zmiennej kontekstowej.

```xml
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    <limit-concurrency/>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Element           | Opis   | Wymagane |
| ----------------- | ------------- | -------- |
| limit-concurrency | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                                                        | Wymagane | Domyślny |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | Ciąg. Wyrażenie jest dozwolone. Określa zakres współbieżności. Mogą być współużytkowane przez wiele zasad. | Tak      | ND     |
| Max-Count | Liczba całkowita. Określa maksymalną liczbę żądań, które mogą wejść do zasad.           | Tak      | ND     |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="log-to-eventhub"></a>Logowanie do centrum zdarzeń

`log-to-eventhub` Zasady wysyłają komunikaty w określonym formacie do centrum zdarzeń zdefiniowanego przez jednostkę rejestratora. Zgodnie z jego nazwą, zasady są używane do zapisywania informacji o wybranym żądaniu lub kontekście odpowiedzi dla analizy w trybie online lub offline.

> [!NOTE]
> Przewodnik krok po kroku dotyczący konfigurowania zdarzeń centrum zdarzeń i rejestrowania można znaleźć w temacie [How to log API Management Events with Azure Event Hubs](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Przykład

Dowolny ciąg może być używany jako wartość, która ma być zalogowana Event Hubs. W tym przykładzie Data i godzina, nazwa usługi wdrażania, identyfikator żądania, adres IP i nazwa operacji dla wszystkich wywołań przychodzących są rejestrowane w rejestratorze centrum zdarzeń zarejestrowanym z `contoso-logger` identyfikatorem.

```xml
<policies>
  <inbound>
    <log-to-eventhub logger-id ='contoso-logger'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )
    </log-to-eventhub>
  </inbound>
  <outbound>
  </outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Element         | Opis                                                                     | Wymagane |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| Logowanie do centrum eventhub | Element główny. Wartość tego elementu jest ciągiem, który ma być zalogowany do centrum zdarzeń. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut     | Opis                                                               | Wymagane                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| logger-id     | Identyfikator rejestratora zarejestrowanego w usłudze API Management.         | Tak                                                                  |
| Identyfikator partycji  | Określa indeks partycji, w której będą wysyłane wiadomości.             | Opcjonalna. Tego atrybutu nie można użyć, jeśli `partition-key` jest używany. |
| klucz partycji | Określa wartość używaną do przypisywania partycji podczas wysyłania wiadomości. | Opcjonalny. Tego atrybutu nie można użyć, jeśli `partition-id` jest używany.  |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="mock-response"></a>Makieta odpowiedzi

`mock-response`, Jak nazwa oznacza, jest używany do makietowania interfejsów API i operacji. Przerywa normalne wykonywanie potoku i zwraca zamakietę odpowiedzi do obiektu wywołującego. Zasady zawsze podejmują próbę zwrócenia odpowiedzi o najwyższej wierności. Preferuje przykłady zawartości odpowiedzi, jeśli są dostępne. Generuje przykładowe odpowiedzi ze schematów, gdy schematy są udostępniane i przykłady nie są. Jeśli nie zostaną znalezione żadne przykłady ani schematy, odpowiedzi bez żadnej zawartości są zwracane.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<mock-response status-code="code" content-type="media type"/>

```

### <a name="examples"></a>Przykłady

```xml
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>
```

### <a name="elements"></a>Elementy

| Element       | Opis   | Wymagane |
| ------------- | ------------- | -------- |
| mock-response | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut    | Opis                                                                                           | Wymagane | Domyślny |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| stan — kod  | Określa kod stanu odpowiedzi i służy do wybierania odpowiedniego przykładu lub schematu.                 | Nie       | 200     |
| content-type | Określa `Content-Type` wartość nagłówka odpowiedzi i służy do wybierania odpowiedniego przykładu lub schematu. | Nie       | Brak    |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="Retry"></a>Spróbuj ponownie wykonać

Zasady `retry` te wykonują zasady podrzędne raz, a następnie ponawiają próbę wykonania, `condition` dopóki `false` nie zostanie `count` ponowiona lub nie zostanie ponowiona.

### <a name="policy-statement"></a>Instrukcja zasad

```xml

<retry
    condition="boolean expression or literal"
    count="number of retry attempts"
    interval="retry interval in seconds"
    max-interval="maximum retry interval in seconds"
    delta="retry interval delta in seconds"
    first-fast-retry="boolean expression or literal">
        <!-- One or more child policies. No restrictions -->
</retry>

```

### <a name="example"></a>Przykład

W poniższym przykładzie przekazanie żądania jest ponawiane do dziesięciu razy przy użyciu algorytmu ponowień wykładniczych. Ponieważ `first-fast-retry` jest ustawiona na wartość false, wszystkie próby ponowienia są objęte algorytmem ponawiania próby wykładniczej.

```xml

<retry
    condition="@(context.Response.StatusCode == 500)"
    count="10"
    interval="10"
    max-interval="100"
    delta="10"
    first-fast-retry="false">
        <forward-request buffer-request-body="true" />
</retry>

```

### <a name="elements"></a>Elementy

| Element | Opis                                                         | Wymagane |
| ------- | ------------------------------------------------------------------- | -------- |
| ponów   | Element główny. Mogą zawierać inne zasady jako elementy podrzędne. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut        | Opis                                                                                                                                           | Wymagane | Domyślny |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| condition        | Literał logiczny lub [wyrażenie](api-management-policy-expressions.md) określające, czy ponawianie próby powinno zostać`false`zatrzymane ()`true`lub ciąg ().      | Tak      | ND     |
| count            | Liczba dodatnia określająca maksymalną liczbę ponownych prób.                                                                                | Tak      | ND     |
| przedział         | Dodatnia liczba sekund określająca interwał oczekiwania między ponownymi próbami.                                                                 | Tak      | ND     |
| max-interval     | Dodatnia liczba sekund określająca maksymalny interwał oczekiwania między ponownymi próbami. Służy do implementowania algorytmu ponowień wykładniczych. | Nie       | ND     |
| delta            | Dodatnia liczba sekund określająca przyrost interwału oczekiwania. Służy do implementowania algorytmów ponawiania liniowego i wykładniczego.             | Nie       | ND     |
| pierwszy — szybko ponów próbę | Jeśli jest ustawiona `true` na, pierwsza próba ponowienia zostanie wykonana natychmiast.                                                                                  | Nie       | `false` |

> [!NOTE]
> Gdy jest określona `interval` tylko wartość, zostanie wykonany ponowna próba interwału **stałego** .
> Gdy tylko `interval` i `interval + (count - 1)*delta`są określone, jest używany algorytm ponawiania interwału, w którym czas oczekiwania między ponownymi próbami jest obliczany zgodnie z poniższą formułą. `delta`
> `interval` Gdy, isą`delta` określone, obowiązuje algorytm ponowienia interwału wykładniczego, gdzie czas oczekiwania między ponownymi próbami rośnie wykładniczo od wartości do wartości `interval` `max-interval` `max-interval`zgodnie z poniższą formułą `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`—.

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) zasad. Należy zauważyć, że ograniczenia użycia zasad podrzędnych będą dziedziczone przez te zasady.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="ReturnResponse"></a>Odpowiedź zwrócona

`return-response` Zasady przerywają wykonywanie potoku i zwracają domyślną lub niestandardową odpowiedź do obiektu wywołującego. Odpowiedź domyślna to `200 OK` bez treści. Niestandardową odpowiedź można określić za pomocą zmiennej kontekstowej lub instrukcji zasad. Gdy są podane oba, odpowiedzi zawarte w zmiennej kontekstowej są modyfikowane przez instrukcje zasad przed zwróceniem do obiektu wywołującego.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<return-response response-variable-name="existing context variable">
  <set-header/>
  <set-body/>
  <set-status/>
</return-response>

```

### <a name="example"></a>Przykład

```xml
<return-response>
   <set-status code="401" reason="Unauthorized"/>
   <set-header name="WWW-Authenticate" exists-action="override">
      <value>Bearer error="invalid_token"</value>
   </set-header>
</return-response>

```

### <a name="elements"></a>Elementy

| Element         | Opis                                                                               | Wymagane |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| odpowiedź zwrotna | Element główny.                                                                             | Tak      |
| set-header      | Instrukcja zasad [Set-header](api-management-transformation-policies.md#SetHTTPheader) . | Nie       |
| Ustaw treść        | Deklaracja zasad dotyczących [zestawu](api-management-transformation-policies.md#SetBody) .         | Nie       |
| set-status      | Zestawienie zasad dotyczących [stanu](api-management-advanced-policies.md#SetStatus) .           | Nie       |

### <a name="attributes"></a>Atrybuty

| Atrybut              | Opis                                                                                                                                                                          | Wymagane  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| response-variable-name | Nazwa zmiennej kontekstowej, do której odwołuje się, na przykład nadrzędne zasady [wysyłania](api-management-advanced-policies.md#SendRequest) i zawierający `Response` obiekt | Opcjonalna. |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="SendOneWayRequest"></a>Wyślij jednokierunkowe żądanie

`send-one-way-request` Zasady wysyłają podane żądanie do określonego adresu URL bez oczekiwania na odpowiedź.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<send-one-way-request mode="new | copy">
  <url>...</url>
  <method>...</method>
  <header name="" exists-action="override | skip | append | delete">...</header>
  <body>...</body>
  <authentication-certificate thumbprint="thumbprint" />
</send-one-way-request>

```

### <a name="example"></a>Przykład

Te przykładowe zasady przedstawiają przykład użycia `send-one-way-request` zasad do wysyłania komunikatu do pokoju rozmów zapasowych, jeśli kod odpowiedzi HTTP jest większy lub równy 500. Aby uzyskać więcej informacji na temat tego przykładu, zobacz [Korzystanie z usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Elementy

| Element                    | Opis                                                                                                 | Wymagane                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| send-one-way-request       | Element główny.                                                                                               | Tak                             |
| url                        | Adres URL żądania.                                                                                     | Nie, jeśli tryb = Copy; w przeciwnym razie. |
| — metoda                     | Metoda HTTP dla żądania.                                                                            | Nie, jeśli tryb = Copy; w przeciwnym razie. |
| nagłówek                     | Nagłówek żądania. Użyj wielu elementów nagłówka dla wielu nagłówków żądań.                                  | Nie                              |
| treść                       | Treść żądania.                                                                                           | Nie                              |
| Uwierzytelnianie — certyfikat | [Certyfikat do użycia na potrzeby uwierzytelniania klientów](api-management-authentication-policies.md#ClientCertificate) | Nie                              |

### <a name="attributes"></a>Atrybuty

| Atrybut     | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślny  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string" | Określa, czy jest to nowe żądanie, czy kopię bieżącego żądania. W trybie wychodzącym tryb = Copy nie inicjuje treści żądania.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nie       | Nowa      |
| name          | Określa nazwę nagłówka, który ma zostać ustawiony.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Tak      | ND      |
| Istnieje — akcja | Określa akcję, która ma zostać podjęta, gdy nagłówek jest już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> -override — zastępuje wartość istniejącego nagłówka.<br />-Skip — nie zastępuje istniejącej wartości nagłówka.<br />-Append-dołącza wartość do istniejącej wartości nagłówka.<br />-DELETE — usuwa nagłówek z żądania.<br /><br /> Gdy ustawione na `override` rejestrowanie wielu wpisów o tej samej nazwie powoduje, że nagłówek jest ustawiany zgodnie ze wszystkimi wpisami (które zostaną wyświetlone wiele razy); w wyniku zostaną ustawione tylko wartości wyświetlane. | Nie       | mapowań |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="SendRequest"></a>Wyślij żądanie

`send-request` Zasady wysyłają podane żądanie do określonego adresu URL, co nie przekracza wartości limitu czasu.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error
="false|true">
  <set-url>...</set-url>
  <set-method>...</set-method>
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>
  <set-body>...</set-body>
  <authentication-certificate thumbprint="thumbprint" />
</send-request>

```

### <a name="example"></a>Przykład

Ten przykład pokazuje jeden ze sposobów na zweryfikowanie tokenu odwołania z serwerem autoryzacji. Aby uzyskać więcej informacji na temat tego przykładu, zobacz [Korzystanie z usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
        <!-- Check active property in response -->
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
            <!-- Return 401 Unauthorized with http-problem payload -->
            <return-response>
                <set-status code="401" reason="Unauthorized" />
                <set-header name="WWW-Authenticate" exists-action="override">
                    <value>Bearer error="invalid_token"</value>
                </set-header>
            </return-response>
        </when>
    </choose>
  <base />
</inbound>

```

### <a name="elements"></a>Elementy

| Element                    | Opis                                                                                                 | Wymagane                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| Wyślij żądanie               | Element główny.                                                                                               | Tak                             |
| url                        | Adres URL żądania.                                                                                     | Nie, jeśli tryb = Copy; w przeciwnym razie. |
| — metoda                     | Metoda HTTP dla żądania.                                                                            | Nie, jeśli tryb = Copy; w przeciwnym razie. |
| nagłówek                     | Nagłówek żądania. Użyj wielu elementów nagłówka dla wielu nagłówków żądań.                                  | Nie                              |
| treść                       | Treść żądania.                                                                                           | Nie                              |
| Uwierzytelnianie — certyfikat | [Certyfikat do użycia na potrzeby uwierzytelniania klientów](api-management-authentication-policies.md#ClientCertificate) | Nie                              |

### <a name="attributes"></a>Atrybuty

| Atrybut                       | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślny  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string"                   | Określa, czy jest to nowe żądanie, czy kopię bieżącego żądania. W trybie wychodzącym tryb = Copy nie inicjuje treści żądania.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nie       | Nowa      |
| response-variable-name="string" | Nazwa zmiennej kontekstowej, która będzie odbierać obiekt odpowiedzi. Jeśli zmienna nie istnieje, zostanie utworzona po pomyślnym wykonaniu zasad i stanie się dostępna za pośrednictwem [`context.Variable`](api-management-policy-expressions.md#ContextVariables) kolekcji.                                                                                                                                                                                                                                                                                                                          | Tak      | ND      |
| timeout="integer"               | Interwał limitu czasu (w sekundach), po którym wywołanie adresu URL nie powiedzie się.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Nie       | 60       |
| Ignoruj-błąd                    | Jeśli wartość jest równa true, a żądanie powoduje błąd:<br /><br /> -Jeśli określono odpowiedź-Variable-Name, będzie zawierać wartość null.<br />-Jeśli odpowiedź-Zmienna-name nie została określona, Context. Żądanie nie zostanie zaktualizowane.                                                                                                                                                                                                                                                                                                                                                                                   | Nie       | false    |
| name                            | Określa nazwę nagłówka, który ma zostać ustawiony.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Tak      | ND      |
| Istnieje — akcja                   | Określa akcję, która ma zostać podjęta, gdy nagłówek jest już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> -override — zastępuje wartość istniejącego nagłówka.<br />-Skip — nie zastępuje istniejącej wartości nagłówka.<br />-Append-dołącza wartość do istniejącej wartości nagłówka.<br />-DELETE — usuwa nagłówek z żądania.<br /><br /> Gdy ustawione na `override` rejestrowanie wielu wpisów o tej samej nazwie powoduje, że nagłówek jest ustawiany zgodnie ze wszystkimi wpisami (które zostaną wyświetlone wiele razy); w wyniku zostaną ustawione tylko wartości wyświetlane. | Nie       | mapowań |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="SetHttpProxy"></a>Ustaw serwer proxy HTTP

`proxy` Zasady umożliwiają kierowanie żądań przesyłanych do frontonu za pośrednictwem serwera proxy HTTP. Między bramą i serwerem proxy obsługiwane są tylko protokół HTTP (nie HTTPS). Tylko uwierzytelnianie podstawowe i NTLM.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Przykład

Zwróć uwagę na użycie [Właściwości](api-management-howto-properties.md) jako wartości nazwy użytkownika i hasła, aby uniknąć przechowywania poufnych informacji w dokumencie zasad.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elementy

| Element | Opis  | Wymagane |
| ------- | ------------ | -------- |
| Serwer proxy   | Element główny | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut         | Opis                                            | Wymagane | Domyślny |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="string"      | Adres URL serwera proxy w postaci http://host:port.             | Tak      | ND     |
| username="string" | Nazwa użytkownika, która ma być używana na potrzeby uwierzytelniania z serwerem proxy. | Nie       | ND     |
| password="string" | Hasło, które ma być używane na potrzeby uwierzytelniania z serwerem proxy. | Nie       | ND     |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące

-   **Zakresy zasad:** wszystkie zakresy

## <a name="SetRequestMethod"></a>Ustaw metodę żądania

`set-method` Zasady umożliwiają zmianę metody żądania HTTP dla żądania.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Przykład

Ta przykładowa zasada korzystająca `set-method` z zasad pokazuje przykład wysyłania komunikatu do pokoju rozmów zapasowych, jeśli kod odpowiedzi HTTP jest większy lub równy 500. Aby uzyskać więcej informacji na temat tego przykładu, zobacz [Korzystanie z usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Elementy

| Element    | Opis                                                       | Wymagane |
| ---------- | ----------------------------------------------------------------- | -------- |
| Set-Method | Element główny. Wartość elementu określa metodę HTTP. | Tak      |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="SetStatus"></a>Ustaw kod stanu

`set-status` Zasada ustawia kod stanu HTTP na określoną wartość.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Przykład

Ten przykład pokazuje, jak zwrócić odpowiedź 401, jeśli token autoryzacji jest nieprawidłowy. Aby uzyskać więcej informacji, zobacz [Korzystanie z usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>

```

### <a name="elements"></a>Elementy

| Element    | Opis   | Wymagane |
| ---------- | ------------- | -------- |
| set-status | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut       | Opis                                                | Wymagane | Domyślny |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code="integer"  | Kod stanu HTTP do zwrócenia.                            | Tak      | ND     |
| reason="string" | Opis przyczyny zwrócenia kodu stanu. | Tak      | ND     |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** wychodzące, zaplecze, w przypadku błędu
-   **Zakresy zasad:** wszystkie zakresy

## <a name="set-variable"></a>Ustaw zmienną

Zasady deklarują zmienną kontekstową i przypisuje jej wartość określoną za pośrednictwem [wyrażenia](api-management-policy-expressions.md) lub literału ciągu. [](api-management-policy-expressions.md#ContextVariables) `set-variable` Jeśli wyrażenie zawiera literał, zostanie on przekonwertowany na ciąg, a typ wartości będzie `System.String`.

### <a name="set-variablePolicyStatement"></a>Instrukcja zasad

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="set-variableExample"></a>Przyklad

Poniższy przykład ilustruje Ustawianie zmiennych zasad w sekcji przychodzące. Ten zestaw zasad zmiennych `isMobile` tworzy zmienną [kontekstu](api-management-policy-expressions.md#ContextVariables) logicznego, która ma wartość true, jeśli `User-Agent` nagłówek żądania zawiera tekst `iPad` lub `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elementy

| Element      | Opis   | Wymagane |
| ------------ | ------------- | -------- |
| Set-Variable | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                              | Wymagane |
| --------- | ------------------------------------------------------------------------ | -------- |
| name      | Nazwa zmiennej.                                                | Tak      |
| value     | Wartość zmiennej. Może to być wyrażenie lub wartość literału. | Tak      |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu
-   **Zakresy zasad:** wszystkie zakresy

### <a name="set-variableAllowedTypes"></a>Dozwolone typy

Wyrażenia używane w `set-variable` zasadach muszą zwracać jeden z następujących typów podstawowych.

-   System.Boolean
-   System.SByte
-   System.Byte
-   System.UInt16
-   System.UInt32
-   System.UInt64
-   System.Int16
-   System.Int32
-   System.Int64
-   System.Decimal
-   System.Single
-   System.Double
-   System.Guid
-   System.String
-   System.Char
-   System.DateTime
-   System.TimeSpan
-   System.Byte?
-   System.UInt16?
-   System.UInt32?
-   System.UInt64?
-   System.Int16?
-   System.Int32?
-   System.Int64?
-   System.Decimal?
-   System.Single?
-   System.Double?
-   System.Guid?
-   System.String?
-   System.Char?
-   System.DateTime?

## <a name="Trace"></a>Szuka

Zasady dodają ciąg do danych wyjściowych [inspektora interfejsu API.](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) `trace` Zasady zostaną wykonane tylko wtedy, gdy śledzenie jest wyzwalane, `Ocp-Apim-Trace` tj. nagłówek żądania jest obecny `true` `Ocp-Apim-Subscription-Key` , a nagłówek żądania jest obecny i zawiera prawidłowy klucz skojarzony z kontem administratora.

### <a name="policy-statement"></a>Instrukcja zasad

```xml

<trace source="arbitrary string literal">
    <!-- string expression or literal -->
</trace>

```

### <a name="elements"></a>Elementy

| Element | Opis   | Wymagane |
| ------- | ------------- | -------- |
| szuka   | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                                             | Wymagane | Domyślny |
| --------- | --------------------------------------------------------------------------------------- | -------- | ------- |
| source    | Literał ciągu istotny dla podglądu śledzenia i określający źródło wiadomości. | Tak      | ND     |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="Wait"></a>Trwa

`wait` Zasady domyślnie wykonują bezpośrednie zasady podrzędne i czekają na zakończenie wszystkich lub jednej z jej bezpośrednich zasad podrzędnych przed zakończeniem. Zasady oczekiwania mogą mieć charakter żądania natychmiastowego wysłania [żądań](api-management-advanced-policies.md#SendRequest), [pobrania wartości z pamięci](api-management-caching-policies.md#GetFromCacheByKey)podręcznej i zasad [przepływu sterowania](api-management-advanced-policies.md#choose) .

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Przykład

W poniższym przykładzie istnieją dwie `choose` zasady jako bezpośrednie zasady `wait` podrzędne zasad. Każda z tych `choose` zasad jest wykonywana równolegle. Każda `choose` zasada próbuje pobrać buforowaną wartość. W przypadku braku miejsca w pamięci podręcznej wywoływana jest usługa zaplecza, aby zapewnić wartość. W tym przykładzie `wait` zasady nie zostaną ukończone przed ukończeniem wszystkich bezpośrednich zasad podrzędnych, `for` ponieważ atrybut jest ustawiony na `all`. W tym przykładzie zmienne kontekstowe (`execute-branch-one`, `value-one`, `execute-branch-two`i `value-two`) są zadeklarowane poza zakresem tych przykładowych zasad.

```xml
<wait for="all">
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-one="])">
      <cache-lookup-value key="key-one" variable-name="value-one" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-one="))">
          <send-request mode="new" response-variable-name="value-one">
            <set-url>https://backend-one</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-two="])">
      <cache-lookup-value key="key-two" variable-name="value-two" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-two="))">
          <send-request mode="new" response-variable-name="value-two">
            <set-url>https://backend-two</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
</wait>

```

### <a name="elements"></a>Elementy

| Element | Opis                                                                                                   | Wymagane |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| trwa    | Element główny. Może zawierać tylko `send-request`elementy podrzędne, `cache-lookup-value`i `choose` zasady. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                                                                                                                                                                                                                                                                                                                                                                            | Wymagane | Domyślny |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| dla       | Określa, `wait` czy zasady czekają na ukończenie wszystkich bezpośrednich zasad podrzędnych, czy tylko jeden. Dozwolone wartości to:<br /><br /> - `all`-Poczekaj na zakończenie wszystkich bezpośrednich zasad podrzędnych<br />-dowolny-poczekaj na zakończenie wszelkich natychmiastowych zasad podrzędnych. Po zakończeniu `wait` pierwszej bezpośredniej zasad podrzędnej zasady zakończą działanie i wykonywanie wszelkich innych bezpośrednich zasad podrzędnych zostanie zakończone. | Nie       | Wszystko     |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących sekcjach i [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zakresach [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze
-   **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

-   [Zasady w API Management](api-management-howto-policies.md)
-   [Wyrażenia zasad](api-management-policy-expressions.md)
-   [Dokumentacja zasad](api-management-policy-reference.md) pełna lista instrukcji zasad i ich ustawień
-   [Przykłady zasad](policy-samples.md)
