---
title: Usługa Azure API Management zaawansowane zasady | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat zaawansowanych zasad dostępne do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 9d3bc50e1578704de029d53c0b1eaa21e74182cf
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401923"
---
# <a name="api-management-advanced-policies"></a>Usługa API Management zaawansowane zasady

Ten temat zawiera odwołania do następujących zasad usługi API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasad w usłudze API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AdvancedPolicies"></a> Zasady zaawansowane

-   [Przepływ sterowania](api-management-advanced-policies.md#choose) — warunkowo stosuje instrukcje zasad na podstawie wyników oceny atrybut typu wartość logiczna [wyrażenia](api-management-policy-expressions.md).
-   [Przekazanie żądania](#ForwardRequest) -przekazuje żądanie do usługi zaplecza.
-   [Limit współbieżności](#LimitConcurrency) — uniemożliwia ujęte zasad wykonania przez więcej niż określoną liczbę żądań w danym momencie.
-   [Dziennik do Centrum zdarzeń](#log-to-eventhub) — wysyła komunikaty w określonym formacie do Centrum zdarzeń zdefiniowanych przez podmiot rejestratora.
-   [Pozorowanie odpowiedzi](#mock-response) -przerwań potoku wykonywania i zwracał pozorowane odpowiedzi bezpośrednio do obiektu wywołującego.
-   [Ponów próbę wykonania](#Retry) -ponawia próbę wykonania instrukcji ujęty zasad, jeśli i do momentu spełnienia warunku. Wykonywanie będzie powtarzać w określonych odstępach czasu i do określonej liczba ponownych prób.
-   [Odpowiedź zwrócona](#ReturnResponse) -przerwań potoku wykonywania i zwraca określoną odpowiedź bezpośrednio do obiektu wywołującego.
-   [Wyślij żądanie jeden ze sposobów](#SendOneWayRequest) — wysyła żądanie do określonego adresu URL bez oczekiwania na odpowiedź.
-   [Wyślij żądanie](#SendRequest) — wysyła żądanie do określonego adresu URL.
-   [Ustaw serwer proxy HTTP](#SetHttpProxy) — pozwala na żądania trasy przekazywane za pośrednictwem serwera proxy HTTP.
-   [Ustawia metodę żądania](#SetRequestMethod) — umożliwia zmianę metody HTTP dla żądania.
-   [Ustawionego kodu stanu](#SetStatus) — zmienia kod stanu HTTP do określonej wartości.
-   [Ustaw zmienną](api-management-advanced-policies.md#set-variable) -będzie się powtarzał wartości w nazwanym [kontekstu](api-management-policy-expressions.md#ContextVariables) zmiennych na potrzeby późniejszego dostępu.
-   [Śledzenie](#Trace) — dodaje ciąg do [inspektora interfejsów API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) danych wyjściowych.
-   [Poczekaj](#Wait) — oczekuje ujęte [żądań wysłania](api-management-advanced-policies.md#SendRequest), [korzyści z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey), lub [przepływ sterowania](api-management-advanced-policies.md#choose) zasady, które należy wykonać przed kontynuowaniem.

## <a name="choose"></a> Przepływ sterowania

`choose` Zasady mają zastosowanie zasady ujęty instrukcje w oparciu o wyniki oceny wyrażenia logiczne, podobnie jak if-then-else lub przełącznika konstruowania w języku programowania.

### <a name="ChoosePolicyStatement"></a> Deklaracja zasad

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

Zasady przepływu sterowania musi zawierać co najmniej jeden `<when/>` elementu. `<otherwise/>` Element jest opcjonalne. Warunki w `<when/>` elementy są obliczane w kolejności ich występowania w ramach zasad. Zasady instrukcji ujęta w obrębie pierwszych `<when/>` elementu z równą atrybutu warunku `true` zostaną zastosowane. Zasady są ujęte w `<otherwise/>` elementu, jeśli jest obecny, zostaną zastosowane, jeśli wszystkie z `<when/>` atrybuty warunek elementu `false`.

### <a name="examples"></a>Przykłady

#### <a name="ChooseExample"></a> Przykład

W poniższym przykładzie pokazano [Ustaw zmienną](api-management-advanced-policies.md#set-variable) zasad i dwie zasady przepływu sterowania.

Ustawianie zasad zmiennej znajduje się w sekcji dla ruchu przychodzącego i tworzy `isMobile` logiczna [kontekstu](api-management-policy-expressions.md#ContextVariables) zmiennej, która jest ustawiona na wartość true, jeśli `User-Agent` żądania nagłówek zawiera tekst `iPad` lub `iPhone`.

Pierwszej zasady przepływu sterowania również znajduje się w sekcji dla ruchu przychodzącego, a następnie warunkowo ma zastosowanie jednej z dwóch [Ustaw parametr ciągu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) zasady w zależności od wartości `isMobile` zmiennej kontekstowej.

Drugie zasady kontroli w przepływie znajduje się w sekcji wychodzących i warunkowo stosuje [przekonwertować XML do formatu JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) zasad po `isMobile` jest ustawiona na `true`.

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

Ten przykład pokazuje, jak przeprowadzić filtrowanie zawartości przez usunięcie elementów danych z odpowiedzi otrzymanych z usługi zaplecza, korzystając z `Starter` produktu. Demonstracyjne, konfigurowania i korzystania z tych zasad, zobacz [Cloud Cover odcinek 177: Więcej funkcji zarządzania interfejsu API za pomocą Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przewijanie do przodu do 34:30. Rozpocznij od 31:50 zobaczyć Przegląd [ciemny Sky prognoz interfejsu API](https://developer.forecast.io/) używany dla tej wersji demonstracyjnej.

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
| Wybierz opcję    | Element główny.                                                                                                                                                                                                                                                             | Yes      |
| kiedy      | Warunek, którego chcesz użyć dla `if` lub `ifelse` części `choose` zasad. Jeśli `choose` zasad ma wiele `when` sekcje są obliczane po kolei. Gdy `condition` z o tym, kiedy element daje w wyniku `true`, żadnych dalszych `when` oceny warunków. | Yes      |
| W przeciwnym razie | Zawiera fragment zasad do użycia, jeśli żaden z `when` warunki zostaną obliczone na `true`.                                                                                                                                                                               | Nie       |

### <a name="attributes"></a>Atrybuty

| Atrybut                                              | Opis                                                                                               | Wymagane |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| warunek = "wyrażenie logiczne &#124; logiczna stała" | Wyrażenia logicznego lub stałą oceniane podczas zawierający `when` deklaracja zasad jest oceniany. | Yes      |

### <a name="ChooseUsage"></a> Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** zaplecza dla ruchu przychodzącego, wychodzący — błąd

-   **Zakresy zasad:** wszystkie zakresy

## <a name="ForwardRequest"></a> Przekazanie żądania

`forward-request` Zasady powodują przesyłanie żądań przychodzących do usługi zaplecza, określony w żądaniu [kontekstu](api-management-policy-expressions.md#ContextVariables). Adres URL usługi wewnętrznej bazy danych jest określony w interfejsie API [ustawienia](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) i mogą być zmieniane za pomocą [Ustaw usługę zaplecza](api-management-transformation-policies.md) zasad.

> [!NOTE]
> Usuwanie wyników tej zasady w żądaniu nie jest przekazywane do zaplecza usługi i zasad w sekcji ruchu wychodzącego są przetwarzane od razu po pomyślnym zakończeniu zasad w sekcji dla ruchu przychodzącego.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<forward-request timeout="time in seconds" follow-redirects="true | false" buffer-request-body="true | false" />
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

Następujące zasady poziom interfejsu API przekazuje wszystkie żądania interfejsu API do usługi zaplecza, który z interwałem limitu czasu równego 60 sekund.

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

Korzysta z tej zasady na poziomie operacji `base` element dziedziczenie zasad zaplecza z elementu nadrzędnego w zakresie poziomu interfejsu API.

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

Ta operacja poziomu zasad jawnie przekazuje wszystkie żądania do usługi zaplecza z limitem czasu równym 120 i nie dziedziczy obiektu nadrzędnego zasad zaplecza na poziomie interfejsu API.

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

Ta operacja poziomu zasad nie przekazuje żądania do usługi zaplecza.

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
| forward-request | Element główny. | Yes      |

### <a name="attributes"></a>Atrybuty

| Atrybut                               | Opis                                                                                                      | Wymagane | Domyślne     |
| --------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | -------- | ----------- |
| timeout="integer"                       | Interwał limitu czasu w sekundach przed wywołaniem do usługi zaplecza nie powiedzie się. Wartość minimalna wynosi 0 sekund. Maksymalne wartości jest 240 sekund.| Nie       | 240 sekund |
| follow-redirects="true &#124; false"    | Określa, czy przekierowania z usługi zaplecza są następuje bramy zwracane do obiektu wywołującego.      | Nie       | false       |
| buffer-request-body="true &#124; false" | Gdy ustawiona na "true" żądania są buforowane i zostanie ponownie użyty w [ponów](api-management-advanced-policies.md#Retry). | Nie       | false       |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** wewnętrznej bazy danych
-   **Zakresy zasad:** wszystkie zakresy

## <a name="LimitConcurrency"></a> Limit współbieżności

`limit-concurrency` Zasada uniemożliwia ujęty zasady wykonywania przez więcej niż określoną liczbę żądań w dowolnym momencie. Po przekroczeniu tej liczby, nowych żądań nie powiedzie się bezpośrednio z kodem stanu usługi 429 zbyt wiele żądań.

### <a name="LimitConcurrencyStatement"></a> Deklaracja zasad

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

Poniższy przykład pokazuje, jak ograniczyć liczbę żądań, przekazywane do zaplecza, na podstawie wartości zmiennej kontekstowej.

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
| limit-concurrency | Element główny. | Yes      |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                                                        | Wymagane | Domyślne |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | Ciąg. Wyrażenie jest dozwolony. Określa zakres współbieżności. Mogą być współużytkowane przez wiele zasad. | Yes      | ND     |
| max-count | Liczba całkowita. Określa maksymalną liczbę żądań, które mogą wprowadzać zasady.           | Yes      | ND     |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** zaplecza dla ruchu przychodzącego, wychodzący — błąd

-   **Zakresy zasad:** wszystkie zakresy

## <a name="log-to-eventhub"></a> Dziennik do Centrum zdarzeń

`log-to-eventhub` Zasad wysyła komunikaty w określonym formacie do Centrum zdarzeń zdefiniowanych przez podmiot rejestratora. Jak sugeruje jej nazwa, zasady są używane do zapisywania wybrane informacje kontekstu żądania lub odpowiedzi do analizy w trybie online lub offline.

> [!NOTE]
> Aby uzyskać przewodnik krok po kroku dotyczące konfigurowania Centrum zdarzeń i rejestrowania zdarzeń, zobacz [jak mają być rejestrowane zdarzenia usługi API Management z usługą Azure Event Hubs](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Przykład

Dowolny ciąg może służyć jako wartość ma zostać zarejestrowany w usłudze Event Hubs. W tym przykładzie daty i godziny, nazwa usługi wdrożenia, identyfikator żądania, adres ip oraz nazwy operacji dla wszystkich połączeń przychodzących, które są rejestrowane w Centrum zdarzeń rejestratora zarejestrowane w usłudze `contoso-logger` identyfikator.

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
| dziennik do Centrum zdarzeń | Element główny. Wartość tego elementu jest ciąg do logowania się do Centrum zdarzeń. | Yes      |

### <a name="attributes"></a>Atrybuty

| Atrybut     | Opis                                                               | Wymagane                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| logger-id     | Identyfikator rejestratora rejestrowania przy użyciu usługi API Management.         | Yes                                                                  |
| partition-id  | Określa indeks partycji, w której są wysyłane wiadomości.             | Opcjonalny. Ten atrybut nie mogą być używane, jeśli `partition-key` jest używany. |
| partition-key | Określa wartość do przypisania partycji podczas wysyłania wiadomości. | Opcjonalny. Ten atrybut nie mogą być używane, jeśli `partition-id` jest używany.  |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** zaplecza dla ruchu przychodzącego, wychodzący — błąd

-   **Zakresy zasad:** wszystkie zakresy

## <a name="mock-response"></a> Pozorowanie odpowiedzi

`mock-response`, Jak nazwa wskazuje, że jest używana do testowanie interfejsów API i operacji. On przerywa wykonywanie potoku normalne i zwracał pozorowane odpowiedzi do obiektu wywołującego. Zasady zawsze próbuje zwracania odpowiedzi najbardziej wiarygodnym. Preferuje go przykłady zawartości odpowiedzi, zawsze, gdy jest to dostępne. Generuje on przykładowej odpowiedzi ze schematów, gdy znajdują się schematów i przykłady nie. Jeśli znajdują się przykłady ani schematów, zwracane są odpowiedzi bez zawartości.

### <a name="policy-statement"></a>Deklaracja zasad

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
| mock-response | Element główny. | Yes      |

### <a name="attributes"></a>Atrybuty

| Atrybut    | Opis                                                                                           | Wymagane | Domyślne |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| Kod stanu:  | Określa kod stanu odpowiedzi i jest używany do wybierania odpowiednich przykład lub schematu.                 | Nie       | 200     |
| Typ zawartości | Określa `Content-Type` wartości nagłówka odpowiedzi i jest używany do wybierania odpowiednich przykład lub schematu. | Nie       | Brak    |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego, ruchu wychodzącego, — błąd

-   **Zakresy zasad:** wszystkie zakresy

## <a name="Retry"></a> Spróbuj ponownie

`retry` Zasad jego zasady podrzędne jest wykonywana raz i następnie ponawia próbę ich wykonania do momentu ponownych prób `condition` staje się `false` lub ponów próbę wykonania `count` jest wyczerpana.

### <a name="policy-statement"></a>Deklaracja zasad

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

W poniższym przykładzie przekazywania żądanie zostanie ponowiony maksymalnie dziesięć razy przy użyciu algorytmu ponawiania wykładniczego. Ponieważ `first-fast-retry` jest ustawiona na wartość FAŁSZ, przeprowadzenia wszystkich ponownych prób podlegają algorytm ponawiania wykładniczego.

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
| retry   | Element główny. Może zawierać innych zasad jako jego elementy podrzędne. | Yes      |

### <a name="attributes"></a>Atrybuty

| Atrybut        | Opis                                                                                                                                           | Wymagane | Domyślne |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| warunek        | Literału wartości logicznej lub [wyrażenie](api-management-policy-expressions.md) Określanie ponownych prób powinna zostać zatrzymana (`false`) lub ciąg dalszy (`true`).      | Yes      | ND     |
| count            | Liczba dodatnia określająca maksymalną liczbę ponowień próby.                                                                                | Yes      | ND     |
| interval         | Wartość dodatnią w ciągu kilku sekund określania interwału oczekiwania między ponowienie próby.                                                                 | Yes      | ND     |
| max-interval     | W ciągu kilku sekund określający maksymalną liczbę dodatnią poczekaj interwał między ponownymi próbami. Służy do implementowania algorytm ponawiania wykładniczego. | Nie       | ND     |
| delta            | Liczba dodatnia, w ciągu kilku sekund, określając przyrost interwał oczekiwania. Służy do implementowania algorytmy ponawiania liniowego i wykładniczą.             | Nie       | ND     |
| pierwsze szybkie ponownej próby | Jeśli ustawiono `true` , pierwszym ponowieniem próby odbywa się natychmiast.                                                                                  | Nie       | `false` |

> [!NOTE]
> Gdy tylko `interval` jest określony, **stałej** Interwał ponownych prób są wykonywane.
> Gdy tylko `interval` i `delta` są określone, **liniowej** interwał ponawiania prób algorytm jest używany, gdy czas oczekiwania między ponownymi próbami jest obliczany zgodnie z następującą formułę - `interval + (count - 1)*delta`.
> Gdy `interval`, `max-interval` i `delta` są określone, **wykładniczego** interwał ponawiania prób algorytm ma być stosowany, gdy czas oczekiwania między ponownymi próbami rośnie wykładniczo od wartości `interval` do wartość `max-interval` zgodnie z następującą formułę - `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Należy pamiętać, że ograniczenia użycia zasady podrzędne będą dziedziczone przez te zasady.

-   **Sekcje zasad:** zaplecza dla ruchu przychodzącego, wychodzący — błąd

-   **Zakresy zasad:** wszystkie zakresy

## <a name="ReturnResponse"></a> Zwraca odpowiedź

`return-response` Zasad przerywa wykonywanie potoku i zwraca domyślne lub niestandardowe odpowiedzi do obiektu wywołującego. Odpowiedź domyślną jest `200 OK` z bez treści. Niestandardowe odpowiedzi można określić za pomocą instrukcji zmiennej lub zasad kontekstu. Podano zarówno odpowiedzi zawartych w zmiennej kontekstowej jest modyfikowany przez instrukcje zasad przed zwróceniem do obiektu wywołującego.

### <a name="policy-statement"></a>Deklaracja zasad

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
| Return odpowiedzi | Element główny.                                                                             | Yes      |
| set-header      | A [set-header](api-management-transformation-policies.md#SetHTTPheader) deklaracji zasad. | Nie       |
| Ustaw treść        | A [Ustaw treść](api-management-transformation-policies.md#SetBody) deklaracji zasad.         | Nie       |
| set-status      | A [Ustaw stan](api-management-advanced-policies.md#SetStatus) deklaracji zasad.           | Nie       |

### <a name="attributes"></a>Atrybuty

| Atrybut              | Opis                                                                                                                                                                          | Wymagane  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| response-variable-name | Nazwa zmiennej kontekstu odwołania, na przykład nadrzędnej [żądań wysłania](api-management-advanced-policies.md#SendRequest) zasad i zawierający `Response` obiektu | Opcjonalny. |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** zaplecza dla ruchu przychodzącego, wychodzący — błąd

-   **Zakresy zasad:** wszystkie zakresy

## <a name="SendOneWayRequest"></a> Wysłanie żądania jednokierunkowego

`send-one-way-request` Zasad spowoduje wysłanie wniosku podana pod określony adres URL bez oczekiwania na odpowiedź.

### <a name="policy-statement"></a>Deklaracja zasad

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

Ta zasada przykładowych pokazano przykład za pomocą `send-one-way-request` zasady, aby wysłać wiadomość Slack pokoju rozmów, jeśli kod odpowiedzi HTTP jest większa niż lub równy 500. Aby uzyskać więcej informacji na temat tego przykładu, zobacz [korzystanie z usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
| send-one-way-request       | Element główny.                                                                                               | Yes                             |
| url                        | Adres URL żądania.                                                                                     | Jeśli nie tryb = kopiowania; w przeciwnym razie jest to tak. |
| method                     | Metoda HTTP dla żądania.                                                                            | Jeśli nie tryb = kopiowania; w przeciwnym razie jest to tak. |
| nagłówek                     | Nagłówek żądania. Wiele elementów nagłówka na użytek wielu nagłówki żądania.                                  | Nie                              |
| treść                       | Treść żądania.                                                                                           | Nie                              |
| authentication-certificate | [Certyfikat do użycia na potrzeby uwierzytelniania klienta](api-management-authentication-policies.md#ClientCertificate) | Nie                              |

### <a name="attributes"></a>Atrybuty

| Atrybut     | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string" | Określa, czy jest to nowe wezwanie lub kopię bieżącego żądania. W trybie ruchu wychodzącego tryb = kopiowania nie inicjuje treści żądania.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nie       | Nowa      |
| name          | Określa nazwę nagłówka do ustawienia.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Yes      | ND      |
| istnieje akcja | Określa, jakie działania podejmowane w momencie nagłówek jest już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> -override - zastępuje wartość istniejący nagłówek.<br />-skip — nie zastępuje istniejącą wartość nagłówka.<br />-dołączania - dołącza wartość do istniejącej wartości nagłówka.<br />-delete - Usuwa nagłówek z żądania.<br /><br /> Po ustawieniu `override` rejestrowanie wiele wpisów z tej samej nazwie wyników w nagłówku ustawiania zgodnie ze wszystkich wpisów, (które zostaną wyświetlone wiele razy); tylko wymienioną wartość zostanie ustawiona w wyniku. | Nie       | zastąpienie |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** zaplecza dla ruchu przychodzącego, wychodzący — błąd

-   **Zakresy zasad:** wszystkie zakresy

## <a name="SendRequest"></a> Wyślij żądanie

`send-request` Zasad spowoduje wysłanie wniosku podana pod określony adres URL nie dłużej niż ustaw wartość limitu czasu oczekiwania.

### <a name="policy-statement"></a>Deklaracja zasad

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

Ten przykład pokazuje, jak można zweryfikować odwołania do tokenu za pomocą serwera autoryzacji. Aby uzyskać więcej informacji na temat tego przykładu, zobacz [korzystanie z usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
| send-request               | Element główny.                                                                                               | Yes                             |
| url                        | Adres URL żądania.                                                                                     | Jeśli nie tryb = kopiowania; w przeciwnym razie jest to tak. |
| method                     | Metoda HTTP dla żądania.                                                                            | Jeśli nie tryb = kopiowania; w przeciwnym razie jest to tak. |
| nagłówek                     | Nagłówek żądania. Wiele elementów nagłówka na użytek wielu nagłówki żądania.                                  | Nie                              |
| treść                       | Treść żądania.                                                                                           | Nie                              |
| authentication-certificate | [Certyfikat do użycia na potrzeby uwierzytelniania klienta](api-management-authentication-policies.md#ClientCertificate) | Nie                              |

### <a name="attributes"></a>Atrybuty

| Atrybut                       | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Wymagane | Domyślne  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string"                   | Określa, czy jest to nowe wezwanie lub kopię bieżącego żądania. W trybie ruchu wychodzącego tryb = kopiowania nie inicjuje treści żądania.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nie       | Nowa      |
| response-variable-name="string" | Nazwa zmiennej kontekstowej, który będzie otrzymywać obiekt odpowiedzi. Jeśli zmienna nie istnieje, zostanie utworzony po pomyślnym wykonaniu zasad i staną się dostępne za pośrednictwem [ `context.Variable` ](api-management-policy-expressions.md#ContextVariables) kolekcji.                                                                                                                                                                                                                                                                                                                          | Yes      | ND      |
| timeout="integer"               | Interwał limitu czasu w sekundach przed wywołaniem do adresu URL zakończy się niepowodzeniem.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Nie       | 60       |
| ignore-error                    | Jeśli wartość PRAWDA, a żądanie będzie skutkowało błędem:<br /><br /> — Jeśli odpowiedź zmienna nazwa została określona, będzie zawierać wartości null.<br />— Jeśli nie określono odpowiedzi zmienna nazwy, kontekstu. Żądanie nie zostanie zaktualizowany.                                                                                                                                                                                                                                                                                                                                                                                   | Nie       | false    |
| name                            | Określa nazwę nagłówka do ustawienia.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Yes      | ND      |
| istnieje akcja                   | Określa, jakie działania podejmowane w momencie nagłówek jest już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> -override - zastępuje wartość istniejący nagłówek.<br />-skip — nie zastępuje istniejącą wartość nagłówka.<br />-dołączania - dołącza wartość do istniejącej wartości nagłówka.<br />-delete - Usuwa nagłówek z żądania.<br /><br /> Po ustawieniu `override` rejestrowanie wiele wpisów z tej samej nazwie wyników w nagłówku ustawiania zgodnie ze wszystkich wpisów, (które zostaną wyświetlone wiele razy); tylko wymienioną wartość zostanie ustawiona w wyniku. | Nie       | zastąpienie |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** zaplecza dla ruchu przychodzącego, wychodzący — błąd

-   **Zakresy zasad:** wszystkie zakresy

## <a name="SetHttpProxy"></a> Serwer proxy HTTP zestawu

`proxy` Zasad umożliwia kierowanie żądań przekazywane do zaplecza za pośrednictwem serwera proxy HTTP. Tylko HTTP (a nie HTTPS) jest obsługiwana między bramą a serwerem proxy. Podstawowe i tylko uwierzytelnianie NTLM.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Przykład

Zwróć uwagę na użycie [właściwości](api-management-howto-properties.md) jako wartości nazwy użytkownika i hasło, aby uniknąć przechowywania poufnych informacji w dokumencie zasady.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elementy

| Element | Opis  | Wymagane |
| ------- | ------------ | -------- |
| Serwer proxy   | Element główny | Yes      |

### <a name="attributes"></a>Atrybuty

| Atrybut         | Opis                                            | Wymagane | Domyślne |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="string"      | Adres URL serwera proxy w postaci http://host:port.             | Yes      | ND     |
| username="string" | Nazwa użytkownika ma być używany do uwierzytelniania przy użyciu serwera proxy. | Nie       | ND     |
| password="string" | Hasło używane do uwierzytelniania przy użyciu serwera proxy. | Nie       | ND     |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego

-   **Zakresy zasad:** wszystkie zakresy

## <a name="SetRequestMethod"></a> Set, Metoda żądania

`set-method` Zasad umożliwia zmianę metody żądania HTTP dla żądania.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Przykład

Te zasady przykładowy, który używa `set-method` zasad pokazano przykład wysyłania komunikatu do pokoju rozmów Slack, jeśli kod odpowiedzi HTTP jest większa niż lub równy 500. Aby uzyskać więcej informacji na temat tego przykładu, zobacz [korzystanie z usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
| set-method | Element główny. Wartość elementu określa metodę HTTP. | Yes      |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego, na błąd

-   **Zakresy zasad:** wszystkie zakresy

## <a name="SetStatus"></a> Kod stanu zestawu

`set-status` Zasad ustawia kod stanu HTTP do określonej wartości.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Przykład

Ten przykład przedstawia sposób zwracania odpowiedzi 401, jeśli token autoryzacji jest nieprawidłowa. Aby uzyskać więcej informacji, zobacz [korzystanie z usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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
| set-status | Element główny. | Yes      |

### <a name="attributes"></a>Atrybuty

| Atrybut       | Opis                                                | Wymagane | Domyślne |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code="integer"  | Kod stanu HTTP do zwrócenia.                            | Yes      | ND     |
| reason="string" | Opis powodu zwróciło kod stanu. | Yes      | ND     |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** zaplecze wychodzących, — błąd
-   **Zakresy zasad:** wszystkie zakresy

## <a name="set-variable"></a> Ustaw zmienną

`set-variable` Deklaruje zasad [kontekstu](api-management-policy-expressions.md#ContextVariables) zmiennej i przypisuje mu wartość określoną przez [wyrażenie](api-management-policy-expressions.md) lub literał ciągu znaków. Jeśli wyrażenie zawiera właściwość literal zostanie przekonwertowany na ciąg, a typ wartości będzie `System.String`.

### <a name="set-variablePolicyStatement"></a> Deklaracja zasad

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="set-variableExample"></a> Przykład

W poniższym przykładzie pokazano zestaw zasad zmiennej w sekcji dla ruchu przychodzącego. Ta zasada zmiennej zestaw tworzy `isMobile` logiczna [kontekstu](api-management-policy-expressions.md#ContextVariables) zmiennej, która jest ustawiona na wartość true, jeśli `User-Agent` żądania nagłówek zawiera tekst `iPad` lub `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elementy

| Element      | Opis   | Wymagane |
| ------------ | ------------- | -------- |
| Ustaw zmienną | Element główny. | Yes      |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                              | Wymagane |
| --------- | ------------------------------------------------------------------------ | -------- |
| name      | Nazwa zmiennej.                                                | Yes      |
| wartość     | Wartość zmiennej. Może to być wyrażenie lub wartość literału. | Yes      |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** zaplecza dla ruchu przychodzącego, wychodzący — błąd
-   **Zakresy zasad:** wszystkie zakresy

### <a name="set-variableAllowedTypes"></a> Dozwolone typy

Wyrażenia używane w `set-variable` zasad musi zwracać jedną z następujących typów podstawowych.

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

## <a name="Trace"></a> Śledzenia

`trace` Zasad dodaje ciąg do [inspektora interfejsów API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) danych wyjściowych. Zasady będą wykonywane tylko kiedy śledzenia jest wyzwalany, czyli `Ocp-Apim-Trace` nagłówek żądania jest obecna i ustawiona na `true` i `Ocp-Apim-Subscription-Key` nagłówek żądania jest obecny i ma prawidłowy klucz skojarzony z kontem administratora.

### <a name="policy-statement"></a>Deklaracja zasad

```xml

<trace source="arbitrary string literal">
    <!-- string expression or literal -->
</trace>

```

### <a name="elements"></a>Elementy

| Element | Opis   | Wymagane |
| ------- | ------------- | -------- |
| Śledzenia   | Element główny. | Yes      |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                                             | Wymagane | Domyślne |
| --------- | --------------------------------------------------------------------------------------- | -------- | ------- |
| source    | Literał ciągu jest zrozumiały dla przeglądarki danych śledzenia i Określanie źródła wiadomości. | Yes      | ND     |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

-   **Sekcje zasad:** zaplecza dla ruchu przychodzącego, wychodzący — błąd

-   **Zakresy zasad:** wszystkie zakresy

## <a name="Wait"></a> Czekaj

`wait` Zasad jego zasady bezpośrednie podrzędne jest wykonywane równolegle i czeka na wszystkich lub jednego z jego zasady bezpośrednie podrzędne, aby ukończyć przed ukończeniem. Zasady oczekiwania może mieć jako jego zasady bezpośrednie podrzędne [żądań wysłania](api-management-advanced-policies.md#SendRequest), [korzyści z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey), i [przepływ sterowania](api-management-advanced-policies.md#choose) zasad.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Przykład

W poniższym przykładzie występują dwa `choose` zasady zgodnie z zasadami bezpośrednie podrzędne `wait` zasad. Każda z tych `choose` zasad jest wykonywane równolegle. Każdy `choose` zasad próbuje pobrać wartość w pamięci podręcznej. W przypadku trafienia pamięci podręcznej, usługi zaplecza jest wywoływana, aby podać wartość. W tym przykładzie `wait` zasada nie zostanie ukończone do czasu ukończenia wszystkich swoich zasad bezpośrednie podrzędne, ponieważ `for` ma ustawioną wartość atrybutu `all`. W tym przykładzie zmienne kontekstowe (`execute-branch-one`, `value-one`, `execute-branch-two`, i `value-two`) są zadeklarowane poza zakresem tego przykład zasad.

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
| Czekaj    | Element główny. Może zawierać jako elementy podrzędne tylko `send-request`, `cache-lookup-value`, i `choose` zasad. | Yes      |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                                                                                                                                                                                                                                                                                                                                                                            | Wymagane | Domyślne |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| for       | Określa, czy `wait` zasad oczekiwania dla wszystkich zasad bezpośrednie podrzędne na ukończone lub po prostu jednego. Dozwolone wartości to:<br /><br /> - `all` -Poczekaj, aż wszystkie zasady bezpośrednie podrzędne w celu ukończenia<br />-wszelkie - poczekaj, aż wszystkie zasady bezpośrednie podrzędne zakończyć. Po zakończeniu pierwszej zasady bezpośrednie podrzędne `wait` kończy zasad i wykonywania innych zasad bezpośrednie podrzędne zostanie zakończony. | Nie       | all     |

### <a name="usage"></a>Sposób użycia

Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego, ruchu wychodzącego, wewnętrznej bazy danych
-   **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, w pracy z tymi zasadami zobacz:

-   [Zasady usługi API Management](api-management-howto-policies.md)
-   [Wyrażenia zasad](api-management-policy-expressions.md)
-   [Informacje o zasadach](api-management-policy-reference.md) pełną listę zasad i ich ustawienia
-   [Przykłady zasad](policy-samples.md)
