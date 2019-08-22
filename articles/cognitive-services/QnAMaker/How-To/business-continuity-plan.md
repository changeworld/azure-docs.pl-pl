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
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 84f13f7e1d83f1ead00303b694b617d3ba1c8931
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876642"
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
