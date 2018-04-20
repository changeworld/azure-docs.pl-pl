---
title: Przepływ pracy wyzwalacze i akcje - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o wyzwalacze i akcje tworzenia automatycznych przepływów pracy i procesy z usługą logic apps
services: logic-apps
author: divyaswarnkar
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/13/2017
ms.author: klam; LADocs
ms.openlocfilehash: 28d28888ce66c354da39dc636579655aadbb9e51
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="triggers-and-actions-for-logic-app-workflows"></a>Wyzwalacze i akcje dla przepływów pracy aplikacji logiki

Wszystkie aplikacje logiki rozpoczynać wyzwalacz następuje akcje. W tym artykule opisano rodzaje wyzwalacze i akcje, które służy do tworzenia integracji systemu i automatyzacja przepływów pracy firmowych lub procesy, tworzenie aplikacji logiki. 
  
## <a name="triggers-overview"></a>Omówienie wyzwalaczy 

Wszystkie aplikacje logiki rozpoczynać wyzwalacz, który określa wywołania, które można uruchomić aplikację logiki, uruchom. Poniżej przedstawiono typy wyzwalaczy, które są dostępne:

* A *sondowania* wyzwalacz, który sprawdza punktu końcowego HTTP usługi w regularnych odstępach czasu
* A *wypychania* wyzwolić, które wywołuje [interfejsu API REST usługi przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows)
  
Wszystkich wyzwalaczy zawierać tych elementów najwyższego poziomu:  
  
```json
"<myTriggerName>": {
    "type": "<triggerType>",
    "inputs": { <callSettings> },
    "recurrence": {  
        "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
        "interval": "<recurrence-interval-based-on-frequency>"
    },
    "conditions": [ <array-with-required-conditions> ],
    "splitOn": "<property-used-for-creating-runs>",
    "operationOptions": "<options-for-operations-on-the-trigger>"
}
```

## <a name="trigger-types-and-inputs"></a>Typy wyzwalaczy i dane wejściowe  

Każdy typ wyzwalacza ma innego interfejsu i inne *dane wejściowe* definiuje zachowanie. 

| Typ wyzwalacza | Opis | 
| ------------ | ----------- | 
| **Cyklu** | Generowane na podstawie zdefiniowanego harmonogramu. Można ustawić przyszłą datę i godzinę dla wyzwalania tego wyzwalacza. Na podstawie częstotliwości, można również określić godziny i dni na potrzeby uruchamiania przepływu pracy. | 
| **Żądanie**  | Umożliwia aplikacji logiki do punktu końcowego, który można wywołać, znanej także jako wyzwalacz "manual". | 
| **HTTP** | Sprawdza, lub *sond*, punkt końcowy HTTP sieci web. Punkt końcowy HTTP musi być zgodny z określonym kontraktem wyzwalająca za pomocą "202" wzorca asynchronicznego lub tablicę. | 
| **ApiConnection** | Sonduje jak wyzwalacz protokołu HTTP, ale używa [zarządzany przez firmę Microsoft interfejsy API](../connectors/apis-list.md). | 
| **HTTPWebhook** | Umożliwia aplikacji logiki do punktu końcowego można wywołać, takich jak **żądania** wyzwalania, ale odwołuje się określony adres URL do rejestrowania i wyrejestrowania. |
| **ApiConnectionWebhook** | Jak działa **HTTPWebhook** wyzwalacz, ale używa API zarządzany przez firmę Microsoft. | 
||| 

Aby uzyskać więcej informacji, zobacz [język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md). 

<a name="recurrence-trigger"></a>

## <a name="recurrence-trigger"></a>Wyzwalacz cyklu  

Wyzwalacz uruchamia na podstawie cyklu i harmonogram, które określisz i zapewnia prosty sposób regularnie uruchamiania przepływu pracy. 

Oto przykład wyzwalacza cyklu podstawowych, w którym jest przeprowadzana codziennie:

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

