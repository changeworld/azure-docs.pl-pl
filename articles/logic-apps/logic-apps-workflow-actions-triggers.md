---
title: Przepływ pracy wyzwalacze i akcje - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o wyzwalacze i akcje w definicji przepływu pracy dla usługi Azure Logic Apps
services: logic-apps
author: kevinlam1
manager: SyntaxC4
editor: ''
documentationcenter: ''
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 5/8/2018
ms.author: klam; LADocs
ms.openlocfilehash: 88ee3d810a80bed418e8dbafa4f3e35ccf5e85b1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="triggers-and-actions-for-workflow-definitions-in-azure-logic-apps"></a>Wyzwalacze i akcje dla definicji przepływu pracy w aplikacjach logiki platformy Azure

W [Azure Logic Apps](../logic-apps/logic-apps-overview.md), wszystkie przepływy pracy aplikacji logiki rozpoczynać wyzwalaczy, a następnie akcje. W tym artykule opisano wyzwalacze i akcje, które służą do tworzenia aplikacji logiki dla przepływów pracy automatyzacji firmowych lub procesów w programie rozwiązań integracji. Można tworzyć aplikacje logiki wizualnie przy użyciu projektanta aplikacji logiki lub przez bezpośrednie tworzenie podstawowej definicji przepływu pracy z [język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md). Można użyć portalu Azure lub programu Visual Studio. Dowiedz się, jak [działa w przypadku wyzwalacze i akcje cenach](../logic-apps/logic-apps-pricing.md).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Omówienie wyzwalaczy

Wszystkie aplikacje logiki rozpoczynać wyzwalacz, który definiuje wywołania, które można utworzyć wystąpienia i uruchomić przepływ pracy aplikacji logiki. Poniżej przedstawiono typy wyzwalaczy, które są dostępne:

* A *sondowania* wyzwalacz, który sprawdza punktu końcowego HTTP usługi w regularnych odstępach czasu
* A *wypychania* wyzwolić, które wywołuje [interfejsu API REST usługi przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows)
 
Wszystkich wyzwalaczy mają takich elementów najwyższego poziomu, chociaż niektóre są opcjonalne:  
  
```json
"<triggerName>": {
   "type": "<triggerType>",
   "inputs": { "<trigger-behavior-settings>" },
   "recurrence": { 
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": "<recurrence-interval-based-on-frequency>"
   },
   "conditions": [ <array-with-required-conditions> ],
   "splitOn": "<property-used-for-creating-runs>",
   "operationOptions": "<optional-trigger-operations>"
}
```

*Wymagane*

| Nazwa elementu | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Nazwa_wyzwalacza*> | Obiekt JSON | Nazwa wyzwalacza jest obiektem opisanego w formacie Javascript Object Notation (JSON)  | 
| type | Ciąg | Typ wyzwalacza, na przykład: "Http" lub "ApiConnection" | 
| Dane wejściowe | Obiekt JSON | Dane wejściowe wyzwalacza, które określają zachowanie tego wyzwalacza | 
| recurrence | Obiekt JSON | Częstotliwość i interwał, w którym opisano, jak często wyzwalacza |  
| frequency | Ciąg | Jednostka czasu, który opisuje, jak często wyzwalacza: "Drugi", "Minute", "Godzina", "Dzień", "Tygodnia" lub "Miesiąc" | 
| interval | Liczba całkowita | Dodatnia liczba całkowita, w tym artykule opisano, jak często wyzwalacza na podstawie częstotliwości. <p>Poniżej przedstawiono minimalne i maksymalne odstępach czasu: <p>-Miesięczny: 1-16 miesięcy </br>-Dniowego: 1-500 dni </br>-Godzinnym: 1-12 000 godzin </br>-Minutowy: 1-72,000 minut </br>-Drugi: 1-9,999,999 sekund<p>Na przykład jeśli interwał to 6 i częstotliwości jest "miesiąc", cykl jest co 6 miesięcy. | 
|||| 

*Opcjonalne*

