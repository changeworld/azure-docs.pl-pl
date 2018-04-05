---
title: Błąd i obsługi dla usługi Logic Apps w usłudze Azure wyjątków | Dokumentacja firmy Microsoft
description: Wzorce dla błędów i obsługa wyjątków w aplikacji logiki.
services: logic-apps
documentationcenter: ''
author: dereklee
manager: anneta
editor: ''
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/2018
ms.author: deli; LADocs
ms.openlocfilehash: 70dd4e98dbffd9dac27752f0b4c2f5ce4ca70bdc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Obsługa błędów i wyjątków w aplikacji logiki

Odpowiednio Obsługa przestoje lub problemów z systemów zależne mogą wiązać się żądanie dla dowolnej architektury integracji. Aby utworzyć niezawodny integracji, które są odporne na błędy i problemy, Logic Apps oferuje najwyższej jakości środowisko do obsługi błędów i wyjątków. 

## <a name="retry-policies"></a>Spróbuj ponownie zasad

Najbardziej podstawowa wyjątku i obsługi błędów można użyć zasady ponawiania. Jeżeli wstępne żądanie przekroczyło limit czasu lub nie powiodło się, jest każde żądanie skutkuje 429 lub odpowiedź 5xx, te zasady określa, czy i jak akcja ponawia próbę żądania. 

Istnieją cztery typy zasad ponawiania: domyślna, brak stałej interwał i interwał wykładniczej. Jeśli Twoje definicji przepływu pracy nie ma zasady ponawiania, domyślne zasady, zgodnie z definicją w usłudze, zamiast niego jest używana.

Aby skonfigurować zasady ponawiania, jeśli ma to zastosowanie, otwórz projektanta aplikacji logiki aplikacji logiki i przejdź do **ustawienia** dla określonej akcji w aplikacji logiki. Można zdefiniować zasady ponawiania w **dane wejściowe** sekcji dla określonej akcji lub wyzwalacza, jeśli powtarzający operację, w definicji przepływu pracy. Poniżej przedstawiono składnię ogólną:

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Aby uzyskać więcej informacji na temat składni i **dane wejściowe** sekcji, zobacz [części zasady ponawiania akcji przepływu pracy i wyzwalaczy][retryPolicyMSDN]. Informacje o ograniczeniach zasady ponawiania, zobacz [limity Logic Apps i konfiguracji](../logic-apps/logic-apps-limits-and-config.md). 

### <a name="default"></a>Domyślne

Gdy nie definiować zasady ponawiania w **retryPolicy** sekcji aplikację logiki używa zasadę domyślną, która jest [wykładniczej interwał zasad](#exponential-interval) wykładniczo wysyłają do czterech ponownych prób w zwiększanie interwałów, które są skalowane 7.5 sekund. Interwał jest ograniczona od 5 do 45 sekund. Ta zasada jest odpowiednikiem zasad w tym przykładzie HTTP definicji przepływu pracy:

```json
"HTTP": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT1H"
        }
    },
    "runAfter": {}
}
```

### <a name="none"></a>Brak

Jeśli ustawisz **retryPolicy** do **Brak**, ta zasada nie ponów żądań zakończonych niepowodzeniem.

| Nazwa elementu | Wymagane | Typ | Opis | 
| ------------ | -------- | ---- | ----------- | 
| type | Yes | Ciąg | **Brak** | 
||||| 

### <a name="fixed-interval"></a>Stały odstęp

Jeśli ustawisz **retryPolicy** do **stałej**, ta zasada ponowi próbę nieudanych żądań przez określony interwał przed wysłaniem żądania dalej oczekiwania.

| Nazwa elementu | Wymagane | Typ | Opis |
| ------------ | -------- | ---- | ----------- |
| type | Yes | Ciąg | **Stałe** |
| Liczba | Yes | Liczba całkowita | Liczba ponownych prób, które musi należeć do zakresu od 1 do 90 | 
| interval | Yes | Ciąg | Interwał ponawiania w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), który musi należeć do zakresu od PT5S i PT1D | 
||||| 

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Interwał wykładniczy

Jeśli ustawisz **retryPolicy** do **wykładniczej**, ta zasada ponawia próby nieudanych żądań, po losowych odstępach czasu z wykładniczo powiększających się. Zasady gwarantuje również wysłać kolejnymi ponowieniami prób w losowych odstępach czasu, która jest większa niż **minimumInterval** i mniejsza niż **maximumInterval**. Zasady wykładniczej wymagają **liczba** i **interwał**, podczas wartości **minimumInterval** i **maximumInterval** są opcjonalne. Jeśli chcesz zastąpić wartości domyślne PT5S i PT1D odpowiednio, można dodać te wartości.

