---
title: Publikowanie bazy wiedzy
titleSuffix: QnA Maker API - Azure Cognitive Services
description: Publikowanie bazy wiedzy przy użyciu usługi QnA Maker API jest to ostatni krok, aby udostępnić swojej wiedzy jako punktu końcowego udzielenie odpowiedzi na pytanie. Podczas publikowania bazy wiedzy pytań i odpowiedzi zawartość bazy wiedzy przenosi się z Indeks testu do produkcji indeksu w usłudze Azure search.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c65654e7d6b2e66a07116ea9555ed316ace88912
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091957"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Publikowanie bazy wiedzy przy użyciu portalu usługi QnA Maker API

Publikowanie bazy wiedzy przy użyciu usługi QnA Maker API jest to ostatni krok, aby udostępnić swojej wiedzy jako punktu końcowego udzielenie odpowiedzi na pytanie. 

Podczas publikowania bazy wiedzy pytań i odpowiedzi zawartość bazy wiedzy przenosi się z Indeks testu do produkcji indeksu w usłudze Azure search.

![Publikowanie prod Indeks testu](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

1. Po zakończeniu wprowadzania zmian w wiedzy wybierz **Publikuj** w górnym pasku nawigacyjnym. Maksymalnie przypisanej liczby baz wiedzy można publikować dla usługi Azure Search. 

    ![Publikowanie bazy wiedzy](../media/qnamaker-how-to-publish-kb/publish.png)

2. Wybierz **Publikuj** ponownie, aby zobaczyć szczegóły punktu końcowego, które mogą być używane w kodzie aplikacji lub dowolnemu botowi.

    ![Pomyślnie opublikowano bazy wiedzy](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy skończysz z bazy wiedzy knowledge base, usuń go w portalu narzędzia QnA Maker.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przeprowadzić analizę bazy wiedzy](./get-analytics-knowledge-base.md)
