---
title: Zasady przekształcania usługi Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się więcej o zasadach przekształcania dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 81b7fb687bb6ef88d1ed436923d0e5ff7561c22b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803249"
---
# <a name="api-management-transformation-policies"></a>Zasady przekształcania w usłudze API Management
Ten temat zawiera odwołanie do następujących zasad zarządzania interfejsami API. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [Zasady w zarządzania interfejsami API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="transformation-policies"></a><a name="TransformationPolicies"></a>Zasady transformacji

-   [Konwertuj JSON na XML](api-management-transformation-policies.md#ConvertJSONtoXML) — konwertuje treść żądania lub odpowiedzi z JSON na XML.

-   [Konwertuj XML na JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) — konwertuje treść żądania lub odpowiedzi z XML na JSON.

-   [Znajdź i zastąp ciąg w treści](api-management-transformation-policies.md#Findandreplacestringinbody) — znajduje podciąg żądania lub odpowiedzi i zastępuje go innym podciągem.

-   [Maskuj adresy URL w zawartości](api-management-transformation-policies.md#MaskURLSContent) — ponownie zapisuje (maskuje) łącza w treści odpowiedzi, dzięki czemu wskazują one równoważne łącze za pośrednictwem bramy.

-   [Ustaw usługę wewnętrznej bazy danych](api-management-transformation-policies.md#SetBackendService) — zmienia usługę wewnętrznej bazy danych dla żądania przychodzącego.

-   [Ustaw treść](api-management-transformation-policies.md#SetBody) — ustawia treść wiadomości dla żądań przychodzących i wychodzących.

-   [Ustaw nagłówek HTTP](api-management-transformation-policies.md#SetHTTPheader) — przypisuje wartość do istniejącego nagłówka odpowiedzi i/lub żądania lub dodaje nowy nagłówek odpowiedzi i/lub żądania.

-   [Ustaw parametr ciągu kwerendy](api-management-transformation-policies.md#SetQueryStringParameter) — dodaje, zastępuje wartość lub usuwa parametr ciągu zapytania żądania.

-   [Przepisz adres URL](api-management-transformation-policies.md#RewriteURL) — konwertuje adres URL żądania z formularza publicznego na formularz oczekiwany przez usługę sieci web.

-   [Przekształcanie xml przy użyciu XSLT](api-management-transformation-policies.md#XSLTransform) - Stosuje transformację XSL do XML w treści żądania lub odpowiedzi.

##  <a name="convert-json-to-xml"></a><a name="ConvertJSONtoXML"></a>Konwertowanie JSON na XML
 Zasady `json-to-xml` konwertuje treści żądania lub odpowiedzi z JSON do XML.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <json-to-xml apply="always" consider-accept-header="false" parse-date="false"/>
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|json-do-xml|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagany|Domyślne|
|----------|-----------------|--------------|-------------|
|apply|Atrybut musi być ustawiony na jedną z następujących wartości.<br /><br /> - zawsze - zawsze stosować konwersję.<br />- content-type-json - konwertuj tylko wtedy, gdy odpowiedź Nagłówek typu zawartości wskazuje obecność JSON.|Tak|Nie dotyczy|
|consider-accept-header|Atrybut musi być ustawiony na jedną z następujących wartości.<br /><br /> - true - zastosuj konwersję, jeśli XML jest wymagane w nagłówku akceptuję żądanie.<br />- false -zawsze stosuje konwersję.|Nie|true|
|data analizy|Gdy ustawiono wartości `false` daty są po prostu kopiowane podczas transformacji|Nie|true|

### <a name="usage"></a>Sposób użycia
 Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, błąd

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="convert-xml-to-json"></a><a name="ConvertXMLtoJSON"></a>Konwertowanie XML na JSON
 Zasady `xml-to-json` konwertuje treści żądania lub odpowiedzi z XML do JSON. Ta zasada może służyć do modernizacji interfejsów API na podstawie usług sieci web tylko w języku XML.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|xml-to-json|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagany|Domyślne|
|----------|-----------------|--------------|-------------|
|Rodzaju|Atrybut musi być ustawiony na jedną z następujących wartości.<br /><br /> - javascript-friendly - przerobiony JSON ma formularz przyjazny dla programistów JavaScript.<br />- direct - przekonwertowany JSON odzwierciedla strukturę oryginalnego dokumentu XML.|Tak|Nie dotyczy|
|apply|Atrybut musi być ustawiony na jedną z następujących wartości.<br /><br /> - zawsze - konwertuj zawsze.<br />- content-type-xml - konwertuj tylko wtedy, gdy odpowiedź Nagłówek typu zawartości wskazuje obecność XML.|Tak|Nie dotyczy|
|consider-accept-header|Atrybut musi być ustawiony na jedną z następujących wartości.<br /><br /> - true - zastosuj konwersję, jeśli JSON jest wymagane w żądaniu Akceptuj nagłówek.<br />- false -zawsze stosuje konwersję.|Nie|true|

### <a name="usage"></a>Sposób użycia
 Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, błąd

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="find-and-replace-string-in-body"></a><a name="Findandreplacestringinbody"></a>Znajdowanie i zastępowanie ciągu w treści
 Zasady `find-and-replace` znajduje podciąg żądania lub odpowiedzi i zastępuje go innym podciągem.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Przykład

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|znajdowanie i zastępowanie|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagany|Domyślne|
|----------|-----------------|--------------|-------------|
|Z|Ciąg do wyszukania.|Tak|Nie dotyczy|
|na|Ciąg zastępujący. Określ ciąg zastępczy o zerowej długości, aby usunąć ciąg wyszukiwania.|Tak|Nie dotyczy|

### <a name="usage"></a>Sposób użycia
 Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, wewnętrznej bazy danych, on-error

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="mask-urls-in-content"></a><a name="MaskURLSContent"></a>Maskowanie adresów URL w treści
 Zasady `redirect-content-urls` ponownie zapisuje (maski) łączy w treści odpowiedzi, tak aby wskazać równoważne łącze za pośrednictwem bramy. Użyj w sekcji wychodzącej, aby ponownie napisać łącza treści odpowiedzi, aby wskazać je do bramy. Użyj w sekcji przychodzącej dla przeciwnego efektu.

> [!NOTE]
>  Ta zasada nie zmienia żadnych `Location` wartości nagłówka, takich jak nagłówki. Aby zmienić wartości nagłówka, użyj zasad [nagłówka zestawu.](api-management-transformation-policies.md#SetHTTPheader)

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<redirect-content-urls />
```

### <a name="example"></a>Przykład

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|przekierowanie-treść-adresy URL|Element główny.|Tak|

### <a name="usage"></a>Sposób użycia
 Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="set-backend-service"></a><a name="SetBackendService"></a>Ustawianie usługi wewnętrznej bazy danych
 Użyj `set-backend-service` zasad, aby przekierować żądanie przychodzące do innej wewnętrznej bazy danych niż określona w ustawieniach interfejsu API dla tej operacji. Ta zasada zmienia podstawowy adres URL usługi wewnętrznej bazy danych żądania przychodzącego na adres określony w zasadach.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<set-backend-service base-url="base URL of the backend service" />
```

lub

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> Jednostkami zaplecza można zarządzać za pomocą [interfejsu API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) zarządzania i [programu PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).

### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <choose>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">
                <set-backend-service base-url="http://contoso.com/api/8.2/" />
            </when>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">
                <set-backend-service base-url="http://contoso.com/api/9.1/" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
W tym przykładzie zasady usługi set backend kieruje żądania na podstawie wartości wersji przekazanej w ciągu kwerendy do innej usługi wewnętrznej bazy danych niż określona w interfejsie API.

Początkowo podstawowy adres URL usługi wewnętrznej bazy danych pochodzi z ustawień interfejsu API. Tak więc `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` adres `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` `http://contoso.com/api/10.4/` URL żądania staje się tam, gdzie jest adres URL usługi wewnętrznej bazy danych określony w ustawieniach interfejsu API.

Po zastosowaniu [instrukcji\><wybierz](api-management-advanced-policies.md#choose) zasad adres URL bazy usługi wewnętrznej bazy `http://contoso.com/api/8.2` `http://contoso.com/api/9.1`danych może zmienić się ponownie na lub , w zależności od wartości parametru kwerendy żądania wersji. Na przykład, jeśli `"2013-15"` wartość jest końcowy `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`adres URL żądania staje się .

Jeśli wymagane jest dalsze przekształcenie żądania, można użyć innych [zasad transformacji.](api-management-transformation-policies.md#TransformationPolicies) Na przykład, aby usunąć parametr kwerendy wersji teraz, gdy żądanie jest kierowane do wewnętrznej bazy danych specyficzne dla wersji, [zasady parametru zestawu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) ciąg może służyć do usuwania teraz nadmiarowy atrybut wersji.

### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
W tym przykładzie zasady kieruje żądanie do wewnętrznej bazy danych sieci szkieletowej usługi, używając ciągu zapytania userId jako klucz partycji i przy użyciu podstawowej repliki partycji.

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|usługa ustawiania zaplecza|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagany|Domyślne|
|----------|-----------------|--------------|-------------|
|adres url bazy|Nowy podstawowy adres URL usługi wewnętrznej bazy danych.|Jeden `base-url` z `backend-id` lub musi być obecny.|Nie dotyczy|
|identyfikator zaplecza|Identyfikator wewnętrznej bazy danych do trasy do. (Encje zaplecza są zarządzane za pośrednictwem [interfejsu API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) i [programu PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).)|Jeden `base-url` z `backend-id` lub musi być obecny.|Nie dotyczy|
|sf-partition-key|Ma zastosowanie tylko wtedy, gdy zaplecze jest usługą sieci szkieletowej usług i jest określona przy użyciu "backend-id". Służy do rozpoznawania określonej partycji z usługi rozpoznawania nazw.|Nie|Nie dotyczy|
|sf-replika-typ|Ma zastosowanie tylko wtedy, gdy zaplecze jest usługą sieci szkieletowej usług i jest określona przy użyciu "backend-id". Określa, czy żądanie powinno przejść do repliki podstawowej lub pomocniczej partycji. |Nie|Nie dotyczy|
|sf-resolve-warunek|Dotyczy tylko wtedy, gdy zaplecze jest usługą sieci szkieletowej usług. Warunek identyfikujący, czy wywołanie wewnętrznej bazy danych sieci szkieletowej usług musi zostać powtórzone z nową rozdzielczością.|Nie|Nie dotyczy|
|sf-service-instance-name|Dotyczy tylko wtedy, gdy zaplecze jest usługą sieci szkieletowej usług. Umożliwia zmianę wystąpień usługi w czasie wykonywania. |Nie|Nie dotyczy|
|sf-listener-name|Ma zastosowanie tylko wtedy, gdy zaplecze jest usługą sieci szkieletowej usług i jest określona przy użyciu "backend-id". Usługa Fabric Reliable Services umożliwia tworzenie wielu odbiorników w usłudze. Ten atrybut jest używany do wybierania określonego odbiornika, gdy wewnętrznej bazy danych niezawodnej usługi ma więcej niż jeden odbiornik. Jeśli ten atrybut nie zostanie określony, usługa API Management spróbuje użyć odbiornika bez nazwy. Odbiornik bez nazwy jest typowe dla niezawodnych usług, które mają tylko jeden odbiornik. |Nie|Nie dotyczy|

### <a name="usage"></a>Sposób użycia
 Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, zaplecze

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="set-body"></a><a name="SetBody"></a>Ustaw obiekt
 Użyj `set-body` zasad, aby ustawić treść wiadomości dla żądań przychodzących i wychodzących. Aby uzyskać dostęp do treści `context.Request.Body` wiadomości, `context.Response.Body`można użyć właściwości lub , w zależności od tego, czy zasady są w sekcji przychodzącej lub wychodzącej.

> [!IMPORTANT]
>  Należy zauważyć, że domyślnie podczas `context.Request.Body` `context.Response.Body`uzyskiwania dostępu do treści wiadomości przy użyciu lub , oryginalna treść wiadomości zostanie utracona i musi być ustawiona przez zwrócenie treści z powrotem w wyrażeniu. Aby zachować zawartość treści, należy ustawić `preserveContent` parametr podczas uzyskiwania dostępu do `true` wiadomości. Jeśli `preserveContent` jest `true` ustawiona i inny obiekt jest zwracany przez wyrażenie, zwracany obiekt jest używany.
>
>  Podczas korzystania z polisy `set-body` należy zwrócić uwagę na następujące kwestie.
>
> - Jeśli używasz `set-body` zasad do zwrócenia nowego lub zaktualizowanego obiektu, `preserveContent` `true` nie musisz ustawiać, ponieważ jawnie dostarczasz nową zawartość treści.
>   -   Zachowanie zawartości odpowiedzi w potoku przychodzącym nie ma sensu, ponieważ nie ma jeszcze odpowiedzi.
>   -   Zachowanie zawartości żądania w potoku wychodzącym nie ma sensu, ponieważ żądanie zostało już wysłane do wewnętrznej bazy danych w tym momencie.
>   -   Jeśli ta zasada jest używana, gdy nie ma treści wiadomości, na przykład w przychodzącym GET, zgłaszany jest wyjątek.

 Aby uzyskać więcej `context.Request.Body`informacji, zobacz , `context.Response.Body`i `IMessage` sekcje w [tabeli zmiennych kontekstu.](api-management-policy-expressions.md#ContextVariables)

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Przykłady

#### <a name="literal-text-example"></a>Przykład tekstu dosłownego

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Przykład uzyskiwania dostępu do treści jako ciąg. Należy zauważyć, że zachowujemy oryginalną treść żądania, dzięki czemu możemy uzyskać do niego dostęp w dalszej części potoku.

```xml
<set-body>
@{ 
    string inBody = context.Request.Body.As<string>(preserveContent: true); 
    if (inBody[0] =='c') { 
        inBody[0] = 'm'; 
    } 
    return inBody; 
}
</set-body>
```

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Przykład uzyskiwania dostępu do treści jako JObject. Należy zauważyć, że ponieważ nie rezerwujemy oryginalnej treści żądania, dostęp do niego później w potoku spowoduje wyjątek.

```xml
<set-body> 
@{ 
    JObject inBody = context.Request.Body.As<JObject>(); 
    if (inBody.attribute == <tag>) { 
        inBody[0] = 'm'; 
    } 
    return inBody.ToString(); 
} 
</set-body>

```

#### <a name="filter-response-based-on-product"></a>Odpowiedź filtru na podstawie produktu
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

### <a name="using-liquid-templates-with-set-body"></a>Korzystanie z szablonów cieczy z osadzoną treścią
Zasady `set-body` można skonfigurować tak, aby używał języka [szablonów płynnych](https://shopify.github.io/liquid/basics/introduction/) do przekształcania treści żądania lub odpowiedzi. Może to być bardzo skuteczne, jeśli trzeba całkowicie zmienić format wiadomości.

> [!IMPORTANT]
> Implementacja Liquid używane `set-body` w zasadach jest skonfigurowany w trybie "C#". Jest to szczególnie ważne podczas wykonywania takich czynności, jak filtrowanie. Na przykład użycie filtru daty wymaga użycia wielkości liter Pascal i formatowania daty języka C#:
>
> {{body.foo.startDateTime| Data:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Aby poprawnie powiązać z treścią XML przy `set-header` użyciu szablonu Liquid, użyj zasad, aby ustawić typ zawartości na aplikację/xml, tekst/xml (lub dowolny typ kończący się na +xml); dla treści JSON musi to być application/json, text/json (lub dowolny typ kończący się na +json).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Konwertowanie JSON na mydło przy użyciu szablonu Liquid
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="transform-json-using-a-liquid-template"></a>Przekształcanie JSON przy użyciu szablonu Liquid
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|set-body|Element główny. Zawiera tekst podstawowy lub wyrażenia, które zwracają treść.|Tak|

### <a name="properties"></a>Właściwości

|Nazwa|Opis|Wymagany|Domyślne|
|----------|-----------------|--------------|-------------|
|szablon|Służy do zmiany trybu tworzenia szablonów, w których będą uruchamiane zasady obiektu zestawu. Obecnie jedyną obsługiwaną wartością jest:<br /><br />- płyn - zasada nadwozia będzie wykorzystywać płynny silnik do tworzenia maszyn |Nie||

Aby uzyskać dostęp do informacji o żądaniu i odpowiedzi, szablon Liquid może powiązać z obiektem kontekstu z następującymi właściwościami: <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>Sposób użycia
 Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, zaplecze

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="set-http-header"></a><a name="SetHTTPheader"></a>Ustawianie nagłówka HTTP
 Zasada `set-header` przypisuje wartość do istniejącego nagłówka odpowiedzi i/lub żądania lub dodaje nowy nagłówek odpowiedzi i/lub żądania.

 Wstawia listę nagłówków HTTP do wiadomości HTTP. Po umieszczeniu w potoku przychodzącym ta zasada ustawia nagłówki HTTP dla żądania przekazywanego do usługi docelowej. Po umieszczeniu w potoku wychodzącym ta zasada ustawia nagłówki HTTP dla odpowiedzi wysyłanej do klienta bramy.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Przykłady

#### <a name="example---adding-header-override-existing"></a>Przykład - dodawanie nagłówka, zastępowanie istniejących

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```
#### <a name="example---removing-header"></a>Przykład - usuwanie nagłówka

```xml
 <set-header name="some header name" exists-action="delete" />
```



#### <a name="forward-context-information-to-the-backend-service"></a>Przesyłanie dalej informacji kontekstowych do usługi wewnętrznej bazy danych
 W tym przykładzie pokazano, jak zastosować zasady na poziomie interfejsu API, aby dostarczyć informacje kontekstowe do usługi wewnętrznej bazy danych. Aby zademonstrować konfigurowanie i używanie tych zasad, zobacz [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) and fast-forward to 10:30. O godzinie 12:10 w portalu dewelopera znajduje się demonstracja wywoływania operacji, w której można zobaczyć zasady w pracy.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Aby uzyskać więcej informacji, zobacz [Wyrażenia zasad i](api-management-policy-expressions.md) [zmienna kontekstowa](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Wiele wartości nagłówka są łączone z ciągiem CSV, na przykład:`headerName: value1,value2,value3`
>
> Wyjątki obejmują znormalizowane nagłówki, które wartości:
> - mogą zawierać przecinki (`User-Agent`, `WWW-Authenticate`, `Proxy-Authenticate`),
> - może zawierać`Cookie`datę `Set-Cookie` `Warning`( , , ),
> - zawierać datę`Date` `Expires`( `If-Modified-Since` `If-Unmodified-Since`, `Last-Modified` `Retry-After`, , , , ).
>
> W przypadku tych wyjątków wiele wartości nagłówka nie zostanie skoncjonowanych w jeden ciąg i zostanie przekazanych jako oddzielne nagłówki, na przykład:`User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|set-header|Element główny.|Tak|
|value|Określa wartość nagłówka, która ma zostać ustawiona. Dla wielu nagłówków o tej `value` samej nazwie dodać dodatkowe elementy.|Nie|

### <a name="properties"></a>Właściwości

|Nazwa|Opis|Wymagany|Domyślne|
|----------|-----------------|--------------|-------------|
|istnieje-działanie|Określa, jaką akcję należy podjąć, gdy nagłówek jest już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> - zastępowanie - zastępuje wartość istniejącego nagłówka.<br />- skip - nie zastępuje istniejącej wartości nagłówka.<br />- dołączanie - dołącza wartość do istniejącej wartości nagłówka.<br />- delete - usuwa nagłówek z żądania.<br /><br /> Gdy ustawiono rejestrowanie `override` wielu wpisów o tej samej nazwie powoduje, że nagłówek jest ustawiany zgodnie ze wszystkimi wpisami (które będą wyświetlane wiele razy); tylko wymienione wartości zostaną ustawione w wyniku.|Nie|override|
|name|Określa nazwę nagłówka, który ma zostać ustawiony.|Tak|Nie dotyczy|

### <a name="usage"></a>Sposób użycia
 Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące, wewnętrznej bazy danych, on-error

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="set-query-string-parameter"></a><a name="SetQueryStringParameter"></a>Ustawianie parametru ciągu kwerendy
 Zasady `set-query-parameter` dodaje, zastępuje wartość lub usuwa parametr ciągu zapytania żądania. Może służyć do przekazywania parametrów kwerendy oczekiwanych przez usługę wewnętrznej bazy danych, które są opcjonalne lub nigdy nie są obecne w żądaniu.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

#### <a name="example"></a>Przykład

```xml

<set-query-parameter name="api-key" exists-action="skip">
  <value>12345678901</value>
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Przesyłanie dalej informacji kontekstowych do usługi wewnętrznej bazy danych
 W tym przykładzie pokazano, jak zastosować zasady na poziomie interfejsu API, aby dostarczyć informacje kontekstowe do usługi wewnętrznej bazy danych. Aby zademonstrować konfigurowanie i używanie tych zasad, zobacz [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) and fast-forward to 10:30. O godzinie 12:10 w portalu dewelopera znajduje się demonstracja wywoływania operacji, w której można zobaczyć zasady w pracy.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Aby uzyskać więcej informacji, zobacz [Wyrażenia zasad i](api-management-policy-expressions.md) [zmienna kontekstowa](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|set-query-parametr|Element główny.|Tak|
|value|Określa wartość parametru zapytania, która ma zostać ustawiona. Dla wielu parametrów kwerendy `value` o tej samej nazwie dodać dodatkowe elementy.|Tak|

### <a name="properties"></a>Właściwości

|Nazwa|Opis|Wymagany|Domyślne|
|----------|-----------------|--------------|-------------|
|istnieje-działanie|Określa akcję, która ma zostać podjęta, gdy parametr zapytania jest już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> - zastępowanie - zastępuje wartość istniejącego parametru.<br />- skip - nie zastępuje istniejącej wartości parametru kwerendy.<br />- dok.<br />- delete - usuwa parametr kwerendy z żądania.<br /><br /> Gdy ustawiono rejestrowanie `override` wielu wpisów o tej samej nazwie powoduje, że parametr kwerendy jest ustawiany zgodnie ze wszystkimi wpisami (które będą wyświetlane wiele razy); tylko wymienione wartości zostaną ustawione w wyniku.|Nie|override|
|name|Określa nazwę parametru kwerendy, który ma zostać ustawiony.|Tak|Nie dotyczy|

### <a name="usage"></a>Sposób użycia
 Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, zaplecze

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="rewrite-url"></a><a name="RewriteURL"></a>Przepisz adres URL
 Zasady `rewrite-uri` konwertuje adres URL żądania z formularza publicznego do formularza oczekiwanego przez usługę sieci web, jak pokazano w poniższym przykładzie.

- Publiczny adres URL -`http://api.example.com/storenumber/ordernumber`

- Adres URL żądania -`http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Ta zasada może być używana, gdy adres URL przyjazny dla ludzi i/lub przeglądarki powinien zostać przekształcony w format adresu URL oczekiwany przez usługę sieci web. Ta zasada musi być stosowana tylko podczas ujawniania alternatywnego formatu adresu URL, takiego jak czyste adresy URL, ponowne adresy URL, przyjazne dla użytkownika adresy URL lub adresy URL przyjazne dla SEO, które są czysto strukturalnymi adresami URL, które nie zawierają ciągu zapytania i zamiast tego zawierają tylko ścieżkę zasobu (po schemacie i urzędzie). Często odbywa się to w celach estetycznych, użyteczności lub optymalizacji pod kątem wyszukiwarek (SEO).

> [!NOTE]
>  Parametry ciągu kwerendy można dodawać tylko przy użyciu zasad. Nie można dodać dodatkowych parametrów ścieżki szablonu w adresie URL ponownego zapisu.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>Przykład

```xml
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put?c=d -->
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" copy-unmatched-params="false" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|przepisać-uri|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagany|Domyślne|
|---------------|-----------------|--------------|-------------|
|szablon|Rzeczywisty adres URL usługi sieci web z dowolnymi parametrami ciągu zapytania. Podczas używania wyrażeń cała wartość musi być wyrażeniem.|Tak|Nie dotyczy|
|kopiowanie-niezrównane params|Określa, czy parametry kwerendy w żądaniu przychodzącym, które nie są obecne w oryginalnym szablonie adresu URL, są dodawane do adresu URL zdefiniowanego przez szablon ponownego zapisu|Nie|true|

### <a name="usage"></a>Sposób użycia
 Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="transform-xml-using-an-xslt"></a><a name="XSLTransform"></a>Przekształcanie języka XML przy użyciu xslt
 Zasady `Transform XML using an XSLT` stosuje transformację XSL do XML w treści żądania lub odpowiedzi.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<xsl-transform>
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:output method="xml" indent="yes" />
        <xsl:param name="User-Agent" />
        <xsl:template match="* | @* | node()">
            <xsl:copy>
                <xsl:if test="self::* and not(parent::*)">
                    <xsl:attribute name="User-Agent">
                        <xsl:value-of select="$User-Agent" />
                    </xsl:attribute>
                </xsl:if>
                <xsl:apply-templates select="* | @* | node()" />
            </xsl:copy>
        </xsl:template>
    </xsl:stylesheet>
  </xsl-transform>
```

### <a name="example"></a>Przykład

```xml
<policies>
  <inbound>
      <base />
  </inbound>
  <outbound>
      <base />
      <xsl-transform>
        <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />
            <!-- Copy all nodes directly-->
            <xsl:template match="node()| @*|*">
                <xsl:copy>
                    <xsl:apply-templates select="@* | node()|*" />
                </xsl:copy>
            </xsl:template>
        </xsl:stylesheet>
    </xsl-transform>
  </outbound>
</policies>
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|xsl-transform|Element główny.|Tak|
|parametr|Służy do definiowania zmiennych używanych w transformacji|Nie|
|xsl:arkusz stylów|Główny element arkusza stylów. Wszystkie elementy i atrybuty zdefiniowane w standardowej [specyfikacji XSLT](https://www.w3.org/TR/xslt)|Tak|

### <a name="usage"></a>Sposób użycia
 Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące, wychodzące

-   **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące tematy:

+ [Zasady w zarządzaniu interfejsami API](api-management-howto-policies.md)
+ [Odwołanie do zasad](api-management-policy-reference.md) dla pełnej listy oświadczeń zasad i ich ustawień
+ [Przykłady zasad](policy-samples.md)
