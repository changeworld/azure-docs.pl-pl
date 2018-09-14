---
title: Publikowanie bazy wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Publikowanie bazy wiedzy jest ostatnim krokiem w zakresie udostępniania wiedzy jako punktu końcowego udzielenie odpowiedzi na pytanie. Podczas publikowania bazy wiedzy pytań i odpowiedzi zawartość bazy wiedzy przenosi się z Indeks testu do produkcji indeksu w usłudze Azure search.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 0a43c54db7af61b72a8e8e6188fd13ad1159271e
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2018
ms.locfileid: "45540937"
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
