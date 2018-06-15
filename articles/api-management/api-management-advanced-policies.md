---
title: Zaawansowane zasady zarządzania interfejsu API platformy Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: dcd4d28341e766baeaf6d581a69312cc33a0282a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30233830"
---
# <a name="api-management-advanced-policies"></a>Zarządzanie interfejsami API zaawansowane zasady
W tym temacie znajdują się informacje na następujące zasady usługi API Management. Aby uzyskać informacje dotyczące dodawania i konfigurowania zasad, zobacz [zasad w usłudze API Management](http://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AdvancedPolicies"></a> Zaawansowane zasady

-   [Przepływ kontroli](api-management-advanced-policies.md#choose) — warunkowo stosuje deklaracji zasad, na podstawie wyników oceny Boolean [wyrażenia](api-management-policy-expressions.md).
-   [Przekazanie żądania](#ForwardRequest) -przekazuje żądanie do usługi zaplecza.
-   [Ogranicz współbieżności](#LimitConcurrency) — uniemożliwia ujęta zasady wykonania przez więcej niż określoną liczbę żądań w czasie.
-   [Dziennika do Centrum zdarzeń](#log-to-eventhub) — wysyła komunikaty w określonym formacie do Centrum zdarzeń zdefiniowanych przez podmiot rejestratora.
-   [Mock odpowiedzi](#mock-response) -przerwań potoku wykonywania i zwraca odpowiedź mocked bezpośrednio do obiektu wywołującego.
-   [Spróbuj ponownie](#Retry) -ponowi próbę wykonania instrukcji objętego zasad, jeśli i do momentu spełnienia warunku. Wykonanie Powtórz w określonych odstępach czasu, a także do określonej liczba ponownych prób.
-   [Odpowiedź zwrócona](#ReturnResponse) -przerwań potoku wykonywania i zwraca określoną odpowiedzią bezpośrednio do obiektu wywołującego.
-   [Wyślij żądanie jednokierunkowej](#SendOneWayRequest) — wysyła żądanie pod określony adres URL bez oczekiwania na odpowiedź.
-   [Wyślij żądanie](#SendRequest) — wysyła żądanie do określonego adresu URL.
-   [Ustaw serwer proxy HTTP](#SetHttpProxy) — zezwala na żądania trasy przekazywane za pośrednictwem serwera proxy HTTP.
-   [Ustawia metodę żądania](#SetRequestMethod) — umożliwia zmianę metody HTTP dla żądania.
-   [Ustaw kod stanu](#SetStatus) — zmienia kod stanu HTTP do określonej wartości.
-   [Ustaw zmienną](api-management-advanced-policies.md#set-variable) -będzie się powtarzał wartości w nazwanym [kontekstu](api-management-policy-expressions.md#ContextVariables) zmiennej nowsze dostępu.
-   [Śledzenia](#Trace) -dodaje ciąg do [inspektora interfejsu API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) danych wyjściowych.
-   [Poczekaj](#Wait) -oczekuje dla ujęta [żądanie wysłania](api-management-advanced-policies.md#SendRequest), [pobrać wartości z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey), lub [sterowania przepływem](api-management-advanced-policies.md#choose) zasad, aby ukończyć przed kontynuowaniem.

##  <a name="choose"></a> Przepływ sterowania
 `choose` Zasad stosuje objętego zasady w oparciu o wyniki obliczania wyrażeń logicznych, podobnie jak w przypadku to inaczej lub przełącznika instrukcje tworzenia w języku programowania.

###  <a name="ChoosePolicyStatement"></a> Deklaracja zasad

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

 Zasady kontroli przepływu musi zawierać co najmniej jeden `<when/>` elementu. `<otherwise/>` Element jest opcjonalny. Warunki w `<when/>` elementy są oceniane w kolejności ich wyglądu w zasadzie. Instrukcji zasad ujętą w nawiasy klamrowe pierwszy `<when/>` element z równą atrybut warunku `true` zostaną zastosowane. Zasady ujętą w nawiasy klamrowe `<otherwise/>` elementu, jeśli jest obecny, zostaną zastosowane, jeśli wszystkie z `<when/>` są atrybuty warunku elementu `false`.

### <a name="examples"></a>Przykłady

####  <a name="ChooseExample"></a> Przykład
 W poniższym przykładzie pokazano [set-variable](api-management-advanced-policies.md#set-variable) zasad i dwie zasady przepływu sterowania.

 Ustaw zasady zmiennej znajduje się w sekcji dla ruchu przychodzącego i tworzy `isMobile` logiczna [kontekstu](api-management-policy-expressions.md#ContextVariables) zmiennej, która ma ustawioną wartość true, jeśli `User-Agent` tekst zawiera nagłówek żądania `iPad` lub `iPhone`.

 Pierwszy zasad przepływu sterowania jest również w sekcji dla ruchu przychodzącego i warunkowo stosuje się jeden z dwóch [ustawić parametr ciągu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) zasad w zależności od wartości `isMobile` zmiennej kontekstu.

 Drugie zasady przepływu sterowania znajduje się w sekcji wychodzących i warunkowo stosuje [XML Konwertuj do formatu JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) zasad podczas `isMobile` ma ustawioną wartość `true`.

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
 W tym przykładzie pokazano, jak wykonać filtrowanie zawartości przez usunięcie elementów danych z odpowiedzi otrzymał od usługi wewnętrznej bazy danych, korzystając z `Starter` produktu. Aby demonstracyjne konfigurowania i korzystania z tych zasad, zobacz [177 epizodu obejmują chmury: więcej funkcji zarządzania interfejsu API z Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przewijanie do przodu do 34:30. Rozpocznij od 31:50 wyświetlić przegląd [ciemny niebo prognozy interfejsu API](https://developer.forecast.io/) używane na potrzeby tego pokazu.

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

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|Wybierz pozycję|Element główny.|Yes|
|Kiedy|Warunek, którego chcesz użyć dla `if` lub `ifelse` części `choose` zasad. Jeśli `choose` zasad ma wiele `when` sekcje są oceniane po kolei. Raz `condition` z o tym, kiedy element daje w wyniku `true`, nie musisz `when` oceny warunków.|Yes|
|w przeciwnym razie|Zawiera fragment zasad do użycia, jeśli żadna z `when` warunki obliczać `true`.|Nie|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|
|---------------|-----------------|--------------|
|warunek = "wyrażenie warunkowe &#124; logiczna stała"|Wyrażenia logicznego lub stała się obliczone, gdy zawierający `when` oceny deklaracji zasad.|Yes|

###  <a name="ChooseUsage"></a> Użycie
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** przychodzący, wychodzący wewnętrznej bazy danych, na błąd

-   **Zakresy zasad:** wszystkich zakresów

##  <a name="ForwardRequest"></a> Przekazanie żądania
 `forward-request` Zasad przesyła żądanie przychodzące do usługi zaplecza, określony w żądaniu [kontekstu](api-management-policy-expressions.md#ContextVariables). Adres URL usługi wewnętrznej bazy danych jest określony w interfejsie API [ustawienia](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) i można zmienić za pomocą [Ustaw usługę zaplecza](api-management-transformation-policies.md) zasad.

> [!NOTE]
>  Usuwanie wyników tej zasady w żądaniu nie są przekazywane do wewnętrznej bazy danych usługi i zasady w sekcji wychodzące są oceniane natychmiast po pomyślnym zakończeniu zasady w sekcji dla ruchu przychodzącego.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<forward-request timeout="time in seconds" follow-redirects="true | false"/>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład
 Następujące zasady na poziomie interfejsu API przekazuje wszystkie żądania do usługi wewnętrznej bazy danych z limit czasu 60 sekund.

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
 Używa tej zasady na poziomie operacji `base` element dziedziczenie zasad wewnętrznej bazy danych z nadrzędnym zakresie poziomu interfejsu API.

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
 Ta operacja zasady na poziomie jawnie przekazuje wszystkie żądania do usługi zaplecza z limitem czasu 120 i nie dziedziczy nadrzędnego zasad poziomu zaplecza interfejsu API.

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
 Zasady na poziomie tej operacji nie przekazuje żądania do usługi zaplecza.

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

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|żądanie do przodu|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|Domyślne|
|---------------|-----------------|--------------|-------------|
|timeout="integer"|Interwał limitu czasu w sekundach przed wywołaniem usługi wewnętrznej bazy danych nie powiedzie się.|Nie|300 sekund|
|follow-redirects="true &#124; false"|Określa, czy przekierowania z usługi wewnętrznej bazy danych są następuje bramy lub zwracany do obiektu wywołującego.|Nie|false|

### <a name="usage"></a>Sposób użycia
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** wewnętrznej bazy danych
-   **Zakresy zasad:** wszystkich zakresów

##  <a name="LimitConcurrency"></a> Limit współbieżności
 `limit-concurrency` Zasad uniemożliwia objętego zasad wykonywania przez więcej niż określoną liczbę żądań w danym momencie. Po przekroczeniu tej liczby, nowe żądania nie powiedzie się bezpośrednio z kodem stanu zbyt wiele żądań 429.

###  <a name="LimitConcurrencyStatement"></a> Deklaracja zasad

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład
 W poniższym przykładzie pokazano sposób ograniczyć liczbę przekazywany do wewnętrznej bazy danych na podstawie wartości zmiennej kontekstu żądania.

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

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|limit-concurrency|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|Domyślne|
|---------------|-----------------|--------------|--------------|
|key|Ciąg. Wyrażenie jest dozwolone. Określa zakres współbieżności. Może być współużytkowane przez wiele zasad.|Yes|ND|
|Maksymalna liczba|Liczba całkowita. Określa maksymalną liczbę żądań, które mogą wprowadzać zasady.|Yes|ND|

### <a name="usage"></a>Sposób użycia
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** przychodzący, wychodzący wewnętrznej bazy danych, na błąd

-   **Zakresy zasad:** wszystkich zakresów

##  <a name="log-to-eventhub"></a> Dziennika do Centrum zdarzeń
 `log-to-eventhub` Zasad wysyła komunikaty w określonym formacie do Centrum zdarzeń zdefiniowanych przez podmiot rejestratora. Jak jego nazwa wskazuje, zasady są używane do zapisywania wybrane informacje kontekstu żądania lub odpowiedzi do analizy w trybie online lub offline.

> [!NOTE]
>  Przewodnik krok po kroku dotyczące konfigurowania Centrum zdarzeń i rejestrowania zdarzeń dla [jak mają być rejestrowane zdarzenia zarządzanie interfejsami API w usłudze Azure Event Hubs](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Przykład
 Dowolny ciąg może służyć jako wartość mają być rejestrowane w usłudze Event Hubs. W tym przykładzie daty i godziny, nazwa usługi wdrożenia, identyfikator żądania, adresu ip oraz nazwy operacji dla wszystkich połączeń przychodzących, które są rejestrowane w Centrum zdarzeń zarejestrowany rejestratora `contoso-logger` identyfikator.

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

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|log-to-eventhub|Element główny. Wartość tego elementu jest ciąg do logowania się do Centrum zdarzeń.|Yes|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|
|---------------|-----------------|--------------|
|logger-id|Identyfikator rejestratora zarejestrowana przy użyciu usługi API Management.|Yes|
|Identyfikator partycji|Określa indeks partycji, którego wysyłane są wiadomości.|Opcjonalny. Ten atrybut nie może być używany, jeśli `partition-key` jest używany.|
|Klucz partycji|Określa wartość służącą do przypisania partycji podczas wysyłania wiadomości.|Opcjonalny. Ten atrybut nie może być używany, jeśli `partition-id` jest używany.|

### <a name="usage"></a>Sposób użycia
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** przychodzący, wychodzący wewnętrznej bazy danych, na błąd

-   **Zakresy zasad:** wszystkich zakresów

##  <a name="mock-response"></a> Zasymulować odpowiedzi
`mock-response`, Jak nazwa oznacza, służy do mock operacji i interfejsów API. Przerywa wykonywanie normalny potok, a zwraca mocked odpowiedź do obiektu wywołującego. Zasady zawsze próbuje zwrócić odpowiedzi wierności najwyższy. Preferuje go przykłady zawartości odpowiedzi, gdy dostępne. Generuje on przykładowe odpowiedzi ze schematów, gdy znajdują się schematów i przykłady nie. W przypadku znalezienia przykłady ani schematów odpowiedzi z zawartością, nie są zwracane.

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

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|makiety odpowiedzi|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|Domyślne|
|---------------|-----------------|--------------|--------------|
|Kod stanu|Określa kod stanu odpowiedzi i umożliwia wybranie odpowiedniego przykład lub schematu.|Nie|200|
|Typ zawartości|Określa `Content-Type` wartość nagłówka odpowiedzi i umożliwia wybranie odpowiedniego przykład lub schematu.|Nie|Brak|

### <a name="usage"></a>Sposób użycia
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** przychodzące, wychodzące, na błąd

-   **Zakresy zasad:** wszystkich zakresów

##  <a name="Retry"></a> Spróbuj ponownie
 `retry` Zasad jego zasad podrzędnych jest wykonywana raz i ponowi próbę ich wykonanie do momentu retry `condition` staje się `false` lub ponów próbę `count` jest wyczerpana.

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
 W poniższym przykładzie przekazywania żądania próba zostanie ponowiona maksymalnie 10 razy przy użyciu algorytm wykładnicze ponów próbę. Ponieważ `first-fast-retry` jest ustawiona na false, wszystkie ponownych prób podlegają algorytm wykładnicze ponownych prób.

```xml

<retry
    condition="@(context.Response.StatusCode == 500)"
    count="10"
    interval="10"
    max-interval="100"
    delta="10"
    first-fast-retry="false">
        <forward-request />
</retry>

```

### <a name="elements"></a>Elementy

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|retry|Element główny. Może zawierać inne zasady jako jego elementy podrzędne.|Yes|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|Domyślne|
|---------------|-----------------|--------------|-------------|
|warunek|Logiczna literał lub [wyrażenie](api-management-policy-expressions.md) określenie ponownych prób powinna zostać zatrzymana (`false`) lub nadal (`true`).|Yes|ND|
|liczba|Liczba dodatnia określająca maksymalną liczbę ponownych prób.|Yes|ND|
|interval|Liczba dodatnia, w sekundach, określając oczekiwania interwału ponawiania prób.|Yes|ND|
|max-interval|Liczba dodatnia, w sekundach określający maksymalną poczekaj interwał między ponownymi próbami. Służy do implementowania algorytm wykładnicze ponów próbę.|Nie|ND|
|delta|Liczba dodatnia, w sekundach, określając przyrost interwału oczekiwania. Służy do zaimplementowania algorytmów liniowej i wykładniczej ponów próbę.|Nie|ND|
|pierwszy ponownej próby fast|Jeśli ustawiono `true` , pierwszy ponowienia próby jest wykonywane bezpośrednio.|Nie|`false`|

> [!NOTE]
>  Gdy tylko `interval` jest określony, **stałej** są wykonywane Interwał ponownych prób.
> Gdy tylko `interval` i `delta` są określone, **liniowej** interwału ponawiania algorytm jest używany, gdy czas oczekiwania między kolejnymi próbami jest obliczana według następującego wzoru - `interval + (count - 1)*delta`.
> Gdy `interval`, `max-interval` i `delta` są określone, **wykładniczej** interwału ponawiania algorytm jest stosowana, gdy czas oczekiwania między kolejnymi próbami rośnie wykładniczo od wartości `interval` wartość `max-interval` zgodnie z następujących forumula - `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.

### <a name="usage"></a>Sposób użycia
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Należy pamiętać, że ograniczenia użycia zasad podrzędne będą dziedziczone przez te zasady.

-   **Sekcje zasad:** przychodzący, wychodzący wewnętrznej bazy danych, na błąd

-   **Zakresy zasad:** wszystkich zakresów

##  <a name="ReturnResponse"></a> Odpowiedź zwrócona
 `return-response` Zasad przerwanie wykonywania potoku i zwraca domyślnego lub niestandardowego odpowiedź do obiektu wywołującego. Domyślny jest `200 OK` nie jednostki. Za pomocą instrukcji kontekstu zmiennej lub zasad można określić niestandardową odpowiedź. Zarówno udostępniane odpowiedzi zawarty w zmiennej kontekstu, która jest modyfikowany przez deklaracji zasad przed zwróceniem do obiektu wywołującego.

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

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|odpowiedź Return|Element główny.|Yes|
|set-header|A [nagłówka set](api-management-transformation-policies.md#SetHTTPheader) deklaracji zasad.|Nie|
|zestaw treści|A [treści zestaw](api-management-transformation-policies.md#SetBody) deklaracji zasad.|Nie|
|set-status|A [Ustaw stan](api-management-advanced-policies.md#SetStatus) deklaracji zasad.|Nie|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|
|---------------|-----------------|--------------|
|response-variable-name|Nazwa zmiennej kontekstu, która odwołuje się do z, na przykład nadrzędnym [żądanie wysłania](api-management-advanced-policies.md#SendRequest) zasad i zawierający `Response` obiektu|Opcjonalny.|

### <a name="usage"></a>Sposób użycia
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** przychodzący, wychodzący wewnętrznej bazy danych, na błąd

-   **Zakresy zasad:** wszystkich zakresów

##  <a name="SendOneWayRequest"></a> Wyślij żądanie jednokierunkowej
 `send-one-way-request` Zasad wysyła żądanie podana pod określony adres URL bez oczekiwania na odpowiedź.

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
 Ta zasada przykładowych pokazano przykład przy użyciu `send-one-way-request` zasad, aby wysłać wiadomość Slack pokoju rozmów, jeśli kod odpowiedzi HTTP jest większa niż lub równa 500. Aby uzyskać więcej informacji na ten przykład, zobacz [za pomocą usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|send-one-way-request|Element główny.|Yes|
|adres url|Adres URL żądania.|Jeśli nie trybu = kopiowania; tak, w przeciwnym razie wartość.|
|metoda|Metoda HTTP dla żądania.|Jeśli nie trybu = kopiowania; tak, w przeciwnym razie wartość.|
|nagłówek|Nagłówek żądania. Użyj wielu elementów nagłówka dla wielu nagłówków żądania.|Nie|
|treść|Treść żądania.|Nie|
|certyfikat uwierzytelniania|[Certyfikat uwierzytelniania klienta](api-management-authentication-policies.md#ClientCertificate)|Nie|


### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|Domyślne|
|---------------|-----------------|--------------|-------------|
|mode="string"|Określa, czy jest to nowe żądanie lub kopię bieżącego żądania. W trybie wychodzących tryb = kopiowania nie inicjuje treści żądania.|Nie|Nowa|
|name|Określa nazwę nagłówka do ustawienia.|Yes|ND|
|istnieje akcja|Określa, jakie działania w sytuacji, gdy nagłówek został już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> -override - zastępuje wartość istniejący nagłówek.<br />-skip — nie zastępuje istniejącą wartość nagłówka.<br />-dołączania - dołącza wartość do istniejącej wartości nagłówka.<br />-Usunięcie — usuwa nagłówek z żądania.<br /><br /> Jeśli wartość `override` powoduje rejestrowanie wiele wpisów o takiej samej nazwie w nagłówku ustawiany zgodnie ze wszystkich zapisów (które zostaną wyświetlone wiele razy); zostanie ustawiona tylko wartości wyświetlane w wynikach.|Nie|zastąpienie|

### <a name="usage"></a>Sposób użycia
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** przychodzący, wychodzący wewnętrznej bazy danych, na błąd

-   **Zakresy zasad:** wszystkich zakresów

##  <a name="SendRequest"></a> Wyślij żądanie
 `send-request` Zasad wysyła żądanie podana pod określony adres URL już ustaw wartość limitu czasu oczekiwania.

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
 Ten przykład przedstawia jedną z metod można zweryfikować odwołania do tokenu z serwera autoryzacji. Aby uzyskać więcej informacji na ten przykład, zobacz [za pomocą usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|Żądanie wysłania|Element główny.|Yes|
|adres url|Adres URL żądania.|Jeśli nie trybu = kopiowania; tak, w przeciwnym razie wartość.|
|metoda|Metoda HTTP dla żądania.|Jeśli nie trybu = kopiowania; tak, w przeciwnym razie wartość.|
|nagłówek|Nagłówek żądania. Użyj wielu elementów nagłówka dla wielu nagłówków żądania.|Nie|
|treść|Treść żądania.|Nie|
|certyfikat uwierzytelniania|[Certyfikat uwierzytelniania klienta](api-management-authentication-policies.md#ClientCertificate)|Nie|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|Domyślne|
|---------------|-----------------|--------------|-------------|
|mode="string"|Określa, czy jest to nowe żądanie lub kopię bieżącego żądania. W trybie wychodzących tryb = kopiowania nie inicjuje treści żądania.|Nie|Nowa|
|response-variable-name="string"|Jeśli nie istnieje `context.Response` jest używany.|Nie|ND|
|timeout="integer"|Interwał limitu czasu w sekundach przed wywołaniem do adresu URL nie powiedzie się.|Nie|60|
|Ignoruj błąd|Jeśli wartość PRAWDA, a wyniki żądania w błąd:<br /><br /> — Jeśli nazwa zmiennej odpowiedzi został określony, będzie zawierać wartości null.<br />— Jeśli nie określono odpowiedzi zmienna nazwy, kontekstu. Żądanie nie zostanie zaktualizowany.|Nie|false|
|name|Określa nazwę nagłówka do ustawienia.|Yes|ND|
|istnieje akcja|Określa, jakie działania w sytuacji, gdy nagłówek został już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> -override - zastępuje wartość istniejący nagłówek.<br />-skip — nie zastępuje istniejącą wartość nagłówka.<br />-dołączania - dołącza wartość do istniejącej wartości nagłówka.<br />-Usunięcie — usuwa nagłówek z żądania.<br /><br /> Jeśli wartość `override` powoduje rejestrowanie wiele wpisów o takiej samej nazwie w nagłówku ustawiany zgodnie ze wszystkich zapisów (które zostaną wyświetlone wiele razy); zostanie ustawiona tylko wartości wyświetlane w wynikach.|Nie|zastąpienie|

### <a name="usage"></a>Sposób użycia
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** przychodzący, wychodzący wewnętrznej bazy danych, na błąd

-   **Zakresy zasad:** wszystkich zakresów

##  <a name="SetHttpProxy"></a> Serwer proxy HTTP zestawu
 `proxy` Zasad umożliwia rozsyłanie żądań przekazywane do zapleczy za pośrednictwem serwera proxy HTTP. HTTP (a nie HTTPS) jest obsługiwana między bramy i serwera proxy. Podstawowe i tylko z uwierzytelniania NTLM.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Przykład
Zwróć uwagę na użycie [właściwości](api-management-howto-properties.md) jako wartości nazwy użytkownika i hasło, aby uniknąć poufne informacje są przechowywane w dokumencie zasady.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elementy

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|Serwer proxy|Element główny|Yes|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|Domyślne|
|---------------|-----------------|--------------|-------------|
|url="string"|Adres URL serwera proxy w postaci http://host:port.|Yes|ND|
|username="string"|Nazwa użytkownika używanego do uwierzytelniania z serwerem proxy.|Nie|ND|
|password="string"|Hasło będzie używany do uwierzytelniania z serwerem proxy.|Nie|ND|

### <a name="usage"></a>Sposób użycia
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego

-   **Zakresy zasad:** wszystkich zakresów

##  <a name="SetRequestMethod"></a> Set, Metoda żądania
 `set-method` Zasad umożliwia zmianę metody żądania HTTP dla żądania.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Przykład
 Przykładowe zasady używa `set-method` zasad przedstawiono przykład wysyłanie wiadomości do Slack pokoju rozmów, jeśli kod odpowiedzi HTTP jest większa niż lub równa 500. Aby uzyskać więcej informacji na ten przykład, zobacz [za pomocą usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|set, metoda|Element główny. Wartość elementu określa metodę HTTP.|Yes|

### <a name="usage"></a>Sposób użycia
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego, na błąd

-   **Zakresy zasad:** wszystkich zakresów

##  <a name="SetStatus"></a> Kod stanu zestawu
 `set-status` Zasad ustawia kod stanu HTTP do określonej wartości.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Przykład
 Ten przykład przedstawia sposób zwracania odpowiedzi 401, jeśli token autoryzacji jest nieprawidłowy. Aby uzyskać więcej informacji, zobacz [za pomocą usług zewnętrznych z usługi Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|set-status|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|Domyślne|
|---------------|-----------------|--------------|-------------|
|code="integer"|Kod stanu HTTP do zwrócenia.|Yes|ND|
|reason="string"|Opis powodu zwróciło kod stanu.|Yes|ND|

### <a name="usage"></a>Sposób użycia
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** wychodzący wewnętrznej bazy danych, na błąd
-   **Zakresy zasad:** wszystkich zakresów

##  <a name="set-variable"></a> Ustaw zmienną
 `set-variable` Deklaruje zasad [kontekstu](api-management-policy-expressions.md#ContextVariables) zmiennej i przypisuje go za pośrednictwem określoną wartość [wyrażenie](api-management-policy-expressions.md) lub literałem ciągu. Jeśli wyrażenie zawiera właściwość literal zostanie przekonwertowany na ciąg i typ wartości będzie `System.String`.

###  <a name="set-variablePolicyStatement"></a> Deklaracja zasad

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

###  <a name="set-variableExample"></a> Przykład
 W poniższym przykładzie pokazano Ustaw zasady zmiennej w sekcji dla ruchu przychodzącego. Ta zasada zmiennej zestaw tworzy `isMobile` logiczna [kontekstu](api-management-policy-expressions.md#ContextVariables) zmiennej, która ma ustawioną wartość true, jeśli `User-Agent` tekst zawiera nagłówek żądania `iPad` lub `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elementy

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|Ustaw zmienną|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|
|---------------|-----------------|--------------|
|name|Nazwa zmiennej.|Yes|
|wartość|Wartość zmiennej. Może to być wyrażenie lub ciągiem literałowym.|Yes|

### <a name="usage"></a>Sposób użycia
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** przychodzący, wychodzący wewnętrznej bazy danych, na błąd
-   **Zakresy zasad:** wszystkich zakresów

###  <a name="set-variableAllowedTypes"></a> Dozwolonymi typami
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

##  <a name="Trace"></a> śledzenia
 `trace` Zasad dodaje ciąg do [inspektora interfejsu API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) danych wyjściowych. Zasady będą wykonywane tylko gdy śledzenie jest wyzwalane, tj. `Ocp-Apim-Trace` nagłówek żądania jest obecny i ustaw do `true` i `Ocp-Apim-Subscription-Key` nagłówek żądania jest obecny i ma prawidłowy klucz, skojarzone z kontem administratora.

### <a name="policy-statement"></a>Deklaracja zasad

```xml

<trace source="arbitrary string literal"/>
    <!-- string expression or literal -->
</trace>

```

### <a name="elements"></a>Elementy

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|śledzenia|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|Domyślne|
|---------------|-----------------|--------------|-------------|
|źródło|Literał ciągu zrozumiały dla przeglądarki śledzenia i Określanie źródła komunikatu.|Yes|ND|

### <a name="usage"></a>Sposób użycia
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

-   **Sekcje zasad:** przychodzący, wychodzący wewnętrznej bazy danych, na błąd

-   **Zakresy zasad:** wszystkich zakresów

##  <a name="Wait"></a> oczekiwania
 `wait` Zasad wykonuje jego zasadami bezpośrednio podrzędne równolegle i czeka na wszystkich lub jednego z jego zasadami bezpośrednio podrzędne, aby ukończyć przed zakończeniem działania. Zasady oczekiwania może mieć jako jego zasadami bezpośrednio podrzędne [żądanie wysłania](api-management-advanced-policies.md#SendRequest), [pobrać wartości z pamięci podręcznej](api-management-caching-policies.md#GetFromCacheByKey), i [sterowania przepływem](api-management-advanced-policies.md#choose) zasad.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Przykład
 W poniższym przykładzie występują dwa `choose` zasad jako zasady bezpośrednio podrzędne `wait` zasad. Każdy z tych `choose` zasad wykonywane równolegle. Każdy `choose` zasad próbuje pobrać wartość w pamięci podręcznej. W przypadku Chybienie pamięci podręcznej usługi wewnętrznej bazy danych jest wywoływana podania wartości. W tym przykładzie `wait` zasad nie zostanie zakończone do czasu ukończenia wszystkich swoich zasad bezpośrednio podrzędne, ponieważ `for` atrybut ma ustawioną `all`.   W tym przykładzie zmienne kontekstu (`execute-branch-one`, `value-one`, `execute-branch-two`, i `value-two`) są deklarowane jako poza zakres tego przykład zasad.

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

|Element|Opis|Wymagane|
|-------------|-----------------|--------------|
|oczekiwania|Element główny. Może zawierać jako elementy podrzędne tylko `send-request`, `cache-lookup-value`, i `choose` zasad.|Yes|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|Domyślne|
|---------------|-----------------|--------------|-------------|
|for|Określa, czy `wait` czeka zasady dla wszystkich zasad bezpośrednio podrzędne być wykonane lub tylko jeden. Dozwolone wartości to:<br /><br /> -   `all` -Poczekaj, aż wszystkie zasady bezpośrednio podrzędne do ukończenia<br />-wszelkie - poczekaj dowolne zasady bezpośrednio podrzędne zakończyć. Po zakończeniu pierwszego zasad bezpośrednio podrzędne `wait` kończy zasad i wykonywania innych zasad bezpośrednio podrzędne zostanie zakończony.|Nie|all|

### <a name="usage"></a>Sposób użycia

Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** przychodzący, wychodzący wewnętrznej bazy danych
-   **Zakresy zasad:** wszystkich zakresów

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, Praca z zasad Zobacz:
+ [Zasady w usłudze API Management](api-management-howto-policies.md)
+ [Wyrażenia zasad](api-management-policy-expressions.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę deklaracji zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)