| Nazwa elementu | Typ | Opis | 
| ------------ | ---- | ----------- | 
| [Warunki](#trigger-conditions) | Tablica | Jeden lub więcej czynników, które określają, czy chcesz uruchomić przepływ pracy | 
| [splitOn](#split-on-debatch) | Ciąg | Wyrażenie, które dzieli, lub *debatches*, elementy tablicy do wielu wystąpień przepływu pracy do przetwarzania. Ta opcja jest dostępna dla wyzwalaczy, które zwracają tablicy i tylko podczas pracy bezpośrednio w widoku kodu. | 
| [operationOptions](#trigger-operation-options) | Ciąg | Niektóre wyzwalacze udostępniają dodatkowe opcje, które umożliwiają zmianę domyślnego zachowania wyzwalacza | 
||||| 

## <a name="trigger-types-and-details"></a>Typy wyzwalaczy i szczegóły  

Każdy typ wyzwalacza ma inny interfejs i dane wejściowe, które określają zachowanie tego wyzwalacza. 

| Typ wyzwalacza | Opis | 
| ------------ | ----------- | 
| [**Cyklu**](#recurrence-trigger) | Generowane na podstawie zdefiniowanego harmonogramu. Można ustawić przyszłą datę i godzinę dla wyzwalania tego wyzwalacza. Na podstawie częstotliwości, można również określić godziny i dni na potrzeby uruchamiania przepływu pracy. | 
| [**Żądanie**](#request-trigger)  | Umożliwia aplikacji logiki do punktu końcowego można wywołać, znanej także jako wyzwalacz "manual". Na przykład, zobacz [zagnieździć przepływy pracy z punktami końcowymi protokołu HTTP lub wywołaj wyzwalacz,](../logic-apps/logic-apps-http-endpoint.md). | 
| [**HTTP**](#http-trigger) | Sprawdza, lub *sond*, punkt końcowy HTTP sieci web. Punkt końcowy HTTP musi być zgodna z kontrakt określonego wyzwalacza, za pomocą "202" wzorca asynchronicznego lub tablicę. | 
| [**ApiConnection**](#apiconnection-trigger) | Działa jak wyzwalacza HTTP, ale używa [zarządzany przez firmę Microsoft interfejsy API](../connectors/apis-list.md). | 
| [**HTTPWebhook**](#httpwebhook-trigger) | Działa jak wyzwalacza żądania, ale odwołuje się określony adres URL do rejestrowania i wyrejestrowania. |
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Działa jak wyzwalacza HTTPWebhook, ale używa [zarządzany przez firmę Microsoft interfejsy API](../connectors/apis-list.md). | 
||| 

<a name="recurrence-trigger"></a>

## <a name="recurrence-trigger"></a>Wyzwalacz cyklu  

Wyzwalacz uruchamia oparte na określonej cyklu i harmonogram i zapewnia prosty sposób regularnie uruchamiania przepływu pracy. 

Poniżej przedstawiono definicję wyzwalacza:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month",
      "interval": <recurrence-interval-based-on-frequency>,
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
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```
*Wymagane*

| Nazwa elementu | Typ | Opis | 
| ------------ | ---- | ----------- | 
| Cykl | Obiekt JSON | Nazwa wyzwalacza jest obiektem opisanego w formacie Javascript Object Notation (JSON)  | 
| type | Ciąg | Typ wyzwalacza, czyli "Cyklu" | 
| Dane wejściowe | Obiekt JSON | Dane wejściowe wyzwalacza, które określają zachowanie tego wyzwalacza | 
| recurrence | Obiekt JSON | Częstotliwość i interwał, w którym opisano, jak często wyzwalacza |  
| frequency | Ciąg | Jednostka czasu, który opisuje, jak często wyzwalacza: "Drugi", "Minute", "Godzina", "Dzień", "Tygodnia" lub "Miesiąc" | 
| interval | Liczba całkowita | Dodatnia liczba całkowita, w tym artykule opisano, jak często wyzwalacza na podstawie częstotliwości. <p>Poniżej przedstawiono minimalne i maksymalne odstępach czasu: <p>-Miesięczny: 1-16 miesięcy </br>-Dniowego: 1-500 dni </br>-Godzinnym: 1-12 000 godzin </br>-Minutowy: 1-72,000 minut </br>-Drugi: 1-9,999,999 sekund<p>Na przykład jeśli interwał to 6 i częstotliwości jest "miesiąc", cykl jest co 6 miesięcy. | 
|||| 

*Opcjonalne*

| Nazwa elementu | Typ | Opis | 
| ------------ | ---- | ----------- | 
| startTime | Ciąg | Data rozpoczęcia i czas w następującym formacie: <p>RRRR-MM-Ddtgg, jeśli określona strefa czasowa <p>— lub — <p>RRRR-MM-Ddtgg, jeśli nie określisz strefy czasowej <p>Tak na przykład, jeśli chcesz 18 września 2017 godzinie 2:00, określ "2017-09-18T14:00:00" i określ strefę czasową, takie jak "Czas pacyficzny", lub określ "2017-09-18T14:00:00Z" bez strefę czasową. <p>**Uwaga:** ta godzina rozpoczęcia musi występować po [ISO 8601 daty czasu specyfikacji](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [format daty i godziny UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [przesunięcie UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie określisz strefy czasowej, należy dodać litery "Z" na końcu bez spacji. To "Z" odwołuje się do jego odpowiednik [milową czas](https://en.wikipedia.org/wiki/Nautical_time). <p>Proste harmonogramów, godzina rozpoczęcia jest pierwsze wystąpienie, natomiast w przypadku złożonych harmonogramy wyzwalacz nie wyzwalać żadnych wcześniej niż czas rozpoczęcia. Aby uzyskać więcej informacji na temat daty rozpoczęcia i godziny, zobacz [tworzenie i harmonogram regularnie uruchomionych zadań](../connectors/connectors-native-recurrence.md). | 
| timeZone | Ciąg | Dotyczy tylko po określeniu godziny rozpoczęcia, ponieważ wyzwalacz nie akceptuje [przesunięcie UTC](https://en.wikipedia.org/wiki/UTC_offset). Określ strefę czasową, który chcesz zastosować. | 
| hours | Liczba całkowita lub tablicy liczba całkowita | Jeśli określisz "Day" lub "Tydzień" dla `frequency`, można określić co najmniej jeden liczby całkowite z przedziału od 0 do 23 rozdzielonych przecinkami, w formacie godziny, dnia, gdy chcesz uruchomić przepływ pracy. <p>Na przykład jeśli określisz "10", "12" i "14" możesz uzyskać 10 AM, 12 PM i 14: 00 jako znaki godzinę. | 
| minutes | Liczba całkowita lub tablicy liczba całkowita | Jeśli określisz "Dzień" lub "Tydzień" dla `frequency`, można określić co najmniej jeden liczby całkowite z przedziału od 0 do 59, oddzielonych przecinkami, jako minuty, godziny, jeśli chcesz uruchomić przepływ pracy. <p>Na przykład "30" można określić jako minuta znaku i w poprzednim przykładzie, godziny, dnia, możesz uzyskać godziny 10:30, 12:30:00 i 14:30:00. | 
| weekDays | Ciąg lub tablicę ciągów | Jeśli określisz "Tydzień" dla `frequency`, można określić co najmniej jeden dzień, oddzielając je średnikami, jeśli chcesz uruchomić przepływ pracy: "Poniedziałek", "Wtorek", "Środa", "Czwartek", "Piątek", "Sobota" i "Niedziela" | 
| Współbieżność | Obiekt JSON | Cykliczne i sondowania wyzwalaczy ten obiekt określa maksymalną liczbę wystąpień przepływu pracy, które można uruchomić w tym samym czasie. Użyj tej wartości, aby ograniczyć liczbę żądań, które systemy zaplecza odbierania. <p>Na przykład ta wartość ustawia limit współbieżności 10 wystąpień: `"concurrency": { "runs": 10 }` | 
| operationOptions | Ciąg | `singleInstance` Opcja określa, że wyzwalacz uruchamia się dopiero po zakończeniu wszystkich aktywnej przebiegi. Zobacz [wyzwalaczy: uruchomienie tylko wtedy, gdy aktywne uruchamia Zakończ](#single-instance). | 
|||| 

*Przykład 1*

Wyzwalacz cyklu podstawowa jest przeprowadzana codziennie:

```json
"recurrenceTriggerName": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Przykład 2*

Można określić daty rozpoczęcia i czas uruchamiania wyzwalacza. Tego wyzwalacza cyklu rozpoczyna się od określonej daty, a następnie uruchamiane codziennie:

```json
"recurrenceTriggerName": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Przykład 3*

Wyzwalacz cyklu rozpoczyna się 9 września 2017 godzinie 2:00 i jest uruchamiany co tydzień w każdy poniedziałek w godziny 10:30, 12:30:00 i 14:30:00 (pacyficzny czas standardowy):

``` json
"myRecurrenceTrigger": {
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

Aby uzyskać więcej informacji oraz przykłady dla tego wyzwalacza, zobacz [tworzenie i harmonogram regularnie uruchomionych zadań](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

## <a name="request-trigger"></a>Wyzwalacz żądania

Tego wyzwalacza sprawia, że aplikację logiki jest wywoływane przez utworzenie punktu końcowego, który może zaakceptować przychodzące żądania HTTP. Aby wywołać tego wyzwalacza, należy użyć `listCallbackUrl` interfejsu API w [interfejsu API REST usługi przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows). Aby dowiedzieć się, jak używać tego wyzwalacza jako punkt końcowy HTTP, zobacz [zagnieździć przepływy pracy z punktami końcowymi protokołu HTTP lub wywołaj wyzwalacz,](../logic-apps/logic-apps-http-endpoint.md).

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "GET | POST | PUT | PATCH | DELETE | HEAD",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<propertyName>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   }
}
```

*Wymagane*

| Nazwa elementu | Typ | Opis | 
| ------------ | ---- | ----------- | 
| Ręcznie | Obiekt JSON | Nazwa wyzwalacza jest obiektem opisanego w formacie Javascript Object Notation (JSON)  | 
| type | Ciąg | Typ wyzwalacza, czyli "Żądania" | 
| rodzaj | Ciąg | Typ żądania, czyli "Http" | 
| Dane wejściowe | Obiekt JSON | Dane wejściowe wyzwalacza, które określają zachowanie tego wyzwalacza | 
|||| 

*Opcjonalne*

| Nazwa elementu | Typ | Opis | 
| ------------ | ---- | ----------- | 
| method | Ciąg | Metoda żądania należy użyć do wywołania wyzwalacza: "GET", "PUT", "POST", "Poprawka", "DELETE" ani "HEAD" |
| RelativePath | Ciąg | Względna ścieżka Parametr, który akceptuje punkt końcowy HTTP URL | 
| Schemat | Obiekt JSON | Schematu JSON, który opisuje i sprawdza poprawność ładunku lub niedozwolonych wyzwalacza odbiera z żądania przychodzącego. Ten schemat ułatwia akcje kolejnych wiedzieć, właściwości, aby odwołać. | 
| properties | Obiekt JSON | Co najmniej jednej właściwości w schemacie JSON, który opisuje ładunku | 
| Wymagane | Tablica | Co najmniej jednej właściwości, które wymagają wartości | 
|||| 

*Przykład*

Tego wyzwalacza żądania określa, czy przychodzące żądanie Wywołaj wyzwalacz i sprawdza poprawność danych wejściowych z żądania przychodzącego schematu przy użyciu metody POST protokołu HTTP: 

```json
"myRequestTrigger": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "Object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "String"
                  },
                  "city": {
                     "type": "String"
                  }
               }
            }
         }
      }
   }
} 
```

<a name="http-trigger"></a>

## <a name="http-trigger"></a>Wyzwalacz HTTP  

Tego wyzwalacza określony punkt końcowy sonduje i sprawdza odpowiedź. Odpowiedź określa, czy przepływ pracy ma uruchomić lub nie. `inputs` Obiektów JSON zawiera i wymaga `method` i `uri` parametrów wymaganych do konstruowania wywołanie HTTP:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET | PUT | POST | PATCH | DELETE | HEAD",
      "uri": "<HTTP-or-HTTPS-endpoint-to-poll>",
      "queries": "<query-parameters>",
      "headers": { "<headers-for-request>" },
      "body": { "<payload-to-send>" },
      "authentication": { "<authentication-method>" },
      "retryPolicy": {
          "type": "<retry-policy-type>",
          "interval": "<retry-interval>",
          "count": <number-retry-attempts>
      }
   },
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": <recurrence-interval-based-on-frequency>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```

*Wymagane*

| Nazwa elementu | Typ | Opis | 
| ------------ | ---- | ----------- | 
| HTTP | Obiekt JSON | Nazwa wyzwalacza jest obiektem opisanego w formacie Javascript Object Notation (JSON)  | 
| type | Ciąg | Typ wyzwalacza, czyli "Http" | 
| Dane wejściowe | Obiekt JSON | Dane wejściowe wyzwalacza, które określają zachowanie tego wyzwalacza | 
| method | Yes | Ciąg | Metoda HTTP do sondowania określony punkt końcowy: "GET", "PUT", "POST", "Poprawka", "DELETE" ani "HEAD" | 
| identyfikator URI | Yes| Ciąg | HTTP lub HTTPS adres URL punktu końcowego wyzwalacz sprawdza lub sondowania <p>Maksymalny rozmiar ciągu: 2 KB | 
| recurrence | Obiekt JSON | Częstotliwość i interwał, w którym opisano, jak często wyzwalacza |  
| frequency | Ciąg | Jednostka czasu, który opisuje, jak często wyzwalacza: "Drugi", "Minute", "Godzina", "Dzień", "Tygodnia" lub "Miesiąc" | 
| interval | Liczba całkowita | Dodatnia liczba całkowita, w tym artykule opisano, jak często wyzwalacza na podstawie częstotliwości. <p>Poniżej przedstawiono minimalne i maksymalne odstępach czasu: <p>-Miesięczny: 1-16 miesięcy </br>-Dniowego: 1-500 dni </br>-Godzinnym: 1-12 000 godzin </br>-Minutowy: 1-72,000 minut </br>-Drugi: 1-9,999,999 sekund<p>Na przykład jeśli interwał to 6 i częstotliwości jest "miesiąc", cykl jest co 6 miesięcy. | 
|||| 

*Opcjonalne*

| Nazwa elementu | Typ | Opis | 
| ------------ | ---- | ----------- | 
| — zapytania | Obiekt JSON | Wszystkie parametry zapytania, które chcesz dołączyć do adresu URL <p>Na przykład dodaje ten element `?api-version=2015-02-01` ciągu do adresu URL zapytania: <p>`"queries": { "api-version": "2015-02-01" }` <p>Wynik: `https://contoso.com?api-version=2015-02-01` | 
| nagłówki | Obiekt JSON | Co najmniej jeden nagłówek wysłać z żądaniem <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| treść | Obiekt JSON | Ładunek (dane) do wysłania do punktu końcowego | 
| uwierzytelnianie | Obiekt JSON | Metoda, która żądania przychodzącego, jeśli ma być używana do uwierzytelniania. Aby uzyskać więcej informacji, zobacz [uwierzytelniania połączeń wychodzących harmonogramu](../scheduler/scheduler-outbound-authentication.md). Poza harmonogramem `authority` właściwość jest obsługiwana. Jeśli nie zostanie określony, wartością domyślną jest `https://login.windows.net`, ale możesz użyć innej wartości, takich jak`https://login.windows\-ppe.net`. | 
| retryPolicy | Obiekt JSON | Ten obiekt dostosowuje zachowanie ponownych prób dla sporadyczne błędy, które mają 4xx lub 5xx kodów stanu. Aby uzyskać więcej informacji, zobacz [ponów zasady](../logic-apps/logic-apps-exception-handling.md). | 
| Współbieżność | Obiekt JSON | Cykliczne i sondowania wyzwalaczy ten obiekt określa maksymalną liczbę wystąpień przepływu pracy, które można uruchomić w tym samym czasie. Użyj tej wartości, aby ograniczyć liczbę żądań, które systemy zaplecza odbierania. <p>Na przykład ta wartość ustawia limit współbieżności 10 wystąpień: <p>`"concurrency": { "runs": 10 }` | 
| operationOptions | Ciąg | `singleInstance` Opcja określa, że wyzwalacz uruchamia się dopiero po zakończeniu wszystkich aktywnej przebiegi. Zobacz [wyzwalaczy: uruchomienie tylko wtedy, gdy aktywne uruchamia Zakończ](#single-instance). | 
|||| 

Do pracy ze aplikację logiki, wyzwalacza HTTP wymaga zgodności HTTP API z określonym wzorcem. Wyzwalacza HTTP rozpoznaje tych właściwości:  
  
| Odpowiedź | Wymagane | Opis | 
| -------- | -------- | ----------- |  
| Kod stanu | Yes | "200 OK" Uruchom zaczyna się kod stanu. Każdy kod stanu nie uruchamia Uruchom. | 
| Spróbuj ponownie po nagłówka | Nie | Liczba sekund do aplikacji logiki sonduje ponownie punkt końcowy | 
| Nagłówek lokalizacji | Nie | Adres URL do wywołania w następnym interwału sondowania. Jeśli nie zostanie określony, używany jest oryginalny adres URL. | 
|||| 

*Przykład zachowania dla innego żądania*

| Kod stanu | Ponów próbę po | Zachowanie | 
| ----------- | ----------- | -------- | 
| 200 | {Brak} | Uruchamianie przepływu pracy, a następnie wyszukaj ponownie większej ilości danych po zdefiniowanych cyklu. | 
| 200 | 10 sekund | Uruchamianie przepływu pracy, a następnie wyszukaj ponownie większej ilości danych po 10 sekundach. |  
| 202 | 60 sekund | Nie wyzwolić przepływu pracy. Przy następnej próbie odbywa się na minutę, mogą ulec zdefiniowanych cyklu. Zdefiniowanych cyklu jest mniej niż minutę, pierwszeństwo ma nagłówek ponownych prób po. W przeciwnym razie jest używana zdefiniowanych cyklu. | 
| 400 | {Brak} | Niewłaściwe żądanie, nie uruchamiaj przepływ pracy. Jeśli nie `retryPolicy` jest zdefiniowany, zostanie użyta domyślna zasada. Po osiągnięciu liczby ponownych prób wyzwalacz sprawdza, czy ponownie danych po zdefiniowanych cyklu. | 
| 500 | {Brak}| Błąd serwera, nie uruchamiaj przepływ pracy. Jeśli nie `retryPolicy` jest zdefiniowany, zostanie użyta domyślna zasada. Po osiągnięciu liczby ponownych prób wyzwalacz sprawdza, czy ponownie danych po zdefiniowanych cyklu. | 
|||| 

### <a name="http-trigger-outputs"></a>Dane wyjściowe wyzwalacza HTTP

| Nazwa elementu | Typ | Opis |
| ------------ | ---- | ----------- |
| nagłówki | Obiekt JSON | Nagłówki z odpowiedzi HTTP | 
| treść | Obiekt JSON | Treść z odpowiedzi HTTP | 
|||| 

<a name="apiconnection-trigger"></a>

## <a name="apiconnection-trigger"></a>Wyzwalacz APIConnection  

Wyzwalacz działa jak [wyzwalacza HTTP](#http-trigger), ale używa [zarządzany przez firmę Microsoft interfejsy API](../connectors/apis-list.md) zatem inne parametry dla tego wyzwalacza. 

Poniżej przedstawiono definicję wyzwalacza, mimo że wiele sekcje są opcjonalne, więc wyzwalacza zachowanie zależy od tego, czy omówione są:

```json
"<APIConnectionTriggerName>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "api": {
            "runtimeUrl": "<managed-API-endpoint-URL>"
         },
         "connection": {
            "name": "@parameters('$connections')['<connection-name>'].name"
         },
      },
      "method": "GET | PUT | POST | PATCH | DELETE | HEAD",
      "queries": "<query-parameters>",
      "headers": { "<headers-for-request>" },
      "body": { "<payload-to-send>" },
      "authentication": { "<authentication-method>" },
      "retryPolicy": {
          "type": "<retry-policy-type>",
          "interval": "<retry-interval>",
          "count": <number-retry-attempts>
      }
   },
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": "<recurrence-interval-based-on-frequency>"
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```

*Wymagane*

| Nazwa elementu | Typ | Opis | 
| ------------ | ---- | ----------- | 
| *APIConnectionTriggerName* | Obiekt JSON | Nazwa wyzwalacza jest obiektem opisanego w formacie Javascript Object Notation (JSON)  | 
| type | Ciąg | Typ wyzwalacza, czyli "ApiConnection" | 
| Dane wejściowe | Obiekt JSON | Dane wejściowe wyzwalacza, które określają zachowanie tego wyzwalacza | 
| host | Obiekt JSON | Obiekt JSON, który opisuje hosta bramy i identyfikator zarządzanego interfejsu API <p>`host` Obiekt JSON ma następujące elementy: `api` i `connection` | 
| api | Obiekt JSON | Adres URL punktu końcowego dla zarządzanego interfejsu API: <p>`"runtimeUrl": "<managed-API-endpoint-URL>"` | 
| połączenie | Obiekt JSON | Nazwa zarządzanego połączenia interfejsu API używanych przez przepływ pracy, który musi zawierać odwołania do parametru o nazwie `$connection`: <p>`"name": "@parameters('$connections')['<connection-name>'].name"` | 
| method | Ciąg | Metoda HTTP służąca do komunikacji przy użyciu zarządzanego interfejsu API: "GET", "PUT", "POST", "Poprawka", "DELETE" ani "HEAD" | 
| recurrence | Obiekt JSON | Częstotliwość i interwał, w którym opisano, jak często wyzwalacza |  
| frequency | Ciąg | Jednostka czasu, który opisuje, jak często wyzwalacza: "Drugi", "Minute", "Godzina", "Dzień", "Tygodnia" lub "Miesiąc" | 
| interval | Liczba całkowita | Dodatnia liczba całkowita, w tym artykule opisano, jak często wyzwalacza na podstawie częstotliwości. <p>Poniżej przedstawiono minimalne i maksymalne odstępach czasu: <p>-Miesięczny: 1-16 miesięcy </br>-Dniowego: 1-500 dni </br>-Godzinnym: 1-12 000 godzin </br>-Minutowy: 1-72,000 minut </br>-Drugi: 1-9,999,999 sekund<p>Na przykład jeśli interwał to 6 i częstotliwości jest "miesiąc", cykl jest co 6 miesięcy. | 
|||| 

*Opcjonalne*

| Nazwa elementu | Typ | Opis | 
| ------------ | ---- | ----------- | 
| — zapytania | Obiekt JSON | Wszystkie parametry zapytania, które chcesz dołączyć do adresu URL <p>Na przykład dodaje ten element `?api-version=2015-02-01` ciągu do adresu URL zapytania: <p>`"queries": { "api-version": "2015-02-01" }` <p>Wynik: `https://contoso.com?api-version=2015-02-01` | 
| nagłówki | Obiekt JSON | Co najmniej jeden nagłówek wysłać z żądaniem <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| treść | Obiekt JSON | Obiekt JSON, który opisuje ładunku (dane) do wysyłania do zarządzanego interfejsu API | 
| uwierzytelnianie | Obiekt JSON | Metoda, która żądanie przychodzące ma być używana do uwierzytelniania. Aby uzyskać więcej informacji, zobacz [uwierzytelniania połączeń wychodzących harmonogramu](../scheduler/scheduler-outbound-authentication.md). |
| retryPolicy | Obiekt JSON | Ten obiekt dostosowuje zachowanie ponownych prób dla sporadyczne błędy, które mają 4xx lub 5xx kody stanu: <p>`"retryPolicy": { "type": "<retry-policy-type>", "interval": "<retry-interval>", "count": <number-retry-attempts> }` <p>Aby uzyskać więcej informacji, zobacz [ponów zasady](../logic-apps/logic-apps-exception-handling.md). | 
| Współbieżność | Obiekt JSON | Cykliczne i sondowania wyzwalaczy ten obiekt określa maksymalną liczbę wystąpień przepływu pracy, które można uruchomić w tym samym czasie. Użyj tej wartości, aby ograniczyć liczbę żądań, które systemy zaplecza odbierania. <p>Na przykład ta wartość ustawia limit współbieżności 10 wystąpień: `"concurrency": { "runs": 10 }` | 
| operationOptions | Ciąg | `singleInstance` Opcja określa, że wyzwalacz uruchamia się dopiero po zakończeniu wszystkich aktywnej przebiegi. Zobacz [wyzwalaczy: uruchomienie tylko wtedy, gdy aktywne uruchamia Zakończ](#single-instance). | 
||||

*Przykład*

```json
"Create_daily_report": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "api": {
            "runtimeUrl": "https://myReportsRepo.example.com/"
         },
         "connection": {
            "name": "@parameters('$connections')['<connection-name>'].name"
         }     
      },
      "method": "POST",
      "body": {
         "category": "statusReports"
      }  
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

### <a name="apiconnection-trigger-outputs"></a>Wyzwalacz APIConnection danych wyjściowych
 
| Nazwa elementu | Typ | Opis |
| ------------ | ---- | ----------- |
| nagłówki | Obiekt JSON | Nagłówki z odpowiedzi HTTP | 
| treść | Obiekt JSON | Treść z odpowiedzi HTTP | 
|||| 

<a name="httpwebhook-trigger"></a>

## <a name="httpwebhook-trigger"></a>Wyzwalacz HTTPWebhook  

Wyzwalacz działa jak [wyzwalacza żądania](#request-trigger) przez utworzenie punktu końcowego można wywołać dla aplikacji logiki. Jednak tego wyzwalacza wymaga także adres URL określony punkt końcowy rejestrowania lub wyrejestrowywania subskrypcji. Można określić limity dla elementu trigger elementu webhook w taki sam sposób jak [limity asynchroniczne HTTP](#asynchronous-limits). 

Poniżej przedstawiono definicję wyzwalacza, chociaż w wielu sekcjach są opcjonalne i zachowanie tego wyzwalacza jest zależna od sekcje, które Użyj lub Pomiń:

```json
"HTTP_Webhook": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "<subscribe-to-endpoint-URL>",
            "headers": { "<headers-for-request>" },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "<subscription-topic>"
            },
            "authentication": {},
            "retryPolicy": {}
        },
        "unsubscribe": {
            "method": "POST",
            "url": "<unsubscribe-from-endpoint-URL>",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "<subscription-topic>"
            },
            "authentication": {}
        }
    },
}
```

*Wymagane*

| Nazwa elementu | Typ | Opis | 
| ------------ | ---- | ----------- | 
| HTTP_Webhook | Obiekt JSON | Nazwa wyzwalacza jest obiektem opisanego w formacie Javascript Object Notation (JSON)  | 
| type | Ciąg | Typ wyzwalacza, czyli "HttpWebhook" | 
| Dane wejściowe | Obiekt JSON | Dane wejściowe wyzwalacza, które określają zachowanie tego wyzwalacza | 
| Subskrypcja | Obiekt JSON| Żądania wychodzącego do połączenia i wykonania rejestracji początkowej podczas tworzenia wyzwalacza. To wywołanie dzieje się tak, aby uruchomić wyzwalacz nasłuchuje zdarzeń w punkcie końcowym. Aby uzyskać więcej informacji, zobacz [subskrybowanie i anulowanie subskrypcji](#subscribe-unsubscribe). | 
| method | Ciąg | Metoda HTTP użytej dla żądania subskrypcji: "GET", "PUT", "POST", "Poprawka", "DELETE" ani "HEAD" | 
| identyfikator URI | Ciąg | Adres URL punktu końcowego, w których można wysłać żądania subskrypcji | 
|||| 

*Opcjonalne*

| Nazwa elementu | Typ | Opis | 
| ------------ | ---- | ----------- | 
| Anulowanie subskrypcji | Obiekt JSON | Wychodzącej żądanie automatycznie wywoła i anulować subskrypcję, gdy operacja sprawia, że wyzwalacza jest nieprawidłowa. Aby uzyskać więcej informacji, zobacz [subskrybowanie i anulowanie subskrypcji](#subscribe-unsubscribe). | 
| method | Ciąg | Metoda HTTP do użycia podczas anulowania żądania: "GET", "PUT", "POST", "Poprawka", "DELETE" ani "HEAD" | 
| identyfikator URI | Ciąg | Adres URL punktu końcowego, w których można wysłać żądania anulowania | 
| treść | Obiekt JSON | Obiekt JSON, który opisuje ładunku (dane) dla subskrypcji lub anulowania żądania | 
| uwierzytelnianie | Obiekt JSON | Metoda, która żądanie przychodzące ma być używana do uwierzytelniania. Aby uzyskać więcej informacji, zobacz [uwierzytelniania połączeń wychodzących harmonogramu](../scheduler/scheduler-outbound-authentication.md). |
| retryPolicy | Obiekt JSON | Ten obiekt dostosowuje zachowanie ponownych prób dla sporadyczne błędy, które mają 4xx lub 5xx kody stanu: <p>`"retryPolicy": { "type": "<retry-policy-type>", "interval": "<retry-interval>", "count": <number-retry-attempts> }` <p>Aby uzyskać więcej informacji, zobacz [ponów zasady](../logic-apps/logic-apps-exception-handling.md). | 
|||| 

*Przykład*

```json
"myAppSpotTrigger": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "headers": {},
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
         },
      }
   },
}
```

<a name="subscribe-unsubscribe"></a>

### <a name="subscribe-and-unsubscribe"></a>`subscribe` i `unsubscribe`

`subscribe` Wywołania się stanie w przypadku przepływu pracy zmiany w dowolny sposób, na przykład gdy poświadczenia zostaną odnowione lub zmiany parametrów wejściowych wyzwalacza. Wywołanie używa tych samych parametrach jako standardowa akcji HTTP. 
 
`unsubscribe` Wywołania się automatycznie stanie, jeśli operacja powoduje wyzwalacza HTTPWebhook nieprawidłowy, na przykład:

* Usuwanie lub wyłączanie wyzwalacza. 
* Usuwanie lub wyłączanie przepływ pracy. 
* Usuwanie lub wyłączanie subskrypcji. 

Do obsługi tych wywołań `@listCallbackUrl()` funkcja zwraca unikatowego "adres URL wywołania zwrotnego" dla tego wyzwalacza. Ten adres URL reprezentuje unikatowy identyfikator dla punktów końcowych, które używają interfejsu API REST usługi. Parametry dla tej funkcji są takie same jak wyzwalacza HTTP.

### <a name="httpwebhook-trigger-outputs"></a>Wyzwalacz HTTPWebhook danych wyjściowych

| Nazwa elementu | Typ | Opis |
| ------------ | ---- | ----------- |
| nagłówki | Obiekt JSON | Nagłówki z odpowiedzi HTTP | 
| treść | Obiekt JSON | Treść z odpowiedzi HTTP | 
|||| 

<a name="apiconnectionwebhook-trigger"></a>

## <a name="apiconnectionwebhook-trigger"></a>Wyzwalacz ApiConnectionWebhook

Wyzwalacz działa jak [wyzwalacza HTTPWebhook](#httpwebhook-trigger), ale używa [zarządzany przez firmę Microsoft interfejsy API](../connectors/apis-list.md). 

Poniżej przedstawiono definicję wyzwalacza:

```json
"<ApiConnectionWebhookTriggerName>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },        
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "queries": "<query-parameters>"
   }
}
```

*Wymagane*

| Nazwa elementu | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*ApiConnectionWebhookTriggerName*> | Obiekt JSON | Nazwa wyzwalacza jest obiektem opisanego w formacie Javascript Object Notation (JSON)  | 
| type | Ciąg | Typ wyzwalacza, czyli "ApiConnectionWebhook" | 
| Dane wejściowe | Obiekt JSON | Dane wejściowe wyzwalacza, które określają zachowanie tego wyzwalacza | 
| host | Obiekt JSON | Obiekt JSON, który opisuje hosta bramy i identyfikator zarządzanego interfejsu API <p>`host` Obiekt JSON ma następujące elementy: `api` i `connection` | 
| połączenie | Obiekt JSON | Nazwa zarządzanego połączenia interfejsu API używanych przez przepływ pracy, który musi zawierać odwołania do parametru o nazwie `$connection`: <p>`"name": "@parameters('$connections')['<connection-name>']['connectionId']"` | 
| treść | Obiekt JSON | Obiekt JSON, który opisuje ładunku (dane) do wysyłania do zarządzanego interfejsu API | 
| NotificationUrl | Ciąg | Zwraca unikatową "adres URL wywołania zwrotnego" dla tego wyzwalacza używanego zarządzanego interfejsu API | 
|||| 

*Opcjonalne*

| Nazwa elementu | Typ | Opis | 
| ------------ | ---- | ----------- | 
| — zapytania | Obiekt JSON | Wszystkie parametry zapytania, które chcesz dołączyć do adresu URL <p>Na przykład dodaje ten element `?folderPath=Inbox` ciągu do adresu URL zapytania: <p>`"queries": { "folderPath": "Inbox" }` <p>Wynik: `https://<managed-API-URL>?folderPath=Inbox` | 
|||| 

<a name="trigger-conditions"></a>

## <a name="triggers-conditions"></a>Wyzwalacze: warunki

Dla dowolnego wyzwalacza z jednego lub kilku warunków, które określają, czy przepływ pracy ma uruchomić lub nie można dołączyć tablicy. W tym przykładzie wyzwalacza raportu generowane tylko podczas przepływu pracy `sendReports` parametr ma wartość true. 

```json
"myDailyReportTrigger": {
   "type": "Recurrence",
   "conditions": [ {
      "expression": "@parameters('sendReports')"
   } ],
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

Ponadto warunki można odwoływać się kod stanu tego wyzwalacza. Na przykład załóżmy, że chcesz uruchomić przepływ pracy tylko w przypadku kodu stanu "500" zwraca witryny sieci Web:

``` json
"conditions": [ {
   "expression": "@equals(triggers().code, 'InternalServerError')"  
} ]  
```  

> [!NOTE]
> Domyślnie, wyzwalacz uruchamia się tylko na odbieranie "200 OK" odpowiedzi. Gdy wyrażenie odwołuje się do tego wyzwalacza kodu stanu w dowolny sposób, zostanie zastąpiony wyzwalacza domyślne zachowanie. Tak Jeśli chcesz, aby uruchomić dla wiele kodów stanu, na przykład kodem stanu 200 i kod stanu 201, wyzwalacz musi zawierać niniejszych jako warunek: 
>
> `@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="triggers-split-an-array-into-multiple-runs"></a>Wyzwalacze: Podział tablicy na wielu uruchomień