Można również zaplanować datę i godzinę dla wyzwalania wyzwalacza. Na przykład aby uruchomić raport co tydzień w każdy poniedziałek, można zaplanować aplikację logiki, aby uruchomić na komputerze określonym poniedziałek, tak jak ten przykład: 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime": "2017-09-18T00:00:00Z"
    }
}
```

Poniżej przedstawiono definicję dla tego wyzwalacza:

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "second|minute|hour|day|week|month",
        "interval": <recurrence-interval-based-on-frequency>,
        "schedule": {
            // Applies only when frequency is Day or Week. Separate values with commas.
            "hours": [ <one-or-more-hour-marks> ], 
            // Applies only when frequency is Day or Week. Separate values with commas.
            "minutes": [ <one-or-more-minute-marks> ], 
            // Applies only when frequency is Week. Separate values with commas.
            "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
        },
        "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
        "timeZone": "<specify-time-zone>"
    }
}
```

| Nazwa elementu | Wymagane | Typ | Opis | 
| ------------ | -------- | ---- | ----------- | 
| frequency | Yes | Ciąg | Jednostka czasu częstotliwość wyzwalacza. Użyj tylko jedną z następujących wartości: "drugi", "min", "Godzina", "day", "tygodnia" lub "miesiąc" | 
| interval | Yes | Liczba całkowita | Dodatnia liczba całkowita, w tym artykule opisano, jak często uruchamia przepływ pracy na podstawie częstotliwości. <p>Poniżej przedstawiono minimalne i maksymalne odstępach czasu: <p>-Miesięczny: 1-16 miesięcy </br>-Dniowego: 1-500 dni </br>-Godzinnym: 1-12 000 godzin </br>-Minutowy: 1-72,000 minut </br>-Drugi: 1-9,999,999 sekund<p>Na przykład jeśli interwał to 6 i częstotliwości jest "miesiąc", cykl jest co 6 miesięcy. | 
| timeZone | Nie | Ciąg | Dotyczy tylko po określeniu godziny rozpoczęcia, ponieważ wyzwalacz nie akceptuje [przesunięcie UTC](https://en.wikipedia.org/wiki/UTC_offset). Określ strefę czasową, który chcesz zastosować. | 
| startTime | Nie | Ciąg | Określ datę i godzinę w następującym formacie: <p>RRRR-MM-Ddtgg, jeśli określona strefa czasowa <p>— lub — <p>RRRR-MM-Ddtgg, jeśli nie określisz strefy czasowej <p>Tak na przykład, jeśli chcesz 18 września 2017 godzinie 2:00, określ "2017-09-18T14:00:00" i określ strefę czasową, takie jak "Czas pacyficzny". Alternatywnie można wskazać "2017-09-18T14:00:00Z" bez strefę czasową. <p>**Uwaga:** ta godzina rozpoczęcia musi występować po [ISO 8601 daty czasu specyfikacji](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [format daty i godziny UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [przesunięcie UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie określisz strefy czasowej, należy dodać litery "Z" na końcu bez spacji. To "Z" odwołuje się do jego odpowiednik [milową czas](https://en.wikipedia.org/wiki/Nautical_time). <p>Proste harmonogramów, godzina rozpoczęcia jest pierwsze wystąpienie, natomiast w przypadku złożonych harmonogramy wyzwalacz nie wyzwalać żadnych wcześniej niż czas rozpoczęcia. Aby uzyskać więcej informacji na temat daty rozpoczęcia i godziny, zobacz [tworzenie i harmonogram regularnie uruchomionych zadań](../connectors/connectors-native-recurrence.md). | 
| weekDays | Nie | Ciąg lub tablicę ciągów | Jeśli określisz "Tydzień" dla `frequency`, można określić co najmniej jeden dzień, oddzielając je średnikami, jeśli chcesz uruchomić przepływ pracy: "Poniedziałek", "Wtorek", "Środa", "Czwartek", "Piątek", "Sobota" i "Niedziela" | 
| hours | Nie | Liczba całkowita lub tablicy liczba całkowita | Jeśli określisz "Day" lub "Tydzień" dla `frequency`, można określić co najmniej jeden liczby całkowite z przedziału od 0 do 23 rozdzielonych przecinkami, w formacie godziny, dnia, gdy chcesz uruchomić przepływ pracy. <p>Na przykład jeśli określisz "10", "12" i "14" możesz uzyskać 10 AM, 12 PM i 14: 00 jako znaki godzinę. | 
| minutes | Nie | Liczba całkowita lub tablicy liczba całkowita | Jeśli określisz "Dzień" lub "Tydzień" dla `frequency`, można określić co najmniej jeden liczby całkowite z przedziału od 0 do 59, oddzielonych przecinkami, jako minuty, godziny, jeśli chcesz uruchomić przepływ pracy. <p>Na przykład "30" można określić jako minuta znaku i w poprzednim przykładzie, godziny, dnia, możesz uzyskać godziny 10:30, 12:30:00 i 14:30:00. | 
||||| 

Na przykład tego wyzwalacza cyklu określa, że aplikację logiki jest uruchamiane co tydzień w każdy poniedziałek w godziny 10:30, 12:30:00 i 14:30:00 i Pacyfik (czas standardowy), nie wcześniej niż 9 września 2017, 2:00 PM uruchamianie:

``` json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": 1,
        "schedule": {
            "hours": [
                10,
                12,
                14
            ],
            "minutes": [
                30
            ],
            "weekDays": [
                "Monday"
            ]
        },
       "startTime": "2017-09-07T14:00:00",
       "timeZone": "Pacific Standard Time"
    }
}
```

Aby uzyskać więcej informacji o cyklu i przykłady czasu rozpoczęcia dla tego wyzwalacza, zobacz [tworzenie i harmonogram regularnie uruchomionych zadań](../connectors/connectors-native-recurrence.md).

## <a name="request-trigger"></a>Wyzwalacz żądania

Wyzwalacz służy jako punkt końcowy, który służy do wywoływania aplikację logiki, za pomocą żądania HTTP. Wyzwalacz żądania wygląda następująco:  
  
```json
"myRequestTrigger": {
    "type": "Request",
    "kind": "Http",
    "inputs": {
        "schema": {
            "type": "Object",
            "properties": {
                "myInputProperty1": { "type" : "string" },
                "myInputProperty2": { "type" : "number" }
            },
            "required": [ "myInputProperty1" ]
        }
    }
} 
```

Wyzwalacz ma opcjonalna właściwość o nazwie `schema`:
  
| Nazwa elementu | Wymagane | Typ | Opis |
| ------------ | -------- | ---- | ----------- |
| Schemat | Nie | Obiekt | Schematu JSON, która weryfikuje żądanie przychodzące. Przydatne w przypadku myśl kolejnych kroków znać właściwości, które ma dotyczyć odwołanie. | 
||||| 

Aby wywołać tego wyzwalacza jako punktu końcowego, należy wywołać `listCallbackUrl` interfejsu API. Zobacz [interfejsu API REST usługi przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows).

## <a name="http-trigger"></a>Wyzwalacz HTTP  

Tego wyzwalacza określony punkt końcowy sonduje i sprawdza odpowiedzi, aby określić, czy przepływ pracy, należy uruchomić lub nie. W tym miejscu `inputs` obiektu przyjmuje tych parametrów wymaganych do konstruowania wywołanie HTTP: 

| Nazwa elementu | Wymagane | Typ | Opis | 
| ------------ | -------- | ---- | ----------- | 
| method | Yes | Ciąg | Użyto jednego z tych metod HTTP: "GET", "POST", "PUT", "DELETE", "Poprawka" ani "HEAD" | 
| identyfikator URI | Yes| Ciąg | Końcowy HTTP lub HTTPs, który sprawdza zgodność wyzwalacza. Maksymalny rozmiar ciągu: 2 KB | 
| — zapytania | Nie | Obiekt | Reprezentuje wszystkie parametry zapytania, które chcesz uwzględnić w adresie URL. <p>Na przykład `"queries": { "api-version": "2015-02-01" }` dodaje `?api-version=2015-02-01` do adresu URL. | 
| nagłówki | Nie | Obiekt | Reprezentuje każdy nagłówek, który jest wysyłany w żądaniu. <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| treść | Nie | Obiekt | Reprezentuje ładunek, które są wysyłane do punktu końcowego. | 
| retryPolicy | Nie | Obiekt | Ten obiekt jest używany dla Dostosowywanie zachowania ponownych prób dla 4xx lub 5xx błędów. Aby uzyskać więcej informacji, zobacz [ponów zasady](../logic-apps/logic-apps-exception-handling.md). | 
| uwierzytelnianie | Nie | Obiekt | Reprezentuje metodę, która powinna być używana do uwierzytelniania żądania. Aby uzyskać więcej informacji, zobacz [uwierzytelniania połączeń wychodzących harmonogramu](../scheduler/scheduler-outbound-authentication.md). <p>Poza harmonogramem, jest jedna właściwość więcej obsługiwanych: `authority`. Domyślnie ta wartość jest `https://login.windows.net` gdy nie jest określony, ale możesz użyć innej wartości, takich jak`https://login.windows\-ppe.net`. | 
||||| 

A *zasady ponawiania* dotyczy sporadycznych błędów jest określony jako kodów stanu HTTP 408 429 i 5xx, oprócz wszelkie wyjątki łączności. Można zdefiniować te zasady z `retryPolicy` obiektów, jak pokazano poniżej:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Aby pracować z aplikacji logiki, wyzwalacza HTTP wymaga API protokołu HTTP był zgodny z określonym wzorcem. Wyzwalacz rozpoznaje tych właściwości:  
  
| Odpowiedź | Wymagane | Opis | 
| -------- | -------- | ----------- |  
| Kod stanu | Yes | Kod stanu 200 ("OK") powoduje, że działa. Każdy kod stanu nie powoduje uruchomienia. | 
| Spróbuj ponownie po nagłówka | Nie | Liczba sekund do aplikacji logiki sonduje ponownie punkt końcowy. | 
| Nagłówek lokalizacji | Nie | Adres URL do wywołania w następnym interwału sondowania. Jeśli nie zostanie określony, używany jest oryginalny adres URL. | 
|||| 

Poniżej przedstawiono niektóre przykładowe zachowania dla różnych typów żądań:
  
| Kod odpowiedzi | Ponów próbę po | Zachowanie | 
| ------------- | ----------- | -------- | 
| 200 | {Brak} | Uruchamianie przepływu pracy, a następnie wyszukaj ponownie większej ilości danych po zdefiniowanych cyklu. | 
| 200 | 10 sekund | Uruchamianie przepływu pracy, a następnie wyszukaj ponownie większej ilości danych po 10 sekundach. |  
| 202 | 60 sekund | Nie wyzwolić przepływu pracy. Przy następnej próbie odbywa się na minutę, mogą ulec zdefiniowanych cyklu. Zdefiniowanych cyklu jest mniej niż minutę, pierwszeństwo ma nagłówek ponownych prób po. W przeciwnym razie jest używana zdefiniowanych cyklu. | 
| 400 | {Brak} | Niewłaściwe żądanie, nie uruchamiaj przepływ pracy. Jeśli nie `retryPolicy` jest zdefiniowany, zostanie użyta domyślna zasada. Po osiągnięciu liczby ponownych prób wyzwalacz sprawdza, czy ponownie danych po zdefiniowanych cyklu. | 
| 500 | {Brak}| Błąd serwera, nie uruchamiaj przepływ pracy. Jeśli nie `retryPolicy` jest zdefiniowany, zostanie użyta domyślna zasada. Po osiągnięciu liczby ponownych prób wyzwalacz sprawdza, czy ponownie danych po zdefiniowanych cyklu. | 
|||| 

Poniżej przedstawiono dane wyjściowe wyzwalacza HTTP: 
  
| Nazwa elementu | Typ | Opis |
| ------------ | ---- | ----------- |
| nagłówki | Obiekt | Nagłówki odpowiedzi HTTP | 
| treść | Obiekt | Treść odpowiedzi HTTP | 
|||| 

<a name="apiconnection-trigger"></a>

## <a name="apiconnection-trigger"></a>Wyzwalacz APIConnection  

Podstawowa funkcjonalność tego wyzwalacza działa jak wyzwalacza HTTP. Jednak parametry do identyfikacji akcji są różne. Oto przykład:   
  
```json
"myDailyReportTrigger": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            }
        },
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "myCategory"
    }
}
```

| Nazwa elementu | Wymagane | Typ | Opis | 
| ------------ | -------- | ---- | ----------- | 
| host | Yes | Obiekt | Hostowanej bramy i identyfikator dla aplikacji interfejsu API | 
| method | Yes | Ciąg | Użyto jednego z tych metod HTTP: "GET", "POST", "PUT", "DELETE", "Poprawka" ani "HEAD" | 
| — zapytania | Nie | Obiekt | Reprezentuje wszystkie parametry zapytania, które chcesz uwzględnić w adresie URL. <p>Na przykład `"queries": { "api-version": "2015-02-01" }` dodaje `?api-version=2015-02-01` do adresu URL. | 
| nagłówki | Nie | Obiekt | Reprezentuje każdy nagłówek, który jest wysyłany w żądaniu. <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| treść | Nie | Obiekt | Reprezentuje ładunek, które są wysyłane do punktu końcowego. | 
| retryPolicy | Nie | Obiekt | Ten obiekt jest używany dla Dostosowywanie zachowania ponownych prób dla 4xx lub 5xx błędów. Aby uzyskać więcej informacji, zobacz [ponów zasady](../logic-apps/logic-apps-exception-handling.md). | 
| uwierzytelnianie | Nie | Obiekt | Reprezentuje metodę, która powinna być używana do uwierzytelniania żądania. Aby uzyskać więcej informacji, zobacz [uwierzytelniania połączeń wychodzących harmonogramu](../scheduler/scheduler-outbound-authentication.md). | 
||||| 

Aby uzyskać `host` obiektu, w tym miejscu są właściwości:  
  
| Nazwa elementu | Wymagane | Opis | 
| ------------ | -------- | ----------- | 
| runtimeUrl interfejsu API | Yes | Punkt końcowy dla zarządzanego interfejsu API | 
| Nazwa połączenia |  | Nazwa zarządzanego połączenia interfejsu API, używanych przez przepływ pracy. Musi odwoływać się do parametru o nazwie `$connection`. |
|||| 

A *zasady ponawiania* dotyczy sporadycznych błędów jest określony jako kodów stanu HTTP 408 429 i 5xx, oprócz wszelkie wyjątki łączności. Można zdefiniować te zasady z `retryPolicy` obiektów, jak pokazano poniżej:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Poniżej przedstawiono dane wyjściowe dla wyzwalacza połączenia interfejsu API:
  
| Nazwa elementu | Typ | Opis |
| ------------ | ---- | ----------- |
| nagłówki | Obiekt | Nagłówki odpowiedzi HTTP | 
| treść | Obiekt | Treść odpowiedzi HTTP | 
|||| 

Dowiedz się więcej o [jak ceny działa połączenie z interfejsem API wyzwala](../logic-apps/logic-apps-pricing.md#triggers).

## <a name="httpwebhook-trigger"></a>Wyzwalacz HTTPWebhook  

Wyzwalacz zawiera punkt końcowy, podobnie jak `Request` wyzwalacz, ale wyzwalacza HTTPWebhook rejestrowanie i wyrejestrowywanie również wywołuje określony adres URL. Oto przykład co wyzwalacz HTTPWebhook może wyglądać tak:

```json
"myAppsSpotTrigger": {
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
            "authentication": {},
            "retryPolicy": {}
        },
        "unsubscribe": {
            "method": "POST",
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": {}
        }
    },
    "conditions": []
}
```

Wiele z tych sekcji są opcjonalne i zachowanie wyzwalacza HTTPWebhook zależy od sekcje, które zapewniają lub Pomiń. Poniżej przedstawiono właściwości wyzwalacza HTTPWebhook:
  
| Nazwa elementu | Wymagane | Opis | 
| ------------ | -------- | ----------- |  
| Subskrypcja | Nie | Określa żądania wychodzącego do wywołania po utworzeniu wyzwalacza i wykonuje początkowe rejestracyjny. | 
| Anulowanie subskrypcji | Nie | Określa żądania wychodzącego do wywołania po wyzwalacz jest usuwany. | 
|||| 

Można określić limity dla elementu trigger elementu webhook w taki sam sposób jak [limity asynchroniczne HTTP](#asynchronous-limits). Poniżej przedstawiono więcej informacji na temat `subscribe` i `unsubscribe` akcje:

* `subscribe` nazywa się tak, aby uruchomić wyzwalacz nasłuchuje zdarzeń. To wywołanie wychodzące rozpoczyna się od takich samych parametrach co standardowe akcji HTTP. To wywołanie odbywa się podczas przepływu pracy zmiany w dowolny sposób, na przykład jeśli poświadczenia zostały wycofane, lub zmień parametry wejściowe tego wyzwalacza. 
  
  Do obsługi tego wywołania `@listCallbackUrl()` funkcja zwraca unikatowy adres URL dla tego wyzwalacza określonych w przepływie pracy. Ten adres URL reprezentuje unikatowy identyfikator dla punktów końcowych, które używają interfejsu API REST usługi.
  
* `unsubscribe` jest automatycznie wywoływane, gdy operacja renderuje tego wyzwalacza nieprawidłowe, w tym te operacje:

  * Usuwanie lub wyłączanie wyzwalacza. 
  * Usuwanie lub wyłączanie przepływ pracy. 
  * Usuwanie lub wyłączanie subskrypcji. 
  
  Parametry dla tej funkcji są takie same jak wyzwalacza HTTP.

Poniżej przedstawiono dane wyjściowe z HTTPWebhook wyzwolenia i treść żądania przychodzące są:
  
| Nazwa elementu | Typ | Opis |
| ------------ | ---- | ----------- |
| nagłówki | Obiekt | Nagłówki odpowiedzi HTTP | 
| treść | Obiekt | Treść odpowiedzi HTTP | 
|||| 

## <a name="triggers-conditions"></a>Wyzwalacze: warunki

Dla dowolnego wyzwalacza można użyć co najmniej jednego warunku można określić, czy przepływ pracy, należy uruchomić lub nie. W tym przykładzie wyzwalaczy tylko raportu podczas przepływu pracy `sendReports` parametr ma wartość true. 

```json
"myDailyReportTrigger": {
    "type": "Recurrence",
    "conditions": [ 
        {
            "expression": "@parameters('sendReports')"
        } 
    ],
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

Na koniec warunki można odwoływać się do kod stanu wyzwalacza. Na przykład tylko wtedy, gdy witryny sieci Web zwraca kod stanu 500 można uruchomić przepływu pracy:
  
``` json
"conditions": [ 
    {  
      "expression": "@equals(triggers().code, 'InternalServerError')"  
    }  
]  
```  

> [!NOTE]
> Domyślnie, wyzwalacz uruchamia się tylko na odbieranie "200 OK" odpowiedzi. Gdy wyrażenie odwołuje się do tego wyzwalacza kodu stanu w dowolny sposób, zostanie zastąpiony wyzwalacza domyślne zachowanie. Tak aby wyzwalacz fire oparte na wiele kodów stanu, na przykład i kodem stanu 200 kod stanu 201, musi zawierać niniejszych jako warunek: 
>
> `@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="triggers-process-an-array-with-multiple-runs"></a>Wyzwalacze: Przetwarzanie tablicy zawierającej wiele przebiegów

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
        "interval": "1"
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
  
## <a name="triggers-fire-only-after-all-active-runs-finish"></a>Wyzwalacze: Fire, który działa tylko po wszystkich aktywnych Zakończ

Wyzwalacze cyklu można skonfigurować tak, aby ich uruchomienie tylko, gdy wszystkie aktywnej przebiegi została ukończona. Aby skonfigurować to ustawienie, ustaw `operationOptions` właściwości `singleInstance`:

```json
"myTrigger": {
    "type": "Http",
    "inputs": { },
    "recurrence": { },
    "operationOptions": "singleInstance"
}
```

W przypadku wystąpienia przepływu pracy jest uruchomiona zaplanowanego cyklu wyzwalacza pomija i czeka, aż do następnego interwał cyklu zaplanowane, aby ponownie sprawdzić.

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
| — zapytania | Nie | Obiekt | Reprezentuje wszystkie parametry zapytania, które chcesz uwzględnić w adresie URL. <p>Na przykład `"queries": { "api-version": "2015-02-01" }` dodaje `?api-version=2015-02-01` do adresu URL. | 
| nagłówki | Nie | Obiekt | Reprezentuje każdy nagłówek, który jest wysyłany w żądaniu. <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| treść | Nie | Obiekt | Reprezentuje ładunek, które są wysyłane do punktu końcowego. | 
| retryPolicy | Nie | Obiekt | Ten obiekt jest używany dla Dostosowywanie zachowania ponownych prób dla 4xx lub 5xx błędów. Aby uzyskać więcej informacji, zobacz [ponów zasady](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nie | Ciąg | Definiuje zestaw specjalnego zachowania do zastąpienia. | 
| uwierzytelnianie | Nie | Obiekt | Reprezentuje metodę, która powinna być używana do uwierzytelniania żądania. Aby uzyskać więcej informacji, zobacz [uwierzytelniania połączeń wychodzących harmonogramu](../scheduler/scheduler-outbound-authentication.md). <p>Poza harmonogramem, jest jedna właściwość więcej obsługiwanych: `authority`. Domyślnie ta wartość jest `https://login.windows.net` gdy nie jest określony, ale możesz użyć innej wartości, takich jak`https://login.windows\-ppe.net`. | 
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
| host | Yes | Obiekt | Reprezentuje informacje łącznika, takich jak `runtimeUrl` i odwołania do obiektu połączenia. | 
| method | Yes | Ciąg | Użyto jednego z tych metod HTTP: "GET", "POST", "PUT", "DELETE", "Poprawka" ani "HEAD" | 
| ścieżka | Yes | Ciąg | Ścieżka dla operacji interfejsu API | 
| — zapytania | Nie | Obiekt | Reprezentuje wszystkie parametry zapytania, które chcesz uwzględnić w adresie URL. <p>Na przykład `"queries": { "api-version": "2015-02-01" }` dodaje `?api-version=2015-02-01` do adresu URL. | 
| nagłówki | Nie | Obiekt | Reprezentuje każdy nagłówek, który jest wysyłany w żądaniu. <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| treść | Nie | Obiekt | Reprezentuje ładunek, które są wysyłane do punktu końcowego. | 
| retryPolicy | Nie | Obiekt | Ten obiekt jest używany dla Dostosowywanie zachowania ponownych prób dla 4xx lub 5xx błędów. Aby uzyskać więcej informacji, zobacz [ponów zasady](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nie | Ciąg | Definiuje zestaw specjalnego zachowania do zastąpienia. | 
| uwierzytelnianie | Nie | Obiekt | Reprezentuje metodę, która powinna być używana do uwierzytelniania żądania. Aby uzyskać więcej informacji, zobacz [uwierzytelniania połączeń wychodzących harmonogramu](../scheduler/scheduler-outbound-authentication.md). |
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
| host | Yes | Obiekt | Reprezentuje informacje łącznika, takich jak `runtimeUrl` i odwołania do obiektu połączenia. | 
| ścieżka | Yes | Ciąg | Ścieżka dla operacji interfejsu API | 
| — zapytania | Nie | Obiekt | Reprezentuje wszystkie parametry zapytania, które chcesz uwzględnić w adresie URL. <p>Na przykład `"queries": { "api-version": "2015-02-01" }` dodaje `?api-version=2015-02-01` do adresu URL. | 
| nagłówki | Nie | Obiekt | Reprezentuje każdy nagłówek, który jest wysyłany w żądaniu. <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| treść | Nie | Obiekt | Reprezentuje ładunek, które są wysyłane do punktu końcowego. | 
| retryPolicy | Nie | Obiekt | Ten obiekt jest używany dla Dostosowywanie zachowania ponownych prób dla 4xx lub 5xx błędów. Aby uzyskać więcej informacji, zobacz [ponów zasady](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Nie | Ciąg | Definiuje zestaw specjalnego zachowania do zastąpienia. | 
| uwierzytelnianie | Nie | Obiekt | Reprezentuje metodę, która powinna być używana do uwierzytelniania żądania. Aby uzyskać więcej informacji, zobacz [uwierzytelniania połączeń wychodzących harmonogramu](../scheduler/scheduler-outbound-authentication.md). |
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
| — zapytania | Nie | Obiekt | Reprezentuje wszystkie parametry zapytania, które chcesz uwzględnić w adresie URL. <p>Na przykład `"queries": { "api-version": "2015-02-01" }` dodaje `?api-version=2015-02-01` do adresu URL. | 
| nagłówki | Nie | Obiekt | Reprezentuje każdy nagłówek, który jest wysyłany w żądaniu. <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| treść | Nie | Obiekt | Reprezentuje ładunek, które są wysyłane do punktu końcowego. | 
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
| runError | Nie | Obiekt | Szczegóły błędu. Obsługiwane tylko wtedy, gdy `runStatus` ma ustawioną wartość `Failed`. |
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
| do | Nie | Obiekt | Czas trwania oczekiwania na podstawie punktu w czasie | 
| do sygnatury czasowej | Yes | Ciąg | Do punktu w czasie w [format daty i godziny UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) wygaśnięcia czas oczekiwania | 
| interval | Nie | Obiekt | Czas oczekiwania na podstawie jednostkę interwału i liczby | 
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
| — zapytania | Nie | Obiekt | Reprezentuje wszystkie parametry zapytania, które chcesz uwzględnić w adresie URL. <p>Na przykład `"queries": { "api-version": "2015-02-01" }` dodaje `?api-version=2015-02-01` do adresu URL. | 
| nagłówki | Nie | Obiekt | Reprezentuje każdy nagłówek, który jest wysyłany w żądaniu. <p>Na przykład, aby ustawić język i typ żądania: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| treść | Nie | Obiekt | Reprezentuje ładunek, które są wysyłane do punktu końcowego. | 
||||| 

Ta akcja dane wyjściowe są oparte na definiowanie w `Response` akcji podrzędnych przepływu pracy. Jeśli przepływ pracy podrzędny nie definiuje `Response` akcji, dane wyjściowe są puste.

## <a name="collection-actions-overview"></a>Przegląd działań kolekcji

Aby kontrolować wykonywanie przepływu pracy, akcje kolekcji może zawierać innych działań. Może bezpośrednio odwoływać się odwołanie do działań w kolekcji poza kolekcji. Na przykład w przypadku definiowania `Http` akcji w zakresie, które są następnie `@body('http')` jest nadal ważny w dowolnym miejscu w przepływie pracy. Ponadto akcje w kolekcji "wykonać tylko" inne akcje w tej samej kolekcji.

## <a name="if-action"></a>Jeśli akcja

Tę akcję, która jest instrukcji warunkowej, pozwala ocenić warunku i wykonywanie gałęzi według tego, czy wyrażenie jako true. Jeśli wyrażenie pomyślnie jako wartość true, ten stan jest oznaczony jako "Powodzenie". Akcje, które znajdują się w `actions` lub `else` obiektów oceny tych wartości:

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
| Akcje | Yes | Obiekt | Wewnętrzny akcje do wykonania, kiedy `expression` daje w wyniku `true` | 
| wyrażenie | Yes | Ciąg | Wyrażenie do oceny |
| else | Nie | Obiekt | Wewnętrzny akcje do wykonania, kiedy `expression` daje w wyniku `false` |
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
      "myCase1" : {
         "actions" : {
           "myAction1": {}
         },
         "case": "<result1>"
      },
      "myCase2": {
         "actions" : {
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
| Przypadków | Yes | Obiekt | Zawiera zestawy wewnętrzny akcje, które są uruchamiane na podstawie wyniku wyrażenia. | 
| Case | Yes | Ciąg | Wartość do dopasowania z wynikami | 
| Akcje | Yes | Obiekt | Wewnętrzny akcje, które są uruchamiane w przypadku pasującego do wyniku wyrażenia | 
| domyślnie | Nie | Obiekt | Wewnętrzny akcje uruchamiane po przypadkach nie pasował do wyniku | 
||||| 

Na przykład:

``` json
"myApprovalEmailAction": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions" : {
           "Send_an_email": {...}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions" : {
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
| Akcje | Yes | Obiekt | Wewnętrzny akcje do wykonania w pętli | 
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
| Akcje | Yes | Obiekt | Wewnętrzny akcje do wykonania w pętli | 
| wyrażenie | Yes | Ciąg | Wyrażenie do oceny po każdej iteracji | 
| Limit | Yes | Obiekt | Limity dla pętli. Należy zdefiniować co najmniej jeden limit. | 
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
| Akcje | Yes | Obiekt | Wewnętrzny akcje do wykonania w zakresie |
||||| 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md)
* Dowiedz się więcej o [interfejsu API REST przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflows)