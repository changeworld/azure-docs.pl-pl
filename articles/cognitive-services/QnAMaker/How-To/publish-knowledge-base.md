---
title: Publikowanie bazy wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Publikowanie bazy wiedzy jest ostatnim krokiem w zakresie udostępniania wiedzy jako punktu końcowego udzielenie odpowiedzi na pytanie. Podczas publikowania bazy wiedzy pytań i odpowiedzi zawartość bazy wiedzy przenosi się z Indeks testu do produkcji indeksu w usłudze Azure search.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: eceb0554d35935f1aee77c4c054cb7e65b327845
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033419"
---
# <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Publikowanie bazy wiedzy jest ostatnim krokiem w zakresie udostępniania wiedzy jako punktu końcowego udzielenie odpowiedzi na pytanie. 

Podczas publikowania bazy wiedzy pytań i odpowiedzi zawartość bazy wiedzy przenosi się z Indeks testu do produkcji indeksu w usłudze Azure search.

![Publikowanie prod Indeks testu](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

1. Po zakończeniu wprowadzania zmian w wiedzy wybierz **Publikuj** w górnym pasku nawigacyjnym. Maksymalnie przypisanej liczby baz wiedzy można publikować dla usługi Azure Search. 

    ![Publikowanie bazy wiedzy](../media/qnamaker-how-to-publish-kb/publish.png)

2. Wybierz **Publikuj** ponownie, aby zobaczyć szczegóły punktu końcowego, które mogą być używane w kodzie aplikacji lub dowolnemu botowi.

    ![Publikowanie bazy wiedzy](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przeprowadzić analizę bazy wiedzy](./get-analytics-knowledge-base.md)
