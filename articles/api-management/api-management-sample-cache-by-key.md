---
title: Buforowanie niestandardowe w usłudze Azure API Management
description: Dowiedz się, jak buforować elementy według klucza w usłudze Azure API Management
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60780825"
---
# <a name="custom-caching-in-azure-api-management"></a>Buforowanie niestandardowe w usłudze Azure API Management
Usługa Azure API Management ma wbudowaną obsługę [buforowania odpowiedzi HTTP](api-management-howto-cache.md) przy użyciu adresu URL zasobu jako klucza. Klucz może być modyfikowany przez nagłówki `vary-by` żądań przy użyciu właściwości. Jest to przydatne do buforowania całych odpowiedzi HTTP (aka reprezentacje), ale czasami jest to przydatne tylko do pamięci podręcznej część reprezentacji. Nowe zasady [wartości wyszukiwania pamięci podręcznej](/azure/api-management/api-management-caching-policies#GetFromCacheByKey) i [magazynu pamięci podręcznej](/azure/api-management/api-management-caching-policies#StoreToCacheByKey) zapewniają możliwość przechowywania i pobierania dowolnych fragmentów danych z definicji zasad. Ta możliwość dodaje również wartość do wcześniej wprowadzonych zasad [wysyłania żądań,](/azure/api-management/api-management-advanced-policies#SendRequest) ponieważ można teraz buforować odpowiedzi z usług zewnętrznych.

## <a name="architecture"></a>Architektura
Usługa API Management używa udostępnionej pamięci podręcznej danych dla dzierżawy, dzięki czemu podczas skalowania do wielu jednostek nadal uzyskujesz dostęp do tych samych danych w pamięci podręcznej. Jednak podczas pracy z wdrożeniem wielu regionów istnieją niezależne pamięci podręczne w każdym z regionów. Ważne jest, aby nie traktować pamięci podręcznej jako magazynu danych, gdzie jest jedynym źródłem niektórych informacji. Jeśli to zrobisz, a później zdecydujesz się skorzystać z wdrożenia w wielu regionach, klienci z użytkownikami, którzy podróżują, mogą utracić dostęp do tych buforowanych danych.

## <a name="fragment-caching"></a>Buforowanie fragmentów
Istnieją pewne przypadki, w których zwracane odpowiedzi zawierają pewną część danych, które są kosztowne do ustalenia, a mimo to pozostają świeże przez rozsądny czas. Na przykład rozważmy usługę wybudowaną przez linię lotniczą, która dostarcza informacji dotyczących rezerwacji lotów, statusu lotu itp. Jeśli użytkownik jest członkiem programu punktów linii lotniczych, będzie miał również informacje dotyczące ich aktualnego statusu i skumulowanego przebiegu. Te informacje związane z użytkownikiem mogą być przechowywane w innym systemie, ale może być pożądane, aby uwzględnić je w odpowiedziach zwróconych na temat statusu lotu i rezerwacji. Można to zrobić za pomocą procesu o nazwie buforowanie fragmentów. Reprezentacja podstawowa może zostać zwrócona z serwera pochodzenia przy użyciu pewnego rodzaju tokenu, aby wskazać, gdzie mają być wstawiane informacje związane z użytkownikiem. 

Należy wziąć pod uwagę następującą odpowiedź JSON z interfejsu API wewnętrznej bazy danych.

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

A drugorzędne zasoby w `/userprofile/{userid}` tym wygląda,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Aby określić odpowiednie informacje o użytkowniku do uwzględnienia, usługa API Management musi zidentyfikować, kto jest użytkownikiem końcowym. Mechanizm ten jest zależny od implementacji. Na przykład używam `Subject` oświadczenia tokenu. `JWT` 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

Usługa API `enduserid` Management przechowuje wartość w zmiennej kontekstowej do późniejszego użycia. Następnym krokiem jest ustalenie, czy poprzednie żądanie zostało już pobrane informacje o użytkowniku i przechowywane w pamięci podręcznej. W tym celu usługa `cache-lookup-value` API Management używa zasad.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Jeśli w pamięci podręcznej nie ma wpisu, który `userprofile` odpowiada wartości klucza, nie jest tworzona żadna zmienna kontekstowa. Usługa API Management sprawdza powodzenie `choose` wyszukiwania przy użyciu zasad przepływu sterowania.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Jeśli `userprofile` zmienna kontekstowa nie istnieje, usługa Zarządzanie interfejsem API będzie musiała złożyć żądanie HTTP, aby ją pobrać.

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

Usługa API Management `enduserid` używa adresu URL do zasobu profilu użytkownika. Gdy usługa API Management ma odpowiedź, pobiera tekst treści z odpowiedzi i przechowuje go z powrotem do zmiennej kontekstu.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Aby uniknąć zarządzania interfejsami API z dokonywania tego żądania HTTP ponownie, gdy ten sam użytkownik sprawia, że inne żądanie, można określić do przechowywania profilu użytkownika w pamięci podręcznej.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

Usługa API Management przechowuje wartość w pamięci podręcznej przy użyciu dokładnie tego samego klucza, z który pierwotnie próbowano pobrać go z usługą API Management. Czas trwania, który usługa API Management zdecyduje się na przechowywanie wartości, powinien zależeć od tego, jak często informacje się zmieniają i jak tolerancyjnie użytkownicy mają nieaktualne informacje. 

Należy zdać sobie sprawę, że pobieranie z pamięci podręcznej jest nadal poza procesem, żądanie sieciowe i potencjalnie nadal można dodać dziesiątki milisekund do żądania. Korzyści pochodzą podczas określania informacji o profilu użytkownika trwa dłużej niż to ze względu na konieczność wykonywania zapytań do bazy danych lub agregacji informacji z wielu zaplecza.

Ostatnim krokiem w procesie jest zaktualizowanie zwróconej odpowiedzi z informacjami o profilu użytkownika.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Można wybrać dołączyć cudzysłowy jako część tokenu, tak aby nawet wtedy, gdy zastąpić nie występuje, odpowiedź jest nadal prawidłowe JSON.  

Po połączeniu wszystkich tych kroków razem, wynik końcowy jest zasadą, która wygląda jak następująca.

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

To podejście buforowania jest używane głównie w witrynach sieci web, gdzie html składa się po stronie serwera, dzięki czemu może być renderowany jako pojedyncza strona. Może to być również przydatne w interfejsach API, w których klienci nie mogą wykonywać buforowania HTTP po stronie klienta lub pożądane jest, aby nie nakładać tej odpowiedzialności na klienta.

Ten sam rodzaj buforowania fragmentów można również wykonać na serwerach sieci web wewnętrznej bazy danych przy użyciu serwera buforowania Redis, jednak użycie usługi API Management do wykonania tej pracy jest przydatne, gdy buforowane fragmenty pochodzą z różnych zaplecza niż podstawowe Odpowiedzi.

## <a name="transparent-versioning"></a>Przezroczyste przechowywanie wersji
Jest powszechną praktyką dla wielu różnych wersji implementacji interfejsu API, które mają być obsługiwane w dowolnym momencie. Na przykład do obsługi różnych środowisk (dev, test, produkcja, itp.) lub do obsługi starszych wersji interfejsu API, aby dać czas dla konsumentów interfejsu API do migracji do nowszych wersji. 

Jednym z podejść do obsługi tego, zamiast wymagać `/v1/customers` od `/v2/customers` deweloperów klienta, aby zmienić adresy URL z do jest do przechowywania w danych profilu konsumenta, która wersja interfejsu API, które obecnie chcą używać i wywołać odpowiedni adres URL wewnętrznej bazy danych. Aby określić poprawny adres URL wewnętrznej bazy danych, aby wywołać dla określonego klienta, konieczne jest zapytanie niektórych danych konfiguracji. Buforując te dane konfiguracji, zarządzanie interfejsem API można zminimalizować karę wydajności wykonywania tego wyszukiwania.

Pierwszym krokiem jest określenie identyfikatora używanego do konfigurowania żądanej wersji. W tym przykładzie wybrałem skojarzenie wersji z kluczem subskrypcji produktu. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

Zarządzanie interfejsem API następnie wykonuje wyszukiwanie w pamięci podręcznej, aby sprawdzić, czy została już pobrana żądana wersja klienta.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Następnie usługa API Management sprawdza, czy nie znajduje się w pamięci podręcznej.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Jeśli usługa API Management go nie znalazła, usługa API Management ją odbierze.

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

Przechowuj go z powrotem w pamięci podręcznej do wykorzystania w przyszłości.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

I na koniec zaktualizować adres URL zaplecza, aby wybrać wersję usługi żądaną przez klienta.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Pełna polityka jest następująca:

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

Umożliwienie użytkownikom interfejsu API przejrzystego kontrolowania, która wersja wewnętrznej bazy danych jest dostępna dla klientów bez konieczności aktualizowania i ponownego rozmieszczania klientów, jest eleganckim rozwiązaniem, które rozwiązuje wiele problemów związanych z przechowywaniem wersji interfejsu API.

## <a name="tenant-isolation"></a>Izolacja dzierżawy
W większych wdrożeniach z wieloma dzierżawcami niektóre firmy tworzą oddzielne grupy dzierżaw na różnych wdrożeniach sprzętu zaplecza. Minimalizuje to liczbę klientów, których dotyczy problem ze sprzętem w wewnętrznej bazy danych. Umożliwia również wdrażanie nowych wersji oprogramowania etapami. Najlepiej ta architektura wewnętrznej bazy danych powinna być przezroczysta dla konsumentów interfejsu API. Można to osiągnąć w sposób podobny do przezroczystego przechowywania wersji, ponieważ jest on oparty na tej samej technice manipulowania adresem URL wewnętrznej bazy danych przy użyciu stanu konfiguracji na klucz interfejsu API.  

Zamiast zwracać preferowaną wersję interfejsu API dla każdego klucza subskrypcji, należy zwrócić identyfikator, który odnosi się do dzierżawy do przypisanej grupy sprzętu. Ten identyfikator może służyć do konstruowania odpowiedniego adresu URL wewnętrznej bazy danych.

## <a name="summary"></a>Podsumowanie
Swoboda używania pamięci podręcznej zarządzania interfejsem Azure do przechowywania dowolnego rodzaju danych umożliwia wydajny dostęp do danych konfiguracyjnych, które mogą mieć wpływ na sposób przetwarzania żądania przychodzącego. Może również służyć do przechowywania fragmentów danych, które mogą rozszerzać odpowiedzi, zwracane z interfejsu API wewnętrznej bazy danych.
