---
title: Analiza bazy wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker przechowuje wszystkie dzienniki rozmowy i inne dane telemetryczne, jeśli włączono usługę App Insights podczas tworzenia usługi QnA Maker. Uruchom przykładowe zapytania, aby pobrać dzienniki rozmowy z usługi App Insights.
services: cognitive-services
author: diberry
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 5c729065076f5dc9f25189632f42ed565a72df8a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563111"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Uzyskiwanie danych analitycznych na potrzeby bazy wiedzy

QnA Maker przechowuje wszystkie dzienniki rozmowy i inne dane telemetryczne, jeśli włączono usługę App Insights podczas [tworzenia usługi QNA Maker](./set-up-qnamaker-service-azure.md). Uruchom przykładowe zapytania, aby pobrać dzienniki rozmowy z usługi App Insights.

1. Przejdź do zasobu usługi App Insights.

    ![Wybierz zasób usługi Application Insights](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Wybierz pozycję **Analiza**. Zostanie otwarte nowe okno, w którym można wykonywać zapytania o dane telemetryczne QnA Maker.

    ![Wybieranie analiz](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Wklej w poniższym zapytaniu i uruchom go.

    ```query
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration, performanceBucket
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId 
    ```

    Wybierz pozycję **Uruchom** , aby uruchomić zapytanie.

    ![Uruchom zapytanie](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Uruchamianie zapytań dotyczących innych analiz w bazie wiedzy QnA Maker

### <a name="total-90-day-traffic"></a>Łączny ruch 90-dniowy

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Łączny ruch związany z pytaniami w danym okresie

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Ruch użytkownika

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Rozkład opóźnień pytań

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zarządzaj kluczami](./key-management.md)
