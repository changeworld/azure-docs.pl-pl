---
title: Generowanie żądań HTTP za pomocą usługi API Management
description: Dowiedz się, jak używać zasad żądań i odpowiedzi w usłudze API Management do wywoływania usług zewnętrznych z interfejsu API
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
ms.openlocfilehash: 1c86570850894a47f57a2d3587811411cc9a76eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190011"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Korzystanie z usług zewnętrznych z usługi Azure API Management
Zasady dostępne w usłudze Azure API Management usługi można wykonać szeroki zakres przydatnych prac opartych wyłącznie na przychodzące żądanie, odpowiedzi wychodzącej i podstawowych informacji o konfiguracji. Jednak możliwość interakcji z usługami zewnętrznymi z zasad zarządzania interfejsami API otwiera o wiele więcej możliwości.

Wcześniej widziałeś, jak wchodzić w interakcje z [usługą Azure Event Hub do rejestrowania, monitorowania i analizy.](api-management-log-to-eventhub-sample.md) W tym artykule przedstawiono zasady, które umożliwiają interakcję z dowolną zewnętrzną usługą opartą na protokoście HTTP. Te zasady mogą służyć do wyzwalania zdarzeń zdalnych lub pobierania informacji, które są używane do manipulowania oryginalnym żądaniem i odpowiedzią w jakiś sposób.

## <a name="send-one-way-request"></a>Wyślij żądanie jednokierunkowe
Prawdopodobnie najprostszą interakcją zewnętrzną jest styl żądania typu fire-and-forget, który umożliwia powiadamianie usługi zewnętrznej o jakimś ważnym wydarzeniu. Zasady `choose` przepływu sterowania mogą służyć do wykrywania dowolnego rodzaju warunek, który cię interesuje.  Jeśli warunek jest spełniony, można wykonać zewnętrzne żądanie HTTP przy użyciu zasad [żądania wysyłania w jedną stronę.](/azure/api-management/api-management-advanced-policies#SendOneWayRequest) Może to być żądanie do systemu obsługi wiadomości, takich jak Hipchat lub Slack, lub interfejsu API poczty, takich jak SendGrid lub MailChimp, lub krytycznych incydentów wsparcia coś pagerDuty. Wszystkie te systemy obsługi wiadomości mają proste interfejsy API HTTP, które mogą być wywoływane.

### <a name="alerting-with-slack"></a>Alerty z slack
W poniższym przykładzie pokazano, jak wysłać wiadomość do pokoju rozmów Slack, jeśli kod stanu odpowiedzi HTTP jest większy lub równy 500. Błąd zakresu 500 wskazuje na problem z interfejsem API wewnętrznej bazy danych, że klient interfejsu API nie może rozwiązać samodzielnie. Zwykle wymaga pewnego rodzaju interwencji w części api management.  

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

Slack ma pojęcie przychodzących haków internetowych. Podczas konfigurowania przychodzącego haka sieci Web Slack generuje specjalny adres URL, który umożliwia zrobienie prostego żądania POST i przekazanie wiadomości do kanału Slack. Tworzęt JSON, który tworzysz jest oparty na formacie zdefiniowanym przez Slack.

![Luźny hak internetowy](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Czy ogień i zapomnieć wystarczająco dobre?
Istnieją pewne kompromisy podczas korzystania z fire-and-forget stylu żądania. Jeśli z jakiegoś powodu żądanie zakończy się niepowodzeniem, błąd nie zostanie zgłoszony. W tej konkretnej sytuacji złożoność posiadania systemu raportowania awarii wtórnej i dodatkowe koszty wykonania oczekiwania na odpowiedź nie jest uzasadnione. W przypadku scenariuszy, w których jest to niezbędne do sprawdzenia odpowiedzi, lepszym rozwiązaniem jest zasada [wysyłania żądań.](/azure/api-management/api-management-advanced-policies#SendRequest)

## <a name="send-request"></a>Wyślij żądanie
Zasady `send-request` umożliwia korzystanie z usługi zewnętrznej do wykonywania złożonych funkcji przetwarzania i zwracać dane do usługi zarządzania interfejsami API, które mogą być używane do dalszego przetwarzania zasad.

### <a name="authorizing-reference-tokens"></a>Autoryzowanie tokenów referencyjnych
Główną funkcją zarządzania interfejsami API jest ochrona zasobów zaplecza. Jeśli serwer autoryzacji używany przez interfejs API tworzy [tokeny JWT](https://jwt.io/) jako część przepływu OAuth2, `validate-jwt` tak jak robi to usługa Azure Active [Directory,](../active-directory/hybrid/whatis-hybrid-identity.md) można użyć zasad, aby zweryfikować ważność tokenu. Niektóre serwery autoryzacji tworzą tak zwane [tokeny referencyjne,](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) których nie można zweryfikować bez wywołania zwrotnego na serwerze autoryzacji.

### <a name="standardized-introspection"></a>Znormalizowana introspekcja
W przeszłości nie było standardowego sposobu weryfikacji tokenu referencyjnego za pomocą serwera autoryzacji. Jednak niedawno proponowany standard [RFC 7662](https://tools.ietf.org/html/rfc7662) został opublikowany przez IETF, który określa, jak serwer zasobów może zweryfikować ważność tokenu.

### <a name="extracting-the-token"></a>Wyodrębnianie tokenu
Pierwszym krokiem jest wyodrębnienie tokenu z nagłówka Autoryzacja. Wartość nagłówka powinna być `Bearer` sformatowana za pomocą schematu autoryzacji, pojedynczego miejsca, a następnie tokenu autoryzacji zgodnie z [RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1). Niestety zdążają przypadki pominięcia schematu autoryzacji. Aby uwzględnić to podczas analizowania, usługa API Management dzieli wartość nagłówka w spacji i wybiera ostatni ciąg z zwróconej tablicy ciągów. Zapewnia to obejście źle sformatowanych nagłówków autoryzacji.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Składanie żądania weryfikacji
Gdy usługa API Management ma token autoryzacji, usługa API Management może złożyć żądanie sprawdzania poprawności tokenu. RFC 7662 wywołuje introspekcję tego `POST` procesu i wymaga, aby formularz HTML do zasobu introspekcji. Formularz HTML musi zawierać co najmniej parę klucz/wartość z kluczem `token`. To żądanie do serwera autoryzacji musi być również uwierzytelnione, aby upewnić się, że złośliwi klienci nie mogą przejść trałowania dla prawidłowych tokenów.

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
Atrybut `response-variable-name` jest używany do udzielania dostępu zwróconej odpowiedzi. Nazwa zdefiniowana w tej właściwości może służyć `context.Variables` jako klucz `IResponse` do słownika, aby uzyskać dostęp do obiektu.

Z obiektu odpowiedzi można pobrać treści i RFC 7622 mówi zarządzania interfejsu API, że odpowiedź musi `active` być obiekt JSON i musi zawierać co najmniej właściwość o nazwie, która jest wartością logiczną. Gdy `active` jest true następnie token jest uważany za prawidłowy.

Alternatywnie, jeśli serwer autoryzacji nie zawiera pola "aktywne", aby wskazać, czy token jest prawidłowy, użyj narzędzia, takiego jak Postman, aby określić, jakie właściwości są ustawione w prawidłowym tokenie. Na przykład jeśli prawidłowa odpowiedź tokenu zawiera właściwość o nazwie "expires_in", sprawdź, czy ta nazwa właściwości istnieje w odpowiedzi serwera autoryzacji w ten sposób:

<, gdy kontekst condition="@(((IResponse).. zmiennych["tokenstate"]). Body.As<JObject>(). Właściwość("expires_in") == null)">

### <a name="reporting-failure"></a>Niepowodzenie raportowania
Można użyć `<choose>` zasad, aby wykryć, czy token jest nieprawidłowy, a jeśli tak, zwrócić odpowiedź 401.

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

Zgodnie z [RFC 6750,](https://tools.ietf.org/html/rfc6750#section-3) który opisuje, jak `bearer` tokeny `WWW-Authenticate` powinny być używane, Zarządzanie interfejsem API zwraca również nagłówek z odpowiedzią 401. Www-Authenticate ma na celu poinstruowanie klienta, jak skonstruować poprawnie autoryzowane żądanie. Ze względu na szeroką gamę możliwych podejść w ramach OAuth2 trudno jest przekazać wszystkie potrzebne informacje. Na szczęście podejmowane są wysiłki, aby pomóc [klientom odkryć, jak prawidłowo autoryzować żądania do serwera zasobów.](https://tools.ietf.org/html/draft-jones-oauth-discovery-00)

### <a name="final-solution"></a>Ostateczne rozwiązanie
Na końcu otrzymasz następujące zasady:

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

Jest to tylko jeden z wielu `send-request` przykładów, jak zasady mogą służyć do integracji przydatnych usług zewnętrznych w procesie żądań i odpowiedzi przesyłanych za pośrednictwem usługi zarządzania interfejsami API.

## <a name="response-composition"></a>Skład odpowiedzi
Zasady `send-request` mogą służyć do zwiększania żądania podstawowego do systemu wewnętrznej bazy danych, jak widać w poprzednim przykładzie lub może służyć jako całkowita zamiana wywołania wewnętrznej bazy danych. Za pomocą tej techniki można łatwo utworzyć zasoby złożone, które są agregowane z wielu różnych systemów.

### <a name="building-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego
Czasami chcesz mieć możliwość udostępnienia informacji, które istnieją w wielu systemach wewnętrznej bazy danych, na przykład do kierowania pulpitu nawigacyjnego. Kluczowe wskaźniki wydajności pochodzą ze wszystkich różnych zaplecza, ale wolisz nie zapewniać bezpośredniego dostępu do nich i byłoby miło, gdyby wszystkie informacje mogły zostać pobrane w jednym żądaniu. Być może niektóre informacje backend potrzebuje trochę krojenia i dicing i trochę odkażania pierwszy! Możliwość buforowania tego zasobu złożonego będzie przydatna do zmniejszenia obciążenia wewnętrznej bazy danych, ponieważ wiesz, że użytkownicy mają zwyczaj wbijania klawisza F5, aby sprawdzić, czy ich słabsze metryki mogą ulec zmianie.    

### <a name="faking-the-resource"></a>Fakowanie zasobu
Pierwszym krokiem do tworzenia zasobu pulpitu nawigacyjnego jest skonfigurowanie nowej operacji w witrynie Azure portal. Jest to operacja zastępcza używana do konfigurowania zasad kompozycji do tworzenia zasobu dynamicznego.

![Działanie pulpitu nawigacyjnego](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Składanie wniosków
Po utworzeniu operacji można skonfigurować zasady specjalnie dla tej operacji. 

![Działanie pulpitu nawigacyjnego](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Pierwszym krokiem jest wyodrębnienie parametrów zapytania z żądania przychodzącego, dzięki czemu można przesłać je dalej do wewnętrznej bazy danych. W tym przykładzie pulpit nawigacyjny wyświetla informacje na podstawie okresu `fromDate` `toDate` czasu i dlatego ma i parametr. Za pomocą `set-variable` zasad można wyodrębnić informacje z adresu URL żądania.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Po uzyskaniu tych informacji można składać żądania do wszystkich systemów wewnętrznej bazy danych. Każde żądanie tworzy nowy adres URL z informacjami o parametrach i wywołuje jego odpowiedni serwer i przechowuje odpowiedź w zmiennej kontekstowej.

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
<set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Te żądania są wykonywane w sekwencji, co nie jest idealne. 

### <a name="responding"></a>Odpowiadać
Aby skonstruować odpowiedź złożoną, można użyć zasad [zwracanej odpowiedzi.](/azure/api-management/api-management-advanced-policies#ReturnResponse) Element `set-body` może użyć wyrażenia do `JObject` konstruowania nowego ze wszystkimi reprezentacjami komponentów osadzonymi jako właściwości.

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

Pełna zasada wygląda następująco:

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
    <set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
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

W konfiguracji operacji zastępczej można skonfigurować zasób pulpitu nawigacyjnego do buforowania przez co najmniej godzinę. 

## <a name="summary"></a>Podsumowanie
Usługa Azure API Management zapewnia elastyczne zasady, które mogą być selektywnie stosowane do ruchu HTTP i umożliwia skład usług zaplecza. Niezależnie od tego, czy chcesz ulepszyć bramę interfejsu API za pomocą funkcji alertów, weryfikacji, możliwości sprawdzania poprawności lub tworzenia nowych zasobów złożonych na podstawie wielu usług zaplecza, `send-request` i powiązane zasady otwierają świat możliwości.