Jeśli Twój wyzwalacz zwraca tablicę aplikacji logiki do przetwarzania, czasami pętli "for each" może trwać zbyt długo przetworzyć każdy element tablicy. Zamiast tego można użyć **SplitOn** właściwości w wyzwalacz do *debatch* tablicy. 

Debatching dzieli elementy tablicy i uruchamia nowe wystąpienie aplikacji logiki działającą dla każdego elementu tablicy. Ta metoda jest przydatna, na przykład, gdy chcesz przeprowadzać sondowanie punktu końcowego, który może zwrócić kilka nowych elementów między interwałami sondowania.
Na maksymalną liczbę tablicy elementy **SplitOn** może przetwarzać do uruchomienia aplikacji logiki pojedynczego, zobacz [limity i konfiguracji](../logic-apps/logic-apps-limits-and-config.md). 

> [!NOTE]
> Możesz dodać **SplitOn** tylko do wyzwalaczy przez ręczne definiowanie lub zastępowanie w widoku kodu dla aplikacji logiki definicji JSON. Nie można użyć **SplitOn** umożliwia Implementowanie wzorca synchronicznej odpowiedzi. Każdy przepływ pracy, który używa **SplitOn** i zawiera odpowiedzi akcja uruchamia asynchronicznie i natychmiast wysyła `202 ACCEPTED` odpowiedzi.

