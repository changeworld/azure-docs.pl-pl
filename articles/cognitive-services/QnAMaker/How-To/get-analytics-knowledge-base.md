---
title: Analiza bazy wiedzy - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker przechowuje wszystkie dzienniki czatu i inne dane telemetryczne, jeśli aplikacja App Insights została włączona podczas tworzenia usługi QnA Maker. Uruchom przykładowe zapytania, aby pobrać dzienniki czatu ze statystyk aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: e769bde39bc796b5b598109328b468b15385f38a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650405"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Uzyskiwanie danych analitycznych na potrzeby bazy wiedzy

QnA Maker przechowuje wszystkie dzienniki czatu i inne dane telemetryczne, jeśli aplikacja App Insights została włączona podczas [tworzenia usługi QnA Maker.](./set-up-qnamaker-service-azure.md) Uruchom przykładowe zapytania, aby pobrać dzienniki czatu ze statystyk aplikacji.

1. Przejdź do zasobu usługi App Insights.

    ![Wybieranie zasobu szczegółowych informacji o aplikacji](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Wybierz **dziennik (Analytics)**. Zostanie otwarte nowe okno, w którym można zbadać dane telemetryczne programu QnA Maker.

3. Wklej w poniższej kwerendzie i uruchom ją.

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    Kliknij przycisk **Uruchom**, aby uruchomić zapytanie.

    [![Uruchamianie kwerendy w celu określenia pytań, odpowiedzi i wyników od użytkowników](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Uruchamianie zapytań dotyczących innych analiz w bazie wiedzy programu QnA Maker

### <a name="total-90-day-traffic"></a>Całkowity ruch 90-dniowy

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Całkowity ruch pytań w danym okresie

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Ruch użytkownika

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Rozkład opóźnień pytań

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>Pytania bez odpowiedzi

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0"
| project timestamp, KbId, question, answer, score
| order  by timestamp  desc
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybierz capactiy](./improve-knowledge-base.md)
