---
title: Przygotowany plan ciągłości działania — QnA Maker
titleSuffix: Azure Cognitive Services
description: Głównym celem plan ciągłości prowadzenia działalności biznesowej jest utworzyć punkt końcowy odporne na błędy bazy wiedzy knowledge base i zapewni bez przestoju Bot lub aplikacji korzystania z nich.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: f9892acb387a655e173ee5d2bde28e7346a6c535
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447535"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Utwórz plan ciągłości biznesowej usługi QnA Maker

Głównym celem plan ciągłości prowadzenia działalności biznesowej jest utworzyć punkt końcowy odporne na błędy bazy wiedzy knowledge base i zapewni bez przestoju Bot lub aplikacji korzystania z nich.

![Usługa QnA Maker bcp planu](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Ogólne pomysł, jak powyżej jest następująca:

1. Konfigurowanie dwóch równoległych [usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md) w [sparowanych regionów platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Synchronizuj indeksy podstawowe i pomocnicze usługi Azure search. Użyj przykładu z serwisu GitHub [tutaj](https://github.com/pchoudhari/QnAMakerBackupRestore) na temat sposobu wykonywania kopii zapasowej i przywracania indeksy platformy Azure.

3. Tworzenie kopii zapasowej za pomocą usługi Application Insights [Eksport ciągły](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Po zostało skonfigurowane stosy podstawowego i pomocniczego, użyj [usługi traffic manager](https://docs.microsoft.com/azure/traffic-manager/) można skonfigurować dwa punkty końcowe i skonfigurować metody routingu.

5. Czy musisz utworzyć certyfikat SSL dla punktu końcowego usługi traffic manager. [Wiązanie certyfikatu protokołu SSL](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl) w usługach App.

6. Na koniec użyj punktu końcowego Menedżera ruchu w sieci Web lub aplikacji Botów.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wybierz pojemność dla danego wdrożenia usługi QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