Jeśli plik struktury Swagger tego wyzwalacza opisuje ładunku, który jest tablicą, **SplitOn** właściwość jest automatycznie dodawany do wyzwalacz. W przeciwnym razie dodać tę właściwość z tablicy, która ma być debatch ładunku odpowiedzi. 

Załóżmy na przykład, interfejs API, który zwraca tej odpowiedzi: 
  
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
  
Aplikację logiki musi tylko zawartość z `Rows`, dzięki czemu można tworzyć wyzwalacz tak jak ten przykład.

``` json
"myDebatchTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "Second",
        "interval": 1
    },
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Jeśli używasz `SplitOn` polecenia i nie można pobrać właściwości, które są poza tablicy. Aby na przykład nie można pobrać `status` zwrócił w odpowiedzi z interfejsu API.
> 
> Aby uniknąć awarii, jeśli `Rows` właściwość nie istnieje, w tym przykładzie użyto `?` operatora.

Definicja przepływu pracy można teraz używać `@triggerBody().name` uzyskanie `customer-name-one` z pierwszego uruchomienia i `customer-name-two` z drugim przebiegu. Tak, wyzwalacz generuje wygląd te przykłady, takich jak:

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

<a name="trigger-operation-options"></a>

## <a name="triggers-operation-options"></a>Wyzwalaczy: Opcje operacji

Te wyzwalacze Podaj więcej opcji, które umożliwiają zmianę domyślnego zachowania.

| Wyzwalacz | Opcja operacji | Opis |
|---------|------------------|-------------|
| [Cykl](#recurrence-trigger), <br>[HTTP](#http-trigger), <br>[ApiConnection](#apiconnection-trigger) | singleInstance | Zakończono Fire, uruchamiania tylko po wszystkich aktywnych wyzwalacza. |
||||

<a name="single-instance"></a>

### <a name="triggers-fire-only-after-active-runs-finish"></a>Wyzwalacze: Uruchamiają się tylko wtedy, gdy aktywne uruchamia Zakończ

Wyzwalaczy, w którym można ustawić cykl można określić, czy uruchomić wyzwalacz działa tylko po wszystkich aktywnych zostało ukończone. W przypadku wystąpienia przepływu pracy jest uruchomiona zaplanowanego cyklu wyzwalacza pomija i czeka, aż do następnego zaplanowanego cyklu przed ponownym sprawdzeniem. Na przykład:

```json
"myRecurringTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Hour",
        "interval": 1,
    },
    "operationOptions": "singleInstance"
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Przegląd działań

Istnieje wiele typów działań, każde z nich unikatowe zachowanie. Każdy typ działania ma różnych komponentów, które określają zachowanie akcji. Akcje kolekcji może zawierać wiele działań między sobą. 

### <a name="standard-actions"></a>Działania standardowe  

| Typ akcji | Opis | 
| ----------- | ----------- | 
| **HTTP** | Wywołuje punkt końcowy HTTP sieci web. | 
| **ApiConnection**  | Działa jak akcja HTTP, ale używa [zarządzany przez firmę Microsoft interfejsy API](https://docs.microsoft.com/azure/connectors/apis-list). | 
| **ApiConnectionWebhook** | Działa jak HTTPWebhook, ale używają API zarządzany przez firmę Microsoft. | 
| **Odpowiedź** | Określa odpowiedź dla połączenia przychodzącego. | 
| **Redaguj** | Tworzy obiekt dowolnych akcji w danych wejściowych. | 
| **Funkcja** | Reprezentuje funkcję platformy Azure. | 
| **oczekiwania** | Czeka stałą lub określonego czasu. | 
| **przepływ pracy** | Reprezentuje zagnieżdżony przepływ pracy. | 
| **Redaguj** | Tworzy obiekt dowolnych akcji w danych wejściowych. | 
| **Zapytanie** | Filtruje tablicy na podstawie warunku. | 
| **Wybierz** | Projektów każdy element tablicy na nową wartość. Na przykład można przekonwertować tablicy liczb znajdujących się na tablicę obiektów. | 
| **Tabela** | Konwertuje tablicę elementów do tabeli CSV lub HTML. | 
| **Zakończenie** | Zatrzymuje wykonywanie przepływu pracy. | 
| **oczekiwania** | Czeka stałą lub określonego czasu. | 
| **przepływ pracy** | Reprezentuje zagnieżdżony przepływ pracy. | 
||| 

### <a name="collection-actions"></a>Akcje kolekcji

| Typ akcji | Opis | 
| ----------- | ----------- | 
| **If** | Oszacować wyrażenia i na podstawie wyniku, uruchamia odpowiednie gałęzi. | 
| **Przełącznik** | Wykonaj różne akcje na podstawie wartości określonego obiektu. | 
| **ForEach** | Ta akcja pętli iteruje tablicy i wykonuje akcje wewnętrzne na każdy element tablicy. | 
| **Until** | Ta akcja pętli wykonuje akcje wewnętrzny dopóki warunek wyników do wartości true. | 
| **Zakres** | Służy do logicznego grupowania innych działań. | 
|||  

## <a name="http-action"></a>Akcja HTTP  

Akcja HTTP wywołuje określony punkt końcowy i sprawdza odpowiedzi, aby określić, czy przepływ pracy, należy uruchomić lub nie. Na przykład:
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest"
    }
}
```

W tym miejscu `inputs` obiektu przyjmuje tych parametrów wymaganych do konstruowania wywołanie HTTP: 

| Nazwa elementu | Wymagane | Typ | Opis | 
| ------------ | -------- | ---- | ----------- | 
| method | Yes | Ciąg | Użyto jednego z tych metod HTTP: "GET", "POST", "PUT", "DELETE", "Poprawka" ani "HEAD" | 
| identyfikator URI | Yes| Ciąg | Końcowy HTTP lub HTTPs, który sprawdza zgodność wyzwalacza. Maksymalny rozmiar ciągu: 2 KB | 
| — zapytania | Nie | Obiekt JSON | Reprezentuje wszystkie parametry zapytania, które chcesz uwzględnić w adresie URL. <p>Na przykład `"queries": { "api-version": "2015-02-01" }` dodaje `?api-version=2015-02-01` do adresu URL. | 
| nagłówki | Nie | Obiekt JSON | Reprezentuje każdy nagłówek, który jest wysyłany w żądaniu. <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| treść | Nie | Obiekt JSON | Reprezentuje ładunek, które są wysyłane do punktu końcowego. | 
| retryPolicy | Nie | Obiekt JSON | Ten obiekt jest używany dla Dostosowywanie zachowania ponownych prób dla 4xx lub 5xx błędów. Aby uzyskać więcej informacji, zobacz [ponów zasady](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nie | Ciąg | Definiuje zestaw specjalnego zachowania do zastąpienia. | 
| uwierzytelnianie | Nie | Obiekt JSON | Reprezentuje metodę, która powinna być używana do uwierzytelniania żądania. Aby uzyskać więcej informacji, zobacz [uwierzytelniania połączeń wychodzących harmonogramu](../scheduler/scheduler-outbound-authentication.md). <p>Poza harmonogramem, jest jedna właściwość więcej obsługiwanych: `authority`. Domyślnie ta wartość jest `https://login.windows.net` gdy nie jest określony, ale możesz użyć innej wartości, takich jak`https://login.windows\-ppe.net`. | 
||||| 

