---
title: Plan ciągłości biznesowej — QnA Maker
titleSuffix: Azure Cognitive Services
description: Głównym celem planu ciągłości działalności biznesowej jest utworzenie odpornego punktu końcowego bazy wiedzy, który zapewni brak czasu dla bot lub aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 67b61fe69543f83bd57e8a976bf3d9980d8298cb
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955119"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Utwórz plan ciągłości biznesowej dla usługi QnA Maker

Głównym celem planu ciągłości działalności biznesowej jest utworzenie odpornego punktu końcowego bazy wiedzy, który zapewni brak czasu dla bot lub aplikacji.

![QnA Maker plan BCP](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Pomysł wysokiego poziomu przedstawiony powyżej jest następujący:

1. Skonfiguruj dwie [usługi QNA Maker Parallel Services](../How-To/set-up-qnamaker-service-azure.md) w [sparowanych regionach platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Zachowaj synchronizację podstawowych i pomocniczych indeksów usługi Azure Search. Skorzystaj z przykładu [](https://github.com/pchoudhari/QnAMakerBackupRestore) GitHub, aby zobaczyć, jak tworzyć kopie zapasowe indeksów platformy Azure.

3. Wykonaj kopię zapasową Application Insights za pomocą [eksportu ciągłego](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Po skonfigurowaniu stosów podstawowych i pomocniczych Użyj usługi [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) , aby skonfigurować dwa punkty końcowe i skonfigurować metodę routingu.

5. Należy utworzyć certyfikat SSL dla punktu końcowego usługi Traffic Manager. [Powiąż certyfikat SSL](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl) w usługach aplikacji.

6. Na koniec użyj punktu końcowego w usłudze Traffic Manager w bot lub aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybieranie pojemności dla wdrożenia QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
