---
title: Zasady transformacji w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zasad przekształcania dostępne do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 72348085a69746306e40029bc7473df271b60221
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946613"
---
# <a name="api-management-transformation-policies"></a>Zasady transformacji usługi API Management
Ten temat zawiera odwołania do następujących zasad usługi API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasad w usłudze API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="TransformationPolicies"></a> Zasady transformacji

-   [Konwertuj JSON do formatu XML](api-management-transformation-policies.md#ConvertJSONtoXML) — konwertuje żądania lub odpowiedzi treści z formatu JSON do formatu XML.

-   [Konwertuj XML do formatu JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) — konwertuje żądania lub odpowiedzi treści z pliku XML do formatu JSON.

-   [Znajdź i zamień ciąg w treści](api-management-transformation-policies.md#Findandreplacestringinbody) — znajduje żądania lub odpowiedzi podciąg i zastępuje go znakiem inny podciąg.

-   [Maski adresów URL w zawartości](api-management-transformation-policies.md#MaskURLSContent) -ponownie zapisuje (maski) łącza w odpowiedzi treści, aby wskazać równoważne link za pośrednictwem bramy.

-   [Ustaw usługę zaplecza](api-management-transformation-policies.md#SetBackendService) — zmiany do usługi zaplecza dla przychodzącego żądania.

-   [Ustaw treść](api-management-transformation-policies.md#SetBody) -ustawia treść wiadomości dla żądań przychodzących i wychodzących.

-   [Set — nagłówek HTTP](api-management-transformation-policies.md#SetHTTPheader) — przypisuje wartość do istniejącej odpowiedzi i/lub nagłówku żądania lub dodaje nowy nagłówek odpowiedzi i/lub żądania.

-   [Ustaw parametr ciągu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) — dodaje, zastępuje wartość lub usuwa parametr ciągu zapytania żądania.

-   [Ponowne zapisywanie adresów URL](api-management-transformation-policies.md#RewriteURL) — konwertuje adres URL żądania w postaci publicznej do postaci oczekiwanej przez usługę sieci web.

-   [Przekształcanie kodu XML za pomocą XSLT](api-management-transformation-policies.md#XSLTransform) -stosuje przekształcenia XSL do pliku XML w treści żądania lub odpowiedzi.

##  <a name="ConvertJSONtoXML"></a> Konwertuj JSON do pliku XML
 `json-to-xml` Zasad konwertuje treści żądania lub odpowiedzi z formatu JSON do formatu XML.

### <a name="policy-statement"></a>Deklaracja zasad

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

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|JSON do pliku xml|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

|Name (Nazwa)|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|zastosuj|Ten atrybut musi być równa jeden z następujących wartości.<br /><br /> — zawsze — Zawsze stosuj konwersji.<br />Konwersja zawartości — typ json — tylko wtedy, gdy nagłówka odpowiedzi Content-Type wskazuje obecność JSON.|Yes|ND|
|consider-accept-header|Ten atrybut musi być równa jeden z następujących wartości.<br /><br /> — wartość true — zastosowania konwersji, jeśli w żądaniu nagłówka Accept żądania JSON.<br />— wartość false — Zawsze stosuj konwersji.|Nie|true|
|Data analizy|Po ustawieniu `false` wartości dat, po prostu są kopiowane podczas przekształcania|Nie|true|

### <a name="usage"></a>Sposób użycia
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego, ruchu wychodzącego, — błąd

-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

##  <a name="ConvertXMLtoJSON"></a> Konwertuj XML do formatu JSON
 `xml-to-json` Zasad konwertuje treści żądania lub odpowiedzi z pliku XML do formatu JSON. Te zasady mogą służyć do modernizowanie interfejsów API opartych na usługach sieci web XML — tylko do wewnętrznej bazy danych.

### <a name="policy-statement"></a>Deklaracja zasad

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

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|xml-to-json|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

|Name (Nazwa)|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|rodzaj|Ten atrybut musi być równa jeden z następujących wartości.<br /><br /> javascript — przyjaznego - przekonwertowany za pomocą pliku JSON ma postać przyjazne dla deweloperów języka JavaScript.<br />-direct - przekonwertowanego JSON odzwierciedla strukturę oryginalnego dokumentu XML.|Yes|ND|
|zastosuj|Ten atrybut musi być równa jeden z następujących wartości.<br /><br /> — zawsze — Konwertuj zawsze.<br />Konwersja zawartości — typ xml — tylko wtedy, gdy nagłówka odpowiedzi Content-Type wskazuje obecność kodu XML.|Yes|ND|
|consider-accept-header|Ten atrybut musi być równa jeden z następujących wartości.<br /><br /> — wartość true — zastosowania konwersji, jeśli w żądaniu nagłówka Accept żądania XML.<br />— wartość false — Zawsze stosuj konwersji.|Nie|true|

### <a name="usage"></a>Sposób użycia
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego, ruchu wychodzącego, — błąd

-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

##  <a name="Findandreplacestringinbody"></a> Znajdź i zamień ciąg w treści
 `find-and-replace` Zasad znajduje żądania lub odpowiedzi podciąg i zastępuje go znakiem inny podciąg.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Przykład

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|find-and-replace|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

|Name (Nazwa)|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|z|Ciąg do wyszukania.|Yes|ND|
|na|Ciąg zastępujący. Określ zero długość ciąg zastępujący usunąć ciąg wyszukiwania.|Yes|ND|

### <a name="usage"></a>Sposób użycia
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** zaplecza dla ruchu przychodzącego, wychodzący — błąd

-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

##  <a name="MaskURLSContent"></a> Maska adresów URL, w zawartości
 `redirect-content-urls` Zasad ponownie zapisuje łącza (maski) w treści odpowiedzi, aby wskazać równoważne link za pośrednictwem bramy. Użyj w sekcji ruchu wychodzącego do ponownego napisania łącza treść odpowiedzi do były wskaż bramy. Na użytek w sekcji dla ruchu przychodzącego odwrotny efekt.

> [!NOTE]
>  Ta zasada nie zmienia żadnych wartości nagłówka takie `Location` nagłówków. Aby zmienić wartości nagłówka, użyj [set-header](api-management-transformation-policies.md#SetHTTPheader) zasad.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<redirect-content-urls />
```

### <a name="example"></a>Przykład

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|redirect-content-urls|Element główny.|Yes|

### <a name="usage"></a>Sposób użycia
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego, ruchu wychodzącego

-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

##  <a name="SetBackendService"></a> Ustawianie usługi wewnętrznej bazy danych
 Użyj `set-backend-service` zasad, aby przekierować żądanie przychodzące do wewnętrznej bazy danych innej niż określona w ustawieniach interfejsu API dla tej operacji. Ta zasada zmienia wewnętrznej bazy danych usługi podstawowy adres URL żądania przychodzącego z określoną w zasadach.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<set-backend-service base-url="base URL of the backend service" />
```

lub

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> Jednostki zaplecza mogą być zarządzane za pośrednictwem funkcji zarządzania [API](https://docs.microsoft.com/en-us/rest/api/apimanagement/backend) i [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).

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
W tym przykładzie Ustaw zasady usługi wewnętrznej bazy danych kieruje żądania na podstawie wartości wersji przekazany ciąg zapytania do usługi zaplecza inny niż zestaw określony w interfejsie API.

Początkowo wewnętrznej bazy danych usługi podstawowy adres URL jest tworzony na podstawie ustawień interfejsu API. Dlatego adres URL żądania `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` staje się `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` gdzie `http://contoso.com/api/10.4/` jest adresem URL usługi wewnętrznej bazy danych, które zostały określone w ustawieniach interfejsu API.

Gdy [< Wybierz\> ](api-management-advanced-policies.md#choose) deklaracji zasad są stosowane wewnętrznej bazy danych usługi podstawowy adres URL może być ponownie zmienić albo `http://contoso.com/api/8.2` lub `http://contoso.com/api/9.1`, w zależności od wartości parametru zapytania żądania wersji. Na przykład, jeśli wartość jest `"2013-15"` ostatecznego żądania staje się adres URL `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.

Jeśli dodatkowo przekształcenie żądania jest żądany, inne [zasad przekształcania](api-management-transformation-policies.md#TransformationPolicies) mogą być używane. Na przykład, aby usunąć parametr zapytania wersję teraz, gdy żądanie jest przesyłane do zaplecza określonej wersji [Ustaw parametr ciągu zapytania](api-management-transformation-policies.md#SetQueryStringParameter) zasad można usunąć atrybut version teraz nadmiarowe.

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
W tym przykładzie zasady kieruje żądanie do usługi zaplecza Service fabric, jako klucza partycji jest ciągu zapytania identyfikatora użytkownika, a następnie używając podstawową replikę partycji.

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|set-backend-service|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

|Name (Nazwa)|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|podstawowy adres url|Nowe zaplecze podstawowy adres URL usługi.|Jedną z `base-url` lub `backend-id` musi być obecny.|ND|
|Identyfikator wewnętrznej bazy danych|Identyfikator zaplecza do kierowania do. (Jednostki zaplecza są zarządzane za pośrednictwem [API](https://docs.microsoft.com/en-us/rest/api/apimanagement/backend) i [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).)|Jedną z `base-url` lub `backend-id` musi być obecny.|ND|
|sf-partition-key|Dotyczy tylko gdy wewnętrznej bazy danych jest usługi Service Fabric i jest określony przy użyciu "backend-id". Używana do rozpoznawania określonej partycji z usługi rozpoznawania nazw.|Nie|ND|
|sf-replica-type|Dotyczy tylko gdy wewnętrznej bazy danych jest usługi Service Fabric i jest określony przy użyciu "backend-id". Kontroluje, czy żądanie należy przejść do podstawowej lub pomocniczej replice partycji. |Nie|ND|
|sf-resolve-condition|Dotyczy tylko w przypadku wewnętrznej bazy danych usługi Service Fabric. Warunek, identyfikowanie, jeśli wywołanie do zaplecza usługi Service Fabric musi być powtarzana z nowego rozwiązania.|Nie|ND|
|sf-service-instance-name|Dotyczy tylko w przypadku wewnętrznej bazy danych usługi Service Fabric. Pozwala zmienić wystąpień usługi w czasie wykonywania. |Nie|ND|
|sf-listener-name|Dotyczy tylko gdy wewnętrznej bazy danych jest usługi Service Fabric i jest określony przy użyciu "backend-id". Usług Reliable Services usługi Service Fabric umożliwia tworzenie wiele odbiorników w usłudze. Ten atrybut jest używany do wybierz określony odbiornik, gdy wewnętrznej bazy danych usługi Reliable Service ma więcej niż jeden odbiornik. Jeśli ten atrybut nie jest określony, API Management spróbuje użyć odbiornika bez nazwy. Odbiornik bez nazwy jest typowy dla usług Reliable Services, który ma tylko jeden odbiornik. |Nie|ND|

### <a name="usage"></a>Sposób użycia
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** zaplecza dla ruchu przychodzącego

-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

##  <a name="SetBody"></a> Ustaw treść
 Użyj `set-body` zasady można ustawić treść komunikatu dla żądań przychodzących i wychodzących. Dostęp do treści wiadomości, można użyć do `context.Request.Body` właściwości lub `context.Response.Body`, w zależności od tego, czy zasady znajduje się w sekcji ruchu przychodzącego lub wychodzącego.

> [!IMPORTANT]
>  Należy zauważyć, że domyślnie, gdy uzyskujesz dostęp do wiadomości, treści, za pomocą `context.Request.Body` lub `context.Response.Body`, oryginalny treść komunikatu zostanie utracony i musi być ustawiona, zwracając treści w wyrażeniu. Aby zachować zawartość treści, należy ustawić `preserveContent` parametr `true` podczas uzyskiwania dostępu do wiadomości. Jeśli `preserveContent` ustawiono `true` i różnych treści jest zwracanych przez wyrażenie, treść zwrócony jest używany.
> 
>  Należy pamiętać następujące uwagi, korzystając z `set-body` zasad.
> 
> - Jeśli używasz `set-body` zasady, aby zwrócić nowych lub zaktualizowanych treści, nie musisz ustawić `preserveContent` do `true` ponieważ są jawnie dostarczenie nowej zawartości treści.
>   -   Zachowywanie zawartości odpowiedzi w przychodzących potoku nie ma sensu, ponieważ nie jest jeszcze żadnej odpowiedzi.
>   -   Zachowywanie zawartości żądania w potoku wychodzącego nie ma sensu, ponieważ żądania została już wysłana do wewnętrznej bazy danych na tym etapie.
>   -   Jeśli zasada ta jest używana po treści wiadomości, na przykład w GET dla ruchu przychodzącego, jest zgłaszany wyjątek.

 Aby uzyskać więcej informacji, zobacz `context.Request.Body`, `context.Response.Body`i `IMessage` sekcje w [zmiennej kontekstowej](api-management-policy-expressions.md#ContextVariables) tabeli.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Przykłady

#### <a name="literal-text-example"></a>Przykładowy tekst dosłowny

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Przykład: uzyskiwanie dostępu do treść jako ciąg. Należy pamiętać, że są firma Microsoft zachowaniu oryginalnej treści żądania tak, aby firma Microsoft dostęp do niego później w potoku.

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

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Przykład: dostęp do treści jako jobject zostanie opróżniony. Należy zauważyć, że ponieważ rezerwujesz firma Microsoft nie oryginalnego treści żądania, uzyskiwania dostępu do później w potoku spowoduje wyjątek.

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

#### <a name="filter-response-based-on-product"></a>Filtr odpowiedzi oparte na produkt
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

### <a name="using-liquid-templates-with-set-body"></a>Za pomocą szablonów Liquid z treścią zestawu
`set-body` Zasad może być skonfigurowana do używania [Liquid](https://shopify.github.io/liquid/basics/introduction/) języka szablonów do przekształcania treści żądania lub odpowiedzi. Może to być bardzo skuteczne, jeśli chcesz całkowicie zmienić kształt format komunikatu.

> [!IMPORTANT]
> Implementacja cieczy używane w `set-body` zasada została skonfigurowana w trybie"C#". Jest to szczególnie ważne podczas wykonywania czynności, takie jak filtrowanie. Na przykład za pomocą filtru dat wymaga użycia Pascal wielkość liter w wyrazie i C# daty, np. formatowania:
>
> {{body.foo.startDateTime| Date:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Aby można było poprawnie powiązać treści XML przy użyciu Liquid szablonu, należy użyć `set-header` zasady można ustawić typu zawartości albo aplikacja/xml, tekstu/xml (lub dowolnego typu, kończąc + xml); treść JSON, application/json, musi to być tekst/json (lub dowolnego typu, kończąc + JSON).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Konwertuj JSON przy użyciu szablonu Liquid protokołu SOAP
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

#### <a name="transform-json-using-a-liquid-template"></a>Przekształcanie kodu JSON przy użyciu szablonu Liquid
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|Ustaw treść|Element główny. Zawiera tekst podstawowy lub wyrażeń, które zwraca treść.|Yes|

### <a name="properties"></a>Właściwości

|Name (Nazwa)|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|szablon|Używane, aby zmienić tryb tworzenia szablonów, który Ustawianie treść zasad zostanie wykonany w. Obecnie jest to jedyna obsługiwana wartość:<br /><br />-liquid — Ustawianie zasad treści użyje aparat szablonów liquid |Nie|cieczy|

Do uzyskania dostępu do informacji na temat żądań i odpowiedzi, Liquid szablonu można powiązać obiektu context, z następującymi właściwościami: <br />
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
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego, ruchu wychodzącego, wewnętrznej bazy danych

-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

##  <a name="SetHTTPheader"></a> Set — nagłówek HTTP
 `set-header` Zasad przypisuje wartość do istniejącej odpowiedzi i/lub nagłówku żądania lub dodaje nowy nagłówek odpowiedzi i/lub żądania.

 Wstawia lista nagłówków HTTP do komunikatu protokołu HTTP. Po umieszczeniu w potoku usługi w przychodzących, ta zasada ustawia nagłówki HTTP dla żądania został przekazany do docelowej usługi. Po umieszczeniu w potoku wychodzącego, ta zasada ustawia nagłówki HTTP dla odpowiedzi są wysyłane do klienta bramy.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```

#### <a name="forward-context-information-to-the-backend-service"></a>Informacje o kontekście do przodu do usługi zaplecza
 Ten przykład przedstawia sposób zastosowania zasad na poziomie interfejsu API, aby podać informacje o kontekście do usługi zaplecza. Demonstracyjne, konfigurowania i korzystania z tych zasad, zobacz [Cloud Cover odcinek 177: Więcej funkcji zarządzania interfejsu API za pomocą Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przewijanie do przodu do 10:30. Od 12:10 ma pokaz wywołanie operacji w portalu dla deweloperów, tam, gdzie zobaczysz zasad w miejscu pracy.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Aby uzyskać więcej informacji, zobacz [wyrażenia zasad](api-management-policy-expressions.md) i [zmiennej kontekstowej](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Wiele wartości nagłówka są łączone na przykład z ciągiem CSV: `headerName: value1,value2,value3`
>
> Wyjątki obejmują standardowe nagłówki, które wartości:
> - może zawierać przecinków (`User-Agent`, `WWW-Authenticate`, `Proxy-Authenticate`),
> - może zawierać datę (`Cookie`, `Set-Cookie`, `Warning`),
> - zawiera daty (`Date`, `Expires`, `If-Modified-Since`, `If-Unmodified-Since`, `Last-Modified`, `Retry-After`).
>
> W przypadku tych wyjątków wielu wartości nagłówka nie będzie można połączyć w jeden ciąg i zostaną przekazane jako osobne nagłówków, na przykład: `User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|set-header|Element główny.|Yes|
|value|Określa wartość nagłówka do ustawienia. Dla wiele nagłówków o takiej samej nazwie, Dodaj dodatkowe `value` elementów.|Yes|

### <a name="properties"></a>Właściwości

|Name (Nazwa)|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|istnieje akcja|Określa, jakie działania podejmowane w momencie nagłówek jest już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> -override - zastępuje wartość istniejący nagłówek.<br />-skip — nie zastępuje istniejącą wartość nagłówka.<br />-dołączania - dołącza wartość do istniejącej wartości nagłówka.<br />-delete - Usuwa nagłówek z żądania.<br /><br /> Po ustawieniu `override` rejestrowanie wiele wpisów z tej samej nazwie wyników w nagłówku ustawiania zgodnie ze wszystkich wpisów, (które zostaną wyświetlone wiele razy); tylko wymienioną wartość zostanie ustawiona w wyniku.|Nie|zastąpienie|
|name|Określa nazwę nagłówka do ustawienia.|Yes|ND|

### <a name="usage"></a>Sposób użycia
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** zaplecza dla ruchu przychodzącego, wychodzący — błąd

-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

##  <a name="SetQueryStringParameter"></a> Parametr ciągu zapytania dla zestawu
 `set-query-parameter` Zasady dodaje zastępuje wartość, projektu i zlecania usuwa parametr ciągu zapytania. Może służyć do przekazania zapytania parametry oczekiwany przez usługi zaplecza, które są opcjonalne, lub nigdy nie są obecne w żądaniu.

### <a name="policy-statement"></a>Deklaracja zasad

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

### <a name="examples"></a>Przykłady

#### <a name="example"></a>Przykład

```xml

<set-query-parameter>
  <parameter name="api-key" exists-action="skip">
    <value>12345678901</value>
  </parameter>
  <!-- for multiple parameters with the same name add additional value elements -->
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Informacje o kontekście do przodu do usługi zaplecza
 Ten przykład przedstawia sposób zastosowania zasad na poziomie interfejsu API, aby podać informacje o kontekście do usługi zaplecza. Demonstracyjne, konfigurowania i korzystania z tych zasad, zobacz [Cloud Cover odcinek 177: Więcej funkcji zarządzania interfejsu API za pomocą Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) i szybkie przewijanie do przodu do 10:30. Od 12:10 ma pokaz wywołanie operacji w portalu dla deweloperów, tam, gdzie zobaczysz zasad w miejscu pracy.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Aby uzyskać więcej informacji, zobacz [wyrażenia zasad](api-management-policy-expressions.md) i [zmiennej kontekstowej](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|set-query-parameter|Element główny.|Yes|
|value|Określa wartość parametru zapytania do ustawienia. Dla wielu parametrów zapytania o takiej samej nazwie, Dodaj dodatkowe `value` elementów.|Yes|

### <a name="properties"></a>Właściwości

|Name (Nazwa)|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|istnieje akcja|Określa, jaką akcję należy podjąć, gdy parametr zapytania jest już określony. Ten atrybut musi mieć jedną z następujących wartości.<br /><br /> -override - zastępuje wartość parametru istniejących.<br />-skip — nie zastępuje istniejącą wartość parametru zapytania.<br />-dołączania - dołącza wartość do istniejącej wartości parametrów zapytania.<br />-delete - Usuwa parametr zapytania z żądania.<br /><br /> Po ustawieniu `override` rejestrowanie wiele wpisów z taką samą nazwę wyniki w parametrze zapytania zostanie ustawiony zgodnie ze wszystkich wpisów, (które zostaną wyświetlone wiele razy); tylko wymienioną wartość zostanie ustawiona w wyniku.|Nie|zastąpienie|
|name|Określa nazwę parametru zapytania do ustawienia.|Yes|ND|

### <a name="usage"></a>Sposób użycia
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** zaplecza dla ruchu przychodzącego

-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

##  <a name="RewriteURL"></a> Ponowne zapisywanie adresów URL
 `rewrite-uri` Zasad konwertuje wartość adresie URL żądania w postaci publicznej do postaci oczekiwanej przez usługę sieci web jak pokazano w poniższym przykładzie.

- Publiczny adres URL — `http://api.example.com/storenumber/ordernumber`

- Adres URL żądania — `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Ta zasada może służyć podczas ludzi i/lub przeglądarki przyjaznego adresu URL powinny zostać przekształcone na format adresu URL oczekiwanej przez usługę sieci web. Ta zasada tylko musi zostać zastosowana podczas udostępniania innego formatu adresu URL, takich jak czyste adresy URL, RESTful adresów URL, adresy URL przyjazne dla użytkownika lub adresy URL przyjaznych dla aparatów wyszukiwania, które są wyłącznie strukturalnych adresów URL, które zawiera ciąg zapytania, a zamiast tego zawiera tylko ścieżka (zasobów Po schemat i Urząd). Jest to często wykonywane estetycznych użyteczność i aparat wyszukiwania (SEO) optymalizacji.

> [!NOTE]
>  Można dodawać tylko parametry ciągu zapytania za pomocą zasad. Nie można dodać szablon dodatkowe parametry ścieżki w adresie ponowne zapisywanie adresów URL.

### <a name="policy-statement"></a>Deklaracja zasad

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

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|rewrite-uri|Element główny.|Yes|

### <a name="attributes"></a>Atrybuty

|Atrybut|Opis|Wymagane|Domyślne|
|---------------|-----------------|--------------|-------------|
|szablon|Rzeczywisty adres URL usługi internetowej za pomocą parametrów ciągu zapytania. Korzystając z wyrażeń, wartości całkowitej musi być wyrażeniem.|Yes|ND|
|copy-unmatched-params|Określa, czy parametry zapytania w żądaniu przychodzącym nie znajduje się w oryginalnym szablonie adres URL są dodawane do adresu URL zdefiniowane w szablonie ponownego napisania|Nie|true|

### <a name="usage"></a>Sposób użycia
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego

-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

##  <a name="XSLTransform"></a> Przekształcanie kodu XML za pomocą XSLT
 `Transform XML using an XSLT` Podlega zasadom przekształcenia XSL do pliku XML w treści żądania lub odpowiedzi.

### <a name="policy-statement"></a>Deklaracja zasad

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

|Name (Nazwa)|Opis|Wymagane|
|----------|-----------------|--------------|
|xsl-transform|Element główny.|Yes|
|parametr|Używane do definiowania zmiennych używanych w transformacji|Nie|
|xsl:stylesheet|Elemencie głównym arkusza stylów. Wszystkie elementy i atrybuty zdefiniowane w ramach zgodne ze standardem [specyfikację XSLT](https://www.w3.org/TR/xslt)|Yes|

### <a name="usage"></a>Sposób użycia
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Sekcje zasad:** dla ruchu przychodzącego, ruchu wychodzącego

-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz następujące tematy:

+ [Zasady usługi API Management](api-management-howto-policies.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)