Akcje HTTP oraz akcje APIConnection obsługuje *ponów zasady*. Zasady ponawiania dotyczy sporadycznych błędów jest określony jako kodów stanu HTTP 408 429 i 5xx, oprócz wszelkie wyjątki łączności. Można zdefiniować te zasady z `retryPolicy` obiektów, jak pokazano poniżej:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

W tym przykładzie akcji HTTP ponowi próbę pobieranie najnowsze dwa razy, jeśli występują sporadyczne błędy dla wszystkich trzech wykonaniami i 30-sekundowe opóźnienie między kolejnymi próbami:
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy": {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

Interwał ponawiania prób jest określona w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Ten interwał ma wartości domyślnej i co najmniej 20 sekund, podczas gdy wartość maksymalna to jedna godzina. Domyślne i maksymalnej liczby ponownych prób odpowiada czterem godzinom. Jeśli nie określisz definicję zasad ponawiania `fixed` strategii jest używany z domyślnej wartości interwału i liczba ponownych prób. Aby wyłączyć zasady ponawiania, ustaw jej typ `None`.

### <a name="asynchronous-patterns"></a>Asynchroniczne wzorce

Domyślnie wszystkie działania oparte na protokole HTTP obsługuje wzorzec standardowych operacji asynchronicznej. Tak w serwerze zdalnym wskazuje, czy żądanie jest zaakceptowano do przetwarzania za pomocą odpowiedzi "202 ZAAKCEPTOWANE", aparat Logic Apps śledzi sondowanie adres URL określony w odpowiedzi nagłówek lokalizacji do momentu osiągnięcia terminali stan odpowiedzi z systemem innym niż 202.
  
Aby wyłączyć asynchroniczne zachowanie opisany powyżej, ustaw `operationOptions` do `DisableAsyncPattern` w danych wejściowych działania. W takim przypadku wynik akcji jest oparty na początkowej 202 odpowiedzi z serwera. Na przykład:
  
```json
"invokeLongRunningOperationAction": {
    "type": "Http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

<a name="asynchronous-limits"></a>

#### <a name="asynchronous-limits"></a>Limity asynchroniczne

Można ograniczyć czas trwania asynchroniczny wzorzec do określonego interwału. Jeśli interwał czasu musi upłynąć bez osiągnięcia terminali stanu, stanu akcji jest oznaczony jako `Cancelled` z `ActionTimedOut` kodu. Limit czasu został określony w formacie ISO 8601. Ten przykład przedstawia, jak można określić ograniczenia:


``` json
"<action-name>": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>Akcja APIConnection

Ta akcja odwołuje się do łącznika zarządzany przez firmę Microsoft, wymagających odwołania do prawidłowego połączenia i informacji na temat interfejsu API i parametry. Oto przykład APIConnection akcji:

```json
"Send_Email": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "POST",
        "body": {
            "Subject": "New tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
}
```

| Nazwa elementu | Wymagane | Typ | Opis | 
| ------------ | -------- | ---- | ----------- | 
| host | Yes | Obiekt JSON | Reprezentuje informacje łącznika, takich jak `runtimeUrl` i odwołania do obiektu połączenia. | 
| method | Yes | Ciąg | Użyto jednego z tych metod HTTP: "GET", "POST", "PUT", "DELETE", "Poprawka" ani "HEAD" | 
| ścieżka | Yes | Ciąg | Ścieżka dla operacji interfejsu API | 
| — zapytania | Nie | Obiekt JSON | Reprezentuje wszystkie parametry zapytania, które chcesz uwzględnić w adresie URL. <p>Na przykład `"queries": { "api-version": "2015-02-01" }` dodaje `?api-version=2015-02-01` do adresu URL. | 
| nagłówki | Nie | Obiekt JSON | Reprezentuje każdy nagłówek, który jest wysyłany w żądaniu. <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| treść | Nie | Obiekt JSON | Reprezentuje ładunek, które są wysyłane do punktu końcowego. | 
| retryPolicy | Nie | Obiekt JSON | Ten obiekt jest używany dla Dostosowywanie zachowania ponownych prób dla 4xx lub 5xx błędów. Aby uzyskać więcej informacji, zobacz [ponów zasady](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nie | Ciąg | Definiuje zestaw specjalnego zachowania do zastąpienia. | 
| uwierzytelnianie | Nie | Obiekt JSON | Reprezentuje metodę, która powinna być używana do uwierzytelniania żądania. Aby uzyskać więcej informacji, zobacz [uwierzytelniania połączeń wychodzących harmonogramu](../scheduler/scheduler-outbound-authentication.md). |
||||| 

Zasady ponawiania dotyczy sporadycznych błędów jest określony jako kodów stanu HTTP 408 429 i 5xx, oprócz wszelkie wyjątki łączności. Można zdefiniować te zasady z `retryPolicy` obiektów, jak pokazano poniżej:

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

## <a name="apiconnection-webhook-action"></a>APIConnection akcji elementu webhook

Akcja APIConnectionWebhook odwołuje się do łącznika zarządzany przez firmę Microsoft. Ta akcja wymaga odwołania do prawidłowego połączenia i informacji na temat interfejsu API i parametry. Można określić limity akcji elementu webhook w taki sam sposób jak [limity asynchroniczne HTTP](#asynchronous-limits).

```json
"Send_approval_email": {
    "type": "ApiConnectionWebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

| Nazwa elementu | Wymagane | Typ | Opis | 
| ------------ | -------- | ---- | ----------- | 
| host | Yes | Obiekt JSON | Reprezentuje informacje łącznika, takich jak `runtimeUrl` i odwołania do obiektu połączenia. | 
| ścieżka | Yes | Ciąg | Ścieżka dla operacji interfejsu API | 
| — zapytania | Nie | Obiekt JSON | Reprezentuje wszystkie parametry zapytania, które chcesz uwzględnić w adresie URL. <p>Na przykład `"queries": { "api-version": "2015-02-01" }` dodaje `?api-version=2015-02-01` do adresu URL. | 
| nagłówki | Nie | Obiekt JSON | Reprezentuje każdy nagłówek, który jest wysyłany w żądaniu. <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| treść | Nie | Obiekt JSON | Reprezentuje ładunek, które są wysyłane do punktu końcowego. | 
| retryPolicy | Nie | Obiekt JSON | Ten obiekt jest używany dla Dostosowywanie zachowania ponownych prób dla 4xx lub 5xx błędów. Aby uzyskać więcej informacji, zobacz [ponów zasady](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nie | Ciąg | Definiuje zestaw specjalnego zachowania do zastąpienia. | 
| uwierzytelnianie | Nie | Obiekt JSON | Reprezentuje metodę, która powinna być używana do uwierzytelniania żądania. Aby uzyskać więcej informacji, zobacz [uwierzytelniania połączeń wychodzących harmonogramu](../scheduler/scheduler-outbound-authentication.md). |
||||| 

## <a name="response-action"></a>Akcja odpowiedzi  

Ta akcja zawiera ładunek całej odpowiedzi z żądania HTTP i `statusCode`, `body`, i `headers`:
  
```json
"myResponseAction": {
    "type": "Response",
    "inputs": {
        "statusCode": 200,
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        }
    },
    "runAfter": {}
}
```

Akcja odpowiedź ma specjalne ograniczenia, które nie dotyczą innych działań, w szczególności:  
  
* Akcje reakcji nie może mieć w równoległych gałęziach w ramach definicji aplikacji logiki, ponieważ przychodzące żądanie wymaga deterministyczne odpowiedzi.
  
* Jeśli przepływ pracy osiągnie akcji odpowiedzi po żądania przychodzącego, jeśli już odebrał odpowiedź, akcji odpowiedzi uważa się nie powiodło się lub powoduje konflikt. W związku z tym jest oznaczony jako Uruchom aplikację logiki `Failed`.
  
* Nie można użyć przepływu pracy z akcjami odpowiedzi `splitOn` polecenie w definicji wyzwalacza, ponieważ wywołanie tworzy wiele przebiegów. W związku z tym sprawdzanie dla tego przypadku, gdy operacji przepływu pracy jest WŁĄCZONY i zwrócenie odpowiedzi "złe żądanie".

## <a name="compose-action"></a>Redagowanie akcji

Ta akcja umożliwia utworzenia dowolnego obiektu, a dane wyjściowe są wyniku obliczenia akcji danych wejściowych. 

> [!NOTE]
> Można użyć `Compose` akcji dla tworzenia żadnych danych wyjściowych, w tym obiektów, tablic i innego typu obsługiwane przez logikę aplikacji, takich jak XML i pliku binarnego.

Na przykład można użyć `Compose` akcji scalania danych wyjściowych z wielu działań:

```json
"composeUserRecordAction": {
    "type": "Compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
    }
}
```

## <a name="function-action"></a>Akcja — funkcja

Ta akcja umożliwia reprezentują i wywołanie [funkcji Azure](../azure-functions/functions-overview.md), na przykład:

```json
"<my-Azure-Function-name>": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/sites/<your-Azure-function-app-name>/functions/<your-Azure-function-name>"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()"
        },
        "method": "POST",
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| Nazwa elementu | Wymagane | Typ | Opis | 
| ------------ | -------- | ---- | ----------- |  
| id — funkcja | Yes | Ciąg | Identyfikator zasobu dla funkcji platformy Azure, która ma zostać wywołana. | 
| method | Nie | Ciąg | Metoda HTTP używana do wywołania tej funkcji. Jeśli nie zostanie określony, "POST" jest domyślną metodą. | 
| — zapytania | Nie | Obiekt JSON | Reprezentuje wszystkie parametry zapytania, które chcesz uwzględnić w adresie URL. <p>Na przykład `"queries": { "api-version": "2015-02-01" }` dodaje `?api-version=2015-02-01` do adresu URL. | 
| nagłówki | Nie | Obiekt JSON | Reprezentuje każdy nagłówek, który jest wysyłany w żądaniu. <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| treść | Nie | Obiekt JSON | Reprezentuje ładunek, które są wysyłane do punktu końcowego. | 
|||||

Podczas zapisywania aplikacji logiki aparatu Logic Apps sprawdza niektórych funkcji, do którego istnieje odwołanie:

* Musi mieć dostęp do funkcji.
* Można użyć tylko standardowe wyzwalacza HTTP lub ogólny element Webhook JSON wyzwalacza.
* Funkcja nie powinny mieć żadnej trasy zdefiniowane.
* Są dozwolone tylko "funkcji" i "anonymous" autoryzacji poziomów.

> [!NOTE]
> Aparat Logic Apps pobiera i buforuje adresu URL wyzwalacz, który jest używany w czasie wykonywania. Dlatego jeśli żadnej operacji unieważnia pamięci podręcznej adres URL, akcja zakończy się niepowodzeniem w czasie wykonywania. Aby obejść ten problem, należy zapisać aplikację logiki, co powoduje, że aplikację logiki, aby pobrać i ponownie pamięci podręcznej adres URL wyzwalacza.

## <a name="select-action"></a>Wybierz akcję

Ta akcja umożliwia projektów każdy element tablicy na nową wartość. W tym przykładzie konwertuje tablicę liczb znajdujących się na tablicę obiektów:

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| Name (Nazwa) | Wymagane | Typ | Opis | 
| ---- | -------- | ---- | ----------- | 
| z | Yes | Tablica | Tablica źródłowa |
| wybierz | Yes | Dowolne | Projekcja zastosowane do każdego elementu w tablicy źródłowej |
||||| 

Dane wyjściowe z `select` akcji jest tablicą, który ma tego samego Kardynalność jako tablica wejściowa. Każdy element jest przekształcana zgodnie z definicją w `select` właściwości. Jeśli dane wejściowe jest pusta tablica, dane wyjściowe jest również być pustą tablicą.

## <a name="terminate-action"></a>Zakończenie akcji

Ta akcja powoduje zatrzymanie przebieg przepływu pracy, anulowanie wszystkie akcje w toku i pomija wszystkie pozostałe akcje. Zakończenie akcji nie ma wpływu na już ukończonego działania.

Na przykład, aby zatrzymać do uruchomienia, która ma `Failed` stanu:

```json
"HandleUnexpectedResponse": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response",
        }
    }
}
```

| Name (Nazwa) | Wymagane | Typ | Opis | 
| ---- | -------- | ---- | ----------- | 
| runStatus | Yes | Ciąg | Element docelowy, uruchom jego stan, który może być `Failed` lub `Cancelled` |
| runError | Nie | Obiekt JSON | Szczegóły błędu. Obsługiwane tylko wtedy, gdy `runStatus` ma ustawioną wartość `Failed`. |
| Kod runError | Nie | Ciąg | Kod błędu przy uruchomieniu |
| komunikat runError | Nie | Ciąg | Komunikat o błędzie przy uruchomieniu | 
||||| 

## <a name="query-action"></a>Akcja kwerendy

Ta akcja umożliwia filtrowanie na podstawie warunku tablicy. 

> [!NOTE]
> Nie można użyć akcji tworzenia do utworzenia żadnych danych wyjściowych, w tym obiektów, tablic i innego typu obsługiwane przez logikę aplikacji, takich jak XML i pliku binarnego.

Na przykład, aby wybrać więcej niż dwa cyfry:

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

| Name (Nazwa) | Wymagane | Typ | Opis | 
| ---- | -------- | ---- | ----------- | 
| z | Yes | Tablica | Tablica źródłowa |
| gdzie | Yes | Ciąg | Warunek, który jest stosowany do każdego elementu z tablicy źródłowej. Jeśli spełniać żadnych wartości `where` warunek, wynik ma być pustą tablicą. |
||||| 

Dane wyjściowe z `query` akcji jest tablicę, która ma elementy z tablicy wejściowej, które spełniają warunek.

## <a name="table-action"></a>Akcja tabeli

Ta akcja umożliwia konwertowanie tablicy do tabeli CSV lub HTML. 

```json
"ConvertToTable": {
    "type": "Table",
    "inputs": {
        "from": "<source-array>",
        "format": "CSV | HTML"
    }
}
```

| Name (Nazwa) | Wymagane | Typ | Opis | 
| ---- | -------- | ---- | ----------- | 
| z | Yes | Tablica | Tablica źródłowa. Jeśli `from` wartość właściwości jest pusta tablica, dane wyjściowe jest pusta tabela. | 
| Format | Yes | Ciąg | Tabela żądany format, "CSV" lub "HTML" | 
| kolumny | Nie | Tablica | Kolumny tabeli, które mają. Umożliwia zastąpienie domyślnego kształtu tabeli. | 
| Nagłówek kolumny | Nie | Ciąg | Nagłówek kolumny | 
| wartość w kolumnie | Yes | Ciąg | Wartość kolumny | 
||||| 

Załóżmy, że zdefiniujesz akcji tabeli, tak jak ten przykład:

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "HTML"
    }
}
```

I używać tej tablicy dla `@triggerBody()`:

```json
[ {"ID": 0, "Name": "apples"},{"ID": 1, "Name": "oranges"} ]
```

Poniżej przedstawiono dane wyjściowe w tym przykładzie:

<table><thead><tr><th>ID</th><th>Name (Nazwa)</th></tr></thead><tbody><tr><td>0</td><td>jabłek</td></tr><tr><td>1</td><td>Pomarańcze</td></tr></tbody></table>

Aby dostosować tej tabeli, można określić kolumn jawnie, na przykład:

```json
"ConvertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [ 
            {
                "header": "Produce ID",
                "value": "@item().id"
            },
            {
              "header": "Description",
              "value": "@concat('fresh ', item().name)"
            }
        ]
    }
}
```

Poniżej przedstawiono dane wyjściowe w tym przykładzie:

<table><thead><tr><th>Tworzy identyfikator</th><th>Opis</th></tr></thead><tbody><tr><td>0</td><td>Nowa jabłek</td></tr><tr><td>1</td><td>świeżych pomarańczy</td></tr></tbody></table>

## <a name="wait-action"></a>Poczekaj akcji  

Ta akcja wstrzymuje wykonywanie przepływu pracy dla określonego interwału. W tym przykładzie powoduje uruchomienie przepływu pracy poczekaj 15 minut:
  
```json
"waitForFifteenMinutesAction": {
    "type": "Wait",
    "inputs": {
        "interval": {
            "unit": "minute",
            "count": 15
        }
    }
}
```
  
Można również czekać, aż do określonego momentu w czasie, można użyć w tym przykładzie:
  
```json
"waitUntilOctoberAction": {
    "type": "Wait",
    "inputs": {
        "until": {
            "timestamp": "2017-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> Można określić czas oczekiwania w trybie `interval` obiektu lub `until` obiektu, ale nie oba.

| Nazwa elementu | Wymagane | Typ | Opis | 
| ------------ | -------- | ---- | ----------- | 
| do | Nie | Obiekt JSON | Czas trwania oczekiwania na podstawie punktu w czasie | 
| do sygnatury czasowej | Yes | Ciąg | Do punktu w czasie w [format daty i godziny UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) wygaśnięcia czas oczekiwania | 
| interval | Nie | Obiekt JSON | Czas oczekiwania na podstawie jednostkę interwału i liczby | 
| Interwał | Yes | Ciąg | Jednostka czasu. Użyj tylko jedną z następujących wartości: "drugi", "min", "Godzina", "day", "tygodnia" lub "miesiąc" | 
| Liczba interwale | Yes | Liczba całkowita | Dodatnią liczbę całkowitą reprezentującą liczbę jednostek interwału czasu trwania oczekiwania | 
||||| 

## <a name="workflow-action"></a>Działania przepływu pracy

Ta akcja umożliwia zagnieździć przepływ pracy. Aparat Logic Apps wykonuje kontrolę dostępu na podrzędny przepływ pracy, w szczególności wyzwalacza, więc musi mieć dostęp do podrzędny przepływ pracy. Na przykład:

```json
"<my-nested-workflow-action-name>": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/<my-subscription-ID>/resourceGroups/<my-resource-group-name>/providers/Microsoft.Logic/<my-nested-workflow-action-name>",
            "triggerName": "mytrigger001"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| Nazwa elementu | Wymagane | Typ | Opis | 
| ------------ | -------- | ---- | ----------- |  
| Identyfikator hosta | Yes | Ciąg| Identyfikator zasobu dla przepływu pracy, który ma zostać wywołana | 
| Nazwa_wyzwalacza hosta | Yes | Ciąg | Nazwa wyzwalacza, który chcesz wywołać | 
| — zapytania | Nie | Obiekt JSON | Reprezentuje wszystkie parametry zapytania, które chcesz uwzględnić w adresie URL. <p>Na przykład `"queries": { "api-version": "2015-02-01" }` dodaje `?api-version=2015-02-01` do adresu URL. | 
| nagłówki | Nie | Obiekt JSON | Reprezentuje każdy nagłówek, który jest wysyłany w żądaniu. <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| treść | Nie | Obiekt JSON | Reprezentuje ładunek, które są wysyłane do punktu końcowego. | 
||||| 

Ta akcja dane wyjściowe są oparte na definiowanie w `Response` akcji podrzędnych przepływu pracy. Jeśli przepływ pracy podrzędny nie definiuje `Response` akcji, dane wyjściowe są puste.

## <a name="collection-actions-overview"></a>Przegląd działań kolekcji

Aby kontrolować wykonywanie przepływu pracy, akcje kolekcji może zawierać innych działań. Może bezpośrednio odwoływać się odwołanie do działań w kolekcji poza kolekcji. Na przykład w przypadku definiowania `Http` akcji w zakresie, które są następnie `@body('http')` jest nadal ważny w dowolnym miejscu w przepływie pracy. Ponadto akcje w kolekcji "wykonać tylko" inne akcje w tej samej kolekcji.

## <a name="if-action"></a>Jeśli akcja

Tę akcję, która jest instrukcji warunkowej, pozwala ocenić warunku i wykonywanie gałęzi według tego, czy wyrażenie jako true. Jeśli wyrażenie pomyślnie jako wartość true, ten stan jest oznaczony stan "Powodzenie". Akcje, które znajdują się w `actions` lub `else` obiektów oceny tych wartości:

* "Powiodło się" podczas uruchamiania i powiodło się
* "Nie powiodło się" podczas uruchamiania i zakończyć się niepowodzeniem
* "Skipped", gdy odpowiednich gałęzi nie działa

Dowiedz się więcej o [warunkowe instrukcje w aplikacjach logiki](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"<my-condition-name>": {
  "type": "If",
  "expression": "<condition>",
  "actions": {
    "if-true-run-this-action": {
      "type": <action-type>,
      "inputs": {},
      "runAfter": {}
    }
  },
  "else": {
    "actions": {
        "if-false-run-this-action": {
            "type": <action-type>,
            "inputs": {},
            "runAfter": {}
        }
    }
  },
  "runAfter": {}
}
```

| Name (Nazwa) | Wymagane | Typ | Opis | 
| ---- | -------- | ---- | ----------- | 
| Akcje | Yes | Obiekt JSON | Wewnętrzny akcje do wykonania, kiedy `expression` daje w wyniku `true` | 
| wyrażenie | Yes | Ciąg | Wyrażenie do oceny |
| else | Nie | Obiekt JSON | Wewnętrzny akcje do wykonania, kiedy `expression` daje w wyniku `false` |
||||| 

Na przykład:

```json
"myCondition": {
    "type": "If",
    "actions": {
        "if-true-check-this-website": {
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
            "if-false-check-this-other-website": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://this-other-url"
                },
                "runAfter": {}
            }
        }
    }
}
```  

### <a name="how-conditions-can-use-expressions-in-actions"></a>Warunki użycia wyrażeń w akcji

Oto kilka przykładów, które pokazują, jak można użyć wyrażenia w warunkach:
  
| Wyrażenie JSON | Wynik | 
| --------------- | ------ | 
| `"expression": "@parameters('hasSpecialAction')"` | Wartość, która daje w wyniku wartość true spowoduje, że ten stan do przekazania. Obsługuje tylko wyrażeń logicznych. Aby przekonwertować inne typy Boolean, korzystania z tych funkcji: `empty` lub `equals` | 
| `"expression": "@greater(actions('action1').output.value, parameters('threshold'))"` | Obsługuje funkcje porównania. Na przykład akcja działa tylko wtedy, gdy dane wyjściowe action1 jest większa niż wartość progowa. | 
| `"expression": "@or(greater(actions('action1').output.value, parameters('threshold')), less(actions('action1').output.value, 100))"` | Obsługuje funkcje logiki tworzenia zagnieżdżonych wyrażeń logicznych. W tym przykładzie akcji jest uruchamiane przy dane wyjściowe action1 więcej niż próg lub w 100. | 
| `"expression": "@equals(length(actions('action1').outputs.errors), 0))"` | Aby sprawdzić, czy tablica zawiera wszystkie elementy, możesz użyć funkcji tablicy. W tym przykładzie akcji jest uruchamiana po błędy tablica jest pusta. | 
| `"expression": "parameters('hasSpecialAction')"` | To wyrażenie powoduje błąd i nie jest prawidłowy warunek. Warunki należy użyć "@" symbolu. | 
||| 

## <a name="switch-action"></a>Akcja przełącznika

Tę akcję, która jest instrukcji switch, wykonuje różne akcje na podstawie określonej wartości obiektu, wyrażenie lub token. Ta akcja ocenia obiektu, wyrażenie lub token, wybierze przypadku dopasowuje wynik i działa tylko w takim przypadku akcje. Gdy nie jest zgodna z wyniku, domyślna akcja działa. Po uruchomieniu instrukcji switch tylko jeden przypadek powinien być zgodny z wynikiem. Dowiedz się więcej o [przełącznika instrukcje w aplikacjach logiki](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"<my-switch-statement-name>": {
   "type": "Switch",
   "expression": "<evaluate-this-object-expression-token>",
   "cases": {
      "myCase1": {
         "actions": {
           "myAction1": {}
         },
         "case": "<result1>"
      },
      "myCase2": {
         "actions": {
           "myAction2": {}
         },
         "case": "<result2>"
      }
   },
   "default": {
      "actions": {
          "myDefaultAction": {}
      }
   },
   "runAfter": {}
}
```

| Name (Nazwa) | Wymagane | Typ | Opis | 
| ---- | -------- | ---- | ----------- | 
| wyrażenie | Yes | Ciąg | Obiekt, wyrażenie lub token do oceny | 
| Przypadków | Yes | Obiekt JSON | Zawiera zestawy wewnętrzny akcje, które są uruchamiane na podstawie wyniku wyrażenia. | 
| Case | Yes | Ciąg | Wartość do dopasowania z wynikami | 
| Akcje | Yes | Obiekt JSON | Wewnętrzny akcje, które są uruchamiane w przypadku pasującego do wyniku wyrażenia | 
| domyślnie | Nie | Obiekt JSON | Wewnętrzny akcje uruchamiane po przypadkach nie pasował do wyniku | 
||||| 

Na przykład:

``` json
"myApprovalEmailAction": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {...}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {...}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

## <a name="foreach-action"></a>Akcja foreach

Ta akcja pętli iteruje tablicy i wykonuje akcje wewnętrzne na każdy element tablicy. Domyślnie pętli Foreach uruchamia się równolegle. Na maksymalną liczbę równoległych cykli, który "for each" pętle można uruchomić, zobacz [limity i konfiguracji](../logic-apps/logic-apps-limits-and-config.md). Aby cyklu są wykonywane sekwencyjnie, ustaw `operationOptions` parametr `Sequential`. Dowiedz się więcej o [pętli Foreach w aplikacjach logiki](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"<my-forEach-loop-name>": {
    "type": "Foreach",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    },
    "foreach": "<array>",
    "runAfter": {}
}
```

| Name (Nazwa) | Wymagane | Typ | Opis | 
| ---- | -------- | ---- | ----------- | 
| Akcje | Yes | Obiekt JSON | Wewnętrzny akcje do wykonania w pętli | 
| Instrukcja foreach | Yes | Ciąg | Tablica do iteracji | 
| operationOptions | Nie | Ciąg | Określa opcje operacji dostosowywania zachowanie. Aktualnie obsługuje tylko `Sequential` sekwencyjnie uruchamiania iteracji domyślne zachowanie w przypadku równoległego. |
||||| 

Na przykład:

```json
"forEach_EmailAction": {
    "type": "Foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "Send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "foreach": "@body('email_filter')",
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Do działania

Ta akcja pętli uruchamia wewnętrzny akcje, dopóki wynikiem warunku jest PRAWDA. Dowiedz się więcej o ["do" pętli w aplikacjach logiki](../logic-apps/logic-apps-control-flow-loops.md#until-loop).

```json
 "<my-Until-loop-name>": {
    "type": "Until",
    "actions": {
        "myActionName": {
            "type": "<action-type>",
            "inputs": {},
            "runAfter": {}
        }
    },
    "expression": "<myCondition>",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| Name (Nazwa) | Wymagane | Typ | Opis | 
| ---- | -------- | ---- | ----------- | 
| Akcje | Yes | Obiekt JSON | Wewnętrzny akcje do wykonania w pętli | 
| wyrażenie | Yes | Ciąg | Wyrażenie do oceny po każdej iteracji | 
| Limit | Yes | Obiekt JSON | Limity dla pętli. Należy zdefiniować co najmniej jeden limit. | 
| liczba | Nie | Liczba całkowita | Limit liczby iteracji do wykonania | 
| timeout | Nie | Ciąg | Limit czasu w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) Określa, jak długo uruchamiać pętli |
||||| 

Na przykład:

```json
 "runUntilSucceededAction": {
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
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 100,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

## <a name="scope-action"></a>Akcja zakresu

Ta akcja umożliwia w logiczne grupy działań w przepływie pracy. Zakres również pobiera własną stan po wszystkich akcji w tym Zakończ zakres uruchomiona. Dowiedz się więcej o [zakresy](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"<my-scope-action-name>": {
    "type": "Scope",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    }
}
```

| Name (Nazwa) | Wymagane | Typ | Opis | 
| ---- | -------- | ---- | ----------- |  
| Akcje | Yes | Obiekt JSON | Wewnętrzny akcje do wykonania w zakresie |
||||| 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md)
* Dowiedz się więcej o [interfejsu API REST przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows)