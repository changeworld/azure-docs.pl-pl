---
title: Odwołanie do schematu dla typów wyzwalaczy i akcji
description: Przewodnik po schemacie dla wyzwalaczy języka definicji przepływu pracy i typów akcji w aplikacjach logiki azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 7e14cc00d1bd716b3e4880e585b05447d2e55e2b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257440"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Przewodnik po schemacie dla typów wyzwalaczy i akcji w usłudze Azure Logic Apps

W tym odwołaniu opisano ogólne typy używane do identyfikowania wyzwalaczy i akcji w podstawowej definicji przepływu pracy aplikacji logiki, która jest opisana i zweryfikowana przez [język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md). Aby znaleźć określone wyzwalacze i akcje łącznika, których można używać w aplikacjach logiki, zobacz listę w [obszarze Omówienie łączników](https://docs.microsoft.com/connectors/).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Omówienie wyzwalaczy

Każdy przepływ pracy zawiera wyzwalacz, który definiuje wywołania, które wystąpienia i uruchamiania przepływu pracy. Oto ogólne kategorie wyzwalaczy:

* Wyzwalacz *sondowania,* który sprawdza punkt końcowy usługi w regularnych odstępach czasu

* Wyzwalacz *wypychania,* który tworzy subskrypcję punktu końcowego i udostępnia *adres URL wywołania zwrotnego,* dzięki czemu punkt końcowy może powiadomić wyzwalacz, gdy wystąpi określone zdarzenie lub dane są dostępne. Wyzwalacz następnie czeka na odpowiedź punktu końcowego przed uruchomieniem.

Wyzwalacze mają te elementy najwyższego poziomu, chociaż niektóre są opcjonalne:  
  
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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*nazwa wyzwalacza*> | Ciąg | Nazwa wyzwalacza | 
| <*typ wyzwalacza*> | Ciąg | Typ wyzwalacza, taki jak "Http" lub "ApiConnection" | 
| <*wejścia wyzwalające*> | JSON, obiekt | Dane wejściowe definiujące zachowanie wyzwalacza | 
| <*jednostka czasu*> | Ciąg | Jednostka czasu opisująca, jak często wyzwalacz uruchamia: "Drugi", "Minuta", "Godzina", "Dzień", "Tydzień", "Miesiąc" | 
| <*liczba jednostek czasu*> | Liczba całkowita | Wartość określająca, jak często wyzwalacz uruchamia się na podstawie częstotliwości, czyli liczby jednostek czasu oczekiwania, aż wyzwalacz zostanie ponownie uruchomiony <p>Oto minimalne i maksymalne odstępy czasu: <p>- Miesiąc: 1-16 miesięcy </br>- Dzień: 1-500 dni </br>- Godzina: 1-12 000 godzin </br>- Minuta: 1-72 000 minut </br>- Drugi: 1-9,999,999 sekundy<p>Na przykład, jeśli interwał wynosi 6, a częstotliwość to "Miesiąc", cykl jest co 6 miesięcy. | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*tablica z warunkami*> | Tablica | Tablica zawierająca co najmniej jeden [warunki określające,](#trigger-conditions) czy należy uruchomić przepływ pracy. Dostępne tylko dla wyzwalaczy. | 
| <*runtime-config-options*> | JSON, obiekt | Zachowanie środowiska uruchomieniowego wyzwalacza można zmienić, ustawiając `runtimeConfiguration` właściwości. Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](#runtime-config-options). | 
| <*wyrażenie splitOn*> | Ciąg | Dla wyzwalaczy, które zwracają tablicy, można określić wyrażenie, które dzieli lub debatuje elementy [ *tablicy* ](#split-on-debatch) do wielu wystąpień przepływu pracy do przetwarzania. | 
| <*opcja obsługi*> | Ciąg | Zachowanie domyślne można zmienić, ustawiając `operationOptions` właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Lista typów wyzwalaczy

Każdy typ wyzwalacza ma inny interfejs i dane wejściowe, które definiują zachowanie wyzwalacza. 

### <a name="built-in-triggers"></a>Wbudowane wyzwalacze

| Typ wyzwalacza | Opis | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Sprawdza lub *sonduje* dowolny punkt końcowy. Ten punkt końcowy musi być zgodny z określonym kontraktem wyzwalacza przy użyciu wzorca asynchronicznej "202" lub przez zwrócenie tablicy. | 
| [**Protokół HTTPWebhook**](#http-webhook-trigger) | Tworzy punkt końcowy wywoływania dla aplikacji logiki, ale wywołuje określony adres URL, aby zarejestrować lub wyrejestrować. |
| [**Cykl**](#recurrence-trigger) | Pożary na podstawie określonego harmonogramu. Można ustawić przyszłą datę i godzinę wystrzelenia tego wyzwalacza. Na podstawie częstotliwości można również określić godziny i dni uruchamiania przepływu pracy. | 
| [**Żądanie**](#request-trigger)  | Tworzy punkt końcowy wywoływania dla aplikacji logiki i jest również znany jako wyzwalacz "ręczny". Na przykład zobacz [Wywołanie, wyzwalanie lub zagnieżdżanie przepływów pracy za pomocą punktów końcowych HTTP](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Zarządzane wyzwalacze interfejsu API

| Typ wyzwalacza | Opis | 
|--------------|-------------| 
| [**Połączenie apiconnection**](#apiconnection-trigger) | Sprawdza lub *sonduje* punkt końcowy przy użyciu [interfejsów API zarządzanych](../connectors/apis-list.md)przez firmę Microsoft . | 
| [**Interfejs ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Tworzy punkt końcowy wywoływania dla aplikacji logiki, wywołując interfejsy API zarządzane przez [firmę Microsoft,](../connectors/apis-list.md) aby subskrybować i anulować subskrypcję. | 
||| 

## <a name="triggers---detailed-reference"></a>Wyzwalacze — informacje szczegółowe

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>Wyzwalacz APIConnection  

Ten wyzwalacz sprawdza lub *sonduje* punkt końcowy przy użyciu [interfejsów API zarządzanych przez firmę Microsoft,](../connectors/apis-list.md) więc parametry dla tego wyzwalacza mogą się różnić w zależności od punktu końcowego. Wiele sekcji w tej definicji wyzwalacza są opcjonalne. Zachowanie wyzwalacza zależy od tego, czy sekcje są uwzględnione.

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

| Wartość | Typ | Opis |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | Ciąg | Nazwa wyzwalacza |
| <*nazwa połączenia*> | Ciąg | Nazwa połączenia z zarządzanym interfejsem API używanym przez przepływ pracy |
| <*typ metody*> | Ciąg | Metoda HTTP do komunikowania się z zarządzanym interfejsem API: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*operacja api*> | Ciąg | Operacja interfejsu API do wywołania |
| <*jednostka czasu*> | Ciąg | Jednostka czasu opisująca, jak często wyzwalacz uruchamia: "Drugi", "Minuta", "Godzina", "Dzień", "Tydzień", "Miesiąc" |
| <*liczba jednostek czasu*> | Liczba całkowita | Wartość określająca, jak często wyzwalacz uruchamia się na podstawie częstotliwości, czyli liczby jednostek czasu oczekiwania, aż wyzwalacz zostanie ponownie uruchomiony <p>Oto minimalne i maksymalne odstępy czasu: <p>- Miesiąc: 1-16 miesięcy </br>- Dzień: 1-500 dni </br>- Godzina: 1-12 000 godzin </br>- Minuta: 1-72 000 minut </br>- Drugi: 1-9,999,999 sekundy<p>Na przykład, jeśli interwał wynosi 6, a częstotliwość to "Miesiąc", cykl jest co 6 miesięcy. |
||||

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*ponowić próbę zachowania*> | JSON, obiekt | Dostosowuje zachowanie ponawiania dla sporadyczne błędy, które mają 408, 429 i 5XX kod stanu i wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [Ponowić zasady .](../logic-apps/logic-apps-exception-handling.md#retry-policies) | 
| <*parametry zapytania*> | JSON, obiekt | Wszelkie parametry zapytania do uwzględnienia w wywołaniu interfejsu API. Na przykład `"queries": { "api-version": "2018-01-01" }` obiekt `?api-version=2018-01-01` dodaje do wywołania. | 
| <*max-przebiegi*> | Liczba całkowita | Domyślnie wystąpienia przepływu pracy są uruchamiane w tym samym czasie (jednocześnie lub równolegle) do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*zliczanie*> wartości, zobacz [Zmienianie współbieżności wyzwalacza](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Liczba całkowita | Gdy przepływ pracy jest już uruchomiony maksymalną liczbę wystąpień, `runtimeConfiguration.concurrency.runs` które można zmienić na podstawie właściwości, wszystkie nowe przebiegi są umieszczane w tej kolejce do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [Zmienianie limitu przebiegów oczekiwania](#change-waiting-runs). | 
| <*wyrażenie splitOn*> | Ciąg | W przypadku wyzwalaczy zwracających tablice to wyrażenie odwołuje się do tablicy, która ma być używana, aby można było utworzyć i uruchomić wystąpienie przepływu pracy dla każdego elementu tablicy, zamiast używać pętli "dla każdego". <p>Na przykład to wyrażenie reprezentuje element w tablicy zwrócony w treści wyzwalacza:`@triggerbody()?['value']` |
| <*opcja obsługi*> | Ciąg | Zachowanie domyślne można zmienić, ustawiając `operationOptions` właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). |
||||

*Dane wyjściowe*
 
| Element | Typ | Opis |
|---------|------|-------------|
| Nagłówki | JSON, obiekt | Nagłówki z odpowiedzi |
| body | JSON, obiekt | Ciało z odpowiedzi |
| kod stanu | Liczba całkowita | Kod stanu z odpowiedzi |
|||| 

*Przykład*

Ta definicja wyzwalacza sprawdza, czy codziennie w skrzynce odbiorczej konta programu Outlook w skrzynce odbiorczej znajduje się adres e-mail:

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

### <a name="apiconnectionwebhook-trigger"></a>Wyzwalacz apiconnectionwebhook

Ten wyzwalacz wysyła żądanie subskrypcji do punktu końcowego przy użyciu [interfejsu API zarządzanego](../connectors/apis-list.md)przez firmę Microsoft, udostępnia *adres URL wywołania zwrotnego,* do którego punkt końcowy może wysłać odpowiedź i czeka na odpowiedź punktu końcowego. Aby uzyskać więcej informacji, zobacz [Subskrypcje punktów końcowych](#subscribe-unsubscribe).

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*nazwa połączenia*> | Ciąg | Nazwa połączenia z zarządzanym interfejsem API używanym przez przepływ pracy | 
| <*zawartość ciała*> | JSON, obiekt | Wszelka zawartość wiadomości do wysłania jako ładunek do zarządzanego interfejsu API | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*ponowić próbę zachowania*> | JSON, obiekt | Dostosowuje zachowanie ponawiania dla sporadyczne błędy, które mają 408, 429 i 5XX kod stanu i wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [Ponowić zasady .](../logic-apps/logic-apps-exception-handling.md#retry-policies) | 
| <*parametry zapytania*> | JSON, obiekt | Wszelkie parametry zapytania do uwzględnienia w wywołaniu interfejsu API <p>Na przykład `"queries": { "api-version": "2018-01-01" }` obiekt `?api-version=2018-01-01` dodaje do wywołania. | 
| <*max-przebiegi*> | Liczba całkowita | Domyślnie wystąpienia przepływu pracy są uruchamiane w tym samym czasie (jednocześnie lub równolegle) do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*zliczanie*> wartości, zobacz [Zmienianie współbieżności wyzwalacza](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Liczba całkowita | Gdy przepływ pracy jest już uruchomiony maksymalną liczbę wystąpień, `runtimeConfiguration.concurrency.runs` które można zmienić na podstawie właściwości, wszystkie nowe przebiegi są umieszczane w tej kolejce do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [Zmienianie limitu przebiegów oczekiwania](#change-waiting-runs). | 
| <*wyrażenie splitOn*> | Ciąg | W przypadku wyzwalaczy zwracających tablice to wyrażenie odwołuje się do tablicy, która ma być używana, aby można było utworzyć i uruchomić wystąpienie przepływu pracy dla każdego elementu tablicy, zamiast używać pętli "dla każdego". <p>Na przykład to wyrażenie reprezentuje element w tablicy zwrócony w treści wyzwalacza:`@triggerbody()?['value']` |
| <*opcja obsługi*> | Ciąg | Zachowanie domyślne można zmienić, ustawiając `operationOptions` właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). | 
|||| 

*Przykład*

Ta definicja wyzwalacza subskrybuje interfejs API programu Office 365 Outlook, udostępnia adres URL wywołania zwrotnego do punktu końcowego interfejsu API i czeka, aż punkt końcowy odpowie na po odebraniu nowej wiadomości e-mail.

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

Ten wyzwalacz wysyła żądanie do określonego punktu końcowego HTTP lub HTTPS na podstawie określonego harmonogramu cyklu. Wyzwalacz następnie sprawdza odpowiedź, aby ustalić, czy przepływ pracy jest uruchamiany.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": "<query-parameters>",
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      }
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

| Właściwość | Wartość | Typ | Opis |
|----------|-------|------|-------------|
| `method` | <*typ metody*> | Ciąg | Metoda wysyłania żądania wychodzącego: "GET", "PUT", "POST", "PATCH" lub "DELETE" |
| `uri` | <*Adres URL punktu końcowego HTTP-lub HTTPS*> | Ciąg | Adres URL punktu końcowego HTTP lub HTTPS, w którym chcesz wysłać żądanie wychodzące. Maksymalny rozmiar ciągu: 2 KB <p>W przypadku usługi lub zasobu platformy Azure ta składnia identyfikatora URI zawiera identyfikator zasobu i ścieżkę do zasobu, do którego chcesz uzyskać dostęp. |
| `frequency` | <*jednostka czasu*> | Ciąg | Jednostka czasu opisująca, jak często wyzwalacz uruchamia: "Drugi", "Minuta", "Godzina", "Dzień", "Tydzień", "Miesiąc" |
| `interval` | <*liczba jednostek czasu*> | Liczba całkowita | Wartość określająca, jak często wyzwalacz uruchamia się na podstawie częstotliwości, czyli liczby jednostek czasu oczekiwania, aż wyzwalacz zostanie ponownie uruchomiony <p>Oto minimalne i maksymalne odstępy czasu: <p>- Miesiąc: 1-16 miesięcy </br>- Dzień: 1-500 dni </br>- Godzina: 1-12 000 godzin </br>- Minuta: 1-72 000 minut </br>- Drugi: 1-9,999,999 sekundy<p>Na przykład, jeśli interwał wynosi 6, a częstotliwość to "Miesiąc", cykl jest co 6 miesięcy. |
|||||

*Opcjonalne*

| Właściwość | Wartość | Typ | Opis |
|----------|-------|------|-------------|
| `headers` | <*zawartość nagłówka*> | JSON, obiekt | Wszystkie nagłówki, które należy dołączyć do żądania <p>Na przykład, aby ustawić język i wpisać: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*parametry zapytania*> | JSON, obiekt | Wszelkie parametry zapytania, których należy użyć w żądaniu <p>Na przykład `"queries": { "api-version": "2018-01-01" }` obiekt `?api-version=2018-01-01` dodaje do żądania. |
| `body` | <*zawartość ciała*> | JSON, obiekt | Zawartość wiadomości do wysłania jako ładunek z żądaniem |
| `authentication` | <*uwierzytelnianie- typ- i- majątek- wartości*> | JSON, obiekt | Model uwierzytelniania używany przez żądanie do uwierzytelniania żądań wychodzących. Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Poza harmonogramem `authority` właściwość jest obsługiwana. Jeśli nie jest określony, `https://management.azure.com/`wartość domyślna jest , ale można użyć innej wartości. |
| `retryPolicy` > `type` | <*ponowić próbę zachowania*> | JSON, obiekt | Dostosowuje zachowanie ponawiania dla sporadyczne błędy, które mają 408, 429 i 5XX kod stanu i wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [Ponowić zasady .](../logic-apps/logic-apps-exception-handling.md#retry-policies) |
| `runs` | <*max-przebiegi*> | Liczba całkowita | Domyślnie wystąpienia przepływu pracy są uruchamiane w tym samym czasie (jednocześnie lub równolegle) do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*zliczanie*> wartości, zobacz [Zmienianie współbieżności wyzwalacza](#change-trigger-concurrency). |
| `maximumWaitingRuns` | <*max-runs-queue*> | Liczba całkowita | Gdy przepływ pracy jest już uruchomiony maksymalną liczbę wystąpień, `runtimeConfiguration.concurrency.runs` które można zmienić na podstawie właściwości, wszystkie nowe przebiegi są umieszczane w tej kolejce do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [Zmienianie limitu przebiegów oczekiwania](#change-waiting-runs). |
| `operationOptions` | <*opcja obsługi*> | Ciąg | Zachowanie domyślne można zmienić, ustawiając `operationOptions` właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). |
|||||

*Dane wyjściowe*

| Element | Typ | Opis |
|---------|------|-------------| 
| Nagłówki | JSON, obiekt | Nagłówki z odpowiedzi | 
| body | JSON, obiekt | Ciało z odpowiedzi | 
| kod stanu | Liczba całkowita | Kod stanu z odpowiedzi | 
|||| 

*Wymagania dotyczące żądań przychodzących*

Aby dobrze współpracować z aplikacją logiki, punkt końcowy musi być zgodny z określonym wzorcem wyzwalacza lub kontraktem i rozpoznać te właściwości:  
  
| Odpowiedź | Wymagany | Opis | 
|----------|----------|-------------| 
| Kod stanu | Tak | Kod stanu "200 OK" rozpoczyna uruchamianie. Żaden inny kod stanu nie uruchamia się. | 
| Nagłówek ponawiania ponawiania | Nie | Liczba sekund, aż aplikacja logiki ponownie sonduje punkt końcowy | 
| Nagłówek lokalizacji | Nie | Adres URL do wywołania w następnym interwale sondowania. Jeśli nie zostanie określony, używany jest oryginalny adres URL. | 
|||| 

*Przykładowe zachowania dla różnych żądań*

| Kod stanu | Ponów próbę po | Zachowanie | 
|-------------|-------------|----------|
| 200 | {brak} | Uruchom przepływ pracy, a następnie sprawdź ponownie, czy więcej danych po zdefiniowanym cyklu. | 
| 200 | 10 sekund | Uruchom przepływ pracy, a następnie sprawdź ponownie, czy po 10 sekundach nie ma więcej danych. |  
| 202 | 60 sekund | Nie wyzwalaj przepływu pracy. Następna próba ma miejsce w ciągu jednej minuty, z zastrzeżeniem zdefiniowanego cyklu. Jeśli zdefiniowany cykl jest krótszy niż jedna minuta, nagłówek ponawiania ma pierwszeństwo. W przeciwnym razie używany jest zdefiniowany cykl. | 
| 400 | {brak} | Złe żądanie, nie uruchamiaj przepływu pracy. Jeśli `retryPolicy` nie jest zdefiniowany, to używana jest zasada domyślna. Po osiągnięciu liczby ponownych prób wyzwalacz ponownie sprawdza dane po zdefiniowanym cyklu. | 
| 500 | {brak}| Błąd serwera, nie uruchamiaj przepływu pracy. Jeśli `retryPolicy` nie jest zdefiniowany, to używana jest zasada domyślna. Po osiągnięciu liczby ponownych prób wyzwalacz ponownie sprawdza dane po zdefiniowanym cyklu. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>Wyzwalacz httpwebhook  

Ten wyzwalacz sprawia, że aplikacja logiki wywoływane przez utworzenie punktu końcowego, który można zarejestrować subskrypcji, wywołując określony adres URL punktu końcowego. Podczas tworzenia tego wyzwalacza w przepływie pracy, żądanie wychodzące sprawia, że wywołanie zarejestrowania subskrypcji. W ten sposób wyzwalacz może rozpocząć nasłuchiwanie zdarzeń. Gdy operacja powoduje, że ten wyzwalacz jest nieprawidłowy, żądanie wychodzące automatycznie wywołuje anulowanie subskrypcji. Aby uzyskać więcej informacji, zobacz [Subskrypcje punktów końcowych](#subscribe-unsubscribe).

Można również określić [limity asynchroniczne](#asynchronous-limits) dla wyzwalacza **httpwebhook.** Zachowanie wyzwalacza zależy od sekcji, których używasz lub pomijasz.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" }
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

Niektóre wartości, takie jak <> *typu metody,* są dostępne `"subscribe"` `"unsubscribe"` zarówno dla obiektów, jak i dla nich.

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*typ metody*> | Ciąg | Metoda HTTP używana dla żądania subskrypcji: "GET", "PUT", "POST", "PATCH" lub "DELETE" | 
| <*adres URL punktu końcowego subskrybowanego*> | Ciąg | Adres URL punktu końcowego, gdzie wysłać żądanie subskrypcji | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*typ metody*> | Ciąg | Metoda HTTP używana dla żądania anulowania: "GET", "PUT", "POST", "PATCH" lub "DELETE" | 
| <*adres URL anulowania subskrypcji punktu końcowego*> | Ciąg | Adres URL punktu końcowego, gdzie wysłać żądanie anulowania | 
| <*zawartość ciała*> | Ciąg | Wszelkie treści wiadomości do wysłania w żądaniu subskrypcji lub anulowania | 
| <*uwierzytelnianie typu*> | JSON, obiekt | Model uwierzytelniania używany przez żądanie do uwierzytelniania żądań wychodzących. Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*ponowić próbę zachowania*> | JSON, obiekt | Dostosowuje zachowanie ponawiania dla sporadyczne błędy, które mają 408, 429 i 5XX kod stanu i wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [Ponowić zasady .](../logic-apps/logic-apps-exception-handling.md#retry-policies) | 
| <*max-przebiegi*> | Liczba całkowita | Domyślnie wszystkie wystąpienia przepływu pracy są uruchamiane w tym samym czasie (jednocześnie lub równolegle) do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*zliczanie*> wartości, zobacz [Zmienianie współbieżności wyzwalacza](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Liczba całkowita | Gdy przepływ pracy jest już uruchomiony maksymalną liczbę wystąpień, `runtimeConfiguration.concurrency.runs` które można zmienić na podstawie właściwości, wszystkie nowe przebiegi są umieszczane w tej kolejce do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [Zmienianie limitu przebiegów oczekiwania](#change-waiting-runs). | 
| <*opcja obsługi*> | Ciąg | Zachowanie domyślne można zmienić, ustawiając `operationOptions` właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). | 
|||| 

*Dane wyjściowe* 

| Element | Typ | Opis |
|---------|------|-------------| 
| Nagłówki | JSON, obiekt | Nagłówki z odpowiedzi | 
| body | JSON, obiekt | Ciało z odpowiedzi | 
| kod stanu | Liczba całkowita | Kod stanu z odpowiedzi | 
|||| 

*Przykład*

Ten wyzwalacz tworzy subskrypcję określonego punktu końcowego, udostępnia unikatowy adres URL wywołania zwrotnego i czeka na nowo opublikowane artykuły technologiczne.

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

### <a name="recurrence-trigger"></a>Wyzwalacz cyklu  

Ten wyzwalacz jest uruchamiany na podstawie określonego harmonogramu cyklu i zapewnia łatwy sposób tworzenia regularnie uruchomionego przepływu pracy.

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*jednostka czasu*> | Ciąg | Jednostka czasu opisująca, jak często wyzwalacz uruchamia: "Drugi", "Minuta", "Godzina", "Dzień", "Tydzień", "Miesiąc" | 
| <*liczba jednostek czasu*> | Liczba całkowita | Wartość określająca, jak często wyzwalacz uruchamia się na podstawie częstotliwości, czyli liczby jednostek czasu oczekiwania, aż wyzwalacz zostanie ponownie uruchomiony <p>Oto minimalne i maksymalne odstępy czasu: <p>- Miesiąc: 1-16 miesięcy </br>- Dzień: 1-500 dni </br>- Godzina: 1-12 000 godzin </br>- Minuta: 1-72 000 minut </br>- Drugi: 1-9,999,999 sekundy<p>Na przykład, jeśli interwał wynosi 6, a częstotliwość to "Miesiąc", cykl jest co 6 miesięcy. | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*data-godzina początkowa-z formatem-YYYY-MM-DDThh:mm:ss*> | Ciąg | Data i godzina rozpoczęcia w tym formacie: <p>YYYY-MM-DDThh:mm:ss jeśli określisz strefę czasową <p>— lub — <p>YYYY-MM-DDThh:mm:ssZ jeśli nie określisz strefy czasowej <p>Jeśli więc chcesz, aby 18 września 2017 r. o godzinie 14:00, a następnie określić "2017-09-18T14:00:00" i określić strefę czasową, taką jak "Pacyficzny czas standardowy", lub określić "2017-09-18T14:00:00Z" bez strefy czasowej. <p>**Uwaga:** Ten czas rozpoczęcia ma maksymalnie 49 lat w przyszłości i musi być zgodny ze [specyfikacją iso 8601 data time](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [formacie daty UTC,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)ale bez [przesunięcia UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie określisz strefy czasowej, musisz dodać literę "Z" na końcu bez spacji. To "Z" odnosi się do równoważnego [czasu żeglarskiego](https://en.wikipedia.org/wiki/Nautical_time). <p>W przypadku prostych harmonogramów czas rozpoczęcia jest pierwszym wystąpieniem, podczas gdy w przypadku złożonych harmonogramów wyzwalacz nie uruchamia się wcześniej niż godzina rozpoczęcia. Aby uzyskać więcej informacji na temat dat i godzin rozpoczęcia, zobacz [Tworzenie i planowanie regularnie uruchomionych zadań](../connectors/connectors-native-recurrence.md). | 
| <*strefa czasowa*> | Ciąg | Ma zastosowanie tylko wtedy, gdy określisz godzinę rozpoczęcia, ponieważ ten wyzwalacz nie akceptuje [przesunięcia UTC](https://en.wikipedia.org/wiki/UTC_offset). Określ strefę czasową, którą chcesz zastosować. | 
| <*jedno-lub więcej godzin-znaki*> | Tablica całkowita lub całkowita | Jeśli określisz "Dzień" lub `frequency`"Tydzień" dla , można określić jedną lub więcej liczby całkowitych od 0 do 23, oddzielone przecinkami, jako godziny dnia, w którym chcesz uruchomić przepływ pracy. <p>Na przykład, jeśli określisz "10", "12" i "14", otrzymasz 10:00, 12:00 i 14:00 jako znaczniki godziny. | 
| <*jedno-lub więcej minut-znaki*> | Tablica całkowita lub całkowita | Jeśli określisz "Dzień" lub `frequency`"Tydzień" dla , można określić jedną lub więcej liczby całkowitych od 0 do 59, oddzielone przecinkami, jako minuty godziny, gdy chcesz uruchomić przepływ pracy. <p>Na przykład można określić "30" jako znacznik minuty i używając poprzedniego przykładu dla godzin dnia, otrzymasz 10:30, 12:30 i 14:30. | 
| weekDays | Tablica ciągów lub ciągów | Jeśli określisz "Tydzień" dla `frequency`, można określić jeden lub więcej dni, oddzielone przecinkami, gdy chcesz uruchomić przepływ pracy: "Poniedziałek", "Wtorek", "Środa", "Czwartek", "Piątek", "Sobota" i "Niedziela" | 
| <*max-przebiegi*> | Liczba całkowita | Domyślnie wszystkie wystąpienia przepływu pracy są uruchamiane w tym samym czasie (jednocześnie lub równolegle) do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*zliczanie*> wartości, zobacz [Zmienianie współbieżności wyzwalacza](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Liczba całkowita | Gdy przepływ pracy jest już uruchomiony maksymalną liczbę wystąpień, `runtimeConfiguration.concurrency.runs` które można zmienić na podstawie właściwości, wszystkie nowe przebiegi są umieszczane w tej kolejce do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [Zmienianie limitu przebiegów oczekiwania](#change-waiting-runs). | 
| <*opcja obsługi*> | Ciąg | Zachowanie domyślne można zmienić, ustawiając `operationOptions` właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). | 
|||| 

*Przykład 1*

Ten podstawowy wyzwalacz cyklu jest uruchamiany codziennie:

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

Można określić datę i godzinę rozpoczęcia wyzwalania. Ten wyzwalacz cyklu rozpoczyna się w określonym dniu, a następnie uruchamia codziennie:

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

Ten wyzwalacz cyklu rozpoczyna się 9 września 2017 r. o godzinie 14:00 i odpala co tydzień w każdy poniedziałek o 10:30, 12:30 i 14:30 czasu pacyficznego:

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

Aby uzyskać więcej informacji oraz przykłady tego wyzwalacza, zobacz [Tworzenie i planowanie regularnie uruchamianych zadań](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Wyzwalacz żądania

Ten wyzwalacz sprawia, że aplikacja logiki wywoływane przez utworzenie punktu końcowego, który może akceptować przychodzące żądania. Dla tego wyzwalacza należy podać schemat JSON, który opisuje i sprawdza poprawność ładunku lub danych wejściowych, które wyzwalacz odbiera z żądania przychodzącego. Schemat również sprawia, że właściwości wyzwalacza łatwiej odwoływać się od późniejszych akcji w przepływie pracy.

Aby wywołać ten wyzwalacz, należy użyć `listCallbackUrl` interfejsu API, który jest opisany w [interfejsie API REST usługi przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows). Aby dowiedzieć się, jak używać tego wyzwalacza jako punktu końcowego HTTP, zobacz [Przepływy pracy wywoływania, wyzwalania lub zagnieżdżania z punktami końcowymi HTTP](../logic-apps/logic-apps-http-endpoint.md).

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*nazwa właściwości*> | Ciąg | Nazwa właściwości w schemacie JSON, która opisuje ładunek | 
| <*typ właściwości*> | Ciąg | Typ obiektu | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*typ metody*> | Ciąg | Metoda, której przychodzące żądania muszą używać do wywoływania aplikacji logiki: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*parametr relative-path-for-accepted*> | Ciąg | Ścieżka względna parametru, który może zaakceptować adres URL punktu końcowego | 
| <*wymagane właściwości*> | Tablica | Co najmniej jedna właściwości wymagające wartości | 
| <*max-przebiegi*> | Liczba całkowita | Domyślnie wszystkie wystąpienia przepływu pracy są uruchamiane w tym samym czasie (jednocześnie lub równolegle) do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*zliczanie*> wartości, zobacz [Zmienianie współbieżności wyzwalacza](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Liczba całkowita | Gdy przepływ pracy jest już uruchomiony maksymalną liczbę wystąpień, `runtimeConfiguration.concurrency.runs` które można zmienić na podstawie właściwości, wszystkie nowe przebiegi są umieszczane w tej kolejce do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić domyślny limit, zobacz [Zmienianie limitu przebiegów oczekiwania](#change-waiting-runs). | 
| <*opcja obsługi*> | Ciąg | Zachowanie domyślne można zmienić, ustawiając `operationOptions` właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). | 
|||| 

*Przykład*

Ten wyzwalacz określa, że żądanie przychodzące musi używać metody HTTP POST do wywołania wyzwalacza i zawiera schemat, który sprawdza poprawność danych wejściowych z żądania przychodzącego:

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

## <a name="trigger-conditions"></a>Warunki wyzwalacza

Dla każdego wyzwalacza i tylko wyzwalacze, można dołączyć tablicy, która zawiera jedno lub więcej wyrażeń dla warunków, które określają, czy przepływ pracy należy uruchomić. Aby dodać `conditions` właściwość do wyzwalacza w przepływie pracy, otwórz aplikację logiki w edytorze widoku kodu.

Na przykład można określić, że wyzwalacz jest uruchamiany tylko wtedy, gdy witryna `conditions` sieci Web zwraca wewnętrzny błąd serwera, odwołując się do kodu stanu wyzwalacza we właściwości:

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

Domyślnie wyzwalacz uruchamia się dopiero po otrzymaniu odpowiedzi "200 OK". Gdy wyrażenie odwołuje się do kodu stanu wyzwalacza, domyślne zachowanie wyzwalacza jest zastępowane. Tak więc, jeśli chcesz, aby wyzwalacz był uruchamiany dla więcej niż jednego kodu stanu, takiego jak kod stanu "200" i "201", musisz dołączyć to wyrażenie jako warunek:

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Wyzwalanie wielu przebiegów

Jeśli wyzwalacz zwraca tablicę dla aplikacji logiki do przetwarzania, czasami "dla każdego" pętli może trwać zbyt długo, aby przetworzyć każdy element tablicy. Zamiast tego można użyć **SplitOn** właściwości w wyzwalaczu do *debaty* tablicy. Debatching dzieli elementy tablicy i uruchamia nowe wystąpienie przepływu pracy, które jest uruchamiane dla każdego elementu tablicy. Takie podejście jest przydatne, na przykład, gdy chcesz sondować punkt końcowy, który może zwracać wiele nowych elementów między interwałami sondowania. Aby uzyskać maksymalną liczbę elementów tablicy, które **SplitOn** może przetwarzać w jednym uruchomieniu aplikacji logiki, zobacz [Limity i konfiguracja](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Nie można użyć **SplitOn** z wzorcem odpowiedzi synchroniczowej. Każdy przepływ pracy, który używa **SplitOn** i zawiera akcję odpowiedzi jest `202 ACCEPTED` uruchamiana asynchronicznie i natychmiast wysyła odpowiedź.
>
> Gdy współbieżność wyzwalacza jest włączona, [limit SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) jest znacznie zmniejszona. Jeśli liczba elementów przekracza ten limit, funkcja SplitOn jest wyłączona.
 
Jeśli plik Swagger wyzwalacza opisuje ładunek, który jest tablicą, właściwość **SplitOn** jest automatycznie dodawana do wyzwalacza. W przeciwnym razie należy dodać tę właściwość wewnątrz ładunku odpowiedzi, który ma tablicę, którą chcesz debatować.

*Przykład*

Załóżmy, że masz interfejs API, który zwraca tę odpowiedź: 
  
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

Aplikacja logiki potrzebuje tylko zawartości `Rows`z tablicy w , dzięki czemu można utworzyć wyzwalacz jak w tym przykładzie:

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
> Jeśli używasz `SplitOn` polecenia, nie można uzyskać właściwości, które znajdują się poza tablicy. Dlatego w tym przykładzie nie `status` można uzyskać właściwości w odpowiedzi zwróconej z interfejsu API.
> 
> Aby uniknąć awarii, `Rows` jeśli właściwość nie istnieje, `?` w tym przykładzie używa operatora.

Definicja przepływu pracy `@triggerBody().name` można teraz `name` użyć, `"customer-name-one"` aby uzyskać wartości, które są z pierwszego uruchomienia i `"customer-name-two"` z drugiego uruchomienia. Wyjścia wyzwalacza wyglądają więc jak w następujących przykładach:

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

## <a name="actions-overview"></a>Actions overview (Omówienie akcji)

Usługa Azure Logic Apps udostępnia różne typy akcji — każdy z różnymi wejściami, które definiują unikatowe zachowanie akcji. Akcje mają te elementy wysokiego poziomu, chociaż niektóre są opcjonalne:

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

| Wartość | Typ | Opis | 
|-------|------|-------------|
| <*nazwa akcji*> | Ciąg | Nazwa akcji | 
| <*typ akcji*> | Ciąg | Typ akcji, na przykład "Http" lub "ApiConnection"| 
| <*nazwa wejścia*> | Ciąg | Nazwa danych wejściowych definiujących zachowanie akcji | 
| <*wartość wejściowa*> | Różnych | Wartość wejściowa, która może być ciągiem, liczeniem, obiektem JSON itd. | 
| <*poprzedni-trigger-or-action-status*> | JSON, obiekt | Nazwa i wynikowy stan wyzwalacza lub akcji, które muszą zostać uruchomione bezpośrednio przed uruchomieniem bieżącej akcji | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------|
| <*ponowić próbę zachowania*> | JSON, obiekt | Dostosowuje zachowanie ponawiania dla sporadyczne błędy, które mają 408, 429 i 5XX kod stanu i wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz Zasady ponawiania prób. | 
| <*runtime-config-options*> | JSON, obiekt | W przypadku niektórych akcji można zmienić zachowanie akcji `runtimeConfiguration` w czasie wykonywania, ustawiając właściwości. Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](#runtime-config-options). | 
| <*opcja obsługi*> | Ciąg | W przypadku niektórych akcji można zmienić `operationOptions` zachowanie domyślne, ustawiając właściwość. Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Lista typów akcji

Oto kilka często używanych typów akcji: 

* [Wbudowane typy akcji,](#built-in-actions) takie jak te przykłady i inne: 

  * [**HTTP**](#http-action) dla wywoływania punktów końcowych za pośrednictwem protokołu HTTP lub HTTPS

  * [**Odpowiedź**](#response-action) na żądania

  * [**Wykonywanie kodu JavaScript**](#run-javascript-code) do uruchamiania fragmentów kodu JavaScript

  * [**Funkcja**](#function-action) wywoływania funkcji platformy Azure

  * Akcje operacji danych, takie jak [**Sprzężenie,**](#join-action) [**Redagowanie,**](#compose-action) [**Tabela,**](#table-action) [**Wybierz**](#select-action)i inne, które tworzą lub przekształcają dane z różnych danych wejściowych

  * [**Przepływ pracy**](#workflow-action) do wywoływania innego przepływu pracy aplikacji logiki

* [Typy akcji zarządzanego interfejsu API,](#managed-api-actions) takie jak [**ApiConnection**](#apiconnection-action) i [**ApiConnectionWebHook,**](#apiconnectionwebhook-action) które wywołują różne łączniki i interfejsy API zarządzane przez firmę Microsoft, na przykład usługę Azure Service Bus, office 365 Outlook, Power BI, Azure Blob Storage, OneDrive, GitHub i inne

* [Kontroluj typy akcji przepływu pracy,](#control-workflow-actions) takie jak [**If**](#if-action), [**Foreach**](#foreach-action), [**Switch**](#switch-action), [**Scope**](#scope-action)i [**Until**](#until-action), które zawierają inne akcje i pomagają w organizowaniu wykonywania przepływu pracy

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Wbudowane akcje

| Typ akcji | Opis | 
|-------------|-------------| 
| [**Komponować**](#compose-action) | Tworzy pojedyncze dane wyjściowe z danych wejściowych, które mogą mieć różne typy. | 
| [**Wykonywanie kodu JavaScript**](#run-javascript-code) | Uruchom fragmenty kodu JavaScript, które mieszczą się w określonych kryteriach. Aby uzyskać wymagania dotyczące kodu i więcej informacji, zobacz [Dodawanie i uruchamianie fragmentów kodu z kodem wbudowanym](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Funkcja**](#function-action) | Wywołuje funkcję platformy Azure. | 
| [**HTTP**](#http-action) | Wywołuje punkt końcowy HTTP. | 
| [**Dołączyć**](#join-action) | Tworzy ciąg ze wszystkich elementów w tablicy i oddziela te elementy z określonym znakiem ogranicznika. | 
| [**Analizować JSON**](#parse-json-action) | Tworzy przyjazne dla użytkownika tokeny z właściwości w zawartości JSON. Następnie można odwołać się do tych właściwości, dołączając tokeny w aplikacji logiki. | 
| [**Zapytanie**](#query-action) | Tworzy tablicę z elementów w innej tablicy na podstawie warunku lub filtru. | 
| [**Odpowiedzi**](#response-action) | Tworzy odpowiedź na przychodzące połączenie lub żądanie. | 
| [**Wybierz**](#select-action) | Tworzy tablicę z obiektami JSON, przekształcając elementy z innej tablicy na podstawie określonej mapy. | 
| [**Tabeli**](#table-action) | Tworzy tabelę CSV lub HTML z tablicy. | 
| [**Zakończyć**](#terminate-action) | Zatrzymuje aktywnie uruchomiony przepływ pracy. | 
| [**Oczekiwanie**](#wait-action) | Wstrzymuje przepływ pracy na określony czas lub do określonej daty i godziny. | 
| [**Przepływ pracy**](#workflow-action) | Zagnieżdża przepływ pracy wewnątrz innego przepływu pracy. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Zarządzane akcje interfejsu API

| Typ akcji | Opis | 
|-------------|-------------|  
| [**Połączenie apiconnection**](#apiconnection-action) | Wywołuje punkt końcowy HTTP przy użyciu [interfejsu API zarządzanego](../connectors/apis-list.md)przez firmę Microsoft . | 
| [**Interfejs ApiConnectionWebhook**](#apiconnectionwebhook-action) | Działa jak http Webhook, ale używa [interfejsu API zarządzanego przez firmę Microsoft.](../connectors/apis-list.md) | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Kontroluj akcje przepływu pracy

Te akcje ułatwiają kontrolowanie wykonywania przepływu pracy i obejmują inne akcje. Spoza akcji przepływu pracy formantu można bezpośrednio odwoływać się do akcji wewnątrz tej akcji sterującej przepływem pracy. Na przykład jeśli masz `Http` akcję wewnątrz zakresu, można `@body('Http')` odwołać się do wyrażenia z dowolnego miejsca w przepływie pracy. Jednak akcje, które istnieją wewnątrz akcji przepływu pracy kontroli można tylko "uruchomić po" inne akcje, które znajdują się w tej samej strukturze przepływu pracy kontroli.

| Typ akcji | Opis | 
|-------------|-------------| 
| [**Foreach**](#foreach-action) | Uruchom te same akcje w pętli dla każdego elementu w tablicy. | 
| [**Jeśli**](#if-action) | Uruchom akcje na podstawie tego, czy określony warunek jest true czy false. | 
| [**Zakres**](#scope-action) | Uruchom akcje na podstawie stanu grupy z zestawu akcji. | 
| [**Przełącznik**](#switch-action) | Uruchom akcje zorganizowane w przypadkach, gdy wartości z wyrażeń, obiektów lub tokenów są zgodne z wartościami określonymi przez każdy przypadek. | 
| [**Do**](#until-action) | Uruchom akcje w pętli, dopóki określony warunek nie zostanie spełniony. | 
|||  

## <a name="actions---detailed-reference"></a>Akcje — szczegółowe informacje

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>Akcja APIConnection

Ta akcja wysyła żądanie HTTP do [interfejsu API zarządzanego przez firmę Microsoft](../connectors/apis-list.md) i wymaga informacji o interfejsie API i parametrach oraz odwołaniu do prawidłowego połączenia. 

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*nazwa akcji*> | Ciąg | Nazwa akcji dostarczonej przez łącznik | 
| <*nazwa api*> | Ciąg | Nazwa interfejsu API zarządzanego przez firmę Microsoft, który jest używany dla połączenia | 
| <*typ metody*> | Ciąg | Metoda HTTP do wywoływania interfejsu API: "GET", "PUT", "POST", "PATCH" lub "DELETE" | 
| <*operacja api*> | Ciąg | Operacja interfejsu API do wywołania | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*inne właściwości-dane wejściowe-action-specific-input*> | JSON, obiekt | Wszelkie inne właściwości wejściowe, które mają zastosowanie do tej konkretnej akcji | 
| <*ponowić próbę zachowania*> | JSON, obiekt | Dostosowuje zachowanie ponawiania dla sporadyczne błędy, które mają 408, 429 i 5XX kod stanu i wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [Ponowić zasady .](../logic-apps/logic-apps-exception-handling.md#retry-policies) | 
| <*parametry zapytania*> | JSON, obiekt | Wszelkie parametry zapytania do uwzględnienia w wywołaniu interfejsu API. <p>Na przykład `"queries": { "api-version": "2018-01-01" }` obiekt `?api-version=2018-01-01` dodaje do wywołania. | 
| <*właściwości inne działania*> | JSON, obiekt | Wszelkie inne właściwości, które mają zastosowanie do tej konkretnej akcji | 
|||| 

*Przykład*

Ta definicja opisuje akcję **Wyślij wiadomość e-mail** dla programu Office 365 Outlook łącznika, który jest interfejsem API zarządzanym przez firmę Microsoft: 

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

### <a name="apiconnectionwebhook-action"></a>ApiConnectionWebhook akcja

Ta akcja wysyła żądanie subskrypcji za pośrednictwem protokołu HTTP do punktu końcowego przy użyciu [interfejsu API zarządzanego](../connectors/apis-list.md)przez firmę Microsoft, udostępnia *adres URL wywołania zwrotnego,* do którego punkt końcowy może wysłać odpowiedź i czeka na odpowiedź punktu końcowego. Aby uzyskać więcej informacji, zobacz [Subskrypcje punktów końcowych](#subscribe-unsubscribe).

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Niektóre wartości, takie jak <> *typu metody,* są dostępne `"subscribe"` `"unsubscribe"` zarówno dla obiektów, jak i dla nich.

*Wymagane*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*nazwa akcji*> | Ciąg | Nazwa akcji dostarczonej przez łącznik | 
| <*typ metody*> | Ciąg | Metoda HTTP używana do subskrybowania lub anulowania subskrypcji z punktu końcowego: "GET", "PUT", "POST", "PATCH" lub "DELETE" | 
| <*adres URL api-subskrybowania*> | Ciąg | Identyfikator URI używany do subskrybowania interfejsu API | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*adres URL api-unsubscribe*> | Ciąg | Identyfikator URI używany do anulowania subskrypcji z interfejsu API | 
| <*zawartość nagłówka*> | JSON, obiekt | Wszystkie nagłówki do wysłania w żądaniu <p>Na przykład, aby ustawić język i wpisać na żądanie: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*zawartość ciała*> | JSON, obiekt | Wszelkie treści wiadomości do wysłania w żądaniu | 
| <*uwierzytelnianie typu*> | JSON, obiekt | Model uwierzytelniania używany przez żądanie do uwierzytelniania żądań wychodzących. Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*ponowić próbę zachowania*> | JSON, obiekt | Dostosowuje zachowanie ponawiania dla sporadyczne błędy, które mają 408, 429 i 5XX kod stanu i wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [Ponowić zasady .](../logic-apps/logic-apps-exception-handling.md#retry-policies) | 
| <*parametry zapytania*> | JSON, obiekt | Wszelkie parametry zapytania do uwzględnienia w wywołaniu interfejsu API <p>Na przykład `"queries": { "api-version": "2018-01-01" }` obiekt `?api-version=2018-01-01` dodaje do wywołania. | 
| <*inne właściwości-dane wejściowe-action-specific-input*> | JSON, obiekt | Wszelkie inne właściwości wejściowe, które mają zastosowanie do tej konkretnej akcji | 
| <*właściwości inne działania*> | JSON, obiekt | Wszelkie inne właściwości, które mają zastosowanie do tej konkretnej akcji | 
|||| 

Można również określić limity akcji **ApiConnectionWebhook** w taki sam sposób, jak [limity asynchroniczne HTTP](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Skomponowanie akcji

Ta akcja tworzy pojedyncze dane wyjściowe z wielu danych wejściowych, w tym wyrażeń. Dane wyjściowe i dane wejściowe mogą mieć dowolny typ, który obsługuje natywnie usługi Azure Logic Apps, takie jak tablice, obiekty JSON, XML i binarne. Następnie można użyć danych wyjściowych akcji w innych akcjach. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Wymagane* 

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*wejścia do kompozycji*> | Dowolne | Wejścia do tworzenia pojedynczego wyjścia | 
|||| 

*Przykład 1*

<!-- markdownlint-disable MD038 -->
Ta definicja `abcdefg ` akcji łączy się z `1234`spacją końcową i wartością:
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Oto dane wyjściowe tworzone przez tę akcję:

`abcdefg 1234`

*Przykład 2*

Ta definicja akcji scala zmienną ciągu zawierającą `abcdefg` zmienną całkowitą `1234`zawierającą:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Oto dane wyjściowe tworzone przez tę akcję:

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>Wykonywanie akcji kodu JavaScript

Ta akcja uruchamia fragment kodu JavaScript i zwraca `Result` wyniki za pomocą tokenu, do którego można odwołać się do późniejszych akcji.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "<JavaScript-code-snippet>",
      "explicitDependencies": {
         "actions": [ <previous-actions> ],
         "includeTrigger": true
      }
   },
   "runAfter": {}
}
```

*Wymagane*

| Wartość | Typ | Opis |
|-------|------|-------------|
| <*Fragment kodu JavaScript*> | Różna | Kod JavaScript, który chcesz uruchomić. Aby uzyskać wymagania dotyczące kodu i więcej informacji, zobacz [Dodawanie i uruchamianie fragmentów kodu z kodem wbudowanym](../logic-apps/logic-apps-add-run-inline-code.md). <p>W `code` atrybucie fragment kodu można użyć obiektu `workflowContext` tylko do odczytu jako dane wejściowe. Ten obiekt ma właściwości podrzędne, które dają dostęp do kodu do wyników z wyzwalacza i poprzednich akcji w przepływie pracy. Aby uzyskać więcej `workflowContext` informacji na temat obiektu, zobacz [Wyzwalacz odwołań i wyniki akcji w kodzie](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Wymagane w niektórych przypadkach*

Atrybut `explicitDependencies` określa, że chcesz jawnie dołączyć wyniki z wyzwalacza, poprzednie akcje lub oba jako zależności dla fragmentu kodu. Aby uzyskać więcej informacji na temat dodawania tych zależności, zobacz [Dodawanie parametrów dla kodu wbudowanego](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

Dla `includeTrigger` atrybutu można `true` określić `false` lub wartości.

| Wartość | Typ | Opis |
|-------|------|-------------|
| <*poprzednie działania*> | Tablica ciągów | Tablica o określonych nazwach akcji. Użyj nazw akcji wyświetlanych w definicji przepływu pracy, w których nazwy akcji używają podkreśleń (_), a nie spacji (" "). |
||||

*Przykład 1*

Ta akcja uruchamia kod, który pobiera nazwę aplikacji logiki \<i zwraca tekst "Hello world from logic-app-name>" w wyniku. W tym przykładzie kod odwołuje się do nazwy przepływu `workflowContext.workflow.name` pracy, uzyskując `workflowContext` dostęp do właściwości za pośrednictwem obiektu tylko do odczytu. Aby uzyskać więcej `workflowContext` informacji na temat korzystania z obiektu, zobacz [Odwołanie wyzwalacza i wyniki akcji w kodzie](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var text = \"Hello world from \" + workflowContext.workflow.name;\r\n\r\nreturn text;"
   },
   "runAfter": {}
}
```

*Przykład 2*

Ta akcja uruchamia kod w aplikacji logiki, która wyzwala po odebraniu nowej wiadomości e-mail z konta programu Outlook usługi Office 365. Aplikacja logiki używa również akcji wysyłania zatwierdzenia wiadomości e-mail, która przekazuje zawartość z odebranej wiadomości e-mail wraz z żądaniem zatwierdzenia.

Kod wyodrębnia adresy e-mail `Body` z właściwości wyzwalacza i `SelectedOption` zwraca adresy wraz z wartością właściwości z akcji zatwierdzania. Akcja jawnie zawiera akcję wysyłania zatwierdzenia wiadomości `explicitDependencies`  >  `actions` e-mail jako zależność w atrybucie.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var re = /(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))/g;\r\n\r\nvar email = workflowContext.trigger.outputs.body.Body;\r\n\r\nvar reply = workflowContext.actions.Send_approval_email_.outputs.body.SelectedOption;\r\n\r\nreturn email.match(re) + \" - \" + reply;\r\n;",
      "explicitDependencies": {
         "actions": [
            "Send_approval_email_"
         ]
      }
   },
   "runAfter": {}
}
```



<a name="function-action"></a>

### <a name="function-action"></a>Akcja funkcji

Ta akcja wywołuje wcześniej [utworzoną funkcję platformy Azure](../azure-functions/functions-create-first-azure-function.md).

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

| Wartość | Typ | Opis | 
|-------|------|-------------|  
| <*Identyfikator funkcji platformy Azure*> | Ciąg | Identyfikator zasobu dla funkcji platformy Azure, którą chcesz wywołać. Oto format dla tej wartości:<p>"/subscriptions/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*typ metody*> | Ciąg | Metoda HTTP używana do wywoływania funkcji: "GET", "PUT", "POST", "PATCH" lub "DELETE" <p>Jeśli nie zostanie określony, wartością domyślną jest metoda "POST". | 
||||

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------|  
| <*zawartość nagłówka*> | JSON, obiekt | Wszystkie nagłówki do wysłania za pomocą połączenia <p>Na przykład, aby ustawić język i wpisać na żądanie: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*zawartość ciała*> | JSON, obiekt | Wszelkie treści wiadomości do wysłania w żądaniu | 
| <*parametry zapytania*> | JSON, obiekt | Wszelkie parametry zapytania do uwzględnienia w wywołaniu interfejsu API <p>Na przykład `"queries": { "api-version": "2018-01-01" }` obiekt `?api-version=2018-01-01` dodaje do wywołania. | 
| <*inne właściwości-dane wejściowe-action-specific-input*> | JSON, obiekt | Wszelkie inne właściwości wejściowe, które mają zastosowanie do tej konkretnej akcji | 
| <*właściwości inne działania*> | JSON, obiekt | Wszelkie inne właściwości, które mają zastosowanie do tej konkretnej akcji | 
||||

Po zapisaniu aplikacji logiki, aparat aplikacji logiki wykonuje te kontrole funkcji, do której istnieje odwołanie:

* Przepływ pracy musi mieć dostęp do funkcji.

* Przepływ pracy może używać tylko standardowego wyzwalacza HTTP lub ogólnego wyzwalacza elementu webhook JSON. 

  Aparat aplikacji logiki pobiera i buforuje adres URL wyzwalacza, który jest używany w czasie wykonywania. Jeśli jednak jakakolwiek operacja unieważnia buforowany adres URL, akcja **Function** kończy się niepowodzeniem w czasie wykonywania. Aby rozwiązać ten problem, zapisz aplikację logiki ponownie, tak aby aplikacja logiki pobiera i buforuje adres URL wyzwalacza ponownie.

* Funkcja nie może mieć zdefiniowanej trasy.

* Dozwolone są tylko poziomy autoryzacji "funkcji" i "anonimowej". 

*Przykład*

Ta definicja akcji wywołuje wcześniej utworzoną funkcję "GetProductID":

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

Ta akcja wysyła żądanie do określonego punktu końcowego HTTP lub HTTPS i sprawdza odpowiedź, aby ustalić, czy przepływ pracy jest uruchamiany.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": { "<query-parameters>" },
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      },
   },
   "runAfter": {}
}
```

*Wymagane*

| Właściwość | Wartość | Typ | Opis |
|----------|-------|------|-------------|
| `method` | <*typ metody*> | Ciąg | Metoda wysyłania żądania wychodzącego: "GET", "PUT", "POST", "PATCH" lub "DELETE" |
| `uri` | <*Adres URL punktu końcowego HTTP-lub HTTPS*> | Ciąg | Adres URL punktu końcowego HTTP lub HTTPS, w którym chcesz wysłać żądanie wychodzące. Maksymalny rozmiar ciągu: 2 KB <p>W przypadku usługi lub zasobu platformy Azure ta składnia identyfikatora URI zawiera identyfikator zasobu i ścieżkę do zasobu, do którego chcesz uzyskać dostęp. |
|||||

*Opcjonalne*

| Właściwość | Wartość | Typ | Opis |
|----------|-------|------|-------------|
| `headers` | <*zawartość nagłówka*> | JSON, obiekt | Wszystkie nagłówki, które należy dołączyć do żądania <p>Na przykład, aby ustawić język i wpisać: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*parametry zapytania*> | JSON, obiekt | Wszelkie parametry zapytania, których należy użyć w żądaniu <p>Na przykład `"queries": { "api-version": "2018-01-01" }` obiekt `?api-version=2018-01-01` dodaje do wywołania. |
| `body` | <*zawartość ciała*> | JSON, obiekt | Zawartość wiadomości do wysłania jako ładunek z żądaniem |
| `authentication` | <*uwierzytelnianie- typ- i- majątek- wartości*> | JSON, obiekt | Model uwierzytelniania używany przez żądanie do uwierzytelniania żądań wychodzących. Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Poza harmonogramem `authority` właściwość jest obsługiwana. Jeśli nie jest określony, `https://management.azure.com/`wartość domyślna jest , ale można użyć innej wartości. |
| `retryPolicy` > `type` | <*ponowić próbę zachowania*> | JSON, obiekt | Dostosowuje zachowanie ponawiania dla sporadyczne błędy, które mają 408, 429 i 5XX kod stanu i wszelkie wyjątki łączności. Aby uzyskać więcej informacji, zobacz [Ponowić zasady .](../logic-apps/logic-apps-exception-handling.md#retry-policies) |
| <*inne właściwości-dane wejściowe-action-specific-input*> | <*input-właściwość*> | JSON, obiekt | Wszelkie inne właściwości wejściowe, które mają zastosowanie do tej konkretnej akcji |
| <*właściwości inne działania*> | <*wartość nieruchomości*> | JSON, obiekt | Wszelkie inne właściwości, które mają zastosowanie do tej konkretnej akcji |
|||||

*Przykład*

Ta definicja akcji pobiera najnowsze wiadomości, wysyłając żądanie do określonego punktu końcowego:

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

Ta akcja tworzy ciąg ze wszystkich elementów w tablicy i oddziela te elementy z określonym znakiem ogranicznika. 

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Tablicy*> | Tablica | Tablica lub wyrażenie, które udostępnia elementy źródłowe. Jeśli określisz wyrażenie, ująć to wyrażenie z cudzysłowami. | 
| <*Ogranicznik*> | Ciąg pojedynczego znaku | Znak, który oddziela każdy element w ciągu | 
|||| 

*Przykład*

Załóżmy, że masz wcześniej utworzoną zmienną "myIntegerArray", która zawiera tę tablicę całkowitą: 

`[1,2,3,4]` 

Ta definicja akcji pobiera wartości ze `variables()` zmiennej przy użyciu funkcji w wyrażeniu i tworzy ten ciąg z tymi wartościami, które są oddzielone przecinkiem:`"1,2,3,4"`

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

### <a name="parse-json-action"></a>Akcja Analizuj JSON

Ta akcja tworzy przyjazne dla użytkownika pola lub *tokeny* z właściwości w treści JSON. Następnie można uzyskać dostęp do tych właściwości w aplikacji logiki przy użyciu tokenów zamiast tego. Na przykład, jeśli chcesz użyć danych wyjściowych JSON z usług, takich jak usługa Azure Service Bus i usługi Azure Cosmos DB, można dołączyć tę akcję do aplikacji logiki, dzięki czemu można łatwiej odwoływać się do danych w tym danych wyjściowych.

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Źródło JSON*> | JSON, obiekt | Zawartość JSON, którą chcesz przeanalizować | 
| <*Schemat JSON*> | JSON, obiekt | Schemat JSON, który opisuje podstawową zawartość JSON, której akcja używa do analizowania źródłowej zawartości JSON. <p>**Wskazówka:** W Logic Apps Designer można podać schemat lub podać ładunek próbki, dzięki czemu akcja może wygenerować schemat. | 
|||| 

*Przykład*

Ta definicja akcji tworzy te tokeny, których można używać w przepływie pracy, ale tylko w akcjach uruchamianych zgodnie z akcją **Parse JSON:**

`FirstName`, `LastName`, oraz`Email`

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

W tym przykładzie właściwość "content" określa zawartość JSON dla akcji do przeanalizowania. Można również podać tę zawartość JSON jako ładunek próbki do generowania schematu.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

Właściwość "schema" określa schemat JSON używany do opisywania zawartości JSON:

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Tablicy*> | Tablica | Tablica lub wyrażenie, które udostępnia elementy źródłowe. Jeśli określisz wyrażenie, ująć to wyrażenie z cudzysłowami. |
| <*warunek lub filtr*> | Ciąg | Warunek używany do filtrowania elementów w tablicy źródłowej <p>**Uwaga:** Jeśli żadne wartości nie spełniają warunku, akcja tworzy pustą tablicę. |
|||| 

*Przykład*

Ta definicja akcji tworzy tablicę, która ma wartości większe niż określona wartość, która jest dwie:

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

Ta akcja tworzy ładunek dla odpowiedzi na żądanie HTTP. 

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*kod-status odpowiedzi*> | Liczba całkowita | Kod stanu HTTP, który jest wysyłany do żądania przychodzącego. Domyślny kod to "200 OK", ale kod może być dowolny prawidłowy kod stanu, który zaczyna się od 2xx, 4xx lub 5xx, ale nie z 3xxx. | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*nagłówki odpowiedzi*> | JSON, obiekt | Co najmniej jeden nagłówek do uwzględnienia w odpowiedzi | 
| <*organ odpowiedzi*> | Różnych | Treść odpowiedzi, która może być ciągiem, obiektem JSON, a nawet zawartością binarną z poprzedniej akcji | 
|||| 

*Przykład*

Ta definicja akcji tworzy odpowiedź na żądanie HTTP z określonym kodem stanu, treścią wiadomości i nagłówkami wiadomości:

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

W przeciwieństwie do innych **akcji, odpowiedź** akcji ma specjalne ograniczenia: 

* Przepływ pracy może używać akcji **Odpowiedź** tylko wtedy, gdy przepływ pracy rozpoczyna się od wyzwalacza żądania HTTP, co oznacza, że przepływ pracy musi być wyzwalany przez żądanie HTTP.

* Przepływ pracy można użyć **Response** akcji w dowolnym miejscu *z wyjątkiem* wewnątrz **Foreach** pętli, **Until** pętli, w tym pętli sekwencyjnych i równoległych gałęzi. 

* Oryginalne żądanie HTTP pobiera odpowiedź przepływu pracy tylko wtedy, gdy wszystkie akcje wymagane przez akcję **Odpowiedź** zostaną zakończone w [terminie limitu czasu żądania HTTP](../logic-apps/logic-apps-limits-and-config.md#request-limits).

  Jeśli jednak przepływ pracy wywołuje inną aplikację logiki jako zagnieżdżony przepływ pracy, nadrzędny przepływ pracy czeka, aż zagnieżdżony przepływ pracy zakończy się, bez względu na to, ile czasu mija przed zakończeniem zagnieżdżonego przepływu pracy.

* Gdy przepływ pracy używa akcji **Odpowiedź** i wzorca odpowiedzi synchroniczowej, przepływ pracy nie może również używać polecenia **splitOn** w definicji wyzwalacza, ponieważ to polecenie tworzy wiele uruchomień. Sprawdź, czy w tym przypadku, gdy put metoda jest używana, a jeśli true, zwraca odpowiedź "złe żądanie".

  W przeciwnym razie jeśli przepływ pracy używa polecenia **splitOn** i akcji **Odpowiedź,** przepływ pracy jest uruchamiany asynchronicznie i natychmiast zwraca odpowiedź "202 ACCEPTED".

* Gdy wykonanie przepływu pracy osiągnie **odpowiedź** akcji, ale żądanie przychodzące już otrzymał odpowiedź, **Odpowiedź** akcji jest oznaczony jako "Nie powiodło się" z powodu konfliktu. W rezultacie uruchamianie aplikacji logiki jest również oznaczone stanem "Niepowodzenie".

<a name="select-action"></a>

### <a name="select-action"></a>Wybierz akcję

Ta akcja tworzy tablicę z obiektami JSON, przekształcając elementy z innej tablicy na podstawie określonej mapy. Tablica wyjściowa i tablica źródłowa zawsze mają taką samą liczbę elementów. Chociaż nie można zmienić liczby obiektów w tablicy wyjściowej, można dodać lub usunąć właściwości i ich wartości w tych obiektach. Właściwość `select` określa co najmniej jedną parę klucz-wartość, które definiują mapę do przekształcania elementów w tablicy źródłowej. Para klucz-wartość reprezentuje właściwość i jej wartość we wszystkich obiektach w tablicy wyjściowej.

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Tablicy*> | Tablica | Tablica lub wyrażenie, które udostępnia elementy źródłowe. Upewnij się, że wyrażenie jest ujęte z cudzysłowami podwójnymi. <p>**Uwaga:** Jeśli tablica źródłowa jest pusta, akcja tworzy pustą tablicę. | 
| <*nazwa klucza*> | Ciąg | Nazwa właściwości przypisana do wyniku z *wyrażenia* <> <p>Aby dodać nową właściwość we wszystkich obiektach w tablicy wyjściowej, podaj <*> nazwy klucza* dla tej właściwości i wyrażenie <*>* wartości właściwości. <p>Aby usunąć właściwość ze wszystkich obiektów w tablicy, należy pominąć <*> nazwy klucza* dla tej właściwości. | 
| <*Wyrażenie*> | Ciąg | Wyrażenie, które przekształca element w tablicy źródłowej i przypisuje wynik do <*nazwa klucza*> | 
|||| 

Akcja **Select** tworzy tablicę jako dane wyjściowe, więc każda akcja, która chce użyć tego wyjścia, musi zaakceptować tablicę lub przekonwertować tablicę na typ akceptowany przez akcję konsumenta. Na przykład, aby przekonwertować tablicę wyjściową na ciąg, można przekazać tę tablicę do akcji **Redpose,** a następnie odwołać się do danych wyjściowych z akcji **Redpose** w innych akcjach.

*Przykład*

Ta definicja akcji tworzy tablicę obiektów JSON z tablicy liczby całkowitej. Akcja iteruje za pośrednictwem tablicy źródłowej, pobiera `@item()` każdą wartość całkowitą przy użyciu`number`wyrażenia i przypisuje każdą wartość do " " właściwości w każdym obiekcie JSON:

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

Oto tablica, która tworzy tę akcję:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Aby użyć tego wyjścia tablicy w innych akcjach, przekaż to dane wyjściowe do akcji **Redpose:**

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Następnie można użyć danych wyjściowych z akcji **Redpose** w innych akcjach, na przykład **w usłudze Office 365 Outlook — Wyślij akcję e-mail:**

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

Ta akcja tworzy tabelę CSV lub HTML z tablicy. W przypadku tablic z obiektami JSON ta akcja automatycznie tworzy nagłówki kolumn z nazw właściwości obiektów. W przypadku tablic z innymi typami danych należy określić nagłówki i wartości kolumn. Na przykład ta tablica zawiera właściwości "ID" i "Product_Name", których ta akcja może być używana dla nazw nagłówków kolumn:

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| \<> CSV *lub* HTML| Ciąg | Format tabeli, którą chcesz utworzyć | 
| <*Tablicy*> | Tablica | Tablica lub wyrażenie, które udostępnia elementy źródłowe dla tabeli <p>**Uwaga:** Jeśli tablica źródłowa jest pusta, akcja tworzy pustą tabelę. | 
|||| 

*Opcjonalne*

Aby określić lub dostosować nagłówki i `columns` wartości kolumn, użyj tablicy. Gdy `header-value` pary mają taką samą nazwę nagłówka, ich wartości są wyświetlane w tej samej kolumnie pod tą nazwą nagłówka. W przeciwnym razie każdy unikatowy nagłówek definiuje unikatową kolumnę.

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*nazwa kolumny*> | Ciąg | Nazwa nagłówka kolumny | 
| <*wartość kolumny*> | Dowolne | Wartość w tej kolumnie | 
|||| 

*Przykład 1*

Załóżmy, że masz wcześniej utworzoną zmienną "myItemArray", która obecnie zawiera tę tablicę:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Ta definicja akcji tworzy tabelę CSV ze zmiennej "myItemArray". Wyrażenie używane przez `from` właściwość pobiera tablicę z "myItemArray" przy użyciu `variables()` funkcji:

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

Oto tabela CSV, którą tworzy ta akcja: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Przykład 2*

Ta definicja akcji tworzy tabelę HTML ze zmiennej "myItemArray". Wyrażenie używane przez `from` właściwość pobiera tablicę z "myItemArray" przy użyciu `variables()` funkcji:

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

Oto tabela HTML, którą tworzy ta akcja: 

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Jabłka</td></tr><tr><td>1</td><td>Pomarańcze</td></tr></tbody></table>

*Przykład 3*

Ta definicja akcji tworzy tabelę HTML ze zmiennej "myItemArray". Jednak w tym przykładzie zastępuje domyślne nazwy nagłówków kolumn za pomocą "Stock_ID" i "Opis" i dodaje słowo "Organiczny" do wartości w kolumnie "Opis".

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

Oto tabela HTML, którą tworzy ta akcja: 

<table><thead><tr><th>Stock_ID</th><th>Opis</th></tr></thead><tbody><tr><td>0</td><td>Jabłka organiczne</td></tr><tr><td>1</td><td>Organiczne pomarańcze</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Akcja Zakończenia

Ta akcja zatrzymuje uruchamianie wystąpienia przepływu pracy, anuluje wszystkie akcje w toku, pomija wszystkie pozostałe akcje i zwraca określony stan. Na przykład można użyć **Terminate** akcji, gdy aplikacja logiki musi zakończyć całkowicie ze stanu błędu. Ta akcja nie wpływa na już ukończone akcje i nie może być wyświetlana wewnątrz **pętli Foreach** i **Until,** w tym pętli sekwencyjnych.

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Stan*> | Ciąg | Stan powrotu do uruchomienia: "Nie powiodło się", "Anulowane" lub "Powiodło się" |
|||| 

*Opcjonalne*

Właściwości obiektu "runStatus" mają zastosowanie tylko wtedy, gdy właściwość "runStatus" jest ustawiona na stan "Failed".

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*błąd- kod- albo- wymienić*> | Ciąg | Kod lub nazwa błędu |
| <*komunikat o błędzie*> | Ciąg | Komunikat lub tekst opisujący błąd i wszelkie działania, które użytkownik aplikacji może podjąć | 
|||| 

*Przykład*

Ta definicja akcji zatrzymuje uruchamianie przepływu pracy, ustawia stan uruchomienia na "Failed" i zwraca stan, kod błędu i komunikat o błędzie:

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

### <a name="wait-action"></a>Akcja oczekiwania

Ta akcja wstrzymuje wykonywanie przepływu pracy dla określonego interwału lub do określonego czasu, ale nie obu.

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*liczba jednostek*> | Liczba całkowita | W przypadku akcji **Opóźnienie** liczba jednostek oczekujących | 
| <*Interwał*> | Ciąg | Dla akcji **Opóźnienie,** interwał oczekiwania: "Drugi", "Minuta", "Godzina", "Dzień", "Tydzień", "Miesiąc" | 
| <*sygnatura daty i godziny*> | Ciąg | Dla **delay until** akcji, data i godzina wznowienia wykonania. Ta wartość musi być używana w [formacie daty utc](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
|||| 

*Przykład 1*

Ta definicja akcji wstrzymuje przepływ pracy na 15 minut:

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

Ta definicja akcji wstrzymuje przepływ pracy do określonego czasu:

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

Ta akcja wywołuje inną wcześniej utworzoną aplikację logiki, co oznacza, że można uwzględnić i ponownie użyć innych przepływów pracy aplikacji logiki. Można również użyć danych wyjściowych z podrzędnych lub *zagnieżdżonych* aplikacji logiki w akcje, które są zgodne z zagnieżdżonej aplikacji logiki, pod warunkiem, że aplikacja logiki podrzędnej zwraca odpowiedź.

Aparat aplikacji logiki sprawdza dostęp do wyzwalacza, który chcesz wywołać, więc upewnij się, że możesz uzyskać dostęp do tego wyzwalacza. Ponadto zagnieżdżona aplikacja logiki musi spełniać następujące kryteria:

* Wyzwalacz sprawia, że aplikacja logiki zagnieżdżonej jest wywoływana, na przykład [wyzwalacz żądania](#request-trigger) lub [http](#http-trigger)

* Ta sama subskrypcja platformy Azure co aplikacja logiki nadrzędnej

* Aby korzystać z wyjść z zagnieżdżonej aplikacji logiki w aplikacji logiki nadrzędnej, zagnieżdżona aplikacja logiki musi mieć akcję [Odpowiedź](#response-action)

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*zagnieżdżona-logika-nazwa aplikacji*> | Ciąg | Nazwa aplikacji logiki, do której chcesz zadzwonić | 
| <*nazwa wyzwalacza*> | Ciąg | Nazwa wyzwalacza w zagnieżdżonej aplikacji logiki, którą chcesz wywołać | 
| <*Identyfikator subskrypcji platformy Azure*> | Ciąg | Identyfikator subskrypcji platformy Azure dla zagnieżdżonej aplikacji logiki |
| <*Grupa zasobów platformy Azure*> | Ciąg | Nazwa grupy zasobów platformy Azure dla zagnieżdżonej aplikacji logiki |
| <*zagnieżdżona-logika-nazwa aplikacji*> | Ciąg | Nazwa aplikacji logiki, do której chcesz zadzwonić |
||||

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------|  
| <*zawartość nagłówka*> | JSON, obiekt | Wszystkie nagłówki do wysłania za pomocą połączenia | 
| <*zawartość ciała*> | JSON, obiekt | Dowolna zawartość wiadomości do wysłania za pomocą połączenia | 
||||

*Dane wyjściowe*

Dane wyjściowe tej akcji różnią się w zależności od akcji Odpowiedź zagnieżdżonej aplikacji logiki. Jeśli zagnieżdżona aplikacja logiki nie zawiera response akcji, dane wyjściowe są puste.

*Przykład*

Po zakończeniu akcji "Start_search" ta definicja akcji przepływu pracy wywołuje inną aplikację logiki o nazwie "Get_product_information", która przechodzi w określonych danych wejściowych:

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

## <a name="control-workflow-action-details"></a>Szczegóły akcji przepływu pracy sterowania

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Foreach działania

Ta akcja pętli iteruje za pośrednictwem tablicy i wykonuje akcje dla każdego elementu tablicy. Domyślnie pętla "dla każdego" działa równolegle do maksymalnej liczby pętli. Aby uzyskać maksymalne, zobacz [Limity i config](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Dowiedz [się, jak tworzyć pętle "dla każdej" pętli](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*action-1... N*> | Ciąg | Nazwy akcji uruchamianych na każdym elemencie tablicy | 
| <*action-definition-1... N*> | JSON, obiekt | Definicje akcji, które są uruchamiane | 
| <*dla każdego wyrażenia*> | Ciąg | Wyrażenie, które odwołuje się do każdego elementu w określonej tablicy | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Liczba*> | Liczba całkowita | Domyślnie iteracje pętli "dla każdego" są uruchamiane w tym samym czasie (jednocześnie lub równolegle) do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten limit, ustawiając nową <*zliczanie*> wartość, zobacz [Zmienianie współbieżności pętli "dla każdej" pętli](#change-for-each-concurrency). | 
| <*opcja obsługi*> | Ciąg | Aby uruchomić pętlę "dla każdego" sekwencyjnie, a nie równolegle, ustaw opcję <`Sequential` opcji *operacji*> lub <`1` *zliczanie*> do , ale nie oba. Aby uzyskać więcej informacji, zobacz [Uruchamianie kolejno pętli "dla każdej" pętli](#sequential-for-each). | 
|||| 

*Przykład*

Ta pętla "dla każdego" wysyła wiadomość e-mail dla każdego elementu w tablicy, która zawiera załączniki z przychodzącej wiadomości e-mail. Pętla wysyła wiadomość e-mail, w tym załącznik, do osoby, która przegląda załącznik.

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

Aby określić tylko tablicy, która jest przekazywana jako dane wyjściowe z wyzwalacza, to wyrażenie pobiera <*macierzy>* tablicy z treści wyzwalacza. Aby uniknąć błędu, jeśli tablica nie istnieje, `?` wyrażenie używa operatora:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Jeśli działanie

Ta akcja, która jest *instrukcją warunkową,* ocenia wyrażenie, które reprezentuje warunek i uruchamia inną gałąź na podstawie tego, czy warunek jest prawdziwy czy fałszywy. Jeśli warunek jest spełniony, warunek jest oznaczony stanem "Powodem". Dowiedz [się, jak tworzyć instrukcje warunkowe](../logic-apps/logic-apps-control-flow-conditional-statement.md).

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*Warunek*> | JSON, obiekt | Warunek, który może być wyrażeniem, do oceny | 
| <*działanie-1*> | JSON, obiekt | Akcja uruchamiana po <*warunek*> jest oceniana jako true | 
| <*definicja działania*> | JSON, obiekt | Definicja działania | 
| <*działanie-2*> | JSON, obiekt | Akcja uruchamiana po <*warunek*> ocenia się na false | 
|||| 

Akcje w `actions` obiektach lub `else` uzyskać następujące stany:

* "Udało się", gdy działają i odnoszą sukcesy
* "Nie powiodło się", gdy działają i nie powiodą się
* "Pominięte", gdy odpowiedni oddział nie działa

*Przykład*

Ten warunek określa, że gdy zmienna liczba całkowita ma wartość większą niż zero, przepływ pracy sprawdza witrynę sieci Web. Jeśli zmienna jest równa zero lub mniej, przepływ pracy sprawdza inną witrynę sieci Web.

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

#### <a name="how-conditions-use-expressions"></a>Jak warunki używają wyrażeń

Oto kilka przykładów, które pokazują, jak można używać wyrażeń w warunkach:
  
| JSON | Wynik | 
|------|--------| 
| "expression":@parameters" ('<*hasSpecialAction*>')" | Tylko w przypadku wyrażeń logicznych warunek przekazuje dowolną wartość, która ma wartość true. <p>Aby przekonwertować inne typy `empty()` na `equals()`logiczne, użyj następujących funkcji: lub . | 
| "expression":@greater" (actions('<*action*>').output.value, parameters('<*threshold*>'))" | Dla funkcji porównania akcja jest uruchamiana tylko wtedy, gdy dane wyjściowe z> *akcji* <jest większa niż wartość> *progu* <. | 
| "expression":@or" (greater(actions('<*action*>').output.value, parameters('<*threshold*>')), less(actions('<*same-action*>').output.value, 100))" | W przypadku funkcji logicznych i tworzenia zagnieżdżonych wyrażeń logicznych akcja jest uruchamiana, gdy dane wyjściowe z <*> akcji* jest większa niż *próg* <> wartość lub poniżej 100. | 
| "expression":@equals" (length(actions('<*action*>').outputs.errors), 0))" | Można użyć funkcji tablicy do sprawdzania, czy tablica ma jakieś elementy. Akcja jest uruchamiana, gdy tablica jest pusta. `errors` | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Akcja zakresu

Ta akcja logicznie grupuje akcje w *zakresy,* które otrzymują swój własny stan po zakończeniu działania w tym zakresie. Następnie można użyć stanu zakresu, aby określić, czy inne akcje są uruchamiane. Dowiedz [się, jak tworzyć zakresy](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

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

| Wartość | Typ | Opis | 
|-------|------|-------------|  
| <*wewnętrzna akcja-1... N*> | JSON, obiekt | Co najmniej jedna akcja uruchamiana wewnątrz zakresu |
| <*wejścia akcji*> | JSON, obiekt | Dane wejściowe dla każdej akcji |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Akcja Przełącznik

Ta akcja, znana również jako *instrukcja switch,* organizuje inne akcje w *sprawy*i przypisuje wartość do każdego przypadku, z wyjątkiem przypadku domyślnego, jeśli taka istnieje. Po uruchomieniu przepływu pracy **switch** akcji porównuje wartość z wyrażenia, obiektu lub tokenu z wartościami określonymi dla każdego przypadku. Jeśli akcja **Switch** znajdzie pasujące sprawy, przepływ pracy uruchamia tylko akcje dla tej sprawy. Za każdym razem, gdy akcja **Switch** jest uruchamiana, istnieje tylko jeden pasujący przypadek lub nie istnieją żadne dopasowania. Jeśli nie istnieje żadne dopasowania, akcja **Przełącz** uruchamia akcje domyślne. Dowiedz [się, jak utworzyć instrukcje przełączania](../logic-apps/logic-apps-control-flow-switch-statement.md).

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*wyrażenie-obiekt lub token*> | Różna | Wyrażenie, obiekt JSON lub token do oceny | 
| <*nazwa akcji*> | Ciąg | Nazwa akcji do uruchomienia dla pasującej sprawy | 
| <*definicja działania*> | JSON, obiekt | Definicja akcji uruchamiana dla pasującej sprawy | 
| <*wartość dopasowania*> | Różna | Wartość do porównania z ocenianym wynikiem | 
|||| 

*Opcjonalne*

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*domyślna nazwa akcji*> | Ciąg | Nazwa akcji domyślnej do uruchomienia, gdy nie istnieje pasujący przypadek | 
| <*definicja akcji domyślnej*> | JSON, obiekt | Definicja akcji do uruchomienia, gdy nie istnieje pasujący przypadek | 
|||| 

*Przykład*

Ta definicja akcji ocenia, czy osoba odpowiadająca na wiadomość e-mail z prośbą o zatwierdzenie wybrała opcję "Zatwierdź" lub opcję "Odrzuć". Na podstawie tego wyboru **switch** akcji uruchamia akcje dla pasujące sprawy, która jest wysłanie innej wiadomości e-mail do obiektu odpowiadającego, ale z różnych sformułowań w każdym przypadku. 

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

### <a name="until-action"></a>Do czasu podjęcia działań

Ta akcja pętli zawiera akcje, które są uruchamiane, dopóki określony warunek nie zostanie spełniony. Pętla sprawdza warunek jako ostatni krok po uruchomieniu wszystkich innych akcji. W `"actions"` obiekcie można dołączyć więcej niż jedną akcję, a akcja musi definiować co najmniej jeden limit. Dowiedz [się, jak tworzyć pętle "aż".](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 

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

| Wartość | Typ | Opis | 
|-------|------|-------------| 
| <*nazwa akcji*> | Ciąg | Nazwa akcji, którą chcesz uruchomić wewnątrz pętli | 
| <*typ akcji*> | Ciąg | Typ akcji, który chcesz uruchomić | 
| <*wejścia akcji*> | Różnych | Dane wejściowe dla akcji do uruchomienia | 
| <*Warunek*> | Ciąg | Warunek lub wyrażenie do oceny po wszystkich akcjach w pętli zakończyć działanie | 
| <*liczba pętli*> | Liczba całkowita | Limit największej liczby pętli, które można uruchomić akcji. Aby uzyskać więcej informacji na temat domyślnego limitu i maksymalnego limitu, zobacz [Limity i konfiguracja usługi Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). | 
| <*Limit czasu pętli*> | Ciąg | Limit najdłuższego czasu, który może działać w pętli. Wartością `timeout` domyślną jest `PT1H`, która jest wymaganym [formatem ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

*Przykład*

Ta definicja akcji pętli wysyła żądanie HTTP do określonego adresu URL, dopóki nie zostanie spełniony jeden z tych warunków:

* Żądanie otrzymuje odpowiedź z kodem stanu "200 OK".
* Pętla została uruchomione 60 razy.
* Pętla działa przez jedną godzinę.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "HTTP": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('HTTP')['statusCode'], 200)",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Elementów webhook i subskrypcji

Wyzwalacze i akcje oparte na interfejsie Webhook nie regularnie sprawdzają punkty końcowe, ale zamiast tego czekają na określone zdarzenia lub dane w tych punktach końcowych. Te wyzwalacze i akcje *subskrybują punkty końcowe,* podając *adres URL wywołania zwrotnego,* w którym punkt końcowy może wysyłać odpowiedzi.

Wywołanie `subscribe` ma miejsce, gdy przepływ pracy zmienia się w jakikolwiek sposób, na przykład, gdy poświadczenia są odnawiane lub gdy parametry wejściowe zmieniają się dla wyzwalacza lub akcji. To wywołanie używa tych samych parametrów co standardowe akcje HTTP. 

Wywołanie `unsubscribe` odbywa się automatycznie, gdy operacja powoduje nieprawidłowe wyzwalanie lub akcję, na przykład:

* Usuwanie lub wyłączanie wyzwalacza. 
* Usuwanie lub wyłączanie przepływu pracy. 
* Usuwanie lub wyłączanie subskrypcji. 

Aby obsługiwać te `@listCallbackUrl()` wywołania, wyrażenie zwraca unikatowy "adres URL wywołania zwrotnego" dla wyzwalacza lub akcji. Ten adres URL reprezentuje unikatowy identyfikator dla punktów końcowych, które używają interfejsu API REST usługi. Parametry dla tej funkcji są takie same jak wyzwalacz lub akcję elementu webhook.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Zmiana czasu trwania asynchronii

Dla wyzwalaczy i akcji można ograniczyć czas trwania wzorca asynchroniczną do `limit.timeout` określonego przedziału czasu, dodając właściwość. W ten sposób, jeśli akcja nie została zakończona po wygaśnięciu interwału, stan akcji jest oznaczony jako `Cancelled` z kodem. `ActionTimedOut` Właściwość `timeout` używa [formatu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

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

Domyślne zachowanie środowiska uruchomieniowego wyzwalaczy i `runtimeConfiguration` akcji można zmienić, dodając te właściwości do definicji wyzwalacza lub akcji.

| Właściwość | Typ | Opis | Wyzwalacz lub akcja | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Liczba całkowita | Zmień [*domyślny limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) liczby wystąpień przepływu pracy, które można uruchamiać w tym samym czasie (jednocześnie lub równolegle). Dostosowanie tej wartości może pomóc ograniczyć liczbę żądań odbieranych przez systemy wewnętrznej bazy danych. <p>Ustawienie `runs` właściwości `1` tak, aby działała `operationOptions` tak `SingleInstance`samo, jak ustawienie właściwości na . Można ustawić jedną z właściwości, ale nie obie. <p>Aby zmienić domyślny limit, zobacz [Zmienianie współbieżności wyzwalacza](#change-trigger-concurrency) lub [Wyzwalanie wystąpień sekwencyjnie](#sequential-trigger). | Wszystkie wyzwalacze | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Liczba całkowita | Zmień [*domyślny limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) liczby wystąpień przepływu pracy, które muszą czekać na uruchomienie, gdy aplikacja logiki jest już uruchomiona maksymalna liczba wystąpień równoczesnych. <p>Aby zmienić domyślny limit, zobacz [Zmienianie limitu przebiegów oczekiwania](#change-waiting-runs). | Wszystkie wyzwalacze | 
| `runtimeConfiguration.concurrency.repetitions` | Liczba całkowita | Zmień [*domyślny limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) liczby iteracji pętli "dla każdego", które można uruchomić w tym samym czasie (jednocześnie lub równolegle). <p>Ustawienie `repetitions` właściwości `1` tak, aby działała `operationOptions` tak `SingleInstance`samo, jak ustawienie właściwości na . Można ustawić jedną z właściwości, ale nie obie. <p>Aby zmienić domyślny limit, zobacz [Zmienianie współbieżności "dla każdej"](#change-for-each-concurrency) lub [Uruchom kolejno "dla każdej" pętli](#sequential-for-each). | Działanie: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Liczba całkowita | Dla określonych akcji, które obsługują i mają pagination włączone, ta wartość określa *minimalną* liczbę wyników do pobrania. <p>Aby włączyć podział na strony, zobacz [Wprowadzanie danych zbiorczych, elementów lub wyników przy użyciu funkcji podziałania na strony](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) | Działanie: Zróżnicowane |
| `runtimeConfiguration.secureData.properties` | Tablica | W wielu wyzwalaczach i akcjach te ustawienia ukrywają dane wejściowe, dane wyjściowe lub oba te elementy z historii uruchamiania aplikacji logiki. <p>Aby dowiedzieć się więcej na temat ochrony tych danych, zobacz [Ukrywanie danych wejściowych i wyjściowych z historii uruchamiania](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | Większość wyzwalaczy i działań |
| `runtimeConfiguration.staticResult` | JSON, obiekt | W przypadku akcji, które obsługują i mają `staticResult` włączone ustawienie [wyników statycznych,](../logic-apps/test-logic-apps-mock-data-static-results.md) obiekt ma następujące atrybuty: <p>- `name`, który odwołuje się do nazwy definicji wyników statycznych `staticResults` bieżącej akcji, która pojawia `definition` się wewnątrz atrybutu w atrybucie przepływu pracy aplikacji logiki. Aby uzyskać więcej informacji, zobacz [Wyniki statyczne — odwołanie do schematu języka definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md#static-results). <p> - `staticResultOptions`, który określa, czy `Enabled` wyniki statyczne są dla bieżącej akcji, czy nie. <p>Aby włączyć wyniki statyczne, zobacz [Testowanie aplikacji logiki z makietami danych przez konfiguranie wyników statycznych](../logic-apps/test-logic-apps-mock-data-static-results.md) | Działanie: Zróżnicowane |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Opcje operacji

Można zmienić domyślne zachowanie wyzwalaczy `operationOptions` i akcji z właściwością w definicji wyzwalacza lub akcji.

| Opcja operacji | Typ | Opis | Wyzwalacz lub akcja | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Ciąg | Uruchamiaj akcje oparte na protokole HTTP synchronicznie, a nie asynchronicznie. <p><p>Aby ustawić tę opcję, zobacz [Uruchamianie akcji synchronicznie](#asynchronous-patterns). | Działania: <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Odpowiedzi](#response-action) | 
| `OptimizedForHighThroughput` | Ciąg | Zmień [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) liczby wykonań akcji na 5 minut do [maksymalnego limitu](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>Aby ustawić tę opcję, zobacz [Uruchamianie w trybie wysokiej przepustowości](#run-high-throughput-mode). | Wszystkie akcje | 
| `Sequential` | Ciąg | Uruchom "dla każdej" iteracji pętli po jednym na raz, a nie wszystkie w tym samym czasie równolegle. <p>Ta opcja działa tak samo, jak ustawienie `runtimeConfiguration.concurrency.repetitions` właściwości na `1`. Można ustawić jedną z właściwości, ale nie obie. <p><p>Aby ustawić tę opcję, zobacz [Uruchamianie kolejno pętli "dla każdej" pętli](#sequential-for-each).| Działanie: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | Ciąg | Uruchom wyzwalacz dla każdego wystąpienia aplikacji logiki sekwencyjnie i poczekaj na zakończenie wcześniej aktywnego uruchomienia przed wyzwoleniem następnego wystąpienia aplikacji logiki. <p><p>Ta opcja działa tak samo, jak ustawienie `runtimeConfiguration.concurrency.runs` właściwości na `1`. Można ustawić jedną z właściwości, ale nie obie. <p>Aby ustawić tę opcję, zobacz [Wyzwalanie wystąpień sekwencyjnie](#sequential-trigger). | Wszystkie wyzwalacze | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Zmienianie współbieżności wyzwalaczy

Domyślnie wystąpienia przepływu pracy aplikacji logiki wszystkie są uruchamiane w tym samym czasie (jednocześnie lub równolegle). To zachowanie oznacza, że każde wystąpienie wyzwalacza jest uruchamiane przed zakończeniem działania wcześniej aktywnego wystąpienia przepływu pracy. Jednak liczba jednocześnie uruchomionych wystąpień ma [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Gdy liczba jednocześnie uruchomionych wystąpień przepływu pracy osiągnie ten limit, wszystkie inne nowe wystąpienia muszą czekać na uruchomienie. Ten limit pomaga kontrolować liczbę żądań odbieranych przez systemy wewnętrznej bazy danych.

Po włączeniu kontroli współbieżności wyzwalacza wystąpienia wyzwalacza są uruchamiane równolegle do [domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Aby zmienić ten domyślny limit współbieżności, można użyć edytora widoku kodu lub projektanta aplikacji logiki, `runtimeConfiguration.concurrency.runs` ponieważ zmiana ustawienia współbieżności za pośrednictwem projektanta dodaje lub aktualizuje właściwość w podstawowej definicji wyzwalacza i odwrotnie. Ta właściwość kontroluje maksymalną liczbę nowych wystąpień przepływu pracy, które można uruchomić równolegle.

Oto kilka zagadnień dotyczących włączenia współbieżności wyzwalacza:

* Gdy współbieżność jest włączona, [limit SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) jest znacznie zmniejszona dla [dyskusyjnych tablic](#split-on-debatch). Jeśli liczba elementów przekracza ten limit, funkcja SplitOn jest wyłączona.

* Nie można wyłączyć współbieżności po włączeniu kontroli współbieżności.

* Gdy współbieżność jest włączona, [limit SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) jest znacznie zmniejszona dla [dyskusyjnych tablic](#split-on-debatch). Jeśli liczba elementów przekracza ten limit, funkcja SplitOn jest wyłączona.

* Gdy współbieżność jest włączona, długotrwałe wystąpienie aplikacji logiki może spowodować, że nowe wystąpienia aplikacji logiki, aby wprowadzić stan oczekiwania. Ten stan uniemożliwia usługi Azure Logic Apps tworzenia nowych wystąpień i dzieje się nawet wtedy, gdy liczba równoczesnych przebiegów jest mniejsza niż określona maksymalna liczba równoczesnych przebiegów.

  * Aby przerwać ten stan, anuluj najwcześniejsze wystąpienia, które *są nadal uruchomione*.

    1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

    1. W sekcji **Historia przebiegów** wybierz najwcześniejsze wystąpienie, które jest nadal uruchomione, na przykład:

       ![Wybierz najwcześniej uruchomione wystąpienie](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Aby wyświetlić tylko wystąpienia, które są nadal uruchomione, otwórz listę **Wszystkie** i wybierz pozycję **Uruchomiona**.

    1. W obszarze **Uruchamianie aplikacji Logika**wybierz **pozycję Anuluj uruchomienie**.

       ![Znajdź najwcześniejsze uruchomione wystąpienie](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * Aby obejść tę możliwość, dodaj limit czasu do dowolnej akcji, która może pomieścić te przebiegi. Jeśli pracujesz w edytorze kodu, zobacz [Zmienianie czasu trwania asynchronii.](#asynchronous-limits) W przeciwnym razie, jeśli używasz projektanta, wykonaj następujące kroki:

    1. W aplikacji logiki, w przypadku akcji, w której chcesz dodać limit czasu, w prawym górnym rogu wybierz przycisk elipsy (**...**), a następnie wybierz pozycję **Ustawienia**.

       ![Otwórz ustawienia akcji](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. W obszarze **Limit czasu**określ czas trwania limitu czasu w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

       ![Określanie czasu trwania limitu czasu](./media/logic-apps-workflow-actions-triggers/timeout.png)

* Aby uruchomić aplikację logiki sekwencyjnie, należy ustawić `1` współbieżność wyzwalacza przy użyciu edytora widoku kodu lub projektanta. Upewnij się, że nie ustawiono również `operationOptions` właściwości `SingleInstance` wyzwalacza w edytorze widoku kodu. W przeciwnym razie pojawia się błąd sprawdzania poprawności. Aby uzyskać więcej informacji, zobacz [wyzwalanie wystąpień sekwencyjnie](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Edycja w widoku kodu 

W podstawowej definicji `runtimeConfiguration.concurrency.runs` wyzwalacza dodaj właściwość, która `1` może `50`mieć wartość, która waha się od do .

Oto przykład, który ogranicza równoczesnych przebiegów do 10 wystąpień:

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

Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Edytuj w Projektancie aplikacji logiki

1. W prawym górnym rogu wyzwalacza wybierz przycisk elipsy (**...**), a następnie wybierz pozycję **Ustawienia**.

1. W obszarze **Kontrola współbieżności**ustaw **limit** na **wł.** 

1. Przeciągnij suwak **Stopień równoległości** do żądanej wartości. Aby uruchomić aplikację logiki sekwencyjnie, przeciągnij wartość suwaka do **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Zmiana współbieżności "dla każdego"

Domyślnie "dla każdej" iteracje pętli wszystkie są uruchamiane w tym samym czasie (jednocześnie lub równolegle). To zachowanie oznacza, że każda iteracja rozpoczyna się przed zakończeniem poprzedniej iteracji. Jednak liczba równocześnie uruchomionych iteracji ma [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Gdy liczba równocześnie uruchomionych iteracji osiągnie ten limit, wszystkie inne iteracje muszą czekać na uruchomienie.

Aby zmienić domyślny limit, można użyć edytora widoku kodu lub projektanta aplikacji logiki, ponieważ `runtimeConfiguration.concurrency.repetitions` zmiana ustawienia współbieżności za pośrednictwem projektanta dodaje lub aktualizuje właściwość w podstawowej definicji akcji "dla każdego" i odwrotnie. Ta właściwość kontroluje maksymalną liczbę iteracji, które można uruchomić równolegle.

> [!NOTE] 
> Jeśli ustawisz akcję "dla każdego" do uruchamiania sekwencyjnie za pomocą projektanta lub edytora `operationOptions` `Sequential` widoku kodu, nie ustawiaj właściwości akcji w edytorze widoku kodu. W przeciwnym razie pojawia się błąd sprawdzania poprawności. Aby uzyskać więcej informacji, zobacz [Uruchamianie kolejno pętli "dla każdej" pętli](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Edycja w widoku kodu 

W podstawowej definicji "dla każdego" `runtimeConfiguration.concurrency.repetitions` dodaj lub zaktualizuj `1` właściwość, która może mieć wartość, która waha się od i `50`.

Oto przykład, który ogranicza równoczesnych przebiegów do 10 iteracji:

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

Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Edytuj w Projektancie aplikacji logiki

1. W sekcji **Dla każdej** akcji w prawym górnym rogu wybierz przycisk elipsy (**...**), a następnie wybierz pozycję **Ustawienia**.

1. W obszarze **Kontrola współbieżności**ustaw **formę współbieżności** **na Wł.**

1. Przeciągnij suwak **Stopień równoległości** do żądanej wartości. Aby uruchomić aplikację logiki sekwencyjnie, przeciągnij wartość suwaka do **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Zmienianie limitu przebiegów oczekujących

Domyślnie wystąpienia przepływu pracy aplikacji logiki wszystkie są uruchamiane w tym samym czasie (jednocześnie lub równolegle). To zachowanie oznacza, że każde wystąpienie wyzwalacza jest uruchamiane przed zakończeniem działania wcześniej aktywnego wystąpienia przepływu pracy. Jednak liczba jednocześnie uruchomionych wystąpień ma [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Gdy liczba jednocześnie uruchomionych wystąpień przepływu pracy osiągnie ten limit, wszystkie inne nowe wystąpienia muszą czekać na uruchomienie.

Liczba oczekujących przebiegów ma również [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Gdy liczba oczekujących przebiegów osiągnie ten limit, aparat aplikacji logiki nie akceptuje już nowych przebiegów. Wyzwalacze żądania i elementu webhook zwracają 429 błędów, a wyzwalacze cykliczne rozpoczynają pomijanie prób sondowania.

Można nie tylko [zmienić domyślny limit współbieżności wyzwalacza,](#change-trigger-concurrency)ale można również zmienić domyślny limit dla oczekujących przebiegów. W podstawowej definicji `runtimeConfiguration.concurrency.maximumWaitingRuns` wyzwalacza dodaj właściwość, która `1` może `100`mieć wartość, która waha się od do .

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

Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](#runtime-config-options).

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Wyzwalanie wystąpień sekwencyjnie

Aby uruchomić każde wystąpienie przepływu pracy aplikacji logiki tylko po zakończeniu poprzedniego wystąpienia, ustaw wyzwalacz, aby działał sekwencyjnie. Można użyć edytora widoku kodu lub projektanta aplikacji logiki, ponieważ zmiana ustawienia `runtimeConfiguration.concurrency.runs` współbieżności za pośrednictwem projektanta również dodaje lub aktualizuje właściwość w podstawowej definicji wyzwalacza i odwrotnie.

> [!NOTE] 
> Po ustawieniu wyzwalacza do uruchamiania sekwencyjnie za pomocą projektanta lub edytora `operationOptions` widoku `Sequential` kodu, nie należy ustawiać właściwości wyzwalacza w edytorze widoku kodu. W przeciwnym razie pojawia się błąd sprawdzania poprawności. 

#### <a name="edit-in-code-view"></a>Edycja w widoku kodu

W definicji wyzwalacza ustaw jedną z tych właściwości, ale nie obie. 

Ustaw `runtimeConfiguration.concurrency.runs` właściwość `1`na:

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

*-lub-*

Ustaw `operationOptions` właściwość `SingleInstance`na:

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

Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](#runtime-config-options) i [opcje operacji](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Edytuj w Projektancie aplikacji logiki

1. W prawym górnym rogu wyzwalacza wybierz przycisk elipsy (**...**), a następnie wybierz pozycję **Ustawienia**.

1. W obszarze **Kontrola współbieżności**ustaw **limit** na **wł.** 

1. Przeciągnij suwak **Stopień równoległości** na `1`liczbę . 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Uruchom kolejno pętle "dla każdej"

Aby uruchomić iterację pętli "dla każdego" dopiero po zakończeniu uruchamiania poprzedniej iteracji, ustaw akcję "dla każdego" do uruchomienia sekwencyjnie. Można użyć edytora widoku kodu lub projektanta aplikacji logiki, ponieważ zmiana współbieżności `runtimeConfiguration.concurrency.repetitions` akcji za pośrednictwem projektanta również dodaje lub aktualizuje właściwość w podstawowej definicji akcji i na odwrót.

> [!NOTE] 
> Po ustawieniu akcji "dla każdego" do uruchamiania sekwencyjnie przy użyciu edytora widoku projektanta `operationOptions` `Sequential` lub kodu, nie ustawiaj właściwości akcji w edytorze widoku kodu. W przeciwnym razie pojawia się błąd sprawdzania poprawności. 

#### <a name="edit-in-code-view"></a>Edycja w widoku kodu

W definicji akcji ustaw jedną z tych właściwości, ale nie obie. 

Ustaw `runtimeConfiguration.concurrency.repetitions` właściwość `1`na:

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

*-lub-*

Ustaw `operationOptions` właściwość `Sequential`na:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

Aby uzyskać więcej informacji, zobacz [Ustawienia konfiguracji środowiska uruchomieniowego](#runtime-config-options) i [opcje operacji](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Edytuj w Projektancie aplikacji logiki

1. W prawym górnym rogu **każdej** akcji wybierz przycisk elipsy (**...**), a następnie wybierz pozycję **Ustawienia**.

1. W obszarze **Kontrola współbieżności**ustaw **formę współbieżności** **na Wł.**

1. Przeciągnij suwak **Stopień równoległości** na `1`liczbę .

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Uruchamianie akcji synchronicznie

Domyślnie wszystkie akcje oparte na protoko> HTTP są zgodne ze standardowym wzorcem operacji asynchronicznego. Ten wzorzec określa, że gdy akcja oparta na protokonie HTTP wysyła żądanie do określonego punktu końcowego, serwer zdalny odsyła odpowiedź "202 ACCEPTED". Ta odpowiedź oznacza, że serwer zaakceptował żądanie przetwarzania. Aparat aplikacji logiki sprawdza adres URL określony przez nagłówek lokalizacji odpowiedzi do momentu zatrzymania przetwarzania, co oznacza dowolną odpowiedź inną niż 202.

Jednak żądania mają limit czasu, więc dla długotrwałych akcji, można wyłączyć zachowanie asynchroniczne, dodając i ustawiając `operationOptions` właściwość `DisableAsyncPattern` w ramach danych wejściowych akcji.

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

Aby uzyskać więcej informacji, zobacz [Opcje operacji](#operation-options).

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Uruchamianie w trybie wysokiej przepustowości

W przypadku pojedynczej definicji aplikacji logiki liczba akcji wykonywanych co 5 minut ma [domyślny limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Aby podnieść ten limit do `operationOptions` [maksimum](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) możliwego, ustaw właściwość na `OptimizedForHighThroughput`. To ustawienie przełącza aplikację logiki w trybie "wysokiej przepływności".

> [!NOTE]
> Tryb wysokiej przepływności jest w podglądzie. Można również rozprowadzić obciążenie w więcej niż jednej aplikacji logiki w razie potrzeby.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>Uwierzytelnij wyzwalacze i akcje

Punkty końcowe HTTP i HTTPS obsługują różne rodzaje uwierzytelniania. Na podstawie wyzwalacza lub akcji, które można wykonać wychodzące wywołania lub żądania dostępu do tych punktów końcowych, można wybrać z różnych zakresów typów uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [języku definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md)
