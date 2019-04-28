---
title: Buforowanie niestandardowe w usłudze Azure API Management
description: Dowiedz się, jak buforowania elementów według klucza w usłudze Azure API Management
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
ms.openlocfilehash: 922ab731ccd76e6a1336d61abe4b0251e358beb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60780825"
---
# <a name="custom-caching-in-azure-api-management"></a>Buforowanie niestandardowe w usłudze Azure API Management
Usługa API Management ma wbudowaną obsługę [buforowanie odpowiedzi HTTP](api-management-howto-cache.md) przy użyciu adresu URL zasobu jako klucza. Klucz może być modyfikowana przez nagłówki żądania przy użyciu `vary-by` właściwości. Jest to przydatne buforowanie całej odpowiedzi HTTP (zwane również reprezentacje), ale czasami przydaje się tylko w pamięci podręcznej część reprezentację. Nowy [wartość w pamięci podręcznej — wyszukiwanie](/azure/api-management/api-management-caching-policies#GetFromCacheByKey) i [wartość w przypadku magazynu pamięci podręcznej](/azure/api-management/api-management-caching-policies#StoreToCacheByKey) zasady zapewniają możliwość przechowywania i pobierania dowolnych rodzajów danych z poziomu definicji zasad. Ta możliwość również dodaje wartość do uprzednio wprowadzonych [żądań wysłania](/azure/api-management/api-management-advanced-policies#SendRequest) zasad, ponieważ teraz można buforować odpowiedzi z usług zewnętrznych.

## <a name="architecture"></a>Architektura
Usługa API Management używa usługi danych dzierżawy udostępnionej pamięci podręcznej tak, aby jako skalowanie do wielu jednostek nadal uzyskać dostęp do tej samej buforowanych danych. Podczas pracy z wdrożenie w wielu regionach istnieją niezależnie od pamięci podręcznej w każdym z regionów. Należy traktuje pamięć podręczną do przechowywania danych, w których jest tylko źródło jakiegoś fragmentu informacji. Jeśli została, a później zdecydowała się korzystać z zalet wdrożenie w wielu regionach, klienci z użytkowników, którzy podróżują mogą stracić dostęp do pamięci podręcznej danych.

## <a name="fragment-caching"></a>Buforowanie fragmentu
Istnieją niektórych przypadki, gdzie odpowiedzi zwracanych zawierać niektórych części danych, jest kosztowna określić, która jeszcze pozostaje świeże rozsądnym czasie. Na przykład należy wziąć pod uwagę w oferująca linii lotniczej, która zawiera informacje dotyczące stanu lotu Rezerwacje lotów, itp. Jeśli użytkownik jest członkiem programu punktów linii lotniczych, będzie również musiał informacje odnoszące się do ich bieżący stan i zgromadzonych przebiegu. Te informacje dotyczące użytkowników mogą być przechowywane w innym systemie, ale może być pożądane, aby uwzględnić go w odpowiedzi zostały zwrócone informacje stanu lotu i zastrzeżenia. Można to zrobić w procesie nazywanym fragmentu pamięci podręcznej. Reprezentacja podstawowego mogą być zwracane z serwera pochodzenia przy użyciu pewnego rodzaju token, aby wskazać, gdzie ma zostać wstawiony informacji dotyczących użytkownika. 

Należy wziąć pod uwagę następujące odpowiedź JSON z interfejsu API zaplecza.

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

I dodatkowych zasobów w `/userprofile/{userid}` który wygląda podobnie,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Aby określić odpowiednie informacje dotyczące użytkownika, obejmujący usługi API Management należy określić, kim jest użytkownik końcowy. Ten mechanizm jest zależna od implementacji. Na przykład używam `Subject` oświadczeń z `JWT` tokenu. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

Interfejs API zarządzania magazynami `enduserid` wartość w zmiennej kontekstowej do późniejszego użycia. Następnym krokiem jest ustalenie, jeśli poprzednie żądanie ma już pobrane informacje o użytkowniku i zapisana w pamięci podręcznej. W tym celu używa usługi API Management `cache-lookup-value` zasad.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Jeśli pamięć podręczna, która odnosi się do wartości klucza, a następnie nie zawiera żadnego wpisu odpowiadającego `userprofile` zmiennej kontekstowej jest tworzony. Usługa API Management sprawdza sukcesu za pomocą wyszukiwania `choose` zasad przepływu sterowania.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Jeśli `userprofile` zmiennej kontekstowej nie istnieje, a następnie usługa API Management ma zostać żądania HTTP można je pobrać.

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

API Management używa `enduserid` do utworzenia adresu URL do zasobu profilu użytkownika. Gdy usługa API Management ma odpowiedzi, pobiera tekst podstawowy poza odpowiedzi i zapisuje go do zmiennej kontekstowej.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Aby uniknąć usługi API Management z ponownie, dzięki czemu to żądanie HTTP, gdy ten sam użytkownik wykonuje kolejne żądanie, można określić, aby przechowywać profil użytkownika w pamięci podręcznej.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

Usługa API Management przechowuje wartość w pamięci podręcznej przy użyciu dokładnie tego samego klucza usługi API Management pierwotnie nastąpiła próba pobrania go za pomocą. Czas trwania, który wybiera usługi API Management, do przechowywania wartości powinna być oparta na jak często zmiany informacji i sposobu odpornego na błędy użytkowników są do nieaktualnych informacji. 

Należy weź pod uwagę, że pobieranie z pamięci podręcznej jest nadal spoza procesu, żądania sieci i potencjalnie można nadal dodawać dziesiątki milisekundy na żądanie. Korzyści są dostarczane podczas określania, że trwa dłużej niż z powodu konieczności bazy danych w zapytaniach ani zagregowanych informacji z wielu zapleczy, informacje o profilu użytkownika.

Ostatnim krokiem w procesie jest zaktualizowanie odpowiedzi zwrócony przy użyciu informacji o profilu użytkownika.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Można wybrać, które uwzględniać znaki cudzysłowu jako część tokenu tak, aby nawet wtedy, gdy nie występuje zamiany, odpowiedź nadal jest prawidłowym plikiem JSON.  

Wszystkie te kroki są łączone ze sobą, wynik końcowy po zasadę, która wygląda podobnie do poniższego.

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

To podejście buforowania jest używany głównie w witrynach sieci web gdzie HTML składa się po stronie serwera, dzięki czemu mogą być renderowane jako pojedynczej strony. Również może być przydatne w interfejsach API, gdzie klienci nie może wykonać po stronie klienta HTTP w pamięci podręcznej lub jest wprowadzania tej odpowiedzialności na komputerze klienckim.

Tego samego rodzaju fragmentu pamięci podręcznej, można również wykonać na serwerach sieci web zaplecza przy użyciu pamięci podręcznej serwer Redis, jednak przy użyciu usługi API Management do wykonywania tej pracy jest przydatne, gdy fragmenty pamięci podręcznej pochodzą z zaplecza innego niż podstawowy odpowiedzi.

## <a name="transparent-versioning"></a>Przechowywanie wersji przezroczyste
Jest to powszechną praktyką w przypadku wielu wersji inną implementację interfejsu API są obsługiwane w dowolnym momencie. Na przykład w celu zapewnienia obsługi różnych środowiskach (dev, test, produkcyjne itp.) lub obsługuje starsze wersje interfejsu API, aby zapewnić czas na interfejs API w konsumentach napisanych migrację do nowszych wersji. 

Jedno z podejść do obsługi tego zamiast klienta deweloperom zmienianie adresów URL z `/v1/customers` do `/v2/customers` jest do przechowywania danych profilu klienta, którą wersję interfejsu API obecnie chcą używać i wywoływać URL odpowiedniej wewnętrznej bazy danych. Aby określić adres URL poprawne wewnętrznej bazy danych do wywołania dla określonego klienta, należy zbadać dane konfiguracji. Buforowanie danych konfiguracji, API Management można zminimalizować spadek wydajności działania tego wyszukiwania.

Pierwszym krokiem jest ustalenie, identyfikator używany do konfigurowania żądanej wersji. W tym przykładzie wybrano skojarzyć wersji klucza subskrypcji produktu. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

Usługa API Management jest następnie przeszukiwania pamięci podręcznej, aby zobaczyć, czy już pobierane żądanej wersji.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Następnie usługa API Management sprawdza, jeśli go nie znaleziono go w pamięci podręcznej.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Jeśli usługa API Management nie znaleźć, API Management pobiera je.

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

Wyodrębnij tekst treści odpowiedzi z odpowiedzi.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Store go w pamięci podręcznej do użytku w przyszłości.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

A na koniec zaktualizuj adres URL zaplecza do wybrania wersji usługi żądanego przez klienta.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Pełne zasad jest następująca:

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

Włączanie interfejsu API w konsumentach napisanych przezroczyste kontrolować, która wersja wewnętrznej bazy danych jest uzyskiwany przez klientów bez konieczności aktualizacji i ponownego wdrożenia klientów to elegancki rozwiązanie, które eliminuje wiele problemów przechowywanie wersji interfejsu API.

## <a name="tenant-isolation"></a>Izolacji dzierżawcy
We wdrożeniach wielodostępnych, większy niektóre firmy utworzenie oddzielnych grup dzierżaw na różnych wdrożeń sprzętu wewnętrznej bazy danych. Pozwala to zmniejszyć liczbę klientów, którzy mają wpływ problem ze sprzętem w wewnętrznej bazie danych. Umożliwia ona także nowe wersje oprogramowania do etapowo wdrażana w etapach. W idealnym tej architektury wewnętrznej bazy danych, powinny być przezroczyste dla konsumentów interfejsu API. Można to osiągnąć w podobny sposób jak przezroczyste przechowywania wersji, ponieważ jest on oparty na tej samej techniki, manipulowania URL wewnętrznej bazy danych przy użyciu stanu konfiguracji dla klucza interfejsu API.  

Zamiast zwracać preferowaną wersję interfejsu API dla każdego klucza subskrypcji, zwróci identyfikator, który odnosi się dzierżawy do niej przypisane sprzętu. Ten identyfikator może służyć do utworzenia adresu URL odpowiedniego serwera zaplecza.

## <a name="summary"></a>Podsumowanie
Użytkownicy mogą korzystać z pamięci podręcznej management interfejsu API platformy Azure do przechowywania dowolnego typu danych umożliwia skutecznego dostępu do danych konfiguracji, które mogą mieć wpływ na sposób, w jaki przychodzące żądanie jest przetwarzane. Może on także służyć do przechowywania fragmentów danych, które można rozszerzyć odpowiedzi, zostały zwrócone z interfejsu API zaplecza.
