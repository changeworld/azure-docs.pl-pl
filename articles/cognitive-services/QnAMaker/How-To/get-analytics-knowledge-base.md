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
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 5c55084a57e46931049841f5011941b2115e9e69
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961537"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Uzyskiwanie danych analitycznych na potrzeby bazy wiedzy

QnA Maker przechowuje wszystkie dzienniki rozmowy i inne dane telemetryczne, jeśli włączono usługę App Insights podczas [tworzenia usługi QNA Maker](./set-up-qnamaker-service-azure.md). Uruchom przykładowe zapytania, aby pobrać dzienniki rozmowy z usługi App Insights.

1. Przejdź do zasobu usługi App Insights.

    ![Wybierz zasób usługi Application Insights](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Wybierz pozycję **Dziennik (analiza)** . Zostanie otwarte nowe okno, w którym można wykonywać zapytania o dane telemetryczne QnA Maker.

3. Wklej w poniższym zapytaniu i uruchom go.

    ```kusto
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

    [![Uruchom zapytanie, aby określić pytania, odpowiedzi i oceny od użytkowników](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Uruchamianie zapytań dotyczących innych analiz w bazie wiedzy QnA Maker

### <a name="total-90-day-traffic"></a>Łączny ruch 90-dniowy

```kusto
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Łączny ruch związany z pytaniami w danym okresie

```kusto
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

```kusto
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

```kusto
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybierz capactiy](../tutorials/choosing-capacity-qnamaker-deployment.md)
