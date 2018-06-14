---
title: Buforowanie niestandardowych w usłudze Azure API Management
description: Dowiedz się, jak i pamięci podręcznej elementów przez klucz w usłudze Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 838850d38c9df51fabcf620831371bed401e9492
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29376035"
---
# <a name="custom-caching-in-azure-api-management"></a>Buforowanie niestandardowych w usłudze Azure API Management
Usługa Azure API Management ma wbudowaną obsługę [buforowanie odpowiedzi HTTP](api-management-howto-cache.md) przy użyciu adresu URL zasobu jako klucz. Klucz może być modyfikowany przez nagłówki żądania przy użyciu `vary-by` właściwości. Jest to przydatne w przypadku buforowanie całej odpowiedzi HTTP (alias oświadczenia), ale czasami jest przydatne do pamięci podręcznej tylko część reprezentacji. Nowy [pamięci podręcznej wyszukiwania wartości](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) i [-magazynu wartość w pamięci podręcznej](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) zasady umożliwiają przechowywanie i pobieranie dowolnych fragmentów danych z poziomu definicji zasad. Tę możliwość również dodaje wartość poprzednio wprowadzone [żądanie wysłania](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) zasad ponieważ teraz może buforować odpowiedzi z usług zewnętrznych.

## <a name="architecture"></a>Architektura
Zarządzanie interfejsami API usługi używa danych dzierżawy udostępnionej pamięci podręcznej, aby jako skali do wielu jednostkach nadal uzyskać dostęp do tej samej dane z pamięci podręcznej. Podczas pracy z wdrożenia w przypadku istnieją niezależne pamięci podręcznych w poszczególnych regionach. Należy koniecznie traktuje jako magazynu danych, gdzie jest tylko źródło dla elementu informacji o pamięci podręcznej. Jeśli została i później chcę korzystać z wdrożenia w przypadku klientów z użytkowników, którzy podróżują mogą stracić dostęp do pamięci podręcznej danych.

## <a name="fragment-caching"></a>Buforowanie fragmentu
Brak niektórych przypadkach, gdy odpowiedzi zwracanych zawiera pewną część danych, jest kosztowna ustalić, który jeszcze pozostaje świeże sensownym czasie. Na przykład należy wziąć pod uwagę usługę utworzony przez linii lotniczych, który zawiera informacje dotyczące zastrzeżenia transmitowane, stan transmitowane itp. Jeśli użytkownik jest członkiem program punktów airlines, musi również informacje dotyczące ich bieżący stan i zebraniu przebiegu. Te informacje dotyczące użytkowników mogą być przechowywane w innym systemie, ale może być pożądane, aby uwzględnić go w odpowiedzi dotyczące stanu transmitowane i zastrzeżenia zwrócone. Można to zrobić w procesie zwanym buforowanie fragmentu. Reprezentacja głównej mogą być zwracane z serwera pochodzenia przy użyciu określonego rodzaju token, aby wskazać, gdzie jest informacji dotyczących użytkownika ma zostać wstawiony. 

Należy wziąć pod uwagę następujące odpowiedzi JSON z zaplecza interfejsu API.

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

I dodatkowej zasobu pod adresem `/userprofile/{userid}` takie, jak,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Aby sprawdzić informacje odpowiedniego użytkownika, aby uwzględnić, zarządzanie interfejsami API musi określić kto jest użytkownika końcowego. Ten mechanizm jest zależne od implementacji. Na przykład używam `Subject` oświadczeń z `JWT` tokenu. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

Zarządzanie interfejsami API magazynów `enduserid` wartość w zmiennej kontekstu do późniejszego użycia. Następnym krokiem jest ustalenie, jeśli poprzednie żądanie już pobrane informacje o użytkowniku i zapisana w pamięci podręcznej. W tym celu używa interfejsu API zarządzania `cache-lookup-value` zasad.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Jeśli w pamięci podręcznej, która odpowiada wartości klucza, a następnie nr nie ma wpisu `userprofile` zmiennej kontekstu, która jest tworzona. Zarządzanie interfejsami API sprawdza została pomyślnie użyta wyszukiwania `choose` sterowanie przepływem zasad.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Jeśli `userprofile` zmiennej kontekstu, która nie istnieje, a następnie przechodzi do interfejsu API zarządzania do przesyłania żądania HTTP można go pobrać.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

Zarządzanie interfejsami API używa `enduserid` do konstruowania adresu URL do zasobu profilu użytkownika. Zarządzanie interfejsami API ma odpowiedzi, pobiera treść tekstu z odpowiedzi i zapisze go do zmiennej kontekstu.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Aby uniknąć API Management z ponownie, co czyni to żądanie HTTP, gdy ten sam użytkownik zgłasza żądanie innego, można określić do przechowywania w pamięci podręcznej profil użytkownika.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

Zarządzanie interfejsami API przechowuje wartość w pamięci podręcznej używa dokładnie tego samego klucza, który API Management pierwotnie nastąpiła próba pobrania go. Czas trwania, który wybiera API Management do przechowywania wartości powinny być oparte na temat często zmiany informacji i sposobu odporny na błędy użytkowników są nieaktualne do informacji. 

