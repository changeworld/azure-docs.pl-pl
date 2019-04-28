---
title: Dokumentacja dla typów akcji i wyzwalaczy w język definicji przepływów pracy — Azure Logic Apps
description: Podręcznik informacyjny dla typów akcji i wyzwalaczy w język definicji przepływów pracy dla usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: reference
ms.date: 06/22/2018
ms.openlocfilehash: bd588eeec8b560411e3fb4b6f84ec8a4a45f08d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844181"
---
# <a name="reference-for-trigger-and-action-types-in-workflow-definition-language-for-azure-logic-apps"></a>Dokumentacja dla typów akcji i wyzwalaczy w język definicji przepływów pracy dla usługi Azure Logic Apps

Ta dokumentacja w tym artykule opisano typy ogólne w celu zidentyfikowania wyzwalaczy i akcji w podstawowej definicji przepływu pracy aplikacji logiki, co jest opisane i zweryfikowany przez używany [język definicji przepływów pracy](../logic-apps/logic-apps-workflow-definition-language.md).
Określony łącznik wyzwalacze i akcje, które można używać w aplikacjach logiki możesz znaleźć na liście w obszarze [omówienie łączników](https://docs.microsoft.com/connectors/).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Omówienie wyzwalaczy

Każdy przepływ pracy zawiera wyzwalacz, który definiuje wywołań, które tworzenia instancji i uruchomić przepływ pracy. Poniżej przedstawiono kategorie ogólne wyzwalacza:

* A *sondowania* wyzwalacz, który sprawdza, czy punkt końcowy usługi w regularnych odstępach czasu

* A *wypychania* wyzwalacza, tworzy subskrypcję do punktu końcowego, która zapewnia *adresów URL wywołania zwrotnego* więc punkt końcowy mogą powiadamiać wyzwalacza w przypadku określonego zdarzenia lub dane będą dostępne. Wyzwalacz jest następnie czeka na odpowiedź punktu końcowego przed uruchomieniu którego. 

Wyzwalacze mają te elementy najwyższego poziomu, mimo że niektóre są opcjonalne:  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*trigger-name*> | String | Nazwa wyzwalacza | 
| <*trigger-type*> | String | Typ wyzwalacza, takiego jak "Http" lub "ApiConnection" | 
| <*dane wejściowe wyzwalacza*> | Obiekt JSON | Dane wejściowe, które określają zachowanie tego wyzwalacza | 
| <*time-unit*> | String | Jednostka czasu, który w tym artykule opisano, jak często wyzwalacza: "Drugi", "Minute", "Hour", "Day", "Week", "Month" | 
| <*Liczba z godziny jednostek*> | Liczba całkowita | Wartość, która określa, jak często wyzwalacza na podstawie częstotliwości, czyli liczba jednostek czasu, poczekać, aż wyzwalacz uruchamia się ponownie <p>Poniżej przedstawiono minimalne i maksymalne odstępach czasu: <p>-Miesiąc: 1 – 16 miesięcy </br>-Dzień: 1 – 500 dni </br>-Godzinny: 1-12 000 godzin </br>-Minutowy: 1 72,000 min </br>-Sekundowych: 1 9,999,999 sekundy<p>Na przykład jeśli interwał wynosi 6 i częstotliwość wynosi "Month", cykl jest co 6 miesięcy. | 
|||| 

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*array-with-conditions*> | Tablica | Tablica, która zawiera co najmniej jeden [warunki](#trigger-conditions) określające, czy chcesz uruchomić przepływ pracy. Dostępne tylko dla wyzwalaczy. | 
| <*runtime-config-options*> | Obiekt JSON | Można zmienić zachowanie środowiska uruchomieniowego wyzwalacza, ustawiając `runtimeConfiguration` właściwości. Aby uzyskać więcej informacji, zobacz [ustawień konfiguracji środowiska uruchomieniowego](#runtime-config-options). | 
| <*splitOn-expression*> | String | Wyzwalacze, które zwracają tablicę, można określić wyrażenie, [dzieli lub *debatches* ](#split-on-debatch) elementy tablicy do wielu wystąpień przepływu pracy do przetwarzania. | 
| <*Opcja operacji*> | String | Można zmienić domyślne zachowanie przez ustawienie `operationOptions` właściwości. Aby uzyskać więcej informacji, zobacz [opcje operacji](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Lista typów wyzwalaczy

Każdy typ wyzwalacza ma inny interfejs i dane wejściowe, które określają zachowanie tego wyzwalacza. 

### <a name="built-in-triggers"></a>Wbudowane wyzwalaczy

| Typ wyzwalacza | Opis | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Sprawdza, czy lub *sond* dowolnego punktu końcowego. Ten punkt końcowy musi być zgodna z kontraktu określonego wyzwalacza, za pomocą "202" wzorca asynchronicznego lub tablicę. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Tworzy wywoływalnej punktu końcowego dla aplikacji logiki, ale wywołuje pod określony adres URL, aby zarejestrować lub wyrejestrować. |
| [**Cykl**](#recurrence-trigger) | Generowane na podstawie zdefiniowanego harmonogramu. Możesz ustawić przyszłą datę i godzinę wyzwalania tego wyzwalacza. Na podstawie częstotliwości, można również określić godziny i dni uruchomiony przepływ pracy. | 
| [**Żądanie**](#request-trigger)  | Tworzy wywoływalnej punktu końcowego dla aplikacji logiki i jest także znana jako "Ręczny" wyzwalacza. Na przykład zobacz [wywołania wyzwalacza lub zagnieżdżanie przepływy pracy za pomocą punktów końcowych HTTP](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Wyzwalacze zarządzanego interfejsu API

| Typ wyzwalacza | Opis | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Sprawdza, czy lub *sond* punktu końcowego przy użyciu [zarządzanych przez firmę Microsoft interfejsy API](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Tworzy wywoływalnej punktu końcowego dla aplikacji logiki przez wywołanie metody [zarządzanych przez firmę Microsoft interfejsy API](../connectors/apis-list.md) subskrybowanie i anulowanie subskrypcji. | 
||| 

## <a name="triggers---detailed-reference"></a>Wyzwalacze — szczegółową dokumentację

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>APIConnection wyzwalacza  

Sprawdza, czy ten wyzwalacz lub *sond* punktu końcowego przy użyciu [zarządzanych przez firmę Microsoft interfejsy API](../connectors/apis-list.md) tak parametrów dla tego wyzwalacza mogą różnić się na podstawie punktu końcowego. Wiele sekcji, w tym definicji wyzwalacza są opcjonalne. Tego wyzwalacza zachowanie zależy od tego, czy sekcje są uwzględniane.

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*APIConnection_trigger_name*> | String | Nazwa wyzwalacza | 
| <*connection-name*> | String | Nazwa połączenia zarządzanego interfejsu API, który korzysta z przepływu pracy | 
| <*method-type*> | String | Metoda HTTP do komunikacji z zarządzany interfejs API: "POBIERZ", "PUT", "POST", "POPRAWKA", "DELETE" | 
| <*Operacja interfejsu API*> | String | Operacja wywołania interfejsu API | 
| <*time-unit*> | String | Jednostka czasu, który w tym artykule opisano, jak często wyzwalacza: "Drugi", "Minute", "Hour", "Day", "Week", "Month" | 
| <*Liczba z godziny jednostek*> | Liczba całkowita | Wartość, która określa, jak często wyzwalacza na podstawie częstotliwości, czyli liczba jednostek czasu, poczekać, aż wyzwalacz uruchamia się ponownie <p>Poniżej przedstawiono minimalne i maksymalne odstępach czasu: <p>-Miesiąc: 1 – 16 miesięcy </br>-Dzień: 1 – 500 dni </br>-Godzinny: 1-12 000 godzin </br>-Minutowy: 1 72,000 min </br>-Sekundowych: 1 9,999,999 sekundy<p>Na przykład jeśli interwał wynosi 6 i częstotliwość wynosi "Month", cykl jest co 6 miesięcy. | 
|||| 

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*sposób ponawiania*> | Obiekt JSON | Dostosowuje zachowanie ponawiania sporadycznych błędów, które mają 408, 429 i kod stanu 5XX oraz wszystkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Obiekt JSON | Wywołaj żadnych parametrów zapytania do uwzględnienia przy użyciu interfejsu API. Na przykład `"queries": { "api-version": "2018-01-01" }` dodaje obiekt `?api-version=2018-01-01` wywołania. | 
| <*max-runs*> | Liczba całkowita | Domyślnie, wystąpienia przepływu pracy jest uruchamiany w tym samym czasie lub w sposób równoległy do [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*liczba*> wartość, zobacz [współbieżności wyzwalacza zmiany](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Liczba całkowita | Gdy przepływ pracy jest już uruchomiona maksymalna liczba wystąpień, które można zmienić na podstawie `runtimeConfiguration.concurrency.runs` właściwości wszelkie nowe przebiegi są umieszczane w tej kolejce [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [ograniczać przebiegi oczekujących zmian](#change-waiting-runs). | 
| <*splitOn-expression*> | String | Wyzwalacze, które zwracają tablice to wyrażenie odwołuje się do tablicy, dzięki czemu można utworzyć i uruchomić wystąpienia przepływu pracy dla każdego elementu tablicy, zamiast pętli "for each". <p>Na przykład to wyrażenie reprezentuje element w tablicy zwracanej w zawartości treści wyzwalacza: `@triggerbody()?['value']` |
| <*Opcja operacji*> | String | Można zmienić domyślne zachowanie przez ustawienie `operationOptions` właściwości. Aby uzyskać więcej informacji, zobacz [opcje operacji](#operation-options). |
||||

*Dane wyjściowe*
 
| Element | Type | Opis |
|---------|------|-------------|
| Nagłówki | Obiekt JSON | Nagłówki z odpowiedzi |
| treść | Obiekt JSON | Jednostka z odpowiedzi |
| Kod stanu: | Liczba całkowita | Kod stanu z odpowiedzi |
|||| 

*Przykład*

Ta definicja wyzwalacza sprawdza, czy adres e-mail każdego dnia w skrzynce odbiorczej dla konta usługi Office 365 Outlook: 

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook wyzwalacza

Ten wyzwalacz wysyła żądanie subskrypcji do punktu końcowego usługi za pomocą [zarządzanych przez firmę Microsoft interfejs API](../connectors/apis-list.md), zapewnia *adresów URL wywołania zwrotnego* do gdzie punkt końcowy może wysłać odpowiedź i czeka na punkt końcowy do reagowania. Aby uzyskać więcej informacji, zobacz [punktu końcowego subskrypcji](#subscribe-unsubscribe).

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*connection-name*> | String | Nazwa połączenia zarządzanego interfejsu API, który korzysta z przepływu pracy | 
| <*body-content*> | Obiekt JSON | Wszelkie zawartość komunikatu do wysłania jako ładunek do zarządzanego interfejsu API | 
|||| 

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*sposób ponawiania*> | Obiekt JSON | Dostosowuje zachowanie ponawiania sporadycznych błędów, które mają 408, 429 i kod stanu 5XX oraz wszystkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Obiekt JSON | Wszelkie parametry zapytania do uwzględnienia przy użyciu wywołania interfejsu API <p>Na przykład `"queries": { "api-version": "2018-01-01" }` dodaje obiekt `?api-version=2018-01-01` wywołania. | 
| <*max-runs*> | Liczba całkowita | Domyślnie, wystąpienia przepływu pracy jest uruchamiany w tym samym czasie lub w sposób równoległy do [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*liczba*> wartość, zobacz [współbieżności wyzwalacza zmiany](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Liczba całkowita | Gdy przepływ pracy jest już uruchomiona maksymalna liczba wystąpień, które można zmienić na podstawie `runtimeConfiguration.concurrency.runs` właściwości wszelkie nowe przebiegi są umieszczane w tej kolejce [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [ograniczać przebiegi oczekujących zmian](#change-waiting-runs). | 
| <*splitOn-expression*> | String | Wyzwalacze, które zwracają tablice to wyrażenie odwołuje się do tablicy, dzięki czemu można utworzyć i uruchomić wystąpienia przepływu pracy dla każdego elementu tablicy, zamiast pętli "for each". <p>Na przykład to wyrażenie reprezentuje element w tablicy zwracanej w zawartości treści wyzwalacza: `@triggerbody()?['value']` |
| <*Opcja operacji*> | String | Można zmienić domyślne zachowanie przez ustawienie `operationOptions` właściwości. Aby uzyskać więcej informacji, zobacz [opcje operacji](#operation-options). | 
|||| 

*Przykład*

Ta definicja wyzwalacza subskrybuje interfejsu API programu Outlook usługi Office 365, zawiera adres URL wywołania zwrotnego do punktu końcowego interfejsu API i czeka na punkt końcowy, aby reagować po nadejściu nowej wiadomości e-mail.

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>Wyzwalacz HTTP

Ten wyzwalacz sprawdza lub sonduje określony punkt końcowy opierają na harmonogramie cyklu określony. Odpowiedzi punktu końcowego określa, czy wykonywania przepływu pracy.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<endpoint-URL>",
      "headers": { "<header-content>" },
      "body": "<body-content>",
      "authentication": { "<authentication-method>" },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*method-type*> | String | Metoda HTTP na potrzeby sondowania określony punkt końcowy: "POBIERZ", "PUT", "POST", "POPRAWKA", "DELETE" | 
| <*endpoint-URL*> | String | HTTP lub HTTPS URL punktu końcowego, sondowanie <p>Maksymalny rozmiar ciągu: 2 KB | 
| <*time-unit*> | String | Jednostka czasu, który w tym artykule opisano, jak często wyzwalacza: "Drugi", "Minute", "Hour", "Day", "Week", "Month" | 
| <*Liczba z godziny jednostek*> | Liczba całkowita | Wartość, która określa, jak często wyzwalacza na podstawie częstotliwości, czyli liczba jednostek czasu, poczekać, aż wyzwalacz uruchamia się ponownie <p>Poniżej przedstawiono minimalne i maksymalne odstępach czasu: <p>-Miesiąc: 1 – 16 miesięcy </br>-Dzień: 1 – 500 dni </br>-Godzinny: 1-12 000 godzin </br>-Minutowy: 1 72,000 min </br>-Sekundowych: 1 9,999,999 sekundy<p>Na przykład jeśli interwał wynosi 6 i częstotliwość wynosi "Month", cykl jest co 6 miesięcy. | 
|||| 

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*header-content*> | Obiekt JSON | Nagłówki do wysłania wraz z żądaniem <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | String | Zawartość komunikatu do wysłania jako ładunek z żądaniem | 
| <*authentication-method*> | Obiekt JSON | Metoda żądania używa do uwierzytelniania. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie połączeń wychodzących usługi Scheduler](../scheduler/scheduler-outbound-authentication.md). Poza harmonogramem `authority` właściwość jest obsługiwana. Jeśli nie zostanie określony, wartością domyślną jest `https://login.windows.net`, ale można użyć innej wartości, takich jak`https://login.windows\-ppe.net`. |
| <*sposób ponawiania*> | Obiekt JSON | Dostosowuje zachowanie ponawiania sporadycznych błędów, które mają 408, 429 i kod stanu 5XX oraz wszystkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies). |  
 <*query-parameters*> | Obiekt JSON | Wszelkie parametry zapytania do uwzględnienia w żądaniu <p>Na przykład `"queries": { "api-version": "2018-01-01" }` dodaje obiekt `?api-version=2018-01-01` na żądanie. | 
| <*max-runs*> | Liczba całkowita | Domyślnie, wystąpienia przepływu pracy jest uruchamiany w tym samym czasie lub w sposób równoległy do [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*liczba*> wartość, zobacz [współbieżności wyzwalacza zmiany](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Liczba całkowita | Gdy przepływ pracy jest już uruchomiona maksymalna liczba wystąpień, które można zmienić na podstawie `runtimeConfiguration.concurrency.runs` właściwości wszelkie nowe przebiegi są umieszczane w tej kolejce [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [ograniczać przebiegi oczekujących zmian](#change-waiting-runs). | 
| <*Opcja operacji*> | String | Można zmienić domyślne zachowanie przez ustawienie `operationOptions` właściwości. Aby uzyskać więcej informacji, zobacz [opcje operacji](#operation-options). | 
|||| 

*Dane wyjściowe*

| Element | Type | Opis |
|---------|------|-------------| 
| Nagłówki | Obiekt JSON | Nagłówki z odpowiedzi | 
| treść | Obiekt JSON | Jednostka z odpowiedzi | 
| Kod stanu: | Liczba całkowita | Kod stanu z odpowiedzi | 
|||| 

*Wymagania dla żądań przychodzących*

Do pracy ze swoją aplikację logiki, punkt końcowy musi są zgodne ze wzorcem określonego wyzwalacza lub umowy i rozpoznaje tych właściwości:  
  
| Odpowiedź | Wymagane | Opis | 
|----------|----------|-------------| 
| Kod stanu | Yes | "200 OK" Kod stanu: uruchamia przebieg. Każdy inny kod stanu nie zaczyna się przebiegu. | 
| Nagłówek retry-after | Nie | Liczba sekund, aż aplikacja logiki sonduje punkt końcowy ponownie | 
| Nagłówek lokalizacji | Nie | Adres URL do wywołania na kolejnego interwału sondowania. Jeśli nie zostanie określony, oryginalnym adresie URL jest używany. | 
|||| 

*Przykład zachowania dla innych żądań*

| Kod stanu | Ponów próbę po | Zachowanie | 
|-------------|-------------|----------|
| 200 | {Brak} | Uruchamianie przepływu pracy, a następnie wyszukaj ponownie większej ilości danych po zdefiniowanego cyklu. | 
| 200 | 10 sekund | Uruchamianie przepływu pracy, a następnie sprawdź ponownie, aby uzyskać więcej danych po 10 sekundach. |  
| 202 | 60 sekund | Nie wyzwalacza przepływu pracy. Następnej próby odbywa się w ciągu jednej minuty, z zastrzeżeniem zdefiniowanego cyklu. Jeśli zdefiniowane cykl jest krótszy niż minuta, pierwszeństwo ma nagłówek retry-after. W przeciwnym razie zdefiniowanego cyklu jest używany. | 
| 400 | {Brak} | Niewłaściwe żądanie, nie uruchamiaj przepływu pracy. Jeśli nie `retryPolicy` jest zdefiniowany, zostanie użyta domyślna zasada. Po osiągnięciu liczbę ponownych prób wyzwalacz sprawdza, czy ponownie dane po zdefiniowanego cyklu. | 
| 500 | {Brak}| Błąd serwera, nie uruchamiaj przepływu pracy. Jeśli nie `retryPolicy` jest zdefiniowany, zostanie użyta domyślna zasada. Po osiągnięciu liczbę ponownych prób wyzwalacz sprawdza, czy ponownie dane po zdefiniowanego cyklu. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>HTTPWebhook wyzwalacza  

Ten wyzwalacz sprawia, że Twoja aplikacja logiki jest wywoływane przez utworzenie punktu końcowego, który można zarejestrować subskrypcji przez wywołanie adresu URL określonego punktu końcowego. Po utworzeniu tego wyzwalacza w przepływie pracy żądania wychodzącego sprawia, że wywołanie, aby zarejestrować subskrypcję. W ten sposób wyzwalacz może uruchomić nasłuchiwanie zdarzeń. Podczas operacji sprawia, że ten wyzwalacz jest nieprawidłowy, wychodzące żądanie automatycznie sprawia, że wywołanie, aby anulować subskrypcję. Aby uzyskać więcej informacji, zobacz [punktu końcowego subskrypcji](#subscribe-unsubscribe).

Można również określić [asynchronicznego limity](#asynchronous-limits) na **HTTPWebhook** wyzwalacza.
Działanie tego wyzwalacza jest zależna od sekcje, w których możesz użyć lub pominąć. 

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

Niektóre wartości, takie jak <*typ metody*>, są dostępne zarówno dla `"subscribe"` i `"unsubscribe"` obiektów.

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*method-type*> | String | Metoda HTTP do użycia dla żądania subskrypcji: "Pobierz", "PUT", "POST", "Poprawka" lub "DELETE" | 
| <*endpoint-subscribe-URL*> | String | Adres URL punktu końcowego, gdzie wysyłać żądania subskrypcji | 
|||| 

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*method-type*> | String | Metoda HTTP do użycia na żądanie anulowania. "Pobierz", "PUT", "POST", "Poprawka" lub "DELETE" | 
| <*endpoint-unsubscribe-URL*> | String | Adres URL punktu końcowego, gdzie wysyłać żądania anulowania | 
| <*body-content*> | String | Każdy komunikat zawartości do wysłania w subskrypcji lub anulowania żądania | 
| <*authentication-method*> | Obiekt JSON | Metoda żądania używa do uwierzytelniania. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie połączeń wychodzących usługi Scheduler](../scheduler/scheduler-outbound-authentication.md). |
| <*sposób ponawiania*> | Obiekt JSON | Dostosowuje zachowanie ponawiania sporadycznych błędów, które mają 408, 429 i kod stanu 5XX oraz wszystkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*max-runs*> | Liczba całkowita | Domyślnie, wszystkie wystąpienia przepływu pracy jest uruchamiany w tym samym czasie lub w sposób równoległy do [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*liczba*> wartość, zobacz [współbieżności wyzwalacza zmiany](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Liczba całkowita | Gdy przepływ pracy jest już uruchomiona maksymalna liczba wystąpień, które można zmienić na podstawie `runtimeConfiguration.concurrency.runs` właściwości wszelkie nowe przebiegi są umieszczane w tej kolejce [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [ograniczać przebiegi oczekujących zmian](#change-waiting-runs). | 
| <*Opcja operacji*> | String | Można zmienić domyślne zachowanie przez ustawienie `operationOptions` właściwości. Aby uzyskać więcej informacji, zobacz [opcje operacji](#operation-options). | 
|||| 

*Dane wyjściowe* 

| Element | Type | Opis |
|---------|------|-------------| 
| Nagłówki | Obiekt JSON | Nagłówki z odpowiedzi | 
| treść | Obiekt JSON | Jednostka z odpowiedzi | 
| Kod stanu: | Liczba całkowita | Kod stanu z odpowiedzi | 
|||| 

*Przykład*

Ten wyzwalacz tworzy subskrypcję do określonego punktu końcowego, zawiera adres URL wywołania zwrotnego unikatowy i czeka na technologii nowo opublikowanych artykułów.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Wyzwalacz cykliczny  

Ten wyzwalacz jest uruchamiany na podstawie na harmonogramie cyklu określonego i zapewnia prosty sposób tworzenia regularnie uruchomiony przepływ pracy. 

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*time-unit*> | String | Jednostka czasu, który w tym artykule opisano, jak często wyzwalacza: "Drugi", "Minute", "Hour", "Day", "Week", "Month" | 
| <*Liczba z godziny jednostek*> | Liczba całkowita | Wartość, która określa, jak często wyzwalacza na podstawie częstotliwości, czyli liczba jednostek czasu, poczekać, aż wyzwalacz uruchamia się ponownie <p>Poniżej przedstawiono minimalne i maksymalne odstępach czasu: <p>-Miesiąc: 1 – 16 miesięcy </br>-Dzień: 1 – 500 dni </br>-Godzinny: 1-12 000 godzin </br>-Minutowy: 1 72,000 min </br>-Sekundowych: 1 9,999,999 sekundy<p>Na przykład jeśli interwał wynosi 6 i częstotliwość wynosi "Month", cykl jest co 6 miesięcy. | 
|||| 

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*start-date-time-with-format-YYYY-MM-DDThh:mm:ss*> | String | Data i godzina rozpoczęcia w następującym formacie: <p>RRRR-MM-Ddtgg, jeśli określona strefa czasowa <p>— lub — <p>RRRR-MM-Ddtgg, jeśli nie określisz strefy czasowej <p>Tak na przykład, jeśli chcesz 18 września 2017 r. o 14:00, następnie określ "2017-09-18T14:00:00" i określić strefę czasową, np. "Pacyfik (czas standardowy)" lub podaj "2017-09-18T14:00:00Z" bez strefy czasowej. <p>**Uwaga:** Ten czas rozpoczęcia musi stosować [specyfikacji czasu daty ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [format daty i godziny UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [przesunięcie czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie określisz strefy czasowej, należy dodać litera "Z" na końcu bez żadnych spacji. Ta "Z" odnosi się do równowartości [morskich czasu](https://en.wikipedia.org/wiki/Nautical_time). <p>W przypadku prostych harmonogramów czas rozpoczęcia przypada po pierwszym wystąpieniu, natomiast w przypadku harmonogramów złożonych wyzwalacz nie zostanie wyzwolony wszelkie wcześniej niż czas rozpoczęcia. Aby uzyskać więcej informacji na temat daty rozpoczęcia i godziny, zobacz [Utwórz i harmonogram, regularnie wykonywanych zadań](../connectors/connectors-native-recurrence.md). | 
| <*Strefa czasowa*> | String | Ma zastosowanie tylko po określeniu godziny rozpoczęcia, ponieważ ten wyzwalacz nie zaakceptuje [przesunięcie czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Określ strefę czasową, który chcesz zastosować. | 
| <*one-or-more-hour-marks*> | Liczba całkowita lub tablicy liczb całkowitych | Jeśli określisz "Day" lub "Week" dla `frequency`, można określić co najmniej jeden liczby całkowite z zakresu od 0 do 23, oddzielone przecinkami, w formacie godziny, dnia, kiedy chcesz uruchomić przepływ pracy. <p>Na przykład jeśli określisz "10", "12" i "14", możesz uzyskać 10 AM, 12 PM i 14: 00 jako znaki godzinę. | 
| <*one-or-more-minute-marks*> | Liczba całkowita lub tablicy liczb całkowitych | Jeśli określisz "Day" lub "Week" dla `frequency`, można określić co najmniej jeden liczby całkowite z zakresu od 0 do 59, oddzielając je średnikami, jako minuty, godziny, kiedy chcesz uruchomić przepływ pracy. <p>Na przykład "30" można określić jako znacznik minutę i godziny, dnia, korzystając z poprzedniego przykładu, możesz uzyskać 10:30:00, 12:30:00 i 14:30:00. | 
| weekDays | Ciąg lub tablicę ciągów | Jeśli określisz "Week" dla `frequency`, można określić co najmniej jeden dzień, oddzielając je średnikami, jeśli chcesz uruchomić przepływ pracy: "Poniedziałek", "Wtorek", "Środa", "Czwartek", "Piątek", "Sobota" i "Niedziela" | 
| <*max-runs*> | Liczba całkowita | Domyślnie, wszystkie wystąpienia przepływu pracy jest uruchamiany w tym samym czasie lub w sposób równoległy do [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*liczba*> wartość, zobacz [współbieżności wyzwalacza zmiany](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Liczba całkowita | Gdy przepływ pracy jest już uruchomiona maksymalna liczba wystąpień, które można zmienić na podstawie `runtimeConfiguration.concurrency.runs` właściwości wszelkie nowe przebiegi są umieszczane w tej kolejce [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [ograniczać przebiegi oczekujących zmian](#change-waiting-runs). | 
| <*Opcja operacji*> | String | Można zmienić domyślne zachowanie przez ustawienie `operationOptions` właściwości. Aby uzyskać więcej informacji, zobacz [opcje operacji](#operation-options). | 
|||| 

*Przykład 1*

Ten wyzwalacz cykliczny podstawowa jest przeprowadzana codziennie:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Przykład 2*

Można określić daty rozpoczęcia i godzinę aktywowanie wyzwalacza. Ten wyzwalacz cykliczny rozpoczyna się w określonym dniu i jest uruchamiany codziennie:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Przykład 3*

Ten wyzwalacz cykliczny uruchamia na 9 września 2017 r. o 14:00 i generowane co tydzień każdy poniedziałek o godzinie 10:30:00, 12:30:00 i 14:30:00 i Pacyfik (czas standardowy):

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

Aby uzyskać więcej informacji oraz przykłady użycia tego wyzwalacza, zobacz [Utwórz i harmonogram, regularnie wykonywanych zadań](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Wyzwalacza żądania

Ten wyzwalacz sprawia, że Twoja aplikacja logiki jest wywoływane przez utworzenie punktu końcowego, który może akceptować żądań przychodzących. Ten wyzwalacz Podaj schematu JSON, który opisuje i sprawdza poprawność ładunku lub danych wejściowych, które wyzwalacz odbiera z żądania przychodzącego. Schemat również ułatwia właściwości wyzwalacza do odwołania z kolejnych akcjach w przepływie pracy. 

Aby wywołać ten wyzwalacz, należy użyć `listCallbackUrl` interfejsu API, który jest opisany w [interfejsu API REST usługi przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows). Aby dowiedzieć się, jak używać tego wyzwalacza jako punktu końcowego HTTP, zobacz [wywołania wyzwalacza lub zagnieżdżanie przepływy pracy za pomocą punktów końcowych HTTP](../logic-apps/logic-apps-http-endpoint.md).

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*property-name*> | String | Nazwa właściwości w schemacie JSON, który opisuje ładunku | 
| <*property-type*> | String | Typ właściwości | 
|||| 

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*method-type*> | String | Metoda żądania przychodzące, należy użyć do wywoływania aplikacji logiki: "POBIERZ", "PUT", "POST", "POPRAWKA", "DELETE" |
| <*relative-path-for-accepted-parameter*> | String | Ścieżka względna dla parametru, który może zaakceptować adresu URL usługi punktu końcowego | 
| <*wymagane właściwości*> | Tablica | Jedną lub więcej właściwości, które wymagają wartości | 
| <*max-runs*> | Liczba całkowita | Domyślnie, wszystkie wystąpienia przepływu pracy jest uruchamiany w tym samym czasie lub w sposób równoległy do [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*liczba*> wartość, zobacz [współbieżności wyzwalacza zmiany](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Liczba całkowita | Gdy przepływ pracy jest już uruchomiona maksymalna liczba wystąpień, które można zmienić na podstawie `runtimeConfiguration.concurrency.runs` właściwości wszelkie nowe przebiegi są umieszczane w tej kolejce [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [ograniczać przebiegi oczekujących zmian](#change-waiting-runs). | 
| <*Opcja operacji*> | String | Można zmienić domyślne zachowanie przez ustawienie `operationOptions` właściwości. Aby uzyskać więcej informacji, zobacz [opcje operacji](#operation-options). | 
|||| 

*Przykład*

Ten wyzwalacz Określa, że żądanie przychodzące należy użyć metody POST protokołu HTTP do wywołania wyzwalacza i obejmuje schemat, który sprawdza poprawność danych wejściowych z żądania przychodzącego: 

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>Warunki wyzwalania

Dla dowolnego wyzwalacza i tylko wyzwalaczy może zawierać tablicę zawierającą co najmniej jednego wyrażenia warunki, które określają, czy należy uruchomić przepływ pracy. Aby dodać `conditions` właściwości wyzwalacza w przepływie pracy, Otwórz aplikację logiki w edytorze widoku kodu.

Na przykład określić, czy wyzwalacz uruchamia, tylko gdy witryny sieci Web zwraca wystąpił wewnętrzny błąd serwera, odwołując się do kodu stanu wyzwalacza w `conditions` właściwości:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

Domyślnie wyzwalacza wyłącznie po "200 OK" odpowiedzi. Gdy wyrażenie odwołuje się do kodu stanu wyzwalacza, zachowanie domyślne wyzwalacza jest zastępowany. Tak Jeśli chcesz aby uruchomić więcej niż jeden kod stanu, na przykład "200" i kodem stanu "201" wyzwalacz, musi zawierać tego wyrażenia jako warunek: 

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Wielu uruchomień wyzwalacza

Jeśli wyzwalacz zwraca tablicę dla twojej aplikacji logiki do przetwarzania, czasami pętlę "for each" może trwać zbyt długo do przetworzenia każdego elementu tablicy. Zamiast tego można użyć **SplitOn** właściwości wyzwalacza do *debatch* tablicy. Usuwanie partii są rozróżniane elementów tablicy, a następnie uruchamia nowe wystąpienie przepływu pracy, który jest wykonywany dla każdego elementu tablicy. To podejście jest przydatne, na przykład, gdy w celu odpytania punktu końcowego, który może zwrócić wiele nowych elementów między interwałami sondowania.
Na maksymalną liczbę tablicy elementów, które **SplitOn** można przetwarzać w przebiegu aplikacji logiki pojedynczego, zobacz [limity i Konfiguracja](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Nie można użyć **SplitOn** za pomocą wzorca synchronicznej odpowiedzi. Każdy przepływ pracy, który używa **SplitOn** i odpowiedzi zawiera działanie jest uruchamiane asynchronicznie i natychmiast wysyła `202 ACCEPTED` odpowiedzi.

Jeśli plik struktury Swagger tego wyzwalacza w tym artykule opisano ładunek, który jest tablicą, **SplitOn** właściwości jest automatycznie dodawany do wyzwalacza. W przeciwnym razie Dodaj tę właściwość w ładunku odpowiedzi, zawierającej tablicy, która ma być debatch. 

*Przykład*

Załóżmy, że masz interfejs API, która zwraca tej odpowiedzi: 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```
 
Twoja aplikacja logiki musi tylko zawartość z tablicy `Rows`, aby można było utworzyć wyzwalacz, np. w tym przykładzie:

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Jeśli używasz `SplitOn` polecenia i nie można pobrać właściwości, które wykraczają poza tablicy. Aby na przykład nie można pobrać `status` właściwości w odpowiedzi zwrócony z interfejsu API.
> 
> Aby uniknąć awarii, jeśli `Rows` właściwość nie istnieje, w tym przykładzie użyto `?` operatora.

Twoja definicja przepływu pracy można teraz używać `@triggerBody().name` można pobrać `name` wartości, które są `"customer-name-one"` z pierwszym uruchomieniu i `"customer-name-two"` z drugiego przebiegu. Dlatego wyzwalacza danych wyjściowych wygląd te przykłady, takie jak:

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Przegląd akcji

Usługa Azure Logic Apps udostępnia różne typy akcji — każdy z różnych danych wejściowych, które określają zachowanie unikatowy akcji. 

Akcje są te elementy wysokiego poziomu, chociaż niektóre są opcjonalne:

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------|
| <*Nazwa akcji*> | String | Nazwa akcji | 
| <*action-type*> | String | Typ akcji, na przykład "Http" lub "ApiConnection"| 
| <*Nazwa danych wejściowych*> | String | Nazwa dla danych wejściowych, który definiuje zachowanie akcji | 
| <*input-value*> | Różne | Wartość wejściowa, który może być ciąg, liczba całkowita, obiekt JSON i tak dalej | 
| <*previous-trigger-or-action-status*> | Obiekt JSON | Nazwa i Wynikowy stan wyzwalacza lub akcji, który należy uruchomić natychmiast, zanim będzie można uruchomić tego bieżącej akcji | 
|||| 

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------|
| <*sposób ponawiania*> | Obiekt JSON | Dostosowuje zachowanie ponawiania sporadycznych błędów, które mają 408, 429 i kod stanu 5XX oraz wszystkie wyjątki łączności. Aby uzyskać więcej informacji zobacz zasady ponawiania prób. | 
| <*runtime-config-options*> | Obiekt JSON | W przypadku niektórych działań, można zmienić zachowanie działania w czasie wykonywania, ustawiając `runtimeConfiguration` właściwości. Aby uzyskać więcej informacji, zobacz [ustawień konfiguracji środowiska uruchomieniowego](#runtime-config-options). | 
| <*Opcja operacji*> | String | W przypadku niektórych działań, można zmienić domyślne zachowanie przez ustawienie `operationOptions` właściwości. Aby uzyskać więcej informacji, zobacz [opcje operacji](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Lista typów akcji

Poniżej przedstawiono niektóre typy powszechnie używane akcji: 

* [Typy wbudowane akcji](#built-in-actions) takich jak te przykłady i inne: 

  * [**HTTP** ](#http-action) dla wywoływanie punktów końcowych za pośrednictwem protokołu HTTP lub HTTPS

  * [**Odpowiedź** ](#response-action) reagowania na żądania

  * [**Funkcja** ](#function-action) wywoływania usługi Azure Functions

  * Dane operacji akcje, takie jak [ **Dołącz**](#join-action), [ **Compose**](#compose-action), [ **tabeli** ](#table-action), [ **Wybierz**](#select-action)i innym osobom, które tworzą lub przekształcać dane z różnych danych wejściowych

  * [**Przepływ pracy** ](#workflow-action) do wywoływania innego przepływu pracy aplikacji logiki

* [Zarządzane typy akcji interfejsu API](#managed-api-actions) takich jak [ **ApiConnection** ](#apiconnection-action) i [ **ApiConnectionWebHook** ](#apiconnectionwebhook-action) wywołujące różne łączniki i interfejsy API zarządzane przez firmę Microsoft, na przykład Azure Service Bus, usługi Office 365 Outlook, usłudze Power BI, Azure Blob Storage, usługi OneDrive, GitHub i więcej

* [Kontrolowanie typów akcji przepływu pracy](#control-workflow-actions) takich jak [ **Jeśli**](#if-action), [ **Foreach**](#foreach-action), [ **przełącznika**  ](#switch-action), [ **Zakres**](#scope-action), i [ **aż**](#until-action), który zawiera inne akcje i pomóc organizowanie wykonywania przepływu pracy

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Wbudowane akcje

| Typ akcji | Opis | 
|-------------|-------------| 
| [**Compose**](#compose-action) | Tworzy pojedynczy dane wyjściowe na podstawie danych wejściowych, które mogą mieć różnych typów. | 
| [**— Funkcja**](#function-action) | Wywołuje funkcję platformy Azure. | 
| [**HTTP**](#http-action) | Wywołuje punkt końcowy HTTP. | 
| [**Join**](#join-action) | Tworzy ciąg ze wszystkich elementów w tablicy, a następnie dzieli te elementy ze znakiem określonego ogranicznika. | 
| [**Parse JSON**](#parse-json-action) | Tworzy zawartości tokeny przyjazny dla użytkownika przy użyciu właściwości w formacie JSON. Następnie można odwoływać się te właściwości, umieszczając tokenów w aplikacji logiki. | 
| [**Query**](#query-action) | Tworzy tablicę z elementami w innej tablicy na podstawie warunku lub filtru. | 
| [**Response**](#response-action) | Tworzy odpowiedź żądania lub połączenia przychodzącego. | 
| [**Select**](#select-action) | Tworzy tablicę z obiektami JSON poprzez przekształcenie elementów z innej tablicy oparte na określonej mapy. | 
| [**Table**](#table-action) | Tworzy tabelę CSV lub HTML z tablicy. | 
| [**Zakończenie**](#terminate-action) | Zatrzymuje aktywnie uruchomiony przepływ pracy. | 
| [**Wait**](#wait-action) | Wstrzymuje przepływ pracy dla określonego czasu trwania lub do określonej daty i godziny. | 
| [**Przepływ pracy**](#workflow-action) | Zagnieżdżony przepływ pracy wewnątrz innego przepływu pracy. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Zarządzane operacje interfejsu API

| Typ akcji | Opis | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Wywołuje punkt końcowy HTTP za pomocą [zarządzanych przez firmę Microsoft interfejs API](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Działa jak element Webhook protokołu HTTP, ale używa [zarządzanych przez firmę Microsoft interfejs API](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Działania przepływu pracy

Te akcje pomóc Ci kontrolować wykonywanie przepływu pracy i zawierać inne akcje. Z poza formant akcji przepływu pracy, możesz bezpośrednio odwoływać akcji wewnątrz akcji przepływu pracy tego formantu. Na przykład, jeśli masz `Http` akcji wewnątrz zakresu, możesz odwoływać się do `@body('Http')` wyrażenia z dowolnego miejsca w przepływie pracy. Jednak akcje, które istnieją w akcji przepływu pracy kontroli mogą "uruchamiać tylko" inne akcje, które są w tej samej strukturze kontroli w przepływie pracy.

| Typ akcji | Opis | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Uruchom te same akcje w pętli dla każdego elementu w tablicy. | 
| [**If**](#if-action) | Wykonywania działań na ich podstawie określonego warunku jest wartość PRAWDA lub FAŁSZ. | 
| [**Zakres**](#scope-action) | Uruchamianie akcji w oparciu o stan grupy z zestawu działań. | 
| [**Switch**](#switch-action) | Uruchom akcje zorganizowane w przypadkach, gdy wartości z wyrażeń, obiekty lub tokenów są zgodne z wartościami, które określono w każdym przypadku. | 
| [**Until**](#until-action) | Uruchom działania w pętli, aż określony warunek ma wartość true. | 
|||  

## <a name="actions---detailed-reference"></a>Akcje — szczegółową dokumentację

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>Akcja APIConnection

Ta akcja spowoduje wysłanie żądania HTTP do [zarządzanych przez firmę Microsoft interfejs API](../connectors/apis-list.md) i wymaga informacji na temat interfejsu API i parametrów oraz odwołania do prawidłowego połączenia. 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*Nazwa akcji*> | String | Nazwa akcji, podany przez łącznik | 
| <*Nazwa interfejsu API*> | String | Nazwa zarządzanych przez firmę Microsoft interfejs API, który jest używany dla połączenia | 
| <*method-type*> | String | Metoda HTTP dla wywołania interfejsu API: "Pobierz", "PUT", "POST", "Poprawka" lub "DELETE" | 
| <*Operacja interfejsu API*> | String | Operacja wywołania interfejsu API | 
|||| 

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*other-action-specific-input-properties*> | Obiekt JSON | Inne wejściowe właściwości, które mają zastosowanie do tej określonej akcji | 
| <*sposób ponawiania*> | Obiekt JSON | Dostosowuje zachowanie ponawiania sporadycznych błędów, które mają 408, 429 i kod stanu 5XX oraz wszystkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Obiekt JSON | Wywołaj żadnych parametrów zapytania do uwzględnienia przy użyciu interfejsu API. <p>Na przykład `"queries": { "api-version": "2018-01-01" }` dodaje obiekt `?api-version=2018-01-01` wywołania. | 
| <*other-action-specific-properties*> | Obiekt JSON | Inne właściwości, które są stosowane do tej konkretnej akcji | 
|||| 

*Przykład*

W tym artykule opisano tę definicję **Wyślij wiadomość e-mail** akcji dla łącznika usługi Office 365 Outlook, który jest zarządzany przez firmę Microsoft interfejs API: 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>Akcja APIConnectionWebhook

Ta akcja spowoduje wysłanie żądania subskrypcji za pośrednictwem protokołu HTTP do punktu końcowego usługi za pomocą [zarządzanych przez firmę Microsoft interfejs API](../connectors/apis-list.md), zapewnia *adresów URL wywołania zwrotnego* do gdzie punkt końcowy może wysłać odpowiedź i czeka na punkt końcowy do odpowiedź. Aby uzyskać więcej informacji, zobacz [punktu końcowego subskrypcji](#subscribe-unsubscribe).

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Niektóre wartości, takie jak <*typ metody*>, są dostępne zarówno dla `"subscribe"` i `"unsubscribe"` obiektów.

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*Nazwa akcji*> | String | Nazwa akcji, podany przez łącznik | 
| <*method-type*> | String | Metoda HTTP do użycia dla subskrypcji lub anulowania subskrypcji z punktu końcowego: "Pobierz", "PUT", "POST", "Poprawka" lub "DELETE" | 
| <*api-subscribe-URL*> | String | Identyfikator URI do użycia dla subskrypcji do interfejsu API | 
|||| 

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*api-unsubscribe-URL*> | String | Identyfikator URI do użycia dla anulowania subskrypcji z interfejsu API | 
| <*header-content*> | Obiekt JSON | Wszystkie nagłówki, aby wysyłać w żądaniu <p>Na przykład, aby ustawić język, a następnie wpisz na żądanie: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Obiekt JSON | Zawartość komunikatu do wysłania w żądaniu | 
| <*authentication-method*> | Obiekt JSON | Metoda żądania używa do uwierzytelniania. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie połączeń wychodzących usługi Scheduler](../scheduler/scheduler-outbound-authentication.md). |
| <*sposób ponawiania*> | Obiekt JSON | Dostosowuje zachowanie ponawiania sporadycznych błędów, które mają 408, 429 i kod stanu 5XX oraz wszystkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Obiekt JSON | Wszelkie parametry zapytania do uwzględnienia przy użyciu wywołania interfejsu API <p>Na przykład `"queries": { "api-version": "2018-01-01" }` dodaje obiekt `?api-version=2018-01-01` wywołania. | 
| <*other-action-specific-input-properties*> | Obiekt JSON | Inne wejściowe właściwości, które mają zastosowanie do tej określonej akcji | 
| <*other-action-specific-properties*> | Obiekt JSON | Inne właściwości, które są stosowane do tej konkretnej akcji | 
|||| 

Można również określić limity na **ApiConnectionWebhook** akcji w taki sam sposób jak [limity asynchronicznego HTTP](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Akcja redagowania

Ta akcja powoduje utworzenie pojedynczego wyjścia z wielu danych wejściowych, włącznie z wyrażenia. Wynik oraz danych wejściowych może mieć dowolny typ, który natywnie obsługuje usługi Azure Logic Apps, takich jak tablice, obiekty JSON, XML i danych binarnych.
Wynik akcji można następnie użyć w innych działań. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Wymagane* 

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*dane wejściowe redagowania*> | Dowolne | Dane wejściowe do utworzenia pojedynczego wyjścia | 
|||| 

*Przykład 1*

<!-- markdownlint-disable MD038 -->
Ta definicja akcji scala `abcdefg ` za pomocą spacji i wartość `1234`:
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Oto dane wyjściowe, które ta akcja tworzy:

`abcdefg 1234`

*Przykład 2*

Ta definicja akcji scala zmiennej ciągu, który zawiera `abcdefg` i zmienna typu Liczba całkowita, która zawiera `1234`:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Oto dane wyjściowe, które ta akcja tworzy:

`"abcdefg1234"`

<a name="function-action"></a>

### <a name="function-action"></a>Akcja — funkcja

Ta akcja wymaga wcześniej utworzony [funkcji platformy Azure](../azure-functions/functions-create-first-azure-function.md).

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------|  
| <*Identyfikator platformy Azure — funkcja*> | String | Identyfikator zasobu dla funkcji platformy Azure, który ma zostać wywołana. Oto formatu dla tej wartości:<p>"/subscriptions/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*method-type*> | String | Metoda HTTP do użycia podczas wywoływania funkcji: "Pobierz", "PUT", "POST", "Poprawka" lub "DELETE" <p>Jeśli nie zostanie określony, wartość domyślna to metoda "POST". | 
||||

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------|  
| <*header-content*> | Obiekt JSON | Wszystkie nagłówki, aby wysłać z wywołania <p>Na przykład, aby ustawić język, a następnie wpisz na żądanie: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Obiekt JSON | Zawartość komunikatu do wysłania w żądaniu | 
| <*query-parameters*> | Obiekt JSON | Wszelkie parametry zapytania do uwzględnienia przy użyciu wywołania interfejsu API <p>Na przykład `"queries": { "api-version": "2018-01-01" }` dodaje obiekt `?api-version=2018-01-01` wywołania. | 
| <*other-action-specific-input-properties*> | Obiekt JSON | Inne wejściowe właściwości, które mają zastosowanie do tej określonej akcji | 
| <*other-action-specific-properties*> | Obiekt JSON | Inne właściwości, które są stosowane do tej konkretnej akcji | 
||||

Po zapisaniu aplikacji logiki, aparat usługi Logic Apps wykonuje te kontrole odwołania funkcji:

* Przepływ pracy musi mieć dostęp do funkcji.

* Przepływ pracy można użyć tylko standardowe wyzwalacz protokołu HTTP lub ogólny wyzwalacza elementu webhook JSON. 

  Aparat usługi Logic Apps pobiera i umieszcza w pamięci podręcznej adresu URL wyzwalacza, który jest używany w czasie wykonywania. Jednakże, jeśli każdej operacji unieważnia buforowane URL, **funkcji** akcja zakończy się niepowodzeniem w czasie wykonywania. Aby rozwiązać ten problem, należy ponownie Zapisz aplikację logiki, dzięki czemu aplikacja logiki pobiera i zapisuje w pamięci podręcznej adresu URL wyzwalacza ponownie.

* Funkcja nie może mieć żadnych tras zdefiniowanych.

* Dozwolone są tylko "Funkcja" i poziomów dostępu "anonimowy". 

*Przykład*

Ta definicja akcji wywołuje wcześniej utworzonej funkcji "GetProductID":

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>Akcja HTTP

Ta akcja spowoduje wysłanie żądania do określonego punktu końcowego i sprawdza odpowiedzi, aby określić, czy należy uruchomić przepływ pracy. 

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>"
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*method-type*> | String | Metoda do użycia podczas wysyłania żądania: "Pobierz", "PUT", "POST", "Poprawka" lub "DELETE" | 
| <*HTTP-or-HTTPS-endpoint-URL*> | String | HTTP lub HTTPS punkt końcowy do wywołania. Maksymalny rozmiar ciągu: 2 KB | 
|||| 

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*header-content*> | Obiekt JSON | Wszelkie nagłówki do wysłania wraz z żądaniem <p>Na przykład, aby ustawić język i typ: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Obiekt JSON | Zawartość komunikatu do wysłania w żądaniu | 
| <*sposób ponawiania*> | Obiekt JSON | Dostosowuje zachowanie ponawiania sporadycznych błędów, które mają 408, 429 i kod stanu 5XX oraz wszystkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [zasady ponawiania](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Obiekt JSON | Wszelkie parametry zapytania do uwzględnienia w żądaniu <p>Na przykład `"queries": { "api-version": "2018-01-01" }` dodaje obiekt `?api-version=2018-01-01` wywołania. | 
| <*other-action-specific-input-properties*> | Obiekt JSON | Inne wejściowe właściwości, które mają zastosowanie do tej określonej akcji | 
| <*other-action-specific-properties*> | Obiekt JSON | Inne właściwości, które są stosowane do tej konkretnej akcji | 
|||| 

*Przykład*

Definicja ta akcja pobiera najnowsze wiadomości, wysyłając żądanie do określonego punktu końcowego:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>Dołącz do akcji

Ta akcja tworzy ciąg ze wszystkich elementów w tablicy i oddziela te elementy ze znakiem określonego ogranicznika. 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*array*> | Tablica | Tablica lub wyrażenie, które zawiera elementy źródła. Jeśli określisz wyrażenia, należy ująć to wyrażenie z podwójnymi cudzysłowami. | 
| <*delimiter*> | Ciąg jednego znaku | Znak oddzielający każdego elementu w ciągu | 
|||| 

*Przykład*

Załóżmy, że masz zmiennej utworzonej wcześniej "myIntegerArray", która zawiera Ta tablica liczb całkowitych: 

`[1,2,3,4]` 

Definicja ta akcja pobiera wartości zmiennej za pomocą `variables()` funkcji w wyrażeniu i tworzy te parametry z tych wartości, które są oddzielone przecinkami: `"1,2,3,4"`

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>Analizowanie JSON akcji

Ta akcja powoduje utworzenie pola przyjazny dla użytkownika lub *tokenów* przy użyciu właściwości w zawartości JSON. Te właściwości można następnie dostępu w aplikacji logiki przy użyciu tokenów zamiast tego. Na przykład jeśli chcesz użyć danych wyjściowych JSON z usług takich jak usługi Azure Service Bus i Azure Cosmos DB, mogą być tę akcję w aplikacji logiki, dzięki czemu możesz łatwiej odwoływać się do danych w te dane wyjściowe. 

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*JSON-source*> | Obiekt JSON | Zawartość JSON, który chcesz przeanalizować | 
| <*JSON-schema*> | Obiekt JSON | Schemat JSON opisujące zawartość JSON, używanym w akcji do analizowania źródła zawartości JSON bazowego. <p>**Porada**: W Projektancie aplikacji logiki możesz podać schematu lub podać przykładowy ładunek akcji można wygenerować schematu. | 
|||| 

*Przykład*

Definicja ta akcja tworzy te tokeny, której można w przepływie pracy, ale tylko w akcji wykonywania następujących **Przeanalizuj dane JSON** akcji: 

`FirstName`, `LastName`, i `Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

W tym przykładzie właściwość "content" Określa zawartość JSON dla akcji, które można przeanalizować. Można również dołączyć tę zawartość JSON jako przykładowego ładunku do generowania schemat.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

Właściwość "schema" Określa schematu JSON, używane do opisywania zawartość JSON:

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>Akcja kwerendy

Ta akcja tworzy tablicę z elementów w innej tablicy na podstawie określonego warunku lub filtru.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*array*> | Tablica | Tablica lub wyrażenie, które zawiera elementy źródła. Jeśli określisz wyrażenia, należy ująć to wyrażenie z podwójnymi cudzysłowami. |
| <*condition-or-filter*> | String | Na warunek używany do filtrowania elementów w tablicy źródłowej <p>**Uwaga**: Jeśli żadne wartości nie spełnia warunku, ta akcja stworzy pustą tablicę. |
|||| 

*Przykład*

Definicja ta akcja tworzy tablicę z wartości jest większa niż określona wartość, która jest dwa:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>Akcja odpowiedzi  

Ta akcja powoduje utworzenie ładunek odpowiedzi na żądania HTTP. 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*response-status-code*> | Liczba całkowita | Kod stanu HTTP są wysyłane do żądania przychodzącego. Domyślny kod to "200 OK", ale kod może być prawidłowym stanem kodu, który rozpoczyna się za pomocą 2xx, 4xx lub 5xx, ale nie z 3xxx. | 
|||| 

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*response-headers*> | Obiekt JSON | Jeden lub więcej nagłówków do uwzględnienia z odpowiedzią | 
| <*response-body*> | Różne | Treść odpowiedzi, który może być ciąg, obiekt JSON lub nawet binarne zawartość z poprzedniej akcji | 
|||| 

*Przykład*

Definicja ta akcja tworzy odpowiedź na żądanie HTTP z określonym kodem stanu, treść wiadomości i nagłówków wiadomości:

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*Ograniczenia*

W przeciwieństwie do innych działań **odpowiedzi** akcja ma specjalne ograniczenia: 

* Przepływ pracy można użyć **odpowiedzi** akcję tylko wtedy, gdy przepływ pracy zaczyna się od wyzwalacza żądania HTTP, co oznacza przepływu pracy musi być wyzwalane przez żądanie HTTP.

* Przepływ pracy można użyć **odpowiedzi** akcji w dowolnym miejscu *z wyjątkiem* wewnątrz **Foreach** pętli, **aż** pętli, tym sekwencyjne pętli i równoległych gałęziach. 

* Oryginalne żądanie HTTP pobiera odpowiedź Twój przepływ pracy, tylko wtedy, gdy wszystkie akcje wymagane przez **odpowiedzi** kończenia działania w ramach [limit czasu żądania HTTP](../logic-apps/logic-apps-limits-and-config.md#request-limits).

  Jednak jeśli przepływ pracy wymaga innego zagnieżdżonego przepływu pracy aplikacji logiki, nadrzędny przepływ pracy czeka, aż zakończy zagnieżdżonego przepływu pracy, niezależnie od tego, ile czasu upływa zanim zakończy się zagnieżdżony przepływ pracy.

* Jeśli przepływ pracy używa **odpowiedzi** akcji i wzorzec synchronicznej odpowiedzi, przepływ pracy nie można również użyć **splitOn** polecenia w definicji wyzwalacza, ponieważ to polecenie powoduje utworzenie wielu uruchomień. Sprawdź dla tego przypadku stosowania metody PUT, a jeśli ma wartość true, zwraca odpowiedź "złe żądanie".

  W przeciwnym razie, jeśli przepływ pracy używa **splitOn** polecenia i **odpowiedzi** akcji przepływu pracy jest uruchamiane asynchronicznie i natychmiast powraca odpowiedzi "202 ZAAKCEPTOWANO".

* Kiedy Twój przepływ pracy wykonywanie osiągnie **odpowiedzi** akcji, ale przychodzące żądanie już otrzymało odpowiedzi **odpowiedzi** akcji jest oznaczony jako "Niepowodzenie" z powodu konfliktu. I w wyniku uruchomienia aplikacji logiki również jest oznaczona stanem "Niepowodzenie".

<a name="select-action"></a>

### <a name="select-action"></a>Wybierz akcję

Ta akcja tworzy tablicę z obiektami JSON poprzez przekształcenie elementów z innej tablicy oparte na określonej mapy. Tablica danych wyjściowych i tablica źródłowa zawsze ma taką samą liczbę elementów. Nie można zmienić liczby obiektów w tablicy danych wyjściowych, ale można dodać lub usunąć właściwości i ich wartości w odniesieniu do tych obiektów. `select` Właściwość określa co najmniej jedną parę klucz wartość definiujące mapy do przekształcania elementów w tablicy źródłowej. Pary klucz wartość reprezentuje właściwości i jego wartość dla wszystkich obiektów w tablicy danych wyjściowych. 

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*Wymagane* 

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*array*> | Tablica | Tablica lub wyrażenie, które zawiera elementy źródła. Upewnij się, że ujmij wyrażenie z podwójnymi cudzysłowami. <p>**Uwaga**: Jeśli tablica źródłowa jest pusta, ta akcja stworzy pustą tablicę. | 
| <*key-name*> | String | Nazwa właściwości, przypisane do wynik <*wyrażenia*> <p>Aby dodać nową właściwość dla wszystkich obiektów w tablicy danych wyjściowych, należy podać <*nazwa klucza*> dla tej właściwości oraz element <*wyrażenie*> dla wartości właściwości. <p>Aby usunąć właściwość z wszystkich obiektów w tablicy, należy pominąć <*nazwa klucza*> dla tej właściwości. | 
| <*expression*> | String | Wyrażenie, które przekształca elementu w tablicy źródłowej i przypisuje wynik <*nazwa klucza*> | 
|||| 

**Wybierz** akcja tworzy tablicę jako dane wyjściowe, więc dowolna akcja, którą chce użyć tych danych wyjściowych musi zaakceptować tablicy lub tablicy należy przekonwertować na typ, który akceptuje akcję odbiorcy. Na przykład, aby dokonać konwersji tablicy danych wyjściowych do ciągu, można przekazać tę tablicę do **Compose** akcji, a następnie Odwołaj dane wyjściowe z **Compose** akcji w innych czynności użytkownika.

*Przykład*

Definicja ta akcja tworzy tablicę obiektów JSON z tablicę liczb całkowitych. Akcja dokonuje iteracji tablicy źródłowej pobiera każda wartość całkowitą przy użyciu `@item()` wyrażenia i przypisuje każda wartość "`number`" właściwość w każdym obiekcie JSON: 

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

Poniżej przedstawiono tablicę, która tworzy tę akcję:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Aby użyć tej tablicy danych wyjściowych w innych działań, Przekaż te dane wyjściowe do **Compose** akcji:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Następnie można użyć danych wyjściowych **Compose** akcji w innych akcji, na przykład **Office 365 Outlook — Wyślij wiadomość e-mail** akcji:

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>Akcja tabeli

Ta akcja tworzy tabelę CSV lub HTML z tablicy. Dla tablic z obiektami JSON, ta akcja powoduje automatyczne utworzenie nagłówków kolumn z nazwami właściwości obiektów. Dla tablic przy użyciu innych typów danych należy określić wartości i nagłówków kolumn. Na przykład ta tablica zawiera właściwości "ID" i "Product_Name", które ta akcja służy do nazw nagłówka kolumny:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*Wymagane* 

| Wartość | Type | Opis | 
|-------|------|-------------| 
| < CSV *lub* HTML >| String | Format dla tabeli, którą chcesz utworzyć | 
| <*array*> | Tablica | Tablica lub wyrażenie, które zawiera elementy źródła w tabeli <p>**Uwaga**: Jeśli tablica źródłowa jest pusta, ta akcja stworzy pustej tabeli. | 
|||| 

*Opcjonalne*

Aby określić lub dostosowywanie nagłówków kolumn i wartości, użyj `columns` tablicy. Gdy `header-value` pary mają taką samą nazwę nagłówka, ich wartości są wyświetlane w tej samej kolumnie pod nazwą tego nagłówka. W przeciwnym razie każdy nagłówek unikatowy definiuje unikatową kolumnę.

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*column-name*> | String | Nazwa nagłówka kolumny | 
| <*column-value*> | Dowolne | Wartość w tej kolumnie | 
|||| 

*Przykład 1*

Załóżmy, że masz zmiennej utworzonej wcześniej "myItemArray", która zawiera obecnie tej tablicy: 

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Definicja ta akcja tworzy tabelę CSV ze zmiennej "myItemArray". Wyrażenie użyte przez `from` właściwości pobiera tablicę z "myItemArray" przy użyciu `variables()` funkcji: 

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Poniżej przedstawiono tabelę CSV, którego ta akcja tworzy: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Przykład 2*

Definicja ta akcja tworzy tabelę HTML na podstawie zmiennej "myItemArray". Wyrażenie użyte przez `from` właściwości pobiera tablicę z "myItemArray" przy użyciu `variables()` funkcji: 

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Oto tabela HTML, którego ta akcja tworzy: 

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Jabłka</td></tr><tr><td>1</td><td>Pomarańcze</td></tr></tbody></table>

*Przykład 3*

Definicja ta akcja tworzy tabelę HTML na podstawie zmiennej "myItemArray". Jednak w tym przykładzie zastępuje domyślne nazwy nagłówków kolumn za pomocą "Stock_ID" i "Description", a dodaje wyraz "Organiczne" do wartości w kolumnie "Description".

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

Oto tabela HTML, którego ta akcja tworzy: 

<table><thead><tr><th>Stock_ID</th><th>Opis</th></tr></thead><tbody><tr><td>0</td><td>Jabłka organicznych</td></tr><tr><td>1</td><td>Pomarańcze organicznych</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Zakończenie akcji

Ta akcja zatrzymuje działanie dla wystąpienia przepływu pracy, anuluje wszystkie akcje w toku, pomija wszelkie pozostałe akcje i zwraca określony stan. Na przykład, można użyć **Zakończ** akcję, gdy aplikacja logiki całkowicie wyjść ze stanu błędu. Ta akcja nie ma wpływu na już ukończonych akcji i nie może występować wewnątrz **Foreach** i **aż** pętli, tym sekwencyjne pętli. 

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*status*> | String | Stan aby powrócić do uruchomienia: "Nie", "Anulowane" lub "Powodzenie" |
|||| 

*Opcjonalne*

Właściwości dla obiektu "runStatus" mają zastosowanie tylko wtedy, gdy dla właściwości "runStatus" ustawiono stan "Niepowodzenie".

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*error-code-or-name*> | String | Kod lub nazwa błędu |
| <*error-message*> | String | Może podjąć wiadomości lub tekst, który opisuje błąd i wszelkie akcje użytkownika aplikacji | 
|||| 

*Przykład*

Ta definicja akcji zatrzyma przebieg przepływu pracy, ustawia stan uruchomienia, aby "Niepowodzenie", a następnie zwraca stan, kod błędu oraz komunikat o błędzie:

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>Poczekaj akcji  

Ta akcja wstrzymuje wykonywanie przepływu pracy przez określony interwał lub do momentu określonego czasu, ale nie oba. 

*Określony interwał*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*Określony czas*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*Liczba jednostek*> | Liczba całkowita | Aby uzyskać **opóźnienie** akcji, liczba oczekiwania | 
| <*interval*> | String | Aby uzyskać **opóźnienie** akcji, interwał oczekiwania: "Drugi", "Minute", "Hour", "Day", "Week", "Month" | 
| <*date-time-stamp*> | String | Aby uzyskać **opóźnienie do momentu** akcji, datę i godzinę wznowić wykonywanie. Ta wartość musi mieć [format daty i godziny UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
|||| 

*Przykład 1*

Ta definicja akcji wstrzymuje przepływ pracy przez 15 minut:

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*Przykład 2*

Ta definicja akcji wstrzymuje przepływ pracy do określonej godziny:

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>Akcja przepływu pracy

Ta akcja wymaga innej aplikacji logiki utworzonej wcześniej, co oznacza, można dołączyć i wielokrotnie używać innych przepływów pracy aplikacji logiki. Możesz również użyć dane wyjściowe z podrzędnego lub *zagnieżdżonych* aplikacji logiki w akcji, które należy wykonać zagnieżdżoną aplikację logiki, pod warunkiem, że aplikacja logiki podrzędne zwraca odpowiedź.

Aparat usługi Logic Apps sprawdza, czy dostęp do wyzwalacza, który chcesz wywołać, dlatego upewnij się, że ma dostęp do tego wyzwalacza. Ponadto aplikacja logiki zagnieżdżonych musi spełniać następujące kryteria:

* Wyzwalacz sprawia, że aplikacja logiki zagnieżdżone jest możliwy do wywołania, takie jak [żądania](#request-trigger) lub [HTTP](#http-trigger) wyzwalacza

* Tej samej subskrypcji platformy Azure jako swoją aplikację logiki nadrzędnego

* Aby użyć danych wyjściowych z zagnieżdżoną aplikację logiki w nadrzędnej aplikacji logiki, aplikację logiki zagnieżdżonych musi mieć [odpowiedzi](#response-action) akcji 

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*nested-logic-app-name*> | String | Nazwa aplikacji logiki, który chcesz wybrać | 
| <*trigger-name*> | String | Nazwa wyzwalacza w aplikacji logiki zagnieżdżonych, który ma zostać wywołana | 
| <*Azure-subscription-ID*> | String | Identyfikator subskrypcji platformy Azure dla aplikacji logiki zagnieżdżonych |
| <*Azure-resource-group*> | String | Nazwa grupy zasobów platformy Azure dla aplikacji logiki zagnieżdżonych |
| <*nested-logic-app-name*> | String | Nazwa aplikacji logiki, który chcesz wybrać |
||||

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------|  
| <*header-content*> | Obiekt JSON | Wszystkie nagłówki, aby wysłać z wywołania | 
| <*body-content*> | Obiekt JSON | Wszelkie zawartość komunikatu do wysłania z wywołania | 
||||

*Dane wyjściowe*

Dane wyjściowe tej akcji różnią się zależnie od aplikacji logiki zagnieżdżonych Akcja odpowiedzi. Jeśli aplikacja logiki zagnieżdżone nie zawiera akcję odpowiedzi, dane wyjściowe są puste.

*Przykład*

Po pomyślnym akcji "Start_search" tę definicję akcji przepływu pracy wywołuje inną aplikację logiki o nazwie "Get_product_information", które przechodzą w określonym danych wejściowych: 

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>Szczegóły akcji przepływu sterowania

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Działanie foreach

Ta akcja pętli wykonuje iterację przez tablicę i wykonuje akcje dla każdego elementu tablicy. Domyślnie pętla "for each" uruchamia się równolegle, aż maksymalnej liczby pętli. Aby ta wartość maksymalna, zobacz [limity i Konfiguracja](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Dowiedz się, [sposób tworzenia "for each" pętli](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*Wymagane* 

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*Akcja — 1 n*> | String | Nazwy akcji, które są uruchamiane dla każdego elementu tablicy | 
| <*Akcja definition-1... n*> | Obiekt JSON | Definicje akcje, które są uruchamiane | 
| <*for-each-expression*> | String | Wyrażenie, które odwołuje się do każdego elementu w określonej tablicy | 
|||| 

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*count*> | Liczba całkowita | Domyślnie pętla "for each" iteracji wykonywać w tym samym czasie lub w sposób równoległy maksymalnie [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*liczba*> wartość, zobacz [zmienić pętli "for each" współbieżności](#change-for-each-concurrency). | 
| <*Opcja operacji*> | String | Aby uruchomić pętlę "for each", po kolei, a nie w sposób równoległy, ustaw <*opcji operacji*> do `Sequential` lub <*liczba*> do `1`, ale nie oba. Aby uzyskać więcej informacji, zobacz [Uruchom "for each" w pętli sekwencyjnie](#sequential-for-each). | 
|||| 

*Przykład*

Ta pętla "for each" wysyła wiadomość e-mail dotyczącą każdego elementu w tablicy, która zawiera załączniki z przychodzących wiadomości e-mail. Pętla wysyła wiadomość e-mail, w tym załącznik do osoby, która monitoruje załącznika.

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

Aby określić tablicę, który jest przekazywany jako dane wyjściowe z wyzwalacza, pobiera wyrażenie <*nazwa macierzy*> tablicy, z treści wyzwalacza. Aby uniknąć awarii, jeśli tablica nie istnieje, w wyrażeniu `?` operator:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Jeśli akcja

Ta akcja, która jest *instrukcji warunkowej*, ocenia wyrażenie, które reprezentuje stan i uruchamia innej gałęzi, w oparciu o tego, czy warunek jest prawdziwy, lub FAŁSZ. Jeśli warunek jest spełniony, warunek jest oznaczona stanem "Powodzenie". Dowiedz się, [sposób tworzenia instrukcji warunkowych](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*Warunek*> | Obiekt JSON | Warunek, który może być wyrażenie do oceny | 
| <*action-1*> | Obiekt JSON | Akcję do przeprowadzenia podczas <*warunek*> zwraca wartość true | 
| <*action-definition*> | Obiekt JSON | Definicja akcji | 
| <*Akcja 2*> | Obiekt JSON | Akcję do przeprowadzenia podczas <*warunek*> zwróci wartość false | 
|||| 

Akcje w `actions` lub `else` obiektów Pobierz następujące stany:

* Stan "Powodzenie" podczas uruchamiania i powiodło się
* "Nie powiodło się" podczas uruchamiania i zakończyć się niepowodzeniem
* "Skipped", gdy odpowiednich gałęzi nie działa

*Przykład*

Ten warunek określa, czy, gdy zmienna liczba całkowita ma wartość większą niż zero, przepływ pracy sprawdza, czy witryny sieci Web. Jeśli zmienna jest zero lub mniej, przepływ pracy sprawdza, czy inna witryna sieci Web.

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>Jak używać wyrażeń w warunkach

Poniżej przedstawiono kilka przykładów, które pokazują, jak można używać wyrażeń w warunkach:
  
| JSON | Wynik | 
|------|--------| 
| "wyrażenie": "@parameters('<*hasSpecialAction*>") " | Dla wyrażenia na logiczne warunek zostanie spełniony dla dowolnej wartości, która daje w wyniku wartość true. <p>Aby przekonwertować inne typy Boolean, za pomocą tych funkcji: `empty()` lub `equals()`. | 
| "wyrażenie": "@greater(actions('<*action*>').output.value, parametry (" <*próg*> "))" | Porównanie funkcji, działania uruchamiane tylko wtedy, gdy dane wyjściowe z <*akcji*> jest więcej niż <*próg*> wartość. | 
| "wyrażenie": "@or(większa (actions('<*action*>').output.value, parametry (" <*próg*> ")), mniej (akcje (" <*tę samą akcję*>').Output.Value, 100)) " | Dla funkcji logiki i tworzenia zagnieżdżonych wyrażeń logicznych, uruchamia akcję, gdy dane wyjściowe z <*akcji*> jest więcej niż <*próg*> wartość lub poniżej 100. | 
| "wyrażenie": "@equals(długość (actions('<*action*>').outputs.errors), 0))" | Funkcje tablicy służy do sprawdzania, czy tablica nie zawiera żadnych elementów. Akcja jest uruchamiany, gdy `errors` tablica jest pusta. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Zakres działania

Ta akcja logicznie grup akcji do *zakresy*, który uzyskać ich stanu po akcji w tym zakresie zakończenie działania. Stan zakresu umożliwia następnie określić, czy uruchomić inne akcje. Dowiedz się, [sposób tworzenia zakresów](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------|  
| <*wewnętrzne — Akcja-1... n*> | Obiekt JSON | Co najmniej jednej akcji, które są uruchamiane w zakresie |
| <*dane wejściowe akcji*> | Obiekt JSON | Dane wejściowe dla każdej akcji |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Przełącz akcję

Tej akcji, nazywana również *switch, instrukcja*, umożliwia organizowanie innych akcji na *przypadków*i przypisuje wartość do każdej sprawy, z wyjątkiem przypadek domyślny, jeśli taka istnieje. Po uruchomieniu przepływu pracy, **przełącznika** akcji porównuje wartość z wyrażenia, obiekt lub tokenu w odniesieniu do wartości określone dla każdego przypadku. Jeśli **przełącznika** akcja umożliwia znalezienie uwzględniania wielkości liter, przepływ pracy jest uruchamiany tylko akcje dla tego przypadku. Każdorazowo **przełącznika** istnieje akcja działa, albo tylko jeden uwzględniania wielkości liter lub istnieje żadnych dopasowań. Jeśli istnieje żadnych dopasowań, **przełącznika** uruchamia akcję domyślnych akcji. Dowiedz się, [sposób tworzenia instrukcji switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*expression-object-or-token*> | Różna | Wyrażenie, obiekt JSON lub tokenu, aby oceniać | 
| <*Nazwa akcji*> | String | Nazwa akcji do uruchomienia uwzględniania wielkości liter | 
| <*action-definition*> | Obiekt JSON | Definicja akcji do uruchomienia uwzględniania wielkości liter | 
| <*matching-value*> | Różna | Wartość do porównania z wynikiem oceniono | 
|||| 

*Opcjonalne*

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*default-action-name*> | String | Nazwa domyślnej akcji do uruchomienia, gdy istnieje nie uwzględniania wielkości liter | 
| <*default-action-definition*> | Obiekt JSON | Definicja akcji do uruchomienia, gdy istnieje nie uwzględniania wielkości liter | 
|||| 

*Przykład*

Ta definicja akcji ocenia, czy osoba odpowiadanie na wiadomość e-mail z zatwierdzeniem żądania wybrano opcję "Zatwierdź" lub "Odrzuć". Na podstawie tego wyboru **przełącznika** akcja uruchamia akcje w przypadku uwzględniania wielkości liter, która polega na wysłaniu innego adresu e-mail, aby obiekt odpowiadający, ale inaczej sformułowane w każdym przypadku. 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {} 
      }
   },
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>Do działania

Ta akcja pętla zawiera akcje, które uruchamiane aż określony warunek ma wartość true. Pętla sprawdza warunek jako ostatni krok po wykonaniu innych akcji. Może zawierać więcej niż jedna akcja w `"actions"` obiektów i akcji, należy zdefiniować co najmniej jeden limit. Dowiedz się, [sposób tworzenia "pętli do"](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| Wartość | Type | Opis | 
|-------|------|-------------| 
| <*Nazwa akcji*> | String | Nazwa akcji, którą chcesz uruchomić wewnątrz pętli | 
| <*action-type*> | String | Typ akcji, którą chcesz uruchomić | 
| <*dane wejściowe akcji*> | Różne | Dane wejściowe akcji uruchomienia | 
| <*Warunek*> | String | Warunek lub wyrażenie do oceny, gdy wszystkie działania w pętli zakończenie działania | 
| <*loop-count*> | Liczba całkowita | Limit na największą liczbę pętli, które można uruchomić akcji. Wartość domyślna `count` wartość 60. | 
| <*loop-timeout*> | String | Limit przez najdłuższy okres czasu, która może działać w pętli. Wartość domyślna `timeout` wartość `PT1H`, co jest wymagane [formatu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

*Przykład*

Ta definicja akcji pętli wysyła żądanie HTTP pod określony adres URL, dopóki nie jest spełniony jeden z następujących warunków: 

* Żądanie uzyskuje odpowiedź z "200 OK" Kod stanu.
* Pętla została uruchomiona 60 razy.
* Pętla została uruchomiona przez jedną godzinę.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200])",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Elementy Webhook i subskrypcje

Oparte na elementach Webhook wyzwalacze i akcje regularnie nie zaznaczaj punkty końcowe, ale czeka na określone zdarzenia lub dane w tych punktach końcowych zamiast tego. Te wyzwalacze i akcje *subskrybowania* do punktów końcowych, zapewniając *adresów URL wywołania zwrotnego* gdzie wysłać odpowiedzi punktu końcowego.

`subscribe` Wywołanie się dzieje, gdy przepływ pracy zmienia się w jakikolwiek sposób, na przykład, jeśli poświadczenia są odnawiane lub gdy zmienią się parametrów wejściowych dla wyzwalacza lub akcji. To wywołanie używa te same parametry jako standardowa akcji HTTP. 

`unsubscribe` Wywołanie się automatycznie stanie, gdy operacja sprawia, że wyzwalacz lub akcję, nieprawidłowy, na przykład:

* Usuwanie lub wyłączanie wyzwalacza. 
* Usuwanie lub wyłączanie przepływu pracy. 
* Usunięcie lub wyłączenie subskrypcji. 

Do obsługi tych wywołań `@listCallbackUrl()` wyrażenie zwraca unikatową "adres URL wywołania zwrotnego" dla wyzwalacza lub akcji. Ten adres URL reprezentuje unikatowy identyfikator dla punktów końcowych, które używają interfejsu API REST usługi. Parametry dla tej funkcji jest taka sama jak wyzwalacza elementu webhook lub akcję.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Zmiana czasu trwania asynchroniczne

Czas trwania dla wzorca asynchronicznego, aby z określonym interwałem wyzwalaczy i akcji można ograniczyć, dodając `limit.timeout` właściwości. Dzięki temu, jeśli akcja nie zakończono, gdy wygaśnie zakres, stan akcji jest oznaczona jako `Cancelled` z `ActionTimedOut` kodu. `timeout` Używa właściwości [formatu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). 

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>Ustawienia konfiguracji środowiska uruchomieniowego

Można zmienić domyślne zachowanie środowiska uruchomieniowego dla wyzwalaczy i akcji przy użyciu tych `runtimeConfiguration` właściwości w definicji wyzwalacza lub akcji.

| Właściwość | Typ | Opis | Wyzwalacza lub akcji | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Liczba całkowita | Zmiana [ *domyślny limit* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) na liczbę wystąpień przepływu pracy, które można uruchomić w tym samym czasie lub w sposób równoległy. Ta wartość może pomóc ograniczyć liczbę żądań, które odbierają systemów zaplecza. <p>Ustawienie `runs` właściwości `1` działa tak samo jak ustawienie `operationOptions` właściwość `SingleInstance`. Możesz ustawić wartość właściwości, ale nie oba. <p>Aby zmienić domyślny limit, zobacz [współbieżności wyzwalacza zmiany](#change-trigger-concurrency) lub [sekwencyjnie wyzwolić wystąpień](#sequential-trigger). | Wszystkie wyzwalacze | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Liczba całkowita | Zmiana [ *domyślny limit* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) na liczbę wystąpień przepływu pracy, które można poczekać i uruchamiany, gdy przepływ pracy jest już uruchomiona maksymalna liczba współbieżnych wystąpień. Limit współbieżności w można zmienić `concurrency.runs` właściwości. <p>Aby zmienić domyślny limit, zobacz [ograniczać przebiegi oczekujących zmian](#change-waiting-runs). | Wszystkie wyzwalacze | 
| `runtimeConfiguration.concurrency.repetitions` | Liczba całkowita | Zmiana [ *domyślny limit* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) "for each" liczby iteracji, które można uruchomić w tym samym czasie lub równolegle w pętli. <p>Ustawienie `repetitions` właściwości `1` działa tak samo jak ustawienie `operationOptions` właściwość `SingleInstance`. Możesz ustawić wartość właściwości, ale nie oba. <p>Aby zmienić domyślny limit, zobacz [zmienić "for each" współbieżności](#change-for-each-concurrency) lub [Uruchom "for each" w pętli sekwencyjnie](#sequential-for-each). | Akcja: <p>[Instrukcja foreach](#foreach-action) | 
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Opcje operacji

Można zmienić domyślne zachowanie dla wyzwalaczy i akcji przy użyciu `operationOptions` właściwości w definicji wyzwalacza lub akcji.

| Opcja operacji | Type | Opis | Wyzwalacza lub akcji | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | String | Uruchom działania oparte na protokole HTTP, synchronicznie zamiast asynchronicznie. <p><p>Aby ustawić tę opcję, zobacz [akcje były uruchamiane synchronicznie](#asynchronous-patterns). | Akcje: <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Odpowiedź](#response-action) | 
| `OptimizedForHighThroughput` | String | Zmiana [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) liczby wykonań akcji na 5 minut, aby [maksymalny limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>Aby ustawić tę opcję, zobacz [działania w trybie wysokiej przepływności](#run-high-throughput-mode). | Wszystkie akcje | 
| `Sequential` | String | Uruchom "for each" pętli iteracji jednego naraz, a nie wszystko na tym samym czasie równolegle. <p>Ta opcja działa tak samo jak ustawienie `runtimeConfiguration.concurrency.repetitions` właściwość `1`. Możesz ustawić wartość właściwości, ale nie oba. <p><p>Aby ustawić tę opcję, zobacz [Uruchom "for each" w pętli sekwencyjnie](#sequential-for-each).| Akcja: <p>[Instrukcja foreach](#foreach-action) | 
| `SingleInstance` | String | Uruchom wyzwalacz dla każdego wystąpienia aplikacji logiki po kolei i poczekaj na wcześniej aktywne Uruchom, aby zakończyć działanie przed wyzwoleniem następne wystąpienie aplikacji logiki. <p><p>Ta opcja działa tak samo jak ustawienie `runtimeConfiguration.concurrency.runs` właściwość `1`. Możesz ustawić wartość właściwości, ale nie oba. <p>Aby ustawić tę opcję, zobacz [sekwencyjnie wyzwolić wystąpień](#sequential-trigger). | Wszystkie wyzwalacze | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Zmień wyzwalacz współbieżności

Domyślnie wystąpień aplikacji logiki uruchamiane w tym samym czasie jednocześnie lub równolegle do [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Tak każde wystąpienie wyzwalacz jest uruchamiany przed odliczania poprzedniego wystąpienia przepływu pracy. Ten limit zapewnia kontrolować liczbę żądań, które odbierają systemów zaplecza. 

Aby zmienić domyślny limit, służy Edytor widoku kodu lub Projektant aplikacji logiki, ponieważ zmiana ustawienia współbieżności przy użyciu narzędzia Projektant dodaje lub aktualizuje `runtimeConfiguration.concurrency.runs` właściwości podstawowej definicji wyzwalacza i na odwrót. Ta właściwość steruje maksymalną liczbą wystąpień przepływu pracy, które można uruchomić równolegle. 

> [!NOTE] 
> Jeśli ustawisz wyzwalacz, aby uruchamiał sekwencyjnie za pomocą narzędzia Projektant lub Edytor widoku kodu, nie należy ustawiać wyzwalacza `operationOptions` właściwości `SingleInstance` w edytorze widoku kodu. W przeciwnym razie otrzymasz błąd sprawdzania poprawności. Aby uzyskać więcej informacji, zobacz [sekwencyjnie wyzwolić wystąpień](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Edytuj w widoku kodu 

W źródłowym wyzwalają definicję, dodać ani zaktualizować `runtimeConfiguration.concurrency.runs` właściwości na wartość z zakresu od `1` i `50` włącznie.

Oto przykład, który ogranicza równoczesnych uruchomień do 10 wystąpień:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

#### <a name="edit-in-logic-apps-designer"></a>Edytuj w Projektancie aplikacji logiki

1. W tego wyzwalacza w prawym górnym rogu, wybierz przycisk wielokropka (...), a następnie wybierz **ustawienia**.

2. W obszarze **kontroli współbieżności**ustaw **Limit** do **na**. 

3. Przeciągnij **stopień równoległości** suwaka na wartość, która ma. Aby uruchomić aplikację logiki po kolei, przeciągnij wartość suwaka na **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Zmień "for each" współbieżności

Domyślnie iteracji wykonywać w tym samym czasie lub w sposób równoległy, maksymalnie pętli "for each" [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, służy Edytor widoku kodu lub Projektant aplikacji logiki, ponieważ zmiana ustawienia współbieżności przy użyciu narzędzia Projektant dodaje lub aktualizuje `runtimeConfiguration.concurrency.repetitions` właściwość w akcji "for each" podstawowych definicji, jak i na odwrót. Ta właściwość określa maksymalną liczbę iteracji, które mogą działać równolegle.

> [!NOTE] 
> Jeśli ustawisz akcję "for each", do uruchomienia po kolei za pomocą narzędzia Projektant lub Edytor widoku kodu, nie należy ustawiać akcji `operationOptions` właściwości `Sequential` w edytorze widoku kodu. W przeciwnym razie otrzymasz błąd sprawdzania poprawności. Aby uzyskać więcej informacji, zobacz [Uruchom "for each" w pętli sekwencyjnie](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Edytuj w widoku kodu 

W definicji "for each" źródłowym należy dodać lub zaktualizować `runtimeConfiguration.concurrency.repetitions` właściwości na wartość z zakresu od `1` i `50` włącznie. 

Oto przykład, który ogranicza równoczesnych uruchomień do 10 iteracji:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

#### <a name="edit-in-logic-apps-designer"></a>Edytuj w Projektancie aplikacji logiki

1. W **dla każdego** akcji w prawym górnym rogu, wybierz przycisk wielokropka (...), a następnie wybierz **ustawienia**.

2. W obszarze **kontroli współbieżności**ustaw **kontroli współbieżności** do **na**. 

3. Przeciągnij **stopień równoległości** suwaka na wartość, która ma. Aby uruchomić aplikację logiki po kolei, przeciągnij wartość suwaka na **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Zmiany oczekujące limit przebiegów

Domyślnie wszystkie wystąpienia przepływu pracy aplikacji logiki uruchamiane w tym samym czasie jednocześnie lub równolegle do [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Każde wystąpienie wyzwalacza generowane przed odliczania wcześniej aktywne wystąpienie przepływu pracy. Chociaż możesz [zmienić to ograniczenie domyślne](#change-trigger-concurrency), gdy liczba wystąpień przepływu pracy osiągnie nowy limit współbieżności nowych wystąpień należy poczekać do uruchomienia. 

Liczba przebiegów, które mogą poczekać ma również [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits), które można zmienić. Jednak po aplikacji logiki osiągnie limit przebiegów oczekiwania, aparat usługi Logic Apps nie są już akceptuje nowe przebiegi. Żądanie i elementy webhook wyzwala zwracane błędy 429 i cykliczne wyzwalacze start pomijanie prób sondowania.

Aby zmienić domyślny limit przebiegów oczekiwania, w źródłowym wyzwalają definicję, Dodaj `runtimeConfiguration.concurency.maximumWaitingRuns` właściwość z wartością z zakresu od `0` i `100`. 

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Wyzwalanie sekwencyjnie wystąpień

Uruchamianie logiki każdego wystąpienia przepływu pracy aplikacji dopiero po zakończeniu poprzedniego wystąpienia uruchomiona, ustaw wyzwalacz, aby uruchamiają się po kolei. Można także użyć edytora widok kodu lub Projektant aplikacji logiki, ponieważ zmiana ustawienia współbieżności za pomocą projektanta również dodaje lub aktualizuje `runtimeConfiguration.concurrency.runs` właściwości podstawowej definicji wyzwalacza i na odwrót. 

> [!NOTE] 
> Gdy ustawisz wyzwalacz służący do uruchamiania po kolei za pomocą narzędzia Projektant lub Edytor widoku kodu, nie należy ustawiać wyzwalacza `operationOptions` właściwości `Sequential` w edytorze widoku kodu. W przeciwnym razie otrzymasz błąd sprawdzania poprawności. 

#### <a name="edit-in-code-view"></a>Edytuj w widoku kodu

W definicji wyzwalacza Ustaw dowolne z tych właściwości, ale nie oba. 

Ustaw `runtimeConfiguration.concurrency.runs` właściwości `1`:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*- lub -*

Ustaw `operationOptions` właściwości `SingleInstance`:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Edytuj w Projektancie aplikacji logiki

1. W tego wyzwalacza w prawym górnym rogu, wybierz przycisk wielokropka (...), a następnie wybierz **ustawienia**.

2. W obszarze **kontroli współbieżności**ustaw **Limit** do **na**. 

3. Przeciągnij **stopień równoległości** suwak, aby liczba `1`. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Uruchom "for each" sekwencyjnie w pętli

Aby uruchomić pętlę "for each" iteracji dopiero po zakończeniu poprzedniej iteracji uruchomiona, Ustawianie akcji "for each" uruchamiają się po kolei. Można także użyć edytora widok kodu lub Projektant aplikacji logiki, ponieważ zmiana współbieżności akcji za pomocą projektanta również dodaje lub aktualizuje `runtimeConfiguration.concurrency.repetitions` właściwość podstawową definicję działania i na odwrót. 

> [!NOTE] 
> Podczas ustawiania akcji "for each" do uruchomienia po kolei za pomocą projektanta lub edytorze widok kodu nie ustawisz akcji `operationOptions` właściwości `Sequential` w edytorze widoku kodu. W przeciwnym razie otrzymasz błąd sprawdzania poprawności. 

#### <a name="edit-in-code-view"></a>Edytuj w widoku kodu

W definicji działania Ustaw dowolne z tych właściwości, ale nie oba. 

Ustaw `runtimeConfiguration.concurrency.repetitions` właściwości `1`:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*- lub -*

Ustaw `operationOptions` właściwości `Sequential`:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Edytuj w Projektancie aplikacji logiki

1. W **dla każdego** akcji w prawym górnym rogu, wybierz przycisk wielokropka (...), a następnie wybierz **ustawienia**.

2. W obszarze **kontroli współbieżności**ustaw **kontroli współbieżności** do **na**. 

3. Przeciągnij **stopień równoległości** suwak, aby liczba `1`. 

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Akcje były uruchamiane synchronicznie

Domyślnie wszystkie działania oparte na protokole HTTP, postępuj zgodnie ze wzorcem standardowych operacji asynchronicznej. Ten wzorzec Określa, że gdy akcji oparty na protokole HTTP wysyła żądanie do określonego punktu końcowego, serwera zdalnego odsyła odpowiedzi "202 ZAAKCEPTOWANO". Odpowiedź oznacza, że serwer zaakceptowane żądanie do przetwarzania. Adres URL określony w nagłówku lokalizacji odpowiedzi do momentu zatrzymanie przetwarzania, czyli odpowiedzi 202 bez kontynuuje sprawdzanie przez aparat usługi Logic Apps.

Jednak żądań ma limit czasu ograniczyć, tak długo trwające akcje zachowanie asynchroniczne mogą wyłączyć przez dodanie i ustawienie `operationOptions` właściwości `DisableAsyncPattern` w obrębie danych wejściowych akcji.
  
```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Działania w trybie wysokiej przepływności

Dla przebiegu aplikacji logiki pojedynczego, liczba akcji, które są wykonywane co 5 minut ma [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Aby podnieść ten limit, [maksymalna](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) możliwości, ustaw `operationOptions` właściwość `OptimizedForHighThroughput`. To ustawienie umieszcza aplikacji logiki z trybu "wysokiej przepływności". 

> [!NOTE]
> Tryb wysokiej przepływności jest dostępna w wersji zapoznawczej. Obciążenia mogą również rozpowszechniać w więcej niż jednej aplikacji logiki zgodnie z potrzebami.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="connector-authentication"></a>

## <a name="authenticate-http-triggers-and-actions"></a>Uwierzytelnianie HTTP wyzwalacze i akcje

Punktów końcowych HTTP obsługuje różne rodzaje uwierzytelniania. Możesz skonfigurować uwierzytelnianie dla tych HTTP wyzwalacze i akcje:

* [HTTP](../connectors/connectors-native-http.md)
* [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
* [Element webhook protokołu HTTP](../connectors/connectors-native-webhook.md)

Poniżej przedstawiono typy uwierzytelniania, które można skonfigurować:

* [Uwierzytelnianie podstawowe](#basic-authentication)
* [Uwierzytelnianie certyfikatu klienta](#client-certificate-authentication)
* [Uwierzytelnianie OAuth usługi Active Directory (Azure AD) systemu Azure](#azure-active-directory-oauth-authentication)

> [!IMPORTANT]
> Upewnij się, że możesz chronić poufne informacje, które obsługuje definicji przepływu pracy aplikacji logiki. Korzystanie z parametrów zabezpieczonych i kodowanie danych zgodnie z potrzebami. Aby uzyskać więcej informacji o używaniu i zabezpieczanie parametrów, zobacz [zabezpieczanie aplikacji logiki](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Uwierzytelnianie podstawowe

Aby uzyskać [uwierzytelnianie podstawowe](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md) za pomocą usługi Azure Active Directory, może zawierać definicji wyzwalacza lub akcji `authentication` obiekt JSON, który zawiera właściwości określone w poniższej tabeli. Aby uzyskać dostęp do wartości parametrów w czasie wykonywania, można użyć `@parameters('parameterName')` wyrażenie, które są dostarczane przez [język definicji przepływów pracy](https://aka.ms/logicappsdocs). 

| Właściwość | Wymagany | Value | Opis | 
|----------|----------|-------|-------------| 
| **type** | Yes | "Podstawowa" | Typ uwierzytelniania do użycia, która jest tutaj "Basic" | 
| **Nazwa użytkownika** | Yes | "@parameters('userNameParam')" | Nazwa użytkownika do uwierzytelniania dostępu do punktu końcowego usługi docelowej |
| **Hasło** | Yes | "@parameters(passwordParam)" | Hasło do uwierzytelniania dostępu do punktu końcowego usługi docelowej |
||||| 

W tym przykładzie definicję akcji HTTP `authentication` sekcja określa `Basic` uwierzytelniania. Aby uzyskać więcej informacji o używaniu i zabezpieczanie parametrów, zobacz [zabezpieczanie aplikacji logiki](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

> [!IMPORTANT]
> Upewnij się, że możesz chronić poufne informacje, które obsługuje definicji przepływu pracy aplikacji logiki. Korzystanie z parametrów zabezpieczonych i kodowanie danych zgodnie z potrzebami. Aby uzyskać więcej informacji na temat zabezpieczania parametrów, zobacz [zabezpieczanie aplikacji logiki](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Uwierzytelnianie certyfikatu klienta

Aby uzyskać [uwierzytelniania opartego na certyfikatach](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) za pomocą usługi Azure Active Directory, w swojej definicji wyzwalacza lub akcji może zawierać `authentication` obiekt JSON, który zawiera właściwości określone w poniższej tabeli. Aby uzyskać dostęp do wartości parametrów w czasie wykonywania, można użyć `@parameters('parameterName')` wyrażenie, które są dostarczane przez [język definicji przepływów pracy](https://aka.ms/logicappsdocs). Limity liczby certyfikatów klienta, można użyć, zobacz [limity i konfiguracja dla usługi Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md).

| Właściwość | Wymagany | Value | Opis |
|----------|----------|-------|-------------|
| **type** | Yes | "ClientCertificate" | Typ uwierzytelniania do użycia dla certyfikatów klienta protokołu Secure Sockets Layer (SSL). Certyfikaty z podpisem własnym są obsługiwane, nie są obsługiwane certyfikaty z podpisem własnym dla protokołu SSL. |
| **pfx** | Yes | "@parameters(pfxParam) | Zawartość algorytmem Base64 z pliku wymiany informacji osobistych (PFX) |
| **Hasło** | Yes | "@parameters(passwordParam)" | Hasło do uzyskiwania dostępu do pliku PFX |
||||| 

W tym przykładzie definicję akcji HTTP `authentication` sekcja określa `ClientCertificate` uwierzytelniania. Aby uzyskać więcej informacji o używaniu i zabezpieczanie parametrów, zobacz [zabezpieczanie aplikacji logiki](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> Upewnij się, że możesz chronić poufne informacje, które obsługuje definicji przepływu pracy aplikacji logiki. Korzystanie z parametrów zabezpieczonych i kodowanie danych zgodnie z potrzebami. Aby uzyskać więcej informacji na temat zabezpieczania parametrów, zobacz [zabezpieczanie aplikacji logiki](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-ad-oauth-authentication"></a>Uwierzytelnianie OAuth usługi Active Directory (AD) systemu Azure

Dla [uwierzytelniania OAuth usługi AD Azure](../active-directory/develop/authentication-scenarios.md), definicji wyzwalacza lub akcji mogą obejmować `authentication` obiekt JSON, który zawiera właściwości określone w poniższej tabeli. Aby uzyskać dostęp do wartości parametrów w czasie wykonywania, można użyć `@parameters('parameterName')` wyrażenie, które są dostarczane przez [język definicji przepływów pracy](https://aka.ms/logicappsdocs).

| Właściwość | Wymagany | Value | Opis |
|----------|----------|-------|-------------|
| **type** | Yes | `ActiveDirectoryOAuth` | Typ uwierzytelniania do użycia, czyli "ActiveDirectoryOAuth" dla usługi Azure AD OAuth |
| **Urząd** | Nie | <*URL-for-authority-token-issuer*> | Adres URL urząd certyfikacji który zawiera token uwierzytelniania |
| **dzierżawy** | Yes | <*Identyfikator dzierżawy*> | Identyfikator dzierżawy dla dzierżawy usługi Azure AD |
| **audience** | Yes | <*resource-to-authorize*> | Zasób, którego chcesz użyć do autoryzacji, na przykład `https://management.core.windows.net/` |
| **clientId** | Yes | <*Identyfikator klienta*> | Identyfikator klienta aplikacji żądanie autoryzacji |
| **credentialType** | Yes | "Certyfikat" lub "Wpis tajny" | Typ poświadczeń klienta używa dla żądania autoryzacji. Tej właściwości i wartości nie są wyświetlane w podstawowej definicji, ale określa wymagane parametry typu poświadczeń. |
| **pfx** | Tak — tylko typ poświadczeń "Certificate" | "@parameters(pfxParam) | Zawartość algorytmem Base64 z pliku wymiany informacji osobistych (PFX) |
| **Hasło** | Tak — tylko typ poświadczeń "Certificate" | "@parameters(passwordParam)" | Hasło do uzyskiwania dostępu do pliku PFX |
| **Klucz tajny** | Tak, tylko w przypadku "Wpis tajny" typ poświadczeń | "@parameters(secretParam)" | Klucz tajny klienta do żądania autoryzacji |
|||||

W tym przykładzie definicję akcji HTTP `authentication` sekcja określa `ActiveDirectoryOAuth` uwierzytelniania i "Wpis tajny" typ poświadczeń. Aby uzyskać więcej informacji o używaniu i zabezpieczanie parametrów, zobacz [zabezpieczanie aplikacji logiki](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
         "audience": "https://management.core.windows.net/",
         "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> Upewnij się, że możesz chronić poufne informacje, które obsługuje definicji przepływu pracy aplikacji logiki. Korzystanie z parametrów zabezpieczonych i kodowanie danych zgodnie z potrzebami. Aby uzyskać więcej informacji na temat zabezpieczania parametrów, zobacz [zabezpieczanie aplikacji logiki](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [język definicji przepływów pracy](../logic-apps/logic-apps-workflow-definition-language.md)
