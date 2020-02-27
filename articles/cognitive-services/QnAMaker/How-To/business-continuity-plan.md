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
ms.openlocfilehash: f920a789f1ec631c5d70863b10d8364b3eb81b6c
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650473"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Utwórz plan ciągłości biznesowej dla usługi QnA Maker

Głównym celem planu ciągłości działalności biznesowej jest utworzenie odpornego punktu końcowego bazy wiedzy, który zapewni brak czasu dla bot lub aplikacji.

![QnA Maker plan BCP](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Pomysł wysokiego poziomu przedstawiony powyżej jest następujący:

1. Skonfiguruj dwie [usługi QNA Maker Parallel Services](../How-To/set-up-qnamaker-service-azure.md) w [sparowanych regionach platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Zachowaj synchronizację podstawowych i pomocniczych indeksów usługi Azure Search. Skorzystaj [z przykładu GitHub, aby](https://github.com/pchoudhari/QnAMakerBackupRestore) zobaczyć, jak tworzyć kopie zapasowe indeksów platformy Azure.

3. Wykonaj kopię zapasową Application Insights za pomocą [eksportu ciągłego](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Po skonfigurowaniu stosów podstawowych i pomocniczych Użyj usługi [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) , aby skonfigurować dwa punkty końcowe i skonfigurować metodę routingu.

5. Należy utworzyć certyfikat SSL (SSL) dla punktu końcowego usługi Traffic Manager. [Powiąż certyfikat SSL](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) w usługach aplikacji.

6. Na koniec użyj punktu końcowego w usłudze Traffic Manager w bot lub aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybierz capactiy](./improve-knowledge-base.md)
