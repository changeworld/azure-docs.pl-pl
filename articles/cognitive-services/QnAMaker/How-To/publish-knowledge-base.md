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
ms.custom: seodec18
ms.openlocfilehash: 9c3d1009742f9ca0df6dc2f2d1a2d1904397275a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087852"
---
# <a name="publish-a-knowledge-base-in-qna-maker"></a>Publikowanie bazy wiedzy w usługi QnA Maker

Publikowanie bazy wiedzy jest ostatnim krokiem w zakresie udostępniania wiedzy jako punktu końcowego udzielenie odpowiedzi na pytanie. 

Podczas publikowania bazy wiedzy pytań i odpowiedzi zawartość bazy wiedzy przenosi się z Indeks testu do produkcji indeksu w usłudze Azure search.

![Publikowanie prod Indeks testu](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

1. Po zakończeniu wprowadzania zmian w wiedzy wybierz **Publikuj** w górnym pasku nawigacyjnym. Maksymalnie przypisanej liczby baz wiedzy można publikować dla usługi Azure Search. 

    ![Publikowanie bazy wiedzy](../media/qnamaker-how-to-publish-kb/publish.png)

2. Wybierz **Publikuj** ponownie, aby zobaczyć szczegóły punktu końcowego, które mogą być używane w kodzie aplikacji lub dowolnemu botowi.

    ![Pomyślnie opublikowano bazy wiedzy](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przeprowadzić analizę bazy wiedzy](./get-analytics-knowledge-base.md)
