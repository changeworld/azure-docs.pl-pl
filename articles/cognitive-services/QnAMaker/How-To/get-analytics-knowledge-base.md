---
title: Jak uzyskać analizy swoją wiedzę podstawowej - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Jak uzyskać analytics na bazy wiedzy
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 1588d0c5a8eaf4e161b5319c9f33a772dc56b247
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348617"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Uzyskaj analizy na bazy wiedzy

Maker — strona główna przechowuje wszystkie dzienniki rozmów i inne dane telemetryczne, jeśli włączono App Insights podczas [tworzenia usługi Maker — strona główna](./set-up-qnamaker-service-azure.md). Uruchamianie przykładowych zapytań można pobrać dzienników rozmów z usługi App Insights.

1. Przejdź do zasobu usługi App Insights.

    ![Wybierz z zasobu usługi application insights](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Wybierz **Analytics**. Nowe otworzyć okno, gdzie można zbadać Maker — strona główna telemetrii.

    ![Wybierz analityka](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Wklej poniższe zapytanie i uruchom go.

    ```query
        requests
        | where url endswith "generateAnswer"
        | project timestamp, id, name, resultCode, duration
        | parse name with *"/knowledgebases/"KbId"/generateAnswer"
        | join kind= inner (
        traces | extend id = operation_ParentId
        ) on id
        | extend question = tostring(customDimensions['Question'])
        | extend answer = tostring(customDimensions['Answer'])
        | project KbId, timestamp, resultCode, duration, question, answer
    ```

    Wybierz **Uruchom** do uruchomienia zapytania.

    ![Uruchamianie zapytania](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Uruchamianie zapytań dotyczących innych analytics na Maker — strona główna bazy wiedzy

### <a name="total-90-day-traffic"></a>Ruch związany z 90-dniowy

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
``` 

### <a name="total-question-traffic-in-a-given-time-period"></a>Pytanie łączny ruch w danym okresie

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Ruchu użytkowników

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse name with *"/knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Rozkład opóźnienia pytania

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zarządzanie kluczami](./key-management.md)