| Nazwa elementu | Wymagane | Typ | Opis |
| ------------ | -------- | ---- | ----------- |
| type | Yes | Ciąg | **Wykładniczy** |
| Liczba | Yes | Liczba całkowita | Liczba ponownych prób, które musi należeć do zakresu od 1 do 90  |
| interval | Yes | Ciąg | Interwał ponawiania w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), który musi należeć do zakresu od PT5S i PT1D. |
| minimumInterval | Nie | Ciąg | Minimalny interwał ponawiania w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), który musi należeć do zakresu od PT5S i **interwał** |
| maximumInterval | Nie | Ciąg | Minimalny interwał ponawiania w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), który musi należeć do zakresu od **interwał** i PT1D | 
||||| 

W poniższej tabeli przedstawiono, jak uniform zmienną losową wskazanego zakresu jest generowany dla każdego ponawiania włącznie **liczba**:

**Zakres zmiennej losowej**

| Spróbuj ponownie numer | Minimalny interwał | Maksymalny interwał |
| ------------ | ---------------- | ---------------- |
| 1 | MAX (0, **minimumInterval**) | Min (interwał, **maximumInterval**) |
| 2 | MAX (interwał, **minimumInterval**) | Min (2 * interwał, **maximumInterval**) |
| 3 | MAX (2 * interwał, **minimumInterval**) | Min (4 * interwał, **maximumInterval**) |
| 4 | MAX (4 * interwał, **minimumInterval**) | Min (8 * interwał, **maximumInterval**) |
| .... | | | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Wychwycić i obsłużyć awarie razem z właściwością RunAfter

Każde działanie aplikacji logiki deklaruje akcje, które musi zakończyć się przed rozpoczęciem tej akcji, podobny do określania kolejności kroków w przepływie pracy. W definicji działania **runAfter** właściwość definiuje ta kolejność i obiektu w tym artykule opisano, które akcje i Stany Akcja wykonania akcji.

Domyślnie wszystkie akcje, które można dodać w Projektancie aplikacji logiki są ustawione na uruchamianie po poprzednim kroku, gdy wynik poprzedniego kroku jest **zakończyło się pomyślnie**. Można jednak dostosować **runAfter** tak, aby akcje zostać wywołane podczas poprzedniej akcji powoduje jako, **pomijane**, lub kombinacji tych wartości. Na przykład, aby dodać element do określonego tematu usługi Service Bus po określonej **Insert_Row** akcji kończy się niepowodzeniem, można użyć w tym przykładzie **runAfter** definicji:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

**RunAfter** uruchomiony, jeśli właściwość ma wartość **Insert_Row** stanu akcji jest. Aby uruchomić akcję, gdy stan działania to **zakończyło się pomyślnie**, lub **pomijane**, należy użyć następującej składni:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Akcje, które Uruchom i zakończyła się pomyślnie po poprzednim akcja nie powiodła się, są oznaczone jako **zakończyło się pomyślnie**. To zachowanie oznacza, że jeśli możesz pomyślnie catch wszystkich błędów w przepływie pracy, uruchom sam jest oznaczona jako **zakończyło się pomyślnie**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Akcje z zakresami i ich wyników oceny

Podobny do uruchamiania kroki po poszczególnych działań z **runAfter** właściwości, można pogrupować Akcje wewnątrz [zakres](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Zakresów można użyć, jeśli chcesz logicznie grupują akcje ocenić Łączny stan zakresu i wykonywać działania na podstawie tego stanu. Po wszystkie akcje w zakresie zakończą działanie, zakres sam pobiera własny stan. 

Aby sprawdzić stan zakresu, można użyć takich samych kryteriów używanych do sprawdzenia stanu uruchomienia aplikacji logiki, takich jak **zakończyło się pomyślnie**, i tak dalej. 

Domyślnie, gdy zakres wszystkie akcje powiedzie się, stan zakresu jest on oznaczony **zakończyło się pomyślnie**. Jeśli powoduje ostatecznych działań w zakresie lub **przerwania**, stan zakresu jest oznaczony jako **nie powiodło się**. 

Przechwytują wyjątki w **nie powiodło się** zakres i wykonywania działań, które obsługi tych błędów, można użyć **runAfter** właściwości, dla którego zakresu. W ten sposób, jeśli *żadnych* akcje w zakresie zakończyć się niepowodzeniem i używasz **runAfter** właściwość dla tego zakresu, można utworzyć jednej akcji, aby wykryć błędów.

Dla ograniczenia na podstawie zakresów, zobacz [limity i konfiguracji](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Pobierz kontekstu i wyniki dla niepowodzenia

Mimo że przechwytywanie błędów z zakresu jest przydatne, będą również chcieli kontekście pomagające zrozumieć dokładnie akcje, które nie powiodło się i występują błędy lub kodów stanu, które zostały zwrócone.  **@result()** Funkcji przepływu pracy zawierają kontekst o wyniku wszystkie akcje w zakresie.

 **@result()** Funkcja przyjmuje jeden parametr (nazwa zakresu) i zwraca tablicę wszystkich akcji wyników w tym zakresie. Te obiekty działania obejmują takie same atrybuty jak  **@actions()** obiektu, na przykład akcji czas rozpoczęcia, godziny zakończenia, stanu, danych wejściowych, identyfikatorów korelacji i dane wyjściowe. Aby wysłać kontekst dla akcji, które nie powiodło się w zakresie, można łatwo skojarzyć  **@result()** działać z **runAfter** właściwości.

Do uruchomienia akcji *dla każdego* akcji w zakresie, którego **nie powiodło się** wyników, aby filtrować tablicy wyniki do akcji nie powiodło się, służący  **@result()** z **[tablicy filtrów](../connectors/connectors-native-query.md)** akcji i **[ForEach](../logic-apps/logic-apps-control-flow-loops.md)** pętli. Możesz pobrać tablicy filtrowane wyniki i wykonania czynności dla każdego błędu przy użyciu funkcji **ForEach** pętli. 

Oto przykład, a następnie szczegółowy opis, który wysyła żądanie HTTP POST z treści odpowiedzi żadnych akcji, których nie powiodła się w zakresie "My_Scope":

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Poniżej przedstawiono szczegółowy przewodnik, który opisuje, co się stanie, w tym przykładzie:

1. Aby uzyskać wynik wszystkich akcji w obrębie "My_Scope" **tablicy filtrów** filtry akcji  **@result(My_Scope)**.

2. Warunek **tablicy filtrów** dowolnego  **@result()** element, który ma stan równa. Ten warunek filtruje tablicę wszystkich akcji wyników z "My_Scope" do tablicy z tylko wyniki akcji nie powiodło się.

3. Wykonaj **dla każdego** pętla akcji na *tablicy filtrowane* danych wyjściowych. Ten krok wykonuje akcję *dla każdego* nie powiodło się wynik akcji, która wcześniej została przefiltrowana.

   Jeśli w zakresie jednej akcji zakończyło się niepowodzeniem, akcje w **foreach** uruchomić tylko raz. 
   Wiele zakończonych niepowodzeniem akcje mogą spowodować jedną akcję na błąd.

4. Wyślij HTTP POST **foreach** elementu treści odpowiedzi, który jest  **@item() ['wyniki'] [treści]**.  **@result()** Kształt element jest taka sama jak  **@actions()** kształtu i może być analizowana taki sam sposób.

5. Obejmują dwa Nagłówki niestandardowe o nazwie nieudanych akcji  **@item() [nazwa]** i nieudane Uruchom klienta, identyfikator śledzenia  **@item() [clientTrackingId]**.

Odwołania, Oto przykład jedną  **@result()** elementu przedstawiający **nazwa**, **treści**, i **clientTrackingId** właściwości, które są parsowane w poprzednim przykładzie. Poza **foreach** akcji,  **@result()** zwraca tablicę tych obiektów.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/folder-name/resource-name does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

Do wykonywania różnych wzorców w obsłudze wyjątków, używając wyrażenia opisany wcześniej w tym artykule. Może również wykonać pojedynczego wyjątków, Obsługa akcji poza zakres, który akceptuje całą macierz filtrowane awarii, a Usuń **foreach** akcji. Możesz również uwzględnić inne przydatne właściwości z  **@result()** odpowiedzi, w sposób opisany wcześniej.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnostyka Azure i telemetrii

Te wzorce poprzedniej to doskonały sposób na Obsługa błędów i wyjątków w ramach Uruchom, ale można również zidentyfikować i odpowiadać na błędy, niezależnie od samego przebiegu. 
[Diagnostyka Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) zapewnia prosty sposób wysyłania wszystkich zdarzeń przepływu pracy, w tym wszystkie stany uruchomienia i akcji, do konta usługi Azure Storage lub Centrum zdarzeń utworzonych za pomocą usługi Azure Event Hubs. 

Aby ocenić stany wykonywania, Monitoruj dzienniki i metryki lub do dowolnego narzędzia monitorowania, które chcesz publikować. Jedną z opcji potencjalnych jest do strumienia wszystkich zdarzeń za pomocą usługi Event Hubs w [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). W Stream Analytics można zapisywać zapytania na żywo na podstawie wszelkie nieprawidłowości, średnie lub błędy z dzienników diagnostycznych. Analiza strumienia służy do wysyłania informacji do innych źródeł danych, takich jak kolejki, tematy, SQL, bazy danych rozwiązania Cosmos Azure lub usługi Power BI.

## <a name="next-steps"></a>Następne kroki

* [Zobacz, jak klient tworzy błąd obsługi z usługi Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Znajdź więcej Logic Apps przykłady i scenariusze](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9