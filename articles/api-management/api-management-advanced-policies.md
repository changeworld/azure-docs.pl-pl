---
title: Zaawansowane zasady usługi Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się więcej o zaawansowanych zasadach dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: c8ef481fe277d6451923da828f0e7473354c24cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266171"
---
# <a name="api-management-advanced-policies"></a>Zaawansowane zasady usługi API Management

Ten temat zawiera odwołanie do następujących zasad zarządzania interfejsami API. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [Zasady w zarządzania interfejsami API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="advanced-policies"></a><a name="AdvancedPolicies"></a>Zaawansowane zasady

-   [Przepływ sterowania](api-management-advanced-policies.md#choose) — warunkowo stosuje instrukcje zasad na podstawie wyników oceny [wyrażeń logicznych](api-management-policy-expressions.md).
-   [Żądanie przesyłania dalej](#ForwardRequest) — przekazuje żądanie do usługi wewnętrznej bazy danych.
-   [Ogranicz współbieżność](#LimitConcurrency) — zapobiega wykonywanie zamkniętych zasad przez więcej niż określoną liczbę żądań naraz.
-   [Zaloguj się do Centrum zdarzeń](#log-to-eventhub) — wysyła wiadomości w określonym formacie do Centrum zdarzeń zdefiniowanego przez jednostkę Rejestratora.
-   [Mock odpowiedzi](#mock-response) — przerywa wykonanie potoku i zwraca mocked odpowiedzi bezpośrednio do wywołującego.
-   [Ponów próbę](#Retry) — ponawia ponawia wykonywanie instrukcji zasad, jeśli i dopóki warunek nie zostanie spełniony. Wykonanie zostanie powtórzone w określonych odstępach czasu i do określonej liczby ponownych prób.
-   [Odpowiedź zwracana](#ReturnResponse) — przerywa wykonanie potoku i zwraca określoną odpowiedź bezpośrednio do wywołującego.
-   [Wyślij żądanie w jedną stronę](#SendOneWayRequest) — wysyła żądanie do określonego adresu URL bez oczekiwania na odpowiedź.
-   [Wyślij żądanie](#SendRequest) — wysyła żądanie do określonego adresu URL.
-   [Ustaw serwer proxy HTTP](#SetHttpProxy) — umożliwia kierowanie żądań przesyłanych dalej za pośrednictwem serwera proxy HTTP.
-   [Ustaw metodę żądania](#SetRequestMethod) — umożliwia zmianę metody HTTP dla żądania.
-   [Ustaw kod stanu](#SetStatus) — zmienia kod stanu HTTP na określoną wartość.
-   [Ustaw zmienną](api-management-advanced-policies.md#set-variable) — utrzymuje wartość w nazwanej zmiennej [kontekstowej](api-management-policy-expressions.md#ContextVariables) dla późniejszego dostępu.
-   [Śledzenie](#Trace) — dodaje niestandardowe ślady do danych wyjściowych [inspektora interfejsu API,](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) telemetrie usługi Application Insights i dzienników diagnostycznych.
-   [Czekaj](#Wait) — przed kontynuowaniem trwa oczekiwanie na zamknięte [żądanie wysłania](api-management-advanced-policies.md#SendRequest), [Pobierz wartość z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey)lub [Zasady przepływu sterowania](api-management-advanced-policies.md#choose) do ukończenia.

## <a name="control-flow"></a><a name="choose"></a>Przepływ sterowania

Zasady `choose` stosuje załączone instrukcje zasad na podstawie wyników oceny wyrażeń logicznych, podobne do if-then-else lub konstrukcji przełącznika w języku programowania.

### <a name="policy-statement"></a><a name="ChoosePolicyStatement"></a>Oświadczenie dotyczące zasad

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

Zasady przepływu sterowania musi zawierać `<when/>` co najmniej jeden element. Element `<otherwise/>` jest opcjonalny. Warunki `<when/>` w elementach są oceniane w kolejności ich wygląd w ramach zasad. Zostaną zastosowane instrukcje zasad ujęte w pierwszym `<when/>` `true` elemencie z atrybutem conditione equals. Zasady zawarte w `<otherwise/>` elemencie, jeśli są obecne, `<when/>` zostaną zastosowane, jeśli wszystkie atrybuty warunku elementu są `false`.

### <a name="examples"></a>Przykłady

#### <a name="example"></a><a name="ChooseExample"></a>Przykład

W poniższym przykładzie przedstawiono zasady [zestawu zmiennych](api-management-advanced-policies.md#set-variable) i dwie zasady przepływu sterowania.

Ustawiona zasada zmiennej znajduje się w `isMobile` sekcji przychodzącej i tworzy `User-Agent` zmienną [kontekstową](api-management-policy-expressions.md#ContextVariables) logiczną ustawioną na true, jeśli nagłówek żądania zawiera tekst `iPad` lub `iPhone`.

Pierwsza zasada przepływu sterowania znajduje się również w sekcji przychodzącej i warunkowo stosuje jedną z `isMobile` dwóch zasad [parametrów set query string](api-management-transformation-policies.md#SetQueryStringParameter) w zależności od wartości zmiennej kontekstowej.

Druga zasada przepływu sterowania znajduje się w sekcji wychodzącej i warunkowo stosuje `isMobile` zasady `true` [Konwertuj XML na JSON,](api-management-transformation-policies.md#ConvertXMLtoJSON) gdy jest ustawiona na .

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

W tym przykładzie pokazano, jak wykonać filtrowanie zawartości, usuwając elementy danych `Starter` z odpowiedzi otrzymanej z usługi wewnętrznej bazy danych podczas korzystania z produktu. Aby zademonstrować konfigurowanie i używanie tych zasad, zobacz [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) and fast-forward to 34:30. Rozpocznij o 31:50, aby zobaczyć przegląd [interfejsu API prognozy ciemnego nieba,](https://developer.forecast.io/) który został użyty w tym pokazie.

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

| Element   | Opis                                                                                                                                                                                                                                                               | Wymagany |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Wybierz    | Element główny.                                                                                                                                                                                                                                                             | Tak      |
| Kiedy      | Warunek do użycia `if` dla `ifelse` lub `choose` części zasad. Jeśli `choose` zasady mają `when` wiele sekcji, są one oceniane sekwencyjnie. Po `condition` gdy element ocenia do `true`, `when` nie dalsze warunki są oceniane. | Tak      |
| Inaczej | Zawiera fragment kodu zasad, który ma być `when` używany, `true`jeśli żaden z warunków nie ma oceny .                                                                                                                                                                               | Nie       |

### <a name="attributes"></a>Atrybuty

| Atrybut                                              | Opis                                                                                               | Wymagany |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| condition="Wyrażenie logiczne &#124; stała logiczna" | Wyrażenie logiczne lub stała do oceny `when` podczas oceny zawierającej instrukcji zasad. | Tak      |

### <a name="usage"></a><a name="ChooseUsage"></a>Użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, wewnętrznej bazy danych, on-error

-   **Zakresy zasad:** wszystkie zakresy

## <a name="forward-request"></a><a name="ForwardRequest"></a>Żądanie przesyłania dalej

Zasada `forward-request` przekazuje żądanie przychodzące do usługi wewnętrznej bazy danych określonej w [kontekście](api-management-policy-expressions.md#ContextVariables)żądania . Adres URL usługi wewnętrznej bazy danych jest określony w [ustawieniach](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) interfejsu API i można go zmienić za pomocą zasad [usługi wewnętrznej bazy danych.](api-management-transformation-policies.md)

> [!NOTE]
> Usunięcie tej zasady powoduje, że żądanie nie jest przekazywane do usługi wewnętrznej bazy danych, a zasady w sekcji ruchu wychodzącego są oceniane natychmiast po pomyślnym zakończeniu zasad w sekcji przychodzącej.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" fail-on-error-status-code="false | true"/>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

Następujące zasady na poziomie interfejsu API przekazuje do przodu wszystkie żądania interfejsu API do usługi wewnętrznej bazy danych z interwałem limitu czasu 60 sekund.

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

Ta zasada poziomu operacji `base` używa tego elementu do dziedziczenia zasad wewnętrznej bazy danych z zakresu poziomu nadrzędnego interfejsu API.

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

Ta zasada poziomu operacji jawnie przekazuje wszystkie żądania do usługi wewnętrznej bazy danych z limitem czasu 120 i nie dziedziczy nadrzędnej zasady wewnętrznej bazy danych interfejsu API. Jeśli usługa wewnętrznej bazy danych odpowiada kodem stanu błędu od 400 do 599 włącznie, zostanie wyzwolona sekcja [on-error.](api-management-error-handling-policies.md)

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120" fail-on-error-status-code="true" />
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Przykład

Ta zasada poziomu operacji nie przekazuje żądań do usługi wewnętrznej bazy danych.

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

| Element         | Opis   | Wymagany |
| --------------- | ------------- | -------- |
| żądanie przesyłania dalej | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut                                     | Opis                                                                                                                                                                                                                                                                                                    | Wymagany | Domyślne |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| limit czasu="całkowita"                             | Czas w sekundach oczekiwania na zwracanie nagłówków odpowiedzi HTTP przez usługę wewnętrznej bazy danych przed podniesieniu błędu limitu czasu. Minimalna wartość to 0 sekund. Wartości większe niż 240 sekund mogą nie być honorowane, ponieważ podstawowa infrastruktura sieciowa może po tym czasie upuścić bezczynne połączenia. | Nie       | Brak    |
| follow-redirects="false &#124; true"          | Określa, czy przekierowania z usługi wewnętrznej bazy danych są następują po bramie lub zwracane do wywołującego.                                                                                                                                                                                                    | Nie       | false   |
| buffer-request-body="false &#124; true"       | Gdy ustawiono żądanie "true", jest buforowane i będzie ponownie na [ponowną próbę](api-management-advanced-policies.md#Retry).                                                                                                                                                                                               | Nie       | false   |
| fail-on-error-status-code="false &#124; true" | Po ustawieniu true wyzwalaczy [na sekcji błędu](api-management-error-handling-policies.md) dla kodów odpowiedzi w zakresie od 400 do 599 włącznie.                                                                                                                                                                      | Nie       | false   |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** zaplecze
-   **Zakresy zasad:** wszystkie zakresy

## <a name="limit-concurrency"></a><a name="LimitConcurrency"></a>Ogranicz współbieżność

Zasady `limit-concurrency` uniemożliwiają wykonywanie zamkniętych zasad przez więcej niż określoną liczbę żądań w dowolnym momencie. Po przekroczeniu tej liczby nowe żądania nie powiedzie się natychmiast z 429 Kod stanu zbyt wiele żądań.

### <a name="policy-statement"></a><a name="LimitConcurrencyStatement"></a>Oświadczenie dotyczące zasad

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

W poniższym przykładzie pokazano, jak ograniczyć liczbę żądań przekazanych do wewnętrznej bazy danych na podstawie wartości zmiennej kontekstowej.

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

| Element           | Opis   | Wymagany |
| ----------------- | ------------- | -------- |
| limit współbieżności | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                                                        | Wymagany | Domyślne |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | Ciąg. Wyrażenie dozwolone. Określa zakres współbieżności. Może być współużytkowana przez wiele zasad. | Tak      | Nie dotyczy     |
| maksymalna liczba | Liczba całkowita. Określa maksymalną liczbę żądań, które mogą wprowadzać zasady.           | Tak      | Nie dotyczy     |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, wewnętrznej bazy danych, on-error

-   **Zakresy zasad:** wszystkie zakresy

## <a name="log-to-event-hub"></a><a name="log-to-eventhub"></a>Zaloguj się do Centrum zdarzeń

Zasady `log-to-eventhub` wysyła wiadomości w określonym formacie do Centrum zdarzeń zdefiniowane przez logger jednostki. Jak sama nazwa wskazuje, zasady są używane do zapisywania wybranych informacji kontekstowych żądania lub odpowiedzi do analizy online lub offline.

> [!NOTE]
> Aby uzyskać przewodnik krok po kroku dotyczący konfigurowania centrum zdarzeń i rejestrowania zdarzeń, zobacz [Jak rejestrować zdarzenia zarządzania interfejsami API za pomocą usługi Azure Event Hubs](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Przykład

Każdy ciąg może służyć jako wartość, która ma być rejestrowana w Centrach zdarzeń. W tym przykładzie data i godzina, nazwa usługi wdrażania, identyfikator żądania, adres IP i nazwa operacji dla `contoso-logger` wszystkich połączeń przychodzących są rejestrowane w rejestratorze centrum zdarzeń zarejestrowanym przy użyciu identyfikatora

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

| Element         | Opis                                                                     | Wymagany |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| log-to-eventhub | Element główny. Wartość tego elementu jest ciąg do logowania do centrum zdarzeń. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut     | Opis                                                               | Wymagany                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| logger-id     | Identyfikator rejestratora zarejestrowanego w usłudze api Management.         | Tak                                                                  |
| identyfikator partycji  | Określa indeks partycji, na której są wysyłane wiadomości.             | Element opcjonalny. Ten atrybut nie może `partition-key` być używany, jeśli jest używany. |
| klucz partycji | Określa wartość używaną do przypisywania partycji podczas wysyłania wiadomości. | Element opcjonalny. Ten atrybut nie może `partition-id` być używany, jeśli jest używany.  |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, wewnętrznej bazy danych, on-error

-   **Zakresy zasad:** wszystkie zakresy

## <a name="mock-response"></a><a name="mock-response"></a>Pozorowane odpowiedzi

, `mock-response`jak sama nazwa wskazuje, jest używany do mock api i operacji. Przerywa normalne wykonanie potoku i zwraca szyderczą odpowiedź do wywołującego. Zasady zawsze stara się zwrócić odpowiedzi najwyższej wierności. Preferuje przykłady zawartości odpowiedzi, gdy są dostępne. Generuje przykładowe odpowiedzi ze schematów, gdy schematy są dostarczane i przykłady nie są. Jeśli nie zostaną znalezione żadne przykłady lub schematy, zwracane są odpowiedzi bez zawartości.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

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

| Element       | Opis   | Wymagany |
| ------------- | ------------- | -------- |
| makieta odpowiedzi | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut    | Opis                                                                                           | Wymagany | Domyślne |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| kod stanu  | Określa kod stanu odpowiedzi i służy do wybierania odpowiedniego przykładu lub schematu.                 | Nie       | 200     |
| typ zawartości | Określa `Content-Type` wartość nagłówka odpowiedzi i służy do wybierania odpowiedniego przykładu lub schematu. | Nie       | Brak    |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, błąd

-   **Zakresy zasad:** wszystkie zakresy

## <a name="retry"></a><a name="Retry"></a>Ponów próbę

Zasady `retry` wykonuje swoje zasady podrzędne raz, a następnie ponowi ich wykonanie, dopóki ponowna próba `condition` staje się `false` lub ponowić próbę `count` zostanie wyczerpany.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

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

W poniższym przykładzie przekazywanie żądań jest ponawiane do dziesięciu razy przy użyciu wykładniczego algorytmu ponawiania. Ponieważ `first-fast-retry` jest ustawiona na false, wszystkie próby ponawiania są przedmiotem wykładniczego algorytmu ponawiania.

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

| Element | Opis                                                         | Wymagany |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | Element główny. Może zawierać inne zasady jako elementy podrzędne. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut        | Opis                                                                                                                                           | Wymagany | Domyślne |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| Warunek        | Literał logiczny lub [wyrażenie](api-management-policy-expressions.md) określające, czy należy`false`przerwać`true`ponowną próbę ( ) lub kontynuować ( ).      | Tak      | Nie dotyczy     |
| count            | Liczba dodatnia określająca maksymalną liczbę ponownych prób do podjęcia próby.                                                                                | Tak      | Nie dotyczy     |
| interval         | Liczba dodatnia w sekundach określająca interwał oczekiwania między próbami ponawiania.                                                                 | Tak      | Nie dotyczy     |
| maksymalny interwał     | Liczba dodatnia w sekundach określająca maksymalny interwał oczekiwania między próbami ponawiania. Służy do implementowania wykładniczego algorytmu ponawiania próby. | Nie       | Nie dotyczy     |
| Delta            | Liczba dodatnia w sekundach określająca przyrost interwału oczekiwania. Służy do implementowania liniowych i wykładniczych algorytmów ponawiania prób.             | Nie       | Nie dotyczy     |
| pierwsza szybka ponowna próby | Jeśli ustawiona na `true` , pierwsza próba ponawiania jest wykonywana natychmiast.                                                                                  | Nie       | `false` |

> [!NOTE]
> Gdy jest `interval` określony tylko, **stałe** interwału ponownych prób są wykonywane.
> Gdy tylko `interval` `delta` i są określone, jest używany algorytm ponawiania **liniowego** interwału, gdzie `interval + (count - 1)*delta`czas oczekiwania między ponownych prób jest obliczany zgodnie z następującą formułą - .
> Gdy `interval`, `max-interval` `delta` i są określone, **wykładniczy** interwał ponawiać algorytm próby, gdzie czas oczekiwania między ponownych `interval` prób `max-interval` rośnie wykładniczo od `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`wartości do wartości zgodnie z następującą formułą - .

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) zasad . Należy zauważyć, że ograniczenia użycia zasad podrzędnych będą dziedziczone przez te zasady.

-   **Sekcje zasad:** przychodzące, wychodzące, wewnętrznej bazy danych, on-error

-   **Zakresy zasad:** wszystkie zakresy

## <a name="return-response"></a><a name="ReturnResponse"></a>Odpowiedź zwrotna

Zasady `return-response` przerywa wykonywanie potoku i zwraca domyślną lub niestandardową odpowiedź do wywołującego. Domyślna `200 OK` odpowiedź jest bez treści. Niestandardową odpowiedź można określić za pomocą zmiennej kontekstowej lub instrukcji zasad. Gdy oba są dostarczane, odpowiedź zawarta w zmiennej kontekstowej jest modyfikowany przez instrukcje zasad przed zwrócone do obiektu wywołującego.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

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

| Element         | Opis                                                                               | Wymagany |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| odpowiedź zwrotna | Element główny.                                                                             | Tak      |
| set-header      | Instrukcja zasad [nagłówka zestawu.](api-management-transformation-policies.md#SetHTTPheader) | Nie       |
| set-body        | Instrukcja zasad [zestawu.](api-management-transformation-policies.md#SetBody)         | Nie       |
| ustaw-status      | Instrukcja zasad [ustawiania stanu.](api-management-advanced-policies.md#SetStatus)           | Nie       |

### <a name="attributes"></a>Atrybuty

| Atrybut              | Opis                                                                                                                                                                          | Wymagany  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| odpowiedź-zmienna-nazwa | Nazwa zmiennej kontekstowej, do którego odwołuje się na przykład nadrzędna `Response` zasada [żądania wysyłania](api-management-advanced-policies.md#SendRequest) i zawierająca obiekt | Element opcjonalny. |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, wewnętrznej bazy danych, on-error

-   **Zakresy zasad:** wszystkie zakresy

## <a name="send-one-way-request"></a><a name="SendOneWayRequest"></a>Wyślij żądanie w jedną stronę

Zasady `send-one-way-request` wysyła podane żądanie do określonego adresu URL bez oczekiwania na odpowiedź.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

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

Ta przykładowa zasada pokazuje `send-one-way-request` przykład użycia zasad do wysyłania wiadomości do pokoju rozmów Slack, jeśli kod odpowiedzi HTTP jest większy lub równy 500. Aby uzyskać więcej informacji na temat tego przykładu, zobacz [Korzystanie z usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

| Element                    | Opis                                                                                                 | Wymagany                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| wyślij jednokierunkowe żądanie       | Element główny.                                                                                               | Tak                             |
| url                        | Adres URL żądania.                                                                                     | Nie, jeśli mode=copy; w przeciwnym razie tak. |
| method                     | Metoda HTTP dla żądania.                                                                            | Nie, jeśli mode=copy; w przeciwnym razie tak. |
| nagłówek                     | Nagłówek żądania. Użyj wielu elementów nagłówka dla wielu nagłówków żądań.                                  | Nie                              |
| body                       | Treść żądania.                                                                                           | Nie                              |
| certyfikat uwierzytelniania | [Certyfikat używany do uwierzytelniania klienta](api-management-authentication-policies.md#ClientCertificate) | Nie                              |

### <a name="attributes"></a>Atrybuty

| Atrybut     | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Wymagany | Domyślne  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="ciąg" | Określa, czy jest to nowe żądanie, czy kopia bieżącego żądania. W trybie wychodzącym mode=copy nie inicjuje treści żądania.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nie       | Nowa      |
| name          | Określa nazwę nagłówka, która ma zostać ustawiona.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Tak      | Nie dotyczy      |
| istnieje-działanie | Określa, jaką akcję należy podjąć, gdy nagłówek jest już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> - zastępowanie - zastępuje wartość istniejącego nagłówka.<br />- skip - nie zastępuje istniejącej wartości nagłówka.<br />- dołączanie - dołącza wartość do istniejącej wartości nagłówka.<br />- delete - usuwa nagłówek z żądania.<br /><br /> Gdy ustawiono rejestrowanie `override` wielu wpisów o tej samej nazwie powoduje, że nagłówek jest ustawiany zgodnie ze wszystkimi wpisami (które będą wyświetlane wiele razy); tylko wymienione wartości zostaną ustawione w wyniku. | Nie       | override |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, wewnętrznej bazy danych, on-error

-   **Zakresy zasad:** wszystkie zakresy

## <a name="send-request"></a><a name="SendRequest"></a>Wyślij żądanie

Zasady `send-request` wysyła podane żądanie do określonego adresu URL, nie czekając dłużej niż ustawiona wartość limitu czasu.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

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

W tym przykładzie pokazano jeden sposób, aby zweryfikować token odwołania z serwerem autoryzacji. Aby uzyskać więcej informacji na temat tego przykładu, zobacz [Korzystanie z usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

| Element                    | Opis                                                                                                 | Wymagany                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| wyślij żądanie               | Element główny.                                                                                               | Tak                             |
| url                        | Adres URL żądania.                                                                                     | Nie, jeśli mode=copy; w przeciwnym razie tak. |
| method                     | Metoda HTTP dla żądania.                                                                            | Nie, jeśli mode=copy; w przeciwnym razie tak. |
| nagłówek                     | Nagłówek żądania. Użyj wielu elementów nagłówka dla wielu nagłówków żądań.                                  | Nie                              |
| body                       | Treść żądania.                                                                                           | Nie                              |
| certyfikat uwierzytelniania | [Certyfikat używany do uwierzytelniania klienta](api-management-authentication-policies.md#ClientCertificate) | Nie                              |

### <a name="attributes"></a>Atrybuty

| Atrybut                       | Opis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Wymagany | Domyślne  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="ciąg"                   | Określa, czy jest to nowe żądanie, czy kopia bieżącego żądania. W trybie wychodzącym mode=copy nie inicjuje treści żądania.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nie       | Nowa      |
| odpowiedź-zmienna-nazwa="ciąg" | Nazwa zmiennej kontekstowej, która otrzyma obiekt odpowiedzi. Jeśli zmienna nie istnieje, zostanie utworzona po pomyślnym wykonaniu zasad [`context.Variable`](api-management-policy-expressions.md#ContextVariables) i staną się dostępne za pośrednictwem kolekcji.                                                                                                                                                                                                                                                                                                                          | Tak      | Nie dotyczy      |
| limit czasu="całkowita"               | Interwał limitu czasu w sekundach przed wywołaniem adresu URL kończy się niepowodzeniem.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Nie       | 60       |
| ignoruj-błąd                    | Jeśli true i żądanie powoduje błąd:<br /><br /> - Jeśli odpowiedź-zmienna nazwa została określona będzie zawierać wartość null.<br />- Jeśli odpowiedź-zmienna nazwa nie została określona, kontekst. Żądanie nie zostanie zaktualizowane.                                                                                                                                                                                                                                                                                                                                                                                   | Nie       | false    |
| name                            | Określa nazwę nagłówka, która ma zostać ustawiona.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Tak      | Nie dotyczy      |
| istnieje-działanie                   | Określa, jaką akcję należy podjąć, gdy nagłówek jest już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> - zastępowanie - zastępuje wartość istniejącego nagłówka.<br />- skip - nie zastępuje istniejącej wartości nagłówka.<br />- dołączanie - dołącza wartość do istniejącej wartości nagłówka.<br />- delete - usuwa nagłówek z żądania.<br /><br /> Gdy ustawiono rejestrowanie `override` wielu wpisów o tej samej nazwie powoduje, że nagłówek jest ustawiany zgodnie ze wszystkimi wpisami (które będą wyświetlane wiele razy); tylko wymienione wartości zostaną ustawione w wyniku. | Nie       | override |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, wewnętrznej bazy danych, on-error

-   **Zakresy zasad:** wszystkie zakresy

## <a name="set-http-proxy"></a><a name="SetHttpProxy"></a>Ustawianie serwera proxy HTTP

Zasady `proxy` umożliwiają kierowanie żądań przesyłanych dalej do zaplecza za pośrednictwem serwera proxy HTTP. Tylko protokół HTTP (nie HTTPS) jest obsługiwany między bramą a serwerem proxy. Tylko uwierzytelnianie podstawowe i NTLM.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Przykład

Należy zwrócić uwagę na użycie [właściwości](api-management-howto-properties.md) jako wartości nazwy użytkownika i hasła, aby uniknąć przechowywania poufnych informacji w dokumencie zasad.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elementy

| Element | Opis  | Wymagany |
| ------- | ------------ | -------- |
| proxy   | Element główny | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut         | Opis                                            | Wymagany | Domyślne |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="ciąg"      | Adres URL serwera http://host:portproxy w postaci pliku .             | Tak      | Nie dotyczy     |
| nazwa użytkownika="ciąg" | Nazwa użytkownika, która ma być używana do uwierzytelniania za pomocą serwera proxy. | Nie       | Nie dotyczy     |
| hasło="ciąg" | Hasło używane do uwierzytelniania za pomocą serwera proxy. | Nie       | Nie dotyczy     |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące

-   **Zakresy zasad:** wszystkie zakresy

## <a name="set-request-method"></a><a name="SetRequestMethod"></a>Ustaw metodę żądania

Zasady `set-method` umożliwiają zmianę metody żądania HTTP dla żądania.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Przykład

Ta przykładowa zasada, `set-method` która używa zasad, zawiera przykład wysyłania wiadomości do pokoju rozmów Slack, jeśli kod odpowiedzi HTTP jest większy lub równy 500. Aby uzyskać więcej informacji na temat tego przykładu, zobacz [Korzystanie z usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

| Element    | Opis                                                       | Wymagany |
| ---------- | ----------------------------------------------------------------- | -------- |
| metoda set- | Element główny. Wartość elementu określa metodę HTTP. | Tak      |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, błąd

-   **Zakresy zasad:** wszystkie zakresy

## <a name="set-status-code"></a><a name="SetStatus"></a>Ustawianie kodu stanu

Zasady `set-status` ustawia kod stanu HTTP do określonej wartości.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Przykład

W tym przykładzie pokazano, jak zwrócić odpowiedź 401, jeśli token autoryzacji jest nieprawidłowy. Aby uzyskać więcej informacji, zobacz [Korzystanie z usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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

| Element    | Opis   | Wymagany |
| ---------- | ------------- | -------- |
| ustaw-status | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut       | Opis                                                | Wymagany | Domyślne |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code="całkowita"  | Kod stanu HTTP do zwrócenia.                            | Tak      | Nie dotyczy     |
| reason="ciąg" | Opis przyczyny zwrócenia kodu stanu. | Tak      | Nie dotyczy     |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad: wychodzące,** zaplecze, błąd
-   **Zakresy zasad:** wszystkie zakresy

## <a name="set-variable"></a><a name="set-variable"></a>Ustaw zmienną

Zasada `set-variable` deklaruje zmienną [kontekstową](api-management-policy-expressions.md#ContextVariables) i przypisuje jej wartość określoną za pomocą [wyrażenia](api-management-policy-expressions.md) lub literału ciągu. jeśli wyrażenie zawiera literał, zostanie przekonwertowane na ciąg, `System.String`a typem wartości będzie .

### <a name="policy-statement"></a><a name="set-variablePolicyStatement"></a>Oświadczenie dotyczące zasad

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="example"></a><a name="set-variableExample"></a>Przykład

W poniższym przykładzie przedstawiono zasadę zmiennej zestawu w sekcji przychodzącej. Ta zasada zmiennej `isMobile` zestawu tworzy zmienną [kontekstową](api-management-policy-expressions.md#ContextVariables) `User-Agent` logiczną ustawioną `iPad` `iPhone`na true, jeśli nagłówek żądania zawiera tekst lub .

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elementy

| Element      | Opis   | Wymagany |
| ------------ | ------------- | -------- |
| ustawiona zmienna | Element główny. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                              | Wymagany |
| --------- | ------------------------------------------------------------------------ | -------- |
| name      | Nazwa zmiennej.                                                | Tak      |
| value     | Wartość zmiennej. Może to być wyrażenie lub wartość literału. | Tak      |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, wewnętrznej bazy danych, on-error
-   **Zakresy zasad:** wszystkie zakresy

### <a name="allowed-types"></a><a name="set-variableAllowedTypes"></a>Dozwolone typy

Wyrażenia używane w `set-variable` zasadach musi zwrócić jeden z następujących typów podstawowych.

-   System.Boolean
-   System.SByte
-   System.Bajt
-   System.UInt16
-   System.UInt32
-   System.UInt64
-   System.Int16
-   System.Int32
-   System.Int64
-   System.decimal
-   System.Single
-   System.double
-   System.guid
-   System.string
-   System.char
-   System.datetime
-   System.timespan
-   System.Byte?
-   System.UInt16?
-   System.UInt32?
-   System.UInt64?
-   System.Int16?
-   System.Int32?
-   System.Int64?
-   System.decimal?
-   System.Single?
-   System.double?
-   System.guid?
-   System.string?
-   System.char?
-   System.datetime?

## <a name="trace"></a><a name="Trace"></a>Śledzenia

Zasady `trace` dodaje śledzenia niestandardowego do danych wyjściowych inspektora interfejsu API, telemetries usługi Application Insights i/lub dzienniki diagnostyczne.

-   Zasady dodaje śledzenia niestandardowego do [danych wyjściowych inspektora interfejsu](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) API `Ocp-Apim-Trace` po wyzwoleniu śledzenia, `Ocp-Apim-Subscription-Key` czyli nagłówek żądania jest obecny i ustawiony na true i nagłówek żądania jest obecny i posiada prawidłowy klucz, który umożliwia śledzenie.
-   Zasady tworzy [dane](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) telemetryczne śledzenia w usłudze Application Insights, gdy [integracja usługi Application Insights](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights) jest włączona, a `severity` poziom określony w zasadach jest na poziomie lub wyższym niż `verbosity` poziom określony w ustawieniu diagnostycznym.
-   Zasada dodaje właściwość we wpisie dziennika, gdy [logi diagnostyczne](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs) jest włączona, a poziom ważności określony w zasadach jest na poziomie lub wyższym niż poziom szczegółowości określony w ustawieniu diagnostycznym.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="example"></a><a name="traceExample"></a>Przykład

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>Elementy

| Element  | Opis                                                                                                                                          | Wymagany |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| śledzenie    | Element główny.                                                                                                                                        | Tak      |
| message  | Ciąg lub wyrażenie do zarejestrowania.                                                                                                                 | Tak      |
| metadane | Dodaje właściwość niestandardową do danych telemetrycznych [śledzenia](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) usługi Application Insights. | Nie       |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                                                                               | Wymagany | Domyślne |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| source    | Literał ciągu znaczące do przeglądarki śledzenia i określania źródła wiadomości.                                   | Tak      | Nie dotyczy     |
| ważność  | Określa poziom ważności śledzenia. Dozwolone wartości `verbose`to `information` `error` , (od najniższego do najwyższego). | Nie       | Pełny |
| name      | Nazwa właściwości.                                                                                                     | Tak      | Nie dotyczy     |
| value     | Wartość właściwości.                                                                                                    | Tak      | Nie dotyczy     |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, wewnętrznej bazy danych, on-error

-   **Zakresy zasad:** wszystkie zakresy

## <a name="wait"></a><a name="Wait"></a>Czekać

Zasady `wait` wykonuje swoje bezpośrednie zasady podrzędne równolegle i czeka na wszystkie lub jeden z jego bezpośrednich zasad podrzędnych, aby zakończyć przed zakończeniem. Zasady oczekiwania mogą mieć jako swoje bezpośrednie zasady podrzędne [Wyślij żądanie](api-management-advanced-policies.md#SendRequest), Pobierz wartość z [pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey)i [Zasady przepływu sterowania.](api-management-advanced-policies.md#choose)

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Przykład

W poniższym przykładzie `choose` istnieją dwie zasady `wait` jako bezpośrednie zasady podrzędne zasad. Każda z `choose` tych zasad jest wykonywana równolegle. Każda `choose` zasada próbuje pobrać wartość buforowaną. Jeśli istnieje miss pamięci podręcznej, usługa wewnętrznej bazy danych jest wywoływana w celu zapewnienia wartości. W tym `wait` przykładzie zasady nie zostanie ukończona, dopóki `for` nie wszystkie jego `all`bezpośrednie zasady podrzędne nie zostanie ukończone, ponieważ atrybut jest ustawiony na . W tym przykładzie zmienne `value-one` `execute-branch-two`kontekstowe `value-two`(`execute-branch-one`, , , i ) są deklarowane poza zakresem tej przykładowej zasady.

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

| Element | Opis                                                                                                   | Wymagany |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| czas oczekiwania    | Element główny. Może zawierać tylko `send-request`jako `cache-lookup-value`elementy `choose` podrzędne i zasady. | Tak      |

### <a name="attributes"></a>Atrybuty

| Atrybut | Opis                                                                                                                                                                                                                                                                                                                                                                                                            | Wymagany | Domyślne |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| for       | Określa, `wait` czy zasady czeka na wszystkie natychmiastowe zasady podrzędne, które mają być zakończone lub tylko jeden. Dozwolone wartości to:<br /><br /> - `all`- poczekaj, aż wszystkie najbliższe zasady dotyczące dzieci dojdą do<br />- dowolny - poczekaj na zakończenie każdej bezpośredniej polityki dziecka. Po zakończeniu pierwszej zasady bezpośredniego `wait` dziecka zasady są wypełniane i wykonywanie innych zasad bezpośredniego dziecka zostaje zakończone. | Nie       | all     |

### <a name="usage"></a>Sposób użycia

Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze
-   **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

-   [Zasady w zarządzaniu interfejsami API](api-management-howto-policies.md)
-   [Wyrażenia zasad](api-management-policy-expressions.md)
-   [Odwołanie do zasad](api-management-policy-reference.md) dla pełnej listy oświadczeń zasad i ich ustawień
-   [Przykłady zasad](policy-samples.md)
