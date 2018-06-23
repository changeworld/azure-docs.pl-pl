---
title: Jak opublikować wiedzy - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Jak opublikować bazę wiedzy
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e9dbeacfb0df98c6b8f084c263690c05fe966cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348588"
---
# <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Publikowanie bazy wiedzy jest ostatnim krokiem w udostępnianie bazy wiedzy jako punktu końcowego udzielenie odpowiedzi na pytanie. 

Podczas publikowania bazy wiedzy przenosi zawartość — strona główna bazy wiedzy z indeksu testu indeks produkcji w usłudze Azure search.

![Publikowanie produkcyjną Indeks testu](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

1. Po zakończeniu ze zmianami w Twojej KB, wybierz **publikowania** w górnym pasku nawigacyjnym. Możesz opublikować do przydzielonego liczby baz wiedzy dla usługi Azure Search. 

    ![Publikowanie bazy wiedzy](../media/qnamaker-how-to-publish-kb/publish.png)

2. Wybierz **publikowania** ponownie, aby wyświetlić szczegóły punktu końcowego, które mogą być używane w kodzie aplikacji lub bot.

    ![Publikowanie bazy wiedzy](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Uzyskaj analizy na bazy wiedzy](./get-analytics-knowledge-base.md)