Należy koniecznie należy pamiętać, że pobieranie z pamięci podręcznej jest nadal poza procesem, żądanie sieciowe i potencjalnie można nadal dodawać dziesiątki w milisekundach czas oczekiwania na żądanie. Zalety trybu podczas ustalania, czy informacje o profilu użytkownika trwa dłużej niż ze względu na potrzeby bazy danych zapytania lub zagregowanych informacji z wielu zapleczy.

Jest ostatnim krokiem w procesie można zaktualizować zwrócona odpowiedź o informacje o profilu użytkownika.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Można wybrać do znaki cudzysłowu należy uwzględnić jako część tokenu, dzięki czemu nawet wtedy, gdy Zastąp nie zachodzi, dane są nadal poprawne dane JSON.  

Te kroki są łączone ze sobą, wynik końcowy po zasad, która wygląda podobnie do następującego.

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

Takie podejście buforowania jest używany głównie w witrynach sieci web w przypadku gdy składa HTML po stronie serwera, dzięki czemu mogą być renderowane jako pojedynczej strony. Może być również przydatne w interfejsów API, gdzie klienci nie buforowanie HTTP po stronie klienta lub jest wprowadzania że odpowiedzialność na kliencie.

Można również wykonać tego samego rodzaju fragmentu buforowanie na serwerach sieci web zaplecza przy użyciu Redis, buforowanie serwera, jednak przy użyciu usługi API Management do wykonywania tej pracy jest przydatne, gdy buforowane fragmenty pochodzą z różnych zapleczy niż podstawowy odpowiedzi.

## <a name="transparent-versioning"></a>Przechowywanie wersji przezroczyste
Jest typowym rozwiązaniem w przypadku wielu wersji inną implementację interfejsu API do obsługi w dowolnym momencie. Na przykład w celu obsługi różnych środowiskach (deweloperów, testu, produkcji, itp.) lub do obsługi starszych wersji interfejsu API, aby zapewnić czas dla konsumentów interfejsu API przeprowadzić migrację do nowszych wersji. 

Jednym z rozwiązań do obsługi, zwalniając deweloperom klienta zmiany adresów URL z `/v1/customers` do `/v2/customers` jest do przechowywania danych profilu konsumenta wersji interfejsu API obecnie chcą korzystać, a następnie wywołać URL odpowiedniej wewnętrznej bazy danych. Można ustalić adresu URL poprawne wewnętrznej bazy danych do wywołania dla określonego klienta, należy zbadać niektóre dane konfiguracji. Buforowanie danych konfiguracji, zarządzanie interfejsami API można zminimalizować spadek wydajności prowadzenia tego wyszukiwania.

Pierwszym krokiem jest identyfikator używany do konfigurowania żądanej wersji. W tym przykładzie wybrano skojarzyć tę wersję produktu klucza subskrypcji. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

Zarządzanie interfejsami API następnie wykonuje wyszukiwanie pamięci podręcznej, aby zobaczyć, czy już pobrać wersji klienta żądany.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Następnie zarządzanie interfejsami API sprawdza, jeśli go nie znaleziono go w pamięci podręcznej.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Jeśli zarządzanie interfejsami API nie znaleziono jej, zarządzanie interfejsami API pobiera go.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

Wyodrębnienie z odpowiedzi tekstu treści odpowiedzi.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Zapisz go w pamięci podręcznej do użycia w przyszłości.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

I na koniec zaktualizuj adres URL zaplecza, aby wybrać wersję żądanej przez klienta usługi.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Zakończenie zasad jest następujący:

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

Umożliwiając użytkownikom interfejsu API niewidocznie kontroli wersji wewnętrznej bazy danych jest uzyskiwany przez klientów bez konieczności aktualizacji i wdrożenie klientów jest atrakcyjny rozwiązaniem rozwiązuje wiele problemów przechowywanie wersji interfejsu API.

## <a name="tenant-isolation"></a>Izolacji dzierżawców
W przypadku większych i wielodostępne wdrożeń niektóre firmy utworzenie oddzielnych grup dzierżawcy na różnych wdrożeń sprzętu wewnętrznej bazy danych. Pozwala to zmniejszyć liczbę klientów, którzy mają wpływ problemem sprzętowym do wewnętrznej bazy danych. Umożliwia również nowe wersje oprogramowania na wprowadzanie etapami. W idealnym przypadku tej architektury wewnętrznej bazy danych powinny być przezroczyste konsumentom interfejsu API. Można to osiągnąć w sposób podobny do przechowywania wersji przezroczysty, ponieważ jest on oparty na tę samą metodę manipulowania URL wewnętrznej bazy danych przy użyciu stan konfiguracji na klucz interfejsu API.  

Zamiast zwracać preferowaną wersję interfejsu API dla każdego klucza subskrypcji, zwróci identyfikator, który dotyczy dzierżawcy grupy przypisanej sprzętu. Ten identyfikator może służyć do skonstruowania URL odpowiedniej wewnętrznej bazy danych.

## <a name="summary"></a>Podsumowanie
Swobody obsługi pamięci podręcznej zarządzania interfejsu API Azure do przechowywania każdego typu danych umożliwia wydajne dostępu do danych konfiguracji, które mogą mieć wpływ na sposób przetwarzania przychodzącego żądania. Można go również używane do przechowywania fragmenty danych, które można rozszerzyć odpowiedzi zwrócone przez interfejs API zaplecza.
