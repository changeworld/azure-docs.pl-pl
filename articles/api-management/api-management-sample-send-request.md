---
title: Do generowania żądań HTTP przy użyciu usługi API Management
description: Dowiedz się, jak używać zasad żądań i odpowiedzi w usłudze API Management do wywołania usług zewnętrznych z interfejsu API
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: adb7329249570750002f04fb72465698f869afdc
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58792488"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Korzystanie z usług zewnętrznych z usługi Azure API Management
Zasady dostępne w usłudze Azure API Management można wykonać szeroką gamę przydatnych działań oparte wyłącznie na żądanie przychodzące, Wychodzące odpowiedzi i podstawowe informacje o konfiguracji. Jednak możliwość interakcji z usługami zewnętrznymi z usługi API Management zasady otwiera wiele więcej.

Zauważyliśmy już wcześniej sposób interakcji z [usługi Azure Event Hub, rejestrowanie, monitorowanie i analizy](api-management-log-to-eventhub-sample.md). W tym artykule przedstawiono zasady, które umożliwiają korzystanie z dowolnymi usługami zewnętrznych oparty na protokole HTTP. Te zasady może służyć do wyzwalania zdalnych zdarzeń lub do odzyskiwania informacji, które jest używane do manipulowania oryginalnego żądania i odpowiedzi w jakiś sposób.

## <a name="send-one-way-request"></a>Send-One-Way-Request
Prawdopodobnie najprostszą interakcja zewnętrznych jest styl pożarowego i zapominać żądania, które umożliwia usługi zewnętrznej w celu otrzymywania powiadomień z pewnego rodzaju ważnych zdarzeń. Zasady przepływu sterowania `choose` może służyć do wykrywania dowolnego rodzaju warunek, który Cię interesuje.  Jeśli warunek jest spełniony, można wprowadzić zewnętrzny HTTP żądania przy użyciu [-co sposób — żądanie wysłania](/azure/api-management/api-management-advanced-policies#SendOneWayRequest) zasad. To żądanie, aby system obsługi komunikatów, takich jak usługi Hipchat Slack i/lub wiadomość e-mail interfejsu API usługi SendGrid lub MailChimp, lub dla zdarzeń krytycznych wymagających pomocy technicznej coś w rodzaju usługi PagerDuty. Wszystkie te systemy obsługi komunikatów ma proste interfejsy API protokołu HTTP, który może być wywoływany.

### <a name="alerting-with-slack"></a>Alerty z Slack
Poniższy przykład pokazuje, jak wysyłać komunikat do pokoju rozmów Slack, jeśli kod stanu odpowiedzi HTTP jest większa niż lub równy 500. Błąd zakresu 500 wskazuje na problem z zaplecza interfejsu API klienta interfejsu API nie można rozwiązać samodzielnie. Zwykle wymaga pewnego rodzaju interwencji ze strony usługi API Management.  

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

Slack jest pojęcie punkty zaczepienia przychodzącego ruchu internetowego. Podczas konfigurowania punktu zaczepienia przychodzącego ruchu internetowego, Slack generuje specjalny adres URL, dzięki czemu można w tym prostego żądania POST i przekazać komunikat do kanału Slack. Treść kodu JSON, które tworzysz jest oparty na formacie definicją Slack.

![Slack Web Hook](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Jest uruchomiony i zapomnij wystarczająco dobra?
Istnieją pewne wady i zalety, korzystając z pożarowego i zapominać styl żądania. Jeśli jakichś powodów, żądanie nie powiedzie się, wówczas nie jest zgłaszany błąd. W szczególności takim złożoność po awarii pomocniczej raportowania systemu oraz wyższą wydajność kosztu oczekiwania na odpowiedź nie jest uzasadnione. W scenariuszach, gdzie jest niezbędne, aby sprawdzić odpowiedzi a następnie [żądań wysłania](/azure/api-management/api-management-advanced-policies#SendRequest) zasad jest lepszym rozwiązaniem.

## <a name="send-request"></a>Send-Request
`send-request` Umożliwia zasad przy użyciu usługi zewnętrznej do wykonywania zadań złożone przetwarzanie i zwraca dane do usługi API management usługi, która może służyć do dalszego przetwarzania zasad.

### <a name="authorizing-reference-tokens"></a>Autoryzowanie tokenów odwołań
Najważniejszych funkcji usługi API Management chroni zasoby zaplecza. Jeśli serwer autoryzacji używany przez interfejs API tworzy [tokenów JWT](https://jwt.io/) jako część jego przepływu OAuth2 jako [usługi Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) jest, wówczas można użyć `validate-jwt` zasad w celu zweryfikowania jego ważności tokenu. Niektóre serwery autoryzacji tworzenia, są nazywane [odwołać tokeny](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) nie można zweryfikować bez wprowadzania wywołanie zwrotne do autoryzacji serwera.

### <a name="standardized-introspection"></a>Standardowe introspekcji
W przeszłości została żaden standardowy sposób sprawdzania, czy token odwołania, za pomocą serwera autoryzacji. Jednak ostatnio proponowany standard [RFC 7662](https://tools.ietf.org/html/rfc7662) została opublikowana przez grupę IETF, który definiuje, jak serwer zasobów można sprawdzić poprawność tokenu.

### <a name="extracting-the-token"></a>Trwa wyodrębnianie tokenu
Pierwszym krokiem jest token zostanie wyodrębniony z nagłówka autoryzacji. Wartość nagłówka powinien być sformatowany za pomocą `Bearer` schemat autoryzacji, pojedynczą spację, a następnie token autoryzacji zgodnie [RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1). Niestety istnieją przypadki, w której zostanie pominięty schemat autoryzacji. Zostało to podczas analizowania, API Management dzieli wartość nagłówka w miejscu i wybiera ostatni ciąg z zwróconej tablicy ciągów. Zapewnia to obejście dla nagłówków autoryzacji niepoprawnie sformatowana.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Tworzenie żądania walidacji
Gdy usługa API Management ma token autoryzacji, API Management można wprowadzać żądania do zweryfikowania tokenu. RFC 7662 wywołuje introspekcji ten proces i wymaga, aby użytkownik `POST` formularza HTML do zasobu introspekcji. Formularza HTML przy użyciu klucza musi zawierać co najmniej pary klucz/wartość `token`. To żądanie do autoryzacji serwera musi także zostać uwierzytelniony, aby upewnić się, że złośliwego klientów nie można go trawling prawidłowy tokenów.

```xml
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
```

### <a name="checking-the-response"></a>Sprawdzanie odpowiedzi
`response-variable-name` Atrybut jest używany w celu udzielenia dostępu zwrócona odpowiedź. Z nazwą zdefiniowaną w tej właściwości może służyć jako klucz do `context.Variables` słownika, aby uzyskać dostęp do `IResponse` obiektu.

Z obiektu odpowiedzi, można pobrać treści i RFC 7622 informuje usługa API Management, czy odpowiedź musi być obiektem JSON i musi zawierać co najmniej właściwość o nazwie `active` oznacza to wartość typu boolean. Gdy `active` ma wartość true, a następnie token jest uznawany za ważny.

### <a name="reporting-failure"></a>Zgłaszanie błędu
Możesz użyć `<choose>` zasady, aby wykryć, czy token jest nieprawidłowy, a jeśli tak, zwracają odpowiedzi 401.

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

Zgodnie [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) opisano jak `bearer` tokeny powinny być używane, API Management zwraca również wartość `WWW-Authenticate` nagłówek odpowiedzi 401. WWW-Authenticate ma na celu poinstruowania klienta, jak utworzyć żądanie prawidłowo autoryzowanych. Z powodu różnych metod, które można zrobić za pomocą OAuth2 framework jest trudne do komunikowania się wszystkie wymagane informacje. Na szczęście istnieją wysiłki w toku, aby ułatwić [klientów Dowiedz się, jak poprawnie autoryzować żądania do serwera zasobów](https://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Ostateczna rozwiązania
Po zakończeniu otrzymasz następujące zasady:

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
              <return-response response-variable-name="existing response variable">
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

Jest to tylko jeden z wielu przykładów jak `send-request` zasady mogą służyć do integracji usług zewnętrznych przydatne przetwarzania żądań i odpowiedzi przepływają przez usługę API Management.

## <a name="response-composition"></a>Kompozycja odpowiedzi
`send-request` Zasady mogą być używane z wzbogacać żądania podstawowego do systemu zaplecza, jak opisany w poprzednim przykładzie, lub może służyć jako ukończone Zastąp dla wywołania wewnętrznej bazy danych. Przy użyciu tej metody można łatwo utworzyć złożone zasoby, które są agregowane z wielu różnych systemach.

### <a name="building-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego
Czasami chcesz być w stanie, aby spowodować ujawnienie informacji, która znajduje się w wielu systemów zaplecza, na przykład dysk na pulpicie nawigacyjnym. Kluczowych wskaźników wydajności pochodzą wszystkie różne zapleczy, ale wolisz nie zapewniać bezpośredni dostęp do nich i byłoby nieuprzywilejowany można pobrać wszystkich informacji w jednym żądaniu. Być może niektóre informacje wewnętrznej bazy danych musi niektóre dzielenie i grupowanie i nieco podczas czyszczenia najpierw! Możliwość złożonego zasobu w pamięci podręcznej będzie przydatna do zmniejszenia obciążenia wewnętrznej bazy danych, ponieważ wiadomo, że użytkownicy mają rodzaj atakowaniu klawisz F5, aby sprawdzić, czy ich o niskiej efektywności metryk mogą ulec zmianie.    

### <a name="faking-the-resource"></a>Faking zasobu
Pierwszym krokiem do tworzenia zasobów pulpitu nawigacyjnego jest skonfigurować nową operację w witrynie Azure portal. Jest to operacja symbolu zastępczego, używany do konfigurowania zasad składu w celu kompilacji dynamicznych zasobów.

![Operacja pulpitu nawigacyjnego](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Tworzenie żądania
Po utworzeniu operacji można skonfigurować zasady specjalnie dla tej operacji. 

![Operacja pulpitu nawigacyjnego](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Pierwszym krokiem jest wyodrębnić wszystkie parametry zapytania z żądania przychodzącego, dzięki czemu możesz je przekazywać do zaplecza. W tym przykładzie pulpit nawigacyjny jest wyświetlane informacje oparte na czasie i dlatego ma `fromDate` i `toDate` parametru. Możesz użyć `set-variable` zasad do wyodrębnienia informacji z adresu URL żądania.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Gdy te informacje mogą wysyłać żądania do wszystkich systemów zaplecza. Każde żądanie tworzy nowy adres URL z informacje o parametrach i wywołuje jego odpowiedniego serwera i odpowiedzi są przechowywane w zmiennej kontekstowej.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Te żądania są wykonywane w sekwencji, która nie jest idealnym rozwiązaniem. 

### <a name="responding"></a>Odpowiada
Aby utworzyć złożone reakcji, można użyć [odpowiedzi zwracany](/azure/api-management/api-management-advanced-policies#ReturnResponse) zasad. `set-body` Elementu można użyć wyrażenia do utworzenia nowego `JObject` przy użyciu reprezentacji składnika osadzonego jako właściwości.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

Pełne zasad wygląda następująco:

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

W konfiguracji operacji symbolu zastępczego można skonfigurować zasobów pulpitu nawigacyjnego przechowywanie w pamięci podręcznej na co najmniej godzinę. 

## <a name="summary"></a>Podsumowanie
Usługa API Management zapewnia elastyczne zasady, które można wybiórczo zastosować do ruchu HTTP oraz umożliwia tworzenie usług zaplecza. Czy chcesz poprawić bramy interfejsu API za pomocą alertów, funkcje, weryfikacji i sprawdzania poprawności możliwości lub utworzyć nowe zasoby złożone oparte na wielu usług zaplecza, `send-request` i powiązane zasady to droga możliwości.

